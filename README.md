#include<iostream>//样例"pr76.tsp","pr107.tsp"
#include<vector>
#include<ctime>
#include<string>
using namespace std;
const double pi = acos(-1);
//const int maxn = 109;
//double d[maxn][maxn];
//double **d;
vector<vector<double>> d;
int n;
double sum = 0;
struct point
{
	int name;
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
		addsum += d[a[i].num - 1][a[i - 1].num - 1];//对照路径上的每个点的距离表依次计算距离
	}
	addsum += d[a[0].num - 1][a[a.size() - 1].num - 1];//结尾的点和开头的点
	return addsum;
}
void init(char a[])
{
	char str[100];
	int i = 0, j = 0, m = 0;
	p.clear();
	point t;
	a = strcat(a, ".tsp");
	FILE *fp;
	fp = fopen(a, "r");
	while (i < 6)
	{
		i++;
		fgets(str, 255, fp);
		//cout << str;
	}
	while (!feof(fp))
	{
		fscanf(fp, "%s\n", str);
		if (j % 3 == 1)
		{
			t.x = atoi(str);
			//cout << atoi(str) << endl;
		}
		else if (j % 3 == 2)
		{
			m++;
			t.y = atoi(str);
			//cout << t.y << endl;
			t.num = m;
			t.name = m;
			p.push_back(t);
		}
		j++;
	}
	n = p.size();
	//cout << n << endl;
	//d = new double*[n];
	for (i = 0; i < n; i++)
	{
		d.push_back(vector<double>());
	}
	for (i = 0; i < n; i++)
	{
		for (j = 0; j < n; j++)
		{
			d[i].push_back(0);
		}
	}
	for (i = 0; i < n; i++)
	{
		for (j = i + 1; j < n; j++)
		{
			d[i][j] = d[j][i] = dist(p[i], p[j]);//生成距离矩阵
		}
	}
	sum = get_sum(p);
	fclose(fp);
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
double e = 1e-40, at = 0.99999999, T = 1.0;//e表示终止温度  at表示温度的变化率  T是初始温度
int L = 200000;//L为最大迭代次数
void Si_An()
{
	while (L--)//最多迭代L次
	{
		//cout << L << endl;
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
	char a[10];
	cin >> a;
	init(a);
	M_C();
	Si_An();
	show();
	system("pause");
	return 0;
}
