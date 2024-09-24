# Upgrading Ruby on Rails Applications

Upgrading a client's Rails application can significantly enhance its functionality, performance, and security, but this needs to be done with the client's budget and current requests in mind. We plan Rails upgrades with our clients to ensure that their apps are as modern and efficient as possible with little to no disruption to their work delivery or downtime for their users. 

Here is our general process for upgrading our clients' Rails apps. It combines the best practices from the Ruby on Rails community with some project-specific considerations we've learned from client app upgrades in the past.

## Preparatory Steps

1. **Identify the Upgrade Scope**: Determine whether you need a minor (e.g., 2.3.x → 2.4.x) or a major (e.g., 2.4.x → 3.0.x) upgrade. 

2. **Create a Jira Ticket**: The Project Manager should create a Jira ticket in 'In Discovery' for each upgrade. 

3. **Estimate the Effort**: The Lead Developer must estimate the Level of Effort (LOE) in hours.

4. **Prepare a Cost Proposal**: The Project Manager should create a cost proposal, which is then sent to IWT for budgeting and approval.

## The Upgrade Process

1. **Start with a Green Test Suite**: Before starting the upgrade process, ensure that your test suite is passing.

2. **Upgrade One Step at a Time**: Upgrade one minor release at a time. This allows you to take advantage of the deprecation warnings in one release that become hard errors in the next.

3. **Review the Rails Upgrade Guides**: Familiarize yourself with the changes between your current Rails version and the target Rails version. This can make any errors you encounter more meaningful.

4. **Change the Rails Version**: Start the upgrade by changing the Rails version in your Gemfile. For example, if you are upgrading from Rails 6.1 to 7.0, change:

    ```ruby
    gem 'rails', '~> 6.1'
    ```

    to

    ```ruby
    gem 'rails', '~> 7.0'
    ```

    Then, run the command:

    ```shell
    bundle update rails
    ```

5. **Manage Dependencies**: If Bundler can't resolve dependencies, identify the ones that rely on a version of Rails that contradicts the one you're upgrading to and resolve them manually. For instance, if you need to update additional gems like `sass-rails`, `coffee-rails`, etc., run:

    ```shell
    bundle update rails sass-rails coffee-rails
    ```

6. **Review Code Differences**: Utilize resources like `railsdiff.org` to see the code differences between your old Rails version and the new Rails version.

7. **Troubleshoot Test Failures**: If your environment fails to load during tests, use the backtrace to figure out where your error is coming from. 

8. **Deprecation Warnings and Gem Upgrades**: If your deprecation warnings are due to outdated gems, find out what version of the gem started supporting your new version of Rails, update it in your Gemfile, run `bundle update <gem>`, and try running your tests again.

    For example, if `gem_X` started supporting your new Rails version from `gem_X 1.1.0`, update it in your Gemfile:

    ```ruby
    gem 'gem_X', '~> 1.1.0'
    ```

    Then, update that gem:

    ```shell
    bundle update gem_X
    ```

## Post-Upgrade Steps

1. **Move to 'Dev Ready'**: Once the approval is received, move the Jira ticket to the status 'Dev Ready'.

2. **Schedule the Upgrade**: The Project Manager should schedule the upgrade with the Lead Developer.

3. **Review and Approval**: Once the upgrade is complete in the Staging environment, attach a brief report to the Jira ticket and send it to IWT for Quality Assurance (QA) review.

4. **Push to Production**: Upon approval, push the update to the Production environment.

5. **Close Jira Ticket**: Close the Jira ticket once the upgrade is successfully completed in the Production environment, and bill the budget for Time & Materials.

6. **QA Testing**: Even with a green test suite, perform manual Quality Assurance (QA) testing on the app, especially on frequently used paths, to ensure everything is working as expected.

Keep in mind that this guide assumes a certain level of familiarity with Ruby on Rails and its associated ecosystem. Each project is unique, and your upgrade process may need adjustments based on specific project needs and configurations.