

BST 



## Binary Search Tree

### 조회 

```java
public static Node search(int goal, Node cur) {
    if (cur == null) {
        return null;
    }

    if (cur.data == goal) {
        return cur;
    }

    if (goal < cur.data) {
        return search(goal, cur.left);
    } 

    else {
        return search(goal, cur.right);
    }
}
```



### 삽입

BST 이므로 왼쪽은 항상 부모보다 작고 오른쪽은 항상 부모보다 큼

1. 삽입할 데이터가 현재 노드의 값보다 크면 오른쪽 재귀 호출
2. 삽입할 데이터가 현재 노드의 값보다 작으면 왼쪽으로 재귀 호출 
3. 계속 재귀 호출하면서 리프노드가 되는 상황에서 삽입해주기

```java
public static void insert(int data, Node cur) {

      if(root == null) {
          root = new Node(data); 
          return;
      }

      if(data <= cur.data) {

          if(cur.left == null) {
              cur.left = new Node(data);
          }

          else {
              insert(data, cur.left);
          }
      }

      if(data > cur.data) {
          if(cur.right == null) {
              cur.right = new Node(data);
          }

          else {
              insert(data, cur.right);
          }
      }
}

```



### 삭제

1. 부모 관점에서 자식이 삭제할 데이터인지 확인해야함

   - 부모가 참조하는 자식 주소를 null로 만들어 삭제처리를 해줘야하는데 이 과정에서 자식 관점에서 처리가 불가능함. 

2. 삭제할 데이터의 자식 보유 현황에 따라서 삭제 처리가 다름 

   1. 삭제할 노드가 자식이 없음

      이때는 자식 참조를 null로 만들면됨 

   2. 삭제할 노드의 자식이 1개 

      자식이 1개이므로 무조건 부모 노드보다 작거나 크므로 left, right에서 자식을 직접적으로 참조하면 됨

   3. 삭제할 노드의 자식이 2개

      이때는 여러처리를 해줄 수 있지만 일반적으로 오른쪽 서브트리의 최소값으로 대체하고 최소값 노드를 삭제함

      자식 노드의 최소값을 찾고 값을 복사한다. 
      최소값 노드를 삭제하고 삭제려는 위치에 복사 값으로 대체한다.

```java
	public static Node remove(Node cur, int data) {

      if (cur == null) {
          return null;
      }

      if (data < cur.data) {
          cur.left = remove(cur.left, data);
      } 

      else if (data > cur.data) {
          cur.right = remove(cur.right, data);
      } 

      else {
          // 1. 자식이 없는 경우
          if (cur.left == null && cur.right == null) {
              return null;
          }

          // 2. 자식이 하나인 경우
          if (cur.left == null) {
              return cur.right;
          }

          if (cur.right == null) {
              return cur.left;
          }

          // 3. 자식이 둘인 경우
          int min = findMin(cur.right);
          cur.data = min;
          cur.right = remove(cur.right, min);
      }

      return cur;
}

public static int findMin(Node cur) {
    if(cur.left != null) {
        return findMin(cur.left);
    }

    return cur.data;
}
```



## BalancedBinarySearchTree

모든 노드에 대해서 왼쪽 서브트리의 높이와 오른쪽 서브트리의 높이의 차가 1이 넘지 않는 이진트리 

