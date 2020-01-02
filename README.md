# Email Coding Guidelines

This document aims to address several principles you can apply to code HTML emails. Each guideline is objectively vindicated to comply with modern email clients and with graceful degradation in mind for others.

This is a living document and new ideas are more than welcome. Feel free to contribute.

## HTML5 doctype

The HTML5 doctype is clean, short and easy to remember. It's used by a vast majority of email clients, so your code will inherit it anyway. Just be aware that some other email clients might use other doctypes and your email might end up being rendered in [Quirks Mode](https://developer.mozilla.org/en-US/docs/Web/HTML/Quirks_Mode_and_Standards_Mode).

```html
<!DOCTYPE html>
<html>
  <head></head>
  <body></body>
</html>
```

**See also:**

* [Which doctype should you use in HTML emails?](https://emails.hteumeuleu.com/which-doctype-should-you-use-in-html-emails-cd323fdb793c)

## Lang

Defining the `lang` of the HTML content helps assistive technologies like screen readers to pick the right voice to read the content. The `lang` attribute needs to be defined with a valid [language tag](https://www.iana.org/assignments/language-subtag-registry/language-subtag-registry) on the `<html>` opening tag. But because some email clients (especially webmails) remove the `<html>` element, the `lang` attribute also needs to be set on a wrapping element within the `<body>`.

```html
<!DOCTYPE html>
<html lang="en">
  <head></head>
  <body>
    <div lang="en">
    </div>
  </body>
</html>
```
**See also:**

* [Email accessibility in action](https://emails.hteumeuleu.com/email-accessibility-in-action-f7f9d945cf67#1592)

## Semantic text markup

The HTML code of an email should use as much semantic markup as possible. The use of `<h1>` to `<h6>` headings as well the use of `<p>` for paragraphs is greatly recommended.

```html
<!-- Bad example -->
<font face="Arial" size="5" color="#00ff00">Lorem ipsum</font>

<!-- Good example -->
<h1 style="margin:0; color:#0f0; font:24px Arial, sans-serif;">Lorem ipsum</h1>
```

Container tags such as `<header>`, `<main>`, `<footer>`, `<article>` or `<section>` are to be used with caution as several major email clients (like Gmail or Outlook.com) don't support them. It is preferred to use the corresponding [implicit ARIA `role`](https://www.w3.org/TR/html-aria/#implicit) of the given element instead.

```html
<!-- Bad example -->
<header>
  <h1>Lorem ipsum</h1>
</header>

<!-- Good example -->
<div role="banner">
  <h1>Lorem ipsum</h1>
</div>
```

## Tables for layout

Presentational tables are unfortunately still required, but only for *the Outlooks* (2007-2019 on Windows). For better accessibility, every presentational table should always include the `role="presentation"` attribute.

```html
<!-- Good example -->
<table role="presentation" border="0" cellpadding="0" cellspacing="0">
```

But even on *the Outlooks*, the use of presentational tables should be limited to the following exceptions for a consistent rendering:

* Setting a fixed width on an element (using `<table style="width:600px">`).
* Setting two elements side by side (using two siblings `<td>`).
* Setting a `padding`, `background-color` or a `border` style.

And because the `role="presentation"` might still be removed by email clients (for example in Yahoo! Mail or AOL), it is even better to include presentational tables only in conditional comments for Outlook.

```html
<!--[if mso]>
<table role="presentation" border="0" cellpadding="0" cellspacing="0"><tr><td>
<![endif]-->
<div>
</div>
<!--[if mso]>
</td></tr></table>
<![endif]-->
```

The single `[if mso]` condition is enough to target all versions of Outlook using Word's rendering engine (aka *the Outlooks* from 2007 to 2019 on Windows). Unless there's a very specific need, the use of Outlook version targeting (like `[if mso gte 16]`) is greatly discouraged.


## Make it work without `<style>`

Not every email clients support `<style>` tags. `<style>` tags filtering can be:

* **Permanent**. For example, the Gmail Apps (on iOS and Android) with Non Gmail Accounts (also known as *GANGA*) don't support `<style>` tags. This also happens on a lot of international email clients like [Libero](http://www.libero.it/mail/) (in Italy), [Mail.ru](https://mail.ru/) or [Yandex](https://mail.yandex.com/) (in Russia), [Nate](http://home.mail.nate.com/) or [Naver](https://mail.naver.com/) (in Korea), [T‑online](https://freemail.t-online.de/) (in Germany), [Telstra](https://www.my.telstra.com.au/) (in Australia) or [Terra](https://mail.terra.com.br/) (in Brazil).
* **Temporary**. In the past year, Gmail removed `<style>` tags for a day at least two times (on [2019/04/23](https://twitter.com/TaxiforEmail/status/1120645381669494785) and on [2018/07/13](https://twitter.com/HTeuMeuLeu/status/1017741221182263296)).
* **Contextual**. When you forward an email in the desktop webmail of Gmail, all `<style>` tags are removed. Gmail is also known for removing `<style>` tags when an email is viewed in its *unclipped* version. (See also: [Gmail removes `<style>` tags in non clipped view](https://github.com/hteumeuleu/email-bugs/issues/56).)
* **Buggy**. The Yahoo app on Android removes `<style>` tags inside the first `<head>` of your page. (See also: [Yahoo! Mail app for Android strips styles from the first `<head>` tag](https://github.com/hteumeuleu/email-bugs/issues/28).)

"*Making an email work*" without `<style>` can mean a lot of different things. But I think first and foremost about:

* **Layout**. An email without `<style>` should adjust to any width without horizontal scroll. I usually consider to go as low as 280px wide which reflects the width an email viewed on Gmail on an iPhone SE.
* **Branding**. An email without `<style>` should reflect the branding of the sender.

## Styles over attributes

Prefer using styles properties instead of HTML attributes. This helps bring together presentational code into a single style attribute instead of multiple attributes. With some exceptions, avoid attributes like `width`, `height`, `align`, `valign`, `border`, `color` or `bgcolor`.

This is especially helpful in case an email client has strong default styles. For example, the desktop webmail of french provider Orange has the following rule, `td { vertical-align:top; }`, taking over any `valign` attribute in HTML. (See also: [Orange's default styles](https://github.com/hteumeuleu/email-bugs/issues/48).)

```html
<!-- Bad example -->
<td valign="middle" align="center" bgcolor="#ffffff"></td>

<!-- Good example -->
<td style="vertical-align:middle; text-align:center; background-color:#fff;"></td>
```

**Exceptions:**

1. The Outlook versions using Word's rendering engine (2007–2019 on Windows) don't understand `auto` as a value for `margin`. Thus, to center a `<table>`, it's best to have both the `align="center"` attribute and a `margin:0 auto` style.

```html
<!-- Bad example -->
<table align="center" width="600" role="presentation">…</table>

<!-- Good example -->
<table align="center" style="margin:0 auto; width:600px;" role="presentation">…</table>
```

2. In *the Outlooks* (2007–2019 on Windows), setting a percentage width to an image doesn't make the image adjust to its parent's width, as you'd expect in CSS. It instead sets the image at the width of the physical file. So to create fluid and responsive images, we need both the HTML `width` attribute (to maintain a fixed width in Outlook) and a style (for other clients).

```html
<!-- Bad example -->
<img src="example.jpg" alt="" width="100%" />

<!-- Good example -->
<img src="example.jpg" alt="" width="600" style="width:100%;" />
```

3. Resetting `border`, `cellpadding` and `cellspacing` on a `<table>`. I find the CSS way to reset those styles on a `<table>` cumbersome for emails and prefer using the HTML attributes instead.

```html
<!-- Bad-ish example -->
<table style="border:0; border-spacing:0;">
	<tr>
		<td style="padding:0; border:none;">Lorem ipsum.</td>
	</tr>
</table>

<!-- Good-ish example -->
<table border="0" cellspacing="0" cellpadding="0">
	<tr>
		<td>Lorem ipsum.</td>
	</tr>
</table>
```

4. Setting `height`. The `height` property in CSS is turned into `min-height` in every Yahoo! Mail and AOL clients. (See also: [Yahoo converts the height property to min-height](https://github.com/hteumeuleu/email-bugs/issues/9).) Using the HTML `height` attribute instead is a safer bet.

```html
<!-- Bad example -->
<td style="height:100px;">Lorem ipsum.</td>

<!-- Good example -->
<td height="100">Lorem ipsum.</td>
```


## Use `margin` or `padding` for spacing

Spacing around or inside elements should be done using the `margin` or `padding` properties in CSS. Empty `<td>`s and multiple `<br>`s must be avoided.

```html
<!-- Bad example -->
<table role="presentation">
  <tr>
    <td colspan="3" height="20"></td>
  </tr>
  <tr>
    <td width="20">
    <td>
      <h1>Hello world</h1>
      <br />
      <br />
    </td>
    <td width="20">
  </tr>
  <tr>
    <td colspan="3" height="20"></td>
  </tr>
</table>

<!-- Good example -->
<table role="presentation">
  <tr>
    <td style="padding:20px;">
      <h1 style="margin:0 0 20px;">Hello world</h1>
    </td>
  </tr>
</table>
```

One caveats of this is that in *the Outlooks* (2007-2019 on Windows), the combination of `margin` and `background-color` behaves differently than in the CSS specification. Mainly, the background color is visible in the margin area as well.

## Don't split visuals

Avoid splitting an image into multiple files. This is important for several reasons:

* **Performance**. Just like on the Web, The fewer HTTP requests the better. Downloading a single 50 Kb image is theoretically faster than downloading five 10 Kb images.
* **Accessibility**. A single image will let you define a single clean alt text, and style it in case images aren't visible.
* **WebKit** adds small thin lines between images when using a CSS transform on a whole email. This is something used by numerous email clients to adjust the rendering of non responsive emails on smaller screens. The current version of Outlook.com uses a CSS transform to adjust the display of an email within its preview pane. On Chrome or Safari, this results in thin lines between split images like in [this example](https://cdn-images-1.medium.com/max/2400/1*2CHIjuhc9JSmpNjoSQl3aw.jpeg).
* **Shit happens**. Email clients or user preferences may change how your email look, making your content larger than expected and your images alignement change. You don't want [this](https://imgur.com/NhoEN) to happen.

## Support Outlook at 120 dpi

On certain Windows configurations, *the Outlooks* (2007-2019 on Windows) applies DPI scaling on emails. To prevent altered scaling, you need to apply the three following rules:

1. Add the Microsoft Office namespace on the `<html>` element.

```html
<html xmlns:o="urn:schemas-microsoft-com:office:office">
```

2. Add the following `OfficeDocumentSettings` declaration inside the `<head>` element.

```html
<!--[if mso]>
<xml>
  <o:OfficeDocumentSettings>
    <o:PixelsPerInch>96</o:PixelsPerInch>
  </o:OfficeDocumentSettings>
</xml>
<![endif]-->
```

3. Always use dimensions defined in CSS instead of HTML attributes.

```html
<!-- Bad example -->
<table align="center" role="presentation" width="600">…</table>

<!-- Good example -->
<table align="center" role="presentation" style="width:600px;">…</table>
```

**See also:**

* [Correcting Outlook DPI Scaling Issues](https://www.courtneyfantinato.com/correcting-outlook-dpi-scaling-issues/) by Courtney Fantinato.

## Acknowledgements

This guide is done in the spirit of @mdo's [Code Guide](https://www.github.com/mdo/code-guide/), @necolas's [Idiomatic CSS](https://www.github.com/necolas/idiomatic-css), @bendc's [Frontend Guidelines](https://www.github.com/bendc/frontend-guidelines) or Stack Overflow's [Email Guidelines](https://www.stackoverflow.design/email/guidelines/).

## License

*Email Coding Guidelines* by Rémi Parmentier (@HTeuMeuLeu) is licensed under the MIT License. This applies to all documents and translations in this repository.

Based on a work at github.com/hteumeuleu/email-guidelines.
