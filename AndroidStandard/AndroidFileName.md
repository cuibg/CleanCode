# Android资源文件命名与使用

## 资源文件需带有模块前缀

## layout

1. 使用举例
    ```
    Activity 的layout 以module_activity 开头
    Fragment 的layout 以module_fragment 开头
    Dialog 的layout 以module_dialog 开头
    include 的layout 以module_include 开头
    ListView 的行layout 以module_list_item 开头
    RecyclerView 的item layout 以module_recycle_item 开头
    GridView 的item layout 以module_grid_item 开头
    ```

## drawable

1. drawable 资源名称以小写单词+下划线的方式命名，根据分辨率不同存放在不同的drawable 目录下，如果介意包大小建议只使用一套，系统去进行缩放。

2. 命名规则

    ```
    模块名_业务功能描述_控件描述_控件状态限定词
    //例如
    module_login_btn_pressed,module_tabs_icon_home_normal
    ```

## anim

1. 资源名称以小写单词+下划线的方式命名
2. 规则
    ```
    模块名_逻辑名称_[方向|序号]
    ```
3. 例子

    * Tween 动画（使用简单图像变换的动画，例如缩放、平移）资源：尽可能以通用的动画名称命名，如module_fade_in , module_fade_out , module_push_down_in (动画+方向)。
    * Frame 动画（按帧顺序播放图像的动画）资源：尽可能以模块+功能命名+序号。module_loading_grey_001。

## color

1. color 资源使用#AARRGGBB 格式，写入module_colors.xml 文件中
2. 规则

      ```
      模块名_逻辑名称_颜色
      ```
3. 举例

    ```
    <color name="module_btn_bg_color">#33b5e5e5</color>
    ```

## dimen

1. dimen 资源以小写单词+下划线方式命名，写入module_dimens.xml 文件中
2. 规则

    ```
    模块名_描述信息
    ```
3. 举例

    ```
    <dimen name="module_horizontal_line_height">1dp</dimen>
    ```

## style

1. style 资源采用“ 父style 名称.当前style 名称”方式命名，写入module_styles.xml 文件中，首字母大写.
2. 举例

    ```
    <style name="ParentTheme.ThisActivityTheme">
    …
    </style>
    ```

## string

1. string资源文件或者文本用到字符需要全部写入module_strings.xml 文件中，字符串以小写单词+下划线的方式命名.
2. 规则

    ```
    模块名_逻辑名称
    ```
3. 举例

    ```
    moudule_login_tips,module_homepage_notice_desc
    ```
    
## id

1. Id 资源原则上以驼峰法命名，View 组件的资源id 建议以View 的缩写作为前缀.
2. 举例

    | 控件        | 缩写    
    | --------   | -----:   
    | LinearLayout        | ll     
    | RelativeLayout      | rl     
    | ConstraintLayout    | cl     
    | ListView    |      lv
    | ScollView    | sv 
    | TextView    | tv    
    | Button    | btn   
    | ImageView    | iv     
    | CheckBox     | cb
    | RadioButton     | rb
    | EditText      |et

3. 其它控件的缩写推荐使用小写字母并用下划线进行分割，例如：ProgressBar 对应的缩写为progress_bar；DatePicker 对应的缩写为date_picker。 

## 图片

1. 图片根据其分辨率，放在不同屏幕密度的drawable 目录下管理
2. 为了支持多种屏幕尺寸和密度，Android 提供了多种通用屏幕密度来适配
3. 但是通常我们只用一种或者两种图片就OK了



