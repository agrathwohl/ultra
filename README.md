# Grathwohl.me

## Get Started

- Clone the repo, then run `bundle install` inside the root.

## Make Changes & Publish To Grathwohl.me

- `bundle exec jekyll build` after making any new changes. New static site assets will be in `_site/`.
- Push changes to the `master` branch.
- SSH into grathwohl.me, `cd` to the repo inside of `/home/user-data/www/`, then copy the data in `_site/` to `../../default/`
