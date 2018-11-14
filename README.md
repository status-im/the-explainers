# The Explainers

The Explainers Initiative is an open effort to bring technical content regarding the Serenity upgrade of the Ethereum blockchain closer to non-technical and semi-technical communities.

Inspired by the very successful [Peer Review at SitePoint](https://github.com/sitepoint-editors/php-peers) and [Bitfalls](https://github.com/sitepoint-editors/php-peers), the purpose of this repository is to be able to publicly draft Ethereum 2.0 content (tutorials, explanations, guides) and get feedback on in-progress posts by anyone who's interested - peer review style.

## What is peer review?

Peer review is the process of peers (those equal in knowledge and/or power) providing constructive criticism on each other's content. Each article and each amendment to an article made by someone other than the author is a pull request itself. The feedback thus comes in the form of (usually written) comments or pull requests and comments on pull requests.

While this does present a barrier to non-technical contributors, this is on purpose. By approaching content as code and encouraging technical users to contribute in any way, we cover the widest possible range of interests and contexts but keep the qualifications of the pool of contributors largely intact.

## How do I join as reviewer/author?

Note that no professional author will take _constructive_ criticism personally. However, given the amount of effort that can go into producing a good long-form post or tutorial, it's rare for people not to get emotionally attached to their work, and the wrong comment can ruffle feathers. I'll do my best to moderate and maintain diplomacy, but the general rule is: do unto others as you would have them do unto you. Don't be unnecessarily cruel, don't be spiteful, and don't be competitive. This is a collaborative effort.

### To become an author

1. Fork this repo
2. Create a folder for yourself in Authors, preferably kebab-case. See those already in there for inspiration.
3. Every post goes into its own folder, prefixed with the ordinal number of that post, and continuing with kebab-case of the title. For example, if you're writing about "validators in ethereum" and it's your first post, the folder should be called `01-validators` or something of the sort.
4. Every post's folder should have the subfolder `xx_XX` corresponding to the locale the content is written in. For English posts this will be `en_US` or `en_UK`, depending on your preference.
5. The content should be in a file called `final.md` in [Markdown](https://guides.github.com/features/mastering-markdown/) format.

In short, it should look like this:

![](https://imgur.com/vZNBua8.png)

### To become a reviewer

1. Just chime in with comments.

That's it.

Okay, you can do a bit more if you want to.

- to contribute a fix to an already published post, send a PR with the fix
- to contribute a fix to a post that's in draft and being discussed (i.e. not merged), comment on that PR or even do a PR on that PR
- to discuss in general, check out the open issues and PRs and let your voice be heard in the comments

Note that fixes include everything from grammar and phrasing (less important) to content and breaking down explanations (uber important).

Other than that, you can also help by doing the following:

- suggest and discuss article topics under issues
- recruit more writers / developers to chime in
- help with grammar / phrasing if that's your forte
- donate your open source code and knowledge if you're not the writer type, and have someone else translate it to _humanese_ for you (feel free to ping me - @swader - in an issue and I'll chime in asap!)

## What's in it for me?

- you help the community and spread the good word of Ethereum
- you train your writing skills and build a portfolio
- you get kudos when we implement some kind of Kudos reward system
- maybe other rewards later on ¯\_(ツ)_/¯
- you could get hired as a tech writer for Status 😱
- amazing new connections

## Usage rights

After a post has been processed by many people and determined to be publishable, who owns it? Who can publish it?

Well, it's all public domain so technically, _anyone_. We don't feel like we need to put any more constraints on the methods here than there already are - if someone crosses some lines really badly, we'll deal with that on a case by case basis. However, the following is implied:

- the content produced here is intended for publication on https://our.status.im and maybe https://studio.status.im. We ask that you not publish the content elsewhere verbatim, as that hurts everyone (duplicate content penalties for SERP etc.) Short snippets and quotes from the articles are, of course, okay.
- the translations of content can be published elsewhere. We'll leave that up to the interested parties to decide. Our aim is to disseminate the content as far and as wide as possible.

If you'd rather keep all rights or use the content commercially, we ask that you leave us alone and open your own similar repo - it's why it's public after all.