![img](https://velog.velcdn.com/images/eunaahn/post/e8a59f12-a3cc-4aa4-8611-b113bf2ae646/image.png)





### 회전 

회전 종류

> LL 회전

![img](https://velog.velcdn.com/images/eunaahn/post/ee08630b-9090-4cd9-8b02-f6a274330ebb/image.png)



> RR 회전

![img](https://velog.velcdn.com/images/eunaahn/post/54acef01-98e3-44f5-bbef-c7209f75d8f0/image.png)





## 1. LL Case

왼쪽의 왼쪽으로 치우친 경우.

```
      30
     /
    20
   /
  10
```

해결: **오른쪽 회전**

```
    20
   /  \
  10   30
```

```java
return rightRotate(cur);
```

------



## 2. RR Case

오른쪽의 오른쪽으로 치우친 경우.

```
10
  \
   20
     \
      30
```

해결: **왼쪽 회전**

```
    20
   /  \
  10   30
```

```
return leftRotate(cur);
```

------

## 3. LR Case

왼쪽의 오른쪽으로 치우친 경우.

```
      30
     /
    10
      \
       20
```

한 번에 해결 안 됨.

1. 왼쪽 자식 기준 왼쪽 회전
2. 현재 노드 기준 오른쪽 회전

```
1단계: 10 기준 왼쪽 회전

      30
     /
    20
   /
  10

2단계: 30 기준 오른쪽 회전

    20
   /  \
  10   30

```

```java
cur.left = leftRotate(cur.left);
return rightRotate(cur);
```

------

## 4. RL Case

오른쪽의 왼쪽으로 치우친 경우.

```
10
  \
   30
  /
20
```

1. 오른쪽 자식 기준 오른쪽 회전
2. 현재 노드 기준 왼쪽 회전

```
1단계: 30 기준 오른쪽 회전

10
  \
   20
     \
      30

2단계: 10 기준 왼쪽 회전

    20
   /  \
  10   30
```

```java
cur.right = rightRotate(cur.right);
return leftRotate(cur);
```



### Node 클래스

```java
static class Node {
      int data;
      int height;
      Node left;
      Node right;

      Node(int data) {
          this.data = data;
          this.height = 1;
      }
}
```



### 회전코드 

> 회전 로직 

매 노드에서 실행되어야 하는 로직

```java
// LL
if (balance > 1 && getBalance(cur.left) >= 0) {
    return rightRotate(cur);
}

// LR
if (balance > 1 && getBalance(cur.left) < 0) {
    cur.left = leftRotate(cur.left);
    return rightRotate(cur);
}

// RR
if (balance < -1 && getBalance(cur.right) <= 0) {
    return leftRotate(cur);
}

// RL
if (balance < -1 && getBalance(cur.right) > 0) {
    cur.right = rightRotate(cur.right);
    return leftRotate(cur);
}
```



```java
public static Node rightRotate(Node y) {
      Node x = y.left;
      Node t2 = x.right;

      x.right = y;
      y.left = t2;

      updateHeight(y);
      updateHeight(x);

      return x;
}

public static Node leftRotate(Node x) {
      Node y = x.right;
      Node t2 = y.left;

      y.left = x;
      x.right = t2;

      updateHeight(x);
      updateHeight(y);

      return y;
}
```





### 삽입

1. BST처럼 먼저 일단 작으면 왼쪽, 크면 오른쪽으로 내려가서 삽입해준다
2. 삽입이 끝나고 재귀가 돌아오면서 현재 노드의 높이를 다시 계산해준다
3. 밸런스 값을 계산하고 삽입 위치에 따라 4가지 회전 종류 중 하나를 적용한다.

```java
public static Node insert(Node cur, int data) {
      if (cur == null) {
          return new Node(data);
      }

      if (data < cur.data) {
          cur.left = insert(cur.left, data);
      } else if (data > cur.data) {
          cur.right = insert(cur.right, data);
      } else {
          return cur; // 중복 허용 안 함
      }

      updateHeight(cur);

      int balance = getBalance(cur);

      // LL
      if (balance > 1 && data < cur.left.data) {
          return rightRotate(cur);
      }

      // RR
      if (balance < -1 && data > cur.right.data) {
          return leftRotate(cur);
      }

      // LR
      if (balance > 1 && data > cur.left.data) {
          cur.left = leftRotate(cur.left);
          return rightRotate(cur);
      }

      // RL
      if (balance < -1 && data < cur.right.data) {
          cur.right = rightRotate(cur.right);
          return leftRotate(cur);
      }

      return cur;
}

```



```java
public static int getBalance(Node node) {
      if (node == null) {
          return 0;
      }

      return height(node.left) - height(node.right);
}

public static int height(Node node) {
      if (node == null) {
          return 0;
      }

      return node.height;
}
```



### 삭제 

1. BST처럼 먼저 삭제할 값을 찾고자식 개수에 따라 삭제한다. 
   1. 자식이 없으면 제거
   2. 하나면 자식을 올리기
   3. 둘이면 오른쪽 서브트리 최솟값으로 대체

2. 삭제가 끝나고 재귀가 돌아오면서 현재 노드의 높이를 다시 계산한다.

3. 밸런스 값을 계산하고 삽입 위치에 따라 4가지 회전 종류 중 하나를 적용한다.

```java
public static Node remove(Node cur, int data) {
      if (cur == null) {
          return null;
      }

      if (data < cur.data) {
          cur.left = remove(cur.left, data);
      } else if (data > cur.data) {
          cur.right = remove(cur.right, data);
      } else {
          if (cur.left == null && cur.right == null) {
              return null;
          }

          if (cur.left == null) {
              return cur.right;
          }

          if (cur.right == null) {
              return cur.left;
          }

          Node minNode = findMinNode(cur.right);
          cur.data = minNode.data;
          cur.right = remove(cur.right, minNode.data);
      }

      updateHeight(cur);

      int balance = getBalance(cur);

      // LL
      if (balance > 1 && getBalance(cur.left) >= 0) {
          return rightRotate(cur);
      }

      // LR
      if (balance > 1 && getBalance(cur.left) < 0) {
          cur.left = leftRotate(cur.left);
          return rightRotate(cur);
      }

      // RR
      if (balance < -1 && getBalance(cur.right) <= 0) {
          return leftRotate(cur);
      }

      // RL
      if (balance < -1 && getBalance(cur.right) > 0) {
          cur.right = rightRotate(cur.right);
          return leftRotate(cur);
      }

      return cur;
}
```

