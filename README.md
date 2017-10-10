# Guard Demo

* Steps to Build the Guard Demo
  * Create App
    ```
    rails new guard_demo --database=postgresql --skip-test-unit;
    cd guard_demo;
    ```
  * Install RSpec for Rails
    ```
    echo 'gem "rspec-rails", :group => [:development, :test]' >> Gemfile;
    bundle install;
    rails generate rspec:install;
    ```
  * Install Dependencies
    ```
    bundle install
    ```
  * Run Server
    ```
    rails s
    ```
  * Add Static Pages
    * Link: https://www.railstutorial.org/book/static_pages
    * Generate Controller
      ```
      rails g controller StaticPages home help
      ```
    * Update Routes
      ```
      root to: 'static_pages#home'
      get 'static_pages/home', to: 'static_pages#home'
      get 'static_pages/help', to: 'static_pages#help'
      ```
  * Run PostgreSQL
  * Create and migrate DB
    ```
    rails db:create db:migrate
    ```
  * Run Tests
    ```
    bundle exec rspec
    ```
  * Add Guard RSpec
    * Reference: https://github.com/guard/guard-rspec
    * Add Guard RSpec to Gemfile
      ```
      group :development, :test do
        gem 'guard-rspec'
      end
      ```
    * Install dependency
      ```
      bundle install
      ```
    * Generate RSpec files
      ```
      bundle exec guard init rspec
      ```
    * Run Guard
      ```
      bundle exec guard
      ```
    * Run Rails Server in a separate Tab
      ```
      rails s
      ```
    * Change a Unit Test to see the Guard Terminal window auto-run the Unit tests and fail
      * Change in spec/controllers/static_pages_controller_spec.rb
        * Before
          ```
          expect(response).to have_http_status(:success)
          ```
        * After
          ```
          expect(response).to have_http_status(404)
          ```
        * Result - Terminal displays test failure
          ```
          expected the response to have status code 404 but it was 200
          ```
      * Reference: https://github.com/rspec/rspec-rails/blob/master/lib/rspec/rails/matchers/have_http_status.rb

  * Add LiveReload - automatically reload after views modified
    * Reference:
      * http://livereload.com/
      * https://github.com/guard/guard-livereload
    * Add Ruby version to Gemfile
      ```
      source 'https://rubygems.org'
      ruby '2.4.2'
      ```
    * Add Guard LiveReload to Gemfile
      ```
      group :development, :test do
        gem 'guard-livereload', '~> 2.5', require: false
      end
      ```
    * Install dependencies
      ```
      bundle install
      ```
    * Generate LiveReload config files
      ```
      bundle exec guard init livereload
      ```
    * Install LiveReload browser extension (see guard-livereload Gem Readme and Wiki on Github)
      * Browser Extension - http://livereload.com/extensions/#installing-sections

    * Switch to Ruby version
      ```
      rbenv global 2.4.2
      rbenv shell 2.4.2
      ```

    * Overcome Errors - encountered errors and mitigation measures

      * Problem 1 - Error when running `rails s`
        ```
        Could not find eventmachine-1.2.5 in any of the sources
        Run `bundle install` to install missing gems.
        ```

      * Solution 1 - Change Gemfile to the following
        ```
        group :development, :test do
          gem 'eventmachine', '1.2.5',
            path: 'vendor/bundle/gems/eventmachine-1.2.5',
            require: 'eventmachine'
          # http://railscasts.com/episodes/264-guard
          gem 'rb-fsevent', :require => false if RUBY_PLATFORM =~ /darwin/i
          gem 'guard-rspec', '~> 4.7.3'
          gem 'guard-livereload', '~> 2.5.2'
        end
        ```

        * Update Gemfile.lock
          ```
          bundle update
          ```

      * Problen 2 - Error still when running `rails s`
        ```
        ~/guard_demo/vendor/bundle/gems/pg-0.21.0/lib/pg.rb:4:in `require': dlopen(~/guard_demo/vendor/bundle/gems/pg-0.21.0/lib/pg_ext.bundle, 9): Symbol not found: _rb_cFixnum (LoadError)
        ```

      * Solution - Switch from PostgreSQL 0.21.0 to 0.20.0 by changing Gemfile to the following:
        ```
        gem 'pg', '0.20',
          path: 'vendor/bundle/gems/pg-0.20.0',
          require: 'pg'
        ```

        * Update Gemfile.lock
          ```
          bundle update
          ```

      * Problem 3 - Error when trying to run Guard or Rails Server
        ```
        $ bundle exec guard

        21:55:09 - ERROR - Invalid Guardfile, original error is:
        > [#]
        > [#] incompatible library version - ~/guard_demo/vendor/bundle/gems/http_parser.rb-0.6.0/lib/ruby_http_parser.bundle,
        ```

        ```
        $ rails s
        Could not find http_parser.rb-0.5.3 in any of the sources
        Run `bundle install` to install missing gems.
        ```

      * Solution - Change Gemfile to from version 0.6.0 of http_parser.rb Gem to 0.5.3
        ```
        group :development, :test do
          gem 'http_parser.rb', '0.5.3'
        end
        ```

        * Update Gems
        ```
        bundle update
        ```

    * Open two Terminal Tabs and run the following
      * In Tab 1 - `rails s`
      * In Tab 2 - `bundle exec guard`

    * Edit code in Rails views and see each changes automatically reflected in Browser and Unit Tests run upon each change (no need to refresh the browser manually, or to manually run RSpec after each code change)

    * The above issues encountered during installation of Guard-LiveReload have been reported here - https://github.com/guard/guard-livereload/issues/177
