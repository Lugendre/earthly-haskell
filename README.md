# [WIP] Earthly-Haskell

Earthlyでcabalを使ってHaskellをbuildするScript.

# Examples

```dockerfile
VERSION 0.8
ARG BASE_TAG="lugendre/earthly-haskell:"

IMPORT github.com/lugendre/earthly-haskell/base/alpine
IMPORT github.com/lugendre/earthly-haskell/builder
IMPORT github.com/lugendre/earthly-haskell/installer

build:
  FROM alpine+alpine3.19.1
  DO installer+SETUP_HASKELL --GHC=9.6.4 --HPACK_VERSION=0.36.0
  # Cache the base image.
  SAVE IMAGE --push "${BASE_TAG}alpine3.19.1-ghc9.6.4"
  WORKDIR /production
  DO v2+INIT
  COPY --keep-ts . .
  # Generate *.cabal files from package.yaml.
  RUN find . -type f -name package.yaml -exec hpack {} \;
  DO v2+CABAL --args="update"
  DO PRODUCTION_BUILD_STATIC --extra-args="--jobs --constraint=\"vector -boundschecks\"" --target="all"
  SAVE ARTIFACT output output

docker:
  FROM alpine:3.19.1
  COPY +build/output output
  EXPOSE 9091
  ENTRYPOINT ["./output"]
  SAVE IMAGE --push lugendre/earthly-haskell/examples:haskell
```
