题源：https://leetcode-cn.com/problems/lfu-cache/

最不经常使用（LFU）跟最近最久未使用（LRU）不一样，LFU是按照**使用次数（当使用次数相同时，按照使用时间的先后）**来决定优先级的，而LRU是按照**使用时间的先后**来决定优先级的。

我最初的想法是哈希表+有序双向链表。链表节点存放数据的value和使用次数和最近使用的时间，排序方式是先比较使用次数，再比较使用时间；哈希表的key是数据的key，哈希表的value是链表节点指针。get操作可以直接获取值，但是get之后有可能需要将该节点的位置放到前面，所以get的操作无法在$O(1)$时间内完成。当进行put的操作时，如果键已存在，那也可能需要将该节点放到前面，所以put的操作也无法在$O(1)$时间内完成。

##### 方法一：哈希表+平衡二叉树

这里有个知识点，set的底层实现时红黑树，并且set内的数据是有序且不可重复的，所以哈希表的key就是数据的key，哈希表的值就是set内的值，这里用自定义的数据结构。put和get的时间复杂度都是$O(logn)$，其实跟我最初的想法差不多。代码如下：

```c++
//这里自定义的数据结构是关键，算是技巧，要看懂
struct Node {
    int cnt, time, key, value;

    Node(int _cnt, int _time, int _key, int _value):cnt(_cnt), time(_time), key(_key), value(_value){}
    
    bool operator < (const Node& rhs) const {
        return cnt == rhs.cnt ? time < rhs.time : cnt < rhs.cnt;
    }
};
class LFUCache {
    // 缓存容量，时间戳
    int capacity, time;
    unordered_map<int, Node> key_table;
    set<Node> S;
public:
    LFUCache(int _capacity) {
        capacity = _capacity;
        time = 0;
        key_table.clear();
        S.clear();
    }
    
    int get(int key) {
        if (capacity == 0) return -1;
        auto it = key_table.find(key);
        // 如果哈希表中没有键 key，返回 -1
        if (it == key_table.end()) return -1;
        // 从哈希表中得到旧的缓存
        Node cache = it -> second;
        // 从平衡二叉树中删除旧的缓存
        S.erase(cache);
        // 将旧缓存更新
        cache.cnt += 1;
        cache.time = ++time;
        // 将新缓存重新放入哈希表和平衡二叉树中
        S.insert(cache);
        it -> second = cache;
        return cache.value;
    }
    
    void put(int key, int value) {
        if (capacity == 0) return;
        auto it = key_table.find(key);
        if (it == key_table.end()) {
            // 如果到达缓存容量上限
            if (key_table.size() == capacity) {
                // 从哈希表和平衡二叉树中删除最近最少使用的缓存
                key_table.erase(S.begin() -> key);
                S.erase(S.begin());
            }
            // 创建新的缓存
            Node cache = Node(1, ++time, key, value);
            // 将新缓存放入哈希表和平衡二叉树中
            key_table.insert(make_pair(key, cache));
            S.insert(cache);
        }
        else {
            // 这里和 get() 函数类似
            Node cache = it -> second;
            S.erase(cache);
            cache.cnt += 1;
            cache.time = ++time;
            cache.value = value;
            S.insert(cache);
            it -> second = cache;
        }
    }
};
```

##### 方法二：双哈希表+双向链表

方法一其实很简单，主要是那个Node的实现算是一个技巧，另外set的特性也算是个技巧，但它们还是无法在$O(1)$的时间复杂度内进行get和put操作。

我们定义两个哈希表，第一个freqTable以使用次数为key，value是一个双向链表（节点存放数据的value即可），存放了所有该使用次数的键值对，该链表使用头插法，保证了该链表内以使用时间为优先级。第二个keyTable以数据的key为key，以该key所在链表节点为value。当进行get操作时，通过keyTable的索引可以直接得到数据的value，同时，由于该key的使用次数+1，所以还需要将该链表节点直接插入到freqTable[+1]中，总的过程是$O(1)$；当进行put操作时，如果容量不满，那直接插入到freqTable[1]的尾部即可，如果容量满了，那直接去找最小使用频率minFreq（需要实时维护）的链表，将freqTable[minFreq]的尾部删除即可。**小技巧：双向链表不用自己实现，用list即可，push_front可以从头部插入节点，pop_back可以从尾部删除节点，很是方便。**代码如下：

