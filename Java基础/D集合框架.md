Java集合框架分为两大类，Collections（单列数据集合，类似数组）和Map（双列数据集合，类似Python中的字典）。
# 一、Collection
#Java集合框架/Collection
其继承关系图分别如下：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20231004153557.png)
## 1.1 常用方法

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

## 1.2 List

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
### 1.2.1 ArrayList与LinkedList

ArrayList（数组线性表）在概念上相当于数组，与数组不同的是，ArrayList的大小可以按其元素多少的变化而变化，不会像数组那样大小是固定的，它只能在末尾插入或删除元素；LinkedList（链表）可以在任何位置随意插入或删除元素，因此除在尾部增删元素外，LinkedList的效率比ArrayList高，以下是LinkedList常用方法：

> `public void addFirst(Object o)`：在头部插入元素o
> `public void addLast(Object o)`：在尾部插入元素o
> `public Object getFirst()`：返回头元素（第一个元素）
> `public Object getLast()`：返回尾元素（最后一个元素）
> `public Object removeFirst()`：返回并删除头元素
> `public Object removeLast()`：返回并删除尾元素

## 1.3 Set

Set（规则集）是一种不允许存在重复元素的容器类型，概念类似于数学中的“集合”，即无序性、唯一性和确定性。其常用子类有三种，分别是HashSet（完全无序）、LinkedSet（按添加元素时的顺序保存各元素的顺序）、TreeSet（按指定的排序规则进行升序排序），因排序会耗时，所以就效率上而言，这三者由高到低排序为：HashSet > LinkedSet > TreeSet。

另外，加入Set中的元素类型最好重写hashCode()和equals()方法。

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

# 二、Map
#Java集合框架/Map

Map是双列集合，用于保存具有映射关系的数据：key-value。

- Map 中的 key 和 value 都可以是任何引用类型的数据。但常用String类作为Map的“键”。
- key所属的类（即Set接口）不允许重复，需重写hashCode()和equals()方法。
- value所属的类（即Collection接口）可以重复，但也需重写equals()方法。
- key和value构成一个entry。所有的entry彼此之间是无序的、不可重复的。

entry（条目）、key（键）、value（值）三者的关系如下：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20231025192323.png)


Map的继承关系图分别如下：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20231004153650.png)

## 2.1 Map 接口常用方法

### 2.1.1 添加、修改操作

>Object put(Object key,Object value)：将指定key-value添加到(或修改)当前map对象中。
> void putAll(Map m):将m中的所有key-value对存放到当前map中

### 2.1.2 删除操作

>Object remove(Object key)：移除指定key的key-value对，并返回value
> void clear()：清空当前map中的所有数据

### 2.1.3 元素查询的操作

>Object get(Object key)：获取指定key对应的value
> boolean containsKey(Object key)：是否包含指定的key
> boolean containsValue(Object value)：是否包含指定的value
> int size()：返回map中key-value对的个数
> boolean isEmpty()：判断当前map是否为空
> boolean equals(Object obj)：判断当前map和参数对象obj是否相等
### 2.1.4 元视图操作的方法

> Set keySet()：返回所有key构成的Set集合
> Collection values()：返回所有value构成的Collection集合
> Set entrySet()：返回所有key-value对构成的Set集合

