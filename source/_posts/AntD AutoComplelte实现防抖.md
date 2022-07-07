---
title: "AntD AutoComplelte实现防抖"
date: 2022-07-07 16:52:43
index_img: https://image-1253774868.cos.ap-nanjing.myqcloud.com/img/20220707165349.png
categories:
  - 前端
tags:
  - React
  - Ant Design
---

## 前言

什么？你个`Ant Design`的`AutoComplate`的组件，连个防抖功能都不支持？

好吧，那我自己封装一下：

- 由于业务场景需要，用的`ProForm.Item`包裹了下
- 防抖用的 `loadsh`
- 使用 `usecallback` 包裹 `debounce` 函数，防止每次重新渲染后函数更新，这样就无法利用闭包特性实现防抖了。

```tsx
import React, { useCallback, useState } from "react";
import { AutoComplete, AutoCompleteProps } from "antd";
import { ProForm } from "@ant-design/pro-components";
import debounce from "lodash/debounce";
import { Rule } from "antd/lib/form";

interface DebounceAutoCompleteProps {
  label?: string;
  name?: string;
  debounceTime?: number;
  rules?: Rule[];
  fieldsProps?: AutoCompleteProps;
  service: (searchText: string) => Promise<Options[]>;
}

interface Options {
  label: string;
  value: string;
}

const DebounceAutoComplete = (props: DebounceAutoCompleteProps) => {
  const { label, name, debounceTime = 500, fieldsProps, service } = props;
  const [options, setOptions] = useState<Options[]>([]);

  const onSeach = useCallback(
    debounce((searchText: string) => {
      service(searchText).then((res) => {
        setOptions(res);
      });
    }, debounceTime),
    []
  );

  return (
    <ProForm.Item label={label} name={name}>
      <AutoComplete options={options} onSearch={onSeach} {...fieldsProps} />
    </ProForm.Item>
  );
};

export default DebounceAutoComplete;
```
