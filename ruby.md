# Ruby Style Guide

Most of the guidelines were taken from https://github.com/bbatsov/ruby-style-guide
and github's [ruby style guide](https://github.com/styleguide/ruby). If
you find any particular patterns or styles that should be included in
this guide, please submit a pull request.

## Codings Style

* Never leave trailing whitespace.

* Use two spaces per indentation level. No hard tabs.

* Keep lines fewer than 80 characters.

* End each file with a blank newline.

* Use spaces around operators, after commas, colons and semicolons, around `{`
  and before `}`.

  ```ruby
  sum = 1 + 2
  a, b = 1, 2
  [1, 2, 3].each { |e| puts e }
  class FooError < StandardError; end
  ```

  For hashes two different styles are fine:

  ```ruby
  # space after { and before }
  { one: 1, two: 2 }

  # no space after { and before }
  {one: 1, two: 2}
  ```

* No spaces after `(`, `[` or before `]`, `)`.

  ```Ruby
  some(arg).other
  [1, 2, 3].size
  ```

* No space after `!`.

  ```Ruby
  !something.include?(other)
  ```

* Indent `when` as deep as `case`.

  ```Ruby
  case
  when song.name == 'Misty'
    puts 'Not again!'
  when song.duration > 120
    puts 'Too long!'
  when Time.now.hour > 21
    puts "It's too late"
  else
    song.play
  end

  kind = case year
         when 1850..1889 then "Blues"
         when 1890..1909 then "Ragtime"
         when 1910..1929 then "New Orleans Jazz"
         when 1930..1939 then "Swing"
         when 1940..1950 then "Bebop"
         else "Jazz"
         end
  ```

* Use empty lines between method definitions and also to break up a method
  into logical paragraphs internally.

  ```Ruby
  def some_method
    data = initialize(options)

    data.manipulate!

    data.result
  end

  def some_method
    result
  end
  ```

* Use spaces around the `=` operator when assigning default values to method
  parameters:

  ```Ruby
  def some_method(arg1 = :default, arg2 = nil, arg3 = [])
    # do something...
  end
  ```

* When continuing a chained method invocation on another line keep the `.`
  on the second line.

    ```Ruby
    one.two.three
      .four
    ```

* Align the parameters of a method call if they span more than one
  line. When aligning parameters is not appropriate due to line-length
  constraints, single indent for the lines after the first is also
  acceptable.

  ```Ruby
  def send_mail(source)
    Mailer.deliver(to: 'bob@example.com',
                   from: 'us@example.com',
                   subject: 'Important message',
                   body: source.text)
  end

  def send_mail(source)
    Mailer.deliver(
      to: 'bob@example.com',
      from: 'us@example.com',
      subject: 'Important message',
      body: source.text
    )
  end
  ```

## Syntax

* Use `def` with parentheses when there are parameters. Omit the
  parentheses when the method doesn't accept any parameters.

   ```Ruby
   def some_method
     # body omitted
   end

   def some_method_with_parameters(param1, param2)
     # body omitted
   end
   ```

