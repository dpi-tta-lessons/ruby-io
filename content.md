# Ruby I/O

Reading and Writing Data

## Goal

Today you'll learn how to use Ruby's I/O system, the tools Ruby gives you to read information in and send information out. By the end, you'll be able to open a CSV file, transform its data, and save the results again. We call these types of data pipelines ETL (Extract → Transform → Load).

## What Is I/O?

I/O stands for **I**nput/**O**utput. *Input* is data coming into your program (like typing into the keyboard, or reading from a file). *Output* is data going out of your program (like printing to the screen, or saving to a file). Every computer program is basically about moving data in and out. Screens, keyboards, files, and networks are all forms of I/O. Data from these devices is sent to and from programs as a stream of characters/bytes. Essentially, I/O is how your computer interacts with the world.

<aside class="tip">
  Historically, early computers had no screens, only punch cards (input) and printed paper (output). Modern I/O feels fancier, but the idea is the same: get data in, process it, return something useful.
</aside>

We've already learned some console I/O by reading user *input* using `gets` and logging *output* using `puts`, `print`, `p` and/or `pp`. In this lesson we'll dig deeper into file I/O.

## Why I/O Matters

Without I/O, a program is like someone talking to themselves, you can't give it instructions, and you'll never see a result.

Learning I/O lets you:

- Read data from files (like logs or spreadsheets).
- Save results for later use.
- Build tools that interact with real-world data and devices.

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

This script extracts data from a CSV file, transforms the age, and loads it to the console.

## 1. Meet Ruby's IO

Ruby uses an [IO class](https://docs.ruby-lang.org/en/master/IO.html) to handle input/output, think of it as a stream of data. [File](https://docs.ruby-lang.org/en/master/File.html) is a subclass of `IO`, so everything you do with files uses the same foundation Ruby uses for console input and output.

<aside class="tip">
  <code>puts</code> and <code>gets</code> are shorthand for working with Ruby's `STDOUT` (output stream) and `STDIN` (input stream).
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

<aside class="warning">
  Forgetting to close files can cause weird errors. Always use a block form (<code>File.open do |f| ... end</code>) to stay safe.
</aside>

### Why this works

- `"r"` means "read mode."
- `file.each_line` loops through each line without loading the entire file into memory. Handle large files efficiently by reading line by line.

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

Ruby has helpers to check on files:

- [`File.exist?("data.csv")`](https://docs.ruby-lang.org/en/master/File.html#method-c-exist-3F): Check if file exists.
- [`File.size("data.csv")`](https://docs.ruby-lang.org/en/master/File.html#method-c-size): File size in bytes.
- [`File.rename("data.csv", "new_name.csv")`](https://docs.ruby-lang.org/en/master/File.html#method-c-rename): Renames the given file to the new name.
- [`File.delete("data.csv")`](https://docs.ruby-lang.org/en/master/File.html#method-c-unlink): Deletes the named files.

and folders:

- [`Dir.entries`](https://docs.ruby-lang.org/en/master/Dir.html#method-c-entries): Returns an array of the entry names in the directory
- [`Dir.foreach`](https://docs.ruby-lang.org/en/master/Dir.html#method-c-foreach): Calls the block with each entry name in the directory
- [`Dir.pwd`](https://docs.ruby-lang.org/en/master/Dir.html#method-c-pwd): Returns the path to the current working directory
- [`Dir.chdir`](https://docs.ruby-lang.org/en/master/Dir.html#method-c-chdir): Changes the current working directory.

<!-- 
5. Error Handling in I/O

Common I/O exceptions (Errno::ENOENT, IOError).

Using begin ... rescue ... ensure with file operations.

Ensuring safe cleanup with ensure blocks.
 -->

## 5. Using ARGV for Command-Line Arguments

So far we've hardcoded filenames like "data.csv". What if we want to pass a filename when we run the script?

Ruby provides [`ARGV`](https://docs.ruby-lang.org/en/master/ARGF.html) (argument vector), an array that stores values passed from the command line. `ARGV[0]` captures first command-line argument after the ruby filename.

```ruby
# reader.rb
filename = ARGV[0]
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

We'll get this output:

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

## Everything is a File

Unix-like systems (linux, macos, etc.) treat all external devices as files. We can see these under the `/dev` directory. Your keyboard? It's a special file. Your printer? Another file. I/O code in Ruby can work across different kinds of input/output.

### Keylogger

We can write ruby code that reads all the input from the keyboard by opening the `dev/tty` file.
```ruby
File.open("/dev/tty", "r") do |keyboard|
  loop do
    char = keyboard.getc
    puts "Got key: #{char.inspect}"
  end
end
```

<aside class="tip">
  "tty" stands for "teletypewriter". In early computing programs talked to users using "teletypes", basically typewriters connected to computers.
</aside>

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

## Project: I/O

In this project, you will write Ruby programs that leverage these I/O objects. This project includes automated tests, so click this link to get started <https://github.com/dpi-tta-projects/ruby-io/fork>, fork the repository and create a codespace.

<aside class="warning">
  In order to get credit for completing this project you'll need to open the assignment link from canvas to generate an access token.
</aside>

## References

- [IO in Ruby](https://thoughtbot.com/blog/io-in-ruby)

<!-- TODO: The role of streams (STDIN, STDOUT, STDERR). -->
<!-- TODO: Rescue Errno::ENOENT and handle gracefully -->
<!-- TODO: OptionParser -->
<!-- TODO: Guarding input -->
