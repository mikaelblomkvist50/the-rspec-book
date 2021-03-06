###  Chapter 3 Describing Features

....The plus signs for the exact matches always come before the minus signs for the number matches and don't align with specific positions in the guess or the secret code.

....We want to avoid the pitfalls of the Big Design Up Front, but we also want to do enough planning to know we're heading in the right direction. ....picking out user stories for our first iteration.

....A great way to get started gathering user stories is to do a high-level brain dump of the sorts of things we might like to do. Here are some titles to get started:

* Code-breaker starts game
* Code-breaker submits guess
* Code-breaker wins game
* Code-breaker loses game
* Code-breaker plays again
* Code-breaker requests hint
* Code-breaker saves score

See how each of these is phrased as role + action? The role is the code-breaker role each time because this game has only one kind of user. In other applications, we might have several different kinds of users, in which case we want to express stories in terms of a specific role (not just a generic user), because that impacts how we think about each requirement and why we're implementing code to satisfy it.

These are also high level and don't tell us much about how the system should respond to these actions. Let's take titles and generate some user stories from them.

**Code-breaker starts game** The code-breaker opens a shell, types a command, and sees a welcome message and a prompt to enter the first guess.

**Code-breaker submits guess** The code-breaker enters the guess, and the system replies by marking the guess according to the marking algorithm.

**Code-breaker wins game** The code-breaker enters a guess that matches the secret code exactly. The system responds by marking  the guess with four + signs and a message congratulating the code-breaker on breaking the code in however many guesses it took.

**Code-breaker plays again** After the game is won or lost, the system prompts the code-breaker to play again. If the code-breaker indicates yes, a new game begins. If the code-breaker indicates no, the system shuts down.

**Code-breaker request hint** At any time during the game, the code-breaker can request a hint, at which point the system reveals one of the numbers in the secret code.

**Code-breaker saves score** After the game is won or lost, the code-breaker can opt to save information about the game: who (initials?), how many turns, and so on.

We can already see some of the challenges ahead: "according to the marking algorithm" is going to require some conversation with the stakeholders. In fact, this is where we'll spend the majority of our time both planning and developing, because the marking algorithm is where much of the complexity lies.

Note the deliberate lack of detail and even some open questions. We'll get into some detail as we choose which of these stories we want to include in the release, and then we'll get more detailed in each iteration within the release. But at each phase, we want to do just enough planning to keep on moving, and no more.

Now that we have some stories, let's consider them in the context of the stated goal for the initial release: to simply be able to play the game, Looking at the original list of stories, there are only two that are absolutely necessary to meet that goal:
* Code-breaker starts game
* Code-breaker submits guess

We definitely have to be able to start the game somehow so that one is a no-brainer. Once we've started the game, if we can submit a guess and get the mark, then we can submit more guesses. As soon as we get a perfect mark, the game is won, and we hit `Ctrl + C` to stop the game and start the game back up to play again. What do you think? I agree I wouldn't add any more stories for the initial release :)

....Wait, wait, wait! We're heading down a slippery slope here. Pretty soon we'll be including our entire backlog of stoires in the first release! Let's step back for a second. What is the release goal? To be able to play the game.

....Given this context, we'll go with Code-breaker starts the game and Code-breaker submits guess. Together, those two stories should suffice to get us to the point where we can play the game--unless, of course, we're missing something.

It turns out that there is one feature of the game that we haven't discussed yet! We won't really see the evidence of it until we submit a guess and the game marks it. Can you guess what it is? Think about how the game will be able to mark the guess. It has to mark it against something, right?

The **secret code**!

The game will need to generate a **secret code** that is different every time in order for it to be truly enjoyable. Now is this a user story? This is one of those grey areas that challenges the boundaries of what a user story is. Ask one experienced XPer, and you'll hear that this is really part of the **Code-breaker starts game** story based on the idea that the **secret code** should be generated when the game starts.

The next person might argue it's really part of the **Code-breaker submits guess** story because that's the first time the user gets any feedback from the system that depends on the guess.

We're going to take third stance and make it a seperate story based on practicality. ...we want to keep things small enough to accomplish in a reasonable amount of time so we can check things off the list as we go. Does that sound selfish?

....Absolutely not! we're just planing! And user stories are, above all else, a planning tool. Although you can find many definitions of what a user story is and therefore must be in order to earn the title, here is a simple set of criteria that David learned from Bob Koss at Object Mentor. A user story must have the following characteristics:

**Have a business value** Clearly, the game is no fun unless it generates a different secret code each time.

**Be testable** That's easy. We just start up a bunch of games and ask for the code. As you'll see when we develop this part, this reveals some interesting questions about designing for testability.

**Be small enough to implement in one iteration** This is the motivation for separating this story. It's guidelines that allows us to balance implementation concerns with requirements.

So, now we have our release plan with three stories. It's time to start breaking down into iterations.

....Cucumber features have three parts: a title, a brief narrative(conextra format narrative or free-form narrative), and an arbitrary number of scenarios that serve as acceptance criteria.

