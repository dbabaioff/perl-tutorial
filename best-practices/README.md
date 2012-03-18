## Code Layout

* **Semicolons:** Place a semicolon after every statement.

RIGHT:

```perl
while (my $line = <>) {
  chomp $line;
  if ($line =~ s{\A (\s*) -- (.*)}{$1#$2}xms) {
    push @comments, $2;
  }
  print $line;
```

WRONG:

```perl
while (my $line = <>) {
  chomp $line;
  if ( $line =~ s{\A (\s*) -- (.*)}{$1#$2}xms ) {
    push @comments, $2 # no semicolons
  }
  print $line # no semicolons
}

```