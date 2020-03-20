---
layout: post
title:      "From Words to Pictures"
date:       2020-03-16 23:15:02 -0400
permalink:  from_words_to_pictures
---

## Visual Engineering in Natural Language Processing

Creating an easily digestible analysis of a text medium, one that stakeholders can easily breeze through and get the gist of the information without killing a day parsing dense tables about word counts and embedding layers, isn't something that exactly springs forth from the fingertips when drilling code into the machine. It's one thing to pound out feature after feature of text attributes using spacy or nltk, and another completely to sit down with matplotlib or plotly and try to figure out how, exactly, to transform all these word stats into meaningful graphs.

I recently coded a project attempting to discern scripts from highly rated movies apart from lowly rated ones. In the process, I needed to find a way to bring out the information in a way that would be quickly and easily understood by someone glancing over it. There were challenges, not least of which was learning plotly and dash from scratch in order to bring it all to life, but it came together. I'll now go over some of the strategies I used to make it work.

Word clouds might be a little bit cliche these days, but in the right context they can be an effective way to visualize the importance of text in two dimensions: not just a selection of important words, but a relative sizing of how prominent those words are in the source text. In determining good scripts from bad, this was an obvious choice, as I could easily show, side by side, the good and the bad. I made these clouds mutually exclusive, so that we only saw the bad-script words that didn't appear in good scripts, and vice versa. Using the wordcloud python library made everything easy, as I was able to insert a mask (from http://www.clker.com/) straight into the code to make images for the finished product:

![thumbs up/down word cloud](https://github.com/terryollila/dsc-capstone-project-v2-online-ds-ft-100719/blob/master/images/two_thumbs-md.png?raw=true)


Credit to James Irving of Flatiron  School for the inspiration!

Naturally, that's not all there is. With the same sort of idea, a good bar chart can still do the job. This one is multi-faceted. On the dashboard, it toggles between three sets of data: Overall word importance in generating a predictive machine learning model, top correlation with good-script words, and top correlation with bad-script words. Here are the three of them side by side:

![important features](https://github.com/terryollila/dsc-capstone-project-v2-online-ds-ft-100719/blob/master/images/all_words.png?raw=true)

Pardon the obscenity.

And then there's this fella. Plotly and dash have great functionality for generating easy dropdown graphics, so I put that to use with some distribution plots. I started with histograms, but these were much more interpretable and, well, better looking. 

![word feature distribution plots](https://github.com/terryollila/dsc-capstone-project-v2-online-ds-ft-100719/blob/master/images/dist_example.png?raw=true)

That graph has 22 different dropdown items, including high-level attributes such as sentence length and unique words, to sentiment analysis, and down to the various parts of speech like nouns and interjections.

I won't go through the entire set of code, but I'll note that I tried to keep the functional graph parts out of the way as much as possible. The portion of the html skeleton, which could easily have gotten out of control in a hurry, was just this:

```
html.Div(id='hist', children=[
                dcc.Dropdown(id='hist_selector',
                         options=POS_hist_selector_list,
                         value='word_count'),
                dcc.Graph(
                    id='hist_graph')
                ]),
```
The actual callback to create the graph had a little bit more going on. Here's the whole thing:
```
# Callback and function for POS graphs and switching dropdown.
@app.callback(Output(component_id='hist_graph', 
                     component_property='figure'),
              [Input(component_id='hist_selector',
                     component_property='value')])
def insert_hist(POS_label):
    
    # Separating out the good from the bad.
    data1 = screenplays_cut[screenplays_cut.good_or_bad == 1]\
    [POS_label]
    
    data0 = screenplays_cut[screenplays_cut.good_or_bad == 0]\
    [POS_label]
    
    # Creating a cutoff at 3 standard deviations so that the graphs don't get 
    # skewed to one side.
    std_high_1 = data1.mean() \
        + data1.std()*3
    std_low_1 = data1.mean() \
        - data1.std()*3
    
    plot_info_1 = data1.drop(data1[lambda x: x > std_high_1].index)
    plot_info_1 = plot_info_1.drop(plot_info_1[lambda x: x < std_low_1].index)
    
    # Same thing for the bad scripts.
    std_high_0 = data0.mean() \
        + data0.std()*3
    std_low_0 = data0.mean() \
        - data0.std()*3
    
    plot_info_0 = data0.drop(data0[lambda x: x > std_high_0].index)
    plot_info_0 = plot_info_0.drop(plot_info_0[lambda x: x < std_low_0].index)
    
    # I need to create different bin sizes for various plots, since there is
    # such a variety of scales here.
    bin_size=0
    
    if plot_info_1.mean() > 500:
        bin_size = 100
    elif plot_info_1.mean() > 1:
        bin_size = .1
    elif plot_info_1.mean() > .1:
        bin_size = .005
    elif plot_info_1.mean() > .01:
        bin_size = .001
    else:
        bin_size = .0004

    fig =  ff.create_distplot([plot_info_1, plot_info_0], 
                              bin_size=bin_size,
                              group_labels=['Awesome Films', 'Awful Films'],
                              colors=['rgb(255,20,20)', 'rgb(35,230,90)'],
                              )
    fig.layout.update(title='Screenplay Metrics',
                      yaxis={'title':'Frequency'})
    
    return fig
```

A lot of the extra code just had to do with me cutting off outliers to make the graphs more readable, and creating multiple bin sizes due to the vast differences in measurements. The important part was right there at the end, in the fig. But it shows the flexitility with which you can create a cool (and cool looking!) visualization of language-based data without a lot of fuss.

After all the exploratory data analysis, which this all came out of, I went on to model everything using the features I had created previously, along with TFIDF  (term frequency/inverse document frequency) vectorization to really pick the scripts apart word by word. I make it sound tedious, but really the sklearn method does it in a snap.

To get my best results, I used two models in sequence: first a support vector machine using a TFIDF matrix, which got things most of the way. After that, I used the test predictions, which had not seen the model itself, to feed into a random forest classifier using the features talked about previously. That got me to 68% accuracy. Not bad considering how many other factors can go into how a movie gets rated, including cast, directing, editing, etc.

Here's the confusion matrix with all the detail of TPR and FPR etc., if you're into that sort of thing (like I am).

![confusion matrix](https://github.com/terryollila/dsc-capstone-project-v2-online-ds-ft-100719/blob/master/images/conf_matrix.png?raw=true)

The entire thing is called What's a Writer Worth: Screenplay Science and the Value of a Few Good Words, and can be found here:

[What's a Writer Worth](https://github.com/terryollila/dsc-capstone-project-v2-online-ds-ft-100719)

And I've built an interactive dashboard for it here:

[Screenplay Science Dashboard](https://lit-hollows-89977.herokuapp.com/)

Thanks for reading!


