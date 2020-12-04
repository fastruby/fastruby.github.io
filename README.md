# Skunk

![skunk](https://github.com/fastruby/skunk/raw/master/logo.png)

[![Contributor Covenant](https://img.shields.io/badge/Contributor%20Covenant-v1.4%20adopted-ff69b4.svg)](CODE_OF_CONDUCT.md) [![Build Status](https://travis-ci.org/fastruby/skunk.svg?branch=master)](https://travis-ci.org/fastruby/skunk) [![Maintainability](https://api.codeclimate.com/v1/badges/3e33d701ced16eee2420/maintainability)](https://codeclimate.com/github/fastruby/skunk/maintainability) [![Docs](http://img.shields.io/badge/yard-docs-blue.svg)](http://rubydoc.info/gems/skunk) [![codecov](https://codecov.io/gh/fastruby/skunk/branch/master/graph/badge.svg)](https://codecov.io/gh/fastruby/skunk)

A RubyCritic extension to calculate SkunkScore for a file or project.

## What is the SkunkScore?

The SkunkScore is a value that assesses the technical debt of a module. It takes
into account:

- Code Complexity
- Code Smells
- Code Coverage

The main goal of the SkunkScore is to serve as a compass in your next
refactoring adventure. It will help you answer these questions:

- What can I do to pay off technical debt?
- What are the most complicated files with the least code coverage?
- What are good candidates for your next test-writing efforts?
- What are good candidates for your next refactoring efforts?

The formula is not perfect and it is certainly controversial, so any feedback is
welcome as a new issue!

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'skunk'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install skunk

## Usage

### Help details

There are not that many options but here they are:

```
skunk -h
Usage: skunk [options] [paths]
    -b, --branch BRANCH              Set branch to compare
    -o, --out FILE                   Output report to file
    -v, --version                    Show gem's version
    -h, --help                       Show this message
```

### Getting a sorted list of smelly files

To get the best results, make sure that you have `coverage/.resultset.json` in
your application directory. That way `skunk` knows what's the status of your
test suite + code coverage.

Then simply run:

```
skunk
```

Then get a list of smelly files:

```
$ skunk
running flay smells

running flog smells
.............
running reek smells
.............
running complexity
.............
running attributes
.............
running churn
.............
running simple_cov
.............
New critique at file:////Users/etagwerker/Projects/fastruby/skunk/tmp/rubycritic/overview.html
+-----------------------------------------------------+----------------------------+----------------------------+----------------------------+----------------------------+----------------------------+
| file                                                | skunk_score                | churn_times_cost           | churn                      | cost                       | coverage                   |
+-----------------------------------------------------+----------------------------+----------------------------+----------------------------+----------------------------+----------------------------+
| lib/skunk/cli/commands/default.rb                   | 166.44                     | 1.6643999999999999         | 3                          | 0.5548                     | 0                          |
| lib/skunk/cli/application.rb                        | 139.2                      | 1.392                      | 3                          | 0.46399999999999997        | 0                          |
| lib/skunk/cli/command_factory.rb                    | 97.6                       | 0.976                      | 2                          | 0.488                      | 0                          |
| test/test_helper.rb                                 | 75.2                       | 0.752                      | 2                          | 0.376                      | 0                          |
| lib/skunk/rubycritic/analysed_module.rb             | 48.12                      | 1.7184                     | 2                          | 0.8592                     | 72.72727272727273          |
| test/lib/skunk/cli/commands/status_reporter_test.rb | 45.6                       | 0.456                      | 1                          | 0.456                      | 0                          |
| lib/skunk/cli/commands/base.rb                      | 29.52                      | 0.2952                     | 3                          | 0.0984                     | 0                          |
| lib/skunk/cli/commands/status_reporter.rb           | 8.0                        | 7.9956                     | 3                          | 2.6652                     | 100.0                      |
| test/lib/skunk/rubycritic/analysed_module_test.rb   | 2.63                       | 2.6312                     | 2                          | 1.3156                     | 100.0                      |
| lib/skunk.rb                                        | 0.0                        | 0.0                        | 2                          | 0.0                        | 0                          |
| lib/skunk/cli/options.rb                            | 0.0                        | 0.0                        | 2                          | 0.0                        | 0                          |
| lib/skunk/version.rb                                | 0.0                        | 0.0                        | 2                          | 0.0                        | 0                          |
| lib/skunk/cli/commands/help.rb                      | 0.0                        | 0.0                        | 2                          | 0.0                        | 0                          |
+-----------------------------------------------------+----------------------------+----------------------------+----------------------------+----------------------------+----------------------------+

SkunkScore Total: 612.31
Modules Analysed: 13
SkunkScore Average: 0.47100769230769230769230769231e2
Worst SkunkScore: 166.44 (lib/skunk/cli/commands/default.rb)
```

The command will run `rubycritic` and it will try to load code coverage data
from your `coverage/.resultset.json` file.

Skunk's report will be in the console. Use it wisely. :)

### Comparing one branch vs. another

Simply run:

```
skunk -b <target-branch-name>
```

Then get a SkunkScore average comparison:

```
$ skunk -b master
Switched to branch 'master'
running flay smells
..
running flog smells
..............
running reek smells
..............
running complexity
..............
running attributes
..............
running churn
..............
running simple_cov
..............
Switched to branch 'feature/compare'
running flay smells
..
running flog smells
.................
running reek smells
.................
running complexity
.................
running attributes
.................
running churn
.................
running simple_cov
.................
Base branch (master) average skunk score: 290.53999999999996
Feature branch (feature/compare) average skunk score: 340.3005882352941
Score: 340.3
```

This should give you an idea if you're moving in the right direction or not.

### Sharing results

If you want to quickly share the results of your report, you can use an
environment variable:

```
SHARE=true skunk app/
...
SkunkScore Total: 126.99
Modules Analysed: 17
SkunkScore Average: 7.47
Worst SkunkScore: 41.92 (lib/skunk/cli/commands/status_sharer.rb)

Generated with Skunk v0.5.0
Shared at: https://skunk.fastruby.io/k
```

Results will be posted by default to https://skunk.fastruby.io which is a free
and open source Ruby on Rails application sponsored by
[OmbuLabs](https://www.ombulabs.com) ([source code](https://github.com/fastruby/skunk.fyi)).

If you prefer to post results to your own server, you can do so:

```
SHARE_URL=https://path.to.your.skunk-fyi-server.example.com skunk app/
...
SkunkScore Total: 126.99
Modules Analysed: 17
SkunkScore Average: 7.47
Worst SkunkScore: 41.92 (lib/skunk/cli/commands/status_sharer.rb)

Generated with Skunk v0.5.0
Shared at: https://path.to.your.skunk-fyi-server.example.com/k
```

## Known Issues

The SkunkScore should be calculated per method. This would provide a more accurate
representation of the average SkunkScore in a module.

I think that the SkunkScore of a module should be the average of the SkunkScores of
all of its methods.

Right now the SkunkScore is calculated using the totals for a module:

- Total Code Coverage Percentage per Module
- Total Churn per Module
- Total Cost per Module

For more details, feel free to review and improve this method: [RubyCritic::AnalysedModule#skunk_score]

## Development

After checking out the repo, run `bin/setup` to install dependencies. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/fastruby/skunk/issues.

## Sponsorship

![FastRuby.io | Rails Upgrade Services](https://github.com/fastruby/skunk/raw/master/fastruby-logo.png)

`skunk` is maintained and funded by [FastRuby.io](https://fastruby.io). The names and logos for FastRuby.io are trademarks of The Lean Software Boutique LLC.
