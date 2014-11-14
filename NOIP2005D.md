*这题考的是字符串处理，然而十分繁琐，需要一定的技巧，还要十分注重细节*

###算法
**分治法**——不断地寻找当前优先级最低的运算符，然后以它为中心将算式分成两段递归计算。

**带入求值**——因为要用多项式乘法对算式进行展开计算的话，不仅程序冗长繁琐，而且程序执行效率也十分低下。由于OI比赛的题目只有十组测试点，而且是按点得分，所以我们可以考虑用具体数字带入a求表达式的值来判断。为了减少误差，必须多取几个值。

###细节问题
1. 如果带入整数，由于幂指数很高，所以有上溢的风险，必须加上取模，当然这是建立在对模算术熟悉的基础上。在这里笔者带入的是小浮点数，可以巧妙地避免溢出。
1. 带入小数就产生了如何比较两个小数相等的问题。小数大小不能用等号直接比较，这应该属于常识了。而常用的方法（比较差是否小于eps，eps一般去10的-6次到10的-9次不等），在这数量级悬殊的数字比较上也无能为力。所以我们可以先比较它们的数量级（log10的值）是否相同，再去比较值。然而这种方法实测时误差不小，请尽量少在别的题目中使用。
1. 题目中的表达式可能会出现多括号、少括号的情况，必须自己把括号补上。
1. 此题的读入也十分猥琐。由于空格的存在所以不能用scanf直接读取了，C++必须gets\fgets\getline，然后自己慢慢处理吧……

###标程

```cpp
#include <cstdio>
#include <cstring>
#include <cmath>

double A; // 代表用来替代a的值

class expr
{
    public:
    char s[100],ts[100];
    double getnum(int l,int r) // 获取数字
    {
        int i,x=0;
        for (i=l;i<=r;i++)
            x=x*10+(int)s[i]-48;
        return (double)x;
    }
    double calc(int l,int r) // 计算从l到r的表达式的值
    {
        int i,k;
        for (i=r,k=0;i>=l;i--) // 从优先级最低的加减号开始
        {
            if (k==0)
            {
                if (s[i]=='+')
                    return calc(l,i-1)+calc(i+1,r);
                else if (s[i]=='-')
                    return calc(l,i-1)-calc(i+1,r);
            }
            if (s[i]=='(') k++;
            else if (s[i]==')') k--;
        }
        for (i=r,k=0;i>=l;i--) // 处理乘号
        {
            if (k==0)
                if (s[i]=='*')
                    return calc(l,i-1)*calc(i+1,r);
            if (s[i]=='(') k++;
            else if (s[i]==')') k--;
        }
        for (i=r,k=0;i>=l;i--) // 处理乘方
        {
            if (k==0)
                if (s[i]=='^')
                    return pow(calc(l,i-1),calc(i+1,r));
            if (s[i]=='(') k++;
            else if (s[i]==')') k--;
        }
        if (s[l]=='('&&s[r]==')') // 处理括号
            return calc(l+1,r-1);
        if (l==r&&s[l]=='a') // 用A替代字母a的值
            return A;
        else
            return getnum(l,r);
    }
    void scan() // 读入字符串并进行预处理
    {
        int i,j,k;
        memset(ts,0,sizeof(ts));
        memset(s,0,sizeof(s));
        fgets(ts,sizeof(ts),stdin);
        for (i=0,j=0,k=0;i<strlen(ts);i++)
            if (ts[i]!=' '&&ts[i]!='\n') // 过滤空格和fgets读到的换行符
            {
                if (ts[i]=='('&&ts[i-1]==')') // 在两个相邻的括号之间添加乘号
                    s[j++]='*';
                else if (ts[i]=='a'&&ts[i-1]>='0'&&ts[i-1]<='9') // 在a与它的系数之间添加乘号
                    s[j++]='*';
                if (ts[i]=='(') k++;
                else if (ts[i]==')') k--; // 统计括号数目
                s[j++]=ts[i];
            }
        if (k>0) while (k--) s[j++]=')'; // 如果左括号多了，补充右括号
        else if (k<0) // 如果有括号多了，补充左括号（这里用的方法很低效，应该还有更好的方案）
        {
            memset(ts,0,sizeof(ts)); j=0;
            while (k++) ts[j++]='(';
            strcat(ts,s);
            memset(s,0,sizeof(s));
            strcpy(s,ts);
        }
    }
    double work() // 计算表达式的值
    {
        return calc(0,strlen(s)-1);
    }
};

bool cmp(double a,double b) // 比较两个浮点数是否相等（这个方法误差其实很大）
{
    int x,y;
    double r;
    x=(int)log10(a)+1;y=(int)log10(b)+1;
    if (x!=y) return false;
    r=pow(10,x-12);
    if (fabs(a-b)>r) return false;
    return true;
}

double P1,P2,P3,Q1,Q2,Q3;
int N,i;
expr S;
char ss[100];

int main()
{
    S.scan();
    A=0.7; P1=S.work();
    A=1.4; P2=S.work();
    A=2.1; P3=S.work();
    fgets(ss,sizeof(ss),stdin);
    sscanf(ss,"%d",&N); // 读入N
    for (i=1;i<=N;i++)
    {
        S.scan();
        A=0.7; Q1=S.work();
        A=1.4; Q2=S.work();
        A=2.1; Q3=S.work();
        if (cmp(P1,Q1)&&cmp(P2,Q2)&&cmp(P3,Q3))
            printf("%c",(char)(64+i));
    }
}

```

###知识点
- cmath库中pow的使用
- cmath库中log10的使用