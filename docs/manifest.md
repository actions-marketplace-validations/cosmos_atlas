# Manifest

Atlas reads a canonical TOML-based manifest when publishing Cosmos SDK modules.
The manifest schema is defined as follows

- [[module]](#module)
  - [name](#name-required)
  - [description](#description)
  - [homepage](#homepage)
  - [keywords](#keywords)
- [[bug_tacker]](#bug_tracker)
  - [url](#url)
  - [contact](#contact)
- [[[authors]]](#authors)
  - [name](#name-required)
  - [email](#email)
- [[version]](#version)
  - [documentation](#documentation)
  - [repo](#repo-required)
  - [version](#version-required)
  - [sdk_compat](#sdk_compat)

## [module]

### `name` (required)

The name of the Cosmos SDK module. The name does not necessarily
have to be unique as module's can be forked from other teams and organizations.
Typically, a module will be named as `x/<name>`, where `<name>` is concise and
meaningful. We encourage that the name is defined as the relative path in the
module's repository. Note, the combination of `name` and `team` (see below) must
be unique.

```toml
[module]

name = "x/poa"
```

### `description`

The description is a short blurb about the Cosmos SDK module. Atlas will display
this with the module. This should be plain text (not Markdown).

```toml
[module]

description = "A short description of my module."
```

### `homepage`

The homepage field should be a URL to a site that is the home page for your module,
organization or team.

```toml
[module]

homepage = "https://interchain.io/"
```

### `keywords`

A list of one or more keywords describing the module.

```toml
[module]

keywords = ["bank", "transfer", "tokens"]
```

## [bug_tracker]

### `url`

A URL to a site that provides information or guidance on how to submit or deal
with security vulnerabilities and bug reports.

```toml
[bug_tracker]

url = "https://interchain.io/bugs"
```

### `contact`

An email address to submit bug reports and security vulnerabilities to.

```toml
[bug_tracker]

contact = "bugs@interchain.io"
```

## [[authors]]

### `name` (required)

The list of authors represents people or organizations that are considered the
"authors" of the module. A module may consist one or more authors, where each
author must have a unique name, typically their GitHub handle. However, the exact
meaning is open to interpretation ??? it may list the original or primary authors,
current maintainers, or owners of the package.

```toml
[[authors]]

name = "alexanderbez"

[[authors]]

name = "fedekunze"
```

### `email`

An optional email address to provide alongside an author's name.

```toml
[[authors]]

# ...
email = "alexanderbez@email.com"

[[authors]]

# ...
email = "fedekunze@email.com"
```

## [version]

### `repo` (required)

The repository field should be a URL to the source repository for your module.
Typically, this will point to the specific GitHub repository release/tag for the
module, although this is not enforced or required.

```toml
[version]

repo = "https://github.com/cosmos/cosmos-sdk/releases/tag/v0.39.1"
```

### `documentation`

The documentation field specifies a URL to a website hosting the module's documentation.
Typically, this is a Markdown file hosted in the module's root directory in GitHub.
In addition, publishers are encouraged to have documentation link to a specific
GitHub release or tag.

```toml
[version]

documentation = "https://raw.githubusercontent.com/cosmos/cosmos-sdk/v0.39.1/x/bank/spec/README.md"
```

### `version` (required)

The module version to be published. It is recommended publishers and authors follow
[Semantic Versioning](https://semver.org/). It is also recommend that you use
version numbers with three numeric parts such as 1.0.0 rather than 1.0.

```toml
[version]

version = "v1.0.0"
```

### `sdk_compat`

An optional Cosmos SDK version compatibility may be provided. This value is optional
and arbitrary. The value could signal a single compatible version or a range of
versions.

```toml
[version]

# ...
sdk_compat = "v0.40.x"
```
