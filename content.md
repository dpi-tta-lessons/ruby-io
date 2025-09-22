# Ruby I/O

Reading and Writing Data

## Goal

Today you'll learn how to use Ruby's I/O system—the tools Ruby gives you to read information in and send information out. By the end, you'll be able to open a CSV file, transform its data, and save the results again—like a mini data pipeline (ETL: Extract → Transform → Load).

## What Is I/O?

I/O stands for Input/Output.

Input is data coming into your program (like typing into the keyboard, or reading from a file).

Output is data going out of your program (like printing to the screen, or saving to a file).

Every computer program is basically about moving data in and out.

<aside class="tip">
  Historically, early computers had no screens, only punch cards (input) and printed paper (output). Modern I/O feels fancier, but the idea is the same: get data, process it, return something useful.
</aside>

## Why I/O Matters

Without I/O, a program is like someone talking to themselves—you can't give it instructions, and you'll never see a result.

Learning I/O lets you:

- Read data from files (like logs or spreadsheets).
- Save results for later use.
- Build tools that interact with real-world data.

## What Is a CSV?

CSV stands for Comma-Separated Values. It's just a plain text file that stores data in a table-like format.

```csv
name,age,city
Alice,30,New York
Bob,25,Chicago
```

Each row is a record. Each column is separated by a comma. Think of CSV as a "spreadsheet without Excel."

## What Is ETL?

ETL stands for Extract, Transform, Load:

- **Extract**: pull data from somewhere (like a CSV file).
- **Transform**: change or clean it (capitalize names, calculate new values, etc).
- **Load**: put the result somewhere new (print to the screen, or save to a file).

This is a common pattern in data engineering and analytics. You'll try a mini-version today.

## What You'll Build

Here's a sneak peek at the kind of program you'll write.

```ruby
require "csv"

CSV.foreach("data.csv", headers: true) do |row|
  puts "Name: #{row['name']}, Age doubled: #{row['age'].to_i * 2}"
end
```
{: .repl }

This script extracts data from a CSV file, transforms the age, and loads it to the console.

## 1. Meet Ruby's IO

