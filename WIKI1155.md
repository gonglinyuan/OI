*这是一道经典的分组背包问题* 

###算法
这道题显然是一道01背包问题，只是在背包的基础上增加了不少内容。每种物品可能有附件——一个或者两个。所以，我们只要对原来的“物品”进行加工——每种主件有五种状态：不放、放主件、放主件和第一个附件、放主件和第二个附件、放主件和两个附件。因此一个物品就被拆分成了5个。

然而，这五个物品之间有着特殊的关系——他们互相排斥，不能同时出现。所以，原问题转化为了一个分组背包问题。
分组背包问题十分简单，但是三重循环之间的顺序不能颠倒，下面给出循环顺序：

    for i in 主件
		for j = max to 0
			for k in 五种状态

###标程

```cpp
#include <cstdio>

inline int max(int a,int b)
{
    return a>b?a:b;
}

int m,n,v[61],p[61],q[61],l[61],r[61],i,j,f[4000];

int main()
{
    scanf("%d%d",&m,&n); m/=10;
    for (i=1;i<=n;i++)
    {
        scanf("%d%d%d",&v[i],&p[i],&q[i]);
        v[i]/=10; p[i]*=v[i];
        if (q[i])
        {
            if (!l[q[i]]) l[q[i]]=i;
            else r[q[i]]=i;
        }
    }
    for (i=1;i<=n;i++)
    {
        if (q[i]) continue;
        for (j=m;j>=0;j--)
        {
            if (v[i]<=j) f[j]=max(f[j],f[j-v[i]]+p[i]);
            if (l[i]&&(v[i]+v[l[i]]<=j)) f[j]=max(f[j],f[j-v[i]-v[l[i]]]+p[i]+p[l[i]]);
            if (r[i]&&(v[i]+v[r[i]]<=j)) f[j]=max(f[j],f[j-v[i]-v[r[i]]]+p[i]+p[r[i]]);
            if (r[i]&&(v[i]+v[l[i]]+v[r[i]]<=j)) f[j]=max(f[j],f[j-v[i]-v[l[i]]-v[r[i]]]+p[i]+p[l[i]]+p[r[i]]);
        }
    }
    printf("%d\n",f[m]*10);
    return 0;
}

```
