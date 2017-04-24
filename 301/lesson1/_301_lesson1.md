# 1	Introduction to Lesson 1


# 2	Lesson: Lecture 1

### Worldwind tour of a Rails app and basic scaffolding.

How Rails MVC handles processing a request and issuing a response.
  ![rails mvc](./request_response_mvc.jpg)

- bunlder, create sandbox per gemfile/project. 
  // bundle exec rake db:migrate ->  the specific version that your project depends on
  // rake db:migrate ->  the system level version
  // gemfile.lock

- files the rails new created
  - config/dababase.yml default is sqlite3 for dev, postgram for production
  - gemfile -> project file, whate gems I need
  - gemfile.loc -> generated when run bundle install
  - **routes.rb** -> routing 

- folder
  - app
    - asset (static asset, css,js,images,...)
    - **controller**
    - mailers -> not needed, but need a email provider
    - **models** -> 
    - **views** -> erb templates
  - bin -> where executabe sits, we never touch it
  - config
    - environments - first class in rails - override the appliation.rb
    - initializers -> be init before everything else
    - locales -> in yml files
  - boot.rb -> never touch, rails to boot itself
  - environment.rb -> 
  - db
    - migrate
  - lib 
    - asset > static asset
    - task
  - log
  - public
    - generic error pages,  static asset
    - robots.txt -> for search engine
  - test
  - tmp, cache,...
  - vendor > not really need, use bundler
  - config.ru x
  - rakefile x

**generator is not for experenced developers, build yourself**

`rake routes` to show all routes or `http://localhost:3000/rails/info/routes`

    ```
    D:\DEVEL\GitHub\notes-ls\301\pre\myapp>rake routes
      Prefix Verb   URI Pattern               Controller#Action
        posts GET    /posts(.:format)          posts#index
              POST   /posts(.:format)          posts#create
    new_post GET    /posts/new(.:format)      posts#new
    edit_post GET    /posts/:id/edit(.:format) posts#edit
        post GET    /posts/:id(.:format)      posts#show
              PATCH  /posts/:id(.:format)      posts#update
              PUT    /posts/:id(.:format)      posts#update
              DELETE /posts/:id(.:format)      posts#destroy  
  ```

controller/action -> render the view (name match)


### ActiveRecord 1:M

Active Record Pattern: how to abstract from database table into code. Object Relational Mapping (ORM). Rails impl the ORM in ruby

*OO -> SQL*

**foreign key** is always in the many side, occurs repeated

in `rails console`, `Post.first` translate into sql statement
```bash
irb(main):001:0> Post.first
  Post Load (0.0ms)  SELECT  "posts".* FROM "posts"  ORDER BY "posts"."id" ASC LIMIT 1
=> #<Post id: 1, title: "This is a post", url: "http://avcavc.com", description: "generated by rails controller, amazing...", created_at: "2017-04-20 23:55:46", updated_at: "2017-04-20 23:55:46">
```

> Exceptions: Save user info may save into different tables in different database, doesn't fit

controller
- plural

model
- singular

view
- controller as folder name
- action as file name

- irb with all active record loaded

> By convention in Rails, the table name should be the lowercase plural of the model name.

association
```ruby
class Post < ActiveRecord::Base
  belongs_to :user
end
class User < ActiveRecord::Base
  has_many :posts
end
```
- in many side: `has_many :posts`
- in 1 side: `belongs_to :user`
- virtual attributes, getter/setters for columns

- in memory object: id is nill
- in database: sql generated, begin/commit transaction, has id


# 3	Diagram: Rails Request/Response


# 4	Diagram: ActiveRecord Pattern


# 5	Diagram: 1:M Association


# 6	Diagram: M:M Association


# 7	Diagram: Entity Relationship Diagram


# 8	Assignment: Download the Project Template


# 9	Solution: System Check


# 10	Solution: Project Template


# 11	Assignment: Post model

