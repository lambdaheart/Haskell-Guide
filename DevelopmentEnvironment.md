# Setting up a solid Haskell development environment on Windows (with GHCJS)

For me, learning Haskell as a language was relatively easy. My struggles began when I started getting serious with Haskell and doing some actual work in it.

I didn't struggle with the language or understanding some advanced concepts I struggled with tooling. Tooling in and of it self is fine and well thought out in my opinion but what was missing (until now) is a comprehensive tutorial / documentation on how to set up your development environment properly.

Coincidentally, this is what I think is missing from most other programming language courses. Absence of such tutorials might be fine if you are slowly growing into a programming language community and picking things up as you go, but if you are experienced programmer it is pain in the ass to slowly collect bits and pieces of information from thousand different places.

So, with that in mind, I present you a comprehensive tutorial on how to set up a solid and flexible Haskell development environment on Windows which also supports compiling with GHCJS.

## Installing Haskell

Personally I like to manually manage my compilers, but in this case we'll first cover a simple installation procedure with automated installer and then I'll show you a more flexible method.

### Method 01: Minimal Installer

[![01_Haskell_Minimal_Installer](https://img.youtube.com/vi/tcyaDUuQaCw/0.jpg)](https://www.youtube.com/watch?v=tcyaDUuQaCw)

I recommend using the minimal installer. Full installer comes with some included libraries but you are most likely going to need some other versions of those libraries for your future projects so it really doesn't matter if they are initially included or not. We'll download them later anyway.

You can download the minimal installer from the following link:

[https://www.haskell.org/platform/windows.html](https://www.haskell.org/platform/windows.html)

After you are done downloading it, run it (obviously). I suggest you go with all the defaults except when you come to the "Choose Component" section of the installer. There you should uncheck "Stack".

Stack is a package manager / build tool and a really cool piece of software which eased pains of development with Haskell for quite some time now, but with recent advances in Cabal ("official" build tool) development I think it's time to start using Cabal again.

You can also install Stack manually from the following [link](https://get.haskellstack.org/stable/windows-x86_64-installer.exe) in case it turns out you really need it.

### Method 02: Manual Installation

[![02_Haskell_Manual_Installation](https://img.youtube.com/vi/30KfCFmvk9U/0.jpg)](https://www.youtube.com/watch?v=30KfCFmvk9U)

Manual installation is my preferred way of doing things. You can download GHC (Glasgow Haskell Compiler) and Cabal from the following links:

+ [GHC download page](https://www.haskell.org/ghc/download.html)
+ [Cabal download page](https://www.haskell.org/cabal/download.html)

On those two pages you will find stable releases and also a list of older releases.

At the time of writing this guide, latest stable version of GHC is `8.6.5`. However, since I like to keep my GHCJS version in sync with my GHC version and the latest GHCJS version to date is `8.4` I will download `GHC 8.4.4` instead (you can find it under "Older releases" section). This can easily be changed later.

Also, current version of Cabal (`cabal-install` actually) is `2.4` but I suggest you upgrade to `3.x` once it is released since it should include a bunch of very nice improvements.

Obviously, I shouldn't have to mention that you want to download GHC and Cabal version for your operating system and architecture.

Since I'm using Windows and have a 64 bit operating system I have downloaded this two files:

+ ghc-8.4.4-x86_64-unknown-mingw32.tar.xz
+ cabal-install-2.4.1.0-x86_64-unknown-mingw32.zip

In my user directory I've created the `Haskell` directory which will contain files from these two archives. You are free to create this directory anywhere you like.

In this directory I've created `GHC` and `Cabal` directory with `Versions` folder into which I've extracted contents of two previously downloaded archives.

Here's the final directory structure:

```
C:\Users\lh\Haskell
├── Cabal
│   └── Versions
│       └── Cabal-2.4.1.0
└── GHC
    └── Versions
        └── ghc-8.4.4
```

This layout is useful because it allows me to have multiple GHC and Cabal versions and switch them up when I feel like it.

To make switching easier I've created symbolic links to the current version of Cabal and GHC with a static name `Link` in each directory. This way I don't have to modify my `Path` variable every time I decide to change my GHC / Cabal version. I can just relink new version to the `Link` link.

Here's how to do it from PowerShell:

```
PS C:\Users\lh\Haskell> cmd /c mklink /D .\GHC\Link .\GHC\Versions\ghc-8.4.4\
PS C:\Users\lh\Haskell> cmd /c mklink /D .\Cabal\Link .\Cabal\Versions\Cabal-2.4.1.0\
```

Since PowerShell doesn't contain `mklink` for some reason we need to execute it through `cmd` (the old prompt). This is why `mklink` is prepended with `cmd /c`.

After this you should have the following directory structure:

```
C:\Users\lh\Haskell
├── Cabal
│   ├── Link -> ./Versions/Cabal-2.4.1.0/
│   └── Versions
│       └── Cabal-2.4.1.0
└── GHC
    ├── Link -> ./Versions/ghc-8.4.4/
    └── Versions
        └── ghc-8.4.4
```

Next step is to add following two paths to your account `Path` variable (you can add it to the system path but this is more of a "personal" setting so I think this belongs to the user path variable):

```
C:\Users\lh\Haskell\Cabal\Link
C:\Users\lh\Haskell\GHC\Link\bin <-- notice the "bin" in GHC Link path
```

To add these two paths to your path variable you can press start and start typing "path" in the search bar. You should see the "Edit the system environment variables" item.

To check if everything works as it should open your console (close the previous ones because they still have the old environment variables cached) and type the following:

```
PS C:\Users\lh> ghc --version
The Glorious Glasgow Haskell Compilation System, version 8.4.4

PS C:\Users\lh> cabal --version
cabal-install version 2.4.1.0
compiled using version 2.4.1.0 of the Cabal library
```

You should get the version information of GHC and Cabal.

#### Useful Resources

+ [GHC user's guide](https://downloads.haskell.org/~ghc/latest/docs/html/users_guide/)
+ [Cabal user's guide](https://www.haskell.org/cabal/users-guide/)
+ [Stack user's guide](https://docs.haskellstack.org/en/stable/README/)

## Installing GIT (the right way)

[![03_Git_Installer](https://img.youtube.com/vi/eQsVp-TTCZI/0.jpg)](https://www.youtube.com/watch?v=eQsVp-TTCZI)

GIT comes with MSYS2 package. We don't nasty third party UNIX utilities form GIT installation polluting our pristine Windows environment. We'll do that our self later.

The reason why is because we'll later use MSYS2 as a package manager, and since GIT is installed in `Program files` directory we'd have to work from administrator GIT bash console to download any library or tool, and they would be downloaded into `Program files` which is not desirable.

1. When installing GIT in the "Adjusting your PATH environment" installer step you should choose "Git from the command line and also from 3rd-party software" option.
2. On the "Configuring the terminal emulator to use with Git Bash" select "Use windows default console window" option.
3. And just to be on the safe side, on the "Configuring extra options" step check "Enable symbolic links" as well.

## Installing MSYS2

[![04_MSYS2_Installer](https://img.youtube.com/vi/OjJp9tf2aoI/0.jpg)](https://www.youtube.com/watch?v=OjJp9tf2aoI)

Installing MSYS2 is pretty straight forward if you have read the [instructions on their page](https://www.msys2.org/). Just for the completeness sake I've also recorded a video of the installation procedure.

## Configuring MSYS2 And Compiling Cairo library

[![05_MSYS2_Configuration_Cairo_Installation](https://img.youtube.com/vi/RfvS7hJYqiU/0.jpg)](https://www.youtube.com/watch?v=RfvS7hJYqiU)

If you can compile [cairo](https://hackage.haskell.org/package/cairo) on Windows then you can do anything!

Cairo is the first Haskell library I've encountered that was complicated to get working on Windows, but in reality it's really easy. You just need to **know what you're doing** ;).

Cairo package provides Haskell bindings to [cairo graphics library](https://www.cairographics.org/) which was written in C... or C++ or something.

The point is, it's a pain in the ass to get and install because it is available as a stand alone, but it isn't, it's actually packaged in GTK but you have to have the right GTK and add it to the path... you get the picture.

Luckily we can use [Arch Linux's](https://www.archlinux.org/) package manager [pacman](https://wiki.archlinux.org/index.php/Pacman) within the MSYS2 UNIX like environment to download Windows cairo binaries.

As you will soon see, this Frankenstein monster works beautifully once you patch all the pieces together.

Let's first try to install `cairo` through `cabal` to see what happens. I'll be using `new-` style commands but if you are using `cabal >= 3.0` you can drop the `new-` prefix.

**Also, don't forget to run `cabal new-update` to update your local package database.**

We can "install" the `cairo` library by running the following command:

```
cabal new-install cairo --lib
```

This should give us a warning that some packages failed to install and that we should rerun the previous command with `-j1` flag to see the error.

```
cabal new-install cairo --lib -j1
```

After running this command we notice that the problem is in the missing `pkg-config` utility. This little command utility is used by build systems and such to tell them where certain libraries are located on the computer.

Since Haskell `cairo` library is looking for C++ `cairo` library it can't automagically find it without the help of `pkg-config`.

We can get the `pkg-config` from our newly installed MSYS2 console. Press `start` and search for "msys". You will notice that we get several results:

1. MSYS2 MSYS
2. MSYS2 MinGW 32-bit
3. MSYS2 MinGW 64-bit

We can use option `1` for now, just keep in mind that 32 bit version will have 32 bit libraries in it's environment (once we download them) and 64 bit version will obviously have 64 bit versions of libraries.

Great, so now you are hopefully in the MSYS console environment.

We can search for installable packages with the following command:

```
pacman -Ss name-of-the-package or keywords
```

Let's search for `pkg-config`:

```
lh@FloatingIsland MSYS ~
$ pacman -Ss pkg-config
mingw32/mingw-w64-i686-pkg-config 0.29.2-1 (mingw-w64-i686-toolchain)
    A system for managing library compile/link flags (mingw-w64)
mingw32/mingw-w64-i686-python2-pkgconfig 1.4.0-1
    A Python interface to the pkg-config command line tool
mingw32/mingw-w64-i686-python3-pkgconfig 1.4.0-1
    A Python interface to the pkg-config command line tool
mingw32/mingw-w64-i686-ruby-pkg-config 1.3.2-1
    Implementation of pkg-config in ruby (mingw-w64)
mingw64/mingw-w64-x86_64-pkg-config 0.29.2-1 (mingw-w64-x86_64-toolchain)
    A system for managing library compile/link flags (mingw-w64)
mingw64/mingw-w64-x86_64-python2-pkgconfig 1.4.0-1
    A Python interface to the pkg-config command line tool
mingw64/mingw-w64-x86_64-python3-pkgconfig 1.4.0-1
    A Python interface to the pkg-config command line tool
mingw64/mingw-w64-x86_64-ruby-pkg-config 1.3.2-1
    Implementation of pkg-config in ruby (mingw-w64)
msys/pkg-config 0.29.2-1 (base-devel)
    A system for managing library compile/link flags
```

As you can see we get several options. Notice that we can install both 32 and 64 bit versions of the packages. They are prefixed with `mingw32` and `mingw64` respectively.

Because I'm on a 64 bit system I'm going to install 64 bit packages, but note that sometimes, depending on what you are doing and what your dependencies are, you will have to install 32 bit version of some package on your 64 bit system. It's nothing unusual.

We can notice that we have some packages with `python` or `ruby` in them. I'm guessing those are `python` and `ruby` bindings / wrappers for `pkg-config` but we are looking for the raw `pkg-config` which is present in the following line:

```
mingw64/mingw-w64-x86_64-pkg-config 0.29.2-1 (mingw-w64-x86_64-toolchain)
    A system for managing library compile/link flags (mingw-w64)
```

So, `mingw-w64-x86_64-pkg-config` is the name of the package we want to install but before telling `pacman` to install it for us notice that we have `mingw-w64-x86_64-toolchain` in the parentheses besides the version number.

This tells us that `pkg-config` is a member of `mingw-w64-x86_64-toolchain` group of packages. This `toolchain` group obviously contains various tools and utilities for compiling so it wouldn't hurt to install the whole group along with the `pkg-config`. We can do this as though the group was a single package and just execute the following command:

```
pacman -S mingw-w64-x86_64-toolchain
```

You will be asked if you want to install the whole group or just parts of it so just press enter a few times until things start installing.

After the installation if we try to use [which](https://www.linux.org/docs/man1/which.html) command to see the location of `pkg-config` on our disk we will get the message that there is no such executable.

This is because we are within `MSYS` environment but the package we have installed belongs to `MinGW64`. Close your current `MSYS` console and open `MSYS2 MinGW 64-bit`.

If we run `which pkg-config` in `MINGW64` console we should see that it is located in `/mingw64/bin` folder.

Question is, where is `/mingw64` folder? Or even better, where is the `/` (root) folder? The root folder of our little UNIX like system is located in the directory where you've installed the msys2 package. In my case that is `C:\msys64`.

If we follow this logic then our `pkg-config` is located in `C:\msys64\mingw64\bin` along with all the other 64 bit libraries and programs that we have installed through MSYS console.

Right now `pkg-config` is only available through MSYS console, but I prefer to use PowerShell and have a "native" feel to my system. Because of that it is now time to pollute our system by adding `C:\msys64\mingw64\bin` to our system path.

In case you need to install some 32 bit packages don't forget to add `C:\msys64\mingw32\bin` to your system path as well.

We also need to add `bin` path of `MSYS` utilities as well, and to make sure everything plays nicely together order those paths like this:

1. C:\msys64\mingw64\bin
2. C:\msys64\mingw32\bin
3. C:\msys64\usr\bin

I've put 64 bit folder first, because I'm on a 64 bit system and I want 64 bit version of libraries to take precedence. This can sometimes not work very well so you'll have to massage your path and installed packages but from my experience it happens very rarely and it is a bridge you should cross when you come to it.

**When you are wondering why something is missing or not working, it is quite useful to use `which` to check where things are coming from.**

Once you've added those paths, don't forget to close your PowerShell console and fire it up again to reflect changes in your new path.

Now we should have some progress with our Haskell `cairo` installation. If we run `cabal new-install cairo` we will be greeted with a new message. This time the message is not about `pkg-config` but from `pkg-config` telling us that he can't find `cairo` (this time a C library not a Haskell one) on our machine.

This is because we haven't installed it yet. To do so, we need to go back to our MSYS console and use `pacman`.

If we search for `cairo` we again get a number of results, among which are some python and ruby bindings to `cairo` as well as `cairo` library it self. To install it run the following command:

```
pacman -S mingw-w64-x86_64-cairo
```

After that we can close our consoles, fire up PowerShell once again and finally install and compile Haskell `cairo` library by running:

```
cabal new-install cairo --lib
```

## Windows Subsystem For Linux

Awesome feature. It's like reverse [WINE](https://www.winehq.org/) and it allows us to run Linux software on Windows machine. Note that MSYS is only UNIX **like** environment and we have been using native Windows versions of `cairo` and `pkg-config` which will not be the case with [Windows Subsystem For Linux](https://docs.microsoft.com/en-us/windows/wsl/about) (aka. WSL).

WSL will allow us much simpler workflow with [GHCJS](https://github.com/ghcjs/ghcjs) without the need for messy compilation of GHCJS through Stack or tools like [Nix](https://nixos.org/nix/) package manager (although you can use Nix as well and I plan to cover it in future guides).

### Enabling WSL

[![06_WSL_Enable](https://img.youtube.com/vi/Kd_CTkziBc4/0.jpg)](https://www.youtube.com/watch?v=Kd_CTkziBc4)

To enable WSL you can either open a console as an administrator and then run following command in PowerShell:

```
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

Or press the `start` button and search for "Turn Windows features on or off" and enabling "Windows Subsystem for Linux" which is what I did.

After enabling it you should restart the computer.

### WSL Ubuntu Installation

[![07_WSL_Installation_Procedure](https://img.youtube.com/vi/goEYFy3NbMg/0.jpg)](https://www.youtube.com/watch?v=goEYFy3NbMg)

You can install [Ubuntu](https://www.ubuntu.com/) for WSL from Windows Store, but I prefer to install it manually.

You can download the installation package with the following command:

```
PS C:\Users\lh\Desktop> Invoke-WebRequest -Uri https://aka.ms/wsl-ubuntu-1804 -OutFile Ubuntu.appx -UseBasicParsing
```

This will download latest (at the time) 18.04 version of Ubuntu to your desktop (or where ever). Once the file has been downloaded you can install it by running:

```
PS C:\Users\lh\Desktop> Add-AppxPackage .\Ubuntu.appx
```

After that is done, press the `start` button and search for "Ubuntu" and hit enter.

You should get the Ubuntu's [bash](https://www.gnu.org/software/bash/) console / shell / what ever and the initial setup will start automatically.

You will have to enter username and password for this new "virtual" linux system.

**Make sure you remember your password**.

After that, you will have Linux powers on your Windows machine and you can even enter that `bash` environment from PowerShell simply by executing `bash` command.

Before continuing you should probably run

```
sudo apt update
sudo apt upgrade
```

to update your new Linux system.

### WSL GHC and Cabal Installation

[![08_WSL_GHC_Cabal_Installation](https://img.youtube.com/vi/v7GLCTGGczc/0.jpg)](https://www.youtube.com/watch?v=v7GLCTGGczc)

Ok, so the whole point of installing WSL was to get GHCJS the easy way. You can thank [Herbert V. Riedel](https://github.com/hvr) for this since he has provided us with several nice repositories with pre-compiled GHCJS / GHC versions optimized for WSL.

+ [GHC PPA](https://launchpad.net/~hvr/+archive/ubuntu/ghc/)
+ [GHC for WSL PPA](https://launchpad.net/~hvr/+archive/ubuntu/ghc-wsl)
+ [GHCJS PPA](https://launchpad.net/~hvr/+archive/ubuntu/ghcjs)

You can add this PPA's by executing following commands (obviously you should only add either GHC PPA or GHC for WSL PPA, not both):

```
sudo add-apt-repository ppa:hvr/ghc-wsl
sudo add-apt-repository ppa:hvr/ghcjs
sudo apt-get update
```

After adding PPA's and updating package database we can start by installing GHC and Cabal. Since GHCJS and GHC major versions need to be in sync (I think) and the latest GHCJS version at the moment is `8.4` I will install `GHC 8.4.4`.

Also, even though HVR has `cabal-install-3.0` available in the PPA it is still not officially released so I'm going to install version `2.4` instead.

```
lh@FloatingIsland:/mnt/c/Users/lh$ sudo apt install ghc-8.4.4
lh@FloatingIsland:/mnt/c/Users/lh$ sudo apt install cabal-install-2.4
```

With this done, GHC and Cabal still won't be in our path. Instead their binaries are available in the `/opt/ghc` and `/opt/cabal` folders and we need to add them to the path.

Layout of those two directories is similar to my "manual" windows setup and we have option to install multiple GHC and Cabal versions and switch the active one by running following commands:

```
sudo update-alternatives --config opt-ghc
sudo update-alternatives --config opt-cabal
```

Moving on, we need to add `/opt/ghc/bin` and `/opt/cabal/bin` directories to our path. To do so I've opened my `~/.profile` file with [VIM](https://www.vim.org/) by running `vim ~/.profile` and added following code at the end of this file:

```bash
# set PATH so it includes GHC from /opt folder
if [ -d "/opt/ghc/bin" ] ; then
    PATH="/opt/ghc/bin:$PATH"
fi
```

After that you can run `. ~/.profile` to update your environment without exiting and reloading `bash`.

You can check if you have GHC and Cabal by executing these commands:

```
lh@FloatingIsland:/opt/ghc$ ghc --version
The Glorious Glasgow Haskell Compilation System, version 8.4.4

lh@FloatingIsland:/opt/ghc$ cabal --version
cabal-install version 2.4.1.0
compiled using version 2.4.1.0 of the Cabal library
```

### WSL GHCJS Installation

[![09_WSL_GHCJS_Installation](https://img.youtube.com/vi/X4QQAIbaji8/0.jpg)](https://www.youtube.com/watch?v=X4QQAIbaji8)

Next we have to install GHCJS, which is also very straight forward. You can do it by running:

```
lh@FloatingIsland:/opt/ghc$ sudo apt install ghcjs-8.4
```

It will be installed into the `/opt/ghcjs/8.4/bin` directory so we need to add this to our path as well. Again, after opening `~/.profile` I've added the following code to the end of the file.

```bash
# set PATH so it includes GHCJS from /opt folder
if [ -d "/opt/ghcjs/8.4/bin" ] ; then
    PATH="/opt/ghcjs/8.4/bin:$PATH"
fi
```

And after sourcing my profile with `. ~/.profile` I can check if GHCJS was set up correctly by executing `ghcjs --version`.

### WSL Building Miso ToDo MVC Single Page Web Application

[![10_WSL_GHCJS_Building_Miso_ToDoMVC](https://img.youtube.com/vi/YIsZMLiSzlY/0.jpg)](https://www.youtube.com/watch?v=YIsZMLiSzlY)

Cool. Now that we have everything set up it's time to test our GHCJS compiling abilities :D

To do so I'll use Haskell [Miso](https://haskell-miso.org/) library written by [David Johnson](https://github.com/dmjio) and try to build [example ToDo MVC](https://todo-mvc.haskell-miso.org/) application.

Source can be found on [Miso's github repository](https://github.com/dmjio/miso/blob/master/examples/todo-mvc/Main.hs). I've downloaded the source by hand into `ToDoMVC` folder on my desktop and ran `cabal init` command inside of that folder to initialize the project.

After deleting all the cr** that is not needed and adding `cabal.project` file (new cabal feature) for this simple test `ToDoMVC` content looks like this:

```
ToDoMVC/
├── Main.hs
├── ToDoMVC.cabal
└── cabal.project
```
**cabal.project**

```cabal
packages    : ToDoMVC.cabal

constraints : base         ^>= 4.11
            , miso         ^>= 0.21
            , aeson        ^>= 1.4
            , containers   ^>= 0.6
            , jsaddle-warp ^>= 0.9
```

**ToDoMVC.cabal**

```cabal
cabal-version       : 2.4
name                : ToDoMVC
version             : 0.1.0.0

executable ToDoMVC
  main-is           : Main.hs
  build-depends     : base
                    , miso
                    , aeson
                    , containers
                    , jsaddle-warp
  default-language  : Haskell2010
```

**Main.hs**

```haskell
{-# LANGUAGE TypeOperators              #-}
{-# LANGUAGE OverloadedStrings          #-}
{-# LANGUAGE FlexibleInstances          #-}
{-# LANGUAGE TypeFamilies               #-}
{-# LANGUAGE DataKinds                  #-}
{-# LANGUAGE DeriveGeneric              #-}
{-# LANGUAGE ScopedTypeVariables        #-}
{-# LANGUAGE RecordWildCards            #-}
{-# LANGUAGE LambdaCase                 #-}
{-# LANGUAGE MultiParamTypeClasses      #-}
{-# LANGUAGE GeneralizedNewtypeDeriving #-}
{-# LANGUAGE ExtendedDefaultRules #-}
module Main where

import           Data.Aeson hiding (Object)
import           Data.Bool
import qualified Data.Map as M
import           Data.Monoid
import           GHC.Generics
import           Miso
import           Miso.String (MisoString)
import qualified Miso.String as S

import           Control.Monad.IO.Class
import           Language.Javascript.JSaddle.Warp as JSaddle

default (MisoString)

data Model = Model
  { entries :: [Entry]
  , field :: MisoString
  , uid :: Int
  , visibility :: MisoString
  , step :: Bool
  } deriving (Show, Generic, Eq)

data Entry = Entry
  { description :: MisoString
  , completed :: Bool
  , editing :: Bool
  , eid :: Int
  , focussed :: Bool
  } deriving (Show, Generic, Eq)

instance ToJSON Entry
instance ToJSON Model

instance FromJSON Entry
instance FromJSON Model

emptyModel :: Model
emptyModel = Model
  { entries = []
  , visibility = "All"
  , field = mempty
  , uid = 0
  , step = False
  }

newEntry :: MisoString -> Int -> Entry
newEntry desc eid = Entry
  { description = desc
  , completed = False
  , editing = False
  , eid = eid
  , focussed = False
  }

data Msg
  = NoOp
  | CurrentTime Int
  | UpdateField MisoString
  | EditingEntry Int Bool
  | UpdateEntry Int MisoString
  | Add
  | Delete Int
  | DeleteComplete
  | Check Int Bool
  | CheckAll Bool
  | ChangeVisibility MisoString
   deriving Show

main :: IO ()
main =
  JSaddle.run 8080 $ startApp App { initialAction = NoOp, ..}
  where
    model      = emptyModel
    update     = updateModel
    view       = viewModel
    events     = defaultEvents
    mountPoint = Nothing
    subs       = []

updateModel :: Msg -> Model -> Effect Msg Model
updateModel NoOp m = noEff m
updateModel (CurrentTime n) m =
  m <# do liftIO (print n) >> pure NoOp
updateModel Add model@Model{..} =
  noEff model {
    uid = uid + 1
  , field = mempty
  , entries = entries <> [ newEntry field uid | not $ S.null field ]
  }
updateModel (UpdateField str) model = noEff model { field = str }
updateModel (EditingEntry id' isEditing) model@Model{..} =
  model { entries = newEntries } <# do
    focus $ S.pack $ "todo-" ++ show id'
    pure NoOp
    where
      newEntries = filterMap entries (\t -> eid t == id') $
         \t -> t { editing = isEditing, focussed = isEditing }

updateModel (UpdateEntry id' task) model@Model{..} =
  noEff model { entries = newEntries }
    where
      newEntries =
        filterMap entries ((==id') . eid) $ \t ->
           t { description = task }

updateModel (Delete id') model@Model{..} =
  noEff model { entries = filter (\t -> eid t /= id') entries }

updateModel DeleteComplete model@Model{..} =
  noEff model { entries = filter (not . completed) entries }

updateModel (Check id' isCompleted) model@Model{..} =
   model { entries = newEntries } <# eff
    where
      eff =
        liftIO (putStrLn "clicked check") >>
          pure NoOp

      newEntries =
        filterMap entries (\t -> eid t == id') $ \t ->
          t { completed = isCompleted }

updateModel (CheckAll isCompleted) model@Model{..} =
  noEff model { entries = newEntries }
    where
      newEntries =
        filterMap entries (const True) $
          \t -> t { completed = isCompleted }

updateModel (ChangeVisibility v) model =
  noEff model { visibility = v }

filterMap :: [a] -> (a -> Bool) -> (a -> a) -> [a]
filterMap xs predicate f = go' xs
  where
    go' [] = []
    go' (y:ys)
     | predicate y = f y : go' ys
     | otherwise   = y : go' ys

viewModel :: Model -> View Msg
viewModel m@Model{..} =
 div_
    [ class_ "todomvc-wrapper"
    , style_  $ M.singleton "visibility" "hidden"
    ]
    [ section_
        [ class_ "todoapp" ]
        [ viewInput m field
        , viewEntries visibility entries
        , viewControls m visibility entries
        ]
    , infoFooter
    , link_
        [ rel_ "stylesheet"
        , href_ "https://d33wubrfki0l68.cloudfront.net/css/d0175a264698385259b5f1638f2a39134ee445a0/style.css"
        ]
    ]

viewEntries :: MisoString -> [ Entry ] -> View Msg
viewEntries visibility entries =
  section_
    [ class_ "main"
    , style_ $ M.singleton "visibility" cssVisibility
    ]
    [ input_
        [ class_ "toggle-all"
        , type_ "checkbox"
        , name_ "toggle"
        , checked_ allCompleted
        , onClick $ CheckAll (not allCompleted)
        ]
      , label_
        [ for_ "toggle-all" ]
          [ text $ S.pack "Mark all as complete" ]
      , ul_ [ class_ "todo-list" ] $
         flip map (filter isVisible entries) $ \t ->
           viewKeyedEntry t
      ]
  where
    cssVisibility = bool "visible" "hidden" (null entries)
    allCompleted = all (==True) $ completed <$> entries
    isVisible Entry {..} =
      case visibility of
        "Completed" -> completed
        "Active" -> not completed
        _ -> True

viewKeyedEntry :: Entry -> View Msg
viewKeyedEntry = viewEntry

viewEntry :: Entry -> View Msg
viewEntry Entry {..} = liKeyed_ (toKey eid)
    [ class_ $ S.intercalate " " $
       [ "completed" | completed ] <> [ "editing" | editing ]
    ]
    [ div_
        [ class_ "view" ]
        [ input_
            [ class_ "toggle"
            , type_ "checkbox"
            , checked_ completed
            , onClick $ Check eid (not completed)
            ]
        , label_
            [ onDoubleClick $ EditingEntry eid True ]
            [ text description ]
        , button_
            [ class_ "destroy"
            , onClick $ Delete eid
            ] []
        ]
    , input_
        [ class_ "edit"
        , value_ description
        , name_ "title"
        , id_ $ "todo-" <> S.ms eid
        , onInput $ UpdateEntry eid
        , onBlur $ EditingEntry eid False
        , onEnter $ EditingEntry eid False
        ]
    ]

viewControls :: Model ->  MisoString -> [ Entry ] -> View Msg
viewControls model visibility entries =
  footer_  [ class_ "footer"
           , hidden_ (null entries)
           ]
      [ viewControlsCount entriesLeft
      , viewControlsFilters visibility
      , viewControlsClear model entriesCompleted
      ]
  where
    entriesCompleted = length . filter completed $ entries
    entriesLeft = length entries - entriesCompleted

viewControlsCount :: Int -> View Msg
viewControlsCount entriesLeft =
  span_ [ class_ "todo-count" ]
     [ strong_ [] [ text $ S.ms entriesLeft ]
     , text (item_ <> " left")
     ]
  where
    item_ = S.pack $ bool " items" " item" (entriesLeft == 1)

viewControlsFilters :: MisoString -> View Msg
viewControlsFilters visibility =
  ul_
    [ class_ "filters" ]
    [ visibilitySwap "#/" "All" visibility
    , text " "
    , visibilitySwap "#/active" "Active" visibility
    , text " "
    , visibilitySwap "#/completed" "Completed" visibility
    ]

visibilitySwap :: MisoString -> MisoString -> MisoString -> View Msg
visibilitySwap uri visibility actualVisibility =
  li_ [  ]
      [ a_ [ href_ uri
           , class_ $ S.concat [ "selected" | visibility == actualVisibility ]
           , onClick (ChangeVisibility visibility)
           ] [ text visibility ]
      ]

viewControlsClear :: Model -> Int -> View Msg
viewControlsClear _ entriesCompleted =
  button_
    [ class_ "clear-completed"
    , prop "hidden" (entriesCompleted == 0)
    , onClick DeleteComplete
    ]
    [ text $ "Clear completed (" <> S.ms entriesCompleted <> ")" ]

viewInput :: Model -> MisoString -> View Msg
viewInput _ task =
  header_ [ class_ "header" ]
    [ h1_ [] [ text "todos" ]
    , input_
        [ class_ "new-todo"
        , placeholder_ "What needs to be done?"
        , autofocus_ True
        , value_ task
        , name_ "newTodo"
        , onInput UpdateField
        , onEnter Add
        ]
    ]

onEnter :: Msg -> Attribute Msg
onEnter action =
  onKeyDown $ bool NoOp action . (== KeyCode 13)

infoFooter :: View Msg
infoFooter =
    footer_ [ class_ "info" ]
    [ p_ [] [ text "Double-click to edit a todo" ]
    , p_ []
        [ text "Written by "
        , a_ [ href_ "https://github.com/dmjio" ] [ text "David Johnson" ]
        ]
    , p_ []
        [ text "Part of "
        , a_ [ href_ "http://todomvc.com" ] [ text "TodoMVC" ]
        ]
    ]
```

After all of this was set up we can finally get to work and try to build it. Inside the `ToDoMVC` directory open a `bash` shell and run:

```
cabal new-build ToDoMVC --ghcjs
```

Of course, don't forget to update the `cabal` package database by running `cabal new-update` beforehand.

This process will take a while the first time because `cabal` has to download a lot of packages and compile them. On consecutive runs and for future projects this process will be much much faster and smoother.


After `cabal` has finished building `ToDoMVC` we can find the results deeply nested within the newly created `new-dist` folder. Last line of `cabal` output should tell you the exact path.

Inside of that folder you will find all the JavaScript files as well as `index.html` file which you can open to test your application.

## Final Thoughts

So, this was a somewhat lenghty procedure but the thing is that for any serious project you need to get acquainted with this tools. It's not different for any other language.

In some cases all of this things come nicely bundled and hidden from you (e.g. Visual Studio) but when you stumble upon some complicated problem regarding your tool chain there is no escape from actually learning about the tools you are using.

---

It can be a pain to switch back and forth from `bash` to `PowerShell` so I recommend creating a `Makefile` for your project to ease your pain.

I intend to make a more comprehensive tutorial in the future covering some nitty gritty details which aren't usually covered but until then here's an example `Makefile` from one of my projects to give you the general idea on how to expand on this guide.

```makefile
.PHONY: js import import-bin server server-bin client client-bin client-bin-copy devdb testdb

windows := $(filter Windows_NT,$(OS))

################################################################################

static = ./Static
schema = ./Shared/sql/schema

jscdir = $(static)/jsc
imgdir = $(static)/img
cssdir = $(static)/css

jsaddle = --constraint=\"miso +jsaddle\"
project = --project-file=CookBook.project
restart = --restart=CookBook.cabal --restart=CookBook.project

################################################################################

bishbash = $(if $(windows),bash -l -c "$(1)",$(1))
mkclient = cabal new-build exe:Client --ghcjs $(project)
mkjscdir = mkdir -p $(jscdir)
gtbindir = $(call bishbash,cabal-plan list-bin exe:Client)

################################################################################

js: client-bin client-bin-copy

import:
	ghcid -c "cabal new-repl exe:Import $(project)" $(restart)

import-bin:
	cabal new-build exe:Import $(project)

server:
	ghcid -c "cabal new-repl exe:Server $(project)" $(restart)

server-bin:
	cabal new-build exe:Server $(project)

client:
	ghcid -c "cabal new-repl exe:Client $(project) $(jsaddle)" $(restart)

client-bin:
	$(call bishbash,$(mkclient))

client-bin-copy:
	$(call bishbash,$(mkjscdir))
	$(call bishbash,cp $(shell $(gtbindir)).jsexe/rts.js $(jscdir))
	$(call bishbash,cp $(shell $(gtbindir)).jsexe/lib.js $(jscdir))
	$(call bishbash,cp $(shell $(gtbindir)).jsexe/out.js $(jscdir))
	$(call bishbash,cp $(shell $(gtbindir)).jsexe/runmain.js $(jscdir)/run.js)

################################################################################

devdb:
	psql -q -f $(schema)/prepare_dev.sql
	psql -q -f $(schema)/schema_dev.sql
	psql -q -f $(schema)/permissions_dev.sql

testdb:
	psql -q -f $(schema)/prepare_test.sql
	psql -q -f $(schema)/schema_test.sql
	psql -q -f $(schema)/permissions_test.sql
```
