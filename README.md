# simulated-annealing
模拟退火算法
#include<iostream>//样例"pr76.tsp","pr107.tsp"
#include<vector>
#include<ctime>
#include<string>
using namespace std;
const double pi = acos(-1);
const int maxn = 109;
double d[maxn][maxn];
int n;
double sum = 0;
struct point
{
	string name;
	double x, y;//点的位置
	int num;//编号
};
vector<point> p;
double dist(point a, point b)//两点之间的距离
{
	return sqrt(pow(a.x - b.x, 2) + pow(a.y - b.y, 2));
}
double get_sum(vector<point> a)//计算路径的总长度，传入的是一条路径
{
	double addsum = 0;
	int i;
	for (i = 1; i < a.size(); i++)
	{
		addsum += d[a[i].num][a[i - 1].num];//对照路径上的每个点的距离表依次计算距离
	}
	addsum += d[a[0].num][a[a.size() - 1].num];//结尾的点和开头的点
	return addsum;
}
void init()//目的为初始化
{
	int i, j;
	cin >> n;//输入点的总数
	p.clear();
	for (i = 0; i < n; i++)
	{
		point t;
		cin >> t.name >> t.x >> t.y;//输入点的名称与点的位置
		t.num = i;
		p.push_back(t);
	}
	for (i = 0; i < n; i++)
	{
		for (j = i + 1; j < n; j++)
		{
			d[i][j] = d[j][i] = dist(p[i], p[j]);//生成距离矩阵
		}
	}
	sum = get_sum(p);
}
void M_C()//得到一个较好的初始解
{
	vector<point> cur;
	cur = p;
	int i, j, k;
	double newsum;
	srand((unsigned)time(NULL));//随机种子
	for (i = 0; i < 8000; i++)
	{
		cout << i << endl;
		//cout << i << endl;
		for (j = 0; j < n; j++)
		{
			int k = rand() % n;
			swap(cur[j], cur[k]);
		}
		double newsum = get_sum(cur);
		if (newsum < sum)//8000次摸奖，摸到好的就采纳
		{
			sum = newsum;
			p = cur;
		}
	}
}
double e = 1e-16, at = 0.99999999, T = 1.0;//e表示终止温度  at表示温度的变化率  T是初始温度
int L = 20000000;//L为最大迭代次数
void Si_An()
{
	while (L--)//最多迭代L次
	{
		cout << L << endl;
		vector<point> cur = p;
		int c1 = rand() % n;
		int c2 = rand() % n;
		if (c1 == c2)
		{
			L++;
			continue;
		}
		swap(cur[c1], cur[c2]);
		double df = get_sum(cur) - sum;//计算前后总结果减小了没有
		double sj = rand() % 10000;//构造10000以内的随机数
		sj /= 10000;
		if (df < 0)//接受更优的结果
		{
			p = cur;
			sum += df;
		}
		else if (exp(-df / T) > sj)//结果不是最优，可以以一定概率接受跳出局部最优解，温度越低，跳出概率越小
		{
			p = cur;
			sum += df;
		}
		T *= at;//降温
		if (T < e)//温度退到0时退出循环
			break;
	}
}
void show()//输出总长度和路径
{
	int i;
	cout << "路径长度" << sum << endl;
	cout << "路径";
	for (i = 0; i < n; i++)
	{
		cout << ' ' << p[i].name;
	}
	cout << endl;
}
int main()
{
	init();
	M_C();
	Si_An();
	show();
	system("pause");
	return 0;
}
