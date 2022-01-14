# AVLTree

​		平衡二叉搜索树（Self-balancing binary search tree）又被称为AVL树（有别于AVL算法），且具有以下性质：它是一 棵空树或它的左右两个子树的高度差的绝对值不超过1，并且左右两个子树都是一棵平衡二叉树。

#### 平衡因子

​   某结点的左子树与右子树的高度(深度)差即为该结点的平衡因子（BF,Balance Factor）。平衡二叉树上所有结点的平衡因子只可能是 -1，0 或 1。如果某一结点的平衡因子绝对值大于1则说明此树不是平衡二叉树。为了方便计算每一结点的平衡因子我们可以为每个节点赋予height这一属性，表示此节点的高度。

### 1、构造树的节点，添加内部类Node

构造出AVL树的节点，添加内部类Node，并且实现一些简单通用的方法

```java
/**
 * 节点必须是可比较的
 */
public class AVLTree<E extends Comparable<E>> {
    //构造树的节点，添加内部类Node
    private class Node{
        public E e;
        public Node left;
        public Node right;
        public int height;

        public Node(E e){
            this.e = e;
            this.left = null;
            this.right = null;
            this.height = 1;
        }
    }

    //AVL树的基本属性和构造函数
    private Node root;
    private int size;

    public AVLTree(){
        root = null;
        size = 0;
    }

    //查找并获取某一节点，根据BST特点，大往右，小往左
    private Node getNode(Node node, E e){
        if (node == null){
            return null;
        }
        if (node.e == e){
            return node;
        }
        while (node!=null){
            if (e.compareTo(node.e)<0){
                return getNode(node.left, e);
            }else if(e.compareTo(node.e)>0){
                return getNode(node.right, e);
            }
        }
        return null;
    }

    //获取最小节点，根据平衡二叉树特点，一直往左查询即可
    private Node minNode(Node node){
        if (node.left==null)
            return node;
        return minNode(node.left);
    }

    //获取某一节点的高度
    private int getHeight(Node node){
        if (node==null){
            return 0;
        }
        return node.height;
    }

    public int getSize(){
        return size;
    }

    public boolean isEmpty(){
        return size==0;
    }

    /**
     * 获取节点的平衡因子
     * @param node
     * @return
     */
    private int getBalanceFactor(Node node){
        if (node == null){
            return 0;
        }
        return getHeight(node.left)-getHeight(node.right);
    }

    //根据平衡因子判断树是否为平衡二叉树
    public boolean isBalanced(){
        return isBalanced(root);
    }

    public boolean isBalanced(Node node){
        if (node==null){
            return true;
        }
        int balanceFactory = Math.abs(getBalanceFactor(node));
        if (balanceFactory>1){
            return false;
        }
        return isBalanced(node.left)&&isBalanced(node.right);
    }
}
```

### 2、添加节点

往平衡二叉树中添加节点会导致二叉树失去平衡，所以需要在每次插入节点后对树进行平衡维护，这也是平衡二叉树（AVL）与二叉搜索树（BST）的区别。

插入节点破坏平衡性有以下四种情况：

#### LL（右旋）

LL是指向左子树（L）的左孩子（L）中插入新节点后导致不平衡，这种情况下需要右旋操作。

```java
   /*
    * 右旋转
    */
	private Node rightRotate(Node y){
        Node x = y.left;
        Node t3 = x.right;
        x.right = y;
        y.left = t3;
        y.height = Math.max(getHeight(y.left),getHeight(y.right));
        x.height = Math.max(getHeight(x.left),getHeight(x.right));
        return x;
    }
```