1. Make a directory called `features` then create a new file under it called `codebreaker_starts_game.feature` so it looks like `features/codebreaker_starts_game.feature`. Copy the content of the feature, shown earlier.

2. Make a directory called `support`, under `features` then create a new file called `env.rb` so it looks like `features/support/env.rb`. Even though we will leave this empty for now, `Cucumber` needs this file (or any `.rb` file) in order to know that we're using Ruby.

3. Create a new file under `features` called `codebreaker_submits_guess.feature` so it looks like `features/codebreaker_submits_guess.feature`. Add Copy the content of the feature, shown earlier.

### Chapter 4  Automating Features with Cucumber

`ch4-automating-features-with-cucumber/features/step_definitions/codebreaker_steps.rb`:
```ruby
Given("I am not yet playing") do

end

When("I start a new game") do
  Codebreaker::Game.new.start
end
```

At this point, we don't have any application code, so we're just writing the code we wish we had. We want to keep it simple, and this is about as simple as it can get.

In my early days at Object Mentor I (David) attended a `TDD` class taught by James Grenning. He was refactoring an existing method, and he wrote a statement that called a `method` that didn't exist yet, saying "start by writing the code you wish you had".

This was a galvanising moment for me.

It is common to write code we wish we had doing `TDD`. We send message from the code example to an `object` that does not have a corresponding `method`. We let the Ruby interpreter tell us that the `method` does not exist yet (red) and then implement that method (green).

Doing the same thing within application code, calling the code we wish we had in one `module` from another `module`, was a different matter. It was as though an arbitrary boundary had been lifted and suddenly all of the code was my personal servant, ready and wiling to bend to my will. It didn't matter whether we were in a test or in the code being tested. What mattered was that we started from the view of the code that was going to use the new code we were about to write.

Over the years this has permeated my daily practise. It is very liberating , and it results in more usable `APIs` than I would have come up with starting with the `object` receiving the message.

In retrospect, this also aligns closely with the outside-in philosophy of `BDD`, if the goal is to provide great `APIs`, then the best place to design them is from their consumers.

<pre><code>
$ <b>cucumber features/codebreaker_starts_game.feature</b>
Feature: code-breaker starts game
As a code-breaker
I want to start a game
So that I can break the code

  Scenario: start game                          # features/codebreaker_starts_game.feature:7
    Given I am not yet playing                  # features/step_definitions/codebreaker_steps.rb:1
    When I start a new game                     # features/step_definitions/codebreaker_steps.rb:5
      uninitialized constant Codebreaker (NameError)
      ./features/step_definitions/codebreaker_steps.rb:6:in `"I start a new game"'
      features/codebreaker_starts_game.feature:9:in `When I start a new game'
    Then I should see 'Welcome to Codebreaker!' # features/codebreaker_starts_game.feature:10
    And I should see 'Enter guess:'             # features/codebreaker_starts_game.feature:11

Failing Scenarios:
cucumber features/codebreaker_starts_game.feature:7 # Scenario: start game

1 scenario (1 failed)
4 steps (1 failed, 2 undefined, 1 passed)
0m0.030s

You can implement step definitions for undefined steps with these snippets:

Then("I should see {string}") do |string|
  pending # Write code here that turns the phrase above into concrete actions
end
</pre></code>

....The error message tells us that we need to create a `Codebreaker` constant. It's coming from the reference to `Codebreaker::Game` in the step definition we just wrote, which also calls the `start()`, so let's go ahead and create that. Create a `lib` directory with a `codebreaker` subdirectory, and add a `game.rb` file in `lib/codebreaker` with the following:
```ruby
module Codebreaker
  class Game
    def start
    end
  end
end
```

....The conventional approach to this is to have a file in the `lib` directory named for the top-level module of the app. In our case, that's `codebreaker.rb` Create that file now with the following:
```ruby
require 'codebreaker/game'
```

Now add the following to `features/support/env.rb`:
```ruby
$LOAD_PATH << File.expand_path('../../../lib', __FILE__)
require 'codebreaker'
```

Cucumber will load `features/support/env.rb`, which now requires `lib/codebreaker.rb`, which, in turn, requires `lib/codebreaker/game.rb`, which is where we defined the `Codebreaker` module with the `Game` with an empty `start()` method. If you now run `$ cucumber features/codebreaker_starts_game.feature`, you should see some different results:

<pre><code>
$ <b>cucumber features/codebreaker_starts_game.feature</b>
Feature: code-breaker starts game
As a code-breaker
I want to start a game
So that I can break the code

  Scenario: start game                          # features/codebreaker_starts_game.feature:7
    Given I am not yet playing                  # features/step_definitions/codebreaker_steps.rb:1
    When I start a new game                     # features/step_definitions/codebreaker_steps.rb:5
    Then I should see 'Welcome to Codebreaker!' # features/codebreaker_starts_game.feature:10
    And I should see 'Enter guess:'             # features/codebreaker_starts_game.feature:11

