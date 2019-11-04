# Recommandations pour l'intégration d'e‑mails

Ce document a pour but de présenter plusieurs principes que vous pouvez appliquer pour coder des e‑mails HTML. Chaque recommandation est objectivement justifiée pour se conformer aux clients de messagerie modernes, et se dégrader gracieusement ailleurs.

Ce document est un document évolutif et toute nouvelle idée est la bienvenue. Votre participation est la bienvenue.

## Doctype HTML5

Le doctype HTML5 est propre, court and facile à mémoriser. Il est utilisé par une vaste majorité de clients de messagerie, donc votre code en héritera quoi qu'il arrive. Gardez cependant en tête que certains autres clients de messagerie peuvent utiliser d'autres doctypes and que votre e‑mail peut finir affiché en [mode quirks](https://developer.mozilla.org/fr/docs/Web/HTML/Quirks_Mode_and_Standards_Mode).

```html
<!DOCTYPE html>
<html>
  <head></head>
  <body></body>
</html>
```

**Voir aussi:**

* [Quel doctype faut-il utiliser dans un e‑mail ?](https://emails.hteumeuleu.fr/2016/10/quel-doctype-faut-il-utiliser-dans-un-email/)

## Lang

Définir l'attribut `lang` sur le contenu HTML aide les technologies d'assistance comme des lecteurs d'écran à utiliser la bonne voix pour lire le contenu. L'attribut `lang` doit être défini avec un [code de langue](https://www.iana.org/assignments/language-subtag-registry/language-subtag-registry) valide sur la balise d'ouverture `<html>`. Mais parce que certains clients (en particulier les webmails) suppriment l'élément `<html>`, l'attribut `lang` doit aussi être placé sur un élément global à l'intérieur du `<body>`.

```html
<!DOCTYPE html>
<html lang="fr">
  <head></head>
  <body>
    <div lang="fr">
    </div>
  </body>
</html>
```

**Voir aussi:**

* [L’accessibilité des e‑mails par l’exemple](http://emails.hteumeuleu.fr/2018/01/laccessibilite-des-e%E2%80%91mails-par-lexemple/)

## Balises sémantiques pour le texte

Le code HTML d'un e‑mail doit utiliser autant que possible des balises sémantiques. L'utilisation de niveaux de titres `<h1>` à `<h6>` ainsi que des paragraphes `<p>` est hautement recommandée.

```html
<!-- Mauvais exemple -->
<font face="Arial" size="5" color="#00ff00">Lorem ipsum</font>

<!-- Bon exemple -->
<h1 style="margin:0; color:#0f0; font:24px Arial, sans-serif;">Lorem ipsum</h1>
```

Les balises de regroupement comme `<header>`, `<main>`, `<footer>`, `<article>` ou `<section>` sont à utiliser avec précaution car la plupart des clients de messagerie (comme Gmail ou Outlook.com) ne les supportent pas. Il est alors préférable de remplacer par des attributs `role` à la place.

## Tableaux de mise en page

Les tableaux de mise en page sont malheureusement toujours nécessaires, mais seulement pour *les Outlook* (2007-2019 sur Windows). Pour une meilleure accessibilité, chaque tableau de présentation doit inclure l'attribut `role="presentation"`.


```html
<!-- Bon exemple -->
<table role="presentation" border="0" cellpadding="0" cellspacing="0">
```

Mais même sur *les Outlook*, l'utilisation de tableaux de mise en page doit être limitée aux exceptions suivantes pour un rendu cohérent :

* Définir une largeur fixe sur un élément (en utilisant `<table style="width:600px">`).
* Positionner deux éléments côte à côte (en utilisant deux `<td>` adjacents).
* Définir des styles `padding`, `background-color` ou `border`.

Et parce que l'attribut `role="presentation"` peut être supprimé par les clients de messagerie (par exemple dans Yahoo! Mail ou AOL), il est de meilleur usage d'inclure les tableaux de mise en page dans des commentaires conditionnels pour Outlook.

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

La simple condition `[if mso]` suffit à cibler toutes les versions d'Outlook utilisant le moteur de rendu de Word (aussi appelées *les Outlook*, de 2007 à 2019 sur Windows). À moins que vous n'ayez un besoin très spécifique, l'utilisation du ciblage d'Outlook par version (par exemple `[if mso gte 16]`) est fortement découragé.

## Il faut que ça marche sans `<style>`

Tous les clients de messagerie ne supportent pas la balise `<style>`. Le filtrage des balises `<style>` peut être :

* **Permanent**. Par exemple, les applications Gmail (sur iOS et Android) avec des comptes non Gmail (aussi appelées *GANGA* pour *Gmail Apps with Non Gmail Accounts*) ne supportent pas les balises `<style>`. C'est aussi le cas sur bon nombre de clients de messagerie internationaux comme [Libero](http://www.libero.it/mail/) (en Italie), [Mail.ru](https://mail.ru/) ou [Yandex](https://mail.yandex.com/) (en Russie), [Nate](http://home.mail.nate.com/) ou [Naver](https://mail.naver.com/) (en Corée), [T‑online](https://freemail.t-online.de/) (en Allemagne), [Telstra](https://www.my.telstra.com.au/) (en Australie) or [Terra](https://mail.terra.com.br/) (au Brésil).
* **Temporaire**. Au cours de l'année écoulée, Gmail a filtré les balises `<style>` le temps d'une journée au moins deux fois (le [23/04/2019](https://twitter.com/TaxiforEmail/status/1120645381669494785) et le [13/07/2018](https://twitter.com/HTeuMeuLeu/status/1017741221182263296)).
* **Contextuel**. Quand vous transférez un e‑mail dans Gmail, toutes les balises `<style>` sont supprimées. Gmail est aussi connu pour supprimer les balises `<style>` quand un email est affiché dans sa version complète. (Voir aussi : [Gmail supprime les balises `<style>` dans l'affichage non tronqué](https://github.com/hteumeuleu/email-bugs/issues/56).)
* **Bugué**. L'application Yahoo sur Android supprime les balises `<style>` à l'intérieur du premier élément `<head>` de la page. (Voir aussi : [L'application Yahoo! Mail app sur Android supprime les styles du premier élément `<head>`](https://github.com/hteumeuleu/email-bugs/issues/28).)

« *Il faut que ça marche* » sans `<style>` peut vouloir dire beaucoup de choses différentes. Mais je pense surtout à :

* **La mise en page**. Un e‑mail sans `<style>` doit s'ajuster à n'importe quelle largeur sans barre de défilement horizontal. Je considère qu'il faut descendre aussi bas que 280px de large ce qui correspond à la largeur d'un e‑mail affiché dans Gmail sur un iPhone SE.
* **L'image de marque**. Un e‑mail sans `<style>` doit refléter l'image de marque de l'expéditeur.

## Des styles plutôt que des attributs

Privilégiez l'utilisation de propriétés de styles plutôt que des attributs HTML. Cela permet de rassembler le code de formattage dans un seul attribut `style` plutôt que dans de multiples attributs HTML. À quelques exceptions près, évitez les attributs `width`, `height`, `align`, `valign`, `border`, `color` ou `bgcolor`.

C'est particulièrement utilise dans le cas où un client de messagerie a des styles par défaut importants. Par exemple, le webmail desktop d'Orange a la règle suivante, `td { vertical-align:top; }`, qui prend le dessus sur n'importe quel attribut `valign` en HTML. (Voir aussi : [Les styles par défaut d'Orange](https://github.com/hteumeuleu/email-bugs/issues/48).)

```html
<!-- Mauvais exemple -->
<td valign="middle" align="center" bgcolor="#ffffff"></td>

<!-- Bon exemple -->
<td style="vertical-align:middle; text-align:center; background-color:#fff;"></td>
```

**Exceptions :**

1. Les versions d'Outlook utilisant le moteur de rendu de Word (2007 à 2019 sur Windows) ne comprennent pas la valeur `auto` de la propriété `margin`. Ainsi, pour centrer une `<table>`, il faut conserver à la fois l'attribut `align="center"` et le style `margin:0 auto`.

```html
<!-- Mauvais exemple -->
<table align="center" width="600" role="presentation">…</table>

<!-- Bon exemple -->
<table align="center" style="margin:0 auto; width:600px;" role="presentation">…</table>
```

2. Dans *les Outlook* (2007 à 2019 sur Windows), définir une largeur en pourcentage sur une image n'ajuste pas l'image selon la largeur de son parent comme on s'y attendrait en CSS. Au lieu de ça, ça définit la largeur de l'image à la largeur physique de son fichier. Donc pour créer des images fluides et responsive, on a besoin à la fois de l'attribut HTML `width` (pour maintenir une largeur fixe dans Outlook) et d'un style (pour les autres clients).

```html
<!-- Mauvais exemple -->
<img src="exemple.jpg" alt="" width="100%" />

<!-- Bon exemple -->
<img src="exemple.jpg" alt="" width="600" style="width:100%;" />
```

3. Remettre à zéro `border`, `cellpadding` et `cellspacing` sur une `<table>`. Je trouve l'approche CSS nécessaire pour remettre à zéro ces styles d'un tableau très lourde pour des e‑mails et je préfère utiliser les attributs HTML à la place.

```html
<!-- Plutôt mauvais exemple -->
<table style="border:0; border-spacing:0;">
	<tr>
		<td style="padding:0; border:none;">Lorem ipsum.</td>
	</tr>
</table>

<!-- Plutôt bon exemple -->
<table border="0" cellspacing="0" cellpadding="0">
	<tr>
		<td>Lorem ipsum.</td>
	</tr>
</table>
```

4. Définir une hauteur avec `height`. La propriété `height` en CSS est transformée en `min-height` dans tous les clients de Yahoo! Mail et AOL. (Voir aussi : [Yahoo transforme la propriété height en min-height](https://github.com/hteumeuleu/email-bugs/issues/9).) Utiliser l'attribut HTML `height` à la place s'avère une solution plus fiable.

```html
<!-- Bad example -->
<td style="height:100px;">Lorem ipsum.</td>

<!-- Good example -->
<td height="100">Lorem ipsum.</td>
```

## Utiliser `margin` ou `padding` pour des espacements

Créer des espacements autour ou à l'intérieur d'éléments devrait être fait avec les propriétés `margin` ou `padding` en CSS. Les `<td>` vides et multiples `<br>` doivent être évités.

```html
<!-- Mauvais exemple -->
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

<!-- Bon exemple -->
<table role="presentation">
  <tr>
    <td style="padding:20px;">
      <h1 style="margin:0 0 20px;">Hello world</h1>
    </td>
  </tr>
</table>
```

Un inconvénient de ça est que dans *les Outlook* (2007 à 2019 sur Windows), la combinaison de `margin` et `background-color` se comporte différemment de la spécification CSS. La couleur de fond se retrouve ainsi visible dans la zone de marge.

## Ne pas découper les images

Évitez de découper des images en plusieurs fichiers. C'est important pour plusieurs raisons :

* La **performance**. Comme sur le web, moins vous faites de requêtes HTTP, mieux c'est. Télécharger une seule image de 50 Ko est en théorie plus rapide que télécharge cinq images de 10 Ko.
* L'**accessibilité**. Une seule image permet de définir un seul et unique texte alternatif, et de le styler dans les cas où les images ne sont pas visibles.
* **WebKit** ajoute de fins espaces entre les images lorsqu'une transformation CSS est utilisée sur un e‑mail. C'est quelque chose que font plusieurs clients de messagerie pour ajuster le rendu d'e‑mails non responsive sur des petits écrans. Par exemple, la version actuelle de Outlook.com utilise une transformation CSS pour ajuster l'affichage dans un e‑mail dans sa zone de visualisation. Sur Chrome et Safari, cela résulte en de fins espaces entre les images comme dans [cet exemple](https://cdn-images-1.medium.com/max/2400/1*2CHIjuhc9JSmpNjoSQl3aw.jpeg).
* Parce que **c'est la vie**. Les clients de messagerie ou les préférences utilisateurs peuvent changer le rendu de votre e‑mail, rendant votre contenu plus large que prévu et modifiant alors l'alignement de vos images. Vous n'avez pas envie que [ce genre de choses](https://imgur.com/NhoEN) arrive.

## Supporter Outlook à 120 dpi

Sur certaines configurations de Windows, *les Outlooks* (2007 à 2019 sur Windows) appliquent une mise à l'échelle des e-mails. Pour éviter des différences de rendu lors de la mise à l'échelle, vous devez appliquer les trois règles suivantes :

1. Ajouter l'espace de nom de Microsoft Office sur l'élément `<html>`.

```html
<html xmlns:o="urn:schemas-microsoft-com:office:office">
```

2. Ajouter la déclaration `OfficeDocumentSettings` suivante dans l'élément `<head>`.

```html
<!--[if mso]>
<xml>
  <o:OfficeDocumentSettings>
    <o:PixelsPerInch>96</o:PixelsPerInch>
  </o:OfficeDocumentSettings>
</xml>
<![endif]-->
```

3. Toujours utiliser des dimensions définies en CSS plutôt que dans des attributs HTML.

```html
<!-- Mauvais exemple -->
<table align="center" role="presentation" width="600">…</table>

<!-- Bon exemple -->
<table align="center" role="presentation" style="width:600px;">…</table>
```

**See also:**

* [Correcting Outlook DPI Scaling Issues](https://www.courtneyfantinato.com/correcting-outlook-dpi-scaling-issues/) by Courtney Fantinato.

## Remerciements

Ce guide est inspiré de [Code Guide](https://www.github.com/mdo/code-guide/) par @mdo, [Idiomatic CSS](https://www.github.com/necolas/idiomatic-css) par @necolas, [Frontend Guidelines](https://www.github.com/bendc/frontend-guidelines) par @bendc ou [Email Guidelines](https://www.stackoverflow.design/email/guidelines/) de Stack Overflow.

## License

*Recommandations pour l'intégration d'e‑mails* par Rémi Parmentier (@HTeuMeuLeu) est distribué sous licence MIT. Cette licence s'applique pour ce document et toutes ses traductions.

Basé sur le travail à l'adresse [github.com/hteumeuleu/email-guidelines](https//www.github.com/hteumeuleu/email-guidelines).
