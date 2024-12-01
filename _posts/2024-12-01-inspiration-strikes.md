---
title: New gear can inspire
date: 2024-12-01 09:38 -0500
categories:
  - Blogging
  - linux
tags:
  - writing
  - tips
  - setup
toc: true
comments: false
---
# Am I a blogger now?
It's been a very long time since I've done any consistent personal blogging. My goal hasn't changed: to have a place to put things I need to remember, things I learned that might help someone else, and maybe a rant or two. 

But the WP site languished, and the site before that, and before that, so why is there a new one? Well, first up I've been on and involved in the internet for long enough that not having any presence feels strange. Even if it's mostly idle. And the social media I used is in a sorry state. 

The other thing was workflows. I didn't really understand that until I built what I did yesterday, but going to a site that's outside where I normally scribble notes and write things down is a pretty high barrier to a short write up of something. There's a second level of intention needed. If I'm in a hot state (wanting to post something), I need to fire up a new tab, find the site, log in, get to the editor, write it, get to the publishing workflow, publish it. And there's friction in that act of writing because I need to remember formatting, switch tabs if I need to review something, wonder if copy paste is going to work. There's a surprising amount of friction to getting things done. Friction that happens during a hot state leads to abandonment.

And blogs languish.

## What's changed?
Before I get to the new workflow, something else changed: I replaced my decade old PC. And this time, I decided that instead of restoring a backup on a new Fedora install, I was going to start from scratch. That took more time on the front end of figuring out what I wanted in the PC and when it came to detailing what I'd installed vs was actually using in Fedora. The canvas wasn't entirely blank, since I had a page and a half of notes on what I had been doing, but I was very deliberate about installing software. To the point that this brand new PC did nothing but email and web browsing for about two weeks.

