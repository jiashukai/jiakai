# ArrayList与LinkedList

## 1.ArrayList
### 1.1 ArrayList 概述
<kbd>ArrayList是基于数组实现的，是一个动态数组，其容量自动增长。实现了所有可选列表操作，并允许包括null在内的所有元素。</kbd>

每个ArrayList实例都有一个容量，该容量是只用来存储列表元素数组的大小，它总是至少等于列表的大小。随着向ArrayList中不断添加元素，该容量也自动增长。自动增长会带来数据项数组的重新拷贝，因此，如果可预知数据量的多少，可在构造ArrayList时指定其容量。在添加大量元素前，应用程序也可以使用ensureCapacity操作来增加ArrayList实例的容量，这样可以减少递增式再分配的数量。

ArrayList不是线程安全的，只能在单线程环境下，多线程环境下可以考虑用Collections.synchronizedList(List list)函数返回一个线程安全的ArrayList类，也可以使用concurrent并发包下的CopyOnWriteArrayList类。

ArrayList实现了Serializable接口，因此它支持序列化，能够序列化传输，实现了RandomAccess接口，支持快速随机访问，实际上就是通过下标序号进行快速访问，实现了Cloneable接口，能够被克隆。
### 1.2 ArrayList的实现
**1. 私有属性**
ArrayList定义两个属性
```
//对象数组：ArrayList的底层数据结构
     transient Object[] elementData;
    //elementData中已存放的元素的个数，注意：不是elementData的容量
    private int size;
```
**transient**

java的serialization提供了一种持久化对象实例的机制。当持久化对象时，可能有一个特殊的对象数据成员，我们不想用serialization机制来保存它，为了在一个特定对象的一个域上关闭serialization可以在这个域前面加上关键字transient

上面的elementData属性采用了transient来修饰，表明其不使用Java默认的序列化机制来实例化，但是该属性是ArrayList的底层数据结构，在网络传输中一定需要将其序列化，之后使用的时候还需要反序列。ArrayList自己实现了序列化和反序列化

**2.构造方法**
ArrayList提供了三种构造方法：可以构造一个默认初始容量为10 的空列表，构造一个指定初始容量的空列表，以及构造一个包含指定collection的列表元素，这些元素按照该collection的迭代器返回它们的顺序排列的。

