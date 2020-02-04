---
layout: post
title:      "Parameter Wrangling on Massive Data Sets"
date:       2020-02-03 20:26:31 -0500
permalink:  parameter_wrangling_on_massive_data_sets
---


They said I was crazy. They called me a madman. But did I listen? No! Because the mics were turned off and they were merely gesturing wildly with their hands.

Anyway, that's what I assume they were saying. They might have been asking me to shut up about how much I love support vector classifiers. At any rate, when we started our mod 4 project on time series forecasting, taking a nationwide approach wasn't really deemed necessary, or a good idea, or even desirable. Which is probably why I did it.

The trouble was that there was no way to effectively grid search parameters for 14,000 zip codes across the country. You'd spend a week waiting for the results, only to have it finish up and you realize you'd forgotten an apostraphe somewhere and you had to start all over again. So it goes.

But I had this idea about time series ARIMA (auto-regressive integrated moving average) parameters. As I was testing different parameter sets with different zip codes (and I tested A LOT), I noticded a funny phenomenon: The top ten parameter sets were all pretty close together in terms of lowest AIC score and how they predicted the outcome. The zips were all fairly different from each other, and there were some parameter combinations that came up with hideous results, but the top end was a tight group. 

So, knowing that, and knowing that I wanted to do a nationwide attempt, I decided that if I couldn't optimize parameters for each zip code individually, then I could at least get them all as close as humanly possible and call it an estimated effort with a high degree of predictable confidence. 

I set out in search of what I had deemed in my head "The Universal Key" parameter set that would unlock optimum forecasting for all national zip codes, or at least as many as possible. But in order to do that, I couldn't just take the top set from one zip code and hope it played out among the others. I couldn't just keep randomly searching from zip code to zip code, hoping to find one that seemed to look sort of ok. I did try this for a while, actually. It was tedious.

So I let the system do that work for me. First I developed a function for doing the basic grid-search leg work: creating combinations of parameters, passing them into the model, then looping the model enough to run each set one time and return the highest AIC scores.

If you haven't done a drid search for ARIMA modeling, it's got a lot of stuff like this:

```
    p = range(0,p_max)
    d = range(0,d_max)
    q = range(0,q_max)
```		
and:

`pdq = list(itertools.product(p, d, q))`

and a little bit of:

```
            temp_df = pd.DataFrame(data = df_data, columns = columns)
            ans_df = pd.concat([ans_df, temp_df])
						```
with some:
```
    ans_df = ans_df.sort_values('AIC', ascending = True)
    ans.sort(key = lambda x: x[1], reverse = False)
		```
and some:

`return ans, ans_df, rejects`

to bring it on home. I left out the modeling because if you didn't know that, you probably wouldn't be here, and my fingers are tired.

Instead of getting just the top number, though, I decided to grab the top ten scores for the given zip code and put them in an array. Remeber how I said the top ten were pretty close to each other in predictability? I was cashing in on that idea here. But that wasn't the end of it.

I got to some random sampling. I took that little loop and ran it for 25 randomly selected zip codes, creating 25 sets of top-ten lists. I then searched for the parameters that were common to *all* of them. Generally speaking, depending on the random search, there were between 1 and 3 that shook out. 

But really, that's not quite sufficient, is it? Because you wind up with a result or two or three, but it might have been different with a different run of 25. After running it a couple times and marking down the results by hand, I decided to just do it right and put it in another loop to run 25 times. At the end of it, I had a dictionary that accumulated the results from each run and how often each parameter set appeared in every one. There turned out to be a couple that were very close in terms of how often they were at the top. I picked the slightly better one and drove on from there. 

Here's some of the code, just to get a sense of it:
```
for _ in range(0, 25):
    zip_list = zdf_by_zip.columns
    random_zips = np.random.choice(zip_list, 25)
    zip_dict = dict()
    univ_list = []
		
		    for azip in random_zips: 
				
				        univ_list, order_df, fails = aic_search(zdf_by_zip[azip], p_max = 2,
                                                d_max = 2,  
                                                q_max = 2,
                                                end = '2017-04-01')
																								
			  univ_list = [x[0] for x in univ_list]
        if len(univ_list) >= 10:
            zip_dict[azip] = univ_list[:10]
						
						
					    comb_list = []
    
    start = 0
    for v in zip_dict.values():
        if start == 0:
            comb_list = v.copy()
            start = 1

        for i in comb_list:
            if i in v:

                continue
            else:

                comb_list.remove(i)
                continue

    order_list.append(comb_list)
```

Then, going on to count it all up and pick the best one out of that whole mess of stuff:

```
order_list2 = order_list.copy()

flat_list = [item for sublist in order_list2 for item in sublist]

set_list = set(flat_list)

counted_orders = dict()

for orders in set_list:
    counted_orders[orders] = flat_list.count(orders)

Print('After 25 runs of 25 random samples, these are the counts of the top model parameters that appear the most times')
print('-----'*20)
counted_orders
```

The hard work done, I went on to do a massive modeling loop for every zip code in the data set, but with a single parameter set to use for every single instance, it was no longer such a daunting computataional task. Perhaps... 60 to 90 minutes? Pretty reasonable in the grand scheme of things, though you can bet I saved a csv each time just to make sure I wouldn't have to run it again unecessarily.

I think I got some pretty good results, after all was said and done. None of the  graphs looked too goofy, anyway, and appeared at the very least believable. I have a lot of confidence in the modeling, and though it might not be as perfect as using an individualized set of parameters for each and every zip code, in the interest of time and computing power, I think it was an excellent tradeoff and probably got close enough to make it hardly indistinguishable from the real thing. I wish I had a way to prove it; I would love to validate the process I went through to make it happen.

Oh, and if you're wondering what the top zip code turned out to be (after accounting for flukes and riskier neighborhoods), it was in Denver, encompassing Mile High Stadium, where the Broncos play. Which just happens to  be about a 30 minute drive from where I live. I promise, it was merely a coincidence. But still kind of cool.


