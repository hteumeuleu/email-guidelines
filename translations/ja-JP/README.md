# Email Coding Guidelines

このドキュメントは、HTML メールのコーディングに適用できるいくつかの原則に対処することを目的としています。各ガイドラインは、最新のメールクライアントに準拠し、その他のメールクライアントのグレイスフルデグラデーション (上品な劣化) を念頭に置いて客観的に実証されています。    

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

## セマンティックなマークアップ     

電子メールの HTML コードは、可能な限り多くのセマンティックマークアップを使用する必要があります。`<h1>`から`<h6>`までの見出しの使用と、段落に`<p>`を使用することを強く推奨します。     

```html
<!-- 悪い例 -->
<font face="Arial" size="5" color="#00ff00">Lorem ipsum</font>

<!-- 良い例 -->
<h1 style="margin:0; color:#0f0; font:24px Arial, sans-serif;">Lorem ipsum</h1>
```

`<header>`、`<main>`、`<footer>`、`<article>`、または`<section>`などの構造化タグは、いくつかの主要な電子メールクライアント(GmailやOutlookなど)でサポートされていないため、注意して使用する必要があります。これらのコンテナタグの代わりに、指定したい要素の対応する[implicit ARIA `role`](https://momdo.github.io/html-aria/#implicit)を使用することを推奨します。     

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

## テーブルのレイアウト    

プレゼンテーションテーブルは*Outlook*（2007-2019年のWindows）の場合のみ残念ながらまだ必要です。アクセシビリティを向上させるために、すべてのプレゼンテーションテーブルには常に`role ="presentation"`属性を含める必要があります。    

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

## `<style>`なしで機能させる    

すべてのメールクライアントが`<style>`タグをサポートしているわけではありません。`<style>`タグのフィルタリングには次のものがあります。     

 * **恒久的なもの**. たとえば、Gmail以外のアカウント(*GANGA*とも呼ばれる)を使用する(iOSおよびAndroid上の)Gmail アプリは、`<style>`タグをサポートしません。この挙動となるMailアカウントは、[Libero](http://www.libero.it/mail/)(イタリア)、[Mail.ru](https://mail.ru/)または[Yandex](https://mail.yandex.com/)(ロシア)、[Nate](http://home.mail.nate.com/)または[Naver](https://mail.naver.com/)(韓国)、[T‑online](https://freemail.t-online.de/)(ドイツ)、[Telstra](https://www.my.telstra.com.au/)(オーストラリア)、[Terra](https://mail.terra.com.br/)(ブラジル)です。     
* **一時的なもの**. 昨年、Gmail は1日に少なくとも2回（[2019/04/23](https://twitter.com/TaxiforEmail/status/1120645381669494785)と[2018/07/13](https://twitter.com/HTeuMeuLeu/status/1017741221182263296)に)`<style>`タグを削除しました。
* **使用コンテキストによるもの**. Gmail のデスクトップウェブメールでメールを転送すると、すべての `<style>`タグが削除されます。 Gmail は、メールが*クリップされていない*バージョンで表示されたときに `<style>`タグを削除することでも知られています。(参照: [Gmail removes `<style>` tags in non clipped view](https://github.com/hteumeuleu/email-bugs/issues/56))
* **バグによるもの**. Android の Yahoo アプリは、ページの最初の`<head>`内の`<style>`タグを削除します。(参照：[Yahoo! Mail app for Android strips styles from the first `<head>` tag](https://github.com/hteumeuleu/email-bugs/issues/28))  

`<style>`を使用しないで*メールを作成する*ことには、さまざまな意味があります。しかし、何よりもまず以下を考えます。

* **レイアウト**. `<style>`のない電子メールは、水平スクロールなしで任意の幅に調整する必要があります。通常、iPhone SEの Gmail で表示されるメールの幅を反映して、幅 280px をまで狭くすることを検討します。

* **ブランディング**. `<style>`のないメールは送信者のブランドを反映する必要があります。

## HTML属性でのスタイル定義      

HTML属性ではなくスタイルプロパティを使用することを推奨します。これにより、プレゼンテーションコードを複数の属性ではなく1つのスタイル属性にまとめることができます。いくつかの例外を除いて、`width`、`height`、`align`、` valign`、`border`、`color`、または`bgcolor`などの属性は避けてください。    

これは、メールクライアントに強力なデフォルトスタイルがある場合に特に役立ちます。 たとえば、フランスのプロバイダーOrangeのデスクトップウェブメールには、デフォルトのレイアウト`td { vertical-align:top; }`が適用され、HTMLはこの `valign`属性を引き継ぎます。(参照: [Orange's default styles](https://github.com/hteumeuleu/email-bugs/issues/48))     


```html
<!-- 悪い例 -->
<td valign="middle" align="center" bgcolor="#ffffff"></td>

<!-- 良い例 -->
<td style="vertical-align:middle; text-align:center; background-color:#fff;"></td>
```

**例外**

1. Word のレンダリングエンジンを使用する Outlook バージョン（2007〜2019年のWindows）では、`auto`が`margin`の値として認識されません。したがって、`<table>`を中央揃えにするには、`align="center"`属性と`margin:0 auto`スタイルの両方を使用するのが最適です。    

```html
<!-- 悪い例 -->
<table align="center" width="600" role="presentation">…</table>

<!-- 良い例 -->
<table align="center" style="margin:0 auto; width:600px;" role="presentation">…</table>
```

2. *Outlook*（2007〜2019年のWindows)では、画像にパーセンテージの幅を設定しても、CSS で期待されるように、画像がその親の要素の幅に調整されません。代わりに画像ファイルの実際の幅を設定します。したがって、流動的でレスポンシブな画像を作成するには、HTML の`width`属性(Outlookで固定幅を維持するため)とスタイル(他のクライアント用)の両方が必要です。    

```html
<!-- 悪い例 -->
<img src="example.jpg" alt="" width="100%" />

<!-- 良い例 -->
<img src="example.jpg" alt="" width="600" style="width:100%;" />
```

3. `<table>`の `border`、`cellpadding`、および`cellspacing`をリセットします。CSSで `<table>`のスタイルをリセットする方法は電子メールでは扱いにくく、代わりに HTML 属性を使用することを推奨します。

```html
<!-- 悪い例 -->
<table style="border:0; border-spacing:0;">
	<tr>
		<td style="padding:0; border:none;">Lorem ipsum.</td>
	</tr>
</table>

<!-- 良い例 -->
<table border="0" cellspacing="0" cellpadding="0">
	<tr>
		<td>Lorem ipsum.</td>
	</tr>
</table>
```

4. `height`を設定します。CSS の`height`プロパティは、すべての Yahoo! メールおよび AOL クライアントで`min-height`に変換されます。(参照:[Yahoo converts the height property to min-height](https://github.com/hteumeuleu/email-bugs/issues/9))代わりにHTMLの `height`属性を使用する方が安全です。     

```html
<!-- 悪い例 -->
<td style="height:100px;">Lorem ipsum.</td>

<!-- 良い例 -->
<td height="100">Lorem ipsum.</td>
```

## 間隔の調整には`margin`または`padding`を使用する     

要素の周囲または内部の間隔は、CSSの`margin`または`padding`プロパティを使用して調整する必要があります。空の`<td>`と複数の`<br>`は避けるべきです。      

```html
<!-- 悪い例 -->
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

<!-- 良い例 -->
<table role="presentation">
  <tr>
    <td style="padding:20px;">
      <h1 style="margin:0 0 20px;">Hello world</h1>
    </td>
  </tr>
</table>
```

1つの注意点として、*Outlook*(2007-2019年のWindows)では、`margin` と `background-color` の組み合わせの動作が CSS 仕様とは異なるということです。主に、背景色もマージン領域で表示されます。    

## 画像を分割しない    

画像を複数のファイルに分割しないでください。 これは以下の理由で重要です。     

* **パフォーマンス**. Web と同じように、HTTP リクエストは少ないほど良いです。理論的には、単一の 50Kb イメージをダウンロードする方が、5つの 10Kb イメージをダウンロードするよりも高速です。
* **アクセシビリティ**. 1つの画像にすると、1つの代替テキストを定義し、画像が表示されない場合に備えてスタイルを設定できます。     
* **WebKit** メール全体で CSS 変換を使用するときに、画像間に細い線を追加します。これは、小さな画面での応答のない電子メールのレンダリングを調整するために、多くの電子メールクライアントで使用されるテクニックです。Outlook.com の現在のバージョンでは、CSS変換を使用して、プレビューウィンドウ内の電子メールの表示を調整しています。Chrome または Safari では、[この例](https://cdn-images-1.medium.com/max/2400/1*2CHIjuhc9JSmpNjoSQl3aw.jpeg)のように、分割画像間に細い線が表示されます。   
* **運が悪いこと**. メールクライアントまたはユーザー設定により、メールの外観が変更され、コンテンツが予想よりも大きくなり、画像の配置が変わる場合があります。 あなたは[これ](https://imgur.com/NhoEN)が起こることを望まないでしょう。

## 120 dpiでOutlookをサポートする    

特定の Windows 構成では、*Outlook*(2007-2019年のWindows)は電子メールに DPI スケーリングを適用します。 スケーリングの変更を防ぐには、次の3つのルールを適用する必要があります。    

1. Microsoft Office の名前空間を `<html>`要素に追加します。       

```html
<html xmlns:o="urn:schemas-microsoft-com:office:office">
```

2. 次の `OfficeDocumentSettings` 宣言を`<head>` 要素内に追加します。

```html
<!--[if mso]>
<xml>
  <o:OfficeDocumentSettings>
    <o:PixelsPerInch>96</o:PixelsPerInch>
  </o:OfficeDocumentSettings>
</xml>
<![endif]-->
```

3. HTML 属性ではなく、CSS で定義された寸法を常に使用します。   

```html
<!-- 悪い例 -->
<table align="center" role="presentation" width="600">…</table>

<!-- 良い例 -->
<table align="center" role="presentation" style="width:600px;">…</table>
```

**以下も参照してください:**

* [Correcting Outlook DPI Scaling Issues](https://www.courtneyfantinato.com/correcting-outlook-dpi-scaling-issues/) by Courtney Fantinato.

## 謝辞    

このガイドは、@mdoの[[コーディングガイド by @mdo](http://kia-king.com/code-guide/)、@necolasの[idiomatic-css/translations/ja-JP at master · necolas/idiomatic-css](https://github.com/necolas/idiomatic-css/tree/master/translations/ja-JP)、@bendcの[Frontend Guidelines](https://www.github.com/bendc/frontend-guidelines)、 Stack Overflow の[Email Guidelines](https://www.stackoverflow.design/email/guidelines/)の推奨事項に基づいて作成されています。      


## ライセンス
Rémi Parmentier (@HTeuMeuLeu)が作成した*Email Coding Guidelines*は、MIT ライセンスです。これは、このリポジトリ内のすべてのドキュメントと翻訳に適用されます。      
github.com/hteumeuleu/email-guidelinesでの作業に基づいています。       

