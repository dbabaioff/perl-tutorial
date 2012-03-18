## Code Layout

* **Semicolons:** Place a semicolon after every statement.

**RIGHT:**

```perl
while (my $line = <>) {
    chomp $line;
    if ($line =~ s{\A (\s*) -- (.*)}{$1#$2}xms) {
        push @comments, $2;
    }
    print $line;
```

**WRONG:**

```perl
while (my $line = <>) {
    chomp $line;
    if ($line =~ s{\A (\s*) -- (.*)}{$1#$2}xms) {
        push @comments, $2 # no semicolons
    }
    print $line # no semicolons
}
```

* **Blocks:** Never place two statements on the same line.

**RIGHT:**

```perl
my @clean_words = map {
    my $word = $_;
    $word =~ s/$EXPLETIVE/[DELETED]/gxms;
    $word;
} @raw_words;
```

**WRONG:**

```perl
my @clean_words
    = map { my $word = $_; $word =~ s/$EXPLETIVE/[DELETED]/gxms; $word } @raw_words;
```