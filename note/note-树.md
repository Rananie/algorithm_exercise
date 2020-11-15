---
title: note-树
date: 2020-11-15 22:50:31
categories: 刷题笔记
tags:
---

二叉树是树结构的一种，两个叉就是说每个节点最多只有两个子节点，我们习惯称之为左节点和右节点

## 二叉树的变量

1.前序遍历
先访问当前结点 -> 遍历左子树 -> 遍历右子树

```
preorder(root) {
	if not root: return
	doSomething(root)
	preorder(root.left)
	preorder(root.right)
}
```

2.中序遍历
先遍历左子树 -> 访问当前结点 -> 遍历右子树

```
inorder(root) {
	if not root: return
	inorder(root.left)
	doSomething(root)
	inorder(root.right)
}
```

3.后序遍历
遍历左子树 -> 遍历右子树 -> 访问当前结点

```
postorder(root) {
	if not root: return
	postorder(root.left)
	postorder(root.right)
	dosomething(root)
```