[![Gem Version](https://badge.fury.io/rb/story_key.svg)](https://badge.fury.io/rb/story_key)


| Gem Version | Locale | Lexicon SHA |
|-------------|--------|-------------|
| 0.5.0       | Miami  | 3bfbbf9     |

Locale will not change until v1.0 release


# StoryKey

StoryKey is a proof of concept [Brainwallet](https://en.bitcoin.it/wiki/Brainwallet) inspired by [BIP39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki) written in Ruby. It converts an arbitrary string of data, such as a [cryptocurrency private key](https://en.bitcoin.it/wiki/Private_key), into an English paragraph intended for longterm human memory. It also assists in decoding the story back into its original form. Optionally, a visual representation of the paragraph is also provided using [OpenAI DALL-E](https://openai.com/dall-e-3).

Each story is provided in multiple formats:
* Humanized Text
  * Version locale header ("In Miami I saw...")
  * Enumerated phrases
  * Colorized parts of speech (adjectives, verbs, nouns)
  * Grammatical filler (articles, prepositions, conjunctions, punctuation)
* Tokenized Text
  * Ordered list of unique tokens
  * Space-delimited lowercase alphanumeric/dash
  * Useful as a seed phrase for generating derivative keys
* Graphical
  * AI-generated images via [DALL-E](https://openai.com/dall-e-3)
  * Requires OpenAI key


## Features

* Encodes arbitrary length data from 1 to 512 bits (default 256)
* Includes checksum for integrity
* Includes version slug to ensure accurate decoding
* Uses a repeating English grammar to aid in mnemonics
* Uses a lexicon curated for mental visualization
* Avoids word repetition
* Provides interactive command-line recovery

Each token of the story, which may be a single word or short compound phrase, encodes 10 bits. The checksum length is variable based on the input size and space available in the last two tokens after accounting for a 4-bit footer. Here are a few example key sizes along with their respective story and checksum sizes.

| Key bits | Story tokens | Checksum bits |
|----------|--------------|---------------|
| 64       | 8            | 12            |
| 128      | 14           | 8             |
| 192      | 21           | 14            |
| 256      | 27           | 10            |
| 384      | 40           | 12            |
| 512      | 53           | 14            |

An example key with its associated story, seed phrase, and image are shown below.

![KeyStory Example Text](examples/cli.png)

![KeyStory Example Image Card](examples/card.png)

```
Key:
J1FzKczQ4XjqJzrnAQxWYWpfmKhmGATo2SRDkQ82iFp6
Story:
In Miami I saw
1. a winded Mothra accompanying BB-8,
2. a dingy Beowulf grieving for a Pegasus,
3. a nutty Madonna swimming with Richard Feynman,
4. a psychotic Mondrian twirling an optician,
5. a woeful Nietzsche eviscerating Optimus Prime,
6. a faded George Orwell eating brownies with a mercenary,
7. and a barrister tilting an umpire.
Seed Phrase:
miami winded mothra accompanying bb-8 dingy beowulf grieving pegasus nutty madonna swimming richard-feynman psychotic mondrian twirling optician woeful nietzsche eviscerating optimus-prime faded george-orwell eating-brownies mercenary barrister tilting umpire
````

This paragraph or seed phrase can be deterministically decoded back into its original form using the same version of StoryKey. The locale of the story (e.g. `Miami`) identifies that version. During key recovery, an exception will be raised if:
 * the `version slug` does not match the current version of StoryKey
 * the embedded `checksum` does not match the expected value


### Lexicon Curation

The lexicon was selected using the following criteria:

* Anthropomorphism. All parts of speech - adjective, noun, and verb - must fit logically when composed into phrases. To accommodate, entries were selected based on how closely they could produce a mental image of commonly known anthropomorphic entities interacting with one another. To produce enough verbs, compound actions such as "eat breakfast" were also used.
   * Adjectives: personal physical qualities, moods, colors, textures
   * Nouns: famous people/characters, professions, animals
   * Verbs: physical actions connecting subject/object, favoring transitive, sometimes compound
* Visualization. Entries should be concrete vs abstract and convey vivid mental imagery.
* Cultural acceptability. Reject sexually suggestive and other controversial imagery.
* Eliminate similar base words across parts of speech.
* Balance brevity with clarity.


## Installation

Add this line to your application's Gemfile:

```ruby
gem 'story_key'
```

And then execute:

```
$ bundle install
```

Or install it yourself as:
```
$ gem install story_key
```


## Usage

This library may be used directly from the command line or by calling Ruby methods.

If you want to generate images of the story along with the text, create a file named `.env` in the project directory and add your [OpenAI key](https://beta.openai.com/account/api-keys) as an environment variable:

```
# .env
OPENAI_KEY=<your-api-key>
```

You must have [ImageMagick](https://imagemagick.org/index.php) installed locally.


### Command Line Usage

Invoke the command line interface by running `bin/storykey`.

```
StoryKey commands:
  storykey decode [STORY]  # Decode a story passed as an argument or from a file
  storykey encode [KEY]    # Encode a key passed as an argument or from a file
  storykey help [COMMAND]  # Describe available commands or one specific command
  storykey new [BITSIZE]   # Create a new key/story (default 256 bits, max 512)
  storykey recover         # Decode a story interactively
```

To see help on a specific command, run `bin/storyke --help [command]`.

The command line also features an interactive recovery tool to aid in converting a story back into its source key. Run `bin/storykey recover` to initiate the process:

![Key/Story Example](https://user-images.githubusercontent.com/104095/161376334-4a591100-e3fc-41ce-b931-4773bebc23fd.png)


### Ruby Usage

After installing the gem, you may run `bin/console` or `require` the gem in your own project.


### Generate new key/story

Generate a new random key and associated story.

```
# StoryKey.generate
 =>
["4eqfoXzMDyqQW6p8zAQj7c8KkynK5K2BW6D5Vfp7xCaQ",
 #<struct StoryKey::Story
  text=
   "In Miami I saw a dim Balrog eat hummus with an appraiser, a facetious scholar play badminton with an economist, a witty uncle insure Bruce Willis, an appreciative dolphin blare at a cyclist, a blissful James Bond undercut a connoisseur, a green Hugh Jackman eat cheese with a bison, and Elvis Presley snorkel with a counselor.",
  humanized=
   "In \e[31mMiami\e[0m I saw\n1. a \e[36mdim\e[0m \e[33mBalrog\e[0m \e[35meat hummus\e[0m with an \e[33mappraiser\e[0m,\n2. a \e[36mfacetious\e[0m \e[33mscholar\e[0m \e[35mplay badminton\e[0m with an \e[33meconomist\e[0m,\n3. a \e[36mwitty\e[0m \e[33muncle\e[0m \e[35minsure\e[0m \e[33mBruce Willis\e[0m,\n4. an \e[36mappreciative\e[0m \e[33mdolphin\e[0m \e[35mblare\e[0m at a \e[33mcyclist\e[0m,\n5. a \e[36mblissful\e[0m \e[33mJames Bond\e[0m \e[35mundercut\e[0m a \e[33mconnoisseur\e[0m,\n6. a \e[36mgreen\e[0m \e[33mHugh Jackman\e[0m \e[35meat cheese\e[0m with a \e[33mbison\e[0m,\n7. and \e[33mElvis Presley\e[0m \e[35msnorkel\e[0m with a \e[33mcounselor\e[0m.",
  tokenized=
   "dim balrog eat-hummus appraiser facetious scholar play-badminton economist witty uncle insure bruce-willis appreciative dolphin blare cyclist blissful james-bond undercut connoisseur green hugh-jackman eat-cheese bison elvis-presley snorkel counselor">]
```


### Encode an existing key

Produce an English paragraph given input data (e.g. a cryptocurrency private key):

```
# StoryKey.encode(key: '4eqfoXzMDyqQW6p8zAQj7c8KkynK5K2BW6D5Vfp7xCaQ')
 =>
 #<struct StoryKey::Story
 text=
  "In Miami I saw a dim Balrog eat hummus with an appraiser, a facetious scholar play badminton with an economist, a witty uncle insure Bruce Willis, an appreciative dolphin blare at a cyclist, a blissful James Bond undercut a connoisseur, a green Hugh Jackman eat cheese with a bison, and Elvis Presley snorkel with a counselor.",
 humanized=
  "In \e[31mMiami\e[0m I saw\n1. a \e[36mdim\e[0m \e[33mBalrog\e[0m \e[35meat hummus\e[0m with an \e[33mappraiser\e[0m,\n2. a \e[36mfacetious\e[0m \e[33mscholar\e[0m \e[35mplay badminton\e[0m with an \e[33meconomist\e[0m,\n3. a \e[36mwitty\e[0m \e[33muncle\e[0m \e[35minsure\e[0m \e[33mBruce Willis\e[0m,\n4. an \e[36mappreciative\e[0m \e[33mdolphin\e[0m \e[35mblare\e[0m at a \e[33mcyclist\e[0m,\n5. a \e[36mblissful\e[0m \e[33mJames Bond\e[0m \e[35mundercut\e[0m a \e[33mconnoisseur\e[0m,\n6. a \e[36mgreen\e[0m \e[33mHugh Jackman\e[0m \e[35meat cheese\e[0m with a \e[33mbison\e[0m,\n7. and \e[33mElvis Presley\e[0m \e[35msnorkel\e[0m with a \e[33mcounselor\e[0m.",
 tokenized=
  "dim balrog eat-hummus appraiser facetious scholar play-badminton economist witty uncle insure bruce-willis appreciative dolphin blare cyclist blissful james-bond undercut connoisseur green hugh-jackman eat-cheese bison elvis-presley snorkel counselor">
```

`key` may be in the form of a hexidecimal (`ab29f3`), a binary string (`1001101`), a decimal (`230938`), or a base58 string (`uMBca`). If not in the default base58, `format` must be provided.


### Decode an existing story

Recover source data (e.g. a cryptocurrency private key) based on the English paragraph:

```
# StoryKey.decode(story: 'In Miami I saw an official Benjamin Franklin transport Matt Damon')
 => "4NTM"
```


## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake spec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

When editing the lexicon, be sure to:
1. Run `rake lexicon:build` to re-generate the data file
2. Copy the lexicon SHA into `version.rb` as well as this README (if publishing)
3. Increment the semantic version of the gem (if publishing)

When incrementing the semantic version post-1.0, be sure to:
1. Create a new `VERSION_SLUG`, adhering to the locale convention
2. Append a row to the version reference at the top of this README


## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/jcraigk/story_key.


## License

The gem is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).
