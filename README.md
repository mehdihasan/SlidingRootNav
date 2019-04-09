# SlidingRootNavKotlin

The library is a DrawerLayout-like ViewGroup, where a "drawer" is hidden under the content view, which can be shifted to make the drawer visible. It doesn't provide you with a drawer builder.
The original library has been written by [yarolegovich](https://github.com/yarolegovich) in Java. To use the library in some of my projects, it was needed some modification and Kotlin conversion. 

![GifSample](art/sample.gif)

## Gradle 
Add this into your dependencies block.
```
implementation 'com.iglyphic:slidingrootnav:1.0.1'
```
## Sample
Please see the [sample app](https://github.com/mehdihasan/SlidingRootNav/tree/master/sample) for a library usage example.

## Wiki
#### Usage:
 1. Create your content_view.xml ([example](sample/src/main/res/layout/activity_main.xml)) or construct a `View` programatically.
 2. Set the content view (for example, using `setContentView` in your activity).
 3. Create your menu.xml ([example](sample/src/main/res/layout/menu_left_drawer.xml)) or construct a `View` programatically.
 4. Now you need to inject the menu in your `onCreate`. You can specify transformations of a content view or use the default ones. 
```kotlin
SlidingRootNavBuilder(this)
  .withMenuLayout(R.layout.menu_left_drawer)
  .inject()
```

### API
#### Transformations
You can specify root transformations using `SlidingRootNavBuilder`.
```kotlin
SlidingRootNavBuilder(this)
  .withDragDistance(140) //Horizontal translation of a view. Default == 180dp
  .withRootViewScale(0.7f) //Content view's scale will be interpolated between 1f and 0.7f. Default == 0.65f;
  .withRootViewElevation(10) //Content view's elevation will be interpolated between 0 and 10dp. Default == 8.
  .withRootViewYTranslation(4) //Content view's translationY will be interpolated between 0 and 4. Default == 0
  .addRootTransformation(customTransformation)
  .inject()
```
`customTransformation` in the above example is a user-created class that implements `RootTransformation` interface. For an example, refer to the [default transformations](library/src/main/java/com/mehdihasan/slidingrootnav/transform). 

#### Menu behavior
```kotlin
SlidingRootNavBuilder(this)
  .withMenuOpened(true) //Initial menu opened/closed state. Default == false
  .withMenuLocked(false) //If true, a user can't open or close the menu. Default == false.
  .withGravity(SlideGravity.LEFT) //If LEFT you can swipe a menu from left to right, if RIGHT - the direction is opposite. 
  .withSavedState(savedInstanceState) //If you call the method, layout will restore its opened/closed state
  .withContentClickableWhenMenuOpened(isClickable) //Pretty self-descriptive. Builder Default == true
```
#### Controling the layout
A call to `inject()` returns you an interface for controlling the layout.
```kotlin
interface SlidingRootNav {
    val isMenuClosed(): Boolean
    val isMenuOpened(): Boolean
    val isMenuLocked(): Boolean
    val layout: SlidingRootNavLayout //If for some reason you need to work directly with layout - you can
    fun closeMenu()
    fun closeMenu(animated: Boolean)
    fun openMenu()
    fun openMenu(animated: Boolean)
}
```

#### Callbacks
* Drag progress:
```kotlin
builder.addDragListener(listener)

interface DragListener {
  fun onDrag(progress: Float) //Float between 0 and 1, where 1 is a fully visible menu
}

```
* Drag state changes:
```kotlin
builder.addDragStateListener(listener);

interface DragStateListener {
  fun onDragStart()
  fun onDragEnd(isMenuOpened: Boolean)
}
```

* Compatibility with `DrawerLayout.DrawerListener`:
```kotlin
val adapter = DrawerListenerAdapter(yourDrawerListener, viewToPassAsDrawer)
builder.addDragListener(listenerAdapter).addDragStateListener(listenerAdapter)
```

