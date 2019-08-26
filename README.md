# expressive-apis - A Better Way to Document

Create expressive apis easily without having to maintain your application code and your docs code.

## NOTE!

Currently in development, we welcome pull requests! We hope to change the way we write code and docs in
our favorite ruby web frameworks!

## What is this?

Too often in code we fail to take the time to update our api documentation, often out of forgetfulness
by a developer. In most cases the only safeguard against this issue is to rely on code reviews or an
automated task to ensure that output matches doc expectations. These can be leaky and cause a maintenace
burden. Who wants to write tests to make sure that the documentation is correct?

Enter expressive-apis - a platform agnostic way in Ruby to create expressive documentation that stays
in sync with your code.

### What do you mean by platform agnostic?

Platform agnostic here means it will run in any context that it is given - rails, sinatra, whatever - using
whatever orm you desire - active_record, sequel, whatever - and output your api in whatever format you desire -
openapi, yard, whatever - and have it stay consistent when you change the code.

## How is this accomplished?

There is of course a tradeoff to this wizardry - your api output must be passed through a serializer -
ExpressiveApis::Serializer objects in fact. There will also be a few conventions that must be followed
in order to achieve the outcomes as well, and maybe a little bit of configuration. But the benefits will
pay off when you no longer have to worry about out-of-date documenation!

Performance currently is not a consideration for this gem, but where possible performance has been considered
and pull requests are always welcome!

## Installation

`gem install expressive-apis`

or in your Gemfile add

`gem 'expressive-apis'`

and then run

`bundle install`

## How to Use

expressive-apis is mean to be as easy to use as possible while still being flexible and robust. As such, there
is very little that needs to be done.

### Initial Setup

There is some configuration required to help tie everything together. This configuration file is called expressive_apis.yml
and should be located at the root of your directory.

An example expressive_apis.yml looks like the following:

```
framework: rails # required, supports only one framework, can currently only be rails, but more to come
target: # required, can target more than one api spec, can currently only be openapi, but more to come
  - openapi
```

### Generating Model Definitions

First, create a directory somewhere that will house your serializers. All ruby files in this directory will be
parsed and any classes that extend that `ExpressiveApis::Serializer` class will be added to the possible models
of your api. If you wish to create an intermediary parent class (ie, a base serializer that extends `ExpressiveApis::Serializer`
but does not itself serialize anything), then it is best to create that parent class outside of the directory.

Then, in your expressive_apis.yml file, add the following:

```
models: '<path/to/serializer/dir>'
```

### Linking Models to Routes

To link models to paths, the following must be done:

#### Rails

In your rails controller you must do one of the following:

- Create a single serializer method that returns the serializer used for all actions on the controller called `serializer`
- Create a method for each action that returns the serializer used in the pattern `<action>_serailizer`. If this is defined
and the controller level is also defined, this will override the controller.
- Return instance of `ExpressiveApis::Response`. This will override the above two possibilities.

If your rails controllers return multiple types, expressive-apis will be able to determine that as well. If one of
the formats is html, that format is ignored.

#### Sinatra

TODO

### Linking Request Parameters to Routes

The final piece of the puzzle finally is to link the request parameters to a given route.

#### Rails

In your rails controller, you must do one of the following

- Create a single params method that returns the expected params called `allowed_params` - this will return a strong_params object
- Create a method for each action the returns the expected params in the pattern `<action>_params`. If this is defined
and the controller level is also defined, this will override the controller.
- Return instance of `ExpressiveApis::Response` that has included the optional params and permitted options passed in. This will override
the above two possibilities.

#### Sinatra

TODO

### Adding Descriptions to Routes

Descriptions about how a particular route should be used or about a particular model can be done by either adding a `docs` directory to your
project root or adding `docs: <path/>to/docs>` to your expressive_apis.yml file. The directory will have the following structure:

```
docs
   | routes
        | <route_path>
             | overview.md
             | <route_method>
   | models
        | <model_name>
```

Note that any markup files that detail higher level overviews of a particular grouping can also be done here. Routes are grouped together
logically by the route path, so all HTTP methods are enclosed in that routes subdirectory. An `overview.md` will be concated to the group
overview if one exists (ie for OpenApi groups).

### Additional Documentation

If additional documentation exists that provides an even higher level overview of how to use the api, you can add these in arbitrary directory
names. All *.md files will be converted to html, and all other files will be treated as html. This will be output as the root of your api with
whatever api documentation target you have set up being linked to from the top nav of these pages. If there are no other directories added, then
this is ommitted and the target api is used at the root of your api docs.

To add these files, your directory for the docs should look like the following:

```
docs
   | routes * reserved, don't use
   | models * reserved, don't use
   | <custom_dir>
      | <dir> * can add dirs and will be nested under custom_dir
      | <file> * added as a page of dir
```

Note that directories are treated as a logical grouping and can be nested to create sub directories. This will also autogenerate a TOC.

### Generating the Docs

To generate the docs, run `rake expressive_apis:generate`. This will output each target in its own folder. You can further do with the
output as you please (ie, for openapi, put behind authentication so the examples work). The world is your oyster with this now, do what you
want!

### Adding Targets

expressive-apis outputs an agonstic format of the api and then uses transformers to output the data to a given api spec. These can be
added by Pull Requests. See the `expressive-apis/targets/base_target.rb` file for the basic hooks that can be used to transform the output.

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake spec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/derigible/expressive-apis. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [Contributor Covenant](http://contributor-covenant.org) code of conduct.

## Code of Conduct

Everyone interacting in the Expressive::Apis project’s codebases, issue trackers, chat rooms and mailing lists is expected to follow the [code of conduct](https://github.com/derigible/expressive-apis/blob/master/CODE_OF_CONDUCT.md).
