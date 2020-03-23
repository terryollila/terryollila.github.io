---
layout: post
title:      "Boxes Within Boxes"
date:       2020-03-23 02:14:23 +0000
permalink:  boxes_within_boxes
---

## Scraping the Naked Web for Sweet, Sweet Data

If you haven't familiarized yourself with web scraping before, it might seem a little bit daunting. Going behind the face of the web and digging into the HTML of a complicated site to find the data you need. Especially for modern sites that have banners and widgets and infinite scrolling and and pop-ups and magic icons that expand and contract and a thousand other things that can seem like the dense vegetation of a jungle you'd need to cut through to get your prize: A list of article authors or image URLs. 

But guess what? It's all an illusion.

All that crazy, wacky, glossy, multi-colored stuff that reaches out to you when you navigate to a site is just a distraction. All you really need to do web scraping (at least in most of the simpler tasks) is a little knowledge of HTML, and to know that web pages are all boxes within boxes within boxes. That's it.

Notice the thing I didn't mention in that sentence? The thing that's almost always mentioned in the same breath as HTML? 

CSS.

To somebody entering into web scraping for the first time, and who may not be 100% familiar with web page infrastructure, there's  a fantastic example to illustrate what I'm about to talk about.

If you've seen this before, you'll know right where I'm going. If you haven't, then you're in for a treat. Especially if you're interested in getting to the underpinnings of web sites a little better.

Take a click and head over to the [CSS Zen Garden](http://www.csszengarden.com/). At first glance, it'll just look like a regular site. Maybe a blog. Slick, but nothingn out of the ordinary. 

Don't read it. Not right away. Instead, click on some of the links to the right, or click on 'view all designs.'

Do you start to notice anything similar about them? Spoiler hint, it's not the design. Give each one a superficial look-through and move on. 

Just a showcase for different types of design? You're right. And wrong.

Every single one of those many, many pages is the same exact page. The *same exact page*. That is, the same html. People have put some brilliant CSS styles on top of it, but underneath, it's the same skeleton. Sort of like how people can look so different, yet their skeletons are pretty similar, aside from variations in size and between sexes. 

As a web scraper, you need not concern yourself with any of those hundreds of faces you might see on a web site. All you want is the stuff underneath. The skeleton. And it gets even better. All you need to worry about is navigating a part of that skeleton. The ribcage, let's say. How much easier would surgery be if all they had to do was get straight inside without worrying about skin and blood and muscle and tendons? That's you. Doing surgery on a skeleton. The heart's just sitting there waiting for you to cut into it. Easy. Well, pretty easy. You still need too know how to operate on the heart.

To illustrate, you can disable the CSS on your browser and see what these sites look like without their clothes on. For Chrome, you have to get the Developer Tools widget and then hit the CSS tab, then clicke 'Disable All Styles'. It'll be different for other browsers, but a quick Internet search should get you there in short order.

And there are some great tools for that. I use BeautifulSoup, a Python library they use as part of the Learn curriulum at Flatiron School. And it does most of the heavy lifting. If you find the site you want to navigate, strip out the CSS (or not, it's almost as easy either way once you know how it works underneath), open up the web inspector, and start moving your cursor through the code it shows you. When you see the part of the page that you want come up highlighted, stop. That's the piece of the html you want. Click open the arrow and you'll step into the next box. Do the same thing to find the item you want (probably a sub-set of what you saw earlier), and that's the next box you want in the nested structure. Keep that up and you'll find the gem you're looking for. If you're lookinig for a list of items from a regularly spaced structure, like titles in a list of books, just find the first one and you'll be all set to grab the rest when you start writing your code.

Once you're there, you'll want to start slowly backing out, taking note of any class names and ids that you find. The ones closest to your item are the ones you'll probably want, because you'll be able to start there and skip all the outer layers. Your parser (BeautifulSoup if you're using what I use) will take it from there. You'll code in the id or class name and then use some simple code to drill into the thing you want. Some loops and you can get every book title on that page. Or whatever. Your parser will have documentation on the best way to do that, but if you've been coding in Python for a while it shouldn't cause you much trouble at all. There are some methods for navigating children and siblings, and once you've gotten the hang of that, you're home free.

I'll show an example from some code I wrote recently as part of a dive into movie titles from metacritic and rottentomatoes. This bit is from metacritic, as I was grabbing their top 2,000 titles:

```
goods_titles = []

for i in range(0,20):
    # There are 10 pages to flip through of 100 movies each.
    page = requests.get(
        'https://www.metacritic.com/browse/movies/score/metascore/all/filtered?page={}'.format(i),
        headers={'User-Agent': 'Chrome/80.0.3987.116'})
    soup = BeautifulSoup(page.content, 'html.parser')
    
    # Now that we've gotten the content from the page, we need to loop through each element.
    for i in range(0,100,1):
        title = soup.find_all('span', class_="title numbered")[i]\
            .next_sibling.next_sibling.contents[1].contents[0]
        goods_titles.append(title)
    
    # We're only pinging 10 times but might as well be safe since it costs like
    # nothing.
    time.sleep(1)
```
That. Is. It. 

And I even had to do something mildly complicated: taking content from 10 different pages in one script. That was a simple loop with an adjustment to the page number in the URL each iteration to move it forward.

Quick tip: You see that bit about User-Agent? That's to keep the target from identifying your script as some bot that's going to use the site's content for nefarious purposes or cause an unusal drain on the site's resources. But that's now what you're doing, is it? No, you're on the up and up. I hope.

Along those lines, the 1 second delay for each iteration is to keep from overloading the host system, or from making it think you're attacking it with excessive requests. There's actually a better way to do that, by using random pauses instead of the 1 second pauses, so I recommend going that route instead.

There can, of course, be complications. The part you want is hidden in a jumble of JavaScript. Or there are JavaScript buttons you need to press to load in the pieces you want to get to. Anything involving JavaScript, really. There are more tools for those, but it gets a bit more involved. That's a good place to go when you've got this bit down. As I'm just starting to do.

One more piece of advice before I go: When you set up your initial code to get into a page, check that there is something in the parser before you try to dig into it. It's common to have some kind of error early on (Like leaving out the header identifying you as a browser!) and you won't realize you don't have anything to parse until after you've already thrown your monitor out the window.
