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

import java.util.*;
import java.util.function.BiPredicate;

/**
 * @author yuefei7746
 * @date 2020-05-18 18:09
 */
public class SinglyLinkedList<T> implements ILinkedList<T> {

    private transient MyNode<T> dummyHead;

    private transient MyNode<T> tail;

    private transient int modCount;

    private transient int size;

    private BiPredicate<T, Object> compare = Objects::equals;

    public SinglyLinkedList() {
        this.dummyHead = new MyNode<>();
    }

    public SinglyLinkedList(T... a) {
        this();
        addAll(a);
    }

    @Override
    public void add(T data) {
        MyNode<T> t = tail;
        MyNode<T> newNode = new MyNode<>(data);
        tail = newNode;
        if (t == null) {
            dummyHead.next = newNode;
        } else {
            t.next = newNode;
        }
        modCount++;
        size++;
    }

    @Override
    public void add(int index, T data) {
        checkIndex(index);
        MyNode<T> prev = getPrev(index);
        prev.next = new MyNode<>(data, prev.next);
        modCount++;
        size++;
    }

    @Override
    public void addAll(T... a) {
        Objects.requireNonNull(a);
        for (T t : a) {
            add(t);
        }
    }

    @Override
    public void addAll(int index, T... a) {
        Objects.requireNonNull(a);
        checkIndex(index);
        MyNode<T> prev = getPrev(index);
        for (T t : a) {
            prev.next = new MyNode<>(t, prev.next);
            prev = prev.next;
            size++;
        }
        tail = prev.next;
        modCount++;
    }

    @Override
    public boolean contains(Object data) {
        return indexOf(data) != -1;
    }

    @Override
    public T remove(int index) {
        checkIndex(index);
        MyNode<T> prev = getPrev(index);
        T removedData = prev.next.data;
        prev.next = prev.next.next;
        size--;
        if (size == 0 || size == index) {
            tail = prev.next;
        }
        modCount++;
        return removedData;
    }

    @Override
    public void removeAll(Object data) {
        MyNode<T> prev = dummyHead, n = dummyHead.next;
        while (n != null) {
            if (compare.test(n.data, data)) {
                prev.next = n.next;
                size--;
            } else {
                prev = n;
            }
            n = n.next;
        }
        tail = size > 0 ? prev : null;
        modCount++;
    }

    @Override
    public T get(int index) {
        checkIndex(index);
        return getNode(index).data;
    }

    @Override
    public T head() {
        MyNode<T> h = dummyHead.next;
        if (h == null)
            throw new NoSuchElementException();
        return h.data;
    }

    @Override
    public T tail() {
        MyNode<T> t = this.tail;
        if (t == null)
            throw new NoSuchElementException();
        return t.data;
    }

    @Override
    public T set(int index, T newData) {
        checkIndex(index);
        MyNode<T> node = getNode(index);
        T oldData = node.data;
        node.data = newData;
        modCount++;
        return oldData;
    }

    @Override
    public int size() {
        return size;
    }

    @Override
    public void clear() {
        dummyHead.clear();
        tail = null;
        size = 0;
        modCount++;
    }

    @Override
    public int indexOf(Object data) {
        Objects.requireNonNull(data);
        int index = 0;
        for (MyNode<T> n = dummyHead.next; n != null && index < size; n = n.next, index++)
            if (compare.test(n.data, data)) return index;
        return -1;
    }

    @Override
    public ILinkedList<T> setCompare(BiPredicate<T, Object> compare) {
        Objects.requireNonNull(compare);
        this.compare = compare;
        modCount++;
        return this;
    }

    /**
     * 根据下标返回指定位置节点
     *
     * @param index 下标
     * @return 指定位置节点，有可能返回 null
     */
    private MyNode<T> getNode(int index) {
        MyNode<T> n = dummyHead.next;
        while (n != null && index-- > 0)
            n = n.next;
        return n;
    }

    /**
     * 根据下标返回指定位置节点的上层节点
     *
     * @param index 下标，如果没有 checkIndex 返回值可能为 null
     * @return 指定位置节点的上层节点
     */
    private MyNode<T> getPrev(int index) {
        MyNode<T> prev = dummyHead;
        while (prev != null && index-- > 0)
            prev = prev.next;
        return prev;
    }

    private void checkIndex(int index) {
        if (index < 0)
            throw new IndexOutOfBoundsException(index + " < 0");
        if (index >= size)
            throw new IndexOutOfBoundsException(index + " >= " + size);
    }

    @Override
    public Iterator<T> iterator() {
        return new Itr<>(this);
    }

    private class Itr<E> implements Iterator<E> {

        private final SinglyLinkedList<E> parent;

        private transient MyNode<E> current;

        private transient int expectedModCount;

        private transient int nextIndex;

        Itr(SinglyLinkedList<E> parent) {
            this.parent = parent;
            this.current = parent.dummyHead.next;
            this.expectedModCount = parent.modCount;
        }

        @Override
        public boolean hasNext() {
            return nextIndex < parent.size;
        }

        @Override
        public E next() {
            checkModCount();
            if (!hasNext())
                throw new NoSuchElementException();
            E val = current.data;
            current = current.next;
            nextIndex++;
            return val;
        }

