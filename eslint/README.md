ESLint Rules
============

Origami Tower JavaScript conventions for eslint.


## Installing

The only supported way to install the transform plugin is through [npm][].

> **NOTE**  
> If you don't have npm yet, you'll need to install [Node.js][]. All newer
> versions (4+) of Node come with npm.

```shell
$ npm install eslint-config-origamitower
```


## Usage

This package is a shared configuration file providing rules for the [ESLint][]
linter. You'll need to install ESLint separately.

To use these rules you use the `extends` feature of the `.eslintrc.*`
configuration file:

```json
{
  "extends": "origamitower"
}
```

You can override any of the rules by setting new values in your `.eslintrc.*`
configuration file.


## Supported platforms

The package requires ESLint 2+.


## Support

If you think you've found a bug in the project, or want to voice your
frustration about using it (maybe the documentation isn't clear enough? Maybe
it takes too much effort to use?), feel free to open a new issue in the
[Github issue tracker](https://github.com/origamitower/conventions/issues).

Pull Requests are welcome. By submitting a Pull Request you agree with releasing
your code under the MIT licence.

You can contact the author over [email](mailto:queen@robotlolita.me), or
[Twitter](https://twitter.com/robotlolita).

Note that all interactions in this project are subject to Origami Tower's
[Code of Conduct](https://github.com/origamitower/conventions/blob/master/code-of-conduct.md).


## Licence

[![CC0](https://licensebuttons.net/p/zero/1.0/88x31.png)](http://creativecommons.org/publicdomain/zero/1.0/)  
To the extent possible under law, [Quildreen Motta](http://robotlolita.me) has
waived all copyright and related or neighboring rights to Origami Tower
Conventions. This work is published from: Brazil

