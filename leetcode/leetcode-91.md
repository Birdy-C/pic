# Leetcode 91

https://leetcode.com/problems/decode-ways/

It is obvious that every letter has length 1 or 2, so we can deduce the total decoding number of string s[0..i] from that of s[0..(i - 1)] and s[0..(i - 2)].

> If s[i] is a valid number(0 < s[i] < 10), then we can get a new decode by adding s[i] to any possible decode of s[0..(i - 1)]
>
> If s[i-1, i] is a valid number (10 <= s[i-1, i] <= 26) (because 01 02 .. should be presented as 1 2 ..), then we can get a new decode by adding s[i-1, i] to any possible decode of s[0..(i - 2)]

This problem has a relatively **low acceptance** (22.8% now, it is much lower than other questions), maybe it is because of special case "0" (or "01", "0xxx"). Just take care of edge case.

## A

To implement it, we can simply allocate an array to record the whole decoding number of s[0...i].

```c++
class Solution {
public:
    int numDecodings(string s) {
        if(s.size() == 0 || s[0] == '0') return 0;
        vector<int> rec(s.size() + 1, 0);
        rec[0] = 1;
        rec[1] = 1;
        for(int i = 1; i < s.size(); i++){
            // update rec[i + 1] using s[i - 1] & s[i]
            if(s[i] != '0')
                rec[i + 1] += rec[i];
            if(s[i - 1] != '0' && stoi(s.substr(i - 1, 2)) <= 26)
                rec[i + 1] += rec[i - 1];
        }
        return rec[s.size()];
    }
};
```

This result has already run well.

Its *time complexity* is **O(n)** and *space complexity* is also **O(n)**.

> Runtime: 0 ms, faster than 100.00% of C++ online submissions for Decode Ways. (Sometimes it shows 4ms, the time is not stable);
>
> Memory Usage: 8.4 MB, less than 88.24% of C++ online submissions for Decode Ways.

## B

We can do a little adjustment to improve the space complexity.

As you can see from the algorithm above, only rec[i - 1] and rec[i] are needed to update rec[i + 1].

So we can remove the unneeded vector and just record rec[i - 1] and rec[i].

Here comes the solution. I save the adjustment to make it better to understand.

```c++
class Solution {
public:
    int numDecodings(string s) {
        if(s.size() == 0 || s[0] == '0') return 0;
        // vector<int> rec(s.size() + 1, 0);
        // rec[0] = 1;
        // rec[1] = 1;
        int l1 = 1, l2 = 1;
        for(int i = 1; i < s.size(); i++){
            // update rec[i + 1] using s[i - 1] & s[i]
            int newl = 0; // represent rec[i + 1]
            if(s[i] != '0'){
                // rec[i + 1] += rec[i];
                newl += l2;
            }
            if(s[i - 1] != '0' && stoi(s.substr(i - 1, 2)) <= 26){
                // rec[i + 1] += rec[i - 1];
                newl += l1;
            }    
            // update records
            l1 = l2;
            l2 = newl;
        }
        // return rec[s.size()];
        return l2;
    }
};
```

Its *time complexity* is still **O(n)**, and *space complexity* should be reduced to **O(1)**.