```c++
// 缓存的节点信息
struct Node {
    int key, val, freq;
    Node(int _key,int _val,int _freq): key(_key), val(_val), freq(_freq){}
};
class LFUCache {
    int minfreq, capacity;
    unordered_map<int, list<Node>::iterator> key_table; //存迭代器是为了方便操作，直接存list也行
    unordered_map<int, list<Node>> freq_table;
public:
    LFUCache(int _capacity) {
        minfreq = 0;
        capacity = _capacity;
        key_table.clear();
        freq_table.clear();
    }
    
    int get(int key) {
        if (capacity == 0) return -1;
        auto it = key_table.find(key);
        if (it == key_table.end()) return -1;
        list<Node>::iterator node = it -> second;
        int val = node -> val, freq = node -> freq;
        freq_table[freq].erase(node);
        // 如果当前链表为空，我们需要在哈希表中删除，且更新minFreq
        if (freq_table[freq].size() == 0) {
            freq_table.erase(freq);
            if (minfreq == freq) minfreq += 1;
        }
        // 插入到 freq + 1 中
        freq_table[freq + 1].push_front(Node(key, val, freq + 1));
        key_table[key] = freq_table[freq + 1].begin();
        return val;
    }
    
    void put(int key, int value) {
        if (capacity == 0) return;
        auto it = key_table.find(key);
        if (it == key_table.end()) {
            // 缓存已满，需要进行删除操作
            if (key_table.size() == capacity) {
                // 通过 minFreq 拿到 freq_table[minFreq] 链表的末尾节点
                auto it2 = freq_table[minfreq].back();
                key_table.erase(it2.key);
                freq_table[minfreq].pop_back();
                if (freq_table[minfreq].size() == 0) {
                    freq_table.erase(minfreq);
                }
            } 
            freq_table[1].push_front(Node(key, value, 1));
            key_table[key] = freq_table[1].begin();
            minfreq = 1;
        } else {
            // 与 get 操作基本一致，除了需要更新缓存的值
            list<Node>::iterator node = it -> second;
            int freq = node -> freq;
            freq_table[freq].erase(node);
            if (freq_table[freq].size() == 0) {
                freq_table.erase(freq);
                if (minfreq == freq) minfreq += 1;
            }
            freq_table[freq + 1].push_front(Node(key, value, freq + 1));
            key_table[key] = freq_table[freq + 1].begin();
        }
    }
};
```

如果自己实现的话，就是这样的：

```c++
struct doubleLinkedNode{
    int key;
    int val;
    int frequency;
    doubleLinkedNode* pre;
    doubleLinkedNode* next;
    doubleLinkedNode(int _key, int _val):key(_key),val(_val),frequency(1),pre(nullptr),next(nullptr){}
};
struct doubleLinkedList{
    doubleLinkedNode* head;
    doubleLinkedNode* tail;
    doubleLinkedList(){
        head = new doubleLinkedNode(-1, -1);
        tail = new doubleLinkedNode(-1, -1);
        head->next = tail;
        tail->pre = head;
    }
};
class LFUCache {
public:
    unordered_map<int, doubleLinkedNode*> KTN;
    unordered_map<int, doubleLinkedList> FTL;
    int maxsize;
    int cursize;
    int minfrequency;
    
    LFUCache(int capacity) {
        maxsize = capacity;
        cursize = 0;
        minfrequency = 0;
    }
    int get(int key) {
        if(KTN.find(key) == KTN.end())
            return -1;
        doubleLinkedNode* targetNode = KTN[key];
        int res = targetNode->val;
        removeNode(targetNode);
        targetNode->frequency += 1;
        headInsert(targetNode);
        return res;
    }
    void put(int key, int value) {
        if(maxsize == 0)
            return;
        if(KTN.find(key) != KTN.end()){
            doubleLinkedNode* targetNode = KTN[key];
            targetNode->val = value;
            removeNode(targetNode);
            targetNode->frequency += 1;
            headInsert(targetNode);
        }else{
            if(cursize == maxsize){
                deleteNode(minfrequency);
                cursize -= 1;
            }
            KTN[key]  = new doubleLinkedNode(key, value);
            minfrequency = 1;
            headInsert(KTN[key]);
            cursize += 1;
        }
    }
    void removeNode(doubleLinkedNode* node){
        node->pre->next = node->next;
        node->next->pre = node->pre;
        if(node->frequency == minfrequency && FTL[minfrequency].head->next == FTL[minfrequency].tail){
            minfrequency += 1;
        }
    }
    void headInsert(doubleLinkedNode* node){
        node->pre = FTL[node->frequency].head;
        node->next = FTL[node->frequency].head->next;
        FTL[node->frequency].head->next = node;
        node->next->pre = node;
    }
    void deleteNode(int minfrequency){
        doubleLinkedNode* last = FTL[minfrequency].tail->pre;
        last->pre->next = last->next;
        last->next->pre = last->pre;
        KTN.erase(last->key);
    }
};
```