* Never use `for`. Most of the time iterators should be used instead.
  `for` is implemented in terms of `each` (so you're adding a level of
  indirection), but with a twist - `for` doesn't introduce a new scope (unlike
  `each`) and variables defined in its block will be visible outside it.

  ```Ruby
  arr = [1, 2, 3]

  # bad
  for elem in arr do
    puts elem
  end

  # note that elem is accessible outside of the for loop
  elem # => 3

  # good
  arr.each { |elem| puts elem }

  # elem is not accessible outside each's block
  elem # => NameError: undefined local variable or method `elem'
  ```

* Never use `then` for multi-line `if/unless`.

  ```Ruby
  # bad
  if some_condition then
    # body omitted
  end

  # good
  if some_condition
    # body omitted
  end
  ```

* Avoid the ternary operator (?:) except in cases where all expressions are
  extremely trivial. However, do use the ternary operator(?:) over
  if/then/else/end constructs for single line conditionals.

  ```Ruby
  # bad
  result = if some_condition then something else something_else end

  # good
  result = some_condition ? something : something_else
  ```

* Avoid multi-line `?:` (the ternary operator); use `if/unless` instead.

* Use one expression per branch in a ternary operator. This
  also means that ternary operators must not be nested. Prefer
  `if/else` constructs in these cases.

  ```Ruby
  # bad
  some_condition ? (nested_condition ? nested_something : nested_something_else) : something_else

  # good
  if some_condition
    nested_condition ? nested_something : nested_something_else
  else
    something_else
  end
  ```

* Leverage the fact that `if` and `case` are expressions which return a
  result.

  ```Ruby
  # bad
  if condition
    result = x
  else
    result = y
  end

  # good
  result =
    if condition
      x
    else
      y
    end
  ```

* The `and` and `or` keywords are banned. It's just not worth it. Always use
  `&&` and `||` instead.

* Favor modifier `if/unless` usage when you have a single-line body. Another
  good alternative is the usage of control flow `&&/||`.

  ```Ruby
  # bad
  if some_condition
    do_something
  end

  # good
  do_something if some_condition

  # another good option
  some_condition && do_something
  ```

* Favor `unless` over `if` for negative conditions (or control flow `||`).

  ```Ruby
  # bad
  do_something if !some_condition

  # bad
  do_something if not some_condition

  # good
  do_something unless some_condition

  # another good option
  some_condition || do_something
  ```

* Never use `unless` with `else`. Rewrite these with the positive case first.

  ```Ruby
  # bad
  unless success?
    puts 'failure'
  else
    puts 'success'
  end

  # good
  if success?
    puts 'success'
  else
    puts 'failure'
  end
  ```

* Don't use parentheses around the condition of an `if/unless/while/until`.

  ```Ruby
  # bad
  if (x > 10)
    # body omitted
  end

  # good
  if x > 10
    # body omitted
  end
  ```

* Prefer `{...}` over `do...end` for single-line blocks. Avoid using `{...}`
  for multi-line blocks (multiline chaining is always ugly). Always use
  `do...end` for "control flow" and "method definitions" (e.g. in Rakefiles and
  certain DSLs). Avoid `do...end` when chaining.

  ```Ruby
  names = %w(Bozhidar Steve Sarah)

  # bad
  names.each do |name|
    puts name
  end

  # good
  names.each { |name| puts name }

  # bad
  names.select do |name|
    name.start_with?('S')
  end.map { |name| name.upcase }

  # good
  names.select { |name| name.start_with?('S') }.map(&:upcase)
  ```

  Some will argue that multiline chaining would look OK with the use of {...},
  but they should ask themselves - is this code really readable and can the
  blocks' contents be extracted into nifty methods?

* Avoid `return` where not required for flow of control.

  ```Ruby
  # bad
  def some_method(some_arr)
    return some_arr.size
  end

  # good
  def some_method(some_arr)
    some_arr.size
  end
  ```

* Never put a space between a method name and the opening parenthesis.

  ```Ruby
  # bad
  f (3 + 2) + 1

  # good
  f(3 + 2) + 1
  ```

* If the first argument to a method begins with an open parenthesis, always
  use parentheses in the method invocation. For example, write `f((3 + 2) + 1)`.

* It's ok to use the return value of `=` (an assignment) in conditional
  expressions.

  ```Ruby
  # bad
  if (v = array.grep(/foo/))
    ...
  end

  # good
  if v = array.grep(/foo/)
    ...
  end

  # good - operator precedence
  if (v = some_value) == other_value
    ...
  end
  ```

* Use `||=` freely to initialize variables.

  ```Ruby
  # bad
  name = name ? name : 'Bozhidar'

  # bad
  name = 'Bozhidar' unless name

  # good - set name to Bozhidar, only if it's nil or false
  name ||= 'Bozhidar'
  ```

* Never use `||=` to initialize boolean variables. (Consider what would happen
  if the current value happened to be `false`.)

  ```Ruby
  # bad - would set enabled to true even if it was false
  enabled ||= true

  # good
  enabled = true if enabled.nil?
  ```

* Avoid explicit use of the case equality operator `===`. As its name implies
  it is meant to be used implicitly by `case` expressions and outside of them it
  yields some pretty confusing code.

  ```Ruby
  # bad
  Array === something
  (1..100) === 7
  /something/ === some_string

  # good
  something.is_a?(Array)
  (1..100).include?(7)
  some_string =~ /something/
  ```

* Avoid using Perl-style special variables (like `$:`, `$;`, etc. ). They are
  quite cryptic and their use in anything but one-liner scripts is discouraged.
  Use the human-friendly aliases provided by the `English` library.

  ```Ruby
  # bad
  $:.unshift File.dirname(__FILE__)

  # good
  require 'English'
  $LOAD_PATH.unshift File.dirname(__FILE__)
  ```


* Omit the parameter parentheses when defining a stabby lambda with
  no parameters.

  ```Ruby
  # bad
  l = ->() { something }

  # good
  l = -> { something }
  ```

* Use `_` for unused block parameters.

  ```Ruby
  # bad
  result = hash.map { |k, v| v + 1 }

  # good
  result = hash.map { |_k, v| v + 1 }
  ```

* Don't do explicit non-`nil` checks unless you're dealing with boolean
  values.

    ```ruby
    # bad
    do_something if !something.nil?
    do_something if something != nil

    # good
    do_something if something

    # good - dealing with a boolean
    def value_set?
      !@some_boolean.nil?
    end
    ```

* Avoid use of nested conditionals for flow of control.

  Prefer a guard clause when you can assert invalid data. A guard clause
  is a conditional statement at the top of a function that bails out as
  soon as it can.

  ```Ruby
  # bad
  def compute_thing(thing)
    if thing[:foo]
      update_with_bar(thing)
      if thing[:foo][:bar]
        partial_compute(thing)
      else
        re_compute(thing)
      end
    end
  end

  # good
  def compute_thing(thing)
    return unless thing[:foo]
    update_with_bar(thing[:foo])
    return re_compute(thing) unless thing[:foo][:bar]
    partial_compute(thing)
  end
  ```

  Prefer `next` in loops instead of conditional blocks.

  ```Ruby
  # bad
  [0, 1, 2, 3].each do |item|
    if item > 1
      puts item
    end
  end

  # good
  [0, 1, 2, 3].each do |item|
    next unless item > 1
    puts item
  end
  ```

* Prefer `map` over `collect`, `find` over `detect`, `select` over `find_all`,
  `reduce` over `inject` and `size` over `length`. This is not a hard
  requirement; if the use of the alias enhances readability, it's ok to use it.
  The rhyming methods are inherited from Smalltalk and are not common in other
  programming languages. The reason the use of `select` is encouraged over
  `find_all` is that it goes together nicely with `reject` and its name is
  pretty self-explanatory.

## Naming

* Name identifiers in English.

* Use `snake_case` for symbols, methods and variables.

* Use `CamelCase` for classes and modules.  (Keep acronyms like HTTP, RFC, XML
  uppercase.)

* Use `SCREAMING_SNAKE_CASE` for other constants.

* The names of predicate methods (methods that return a boolean value) should
  end in a question mark.  (i.e. `Array#empty?`).

