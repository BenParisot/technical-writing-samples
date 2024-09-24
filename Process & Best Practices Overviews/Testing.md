# Testing Standards

## Our Test-Driven Development Standard
The Planet Argon standards for testing and quality assurance (QA) are rooted in [test-driven-development](https://www.agilealliance.org/glossary/tdd/) (TDD):

1. Write a test
2. Run the test and observe the failure
3. Write code to pass tests
4. Run the test and observe the pass
5. Refactor the code

At a higher level, this philosophy can be described as follows: 

1. Understand what you're trying to do
2. Get it to work (sloppily, if necessary)
3. Polish it until it's ready to ship

In addition to shipping fewer bugs, practicing TDD allows us to better ensure that we cover all the requirements of the feature or request and that we write more modular, understandable code.

> ***NOTE:*** We recognize that we inherit applications with wildly varying degress of code and test coverage. We also recognize that backfilling tests is often a luxury not afforded to us by clients, as most companies want to spend money on new feature development or critical fixes. That said, we still expect engineers to use TDD on new code, as well as while bug fixing or refactoring existing code. 

## Our Code Coverage Standard

The Planet Argon standard for code coverage is 80%. This is very much an aspirational standard, as we often work on apps that had little to no tests added when we got them.

As long as coverage percentages are increasing towards our 80% coverage standard instead of falling away from it, we consider that successful progress towards achieving our goal.

### What is code coverage?

Code coverage is a metric that shows the extent to which code has been executed in tests. Primarily performed at unit testing level, code coverage determines how much of your codebase is being tested and if the code you wrote is doing what it is supposed to be doing and. This is the metric you probably think of when you think of "testing".

### Code coverage expectations
The following are steps we expect you to take to move a client's application towards our 80% coverage standard: 

- Practice TDD on all apps
- Backfill tests when time and budgets allow
- Use coverage thresholds as checks in the app's CI 
- Pull detailed coverage reports as part of the application's regular maintenance

### Advantages of code coverage:

* Shores up insecure or broken code so bugs don’t get pushed to production
* Provides quantitative metrics we can use to improve our code
* Highlights areas of your code base that are not being executed in tests and that could be considered for removal

### Levels of coverage:

* `branch` - tests that every condition is executed at least once. For instance: 
  ```ruby
  a = 10
  if a > 0
    puts "a is positive"
  end 
  ```
  Branch coverage would test for `a > 0` once and end;
* `condition` - tests every possible outcome of all conditions. For instance: 

  ```ruby
  a = 10
  if a > 0
    puts "a is positive"
  end 
  ```  
  Condition coverage would test `a > 0` against `a > 0 = true` and `a > 0 = false` outcomes; i.e., testing the conditional with both `a = 10` and `a = -2` inputs;
* `function` - tests functions with happy paths and edge cases/outlier inputs
* `statement` - tests every executable statement in the source code at least once
* `loop` - tests every loop in the source code at least once, including loops that execute at runtime
* `finite state machine` - tests the ways different external inputs and the combination of those inputs change the state, and if the corresponding state transitions are what’s intended. 
  
  This is a very complicated level of test coverage and takes place most often during the technical design of the application (e.g., “What state should my app be in when this user causes XYZ event, and how can I test for that?”).
  
  Planet Argon is not generally involved in development at this stage, so it’s unlikely you’ll deal with finite state machine test coverage. 

> ***NOTE!*** We only concern ourselves with code coverage at Planet Argon. We do not track test coverage, a type of metric that measures tests in the context of user and product requirements. [You can learn more about test coverage here](https://www.browserstack.com/guide/code-coverage-vs-test-coverage#:~:text=explore%20test%20coverage.-,What%20is%20Test%20Coverage%3F,-Unlike%20code%20coverage).


## Testing Tools

We use Rspec to write tests for our client's Rails applications. RSpec is a behavior-driven development (BDD) testing tool for Ruby applications, commonly used with Ruby on Rails. BDD emphasizes writing tests in a more descriptive and narrative way, focusing on the behavior of the application.

### How to Use RSpec
RSpec is beneficial for both unit testing (testing individual components in isolation) and integration testing (testing how components interact).

After adding the RSpec gem to your project, you can create tests, referred to as "specs", within the `spec/` directory in your project.

A basic RSpec test could look like this:

```ruby
describe MyClass do
  describe '#my_method' do
    it 'does something' do
      expect(MyClass.new.my_method).to eq(expected_result)
    end
  end
end
```
The `describe` and `it` methods help structure your tests and provide context. The `expect` method is used with a matcher (in this case, `eq`) to define the expected behavior of the test.

## Code Coverage Tools
We used two main tools for measuring the code coverage of an application:

### SimpleCov
SimpleCov is a code coverage analysis tool for Ruby. It generates a report showing which parts of your codebase are covered by your tests.

SimpleCov should be used throughout the software development process, ideally running every time you run your tests. It will provide insights into areas of your codebase that might need more testing.

#### How to Use SimpleCov
Add the SimpleCov gem to your project, then simply call `SimpleCov.start` at the very top of your `spec_helper.rb` or `rails_helper.rb` file before any application code is required:

```ruby
require 'simplecov'
SimpleCov.start 'rails'
```
When you run your tests, SimpleCov will generate a coverage report in your project directory, usually under a `coverage/` folder. Open `index.html` in this folder to view the report in a browser.

You can read the full set-up and usage instructions in the [Simplecov docs here](https://rubydoc.info/gems/simplecov/frames).

### Codecov
Codecov is a cloud-based code coverage tool that integrates with continuous integration (CI) systems to collect coverage reports.

Use Codecov when you want to integrate code coverage analysis into your CI/CD pipeline, and when you're working with a team and you want a shared, visual representation of your project's test coverage.

#### How to Use Codecov
First, you'll need to make sure SimpleCov is generating a coverage report as outlined above. After setting up Codecov on your CI server and adding the repository, your CI server needs to send the coverage report generated by SimpleCov to Codecov after every build. This is typically done with a bash uploader provided by Codecov.

You can read the full set-up and usage instructions in the [CodeCov docs here](https://docs.codecov.com/docs).



