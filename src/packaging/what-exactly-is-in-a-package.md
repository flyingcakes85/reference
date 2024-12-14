# What exactly is in a package?

Continuing from last section, *what exactly is in a package*?

Package contains a compiled version of the software itself along with some necessities. So the next question is...

## ...What goes around a "software" anyway?

Honestly, I wish the answer were simple. I can compile down my C code into a single binary and *that* pretty much is the software.

But real world ain't that straightforward yet.

Say, your software makes API calls over the internet. It will need an http library, right? Do you start writing your own http library? Nope. You make a web search for http libraries for C language and end up at the iconic [LibCurl](https://curl.se/libcurl/) website. What now? Do you bundle LibCurl along with your software itself?

Well, you can. And thats called static linking. But often, you don't.

What you do is to dynamically link to the library (in our example, `libcurl.so`) and then expect it to be there on user's system during runtine. No need to include it with your application.

But hey, *who* ensures that `libcurl.so` will exist on user's system. Thats where the package manager says hi! It will see that `curl` is a dependency of your application and automatically pull it in when a user installs your application.

These are called **dependencies**. These are just the list of other packages which are required for your software to run.

The major block (dependencies) aside, what else goes into a package?
- **manual pages** are an aboslute must for most CLI utilities
- similarly, **desktop files** are important for GUI applications to show up in app launchers
- if an icon is showing up in the app launcher, it must need an **app icon**
- application must have some **configurations**, right?
- if it runs as a service, then **service files** are necessary
- for CLI applications, often **shell completions** need to be bundled
- **scripts**
    - pre install
    - post install
    - pre remove
    - post remove

Depending on the packaging format, the components might be more or less, but the idea remains same:

\\[
\text{Package} = \text{Software} + \text{Necessary components}
\\]