1 scenario (1 undefined)
4 steps (2 undefined, 2 passed)
0m0.024s

You can implement step definitions for undefined steps with these snippets:

Then("I should see {string}") do |string|
  pending # Write code here that turns the phrase above into concrete actions
end
</pre></code>

With the second step passing, we can now move on to the `Then` steps. The last snippet is a single step definition that will handle both the `Then` and `And` steps in the scenario, passing whatever is captured by the `{string}` part to the block as the `message` parameter.

As for what to write in the block, when we say **I should see "Welcome to Codebreaker!"**, we're really saying **I should see "Welcome to Codebreaker!" in the console**, and that means we need a means of capturing messages that the `Game` sends to `STDOUT`.

The trick, of course, is that we're running `Cucumber` in the console, and it is already using `STDOUT`. We need a `fake object` that the `Game` thinks is `STDOUT`, but really just captures messages for us so we can set expectations about those messages.

A `fake object` that pretends to be `real object` is called a `test double`. You're probably familiar with `stubs` and `mocks`. `Test double` is a generic name for them, along with `fakes`, `spies`, and so on, and so on.

https://stackoverflow.com/questions/26748693/private-method-puts-cant-pass-cucumber-test-in-rspec-books-codebreaker-exam

....Given that we'll use a `test double` for output, here is what we want the step definition to look like

`ch4-automating-features-with-cucumber/features/step_definitions/codebreaker_steps.rb`:
```ruby
Given("I am not yet playing") do

end

When("I start a new game") do
  Codebreker::Game.new.start
end

Then("I should see {string}") do |string|
  fake_output.messages.should include(string)
end
```

Again, we're writing the code we wish we had so that we know what code to add. This line suggests that our fake objeect should have a `messages` collection. We'll also want it to have a `puts()` method that the `Game` can use.

Here's what that looks like

`ch4-automating-features-with-cucumber/features/step_definitions/codebreaker_steps.rb`:
```ruby
class FakeOutput
  def messages
    @messages ||= []
  end

  def puts(message)
    messages << message
  end
end

def fake_output
  @fake_output ||= FakeOutput.new
end

Given("I am not yet playing") do

end

When("I start a new game") do
  Codebreker::Game.new.start
end

Then("I should see {string}") do |string|
  fake_output.messages.should include(string)
end
```

The `fake_output()` method uses a caching technique called `memorization`. The first time `fake_output()` is called, it creates a `FakeOutput`, stores in in a `@fake_output` variable, and returns it. If it gets called again, it returns the same `FakeOutput` `object`.

Now we need to give the `Game` a reference to the `FakeOutput`. Modify the `When` step as follows

`ch4-automating-features-with-cucumber/features/step_definitions/codebreaker_steps.rb`:
```ruby
class FakeOutput
  def messages
    @messages ||= []
  end

  def puts(message)
    messages << message
  end
end

def fake_output
  @fake_output ||= FakeOutput.new
end

Given("I am not yet playing") do

end

When("I start a new game") do
  game = Codebreaker::Game.new(fake_output)
  game.start
end

Then("I should see {string}") do |string|
  fake_output.messages.should include(string)
end
```

Run `$ cucumber features/codebreaker_starts_game.feature` after making these modifications and additions to `codebreaker_steps.rb`. You should see the following output:
<pre><code>
$ <b>cucumber features/codebreaker_starts_game.feature</b>
Feature: code-breaker starts game
As a code-breaker
I want to start a game
So that I can break the code

  Scenario: start game                          # features/codebreaker_starts_game.feature:7
    Given I am not yet playing                  # features/step_definitions/codebreaker_steps.rb:15
    When I start a new game                     # features/step_definitions/codebreaker_steps.rb:19
      wrong number of arguments (given 1, expected 0) (ArgumentError)
      ./features/step_definitions/codebreaker_steps.rb:20:in `initialize'
      ./features/step_definitions/codebreaker_steps.rb:20:in `new'
      ./features/step_definitions/codebreaker_steps.rb:20:in `"I start a new game"'
      features/codebreaker_starts_game.feature:9:in `When I start a new game'
    Then I should see 'Welcome to Codebreaker!' # features/step_definitions/codebreaker_steps.rb:24
    And I should see 'Enter guess:'             # features/step_definitions/codebreaker_steps.rb:24

Failing Scenarios:
cucumber features/codebreaker_starts_game.feature:7 # Scenario: start game

1 scenario (1 failed)
4 steps (1 failed, 2 skipped, 1 passed)
0m0.041s
</pre></code>

We need to modify the game to accept the `fake_output` `object` passed to `new`

`ch4-automating-features-with-cucumber/lib/codebreaker/game.rb`:
```ruby
module Codebreaker
  class Game
    def initialize(output)
    end

    def start
    end
  end
end
```

Now run `$ cucumber features/codebreaker_starts_game.feature` again, and this time you should see this<pre><code>
$ <b>cucumber features/codebreaker_starts_game.feature</b>
Feature: code-breaker starts game
As a code-breaker
I want to start a game
So that I can break the code

  Scenario: start game                          # features/codebreaker_starts_game.feature:7
    Given I am not yet playing                  # features/step_definitions/codebreaker_steps.rb:15
    When I start a new game                     # features/step_definitions/codebreaker_steps.rb:19
