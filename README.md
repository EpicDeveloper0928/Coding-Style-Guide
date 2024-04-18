# Coding Style Guide for Frontend Development.

This styleguide does not yet cover all potential aspects in detail — only on a high-level — but all code should be written to one of the following guides. Every line of code should appear to be written by a single person, no matter the number of contributors.

At a very high-level, try following rules
- Around 100 character wide columns
- Meaningful use of whitespace
- Descriptive comments, especially for functions that are exported to other files.

Currently, there are guidelines for
- [Commits](#commit-guidelines)
- [Branching](#branching)
- [TypeScript/JavaScript](#typescriptjavascript)
- [HTML](#html)
- [CSS](#css)

## Commit Guidelines

### Purpose

- To speed up the reviewing process.
- To help us write good and automated release notes.
- To help any developers, say five years into the future, to find out why a particular change was made to the code or why a specific feature was added.

### Structure

The commit message structure is based on [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0) and should always look as follows:

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

The commit contains the following structural elements, to communicate intent to other developers:

1. <type> can be one of the following
  - **fix**: a commit of the type `fix` patches a bug in your codebase (this correlates with [`PATCH`](http://semver.org/#summary) in semantic versioning).
  - **feat**: a commit of the type `feat` introduces a new feature to the codebase (this correlates with [`MINOR`](http://semver.org/#summary) in semantic versioning).
  - **docs**: a commit of the type changes the documentation.
  - **style**: a commit of the type `style` (re)formats code to match specific guidelines (no production code changes).
  - **refactor**: a commit of the type `refactor` refactors production code, e.g. renaming of a variable or function.
  - **test**: a commit of type `test` adds or refactors tests (no production code changes).
  - **chore**: a commit of the type `chore` e.g. changes the build process or auxiliary tools and libraries such as documentation generation (no production code changes).
  - **wip**: a commit of this type will be used mostly for experimental work, POCs and other non-ticket based tasks.
2. <description> should always start with a lower case letter and it should be written in imperative mode, that is as if you were commanding someone, e.g. "update", "add", "change" instead of "updated", "added", "changed", etc.
3. **BREAKING CHANGE**: a commit that has a footer `BREAKING CHANGE:`, or appends a `!` after the type/scope, introduces a breaking API change (correlating with [`MAJOR`](http://semver.org/#summary) in semantic versioning). A BREAKING CHANGE can be part of commits of any type.
4. A scope may be provided to a commit’s type, to provide additional contextual information and is contained within parenthesis, e.g., `fix(auth): fix multi-factor authentication for SSO users`.
5. Footers other than `BREAKING CHANGE: <description>` may be provided and follow a convention similar to git trailer format.

### Examples

#### Commit message with description and breaking change footer

```
feat: allow provided config object to extend other configs

BREAKING CHANGE: `extends` key in config file is now used for extending other config files
```

#### Commit message with ! to draw attention to a breaking change

```
refactor!: drop support for Node 6
```

#### Commit message with scope

```
feat(lang): add polish language
```

#### Commit message with multi-paragraph body and multiple footers

```
fix: correct minor typos in code

see #133 for details
```

## Branching

### Core Principals

- **One source of truth**: Every development effort always starts at the main branch. The code in the main branch is guaranteed to be tested and in production—there are no surprises. The main branch is protected and no one is allowed to push to this branch directly. New features or hotfixes can only be merged in via specific release branches. Before merging to the release branch, it has to be ensured that the latest code from the main is merged into the feature or hotfix branch.
- **Consistent naming**: Prefixes for branches help to find branches easier and understand their purpose. They also greatly simplify cleaning up the GIT. The name of the branch should be descriptive and is essential to help understand their purpose. Names that are only IDs of issues should be avoided.
- **Testing**: Branches can only be merged, especially when merging release branches into main, if all automated tests succeed.

### Branch Naming

Branch names should be descriptive, human readable and always be structured in the following way:

```
<type>/<name>
```

- `<type>` can be one of the types mentioned in the [commit guidelines](#commit-guidelines)
- `<name>` should be lower case, hyphen separated, short and descriptive. The name can also reference a specific issue by appending the issue ID, e.g. "fix/authentication-1" to provide a fix for issue 1.

In addition to the types mentioned above, there is a specific "release" type which follows the following naming convention: release/X.XX. All new features are merged into this release branch before being deployed to production (via the main branch). The branch name contains the version using the [major and minor numbers](https://semver.org/). Release branches are never deleted and can be used to go through history, check code at a specific time, or review updates.
  
### Tagging
  
Tagging is used to capture individual releases. The main branch is automatically tagged when a release branch is merged into main. The name of the tag is a combination of the branch name and an autoincrementing build number, e.g. release/1.2-build123.
  
### Merging
  
As work is happening on a feature or hotfix branch, small and self-contained commits are being pushed to these branches. While these commits help describe the process of building a feature or fixing an issue, they can clutter the Git history in release or main branches. To work around this clutter, commits in feaature and hotfix branches are always squashed before merging them in a release branch.
  
Commits in the release branch are not squashed before they are merged into main.

### Deployment

- main: Manually deployed to production environment via a Github Actions workflow dispatch event.
- release: Similar to the main branch, a release branch can also be manually deployed to staging/next environment.

### Examples
  
All charts below are simplified and e.g. don't show that the main branch is merged into e.g. any feature or hotfix branch before these are merged (squashed commits) into the release branch.

#### Simple

Two developers are working on two features and releasing them as part of a single release.

```
                         tag: release/x.xx-build
main            o === o =========== o
                |\     \           /
release/x.xx    | |     o —- o -- o
                | \         /    /
feat/xxx        |  o — o — o    /
                 \             /
feat/xxx          o - o — o — o
```

#### Hotfix after release

If we or a customer notice a minor issue after a new release—major issues should result in a rollback—a hotfix can be used to update the existing version.


```
                         tag: release/x.xx-build  tag: release/x.xx-build
main            o === o =========== o ================ o
                 \     \           /                  /
release/x.xx      |     o —— o —— o ···· o ————————— o
                  \         /             \         /
feat/xxx           o — o — o               \       /
                                            \     /
fix/xxx                                      o — o 
```
  
#### Complex

In certain situations, multiple releases branches can exist at a given time, e.g. if a new release is being worked on while applying a hotfix to the current release.

```
feat/xxx                          o ——— o ———— o
                                 /              \
release/x.xx                    o      o ——— o—— o ————— o — o
                               /      /                 /     \
main            o === o ===== o === o ================ o ===== o
                 \     \           /                  /
release/x.xx      |     o —— o —— o ···· o ————————— o
                  \         /             \         /
feat/xxx           o — o — o               \       /
                                            \     /
fix/xxx                                      o — o 
```


## TypeScript/JavaScript

All JavaScript/TypeScript code should be formatted with [`prettier`](https://prettier.io/). Since JavaScript, being a dynamic and loosely-typed language, is especially prone to developer errors, TypeScript should be used whenever possible. All TypeScript / JavaScript should be written to the [Airbnb JavaScript standard](https://github.com/airbnb/javascript) in the latest ECMAScript with the following exceptions:

- Use double quotes instead of single quotes

Any function that is exported should be annotated to communicate intent to other developers as long as the name is not descriptive enough. Annotation should follow the [JSDoc](https://jsdoc.app/) specification: Types, return types, etc. are only needed for JavaScript files whereas Typescript files only need to include a description.

React/TSX/JSX should be written to the [Airbnb React/TSX/JSX standard](https://github.com/airbnb/javascript/tree/master/react) with the following exception:

- Double quotes should be used for JavaScript within JSX (see section [Quotes](https://github.com/airbnb/javascript/tree/master/react#quotes))

## HTML

All HTML code should be formatted with [`prettier`](https://prettier.io/).

Please note that there's a specific styleguide for TSX/JSX which is mentioned in the [TypeScript/JavaScript](#typescript-javascript) section. As mentioned below, some of these rules may overrule the general HTML syntax guides mentioned below.

### Syntax

- Use two space indents, no tabs.
- Nested elements should be indented once (two spaces).
- Always use double quotes, never single quotes, on attributes.
- Always include a trailing slash in self-closing elements. TSX/JSX only: Always self-close tags that have no children.
- Always include an `alt` prop on `<img>` tags. If the image is presentational, `alt` can be an empty string or the `<img>` must have `role="presentation"`.
- Don’t omit optional closing tags (e.g. `</li>` or `</body>).
- Per HTML5 spec, there is no need to specify a type when including CSS and JavaScript files as "text/css" and "text/javascript" are their respective defaults.
- A boolean attribute is one that needs no declared value, meaning that there's no reason to add a value, e.g. `<input type="text" disabled>`.
- Always use semantic HTML

### Attribute Order

HTML attributes should come in this particular order for easier reading of code, especially for large files.

- `class`
- `id`, `name`
- `data-*`
- `src`, `for`, `type`, `href`, `value`, `width`, `height`, etc.
- `title`, `alt`
- `role`, `aria-*`
- non-standard syntax, e.g. for React

## CSS

All CSS code should be formatted with [`prettier`](https://prettier.io/). 

### Syntax

- *Indendation*: 2 space indent, no tabs.
- *Class names*: All strings in classes are delimited with a hyphen (-). Pick a name that is sensible, but somewhat ambiguous: aim for high reusability. For example, instead of a class name like `.site-nav`, choose something like `.primary-nav`; rather than `.footer-links`, favour a class like `.sub-links`. We should avoid using classes which describe the exact nature of the content and/or its use cases. Using a class name to describe content is redundant because content describes itself.
- *Selectors*: Always use an unambiguous, explicit selector with good Selector Intent, e.g. `.site-nav` instead of `header ul` — select what you want explicitly, rather than relying on circumstance or coincidence.
- *Specificity*: Keep the specifity low at all times. Doing so will instantly help you tame and manage your project, meaning that no overly-specific selectors are likely to impact or affect anything of a lower specificity elsewhere. It also means you’re less likely to need to fight your way out of specificity corners. This includes, but are not limited to,
  - not using IDs in your CSS
  - not qualifying classes
  - not chaining selectors
  - not using `!important` as a fix and only proactively, e.g. for helper or utility classes that are very specific
- *Multi-line CSS*: CSS should be written across multiple lines. This leads to a reduced chance of merge conflicts, because each piece of functionality exists on its own line.
- Meaningful use of whitespace: There should be one line between different CSS rulesets
- *Commenting*: As a rule, you should comment anything that isn’t immediately obvious from the code alone. That is to say, there is no need to tell someone that `color: red;` will make something red, but if you’re using `overflow: hidden`; to clear floats — as opposed to clipping an element’s overflow — this is probably something worth documenting. For large comments that document entire sections or components, we use a DocBlock-esque multi-line comment which adheres to our 100 column width.
- *Modern CSS*: Write CSS according to the latest CSS spec (can be automatically compiled to something that old browsers understand using [PostCSS Env](https://github.com/csstools/postcss-preset-env))
- *Custom Properties*: Should be used for most global styling, e.g. spacings, type, etc., and follow a [scalable naming convention](https://medium.com/digio-australia/a-scalable-naming-convention-for-style-variables-6363b916432a)

### Attribute Order

- Position, e.g. `position`, `top`, `z-index`, etc.
- Box Model, e.g. `display`, `width`, `height`, `border`, etc.
- Type, e.g. `font-family`, `color`, etc.
- Misc, e.g. `background`, `transform` etc.
