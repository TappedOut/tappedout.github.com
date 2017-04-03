# Welcome

#tappedout.js

Just add this script tag to the head tag your blog or website. It provides two handy features: taking simple card names and deck-lists and creating a rich javascript interface with popover images, etc.
```
<script src="http://tappedout.net/tappedout.js"></script>
```

The parser will strip existing html and use a line by line evaluation of the contents to submit to tapped out for proper formatting.

## Card display

```
I'd like to thank <span class="mtgcard">Phyrexian Dreadnought</span> for existing. But really <span class="mtgcard">Serra Angel [M10]</span> is my jam.
```

The widget will turn the simple text into a link with image popover if it can find the card.

## Decklists

Elements with the class deck-list will be evaluated, parsed and built out.

Valid code:
```
<div class="deck-list">
    1x Card Name
    99 Card Name 2
    Ignored line
    9999x Card Name 3

    Sideboard|sideboard|Sideboard:
    2x
</div>
```

Numbers in a explicit block can be followed optionally by 'x'.  The word sideboard on its own line designates the following items are sideboard items. Lines starting with neither a number nor sideboard are ignored and removed in the final rendering.


## Decklist 2
Additionally, if you want to show off a deck you created on tapped out, you can do the following:
```
<div class="deck-list" data-stub="theres-a-sliver-for-that">&nbsp;</div>
```
Where the data-stub attributes contains your deck's stub.

<div class="deck-list" data-stub="theres-a-sliver-for-that">&nbsp;</div>


## Implicit Use

The widget will search for blockquote tags on your page.  If it finds one and the block contains a line that begins with a number followed by x, it will attempt to parse the content just like the explicit use.

```
<blockquote>
    12x Serra Angel
</blockquote>
```

This will not parse:
```
<blockquote>
    12 Serra Angel
</blockquote>
```