```
public ArrayList(int initialCapacity) {
       if (initialCapacity > 0) {
           this.elementData = new Object[initialCapacity];
       } else if (initialCapacity == 0) {
           this.elementData = EMPTY_ELEMENTDATA;
       } else {
           throw new IllegalArgumentException("Illegal Capacity: "+
                                              initialCapacity);
       }
   }    

   //构造函数二，使用默认的DEFAULTCAPACITY_EMPTY_ELEMENTDATA赋值
       public ArrayList() {
       this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
   }

   //构造一个传入的集合，作为数组的数据
       public ArrayList(Collection<? extends E> c) {
       elementData = c.toArray();
       if ((size = elementData.length) != 0) {
           // c.toArray might (incorrectly) not return Object[] (see 6260652)
           if (elementData.getClass() != Object[].class)
               elementData = Arrays.copyOf(elementData, size, Object[].class);
       } else {
           // replace with empty array.
           this.elementData = EMPTY_ELEMENTDATA;
       }
   }
```
**3.添加方法**
ArrayList提供了set(int index ,E element),add(E e),add(int index,E element),addAll(Collection<?extends E> c),addAll(int index,Collection<? extends E> c)这些添加元素的方法
```
// 用指定的元素替代此列表中指定位置上的元素，并返回以前位于该位置上的元素。  
 public E set(int index, E element) {  
    RangeCheck(index);  

    E oldValue = (E) elementData[index];  
    elementData[index] = element;  
    return oldValue;  
 }    
// 将指定的元素添加到此列表的尾部。  
 public boolean add(E e) {  
    ensureCapacity(size + 1);   
    elementData[size++] = e;  
   return true;  
 }    
 // 将指定的元素插入此列表中的指定位置。  
 // 如果当前位置有元素，则向右移动当前位于该位置的元素以及所有后续元素（将其索引加1）。  
 public void add(int index, E element) {  
    if (index > size || index < 0)  
        throw new IndexOutOfBoundsException("Index: "+index+", Size: "+size);  
   // 如果数组长度不足，将进行扩容。  
    ensureCapacity(size+1);  // Increments modCount!!  
    // 将 elementData中从Index位置开始、长度为size-index的元素，  
   // 拷贝到从下标为index+1位置开始的新的elementData数组中。  
   // 即将当前位于该位置的元素以及所有后续元素右移一个位置。  
   System.arraycopy(elementData, index, elementData, index + 1, size - index);  
   elementData[index] = element;  
    size++;  
 }    
 // 按照指定collection的迭代器所返回的元素顺序，将该collection中的所有元素添加到此列表的尾部。  
 public boolean addAll(Collection<? extends E> c) {  
   Object[] a = c.toArray();  
   int numNew = a.length;  
  ensureCapacity(size + numNew);  // Increments modCount  
   System.arraycopy(a, 0, elementData, size, numNew);  
   size += numNew;  
  return numNew != 0;  
 }    
 // 从指定的位置开始，将指定collection中的所有元素插入到此列表中。  
 public boolean addAll(int index, Collection<? extends E> c) {  
   if (index > size || index < 0)  
        throw new IndexOutOfBoundsException(  
            "Index: " + index + ", Size: " + size);  

    Object[] a = c.toArray();  
   int numNew = a.length;  
    ensureCapacity(size + numNew);  // Increments modCount  

    int numMoved = size - index;  
    if (numMoved > 0)  
    System.arraycopy(elementData, index, elementData, index + numNew, numMoved);  
    System.arraycopy(a, 0, elementData, index, numNew);  
    size += numNew;  
    return numNew != 0;  
   }  
```

**Add（E  element）分析**

这里一步步分析，在调用了add(E e)的方法第一步，我们看到了它调用了ensureCapacityInternal(size + 1)方法，在这个方法里面首先判断了数组是不是一个长度为0的空数组，如果是的话就给它容量赋值为默认的容量大小也就是10，然后调用了ensureExplicitCapacity方法，这个方法里面记录了modCount+1之后，并判断了当前的容量是否大于数组当前的长度，如果大于当前数组的长度就开始进行扩容操作调用方法 grow(minCapacity)，扩容的长度是增加了原来数组数组的一半大小，然后并判断了是否达到了数组扩容的上限并赋值，接着把旧数组的数据拷贝到扩容后的新数组里面再次赋值给旧数组，最后把新添加的元素赋值给了扩容后的size+1的位置里面。

**Add（int index ，E element）分析**

这里面主要是给指定位置添加一个元素，ArrayList首先检查是否索引越界，如果没有越界，就检查是否需要扩容，然后将index位置之后的所有数据，整体拷贝到index+1开始的位置，然后就可以把新加入的数据放到index这个位置，而index前面的数据不需要移动，在这里我们可以看到给指定位置插入数据ArrayList是一项大动作比较耗性能。

**4.移除**
1. 根据下标移除
```
public E remove(int index) {
        //检查是否越界
        rangeCheck(index);
        //记录修改次数
        modCount++;
        //获取移除位置上的值
        E oldValue = elementData(index);
        //获取要移动元素的个数
        int numMoved = size - index - 1;
        if (numMoved > 0)
        //拷贝移动的所有数据到index位置上
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        //把size-1的位置的元素赋值null，方便gc
        elementData[--size] = null; // clear to let GC do its work
        //最终返回旧的数据
        return oldValue;
    }
```
2. 根据元素移除
```
、
public boolean remove(Object o) {
    //等于null值的移除
        if (o == null) {
         //遍历数组
            for (int index = 0; index < size; index++)
            //找到集合里面第一个等于null的元素
                if (elementData[index] == null) {
                //然后移除
                    fastRemove(index);
                    return true;
                }
        } else {
        //非null情况下，遍历每一个元素通过equals比较
            for (int index = 0; index < size; index++)
                if (o.equals(elementData[index])) {
                //然后移除
                    fastRemove(index);
                    return true;
                }
        }
        return false;
    }

   //该方法与通过下标移除的原理一样，整体左移
    private void fastRemove(int index) {
        modCount++;
        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        elementData[--size] = null; // clear to let GC do its work
    }
```

