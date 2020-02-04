---
layout: post
title:      "Repetitive Model Fitting in Classification"
date:       2020-02-03 18:14:06 -0500
permalink:  repetitive_model_fitting_in_classification
---


There are a lot of different types of classification models out there. Decision trees. Random forests. K nearest neighbors. XGBoost. Support Vector Machines. They all have their ups and downs, but you know what they also have? 

Coding commonalities. 

Pipelines take advantage of this fact. The syntax in the sklearn libraries for these classifiers are all very similar--a fact you can leverage powerfully when coding to compare one against the other. Because it means there's no real need to re-invent the wheel every time you want to create a new model. If you're following the DRY concepts (Don't Repeat Yourself!), you probably already know where I'm going with this. Functions all the way, baby. 

In my recent project, *At Risk: Predictions in Mental Illness*, I wanted to tackle all the models I could in an effort to find the best means of prediction if a person was at risk for debilitating depression and/or anxiety. Though the mechanisms behind all of these classifiers are all very different, the code is so simple and similar you almost feel guilty for throwing together a few lines to create something that has, well, more math behind it than I have the powers to follow.

So in my project, I went straight for functions to do all the work, and make repeatability a breeze. But I didn't just want the capacity to model. I wanted to grid search for parameters and graph, too, with just a few lines. 

There are multiple pieces involved when creating a model, but with some care, they can all weave together nicely. I made two functions: One for the grid search, and one for the model itself. 

In the grid search function, starting off  is some train-test sample splitting.

`X_train, X_test, y_train, y_test = train_test_split(data, 
                                                    target,
                                                    random_state=42,
                                                    test_size=.25)`
																										
Probaby pretty familiar to you, right?

Next was some undersampling which wouldn't necessarily be used for everyone's data, so I'll skim past that.

Then comes the classifier... or does it? Some classifiers use an n_estimators parameter, and some don't. So I'll account for that in what's probably a clumsy fashion but, hey, I'm a student here, and it's simple and it works.

```
mod = None

if clf == SVC or clf == KNeighborsClassifier or clf == DecisionTreeClassifier:
        mod = clf()
    else:
        mod = clf(n_estimators=n_estimators)
```
				
And, of course, the grid search itself:

```
grid_search = GridSearchCV(mod, cv=5, param_grid=params,
                               return_train_score=True, verbose=verbose,
                               scoring=scoring)
 grid_search.fit(Xt_resampled, yt_resampled)
 ```

Note that my variables are Xt_resampled and yt_resampled because that's what I got out of my under-sampling. You'd probably have something different.

Next I printed up some scores; typical stuff. The function returned this:

`return grid_search.best_params_`

This generated a variable I could use to feed into my next function: the classifier itself.

Once again, I ran a train-test and undersampled, which I won't repeat here. Then we build the classification model. Basic stuff:

```
apple_tree = classifier(\**params)
    apple_tree.fit(Xt_resampled, yt_resampled)
```
Those 'params' its bringing in are straight from the grid search. Because I've broken this up into two functions, those parameters can be toyed with in between, if one didn't want to feed them straight into the classifier. Maybe you have a reason to change the gamma on that SVC, who am I to judge?

The typical prediction stuff comes after that, but there are some idiosyncrasies. For example, not everybody has a decision function. That's where this comes in:

```
try:
        y_score = apple_tree.decision_function(X_test)
        fpr, tpr, thresholds = roc_curve(y_test, y_score)
        roc_auc = auc(fpr, tpr)
    except:
        roc_auc = roc_auc_score(y_test, y_test_pred)
				```
				
As mentioned previously, I want them all to have some cool graphics I don't have to fuss with. The first one I used was a confusion matrix. The one I used was from the mlextend library:
	
```
plot_confusion_matrix(so_confused, figsize=(7,7), colorbar=True,
                          show_normed=True, cmap=plt.cm.Greens)
    plt.tick_params(labelsize=20)
    plt.title('Identification of Depression/Anxiety')
    plt.show();
		```
It comes out looking like so:
	
![](https://raw.githubusercontent.com/terryollila/dsc-mod-5-project-online-ds-ft-100719/master/output_95_1.png)
	
	
I have a thing for green.

And it comes out like that for *every single model* I create, with every single classifier. The confusion matrix is my favorite means of assessing my models, so it's great to have it pop out with no fuss without any extra effort.

You get the picture. There is a block of code that conditionally allows random forest generators and decision trees to create feature importances, since the others can't do that. Those get returned at the end, if they exist.

Finally, my favorite, which is unique to the decision tree classifier:

```
if classifier == DecisionTreeClassifier:
        dot_data = export_graphviz(apple_tree, out_file=None, 
                                   feature_names=data.columns, 
                                   class_names=np.unique(target).astype('str'), 
                                   filled=True, rounded=True, 
                                   special_characters=True)

        graph = graph_from_dot_data(dot_data)  
				
        Image(graph.create_png()) 
        return Image(graph.create_png()) 
				```			
Recognize the code? It's the one that comes out looking like a pretty, pretty picture of a tree you could almost imagine seeing at the park:

![](https://raw.githubusercontent.com/terryollila/dsc-mod-5-project-online-ds-ft-100719/master/output_84_4.png)

Love it as much as I do?

One more time: I don't have to do anything to make this anymore. It comes along for the ride when I run a model, if that model happes to be a decision tree.

The point is that a little work up front on getting your functions straight can not only save you time and trouble down the road, it can encourage you to throw in some things you might not otherwise bother to code each time you do something. And some great insights might come out of that stuff as you happen to see it get spit out of your notebook.
