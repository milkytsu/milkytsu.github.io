---
layout: post
title:  "`HTML`的`input`标签中属性`readonly`与`disabled`区别"
date:   2016-06-15 23:14:29 +0800
categories: html readonly disabled
---

# `HTML`的`input`标签中属性`readonly`与`disabled`区别

　　在表单中使用`jQuery`的`datetimepiker`插件填充时间的输入框时，想要实现不允许用户手动在输入框中输入时间，只允许使用插件填充。首先想到 `disabled` ，但是测试后发现使用`disabled`的元素无法响应`click`事件，经过搜索发现有`contenteditable`属性，设置为 `true`可编辑，`false`为不可编辑，但测试发现对`input`框不起作用，设置为`false`仍然可以填入内容。进一步搜索发现该属性是控制类似于`p`，`span`等标签的内容是否能够改变的。

　　后来搜索到`readonly`这个属性，发现可以响应`click`及`focus`事件，也就可以绑定`datetimepike`控件，匹配需求。

　　以下是对搜索到的`stackoverflow`上对`readonly`和`disabled`的区别的回答（[http://stackoverflow.com/questions/7730695/whats-the-difference-between-disabled-disabled-and-readonly-readonly-for-ht](http://stackoverflow.com/questions/7730695/whats-the-difference-between-disabled-disabled-and-readonly-readonly-for-ht)）的翻译：

　　带有`readonly`属性的元素仅仅是不可编辑，在表单提交的时候，仍然会把该元素及其值提交。`disabled`元素不可编辑，同时该元素和其值并不会随表单提交。另一个区别是，带有`readonly`属性的元素能够获取焦点（`focus`），在表单中使用tab键的时候，也可以获取焦点；而具有`disabled`属性的元素不能获取焦点（博主：也不能响应`click`事件）。

　　更详细的说明可参考 [这篇文章](https://kreotekdev.wordpress.com/2007/11/08/disabled-vs-readonly-form-fields/) 或 [W3C规范的定义](http://www.w3.org/TR/html4/interact/forms.html#h-17.12) 。以下列出比较重要的区别：

#### 主要区别：

##### `disabled`属性

* 表单中带有`disabled`属性的元素的值不会随表单一起被提交。`W3C`把这种元素称为`successful element`（博主：一个已经被成功填充的元素，不需要再入库？）（就像是表单中未勾选的`checkbox`的值不会被提交的行为）。
* ​一些浏览器会对表单中`disabled`属性的元素的样式进行覆盖，或对其提供默认样式（灰色或浮凸文字）。
* 具有`disabled`属性的表单元素不能被聚焦`focus`（选中）。
* 使用`tab`在表单中切换到下一元素时，会跳过具有`disabled`属性的元素。

##### `readonly`属性
* 并不是所有表单元素都可以使用`readonly`属性。比如`<select>`，`<option>`，`<button>`就没有。 (although thy both have disabled attributes)（博主：不太懂thy如何翻译，求指点）
* 浏览器对具有`readonly`属性的元素不提供默认样式。
* 具有`readonly`属性的表单元素的值会随表单一起提交。
* 具有`readonly`属性的表单元素能够被聚焦`focus`（选中）。
* 具有`readonly`属性的表单元素会在使用`tab`键切换时正常选中。

##### 该答案的其他有用的回复：
* 具有`disabled`属性的表单元素不能触发任何事件，而`readonly`可以。