**首先通过代码可以看到，当移除成功后返回true，否则返回false。remove(Object o)中通过遍历element寻找是否存在传入对象，一旦找到就调用fastRemove移除对象。为什么找到了元素就知道了index，不通过remove(index)来移除元素呢？因为fastRemove跳过了判断边界的处理，因为找到元素就相当于确定了index不会超过边界，而且fastRemove并不返回被移除的元素。**
**5.查询**
```

public E get(int index) {
      //检查是否越界
        rangeCheck(index);
        //返回指定位置上的元素
        return elementData(index);
    }
```
**6.修改**
```
public E set(int index, E element) {
    //检查是否越界
        rangeCheck(index);
        //获取旧的元素值
        E oldValue = elementData(index);
        //新元素赋值
        elementData[index] = element;
        //返回旧的元素值
        return oldValue;
    }
```
##### 总结
ArrayList总体来说比较简单，不过ArrayList还有以下一些特点：

ArrayList自己实现了序列化和反序列化的方法，因为它自己实现了 
private void writeObject(java.io.ObjectOutputStream s)和 private void readObject(java.io.ObjectInputStream s) 方法

ArrayList基于数组方式实现，无容量的限制（会扩容）

添加元素时可能要扩容（所以最好预判一下），删除元素时不会减少容量（若希望减少容量，trimToSize()），删除元素时，将删除掉的位置元素置为null，下次gc就会回收这些元素所占的内存空间。

线程不安全

add(int index, E element)：添加元素到数组中指定位置的时候，需要将该位置及其后边所有的元素都整块向后复制一位

get(int index)：获取指定位置上的元素时，可以通过索引直接获取（O(1)）

remove(Object o)需要遍历数组

remove(int index)不需要遍历数组，只需判断index是否符合条件即可，效率比remove(Object o)高

contains(E)需要遍历数组

使用iterator遍历可能会引发多线程异常


## LinkedList源码分析
**LinkedList实现原理总结**
1. 数据存储是基于双向链表实现的。
2. 插入数据很快。先是在双向链表中找到要插入节点的位置index，找到之后再插入一个新节点。双向链表查找index位置的节点时，有一个加速动作：若index < 双向链表长度的1/2，则从前向后查找；否则，从后向前查找。
3. 删除数据很快。先是在双向链表中找到要插入节点的位置index，找到之后，进行如下操作：node.previous.next = node.next;node.next.previous = node.previous; node =null.查找节点过程和插入一样。
4. 获取数据很慢，需要从head节点进行查找。
5. 遍历数据很慢，每次获取数据都需要从头开始。

### 1.概述
LinkedList底层是基于双向链表实现的，链表在内存中是不连续的，而是通过引用来关联所有的元素，所以链表的优点在于添加和删除元素比较快，因为只是移动指针，并且不需要判断是否扩容，缺点是查询和遍历效率比较低

**2.1 类结构**

LinkedList底层是双链表，实现了list接口可以有队列操作，实现了Deque接口可以有双端队列操作，实现了所有可选的list操作并且允许存储任何元素，包括Null

所有的操作都体现了双链表的结构，所以进入List的操作从头开始或则结尾遍历List，无论任何一个指定的索引

注意：这些实现都是不同步的，意味着线程不安全，如果有多个线程同时访问双链表，至少有一个线程在结构上修改了list，name必须在外部加上同步操作（synchronized）(所谓结构化修改是指增加或则修改一个元素或则多个元素，重新设置元素的值不是结构化修改)，通常通过自然地同步一些对象来封装List来完成

