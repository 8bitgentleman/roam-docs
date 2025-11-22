- ##  Introduction to **:q** Queries
    - The `:q` feature in Roam allows you to display results of datomic/datascript queries directly in your graph. This powerful tool lets you flexibly query any condition in your graph and display the results either inline or as tables.
    - ### What are **:q** Queries?
        - `:q` queries use Datalog, a declarative logic programming language that's the query language for Datomic and DataScript. With these queries, you can extract specific information from your Roam graph based on complex conditions.
    - ### Key Resources
        - **Tip:** If your variable name contains "uid", then it will be displayed as a block view or page view in the results table.
        - [Datomic Query Reference](https://docs.datomic.com/query/query-data-reference.html)
        - [Roam-flavored Datascript Queries](https://www.zsolt.blog/2021/01/Roam-Data-Structure-Query.html) - A useful blog article on getting started
        - {{[[video]]: https://www.loom.com/share/b384798d37904947afefe9059c71035e}}
- ## Getting Started with **:q**
    - ### Simple Examples
        - **Count the number of pages in your graph:**
            - :q "Number of pages in the graph" [:find (count ?page) . :where [?page :node/title _]]
        - **Count the number of TODOs in your graph:**
            - :q "Number of TODOs in the graph:" [:find (count ?b) . :where [?todo-page :node/title "TODO"] [?b :block/refs ?todo-page]]
        - **Show 5 random pages:**
            - :q "5 random pages in the graph" [:find (sample 5 ?class_title) . :where [?a_class_page :node/title ?class_title] [?a_class_page :block/uid ?class_page_uid]]
- ## Query Types & Output Formats
    - ### Single Value Queries
        - Single value queries return a single result, which is shown inline. These are typically count queries or aggregation functions.
        - **Count of code blocks:**
            - :q 
                        "Number of code blocks in the graph" 
                        [:find (count ?b) . 
                             :where [?b :block/string ?s]
                                          [(clojure.string/starts-with? ?s "
              ```")]]
    - ### Table Outputs
        - When your query returns multiple "columns" of data, the results are displayed as a table.
            - Technically, we show as table [when the find spec denotes a relation](https://docs.datomic.com/query/query-data-reference.html#find-specs). This will look something like **:find ?a ?b** in the query
        - **Pages with 'API' in the title and their first blocks:**
            - :q "All Roam pages with 'API' in their title, their first blocks"
                        [:find ?page-title ?page-uid ?first-block-uid
                         :where 
                        [?page :node/title ?page-title]
                        [?page :block/uid ?page-uid]
                        [(clojure.string/includes? ?page-title "API")]
                        [?page :block/children ?block]
                        [?block :block/order 0]
                        [?block :block/uid ?first-block-uid]]
        - **Recently created pages with 'API' in the title:**
            - :q "All Roam pages with 'API' in their title, sorted so that recently created at top"
                        [:find ?page-uid ?create-time
                         :where 
                        [?page :node/title ?page-title]
                        [?page :block/uid ?page-uid]
                        [?page :create/time ?create-time]
                        [(clojure.string/includes? ?page-title "API")]]
- ## Advanced Features & Quality of Life Improvements
    - ### Nestable Results
        - You can nest results under parent blocks/pages for columns containing blocks or pages to create a more hierarchical view.
        - ![Nestable Results Demo](https://firebasestorage.googleapis.com/v0/b/firescript-577a2.appspot.com/o/imgs%2Fapp%2Fhelp%2FW_4ykkTS4q.gif?alt=media&token=725810da-fb46-4240-a8d9-707e1cb648e5)
        - The system automatically detects which attributes in the find spec are Roam entities, so you don't need to return UIDs and include "uid" in the name. For example, to show all pages in the graph:
        - ```plain text
          :q [:find ?p :where [?p :node/title]]
          ```
    - ### Column Transformations
        - You can transform columns containing strings to modify how they're displayed or processed.
            - ![Column Transformations Demo](https://firebasestorage.googleapis.com/v0/b/firescript-577a2.appspot.com/o/imgs%2Fapp%2Fhelp%2FGa-vTwnAYN.gif?alt=media&token=11214de0-2b55-4821-940a-76efc9d1fe53)
        - Available transformations:
            - **to number** - Extract a number from a block for sorting
            - **remove attribute **- Remove attribute information
        - **Example: Getting children of a block and parsing out numbers**
            - :q [:find ?e ?s
                 :where
                 [?parent :block/uid "mKwNJQ3jb"]
                 [?parent :block/children ?e]
                 [?e :block/string ?s]]
    - ### Date Operations
        - Roam provides special symbols to represent dates, which you can use directly in the **:where** clause.
        - **Get current page and backref count:**
            - :q [:find ?p (count ?b)
                  :where
                  [?p :node/title current/page-title]
                  [?b :block/refs ?p]]
        - **Get quote page additions in the current month:**
            - :q [:find ?b ?t
                  :where
                  [?changelog-p :node/title "quotes"]
                  [?b :block/page ?changelog-p]
                  [?b :create/time ?t]
                  [(<= ms/this-month-start ?t ms/this-month-end)]]
    - ### Date Symbols Reference
        - **__current__ - Location specifiers:**
- ## Example Gallery
    - ### Page Analysis Examples
        - **All Readwise Highlight Pages, sorted by creation time:**
        - ```plain text
          :q "All Readwise Highlight Pages, sorted so that recently created at top"
          [:find ?page-uid ?first-child-uid ?page-create-time
          :where 
          [?page :node/title ?page-title]
          ;; hack: readwise default template all page titles contain "(highlights)" string
          [(clojure.string/includes? ?page-title "(highlights)")]
          [?page :block/uid ?page-uid]
          [?page :create/time ?page-create-time]
          [?page :block/children ?b]
          [?b :block/order 0]
          [?b :block/uid ?first-child-uid]]
          ```
    - ### Time-Based Queries
        - **Get additions to changelog in current calendar month:**
        - ```plain text
          :q [:find ?b ?t
          ```
            - :where
            - [?changelog-p :node/title "Change Log"]
            - [?b :block/page ?changelog-p]
            - [?b :create/time ?t]
            - [(<= ms/this-month-start ?t ms/this-month-end)]]
        - ```plain text
          
          ### User-Based Queries
          
          **Get backrefs of a page created by a specific user in the current year:**
          ```
        - :q [:find ?b ?t
            - :where
            - [?b :block/uid ?uid]
            - (refs-page "Quality of Life Improvements" ?b)
            - (created-by "Baibhav Bista" ?b)
            - [?b :create/time ?t]
            - (created-between ms/this-year-start ms/this-year-end ?b)]
        - ```plain text
          ```
- ## References
    - ## Special Symbols
        - ### Current Location Specifiers
            - {{[[table]]}}
                - Symbol
                    - Description
                - `current/block-id`
                    - :db/id for the block with the `:q` query
                - `current/page-id`
                    - :db/id for the page whose child block has the `:q` query
                - `current/page-title`
                    - Title of the page where the `:q` exists
                - `current/block-uid`
                    - :block/uid for the block with the `:q` query
                - `current/page-uid`
                    - :block/uid for the page whose child block has the `:q` query
        - ### Time in Milliseconds
            - {{[[table]]}}
                - Symbol
                    - Description
                - `ms/today-start`
                    - Equivalent to `:ms/+0D-start`
                - `ms/today-end`
                    - Equivalent to `:ms/+0D-end`
                - `ms/this-week-start`
                    - Equivalent to `:ms/+0W-start`
                - `ms/this-week-end`
                    - Equivalent to `:ms/+0W-end`
                - `ms/last-week-start`
                    - Equivalent to `:ms/-1W-start`
                - `ms/last-week-end`
                    - Equivalent to `:ms/-1W-end`
                - `ms/next-week-start`
                    - Equivalent to `:ms/+1W-start`
                - `ms/next-week-end`
                    - Equivalent to `:ms/+1W-end`
                - `ms/this-month-start`
                    - Start of current month
                - `ms/this-month-end`
                    - End of current month
                - `ms/this-year-start`
                    - Start of current year
                - `ms/this-year-end`
                    - End of current year
                - `ms/+1D-end`
                    - End of tomorrow
                - `ms/-5D-start`
                    - Start of 5 days ago
                - `ms/+1W-end`
                    - End of next week (not equal to `ms/+7D-end`)
                - `ms/+1M-start`
                    - Next month's start (might be 31 days later or even 1 day later)
                - `ms/+0Y-start`
                    - First millisecond of Jan 1 for this year (not equal to `ms/+365D-start`)
                - `ms/+0Y-end`
                    - Last millisecond of Dec 31 for this year
                - `ms/=2025-01-01-start`
                    - Start of specific date
                - `ms/=2025-12-31-end`
                    - End of specific date
        - ### Daily Note Page Title References
            - {{[[table]]}}
                - Symbol
                    - Description
                - `dnp/today`
                    - Today's daily note page title
                - `dnp/yesterday`
                    - Yesterday's daily note page title
                - `dnp/tomorrow`
                    - Tomorrow's daily note page title
                - `dnp/-1D`
                    - Daily note page title from 1 day ago
                - `dnp/+1D`
                    - Daily note page title for 1 day from now
                - `dnp/this-week-start`
                    - Daily note page title for start of current week
                - `dnp/this-week-end`
                    - Daily note page title for end of current week
                - `dnp/this-month-start`
                    - Daily note page title for start of current month
                - `dnp/this-year-end`
                    - Daily note page title for end of current year
                - `dnp/=2025-01-01`
                    - Resolves to "January 1st, 2025"
    - ## Rules
        - {{[[table]]}}
            - Rule
                - Description
            - `(created-by ?user-name ?block)`
                - Find blocks created by a specific user
            - `(edited-by ?user-name ?block)`
                - Find blocks edited by a specific user
            - `(by ?user-name ?block)`
                - Find blocks created or edited by a specific user
            - `(refs-page ?page-title ?b)`
                - Find blocks referencing a specific page
            - `(block-or-parent-refs-page ?page-title ?b)`
                - Find blocks or their parents referencing a specific page
            - `(created-between ?t1 ?t2 ?b)`
                - Find blocks created between two timestamps
            - `(edited-between ?t1 ?t2 ?b)`
                - Find blocks edited between two timestamps
- ## Best Practices & Tips
    - 1. **Naming variables:** If your variable name contains "uid", it will be displayed as a block view or page view in the results table.
    - 2. **Performance considerations:** Complex queries with many constraints may take longer to execute. Start with simpler queries and add constraints incrementally.
    - 3. **Date handling:** Use the built-in date symbols for time-based queries instead of calculating timestamps manually.
    - 4. **Sorting results:** You can sort the table results by clicking on column headers after the query executes.
    - 5. **Hiding columns:** You can hide columns used solely for sorting by using the UI options.
    - 6. **Query organization:** Place frequently used queries in a dedicated page for easy access.
    - 
- ## Video Demonstrations
    - Introduction to :q Queries
        - https://www.loom.com/share/b384798d37904947afefe9059c71035e
    - Quality of Life Improvements Demo
        - https://www.loom.com/share/a3e5f8126d924b3eb95c7c11209eceee
- {{roam/css}}
    - ```css
      .rm-heading-level-2 .rm-bullet__inner--numbered{
        opacity: .85;
        font-weight: 500;
        font-size: 1.4em;
      }
      .rm-heading-level-2  span:has(> .rm-bullet__inner--numbered:first-of-type) {
          margin-top: .3em;
      }
      ```
