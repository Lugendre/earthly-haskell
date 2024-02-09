# [WIP] Earthly-Haskell

Earthlyでcabalを使ってHaskellをbuildするScript.

# Examples

```dockerfile
VERSION 0.8
ARG BASE_TAG="lugendre/earthly-haskell:"

IMPORT github.com/lugendre/earthly-haskell/base/alpine
IMPORT github.com/lugendre/earthly-haskell/builder
IMPORT github.com/lugendre/earthly-haskell/installer

production-build-static:
  FROM alpine+alpine3.19.1
  DO installer+SETUP_HASKELL --GHC=9.6.4 --HPACK_VERSION=0.36.0
  # Cache the base image.
  SAVE IMAGE --push "${BASE_TAG}alpine3.19.1-ghc9.6.4"
  WORKDIR /production
  COPY --keep-ts . .
  # Build only dependencies.
  DO builder+GENERATE_PACKAGE_CABAL
  DO builder+DEPS --LTS_VERSION=lts-22.9
  # Cache the dependencies.
  SAVE IMAGE --push "${BASE_TAG}product-alpine3.19.1-ghc9.6.4"
  DO builder+PRODUCTION_BUILD_STATIC \
    --TARGET=all \
    --JOBS=4 \
    --WITH_VECTOR=1 \
  # Save static binaries to local.
  FOR binary IN $(cabal list-bin all)
    SAVE ARTIFACT $binary AS LOCAL output/
  END
```