如果没有这样的对象存在，name应该使用Collections.synchronizedList来封装链表。最好是在创建时完成，以防止意外的对链表进行非同步的访问。

此类迭代器和迭代方法返回的迭代器是快速失败的：如果链表在迭代器被创建后的任何时间呗结构化修改，除非是通过迭代器的remove或则add方法操作的，否则将抛出concurrentModificationException异常

**注意**
迭代器快速失败的行为不能保证，一把来说，在存在并发修改的情况下不能确保任何的承诺，快速失败的迭代器尽最大努力抛出异常。因此，编写依赖于此异常的程序的方式是错误的。
迭代器的快速失败行为应该仅用于错误检测。

**成员变量和构造方法**
 1. LinkedList的核心数据结构
 ```
 private static class Node<E> {
        E item;        //当前节点
        Node<E> next;  //下一个节点
        Node<E> prev;  //上一个节点

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
```
 2. 成员变量和构造方法
 ```
 transient int size = 0;      //当前存储元素的个数

     transient Node<E> first; //首节点

     transient Node<E> last  ;//末节点

     //无参数构造方法
     public LinkedList() {
     }

    //有参数构造方法
     public LinkedList(Collection<? extends E> c) {
         this();
         addAll(c);
     }
```
可以看出LinkedList有两个构造方法，一个有参，一个无参，一个有参的够着函数的功能是通过一个集合参数并把里面所有的元素给插入到LinkedList中，注意这里之所以说是插入，而不是说初始化添加，因为LinkedList并非线程安全，完全有可能在this()方法调用之后，后面有其他线程向里面插入了数据

**2.3 常用方法**
1. addAll方法
```
//在链表的尾端追加指定集合的所有元素，按指定的迭代器的集合顺序返回，在这个操作执行总是如果指定的集合被修改了，那么该行为操作将提示未定义

public boolean addAll(Collection<? extends E> c) {
    return addAll(size, c);
}
public boolean addAll(int index, Collection<? extends E> c) {

	 //检查index是否越界，index=size+1

    checkPositionIndex(index);

    //  将集合参数转化为数组

    Object[] a = c.toArray();
    int numNew = a.length;             //要插入的集合长度
    if (numNew == 0)
        return false;

                                      // 定义pred和succ两个Node对象，用于标识要插入元素的前置节点和后置节点

    Node<E> pred, succ;

                                      //这里为什么要写if..else？

                                       //因为该方法不一定是从上层方法addAll(size, c)过来的，还有可能是直接调用了addAll(int index, Collection<? extends E> c)
                                        方法，从上层addAll(size, c)跳转过来的，size=index也就从尾部插入，但是直接调用的该方法，则从传进来的参数index这个位置（肯能是任何位置）插入

    if (index == size) {             //表明是从尾部插入
        succ = null;                 //从尾部插入，后置节点为null
        pred = last;                 //从尾部插入，前置节点为当前LinkedList中的最后一个节点
    } else {                         //表明不是从尾部插入
        succ = node(index);          //查到当前LinkedList中位置为index的节点并把它赋给要插入元素的后置节点
        pred = succ.prev;            //把上一步得到的节点的前置节点赋值给要插入元素的后置节点
    }

    for (Object o : a) {             //变量集合参数
        @SuppressWarnings("unchecked") E e = (E) o;
        Node<E> newNode = new Node<>(pred, e, null);
        if (pred == null)            //说明插入之前当前链表是空链表
            first = newNode;         //新节点是第一个节点
        else
            pred.next = newNode;    //设置插入元素的的前置节点的后置节点为新节点
        pred = newNode;             //更改指向后将新节点对象赋给pred作为下次循环中新插入节点的前一个对象节点，依次循环
    }
                                   //此时pred代表集合元素的插入完后的最后一个节点对象
    if (succ == null) {           //结尾添加的话在添加完集合元素后将最后一个集合的节点对象pred作为last
        last = pred;
    } else {
        pred.next = succ;         //将集合元素的最后一个节点对象的next指针指向原index位置上的Node对象
        succ.prev = pred;         //将原index位置上的pred指针对象指向集合的最后一个对象
    }

    size += numNew;              //修改当前元素的数量
    modCount++;
    return true;
}
/**
 * Returns the (non-null) Node at the specified element index.
 * 返回index位置的非空节点
 * 折半查询
 */
Node<E> node(int index) {
    /**
     * 如果index小于当前元素个数的一半，则从前向后遍历查询 ，否则从后向前遍历查询
     */
    if (index < (size >> 1)) {
        Node<E> x = first;
        for (int i = 0; i < index; i++)
            x = x.next;
        return x;
    } else {
        Node<E> x = last;
        for (int i = size - 1; i > index; i--)
            x = x.prev;
        return x;
    }
}
```
**这里面主要用了两个方法：**

