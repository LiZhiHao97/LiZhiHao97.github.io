---
title: React最佳实践（一）：编写一个好的React Hook
date: 2022-08-02 20:02:04
index_img: https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/20220802201426.png
categories: 前端
tags:
  - Typescript
  - React
  - React Hooks
---

## 前言

本系列用来记录 React 在工作学习中的实践，我写的内容一定存在不足之处，以及能够优化的地方，文章之所以取这个名字，旨在鞭策自己写出更优雅的代码，摈弃不好的习惯。

本文的例子是在网易云音乐实习时参与的项目中写的一个表单通用功能的 Hook，因为项目需求配合[ProForm](https://procomponents.ant.design/components/form/)使用，根据业务需求需要有以下功能。

- 表单创建(create)
- 表单保存(save)、编辑(edit)
- 表单清除(clear)
- 数据读取（详情页的数据初始化）

## 比较

为了加深学习印象，理解后面为什么这么写，先来一个最初本人的乞丐版（现在回顾有点惨不忍睹），这里得感谢龙云大佬的指点。

```typescript
interface QueryParams {
  id: string;
}

export const useForm = (
  initialSavedStatus: boolean,
  service: (data: any, options?: any) => Promise<any>
) => {
  const formRef = useRef<ProFormInstance>();
  const [savedStatus, setSavedStatus] = useState(initialSavedStatus);
  const params = useParams<QueryParams>();

  const save = async () => {
    formRef.current?.validateFields().then(() => {
      service({
        ...formRef.current?.getFieldsValue(),
        id: params.id,
      }).then((res) => {
        console.log(formRef.current?.getFieldsValue());
        message.success("保存成功");
        setSavedStatus(true);
      });
    });
  };

  const edit = () => {
    setSavedStatus(false);
  };

  const getActionBar = () => {
    return savedStatus ? (
      <Button onClick={() => edit()} size="large">
        编辑
      </Button>
    ) : (
      <Button type="primary" onClick={() => save()} size="large">
        保存
      </Button>
    );
  };

  return { formRef, savedStatus, getActionBar, edit, save };
};
```

以上代码，抛开功能不全的问题不谈，存在几个槽点。

- 首先，以上代码把 UI 封装进了 Hook，Hook 应该是对一套通用逻辑的封装，我把 UI 封装进去，还不如封装个组件呢。

- 其次，因为最初考虑这个 hook 的保存编辑功能，是在详情页读取路由参数`id`，来调用`service`函数获取后端数据，于是我直接把这套逻辑写进来了，这样会让 Hook 与业务逻辑极度耦合，如果我另一个场景改了个参数名呢？比如叫`uid`，或者`service`传参的形式变了。因此，正确的做法是在外对`service`包装一层，这里的`service`只应返回`data`。

- 最后，可以把 promise then 的写法换成 async await，可读性会更好一点。

## 编写

ok，首先写个基本结构，为了方便起见，会先给出所有的参数。如果理解不了部分参数以及范型可以先忽略。

这里之所以把参数写进一整个对象，是因为 JS 自带的一个问题，不能通过`形参名`给参数赋值。

比如我不想传`initialSavedStatus`但想传`data`，就得 `useForm(undefined, data)`这样调用，而比如 python 这样的语言就可以 `useForm(data = data)`这样调用。

```typescript
interface useFormOptions<T, DT, R> {
  initialSavedStatus?: boolean;
  data?: DT;
  transformer?: (data: DT) => T | Promise<T>;
  onSave?: (formData: T) => Promise<ResponseData<R>>;
  onCreate?: (formData: T) => Promise<ResponseData<R>>;
  showErrorTip?: boolean;
}

export const useForm = <T = any, DT = any, R = boolean>(
  options: useFormOptions<T, DT, R>
) => {
  const formRef = useRef<ProFormInstance>();
  const [savedStatus, setSavedStatus] = useState(initialSavedStatus);
  const [loading, setLoading] = useState(false);

  // 表单创建功能
  const create = () => {};

  // 表单保存功能
  const save = () => {};

  // 表单编辑功能
  const edit = () => {};

  // 表单清除功能
  const clear = () => {};

  return { formRef, savedStatus, loading, create, save, edit, clear };
};
```

这里基本上给出了一个完整的 `useForm` 结构，除了读取数据部分还没有添加。下面讲解一下各个状态的作用。

- `loading`状态来表示表单的状态，在`save`和`create`的时候`loading`应为**true**

- `savedStatus`表示表单的保存状态，当`savedStatus`为 true 时，表单的`disable`为`true`，即被锁定无法编辑，`false`时可以编辑。这里会传入一个初始状态`initialSavedStatus`，**新建页**传`false`，因为新建页不应该被锁定，所有字段都应该被填写，**详情页**一开始传`true`。

- `formRef`为`ProForm`的一个实例，用于拿到`Proform`的方法。

接下来，逐一完善功能

### edit

`edit`是最简单的部分，因为点击编辑按钮，我们并不需要调用`service`发起请求，只需要改变表单的保存状态，因此：

```typescript
const edit = () => {
  setSavedStatus(false);
};
```

### clear

`clear`也很简单，只需要调用`ProForm`实例的方法，重制表单的所有字段即可。

```typescript
const clear = () => {
  formRef.current?.resetFields();
};
```

> formRef 作为一个 hook 返回出去的 ref，会附加给 Proform，成为 ProForm 的实例（Instance），这样就可以拿到 ProForm 的方法，ProForm 这种基于 Ref 的设计思路优点在于与业务解耦，其他业务组件想用它的方法直接通过实例调用即可，我们将在这个系列第二篇，阐述这种思路。

### create

`create`相对复杂一点，在新建的过程中，我们分步骤来讲一下过程

1. 判断有没有传入创建相关的函数`onCreate`，如果没有传，那当然不用进行下去了，可能的不可能创建得了。

2. 判断表单字段是否通过校验，比如一些必传字段，必须要有值才可以提交。

3. 获取表单数据，当有表单数据才能继续进行。

4. 准备工作完成了，进入加载状态,设置`loading`为`true`。

5. 发起请求

值得注意的是，因为`async await`不像`Promise`那样有`Catch`函数可以捕获异常，所以需要包一层`try catch`，否则在请求发生错误时无法进行后面代码的执行，loading 将一直为`true`

此外，我们在创建成功后，可能会执行一些回调，比如创建成功后返回列表页。所以我们 return 了`res`，显然它是一个`Promise`，方便我们在外部做一些回调操作。

```typescript
const create = async () => {
  // step 1
  if (!onCreate) {
    return;
  }

  // step 2
  const valid = await formRef.current?.validateFields();
  if (!valid) {
    message.error("创建失败");
    return;
  }

  // step 3
  const formData = formRef.current?.getFieldsValue();

  if (!formData) {
    return;
  }

  // step 4
  setLoading(true);

  try {
    const res = await onCreate(formData);
    setLoading(false);
    if (res && res.code === 200) {
      message.success("创建成功");
      return res;
    }
  } catch (error: any) {
    setLoading(false);
    showErrorTip && message.error("创建失败");
    console.log(error);
  }
};
```

### save

学会了`create`，保存就也很简单了，他们实际上只有两个区别

- 调用的 service 不同
- 保存需要在保存成功后将`savedStatus`设为`true`

```typescript
const save = async () => {
  if (!onSave) {
    return;
  }

  const valid = await formRef.current?.validateFields();

  if (!valid) {
    message.error("保存失败");
    return;
  }

  const formData = formRef.current?.getFieldsValue();

  if (!formData) {
    return;
  }

  setLoading(true);

  try {
    const res = await onSave(formData);
    setLoading(false);
    if (res && res.code === 200) {
      setSavedStatus(true);
      message.success("修改成功");
      return res;
    }
  } catch (error) {
    setLoading(false);
    showErrorTip && message.error("保存失败");
    console.log(error);
  }
};
```

### 数据初始化

数据初始化我们可以用`useEffect`来实现，需要注意的是，`data`最开始可能是`undefined`，所以我们要进行判断。

这个步骤也非常简单，只要将得到的数据，使用`ProForm`自带的`setFieldsValue`方法及好了

我们看到，代码中有个`transformer`，它的作用是做数据转换(`data -> formData`)，因为有时候我们表单的`formData`和后端返回给我们的`data`格式是不一样的，所以转换很必要。此外，transformer 中可能有异步操作，所以需要`await`一下。

```javascript
const initialData = useCallback(async () => {
  if (data) {
    const formData = transformer ? await transformer(data) : data;
    formRef.current?.setFieldsValue(formData);
  }
}, [data, transformer]);

// 表单读取数据初始化
useEffect(() => {
  initialData();
}, [initialData]);
```

至此，我们就完成一个完整的表单`Hook`了

## 总结

我们可以总结一下开发`React Hooks`过程中需要注意的点：

- 注意与业务逻辑解耦，增加可复用性。
- 考虑不同场景下会遇到的问题，写出一套尽可能通用的方案。
- 不能引入`JXS/TSX`, `Hooks`是对逻辑的封装。

完整代码如下：

```typescript
interface useFormOptions<T, DT, R> {
  initialSavedStatus?: boolean;
  data?: DT;
  transformer?: (data: DT) => T | Promise<T>;
  onSave?: (formData: T) => Promise<ResponseData<R>>;
  onCreate?: (formData: T) => Promise<ResponseData<R>>;
  showErrorTip?: boolean;
}

export const useForm = <T = any, DT = any, R = boolean>(
  options: useFormOptions<T, DT, R>
) => {
  const {
    initialSavedStatus = false,
    data,
    transformer,
    onSave,
    onCreate,
    showErrorTip = true,
  } = options;

  const formRef = useRef<ProFormInstance>();
  const [savedStatus, setSavedStatus] = useState(initialSavedStatus);
  const [loading, setLoading] = useState(false);

  const initialData = useCallback(async () => {
    if (data) {
      const formData = transformer ? await transformer(data) : data;
      formRef.current?.setFieldsValue(formData);
    }
  }, [data, transformer]);

  // 表单读取数据初始化
  useEffect(() => {
    initialData();
  }, [initialData]);

  // 表单创建功能
  const create = async () => {
    if (!onCreate) {
      return;
    }

    const valid = await formRef.current?.validateFields();
    if (!valid) {
      message.error("创建失败");
      return;
    }

    const formData = formRef.current?.getFieldsValue();

    if (!formData) {
      return;
    }

    setLoading(true);

    try {
      const res = await onCreate(formData);
      setLoading(false);
      if (res && res.code === 200) {
        message.success("创建成功");
        return res;
      }
    } catch (error: any) {
      setLoading(false);
      showErrorTip && message.error("创建失败");
      console.log(error);
    }
  };

  // 表单保存功能
  const save = async () => {
    if (!onSave) {
      return;
    }

    const valid = await formRef.current?.validateFields();

    if (!valid) {
      message.error("保存失败");
      return;
    }

    const formData = formRef.current?.getFieldsValue();

    if (!formData) {
      return;
    }

    setLoading(true);

    try {
      const res = await onSave(formData);
      setLoading(false);
      if (res && res.code === 200) {
        setSavedStatus(true);
        message.success("修改成功");
        return res;
      }
    } catch (error) {
      setLoading(false);
      showErrorTip && message.error("保存失败");
      console.log(error);
    }
  };

  // 表单编辑功能
  const edit = () => {
    setSavedStatus(false);
  };

  // 表单清除功能
  const clear = () => {
    formRef.current?.resetFields();
  };

  return { formRef, savedStatus, loading, create, save, edit, clear };
};
```
