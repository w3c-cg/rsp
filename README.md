
# Specification 'rsp'

This is the repository for rsp. You're welcome to contribute! Let's make the Web rock our socks
off!

## Contributing

Please see [CONTRIBUTING.md](CONTRIBUTING.md) and [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) for details.

## Specifications

<!-- TODO: update URLs once we get the CI working -->
<!-- TODO: add a simple index.html that links to the specs -->

- RDF Messages – [source code](spec/messages.bs), [published version](https://www.pieter.pm/rdf-messages)

### Editing the specifications

We use [bikeshed](https://github.com/speced/bikeshed) with [pipx](https://pipx.pypa.io/stable/) to build the specifications. To build a spec, run:

```bash
pipx run bikeshed spec spec/<spec-name>.bs
```

You can also start bikeshed in watch mode to automatically rebuild the spec on changes, and have it serve the spec on a local web server:

```bash
pipx run bikeshed serve spec/<spec-name>.bs
```
