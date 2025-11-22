- [[January 12th, 2022]]
    - [[Loom video]]: {{[[video]]: https://www.loom.com/share/89387c67013844138d4bd6e39832515b}}
        - AI Whisper Transcript::
            - Okay, this is another loom about data log block query. I was working at this a bit more today and I realized that it needed some things which didn't have currently. So I've added those things. The first is the refresh button and that is just because the queries that's there right now are not reactive. The, I mean like these queries are reactive, like this kind, but the data log block queries are not reactive and the reason for that is because like data queries are like less performant than these queries which we have finetuned. And like these queries by enough if you have loss of them like in this open in the pages can slow down room while typing. So they didn't want this to like have more of an effect in that respect. So I've made it non-reactive but with a refresh button and the refresh button looks like this. The next thing would be we've added the capability for rules and what rules are rules are the means of abstraction in data logs like you can think of them as functions in other languages and they give us facilities like representing like a block of these like query blocks with a single name or something. That would be the abstraction thing. Similarly, we can like have logical ors. So this would be two either of this or this is two. So it lets us write logical or queries whereas whatever inside where is by default and we can write or using these and look at this example. Like we can even do like recursion using rules where we can see if the new rule and this is same then this would be recursion. Yep, so that is where rules are necessary and this is the like pattern for rules. So a tuple of rules and every rule would begin with this is the definition of the rule. I think it's called the head of the rule and then others would be the as usual. Okay, the way you specify rules is similar. So the thing about rules is that in the in we have to keep percentage. So if you see it in any of these you can see that when rules are passed we have to pass this and this would take it as rules. Similarly for taking it in from room we have to have a syntax which makes it clear that these are rules, right? So similar to the UID stuff, the int and float stuff we are using an attribute for this as well. So the attribute would be something like rules and then you can write the rules here. Problem with writing rules directly is this here most of the time when writing rules unless you have a space or a white space here the issue is that everyone would think of this as a feed and feed a feed. So what you can do in rules is you can like make a code block and you can type the rules here and it will work. And you can keep a JavaScript or you can change it to closure whatever you want. If you change it to closure you'll probably get syntax or you can just type it in on the keyboard and stuff like that. So yeah, that's how rules work. For queries and this is, and I'll show an example in a bit but I'll talk about queries first. So say I have a query, right? So I have a query for example which would end Andy and like go. And I have a query. So the use case for queries is and for being able to use queries as input for data log block query is that you might want to do something to the results of this. So one example would be you might want to filter this based on your own criteria. So you could take the output of this as an input and then you could filter it using the data log. And I'll show an example but like before that I'll show how we use it. So we actually don't use the whole thing because we actually don't want the UI. So what we want is this bit and we specify it as an argument in such a manner. And as I am talking about arguments like this needs type testing to make sure that room knows that this is a query and it should convert it to UID. So it would be query to UIDs. So just to be clear what this does is this treats the thing after it as a query and then that creates the query. It returns all of the UIDs, this, this and this would be the three UIDs that this returns. Okay, so let's look at an example. So in this example, and this is one of the examples of the UCS I was talking about earlier. So here what we have is we have a query of Andy and Michael and what we want to do is, and like this particular example will be of like useful for example multi-user graphs. I want to look at the output of this query but I want to filter it by only the blocks that were created by me. So this is what that does. So this filters the output of this query to be only the blocks that I have created. It doesn't make much sense in this graph because like this graph was, I'm the only person who writes in this graph but like you can see this of being used in a multi-user graph where you might not be interested in, where you might be interested in what a particular user has linked these two. So it could be mine or like if I wanted to see if what person A has linked these two with, if any, then I can look at that by using this. And in this example, you can see that I'm using a rule as well and this rule checks if a block was created by a user with a given name. This is like golden candidate for rule because this is kind of an implementation detail. So the way this query actually works is it takes the name of the user, so for a best of and what it does is it finds the user's page. So every user has a page in room now since a few months ago. And from the title, we get the user's page and from the user's page, we get the user and we check if the block was created by the user. And that is how this works. And that is the rule. Here's another example of a rule and which in this case, this does oring. And I'm going to watch into that. But yeah, that is how this works. These are two different rules and we're only currently using the above rule. So it makes clear how we use rules and query ideas. The next step is writing detail of queries here. I've added a cavity for it to be multi-line here, but I'm not sure if that will be in the final version because like, yeah, that is an irrelevant thing here, but it would probably be, but I'm not sure about that. But actually, this is not what we want. Like we want something similar to this. We want to have a code block where we'll have syntax highlighting, where we'll have bracket like polarization depending upon where we are and stuff like that. And for that, then the thing you want to do is, okay, so let's make this thing right. Okay, what you want is you want the catalog block query and like users of room render will recognize this trick. So you want to create a block inside here. You want this to be a block and keep it here. And then, okay, so, and this is how you would do that. So, the arguments you'd have as the children of the query block, and then you could add this block anywhere, right? Let's see here. Okay, so if you look at, let's actually close this one, this one, this one, if you look at this, we can see that data log block query now also takes a block ref as an input. And it assumes that the block ref contains the query and the children of the block ref contain the argument. So if you were to go to this block ref, this contains the query and the children contain the arguments. And in this way, you can have all of the benefits of a code editor while you are writing the queries. But you can also see the query here. Any of you, yeah, I think like for longer queries, you would not want to write it like this. You would not want to write it like, you would not want to write it like this anyways. You would want to write it inside a code editor. So for short, like what I suggest is for shorter queries, you might want to write it like this, but for longer queries, you would want to write it inside an editor and use something like this. And this is pretty similar to what Romander uses. That's it. Let's think about if I missed anything. (audience chattering) This is also the same example. There's nothing, you can still have it. You can still have the query block right under this. But you don't have to have it. And if you have it like this, then it is easier to edit. It is easier to do stuff. So that makes sense. These are the additions. Let me know what you think about it.
    - The new things
        - refresh button
            - Just to be clear, the thing refreshes automatically during loading of the component. you only need to refresh if you keep it open (in the sidebar or something) and want to rerun the query again
            - not made completely reactive since datascript queries are less performant than `{{queries}}` (which we've tuned)
        - rules
            - how they look like in the query
                - :in $ %
            - How rules are specified as arguments
                - rules:: 
```clojure
[[(rule-a ?a ?b)
  ...]
 [(rule-b ?a ?b)
  ...]
 ...]```
        - `{{queries}}` as input
            - {{[[query]]: {and: [[Adam Krivka]] [[Roam Alpha API]]}}}
            - How the above query would be specified as argument
                - query->uids:: {and: [[Adam Krivka]] [[Roam Alpha API]]}
        - block refs as query
            - same pattern as roam/render
    - Base query
        - {{[[query]]: {and: [[Adam Krivka]] [[Roam Alpha API]]}}}
    - Test data
        - ###  [[Adam Krivka]] has written a bunch of stuff you can consult for [[Roam Alpha API]]
    - rules + `{{queries}}` as input
        - Here we're using datalog to filter the outputs of the Base query
        - {{datalog-block-query: 
[:find [?uid ...]
 :in $ % [?uid ...]
 :where
 [?block :block/uid ?uid]
 (created-by ?block "Baibhav Bista")]
}}
            - rules::
```clojure
[
 ;; rule that checks if a block was created by user with given name
 [(created-by ?block ?user-page-title)
  [?user-page :node/title ?user-page-title]
  [?user :user/display-page ?user-page]
  [?block :create/user ?user]]
 
 ;; an example of an OR using rules
 [(created-by-either ?block ?user-page-title-1 ?user-page-title-2)
  (created-by ?block ?user-page-title-1)]
 [(created-by-either ?block ?user-page-title-1 ?user-page-title-2)
  (created-by ?block ?user-page-title-2)]
]```
            - query->uids:: {and: [[Adam Krivka]] [[Roam Alpha API]]}
    - block refs as query
        - Here we're using a pattern similar to [[roam/render]] i.e. passing a block ref as argument to datalog-block-query
            - the arguments will then be the children of the block being reffed 
        - {{datalog-block-query: ((2fd1vthv9))}}
        - the block being reffed
            - ```javascript
[:find [?uid ...]
 :in $ % [?uid ...]
 :where
 [?block :block/uid ?uid]
 (created-by ?block "Baibhav Bista")]```
                - rules::
```clojure
[[(created-by ?block ?user-page-title)
  [?user-page :node/title ?user-page-title]
  [?user :user/display-page ?user-page]
  [?block :create/user ?user]]
 [(created-by-either ?block ?user-page-title-1 ?user-page-title-2)
  (created-by ?block ?user-page-title-1)]
 [(created-by-either ?block ?user-page-title-1 ?user-page-title-2)
  (created-by ?block ?user-page-title-2)]]```
                - query->uids:: {and: [[Roam Alpha API]]}
                - result-transform::
```clojure
(fn [block-ents]
  (->>
    block-ents
    (group-by #(count (:block/_refs %)))))```
- testing a complicated example for [[datalog-block-query]]
    - TODOs
        - {{[[TODO]]}} have to make it support js/Date
        - {{[[TODO]]}} have to show both results and groups in the count at the top
    - Datalog block query which shows pages where the two last backrefs are more than 1 week apart
        - the actual running query 
            - {{[[datalog-block-query]]: ((dLg6R4GyR))}}
        - the query "code" (datalog query just gets the page uids, the heavy lifting is done by the result-transform)
            - ```javascript
[:find [?uid ...]
 :where
 [?page :block/uid ?uid]
 [?page :node/title ?page-title]]```
                - result-transform::
```clojure
;; FIXME: could not figure out how to get the current timestamp i.e. (js/Date.now) is not working
;;   After figuring that out, we need to first check for pages which have new backrefs in say the last week, then when it comes to comparing with old refs, get the most recent backref not in the last week
;;   or something like the above
;;   For now, ordering them in reverse chronological order
;; FIXME: unsure if we want to use :edit/date :create/date or others
;; FIXME: CONSTANTS that can be adjusted at the top

(fn [page-ents]
  (let [page-ent->title+most-recent-two-back-refs
        (fn [page-ent]
          (let [most-recent-two (->> page-ent
                                     :block/_refs
                                     (sort-by :edit/time >)
                                     (take 2))
                [a b] most-recent-two
                referenced-after-days (quot (- (:edit/time a) (:edit/time b))
                                         (* 24 60 60 1000)) ]
            
            (when (and
                   ;; has at least 2 reference
                   a
                   b
                   ;; at least 1 week difference between the two references
                   (<= 7 referenced-after-days)
                   )
              [(str "'" (:node/title page-ent) "' (referenced after " referenced-after-days " days"  ")" )
               most-recent-two])))]
    (->> page-ents
         (map page-ent->title+most-recent-two-back-refs)
         (remove nil?)
         (sort-by (comp :edit/time first second) >)
         (into []))))```
- Example of [[datalog-block-query]] with two inputs
    - test [[test page A]] `[[test page B]]`
    - Datalog block query which takes two pages as inputs and finds a block which refs both of them
        - Source
            - ```javascript
[:find [?uid ...]
 :in $ ?p1-uid ?p2-uid
 :where
 [?p1 :block/uid ?p1-uid]
 [?p2 :block/uid ?p2-uid]
 [?block :block/refs ?p1]
 [?block :block/refs ?p2]
 [?block :block/uid ?uid]]```
                - uid:: [[test page A]]
                - uid:: [[test page B]]
        - The query in action
            - {{[[datalog-block-query]]: ((U8RXsGWm0))}}