**1.addAll(int index,Collection<? extends E> c),这里面首先是会判断是否会出现索引越界，然后定义pred和succ两个Node对象，用于标识要插入元素的前置节点和后置节点**
**2.node(int index):这个方法的主要功能是找到index位置的Node节点，源码上利用责办查询进行优化，即使这样，遍历和查询效率还是比较差**
2. add方法

```
public boolean add(E e) {
    linkLast(e);
    return true;
}
void linkLast(E e) {

	  // 获取当前链表的最后一个节点

    final Node<E> l = last;

   // 创建一个以当前最后一个节点为之前节点的节点

    final Node<E> newNode = new Node<>(l, e, null);
    last = newNode;

     // 空表，首次插入

    if (l == null)
        first = newNode;
    else
        l.next = newNode;//不是首次插入，则最后一个节点的后置节点地址赋值给新节点
    size++;
    modCount++;
}

```
**可以看到add方法每次都会把新增节点放在链表的最后一位，正因为放在链表的末位，所以链表的添加性能可以看成O(1)操作**
3. remove方法
移除方法主要有两个：

(1). 根据元素移除
```

 // 从第一个节点循环指针查找

public boolean remove(Object o) {
    //如果移除的数据为Null
    if (o == null) {
        //遍历找到第一个为null的节点，然后移除掉
        for (Node<E> x = first; x != null; x = x.next) {
            if (x.item == null) {
                unlink(x);
                return true;
            }
        }
    } else {
    //遍历找到第一条不为null与参数相等的数据，然后移除掉
        for (Node<E> x = first; x != null; x = x.next) {
            if (o.equals(x.item)) {
                unlink(x);
                return true;
            }
        }
    }
    return false;
}

E unlink(Node<E> x) {
    // assert x != null;
	  // 移除的数据
    final E element = x.item;
    //移除节点的后置节点
    final Node<E> next = x.next;
    //移除节点的前置节点
    final Node<E> prev = x.prev;

    if (prev == null) {  //前一个节点为null  说明要删除的节点就是 头结点
        first = next;
    } else {
        prev.next = next;
        x.prev = null;
    }

    if (next == null) {
        last = prev;
    } else {
        next.prev = prev;
        x.next = null;
    }

    x.item = null;
    size--;
    modCount++;
    return element;
}
```
（2）. 根据索引移除
```
public E remove(int index) {
    checkElementIndex(index);
    return unlink(node(index));
}
```
**从上面的源码可以看出根据index移除 里面调用了node(index)方法来查找需要移除的节点，而根据Object移除的时候，则是进行了整个链表的遍历，然后再卸载节点。**
**除此之外链表还有没有任何参数的remove，removeFirst，removeLast方法，其中remove方法本质是调用了removeFirst方法**
**这里能够总结出链表基于首尾节点的删除可以看成O(1)操作，而非首尾的删除最坏的情况下能够达到O(n)操作，因为要遍历查询指定节点，所以性能较差**
4. get方法   
get系的方法有三个：分别是get(index),getFirst(),getLast()
```
public E get(int index) {
        checkElementIndex(index);//是否越界
        return node(index).item;//折半遍历查询
    }
```
**get(index)方法本质调用了node(index),这个方法的性能O(n),其他的getFirst和getLast为O(1)**
5. set方法
```
public E set(int index, E element) {
    checkElementIndex(index);   //检查是否越界
    Node<E> x = node(index);    //折半查询索引为index的节点
    E oldVal = x.item;          //查询index节点原来的数据值
    x.item = element;           //将新值插入
    return oldVal;              //返回旧值
}
```
**set方法依旧是调用的node方法，所以链表在指定位置更新数据，性能也一般**
6. clear()方法
```
public void clear() {
         //遍历所有的数据，置位null      
        for (Node<E> x = first; x != null; ) {
            Node<E> next = x.next;
            x.item = null;
            x.next = null;
            x.prev = null;
            x = next;
        }
        first = last = null;
        size = 0;
        modCount++;
    }
```
**clear方法比较简单，就是所有的节点的数据置位null，方便垃圾回收**
7. toArray方法分析
```
public Object[] toArray() {
      //声明长度一样的数组
        Object[] result = new Object[size];
        int i = 0;
        for (Node<E> x = first; x != null; x = x.next)
            result[i++] = x.item;
        return result;
    }
```
**声明一个长度一样的数组，依次遍历所有数据放入数组**
8. 序列化和反序列化
```
//序列化
    private void writeObject(java.io.ObjectOutputStream s)
        throws java.io.IOException {
        s.defaultWriteObject();
        //先写入大小
        s.writeInt(size);
        //再依次遍历链表写入字节流中
        for (Node<E> x = first; x != null; x = x.next)
            s.writeObject(x.item);
    }

    //反序列化
        private void readObject(java.io.ObjectInputStream s)
        throws java.io.IOException, ClassNotFoundException {
        // Read in any hidden serialization magic
        s.defaultReadObject();

         //先读取大小
        int size = s.readInt();
        //再依次读取元素，每次都追加到链表末尾
        for (int i = 0; i < size; i++)
            linkLast((E)s.readObject());
    }
```
**这里我们看到链表中也自定义了序列化和反序列化的方法，在序列化时只写入x.item而并不是整个Node，这样做避免java自带的序列化机制会把整个Node的数据给写入序列化，并且如果Node还是双端链表的数据结构，那么无疑会导致重复两倍的空间浪费。**

