# Simplebirb

Idea: a static site based alternative to twitter, but simpler

How: Just put files in a folder.

## Design goals

* Be backwards compatible with existing RSS readers. Do this by using a subset of [RSS 2.0].
* It should be possible (if not pleasant) to maintain a simplebirb site by hand
* It should be easy to write a generator for a simplebirb site
* It should be easy to write a simplebirb client
* Simplebirb sites should be possible to host with no dynamic content, and be hostable on free static site hosts, like:
    * github pages
    * netlify
* Identity should be based solely off a domain name/URL
* Support niceties, like:
    * Custom/Changable Usernames
    * Reposts
    * Replies
* Posts should be written with markdown. Posts should be limited to 500 bytes, including markdown syntax
* You should only see content from people you are subscribed to, or content reposted from people you are subscribed to.
* Immutability is not currently a design goal of simplebirb. Clients or indexes may decide to detect and display changes.
* simplebirb makes no guarantees or suggestions to how content should be displayed. This is left to convention and individual client information.

[RSS 2.0]: https://validator.w3.org/feed/docs/rss2.html

## Specification

This specification currently represents `v0.1.0` of SimpleBirb.

The content of the following files are described below.

```
* root/
\ - me/
|  \ - about.json
|  | - subscriptions.xml
| - posts/
   \ - folder
   | \ - farewell-january.md
   | | - so-long-february.md
   | - feed.xml
   | - goodbye-march.md
   | - hello-april.md
```

### `me/about.json`

This file is required. It contains information about the author of this simplebirb site.

All fields of this file must support UTF-8. All keys and values must be strings.

This file should have the following content:

| field name  | required? | Description          | Max Size (bytes) | Notes |
| :---        | :---      | :---                 | :---  | :--- |
| `name`      | yes       | Username             | 50    | |
| `signature` | N/A       | RESERVED             | N/A   | This must not be used. This is reserved for future usage |
| `photo_url` | optional  | User profile picture | 200   | |
| `about`     | optional  | User Description     | 500   | |
| `location`  | optional  | User Location        | 100   | |
| `website`   | optional  | User Home Page       | 100   | |

### `me/subscriptions.xml`

This file is required. It contains a list of simplebirb sites this user subscribes to.

This file must be valid [RSS 2.0]. Additionally, it must only use the following fields:

The file must have a single `<channel>` element.

* `title`: This field is currently ignored by simplebirb. For backwards compatibility, it should read "{}'s subscriptions", where "{}" is replaced by the "name" field of `me/about.json`.
* `link`: This field is currently ignored by simplebirb. For backwards compatibility, it should contain the root URL of the simplebirb site.
* `description`: This field is currently ignored by simplebirb. For backwards compatibility, it should read "{}'s simplebirb subscriptions", where "{}" is replaced by the "name" field of `me/about.json`.

No further fields should be used in the `<channel>` element.

Each subscription should be represented as a single `<item>` element. Each `<item>` element is required to have exactly one of each of the following sub-elements:

* `title`: This field is currently ignored by simplebirb. For backwards compatibility, it should contain the "name" field of the `me/about.json` of the followed user.
* `link`: This field must point to the root simplebirb url of the followed user.
* `pubDate`: This field must be a date time in [RFC822] format. It should be the date time of when this user was subscribed to.

No further fields should be used in any `<item>` sub-elements.

No two items should have the same link.

[RFC822]: https://www.ietf.org/rfc/rfc822.txt

### `posts/` folder

This folder is required.
The `posts/` folder contains all simplebirb posts by this user.
The folder may have sub-folders.
All posts must be in a markdown format, and must end with the extension ".md".
The `feed.xml` file for this user must exist at the root of the `posts/` folder, e.g. `posts/feed.xml`.


### `posts/feed.xml`

This file is required. It contains a list of simplebirb posts this user has made.

This file must be valid [RSS 2.0]. Additionally, it must only use the following fields:

The file must have a single `<channel>` element.

* `title`: This field is currently ignored by simplebirb. For backwards compatibility, it should read "{}'s simplebirb feed", where "{}" is replaced by the "name" field of `me/about.json`.
* `link`: This field is currently ignored by simplebirb. For backwards compatibility, it should contain the root URL of the simplebirb site.
* `description`: This field is currently ignored by simplebirb. For backwards compatibility, it should read "{}'s simplebirb feed", where "{}" is replaced by the "name" field of `me/about.json`.

No further fields should be used in the `<channel>` element.

Each post must be represented as a single `<item>` element.
Each `<item>` element should be ordered in reverse order, e.g. newest first.


Each `<item>` element is required to have exactly one of each of the following sub-elements:

* `title`: This field is currently ignored by simplebirb. For backwards compatibility, it should contain the file name of the post.
* `link`: This field must be a URL of a post that exists within the `posts/` folder of this site. It must link to a file in markdown format, that ends in the extension `.md`.
* `pubDate`: This field must be a date time in [RFC822] format. It must be the date time of when this user posted this message.

No further fields should be used in any `<item>` sub-elements.

### `posts/**/*.md`

All posts within the `posts/` folder must have a unique path from the root of the simplebirb site. Files with the same name may coexist in separate folders/subfolders.

The contents of each post file should be less than or equal to 500 bytes, including all markdown syntax.

The specific flavor of markdown used is not specified.

#### Reposts

If a user post is a repost of another user's post, the post file must start with a markdown link to the reposted post, with the link text containing "repost-of".

Any content after this markdown link is considered a comment on the repost. The content must be preceded by a blank line.

**Example**:

```markdown
[repost-of](https://jamesmunns.com/simplebirb/posts/goodbye-march.md)

This is an optional comment on the repost
```

#### Replies

If a user post is a reply to another user's post, the post file must start with a markdown link to the post replies to, with the link text containing "reply-to".

Any content after this markdown link is the reply to the post commented on.
It is not allowed to have a reply without further comment.
The content must be preceded by a blank line.

**Example**:

```markdown
[reply-to](https://jamesmunns.com/simplebirb/posts/goodbye-march.md)

This is a required comment on a reply
```


# License

Licensed under either of

- Apache License, Version 2.0 ([LICENSE-APACHE](LICENSE-APACHE) or
  http://www.apache.org/licenses/LICENSE-2.0)

- MIT license ([LICENSE-MIT](LICENSE-MIT) or http://opensource.org/licenses/MIT)

at your option.

## Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in the work by you, as defined in the Apache-2.0 license, shall be
dual licensed as above, without any additional terms or conditions.
