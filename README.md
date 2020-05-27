# Grathwohl.me

## Get Started

- Clone the repo, then run `bundle install` inside the root.

## Make Changes & Publish To Grathwohl.me

- `bundle exec jekyll build` after making any new changes. New static site assets will be in `_site/`.
- Push changes to the `master` branch.
- SSH into grathwohl.me, `cd` to `/home/user-data/www/ultra/_site`, then copy the data: `cp -rv * ../../default/`
