# 安全

1. 禁止使用常量初始化矢量参数构建IvParameterSpec，建议IV 通过随机方式产生。

    * 使用常量初始化向量，密码文本的可预测性会高得多，容易受到字典式攻击。iv 的作用主要是用于产生密文的第一个block，以使最终生成的密文产生差异（明文相同的情况下），使密码攻击变得更为困难。
     
2. 将android:allowbackup 属性必须设置为false，阻止应用数据被导出。

    * android:allowBackup 原本是 Android 提供的 adb 调试功能，如果设置为 true，可以导出应用数据备份并在任意设备上恢复。这对应用安全性和用户数据隐私构成极大威胁，所以必须设置为 false，防止数据泄露。

3. 如果使用自定义HostnameVerifier 实现类，必须在verify()方法中校验服务器主机名的合法性，否则可能受到中间人攻击。

    * 在与服务器建立 https 连接时，如果 URL 的主机名和服务器的主机名不匹配，则可通过该回调接口来判断是否应该允许建立连接。如果回调内实现不恰当，没有有效校验主机名，甚至默认接受所有主机名，会大大增加安全风险。

4. 如果使用自定义X509TrustManager 实现类，必须在checkServerTrusted()方法中校验服务端证书的合法性，否则可能受到中间人攻击。

    * 常见误区是checkServerTrusted()方法根本没有实现，这将导致 X509TrustManager形同虚设。该方法中需要实现完备的校验逻辑， 对于证书错误抛出CertificateException 。
    * 正例

        ```
        HostnameVerifier hnv = new HostnameVerifier() {
            @Override
            public boolean verify(String hostname, SSLSession session) {
              if("yourhostname".equals(hostname)){
                return true;
              } else {
                HostnameVerifier hv = HttpsURLConnection.getDefaultHostnameVerifier();
                return hv.verify(hostname, session);
              }
            }
        };
        ```

5. 在SDK 支持的情况下，Android 应用必须使用V2 签名，这将对APK 文件的修改做更多的保护。
6. 所有的 Android 基本组件（Activity、Service、BroadcastReceiver、ContentProvider 等）都不应在没有严格权限控制的情况下，将 android:exported 设置为 true。
7. WebView 应设置 WebView#getSettings()#setAllowFileAccess(false)、WebView#getSettings()#setAllowFileAccessFromFileURLs(false) 、WebView#getSettings()#setAllowUniversalAccessFromFileURLs(false)，阻止 filescheme URL 的访问。
8. 不要把敏感信息打印到log 中。
9. 确保应用发布版本的android:debuggable 属性设置为false。
10. 本地加密秘钥不能硬编码在代码中，更不能使用 SharedPreferences 等本地持久化机制存储。应选择Android 自身的秘钥库（KeyStore）机制或者其他安全性更高的安全解决方案保存。

    * 应用程序在加解密时，使用硬编码在程序中的密钥，攻击者通过反编译拿到密钥可以轻易解密APP 通信数据。

11. addJavascriptInterface() 可以添加JS 对本地Java 方法的调用，但这本身会导致恶意代码的攻击。在Android 4.2（API Level 17）以下，不应再使用这样的调用方式。在Android 4.2 及以上，需要对本地被远程调用的方法显式添加@JavascriptInterface annotation。
12. 使用Android 的AES/DES/DESede 加密算法时，不要使用ECB 加密模式，应使用CBC 或CFB 加密模式。

    * 加密模式有 ECB、CBC、CFB、OFB 等，其中 ECB 的安全性较弱，如果使用固定的密钥，相同的明文将会生成相同的密文，容易受到字典攻击，建议使用 CBC、CFB 或OFB 等模式。
    * ECB：Electronic codebook，电子密码本模式
    * CBC：Cipher-block chaining，密码分组链接模式
    * CFB：Cipher feedback，密文反馈模式
    * OFB：Output feedback，输出反馈模式

13. Android APP 在HTTPS 通信中，验证策略需要改成严格模式。

    * Android APP 在HTTPS 通信中，使用ALLOW_ALL_HOSTNAME_VERIFIER，表示允许和所有的HOST 建立SSL 通信，这会存在中间人攻击的风险，最终导致敏感信息可能会被劫持，以及其他形式的攻击。
    * 反例

        ```
        SSLSocketFactory sf = new MySSLSocketFactory(trustStore);
        sf.setHostnameVerifier(SSLSocketFactory.ALLOW_ALL_HOSTNAME_VERIFIER);
        ```
    * ALLOW_ALL_HOSTNAME_VERIFIER 关闭host 验证，允许和所有的host 建立SSL 通信，BROWSER_COMPATIBLE_HOSTNAME_VERIFIER 和浏览器兼容的验证策略，即通配符能够匹配所有子域名 ，STRICT_HOSTNAME_VERIFIER 严格匹配模式，hostname 必须匹配第一个CN 或者任何一个subject-alts，以上例子使用了ALLOW_ALL_HOSTNAME_VERIFIER，需要改成STRICT_HOSTNAME_VERIFIER。

14. 在Android 4.2（API Level 17）及以上，对安全性要求较高的应用可在Activity中，对 Activity 所关联的 Window 应用 WindowManager.LayoutParams.FLAG_SECURE，防止被截屏、录屏。但要注意的是，一个 Activity 关联的 Window 可能不止一个，如果使用了 Dialog / DialogFragment 等控件弹出对话框，它们本身也会创建一个新的 Window，也一样需要保护。
15. zip 中不要包含 ../../file 这样的路径，可能被篡改目录结构，造成攻击。

    * 当zip 压缩包中允许存在"../"的字符串，攻击者可以利用多个"../"在解压时改变zip 文件存放的位置，当文件已经存在是就会进行覆盖，如果覆盖掉的文件是so、dex 或者odex 文件，就有可能造成严重的安全问题。
    * 对路径进行判断，存在".."时抛出异常。

        ```
        //对重要的Zip 压缩包文件进行数字签名校验，校验通过才进行解压
        String entryName = entry.getName();
        if (entryName.contains("..")){
          throw new Exception("unsecurity zipfile!");
        }
        ```

16. MD5 和SHA-1、SHA-256 等常用算法是Hash 算法，有一定的安全性，但不能代替加密算法。敏感信息的存储和传输，需要使用专业的加密机制。