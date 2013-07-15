# Deploying a Sinatra application
[Sinatra][sinatra] is a DSL for quickly creating web applications in Ruby with minimal effort.

In this tutorial we're going to show you how to deploy a Sinatra application on
[cloudControl]. You can find the [source code on Github][example-app].
Check out the [Ruby buildpack] for supported features.


## Prerequisites
*   [Ruby +1.9][install-ruby] and [Rubygems][install-rubygems]
*   [cloudControl command line client][cloudControl-doc-cmdline]
*   [cloudControl user account][cloudControl-doc-user]
*   [git]


## The Hello World App in Sinatra Explained 

### Get the App
First, clone the Hello World App from our repository:
~~~bash
$ git clone git://github.com/cloudControl/ruby-sinatra-example-app.git
$ cd ruby-sinatra-example-app
~~~

Now you have a small but fully functional Sinatra application.

### Dependency Management with Bundler
[Bundler] requirements are read from the `Gemfile` (and `Gemfile.lock`) in the project's root directory.

Our simple application depends only on Sinatra:
~~~ruby
source :rubygems
gem 'sinatra'
~~~

Note that there is also the `Gemfile.lock`. When you change the dependencies,
you should run the `bundle install` command to update the `Gemfile.lock`:
~~~bash
$ bundle install
		Fetching gem metadata from http://rubygems.org/..........
		Enter your password to install the bundled RubyGems to your system: 
		Using rack (1.5.2) 
		Installing rack-protection (1.3.2) 
		Using tilt (1.3.3) 
		Installing sinatra (1.3.4) 
		Using bundler (1.2.1) 
~~~

This file must be in your repository and ensures that all the developers always
use the same versions of all the gems.

### Defining the Process Type

cloudControl uses a [Procfile] to know how to start your processes.

The example code already includes a file called `Procfile` at the top level of your repository. It looks like this:
~~~
web: bundle exec ruby server.rb -p $PORT
~~~

Left from the colon we specified the **required** process type called `web` followed by the command that starts the app and listens on the port specified by the environment variable `$PORT`.

## Pushing and Deploying your App
Choose a unique name (from now on called APP_NAME) for your application and
create it on the cloudControl platform:
~~~bash
$ cctrlapp APP_NAME create ruby
~~~

Push your code to the application's repository. This will create a deployment image:
~~~bash
$ cctrlapp APP_NAME/default push
    Counting objects: 14, done.
		Delta compression using up to 4 threads.
		Compressing objects: 100% (10/10), done.
		Writing objects: 100% (14/14), 258.14 KiB, done.
		Total 14 (delta 0), reused 14 (delta 0)
		       
		-----> Receiving push
		-----> Installing dependencies using Bundler version 1.2.1
		       Running: bundle install --without development:test --path vendor/bundle --binstubs bin/ --deployment
		       Fetching gem metadata from http://rubygems.org/..........
		       Fetching gem metadata from http://rubygems.org/..
		       Installing rack (1.5.2)
		       Installing rack-protection (1.3.2)
		       Installing tilt (1.3.3)
		       Installing sinatra (1.3.4)
		       Using bundler (1.2.1)
		       Your bundle is complete! It was installed into ./vendor/bundle
		       Cleaning up the bundler cache.
		-----> Building image
		-----> Uploading image (1020K)
		       
		To ssh://APP_NAME@cloudcontrolled.com/repository.git
 		* [new branch]      master -> master
~~~
Deploy the App:
~~~bash
$ cctrlapp APP_NAME/default deploy
~~~

Congratulations, you should now be able to reach your application at **http://APP_NAME.cloudcontrolled.com**.


[sinatra]: http://www.sinatrarb.com/
[install-ruby]: http://www.ruby-lang.org/en/downloads/
[install-rubygems]: http://rubygems.org/
[cloudControl]: http://www.cloudcontrol.com
[cloudControl-doc-user]: https://www.cloudcontrol.com/dev-center/Platform%20Documentation#user-accounts
[cloudControl-doc-cmdline]: https://www.cloudcontrol.com/dev-center/Platform%20Documentation#command-line-client-web-console-and-api
[ruby buildpack]: https://github.com/cloudControl/buildpack-ruby
[procfile]: https://www.cloudcontrol.com/dev-center/Platform%20Documentation#buildpacks-and-the-procfile
[git]: https://help.github.com/articles/set-up-git
[bundler]: http://gembundler.com/
[example-app]: https://github.com/cloudControl/ruby-sinatra-example-app