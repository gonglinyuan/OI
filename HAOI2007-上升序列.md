*经典问题——最长上升子序列的变形，十分巧妙* 

###算法
- 此题主要瓶颈在于输出的序列下标要满足字典序最小，否则就是一道水题了。

1. 首先大约等同于最长上升子序列的 *O(n log n)* 做法，只是这回我们要 **从后往前** 处理。计算出 *f[i]* 为从 *i* 到 *n* 的最长上升序列长度，并记录 *ans* n为其中的最大值。

2. 对于每个询问 *x* ，如果 *x>ans* 则说明无解。从前往后扫，如果当前的 *f[i]>=x* 就输出，然后将 *x* 减去 *1* 继续往后找，碰到 *f[i]>=x* 而且当前的 *a[i]* 大于上一个输出的值就继续输出，……直到 *x=0* 。

###简单的证明
在第二步中，因为一直从前往后找第一个可行的，所以找到的必定是字典序最小的。由于保证了当前的 *f[i]>=x* ，所以继续往后找可以保证有解。因此这个算法一般是正确的。

###标程

```cpp
#include <cstdio>
#include <climits>
#include <algorithm>
using namespace std;

int n,m,i,j,l,r,mm,ans,x,y,a[10010],f[10010],s[10010];

int main()
{
    scanf("%d",&n);
    for (i=1;i<=n;++i) scanf("%d",a+i);
    s[++s[0]]=n; f[n]=1;
    for (i=n-1;i;--i)
    {
        if (a[i]<a[s[s[0]]])
        {
            s[++s[0]]=i; f[i]=s[0];
            ans=max(ans,f[i]); continue;
        }
        l=1; r=s[0];
        while (l<r)
        {
            mm=(l+r)>>1;
            if (a[s[mm]]>a[i]) l=mm+1;
            else r=mm;
        }
        f[i]=l; s[l]=i; ans=max(ans,f[i]);
    }
    scanf("%d",&m);
    while (m--)
    {
        scanf("%d",&x);
        if (x>ans) {printf("Impossible\n"); continue;}
        for (y=INT_MIN,i=1;i<=n&&x;++i)
            if (f[i]>=x&&a[i]>y)
            {
                printf("%d ",a[i]);
                --x; y=a[i];
            }
        putchar('\n');
    }
    return 0;
}

```