**在反序列化时我们看到先读取size，然后根据size依次循环读取item，并重新生成双端链表的数据结构，依次追加到链表的尾部。**

#ArrayList和LinkedList的区别
1. ArrayList是基于动态数组的数据结构，每个元素在内存中存储地址时连续的；LinkedList是是基于链表的额数据结构，每个元素内容包括 上一个节点，当前节点的值，下一个节点，也是由于这一性质支持了每个元素在内存中的分布存储。
2. 为了使得突破动态长度数组而衍生的ArrayList初始容量为10，每次扩容会固定为之前的1.5倍，所以当你ArrayList达到一定量之后回事一种很大的浪费，并且每次扩容的过程是内部数组复制到新数组；LinkedList的每一个元素都需要消耗一定的空间
3. 对于每个元素的检索，ArrayList要优于LinkedList。因为ArrayList从一定意义上来说，就是复杂的数组，所以基于数组index的检索性能显然要高于通过for循环来查找每个元素的LinkedList。
4. 元素的插入删除的效率对比，要视插入删除的位置来分析，各有优劣

       在列表首位添加（删除）元素，LinkedList性能远远优于ArrayList，原因在于ArrayList要后移（前移）每个元素的索引和数组扩容（删除元素时则不需要扩容）
       而LinkedList则直接增加元素，修改原第一个元素节点的上一个节点即可，删除同理。

在列表中间删除（添加）元素，总的来说位置越靠前LinkedList性能优于ArrayList。

在列表末位删除（添加）元素，新歌能相差不大。
