# Interactive quest / book format.
Specification for data format of interactive books / quests / dialogues / scenarios.

## Goals
There is [Ink narrative language](http://www.inklestudios.com/ink/), it works good enough, but its fat / complex and allows to solve one problem in multiple ways.

This format - attempt to create:
* Simple readable way to create graph of textual content.
* Without standalone compiler as requirement.
* Lightwight processing / runtime without complex requirements.
* Save / load feature.
* Not so painful localization feature.

## Format description
All data splitted to logical blocks - pages, first page will be used as entry point. Each page contains:
* Comment

    Optional one line comment, should be started from start of line, can be placed at any line:
    ```
    // This is one line comment.
    // Another line of comment.
    ```
* Header

    Contains start marker of page and logical name for redirect from others pages:
    ```
    -> My first page
    ```
    Name can contains spaces, numbers, dashes, underscores, latin characters, case insensitive.

    There is special page name - "END", it cant be used as user page name.
* Logics

    Optional block. Contains variable initializations / modifications:
    ```
    { rewardReceived = 1 }
    { ammoAmount = 123 }
    ```
    One block - one variable manipulation, each block should be placed on new line.

    Name of variable can contains numbers, underscores, latin characters, case insensitive.
    Names

    Type of all variables - integer (can be negative), scope of view - global space.

    By default value of non-exist variable is 0 (zero).
* Texts

    Contains main text content. All line breaks will be ignored (all text will be processed as one block), if you need multiple paragraphs - use "[b]" marker inside text:
    ```
    First paragraph of page text.[br]Second paragraph of page text,
    this line will be part of second paragraph too.
    ```
* Choices

    List of choices allowed to user. Each choice contains:

    * Choice prefix

        Symbol "*" at start of line.
    * Precondition

        Optional part. Its logic with conditional expression - result means visible state of choice:
        ```
        { rewardReceived > 10 }
        ```
        There is short version with variable only name:
        ```
        { rewardReceived }
        ```
        It will be unrolled internally to:
        ```
        { rewardReceived > 0 }
        ```
    * Text

        Text for user. Can be optional if its only one variant - redirection will be processed without delay.
        ```
        Cut red wire and pray...
        ```
    * Link

         Link to target page on selection of this variant. Should be valid name of one of exist pages.
         ```
         -> FindSomething
         ```
         There is special page name - "END", it should be used for stop execution flow (game over).

    Each choice should be placed on separate line:
    ```
    * Go to north -> GoNorth
    ```

Order of these blocks important and fixed (as described above), you cant change it.

## Example
```
// Comments can be placed anywhere as separate lines.
-> StartYourJourney
Its first paragraph of entry page,
and it will be at same paragraph too (line breaks are ignored).[br]
This is second paragraph of entry page.
* Enter to darky-dark cave at darky-dark dungeon... -> Fight

-> Fight
// "swordCount" will be our variable for conditional branching for user choices.
You see ugly monster! What you will do with him?
* Spit at his face! -> SpitToMonster
* { swordCount == 0 } Look around -> FindSword
* { swordCount > 0 } Stab and cut! -> KillMonster

-> SpitToMonster
Monster wiped off spittle and gobbled you up![br]
Its bad end for you...
* -> END

-> FindSword
// You will get another brand new sword each time when you will enter to this page.
{ swordCount += 1 }
You found rusty sword![br]
You already have swords: { swordCount }.
* Return back to fight -> Fight

-> KillMonster
The guts of monster lies on ground and he no longer looks so pleased after dead.[br]
Its good ending of your journey!
* -> END
```

## Implementation
* [Typescript/ javascript implementation](https://github.com/Leopotam/questbook-js.git).