示例如下：
```java
import java.util.Collection;
import java.util.HashMap;
import java.util.Map;
import java.util.Set;


public class MapDemo {
    public static void main(String[] args) {
        HashMap<String, Integer> map = new HashMap<String, Integer>();
        map.put("Litan", 26);
        map.put("ZhangSan", 22);
        map.put("LiSi", 25);
        map.put("WangWu", 19);
  
        System.out.println("Map成员：" + map);

        map.remove("WangWu");
        System.out.println("Map成员详情：" + map);

        System.out.println("Litan的年龄：" + map.get("Litan"));
        System.out.println("是否存在LiSi：" + map.containsKey("LiSi"));
        System.out.println("是否存在年龄为22的成员：" + map.containsValue(22));
        System.out.println("Map成员个数：" + map.size());
        System.out.println("Map是否为空：" + map.isEmpty());

        HashMap<String, Integer> map2 = new HashMap<>();
        map2.putAll(map);
        map2.put("WangWu", 19);
        System.out.println("map2与map是否相等：" + map.equals(map2));

        Set<String> keySet = map.keySet();
        System.out.println("Map成员姓名如下：");
        for (String key : keySet) {
            System.out.println(key);
        }

        Collection<Integer> valueSet = map.values();
        System.out.println("Map成员年龄如下：");
        for (int age : valueSet) {
            System.out.println(age);
        }

        System.out.println("Map成员详情遍历：");
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            System.out.println(entry.getKey() + "的年龄是：" +
            entry.getValue() + "岁");
        }
    }
}
```

运行结果结果如下：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20231028115138.png)

## 2.2 HashMap

1. HashMap是线程不安全的。允许添加 null 键和 null 值。
2. 存储数据采用的哈希表结构，底层使用一维数组+单向链表+红黑树进行key-value数据的存储。与HashSet一样，元素的存取顺序不能保证一致。
3. **HashMap 判断两个key相等的标准是：两个 key 的hashCode值相等，通过 equals() 方法返回 true。**
4. **HashMap 判断两个value相等的标准是：两个 value 通过 equals() 方法返回 true。**

## 2.3 LinkedHashMap

其Entry的key按添加时的顺序保存其key的相对顺序
示例
```java
import java.util.LinkedHashMap;
import java.util.Map;


public class LinkedHashMapDemo {
    public static void main(String[] args) {
        LinkedHashMap<String, Integer> map = new LinkedHashMap<String, Integer>();
        map.put("Litan", 26);
        map.put("ZhangSan", 22);
        map.put("LiSi", 25);
        map.put("WangWu", 19);

        for (Map.Entry<String, Integer> entry: map.entrySet()) {
            System.out.println(entry.getKey() + "的年龄为：" + entry.getValue() + " 岁");
        }
    }
}
```

运行结果如下：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20231028121150.png)

## 2.4 TreeMap

TreeMap即有序的、支持排序的Map，排序的逻辑和TreeSet类似，TreeMap是按key排序，因此，对于key所属的类，实现排序逻辑为[Java比较器](C常用类和基础API#三、Java比较器)。TreeMap对两个key排序的标准：两个key通过compareTo()方法或者compare()方法的返回值是否大于零。

示例：
```java
import java.util.Comparator;
import java.util.Map;
import java.util.TreeMap;

import chapter12.node05.User;


public class TreeMapDemo {
    // key-value的关系 {User: Grade}
    public static void main(String[] args) {
        TreeMap<User, Integer> treeMap = new TreeMap<User, Integer>();
        treeMap.put(new User("Tom",12), 67);
        treeMap.put(new User("Rose",23), 74);
        treeMap.put(new User("Jerry",2), 80);
        treeMap.put(new User("Eric",18), 90);
        treeMap.put(new User("Tommy",44), 55);
        treeMap.put(new User("Jim",23), 43);
        treeMap.put(new User("Maria",18), 95);
        treeMap.put(new User("Jack",23), 86);

        // Comparable 自然排序: 按照age从小到大的顺序排列,如果age相同，则按照name从大到小的顺序排列
        User user;
        System.out.println("User的字典排序: 按照age从小到大的顺序排列,如果age相同，则按照name从大到小的顺序排列:");
        for (Map.Entry<User, Integer> userEntry : treeMap.entrySet()) {
            user = userEntry.getKey();
            System.out.println(user.getName() + "的年龄为：" + user.getAge()
            + ", 成绩为：" + userEntry.getValue());  
        }

        // Comparator 定制排序 :按照User的姓名的从小到大的顺序排列
        Comparator<User> comparator = new Comparator<User>() {
            @Override
            public int compare(User o1, User o2) {
                //按照User的姓名的从小到大的顺序排列
                return o1.getName().compareTo(o2.getName());
            }
        };
  

        TreeMap<User, Integer> treeMap2 = new TreeMap<User, Integer>(comparator);
        treeMap2.putAll(treeMap);
        System.out.println("定制排序 :按照User的姓名的从小到大的顺序排列:");
        for (Map.Entry<User, Integer> userEntry : treeMap2.entrySet()) {
            user = userEntry.getKey();
            System.out.println(user.getName() + "的年龄为：" + user.getAge()
            + ", 成绩为：" + userEntry.getValue());  
        }
    }
}
```

运行结果如下：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20231028152532.png)

