### 注解的作用

使用注解向 Lint 之类的代码检查工具提供提示，帮助检测这些更细微的代码问题。您可以将注解作为元数据标记附加至变量、参数和返回值，用于检查方法返回值、传递的参数以及本地变量和字段。

菜单栏中选择 **Analyze** > **Inspect Code**。Android Studio 将显示冲突消息，在您的代码与注解冲突的地方标记潜在问题并建议可能的解决方法。

### 常用注解分类

* Nullness

  `@Nullable` 注解指示可以为 null 的变量、参数或返回值，而 `@NonNull` 则指示不可为 null 的变量、参数或返回值。

  ```java
  //会检查传递的参数context/attrs，以及onCreateView返回值是否不为null
  @NonNull
  @Override
  public View onCreateView(String name, @NonNull Context context,
        @NonNull AttributeSet attrs) {
        ...
  }
  ```

* 资源注解

  用于验证注解类型，Android 对资源（例如可绘制对象和字符串资源）的引用以整型形式传递。

  例如 @DrawableRes、@DimenRes、@ColorRes 、 @InterpolatorRes、@StringRes、@AnyRes [可为任意类型的 R 资源]

  ```java
  //代码检查期间，resId如果不是R.string 引用，注解将生成警告。
  public abstract void setTitle(@StringRes int resId) { … }
  ```

* 线程注解

  线程注解可以检查某个方法是否从特定类型的线程调用。

  常见的线程注解有@MainThread、@UiThread、@WorkerThread、@BinderThread、@AnyThread

* 值约束注解

  验证传递的参数的值的范围

  ```java
  //@IntRange 注解
  public void setAlpha(@IntRange(from=0,to=255) int alpha) { … }
  //@FloatRange
  public void setAlpha(@FloatRange(from=0.0, to=1.0) float alpha) {...}
  /**
   * @Size 注解
   * 最小大小（例如 @Size(min=2)）
   * 最大大小（例如 @Size(max=2)）
   * 确切大小（例如 @Size(2)）
   * 表示大小必须为此倍数的数字（例如 @Size(multiple=2)）
   */
  //检查location不为空
  button.getLocationOnScreen(@Size(min=1) location);

  ```

* 权限注解

   `@RequiresPermission` 注解可以验证方法调用方的权限

  ```java
  @RequiresPermission(Manifest.permission.SET_WALLPAPER)
  public abstract void setWallpaper(Bitmap bitmap) throws IOException;
  //allOf属性，表示需要集合中的所有属性;anyOf 属性
  @RequiresPermission(allOf = {
      Manifest.permission.READ_EXTERNAL_STORAGE,
      Manifest.permission.WRITE_EXTERNAL_STORAGE})
  public static final void copyFile(String dest, String source) {
      ...
  }
  //权限要求添加到定义 intent 操作名称的字符串字段上
  @RequiresPermission(android.Manifest.permission.BLUETOOTH)
  public static final String ACTION_REQUEST_DISCOVERABLE =
              "android.bluetooth.adapter.action.REQUEST_DISCOVERABLE";
  //单独读写权限
  @RequiresPermission.Read(@RequiresPermission(READ_HISTORY_BOOKMARKS))
  @RequiresPermission.Write(@RequiresPermission(WRITE_HISTORY_BOOKMARKS))
  public static final Uri BOOKMARKS_URI = Uri.parse("content://browser/bookmarks");
  ```

  间接权限，权限依赖于提供给方法参数的特定值

  ```java

  public abstract void startActivity(@RequiresPermission Intent intent, @Nullable Bundle) {...}
  ```

* 返回值注解

  提醒开发者处理方法的返回值

  ```java
  //确保实际引用方法的返回值，并且向开发者建议采用enforcePermission替换方法
  @CheckResult(suggest="#enforcePermission(String,int,int,String)")
  public abstract int checkPermission(@NonNull String permission, int pid, int uid);
  ```

* CallSuper注解

  ```java
  //确保重写的方法调用父方法
  @CallSuper
  protected void onCreate(Bundle savedInstanceState) {
  }
  ```

* Typedef

  Typedef 注解可以确保特定参数、返回值或字段引用特定的常量集。

  ```java
  //告知编译器不将枚举的注解数据存储在 .class 文件
  @Retention(RetentionPolicy.SOURCE)
  @IntDef({NAVIGATION_MODE_STANDARD, NAVIGATION_MODE_LIST, NAVIGATION_MODE_TABS})
  //声明新的枚举注解类型
  public @interface NavigationMode {}

  // Declare the constants
  public static final int NAVIGATION_MODE_STANDARD = 0;
  public static final int NAVIGATION_MODE_LIST = 1;
  public static final int NAVIGATION_MODE_TABS = 2;

  // Decorate the target methods with the annotation
  @NavigationMode
  public abstract int getNavigationMode();

  // 如果mode不是上述定义的常量，则会生成警告
  public abstract void setNavigationMode(@NavigationMode int mode);
  ```

* 代码访问性

  `@Keep` 确保在构建时，标注的元素不会移除，一般用于通过反射访问的方法和类。

  ​