DEPRECATION: Using `should` from rspec-expectations' old `:should` syntax without explicitly enabling the syntax is deprecated. Use the new `:expect` syntax or explicitly enable `:should` with `config.expect_with(:rspec) { |c| c.syntax = :should }` instead. Called from /Users/mikaelblomkvist/the-rspec-book/GettingStartedWithRSpecAndCucumber/ch4-automating-features-with-cucumber/features/step_definitions/codebreaker_steps.rb:25:in `block in <top (required)>'.
    Then I should see 'Welcome to Codebreaker!' # features/step_definitions/codebreaker_steps.rb:24
      expected [] to include "Welcome to Codebreaker!" (RSpec::Expectations::ExpectationNotMetError)
      ./features/step_definitions/codebreaker_steps.rb:25:in `"I should see {string}"'
      features/codebreaker_starts_game.feature:10:in `Then I should see 'Welcome to Codebreaker!''
    And I should see 'Enter guess:'             # features/step_definitions/codebreaker_steps.rb:24

Failing Scenarios:
cucumber features/codebreaker_starts_game.feature:7 # Scenario: start game

1 scenario (1 failed)
4 steps (1 failed, 1 skipped, 2 passed)
0m0.102s
</pre></code>

So far, all the failures we've seen have been because of exceptions and errors. We now have out first logical error, so it's time to add some behaviour to out `Game`. for that we're going to shift gears and jump over to `RSpec`.

### Chapter 5 Describing Code with RSepc

....we're going to use `RSpec` to `describe` behaviour at a much more granular level: the expected behaviour of insstances of the `Game` class.

1. ....create a `spec` directory, with a subdirectory named `codebreaker`. Now create a file named `game_spec.rb` in `spec/codebreker/`. Add the following to `game_spec.rb`

`spec/codebreker/game_spec.rb`:
```ruby
require 'spec_helper'

module Codebreaker
  describe Game do
    describe "#start" do
      it "sends a welcome message"
      it "prompts for the first guess"
    end
  end
end
```

....The second statement declares a Ruby module named `Codebreaker`. This isn't necessary in order to run the specs, but it provides some conveniences. For example, we don't have to fully qualify `Game` on line 4.

The `describe()` method hooks into `RSpec`'s `API` and returns a `subclass` of `RSpec::Core::ExampleGroup`. As its name suggests, this is a group of examples of the expected behaviour of an `object`.

The `it()` method creates an `example`. Technically, it's an instance of the `ExampleGroup` returned by `describe()`, but you really don't need to worry about that at this point.

2. Crate a file named `spec_helper.rb` in `spec`. Add the following to `spec_helper.rb`

`spec/spec_helper.rb`:
```ruby
require 'codebreaker'
```

....`spec/codebreaker/game_spec.rb` requires `spec/spec_helper.rb`, which requires  `lib/codebreaker.rb`, which in turn, requires `lib/codebreaker/game.rb`.

`cd` to the `codebreaker` project root directory, and run `game_spec.rb` file with the `rspec` command like this:

<pre><code>
$ <b>rspec spec/codebreaker/game_spec.rb --format doc</b>

Codebreaker::Game
  #start
    sends a welcome message (PENDING: Not yet implemented)
    prompts for the first guess (PENDING: Not yet implemented)

Pending: (Failures listed here are expected and do not affect your suite's status)

  1) Codebreaker::Game#start sends a welcome message
     # Not yet implemented
     # ./spec/codebreaker/game_spec.rb:6

  2) Codebreaker::Game#start prompts for the first guess
     # Not yet implemented
     # ./spec/codebreaker/game_spec.rb:7


Finished in 0.00156 seconds (files took 0.26873 seconds to load)
2 examples, 0 failures, 2 pending
</pre></code>

The `--format doc` option tells `RSpec` to format the output using the same nesting we see in the nested describe blocks in the file.

In `game_spec.rb`, we want to do what we've done in the feature: specify that when we start the game, it sends the right messages to the output. Start by modifying `game_spec.rb` as follows

`ch5-describing-code-with-rspec/spec/codebreaker/game_spec.rb`:
```ruby
require 'spec_helper'

module Codebreaker
  describe Game do
    describe "#start" do
      it "sends a welcome message" do
        output = double('output')
        game = Game.new(output)

        output.should_receive(:puts).with('Welcome to Codebreaker!')

        game.start
      end
      it "prompts for the first guess"
    end
  end
end
```

Just as we did in the scenario, we want a test double to stand in for the real `STDOUT`. Instead of rolling our own as we did in the scenario, however, we're using `RSpec`'s dynamic test double framework, `RSpec::Mock`, to create dynamic test double on the first line of the example.

