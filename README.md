## Perl - There's more than one way to do it

```perl
use strict;
use warnings;
use Carp;
```

##### use strict
```perl
use strict;         # Install all three strictures.

use strict "vars";  # Variables must be predeclared.
use strict "refs";  # Can't use symbolic references.
use strict "subs";  # Bareword strings must be quoted.

use strict;         # Install all...
no strict "vars";   # ...then renege on one.
```

## Numbers
```perl
30_000_000 # equal to...
30000000
```

## Scalars, arrays and hashes
##### Variables in Perl are always preceded with a sign called a sigil. These signs are $, @ and %.

```perl
# PROBLEM:
my $a, $b # --> first var. "$a" is local and the sencod "$b" is *global*

# FIXING THE PROBLEM...
my $a;
my $b; # OR

my($a, $b);
###############
my($a, $b) = (1, 2);
```

##### Gotchas
```perl
my ($x, $y, $z) = (0) x 3; #OR my ($x, $y, $z) = 0 x 3;

sub zero {$_ = 0 for @_}
zero my ($x, $y, $z);

$_ = 0 for my ($x, $y, $z);
```

### Lists
Perl automatically flattens lists - Perl removes all the parentheses apart from the
outermost pair. There’s no difference at all between any of these three lists:

```perl
(3, 8, 5, 15)
((3, 8), (5, 15))
(3, (8, 5), 15)
```

##### List slices
Inside the square brackets we don’t need the additional set of parentheses.

```perl
(19, 68, 47, 60, 53, 51, 58, 55, 47)[4, 5, 6] # <==> (19, 68, 47, 60, 53, 51, 58, 55, 47)[(4, 5, 6)]
```

##### Ranges
```perl
(1 .. 6) # <==> (1, 2, 3, 4, 5, 6)
('a'..'k')
```

### Arrays
```perl
@array = (1, 2, 3);
```

When an array is interpolated in a double quoted string:

```perl
@array = (4, 6, 3, 9, 12, 10); 
print @array, "\n"; # prints 46391210
print "@array\n";   # prints 4 6 3 9 12 1
```

##### Scalar vs. List Context
```perl
print @array; # list context
$_scalar = @array; #scalar context - will print the number of elements in array
```
We can force something to be in scalar context when it expects to be in list context by using the scalar() function.

```perl
print scalar @array; #or print scalar(@array);
```

