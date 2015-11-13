##目录

[LinearLayout](#linear_layout)

<a name="linear_layout"/>
### LinearLayout

LinearLayout 代表线性布局，它会将容器里的组件一个接一个的排列起来。通过 LinearLayout 的 `android:orientation="horizontal/vertical"` 属性可以指定水平排列还是垂直排列。

当线性布局水平排列组件时，它不会自动换行，当组件超出屏幕宽度时，超出的组件将不可见。

**常用属性：**

* android:orientation

	>布局内元素的排列方式，水平排列(horizontal) 或 垂直排列(vertical)。

* android:layout_width
* android:layout_height

	>这2个属性可以控制 LinearLayout 的大小，可取值有 `fill_parent` 、 `match_parent` 、`wrap_content` 、`100dp`。

	>`fill_parent` 与 `match_parent` 效果相同，都会使 LinearLayout 完全填充其父元素，这就会导致该布局后面的元素无法显示。

	>`wrap_content` 使布局正好包裹它里面的内容。

	>`100dp` 指定布局的大小。

* android:gravity

	>指定布局内元素的对齐方式，可取值比较多，常用的有:
	>
	>* top : 屏幕左上角
	>* bottom : 屏幕左下角
	>* left : 屏幕左上角
	>* right : 屏幕右上角
	>* center : 屏幕正中间
	>* center_horizontal : 上边中间
	>* center_vertical : 左边中间

* android:layout_gravity

	>指定当前元素本身在其父元素中的布局方式