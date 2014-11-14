*一道较难的树状数组题目，模型很经典但是也很难想到*

###算法
- 模型转化：题目大意就是要求区间内数字有多少种， **只要求询问不要求修改** 。看起来类似线段树裸题，实际上并非如此。
- 离线：本题n的规模只有50000，而询问数m的规模却高达200000。如果有O(mlogn)的在线算法也是可行的，但这题 **不禁让我们想到了离线** 。
- 大致算法：首先预处理出每个数字之前第一个与它相同的数字的位置pre(i)，接着对所有的询问按照右端点进行排序，然后按顺序一一处理这些询问：从左往右遍历当前询问右端点之前的数字，对于每一个i，添加一条pre(i)+1~i的线段，代表从pre(i)+1~i有这种数字。（由于之前一定添加过一条终点为pre(i)的线段，所以刚才添加的线段与前面的线段（一直到1）一定是联通的）当前询问的答案，就相当于当前询问 *左端点上的线段覆盖条数* 。
- 原理：如果第a[i]种数字在当前询问l~r的范围内，则一定有线段1～i，而当前询问左端点l一定在1～i的范围内，所以就成功记录了这第a[i]种数字。对于其它数字也同理。
- 树状数组：刚刚用到的操作大概有： *添加线段、查询某点上覆盖的线段数目* 。这当然可以用 **线段树** 来做。然而，如果把它们抽象成： *添加起终端点（分别用1和-1表示）、统计前缀和* ，那么就可以使用效率更高的 **树状数组** 了。
- 总体时间复杂度O(nlogn(排序)+mlogn)= *O(mlogn)* ，空间复杂度O(color(last数组)+n+m)= *O(color)* 。虽然空间上还有优化空间，但也已经很不错了。

###标程

```cpp
#include <cstdio>
#include <cstdlib>
#define lowbit(x) ((x)&(-(x)))

const int MAXN=50000;
const int MAXM=200000;
const int MAXC=1000000;

struct seg { int n,l,r;};

inline int cmp(const void *a,const void *b)
{ return (*(seg *)a).r-(*(seg *)b).r;}

int n,m,i,j,a[MAXN+10],pre[MAXN+10],last[MAXC+10],ans[MAXM+10];
seg Q[MAXM+10];

struct fwtree
{
    int a[MAXN+10],c[MAXN+10];
    void modify(int i,int x)
    {
        a[i]+=x;
        while (i<=n)
        {
            c[i]+=x;
            i+=lowbit(i);
        }
    }
    int sum(int i)
    {
        int ret=0;
        while (i)
        {
            ret+=c[i];
            i-=lowbit(i);
        }
        return ret;
    }
};

fwtree T;

int main()
{
    freopen("diff.in","r",stdin);
    freopen("diff.out","w",stdout);
    scanf("%d",&n);
    for (i=1;i<=n;i++)
    {
        scanf("%d",&a[i]);
        pre[i]=last[a[i]]; // pre[i]代表i之前第一个与a[i]颜色相同的位置
        last[a[i]]=i; // last[i]代表第i种颜色上次出现位置
    }
    scanf("%d",&m);
    for (i=1;i<=m;i++)
    {
        Q[i].n=i;
        scanf("%d%d",&Q[i].l,&Q[i].r);
    }
    qsort(Q+1,m,sizeof(seg),cmp); // 按照右端点对询问升序排序
    for (i=1;i<=m;i++)
    {
        while (j<Q[i].r) // 处理该询问右端点之前的所有位置
        {
            j++;
            T.modify(j+1,-1);
            T.modify(pre[j]+1,1);
        } // 在pre[j]+1 ~ j添加一段线段
        ans[Q[i].n]=T.sum(Q[i].l); // 记录答案为当前左端点上的线段数目
    }
    for (i=1;i<=m;i++) printf("%d\n",ans[i]);
    fclose(stdin); fclose(stdout);
    return 0;
}

```
