A closer look at {{roam/render}}
================================

[![Swiss Army Knife](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjDjCOZeT6BMOnZr-haZEWOvNC9Us9DFE3zZUhG9KyLr5nikhzDi56oOHMRDbYiluGbiD79Lnouib_7tcg5_cDpGPPuex1MKXWLidRKXXktkDDifw5OwVowkmbfCc-zdP6hTvAwyQXYQHEB/w640-h548/swiss-army-knife.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjDjCOZeT6BMOnZr-haZEWOvNC9Us9DFE3zZUhG9KyLr5nikhzDi56oOHMRDbYiluGbiD79Lnouib_7tcg5_cDpGPPuex1MKXWLidRKXXktkDDifw5OwVowkmbfCc-zdP6hTvAwyQXYQHEB/s1000/swiss-army-knife.jpg)

Roam is like a good swiss army knife, it even has a ClojureScript development environment. I spent the past 14 days getting acquainted with `{{roam/render}}`. This post serves as a note-to-self, to summarize and organize what I have learned. 

This post is targeted at developers and Roam hackers. If you are not in one of these camps, you will likely struggle with the content.

I am not a web developer. I am new to React, Clojure, Reagent and Datalog. I am the typical dangerous/ignorant end-user who knows enough to build a monster of a macro but not enough to make it robust/testable/maintainable. In the process of playing with roam/render, at one point I nearly wiped out my entire roam database - nothing to do with roam/render, everything to do with me being stupid. Treat my conclusions and examples with caution and feel free to share better solutions in the comments or to contact me for corrections.

What is roam/render?
--------------------

`{{roam/render}}` is the native capability in [Roam](https://www.roamresearch.com/) for building custom components. These can be simple forms, calculators, sliders, or complex interactive tools like pivot tables, diagrams, flowcharts, etc.

Roam/render is a React component implemented in ClojureScript using Reagent. You can access your data in Roam's Datomic graph using Datalog. 

Practically every second word in the previous two sentences was completely new to me just a few weeks ago. Let's quickly define each.

**React** is a JavaScript library for building user interfaces. [\*](https://reactjs.org/tutorial/tutorial.html)

**Clojure** (/ˈkloʊʒər/, like closure) is a dynamic and functional dialect of the Lisp programming language. Like other Lisp dialects, Clojure treats code as data and has a Lisp macro system. [\*](https://en.wikipedia.org/wiki/Clojure)

**ClojureScript** is a compiler for [Clojure](http://clojure.org/) that targets JavaScript. [\*](https://clojurescript.org/)

**Reagent** is a ClojureScript wrapper around React. It helps you easily create React components. Reagent has three main features that make it easy to use: using functions to create [React components](https://purelyfunctional.tv/guide/reagent/js-comp), using [Hiccup to generate HTML](https://purelyfunctional.tv/guide/reagent/hiccup), and storing state in [Reagent Atoms](https://purelyfunctional.tv/guide/reagent/atoms). Reagent lets you write React code that is concise and readable. [\*](https://purelyfunctional.tv/guide/reagent/what-is-reagent)

**Hiccup** is a library for representing HTML in Clojure. It uses vectors to represent elements and maps to represent an element's attributes. [\*](https://github.com/weavejester/hiccup#:~:text=Hiccup%20is%20a%20library%20for,to%20represent%20an%20element%27s%20attributes.) Try playing with [HTML2Hiccup](http://html2hiccup.buttercloud.com/) to get a better sense of how hiccup works. Also, you can input hiccup directly into Roam, just type `:hiccup [:code "Hellow World!"]` into an empty block and see what happens.

**Atoms** are a data type in Clojure that provide a way to manage shared, synchronous, independent state. An atom is just like any reference type in any other programming language. The primary use of an atom is to hold Clojure's immutable data structures. [\*](https://www.tutorialspoint.com/clojure/clojure_atoms.htm#:~:text=Advertisements,is%20changed%20with%20the%20swap!)

**Datomic** is a new kind of database. Instead of collecting data into tables and fields, Datomic is built from **Datoms** `[entity-id attribute value transaction-id]`. This architecture provides a high level of flexibility for changing and extending the database schema without impacting existing code. [\*](https://www.zsolt.blog/2021/01/Roam-Data-Structure-Query.html)

**Datalog** is a declarative, formal logic-based query language based on Prolog. [\*](https://www.igi-global.com/dictionary/datalog/6908) I share some simple datalog examples to generate basic Roam statistics [here](https://roamresearch.com/#/app/Zsolt-Blog/page/WUn5PuTDV).

Getting started
---------------

Enable custom components
------------------------

Custom components are disabled by default. Before you can get anything done, you must enable them in user settings.

[![enable custom components in user settings](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi_-6cExmgUBg-VI-6zvvzXOK1KKvvFSBjAMtmVsjCDnGBJWG6RVBeIqLE4d5hPuIpokBL_Yg9bWuwnAUSPniWbUcvYPxW1G73Xz00UuslwW_3eB3wFE60VXD_0GeNjC4EhRdv1rTB9JFcB/w640-h468/user-settings.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi_-6cExmgUBg-VI-6zvvzXOK1KKvvFSBjAMtmVsjCDnGBJWG6RVBeIqLE4d5hPuIpokBL_Yg9bWuwnAUSPniWbUcvYPxW1G73Xz00UuslwW_3eB3wFE60VXD_0GeNjC4EhRdv1rTB9JFcB/s1195/user-settings.jpg)

  

Hello World!
------------

You can embed roam/render by adding the following code to a block: `{{roam/render: ((block-ref))}}` where block-ref references the block with your script. The script must have at least one function. If the block has multiple functions, then the last one will be called when the component is created.

The referenced block must contain a code block set to type Clojure. Codeblocks are created using triple backticks  ` ``` `. If you don't know where the backtick is on your keyboard, you can also use the / menu and select Code Block.

[![Hello World Roam Render Demo](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEguyUEw5paH07r0q5qPSt6ZDReEyZyWzh9LByEwmNrXrNGR9WKp79WwUqha6jSrBBxNF-hayRlv3vOguLinMIETVubZfbPnDVPQMjoCKkPqKnoHNqjxT5tKpb2YAzr1eEygBEu8xOrES0MY/w640-h360/HelloWorldRoamRender.gif)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEguyUEw5paH07r0q5qPSt6ZDReEyZyWzh9LByEwmNrXrNGR9WKp79WwUqha6jSrBBxNF-hayRlv3vOguLinMIETVubZfbPnDVPQMjoCKkPqKnoHNqjxT5tKpb2YAzr1eEygBEu8xOrES0MY/s960/HelloWorldRoamRender.gif)

### Step by step:

1.  In a new block type: ``{{roam/render: ((`))}}``
2.  When you start to type between the double parenthesis Roam will bring up "search for blocks". Select "Create as block below". This will create a block below your current block and automatically place a reference to it between the parenthesis.  
3.  Navigate to the new block and add two additional backticks to create a code block.
4.  Set language to Clojure.
5.  Though Hello World would work without it, it is good practice to give each component its own namespace `(ns myns.1)`. This will save you much headache down the road when functions with the same name compete and you struggle with debugging. I follow a simple pattern of sequentially numbering my test namespaces. If I am ready with a component, only then do I give it its own proper namespace.
6.  The code block should have at least one function: `(defn hello-world [])`
7.  `[:div "..."]` is the hiccup equivalent of `<div>Hello World</div>`.

```
(ns blogpost.1)
(defn hello-world []
  [:div "Hello World"])
```
Congratulations! You have built your first ever Roam component using ClojureScript! You should now see Hello World appear in the block above your code.

Receiving input arguments
-------------------------

As you start to build components for practical use, you will soon reach a point when you will want to know the ID of the block where your component is running. You need this to create components that are context-aware, i.e. they know which page they are on, what blocks are above or below them, etc.

I spent a good couple of days trying to figure out how to do this. I want to save you the effort! When a component is called, Roam will pass the block-id as the zeroeth argument to the main function (remember: the last function in your code block). Roam will also pass any additional arguments supplied when you call the component. 

Executing the script below with the input line `{{roam/render: ((Vy8uEQJiL)) 10 "input 1" ["input" "vector" "with" 5 "elements"] {:key1 "this is a map" "key2" "value 2" :key3 15} (1 2 3) #{"a" "b" "c"}}}` will produce the following output. (Note that of course, when you try this script in your own graph, the block id ((Vy8uEQJiL)) will be different, so will "eR7tRno7B".)
```
The block-uid is: "eR7tRno7B"
Number of arguments received: 6
Arg 0: 10
Arg 1: input 1
Arg 2: ["input" "vector" "with" 5 "elements"]
Arg 3: {:key1 "this is a map", "key2" "value 2", :key3 15}
Arg 4: (12 3)
Arg 5: #{"a" "b" "c"}
```

There is a lot going on here. Let's take this opportunity to learn a bit about Clojure data structures. I am passing six arguments to my component. In addition to these six, Roam by default passes the block-uid as the very first. If you want to dive deeper into Clojure data structures I recommend this [article](https://medium.com/@daniel.oliver.king/getting-work-done-in-clojure-the-building-blocks-39ad82796926).

*   The very first argument is the block-uid. It is passed as a map with a single element.
*   Next, the first argument I added to the component is an integer. I can simply pass it to the component as a number. 
*   Then comes a string. Clojure only accepts double "quotation" marks for strings. Single 'quotation' marks (apostrophes) have a different meaning. Using ' yields an unevaluated form, we will later use single quotes in datalog queries.
*   The third input is a vector. Note that in Clojure you separate elements of a vector with space. This vector has four elements, three strings, and one integer.
*   Next is a map with three keys and three values. You can use a "string" as a key, just like in a JavaScript object, however, Clojure also offers the option of using :keywords as keys. Keywords start with a colon. You may use a comma to separate key-value pairs, but it is not necessary. Notice in the input I did not use a comma.
*   Arg 4 is a list. The main use case of lists is to represent unevaluated code when you are metaprogramming. Writing code that generates or manipulates other code.
*   The final argument is a set. A set is much like a vector, with the key difference that each value in a set is unique. Also by design, the order of items in a set is arbitrary.

The code itself is self-explanatory. You should notice how I use `[:b]` the equivalent of `<b>...</b>` for bold text. [clojure.core/map-indexed](https://clojuredocs.org/clojure.core/map-indexed) will take each element of the input vector args and pass it to the anonymous function fn\[i n\], where i is the index number and n is the current element of the vector being processed.

```
(ns blogpost.2)
(defn main [ & args]
  [:div 
   [:b "Number of arguments received: "] (count args)
   (map-indexed (fn[i n] [:div [:b "Arg " i ": "] (str n) ]) args)])
```
Now that we know that the zeroeth argument is always the map with the block-uid, we can change slightly our code to receive the block-uid into a dedicated variable. This is the "standard" main function declaration I use in all my components: `(defn main [{:keys [block-uid]} & args]`. The previous example slightly re-written using my standard main function would look like this:

```
(ns blogpost.2)
(defn main [{:keys [block-uid]} & args]
  [:div 
   [:b "The block-uid is: " ] (str block-uid) [:br]
   [:b "Number of arguments received: "] (count args)
   (map-indexed (fn[i n] [:div [:b "Arg " i ": "] (str n) ]) args)])

```

A simple reagent form
---------------------

Reagent is what makes forms interactive. They allow you to dynamically change the HTML rendering of your component based on data entered by the user, either in the component itself or on the page in the graph. 

To understand the next example you first need to get your head around Clojure variables. All variables in Clojure are immutable, meaning they are created as constants. Variables can have different scopes, i.e. you can define variables at the level of the namespace, or just within a function. But once set, their value cannot change. 

There are few ways around immutability. One of them is through the use of atoms. Without going into the depth of how atoms work (btw. I wouldn't be able to explain even if I wanted to), atoms are a reference to a value. Using swap! and reset! it is possible to change this reference to a new value. With [clojure.core/swap!](https://clojuredocs.org/clojure.core/swap!) you can access the previous value of the atom before assigning it a new value, in [clojure.core/reset!](https://clojuredocs.org/clojure.core/reset!) you simply assign a new value to the atom irrespective of the previous value. 

In roam/render I use [reagent.core/atom](http://reagent-project.github.io/docs/master/reagent.core.html#var-atom) instead of [clojure.core/atom](https://clojuredocs.org/clojure.core/atom). Reagent atoms are exactly the same as normal atoms except that they keep track of [derefs](https://clojuredocs.org/clojure.core/deref) (i.e. access to their value). Reagent components that derefs one of these are automatically re-rendered. In practice, this means you can alter the component dynamically based on changes to data.

Our simple form will consist of a single input field. When you type in the name of a page in your graph, the form will return the UID for the page. You could for example use this to create a direct hyperlink to your page using the following format `https://roamresearch.com/#/app/YOUR-GRAPH/page/9char-UID`.

 ```
 (ns blogpost.3
  (:require 
   [reagent.core :as r]
   [roam.datascript :as rd]))

(defn query-list [x]
  (rd/q '[:find ?uid .
          :in $ ?title
          :where [?e :node/title ?title]
                 [?e :block/uid  ?uid]]
        x ))
  
(defn main []
  (let [x (r/atom "TODO")]
    (fn []
     [:div 	
     [:input {:value @x
      :on-change (fn [e] (reset! x (.. e -target -value)))}]
     [:br] 
     (query-list @x)])))
 ```

There are a couple of things that deserve an explanation here.

*   Notice the `:require` block right under the namespace declaration. These are namespaces referenced in our code. I will share a simple component a bit later on, that you can use to explore all available namespaces in Roam. Using `:as` you can specify an alias for referencing the namespace in your code.
*   In the `query-list` function we execute a simple datalog query using the `q` function in the roam.datascript namespace. Notice the single quotation mark at the beginning of the query `'[:find ?uid .`. Also, notice the dot after ?uid. The dot converts the result of the query to a scalar. I use it here since I am looking for a single uid value as the result.
*   Still focusing on `query-list`, notice that we don't have a statement similar to javascript `return` at the end of the function. In Clojure always the result of the very last call in a function is returned. Since `query-list` includes just a single call, to execute the query, the result of `rd/q` will be returned.
*   Notice how in `main` x is defined as a reagent atom with the initial value of "TODO". Placing the declaration outside the anonymous function that follows `(fn []` ensures that the atom is set only once when the component is created, and it is not reset to its default value every time you try to type something into INPUT.
*   The `:on-change` attribute (HTML `<input onChange="...">`) is set to a function that will reset the value of x using reset!. This will automatically trigger the update of the query because that also takes the atom as its input.
*   Notice how x is sometimes referred to as `x` and sometimes as `@x`. @ is a macro. A short form for `(deref x)`. `@x` will return the value referenced by x.

The above is an adaptation of an example by [Conor](https://twitter.com/Conaw?s=20). You can find Conor's version [here](https://roamresearch.com/#/app/help/page/Tbl1U8OFT) in the Roam help database. A key difference between my solution and Conor's, is that he is using roam.datascript.reactive instead of just roam.datascript. In this specific example, it is my understanding that there is no difference. If my understanding is correct, Datascript reactive offers a way to create queries that recognize when their result-set changes. They are used to create interactive components such as `{{table}}`.

Storing the component properties
--------------------------------

Components are re-initialized when you reopen the page or close and reopen Roam. In most cases, you will want to store the properties of your component somehow, such that next time when you open your page, you would find the component in the state you left it. 

Your options for accomplishing this all involve writing information into blocks. You have a choice as to which blocks you write to. You can write to blocks nested under the component, or write to a block on a utility page such as \[\[my component/data\]\] or update the very block from where you executed the component. This last option involves updating `{{roam/render: ((block-UID)) }}` with input arguments, in a somewhat similar way to how we printed the input arguments in the example earlier. I will demonstrate how to do it in a very simple example.

As a side note, I did an experiment with datascript.core to write custom entities into the Roam database. I was able to execute the query but wasn't able to find a way for Roam to save the changes. Manually editing a custom entity into the EDN file works ([demonstrated here](https://twitter.com/zsviczian/status/1353772383186923520?s=20)), so adding custom entities using datascript should also be possible.

 ```
 (ns blogpost.4
  (:require
   [roam.datascript :as rd]
   [roam.block :as block]
   [clojure.string :as str]))

(defn save [block-uid & args]
  (let [code-ref (->> (rd/q '[:find ?string .
                              :in $ ?uid
                              :where [?b :block/uid ?uid]
                         	     [?b :block/string ?string]]
                            block-uid)
                      (str)
                      (re-find #"\({2}.{9}\){2}"))
        args-str (str/join " " args)
        render-string (str/join ["{{roam/render: " code-ref " " args-str "}}"])]
    (block/update 
      {:block {:uid block-uid
               :string render-string}})))

(defn main [{:keys [block-uid]} & args]
  (let [some-value [1 2 "string" {:map "value"}]]       
    [:div [:strong "args: "] (str/join " " args) [:br]
     [:button
      {:draggable true
       :on-click (fn [e] (save block-uid some-value))}
      "Save"]]))
```

This component will save `some-value`. It is hardcoded for sake of simplicity in this example, but of course, you can construct whatever data structure you want in place of some-value. Notice the following:

*   My save function is called on the `:on-click` event of the button in the main function. Automatically calling save each time the component changes its value did not work well in my experiments, because each time you overwrite `{{roam/render: ((block-UID))}}`, the component re-initializes, making it impossible to fill in a form or to use a component in an interactive manner. 
*   I define three variables in save: `code-ref`, `arg-str`, and `render-string`.
*   `code-ref` will hold the current value of the block-string as I execute the datalog query to read the current value of the `:block/string` attribute filtered by block-uid.
*   `->>` is a function that threads the expression through a set of forms `(str)` and `(re-find)`. Its only purpose is to make the code more readable.
*   The regular expression in `re-find` will find the `((block-UID))` following `{{roam/render:`.
*   Once the `render-string` is ready, I call roamAlphaAPI `block/update` to update the block with my string.

Here's how the code looks like in action:

[![Saving state](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh5FjSlMz2pFDVj66XHJI9eSWO71vGpzWY4jKh6jkIRNhG_eMxK-XBPQBolbQUXAeVR0-c4jNE8m0ncl-asNI0J9WxdcZ_5EuqtCJrxKo13ROsvK06rw4xR7WyG5Hf1_FSMeHRU2ykw-e_-/w640-h360/2021-02-28+15-08-39.gif)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh5FjSlMz2pFDVj66XHJI9eSWO71vGpzWY4jKh6jkIRNhG_eMxK-XBPQBolbQUXAeVR0-c4jNE8m0ncl-asNI0J9WxdcZ_5EuqtCJrxKo13ROsvK06rw4xR7WyG5Hf1_FSMeHRU2ykw-e_-/s1920/2021-02-28+15-08-39.gif)

There also seems to be a way to trigger saving your component using the `:component-will-unmount` event handler offered by reagent in Form-3 Components. While I haven't yet tried this approach, based on documentation this should offer a way to store component properties before it disappears out of view as you navigate to a different page. If you are interested in this, you can read about it [here](https://purelyfunctional.tv/guide/reagent/#what-is-reagent). 

Javascript Interoperability
---------------------------

ClojureScript offers a simple way to call javascript functions. This is very practical when you want to access document properties or functions such as calling roam42 functions or creating a javascript hook to process data for a roam/render component (saving you the time to learn clojure).

This first example returns the location of the page.

```
(ns blogpost.5)
(defn main []
  [:div (. (. js/document -location) -href)])
```

*   Notice how the javascript document is accessed using `js/document`.
*   Properties are accessed with the `-property` notation. The javascript dot notation for accessing an object's property translates into nested parenthesis each with a call to the next property or function. 

Using `->` makes the code slightly more readable, especially in the case of long property chains.

```
(ns blogpost.6)
(defn main []
  [:div (-> js/document 
          (.-location)
          (.-href))])
```

We will now repeat the simple reagent form example from above (blogpost.3) but replacing `(defn query-list [x]` with javascript to execute our datalog query.

[![javascript interoperability](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhFfNutZz7Q7XoBljzvmYS6HyqriztYssagWT5M9B6y5SWvwgQOmMuBcS0MzT7ww0XVLbj5RiU_gQe6JyqHp1OJ3TmcilaS_caMS_msatORrmPdaRJJSenIDNNj9RnjWm_dLCHuU8DW7N36/w640-h422/javascript+interop.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhFfNutZz7Q7XoBljzvmYS6HyqriztYssagWT5M9B6y5SWvwgQOmMuBcS0MzT7ww0XVLbj5RiU_gQe6JyqHp1OJ3TmcilaS_caMS_msatORrmPdaRJJSenIDNNj9RnjWm_dLCHuU8DW7N36/s1448/javascript+interop.jpg)

Here's the ClojureScript code

 ```
 (ns blogpost.7
  (:require 
   [reagent.core :as r]))
  
(defn main []
  (let [x (r/atom "TODO")]
    (fn []
     [:div 	
     [:input {:value @x
       :on-change (fn [e] (reset! x (.. e -target -value)))}]
      [:br] 
      (.myDemoFunction js/window @x)])))
```

and the code for `{{[[roam/js]]}}`

```
window['myDemoFunction'] = function (x) {
  return roamAlphaAPI.q(`[:find ?uid .
                          :in $ ?title
                          :where [?e :node/title ?title]
                                 [?e :block/uid ?uid]]`, x);
}
```

Should you want to pass multiple variables to javascript you can do it by simply listing those after the function call `(.myFunction js/window variable-1, @an-atom, block-uid)`.

Roam namespaces
---------------

The following namespaces are available in roam/render:

**Clojure:** cljs.pprint, clojure.core, clojure.edn, clojure.pprint, clojure.repl, clojure.set, clojure.string, clojure.template, clojure.walk

**Roam:** roam.block, roam.datascript, roam.datascript.reactive, roam.page, roam.right-sidebar, roam.user, roam.util

**Others:** datascript.core, reagent.core

This code will list all the functions available in each namespace.

 ```
 (ns blogpost.8)

(defn main []
  [:div (map (fn [x] [:div 
              [:strong (pr-str x)]
              [:div (map 
                     (fn [n] [:div (pr-str n)])
                     (->> (ns-publics x)
                          (seq)
                          (sort)))]]) 
          (into [] (->> (all-ns)(map ns-name)(sort))))])

```

Tips and tricks
---------------

*   Helpful links
*   Calling components
*   Forward declaration
*   Output Roam native links
*   Debugging

Helpful links
-------------

*   [A community coding style guide for the Clojure programming language](https://github.com/bbatsov/clojure-style-guide)
*   [Clojure - Cheatsheet](https://clojure.org/api/cheatsheet)
*   [Community-Powered Clojure Documentation and Examples](https://clojuredocs.org/)
*   [A visual overview of the similarities and differences between ClojureScript and JavaScript](https://www.freecodecamp.org/news/here-is-a-quick-overview-of-the-similarities-and-differences-between-clojurescript-and-javascript-c5bd51c5c007/)
*   [My blogpost on Roam Datalog](https://www.zsolt.blog/2021/01/Roam-Data-Structure-Query.html)
*   [Datomic Queries and Rules](https://docs.datomic.com/on-prem/query/query.html)
*   [datascript.core - datascript 1.0.4](https://cljdoc.org/d/datascript/datascript/1.0.4/api/datascript.core)
*   [ClojureScript + Reagent Tutorial with Code Examples](https://purelyfunctional.tv/guide/reagent/#what-is-reagent)
*   [Clojure Data Structures Tutorial with Code Examples](https://purelyfunctional.tv/guide/clojure-collections/)
*   [My growing list of roam/render examples in the Roam help database](https://roamresearch.com/#/app/Zsolt-Blog/page/ZaQLABByl)
*   [Clojurescript Interop With Javascript](https://lwhorton.github.io/2018/10/20/clojurescript-interop-with-javascript.html#property-access)
*   [Getting Work Done in Clojure: The Building Blocks](https://medium.com/@daniel.oliver.king/getting-work-done-in-clojure-the-building-blocks-39ad82796926)
*   [HTML2Hiccup - An HTML to Hiccup converter for Clojure and ClojureScript](http://html2hiccup.buttercloud.com/)
*   Plus some of my own components as I continue to deep dive into roam/render. Be extra careful with these. Play with them in a test/development graph. While I believe these examples should work without damaging your database, I take no responsibility if something goes wrong...

*   [A configurable roam/render form with a javascript hook](https://roamresearch.com/#/app/Zsolt-Blog/page/LXWC4CgKj)
*   [An alternative table component](https://roamresearch.com/#/app/Zsolt-Blog/page/HSJjyWEZJ)
*   [A query component for free text sibling query](https://www.zsolt.blog/2021/02/((npT41MApz)))
*   Feel free to browse around in my graph, it is full of successful and unsuccessful attempts at understanding Clojure and reagent. I do my best to tag solutions that worked/did not work.

Calling components
------------------

The `{{roam/render: ((block-UID))}` form of referencing components is not very user-friendly. It takes a couple of clicks to get the block-uid and insert it into the render block. I have found two alternatives.

1.  You can create a simple native roam template in `[[roam/templates]]` and add your component there with a friendly name. Then, when you need it, you can insert the component into your document using  `;;template-name`.
2.  You can also hack block-UIDs. I provide a solution [here](https://twitter.com/zsviczian/status/1365606725924044808?s=20). This will allow you to create components with more user-friendly names, like my query component: `{{roam/render: ((str_query))}}`.

Forward declaration
-------------------

This one has caused me hours of headaches. Clojure does a single-pass compilation. This means that if a function is called before it is declared the compiler will throw an error. There are certain situations when it is not possible to declare functions in the sequence of their use, e.g. in an iteration involving two functions. This is when [clojure.core/declare](https://clojuredocs.org/clojure.core/declare)  is valuable. You can make a forward declaration, and the compiler will know to expect a function definition later on.

Output Roam native links
------------------------

If you are building components, you will quickly reach a point when you want to output Roam native links. Links you can click and the page opens. Links you can shift-click and they open in the sidebar. I have found three ways to achieve an output with working links:

*   The simplest solution is creating a block using roamAlphaAPI and placing a `[[page link]]` or `((block-ref))` in the block string. This will convert into a proper Roam link.
*   Another approach involves creating a block containing roam query, and including the links you want to show in the query parameter: `{{[[query]]: {or: [[page link]] ((block-ref))}}`.
*   Since Roam has released the roam.right-sidebar namespace a few days ago, it is now possible to fully mimic Roam native links. I haven't yet had the time to experiment with this third option, but it looks feasible. If you are interested in this, you may want to look around in my [graph](https://roamresearch.com/#/app/Zsolt-Blog/page/BLY329mUQ), maybe since the time of writing this, I have implemented the solution.  I will create a reagent component that responds to click events. On-click I will navigate the browser to a link crafted similar to the javascript example below. On shift-click, I will use roam.right-sidebar to open the link. 

```
function getRoamLink(uid) {
  if (window.location.href.includes('/page/')) 
    return window.location.href.substring(window.location.origin.length+1,window.location.href.length-9)+uid;
  else
    return window.location.href.substring(window.location.origin.length+1,window.location.href.length)+'/page/'+uid
}
```

Debugging
---------

I use the following code for debugging. By turning the silent switch to true, I can disable console.log debug messages. Probably no need to say, the input arguments are just for the sake of this example.

[![Debug demo](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhVRbQc2lMeETr2ZIC80TT39_4-w5BYr7bQhhF5WfWeUi8QIHVq-NeWnUnxVRIYMJ9FdK95-t-d25_wEFJE0Q02W97aoyaLS5CyMP3EtwALRnhL33gTiS8cz0lQtlQ4pAEqc2HBIpq9IRE2/w640-h210/debug-demo.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhVRbQc2lMeETr2ZIC80TT39_4-w5BYr7bQhhF5WfWeUi8QIHVq-NeWnUnxVRIYMJ9FdK95-t-d25_wEFJE0Q02W97aoyaLS5CyMP3EtwALRnhL33gTiS8cz0lQtlQ4pAEqc2HBIpq9IRE2/s1228/debug-demo.jpg)

[![console.log](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi7fBWmDk2rwDji-xi1NqhfCFZS0LvAbrnQ3bBdFO0Z-gCU_NgnbocYKWQxiaZVimxYcSzkp6-U63phsWqpsoLietagaXZuUlQWI_spml8x5rHlHxnRmXIjZlBP3OxClhFo2Lkc4TB_gRB7/w640-h252/console.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi7fBWmDk2rwDji-xi1NqhfCFZS0LvAbrnQ3bBdFO0Z-gCU_NgnbocYKWQxiaZVimxYcSzkp6-U63phsWqpsoLietagaXZuUlQWI_spml8x5rHlHxnRmXIjZlBP3OxClhFo2Lkc4TB_gRB7/s813/console.jpg)

  
```
(ns blogpost.9)

(def silent false)
(defn debug [x] 
  (if-not silent (apply (.-log js/console) x)))

(defn main [{:keys [block-uid]} & args]
  (debug ["(main)  block-uid: " block-uid "  args: " args])
  [:div "debug demo"])
```

Limitations
-----------

Considering that Roam is a note-taking application, the ClojureScript environment is astonishing. However, like with the Swiss Army knife, which has a miniature saw, in some situations, it comes in handy, but if you want to cut down a tree, get a chainsaw. 

There are two huge limitations with roam/render. It is not documented, and more importantly, it lacks proper debugging tools. It does output some error messages to console log, and with consistent debug messages it is possible to track down errors, but it sorely lacks basic debug features like breakpoints, watches, etc. Troubleshooting your ClojureScript will take considerably longer than it should.

It is nice that you can distribute code to sibling blocks as it allows for better commenting by using the blocks in-between to include flowcharts, explanations, etc. a little similar to a Jupiter Notebook in Python. But, you cannot group frequent functions into a shared namespace for reuse between components. I tried block referencing a shared namespace as a sibling block to a component, but that did not work. A workaround is to put all your source code for all your components on the same page and into the same namespace. This will work, but I fear that it will become messy if you have lots of components. It will also make it very hard to share components with others.

Datascript has a number of limitations. You cannot create your own [transformation functions](http://www.learndatalogtoday.org/chapter/6). You cannot create custom entities in the Roam database. Some of the clojure namespaces and functions are missing. e.g. [clojure.string/lower-case](https://clojuredocs.org/clojure.string/lower-case) does not work. I wanted to use lower-case to support case insensitive search, luckily in the concrete case there is a workaround with [clojure.core/re-find](https://clojuredocs.org/clojure.core/re-find) with [clojure.core/re-pattern](https://clojuredocs.org/clojure.core/re-pattern) and the (?i) flag.

Final verdict
-------------

It is absolutely cool to have a Clojure execution environment in a note-taking application. However,  without debug tools and documentation, it is very inefficient to develop components. 

Roam/render and ClojureScript are great for rendering custom output, like forms, pivot tables, and other interactive tools.

Given my current level of knowledge, javascript interoperability seems to be the best way to go. You can use roam/render for what the name suggests, to render output, and you can build application logic in javascript. By doing this you get the best of two worlds. You get the ease of rendering reactive components in Reagent, and you get to do most of your development work in a proper development environment with adequate debug tools. In addition, you can work with a language that is already familiar to you (assuming you are coming with preexisting JS skills), and you can create reusable code in javascript, that is easy to share with others.