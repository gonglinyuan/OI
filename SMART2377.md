*这是一道最大回文字串的变式题，解法可以十分地巧妙。* 

###算法
一个通用的方法，几乎万能。那就是著名的**哈希乱搞**+二分，复杂度*O(n log n)*，而且代码极复杂，想要AC还需要足够幸运。所以不推荐。

其实这道题要用到鲜为人知却又异常巧妙的**Manacher算法**。Manacher算法的作用是用类似DP的方法求最大回文字串的方法，代码十分简短，主要利用了回文串的对称性来优化 *O(n^2 )* 的暴力算法，使其理论时间复杂度达到了 *O(n)* ，这是十分了不起的。

关于Manacher算法的具体实现，可以参考[http://www.felix021.com/blog/read.php?2040](http://www.felix021.com/blog/read.php?2040)，这里不再赘述。

###特殊处理
这题只是把“对称”改成了“相反”，实际是类似的。只要把原来的*1*改成*2*，原先的*0*不变，然后插入的字符改成*1*即可。然后判断两个字符相加是否等于2来判断回文，剩下的就和Manacher算法一样了。值得注意的是，首尾不仅要加干扰字符，还要再多加两个*1*。

比如，样例*11001011*就变成了*$12121010121012121$*。

###标程

```cpp
#include <cstdio>

int len,n,s[1200000],f[1200000],i,j,k;
char str[20],ch;
long long ans;

inline int min(int a,int b)
{
    return a<b?a:b;
}

int main()
{
    fgets(str,sizeof(str),stdin);
    sscanf(str,"%d",&len);
    s[1]=1; n=2;
    while (len--)
    {
        scanf("%c",&ch);
        s[n++]=ch=='0'?0:2;
        s[n++]=1;
    }
    n--;
    for (i=1;i<=n;i++)
    {
        f[i]=k>i?min(f[2*j-i],k-i):0;
        while (s[i+f[i]]+s[i-f[i]]==2&&i-f[i]>0&&i+f[i]<=n) f[i]++;
        if (i+f[i]>k)
        {
            k=i+f[i];
            j=i;
        }
    }
    for (i=1;i<n;i++)
        if (s[i]==1)
            ans+=(long long)(f[i]-1)/2;
    printf("%lld\n",ans);
    return 0;
}

```
