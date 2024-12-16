# Static site URL shortener with Linky

## Background

For over 2 years now, I've been running [Suri](https://github.com/surishortlink/suri) for my static site URL shortener. Recently while updating the links, I noticed that Suri builds were failing. I had to re-clone the upstream repo for Suri and add my short links as JSON.

While it did work, I wondered why should building a simple static site with 99% stuff predefined be so cumbersome. I stopped giving thoughts to this, since now my site was working. But few days back, Netlify sent me an email about their changed pricing tiers. Being too lazy to see the changes, I decided to write and host my own URL shortener, which doesn't need a lengthy build / deploy process.

## How does a static site URL shortener work?

First, understand how a server side HTTP redirect would work. [RFC 9110](https://www.rfc-editor.org/rfc/rfc9110) defines HTTP semantics. As per this spec, the [`HTTP/1.1 302`](https://www.rfc-editor.org/rfc/rfc9110#section-15.4.3) response along with a `Location` header should cause a redirect.

But for static site hosting like GitHub Pages, generating such an HTTP response is not possible. So what do we do?

The solution is to use [meta refresh](https://www.w3.org/TR/WCAG20-TECHS/H76.html) to ceate an instant client side redirect.

In short, adding the following to your HTML page will cause the browser to redirect to `https://snehit.dev`.

```html
<!doctype html><meta http-equiv="refresh" content="0; url=https://snehit.dev" />
```

## Extending this to create a static site URL shortener

I'll use a `links.json` file to list out routes and redirects, exactly the same way Suri does it. So, for example, my `links.json` could look like this:

```json
{
  "/": "https://snehit.dev",
  "tw": "https://x.com/flyingcakes85",
  "gh": "https://github.com/flyingcakes85"
}
```

Now, all I need to do is generate HTML pages for each route, which contains the meta refresh code along with corresponding URL. A simple Python script can achieve that.

```py
output_dir = "build"
for link in links:
    output_file = Path(output_dir + "/" + link + "/index.html")
    output_file.parent.mkdir(exist_ok=True, parents=True)
    output_file.write_text(f"<!doctype html><meta http-equiv=\"refresh\" content=\"0; url={links[link]}\" />")
```

Loading the JSON in `links`, this loop will create an HTML file for all the individual route specified.

I've wrapped the above loop in a neat function and taken command line arguments using `argparse` in order to make this script easy to use. Refer the repo [flyingcakes85/linky](https://github.com/flyingcakes85/linky) on GitHub to see the full code.

Now all we need to do is to run this script with out complete `links.json`.

```sh
python linky.py links.json
```

I've also added `-o`/`--output-dir` flag to change the output folder from `build` to something else.

```sh
python linky.py links.json --ouptut-dir public
```

Additionally, anything in `static` folder will be copied as it is into the build output directory.

## Hosting it

You can host this on GitHub Pages. However, my choice is Cloudflare Pages, because I find it easier to configure build on Cloudflare dashboard as compared to GitHub Actions.

Go to your Cloudflare dashboard and select your site. On the left bar, go to "Workers & Pages". Hit the "Create" button. Under "Pages" tab, connect your GitHub or GitLab repository which has `linky.py` and your `links.json` file.

For build settings, select "None" for the framemwork preset. Build command should be `python linky.py links.json`. Set build output directory to `build`. Hit save and deploy and voila, you link shortener is up and running!

### Adding your custom URL

There is one last step: adding your domain. This assumes you already have a domain purchased, otherwise your base URL will be `project-name.pages.dev`. If you don't have a URL, you can skip this, but note that the links this way won't actually be short.

On the "Custom domains" tab in your new project, click "Set up a custom domain". Save it and Cloudflare should do the rest if your DNS is managed by Cloudflare itself. Otherwise you'll have to manually update the `CNAME` on your DNS.