![](https://img-blog.csdnimg.cn/20190408211619425.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1MzQzNTU3,size_16,color_FFFFFF,t_70)

#### RR（左旋）

RR是指向右子树（R）的右孩子（R）中插入新节点后导致不平衡，这种情况下需要左旋操作。

```java
   /*
    * 左旋转
    */
	private Node leftRotate(Node y){
        Node x = y.right;
        Node t3 = x.left;
        x.left = y;
        y.right = t3;
        y.height = Math.max(getHeight(y.left),getHeight(y.right));
        x.height = Math.max(getHeight(x.left),getHeight(x.right));
        return x;
    }
```

![](https://img-blog.csdnimg.cn/20190408214353545.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1MzQzNTU3,size_16,color_FFFFFF,t_70)

#### LR（左旋+右旋）

LR是指向左子树（L）的右孩子（R）中插入新节点后导致不平衡，这种情况下需要对左子树进行左旋操作，再对父节点进行右旋。

![](https://img-blog.csdnimg.cn/20190408220207905.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1MzQzNTU3,size_16,color_FFFFFF,t_70)

#### RL（右旋+左旋）

RL是指向右子树（R）的左孩子（L）中插入新节点后导致不平衡，这种情况下需要对右子树进行右旋操作，再对父节点进行左旋。

![](https://img-blog.csdnimg.cn/20190408215508810.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1MzQzNTU3,size_16,color_FFFFFF,t_70)



#### 添加节点代码

```java
	//向平衡二叉树中加入新的元素（value）
    public void add(E e){
        root = add(root, e);
    }

	//向以node为根节点的平衡二叉树中加入新元素，返回插入新节点后的平衡二叉树的根
    private Node add(Node node, E e){
        if (node == null){
            size++;
            return new Node(e);
        }
        if (e.compareTo(node.e)<0){
            node.left = add(node.left, e);
        }else if (e.compareTo(node.e)>0){
            node.right = add(node.right, e);
        }
        //插入新节点后需要更新高度height
        node.height = 1+Math.max(getHeight(node.left),getHeight(node.right));
        //并且重新计算平衡因子并维护树平衡
        node = keepBalance(node);
        return node;
    }

    private Node keepBalance(Node node){
        //重新计算平衡因子
        int balanceFactor = getBalanceFactor(node);
        if (balanceFactor>1&&getBalanceFactor(node.left)>0){
            //LL右旋
            return rightRotate(node);
        }else if (balanceFactor<-1&&getBalanceFactor(node.right)<0){
            //RR左旋
            return leftRotate(node);
        }else if (balanceFactor>1&&getBalanceFactor(node.left)<0){
            //LR
            node.left = leftRotate(node.left);
            return rightRotate(node);
        }else if (balanceFactor<1&&getBalanceFactor(node.right)>0){
            //RL
            node.right = rightRotate(node.right);
            return leftRotate(node);
        }
        return node;
    }
```

### 3、删除节点

往平衡二叉树中删除节点会导致二叉树失去平衡，所以需要在每次删除节点后对树进行平衡维护，这也是平衡二叉树（AVL）与二叉搜索树（BST）的区别。

在删除任意结点时有以下三种情况，每种情况的删除方法不同。

![](https://img-blog.csdnimg.cn/20181121222844819.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1MzQzNTU3,size_16,color_FFFFFF,t_70)

#### 右孩子为空

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181121223344796.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1MzQzNTU3,size_16,color_FFFFFF,t_70)

#### 左孩子为空

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181121223509128.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1MzQzNTU3,size_16,color_FFFFFF,t_70)

#### 左右不为空

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181122191919634.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1MzQzNTU3,size_16,color_FFFFFF,t_70)

也可以将左子树的最大值替换待删除的结点，依旧是一棵二分搜索树。

如上所说过的，和二分搜索树删除节点不同的是删除AVL树的节点后需要进行平衡的维护操作。

#### 代码实现如下：

```java
    //向平衡二叉树中删除元素（value）
	public E remove(E e){
        Node node = getNode(root, e);
        if (node != null){
            root = remove(root, e);
            return node.e;
        }
        return null;
    }

	//向以node为根节点的平衡二叉树中删除元素，返回删除节点后的平衡二叉树的根
    private Node remove(Node node, E e){
        if (node == null){
            return null;
        }
        Node retNode;
        if (e.compareTo(node.e)<0){
            node.left = remove(node.left,e);
            retNode = node;
        }else if (e.compareTo(node.e)>0){
            node.right = remove(node.right,e);
            retNode = node;
        }else {  //e.compareTo(node.e)==0
            //待删除节点左子树为空的情况
            if (node.left==null){
                Node rightNode = node.right;
                node.right = null;
                size--;
                retNode=rightNode;
            //待删除节点右子树为空的情况
            }else if (node.right==null){
                Node leftNode = node.left;
                node.left = null;
                size--;
                retNode = leftNode;
            //待删除节点左右子树均不为空的情况
            }else {
                //找到比待删除节点大的最小节点，用该节点替代删除节点的位置
                Node successor = minNode(node.right);
                successor.right = remove(node.right, successor.e);
                successor.left = node.left;
                node.left = node.right = null;
                retNode = successor;
            }
        }
        if (retNode==null)
            return null;
        //更新高度height
        retNode.height = 1+Math.max(getHeight(retNode.left),getHeight(retNode.right));
		//维护树的平衡
        retNode = keepBalance(retNode);
        return retNode;
    }
```



## 完整代码：

```java
public class AVLTree<E extends Comparable<E>> {
    private class Node{
        public E e;
        public Node left;
        public Node right;
        public int height;

        public Node(E e){
            this.e = e;
            this.left = null;
            this.right = null;
            this.height = 1;
        }
    }

    private Node root;
    private int size;

    public AVLTree(){
        root = null;
        size = 0;
    }

    private Node getNode(Node node, E e){
        if (node == null){
            return null;
        }
        if (node.e == e){
            return node;
        }
        while (node!=null){
            if (e.compareTo(node.e)<0){
                return getNode(node.left, e);
            }else if(e.compareTo(node.e)>0){
                return getNode(node.right, e);
            }
        }
        return null;
    }

    private Node minNode(Node node){
        if (node.left==null)
            return node;
        return minNode(node.left);
    }

    private int getHeight(Node node){
        if (node==null){
            return 0;
        }
        return node.height;
    }

    public int getSize(){
        return size;
    }

    public boolean isEmpty(){
        return size==0;
    }

    private int getBalanceFactor(Node node){
        if (node == null){
            return 0;
        }
        return getHeight(node.left)-getHeight(node.right);
    }

    public boolean isBalanced(){
        return isBalanced(root);
    }

    public boolean isBalanced(Node node){
        if (node==null){
            return true;
        }
        int balanceFactory = Math.abs(getBalanceFactor(node));
        if (balanceFactory>1){
            return false;
        }
        return isBalanced(node.left)&&isBalanced(node.right);
    }

    private Node rightRotate(Node y){
        Node x = y.left;
        Node t3 = x.right;
        x.right = y;
        y.left = t3;
        y.height = Math.max(getHeight(y.left),getHeight(y.right));
        x.height = Math.max(getHeight(x.left),getHeight(x.right));
        return x;
    }

    private Node leftRotate(Node y){
        Node x = y.right;
        Node t3 = x.left;
        x.left = y;
        y.right = t3;
        y.height = Math.max(getHeight(y.left),getHeight(y.right));
        x.height = Math.max(getHeight(x.left),getHeight(x.right));
        return x;
    }

    public void add(E e){
        root = add(root, e);
    }

    private Node add(Node node, E e){
        if (node == null){
            size++;
            return new Node(e);
        }
        if (e.compareTo(node.e)<0){
            node.left = add(node.left, e);
        }else if (e.compareTo(node.e)>0){
            node.right = add(node.right, e);
        }
        node.height = 1+Math.max(getHeight(node.left),getHeight(node.right));
        node = keepBalance(node);
        return node;
    }

    public E remove(E e){
        Node node = getNode(root, e);
        if (node != null){
            root = remove(root, e);
            return node.e;
        }
        return null;
    }

    private Node remove(Node node, E e){
        if (node == null){
            return null;
        }
        Node retNode;
        if (e.compareTo(node.e)<0){
            node.left = remove(node.left,e);
            retNode = node;
        }else if (e.compareTo(node.e)>0){
            node.right = remove(node.right,e);
            retNode = node;
        }else {
            if (node.left==null){
                Node rightNode = node.right;
                node.right = null;
                size--;
                retNode=rightNode;
            }else if (node.right==null){
                Node leftNode = node.left;
                node.left = null;
                size--;
                retNode = leftNode;
            }else {
                Node successor = minNode(node.right);
                successor.right = remove(node.right, successor.e);
                successor.left = node.left;
                node.left = node.right = null;
                retNode = successor;
            }
        }
        if (retNode==null)
            return null;
        retNode.height = 1+Math.max(getHeight(retNode.left),getHeight(retNode.right));

        retNode = keepBalance(retNode);
        return retNode;
    }

    private Node keepBalance(Node node){
        int balanceFactor = getBalanceFactor(node);
        if (balanceFactor>1&&getBalanceFactor(node.left)>0){
            return rightRotate(node);
        }else if (balanceFactor<-1&&getBalanceFactor(node.right)<0){
            return leftRotate(node);
        }else if (balanceFactor>1&&getBalanceFactor(node.left)<0){
            node.left = leftRotate(node.left);
            return rightRotate(node);
        }else if (balanceFactor<1&&getBalanceFactor(node.right)>0){
            node.right = rightRotate(node.right);
            return leftRotate(node);
        }
        return node;
    }

}

```

