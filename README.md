# nix-runghc

A tiny, Haskell-specific wrapper for the [`nix-shell` script interpreter][nix-shebang-release]. Extracts package dependencies specified with `-XPackageImports` and constructs a `nix-shell` command.

### Installation

Put the script on your `$PATH`:

```bash
$ curl https://raw.githubusercontent.com/jmackie/nix-runghc/master/nix-runghc > SOME_PATH_DIRECTORY/nix-runghc
```

### Example

**Main.hs**
```haskell
#! /usr/bin/env nix-runghc
#! ghc=842
{-# LANGUAGE LambdaCase        #-}
{-# LANGUAGE OverloadedStrings #-}
{-# LANGUAGE PackageImports    #-}
module Main where

import           "aeson" Data.Aeson                      ((.:))
import qualified "aeson" Data.Aeson                      as Aeson
import qualified "bytestring" Data.ByteString.Lazy.Char8 as ByteString
import qualified "HTTP" Network.HTTP                     as HTTP
import qualified System.IO                               as IO

-- Some actual code here...

```

Provided `nix-runghc` is available on your path and the module (above) is executable, you can run it with:

```bash
$ ./Main.hs
```

Which will end up invoking: 

```bash
$ nix-shell \
    --packages "haskell.packages.ghc842.ghcWithPackages (pkgs: with pkgs; [aeson bytestring HTTP])" \
    --pure \
    --command "sed '/^!#/d' ./Main.hs | runghc --ghc-arg=-XPackageImports; exit"
```

### Development Mode

I feel lost without [`ghcid`][ghcid] these days. So I added the ability to drop into a configured `nix-shell`, from which you can fire up `ghcid` or your `editor` or whatever.

```bash
$ nix-runghc --shell ./Main.hs
$ nix-runghc -v --shell ./Main.hs # verbose output
$ nix-runghc --shell --hoogle ./Main.hs # with docs
```


[nix-shebang-release]: https://nixos.org/releases/nix/nix-1.9/manual/#ssec-relnotes-1.9
[ghcid]: https://github.com/ndmitchell/ghcid
