## 组织选择组件

本组件用 svelte 编写，编译成直接操作 dom 的语句，不依赖任何库与框架，可以跑在任意平台(前台，后台)，任意端(web 端, 移动端)，任意技术栈(react, vue，jquery)，支持 ie11.

gzip 包体积(包含 icon 资源)仅 7kb 左右。

## 安装

```
xnpm i @xm/xm-org-selector
```

## 使用

就是一个函数,调用会打开一个弹窗，用户选择完毕后会把结果返回。

```
import from '@xm/xm-org-selector/style.css' // 入口页导入下css


import select from '@xm/xm-org-selector'

// 简单使用
select({
  data:[],
  onOk:console.log
})

// 参数说明
select({
  title: '弹窗名称',// 非必填，默认为“请选择”
  types, // Array<'user' | 'dept' | 'org'> // 可选范围, 非必填， 默认是选人。
  max, // 最大选择数，非必填， 默认为20000
  value: [] // 非必填 初始值
  data: [] // 必填 备选项
  onOk(value) { // 点击确定时的回调函数，必填，参数为选择结果数组
    console.log(value)
  }
})

```

## 备选项

本组件的目的做成通用组件，所以不涉及任何接口调用，只是接受一个备选项，你需要自己拉取备选项后传给组件。

备选项结构是一个标准结构，所以你通常不需要做数据转换，只需 fetch 一下传给组件即可(`fetchData().then(data => select({data,onOk}))`)。这个标准结构如下(只展示了必要信息)：

```
[
  { id: 10016, name: '中国移动广西公司', type: 'org' },
  { id: 530845, name: '文秘档案室', type: 'dept', pid: 10016 },
  { id: 530570, name: '新闻宣传室', type: 'dept', pid: 10016 },
  { id: 530385, name: '公共关系室', type: 'dept', pid: 10016 },
  { id: 530362, name: '安全管理室', type: 'dept', pid: 10016 },
  { id: 8888975627, name: '吴莹', type: 'user', pid: 530845 },
  { id: 8888971988, name: '林洁', pid: 530570, type: 'user' },
  { id: 8888968243, name: '裴莉', pid: 530385, type: 'user' },
  { id: 8888968635, name: '秦志军', pid: 530362, type: 'user' },
]

// id: 实体id
// pid: 实体的是直接上层id

id+pid就可以拼装成树结构了。
```

之所以是平铺结构，因为可能涉及多棵树的合并，去除，查找等， 你可能需要把这个结构告诉你的业务后端；接口传参最好也约定好，这是推荐格式：

```
// post 请求

findChildren({
  id // 实体id
  type: 'org' | 'dept' // 实体类型
  range: Array<'user' | 'dept' | 'org'> // 数据范围
})

// 这样一个接口可以涵盖大部分场景。
```

## 封装

复杂场景下，你可能需要对本组件进行二次封装供项目内其他人使用，所有的表单方案都接受标准的{value, onChange}协议，你可以用此协议把它封装成表单控件，以 react 为例：

```
import select from '@xm/xm-org-selector'
import { Input } from 'antd'

export function default OrgSelect({value, onChange, ...rest}) {

  return (
    <Input
      value={value.map(it => it.name).join(',')}
      onClick={() => select({
        value,
        onOk:onChange
         ...rest,
      })}
    />)
}

```

这样你就可以像普通的表单控件一样使用它。
