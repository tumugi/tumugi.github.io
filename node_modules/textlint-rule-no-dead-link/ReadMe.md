# textlint-rule-no-dead-link

[![Build Status](https://travis-ci.org/nodaguti/textlint-rule-no-dead-link.svg?branch=master)](https://travis-ci.org/nodaguti/textlint-rule-no-dead-link)
[![Dependency Status](https://david-dm.org/nodaguti/textlint-rule-no-dead-link.svg)](https://david-dm.org/nodaguti/textlint-rule-no-dead-link)
[![devDependency Status](https://david-dm.org/nodaguti/textlint-rule-no-dead-link/dev-status.svg)](https://david-dm.org/nodaguti/textlint-rule-no-dead-link#info=devDependencies)

[textlint](https://github.com/textlint/textlint) rule
to check if all links are alive.

This rule is mainly for Markdown documents, but it may also work for plain texts.

## Installation
```
$ npm install textlint-rule-no-dead-link
```

## Usage
```
$ npm install textlint textlint-rule-no-dead-link
$ textlint --rule textlint-rule-no-dead-link text-to-check.txt
```

## Options
Write your configurations into `.textlintrc`.

The default options are:
```
{
  "rules": {
    "no-dead-link": {
      "checkRelative": false,
      "baseURI": null,
      "ignore": [],
    }
  }
}
```

### checkRelative
Enable the dead link checks against relative URIs.
Note that you also have to specify the `baseURI`.

### baseURI
The base URI to use for all relative URIs contained within a document.

Example:
```
{
  "rules": {
    "no-dead-link": {
      "checkRelative": true,
      "baseURI": "http://example.com/"
    }
  }
}
```

### ignore
An array of URI string to be ignored, i.e. skipped from availability checks.

Example:
```
{
  "rules": {
    "no-dead-link": {
      "ignore": [
        "http://example.com/not-exist/index.html"
      ]
    }
  }
}
```

## Tests
```
npm test
```

## Contribution

1. Fork it!
2. Create your feature branch: `git checkout -b my-new-feature`
3. Commit your changes: `git commit -am 'Add some feature'`
4. Push to the branch: `git push origin my-new-feature`
5. Submit a pull request :D

## License

MIT License (http://nodaguti.mit-license.org/)
