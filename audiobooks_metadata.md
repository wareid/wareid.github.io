# Best Practices Guide for Audiobooks Internal Metadata

**Note: This guide is not intended to advise on best practices for external sales metadata (i.e. ONIX), only the contents of the files themselves.**

The [W3C Audiobooks Specification](https://www.w3.org/TR/audiobooks/) is a manifest format that allows publishers and content creators to provide users and retailers with a single, data-rich format that takes advantage of the best features of popular mobile listening applications and the open web platform.

One of the major features of the manifest format is the ability for publishers and content creators to provide their own data on the contents of the audiobook, including important details like title, author, narrator, and chapter details. This manifest, by creating a single way to express this information, allows publishers and retailers to work together to give users the best possible version of their audiobooks, without having to compile multiple versions of the data for different parties, in multiple formats.

The manifest format was developed by the Publishing Working Group in an effort to provide a single method for the audiobooks industry to deliver audiobooks through the supply chain, from creator to user and everywhere in between.

This best practices guide will explain some of the requirements of the format, and also how to take current formats and format them for easy conversion to the Audiobooks standard.

## Introduction: Requirements
The Audiobooks standard is flexible, but does have a handful of required fields. These ensure that user agents and reading systems are able to recognize and validate incoming content correctly.

Four fields are specified as *required*:
	- `conformsTo`
	- `@context`
	- `name`
	- `readingOrder`

The first two fields, for a valid audiobook, will always remain the same. These fields are used to determine what type of content it is, and what a user agent should use to validate that the content is what it claims to be. In your internal metadata, these fields can be part of a template, as they will not change from audiobook to audiobook.

`name` refers to the title of the audiobook, this is a text value that should correspond to the title of the audiobook, for the user to identify what the content is.

In a manifest, they would look like this:
```
	{
	“@context” : [
		“https://schema.org”,
		“https://www.w3.org/ns/pub-context”
		],
	“conformsTo” : “https://www.w3.org/TR/audiobooks/“,
	“name” : “Jane Eyre”,
	…
	}
```

The final required field is the `readingOrder`. The reading order is the authoritative list of audio files in the audiobook, and should be listed in the order they are meant to play in. The `readingOrder` is where the values for the chapter-level metadata reside. For the audiobook to be valid, the items in the reading order can only be audio resources, however the file type is not restricted to any particular type of audio file. `readingOrder` is a list of resources that the user agent will use to determine what to play and display to the user, depending on the type of user agent. For the `readingOrder`, the creator must include the URL (location) of the audio files, and their type. They should also include the name of the content represented in that file, and the duration of it. The `readingOrder` can be treated like a simplified table of contents, or a backup.
```
{
…
“readingOrder” : [
	{“name” : “Chapter 1”,
	 “url” : “chapter001.mp3”,
	 “encodingFormat” : “audio/mpeg”,
	 “duration” : “PT1234S”},
	{“name”: “Chapter 2”,
	 “url” : “chapter002.mp3”,
	 “encodingFormat” : “audio/mpeg”,
	 “duration” : “PT234S”},
	…],
…
```

## Building the Reading Order
As mentioned above, `readingOrder` is a required field, as it serves as the authoritative order and summary of the content of the audiobook. The specification allows for a lot of flexibility and detail in the `readingOrder`, but also maps cleanly to existing ways to track chapter metadata.

If you are using a spreadsheet to track information about your audiobooks, it is simple to track the details and convert them to the same format as audiobooks manifest, as long as the data is consistently structured. The specification was built on data from [Schema](https://schema.org), meaning that there is a library of fields that can be used to describe the content.

Audiobooks are structured in a number of different ways in terms of chapters or parts, and the `readingOrder` is flexible enough to handle any variation.

Example 1 - Audiobook with 1 file/chapter
```
{
…
“readingOrder” : [
	{“name” : “Chapter 1”,
	 “url” : “chapter001.mp3”,
	 “encodingFormat” : “audio/mpeg”,
	 “duration” : “PT1234S”},
	{“name”: “Chapter 2”,
	 “url” : “chapter002.mp3”,
	 “encodingFormat” : “audio/mpeg”,
	 “duration” : “PT234S”},
	…],
…
```

One important consideration is that a file can only be referenced in the `readingOrder` once. This means that if the audiobook is made up of longer "part" files, they can only be referenced once, and the `"name"` value would be up to the content creator for identification. To provide more detailed chapter metadata for the user agent, it is advised to provide a table of contents file that references the chapters using [Media Fragments](https://www.w3.org/TR/media-frags/).

Example 2 - Audiobook with shorter Audio Files and one Part or Chapter
```
{
…
“readingOrder” : [
	{“name” : “Chapter 1”,
	 “url” : “chapter001-1.mp3”,
	 “encodingFormat” : “audio/mp3”,
	 “duration” : “PT1234S”},
	{“name”: “Chapter 1”,
	 “url” : “chapter001-2.mp3”,
	 “encodingFormat” : “audio/mp3”,
	 “duration” : “PT234S”},
	…],
…
```
In this example I’ve chosen to leave the name of the two parts the same, but the name could be different to indicate a continuation or part 2. Some user agents might be able to combine the chapters if it is clear that the content is part of the same section (i.e. if they’re all named Chapter 1 or similar).

Example 3 - Audiobook with Parts and Chapters
```
{
…
“readingOrder” : [
	{“name” : “Little Red Riding Hood”,
	 “url” : “chapter001.mp3”,
	 “encodingFormat” : “audio/mp3”,
	 “duration” : “PT1234S”},
	{“name”: “Snow White”,
	 “url” : “chapter002.mp3”,
	 “encodingFormat” : “audio/mp3”,
	 “duration” : “PT234S”},
	…],
…
```
This book has parts and chapters within those parts. In this case it’s best to label the chapters accordingly. Alternately, this is a good opportunity for a detailed table of contents, since the navigation is so rich. It is still possible to do this with the `name` field, but it can be verbose. Some user agents may parse the `name` like in the previous example, but it is not guaranteed.

As seen in the examples, there’s 4 main fields recommended for each chapter item, `name` for identification, `url` to know where the content is, `encodingFormat` for the user agent to know what the content type is, and `duration` for calculating the length of the content. These 4 fields are easy to track in spreadsheets or other methods for managing metadata.

The order and relationship of items is declared by their order in the `readingOrder`. If a chapter or part is located at the beginning of the audiobook, it needs to be in the beginning of the `readingOrder`. There is not a field for "order" because the order is derived by the position of the item in the overall order. 

## Resources
In the [Audiobooks](https://www.w3.org/TR/audiobooks/) manifest there is a third section that is not mandatory, but most content creators will end up using, and this is the `resources` section. The purpose of `resources` is to store any non-audio content that is required for the audiobook, like cover images, table of contents, or supplemental content. Referencing this content is almost identical to the reading order, but this content will not appear in the reading order itself (there is one exception to this which I will explain in a later section).

Example 4 - Sample Resources for a basic Audiobook
```
{
…
“resources” : [
	{“rel” : “cover”,
	 “url” : “cover.jpg”,
	 “encodingFormat” : “image/jpeg”},
	{“rel” : “contents”,
	 “url” : “toc.html”,
	 “encodingFormat” : “text/html”},
	…],
…
```
For these two items, we have added a new field: `rel`. The purpose of this is to provide a hint to the user agent about what the purpose of this item is, in this example, the cover and the table of contents. `resources` is also not limited on what types of files can be included, so any file type is permissible, and it is up to the user agent to handle whether or not the content is processable. Some user agents may not display certain types of content (i.e. a spreadsheet), but anything is possible in the manifest.

The secondary purpose of `resources` is to allow content creators a simple and straightforward way to include supplemental content with their audiobook. This content could be a PDF, an EPUB, images, videos, or any other content believed to be important to the audiobook. Previously this content would be sent separately, but it can now be tracked in the same metadata.

## Accessibility
There is one exception to the “no non-audio resources in the `readingOrder`” rule, in Audiobooks, and that is `alternate`. Audiobooks is designed to be an accessible format as well, and to meet all the requirements, we reference a separate specification for [Synchronized Narration](https://w3c.github.io/sync-media-pub/synchronized-narration.html). This reference allows content creators to link text, audio, or video resources in sync to create more accessible media.

If your audiobook has `alternate` media, you reference it in the `readingOrder` as well as in `resources`.
```
…
“readingOrder” : {
	“name” : “Chapter 1 - The Story Begins”,
	“url” : “chapter001.wav”,
	“encodingFormat” : “audio/wav”,
	“duration” : “PT1234S”,
	“alternate” : {
		“url” : “chapter001_narr.json”,
		“encodingFormat” : “application/vnd.syncnarr+json”}
	},
“resources” : {
	“url” : “chapter001_narr.json”,
	“encodingFormat” : “application/vnd.syncnarr+json”}
…
```

## Building Audiobooks
Creating Audiobooks spec-compliant content was designed to be as adaptable from current tracking methods as possible. The goal was to create a format that used information publishers had in their possession, and provide a method for user agents to display it to users in a consistent manner.

We built the specification around a few major principles, the most important of them being that a user should always know “where” they are in the audiobook. Which chapter, section, and minute, as easily as possible.

When building your audiobooks, be it in this format or in a more general way for multiple distributors, consider how your data is structured, do you have all of the applicable information, and if not, how to get it.

In the first section we listed the required metadata for every audiobook:
	- `conformsTo`
	- `context`
	- `name`
	- `readingOrder`

In addition to this, consider the following fields as strongly recommended:
	- `author`
	- `readBy`
	- `duration`
	- `publisher`
	- `@id`
	- `inLanguage`

These fields give your listeners most of the information they need about the book, without relying on external metadata, which while useful does not always give the user everything they need, and many user agents don’t display it at all.