The next line sets up a `message exception`: an expectation that the `output` `object` should receive the `puts` message with the string `"Welcome to Codebreaker!"` as its only argument. If it does, then the expectation will pass. If not, we'll get a failure.

We send the `game` the `start` message on the last line. The intent we're expressing is that when we call `game.start`, the `output` should receive `puts("Welome to Codebreaker")`.

Now we run the `rspec` command again, but this time use the `--color` flag:
<pre><code>
$ <b>rspec spec/codebreaker/game_spec.rb --color --format doc</b>

Codebreaker::Game
  #start
    sends a welcome message (FAILED - 1)
    prompts for the first guess (PENDING: Not yet implemented)

Pending: (Failures listed here are expected and do not affect your suite's status)

  1) Codebreaker::Game#start prompts for the first guess
     # Not yet implemented
     # ./spec/codebreaker/game_spec.rb:14


Failures:

  1) Codebreaker::Game#start sends a welcome message
     Failure/Error: output.should_receive(:puts).with('Welcome to Codebreaker!')

       (Double "output").puts("Welcome to Codebreaker!")
           expected: 1 time with arguments: ("Welcome to Codebreaker!")
           received: 0 times
     # ./spec/codebreaker/game_spec.rb:10:in `block (3 levels) in <module:Codebreaker>'

Deprecation Warnings:

Using `should_receive` from rspec-mocks' old `:should` syntax without explicitly enabling the syntax is deprecated. Use the new `:expect` syntax or explicitly enable `:should` instead. Called from /Users/mikaelblomkvist/the-rspec-book/GettingStartedWithRSpecAndCucumber/ch5-describing-code-with-rspec/spec/codebreaker/game_spec.rb:10:in `block (3 levels) in <module:Codebreaker>'.


If you need more of the backtrace for any of these deprecations to
identify where to make the necessary changes, you can configure
`config.raise_errors_for_deprecations!`, and it will turn the
deprecation warnings into errors, giving you the full backtrace.

1 deprecation warning total

Finished in 0.00243 seconds (files took 0.19568 seconds to load)
2 examples, 1 failure, 1 pending

Failed examples:

rspec ./spec/codebreaker/game_spec.rb:6 # Codebreaker::Game#start sends a welcome message
</pre></code>

The summary at the bottom of the output tells us we have one failure and one pending example. ....The failure message tells us that `output` never received `puts`. Here's what we need to do to get this example to pass:
`lib/codebreaker/game.rb`:
```ruby
module Codebreaker
  class Game
    def initialize(output)
      @output = output
    end

    def start
      @output.puts 'Welcome to Codebreaker!'
    end
  end
end
```

Make thos changes and run `rspec` command again, and you should see this:
<pre><code>
$ <b>rspec spec/codebreaker/game_spec.rb --color --format doc</b>

Codebreaker::Game
  #start
    sends a welcome message
    prompts for the first guess (PENDING: Not yet implemented)

Pending: (Failures listed here are expected and do not affect your suite's status)

  1) Codebreaker::Game#start prompts for the first guess
     # Not yet implemented
     # ./spec/codebreaker/game_spec.rb:14


Deprecation Warnings:

Using `should_receive` from rspec-mocks' old `:should` syntax without explicitly enabling the syntax is deprecated. Use the new `:expect` syntax or explicitly enable `:should` instead. Called from /Users/mikaelblomkvist/the-rspec-book/GettingStartedWithRSpecAndCucumber/ch5-describing-code-with-rspec/spec/codebreaker/game_spec.rb:10:in `block (3 levels) in <module:Codebreaker>'.


If you need more of the backtrace for any of these deprecations to
identify where to make the necessary changes, you can configure
`config.raise_errors_for_deprecations!`, and it will turn the
deprecation warnings into errors, giving you the full backtrace.

1 deprecation warning total

Finished in 0.00205 seconds (files took 0.19045 seconds to load)
2 examples, 0 failures, 1 pending
</pre></code>

We have our first passing example! We've gone from red to green. ....let's see whether we've had any impact on the features

<pre><code>
$ <b>cucumber features/codebreaker_starts_game.feature</b>
Feature: code-breaker starts game
As a code-breaker
I want to start a game
So that I can break the code

  Scenario: start game                          # features/codebreaker_starts_game.feature:7
    Given I am not yet playing                  # features/step_definitions/codebreaker_steps.rb:15
    When I start a new game                     # features/step_definitions/codebreaker_steps.rb:19
DEPRECATION: Using `should` from rspec-expectations' old `:should` syntax without explicitly enabling the syntax is deprecated. Use the new `:expect` syntax or explicitly enable `:should` with `config.expect_with(:rspec) { |c| c.syntax = :should }` instead. Called from /Users/mikaelblomkvist/the-rspec-book/GettingStartedWithRSpecAndCucumber/ch5-describing-code-with-rspec/features/step_definitions/codebreaker_steps.rb:25:in `block in <top (required)>'.
    Then I should see 'Welcome to Codebreaker!' # features/step_definitions/codebreaker_steps.rb:24
    And I should see 'Enter guess:'             # features/step_definitions/codebreaker_steps.rb:24
      expected ["Welcome to Codebreaker!"] to include "Enter guess:" (RSpec::Expectations::ExpectationNotMetError)
      ./features/step_definitions/codebreaker_steps.rb:25:in `"I should see {string}"'
      features/codebreaker_starts_game.feature:11:in `And I should see 'Enter guess:''