1. First, create a migration to modify the database. In this migration, we want to create the table. Rails migrations are the only place where we want to use generators. Issue this command from the terminal within your project directory: `rails generate migration create_posts`

2. Open the newly created migration file in your code editor, and take a look at the migration file. Use the create_table method to create the necessary table and columns. We want: a url and title, which should both be strings and a description which should be text.

3. From the terminal again, issue this command: `rake db:migrate` (you may need to do rake db:create first, if you're using mysql or postgres). If you're getting a rake error, you can try adding `bundle exec` before the rake command.

4. Then take a look at your database and see that you have a posts table, with three columns: url, title and description.

5. Create a Post model file: under app/models directory, create a post.rb file put class Post < ActiveRecord::Base; end

6. Open rails console, and create your first Post object: `Post.create(title: "My first post", description: "I sure hope this works!", url: "www.yahoo.com")`. Verify by looking at your database that this worked.

snake_case for 

CamelCase for Model

# 12	Assignment: User model
# 13	Assignment: 1:M between User and Post
# 14	Assignment: Verify in "rails console"
# 15	Assignment: Comment model
# 16	Assignment: Comment associations
# 17	Assignment: Category model
# 18	Solution: Setting up the models
# 19	Solution: Pushing to Github

use generate only for migration

# 20	Lesson: Lecture 2

### migration

like sql schema, timestamped by generator

increamental, run it in order, no conflicts allowed, e.g.

order matters a lot, tracking in schema_migrations table, don't modify the table by hand


Migrations:
1. create posts table
2. add title column to posts (delete this, error occurs on 3)
3. remove title column to posts
4. create users table  (what if I want rename user to posters)

> never ever modify submitted migration files, prefer to add new migration

`reload!` in rails console reload the changes in data layer

- object to object // the virtual attributes
- id to id // the actualy column name


# 21	Assignment: M:M between Post and Category

habtm vs hmt

Database layer:
- join table

Model layer:

1. `has_and_belongs_to_many` 
- no join model
- implicit join table at db layer
  - `table1_name_table2_name` ex: `groups_users`
- Problems:
  - can't put additional attributes (columns) on association

2. `has_many` :through (**always preferred**)
- has a join model
- can put additional attributes (columns) on association


`tableize`

```
irb(main):021:0> "PostCategory".tableize
=> "post_categories"
```

### resouces and route

in routes.rb, `resources :posts` ~=

**memorise the mapping**

```
  # get   '/posts',          to: 'posts#index'
  # get   '/posts/:id',      to: 'posts#show'

  # get   '/posts/new',       to: 'psots#new'
  # post  '/posts',          to: 'posts#create'
  # get   '/posts/:id/edit', to: 'posts#edit'
  # patch '/posts/:id',      to: 'posts#update'
```

nested URL
  ```ruby
    resources :posts, except: [:destroy] do
      resources :comments, only: [:create]
    end
  ```

  ```
  post_comments_path	POST	/posts/:post_id/comments(.:format)	comments#create
  ```

name route
- build a link back to home page, 
- use name route rather than url which is a hard code string

```erb
# never ever use url
<%= link_to 'go back to all posts', '/posts' %>
# use name route
<%= link_to 'go back to all posts', posts_path %>
```

partial - reuse view
- 


# 22	Assignment: Verify with ERD Diagram


# 23	Solution: M:M Association


# 24	Assignment: Read ActiveRecord Association Methods doc


# 25	Assignment: Play with Associations


# 26	Assignment: Read Routes tutorial


# 27	Assignment: Read Layouts and Rendering


# 28	Assignment: resources :posts


# 29	Assignment: PostsController


# 30	Solution: Post resources


# 31	Assignment (optional): blog entry


# 32	Assignment (optional): change default association name


# 33	Assignment (optional): 1:M or M:M associations in the wild


# 34	Quiz: Lesson 1


# 35	Quiz: Lesson 1 Solution
