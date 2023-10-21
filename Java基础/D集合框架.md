# 一、概述
Java集合框架分为两大类，Collection（单列数据集合，类似数组）和Map（双列数据集合，类似Python中的字典）。其继承关系图分别如下：

- Collection：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20231004153557.png)

- Map：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20231004153650.png)

# 二、Collection
#Java集合框架/Collection

## 2.1 常用方法

#Java集合框架/Collection/添加
1. `add(E obj)`：添加元素对象到当前集合中 。
2. `addAll(Collection other)`：添加other集合中的所有元素对象到当前集合中，即this = this ∪ other。

#Java集合框架/Collection/判断 
3. `int size()`：获取当前集合中实际存储的元素个数。 
4. `boolean isEmpty()`：判断当前集合是否为空集合 。
5. `boolean contains(Object obj)`：判断当前集合中是否存在一个与obj对象equals返回true的元素。
6. `boolean containsAll(Collection coll)`：判断coll集合中的元素是否在当前集合中都存在。即coll集合是否是当前集合的“子集” 。
7. `boolean equals(Object obj)`：判断当前集合与obj是否相等。

#Java集合框架/Collection/删除
8. `void clear()`：清空集合元素 
9. `boolean remove(Object obj)` ：从当前集合中删除第一个找到的与obj对象equals返回true的元素。 
10. `boolean removeAll(Collection coll)`：求差集，从当前集合中删除所有与coll集合中相同的元素，即this = this - this ∩ coll 。
11. `boolean retainAll(Collection coll)`：求补集，从当前集合中删除两个集合中不同的元素，使得当前集合仅保留与coll集合中的元素相同的元素，即当前集合中仅保留两个集合的交集，即this = this ∩ coll。

#Java集合框架/Collection/转换与迭代
12. `Object[] toArray()`：返回包含当前集合中所有元素的数组 。
13. `hashCode()`：获取集合对象的哈希值 。
14. `iterator()`：返回迭代器对象，用于集合遍历。

## 2.2 List

List是Collection的子接口之一，List集合类中元素有序、且可重复，集合中的每个元素都有其对应的顺序索引，常用方法如下：

1. 插入元素
> `void add(int index, Object ele)`:在index位置插入ele元素。
> `boolean addAll(int index, Collection eles)`:从index位置开始将eles中的所有元素添加进来。

2. 获取元素
> `Object get(int index)`:获取指定index位置的元素。
> `List subList(int fromIndex, int toIndex)`:返回从fromIndex到toIndex位置的子集合。


3. 获取元素索引
> `int indexOf(Object obj)`:返回obj在集合中首次出现的位置。
> `int lastIndexOf(Object obj)`:返回obj在当前集合中末次出现的位置。

4. 删除和替换元素
> `Object remove(int index)`:移除指定index位置的元素，并返回此元素。
> `Object set(int index, Object ele)`:设置指定index位置的元素为ele。

可以使用以下代码快速创建List对象：
```java
List<String> list1 = Arrays.asList("red", "green", "blue");
List<Integer> list2 = Arrays.asList(1, 2, 3);
```
### 2.2.1 ArrayList与LinkedList

ArrayList（数组线性表）在概念上相当于数组，与数组不同的是，ArrayList的大小可以按其元素多少的变化而变化，不会像数组那样大小是固定的，它只能在末尾插入或删除元素；LinkedList（链表）可以在任何位置随意插入或删除元素，因此除在尾部增删元素外，LinkedList的效率比ArrayList高，以下是LinkedList常用方法：

> `public void addFirst(Object o)`：在头部插入元素o
> `public void addLast(Object o)`：在尾部插入元素o
> `public Object getFirst()`：返回头元素（第一个元素）
> `public Object getLast()`：返回尾元素（最后一个元素）
> `public Object removeFirst()`：返回并删除头元素
> `public Object removeLast()`：返回并删除尾元素

## 2.3 Set

Set（规则集）是一种不允许存在重复元素的容器类型，概念类似于数学中的“集合”，即无序性、唯一性和确定性。其常用子类有三种，分别是HashSet（完全无序）、LinkedSet（按添加元素时的顺序保存各元素的顺序）、TreeSet（按指定的排序规则进行升序排序），因排序会耗时，所以就效率上而言，这三者由高到低排序为：HashSet > LinkedSet > TreeSet。

重点介绍TreeSet，TreeSet常用构造方法如下：
```java
public TreeSet(){
    // 无参构造
}

public TreeSet(Collection<? extends E> c){
    // 将特定的Collection转为TreeSet
}

public TreeSet(Comparator<? super E> c){
    // 指定排序规则的TreeSet
}

```
以下例子是TreeSet常用方法的使用：

```java
import java.util.HashSet;
import java.util.Set;
import java.util.TreeSet;

/*TreeSet常用方法*/
public class TreeSetDemo {
    public static void main(String[] args) {
        Set<String> set = new HashSet<String>();
        set.add("London");
        set.add("Paris");
        set.add("New York");
        set.add("San Francisco");
        set.add("Beijing");
        set.add("New York");

        TreeSet<String> treeSet = new TreeSet<String>(set);
        System.out.println("Sorted tree set: " + treeSet);
        
        // 使用继承自SortedSet接口中的方法
        System.out.println("first(): " + treeSet.first());
        System.out.println("last(): " + treeSet.last());

        // “排序”小于New York的部分
        System.out.println("headSet(): " + treeSet.headSet("New York"));
        // “排序”大于等于New York的部分
        System.out.println("tailSet(): " + treeSet.tailSet("New York"));

        // 使用继承自NavigableSet接口中的方法
        System.out.println("lower(\"P\"): " + treeSet.lower("P")); // treeSet小于"P"中的最大元素
        System.out.println("higher(\"P\"): " + treeSet.higher("P")); // treeSet大于"P"中的最小元素

        System.out.println("floor(\"P\"): " + treeSet.floor("P")); // treeSet小于或等于"P"中的最大元素
        System.out.println("ceiling(\"P\"): " + treeSet.ceiling("P")); // treeSet大于或等于"P"中的最小元素

        System.out.println("pollFirst(): " + treeSet.pollFirst()); // 返回并删除treeSet中的第一个元素
        System.out.println("pollLast(): " + treeSet.pollLast()); // 返回并删除treeSet中的最后一个元素

        System.out.println("New tree Set: " + treeSet);
    }
}
```

运行结果如下：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20231021171542.png)

