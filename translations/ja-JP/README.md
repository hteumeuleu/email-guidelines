# Email Coding Guidelines

このドキュメントは、HTMLメールのコーディングに適用できるいくつかの原則に対処することを目的としています。各ガイドラインは、最新のメールクライアントに準拠し、その他のメールクライアントのグレースフルデグラデーション (上品な劣化) を念頭に置いて客観的に実証されています。    

これは絶えず更新または編集される文書であり、新しいアイデアは大歓迎です。貢献してください。     

## HTML5 doctype

HTML5 doctype は簡潔で短く、覚えやすいものです。 大半のメールクライアントで使用されているので、コードではとにかく HTML5 doctype を使用します。 他のメールクライアントが、他の doctype だと解釈し、メールが[後方互換モード](https://developer.mozilla.org/ja/docs/Web/HTML/Quirks_Mode_and_Standards_Mode)でレンダリングされる可能性があることに注意してください。     

```html
<!DOCTYPE html>
<html>
  <head></head>
  <body></body>
</html>
```

**以下も参照してください:**

* [Which doctype should you use in HTML emails?](https://emails.hteumeuleu.com/which-doctype-should-you-use-in-html-emails-cd323fdb793c)

## Lang

HTML コンテンツの`lang`を定義すると、スクリーンリーダーなどの支援技術が適切な音声を選んでコンテンツを読むのに役立ちます。`lang`属性は、` <html> `開始タグで有効な[言語タグ](https://www.iana.org/assignments/language-subtag-registry/language-subtag-registry)を使用して定義する必要があります。ただし、一部のメールクライアント(特にウェブメール)は`<html>`要素を削除するため、`<body>`内の折り返し要素に`lang`属性も設定する必要があります。    

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
**以下も参照してください:**

* [Email accessibility in action](https://emails.hteumeuleu.com/email-accessibility-in-action-f7f9d945cf67#1592)

## Semantic text markup

電子メールの HTML コードは、可能な限り多くのセマンティックマークアップを使用する必要があります。`<h1>`から`<h6>`までの見出しの使用と、段落に`<p>`を使用することを強く推奨します。     

```html
<!-- 悪い例 -->
<font face="Arial" size="5" color="#00ff00">Lorem ipsum</font>

<!-- 良い例 -->
<h1 style="margin:0; color:#0f0; font:24px Arial, sans-serif;">Lorem ipsum</h1>
```

`<header>`、`<main>`、`<footer>`、`<article>`、または`<section>`などのコンテナタグは、いくつかの主要な電子メールクライアント(GmailやOutlookなど)でサポートされていないため、注意して使用する必要があります。これらのコンテナタグの代わりに、指定したい要素の対応する[implicit ARIA `role`](https://momdo.github.io/html-aria/#implicit)を使用することを推奨します。     

```html
<!-- 悪い例 -->
<header>
  <h1>Lorem ipsum</h1>
</header>

<!-- 良い例 -->
<div role="banner">
  <h1>Lorem ipsum</h1>
</div>
```

## Tables for layout

プレゼンテーションテーブルは残念ながらまだ必要ですが、*Outlook*（Windowsの2007-2019）の場合のみです。アクセシビリティを向上させるために、すべてのプレゼンテーションテーブルには常に`role ="presentation"`属性を含める必要があります。    

```html
<!-- Good example -->
<table role="presentation" border="0" cellpadding="0" cellspacing="0">
```

ただし、*Outlook*でも、プレゼンテーションテーブルの使用は、一貫したレンダリングのために次の例外に限定する必要があります。    

* 要素に固定幅を設定する(`<table style ="width:600px">`を使用する等)。    
* 2つの要素を並べて設定する(2つの連なった`<td>`を使用する等)。
* `padding`、`background-color`、または `border`をスタイルとして設定します。    

また、`role="presentation"`はメールクライアント(Yahoo! MailやAOLなど)によって削除される可能性があるため、Outlookの条件付きコメントにのみプレゼンテーションテーブルを含めることをを推奨します。        

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

単一の`[if mso]`の条件式は、Word のレンダリングエンジン(Windowsでは2007年から2019年までの*Outlook*)を使用して Outlook のすべてのバージョンをターゲットにするには十分です。特に必要がない限り、Outlook のバージョンターゲティング(`[if mso gte 16]`等)の使用はお勧めしません。     

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
