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

すべてのメールクライアントが`<style>`タグをサポートしているわけではありません。`<style>`タグのフィルタリングには次のものがあります。     

 * **Permanent**. たとえば、Gmail以外のアカウント(*GANGA*とも呼ばれる)を使用する(iOSおよびAndroid上の)Gmail アプリは、`<style>`タグをサポートしません。この挙動となるMailアカウントは、[Libero](http://www.libero.it/mail/)(イタリア)、[Mail.ru](https://mail.ru/)または[Yandex](https://mail.yandex.com/)(ロシア)、[Nate])(http://home.mail.nate.com/)または[Naver])(https://mail.naver.com/)(韓国)、[T‑online](https://freemail.t-online.de/)(ドイツ)、[Telstra](https://www.my.telstra.com.au/) (オーストラリア)、または[Terra](https://mail.terra.com.br/)(ブラジル)です。     
* **Temporary**. 昨年、Gmail は1日に少なくとも2回（[2019/04/23](https://twitter.com/TaxiforEmail/status/1120645381669494785)と[2018/07/13](https://twitter.com/HTeuMeuLeu/status/1017741221182263296)に)`<style>`タグを削除しました。
* **Contextual**. Gmail のデスクトップウェブメールでメールを転送すると、すべての `<style>`タグが削除されます。 Gmail は、メールが*クリップされていない*バージョンで表示されたときに `<style>`タグを削除することでも知られています。(参照: [Gmail removes `<style>` tags in non clipped view](https://github.com/hteumeuleu/email-bugs/issues/56))
* **Buggy**. Android の Yahoo アプリは、ページの最初の`<head>`内の`<style>`タグを削除します。(参照：[Yahoo! Mail app for Android strips styles from the first `<head>` tag](https://github.com/hteumeuleu/email-bugs/issues/28))

`<style>`を使用しないで*メールを作成する*ことには、さまざまな意味があります。しかし、何よりもまず以下を考えます。

* **Layout**. `<style>`のない電子メールは、水平スクロールなしで任意の幅に調整する必要があります。通常、iPhone SEの Gmail で表示されるメールの幅を反映して、幅 280px をまで狭くすることを検討します。　　　　　

* **Branding**. `<style>`のないメールは送信者のブランドを反映する必要があります。

## Styles over attributes

HTML属性ではなくスタイルプロパティを使用することを推奨します。これにより、プレゼンテーションコードを複数の属性ではなく1つのスタイル属性にまとめることができます。いくつかの例外を除いて、`width`、`height`、`align`、` valign`、`border`、`color`、または`bgcolor`などの属性は避けてください。    

これは、メールクライアントに強力なデフォルトスタイルがある場合に特に役立ちます。 たとえば、フランスのプロバイダーOrangeのデスクトップウェブメールには、デフォルトのレイアウト`td { vertical-align:top; }`が適用され、HTMLはこの `valign`属性を引き継ぎます。(参照: [Orange's default styles](https://github.com/hteumeuleu/email-bugs/issues/48))     


```html
<!-- 悪い例 -->
<td valign="middle" align="center" bgcolor="#ffffff"></td>

<!-- 良い例 -->
<td style="vertical-align:middle; text-align:center; background-color:#fff;"></td>
```

**例外**

1. Word のレンダリングエンジンを使用する Outlook バージョン（Windowsの2007〜2019年）では、`auto`が`margin`の値として認識されません。したがって、`<table>`を中央揃えにするには、`align="center"`属性と`margin:0 auto`スタイルの両方を使用するのが最適です。    

```html
<!-- 悪い例 -->
<table align="center" width="600" role="presentation">…</table>

<!-- 良い例 -->
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
