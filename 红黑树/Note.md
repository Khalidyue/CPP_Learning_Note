# 红黑树
红黑树对每个结点增加颜色位进行控制，确保最长路径不会比最短路径长出两倍。
搜索效率确实比avl低，但是CUP效率足够高，实际上只是常数级别差距，影响不大

## 红黑树性质
![alt text](image.png)
1.红黑树非黑即红
2.根节点是黑色的
3.颜色为红的节点，则它的孩子节点是黑色->树中不能出现连续的红色节点：黑+黑/红+黑/黑+红->最短节点全黑，最长节点红黑交替
4.每条路径都包含相同的黑色节点
5.每个空节点（NIL节点）都是黑色
![](image-1.png)
所以该树有八条路径

## 定义
```cpp
enum Color
{
    RED,
    BLACK
};
template<class K,class V>
struct RBTreeNode
{
    RBTreeNode<K,V>* _left;
    RBTreeNode<K,V>* _right;
    RBTreeNode<K,V>* _parent;
    pair<K,V> _kv;
    Color _CO;
    RBTreeNode(const pair<K,V>& kv)
        :_left(nullptr)
        ,_right(nullptr)
        ,_parent(nullptr)
        ,_kv(kv)
        ,_CO(RED)
    {}
}
```

## 插入
每次新增插入黑色节点会影响所有节点，新增插入红色节点只会影响父亲，所以新增节点选择新增红色节点
1.新增节点父亲是黑色，那么就插入结束，不需要处理
2.新增节点父亲是红色，需要处理（变色/变色+旋转）
![alt text](image-2.png)
**所以需要更改时祖父一定是黑**，**父亲一定是红**
**情况一：叔叔是红色**
将父亲和叔叔变黑，将祖父变红（子树凭空多了黑节点，所以是为了保证黑节点数相同），然后将祖父当成cur，继续向上调整，上面是黑的就结束了，但是是红的就需要继续调整
**情况二：叔叔是黑色/不存在**
旋转+变色
```cpp
    bool Insert(const pair<K, V>& kv)
    {
        if (_root == nullptr)
        {
            _root = new Node(kv);
            _root->_CO = BLACK;
            return true;
        }
        Node* parent = nullptr;
        Node* cur = _root;
        while (cur)
        {
            if (cur->_kv.first < kv.first)
            {
                parent = cur;
                cur = cur->_right;
            }
            else if (cur->_kv.first > kv.first)
            {
                parent = cur;
                cur = cur->_left;
            }
            else
            {
                return false;
            }
        }
        cur = new Node(kv);
        if (parent->_kv.first < kv.first)
        {
            parent->_right = cur;
            cur->_parent  = parent
        }
        else
        {
            parent->_left = cur;
            cur->_parent = parent
        }
        return true;
    }
```