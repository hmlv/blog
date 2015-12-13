---
layout: post
title:  "Best Tiem to Buy and Sell Stock"
date:   2015-12-13 20:00:00
categories: OJ
---


这是leetcode上的一组题，共4道。题目的大意是给定一只股票连续n天的价格，要求对这只股票进行买卖实现最大收益，可能会限定交易次数。

##Best Time to Buy and Sell Stock##
[Problem][pro1]: Say you have an array for which the i<sup>th</sup> element is the price of a given stock on day i. If you were only permitted to complete at most one transaction (ie, buy one and sell one share of the stock), design an algorithm to find the maximum profit.
这道题目只需要从头开始遍历股票价格的数组，用一个变量记住在当前位置i之前出现的最低价格min，然后用prices[i]-min与当前最大收益进行比较，记录出现过的最大收益。

代码：

		class Solution {
		public:
			int maxProfit(vector<int>& prices) {
				if(prices.size()==0)
				{
					return 0;
				}
				int min_price = prices[0];
				int max_profit = 0;
				for(int i = 1; i < prices.size(); i++)
				{
					min_price = ((prices[i]<min_price)?prices[i]:min_price);
					if(prices[i] - min_price > max_profit)
					{
						max_profit = prices[i] - min_price;
					}
				}
				return max_profit;
			}

##Best Time to Buy and Sell Stock II##
[Problem][pro2]: Say you have an array for which the i<sup>th</sup> element is the price of a given stock on day i. Design an algorithm to find the maximum profit. You may complete as many transactions as you like (ie, buy one and sell one share of the stock multiple times). However, you may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).
这道题目没有买卖次数的限制，只要求多次交易后累计的最大收益。

*思路*：如果已经买入股票且明天的价格低于今天，那么就必须卖出股票；如果还没买入股票，且明天的价格高于今天，那么买入股票；其余情况不交易。

代码：

 		class Solution {
		public:
			int maxProfit(vector<int>& prices) {
				if(prices.size()<=1)
				{
					return 0;
				}
				int profit = 0;
				int buy_price = 0;
				bool isbuy = false;
				int i = 0;
				if(prices[0]<prices[1])
				{
					buy_price = prices[0];
					isbuy = true;
				}
				for(i = 1; i < prices.size()-1; i++)
				{
					if(isbuy && (prices[i]>prices[i+1]))
					{
						profit += (prices[i]-buy_price);
						isbuy = false;
					}
					else if((!isbuy) && (prices[i]<prices[i+1]))
					{
						buy_price = prices[i];
						isbuy = true;
					}
				}
				if(isbuy)
				{
					profit += (prices[i]-buy_price);
				}
				return profit;
			}
		};

##Best Time to Buy and Sell Stock III##
[Problem][pro3]: Say you have an array for which the i<sup>th</sup> element is the price of a given stock on day i. Design an algorithm to find the maximum profit. You may complete at most two transactions.

这道题限定交易次数小于等于2，求最大收益。

*思路*：我的思路是首先扫一遍数组，求得单次最大交易的买入时间和卖出时间（相当于贪心一次），因为可以确保这两个时间必定出现在累计最大收益的交易时间中，但他们可以不属于同一次交易。
获得这两个时间点后，整个数组可以分为前中后三部分，接下来只需要分别求得这三个部分中的单词最大交易，中间一部分我选择反着访问数组部分。

代码:
	
		class Solution {
		public:
			int maxProfit(vector<int>& prices) {
				int size = prices.size();
				int i = 0;
				
				int min_price = 0;
				int max_profit = 0;
				int min_loc = 0;
				int buy_loc = 0;
				int sell_loc = 0;
				
				int left_profit = 0;
				int right_profit = 0;
				int middle_profit = 0;
				
				if(size < 1)
				{
					return 0;
				}
				
				min_price = prices[0];
				for(i = 1; i < size; i++) 
				{
					if(prices[i] < min_price)
					{
						min_price = prices[i];
						min_loc = i;
					}
					if(prices[i] - min_price > max_profit)
					{
						max_profit = prices[i] - min_price;
						buy_loc = min_loc;
						sell_loc = i;
					}
				}
				
				min_price = prices[0];
				for(i = 1; i < buy_loc; i++)
				{
					min_price = ((prices[i]<min_price)?prices[i]:min_price);
					if(prices[i] - min_price > left_profit)
					{
						left_profit = prices[i] - min_price;
					}
				}
				
				if(sell_loc < size-1)
				{
					min_price = prices[sell_loc+1];
					for(i = sell_loc+2; i < size; i++)
					{
						min_price = ((prices[i]<min_price)?prices[i]:min_price);
						if(prices[i] - min_price > right_profit)
						{
							right_profit = prices[i] - min_price;
						}
					}
				}
				
				min_price = prices[sell_loc-1];
				for(i = sell_loc - 2; i > buy_loc; i--)
				{
					min_price = ((prices[i]<min_price)?prices[i]:min_price);
					if(prices[i] - min_price > middle_profit)
					{
						middle_profit = prices[i] - min_price;
					}
				}
				
				left_profit = ((left_profit>right_profit)?left_profit:right_profit);
				left_profit = ((left_profit>middle_profit)?left_profit:middle_profit);
				
				return (left_profit + max_profit);
			
			}
		};