The first thing I decided to do with this new tool was very out of character. I took a look at the [ramalama](https://github.com/containers/ramalama) project to see how it was coming along. I have an older NVIDIA card, so I thought maybe I could test my cynical optimism on this local AI environment. The container I need isn't published yet, and I ran into a problem building it locally. Instead of walking away and waiting, I dug in and [actually figured it out](https://github.com/containers/ramalama/pull/473).  This isn't a big complicated fix, but it unblocked me and some others to have a discussion, and now there's 2 PRs in to update the CUDA detection. 

Sometimes, new gear will inspire you to do something different, something you might not have been able to try before. And when it works, it's fun. Even the process of writing my own detection was fun and useful, even if I didn't directly submit a PR based on that work.

## Something new, something old
In my deliberate approach to adding software, I took a long look at how I had been using [Obsidian](https://obsidian.md/) to organize notes and things. My personal setup was a bit of a mess, and I was also thinking about how to get notes off the system.  I have notes about setting up a git sync workflow, but just never got [my round tuit](https://en.wiktionary.org/wiki/round_tuit).  Starting a new vault with git from the jump seemed like a pretty simple idea. I use GitHub a lot, so my mind wandered to GitHub Pages as a publishing option.

But I'm not familiar with Jekyll and haven't looked at anything Ruby related in a very long time. A little searching for obsidian jekyll templates uncovered a whole bunch of options and something called a 'digital garden'. A little more poking around and I found [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy?tab=readme-ov-file), a Jekyll template that already had GitHub Actions integrated. Not needing to set up a local build environment and publish the static HTML from there was a big win. It also has a few nice streamlined options that focus on just blog posts, and not all the things Jekyll can do. The important part is Jekyll processes Markdown to static HTML and Obsidian stores notes in Markdown. I just wanted to get something from Obsidian into GitHub Pages, not run a full site, so I appreciate the approach.

## Get to building
I found all the parts I need: Obsidian to write things in Markdown, a community plugin to do Git things, and a GitHub Action based publishing template. This lets me open a vault in Obsidian and do everything there, instead of going to a blogging site. But the parts still need to be integrated into a working whole.

### GitHub Pages repository
The other thing that I like about Chirpy over the other templates I found was [the public template](https://github.com/cotes2020/chirpy-starter) they created. This makes the setup for the GitHub Pages and Actions super simple: create a new repository, clone locally, make posts, push to repo. There's also some really nice styling options for syntax, image handling, and comment providers too. 

Setting up a [GitHub pages user site](https://pages.github.com/) is pretty simple, just name the repo you create from the template `<yourusername>.github.io`. I want to use the provided Actions to do the build and deploy, so under **Settings** I found the **_Pages_** tab and changed the _Build and deployment_ drop down to GitHub Actions. You can find the workflow file for the GitHub action in the `.github/workflows` directory. It's pretty easy to follow along with, even if you are new to Actions. This is where you can control when the site gets rebuilt and published. It defaults to any push to main, unless you're updating the `README`, `LICENSE`, or `.gitignore`. One thing on my To Do list here is to add the `.obsidian` directory to that ignore list, since the vault is set up inside a folder of this repo. More on that in a minute. As soon as you create the repo, you should have a default Chirpy template available on your site. 
#### GitHub Authentication
To get things working smoother with the Obsidian git plugin I installed, it's easier to use a [Personal Authentication Token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) (PAT) than try to faff about with other methods. I found this out the hard way, so do as I say not as I did. Setting this up first will make things easier in the long run. You'll want to be using HTTPS not SSH to access the repo.

I used a [fine-grained token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-fine-grained-personal-access-token) to limit the permissions and the repository access. The token is limited to just the Page repo, has read access to Metadata, and read / write for code, commit status, pages, and workflows. This may be more than it really needs, but it's based on the git plugin requirements and that I thought it would need to publish the site.  It works fine, and you can experiment with tighter permissions if you like. This way I can push / pull directly from Obisidian just like you might from an IDE like VSCode. Other than making changes to the configuration of the site or updating the Obsidian templates, the entire process never leaves the open vault. This removes a lot of the friction I talked about before.
### Configure the starter template to your liking
If you know what you are doing, you can add your PAT to the HTTPS path for the repo when you first clone it. Otherwise you can do it like I did after the fact, by swapping out the remote after you clone the Pages repo:
```shell
git remote remove origin
git remote add origin https://PAT@github.com/username/username.github.io.git
```

If you do it this way, you may need to set the upstream for main when you push the next time.
```shell
git push --set-upstream origin main
```

Then I started the basic configuration on the template. This way I could test all the GitHub Pages and Actions integrations without having invested too much time. To this point, I've created a repo from a template, configured Pages, and cloned the repo locally.

I'm not using a custom domain, so I set up the URL to point to the `github.io` site, modified the title and timezone, and updated the default author information under `social`. I also decided I wasn't going to deal with offsite image hosting yet, so I made an `imgs` directory under the existing `assets` directory and dropped an avatar PNG there.

Commit all the changes, push, and the build triggers automatically. If you visit the Actions tab in the GitHub repo and click on the job, the deployment action will put the link to the site on the workflow diagram. Nifty!

There may be a few more tweaks you want to make to the site, but I'm going to move to the local portion of the workflow: integration with Obsidian
### Obsidian vault
This is probably where I spent the most time getting conventions sorted to make everything happy. One thing you'll need to deal with, the vault name will probably be ugly. I'm certain with enough time I could get the Jekyll template to use a path that's prettier as an Obsidian vault name, but for now the new vault should be created using _Open folder as vault_ and picking the `_posts` directory. This is where the template processes blogs from, and only if they match a particular criteria. So we can safely store Obsidian's operational files and a templates directory without those getting published.

Speaking of templates, that's the first thing I created. Jekyll uses the idea of front matter to handle metadata about the blog post. Chirpy has some examples and preset notions for [front matter](https://chirpy.cotes.page/posts/write-a-new-post/#front-matter), so I used that example to create `Front_Mattter.md` in the templates directory.

```
---
title: TITLE
date: {{date}} {{time}} -0500
categories: [TOP_CATEGORY, SUB_CATEGORY]
tags: [TAG]     # TAG names should always be lowercase
# description: Short summary of the post.
toc: false
comments: false
---
```
{: file="_posts/templates/Front_Matter.md"}

The `date` field uses the Obsidian variables to expand the date and time, and I set the timezone offset manually. The `title` field is what shows up as the blog title, and will show up as a editable field in the Properties of a note along with `categories` and `tags`. A nice bonus is that `toc` and `comments` will show up as check boxes, and act right in the final Markdown saved by Obsidian. The `tags` field also works as Obsidian tags, so you'll have the same local tags as the blog site.

With the template set up the way Jekyll wants to see a blog post, we need to see how it expects files to be named. According to Jekyll, posts are things that have dates, and expects to see something like `YYYY-MM-DD-slug.md`, where slug is the title that shows up in the URL, not the blog roll. The daily note in Obsidian already creates files that use that date format as the name, we just need to use our Front_Matter template with the daily note to get 99% percent of the way there. To write a new post, open a new daily note however works, then add some sort of unique short title as the `slug`. 

The `date` will auto-populate with the full timestamp, which the template will use to order posts. How you deal with `categories` and `tags` are up to you, but once you've used them, they'll show up in the Properties as drop downs. And Chirpy already has handling for trending, sorting, and displaying posts based on both.

The git plugin is a community plugin, so I had to enable that since this is a fresh install. After installing and enabling the plugin, the only option I changed was the _Merge_ strategy under **Pull** to use rebase instead of merge. Since I'm the only user I expect that will be a little bit easier to manage.  Once it's enabled, you get source control windows right in Obsidian. Opening it on the right pane looks very much like other tools I've used, so I like that I don't really need to drop to a terminal for git operations. I can stage changes, make comments, and sync the repo right in the same window I'm writing this post. The git plugin will pick up changes to the template or the workflow files, so even if I need to edit outside Obsidian to manage the site, I can still publish everything from here.  If I really want to use a different tool like VSCode for anything, that will work just fine too.

## What's next
Well hopefully more blogging is next. This was a fun little project on it's own, but eliminating friction that I felt when blogging before was the goal. One thing I've learned in the $DAYJOB is anything that pulls you away from the actual work of getting a thing done to do something else is a leading cause of abandonment (that hot state friction). Logging into a website is not writing a blog, it's friction to writing a blog. 

There's also already a few things I think I want to clean up with this config and some other things that could be interesting, like changing the CSS theme and setting up a custom domain. But for now, let's see about more content.