##### $#array = Last array index
Note: This syntax return the last index of the array, not its last value. The last value of @a can be accessed by indexing with $#a into 
@a with the syntax $a[$#a].

##### swap two array elements
```perl
@months[3, 4] = @months[4, 3];

#This is exactly the same as the following statement: 
($months[3], $months[4]) = ($months[4], $months[3]);
```
##### array sort
**sort()** - Sort array

```perl
my @unsorted = (1, 2, 11, 24, 3, 36, 40, 4); 
my @sorted = sort @unsorted; # uses ASCII sort - perl's default sort
print "Sorted: @sorted\n"; # 1 11 2 24 3 36 4 40

# sort() a block to describe how two values should be ordered
my @number = sort { $a <=> $b } qw(23 101 11 1 102); 
print "Numeric sort:  @number\n"; # 1 11 23 101 102
```

### Hashes
Hashes prefix - percent sign (%).

```perl
%where = (
    'Gary', 'Dallas',
    'Lucy', 'Exeter',
    'Ian', 'Reading',
    'Samantha', 'Portland'
);

# OR
%where = (
    'Gary'     => 'Dallas',
    'Lucy'     => '"Exeter',
    'Ian'      => '"Reading',
    'Samantha' => 'Portland'
);
print "Gary lives in ", $where{Gary}, "\n";
```

Assigning an array to a hash works properly only when there is an even number of elements in the array.

```perl
my @array = qw(Gary Dallas Lucy Exeter Ian Reading Samantha Portland);
my %where = @array;
```

##### Hash in List Context/ Scalar Context
```perl
my %person = (
    'name'  => 'John Doe',
    'age'   => 39,
    'phone' => '555-1212',
    'city'  => 'Chicago'
);
my @data = %person; # list context
print "list context: ", join("|", @data), "\n"; # list context: age|39|city|Chicago|phone|555-1212|name|John Doe

# hash in scalar context
my $scalar = %person;
#  Therefore, this hash in scalar context is “3/8”, which means we are
# using three buckets, or memory locations, out of eight buckets allocated. This string is not so interesting unless we notice that the string 
# “3/8” is a true value in Perl. Also, if our hash was empty, its value in scalar context would be the empty string, "". So a hash in scalar 
# context is normally treated as a true/false value—true if there is anything in it, false if empty.
print "scalar context: $scalar\n"; # prints the string “scalar context: 3/8”. 

if (%person) {
    print "%person has at least one key/value pair\n";
} else {
    print "%person is empty!\n";
}
```

##### keys()/ values()/ each()/ delete()
```perl
my %where = (
    'Gary'     => "Dallas",
    'Lucy'     => "Exeter",
    'Ian'      => "Reading",
    'Samantha' => "Portland"
);

foreach (keys %where) {
    print "$_ lives in $where{$_}\n";
}
foreach (values %where) {
    print "someone lives in $_\n";
}
while (my ($k, $v) = each %where) {
    print "$k lives in $v\n";
}

delete $where{'Lucy'};

if (exists $where{'Gary'}) # exists() returns 1 when true, an empty string when false
{
    print 'YES!';
}
```

### Subroutines/Functions
```perl
sub example_subroutine {
#    ...
}
example_subroutine();
# OR
example_subroutine('Perl is', 'my favorite', $language);
```

**[Note]: We can also call them without the parentheses if the function is defined before it is invoked:**

```perl
sub example_subroutine {
}

example_subroutine;
```

OR

```perl
sub example_subroutine;

example_subroutine;

sub example_subroutine {
}

```

Alternatively, we can use: **use subs (...)**

```perl
use subs qw(example_subroutine test my_func);

example_subroutine;

sub example_subroutine {
}
```

##### Arguments

**Function arguments are passed in through one of Perl’s special variables, the array @_.**

```perl
total(111, 107, 105, 114, 69);
total(1...100);

add(10, 2);

sub total {
    my $total = 0;
    $total += $_ foreach @_;
    print "The total is $total\n";
}

sub add {
    my $arg1 = shift @_;
    my $arg2 = shift; # defaults to shifting @_
    print "sum is: ", $arg1 + $arg2, "\n";
}

# OR
sub add {
    my($arg1, $arg2) = @_;
    print "sum is: ", $arg1 + $arg2, "\n";
}
# [Note]: Make sure that parentheses are used even if there is only one argument assigned to a variable:
my($arg) = @_;
# and not:
my $arg = @_;
# Using parentheses creates a list so that the 0th element of @_ is copied. If the parentheses are dropped, then @_ is
# taken in scalar context, which is its size, in this case 1.
```
##### Return Values
There are two ways to do this: implicitly or explicitly. The implicit way: just make the value we want to return the last item in our subroutine.

```perl
sub test {
    print 'test';
    1; # return 1
}

# OR
sub test {
    print 'test';
    return 1;
}
```

**[!!!] It’s important to know that in Perl, arguments are passed into functions by reference, not by value**

```perl
my $var = 10; 

print "before: $var\n"; 
change_var($var); 
print "after:  $var\n"; 

sub change_var { 
    print "in change_var() before: $_[0]\n"; 
    ++$_[0]; 
    print "in change_var() after:  $_[0]\n"; 
}

# Output: 
# before: 10 
# in change_var() before: 10 
# in change_var() after:  11 
# after:  11
```

##### Default Argument Values
```perl
test();
test('a', 'b');
test('a');

sub test { 
    my $first_arg = shift || 'first';
    my $second_arg = shift || 'second';
    
    print $first_arg,  ' - ', $second_arg, "\n";
}

# Output:
# first - second
# a - b
# a - second
```

##### Named Parameters
```perl
logon(username => $name, password => $pass, host => $hostname);

sub logon { 
   die "Parameters to logon should be even" if @_ % 2; 
   my %args = @_; 
   print "Logging on to host $args{hostname}\n"; 
   ... 
}
```

### q/qq/qw operators
* q/qq functions - can use {}, [], (), and <> as left and right delimiters and others...
* q - quotes
* qq - double quotes
* qw - quote individual words - takes all the items within the slashes (or {}, [], (), and <>...)  that are separated by whitespace characters and
creates a single-quoted list of them --> @array = ('a', 'b', 'c') <==> @array = qw/a b c/

#### *Examples:*
```perl
print qq|'"Hi," said Jack. "Have you read /. today?"'\n|;
print qq#'"Hi," said Jack. "Have you read /. today?"'\n#;
print qq('"Hi," said Jack. "Have you read /. today?"'\n);
print qq<'"Hi," said Jack. "Have you read /. today?"'\n>;
```

### print function
Functions like print() that treat their arguments as lists is that you can omit the parentheses.

```perl
print("test", "\n"); # OR
print "test", "\n";
```
We were actually passing the following list to the print() function: ("test", "\n")

```perl
print "test" . "\n"; <==> print("test" . "\n");
```

### chomp/chop functions
The chomp() function removes the last character of a string if and only if it is the newline character (\n).

```perl
my $string = "testing 1, 2, 3\n"; 
chomp($string);                 # $string is now "testing 1, 2, 3"
```

The chop() function removes the last character of a string, regardless of what character it is.

```perl
my $string = "testing 1, 2, 3"; 
chop($string);                 # $string is now "testing 1, 2, "
```

## Control Flow
**Note: the curly braces — { … } — around the body (the statements) are required. (Even if the body is one line of code).**

* if ... elsif ... else 
* unless ==> another way of saying if (not $a) - If this is not true, then { ... } OR Unless this is true, then { ... }.
* [action] if condition - Example: print 'Test' if ($a); - There is no need for parentheses around the condition, and there are no curly braces around the action.

## Loops
* while (not condition) { ... }
* until (condition) { ... } ==> while (not condition) { ... }
* do { action } while ( condition ); 
* do { action } until ( condition ); 
* [statement] until condition - Example: print "You entered: $_" while (<STDIN>); - There is no need for parentheses around the condition, and there are no curly braces around the statement.

## foreach
**Syntax:**
foreach scalar_variable ( list_or_array ) { 
    body 
}

* Declaring the iterator within the loop, creates a new variable $i each time, which exists only for the duration of the loop.

```perl
foreach my $i (@array)
{ 
    $i *= 2; 
} 
```

**Default iterator:** $_

```perl
foreach (@array)
{ 
    $_ *= 2; # default iterator
} 
```

##### Expression Modifier for the foreach Loop
Using this syntax you don’t get to choose your own iterator variable: it’s always **$_**.

```perl
my @array = qw(John Paul George Ringo); 
print "[$_]\n" foreach @array; 
```

## References
```perl
my @array   = (2, 4, 6, 8, 10);
my $array_r = \@array;
# OR
my $array_r = [2, 4, 6, 8, 10];

my %hash   = (apple => 'pomme', pear => 'poire');
my $hash_r = \%hash;
# OR
my $hash_r = {apple => 'pomme', pear => 'poire'};

my $scalar   = 42; 
my $scalar_r = \$scalar;

my ($var1, $var2, $var3) = (10, 20, 30); 
my @refs = (\$var1, \$var2, \$var3);
```

##### Using Reference
To **dereference** data, put the reference in curly braces wherever you would normally use a variable’s name.

```perl
my @array   = (1, 2, 3, 4, 5); 
my $array_r = \@array; 

my @_array  = @{$array_r};
```

##### References and arrays/ hashes
$# - Last index using reference

```perl
my $ref_arrray = [1, 2, 3, 4];
print 'Last Index:', $#{$ref_arrray}, "\n";

foreach (0..$#{$ref_arrray}) {
     print '-->',  ${$ref_arrray}[$_], "\n";
}

my $ref_hash = {
   1 => "January",    2 => "February", 3 => "March",     4 => "April", 
   5 => "May",        6 => "June",     7 => "July",      8 => "August", 
   9 => "September", 10 => "October", 11 => "November", 12 => "December"
};

foreach (keys %{$href}) { 
   print "Key: ", $_, "\t"; 
   print "Ref: ", ${$href}{$_}, "\n"; 
}
```
##### Notation Shorthand (->)
```perl
$ref = [1, 2, [ 10, 20 ]];
$element = ${${ref}[2]}[1];
```
Instead of ${$ref}, we can say $ref->

```perl
$element = $ref->[2]->[1]; 
# Between sets of square brackets, the arrow is optional
$element = $ref->[2][1]; 
```

```perl
my @array = (68, 101, 114, 111, 117); 
my $ref = \@array; 
${$ref}[0] = 100; 
print "Array is now : @array\n"; 

# and here it is rewritten: 
my @array = (68, 101, 114, 111, 117); 
my $ref = \@array; 
$ref->[0] = 100; 
print "Array is now : @array\n"; 

# Likewise for hashes, we can use this arrow notation to make things a bit clearer.
my %hash = ( 
   1 => "January",    2 => "February", 3 => "March",     4 => "April", 
   5 => "May",        6 => "June",     7 => "July",      8 => "August", 
   9 => "September", 10 => "October", 11 => "November", 12 => "December" 
); 
my $href = \%hash;

foreach (keys %{$href}) { 
   print "Key: ", $_, "\t"; 
   print "Hash: ", $hash{$_}, "\t"; 
   print "Ref: ", ${$href}{$_}, "\n"; 
} 
Instead, we can write the following: 
foreach (keys %{$href}) { 
   print "Key: ", $_, "\t"; 
   print "Hash: ", $hash{$_}, "\t"; 
   print "Ref: ", $href->{$_}, "\n"; 
} 
```

##### Autovivification
```perl
my $ref; 
$ref->{UK}->{England}->{Oxford}->[1999]->{Population} = 500000;
```

Perl will automatically know that we need $ref to be a hash reference. So, it’ll make us a nice new anonymous hash: 

```perl
$ref = {}; 
$ref->{UK} = {}; 
$ref->{UK}->{England} = {}; 
$ref->{UK}->{England}->{Oxford} = []; 
$ref->{UK}->{England}->{Oxford}->[1999] = {}; 
$ref->{UK}->{England}->{Oxford}->[1999]->{Population} = 500000;
```
```perl
my @array;
$array[0]->[0] = 'test';

# OR
$array[0][0] = 'test';
```

## Modules
A **library** is a just a file of code.
A module is a collection of related functions and variables that can be used by any program (and has a **package** in it).

* Put the source code into a file named modulename.pm (pm stands for “Perl module”). 
* Make the module a package, or namespace, by placing the line of code **package Logger**; at the top of the file. 
* Define variables and function in the file. 
* Have the file return a true value by ending in 1;

**[Note]:** A note about 1; at the end of the file.  Earlier versions of Perl required that the module return a true value 
when it is used by a program. Since the module will return the last line of code in the file, most programmers 
simply put 1;, a true value, at the end of the module. Newer versions of Perl do not require the 1; at the end of the 
file, but for backward compatibility and historical reasons, programmers still put in that true value.

##### A few benefits of migrating code from scripts (libraries) to modules:
* Without packages, everything occupies a single namespace, so you may hit a situation where two functions from separate files want the same name.
* A package allows you to expose some functions, but hide others. With no packages, all functions are visible.
* Files included with require are only loaded at run time, whereas packages loaded with use are subject to earlier compile-time checks.

##### Including Other Files
Three different ways for a Perl program to import a module and call that module’s functions. **do, require, and use**.

* **use** - evaluated at compile time.
* **require** - evaluated at run time.

* **use** only works with modules, whereas **require** works with both modules and libraries. **use** only works with module names (Foo), whereas **require** also works with paths (eg ~/lib/foo.pm).
* **require** Foo will check if Foo has already been loaded, whereas **do** Foo will unconditionally reload Foo.

##### do
**do** will look for a file by searching the @INC path. If the file can’t be found, it will silently move on. If it is found, it will run the file just as if it was placed in a block within your main program.

##### require
**require** is like **do**, but it’ll only do it once. It’ll record the fact that a file has been loaded, and will henceforth ignore further requests to require it again.

##### use
It's like **require**, except that Perl applies it before anything else in the program starts — if Perl sees a **use** statement anywhere in your program, it’ll include that module (compiled-time).

```perl
# these two calls are equivalent:
use File::Basename;

sub BEGIN {
    require File::Basename;
    File::Basename->import;
}
```

##### BEGIN/END subroutine
**BEGIN** - Run some code as soon as it has all been read in by the parser and before the rest compiles.
**END** - Run some code as late as possible. When the interpreter is ready to shut down.

```perl
BEGIN {
    print "Birth of a process...\n";
}

print "Life of a process...\n";
die "Murder of a process...\n";

END {
    print "Death of a process...\n";
}

# Output:
# Birth of a process...
# Life of a process...
# Murder of a process...
# Death of a process...

die "Bye";

END   { print "End 1\n"    }
BEGIN { print "Begin 1\n"; }
END   { print "End 2\n"    }
BEGIN { print "Begin 2\n"; }

# Output:
# Begin 1
# Begin 2
# Bye at 1.pl line 1.
# End 2
# End 1
```

##### Exporters
* See: http://perldoc.perl.org/Exporter.html.
* To use a new module, modify the use statement, and then adjust the function calls to use the full package name.

```perl
use warnings; 
use strict; 
use Acme::Webserver::Logger;

Acme::Webserver::Logger::open_log("webserver.log");
Acme::Webserver::Logger::close_log();
```

We can shorten these lines by exporting the function names in the module. using **Exporter**.

```perl
package YourModule;
use Exporter; 
our @ISA = qw(Exporter);
our @EXPORT = qw(close_log write_log log_level);
our @EXPORT_OK = qw(open_log);

# OR
package YourModule;
use base 'Exporter'; # use base 'Exporter' statement is replacement for BEGIN { require Exporter; @ISA = qw(Exporter); }
our @EXPORT = qw(close_log write_log log_level);
our @EXPORT_OK = qw(open_log);

# OR
package YourModule;
use Exporter 'import'; # gives you Exporter's import() method directly - [OR] - use Exporter qw(import)
our @EXPORT = qw(close_log write_log log_level);
our @EXPORT_OK = qw(open_log);
```
**This does not stop someone from saying YourModule::function_name (if it existed).**

* **@EXPORT** this array stores the subroutins to be exported from a module.
* **@EXPORT_OK** this array stores the subroutins to be exported only on request.

##### Import Modules
```perl
# This imports all the symbols from YourModule's @EXPORT into the namespace of the use statement.
use YourModule;

# This causes perl to load your module but does not import any symbols.
use YourModule ();

# This imports only the symbols listed by the caller into their namespace. All listed symbols must be in your @EXPORT or @EXPORT_OK , else an error occurs.
use YourModule qw(...);
```