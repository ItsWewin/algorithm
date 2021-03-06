我这个实现的代码比较麻烦，但是思路应该是最好理解的，通过了 LeetCode 检查，但是内存和空间占用比较大，有待优化。

思路如下：

我们对一个人的身高和位置组成的数对表示如下 [h,i]，其中 h 是身高，i 是位置，排序后元素前面 >= h 的人数记为 count.
对于身高最低，序列号又最小的用户而言，他排队的序列号是可以确定的，因为它所再的位置，只取决于前面 h 比他大的数对，所在的位置就是他序列号（i）；
拿测试用例 1 看，[4, 4] 即 h = 4 的人一定是在第 4 个，即出现在排序结果的索引为 3 的位置。

对于身高相同的 [5,0] 和 [5.2] 而言，毫无疑问位置号越小的，排序越靠前。

所以我们可以先对二维数组以身高 h 进行正向排序，测试用例 1 中的 [[7,0], [4,4], [7,1], [5,0], [6,1], [5,2]] 的排序结果是: [[4,4], [5,0], [5,2], [6,1], [7,0], [7,1]];

然后按照位置 i 再将身高相同的数据对排序，由于测试用户数据原因，测试用例 1 的排序结果仍然是:
[[4,4], [5,0], [5,2], [6,1], [7,0], [7,1]]

排序后，我们可以考虑遍历，将合适的元素放置到合适的位置，如下定义一个存储排序结果的数组 resultArr

resultArr = [nil, nil, nil, nil, nil, nil]

开始遍历：

---> [4, 4]
resultArr = [[], [], [], [4,4], [], []]

---> [5.0]
此时 [5,0] 是剩余未排序数对中 h 最小，并且 i 最小的元素。
也就是 [5, 0] 在结果数组 resultArr 的位置，一定是从 resultArr[0] 开始往后遍历，第一个没有被占用(== nil)并且前面元素的可以满足排序后有 0 个元素 >= 5 的位置。
由于上一轮排序完的结果是 resultArr = [nil, nil, nil, [4,4], nil, nil]
resultArr[0] == nil, count = 0; 所以显然 resultArr[0] 就满足 [5,0] 插入，所以 resultArr[0] = [5,0]

---> [5,2]
此时 [5,2] 是剩余未排序数对中 h 最小，并且 i 最小的元素。
[5, 2] 在结果数组 resultArr 的位置，一定是从 resultArr[0] 位开始往后遍历，第一个没有被占用(== nil)并且前面元素的可以满足排序后有 2 个元素 >= 5 的位置；
上一轮的排序结果是 resultArr = [[5,0], nil, nil, [4,4], nil, nil];

resultArr[0], 5 == 5, count++;
resultArr[1] = nil, count == 1, 此时 [5, 2] 显然不能插入此处，只能 count ++ 继续往后遍历 (为什么 count ++ 呢？， 因为当前的 [5, 2] 是未排序元素中的 h, i 最小的元素，所以将来这里插入的元素一定是 > 5 的) 
resultArr[2] = nil, count == 2, 满足 [5, 2] 插入条件 resultArr[2] = [5, 2]

---> [6,1]
同样的套路，从头遍历数组 resultArr，找到那个 == nil，并且前面有 1 个元素 >= 6 的位置.
上一轮的结果: resultArr = [[5,0], nil, [5, 2], [4,4], nil, nil];
resultArr[0], 位置非 nil, 5 < 6, count 不能 ++;

resultArr[1] == nil, count = 0, 不满足插入条件，count ++； 
resultArr[2] 5 < 6; count 不能 ++, count = 1;
resultArr[3] 4 < 6; count 不能 ++, count = 1,count = 1;
resultArr[4] == nil, count = 1; 满足 [6, 1] 插入条件, 所以 resultArr[4] = [6, 1]

---> [7,0]
同样的套路，从头遍历数组 resultArr，找到那个 == nil，并且前面有 0 个元素 >= 7 的位置
上一轮的结果: resultArr = [[5,0], nil, [5, 2], [4,4], [6, 1], nil];

resultArr[0] 非 nil, 5 < 7, count 不能 ++, count = 0;
resultArr[1] == nil, count = 0; 满足 [7, 0] 插入条件 resultArr[1] = [7, 0]

---> [7,1]
同样的套路，从头遍历数组 resultArr，找到那个 == nil，并且前面有 1 个元素 >= 7 的位置
上一轮的结果: resultArr = [[5,0], [7, 0], [5, 2], [4,4], [6, 1], nil];
resultArr[0] 非 nil, 5 < 7, count 不能 ++, count = 0;
resultArr[1], 7 <= 7, count ++;
resultArr[2], 5 < 7, count 不能 ++, count = 1;
resultArr[3], 4 < 7, count = 1;
resultArr[5], 6 < 7, count = 1;
resultArr[6] == nil, count = 1; 满足 [7, 1] 插入条件 resultArr[1] = [7, 1]

现在我们回头看 [4, 4] 你会发现，[h, i] 最小组合，同样也满足这个套路：
从头开始遍历 resultArr 数组，找到第一个为 nil 且满足前面有 4 个元素 >= 4 的那个位置，然后插入 [4,4] 即可。

### 总结
先按照身高 h 正向排序，然后通过 i 将 h 相同的进行内部的正向排序，然后遍历元素，找个一个合适的位置插入元素即可。