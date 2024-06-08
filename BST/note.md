# 二叉搜索树：从根开始，探索问题和解决方案的核心
准备好深入探索二叉搜索树（BST）的世界了吗？系好安全带，因为我们即将踏上一段旅程.

## 核心代码：窥探二叉丛林

首先，让我们看看BST的基本结构。以下是我们树的核心代码：

```cpp
#include<iostream>
using namespace std;

template<class K>
struct BSTNode
{
    BSTNode* _left;
    BSTNode* _right;
    K _key;

    BSTNode(const K& key)
        : _left(nullptr), _right(nullptr), _key(key)
    {}
};

template<class K>
class BSTree
{
    typedef BSTNode<K> Node;
public:
    bool Insert(const K& key)
    {
        if (_root == nullptr)
        {
            _root = new Node(key);
            return true;
        }

        Node* parent = _root;
        Node* tmp = _root;
        while (tmp)
        {
            if (tmp->_key > key)
            {
                parent = tmp;
                tmp = tmp->_left;
            }
            else if (tmp->_key < key)
            {
                parent = tmp;
                tmp = tmp->_right;
            }
            else
            {
                return false;
            }
        }
        tmp = new Node(key);
        if (parent->_key > key)
        {
            parent->_left = tmp;
        }
        else
        {
            parent->_right = tmp;
        }
        return true;
    }

    void InOrder()
    {
        _InOrder(_root);
        cout << endl;
    }

    bool Find(const K& key)
    {
        Node* cur = _root;
        while (cur)
        {
            if (cur->_key < key)
            {
                cur = cur->_right;
            }
            else if (cur->_key > key)
            {
                cur = cur->_left;
            }
            else
            {
                return true;
            }
        }
        return false;
    }

    bool Erase(const K& key)
    {
        Node* parent = nullptr;
        Node* cur = _root;
        while (cur)
        {
            if (cur->_key < key)
            {
                parent = cur;
                cur = cur->_right;
            }
            else if (cur->_key > key)
            {
                parent = cur;
                cur = cur->_left;
            }
            else
            {
                if (cur->_left == nullptr)
                {
                    if (cur == _root)
                    {
                        _root = _root->_right;
                    }
                    else
                    {
                        if (parent->_left == cur)
                            parent->_left = cur->_right;
                        else
                            parent->_right = cur->_right;
                    }
                    delete cur;
                }
                else if (cur->_right == nullptr)
                {
                    if (cur == _root)
                    {
                        _root = _root->_left;
                    }
                    else
                    {
                        if (parent->_right == cur)
                            parent->_right = cur->_left;
                        else
                            parent->_left = cur->_left;
                    }
                    delete cur;
                }
                else
                {
                    Node* subParent = cur;
                    Node* subleft = cur->_right;
                    while (subleft->_left)
                    {
                        subParent = subleft;
                        subleft = subleft->_left;
                    }
                    swap(cur->_key, subleft->_key);
                    if (subParent->_left == subleft)
                        subParent->_left = subleft->_right;
                    else
                        subParent->_right = subleft->_right;
                    delete subleft;
                }
                return true;
            }
        }
        return false;
    }

private:
    void _InOrder(Node* root)
    {
        if (root == nullptr)
            return;
        _InOrder(root->_left);
        cout << root->_key << " ";
        _InOrder(root->_right);
    }

    Node* _root = nullptr;
};
```

## 核心思想：深入问题的根源

### 查找宝藏（或键值）
在BST中查找就像是一场寻宝游戏。从根节点开始比较键值。如果你的键值较小，往左走；如果较大，往右走。重复这个过程，直到找到你的键值或走到尽头（即空指针）。

### 插入新节点
插入操作非常简单：
- 如果树为空，新节点就成为根节点。
- 否则，像查找一样遍历树。当找到空位时，把你的新节点插入那里。

### 删除节点
删除操作有些复杂，有三种情况：
1. **没有孩子节点：** 直接删除节点。
2. **只有一个孩子节点：** 跳过节点，直接将其父节点链接到其孩子节点。
3. **有两个孩子节点：** 找到节点的中序后继（右子树中最小的节点），交换值，然后删除后继节点。

### 遍历树
中序遍历以升序访问节点。从最左边的节点开始，访问根节点，然后是右子树。

## 高级特性：让树更聪明

### 递归：因为谁不喜欢递归呢？
我们可以让树的操作变成递归的，以简化代码并增加优雅感。

### 析构和拷贝
为了避免内存泄漏，编写析构函数来清理节点。此外，支持拷贝功能以便轻松克隆树。

## 算法分析：时间复杂度

### 时间复杂度
- **查找、插入、删除：** 对于平衡树（如AVL树、红黑树）是O(log n)。对于偏斜树是O(n)。

**小提示：** 保持你的树平衡以确保最佳性能！

## 实际应用

### 键值查找模型
非常适合需要快速查找的系统，如门禁系统或认证系统。

### 键值/值模型
完美适用于字典或数据库，通过键查找值，使检索变得轻而易举。

### 示例：给树增加值
```cpp
#include<iostream>
using namespace std;

template<class K, class V>
struct BSTNode
{
    BSTNode<K, V>* _left;
    BSTNode<K, V>* _right;
    K _key;
    V _value;

    BSTNode(const K& key, const V& value)
        : _left(nullptr), _right(nullptr), _key(key), _value(value)
    {}
};
```
