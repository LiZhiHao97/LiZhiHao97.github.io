---
title: React最佳实践（二）：ref的正确使用姿势
date: 2022-08-04 11:35:04
index_img: https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/20220804113533.png
categories: 前端
tags:
  - Typescript
  - React
  - React Ref
---

## 前言

本系列用来记录 React 在工作学习中的实践，我写的内容一定存在不足之处，以及能够优化的地方，文章之所以取这个名字，旨在鞭策自己写出更优雅的代码，摈弃不好的习惯。

本文的例子基于`Ant Design`的`Modal`组件和`Table`组件，主要实现的功能是在`Modal`完成设置，点击确定按钮后，在执行一些事件的同时，并完成`Table`组件的更新(refresh)，完成一个联动效果。

## 比较

我们选用与上一篇同样的方法来学习，先选用一种较为不合理的写法（为了代码简洁，会省掉一些属性，比如`Table`组件的 columns 之类）

```tsx

const mockData = [
	{name: '蜘蛛侠'},
	{name: '钢铁侠'}
]

cosnt App = () => {
	const [data, setData] = useState([])
	const [visible, setVisible] = useState(false)

	const show = () => {
		setIsModalVisible(true);
	};

	const onOk = () => {
		setIsModalVisible(false);
		setData(mockData)
	};

	const onCancel = () => {
		setIsModalVisible(false);
	};

	return (
	    <>
	      <Button onClick={show}>
	        Open Modal
	      </Button>
	      <Table dataSource={data} />
	      <Modal visible={visible} onOk={onOk} onCancel={onCancel}>
		      <p>content</p>
	      </Modal>
	    </>
	);
}
```

实际上，这样已经差不多满足了我们需要的功能了。我们来思考一下这样有什么缺点呢？

首先，在真实场景中代码会复杂很多，我们将`Table`、`Modal`两个独立的个体写在了一起，增加了模块之间的`耦合性`，为什么说他们耦合了呢？

假如出现了这样一个场景，在两个不同的页面，我们都需要同样的`Modal`对一组数据进行操作（在上述代码中是`Table`的 data），其中一个页面是以`表格`形式展示，另一个页面是以`卡片`形式展示，那我们以上的代码就没法很好的复用了。

相信说到这里你就懂了，我们需要将`Table`、`Modal`独立封装成组件，我们将遵循一以下设计

- `data`仅由`Table`对其进行展示，所以应当封装在`Table`内部，但是外部组件的变动可能导致`Table`需要重新加载数据，所以`Table`需要向外部提供一个`refresh`方法，让外部能够手动刷新`Table`数据
- `visible`也是如此，该状态以及操作它的一系列函数仅在`Modal`中使用，所以封装到一个新的组件，该组件向外部提供一个`show`方法，让外部能够打开`Modal`窗口。

ok，我们来使用`ref`对代码进行实现。如果不熟悉`ref`的相关使用，可以学习 react 官网。

## 编码

我们自顶向下进行编程。

首先定义两个实例类型，分别为`MyTable`和`MyModal`的实例，清晰的表示了我们希望它们提供的能力。

```tsx
interface MyTableInstance {
  refresh: () => void;
}
interface MyModalInstance {
  show: () => void;
}
```

### APP

```tsx
// app.tsx

const App = () => {
  const tableRef = useRef<MyTableInstance | null>(null);
  const modalRef = useRef<MyModalInstance | null>(null);

  const showModal = () => {
    modalRef.current.show();
  };

  return (
    <>
      <Button onClick={showModal}>show</Button>
      <MyTable ref={tableRef} />
      <MyModal ref={modalRef} TableRef={tableRef} />
    </>
  );
};
```

ok，这样最外层的结构就完成了，`tableRef`和`modalRef`分别为，`MyTable`和`MyModel`的一个实例上的引用。

### MyModal

这里将`Modal`的状态`visible`封装成了一个`hook`。

```typescript
const useVisible = () => {
  const [visible, setVisible] = useState(false);

  const show = useCallback(() => {
    setVisible(true);
  }, []);

  const hide = useCallback(() => {
    setVisible(false);
  }, []);

  return {
    visible,
    hide,
    show,
  };
};

const MyModal = forwardRef<MyModalInstance, MymodalProps>(
	({ tableRef }, ref) => {
		const {visible, hide, show} = useVisible()

		useImperativeHandle(ref, () => ({
      show, // 直接将useVisible的show赋值给MyModal实例方法上的show
    }));

		cosnt onOk = async () => {
			await something()

			hide()
			tableRef.current.refresh()
		}

		const onCancel = () => {
			hide()
		}

		reutrn (
			<Modal visible={visible} onOk={onOk} onCancel={onCancel}>
				<p>content</p>
			</Modal>
		)
	}
);
```

> useImperativeHandle 可以让你在使用 ref 时自定义暴露给父组件的实例值。在大多数情况下，应当避免使用 ref 这样的命令式代码。useImperativeHandle 应当与 forwardRef 一起使用。
> ———摘自[React 官方文档](https://zh-hans.reactjs.org/docs/hooks-reference.html#useimperativehandle)

### MyTable

类似的，我们实现了一个`MyTable`组件

```tsx
const mockData = [{ name: "蜘蛛侠" }, { name: "钢铁侠" }];

const MyTable = forwardRef<MyModalInstance, MymodalProps>((props, ref) => {
  const [data, setData] = useState([]);

  useImperativeHandle(ref, () => ({
    refresh: () => {
      setData(mockData);
    },
  }));

  return <Table dataSource={data} />;
});
```

## 总结

至此，一个简单的联动实例就完成了，我们可以发现与之前相比

- 它们各自管理需要的状态，`data`在`MyTable`内部，`visible`在`Modal`内部
- 它们耦合非常低，可以在其他场景复用，`Modla`只调用了`Table`实例提供的能力，完全可以在其他不同`Table`的场景下复用

## 扩展

### ref 的其他应用

总结自一道面试题：

假设我们通过一个 service 异步获取数据，如果保证获取到的数据是最新的。

> 一个场景就是搜索联想，我们需要保证联想的内容是最新的。

可能产生 bug 的写法

```tsx
const App = () => {
  const [data, setData] = useState([]);

  useEffect(() => {
    for (let i = 0; i < 3; i++) {
      service.then((res) => {
        setData(res.data);
      });
    }
  }, []);

  return <RenderComponent data={data} />;
};
```

显然，`service`会发起三次请求，我们假设响应速度为`1 3 2`，那么`data`最终就会被设为第二次请求响应的数据，`RenderComponent`渲染的数据也是第二次的

解决方法：

```tsx
const App = () => {
  const [data, setData] = useState([]);
  const serviceIndex = useRef(0);

  useEffect(() => {
    for (let i = 0; i < 3; i++) {
      service.then((res) => {
        if (i >= serviceIndex.current) {
          setData(res.data);
        }
        serviceIndex.current++;
      });
    }
  }, []);

  return <RenderComponent data={data} />;
};
```

当 `i >= serviceIndex`，可以保证请求是最新的，我们才进行`data`的更新
