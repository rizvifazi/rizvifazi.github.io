# Update 09/09/2020

I've tweaked and Added Few features to the Jekyll Powered Leonids theme by [renyuanz](https://renyuanz.github.io/leonids/)

Update Log:

| Description | Date |
| --- | --- |
| Added Read More button for the Longer Paragraphs | 09/07/2020 |
| Added Few Style Features | 09/07/2020 |
| Adding Pagination to move to Previous, Next Posts | In Future... |


Important! It's better to download the gzipped files instead of forking the repo. I would really appreciate if you could give me a star. 😁

This project is under MIT license, so feel free to make it your own.

# Leonids Jekyll Themes

**[Leonids](http://renyuanz.github.io/leonids)** is a clean Jekyll theme perfect for powering your GitHub hosted blog.

## What is Leonids?

* Responsive templates. Looking good on mobile, tablet, and desktop.
* Simple and clear permalink structure.
* Support for Disqus Comments.
* Support for multi-authors.
* **And** the Leonids (/ˈliːənɪdz/ lee-ə-nidz) are a prolific meteor shower associated with the comet [Tempel-Tuttle](https://en.wikipedia.org/wiki/55P/Tempel%E2%80%93Tuttle).

See a [demo](http://renyuanz.github.io/leonids/) hosted on GitHub.

## Quick setup

```
git clone https://github.com/rizvifazi/rizvifazi.github.io/
cd leonids
bundle exec jekyll server
```

Check out your awesome blog at `http://localhost:4000` and Cheers!

## Running with Docker

```
docker run --rm -it --volume=$PWD:/srv/jekyll -p 4000:4000 jekyll/jekyll:pages jekyll serve --watch --force_polling
```

## Resume Page by [@Skn0tt](https://github.com/Skn0tt)
Leonids features a simple resume page. It is divided up into five sections:

* Bio (Edit \_data/index/careers.yml)
* Education (Edit \_data/index/education.yml)
* Skills (Edit \_data/index/skills.yml)
* Projects (Edit \_data/index/projects.yml)
* About (Edit \_includes/sections/about.html)

You can put all your info into these files, and they will be featured on the resume page.

