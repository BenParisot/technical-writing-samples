# Debugging

Debugging is an essential aspect of software development, and it's particularly important when it comes to working with a complex framework like Ruby on Rails. This guide provides an overview of various methods we use for debugging Rails applications as well as Planet Argon standards for each method.

## Rails Logger

The Rails Logger is a built-in feature that allows developers to record any kind of information, including errors, specific variable states, or just traces of program execution. The Logger is set up by default with every Rails application, and log messages are automatically outputted to the console and written to a log file in the log directory.

Here's an example of how to use the Rails Logger:

```ruby
def some_method
  Rails.logger.info "Inside some_method at #{Time.now}"
  #Rest of the method
rescue StandardError => e
  Rails.logger.error "Error occurred: #{e.message}"
end
```

In this example, the method logs an info message when it begins execution, and logs an error message if any StandardError is raised. You can view these logs by navigating to the log directory in your Rails project and opening the file that corresponds to your environment, like development.log.

### Rails Logger Standards
The following are standards we attempt to adhere to when using the Rails Logger:

1. **Use Appropriate Log Levels**
  
    Rails Logger provides different log levels like debug, info, warn, error, fatal, and unknown. Use these levels wisely to categorize the log messages. For instance, use debug for detailed diagnostic information useful in a development environment, and error or fatal for logging critical issues that need immediate attention. This categorization helps in filtering and analyzing logs based on the severity of the issues.

2. **Provide Context to Log Messages**

    Always include relevant identifiers like user IDs, transaction IDs, or other unique identifiers that can help trace the flow of a request or the occurrence of an event. Contextual information makes it easier to debug issues by allowing you to correlate log entries with specific operations or user actions within your application.

3. **Don't Log Sensitive Data**

    Be cautious about what is being logged. Avoid logging sensitive information such as passwords, personal identifiable information (PII), or payment details. Exposing such information in logs can lead to security vulnerabilities. Use filtering mechanisms provided by Rails to exclude or obfuscate sensitive data from the logs.

4. **Log Structured Data**

    Instead of logging plain text messages, consider logging structured data, like JSON. This approach allows for easier parsing and analysis of log data, especially when using log management tools. Structured logs can be more readily queried and analyzed, helping you to extract insights and identify issues more efficiently.

5. **Avoid Logging Unnecessary Information**
    
    Logging too much information leads can make it difficult to spot real issues. Focus on logging information that provides value for debugging and operational insight

6. ***Test Log Outputs**

    When writing complex log statements, particularly those that include logic or data transformation, include tests to ensure they produce the expected output. This helps prevent logging errors or inconsistencies.

7. **Document Your Logging Strategy**

    Maintain documentation on your logging strategy, either in a project README or client Wiki. Include what is logged at each level, how logs are rotated and stored, and any other relevant practices. This ensures consistency and helps new team members understand and use the logging system effectively.

## Rails Console

The Rails Console is an interactive command-line interface for Rails. It loads the Rails application environment and allows you to interact with your application models, view and change data, and test new methods. It is an excellent tool for quickly understanding the state of your application.

To start the Rails console, run rails console or rails c from your terminal:

```bash
rails console
```

Now you can interact with your application. For example, to find a user with an id of 1:

```ruby
User.find(1)
```
You can also make changes to your data:

```ruby
user = User.find(1)
user.name = "New Name"
user.save
```
***Remember that the Rails console operates on your actual data, so be careful with commands that alter data when working in a production console!***

### Rails Console Standards
The following are standards we attempt to adhere to when using the Rails Console:

1. **Use Safe Methods for Data Manipulation**

    Use `find_by` instead of `find` for retrieving records, as it returns `nil` instead of raising an exception if the record is not found. Use `update` and `save` instead of `update!` and `save!` for updating records, as the latter will raise exceptions if validations fail.

2. **Leverage the Rails Console Sandbox**
    Use the Rails console in sandbox mode (`rails console --sandbox`) when you need to experiment with data changes. In this mode, any changes you make to the database will be rolled back when you exit the console, preventing accidental alterations to your data.

3. **Be Cautious with Sensitive Data**
    
    Just like with logging, be mindful of not exposing sensitive data in the console. This is particularly important if your console sessions are logged or monitored.

4. **Be Aware of Your Environment**

    Be aware of which environment (development, test, production) you are in when using the console. Avoid running experimental or potentially destructive commands in the production console.

5. **Use Version Control Wisely**

    Before using the console to make changes to data or debug issues, ensure your application is under version control. This practice helps in tracking changes and reverting them if needed.

## Unit and Integration Tests
Most people don't think of tests as part of debugging, but practicing TDD has a direct impact on the amount of bugs that make it to production and the speed at  which engineers can find, understand, and fix bugs.
- TDD ensures code begins with corresponding tests, reducing bugs in production
- Failing tests quickly pinpoint potential bugs, saving time and resources in bug fixing.
- Tests clarify intended functionality and provide more context to a bug
- Tests make it easier to identify when actual behavior deviates from expected behavior
- Adding new tests added for each bug prevents future regressions
- Adding tests after a bug is fixed is a form of continuous improvement of the test suite that strengthens code resilience.

### Testing Standards
The following are standards we attempt to adhere to when using tests for debugging purposes:

1. **Use Test-Driven Development (TDD) from the Start**

    Using TDD can help in understanding the requirements better and catching errors earlier in the development process.

2. **Write Specific and Descriptive Test Cases **

    Ensure that each test case is focused and tests only one aspect of your application. The description of each it block should clearly state what is being tested and what the expected outcome is. This clarity makes it easier to identify what exactly fails when a test does not pass.

