BDD and Cucumber
================


In this homework you will create user stories to describe a feature of a SaaS app, use the Cucumber tool to turn those stories into executable acceptance tests, and run the tests against your SaaS app.  

Specifically, you will write Cucumber scenarios that test the happy
paths of parts 1-3 of the Rails Intro homework, in which you added
filtering and sorting to RottenPotatoes' `index` view for Movies.

The app code in `hw-bdd-cucumber/rottenpotatoes` contains a "canonical" solution to the
Rails Intro homework against which to write your scenarios, and the
necessary scaffolding for the first couple of scenarios. 

You may fork your own copy, or just clone this repo directly. If you clone it directly, you won't be able to push your code changes back from your IDE. Maybe that's OK with you. If you fork into your own GitHub account first and clone your fork, you can push your changes back to your github account. We recommend
that you do a `git commit` as you get each part working. 

Once you clone the repo, these commands should get you up and running:

```
$ cd hw-bdd-cucumber/rottenpotatoes
$ bundle install --without production
$ bundle exec rake db:migrate
$ bundle exec rake db:seed
$ bundle exec rake db:test:prepare
```

Also, make sure Cucumber is working by running the `bundle exec cucumber` command. You can also run individual features by specifying a filename with the cucumber command, such as `bundle excec cucumber features/sort_movie_list.feature`.

You may see deprecation warnings, which are shown below. While ugly, you can ignore these for the assignment. They just mean that the step definitions are using an old method called .should, and need to be updated to use .expect eventually.

```
DEPRECATION: Using `should` from rspec-expectations' old `:should` 
syntax without explicitly enabling the syntax is deprecated. Use 
the new `:expect` syntax or explicitly enable `:should` with 
`config.expect_with(:rspec) { |c| c.syntax = :should }` instead. 
Called from /home/ubuntu/workspace/hw-bdd-cucumber/rottenpotatoes
/features/step_definitions/web_steps.rb:107:in `block in <top 
(required)>'.
```


**Part 1: Create a declarative scenario step for adding movies**

The goal of BDD is to express behavioral tasks rather than low-level operations.  We keep the scenarios declarative with solid, state-describing steps and accopmlish the low-level operations behind the scenes with step definitions. In addition to the lowest-level step definitions found in web_steps.rb (the ones that facilitate clicking a button or filling in a text box), we will supply movie-specific step definitions in a separate movie_steps.rb file. These movie step definitions serve as a middle layer of abstraction between the declarative scenario steps and the web step definitions.

The background step of all the scenarios in this homework requires that
the movies database contain some movies.  Analogous to the explanation
in Section 4.7, it would go against the goal of BDD to do this by
writing scenarios that spell out every interaction required to add a new
movie, since adding new movies is not what these scenarios are about. 

Recall that the `Given` steps of a user story specify the initial state
of the system: it does not matter how the system got into that state.
For part 1, therefore, you will create a step definition that will match
the step `Given the following movies exist` in the `Background` section
of both `sort_movie_list.feature` and `filter_movie_list.feature`.
(Later in the course, we will show how to DRY out the repeated
`Background` sections in the two feature files.) 

Add your code in the `movie_steps.rb` step definition file.  You can
just use ActiveRecord calls to directly add movies to the database; it`s
OK to bypass the GUI associated with creating new movies, since that's
not what these scenarios are testing. 

SUCCESS is when all Background steps for the scenarios in
`filter_movie_list.feature` and `sort_movie_list.feature` are passing
Green. 

**Part 2: Happy paths for filtering movies**

1. Complete the scenario `restrict to movies with `PG` or `R` ratings` in `filter_movie_list.feature`. You can use existing step definitions in `web_steps.rb` to check and uncheck the appropriate boxes, submit the form, and check whether the correct movies appear (and just as importantly, movies with unselected ratings do not appear). But to make the solution more declarative, consider tips A, B, and C below.

A. Since it's tedious to repeat steps such as When I check the 'PG' checkbox, And I check the 'R' checkbox, etc., create a movie_steps.rb step definition to match a step such as:
`When I check the following ratings: G PG R` or `When I uncheck the following ratings: PG-13 R`
This single step definition should only check the specified boxes, and
leave the other boxes as they were. HINT: this step definition can reuse
existing steps in  `web_steps.rb` , as shown in the example in Section
7.9 in ESaaS.

B. COnsider adding a movie_steps.rb step definition for seeing a list of specific movies in the same form that you list a series of checkboxes to check in tip A above. For instance: `Then I should see the following movies: ` or `Then I should not see the following movies:`. You can use the string split method to parse the list of movies just like you did the ratings, and you can feed the titles into the web step `I should see` or `I should not see`. See the definitions of the web steps for ways to make one step definition handle both cases.

C. For the scenario `all ratings selected`, it would be tedious to use `And I should see` to name every single movie. That would detract from the goal of BDD to convey the behavioral intent of the user story. To fix this, create step definitions that will match steps of the form: 
`Then I should see all of the movies` in `movie_steps.rb`. 
HINT: One way to tackle this is to retrieve the list of all movies from the model, map their titles into an iterable collection, and delegate to the web step `I should see` with each title. Another (slicker) option is to consider counting the number of rows in the table to implement these steps. If you have computed rows as the number of table rows, you can use the assertion `rows.should == value` to fail the test in case the values don't match.
Update: You no longer need to implement the scenario for no ratings selected.

2. Use your new step definitions to complete the scenario `all ratings selected`. SUCCESS is when all scenarios in `filter_movie_list.feature` pass with all steps green.

**Part 3: Happy paths for sorting movies by title and by release date**

1. Since the scenarios in `sort_movie_list.feature` involve sorting, you will need the ability to have steps that test whether one movie appears before another in the output listing. Create a movie_steps.rb step definition that matches a step such as 
`Then I should see "Aladdin" before "Amelie"`. There is a hint in the slides for checking the relative order of strings using a regexp.

2. Once you finish the step definition, complete the scenarios in `sort_movie_list.feature` to check for movies sorted by date and by title. Use your new step definition.

### HINTS

  * `page` is the Capybara method that returns an object representing
  the page returned by the app server.  You can use it in expectations
  such as `expect(page).to have_content('Hello World')`.  More
  importantly, you can search the page for specific elements matching
  CSS selectors or XPath expressions; see the [Capybara
  documentation](https://github.com/jnicklas/capybara) under **Querying**.
  * `page.body` is the page's HTML body as one giant string.  
  * A regular expression could capture whether one string appears before
  another in a larger string, though that's not the only possible
  strategy. 

**SUCCESS** is all steps of all scenarios in both feature files passing Green.

**Submission**

To submit your assignment, follow instructions from your professor specific to your class. Your submission should reflect all changes you made to the .feature files and movie_steps.rb, and any other changed files, and may include verification of your cucumber output.
