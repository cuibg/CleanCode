# 文件和数据库

1. 任何时候不要硬编码文件路径，请使用Android 文件系统API 访问

    ```
    Environment.getDataDirectory() = /data
    Environment.getDownloadCacheDirectory() = /cache
    Environment.getExternalStorageDirectory() = /mnt/sdcard
    Environment.getExternalStoragePublicDirectory(“test”) = /mnt/sdcard/test
    Environment.getRootDirectory() = /system
    getPackageCodePath() = /data/app/com.my.app-1.apk
    getPackageResourcePath() = /data/app/com.my.app-1.apk
    getCacheDir() = /data/data/com.my.app/cache
    getDatabasePath(“test”) = /data/data/com.my.app/databases/test
    getDir(“test”, Context.MODE_PRIVATE) = /data/data/com.my.app/app_test
    getExternalCacheDir() = /mnt/sdcard/Android/data/com.my.app/cache
    getExternalFilesDir(“test”) = /mnt/sdcard/Android/data/com.my.app/files/test
    getExternalFilesDir(null) = /mnt/sdcard/Android/data/com.my.app/files
    getFilesDir() = /data/data/com.my.app/files
    ```

2. 当使用外部存储时，必须检查外部存储的可用性。
3. 应用间共享文件时，不要通过放宽文件系统权限的方式去实现，而应使用FileProvider。
4. SharedPreference 中只能存储简单数据类型（int、boolean、String 等），复杂数据类型建议使用文件、数据库等其他方式存储。
5. SharedPreference 提交数据时， 尽量使用Editor#apply() ， 而非Editor#commit()。一般来讲，仅当需要确定提交结果，并据此有后续操作时，才使用Editor#commit()。
6. 数据库Cursor 必须确保使用完后关闭，以免内存泄漏。
7. 多线程操作写入数据库时，需要使用事务，以免出现同步问题。
8. 大数据写入数据库时，请使用事务或其他能够提高I/O 效率的机制，保证执行速度。

    ```
    public void insertBulk(SQLiteDatabase db, ArrayList<UserInfo> users) {
          db.beginTransaction();
          try {
            for (int i = 0; i < users.size; i++) {
              ContentValues cv = new ContentValues();
              cv.put("userId", users[i].userId);
              cv.put("content", users[i].content);
              db.insert(TUserPhoto, null, cv);
            }
            // 其他操作
            db.setTransactionSuccessful();
          } catch (Exception e) {
          // TODO
          } finally {
            db.endTransaction();
          }
      }
    ```

9. 执行SQL 语句时，应使用SQLiteDatabase#insert()、update()、delete()，不要使用SQLiteDatabase#execSQL()，以免SQL 注入风险。
10. 如果ContentProvider 管理的数据存储在SQL 数据库中，应该避免将不受信任的外部数据直接拼接在原始SQL 语句中。