Failing Scenarios:
cucumber features/codebreaker_starts_game.feature:7 # Scenario: start game

1 scenario (1 failed)
4 steps (1 failed, 3 passed)
0m0.068s
</pre></code>

The following failing step is the next thing to work on: `And I should see "Enter guess:"`. Go ahead and add an example for the behaviour to `game_spec.rb`

`ch5-describing-code-with-rspec/spec/codebreaker/game_spec.rb`:
```ruby
require 'spec_helper'

module Codebreaker
  describe Game do
    describe "#start" do
      it "sends a welcome message" do
        output = double('output')
        game = Game.new(output)

        output.should_receive(:puts).with('Welcome to Codebreaker!')

        game.start
      end

      it "prompts for the first guess" do
        output = double('output')
        game = Game.new(output)

        output.should_receive(:puts).with('Enter guess:')

        game.start
      end
    end
  end
end
```

This is very similar to the first example, but we're expecting a differnet message. We'll come back to `DRY` that up in a bit, but first let's get it passing. Run the spec and watch it fail.

<pre><code>
$ <b>rspec spec/codebreaker/game_spec.rb --color --format doc</b>

Codebreaker::Game
  #start
    sends a welcome message
    prompts for the first guess (FAILED - 1)

Failures:

  1) Codebreaker::Game#start prompts for the first guess
     Failure/Error: @output.puts 'Welcome to Codebreaker!'

       #<Double "output"> received :puts with unexpected arguments
         expected: ("Enter guess:")
              got: ("Welcome to Codebreaker!")
     # ./lib/codebreaker/game.rb:8:in `start'
     # ./spec/codebreaker/game_spec.rb:21:in `block (3 levels) in <module:Codebreaker>'

Deprecation Warnings:

Using `should_receive` from rspec-mocks' old `:should` syntax without explicitly enabling the syntax is deprecated. Use the new `:expect` syntax or explicitly enable `:should` instead. Called from /Users/mikaelblomkvist/the-rspec-book/GettingStartedWithRSpecAndCucumber/ch5-describing-code-with-rspec/spec/codebreaker/game_spec.rb:10:in `block (3 levels) in <module:Codebreaker>'.


If you need more of the backtrace for any of these deprecations to
identify where to make the necessary changes, you can configure
`config.raise_errors_for_deprecations!`, and it will turn the
deprecation warnings into errors, giving you the full backtrace.

1 deprecation warning total

Finished in 0.03309 seconds (files took 0.18397 seconds to load)
2 examples, 1 failure

Failed examples:

rspec ./spec/codebreaker/game_spec.rb:15 # Codebreaker::Game#start prompts for the first guess
</pre></code>

`lib/codebreaker/game.rb`:
```ruby
module Codebreaker
  class Game
    def initialize(output)
      @output = output
    end

    def start
      @output.puts 'Welcome to Codebreaker!'
      @output.puts 'Enter guess:'
    end
  end
end
```

<pre><code>
$ <b>rspec spec/codebreaker/game_spec.rb --color --format doc</b>

Codebreaker::Game
  #start
    sends a welcome message (FAILED - 1)
    prompts for the first guess (FAILED - 2)

Failures:

  1) Codebreaker::Game#start sends a welcome message
     Failure/Error: @output.puts 'Enter guess:'

       #<Double "output"> received :puts with unexpected arguments
         expected: ("Welcome to Codebreaker!")
              got: ("Enter guess:")
     # ./lib/codebreaker/game.rb:9:in `start'
     # ./spec/codebreaker/game_spec.rb:12:in `block (3 levels) in <module:Codebreaker>'

  2) Codebreaker::Game#start prompts for the first guess
     Failure/Error: @output.puts 'Welcome to Codebreaker!'

       #<Double "output"> received :puts with unexpected arguments
         expected: ("Enter guess:")
              got: ("Welcome to Codebreaker!")
     # ./lib/codebreaker/game.rb:8:in `start'
     # ./spec/codebreaker/game_spec.rb:21:in `block (3 levels) in <module:Codebreaker>'

Deprecation Warnings:

Using `should_receive` from rspec-mocks' old `:should` syntax without explicitly enabling the syntax is deprecated. Use the new `:expect` syntax or explicitly enable `:should` instead. Called from /Users/mikaelblomkvist/the-rspec-book/GettingStartedWithRSpecAndCucumber/ch5-describing-code-with-rspec/spec/codebreaker/game_spec.rb:10:in `block (3 levels) in <module:Codebreaker>'.


If you need more of the backtrace for any of these deprecations to
identify where to make the necessary changes, you can configure
`config.raise_errors_for_deprecations!`, and it will turn the
deprecation warnings into errors, giving you the full backtrace.

