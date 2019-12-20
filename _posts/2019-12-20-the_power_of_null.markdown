---
layout: post
title:      "The Power of Null"
date:       2019-12-20 19:41:26 +0000
permalink:  the_power_of_null
---


The status quo is boring. Morover, people seek improvement. There's a human drive for changing things for the better, and rightly so. After all, without it,  we wouldn't have Evil Dead 2. And when it comes to testing hypotheses, there is an inherent desire to *prove* something new. Show something different. Something people haven't seen before. Create change for the better. Reject that null hypothesis like an obnoxious drunk at the corner bar.

Null isn't very sexy. It's nothing. Alternative, on the other hand... well, it brings to mind powerful powercords and popular yet brooding musicans (cue my second Kurt Cobain / Chris Cornell reference in two posts). Maybe that's too specific of a reference, coming from a Gen-Xer, so insert your most pleasant assotiation so my point doesn't go off the rails. Anyway, who wants to publish a paper based on nothing happening?  Null seems more like, let's say, Dave Matthews Band: devoid of any meaning or interest. (Sssss burn.) That lack of interest by itself can create bias in scientific journals, but that's a subject beyond the scope of this post, and beyond my range of knowledge.

But there's something to be learned by nothing. Science isn't always about change; it's about truth and knowledge. And knowing something doesn't cause a change can be as important as knowing something that does. 

Take, for example, a hypothesis that eating ice cream causes weight gain. The null hypothesis in this case, assuming we didn't already have numerous studies to lean on, would be that eating ice cream causes no change to body weight. Nothing. Null. "No change!" they all cried after the bandages were removed. You get the reference, right? Put it in your pocket for later.

An alternative hypothesis would be that eating ice cream causes weight gain. (We'll assume a one-tail test here, to make things more dramatic.) So, how sexy is Curt Cobain now? No, I mean literally. Now. Like, over twenty years after he died. Did I just make this data science blog too dark? 

As I was saying, the null hypothesis, here, sounds pretty great. Dive into that caramel toffee crunch, baby, and throw some peanut butter in there while you're at it (we'll assume for now that there are no additional effects caused by the caramel, toffee, and peanut butter; let's leave those studies for another time so they don't ruin our fun). Spoiler alert, though--you're unfortunately not likely to find much research in support of this claim. We're pretending here, right? I can accept or reject whatever hypothesis I want to in fantasy land.

Getting to the point, for everybody who hasn't rolled their eyes and stopped reading. In delving into the Norwhild dataset, which is a database of a fictional company that houses information on employees, customers, orders, and products I combed the tables for something interesting to say. 

I had four hypotheses for this report. Taken in their null form, they went a litte something like this:

* Discounts have no effect on order quantity.
* 
* Discounts have no effect on order revenue.
* 
* Customers with late shipments ordered the same amount of product as customers without any late shipments.
* 
* There is no difference among employees in the proportion of products they sell, by product category

I was able to soundly reject the null hypothesis for the first one--customers did in fact appear to be purchasing more product if it was discounted. Who doesn't want a deal?

The second null hypothesis failed to be rejected. Discounts seemed to have little to no effect on overall revenue. Our customers loved deals, but that wasn't going to make them open their pocketbooks any wider.

The third one got interesting, as I had to account for the fact that customers with late shipments tended to have more orders than those that didn't (meaning that if a customer had more orders, they were more likely to have late shipments by simple probability). After carving out samples without confounding variables, I also failed to reject the null hypothesis. Customers were not terribly disgruntled about their late shipments.

Lastly came the employee product category breakdown. This went pretty granular, but I was only able to verify statistically significant change for about 4 out of dozens of individual employee-category matchups. So, call it mostly null, with some opportunities for guidance.

So maybe not a ton of opportunities for increasing revenue. But let's look a bit closer. If there is no increase in revenue by discounting products, maybe we can stop discounting, thereby relieving some pressure on our operations team. So while we might not increase revenue, we can save some cost. Since we're already discounting, ending those discounts can result in a net cost savings.

It gets even better with late shipments. If customers aren't getting too bent out of shape by a late shipment now and then, maybe we don't need to throw a bunch of money into shoring up our warehousing to stock enough of every possible product to ship at every possible time. Those square feet can add up, and companies have toppled thanks to mismanagement of space and stock. I know. Buy me a beer and I'll tell you all about it. (Decaf coffee is also acceptable.)

And if most sales people are moving a good mix of product, maybe you don't need to take any disciplinary action. Firings take their toll on morale, and hiring and training new employees has an unbelievable cost associated with it these days (I was astounded the first time I heard the numbers).

So is null so bad now? If you're a truth junkie like me, it can be your friend. Your buddy. The one who's misunderstood and someday, *someday*, you just know he'll get a girlfriend. 

Sometimes, after the bandages are removed, you'll find something beautiful underneath.




