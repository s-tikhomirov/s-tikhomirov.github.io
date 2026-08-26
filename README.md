# Sergei Tikhomirov

Personal homepage and blog, hosted on GitHub Pages with the Minima 2.5.1 theme.

The site has two public surfaces. Blog is the post list at the root.
About is biography, research, publications, talks, and contacts.

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

Open http://127.0.0.1:4000/ and http://127.0.0.1:4000/about
