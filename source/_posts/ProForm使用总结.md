---
title: ProForm使用总结
date: 2022-08-11 21:20
index_img: https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/20220811212124.png
categories:
  - 前端
tags:
  - Ant Design
  - ProForm
---

### 1. 不同表单相同 name 的 ProFormSelect 产生的 bug

如以下代码所示，第二个 ProForm 中的 ProFormSelect 延迟 2 秒渲染，当第二个 ProFormSelect 渲染后，第一个 ProFormSelect 的可选项从选项 1 变为选项 2。

```tsx
export const () => {
  const [display, setDisplay] = useState(false);

  useEffect(() => {
    setTimeout(() => {
      setDisplay(true);
    }, 2000)
  }, []);

  return (
    <div className="App">
      <ProForm>
        <ProFormSelect
          name="select"
          request={async () => [{ label: "选项1", value: "1" }]}
        />
      </ProForm>
      <ProForm>
        {display ? (
          <ProFormSelect
            name="select"
            request={async () => [{ label: "选项2", value: "2" }]}
          />
        ) : null}
      </ProForm>
    </div>
  );
}
```

### 2.如何组织嵌套的表单结构

`ProForm`中的`name`可以为数组形式，比如`name={`['a', 'b']`}`渲染出来的数据（对象形式）是链式的`a.b`，我们在实现深层次表单组件时，可以传递前缀`path`，在子组件中`name`的形式为`name=[...path, name]`。此外，若`formData`的结构中包含数组，`name`则为`[...path, index, name]`的形式，但这样生成"数组结构"是类数组对象，不是数组。

`ProFormList`会给下方的组件追加一个前缀`path`，因此不需要再传入前缀，`ProformFiledSet`组件也会这么做，即时其实质上没有生效。

### 3. 表单联动方案