3. **Utilize `describe` and `context` Blocks Effectively** 

    Organize your tests using `describe` and `context` blocks to group related tests together. This helps in understanding the structure of your tests and makes it easier to locate tests for specific modules or functionalities.

4. **Keep Tests Isolated**

    Each test should run independently of other tests. This isolation prevents side effects from one test affecting the outcome of others, which is crucial for accurate debugging.

5. **Write Tests for Bug Fixes**

    When you fix a bug, write a test that covers the scenario that was not working before. This ensures that the bug does not reappear later, a practice known as regression testing.

6. **Review and Refactor Tests Regularly**

    Periodically review your test suite for outdated tests or tests that no longer reflect your application's current functionality.

## Exception Notification Services
Exception notification services such as [Pingdom](05.Tools/05.ApplicationMonitoringTools/Pingdom.md), [New Relic](05.Tools/05.ApplicationMonitoringTools/NewRelic.md), and [Bugsnag](05.Tools/05.ApplicationMonitoringTools/Bugsnag.md) provide real-time error tracking and notifications. They catch exceptions that occur in your Rails application and provide detailed reports with stack traces, request parameters, and session details.

Here's an example of how to setup Bugsnag in your Rails application:

First, add the Bugsnag gem to your Gemfile:

```ruby
gem 'bugsnag'
```
Then, run bundle install. After that, you need to configure Bugsnag with your API key. You can do this in an initializer:

```ruby
# config/initializers/bugsnag.rb

Bugsnag.configure do |config|
  config.api_key = 'your-api-key-here'
  config.app_version = '1.2.3'
end
```
Now, whenever an unhandled exception occurs, Bugsnag will automatically report the error. You can also manually notify Bugsnag of errors:

```ruby
begin
  1 / 0
rescue ZeroDivisionError => e
  Bugsnag.notify(e)
end
```
In this example, a ZeroDivisionError is caught and reported to Bugsnag manually.

### Application Monitoring Standards
The following are standards we attempt to adhere to when using application monitoring tools for debugging purposes:

1. **Use Real-time Alerts**

    We set up these tools to use real-time alerts for critical issues as part of our [Incident Response Process](12.IncidentResponse/01.Incident_Response_Process.md). This includes server downtime, performance degradation, or a spike in error rates.

2. **Correlate Monitoring Data with App Logs**

    This practice is particularly useful for in-depth analysis of issues. When an error is reported by a monitoring tool, checking the logs for that specific time frame can provide additional context.

3. **Use Environment-Specific Configurations**

    Differentiate between the monitoring settings for production, staging, and development environments. For instance, you might want more verbose error reporting in staging and development than in production.

## Pry

Pry is a powerful alternative to the standard IRB shell for Ruby. It provides numerous features that make debugging and exploration of code much easier. Some of the features include syntax highlighting, a flexible plugin architecture, runtime invocation, source and documentation browsing, command shell integration, and many more.

Installation
To install Pry, you need to have Ruby and RubyGems installed on your machine. If you have them, you can install Pry by running:

```bash
gem install pry
```
You can start a Pry session by simply running pry in your terminal:

```bash
pry
```
### Debugging with Pry
One of the most common use cases for Pry is to use it for debugging. You can place binding.pry in your code where you want the execution to pause, and when the code is run, a Pry session will start at that point:

```ruby
def some_method
  x = 10
  binding.pry   # Execution will stop here.
  x = 20
end
```
When the above code is run, it will stop at binding.pry and start a Pry session. In this session, you can inspect and manipulate variables, call methods, etc. For example, you can check the value of x by simply typing x and hitting enter. You can change the value of x by doing x = 30, and when you resume the execution (by typing exit or continue), the new value of x will be used.

### Navigating Code with Pry

Pry allows you to navigate your code easily. You can "cd" into objects, classes, and even scopes:

```ruby
class SomeClass
  def some_method
    x = 10
    binding.pry
  end
end

SomeClass.new.some_method
```
When the binding.pry is hit in the code above, you can "cd" into self to explore the state of the current object:

```bash
pry(main)> cd self
```
### Viewing Source Code and Documentation with Pry
Pry can show you the source code of methods, and even the documentation for them. For example, to view the source code of a method:

bash
pry(main)> show-method SomeClass#some_method
To view the documentation of a method:

```bash
pry(main)> show-doc SomeClass#some_method
```
### Pry with Rails
Pry can also be used as the console for Rails, replacing the default IRB console. To do this, add the pry-rails gem to your Gemfile and run bundle install:

```ruby
gem 'pry-rails'
```
Then, instead of using rails console, use rails c to start a Pry session with your Rails application loaded:

```bash
rails c
```
### Using Pry-remote
For applications running on a remote server or in the context of another framework like Pow or Passenger, pry-remote can be used. pry-remote starts a Pry session on a server, which can be connected to remotely.

To use pry-remote, add it to your Gemfile and run bundle install:

```ruby
gem 'pry-remote'
```
Instead of binding.pry, use binding.remote_pry to start a remote session:

```ruby
def some_method
  x = 10
  binding.remote_pry   # This will start a remote Pry session.
  x = 20
end
```
After loading the relevant page in the browser, you can use pry-remote:

```bash
bundle exec pry-remote
```
Pry is an extremely powerful tool that can significantly improve the debugging and exploration experience in Ruby. The examples above demonstrate just a few of the capabilities of Pry. The full functionality is much broader and can be customized to your needs. Learn more in the [Pry wiki here](https://github.com/pry/pry/wiki).

