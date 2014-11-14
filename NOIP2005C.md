*一道比较考验思维的题目，同时也有很多需要注意的细节* 

###无解的判断###
判断无解看似容易，其实选手往往会遗漏情况。如果用图论的模型就可以考虑得很清楚了。如果A希望和B坐在一起，那么就从A往B连一条双向边。对于每个节点，把从它连出去的重复的边去掉。如果有解，那么连成的图一定是一个环，也就是说每个点度为2而且所有点属于一个联通块。否则就一定无解。

###计算最小代价###
在判断是否有解的过程中，我们可以把正确的序列列出来。我们只要比较这个序列与原来的序列有几个位置是不同的（假设有M个），那么只需要一次命令，代价为M，就可以把这些人全部换到正确的位置上了，复杂度是 *O(n)* 的。可是原题中的所有人是排成一个圈的。朴素的想法是对于每一个位置都这样判断一次，总复杂度为 *O(n^2)* ，会超时。所以需要用更巧妙的办法。

我们会发现，不管整个圈如何旋转，每两个人之间的 **相对位置** 都是不会改变的，所以我们可以作差。
> 原序列 1 5 3 2 4

> 减去了 1 2 3 4 5   

> 结果是 0 3 0 3 4

这就说明如果不转动，1和3会在正确的位置上。转动3个位置，2和4会在正确的位置上。转动4个位置，5会在正确的位置上。如果我们用 *c[i]* 表示差值序列中i出现的次数，那么 *c[i]* 中最大的数 *c[max]* 就是最多可以有几个数可以在正确的位置上，最小交换次数就是 *n-c[max]* 。复杂度 *O(n)* ，可以通过。

这道题还有一个容易被忽视的细节。把整个序列左右翻转，也是可以符合每个人的要求的。所以我们要在翻转之后再统计一次，然后输出两者答案的最小值。

###标程###

```cpp
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <climits>
#include <algorithm>
#include <vector>
using namespace std;

int n,i,x,a[50010],c[50010],ans;
vector<int> g[50010];
bool vis[50010];

void noAnswer()
{
	printf("-1\n");
	fclose(stdin); fclose(stdout);
	exit(0);
}

int main()
{
	freopen("fire.in","r",stdin);
	freopen("fire.out","w",stdout);
	scanf("%d",&n);
	for (i=1;i<=n;++i)
	{
		int x,y;
		scanf("%d%d",&x,&y);
		g[i].push_back(x); g[x].push_back(i);
		g[i].push_back(y); g[y].push_back(i);
	}
	for (i=1;i<=n;++i)
	{
		vector<int>::iterator it;
		sort(g[i].begin(),g[i].end());
		it=unique(g[i].begin(),g[i].end());
		g[i].resize(distance(g[i].begin(),it));
		if (g[i].size()!=2) noAnswer();
	}
	for (x=i=1;i<=n;++i)
	{
		vector<int>::iterator it;
		a[i]=x; vis[x]=1;
		for (it=g[x].begin();it!=g[x].end();++it)
			if (vis[*it]) continue;
			else {x=*it; break;}
		if (x==a[i]&&i<n) noAnswer();
	}
	for (i=1;i<=n;++i) {++c[(a[i]-i+n)%n];}
	for (i=0;i<n;++i) ans=max(ans,c[i]);
	memset(c,0,sizeof(c));
	reverse(a+1,a+n+1);
	for (i=1;i<=n;++i) {++c[(a[i]-i+n)%n];}
	for (i=0;i<n;++i) ans=max(ans,c[i]);
	printf("%d\n",n-ans);
	fclose(stdin); fclose(stdout);
	return 0;
}

```
