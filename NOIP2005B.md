*这道题是NOIP第一道DP优化题，看似容易，实际上想要满分也颇有难度。* 

###算法
此题显然要用到DP，DP方程也显而易见：

> f[i]=min{f[i-j]}+1 , *i位置有石头* 

> f[i]=min{f[i-j]} 

> *(S<=j<=T)* 

这样的时间复杂度为 *O(LT)* ，空间复杂度为 *O(L)* 。
而此题的L高达 **10亿** ，所以这种朴素的方法只能得 **30分** ，显然无法AC。

###优化
1. 滚动数组
根据我们得出的DP方程，状态转移只需用到 *f[i-T]~f[i-S]* 的值，所以只需开大小为T的滚动数组即可。
所以空间复杂度被优化到了 *O(T)* 。由于T小于等于10，所以空间上显然没有问题。
1. 状压DP
我们会发现，石头的个数M远远小于长度L，不禁让我们想到了状态压缩——当S<T且有一大段桥没有石头时，常常会出现整个滚动数组f的值一样的情况，这时我们可以在遇到下一颗石头之前不再改变f的值，从而达到优化的效果。
我用tot变量记录当前数值连续出现的次数，如果超过T次，则将i直接跳转到下一个石头的位置之前，从而提高效率。当然，也可以把长度超过 *ST*  的空白段全部都合并为长度等于 *ST*  的空白段。
优化后，时间复杂度降至 *O(MT)* ，已经达到了AC的要求。

###特判
上面的离散化显然建立在S<T的基础上。如果S==T，那么永远达不到优化的条件，优化就不会奏效，从而 *TLE：90分* 。
因此我们必须加上特判，当S==T时，ans就是位置为T的倍数的石头的数量。虽然简单，却十分考验选手思维的严密性。

###标程

```cpp
#include <cstdio>
#include <cstring>
#include <climits>

inline int min(int a,int b)
{
    return a<b?a:b;
}

int f[20],a[200],L,S,T,M,i,j,p,c,ans=INT_MAX,now,tot;

int main()
{
    scanf("%d%d%d%d",&L,&S,&T,&M);
    for (i=0;i<M;i++)
        scanf("%d",&a[i]);
    if (S==T)
    {
        ans=0;
        for (i=0;i<M;i++)
            if (a[i]%T==0)
                ans++;
        printf("%d",ans);
        return 0;
    }
    for (i=0;i<M-1;i++)
        for (j=i+1;j<M;j++)
        {
            if (a[i]>a[j])
            {
                c=a[i];
                a[i]=a[j];
                a[j]=c;
            }
        }
    memset(f,0x7F,sizeof(f));
    f[0]=0; now=0; tot=0;
    for (i=1;i<L+T;i++)
    {
        for (j=S;j<=T;j++)
        {
            f[i%T]=min(f[i%T],f[(i-j+T)%T]);
        }
        if (a[p]==i)
        {
            f[i%T]++;
            p++;
        }
        if (now==f[i%T])
        {
            tot++;
        }
        else
        {
            now=f[i%T];
            tot=0;
        }
        if (tot==T)
        {
            i+=(min(a[p]-T,L)-i)/T*T;
        }
    }
    for (i=0;i<T;i++)
    {
        ans=min(ans,f[i]);
    }
    printf("%d\n",ans);
    return 0;
}
```