1 deprecation warning total

Finished in 0.02637 seconds (files took 0.13237 seconds to load)
2 examples, 2 failures

Failed examples:

rspec ./spec/codebreaker/game_spec.rb:6 # Codebreaker::Game#start sends a welcome message
rspec ./spec/codebreaker/game_spec.rb:15 # Codebreaker::Game#start prompts for the first guess
</pre></code>

And ta-da! Now not only is the second example still failing, but the first example is failing as well! Who'da funk it? This may seem a bit confusing if you've never worked with `test doubles` and `message expectations` before, but they are not all that clever. Be default, they will expect exactly what you tell them to expect, nothing more and nothing less. We're told the double in the first example to `expect puts()` with `"Welcome to Codebreaker!"` and we've satisfied that requirement, but we've only told it to expect `"Welcome to Codebreaker!" It doesn't know anything about `"Enter guess:"

Similarly, the `double` in the second example expects `"Enter guess:"` but the first message it gets is "Welcome to Codebreaker!"

We could combine these two into a single example, but we like to follow the guideline of `"one expectation per example"` The rationale here is that if there are two expectations in an example that should both fail given the implementation at that moment, we'll only see the first failure. No sooner do we meet that expectation than we discovered that we haven't met the second expectation. If they live in separate examples, then they'll both fail, and that will provide us with more accurate information than if only one of them is failing.

We could also try to break the message up into different steps, but we've already defined how we want to talk to the `game` `object`. So how can we resolve this?

There are a couple of ways we can go about it, but the simplest way is to tell the `double output` to only listen for the messages we tell it to expect and ignore any other messages. This is based on the `Null Object design` pattern described in Pattern Languages of Program Design 3 and is supported by `RSpec`'s double framework with the `as_null_object()` `method`.

`ch5-describing-code-with-rspec/spec/codebreaker/game_spec.rb`:
```ruby
require 'spec_helper'

module Codebreaker
  describe Game do
    describe "#start" do
      it "sends a welcome message" do
        output = double('output').as_null_object
        game = Game.new(output)

        output.should_receive(:puts).with('Welcome to Codebreaker!')

        game.start
      end

      it "prompts for the first guess" do
        output = double('output').as_null_object
        game = Game.new(output)

        output.should_receive(:puts).with('Enter guess:')

        game.start
      end
    end
  end
end
```

Run the `rspec` command again, and you should see this:
<pre><code>
$ <b>rspec spec/codebreaker/game_spec.rb --color --format doc</b>

Codebreaker::Game
  #start
    sends a welcome message
    prompts for the first guess

Deprecation Warnings:

Using `should_receive` from rspec-mocks' old `:should` syntax without explicitly enabling the syntax is deprecated. Use the new `:expect` syntax or explicitly enable `:should` instead. Called from /Users/mikaelblomkvist/the-rspec-book/GettingStartedWithRSpecAndCucumber/ch5-describing-code-with-rspec/spec/codebreaker/game_spec.rb:10:in `block (3 levels) in <module:Codebreaker>'.


If you need more of the backtrace for any of these deprecations to
identify where to make the necessary changes, you can configure
`config.raise_errors_for_deprecations!`, and it will turn the
deprecation warnings into errors, giving you the full backtrace.

1 deprecation warning total

Finished in 0.00236 seconds (files took 0.19509 seconds to load)
2 examples, 0 failures
</pre></code>

....Now that we have green it's time to refactor.

....Martin Fowler writes, "Refactoring is the process of changing a software system in such a way that it does not alter the external behaviour of the code yet improves its internal structure."

How do we know that we're not changing behaviour? We run the examples between every change. If they pass, we've refactored successfully. If any fail, we know that the very last change we made caused problem that step back to green and try again.

In this case, we have a very clear break between what is context and what is behaviour, so let's take advantage of that and move the context to a block that is executed before each of the examples. Modify `game_spec.rb`.

`ch5-describing-code-with-rspec/spec/codebreaker/game_spec.rb`:
```ruby
require 'spec_helper'

module Codebreaker
  describe Game do
    describe "#start" do
      before(:each) do
        @output = double('output').as_null_object
        @game = Game.new(@output)
      end

      it "sends a welcome message" do
        @output.should_receive(:puts).with('Welcome to Codebreaker!')
        @game.start
      end

      it "prompts for the first guess" do
        @output.should_receive(:puts).with('Enter guess:')
        @game.start
      end
    end
  end
end
```

Just as you might expect from reading this, the block passed to `before(:each)` will be run before each example. The before block and the example are executed in the same `object`, so they have access to the same instance variables.

<pre><code>
$ <b>rspec spec/codebreaker/game_spec.rb --color --format doc</b>

Codebreaker::Game
  #start
    sends a welcome message
    prompts for the first guess

Deprecation Warnings:

