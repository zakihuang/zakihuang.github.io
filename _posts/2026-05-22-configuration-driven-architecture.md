---
layout: post
title: "配置驱动架构：让代码从'雕版'变成'活字'"
date: 2026-05-22
categories: [react, architecture]
tags: [react, antd, form, movabletype]
---

你有没有算过，过去一年你写了多少个表单？

Input + Select + DatePicker，组合、联动、校验、提交。80% 的代码结构几乎一样，但每次都要复制粘贴，改字段名，调布局，测联动。产品经理说"加两个字段"，你默默打开三个文件，修改几十行代码，再测试各种边界情况。

**这些重复劳动的本质，不是表单太复杂，而是我们还在用"手工雕版"的方式做开发。**

---

## 一个古老的类比

北宋毕昇发明活字印刷术之前，印一本书需要先把整页内容刻在木板上。想改一个字？整块木板作废，重新雕刻。

今天的表单开发，像极了这种"雕版印刷"：

- 字段定义和表单布局写在同一份 JSX 里，耦合在一起
- 每个表单都是一份独立的"雕版"，无法复用
- 需求变更等于"改字重刻"，牵一发而动全身

活字印刷的思路是：把每个字做成独立的字模，排版时按需组合，印完可复用。

**配置驱动架构（Configuration-Driven Architecture）做的正是这件事：把"字"（字段）和"版"（表单）分开。**

---

## 什么是配置驱动架构

简单说，就是把原本硬编码在代码里的逻辑，抽出来变成可维护的配置。

不是新概念。Webpack 用配置文件管理构建，Terraform 用 HCL 管理基础设施，很多系统都在这么做。但在前端表单领域，这个概念被贯彻得还不够彻底。

传统的表单开发：

```tsx
const FormPage = () => {
  const [name, setName] = useState('');
  const [type, setType] = useState('');
  // ... 还有28个

  return (
    <Form>
      <Form.Item label="名称" required>
        <Input value={name} onChange={setName} />
      </Form.Item>
      <Form.Item label="类型">
        <Select value={type} onChange={setType} options={options} />
      </Form.Item>
      {/* 重复30次 */}
    </Form>
  );
};
```

配置驱动的思路：字段长什么样、怎么校验、怎么联动，全部用 JSON 描述。代码只负责读配置、渲染界面。

```js
const fields = {
  name: { label: '名称', component: 'Input', required: true },
  type: { label: '类型', component: 'Select', options },
};

const config = {
  sections: [{ fields: ['name', 'type'] }],
};

// 渲染引擎读取配置，自动生成表单
<FormEngine fields={fields} config={config} />
```

**界面是配置的执行结果，不是代码的堆砌。**

---

## MovableType：一个具体的实现

如果你认同这个方向，可以看看 [MovableType](https://blog.7hihi.com/MovableType)。它是一个基于 React 和 Ant Design 的纯渲染型配置引擎，命名就来自活字印刷术。

它的设计很克制，核心只有三个概念：

| 概念 | 作用 | 类比 |
|------|------|------|
| **字段池 (fields)** | 定义所有可用字段的标签、组件、校验规则 | 字模仓库 |
| **表单配置 (config)** | 决定某张表单用哪些字段、如何分组、何种布局 | 排版方案 |
| **组件注册表** | 自定义组件的加载方式，扩展引擎能力 | 特殊字体 |

字段池只管"字模长什么样"，配置只管"这次怎么排版"。同一份字段池可以被 N 个表单复用。需求变更时，改几行 JSON，而不是几十个组件文件。

---

## 它能做什么

**1. 声明式联动，无需手写 useEffect**

配置里写一句 `watch`，字段间联动自动处理：

```js
{
  name: 'city',
  watch: {
    province: (province, { form }) => {
      form.setFieldsValue({ city: undefined });
      return { dataLoader: `/api/cities?province=${province}` };
    }
  }
}
```

省份变化后，城市字段自动清空并加载新选项。不需要 `useEffect`，不需要手动处理 `loading`。

**2. 一套配置，两种模式**

通过 `mode="edit"` 或 `mode="view"` 切换。查看模式下，输入框自动变成纯文本展示，内置组件自动把 `value` 翻译成 `label`。不用写两份代码。

**3. 为 AI 而生**

这是最有意思的一点。因为整个表单被抽象为纯粹的结构化数据，大语言模型可以极低成本地生成、理解和修改这些配置。你不是在让 AI 写 React 组件，而是在让 AI 写 JSON——这恰恰是它最擅长的事。

---

## 效果对比

| 维度 | 传统开发 | 配置驱动 |
|------|----------|----------|
| 30字段表单代码量 | ~1000行 | ~230行（配置+引擎） |
| 新增一个字段 | 改多个 JSX 文件 | 加一行配置 |
| 修改验证规则 | 找到组件，改代码 | 改配置里的 rules |
| 查看模式 | 重写一份展示组件 | 切换 mode |
| AI 生成可行性 | 低（需理解组件上下文） | 高（纯结构化数据） |

---

## 背后的认知

配置驱动架构不是让你写更多 JSON，而是逼你做一件事：**把数据和表现分开。**

字段池是"数据"，表单配置是"排版方案"。两者解耦后，表单逻辑才变得高度结构化、可序列化、可复用。

这和 Form-Driven Architecture 是相通的：让专门的工具做专门的事。Form 管理表单状态，配置引擎管理表单结构，开发者只处理业务逻辑。

---

## 什么时候用

✅ 字段超过10个、多表单复用相同字段、需求变更频繁、需要AI辅助生成表单

❌ 2-3个字段的一次性表单（不值得引入引擎）

---

## 总结

从手工雕版到活字印刷，本质上是一次"分离关注"的胜利：字是字，版是版，各管各的。

配置驱动架构在做同样的事：字段定义一次，表单配置百次复用。需求变更时，改配置而不是改代码。

如果你想深入了解这个思路的具体实现，包括声明式联动的完整语法、自定义组件的扩展方式、以及如何让 AI 帮你生成配置，可以去看 [MovableType 的完整文档](https://blog.7hihi.com/MovableType)。

> 技术的选择，往往取决于你愿不愿意承认一件事：很多重复劳动，本来就不该存在。
