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
