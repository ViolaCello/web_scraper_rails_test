# README

This README would normally document whatever steps are necessary to get the
application up and running.

Things you may want to cover:

* Ruby version

* System dependencies

* Configuration

* Database creation

* Database initialization

* How to run the test suite

* Services (job queues, cache servers, search engines, etc.)

* Deployment instructions

* ...
# web_scraper_rails_test
Web Scraping with Ruby on Rails
Lal Saud
Lal Saud
Follow
Jan 25, 2020 · 5 min read



Web Scraping is used to extract useful data from websites. This extracted data can be used in many applications. Web Scraping is mainly useful in gathering data while there is no other means to collect data — eg API or feeds.
Creating a Web Scraping Application using Ruby on Rails is pretty easy. Here, I will explain how I created a simple Scraper application using Kimurai Gem, which internally uses Capybara and Nokogiri.
When the application is complete, it will pull data from cars.com. For our application, we will use this url (https://www.cars.com/shopping/sedan/). If you check the link, you will see the page lists New or Used cars from different sellers. We will try to grab all the vehicles which are currently available for sale in cars.com in that page.
Image for post
Cars.com Shopping page showing multiple vehicles
To keep our application simple, we will only scrape data from first page and will leave pagination and crawling more pages as an exercise task.
I am assuming you have setup ruby and rails in your machine. If not, I would request to setup your computer first for Ruby on Rails environment and come back here. A quick side note: I am using rails 6.0.x and ruby 2.6.2, however it should work with other versions.
So, without delay, let’s start by creating a new Rails application.
Step 1
Go to terminal and type following commands:
$> rails new web_scraper -T -d postgresql
$> cd web_scraper
Now open the Gemfile in your editor and add this at the bottom.
gem 'kimurai'
then run following commands in your terminal:
$> bundle install
$> rails db:create
$> rails server
This will setup kimurai gem, create database and start the rails application. At this point, if you open browser and go to http://localhost:3000, you should see the default rails page like this.
Ruby on Rails application default page
Rails default page
Step 2
Now let’s create a scaffold to add interface and store scraped data in to the database. In your terminal, type following commands:
$> rails g scaffold Vehicles title stock_type exterior_color interior_color transmission drivetrain price:integer miles:integer
$> rails db:migrate
This generates necessary model, controller and view files to display and process vehicles data.
Above code also adds vehicles resource routes to config/routes.rb file. We will update the file to include two more route entries. First — A post request to scrape url action. Second — a default route to application index page.
Step 3
The config/routes.rb file should look like this:

config/routes.rb file
This adds a new routes helper — scrape_vehicles_path which we can use to add a Scrape link in next step.
Step 4
Now let’s add a button in the index page to start scraping. Open app/views/vehicles/index.html.erb and add following code at the top of the page:
<%= button_to 'Scrape', scrape_vehicles_path %>
Now refreshing the browser should display vehicles index page as shown below:
Image for post
Index page with Scrape Button
Clicking the Scrape button should redirect to scrape action of vehicles controller. However we haven’t created the controller action. So let’s add that code now.
Step 5
Open app/controllers/vehicles_controller.rb file to add scrape action as shown below:

Body of scrape action
Image for post
VehiclesController with scrape action
At the same time, let’s also add a view file for scrape action. Create a new file as app/views/vehicles/scrape.html.erb and add the following code.
Image for post
scrape.html.erb file
Now let’s add VehiclesSpider code that does the magic of scraping, by creating a new file as app/models/vehicles_spider.rb
Step 6
If you go to our scrape url, you will see how the data is structured inside html tags. For eg:
Image for post
Looking at the screen above, we can see that each vehicle information is wrapped inside <div class=”shop-srp-listings__listing-container”></div> tags. So, using Nokogiri’s xpath data attributes, we will loop through this tag to grab each vehicle.
Then inside each vehicle block, we will go through each tag to find required information. For eg. Price of the vehicle is structured inside a <span class=”listing-row__price”></span> tag. To grab this information, again we will use Nokogiri’s css data attributes.
Let’s add the following code to app/models/vehicles_spider.rb file.

Vehicles Spider
That’s it. The basic scraping application is done! Congratulations!!
Let’s test the application.
Clicking the scrape button runs vehicles_spider.rb file which actually uses ‘mechanize’ fake http browser and starts scraping data. It grabs all vehicles (based on Nokogiri’s xpath data attribute) available in the page and loops through each vehicle record.
For each vehicle, it grabs title, price, stock_type, miles, color information, transmission and drivetrain. Then it inserts the record to vehicles table if the record is not already in that table.
Once the scraping is done, you will see following screen.
Image for post
Scrape Successful/Error Page
Now clicking on Go Back link should display the scraped vehicles information, as below:
Image for post
Index page with scraped data
Further Steps
Though the application so far works fine to scrape from a single page, there are still many things we can implement to make it more promising. Some things to consider:
Write tests and refactor, beautify with css/bootstrap and implement pagination.
Implement multi-page scraping eg. pagination, infinite scroll or crawling sub-links. Hint — Check Kimurai github page for more examples.
Move the scraping task to background job using Active Job and Sidekiq.
Kimurai supports several advanced features so go through its documentation.