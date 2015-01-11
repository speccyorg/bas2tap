BAS2TAP v2.6                                                (Release 20-01-2013)
Copyleft (C) 1998-2013 Martijn van der Heide, ThunderWare Research Center.

This program converts 'BASIC in an ASCII file' to a TAP tape image file, to be
used in a ZX Spectrum emulator. Both 48K and 128K listings are supported.


COPYLEFT AND DISCLAIMER
-----------------------
This program is free software; you can redistribute it and/or
modify it under the terms of the GNU General Public License
as published by the Free Software Foundation; either version 2
of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program; if not, write to the Free Software
Foundation, Inc., 59 Temple Place - Suite 330, Boston, MA  02111-1307, USA.

Remember you can always freely obtain the latest version from
the utilities section of The World of Spectrum, at:
    http://www.worldofspectrum.org/utilities.html


REVISION HISTORY
----------------
1.0  26-03-98: First public release
1.1  01-04-98: Changes:
               - Added banner.
               - Switch -q does not suppress warnings, but progress indication.
               - Added switch -w to suppress warnings.
               - Added switch -e on popular demand.
               - Empty lines are skipped and only generate a warning.
               - Lines with only a line number generate an error.
               - Numbers can also start with a decimal point (eg. '.5').
               - Lines no longer require an end-of-line terminator.
               - In-line sequence expansions also remove any trailing
                 spaces unless it was between quotes.
               - The created TAP is made valid (although incomplete) if an error
                 occurs, unless it was a write error.
1.2  11-12-98: Changes:
               - Block graphics were encoded rotated by 1.
               - Added full syntax checking, and
               - Added switch -n to disable the syntax checking again.
               - Added expansion sequences '{CAT}' and '{CODE}' with the sole
                 purpose of being used as channel name in the OPEN # command.
               - Changed the distribution policy from freeware to Open Source
                 Software.
1.3  04-01-99: Changes:
               - Spaces after strings are now skipped (they are not needed).
               - The 'CLS #' command does exist on the Interface 1 after all.
               - Exponential numbers were raised the wrong way around
                 (eg. 25e3 became 0.025 and 25e-3 became 25000). Oooops....
1.4  17-05-99: Changes:
               - UDGs used as 'USR "x"' would result in a crash due to 2
                 bad pointers.
               - Complex expressions between brackets as parameter to other
                 expression types (eg. ATTR or string slicing) would screw
                 up the evaluation.
               - The 'CLS #' command was flagged as being Interface 1
                 specific in the previous version rather than either
                 Interface 1 or Opus Discovery (aaargh!)
1.5  19-05-99: Changes:
               - The 'DIM' command's syntax description was incorrect, so the
                 checker didn't like the variable name before the opening
                 bracket.
1.6  23-06-99: Changes:
               - Several problems with array indices!
               - The 'USR' token only accepted a numerical operand. An
                 exception was made for UDG expressions '"x"' but constructions
                 like 'USR CHR$ a' didn't work.
1.7  02-11-99: Changes:
               - The DEF FN function requires insertion of evaluation room.
1.8  09-11-00: Changes:
               - The DEF FN function only requires insertion of evaluation room
                 in the parameter area before the '=' token.
                 Any bracketed argument would upset the parser.
1.9  08-12-01: Changes:
               - Forgot to skip the ';' colour token separator in class 9.
               - REM statements should be parsed as-is (except for expansion
                 sequences), without taking out multiple spaces or counting
                 quotes.
2.0  11-12-02: Changes:
               - SAVE/LOAD/VERIFY/MERGE DATA didn't work, as the DATA token was
                 set to be allowed as keyword only.
               - ERASE ! didn't work, as ERASE was only allowed from the "m"
                 channel.
2.1  22-12-02: Changes:
               - Any alphanumerical expression can be sliced, not only
                 variables and direct strings.
               - If the output filename provided on the command line had the
                 extension '.TAP', it shouldn't be forced to '.tap'.
2.2  13-06-03: Changes:
               - Expand sequences PAPER and INK had their codes reversed.
               - Expand sequences BRIGHT and FLASH had their codes reversed.
               - Expand sequences INVERSE and OVER had their codes reversed.
               - Expand sequences {XX} didn't work (missing pointer advance).
               - Expand sequences AT and TAB didn't work and threw a
                 warning instead (missing pointer advance).
2.3  05-07-04: Changes:
               - LET is allowed to write to a substring.
2.4  24-07-05: Changes:
               - BIN is a number format, not an expression token.
2.5  06-06-10: Changes:
               - Functions would take "the rest of the expression" as argument
                 rather than just the next value.
               - Stronger enforcement of separator tokens (;,') in a PRINT
                 statement.
               - The use of 'TO' in string arrays was not accepted.
2.6  20-01-13: Changes:
               - The PLAY command was not parsed properly.

SYNTAX
------
  BAS2TAP [-q] [-w] [-e] [-c] [-aX] [-sX] FileIn [FileOut]

-q  goes into quiet mode, suppressing banner and progress indication.
-w  suppress generation of warnings.
-e  write errors to the stdout in stead of the stderr stream.
-c  makes token matching case independant. The drawback is that variables that
    have the same name as a token (eg. 'print') cannot be used, as they will be
    treated as (unexpected) tokens.
-n  disables syntax checking.
-aX sets the auto-start line to line X.
-sX sets the TAP block name to X (max 10 characters).

If no output filename is provided, the same name as the input file will be
used, but with extension `.TAP'.


THE CONVERTOR
-------------
The following rules apply:

1. One BASIC line must be provided in one ASCII line.
2. Keywords must appear in upper case (unless switch -c is used).
3. Keywords that consist of multiple words (eg. `GO TO') must have the space
   in between the words.
4. Spaces between words are not needed, unless this leads to concatenation
   problems - eg. `PRINTVAL"10"' must appear as `PRINT VAL"10"', while
   `PRINT10' is perfectly acceptable.
5. Syntax is fully checked according to
   - the normal Spectrum ROM,
   - Interface 1/Microdrive extension,
   - Opus Discovery extension.

The following sequences are expanded in-line:

   Chars   Expand to

   tab     character 06     ("print '")
   {XX}    XX is 2-digit hex, converts to the Spectrum ASCII value
   {(C)}   character 7F     (copyright sign)
   {-X}    X is 1-8, characters 80-87 (block graphics without shift)
   {+X}    X is 1-8, characters 88-8F (block graphics with shift)
   {X}     X is `A'-`U', converts to the UDG Spectrum ASCII value
   {CODE}  character AF     ('CODE')
   {CAT}   character CF     ('CAT')

Special sequences are (case independant) PAPER, INK, BRIGHT, FLASH, OVER,
INVERSE, AT and TAB control sequences. They may appear as "{PAPER 3}" to convert
to the sequence 0x10,0x03. Note that both AT and TAB take 2 operands, which
should be seperated by a comma (eg. "{AT 1,10}").
Notice that these expansions are identical to what SGD and TAPER create when
saving out BASIC blocks.


RECOMPILING THE SOURCE
----------------------
The source is provided in (almost) fully portable ANSI C, the source text is
written in 132 columns.

If you wish to experiment with different syntax (eg. for a DISCiPLE), you may
find it useful to set the `__DEBUG__' define.

If you make any fundamental change to the source, you are requested - but not
obligated - to send the changed source back to me, so that everyone can benefit
from it. My e-mail address is `mheide@worldofspectrum.org'


Enjoy!

Martijn van der Heide
ThunderWare Research Center
