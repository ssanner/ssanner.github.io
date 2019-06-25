Author:   Scott Sanner (ssanner@cs.stanford.edu)
Updated:  10/6/99, 10/13/99, 11/19/99, 11/22/99, 12/9/99, 7/15/00

Includes: ACT-R-Gammon 2.0 (Backgammon player based on the ACT-R
          ----------------  cognitive architecture)

          Pubeval          (Gerry Tesauro's single layer neural
          -------           net backgammon evaluation player)

Backgammon for UNIX/PC Instructions v2.0:
=========================================
This game player was developed as a generic backgammon playing interface
with a number of built-in players (some of which are capable of learning), 
display options, and data aggregation utilities.  All aspects of using
the system: compilation, player and display interfaces, backgammon 
references, and a description of the software architecture are described
in this file.  The interfaces should allow for easy future extensions of
this software to domains such as networked game play or a Java GUI for
playing over the web.

Compilation:
============
To compile the backgammon program, ensure that the directory contains the
file 'Makefile' and type 'gmake all'.  The compiler may give a few warnings
but the executable 'backgammon' should be produced.  If you want to run
backgammon on DOS or Windows 95/95/NT machines, simply change the .C
filename extension to .cpp and it should compile and run in DOS console 
mode under the Microsoft or Borland compilers (I have not verified it with 
any others).

