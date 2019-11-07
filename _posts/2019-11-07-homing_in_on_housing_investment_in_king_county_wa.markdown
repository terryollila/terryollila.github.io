---
layout: post
title:      "Homing in on Housing Investment in King County, WA"
date:       2019-11-07 20:57:16 +0000
permalink:  homing_in_on_housing_investment_in_king_county_wa
---

Do you remember hearing about those deadly fish that are delicious as long as you can dissect them just the right way, while avoiding the parts that will kill you? I think it's called the fugu, and it was featured in an episode of The Simpsons where Homer ate some that may have been incorrectly prepared and they thought he was going to die. Not unlike how I felt going into this project. 

Multilinear regression is sort of like that. Find the right stats to hammer at, and you're in r^2 heaven. Put the wrong ones in and it's adios, amigo. Say goodbye to your customer. Or, in our case, your model. Which, luckily, can be resurrected if you cut out the poison parts, unlike the poor guy to whom you fed the wrong piece of fishy flesh.

There are 19 features in the King County Housing data set, not including price, which is going to be our target variable we want to chase down for predictability from the other 19. Many of those features follow a similar path. Too similar, you might say. Mixing in independent variables that correlate too strongly with each other can blow your calculations out of whack with multicollinearity. So after cleaning and exploring the data a bit, I put together my correlation grids and took a close look. I found out that there was one important decision I had to make.

The feature for square feet of living space (sqft_living) was at least somewhat in conflict with four other features: square footage of 15 neighboring homes, square footage above ground,  and to a lesser extent, number of bathrooms and grade. Pay attention to that "lesser extent" part, now. It'll be important later. I'll also circle back to tell you more about what 'grade' represents.

There are a couple ways one could go with this kind of quandry. The first way would have been to cut loose the sqft_living feature so that the rest of those features would be free for us to play with. But at the end of the day, did I really think that sqaure footage of neighboring homes, square footage above ground, grade, and number of bathrooms was going to drive the home value as much as total square footage of livable space? I made the judgment call to cut loose the other four and keep the total square footage value. If nothing else, it made things simpler to get rid of those other features, and I'm all for simplifying. 

Fast forward to model time. For the first go, I used three features agains the price target: square feet of living space, number of bedrooms, and waterfront property. It didn't go that great.

During some fidling and fidgeting, it bounced between an R^2 value of .43 and .55. I also put together a function where I could plug numbers into and see what the model would estimate for a house price. One amusing aspect was when I tried to factor in latitude prior to scaling and got a number somewhere in the ten trillion range. Unless it was Kurt Cobain's house, I didn't think I could rely on those figures. And even then, well, even I probably wouldn't pay THAT much for Kurt's old place. Chris Cornell's, maybe.

For the second model, I corrected the issue with scaling coordinates and included latitude and longitude. That one bumped up to a solid .57, with some variations showing .61 for some reason I still don't understand. But no need to. The next model was the better brother.

For model 3, I decided to resurrect grade to see what would happen, since it was a bit of a different theme than square feet of living space and might put a different spin on things. So now my features were: square feet of living space, grade, waterfront property, latitude, longitude, and age. I threw that last one in a bit later and it gave us a minor bump.

Notice what's NOT in that list? Bedrooms. When I was running my first two models, I noticed a funny thing. As I increased the number of bedrooms, the value actually went down. Wha? Well, I had to take a closer look at that. And what I found was that the value bedrooms provided dropped off sharply after 5 or 6 bedrooms. That behavior was throwing off my entire model. So it got the axe in favor of grade and age. 

The third model got a more respectable R^2 of .66, and that turned out to be my best score. I fiddled with scaling all the variables, and couldn't really do better than that. 

I did make a fourth model using recursive feature elimination, but that one didn't turn out as well. It probably had something to do with the variables I was feeding into it, as it kept trying to drop sqft_living, which was my best feature (well I think my BEST best feature is my eyes but we're only referring to our data model here). 

The thing that made that third model so much better than the others was adding in the "grade" feature. It's a measurement for King County homes of a home's quality of materials, craftsmanship, and design, and along with square feet of living space, it turns out to be a strong predictor of home value. When the two of them are paired together, they seem to push predictability up quite a bit. And I had once dropped it from the table completely.

I guess the point is that in avoiding the poison parts, you can wind up overlooking something tasty. And unlike in the kitchen, we get to take several stabs at our subject while our seafood connoisseur would seldom tolerate that kind of service. Hey, maybe that's how they found the best parts of the fish to begin with? Experimenting on volunteers (presumably unwilling) in hopes of finding the non-deadly parts of the fish, hearing them talk one after another about how delectable their meal was right before they croaked. In our case, at the end of the day, what we have is some great data to sink our teeth into. Delicious, delicious data.
