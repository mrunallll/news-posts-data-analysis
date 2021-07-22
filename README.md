# news-posts-data-analysis
## Making sense out of Random Social Media Posts' views data

The goal is to understand and make sense out of the given data, in order to derive insights and this repository will take you through my steps, challenges and results in the process.

The data is in the format as specified below:

Postid | Hours since creation | Total users viewed this postid
Postid 1 | 0 | 10
Postid 1 | 2 | 12...
Postid 2 | 0 | 9 and so on ***(except, not really)***
Well then, what is the big deal about this data?

1. It is highly inconsistent, with the time period varying for each Post ID
2. There are duplicate entries for certain Post IDs (imposters, if you will)
3. While some Post IDs have 20 records of views, some have just 3! (Irregularity)
4. And, the data is invariably small (It has only 48 entries!)

Overview of the process
**Data Pre-processing —> Data Visualization &Analysis —> Deriving Insights & Conclusion.**

## Step 1 - Data preprocessing

First of all, we check what's wrong with the data, this is how it looks like.

![image](https://user-images.githubusercontent.com/69042198/126679077-926895a4-e3ba-4fab-8210-14d92e4da601.png) ![image](https://user-images.githubusercontent.com/69042198/126681896-6e6a25ec-6cba-4539-ac1d-71a8b6a95ec2.png)



>One can observe that there exists no data for hours>24 &hours<98, thus a big chunk of data is missing, for all post IDs.
>One can also observe that P3 & P5 display views predominantly during the 99th & 100th hour.
>The value '#users that view a particular post' is mutually exclusive for `ith` hour and `(i+1)th` hour, unlike what is specified in the description of data ('Total' # users viewed).

### Solution for this *bad data* is converting DataFrame to **`Pivot Table`**

### Step 1 - Convert *bad data* DataFrame into **`Pivot Table` for ease of accessibility**

```python
df_pivot = pandas.pivot_table(data, values="# users viewed this post", 
index="Hours since creation", columns="Postid ", aggfunc=np.mean)
```

### Step 2 - Scale Down all *hour* values to the interval (0-23) hours using Normalization.

99th hour and 100th hour wouldn't make sense if there's almost 70 hours' data chunk missing, so scale down 99 to 0 and 100 to 1 and so on..

### Step 3 - Impute Missing values by mean and median of previous data.

Since it seemed practically possible to sit and custom-clean the data, it also seemed logical to fill missing values in a post by taking either mean of previous posts' views around same hour, or mean/median of previous hourly views of the same post.  Here's the logic →

```python
if (previous posts' nth hour views ==0):
	df[['p3']]=df[['p3']].fillna(np.mean((p3[n-1]th hour), (p3[n-2]th hour)..)
else:
	df[['p3']]=df[['p3']].fillna(np.mean((p2[n]th hour), (p1[n]th hour)..)
```
### Step 4 - Making Assumptions

1. All the posts are related to each other
2. From (1) we can say that the value '#users that view `i+1`th post' is dependent on views of `ith` post. Thus, the hourly views of Post Ids P1, P2, P3, P4, P5, P6 are partially in sync.
3. [Not really an assumption, a statement rather] Since the original data has only 48 entries for over 6 post IDs, and is faulty at places, any manipulation/change done to improve size & accessibility does not deter the quality of data, if anything it only enhances its quality.

This is how data looks like now
![image](https://user-images.githubusercontent.com/69042198/126689468-14fc8170-3b6a-4313-a40d-8e43d167a3c5.png)

## B - Data Visualization & Insights

Let us take a look at some of our graphs now and answer some questions. The first question that one might ask is this -

Q-What does the distribution of the posts looks like?

For Post 1, we can see that this is how the graph turns out, and there are highs and lows for batch of hours.

![image](https://user-images.githubusercontent.com/69042198/126689494-5e9ebb0d-3191-4992-b428-529a6a8583ab.png)

If we wish to see a more comparative view, we can visualize all posts in one space

![image](https://user-images.githubusercontent.com/69042198/126689514-716e2500-5b82-470e-87a7-8088e8b23133.png)

Q-Why are there so many highs and lows? Shouldn't the graphs be strictly increasing as the views increase over time?

Well in the above graphs we show hourly views. To take a look at total views till hour, we take cumulative sum of the value `#users viewed this post` and plot it.

```python
plt.plot(data.cumsum())
```

![image](https://user-images.githubusercontent.com/69042198/126689558-6ec247cb-09dc-42b8-b6ab-0862a8b13310.png)

Now, in order to understand the most important aspect of this process "When does a post peak it's viewability" we need to take into account the fact that viewability peak for each post can be different, but a common local-peak can be generalized for all posts

Thus, we can see that all posts peak their viewability, after 10 hours of being created/posted. Thus the time starting 10 hours to 15 hours is when most posts get a hike in their views.
