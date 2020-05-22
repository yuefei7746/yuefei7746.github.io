---
title: Java 实现单链表
date: 2020-05-22 21:37:27
tags:
---
### 1. 单链表定义
> 单链表是一种链式存取的数据结构，用一组地址任意的存储单元存放线性表中的数据元素。链表中的数据是以结点来表示的，每个结点的构成：元素(数据元素的映象) + 指针(指示后继元素存储位置)，元素就是存储数据的存储单元，指针就是连接每个结点的地址数据。 --百度百科

### 2. 链表接口
第一步，定义一个链表接口。以后的链表结构应该都会使用该接口。需要提示的信息都已经写到了注释中。详细代码如下：
``` java

import java.io.Serializable;
import java.util.function.BiPredicate;

/**
 * 链式结构接口
 *
 * @author yuefei7746
 * @date 2020-05-18 17:55
 */
public interface ILinkedList<T> extends Serializable, Iterable<T> {

    /**
     * 链表尾部插入数据
     *
     * @param data 指定数据
     */
    void add(T data);

    /**
     * 指定位置插入
     *
     * @param index 指定下标
     * @param data  指定数据
     */
    void add(int index, T data);

    /**
     * 链表尾部插入数据集
     *
     * @param data 数据集
     */
    void addAll(T... data);

    /**
     * 链表指定下标插入数据集
     * 如果不是在当前链表尾部插入数据，则原链表的后续数据将会指定到传入数据集的最后一个节点下
     *
     * @param index 指定下标
     * @param data  数据集
     */
    void addAll(int index, T... data);

    /**
     * 是否包含
     *
     * @param data 指定数据
     * @return 返回是否包含
     */
    boolean contains(Object data);

    /**
     * 删除对应下标数据
     *
     * @param index 下标
     * @return 返回被删除的数据
     */
    T remove(int index);

    /**
     * 删除所有与指定数据匹配的数据
     *
     * @param data 指定数据
     */
    void removeAll(Object data);

    /**
     * 获取对应下标数据
     *
     * @param index 指定下标
     * @return 数据
     */
    T get(int index);

    /**
     * 获取链表头部数据
     *
     * @return 链表头
     */
    T head();

    /**
     * 获取链表尾部数据
     *
     * @return 链表尾
     */
    T tail();

    /**
     * 替换对应下标数据
     *
     * @param index   指定下标
     * @param newData 即将替换的新数据
     * @return 被替换的旧数据
     */
    T set(int index, T newData);

    /**
     * 链表长度
     *
     * @return 长度
     */
    int size();

    /**
     * 清空列表数据
     */
    void clear();

    /**
     * 根据实例返回节点下标，如果有多个相等实例，返回第一次出现的下标位置
     *
     * @param data 实例
     * @return 节点下标，如果不存在返回 -1
     */
    int indexOf(Object data);

    /**
     * 设定 removeAll 和 indexOf 函数的对比行为
     * 设置该函数主要是为了防止 java.util 中写死的需要使用 equals 方法判断的缺陷
     *
     * @param compare 对比函数
     * @return 方便链式调用，返回自身实例
     */
    ILinkedList<T> setCompare(BiPredicate<T, Object> compare);

}

``` 

### 3. 单链表实现

```java

```
