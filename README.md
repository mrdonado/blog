# JDonado blog - JDonado's Readme
## [blog.jdonado.com](https://blog.jdonado.com)
Blog powered by [Jekyll](https://jekyllrb.com/) with a template based in [Travelogue](https://github.com/SalGnt/Travelogue);

## Dependencies installation

If you're running Mac OSX 10.11.06 El Capitan or higher, these are the commands
you'll need to get the blog up and running.

If you are using Linux or Windows, the commands will be pretty similar (just ignore the part with `-n /usr/local/bin`).

First of all, [install Ruby on your system](https://www.ruby-lang.org/en/documentation/installation/).

Then, run the following commands on the console.

```sh
 sudo gem update --system
 sudo gem install -n /usr/local/bin jekyll
 sudo gem install -n /usr/local/bin jekyll-feed
 sudo gem install -n /usr/local/bin jekyll-sitemap
 sudo gem install -n /usr/local/bin jekyll-paginate
 jekyll serve
```

## Updated instructions – macOS 13.1+ with ARM CPU

Use [Homebrew](https://brew.sh/) to install Ruby.

Follow the instructions on [the jekyll blog](https://jekyllrb.com/docs/installation/macos/)

Then install the rest of the dependencies.

```sh
gem install jekyll-feed
gem install jekyll-sitemap
gem install jekyll-paginate
```

Finally, run jekyll in development mode with `jekyll serve`
