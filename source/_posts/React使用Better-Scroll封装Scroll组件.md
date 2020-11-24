---
title: React使用Better-Scroll封装Scroll组件
date: 2018-04-11 20:06:20
tags: 
  - React
---
#React使用Better-Scroll封装Scroll组件

**better-scroll**是一个移动端滚动插件，基于iscroll重写的。普通的网页滚动效果是很死板的，better-scroll具有拉伸、回弹的效果并且滚动的时候具有惯性，很接近原生体验。better-scroll更多相关内容见[github地址](https://github.com/ustbhuangyi/better-scroll)
相信很多人在vue中都用过better-scroll，因为better-scroll的作者很好的把它运用在了vue中，几乎一说到better-scroll大家就会想到vue(2333~~~)。其实better-scroll是利用原生js编写的，所以在所有使用原生js的框架中几乎都能使用它，这里我将在React中的运用better-scroll
首先在**src**目录下新建一个**common**目录用来存放公用的组件，新建**scroll**文件夹，然后在scroll文件夹下新建**Scroll.js**和**scroll.scss文件**。先来分析一下怎么设计这个**Scroll**组件，better-scroll的原理就是外层一个固定高度的元素，这个元素有一个子元素，当子元素的高度超过父元素时就可以发生滚动，那么子元素里面的内容从何而来？React为我们提供了一个props的**children**属性用来获取组件的子组件，这样就可以用**Scroll**组件去包裹需要滚动的内容。在**Scroll**组件内部的列表，会随着增加或减少原生而发生变化，这个时候元素的高度也会发生变化，better-scroll需要重新计算高度，better-scroll为我们提供了一个**refresh**方法用来重新计算以保证正常滚动，组件发生变化会触发React的**componentDidUpdate**周期函数，所以我们在这个函数里面对better-scroll进行刷新操作，同时需要一个props来告诉Scroll是否**refresh**刷新。某些情况下我们需要手动调用Scroll组件去刷新better-scroll，这里对外暴露一个Scroll组件的方法。better-scroll默认是禁止点击的，需要提供一个控制是否点击的props，为了监听滚动Scroll需要对外暴露一个函数，便于使用Scroll的组件监听滚动进行其他操作。当组件销毁时我们把better-scroll绑定的事件取消以及better-scroll实例给销毁掉，释放资源

安装better-scroll

```
npm install better-scroll --save
```

对组件的props进行类型检查，这里使用**prop-types**库。类型检查是为了提早发现开发问题，避免一些bug产生

安装props-types

```
npm install prop-types --save
```

编写**Scroll**组件



`scroll.js`



```
import React from "react"
import ReactDOM from "react-dom"
import PropTypes from "prop-types"
import BScroll from "better-scroll"
import "./scroll.styl"

class Scroll extends React.Component {
    componentDidUpdate() {
        //组件更新后，如果实例化了better-scroll并且需要刷新就调用refresh()函数
        if (this.bScroll && this.props.refresh === true) {
            this.bScroll.refresh();
        }
    }
    componentDidMount() {
        this.scrollView = ReactDOM.findDOMNode(this.refs.scrollView);
        if (!this.bScroll) {
            this.bScroll = new BScroll(this.scrollView, {
                //实时派发scroll事件
                probeType: 3,
                click: this.props.click
            });

            if (this.props.onScroll) {
                this.bScroll.on("scroll", (scroll) => {
                    this.props.onScroll(scroll);
                });
            }

        }
    }
    componentWillUnmount() {
        this.bScroll.off("scroll");
        this.bScroll = null;
    }
    refresh() {
        if (this.bScroll) {
            this.bScroll.refresh();
        }
    }
    render() {
        return (
            <div className="scroll-view" ref="scrollView">
                {/*获取子组件*/}
                {this.props.children}
            </div>
        );
    }
}

Scroll.defaultProps = {
    click: true,
    refresh: false,
    onScroll: null
};

Scroll.propTypes = {
    //是否启用点击
    click: PropTypes.bool,
    //是否刷新
    refresh: PropTypes.bool,
    onScroll: PropTypes.func
};

export default Scroll
```

上诉代码中**ref**属性来标记div元素，使用**ReactDOM.findDOMNode**函数来获取dom对象，然后传入better-scroll构造函数中初始化。在Scroll组件中调用外部组件的方法只需要把外部组件的函数通过props传入即可，这里就是**onScroll**函数



`scroll.scss`



```
.scroll-view {
    width: 100%;
    height: 100%;
    overflow: hidden;
}
```



scroll.styl中就是一个匹配父容器宽高的样式



接下来在Recommend组件中加入Scroll组件，导入Scroll组件



```
import Scroll from '@/common/scroll/Scroll'
```



在state中增加**refreshScroll**用来控制Scroll组件是否刷新



```
this.state = {
    refreshScroll: false
};
```



使用**Scroll**组件包裹需要的内容



```
<Scroll refresh={this.state.refreshScroll}>
	// 我是内容啊
</Scroll>
```

