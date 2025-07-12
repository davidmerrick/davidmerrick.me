My personal website, built with [Jekyll](https://jekyllrb.com/).

https://www.davidmerrick.me

# Running locally

Run `docker-compose up`
Visit http://localhost:4000

# Infrastructure

This site is hosted on CloudFlare Pages. Highly recommend.

# Todo
- Stand up a prd deploy to CloudFlare

# Manually building
```shell
docker run --rm \
  --volume "$PWD:/srv/jekyll" \
  --volume "$PWD/vendor/bundle:/usr/local/bundle" \
  -w /srv/jekyll \
  jekyll/builder:4 \
  jekyll build --destination ./_site
```