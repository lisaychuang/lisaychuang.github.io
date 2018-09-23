---
layout: post
title:      "Building a Wishlist App using Ruby on Rails"
date:       2018-09-23 01:00:47 +0000
permalink:  building_a_wishlist_app_using_ruby_on_rails
---

![](https://cdn-images-1.medium.com/max/1600/1*e-kgzIOXh6oKCHruWuen6A.png)
Welcome to my wishlist app!

A part of building software is learning to “scratch your own itch” — identifying a problem with an existing website, or a feature that you wish it had. I was browsing through [Rifle Paper's online store](https://riflepaperco.com/) and noted that there was no easy way to sort products!


In their online shop, products are organized by categories and you can only view products for each category. I was trying to find some "fillers" to add to my cart to meet the free shipping requirements (order over $50), but there was no easy way to find products under a certain price point (e.g. $7). Within each category, you can "sort" the products. However, there were no options for sorting (e.g. by name, price or size).

So, of course, I decided to build a wishlist app! My app will have the following features:

1. Provide user login with Authentification and Authorization
2. User can view all products on one page
3. User can sort products by name and price
4. User can create, read, update and delete (CRUD) a wish list
5. User can add product(s) to a wishlist

![](https://cdn-images-1.medium.com/max/1600/1*n_K-nrXnmZdf3eVqbiPKmA.png)

**User can sort products and add items to their wishlist**
The app follows the Model–view–controller (MVC) architectural pattern.

To start, I need to define the necessary models/ Ruby classes. Models are the blueprint for individual objects to be created, and each model has a corresponding database table:

* product: stores product information such as name, price, category, image link etc
* category: stores category information such as name, image link etc
* wishlist: stores wishlist information such as name, budget, user notes etc
* user: stores user login information such as name, email, password etc

Validation rules and model association are defined in each model file, here's the code for my `Product` model class:

```
class Product < ActiveRecord::Base
  belongs_to :wishlist
  belongs_to :category

  # Validations
  validates :name, :price, :url, :image_link, presence: true
  validates :url, uniqueness: true
  validates :name, length: {minimum: 2}

  #Scope methods
  scope :free_shipping, -> { where('price >= ?', 50.0)}
  scope :sort_by_price, -> { order("price ASC")}
end
```

In my product model, I defined two scope methods: `free_shipping` and `sort_by_price`. Scope methods are named class method for retrieving and querying objects, with a corresponding route.

![](https://cdn-images-1.medium.com/max/1600/1*UElsFHU_hK1Dj2qIaxIuoA.png)

**User can view products that qualify for free shipping**

Once the database is created using [ActiveRecord](https://guides.rubyonrails.org/active_record_basics.html#creating-active-record-models), it was time to populate our database with actual data! I wanted my app to have the most updated, live product data from Rifle Paper's website. As the company does not provide a developer API, I decided to build a web scraper to solve this problem.

I had learned to build a web scraper for my [Ruby CLI gem](https://medium.com/@lisaychuang/my-first-ruby-cli-gem-travel-inspiration-8f7cf5026b3d) project, and Nokogiri is still my default library for parsing HTML. Thankfully, rifle paper's website is well structured with semantic HTML.

Following the **single responsibility principle**, I defined four methods within my scraper: `get_page, get_categories, get_products, get_all_products`:

```
require 'nokogiri'
require 'open-uri'
require 'pry'

class Scraper

  def get_page(url_str)
    html = open(url_str)
    doc = Nokogiri::HTML(html)
    doc
  end

  def get_categories
    doc = self.get_page("https://riflepaperco.com/shop/")
    categories = doc.css('ol#products-container li.product')
    categories.map do |category|
      ["#{category.css('h4 a').text}", "#{category.css('h4 a')[0]['href']}", "#{category.css('a>img')[0].attributes['src'].value}"]
    end
  end

  # url_str = "https://riflepaperco.com/shop/home/art-prints/"
  def get_products(url_str)
    doc = self.get_page(url_str)
    products = doc.css('ol#products-container li.product')
    products.map do |product|
      name = product.css('h4 a').text.strip
      normalized_name = part_before_tab(name)

      price = product.css('em')[0].children.text.strip
      normalized_price = normalize_price(price)

      # [name, price, url, image_link, category_url]
      ["#{normalized_name}", normalized_price, "#{product.css('h4 a')[0]['href']}", "#{product.css('a>img')[0].attributes['src'].value}", url_str]
    end
  end

  def get_all_products
    categories = get_categories()
    products = categories.map do |cat|
      cat_url = cat[1]
      cat_products = get_products(cat_url)
      cat_products
    end
    products.flatten(1)
  end

  private
  def part_before_tab(str)
    str.split("\t")[0] 
  end

  # Use this Ruby Format method to display price to 2 decimal point 
  # <%= "#{'%.2f' % product.price }" %>  instead of <%= product.price %>
  def normalize_price(str)
    str.gsub("$","").to_f
  end

end
```


Ruby make it easy for developers to populate their database using [rake](https://rubygems.org/gems/rake/versions/11.2.2): rake is a Ruby gem and the command line utility of Rails. Typically, you will add the initial data to populate your database in `db/seeds.rb` file. In this app, the database will be seeded with [records created by my scraper](https://github.com/lisaychuang/rails-riflepaper/blob/master/db/seeds.rb).

![](https://cdn-images-1.medium.com/max/1600/1*HpFhor-b2WSIrqYIdc12tw.png)
**There are 807 products currently available on Rifle Paper website**

As with any Ruby app, users will interact with your app through its views (HTML templates mixed with Ruby tags). But first, I need to define the available routes and controller actions.

The [router](https://guides.rubyonrails.org/routing.html) processes URL request and 'routes' the request data to the corresponding [controller actions](https://guides.rubyonrails.org/action_controller_overview.html). In my router, I defined all the possible URLs including logical nested routes (e.g. `users/1/wishlists` to see all of `user1`'s wishlists):

```
Rails.application.routes.draw do

  post '/wishlists/:id/toggle_product' => 'wishlists#toggle_product', as: :toggle_wishlist_product

  resources :users do
    resources :wishlists
  end

  resources :sessions
  resources :products do
    collection do
      get 'free_shipping'
      get 'sort_by_price'
    end
  end

  resources :categories

  # Welcome page upon starting up the app
  root to: 'sessions#welcome'

  # Signup page
  get '/signup' => 'users#new'
  post '/users' => 'users#create'

  # Routes for user login form, logging user in, and logging user out
  get '/login' => 'sessions#new'
  post '/login' => 'sessions#create'
  get '/logout' => 'sessions#destroy'

  # Omniauth Facebook login
  get '/auth/facebook/callback' => 'sessions#create'
end
```


For example, users expect a website's sign-up page to have a URL that looks something like this: `...com/signup` .

User signup actually calls for the creation of a new `user` in our app. When the Rails app receives this request, it is translated to `GET '/signup'` in your router, which needs to be matched to a controller action. The controller then creates a new instance of the `User` model:

```def new
  @user = User.new
end
def create
  @user = User.new(user_params)
  if @user.save
     session[:user_id] = @user.id
    redirect_to '/'
  else
    render 'new'
  end
end
```

Lastly, I created my user authentification and authorization using [Bcrypt](https://rubygems.org/gems/bcrypt/versions/3.1.12) and [OmniAuth](https://github.com/omniauth/omniauth) gems. Bcrypt is used to securely hash user password to be stored in our database, and OmniAuth is a library for user authentification. I've also added `omniauth-facebook` strategy, which allows authenticate user sign-up with their Facebook account using the OAuth 2 Graph API.

![](https://cdn-images-1.medium.com/max/1600/1*11pVHwP4PAWYcRHEycJbZw.png)

![](https://cdn-images-1.medium.com/max/1600/1*11pVHwP4PAWYcRHEycJbZw.png)
**User sign-up flow with Facebook**

Overall, this was a challenging project! I learned a lot about building CRUD apps in Rails, went down the rabbit hole of user authentification with OmniAuth configurations, and managing complex associations for multiple data models!

I invite you to check out my [Github repo here](https://github.com/lisaychuang/rails-riflepaper), clone or fork it and poke around! If you spot a bug, please file an issue and let me know. 

Contributes are always welcomed! And thank you for taking the time to read through this blog.
