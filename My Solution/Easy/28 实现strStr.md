# 题目

实现 strStr() 函数。

给定一个 haystack 字符串和一个 needle 字符串，在 haystack 字符串中找出 needle 字符串出现的第一个位置 (从0开始)。如果不存在，则返回  -1。

示例 1:
输入: haystack = "hello", needle = "ll"
输出: 2

示例 2:
输入: haystack = "aaaaa", needle = "bba"
输出: -1

说明:

当 needle 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。
对于本题而言，当 needle 是空字符串时我们应当返回 0 。这与C语言的 strstr() 以及 Java的 indexOf() 定义相符。

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/implement-strstr
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

# 解题思路

看到这题第一反应就是kmp

冷落了hash，唉

# 代码

## KMP

```c++
class Solution {
public:
    const static int MAXN=1e5+5;
    int Next[MAXN]; 
    void getNext(string needle){
        int k=Next[0]=-1;
        int len=needle.size(),j=0;
       
        while(j<len){
            if(k==-1||needle[k]==needle[j]) Next[++j]=++k;
            else k=Next[k];
        }
    }
    
    int kmp(string haystack, string needle){
        int i=0,j=0,pos=-1;
        int lenH=haystack.size(),lenN=needle.size();
        while(i<lenH){
            if(j==-1||haystack[i]==needle[j]) ++i,++j;
            else j=Next[j];
            if(j==lenN) {
                pos=i-j;
                break;
            }
        }
        return pos;
    }
    
    int strStr(string haystack, string needle) {
        getNext(needle);
        if(needle.size()==0) return 0;
        else return kmp(haystack,needle);
    }
};
```

## HASH

滚动哈希。近似与官方题解中提到的Rabin-Karp算法。

```C++
#define ull unsigned long long
class Solution {
public:
    const static ull B = 1e9+7;
    int strStr(string haystack, string needle) {
       int lenH=haystack.size(),lenN=needle.size();
       if(lenN==0) return 0;
       ull t=1,hashH=0,hashN=0;
       for(int i=1;i<=lenN;++i) t*=B;//B的lenN次方
       //计算haystack长度为lenN的前缀的哈希值 和 needle的哈希值 
       for(int i=0;i<lenN;++i) hashH=hashH*B+haystack[i],hashN=hashN*B+needle[i];
       //对haystack不断右移一位，更新哈希值并判断 
       for(int i=0;i+lenN<=lenH;++i){
           if(hashH==hashN) return i;
           if(i+lenN<lenH) hashH=hashH*B+haystack[i+lenN]-haystack[i]*t;
       } 
       return -1;
    }
};
```

