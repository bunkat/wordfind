_Wordfind_ is a simple `javascript` library for generating (hopefully fun) word find (also known as word search) puzzles. Just give it a set of words and a few milliseconds later it  will spit out a puzzle containing those words.

The core `wordfind.js` library contains no dependencies and will work both in the browser and in node.js.  The repository also includes a fully functional word find game (aptly called `wordfindgame.js`) as an example. The game has a dependency on `jQuery`.

Check out the sample game at http://bunkat.github.com/wordfind/example/index.html.

## Basic Browser Example
    
    <script type="text/javascript" src="../src/wordfind.js"></script> 
    <script>
    
      var words = ['cows', 'tracks', 'arrived', 'located', 'sir', 'seat',
                   'division', 'effect', 'underline', 'view', 'annual',
                   'anniversary', 'centennial', 'millennium', 'perennial',
                   'artisan', 'apprentice', 'meteorologist', 'blizzard', 'tornado'];
      
      // create a new puzzle
      var puzzle = wordfind.newPuzzle(words);
      
      // print the puzzle to console
      wordfind.print(puzzle);
    
    </script>

## Word Find Game Example
    
    <div id='puzzle'></div>
    <div id='words'></div>
 
    <script language="javascript" type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.7.1/jquery.min.js"></script> 
    <script type="text/javascript" src="../src/wordfind.js"></script> 
    <script type="text/javascript" src="scripts/wordfindgame.js"></script> 
    <script>
    
      var words = ['cows', 'tracks', 'arrived', 'located', 'sir', 'seat',
                   'division', 'effect', 'underline', 'view', 'annual',
                   'anniversary', 'centennial', 'millennium', 'perennial',
                   'artisan', 'apprentice', 'meteorologist', 'blizzard', 'tornado'];
    
      // start a word find game
      var gamePuzzle = wordfindgame.create(words, '#puzzle', '#words');
        
    </script>

## Options

_Wordfind_ supports an options object when creating new puzzles. The options object supports the following properties.

#### height

The desired number of rows in the puzzle. Will automatically be increased if a valid puzzle cannot be found with specified number of rows. Specifying a reasonable `height` will improve performance when creating puzzles.

Defaults to the minimum number of rows needed to create a valid puzzle.  

#### width

The desired number of columns in the puzzle. Will automatically be increased if a valid puzzle cannot be found with specified number of columns. Specifying a reasonable `width` will improve performance when creating puzzles.

Defaults to the minimum number of columns needed to create a valid puzzle.

#### orientations

An array containing the names of the word orientations that should be used when creating the puzzle. The list of valid orientations can be found by calling `wordfind.validOrientations`.  

By default they include:

    horizontal
    horizontalBack
    vertical
    verticalUp
    diagonal
    diagonalUp
    diagonalBack
    diagonalUpBack

To generate easier puzzles, you may wish to specify only the forward orientations.  The simplest puzzles would include only `horizontal` and `vertical`. 

Default is to use all of the orientations when placing words.  

#### fillBlanks

True to fill in the remaining empty squares after generating the puzzle, false to leave them empty.  This is useful when testing to see what the shape of the solution looks like. Can also be used to generate a mask to determine when all words have been found. 

Default is `true`.

#### maxAttempts

Specifies the maximum number of attempts to create a valid puzzle of a certain size.  If a valid puzzle cannot be constructed after `maxAttempts` have been  made, the puzzle height and width are incremented by one and the number of attempts is reset. Increasing this number can result in slightly more compact puzzles but at the cost of performance.

Default is `3`.  

#### preferOverlap

Determines how `wordfind` decides where to place a word within the puzzle. When `true`, it randomly selects amongst the positions the highest number of letters that overlap creating a more compact puzzle. When `false`, it randomly selects amongst all valid positions creating a less compact puzzle.

Default is `true`.

## Creating Puzzles

Including `wordfind.js` creates a `wordfind` object with the following properties and methods.

#### validOrientations

Returns an array with the names of all of the orientations that _Wordfind_ understands.

For example:

    wordfind.validOrientations

will return the following by default:

    ['horizontal','horizontalBack','vertical','verticalUp',
     'diagonal','diagonalUp','diagonalBack','diagonalUpBack']

#### orientations

Returns the definition functions for the valid orientations.  The definition functions are used to traverse a word, letter by letter, within a puzzle using the specified orientation.

Given the puzzle:

    var puzzle = [[A, X, C],
                  [P, E, O],
                  [J, I, W]]

We can traverse the word `cow` by knowing the orientation of the word along with its starting position.

    var next = wordfind.orientations['vertical'];
    for (var i = 0, len = cow.length; i < len; i++) {
      var square = next(2, 0, i);
      console.log(puzzle[square.y][square.x]);
    }

You can use the `solve` function to get a list of all of the words starting positions and orientations within a given puzzle.

#### newPuzzle(words, options)

Generates a new word find puzzle that contains the specified `words` using the specified `options`. The `words` should be an array of strings that are the words to include in the puzzle. The puzzle returned is always a 2d matrix ([[string]]) where `puzzle.length` is the number of rows and `puzzle[0].length` is the number of columns.

For example:

    var puzzle = wordfind.newPuzzle(['cow']);

Or specifying an options object:

    var puzzle = wordFind.newPuzzle(['cow'], {
          height: 3,
          width:  3,
          orientations: ['horizontal', 'vertical'],
          fillBlanks: true,
          preferOverlap: false
        });

Will return a puzzle in the following form:

    [[A, X, C],
     [P, E, O],
     [J, I, W]]


#### fillBlanks(puzzle)

Fills in all empty squares within the specified `puzzle` with a random letter. Useful if the puzzle was initially generated with the `fillBlanks: false` option.  

For example:

    var puzzle = wordfind.newPuzzle(['cow'], {fillBlanks: false});
    wordfind.fillBlanks(puzzle);

#### solve(puzzle, words)

Locates the specified `words` within the `puzzle`. `words` should be an array of strings containing the words to be solved for. `puzzle` should be a valid _Wordfind_ puzzle. Returns two arrays, `found` and `notFound`.  

The `found` array contains a set of `location` objects that have the following properties:

    word:        the word that was found
    x:           the column position of the first leter of the word
    y:           the row position of the first letter of the word
    orientation: the name of the orientation of how the word appears
    overlap:     will always equal `word.length`

The `notFound` array contains a list of words that were not located in `puzzle`.

#### print(puzzle)

A simple helper function that prints the puzzle to the console. It also returns the string representation of the puzzle. Useful for quickly viewing puzzles that are being generated.

## Running tests

There are currently no tests for this library. Feel free to create some and send me a pull request! :)

## Performance

A simple 15x15 20 word puzzle can be created in 12ms on a decent computer. As a general rule, doubling the words quadruples the time required to generate a puzzle. Luckily word finds with hundreds of words generally aren't that enjoyable to do.  Performance can be improved by always specifying a reasonable `height` and `width` when creating puzzles. 

## License 

(The MIT License)

Copyright (c) 2011 BunKat LLC &lt;bill@bunkat.com&gt;

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WIT