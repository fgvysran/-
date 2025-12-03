## 题目1

[3757. 有效子序列的数量 - 力扣（LeetCode）](https://leetcode.cn/problems/number-of-effective-subsequences/description/)

集合容斥

<img src="file:///C:/Users/30867/AppData/Roaming/marktext/images/2025-12-03-10-19-40-image.png" title="" alt="" width="301">

```c
+ |A|
+ |B|
+ |C|
- |A∩B|
- |A∩C|
- |B∩C|
+ |A∩B∩C|
```

本题:1个数相差奇数减。

求删除的子序列有多少个使得剩余子序列**按位或**严格小于所有元素的 **按位或 (Bitwise OR)**  （sum_or）。

=>求多少个子序列**按位或**严格小于所有元素的 **按位或 (Bitwise OR)**

=> 2 ^ n  - 多少个子序列按位或等于所有元素的 **按位或 (Bitwise OR)**

=>SOSDP求集合 有多少个子集

111 = 111 - 110 - 011 - 101  + 100 + 001 + 010 - 000;

egg: 110 = 2 ^2 - 2 ^ SOSDP[110];

```cpp
SOSDP:

egg:1101

1101

第一层=> 1101 , 0101

第二层=>1101 -- 1001 , 0101 -- 0001

第三层=>1101 -- 1001,0101,0001

第四层=>1101 -- 1100 , 1001 -- 1000 , 0101-- 0100,0001 -- 0000

i位上为1：

dp[i][x] = dp[i - 1][x] + dp[i][x ^ (1 << i)]

i位上为0：

dp[i][x] = dp[i - 1][x];
```

```cpp
class Solution {
public:
    int countEffective(vector<int>& nums) {
        int sum_or = 0, n = nums.size();
        const int MOD = 1e9 + 7;bool f = true;
        vector<long long> pow2(n + 1,1);
        for(int i = 0;i < n;i++){
            sum_or |= nums[i];
            if(i)pow2[i] = pow2[i - 1] * 2 % MOD;
            if(i && nums[i] != nums[i - 1])f = false;
        }
        if(f)return 1;
        pow2[n] = pow2[n - 1] * 2 % MOD;
        int w = 30;
        while((1 << w) > sum_or)w--;
        vector<long long> dp((1 << (w + 1)) , 0);
        for(int i = 0;i < n;i++)dp[nums[i]] += 1;
        for(int i = 0;i <= w;i++){
            for(int j = 0,J = (1 << (w + 1));j < J;j++){
                if((1 << i) & j)dp[j] += dp[j ^ (1 << i)];
            }
        }

        long long res = 0;
        //枚举子集
        int t = sum_or;
        while(true){
            if(bit(t ^ sum_or) & 1){
                res = (res - pow2[dp[t]]) % MOD;
            }else{
                res = (res + pow2[dp[t]]) % MOD;
            }
            if(t == 0)break;t = (t - 1) & sum_or;
        }
        return (((pow2[n] - res) % MOD) + MOD) % MOD;
    }
    int bit(int x){
        int res = 0;
        while(x){
            res ++;x -= (x & -x);
        }return res;
    }
};
```
