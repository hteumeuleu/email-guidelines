# Email Coding Guidelines

This document aims to address several principles you can apply to code HTML emails. This is done in the spirit of @mdo's [Code Guide](https://www.github.com/mdo/code-guide/), @necolas's [Idiomatic CSS](https://www.github.com/necolas/idiomatic-css), @bendc's [Frontend Guidelines](https://www.github.com/bendc/frontend-guidelines) or Stack Overflow's [Email Guidelines](https://www.stackoverflow.design/email/guidelines/).

# HTML5 doctype

The HTML5 doctype is clean, short and easy to remember. It's used by a vast majority of email clients, so your code will inherit it anyway. Just be aware that some other email clients might use other doctypes and your email might end up being rendered in [Quirks Mode](https://developer.mozilla.org/en-US/docs/Web/HTML/Quirks_Mode_and_Standards_Mode).

```html
<!-- Good example -->
<!DOCTYPE html>
<html lang="en">
  <head></head>
  <body></body>
</html>
```

**See also:**

* [Which doctype should you use in HTML emails?](https://emails.hteumeuleu.com/which-doctype-should-you-use-in-html-emails-cd323fdb793c)

# Make it work without `<style>`

Not every email clients support `<style>` tags. `<style>` tags filtering can be:

* **Permanent**. For example, the Gmail Apps (on iOS and Android) with Non Gmail Accounts (also known as *GANGA*) don't support `<style>` tags. This also happens on a lot of international email clients like [Libero](http://www.libero.it/mail/) (in Italy), [Mail.ru](https://mail.ru/) or [Yandex](https://mail.yandex.com/) (in Russia), [Nate](http://home.mail.nate.com/) or [Naver](https://mail.naver.com/) (in Korea), [T‑online](https://freemail.t-online.de/) (in Germany), [Telstra](https://www.my.telstra.com.au/) (in Australia) or [Terra](https://mail.terra.com.br/) (in Brazil).
* **Temporary**. In the past year, Gmail removed `<style>` tags for a day at least two times (on [2019/04/23](https://twitter.com/TaxiforEmail/status/1120645381669494785) and on [2018/07/13](https://twitter.com/HTeuMeuLeu/status/1017741221182263296)).

# Styles over attributes

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

# Use margin or padding for spacing

Spacing around or inside elements should be done using the `margin` or `padding properties in CSS. Empty `<td>`s and multiple `<br>`s must be avoided.

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

# Don't split visuals

Avoid splitting an image into multiple files. This is important for several reasons:

* **Performance**. Just like on the Web, The fewer HTTP requests the better. Downloading a single 50 Kb image is theoretically faster than downloading five 10 Kb images.
* **Accessibility**. A single image will let you define a single clean alt text, and style it in case images aren't visible.
* **WebKit** adds small thin lines between images when using a CSS transform on a whole email. This is something used by numerous email clients to adjust the rendering of non responsive emails on smaller screens. The current version of Outlook.com uses a CSS transform to adjust the display of an email within its preview pane. On Chrome or Safari, this results in thin lines between split images like in [this example](https://cdn-images-1.medium.com/max/2400/1*2CHIjuhc9JSmpNjoSQl3aw.jpeg).
* **Shit happens**. Email clients or user preferences may change how your email look, making your content larger than expected and your images alignement change. You don't want [this](https://imgur.com/NhoEN) to happen.