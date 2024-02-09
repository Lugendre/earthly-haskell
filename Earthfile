VERSION 0.8
IMPORT ./earthfiles/base/ubuntu
IMPORT ./earthfiles/base/alpine
IMPORT ./earthfiles/builder
IMPORT ./earthfiles/installer

ARG BASE_TAG=lugendre/earthly-haskell:

# TODO: multi cradle対応

build:
  FROM +ubuntu20.04-llvm12
  DO +INSTALL_GHCUP
  DO +INSTALL_GHC --GHC=9.6.4
  COPY --keep-ts ./cabal.* ./Setup.hs ./*.cabal ./LICENSE ./
  COPY --keep-ts --dir ./app ./src ./test ./
  RUN cabal build --ghc-options="-fllvm -pgmlo $GHC_LLVM_OPT_PATH -pgmlc $GHC_LLVM_LLC_PATH -optlo-O3 -mavx2"
