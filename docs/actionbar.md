# ActionBar设置
主要功能集中在ActionBarUtil这个类，在BaseActivity进行了调用。
## 设置显示或隐藏ActionBar
- 在activity里调用如下方法:

	```java
	showActionBar();
	hideActionBar();
	```
     
## 设置title
- 如下方法，重写了多个方法，可以传参数字符串、resouceId、点击监听器、颜色、字体大小等。

	```java
	setTitle();	
	```

## 设置返回按钮
- 通过`setBackIcon`方法，传入点击事件，或者null。

	```java
	setBackIcon(new View.OnClickListener() {
          @Override
          public void onClick(View v) {
              
          }
      });
    }
	```

## 设置Actionbar颜色
- 通过`setActionbarBackground`方法。
	```java
	 setActionbarBackground();	
	```

## 设置右边选项菜单
- 只有一个按钮的选项菜单，点击直接跳转

	```java
 	RightMenu rightMenu = new RightMenu();
    rightMenu.setIcon("历史");
    rightMenu.setSize(13);
    setRightMenu(rightMenu, new OnMenuClickListener() {
        @Override
        public void onMenuClick(int id) {
            Bundle extra = new Bundle();
            extra.putString("eid", mEid);
            extra.putString("address", mAddress);
            navigateTo(BCLWalletConstants.ROUTER.BCLWALLETMODULE_BLUETOOTHHIS, extra);
        }
    });
	```

- 选项菜单里有多个菜单，如果填充两个菜单，则直接展示在actionbar上，如果填充大于2个菜单，则在actionbar上展示加号，点击加号展开一个列表，点击各个列表里元素执行自定义的操作。

	```java
	 List<RightMenu> list = new ArrayList();
     RightMenu menu1 = new RightMenu();
     menu1.setIcon("\ue6a4");
     menu1.setTitle("shanchu1");
     menu1.setSizeId(R.dimen.text_size_10);
     menu1.setExpandable(false);
     OnMenuClickListener onMenuClickListener = new OnMenuClickListener() {
         @Override
         public void onMenuClick(int id) {
             ToastUtil.normal("menuClicked,  id =   "+id);
         }
     };
     list.add(menu1);
     //这里同样往list里添加menu2、menu3...
      setRightMenu(list,onMenuClickListener);
	```

	对于以上代码的说明：
	+ 右上角菜单在Actionbar上最多只能显示2个菜单按钮。可以设置一个RightMenu设置一个菜单，也可以设置一个Rightmenu的
集合来进行设置多个菜单，多个菜单具体情况下面详细描述。

	+ 当设置为list时经常需要将菜单折叠起来，setExpandable为设置右侧菜单是否可折叠。如果为true，则会折叠到加号里能够展开，如果为false，会直接展示在actionbar上面，不会折叠到加号里。不可折叠的菜单直接显示在actionbar右上角。如果加号存在，因为加号占用了一个菜单的位置，只剩下一个位置了，你设置的Rightmenu里的isExpandable属性最多只可能有一个false会生效，其它自动变成true。如果右侧只有两个菜单条目，则都能设置expandable为false，这样这两个菜单都可以直接展示在actionbar上。

	+ setIcon和setTitle为设置右侧的图标和文字。图片和文字可以展示在popupwindow里，图标可以展示在actionbar上，如果图标
直接设置的汉字，则最多展示3个字。
	+ setSizeId可以设置icon和title的文字大小，RightMenu展示在popupwindow里时，设置文字大小不会生效，只有设置在actionbar
上时才会生效。
	+ OnMenuClickListener，menu的点击事件。根据你传RightMenu的顺序，生成id，从0开始一直到list.size()-1。根据id我们可以
判断点击的是哪个菜单，实现自己的逻辑。如果传了url，则会自动触发url的跳转到webview，不管点击事件里有没有写东西。







