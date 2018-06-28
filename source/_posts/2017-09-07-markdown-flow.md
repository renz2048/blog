---
layout: post
title: markdown flow
author: renz
date: 2017-09-07
categories: 前端
update: 2017-09-30
---
# flowchart
流程图语法分为两部分：
- 定义流程图元素
- 定义流程图的执行走向（用来连接流程图元素）

## 定义元素
```
tag=>type: content
```
### 说明
- **tag**流程图的标签，用来在定义执行走向的时候指定下一步到达何处，可理解为**名字**
- **type**确定标签类型，表示这个标签的种类是开始结束，输入输出还是判断等
- **content**流程图文本框内的描述内容

### 标签类型
- `start`：开始`st=>start: 开始`
- `end`：结束`e=>end: 结束`
- `operation`：操作、步骤、执行说明`op1=>operation: come to eat`
- `subroutine`：子步骤`sb1=>subroutine: go back`
- `condition`：条件选择`cond=>condition: is it rain?`
- `inputoutput`：输入输出

## 定义执行走向
- 使用`->`连接两个元素(标签)
- 对于`condition`类型，有`cond(yes)`和`cond(no)`两种控制走向的语法
- 其他元素也可以控制分支方向，默认`向下`，使用`right`向右，例`sb1(right)`

## 实例

```flow
st=>start: Home
e=>end: Restaurant
op1=>operation: get out to eat
cond=>condition: is it rain?
sb1=>subroutine: go back and try again
op2=>operation: arrive
st->op1->cond
cond(yes)->op2->e
cond(no)->sb1(right)->op1
```
![flowchart](/images/example-flowchart.png)
# sequence
sequence语法仅一种格式，即A到B的路径，外加可控制的note
## 定义元素
![sequence grammar](/images/sequence-grammar.png)
### 说明
- 路径：包含两种线型和两种箭头，共四种组合方式
- **note**：旁注

## 实例
```sequence
Title: Here is a title
A->B: Normal line
B-->C: Dashed line
C->>D: Open arrow
D-->>A: Dashed open arrow
Note left of A: Note to the\n left of A
Note right of A: Note to the\n right of A
Note over A: Note over A
Note over A,B: Note over both A and B
```
![js sequence diagrams](/images/example-sequence.PNG)
## 参考
[js-sequence-diagrams/Turns text into UML sequence diagrams](https://bramp.github.io/js-sequence-diagrams/)
# 附
未完待续，后面还会介绍mathjax用法，这个在这个模板里直接就可以使用，而以上两个属性暂时还不支持，应该是和js调用有关，太麻烦，我只是想使用，便于展示总结，所以暂时使用截图
