---
title: PA3-1-2_kth(normal)broken_post
math: false
mermaid: false
date: 2024-11-24 15:50:17
tags: 
    - Data Structure
    - BST
categories: 
    - Learning
index_img: https://raw.githubusercontent.com/Tendourisu/images/master/20241125154011.png
---
### 破防经历
- 不知道我在犯什么病，并非OIer，竟然斗胆选第二道题来做（邓老师的PA中的每一组中都有两到三道题，可以选一道题来做，普遍意义下难度逐题迅速上升），导致周六晚从20时开始做，一直到26时都还在debug，oj的错误提示从RE->Memory Limit Exceeded->Time Limit Exceeded->wrong answer,破防后悻悻而睡。与此同时，周日还有比赛等着我，oj的截止日为下周日，还有一道线段树的题等着我...拼尽全力，无法战胜...
- 然而在周日下午刚裁完一场比赛后，我竟然成功用对拍找到了问题测例，成功找到了逆天bug！！！😭感动。这下是真体会到测试岗有多难了。
- 下面是该题的解题报告。（因为太懒就直接用了）

# PA3-1-2 kth(normal)
## 算法思路和实现要点
1. BSTNode节点：除了常规的left, right, parent, data，_root，_hot外，还需要维护
   - height：用于平衡因子的计算
   - node_num: 用于计数重复的对象的个数
   - subtree_size: 用于在查询第operand个最大数时使用，（~~如果直接暴力递归查询的话单次时间复杂度会到$O(n)$，血的教训😭~~）
2. 如字面意义上的函数：getHeight，updateHeight，getBalanceFactor，updateSubtreeSize，isLeftChild，isRightChild，zig，zagtallerChild，
3. search 与connect34使用的是邓老师的方法，rotateAt类似，但是在rotateAt的实际实现中，还必须考虑_root参与旋转的情况————root是可能变的！！！~~血的教训😭~~
4. insert和remove(基于removeAT)都使用了rotateAt来保证AVL的结构。这里有两个注意的点：
   1. insert要注意，第一次插入时_root没有记录下来，所以要单独判断
   2. updateSubtreeSize是后面加进来的功能，所以之前只对当前对象update，而没有考虑其祖先，应该要将其祖先都update才逻辑正确
5. query：最先采用的是直接用一个stack来遍历寻找，后来发现时间复杂度过大😭后面采用从根开始一路向下，通过subtree_size与自身的node_num来判断此data是否是目标data。这样时间复杂度就限制在树的深度的数量级，即$O(\log n)$
## 渐进时间复杂度的分析，包含过程
每次插入，删除，查询的时间复杂度均$O(\log n)$
共k次操作

故总时间复杂度为$O(k\log n)$
## 渐进空间复杂度的分析，包含过程
储存每个节点的空间复杂度为$O(1)$
k个节点，故空间复杂度为$O(k)$
## 遇到的困难和解决方法
1. 最开始测例都无法通过，结果发现是前文提到的insert的时候没有处理_root，结果_root根本没有被更新
2. 解决_root的更新后，测例能过，但是oj后面全是Runtime Error，结果发现rotateAt如果有_root参与的话_root没有更新，逻辑出现错误。
![](https://raw.githubusercontent.com/Tendourisu/images/master/20241124172134.png)
3. 解决问题2后，发现出现了oom，
   ![](https://raw.githubusercontent.com/Tendourisu/images/master/20241124172441.png)判断是stack的开销太大（为了省时间，stack pop后是没有delete的），把stack改成了静态的array
4. 解决oom后，发现time limit Exceeded了
   ![](https://raw.githubusercontent.com/Tendourisu/images/master/20241124172412.png)分析发现问题还是出在stack上，遍历是$O(n)$，太慢了，于是从这里引入subtree_size来优化query的时间复杂度。此时oj由time limit Exceeded成功变成了wrong answer![](https://raw.githubusercontent.com/Tendourisu/images/master/20241124172537.png)
5. 由于subtree_size是新添进来的功能，之前的insert在rotate后可以break了，但是现在不行！它的祖先的subtree_size需要维护！删掉了break后，wrong answer由四个变成了三个![](https://raw.githubusercontent.com/Tendourisu/images/master/20241124172757.png)
6. 在insert和remove时只在该对象维护了subtree_size而祖先没有维护，修改之后，终于！通过了九成测！(以及这个bug的反例是由对拍找出来的)
![](https://raw.githubusercontent.com/Tendourisu/images/master/20241124172817.png)
## 估计完成本题所用时间 
7 小时
## 关于本题的更多感想
对拍以意想不到的方式发挥了作用！