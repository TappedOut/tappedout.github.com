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

# API Key Concepts

## CardSpec

A CardSpec is a representation of a card. Its a map, a hash, or a dict,
depending on the language. It can contain information, such as image, of
a specific printing, but it defaults to the latest one.

In general, where CardSpecs are returned by any API, you may passcard_fields=['attr',...']
as a GET variable to restrict the response to only those you care about.


    {
        "name": "Demonic Tutor",
        "slug": "demonic-tutor",
        "image_large": "http://...",
        "tapped_url": "http://tappedout.net/mtg-card/demonic-tutor/",
        "wizards_url": "...",
    }


# API Endpoints

## validator.tappedout.net

### Check for format of a single card

<a href='http://validator.tappedout.net/validate/?cards=["Black%20Lotus"]'>/validate/?cards=["Black Lotus"]</a>

### Check format for list of cards
<a href='http://validator.tappedout.net/validate/?cards=[%22llanowar%20elves%22,%22phyrexian%20dreadnought%22]'>/validate/?cards=["llanowar elves","phyrexian dreadnought"]</a>  

### Validate format of a list of cards
<a href='http://validator.tappedout.net/validate/?cards=["llanowar%20elves","phyrexian%20dreadnought"]&format=standard'>/validate/?cards=["llanowar elves","phyrexian dreadnought"]&format=standard</a>

## MTG Resources

### MTG Set Booster [Public]

Returns a response that's like opening a pack of cards.

URI: /api/mtg/boosters/?tla=count&pack_size=X [GET]

pack_size can be [14, 15], defaults to 15

    /api/mtg/booster/?rav=10&m10=5
    /api/mtg/booster/?rav=3&m10=3&9ed=4&pack_size=14

Response:  JSON map of the sets given, mapped to their packs (a list of CardSpecs). 

    {  
       "set": [  # Pack: List of CardSpec (see above)
            {"card_name": "Demonic Tutor" ... },
            {"card_name": "Phyrexian Dreadnought" ... },
       ]
    }

### Card Detail  (Developer Token)

URI /api/mtg/[card slug]/ [GET]

Response with a CardSpec

## Deck Resources

### Latest Decks [Public]

URI /api/deck/latest/[list name]/ [GET]

List name is a slug (lower case, hyphens replace spaces) and can flexibly be either

* Any of: 'top8', 'featured', 'latest', 'top8-latest'
* An MTG Format name
* A Deck Hub name (See http://tappedout.net/mtg-deck-builder/)
* A Card name

## Collection Resources

### Collection List [Public with cookie or API Key]

URI: /api/collection/collection/  [GET]  (all of all types)
URI: /api/collection/collection:[deck/cube/list/binder]/  [GET]   (all of type)

Response:

    "deck": [
        {"user_display": "<font color=\"black\">yea</font><font color=\"red\">GO!</font>",
         "name": "DOMINARIA - Tolaria [D10.1a] copy", 
         "url": "http://tappedout.net/mtg-decks/dominaria-tolaria-d101a-copy/", 
         "inventory": [["Tolaria West", {"b": "main", "qty": 1}], ...]}
    "cube": [
        {.....]

### Collection Detail [Public with cookie or API Key]

URI: /api/collection:deck/dominaria-tolaria-d101a-copy/  [GET]

you may pass card_fields GET variable to get a subset of inventory

Response:

     {"user_display": "<font color=\"black\">yea</font><font color=\"red\">GO!</font>",
      "name": "DOMINARIA - Tolaria [D10.1a] copy", 
      "url": "http://tappedout.net/mtg-decks/dominaria-tolaria-d101a-copy/", 
      "inventory": [["Tolaria West", {"b": "main", "qty": 1}], ...]}

### Inventory [Public with cookie]

Allows you to change inventory of a collection on TappedOut. You must have a TappedOut
cookie, so this mostly only works on client-side calls by logged-in users.

URI: /api/collection/*collection_type*/*collection_slug*/inventory/ [PUT]

*    collection_type [deck, cube, binder, list]
*    collection_slug - for decks, cubes, lists, the slug. for binder, the username

#### PUT  (JSON list of changes)
    ?changes=[{"quantity": "+3", "card": "demonic tutor", "board": "side"}]  Adds 3
    ?changes=[{"quantity": 3, "card": "demonic tutor", "board": "side"}]   Sets to 3
    ?changes=[{"quantity": 0, "card": "demonic tutor", "board": "side"}]   Removes all
    ?changes=[{"quantity": -3, "card": "demonic tutor", "board": "side"}]  Removes 3

Response: A string and status code, aka "OK" 200 or "Error" 400

### Cube Boosters [Public]

URI: /api/collection/cube/*cube_slug*/booster/?count=X [GET]

count can be anything under 100 and above 0 and an integer

you may pass card_fields as a GET variable if you want a subset of card attributes.

Response:  JSON list of packs in order given, each with a list of cardspecs. 

    [  # JSON list of packs
       [  # Pack: List of CardSpec (see above)
            {"card_name": "Demonic Tutor" ... },
            {"card_name": "Phyrexian Dreadnought" ... },
       ]
    ]

## Base Resources

### TappedOut User Session [Developer Access Token]

Returns a user and their displayname given a valid TappedOut cookie. Bear in mind the 'display' may be an html snippet.

URI: /api/v1/cookie/*cookie*/

    /api/v1/cookie/ffa6b0344df1e8fe775af3dd99df4a6/?access_token=API_ACCESS_TOKEN

Response:

    {
        "user": "Some User Name",
        "display": "<font color=\"blue\">Some</font> User Name",
    }