* The names of potentially *dangerous* methods (i.e. methods that modify
  `self` or the arguments, `exit!` (doesn't run the finalizers like `exit`
  does), etc.) should end with an exclamation mark if there exists a safe
  version of that *dangerous* method.

## Comments

* Write self-documenting code! Make sure that others can read and
  understand your code easily. Follow the [TRUE](http://designisrefactoring.com/2015/02/08/introducing-sandi-metz-true/)
  heuristic where ever possible.

* Keep annotations to a minimum and never annotate blocks...

## Classes & Modules

* Avoid the usage of class (`@@`) variables due to their "nasty" behavior in
  inheritance.

  ```Ruby
  class Parent
    @@class_var = 'parent'

    def self.print_class_var
      puts @@class_var
    end
  end

  class Child < Parent
    @@class_var = 'child'
  end

  Parent.print_class_var # => will print "child"
  ```

  As you can see all the classes in a class hierarchy actually share one
  class variable. Class instance variables should usually be preferred
  over class variables.

* Indent the `public`, `protected`, and `private` methods as much as the method
  definitions they apply to. Leave one blank line above the visibility modifier
  and one blank line below in order to emphasize that it applies to all methods
  below it.

  ```Ruby
  class SomeClass
    def public_method
      # ...
    end

    private

    def private_method
      # ...
    end

    def another_private_method
      # ...
    end
  end
  ```

* Use `def self.method` to define class methods. This makes the code
  easier to refactor since the class name is not repeated.

  ```Ruby
  class TestClass
    # bad
    def TestClass.some_method
      # body omitted
    end

    # good
    def self.some_other_method
      # body omitted
    end
  end
  ```

* Avoid `class << self` except when neccessary, e.g. single accessors
  and aliased attributes.

  ```Ruby
  class TestClass
      # bad
      class << self
        def first_method
          # body omitted
        end

        def second_method_etc
          # body omitted
        end
      end

      # good
      class << self
        attr_accessor :per_page
        alias_method :nwo, :find_by_name_with_owner
      end

      def self.first_method
        # body omitted
      end

      def self.second_method_etc
        # body omitted
      end
    end
  ```

* Avoid explicit use of `self` as the recipient of internal class or instance
  messages unless to specify a method shadowed by a variable.


## Exceptions

* Signal exceptions using the `fail` method. Use `raise` only when catching an
  exception and re-raising it (because here you're not failing, but explicitly
  and purposefully raising an exception).

  ```Ruby
  begin
    fail 'Oops'
  rescue => error
    raise if error.message != 'Oops'
  end
  ```

* Don't suppress exceptions.

  ```Ruby
  # bad
  begin
    # an exception occurs here
  rescue SomeError
    # the rescue clause does absolutely nothing
  end

  # bad
  do_something rescue nil
  ```

* Don't use exceptions for flow of control.

  ```Ruby
  # bad
  begin
    n / d
  rescue ZeroDivisionError
    puts 'Cannot divide by 0!'
  end

  # good
  if d.zero?
    puts 'Cannot divide by 0!'
  else
    n / d
  end
  ```

* Avoid rescuing the `Exception` class.  This will trap signals and calls to
  `exit`, requiring you to `kill -9` the process.

  ```Ruby
  # bad
  begin
    # an exception occurs here
  rescue
    # exception handling
  end

  # still bad
  begin
    # an exception occurs here
  rescue Exception
    # exception handling
  end
  ```

## Collections

* Prefer `%w` to the literal array syntax when you need an array of
  strings.

  ```Ruby
  # bad
  STATES = ['draft', 'open', 'closed']

  # good
  STATES = %w(draft open closed)
  ```

* Use `Set` instead of `Array` when dealing with unique elements. `Set` 
  implements a collection of unordered values with no duplicates. 
  This is a hybrid of `Array`'s intuitive inter-operation facilities and Hash's 
  fast lookup.

* Use symbols instead of strings as hash keys.

  ```Ruby
  # bad
  hash = { 'one' => 1, 'two' => 2, 'three' => 3 }

  # good
  hash = { one: 1, two: 2, three: 3 }
  ```

* Never mix the Ruby 1.9 hash syntax with hash rockets in the same hash
  literal. When you've got keys that are not symbols stick to the hash rockets
  syntax.

  ```Ruby
  # bad
  { a: 1, 'b' => 2 }

  # good
  { :a => 1, 'b' => 2 }
  ```

* Use `Hash#fetch` when dealing with hash keys that should be present.

  ```Ruby
  heroes = { batman: 'Bruce Wayne', superman: 'Clark Kent' }

  # bad - if we make a mistake we might not spot it right away
  heroes[:batman] # => "Bruce Wayne"
  heroes[:supermann] # => nil

  # good - fetch raises a KeyError making the problem obvious
  heroes.fetch(:supermann)
  ```

* Introduce default values for hash keys via `Hash#fetch` as opposed to using
  custom logic.

  ```Ruby
  batman = { name: 'Bruce Wayne', is_evil: false }

  # bad - if we just use || operator with falsy value we won't get the expected result
  batman[:is_evil] || true # => true

  # good - fetch work correctly with falsy values
  batman.fetch(:is_evil, true) # => false
  ```

## Strings

* Prefer string interpolation and string formatting instead of string
  concatenation.

  ```Ruby
  # bad
  email_with_name = user.name + ' <' + user.email + '>'

  # good
  email_with_name = "#{user.name} <#{user.email}>"
  ```

* Prefer double-quotes unless your string literal contains `"`. Feel
  free to escape characters.

* Avoid using `String#+` when you need to construct large data chunks.
  Instead, use `String#<<`. Concatenation mutates the string instance in-place
  and is always faster than `String#+`, which creates a bunch of new string
  objects.

  ```Ruby
  # good and also fast
  html = ''
  html << '<h1>Page title</h1>'

  paragraphs.each do |paragraph|
    html << "<p>#{paragraph}</p>"
  end
  ```

## Regular Expressions

* Avoid using the cryptic Perl-legacy variables denoting last regexp group
  matches (`$1`, `$2`, etc). Use named groups instead.

  ```Ruby
  # bad
  /(regexp)/ =~ string
  ...
  process $1

  # good
  /(?<meaningful_var>regexp)/ =~ string
  ...
  process meaningful_var
  ```

* Be careful with `^` and `$` as they match start/end of line, not string
  endings.  If you want to match the whole string use: `\A` and `\z` (not to be
  confused with `\Z` which is the equivalent of `/\n?\z/`).

  ```Ruby
  string = "some injection\nusername"
  string[/^username$/]   # matches
  string[/\Ausername\z/] # doesn't match
  ```

* Use `x` modifier for complex regexps. This makes them more readable and you
  can add some useful comments. Just be careful as spaces are ignored.

  ```Ruby
  regexp = /
    start         # some text
    \s            # white space char
    (group)       # first group
    (?:alt1|alt2) # some alternation
    end
  /x
  ```
## Percent Literals

* Use `%()`(it's a shorthand for `%Q`) for single-line strings which require
  both interpolation and embedded double-quotes. For multi-line strings, prefer
  heredocs.

  ```Ruby
  # bad (no interpolation needed)
  %(<div class="text">Some text</div>)
  # should be '<div class="text">Some text</div>'

  # bad (no double-quotes)
  %(This is #{quality} style)
  # should be "This is #{quality} style"

  # bad (multiple lines)
  %(<div>\n<span class="big">#{exclamation}</span>\n</div>)
  # should be a heredoc.

  # good (requires interpolation, has quotes, single line)
  %(<tr><td class="name">#{name}</td>)
  ```

* Use `%r` only for regular expressions matching at least one '/'
  character.

  ```Ruby
  # bad
  %r{\s+}

  # good
  %r{^/(.*)$}
  %r{^/blog/2011/(.*)$}
  ```

## Keyword Arguments

Keyword arguments are recommended but not required when a method's arguments 
may otherwise be opaque or non-obvious when called. Additionally, prefer them 
over the old "Hash as pseudo-named args" style from pre-2.0 ruby.

Instead of: 

```ruby
def remove_member(user, skip_membership_check=false)
  # ...
end

# Elsewhere: what does true mean here?
remove_member(user, true)
```

Do this, which is much nicer to read.

```ruby
def remove_member(user, skip_membership_check: false)
  # ...
end

# Elsewhere, now with more clarity:
remove_member user, skip_membership_check: true
```
