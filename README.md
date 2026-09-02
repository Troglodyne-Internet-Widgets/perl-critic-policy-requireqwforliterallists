# NAME

Perl::Critic::Policy::RequireQwForLiteralLists - A row of quoted words is a qw() list written the long way.

# VERSION

version 1.000

# Perl::Critic::Policy::RequireQwForLiteralLists

A run of plain quoted words handed to something that takes a list is a `qw()`
list spelled out one character at a time:

```
$hv->system_hv( 'sudo', 'rm', '-f', $conf );
return ( 'virsh', '-c', $uri, @args );
```

The quotes and commas carry no information here.  What they do is give you more
places to leave one out, and they bury the fact that the whole run is a single
command line under punctuation:

```
$hv->system_hv( qw{sudo rm -f}, $conf );
```

## PROHIBITED

```perl
my @cmd = ( 'sudo', 'rm', '-f' );
system( 'git', 'rev-parse', 'HEAD' );
push @args, '--connect', '--verbose', '--force';
```

## ALLOWED

Runs too short to be worth it, and anything `qw` would change the meaning of:

```perl
my @two = ( 'a', 'b' );                       # under the threshold
my %h   = ( a => 'b', c => 'd' );             # fat commas, not a list
my @s   = ( 'a b', 'c d', 'e f' );            # whitespace inside
my @i   = ( "$dir", "$file", "$other" );      # interpolation
my @e   = ( 'it\'s', 'a "quote"', 'a\\b' );   # quotes and escapes
```

## CONFIGURATION

- `min_run_length`

    How many literals have to be adjacent before this is worth saying anything
    about.  Defaults to 3.

    ```
    [RequireQwForLiteralLists]
    min_run_length = 4
    ```

## CAVEATS

A run split across a `qw` and some literals -- `qw{sudo rm}, '-f'` -- is
not detected, since the `qw` breaks the run.  Only the literals on either side
of it are counted.

## METHODS

### supported\_parameters

`min_run_length`, how many adjacent literals make a run.

### default\_severity

SEVERITY\_LOW

### default\_themes

cosmetic, maintenance

### applies\_to

PPI::Token::Quote::Single, PPI::Token::Quote::Double, PPI::Token::Quote::Literal

### violates

Standard [Perl::Critic::Policy](https://metacpan.org/pod/Perl%3A%3ACritic%3A%3APolicy) interface.  Returns one violation per run,
anchored on the literal that starts it, so a list of ten does not produce ten
complaints about itself.

# BUGS

Please report any bugs or feature requests on the bugtracker website
[https://github.com/teodesian/perl-critic-policy-requireqwforliterallists/issues](https://github.com/teodesian/perl-critic-policy-requireqwforliterallists/issues)

When submitting a bug or request, please include a test-file or a
patch to an existing test-file that illustrates the bug or desired
feature.

# AUTHORS

Current Maintainers:

- George S. Baugh <teodesian@gmail.com>

# COPYRIGHT AND LICENSE

Copyright (c) 2026 Troglodyne LLC

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:
The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
