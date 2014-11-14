*这是一道裸的Catalan数题目，但是十分考验细节*

###算法
Catalan数。此题相当于求进栈出栈方案数的题目（因为在任意时刻，拥有5元钱的乘客数目必须多于拥有10元钱的乘客数目，否则就会找不出钱来），所以可以直接套用Catalan数的公式。

###Catalan数求解方法
- 递推式一： *h(0)=1，h(1)=1，h(n)=h(0)\*h(n-1)+h(1)\*h(n-2)+...+h(n-1)\*h(0) (n>=2)*    然而时间复杂度高达 *O(n^2 )* ，所以不能使用。
- 递推式二： *h(0)=1，h(n)=h(n-1)*(4*n-2)/(n+1)*   时间复杂度为 *O(n)* 。
- 通项公式： *h(n)=C(2n,n)/(n+1)*    因为要求阶乘，所以时间复杂度为 *O(n)* 。
- 实际上，通项公式求解时，一般要先乘后除，所以会产生很大的数据，导致效率不高。而用第二个递推式求解，可以边乘边除，提高效率。
- 另外，高精度的压位也是不可缺少的。

###标程

```cpp
#include <cstdio>

const int LEN=70000;

struct number
{
    int a[LEN],n;
    void mul(int x)
    {
        int i,t=0;
        for (i=0;i<=n;i++)
        {
            a[i]=a[i]*x+t;
            t=a[i]/10000;
            a[i]%=10000;
        }
        while (t)
        {
            a[++n]=t;
            t=a[n]/10000;
            a[n]%=10000;
        }
    }
    void div(int x)
    {
        int i,r=0;
        for (i=n;i>=0;i--)
        {
            a[i]+=r*10000;
            r=a[i]%x;
            a[i]/=x;
        }
        while (a[n]==0) n--;
    }
    void out()
    {
        int i,k;
        printf("%d",a[n]);
        for (i=n-1;i>=0;i--)
            printf("%04d",a[i]);
        putchar('\n');
    }
};

int n,i;
number ans;

int main()
{
    scanf("%d",&n);
    ans.a[0]=1;
    for (i=1;i<=n;i++)
    {
        ans.mul(4*i-2);
        ans.div(i+1);
    }
    ans.out();
    return 0;
}

```
