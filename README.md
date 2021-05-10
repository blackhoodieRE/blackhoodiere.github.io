# blackhoodie.github.io

## Local build on your machine

Configure where to put the dependencies, here in a subdirectory 'vendor/bundle' in the current directory:
```
bundle config set --local path 'vendor/bundle'
```

Fetch and install the dependencies:
```
$ bundle install
```

Then you can build and run the development web server:

```
$ bundle exec jekyll serve
```

The website can be accessed at http://localhost:4000 and the development server will watch the directory for changes and rebuild.

## Structure

- `_data/navigation.yml` defines the menu