## 2.5 Properties

- Properties 类是 Hashtable 的子类，该对象用于处理属性文件。
- 由于属性（配置文件）文件里的 key、value 都是字符串类型，所以 Properties 中要求 key 和 value 都是字符串类型。
- 存取数据时，建议使用setProperty(String key,String value)方法和getProperty(String key)方法。

> 使用Properties时，需预先建立对应的属性配置文件

使用示例：

```java
import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.util.Properties;

  
public class PropertiesDemo {
    public static void main(String[] args) throws FileNotFoundException, IOException{
        File file = new File("info.properties");
        System.out.println(file.getAbsolutePath()); // 打印文件所在路径
  
        FileInputStream fis = new FileInputStream(file);

        Properties properties = new Properties();
        properties.load(fis); // 加载配置文件

        // 读取配置文件
        String name = properties.getProperty("name");
        String password = properties.getProperty("password");

        System.out.println("name: " + name);
        System.out.println("password: " + password);
    }
}
```

其中，info.properties（等号两边不留空格）的内容为：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20231028180219.png)

运行结果为：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20231028180335.png)

# 三、Collections常用静态工具方法

## 3.1 排序操作

> reverse(List)：反转 List 中元素的顺序。
> shuffle(List)：对 List 集合元素进行随机排序。
> sort(List)：根据元素的自然顺序对指定 List 集合元素按升序排序。
> sort(List，Comparator)：根据指定的 Comparator 产生的顺序对 List 集合元素进行排序。
> swap(List，int， int)：将指定 list 集合中的 i 处元素和 j 处元素进行交换。

## 3.2 查找

> Object max(Collection)：根据元素的自然顺序，返回给定集合中的最大元素。
> Object max(Collection，Comparator)：根据 Comparator 指定的顺序，返回给定集合中的最大元素。
> Object min(Collection)：根据元素的自然顺序，返回给定集合中的最小元素。
> Object min(Collection，Comparator)：根据 Comparator 指定的顺序，返回给定集合中的最小元素。
> int binarySearch(List list,T key)在List集合中查找某个元素的下标，但是List的元素必须是T或T的子类对象，而且必须是可比较大小的，即支持自然排序的。而且集合也事先必须是有序的，否则结果不确定。> int binarySearch(List list,T key,Comparator c)在List集合中查找某个元素的下标，但是List的元素必须是T或T的子类对象，而且集合也事先必须是按照c比较器规则进行排序过的，否则结果不确定。
> int frequency(Collection c，Object o)：返回指定集合中指定元素的出现次数。

## 3.3 复制替换

> void copy(List dest,List src)：将src中的内容复制到dest中
> boolean replaceAll(List list，Object oldVal，Object newVal)：使用新值替换 List 对象的所有旧值
> 提供了多个unmodifiableXxx()方法，该方法返回指定 Xxx的不可修改的视图。

## 3.4 添加

> boolean addAll(Collection c,T... elements)将所有指定元素添加到指定 collection 中。

## 3.5 同步

> Collections 类中提供了多个 synchronizedXxx() 方法，该方法可使将指定集合包装成线程同步的集合，从而可以解决多线程并发访问集合时的线程安全问题。