        @Override
        public void remove() {
            checkModCount();
            current = hasNext() ? current.next : null;
            parent.remove(nextIndex);
            expectedModCount++;
        }

        private void checkModCount() {
            if (expectedModCount != parent.modCount)
                throw new ConcurrentModificationException();
        }

    }

    @Override
    public String toString() {
        Iterator<T> it = iterator();
        int index = 0;
        Object[] a = new Object[size];
        while (it.hasNext()) {
            a[index++] = it.next();
        }
        return Arrays.toString(a);
    }

    private static class MyNode<E> {
        private E data;
        private MyNode<E> next;

        MyNode() {
        }

        MyNode(E data) {
            this(data, null);
        }

        MyNode(E data, MyNode<E> next) {
            this.data = data;
            this.next = next;
        }

        void clear() {
            data = null;
            if (next != null) {
                next.clear();
                next = null;
            }
        }

    }
}
```

### 4. 测试类
```java

import org.junit.Assert;
import org.junit.Test;

import java.util.Arrays;

/**
 * @author yuefei7746
 * @date 2020-05-22 22:07
 */
public class SinglyLinkedListTest {

    @Test
    public void add() {
        SinglyLinkedList<String> list = new SinglyLinkedList<>();
        list.add("a");
        list.add("c");
        list.add(1, "b");
        Assert.assertEquals(list.toString(), Arrays.toString(new String[]{"a", "b", "c"}));
    }

    @Test
    public void addAll() {
        SinglyLinkedList<String> list = new SinglyLinkedList<>();
        list.addAll("a", "d");
        list.addAll(1, "b", "c");
        Assert.assertEquals(list.toString(), Arrays.toString(new String[]{"a", "b", "c", "d"}));
    }

    @Test
    public void contains() {
        SinglyLinkedList<String> list = new SinglyLinkedList<>("a", "b");
        list.addAll("a", "b");
        Assert.assertTrue(list.contains("a"));
    }

    @Test
    public void remove() {
        SinglyLinkedList<String> list = new SinglyLinkedList<>("a", "b");
        String removed = list.remove(0);
        Assert.assertEquals(removed, "a");
        Assert.assertFalse(list.contains("a"));
        list.remove(0);
        // TODO 此时就算 size 等于 0 也可能有 bug 存在。只有 dummyHead 和 tail 中的引用也都被清空才是正确的。
        Assert.assertEquals(list.size(), 0);
    }

    @Test
    public void removeAll() {
        SinglyLinkedList<String> list = new SinglyLinkedList<>("a", "b", "a", "a");
        list.removeAll("a");
        Assert.assertEquals(list.toString(), Arrays.toString(new String[]{"b"}));
        list.removeAll("b");
        // TODO 此时就算 size 等于 0 也可能有 bug 存在。只有 dummyHead 和 tail 中的引用也都被清空才是正确的。
        Assert.assertEquals(list.size(), 0);
    }

    @Test
    public void get() {
        SinglyLinkedList<String> list = new SinglyLinkedList<>("a");
        Assert.assertEquals(list.get(0), "a");
    }

    @Test
    public void head() {
        SinglyLinkedList<String> list = new SinglyLinkedList<>("a");
        Assert.assertEquals(list.head(), "a");
    }

    @Test
    public void tail() {
        SinglyLinkedList<String> list = new SinglyLinkedList<>("a");
        Assert.assertEquals(list.tail(), "a");
    }

    @Test
    public void set() {
        SinglyLinkedList<String> list = new SinglyLinkedList<>("b", "b");
        list.set(0, "a");
        Assert.assertEquals(list.toString(), Arrays.toString(new String[]{"a", "b"}));
    }

    @Test
    public void indexOf() {
        SinglyLinkedList<String> list = new SinglyLinkedList<>("a", "b", "c");
        Assert.assertEquals(list.indexOf("b"), 1);
    }

}
```

### 5. 开发过程中遇到的问题汇总
1. 变量`dummyHead`：如果直接把链表头节点保存，当遇到涉及到头节点的变动时，就会复杂的多。现在使用`dummyHead`作为头节点，新增的第一个数据节点作为其子节点，当需要修改或删除第一个数据节点时，只需要修改`dummyHead`下的引用即可。
2. 变量`tail`：如果从链表尾部插入节点时没有指定`tail`节点，就需要遍历整个链表。设置`tail`变量后，所以和尾部节点操作相关的操作只需要直接获取该尾结点即可。
3. 变量`modCount`：用来保证`Iterator`的访问可靠性，但并不是绝对安全的。改动链表数据时递增`modCount`，访问`Iterator`时将会维护一个变量`exportedModCount`，通过对比来确定`Iterator`的可靠性。
4. 通过` transient`去除指令重排序，不过只能减少可能发生的并发异常，无法杜绝。
5. `remove`操作：当执行删除操作时，就算`size`被赋值到 0 也还是有可能存在 **bug**。只有`dummyHead`和`tail`中的引用也都被清空才是正确的。
6. `clear`操作：考虑到循环清除操作需要定义额外的变量，同时也是为了操作方便，使用了递归节点的方式完成。
