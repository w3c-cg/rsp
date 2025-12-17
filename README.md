
# Specification 'rsp'

This is the repository for rsp. You're welcome to contribute! Let's make the Web rock our socks
off!

## Contributing

Please see [CONTRIBUTING.md](CONTRIBUTING.md) and [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) for details.

Please make changes through a [pull request](https://github.com/w3c-cg/rsp/pulls).

Any feedback, suggestions, or questions are welcome – please [open an issue](https://github.com/w3c-cg/rsp/issues/new) for that.

The [RSP CG wiki](https://www.w3.org/community/rsp/wiki/Main_Page) contains more information about the group, its activities, and how to get involved.

## Specifications

Currently, this repository contains the following specifications:

- RDF Messages – [source code](spec/messages.bs), [published version](https://w3c-cg.github.io/rsp/spec/messages)
    - Defines the base concepts of RDF Messages, RDF Message Streams, and RDF Message Logs.

Additionally, there is the [index.bs](index.bs) file that lists the specifications ([published version](https://w3c-cg.github.io/rsp)).

### Editing the specifications

We use [bikeshed](https://speced.github.io/bikeshed/) with [pipx](https://pipx.pypa.io/stable/) to build the specifications. To build a spec, run:

```bash
pipx run bikeshed spec spec/<spec-name>.bs
```

You can also start bikeshed in watch mode to automatically rebuild the spec on changes, and have it serve the spec on a local web server:

```bash
pipx run bikeshed serve spec/<spec-name>.bs
```