Using `should_receive` from rspec-mocks' old `:should` syntax without explicitly enabling the syntax is deprecated. Use the new `:expect` syntax or explicitly enable `:should` instead. Called from /Users/mikaelblomkvist/the-rspec-book/GettingStartedWithRSpecAndCucumber/ch5-describing-code-with-rspec/spec/codebreaker/game_spec.rb:12:in `block (3 levels) in <module:Codebreaker>'.


If you need more of the backtrace for any of these deprecations to
identify where to make the necessary changes, you can configure
`config.raise_errors_for_deprecations!`, and it will turn the
deprecation warnings into errors, giving you the full backtrace.

1 deprecation warning total

Finished in 0.00221 seconds (files took 0.12305 seconds to load)
2 examples, 0 failures
</pre></code>

Adding all of those `@` symbols can be tedious and error prone, so `RSpec` offers an alternative approach.

When the code in a `before` block is only creating `instance variables` and assigning them values, which is most of the time, we can use `RSpec`'s `let()` method instead. `let()` takes a symbol representing a method name and a block, which represents the implementation of that `method`.

`ch5-describing-code-with-rspec/spec/codebreaker/game_spec.rb`:
```ruby
require 'spec_helper'

module Codebreaker
  describe Game do
    describe "#start" do
      let(:output) { double('output').as_null_object }
      let(:game)   { Game.new(output) }

      it "sends a welcome message" do
        output.should_receive(:puts).with('Welcome to Codebreaker!')
        game.start
      end

      it "prompts for the first guess" do
        output.should_receive(:puts).with('Enter guess:')
        game.start
      end
    end
  end
end
```

<pre><code>
$ <b>rspec spec/codebreaker/game_spec.rb --color --format doc</b>

Codebreaker::Game
  #start
    sends a welcome message
    prompts for the first guess

Deprecation Warnings:

Using `should_receive` from rspec-mocks' old `:should` syntax without explicitly enabling the syntax is deprecated. Use the new `:expect` syntax or explicitly enable `:should` instead. Called from /Users/mikaelblomkvist/the-rspec-book/GettingStartedWithRSpecAndCucumber/ch5-describing-code-with-rspec/spec/codebreaker/game_spec.rb:10:in `block (3 levels) in <module:Codebreaker>'.


If you need more of the backtrace for any of these deprecations to
identify where to make the necessary changes, you can configure
`config.raise_errors_for_deprecations!`, and it will turn the
deprecation warnings into errors, giving you the full backtrace.

1 deprecation warning total

Finished in 0.00415 seconds (files took 0.16234 seconds to load)
2 examples, 0 failures
</pre></code>

The first call to `let()` defines a memorized `output()` method that returns a `double` `object`. Memorized means that the first time the method is invoked, the return value is `cached` and that same value is returned every subsequesnt time the method is invoked within the same scope. That fact doesn't affect our current example, but it will come in handy a bit later.

Now run the feature again.

<pre><code>
$ <b>cucumber features/codebreaker_starts_game.feature</b>
Feature: code-breaker starts game
As a code-breaker
I want to start a game
So that I can break the code

  Scenario: start game                          # features/codebreaker_starts_game.feature:7
    Given I am not yet playing                  # features/step_definitions/codebreaker_steps.rb:15
    When I start a new game                     # features/step_definitions/codebreaker_steps.rb:19
DEPRECATION: Using `should` from rspec-expectations' old `:should` syntax without explicitly enabling the syntax is deprecated. Use the new `:expect` syntax or explicitly enable `:should` with `config.expect_with(:rspec) { |c| c.syntax = :should }` instead. Called from /Users/mikaelblomkvist/the-rspec-book/GettingStartedWithRSpecAndCucumber/ch5-describing-code-with-rspec/features/step_definitions/codebreaker_steps.rb:25:in `block in <top (required)>'.
    Then I should see 'Welcome to Codebreaker!' # features/step_definitions/codebreaker_steps.rb:24
    And I should see 'Enter guess:'             # features/step_definitions/codebreaker_steps.rb:24

1 scenario (1 passed)
4 steps (4 passed)
0m0.034s
</pre></code>

....now we have our first passing code examples and our first passing feature. There were a lot of steps to get there, but in practise this all really takes just a few minutes, even with all the wiring and `require` statements.

We've also set up quite a bit of infrastructure. You'll see, as we move along, that there is is and less new material need to add more features, code examples, and application code. It just builds gradually on what we've already developed.

Now that we have a passing feature, it would be nice to see it in action. For that, we'll need to create and execute a simple script. Create a `bin` in the project root directory, and add a `bin/codebreaker` file. If you're on a `*nix` system, enter this code in that file:

`bin/codebreaker`:
```ruby
#!/usr/bin/env ruby
$LOAD_PATH.unshift File.expand_path('../../lib', __FILE__)
require 'codebreaker'

game = Codebreaker::Game.new(STDOUT)
game.start
```

If you're on `*nix`, now run `chmod+x bin/codebreaker` so we can execute it and then run this:

<pre><code>
$ <b>bin/codebreaker</b>
Welcome to Codebreaker!
Enter guess:
</pre></code>
