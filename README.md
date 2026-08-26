# Sergei Tikhomirov

Personal homepage and blog, hosted on GitHub Pages with the Minima 2.5.1 theme.

The site has four public surfaces. Home is identity, the research journey, and the third-person bio.
Publications is the paper list.
Talks is media and conference appearances.
Writing is the post list.

Live site: https://s-tikhomirov.github.io/

## Run locally

Gems install under `$HOME/gems` so they do not mix with apt's `/var/lib/gems`.

Install Ruby (Debian or Ubuntu):

```bash
sudo apt install ruby-full build-essential zlib1g-dev
```

Put this in `~/.bashrc`, then `source ~/.bashrc`:

```bash
export GEM_HOME="$HOME/gems"
export PATH="$HOME/gems/bin:$PATH"
```

In this directory, create a `Gemfile` (gitignored) with:

```ruby
source "https://rubygems.org"
gem "github-pages", group: :jekyll_plugins
```

Then:

```bash
gem install bundler
bundle install
bundle exec jekyll serve --livereload
```

Open http://127.0.0.1:4000/ , http://127.0.0.1:4000/publications/ , http://127.0.0.1:4000/talks/ , and http://127.0.0.1:4000/writing/
