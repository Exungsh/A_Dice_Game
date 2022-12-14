# A_Dice_Game

A SE homework by Exungsh and Ikennyooo

Online:[逍遥骰🎲 ](https://exungsh.github.io/)

GitHub:[Exungsh/A_Dice_Game](https://github.com/Exungsh/A_Dice_Game)

Bilibili:[软件工程结对编程作业 ik&小鱼_bilibili](https://www.bilibili.com/video/BV1DG4y1H7XS/?vd_source=2b9cb555925b9b4df90c67f74ed75e2f)

## 一、原型界面

[⭐逍遥骰_原型⭐](https://modao.cc/app/C4CDZGOrjfowjvMCJ7X)

## 二、重要的/有价值的代码片段解释



### 2.1 基础游戏逻辑

**消除相同数字：**

```javascript
//输入列、对手棋盘名，消除对手该列所有和骰子值相同的数
function judge(col, arch) {
    //遍历每一行
    for (var i = 0; i < 3; i++) {
        //如果该行col列元素和NUM相等则置为0
        if (arch[i][col] == NUM) {
            arch[i][col] = 0;
        }
    }
}
```



**统计总分：**

```javascript
//传入棋盘名，返回当前棋盘总分
function count(player) {
    sum = 0;
    //遍历每一列
    for (var j = 0; j < 3; j++) {
        count_dict = { 0: 0, 1: 0, 2: 0, 3: 0, 4: 0, 5: 0, 6: 0 };
        //遍历该列的每一个元素
        for (var i = 0; i < 3; i++) {
            count_dict[player[i][j]] += 1;//使用count_dict统计每个数字出现次数
        }
        for (num in count_dict) {
            sum += num * count_dict[num] * count_dict[num];//根据count_dict计算该列总分
        }
    }
    return sum;
}
```



**判断游戏是否结束：**

```javascript
//传入棋盘名，返回1说明棋盘已满，游戏结束
function check(player) {
    var count = 0;
    for (var i = 0; i < 3; i++) {
        for (var j = 0; j < 3; j++) {
            if (player[i][j] == 0)
                count += 1;
        }
    }
    return (count == 0)
}
```

<br>

### 2.2 AI逻辑

**建立`col_cnt`二维数组记录玩家每一列每个点数的数量；建立`can_done`数组来记录ai每一列剩余空位的数量。根据这两个数组进行此后算法的编写。**

```python
copy_ai = copy.deepcopy(ai)
copy_player = copy.deepcopy(player)
col_cnt = [[0, 0, 0], [0, 0, 0], [0, 0, 0],
           [0, 0, 0], [0, 0, 0], [0, 0, 0], [0, 0, 0]]
can_done = [0, 0, 0]
        
# 设置col_cnt\can_done
for i in range(3):
	for j in range(3):
		col_cnt[int(copy_player[j][i])][i] += 1
			if copy_ai[i][j] == 0:
				can_done[j] += 1
```



**对于棋盘相应的情况，采取相应的策略。**

| 棋盘状态情况（优先级自顶向下）                               | 采取策略                                   |
| ------------------------------------------------------------ | ------------------------------------------ |
| 对方某一列与己方摇到点数（>=3）相同的多于两个，并且己方棋盘该列有空位 | 填入空位，消除双连/三连                    |
| 对方存在某一列与己方摇到的点数（>=4）相同的点数，并且己方棋盘该列有空位 | 填入空位，消除点数较大的子                 |
| 对方存在某一列存在 “4、5、6 双连 / 三连或者3三连” 且己方摇到点数（>=3）不等于该点数；如果己方棋盘该列有空位，并且另外两列也存在两个及以上空位 | 该列留空，在另外两列运用贪心算法           |
| 对方存在某一列存在4、5、6 双连 / 三连且己方摇到点数小于3；如果己方棋盘该列有空位，并且另外两列也存在两个及以上空位 | 该列留空，在另外两列选可下的空位多的列落子 |
| 己方摇到点数1，对方某列存在点数1；如果己方棋盘该列有空位，并且另外两列也存在三个及以上空位 | 该列留空，另外两列选可下的空位多的列落子   |
| 己方摇到点数2，对方某列仅存在一个点数2；如果己方棋盘该列有空位，并且另外两列也存在三个及以上空位 | 该列留空，另外两列选可下的空位多的列落子   |
| 己方摇到点数<3                                               | 在可下空位多的列落子                       |
| 己方摇到点数>=3                                              | 贪心算法落子                               |



**贪心算法相关代码**

```python
max_point = -162
best_row = 0
best_col = 0
for row in range(3):
    for col in range(3):
        if col != j:
            if ai[row][col] != 0:
                continue
            else:
                copy_ai = copy.deepcopy(ai)
                copy_player = copy.deepcopy(player)
                copy_ai[row][col] = get_num
                copy_player = judge(
                    col, get_num, copy_player)
                point = count(copy_ai)-count(copy_player)
                if point > max_point:
                    best_row = row
                    best_col = col
                    max_point = point
ai[best_row][best_col] = get_num
judge(best_col, get_num, player)
return ai
```



**选空位多的列落子代码**

```python
colmax = 0
done_max = 0
for col in range(0, 3):
    if can_done[col] > done_max:
        done_max = can_done[col]
        colmax = col
for i in range(0, 3):
    if ai[i][colmax] == 0:
        ai[i][colmax] = get_num
        judge(colmax, get_num, player)
```

实现“该列留空”则设置`avoid_j`变量来避免填入该列。

## 三、结对编程心得体会

* 很高兴有一个机会可以实践暑假时学的html和css的知识（网课是做一些boring的静态网页一直没有什么兴趣做），实践带给我未知和新奇的挑战，这种机会对我而言是十分宝贵的
* 有压力才有动力。暑假的时候html和css学麻了一直下不了决心学JavaScript，作业push我去抓紧学习。事实上JavaScript和python语法十分相似，非常快就上手了
* 深感自己能力的不足，很多很不错的想法最后因为技术原因成为废案，自己的能力还需要在实践中继续加强
* 感谢我的队友ik，和他的配合非常的愉快🌹
* 很感谢每一个在界面代码还很不完善的时候体验这个游戏的朋友们，谢谢他们宝贵的建议让这个游戏的用户体验越来越好
