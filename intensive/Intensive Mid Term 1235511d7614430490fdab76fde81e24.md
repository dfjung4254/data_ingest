# Intensive Mid Term

## Data Engineering - Group D

### Members : 신성훈 수석, 임성민 선임, 정근화 선임

## ✍🏻 Questions

```java
1. Map of restaurants across the United States
2. Which cities have the highest number of restaurants?    #Toronto
3. Which are the top 15 subcategories in Restaurants?
4. What ratings do the majority of restaurants have?
5. What is rating distribution in the restaurant reviews?
6. Which type of restaurants get good reviews? How about bad reviews?
		a. This will depend on what you consider a good rating. Above 4 star erhaps? You choose.
		b. Similarly, for bad reviews. What would be considered a bad review?
7. Which restaurants have the most reviews?
8. What number of yelp users are elite users? Do they rate differently than non-elite users?
```

## 🐳 Solutions

1. **Map of restaurants across the United States**
    - Answer

    ```sql
    select name, latitude, longitude, state from restaurants
    where length(state) = 2;
    ```

    - Result

    ![Intensive%20Mid%20Term%201235511d7614430490fdab76fde81e24/_2021-06-01_14.57.58.png](Intensive%20Mid%20Term%201235511d7614430490fdab76fde81e24/_2021-06-01_14.57.58.png)

