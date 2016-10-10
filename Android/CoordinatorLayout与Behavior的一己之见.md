# ǰ��

������¶��ǽ�`CoordinatorLayout`��`AppbarLayout`��`CollapsingToolbarLayout`��`Toolbar`�ȷ���һ����ܣ��������Ϊ�⼸������һ��Ҫ������䣬�ҽ�����������Щ�����С�
��ʵ��Ȼ����������Ҫ����`CoordinatorLayout`���Ұ�����ΪЭ�����֡�Э��ʲô�����أ���Ȼ��Ƕ�������ڲ��� Child View��
`CoordinatorLayout`�䵱��һ���м��Ľ�ɫ��һ�߽�������������¼���һ�߽����յ����¼�֪ͨ���ڲ������������
`Behavior`����`CoordinatorLayout`�����¼���ý�飬`Behavior` ������ `CoordinatorLayout` ��**ֱ���� View?**����Ϊ�淶�������˵��յ���ͬ�¼�ʱ��Ӧ���������Ĵ���
�ܽ���˵��`Behavior`�������������¼�������´�����������ͼ��

![](https://github.com/ghnor/TechNote/assets/images/behavior-flux.png)

�¼�������ܸ���Ī�������...���ټ�һ���˵����`CoordinatorLayout`�е�ĳ����ĳ������������������ã�֮��`CoordinatorLayout`�ٵ���`Behavior`�е�ĳ����ĳ����������=��=����������ˣ�����֮�����������¼������������и�ӡ��Ϳ����ˡ�

�����Ƚ���һ���Զ���Behavior��ͨ�����̡�Ϊʲô��ͨ�������أ���Ϊ�����ᵽ���������¼��������ݲ�ͬ���¼�������Ҫ��д��ͬ�ķ����ģ���������һһ˵����
# �Զ���Behavior��ͨ������
**1. ��д���췽��**
```java
public class CustomBehavior extends CoordinatorLayout.Behavior {

    public CustomBehavior(Context context, AttributeSet attrs) {
        super(context, attrs);
    }
}
```
һ��Ҫ��д������췽������Ϊ������XML�����ø�`Behavior`ʱ���� `CoordinatorLayout` �лᷴ����ø÷����������ɸ� `Behavior` ʵ����
**2. �󶨵�View**
�󶨵ķ��������֣�
�� XML �ļ��У��������� View ������
```java
app:layout_behavior="���Behavior�İ�·��������"
```
�����ڴ����У�
```java
(CoordinatorLayout.LayoutParams)child.getLayoutParams().setBehavior();
```
�ٻ��ߵ����View���Զ����Viewʱ��
������Զ���View�������@DefaultBehavior(���Behavior.class)��
```java
@DefaultBehavior(CustomBehavior.class)
public class CustomView extends View {}
```

**3. �ж���������**
�� `CoordinatorLayout` �յ�ĳ�� view �ı仯����Ƕ�׻����¼�ʱ��`CoordinatorLayout`�ͻ᳢�԰��¼��·���`Behavior`�����˸� `Behavior` �� view �ͻ���¼�������Ӧ��

���������������������Ĺ�ϵ��view��`Behavior`�����е��β�����������߷ֱ棺
�����仯��Ҳ���ǰ���`Behavior`��view��Ϊ`child`
�����仯��view�ڡ��仯�¼����г�Ϊ`dependency`���ڡ�Ƕ�׻����¼����г�Ϊ`target`��

��Ϊ���ܻ���ںܶ��Child View������`CoordinatorLayout`������Ϣ��Ҳͬʱ���ںܶ��Child Viewӵ���Ų�ͬ��`Behavior`����ô��`CoordinatorLayout`���������¼����ݽ����`Behavior`֮ǰ���϶���Ҫһ����������֪`CoordinatorLayout`�����ߵ�������ϵ�Ƿ�����������ϵ��������ô�Ͱ��¼��·����㣬�����ϵ�����������۾͵���over��
�����ԡ��仯�¼�����`layoutDependsOn`˵�������ӣ���Ƕ�׻����¼�������`onStartNestedScroll`����ͬ�����жϡ���������֡������¼����������¼�����û����һ���ˡ�
**a.����id**
```java
@Override
public boolean layoutDependsOn(CoordinatorLayout parent, View child, View dependency) {
    return dependency.getId() == R.id.xxx;
}
```
**b.��������**
```java
@Override
public boolean layoutDependsOn(CoordinatorLayout parent, View child, View dependency) {
	return dependency instanceof CustomView;
}
```
**c.����id����һ��д��**
```java
<declare-styleable name="Follow">
	<attr name="target" format="reference"/>
</declare-styleable>
```
���Զ���target������ԡ�
```java
<android.support.design.widget.CoordinatorLayout    
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true"
    tools:context=".MainActivity">

    <View
        android:id="@+id/first"
        android:layout_width="match_parent"
        android:layout_height="128dp"
        android:background="@android:color/holo_blue_light"/>

    <View
        android:id="@+id/second"
        android:layout_width="match_parent"
        android:layout_height="128dp"
        app:layout_behavior=".FollowBehavior"
        app:target="@id/first"
        android:background="@android:color/holo_green_light"/>

</android.support.design.widget.CoordinatorLayout>
```
```java
public class FollowBehavior extends CoordinatorLayout.Behavior {
	private int targetId;

	public FollowBehavior(Context context, AttributeSet attrs) {
		super(context, attrs);
		TypedArray a = context.obtainStyledAttributes(attrs, R.styleable.Follow);
		for (int i = 0; i < a.getIndexCount(); i++) {
			int attr = a.getIndex(i);
			if(a.getIndex(i) == R.styleable.Follow_target){
				targetId = a.getResourceId(attr, -1);
			}
		}
		a.recycle();
	}

	@Override
	public boolean onDependentViewChanged(CoordinatorLayout parent, View child, View dependency) {

		return true;
	}

	@Override
	public boolean layoutDependsOn(CoordinatorLayout parent, View child, View dependency) {
		return dependency.getId() == targetId;
	}
}
```

# ���ֲ�ͬ���¼���

## 1. �����¼�
TouchEvent ����Ҫ�ķ�������������
```java
public boolean onInterceptTouchEvent(MotionEvent ev)
public boolean onTouchEvent(MotionEvent ev)
```
�� `CoordinatorLayout` �� `onInterceptTouchEvent` �� `onTouchEvent` �����У��᳢�Ե����� Child View ӵ�е� `Behavior` �е�ͬ��������
```java
public boolean onInterceptTouchEvent(CoordinatorLayout parent, View child, MotionEvent ev)
public boolean onTouchEvent(CoordinatorLayout parent, View child, MotionEvent ev)
```
��� `Behavior` �Դ����¼����������أ��Ͳ����ٷַ��� Child View ����ӵ�еĴ����¼��С�
�����ζ�ţ�**�ڲ�֪������View������£��Ϳ�����д���Ĵ����¼���**
Ȼ����һ��������Ҫע�⵽���ǣ�**onTouch�¼���CoordinatorLayout�ַ������ģ����������onTouchEvent���������ǿؼ��Լ���onTouch�¼�**��Ҳ����˵���������ָ�������ǵĿؼ��ϻ�����Ҳ�ᴥ��onTouchEvent��
��Ҫ��`onTouchEvent`�����е�`MotionEvent.ACTION_DOWN`����ӣ�
```java
ox = ev.getX();
oy = ev.getY();
if (oy < child.getTop() || oy > child.getBottom() || ox < child.getLeft() || ox > child.getRight()) { 
	return true;
}
```
�����Ƶ�λ�ý��й��ˣ��������ǿؼ���Χ�ڵģ���������
## 2. �����¼�
��ͼ�����޷Ǿ���������������
```java
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec)
protected void onLayout(boolean changed, int l, int t, int r, int b)
```
�� `CoordinatorLayout` �� `onMeasure ` �� `onLayout` �����У�Ҳ�᳢�Ե����� Child View ӵ�е� `Behavior` �ж�Ӧ�ķ������ֱ��ǣ�
```java
public boolean onMeasureChild(CoordinatorLayout parent, V child, int parentWidthMeasureSpec, int widthUsed,
								int parentHeightMeasureSpec, int heightUsed)
public boolean onLayoutChild(CoordinatorLayout parent, V child, int layoutDirection)
```
ͬ���أ�`CoordinatorLayout` �����ȴ��� `Behavior` ������д�Ĳ����¼���

## 3. �仯�¼�
����仯��ָ View ��λ�á��ߴ緢���˱仯��
�� `CoordinatorLayout` �� `onDraw` �����У������ȫ���� Child View ����Ѱ���Ƿ����໥�����Ķ���
ȷ���Ƿ�����ķ�ʽ�����֣�
**1. Behavior�ж���**
ͨ�� `Behavior` �� `layoutDependsOn` �������ж��Ƿ���������ϵ������оͼ������� `onDependentViewChanged`��FloatActionButton ������ Snackbar ����ʱ�������ƾ�ͨ���÷�ʽʵ�֡�
```java
/**
 * �ж���dependency�Ƿ��ǵ�ǰbehavior��Ҫ�Ķ���
 * @param parent CoordinatorLayout
 * @param child ��Behavior��Ӧ���Ǹ�View
 * @param dependency dependency Ҫ����View(child�Ƿ�Ҫ�������dependency)
 * @return true ����, false ������
 */
@Override
public boolean layoutDependsOn(CoordinatorLayout parent, Button child, View dependency) {
	return false;
}

/**
 * ���ı�dependency�ĳߴ����λ��ʱ������
 * @param parent CoordinatorLayout
 * @param child  ��Behavior��Ӧ���Ǹ�View
 * @param dependency child����dependency
 * @return true ������, false û����
 */
@Override
public boolean onDependentViewChanged(CoordinatorLayout parent, Button child, View dependency) {
	return false;
}

/**
 * ��layoutDependsOn����true�Ļ�����֮��֪ͨdependency���Ƴ���
 * @param parent CoordinatorLayout
 * @param child ��Behavior��Ӧ���Ǹ�View
 * @param dependency child����dependency
 */
@Override
public void onDependentViewRemoved(CoordinatorLayout parent, Button child, View dependency) {
	
}
```
**2. XML����������**
ͨ�� XML �����õ� `layout_anchor`������������ `layout_anchor` �� Child View �� `layout_anchor` ��Ӧ��Ŀ�� dependency View�������� `offsetChildToAnchor(child, layoutDirection);`����ʵ���ǵ������ߵ�λ�ã������ǿ���һ��仯��FloatActionButton ���Ը��� Toolbar �����ƶ����Ǹ÷�ʽʵ�֡�
```java
app:layout_anchor="@id/dependencyView.id"
```

## 4. Ƕ�׻����¼� 
**ʵ��NestedScrollingChild**
���һ��View������紫�ݻ����¼�����֪ͨ NestedScrollingParent?���ͱ���ʵ�ִ˽ӿڡ�
�� Child �� Parent �ľ��彻���߼���?[NestedScrollingChildHelper](https://developer.android.com/reference/android/support/v4/view/NestedScrollingChildHelper.html) ����������Ѿ������Ƿ�װ���ˣ���������ֻ��Ҫ���ö�Ӧ�ķ������ɡ�
NestedScrollingChild�ӿڵ�һ��ʵ�֣�
```java
public class CustomNestedScrollingChildView extends View implements NestedScrollingChild {

    private NestedScrollingChildHelper mChildHelper = new NestedScrollingChildHelper(this);

    /**
     * ���õ�ǰView�ܷ񻬶�
     * @param enabled
     */
    @Override
    public void setNestedScrollingEnabled(boolean enabled) {
        mChildHelper.setNestedScrollingEnabled(enabled);
    }

    /**
     * �жϵ�ǰView�ܷ񻬶�
     * @return
     */
    @Override
    public boolean isNestedScrollingEnabled() {
        return mChildHelper.isNestedScrollingEnabled();
    }

    /**
     * ����Ƕ�׻����¼���
     * 1. Ѱ�ҿ��Խ��� NestedScroll �¼��� parent view����ʵ���� NestedScrollingParent �ӿڵ� ViewGroup
     * 2. ֪ͨ�� parent view��������Ҫ�ѻ����Ĳ������ݸ���
     * @param axes
     * @return
     */
    @Override
    public boolean startNestedScroll(int axes) {
        return mChildHelper.startNestedScroll(axes);
    }

    /**
     * ֹͣǶ�׻����¼���
     */
    @Override
    public void stopNestedScroll() {
        mChildHelper.stopNestedScroll();
    }

    /**
     * �Ƿ���ڽ��� NestedScroll �¼��� parent view
     * @return
     */
    @Override
    public boolean hasNestedScrollingParent() {
        return mChildHelper.hasNestedScrollingParent();
    }

    /**
     * �ڻ���֮����view�㱨�������������child view���ѵĲ��ֺ�child viewû�����ѵĲ��֡�
     * @param dxConsumed x���������ѵĻ�������
     * @param dyConsumed y���������ѵĻ�������
     * @param dxUnconsumed x����δ���ѵĻ�������
     * @param dyUnconsumed y����δ���ѵĻ�������
     * @param offsetInWindow ���parent view��������child view�Ĵ��ڷ����˱仯��child View��λ�÷����˱仯��
     *                       �ò�������x(offsetInWindow[0]) y(offsetInWindow[1])����ı仯
     *                       ������¼����ָ����λ�ã���Ҫ���ݲ���offsetInWindow����ƫ������
     *                       ���ܱ�֤��һ�ε�touch�¼��ļ�������ȷ�ġ�
     * @return ���parent view���������Ĺ��������������˲������ѣ��������������true������Ϊfalse��
     */
    @Override
    public boolean dispatchNestedScroll(int dxConsumed, int dyConsumed, int dxUnconsumed,
                                        int dyUnconsumed, int[] offsetInWindow) {
        return mChildHelper.dispatchNestedScroll(dxConsumed, dyConsumed, dxUnconsumed, dyUnconsumed,
                offsetInWindow);
    }

    /**
     * �ڻ���֮ǰ������һ�� parent view �Ƿ���Ҫ������
     * ��child view��onInterceptTouchEvent��onTouchEvent�����е��á�
     * 1. ���parent view������һ�����룬����Ҫ���¼���һ��parent view������ʣ�¸���Ļ�������ʣ������
     *      Ȼ���Լ�����ʣ��Ļ�����
     * 2. �÷����ĵ������ĸ���������parent view���ѵ��Ļ��������child view�Ĵ���ƫ������
     *      ������¼����ָ����λ�ã���Ҫ���ݵ��ĸ�����offsetInWindow����ƫ������
     *      ���ܱ�֤��һ�ε�touch�¼��ļ�������ȷ�ġ�
     * @param dx x����Ļ�������
     * @param dy y����Ļ�������
     * @param consumed �������null, �����child view����parent view�����������
     *                 consumed[0]parent view����child viewˮƽ���򻬶��ľ���(dx)
     *                 consumed[1]parent view����child view��ֱ���򻬶��ľ���(dy)
     * @param offsetInWindow ��ѡ length=2 �����飬
     *                       ���parent view��������child View�Ĵ��ڷ����˱仯����View��λ�÷����˱仯��
     *                       �ò�������x(offsetInWindow[0]) y(offsetInWindow[1])����ı仯
     *                       ������¼����ָ����λ�ã���Ҫ���ݲ���offsetInWindow����ƫ������
     *                       ���ܱ�֤��һ�ε�touch�¼��ļ�������ȷ�ġ�
     * @return ���parent view�Ի�����������˲������ѣ��������������true������Ϊfalse��
     */
    @Override
    public boolean dispatchNestedPreScroll(int dx, int dy, int[] consumed, int[] offsetInWindow) {
        return mChildHelper.dispatchNestedPreScroll(dx, dy, consumed, offsetInWindow);
    }

    /**
     * ��Ƕ�׻�����child view���ٻ���֮���ٵ��øú�����parent view�㱨���ٻ��������
     * @param velocityX ˮƽ������ٶ�
     * @param velocityY ��ֱ������ٶ�
     * @param consumed true ��ʾchild view���ٻ�����, false ��ʾchild viewû�п��ٻ���
     * @return true ��ʾparent view���ٻ�����, false ��ʾparent viewû�п��ٻ���
     */
    @Override
    public boolean dispatchNestedFling(float velocityX, float velocityY, boolean consumed) {
        return mChildHelper.dispatchNestedFling(velocityX, velocityY, consumed);
    }

    /**
     * ��Ƕ�׻�����child view���ٻ���֮ǰ����parent view���ٻ����������
     * @param velocityX ˮƽ������ٶ�
     * @param velocityY ��ֱ������ٶ�
     * @return true ��ʾparent view���ٻ�����, false ��ʾparent viewû�п��ٻ���
     */
    @Override
    public boolean dispatchNestedPreFling(float velocityX, float velocityY) {
        return mChildHelper.dispatchNestedPreFling(velocityX, velocityY);
    }
```
**ʵ��NestedScrollingParent**
���һ��View Group��������� NestedScrollingChild �Ļ����¼�������Ҫʵ�ָýӿڡ�
ͬ����һ��?[NestedScrollingParentHelper
](https://developer.android.com/reference/android/support/v4/view/NestedScrollingParentHelper.html) �����࣬�����Ƿ�װ���� parent view �� child view֮��ľ��彻���߼���
�� NestedScrollingChild �������������¼����ݸ� NestedScrollingParent��NestedScrollingParent ������Ӧ�� 
֮��ĵ��ù�ϵ���±���ʾ��

|Child View|Parent View|
|---------|---------|
|  startNestedScroll  |  onStartNestedScroll��onNestedScrollAccepted  |
|  dispatchNestedPreScroll  |  onNestedPreScroll  |
|  dispatchNestedScroll  |  onNestedScroll  |
|  stopNestedScroll  |  onStopNestedScroll  |
|  dispatchNestedFling  |  onNestedFling  |
|  dispatchNestedPreFling  |  onNestedPreFling  |

**�̳�Behavior**
�������˵�����ᵽ Parent View ������һ���ֻ�ȫ���Ļ������룬����ʵ�󲿷�����£����ǵ� Parent View �����������ѻ������룬���Ǵ��ݸ� `Behavior`��Ҳ����ӵ����� `Behavior` �� Child View �����������ѻ��������ʵ����
`Behavior` ӵ���� `NestedScrollingParent?` �ӿ���ȫͬ���ķ�������ÿһ�� `NestedScrollingParent?` �ķ����ж������ `Behavior` �е�ͬ��������
����ô�������������ر�˵����
```java
/**
 * ��ʼǶ�׻�����ʱ�򱻵���
 * 1. ��Ҫ�жϻ����ķ����Ƿ���������Ҫ�ġ�
 *      nestedScrollAxes == ViewCompat.SCROLL_AXIS_HORIZONTAL ��ʾ��ˮƽ����Ļ���
 *      nestedScrollAxes == ViewCompat.SCROLL_AXIS_VERTICAL ��ʾ����ֱ����Ļ���
 * 2. ���� true ��ʾ�������պ����Ļ����¼������� false ��ʾ���ٽ��պ��������¼�
 */
@Override
public boolean onStartNestedScroll(CoordinatorLayout coordinatorLayout, View child,
								   View directTargetChild, View target, int nestedScrollAxes) {
}

/**
 * �����е���
 * 1. �����ϻ���dyConsumed > 0 && dyUnconsumed == 0
 * 2. �Ѿ��������˻����ϻ���dyConsumed == 0 && dyUnconsumed > 0
 * 3. �����»���dyConsumed < 0 && dyUnconsumed == 0
 * 4. �Ѿ���ײ��˻����»���dyConsumed == 0 && dyUnconsumed < 0
 */
@Override
public void onNestedScroll(CoordinatorLayout coordinatorLayout, View child, View target,
						   int dxConsumed, int dyConsumed, int dxUnconsumed, int dyUnconsumed) {
}

/**
 * ���ٻ����е���
 */
@Override
public boolean onNestedFling(CoordinatorLayout coordinatorLayout, View child, View target,
							 float velocityX, float velocityY, boolean consumed) {
}
```

# �ܽ�
�ܽ�һ���������¼������͸�����Ҫʵ�ֵķ�����
������`�Զ���Behavior`ʱ�Ƿ���Ҫ�ж�������ϵ����`Behavior`�������������ֳ����ࣺ
�¼������ⲿ��view��
1.�����¼���`Behavior`�� `onMeasureChild`+`onLayoutChild`
2.�����¼���`Behavior`��`onInterceptTouchEvent`+`onTouchEvent`
�¼������ڲ���view��
3.view�仯�¼���`Behavior`��`layoutDependsOn`+`onDependentViewChanged`+`onDependentViewRemoved`
4.Ƕ�׻����¼���`Behavior`��`onStartNestedScroll`+`onNestedScrollAccepted`+`onStopNestedScroll`+`onNestedScroll`+
`onNestedPreScroll`+`onNestedFling`+`onNestedPreFling`

# ���
֮ǰ��Google���ٶ�`�Զ���Behavior`�����ӵ�ʱ�򣬸տ�ʼ��һƪ�����ò�����ˣ�����ô�㶫�����ٿ�һƪ����~ʵ����ô�ֲ�һ���ˣ�����һƪ�ֲ�һ���ˡ�
���ľ�������һ����ٵ����ã���������ô�죬������ôЩ���������Ժ��ٿ����˵����ӻ����Լ������ӵ�ʱ�򣬿�������һЩ��

# ��չ
[sidhu���е�CoordinatorLayout.Behavior��һ��](https://segmentfault.com/a/1190000006657044)
[sidhu���е�CoordinatorLayout.Behavior������](https://segmentfault.com/a/1190000006665225)
[sidhu���е�CoordinatorLayout.Behavior������](https://segmentfault.com/a/1190000006666005)
[Material Designϵ�У��Զ���Behavior֧������View](http://blog.csdn.net/yanzhenjie1003/article/details/52205665)
[CoordinatorLayout��ʹ����˼�](http://blog.csdn.net/huachao1001/article/details/51554608)