Ruby uses an [IO class](https://docs.ruby-lang.org/en/master/IO.html) to handle input/output, think of it as a stream of data. [File](https://docs.ruby-lang.org/en/master/File.html) is a subclass of `IO`, so everything you do with files uses the same foundation Ruby uses for console input and output.

<aside class="tip">
  If <code>puts</code> and <code>gets</code> feel familiar, that's because they are shorthand for working with Ruby's `STDOUT` (output stream) and `STDIN` (input stream).
</aside>

## 2. Reading a File

Open a file with `File.open` and pass in a block. Ruby will automatically close the file when the block ends.

```ruby
File.open("example.txt", "r") do |file|
  file.each_line do |line|
    puts line
  end
end
```

### Why this works

- `"r"` means "read mode."
- `file.each_line` loops through each line without loading the entire file into memory.

## 3. Writing to a File

You can also create or append to files:

- `"w"` overwrites
- `"a"` appends

```ruby
File.open("log.txt", "a") do |file|
  file.puts "New log entry at #{Time.now}"
end
```

## 4. File and Directory Tools

Ruby has helpers to check on files and folders:

```ruby
puts File.exist?("data.csv")   # true or false
puts File.size("data.csv")     # file size in bytes
puts Dir.pwd                   # current directory
```

<aside class="warning">
  Forgetting to close files can cause weird errors. Always use a block form (<code>File.open do |f| ... end</code>) to stay safe.
</aside>

## 5. Using ARGV for Command-Line Arguments

So far we've hardcoded filenames like "data.csv". What if we want to pass a filename when we run the script?

Ruby provides `ARGV` (argument vector), an array that stores values passed from the command line.

```ruby
# filename: reader.rb
filename = ARGV[0]    # first command-line argument
puts "Reading from #{filename}..."

File.open(filename, "r") do |file|
  puts file.readline
end
```

Let's say we have a file `example.txt` with this content:

```txt
This is the first line of example.txt
```

We can run the `reader.rb` script like this in the terminal:

`ruby reader.rb example.txt`

We'd get this output:

```
Reading from example.txt...
This is the first line of example.txt
```

### Why this works

`ARGV[0]` is the first argument after the script name. This makes your program flexible. You can pass different files without editing your code.

<aside class="tip">
  You can pass multiple arguments (like input and output filenames) using <code>ARGV[1]</code>, <code>ARGV[2]</code>, etc.
</aside>

## 6. Reading CSV Files

Ruby's standard library has a built-in [CSV class](https://ruby.github.io/csv/).

```ruby
require "csv"

filename = ARGV[0] || "data.csv"  # default if no argument
CSV.foreach(filename, headers: true) do |row|
  puts row.inspect
end
```

<aside class="tip">
  We need to call <code>require "csv"</code> because the CSV class is part of Ruby's standard library (not core). Calling <code>require</code> imports the CSV class code into our script.
</aside>

## 7. A Mini ETL

Now let's extract, transform, and load data (ETL).

```ruby
require "csv"

filename = ARGV[0] || "data.csv"

CSV.foreach(filename, headers: true) do |row|
  name = row["name"]
  age = row["age"].to_i
  puts "#{name.upcase} will be #{age + 10} years old in 10 years."
end
```

<!-- 
Practice Challenge

Modify the ETL script:

Add a new transformation: write names in lowercase.

Write the transformed rows into a new CSV file called output.csv.

Bonus: Let the user pass the output filename as the second argument (ARGV[1]).

-->

## Wrap-Up

- I/O means moving data in and out of your program.
- `ARGV` lets you pass information (like filenames) when starting a script.
- CSV is a simple table-like format for storing data.
- ETL is a common data workflow: Extract → Transform → Load.
- In Ruby, IO, File, ARGV, and CSV give you everything you need to handle real-world data.

## Quiz

- What does ARGV[0] represent?
- The script name
  - Not correct. That's $0 in Ruby.
- The first command-line argument
  - Correct!
- The last line of a file
  - Not correct.
{: .choose_best #argv_basics title="ARGV Basics" answer="2" }

<!-- 
1. Introduction to I/O in Ruby

What Input/Output means in programming.

The role of streams (STDIN, STDOUT, STDERR).

Difference between console I/O and file I/O.

2. Console Input and Output

Printing to the console: puts, print, p.

Reading from the console: gets, chomp, ARGV (command-line arguments).

Practical examples (echo program, user prompts).

3. Working with Files

Opening files with File.open and modes ("r", "w", "a", "r+").

Reading files: read, readline, readlines, iteration with each_line.

Writing files: write, puts, print.

Closing files vs using blocks (File.open("file.txt", "r") do |f| ... end).

4. File and Directory Operations

File class methods (exist?, size, rename, delete).

Dir class (entries, foreach, pwd, chdir).

Using Pathname for cleaner path manipulation.

5. Error Handling in I/O

Common I/O exceptions (Errno::ENOENT, IOError).

Using begin ... rescue ... ensure with file operations.

Ensuring safe cleanup with ensure blocks.

6. Advanced I/O Techniques

Binary file handling (binmode, working with raw bytes).

Temporary files with Tempfile.

Pipes and process I/O (IO.popen, backticks, system calls).

Redirection (STDIN.reopen, STDOUT.reopen).

7. Best Practices

Always close or use blocks for automatic cleanup.

Be mindful of encoding (File.open("file.txt", "r:UTF-8")).

Handle large files efficiently (reading line by line).

Avoid insecure methods when dealing with user input.

8. Hands-On Exercises

Write a script that reads a file and prints line numbers.

Create a log writer that appends to a file with timestamps.

Parse command-line arguments and interactively read from STDIN.

 -->

## References

- [IO in Ruby](https://thoughtbot.com/blog/io-in-ruby)