*注*：这道题有其他两种DP的方法，这里先讲一个，另一个留到下一题。第一种方法的思想是，依次选择某天作为两次交易的分界线，然后求得前半部分和后半部分的最大收益。先计算出子序列[0,...,i]中的最大利润，用一个数组保存下来，那么时间是O(n)。计算方法也是利用第一个问题的计算方法。 第二步是逆向扫描，计算子序列[i,...,n-1]上的最大利润，这一步同时就能结合上一步的结果计算最终的最大利润了，这一步也是O(n)。 所以最后算法的复杂度就是O(n)的。

来自[cite1]
代码:
		public class Solution {
			public int maxProfit(int[] prices) {
				if(prices.length == 0) return 0;
				int ans = 0;
				int n = prices.length;

				//正向遍历，opt[i]表示 prices[0...i]内做一次交易的最大收益.
				int opt[] = new int[n];
				opt[0] = 0;
				int low = prices[0];
				int curAns = 0;
				for(int i = 1; i<n; i++)
				{
					if(prices[i] < low) low = prices[i];
					else if(curAns < prices[i] - low) curAns = prices[i] - low;
					opt[i] = curAns;
				}
					
				//逆向遍历, opt[i]表示 prices[i...n-1]内做一次交易的最大收益.
				int optReverse[] = new int[n];
				optReverse[n - 1] = 0;
				curAns = 0;
				int high = prices[n - 1];
				for(int i=n-2; i>=0; i--)
				{
					if(prices[i] > high) high = prices[i];
					else if(curAns < high - prices[i]) curAns = high - prices[i];
					optReverse[i] = curAns;
				}
						
				//再进行划分，分别计算两个部分
				for(int i=0; i<n; i++)
				{
					int tmp = opt[i] + optReverse[i];
					if(ans < tmp) ans = tmp;
				}
				return ans;
			}
		}


##Best Time to Buy and Sell Stock IV##
[Problem][pro1]: Say you have an array for which the i<sup>th</sup> element is the price of a given stock on day i. Design an algorithm to find the maximum profit. You may complete at most k transactions.
这道题目要求在最多k次交易中求得最大收益。

*思路*：这个解法来自上一题的[Discuss][key1]。假定我们一开始我们有一定的本钱，无论是买还是卖，我们都希望手上的钱剩余最多。用两个数组分别记录买和卖的时候手上剩余的钱。动态规划的构建：我们假定在第i次“买”的时候，我们已近获得了第i-1次“卖”后剩余的最多钱；在第i次“卖”的时候，我们已近获得了第i次“买”后剩余的最多钱。
对于“买”，我们希望花最少的钱买；对于“卖”，我们希望卖后能获得最大收益。由于交易是先买后卖，我们卖的时候只能知道之前的最大收益（买入后剩余的钱），所以在循每天的价格时我们要先更新“卖”,再来更新“买”。

代码：

		class Solution {
		public:
			int maxProfit(int k, vector<int>& prices) {
				 int len = prices.size();
				 int i = 0;
				 int j = 0;
				 int ret = 0;
				 
				 if(k > (len/2+1))
				 {
					 int buy_price = 0;
					 int sell_price = 0;
					 bool is_buy = false;
					 for(i = 0; i < len-1; i++)
					 {
						 if(!is_buy && prices[i]<prices[i+1])
						 {
							 buy_price  = prices[i];
							 is_buy = true;
						 }
						 else if(is_buy && prices[i]>prices[i+1])
						 {
							 ret += (prices[i]-buy_price);
							 is_buy = false;
						 }
					 }
					 if(is_buy && (prices[len-1]>buy_price))
					 {
						 ret += prices[len-1] - buy_price;
					 }
					 return ret;
				 }
				 
				 int * sell = new int [k];
				 int * buy = new int [k];

				 for(i = 0; i < k; i++)
				 {
					 sell[i] = 0;
					 buy[i] = INT_MIN;
				 }
				 
				 for(i = 0; i < len; i++)
				 {
					 for(j = k-1; j > 0; j--)
					 {
						 sell[j] = std::max(sell[j], buy[j]+prices[i]);
						 buy[j] = std::max(buy[j], sell[j-1]-prices[i]);
					 }
					 sell[0] = std::max(sell[0], buy[0]+prices[i]);
					 buy[0] = std::max(buy[0], -prices[i]);
				 }
				 ret = sell[k-1];
				 delete []sell;
				 delete []buy;
				 return ret;
			}
		};

[pro1]: https://leetcode.com/problems/best-time-to-buy-and-sell-stock/
[pro2]: https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/
[pro3]: https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/
[pro4]: https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/
[cite1]: http://www.tuicool.com/articles/rMJZj2
[key1]: https://leetcode.com/discuss/18330/is-it-best-solution-with-o-n-o-1