2. **Which cities have the highest number of restaurants?**
    - Answer

    ```sql
    -- #3-2 answer
    select city, count(city) from restaurants_df
    group by city
    order by count(city) desc limit 5;
    ```

    - Result

    [Cities which have the highest number of restaurants](https://www.notion.so/e0bb7204c30647e98d434696ecd790c2)

    ![Intensive%20Mid%20Term%201235511d7614430490fdab76fde81e24/_2021-06-01_13.11.13.png](Intensive%20Mid%20Term%201235511d7614430490fdab76fde81e24/_2021-06-01_13.11.13.png)

3. **Which are the top 15 subcategories in Restaurants?**
    - Answer

        ```sql
        -- #3-3 answer
        select cat2_exploded as Subcategories, count(cat2_exploded)
        from restaurantsrestaurantsrestaurantsdf_new_new
        where cat2_exploded != 'Restaurants'
        group by cat2_exploded
        order by count(cat2_exploded) desc
        limit 15;
        ```

    - Result

    [Top 15 subcategories in Restaurants](https://www.notion.so/e3d680395d0f4d7ba50b68d06163ae73)

4. **What ratings do the majority of restaurants have?**
    - Answer

    ```sql
    -- #3-4 answer 
    select stars,count(stars)
    from restaurantsdf_new
    group by stars
    order by count(stars) desc
    limit 1;
    ```

    - Result

        ## 🌟 4.0 Stars count 13526!

    [Ratings, restaurants have the majority](https://www.notion.so/35255f612ea6488bb5b0fbb2a3796358)

5. **What is rating distribution in the restaurant reviews?**
    - Answer

    ```sql
    -- #3-5 answer 
    select stars,count(stars)
    from restaurantsdf_new
    group by stars
    order by count(stars) desc;
    ```

    - Result

    [Rating distribution in the restaurant reviews](https://www.notion.so/7c0d9f52c33c42c29d6da014a1a2b927)

    ![Intensive%20Mid%20Term%201235511d7614430490fdab76fde81e24/_2021-06-01_14.09.41.png](Intensive%20Mid%20Term%201235511d7614430490fdab76fde81e24/_2021-06-01_14.09.41.png)

6. **Which type of restaurants get good reviews? How about bad reviews?**

    a. This will depend on what you consider a good rating. Above 4 star erhaps? You choose.

    - Answer

    ```sql
    -- #3-6 answer
    select name, categories
    from restaurants_df
    where stars = 5;
    ```

    - Result

    b. Similarly, for bad reviews. What would be considered a bad review?

    - Answer

    ```sql
    -- #3-6 answer
    select name, categories
    from restaurants_df
    where stars = 1;
    ```

    - Result

7. **Which restaurants have the most reviews?**
    - Answer

    ```sql
    -- #3-7 answer
    select name, review_count
    from restaurantsdf_new
    order by review_count desc limit 5;
    ```

    - Result

    [Top 5 Restaurants which have the most reviews](https://www.notion.so/baeda0dd2d864807959b320bb1eedbf7)

    ## 🎉 Mon Ami Gabi has the most reviews with 7361!

8. **What number of yelp users are elite users? Do they rate differently than non-elite users?**
    - Answer

    ```sql
    -- #3-8 answer - elite users
    select count(*) as elite_count from (
      select user_id, count(elite)
      from elitedf_new
      group by user_id
    ) a;

    -- #3-8 answer - elite users average stars
    select avg(average_stars) as elite_star_average from (
      select user_id, count(elite), avg(average_stars) as average_stars from elitedf_new
      group by user_id
    ) b;
    ```

    - Result

    [Number of elite yelp users](https://www.notion.so/6e86997e5c264816ac3b64730dfd1732)

    [Average stars of elite yelp users](https://www.notion.so/2dba64eb58094bb38111543654ad506f)

# Group Presentation

### Subject : The hottest restaurants with photos.

```sql
# 주제 : 최근 가장 Hot한 식당 찾기 - 가장 사진을 많이 가진 식당

- 1. 가장 많은 사진(Hot한) 식당 Top 10
- 2. 가장 Hot한 식당을 가진 도시 Top 10
- 3. 2015년 가장 좋은 리뷰(4점 이상)를 받은 식당 Top 10
- 4. 2015년 가장 나쁜 리뷰(1점 이하)를 받은 식당 Top 10
```

### 1. 가장 많은 사진(Hot한) 식당 Top 10

```sql
select A.name, SUM(B.cnt) AS Pho_cnt
from restaurants A
inner join (
	select business_id,
				 count(business_id) AS cnt
	from photosdf_new
	group by business_id
) B
on A.business_id = B.business_id
group by A.name
order by Pho_cnt desc
limit 10;
```

[Top 10 hottest restaurants](https://www.notion.so/0b8a846d36b74abfa68a6b1fae3bc59f)

![Intensive%20Mid%20Term%201235511d7614430490fdab76fde81e24/_2021-06-01_15.28.12.png](Intensive%20Mid%20Term%201235511d7614430490fdab76fde81e24/_2021-06-01_15.28.12.png)

### 2. 가장 Hot한 식당을 가진 도시 Top 10

```sql
select A.city, sum(b.cnt) AS sum_cnt  from restaurants A
inner join (
	select business_id,
				 count(business_id) AS cnt
	from photosdf_new
	group by business_id
) B
on A.business_id = B.business_id
group by A.city
order by sum_cnt desc
limit 10;
```

[Top 10 cities, having hottest restaurants](https://www.notion.so/e7efc090b01a4cce88d896bc1a703179)

![Intensive%20Mid%20Term%201235511d7614430490fdab76fde81e24/_2021-06-01_15.35.52.png](Intensive%20Mid%20Term%201235511d7614430490fdab76fde81e24/_2021-06-01_15.35.52.png)

### 3. 2015년 가장 좋은 리뷰(4점 이상)를 받은 식당 Top 10

```sql
select A.name, B.rev_year, B.rev_cnt
  from restaurants A
    inner join
    (
        select business_id, date_format(reviewdf_new.`date`, 'y') AS rev_year, count(business_id) As rev_cnt
        from reviewdf_new
        where reviewdf_new.stars >= 4
        group by business_id, date_format(reviewdf_new.`date`, 'y')
    ) B
    on A.business_id = B.business_id
 where B.rev_year >= '2015'
order by rev_year, B.rev_cnt desc
limit 10;
```

[Top 10 restaurants, got best reviews (over 4.0)](https://www.notion.so/e72d6d1f6b0a46da8a460573b489a798)

![Intensive%20Mid%20Term%201235511d7614430490fdab76fde81e24/_2021-06-01_15.50.10.png](Intensive%20Mid%20Term%201235511d7614430490fdab76fde81e24/_2021-06-01_15.50.10.png)

### 4. 2015년 가장 나쁜 리뷰(1점 이하)를 받은 식당 Top 10

```sql
select A.name, B.rev_year, B.rev_cnt
  from restaurants A
    inner join
    (
        select business_id, date_format(reviewdf_new.`date`, 'y') AS rev_year, count(business_id) As rev_cnt
        from reviewdf_new
        where reviewdf_new.stars <= 1
        group by business_id, date_format(reviewdf_new.`date`, 'y')
    ) B
    on A.business_id = B.business_id
 where B.rev_year >= '2015'
order by rev_year, B.rev_cnt desc
limit 10;
```

[Top 10 restaurants, got worst reviews (under 1.0)](https://www.notion.so/b8dabf6e456c40f59bab6f999aca1c39)

![Intensive%20Mid%20Term%201235511d7614430490fdab76fde81e24/_2021-06-01_15.52.50.png](Intensive%20Mid%20Term%201235511d7614430490fdab76fde81e24/_2021-06-01_15.52.50.png)