Command Line:
=============
The command line for the backgammon program currently accepts parameters
for the white and black players, the display type, and the number of games
to be played.  The command line takes four parameters, with any other 
number of parameters a command line usage *similar* to the following
will be printed out.

   usage: backgammon <White Player> <Black Player> <Display> <# Games>

   Players:
   --------
   1: Random Player   (Randomly selects moves)
   2: Static Player   (Always selects first move)
   3: Text Player     (User interface)
   4: Pubeval         (Single layer neural net)
   5: ACT-R v.1       (Prob. Learning/PM,Config #1)
   6: ACT-R v.2       (Prob. Learning/PM,Config #2)
   7: TD-Base Rep v.1 (*In progress*,Config #1)
   8: TD-Base Rep v.2 (*In progress*,Config #2)
	
   Displays:
   ---------
   1: Console Output (ASCII Text)
   2: File Output    (ASCII Text > 'textdisp.txt')
   3: Null Display   (No display)

Examples:
---------
To set the white player to a random player, the black player to the user, 
the display to the console, and to play one game, the following command line 
would be entered:

backgammon 1 3 1 1

To pit the TD-Gammon player (single layer neural net version) against 
the ACT-R player (v.1), not display or store any game transcripts, and play
10,000 games (i.e. the reason for not having any game transcript), the
following command line would be entered:

backgammon 4 5 3 10000

For every invocation of the backgammon program, a file named 'results.txt'
is written to disk which indicates the results of the games played during
the program session.  This file has two columns, the first for the game
number, and the second for the winner.  The second column indicates a 
0 for a win by white and a 1 for a win by black.  

There should be three Matlab m-files in the backgammon directory.  This
allows users who have Matlab to aggregate the data in this file into
a plot showing winning percentage over time for each player.  To use
this feature, simply run Matlab (executable name 'matlab' on the Andrew
network) and run the script 'compare' by typing this at the command
line.  This will bring up a plot window and plot the aggregated results 
of the data in the 'results.txt' file.  This script will automatically
fit the data to a smooth curve and update every five seconds so that
one may observe the performance of the players as they train.  To
simply display the same plot without the auto-update feature, simply
use the alternate command 'compare1'.

Display Setup:
==============

1: Console Output:
------------------
When display option 1 is selected, the game board is displayed to the
user's standard output terminal on the console.  The game board 
should resemble the following.

       X Outer Table         X Home Table [6]
  +-------------------+---+-------------------+
  | 13 14 15 16 17 18 |   | 19 20 21 22 23 24 |
  |  O  |  |  |  X  | |   |  X  |  |  |  |  O |
  |  O  |  |  |  X  | |   |  X  |  |  |  |  O |
  |  O  |  |  |  |  | |   |  X  |  |  |  |  | |
  |  O  |  |  |  |  | |   |  X  |  |  |  |  | |
  |  O  |  |  |  |  | |   |  6  |  |  |  |  | |
  |                   |   |                   |
  +-------------------+   +-------------------+
  |                   |   |                   |
  |  X  |  |  |  |  | |   |  O  |  |  |  |  | |
  |  X  |  |  |  |  | |   |  O  |  |  |  |  | |
  |  X  |  |  |  |  | |   |  O  |  |  |  |  | |
  |  X  |  |  |  |  | | X |  O  |  |  |  |  | |
  |  X  |  |  |  O  | | X |  O  |  O  O  |  | |
  | 12 11 10 09 08 07 |   | 06 05 04 03 02 01 |
  +-------------------+---+-------------------+
       O Outer Table         O Home Table [7]

        Turn #3: *Black(X)*  Dice: *5* *2*  

This is the basic game board setup where player X is attempting to move
from the low points to the high points and player O is moving in the
opposite direction from the high points to the low points.  The number in
brackets indicates how many men each player has in the their home board.
Once this number reaches 15, [R#] will be displayed to indicate that the
player is in the end-game (race) mode.  The # in the above display will
indicate how many men are left in the player's home board.  Men in the 
center portion of the board are considered to be on the BAR (i.e. the two 
X's above).  If a number is displayed on a point (i.e. the 6 on point 19) 
it indicates that the number of players indicated are on that point but 
could not be displayed.

At the end of the game, the winner will be displayed.

2: File Output:
---------------
When display option 2 is selected, the game board is displayed to the
file 'textdisp.txt'.  The display should be identical to that of the
console output specified above except that it is piped to the specified
file.

3: Null Display:
----------------
When display option 3 is selected, all output is discarded except for
a printout of the game number and winner at the conclusion of each
game.  If running over 10 games, it is best to use this option
since it will not waste unnecessary disk space.  Following is example
output of this display option over multiple games:

Game 1: White(O) won
Game 2: Black(X) won
Game 3: White(O) won
Game 4: White(O) won
...

Player Setup:
=============

1: Random Player:
-----------------
This player randomly selects moves from all possible move sequences.  It
requires no explicit setup and has no mechanisms for learning.

2: Static Player:
-----------------
This player always selects the first move of all all possible move
sequences which leads to a strategy of always moving up the lowest
pieces to a higher board position first.  It requires no explicit setup
and has no mechanisms for learning.

3: Text Player:
---------------
This is the user interface that allows a person to interact with any
of the backgammon players.  The following prompt is given at the
beginning of the user's turn:

Black(X)'s turn (<src dest>*|m #|h|d|e): 

To make a move, simply type a series of start and destination points 
separated by spaces and end the line with 'e' before pressing enter.  
For example, two possible moves for a board state could be 
'1 5 6 7 e <enter>' or 'b 2 1 3 11 15 e <enter>'.  In the first example, 
the user is indicating to move a man from point 1 to point 5 and another 
man from point 6 to point 7.  In the second example, the user is 
indicating to move a man from the BAR to point 2, a man from point 1 to 
point 3, and another man from point 11 to point 15 (perhaps doubles 
were rolled and the first two men were moved 2 each and the third man 
was moved from point 11-13 and from 13-15). 

*Note: 'b' is used in a move to represent the BAR, 'o' is used to represent 
when a piece on the board should be bore off (during the endgame).

To see a list of all possible moves, type 'h <enter>'.  To select a move 
from this list, type 'm' followed by the move number and '<enter>'.  For 
example, suppose the following list were generated from a board state:

(1) 8-19 12-17
(2) 8-14 12-17
(3) 8-14 15-20
(4) 8-14 17-22
(5) 8-14 19-24
(6) 12-23 19-24
(7) 12-18 12-17
(8) 12-18 15-20
(9) 12-18 17-22
(10) 12-18 19-24
(11) 12-17 15-21
(12) 12-17 17-23
(13) 15-21 17-22
(14) 15-21 19-24
(15) 15-20 17-23
(16) 17-23 17-22
(17) 17-23 19-24

One could simply select move (10) by typing 'm 10 <enter>'.

Finally, to redisplay the game board, simply type 'd <enter>'.

4: TD-Gammon:
-------------
This player is based upon Gerry Tesauro's public code for a backgammon
benchmark player.  It is based upon a single layer neural network that
uses two weight sets, one for contact mode and one for race mode.  This
player has no mechanism for learning, but one can provide it with
customized weight sets.  The respective weight sets for the contact
and race positions can be found in the following configuration files
for this player:

td1-config-cntc.txt
td1-config-race.txt

5,6: ACT-R v.1,v.2:
-------------------
This player is based upon the ACT-R Cognitive Architecture and uses
a simple feature derivation scheme along with procedural learning
and partial matching to learn to play.  The player can be configured
in many ways which are described below.

Since it is possible to play the ACT-R player against itself and the
user may want to play different versions of the player, two options
(v.1 & v.2) are given for the player.  These options load and save
to the respectively named files indicated below.  Since it is useful
to see what the ACT-R player has learned at the end of a training set
without overwriting the configuration file, the training results (if
learning is turned on) are written to an output file.  The 
configuration and output files are listed below, which file 
corresponds to which should be obvious from the naming convention:

actr-config-v1.txt
actr-output-v1.txt

actr-config-v2.txt
actr-output-v2.txt

The output files can be copied to the configuration files (they 
use exactly the same format) if one wants to play the results of 
a trained player against another player.

Some pretrained configuration files are listed below.  The numerical
extension in the filename indicates its winning percentage against
the single-layer TD-Gammon player.  To play one of these configurations,
make sure to copy the file to the appropriate configuration being used.

actr-config-33.txt
actr-config-40.txt
actr-config-45.txt
actr-config-48.txt

An example configuration file and an explanation of its contents follows:

CONFLICT_RES_TEMP 50.0
PM_THRESHOLD      0.1
LAMBDA            1.0
MIN_BLOT_PENALTY  0
MAX_BLOT_PENALTY  10
MULTIPLE_NEIGHBOR ON 
LEARNING          OFF 

FeatureArray: 
Feature: Attack 0  9.55861 12.1332  PT 10            Odds: 0.787804
Feature: Expose 2 130.782  395.754  PT  1 OP 15      Odds: 0.330462
Feature: Expose 2 11.2704  68.4143  PT  9 OP  7      Odds: 0.164738
Feature: Expose 2 13.2744  20.4985  PT 17 OP  4      Odds: 0.647579
Feature: Expose 2  5        7.53319 PT 24 OP  4      Odds: 0.663729
Feature: Block  1  5       22.6687  PT  1 OP  2 SZ 1 Odds: 0.220568
Feature: Block  1 11.4053  80.5922  PT  7 OP  1 SZ 1 Odds: 0.141519
Feature: Block  1 10.3029  42.7212  PT 22 OP  1 SZ 5 Odds: 0.241166
Feature: Block  1  8.1502   5.80567 PT 19 OP  5 SZ 3 Odds: 1.40383
Feature: Block  1 47.8426  25.8186  PT 24 OP  3 SZ 3 Odds: 1.85303
...

CONFLICT_RES_TEMP defines how stochastic the system is when choosing
a move.  The higher the temperature, the less stochastic the choice.
The conflict resolution temperature should always be greater than 1.0.

PM_THRESHOLD defines the threshold penalty at which point a feature
chunk is determined not to match another feature.  As this value
gets smaller, the system tends to chunk more and more specific
examples since it becomes harder to find matches.

LAMBDA defines the update discount as the update is propagated from
the final move in the game to the first move.

MIN_BLOT_PENALTY and MAX_BLOT_PENALTY define the respective min and
max intermediate update penalties for having a player 'blotted' to
the bar.  The actual penalty is varies linearly from MIN_BLOT_PENALTY
at point 1 to MAX_BLOT_PENALTY at point 24.

MULTIPLE_NEIGHBOR defines whether multiple neighbor blending based on
the weighted nearest neighbor algorithm is used ('ON') or whether 
nearest neighbor matching (the ACT-R default) is used ('OFF').  The
player generally performs better under the multiple neighbor method.

LEARNING defines whether learning is turned 'ON' or 'OFF' in the
configuration.

Following these parameters is the header 'FeatureArray' followed by
a number of features.  These features are used by the player in
matching to features of the current state and determining the
utility of the feature.

7,8: TD-Base Rep v.1,v.2:
-------------------------
This player is based upon the same base feature representation as the 
ACT-R player except that it uses neural nets in place of partial 
matching and chunking to learn feature odds.

Consequently, the input file consists of the same basic parameters
as the ACT-R player except that in place of the PM_PENALTY parameter
it has three parameters for the learning rate (ETA), the momentum
for stochasticity in training (MOMENTUM), and the number of hidden 
units that each feature neural network should have (HIDDEN).  
Additionally, the file also has the neural network weight 
specifications in place of the feature chunks found in the ACT-R
player.

To create a new TD-Base Rep player, simply specify the top set of
parameters but do not specify the neural network.  If a neural network
is not found in the input file it will be created according to the
hidden unit specifications.

As in the ACT-R player, there are two versions of the player so that
it can be played against itself and there are two corresponding
output files:

td-baserep-config-v1.txt
td-baserep-output-v1.txt

td-baserep-config-v2.txt
td-baserep-output-v2.txt

A sample input file resembles the following:

CONFLICT_RES_TEMP 50
LAMBDA            1
ETA               0.05
MOMENTUM          0
HIDDEN            20
MIN_BLOT_PENALTY  0
MAX_BLOT_PENALTY  0.5
LEARNING          OFF 

Neural-Net:

Input:    46
Hidden:   20
Output:   1
Eta:      0.05
Momentum: 0

In-Hidden:
0.626317 0.348916 0.189232 -0.786174 -0.174522 0.386258 -0.913733 ...
0.584547 0.957854 0.087242 0.142801 0.630143 -0.336847 0.0526502 ...
0.822039 0.0180319 0.654531 -0.556018 0.146189 0.411137 0.20879 ...
0.064707 0.131452 0.474331 0.62681 0.151974 0.773033 -0.304118 ...
0.939611 0.339311 0.473209 0.451301 0.416319 0.262692 -0.297964 ...
-0.304246 -0.892339 -0.832674 -0.806023 0.232466 -0.794967 0.683618 ...
-0.984778 -0.37458 0.122417 0.879195 0.693047 -0.58524 -0.389695  ...
...

Hidden-Out:
0.250791 0.22985 
0.985712 -0.455093 
-0.112767 -0.940436 
-0.20796 0.377153 
0.410966 -0.576068 
-0.557704 0.239459 
...

Game Play:
==========
For rules of play for backgammon, the website 'http://www.bkgm.com' has a
fairly extensive on-line overview.

Software Architecture:
======================
There are three main classes to this application, 'Backgammon', 'Player',
and 'Display'.  The Backgammon class accepts two instances of a Player
and the display class accepts an instance of a Display.  Derived from the
Player class are 'RandomPlayer', 'StaticPlayer', 'TDPlayer', and a
base representation class 'BaseRepPlayer' from which 'ACTRPlayer' and
'TDBaseRepPlayer' derive.  Derived from the Display class are 'TextDisplay',
'FileDisplay', and 'NullDisplay'.

To develop either a new Player class or a new Display class, one should
only have to look at one of these class instances to understand how to
modify it or build a new one.

Bugs:
=====
If you find any bugs in this software such as the allowance of illegal moves
or other anomalies please let me know (email: ssanner@cs.stanford.edu).





