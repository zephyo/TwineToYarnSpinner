# Twine To Yarn Spinner

Converting twine to yarn spinner with regex in VSCode.

(I'm converting from Harlowe - I like Harlowe better because syntax highlighting - but you can use regex for any story format, really; the regexes will just be a little different.)

For each step, the first line of code is the regex; the second is what it should be replaced with.


1. Convert into proper node syntax

`:: (.+?)\n((.|\n(?!\n:: ))*)`

  title: $1
  ---
  $2
  ===

2. Place tags properly

`title: (.*)\[(.*)\]`

`title: $1
tags: $2`

3. Transform if, else, set, etc

(note: wont work for more than 2 levels of nested parentheses)

`\(((.)*?):[ ]?((?:[^)(]+|\((?:[^)(]+|\([^)(]*\))*\))*)\)(\\)?`

`<<$1 $3>>`

4. Get rid of trailing space
`<<((.)*) >>`

`<<$1>>`

5. Get rid of extraneous colons
`<<if:`

`<<if`

3. Transform conditionals

- first get all [[]]] so that it's [[]]\n]

`\[\[((.)*?)\]\]\]`

  [[$1]]
  ]

- then get all bracket after `(*)[` to be `()\n[`

  \(((.)*?)\)\[

  ($1)
  [

- then get all closing brackets to be `(*)\n[\n]`

  \(((.)*?)\)\n\[(([^\[]|\n)*?)\]

  ($1)
  [
  $3
  ]

- then again

  \n\]\]

  ]
  ]

- then find all else and end with endif
  <<else>>\n\[[ \n\t]?((.|\n)*?)\n\]

  <<else>>
      $1
  <<endif>>

- then find all else-if that don't end with else and endif

  ^<<else-if((.)*?)>>\n\[[ \n]?((.|\s)*?)\n\]

- then i manually replace through bc for the life of me i cant figure out how to regex an  else-if that isn't followed by an else...

  <<elseif$1>>
      $3
  <<endif>>

- then find all if that dont have endif; endif

(this doesn't really work but idk how to make it better....)

  ^<<if((.)*?)>>\n\[[ \n]?((.|\s)*?)\n\](?!.*<<else>>)

  <<if$1>>
      $3

4. Transform (display:)

  <<display "((.)*?)">>[\\]?

  [[$1]]
  
5. Transform markdown

- Get rid of header

  ### (.+?)

  <size=200%>$1


- Get rid of align

  =><=((.|\n)*?)<==

  Center: $1

  Body:
  

- Italicize

(doesn't include new line incase there are urls for imgs, etc)

  \/\/((.)*?)\/\/

  <i>$1</i>

- Bold

  \*\*((.|\n)*?)\*\*

  <b>$1</b>

- Subscript

  \^\^((.|\n)*?)\^\^

  Subtitle: $1


6. Get rid of of {}

  \{[ \n]?((.|\n)*?)[ \n]?\}

  $1

7. Get rid of \

  ((.)*)\\\n

  $1


8. Transform span classes outside of options marking them as new options 

  <span class='red'>((.)*)</span>

  <<set-option "new">>
  $1
  <<unset-option>>


9. Transform img classes

  <img src="((.)*)">(</img>)?

  Image: $1


10. Transform history:

  \(count: \(history: \), "((.)*)"\)

  visited("$1")


11. Get rid of passage name unless in a 'set' context

- remove it in and statements
   and \(passage:\)'s name is (not )?"(.)*"

  (empty)

- replace set context with getter

  \(passage:\)'s name

  currentTitle()

12. Transform comments

  <!-- ((.)*) -->

  // $1

13. other useful things

- make indent consistent

  ^  (?!(\s))

- remove double quotes

  "((.)*?),"
  
  $1,