- `ProFormDependency`
  - 能够直接读取表单中任意位置的字段，将其作为依赖来实现表单控件之间的联动。[文档地址](https://procomponents.ant.design/components/dependency)
  - **优点：**
    - 可以跨组件通信，读到其他组件中的表单字段值，甚至可以在深层嵌套中，读到同级的表单字段值。比如`formData`是`{name: {firstName: 'a', LastName: 'b'}}`，`lastName`就可以依赖`firstName`。
  - **缺点：**
    - 由于`ProFormDependency`本质是个 JSX 元素，所以想用拿到的依赖值写一些逻辑比较不合适，就算能达到效果也会在标签内部写大量的逻辑代码，可读性极差。
- `useWatch Hooks`
  - Ant Design 实现的一个 Hooks，可以通过传入字段名和表单实例，拿到字段值，并会实时更新字段值。[文档地址](https://ant.design/components/form-cn/#Form.useWatch)
  - **优点：**
    - 获取到的字段可以当作组件中的变量，用在组件的任意地方。
  - **缺点：**
    - 只能获取到`formData`中第一层的字段，如果有层级嵌套，就获取不到了。

### 4.当 FormItem 未渲染时，useWatch 无法获取对应字段值

这与`getFieldsValue`的实现有关。

`Form`在创建时会新建一个`store`来存储数据，并为每个`FormItem`创建`Field Entity`。

假设我们使用了`setFieldsValue`初始化了数据，会将传入的数据存入`store`。

当我们调用`getFieldsValue()`时，会去遍历已创建`Field Entity`，获取对应的字段值，返回结果，所以未渲染的`FormItem`的字段我们是拿不到的。

如果我们调用`getFieldsValue(true)`，会去从`store`获取所有的数据。

`useWatch`在初始化数据的时候使用了`getFieldsValue()`，因此无法获取未渲染`FormItem`对应的字段值。

### 5.ProFormFieldSet 是个坑

在官方文档里说 `ProFormFieldSet` 可以用于组装结构化数据，然而其实现的问题很多，基本不推荐使用。

#### 存在嵌套无法工作

`ProFormFieldSet` 本质是通过 clone children 接管其 `onChange` `value` 来实现的，一旦有嵌套的结构就无法正常工作了，而内部嵌套的组件的值会直接泄漏。

```
<ProFormFieldSet>
  <ProFormText name="abc"/> ==> 这个组件的值会写到 FieldSet 中
  <div>
       <ProFormText name="child" />  ==> 这个组件的值会直接写到 Form 最顶层
  </div>
</ProFormFieldSet>
```

#### 然而部分组件认为嵌套是工作的

例如上面提到的 `ProFormDependency` ，即使在嵌套中也会从 `Context` 读取 `name` 的前缀，导致逻辑错误。

```
<ProFormFieldSet>
 <ProFormText name="abc"/> ==> 这个组件的值会写到 FieldSet 中
 <div>
      <ProFormText name="child" />  ==> 这个组件的值会直接写到 Form 最顶层
      <ProFormDependency name={['name']} /> ==> 读不到上面这个 child
 </div>
</ProFormFieldSet>
```

#### 实现很脆弱

通过 `displayName` 判断是否是内部组件，而做出不同的行为。

```tsx
let itemIndex = -1;
const list = toArray(children).map((item: any) => {
  if (React.isValidElement(item)) {
    itemIndex += 1;
    const index = itemIndex;
    const isProFromItem =
      // @ts-ignore
      item?.type?.displayName === "ProFormComponent" || item?.props?.readonly;
    const forkProps = isProFromItem
      ? {
          key: index,
          ignoreFormItem: true,
          ...((item.props as any) || {}),
          // 如果不是我们自定义的组件 fieldProps 无法识别
          fieldProps: {
            ...(item?.props as any)?.fieldProps,
            onChange: (...restParams: any) => {
              fieldSetOnChange(restParams[0], index);
            },
          },
          value: value?.[index],
          onChange: undefined,
        }
      : {
          key: index,
          ...((item.props as any) || {}),
          value: value?.[index],
          onChange: (itemValue: any) => {
            fieldSetOnChange(itemValue, index);
            (item as any).props.onChange?.(itemValue);
          },
        };
    return React.cloneElement(item, forkProps);
  }
  return item;
});
```

#### 会导致表单项功能失效

由于 `ProFormFieldSet` 接管了子组件的 `onChange` `value` ，所以 `FormItem` 其实没有在逻辑上起作用了，于是 ProForm 做了一个性能优化把 FormItem 这层给去掉。然而这会导致一些样式上的功能异常，例如 `label` 无法生效。

### 6. 怎么写 Form 的测试

推荐 `jest + testing-library/react`

```jsx
test('strategyRender 不能丢数据', async () => {
  let form: ProFormInstance;
  const { container, getByText } = render(
    <ProForm formRef={((ins: any) => (form = ins)) as any}> // 把 formRef 赋值出去方便测试
      // ...
    </ProForm>
  );

  act(() => {
    form.setFieldsValue(测试表单数据);  // 给表单设置数据，行为注意要放在 act 中
  });

  await waitFor(() => {  // 过程中可能会渲染多次，用 waitFor + getFieldsValue 等待表单 ready
    const values = form!.getFieldsValue();
    expect(Object.keys(values).length).toBeGreaterThan(0);
  });

  const values = form!.getFieldsValue();
  expect(values).toEqual(...);
});
```

### 7.useForm Hooks

为灵渠中的所有表单提供基本能力支持，拥有以下功能。

- 表单创建(create)
- 表单保存(save)、编辑(edit)
- 表单清除(clear)
- 表单回退(cancel)
- 数据读取（详情页的数据初始化）

```tsx
interface useFormOptions<T, DT, R> {
  initialSavedStatus?: boolean;
  data?: DT;
  transformer?: (data: DT) => T | Promise<T>;
  onSave?: (formData: T) => Promise<ResponseData<R>>;
  onCreate?: (formData: T) => Promise<ResponseData<R>>;
  createMessage?: string;
}

let FORM_DATA_CACHE = {};

export const useForm = <T = any, DT = any, R = boolean>(
  options: useFormOptions<T, DT, R>
) => {
  const {
    initialSavedStatus = false,
    data,
    transformer,
    onSave,
    onCreate,
    createMessage,
  } = options;

  const formRef = useRef<ProFormInstance>();
  const [savedStatus, setSavedStatus] = useState(initialSavedStatus);
  const [loading, setLoading] = useState(false);

  const initialData = useCallback(async () => {
    if (data) {
      const convertData = transformer ? await transformer(data) : data;
      formRef.current?.setFieldsValue(convertData);
      FORM_DATA_CACHE = convertData;
    }
  }, [data]);

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
        message.success(createMessage ? createMessage : "创建成功");
        return res;
      }
    } catch (error: any) {
      setLoading(false);
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
      console.log(error);
    }
  };

  // 表单编辑功能
  const edit = () => {
    setSavedStatus(false);
  };

  const cancel = () => {
    formRef.current?.resetFields();
    formRef.current?.setFieldsValue(FORM_DATA_CACHE);
  };

  // 表单清除功能
  const clear = () => {
    formRef.current?.resetFields();
  };

  return { formRef, savedStatus, loading, create, save, edit, clear, cancel };
};
```
