---
title: API – kognitivní služby Azure | Microsoft Docs
description: Kompletní a uživatelsky přívětivý API Průvodce služby Azure vlastní rozhodnutí, rozhraní API založené na cloudu kontextové rozhodnutí, které zvýší prostředí.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/11/2018
ms.author: slivkins
ms.reviewer: marcozo, alekh
ms.openlocfilehash: 403b17e33394016a07a7b33ba1bcbfe6afdcc05b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343554"
---
# <a name="api"></a>Rozhraní API

Služba Azure rozhodnutí vlastní nabízí dvě rozhraní API, které se nazývají pro každé rozhodnutí: [řazení rozhraní API](#ranking-api) jako vstup hodnocení akcí a [potřebu API](#reward-api) výstup potřebu. Kromě toho zadáte [API nastavit akce](#action-set-api-customer-provided) zadejte akce službě Azure vlastního rozhodnutí. Tento článek se týká těchto tří rozhraní API. Typický scénář se používá níže zobrazí, když služba rozhodnutí vlastní optimalizuje hodnocení článků.

## <a name="ranking-api"></a>Řazení rozhraní API

Hodnocení API používá standardní [JSONP](https://en.wikipedia.org/wiki/JSONP)– styl komunikace vzor optimalizovat latenci a nepoužívat [stejného původu zásad](https://en.wikipedia.org/wiki/Same-origin_policy). K tomu zakazuje JavaScript načítání dat z mimo stránky původu.

Vložte tento fragment kódu do head HTML front stránky (kde se zobrazují přizpůsobené seznam článků):

```html
// define the "callback function" to render UI
<script> function callback(data) { … } </script>
// "data" provides the ranking of URLs, see example below.

// call to Ranking API
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>?<parameters>" async></script>
// action set id is the name of the corresponding RSS/Atom feed.

// same call with multiple action sets:
// <script src="https://ds.microsoft.com/api/v2/<appId>/rank/<A1>/<A2>/.../<An>?<parameters>" async></script>
```

> [!IMPORTANT]
> Funkce zpětného volání musí být definován před voláním rozhraní API řazení.

> [!TIP]
> Pokud chcete zlepšit latenci, rozhraní API řazení je vystaven prostřednictvím protokolu HTTP místo protokolu HTTPS, jako v `http://ds.microsoft.com/api/v2/<appId>/rank/*`.
> Koncový bod HTTPS, ale musíte použít, pokud titulní stránky obsluhovaného prostřednictvím protokolu HTTPS.

Parametry se nepoužívají, odpovědi HTTP z rozhraní API řazení při řetězec ve formátu JSONP:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<A2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]});
```

Prohlížeč pak provede tento řetězec jako volání `callback()` funkce.

Parametr funkce zpětného volání v předchozím příkladu má následující schéma:

- `ranking` poskytuje hodnocení adres URL, který se má zobrazit.
- `eventId` je interně službou rozhodnutí vlastní tak, aby odpovídaly tento hodnocení odpovídající kliknutími.
- `appId` umožňuje funkce zpětného volání k rozlišení mezi více aplikacemi vlastní rozhodnutí služby spuštěné na stejné webové stránce.
- `actionSets` uvádí každá akce používá řazení volání rozhraní API služby, spolu s UTC časové razítko poslední úspěšná aktualizace. Vlastní služba rozhodnutí se pravidelně aktualizuje kanály set akce. Například pokud některá ze sad akce nejsou aktuální, funkce zpětného volání chtít vrátit zpět k jejich výchozím nastavení se používá.

> [!IMPORTANT]
> Nastaví zadanou akci zpracování a pravděpodobně vyřazení a vytvořit výchozí hodnocení článků. Výchozí pořadí pak získá přeuspořádány a vrátí odpověď HTTP. Výchozí pořadí je definováno zde:
>
> - V rámci jednotlivých sad akce jsou vyřazení článků 15 nejnovější článků (pokud jsou vráceny více než 15).
> - Když je zadaná víc sad akce, se sloučí ve stejném pořadí jako volání rozhraní API. Původní pořadí z článků je zachováno v rámci jednotlivých sad akce. Považuje starší kopie se odeberou duplicitní položky.
> - První `n` články jsou uchovány ze seznamu sloučené článků, kde `n=20` ve výchozím nastavení.

### <a name="ranking-api-with-parameters"></a>Řazení rozhraní API s parametry

Rozhraní API klasifikace umožňuje tyto parametry:

- `details=1` a `details=2` vloží další podrobnosti o jednotlivých článků uvedené v `ranking`.
- `limit=<n>` Určuje maximální počet články ve výchozím nastavení se používá. `n` musí být v rozmezí `2` a `30` (nebo jinak se zkrátí na `2` nebo `30`, v uvedeném pořadí).
- `dnt=1` Zakáže soubory cookie uživatele.

Parametry lze spojit do standard syntaxe řetězce dotazu, například `details=2&dnt=1`.

> [!IMPORTANT]
> Výchozí nastavení v Evropě by měl být `dnt=1` dokud zákazník souhlasí s tím Banner informující o souboru cookie. Také je nutné výchozí nastavení pro weby, které cílí na nezletilých. Další informace najdete v tématu [podmínky použití](https://www.microsoft.com/cognitive-services/en-us/legal/CognitiveServicesTerms20160804).

`details=1` Element vloží jednotlivých článků `guid`, pokud je zpracovaných rozhraním API nastavit akce. Odpověď HTTP:

```json
callback({
   "ranking":[{"id":"url1","details":[{"guid":"123"}]},
              {"id":"url2","details":[{"guid":"456"}]},
              {"id":"url3","details":[{"guid":"789"}]}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

`details=2` Element přidá další podrobnosti, které může služba rozhodnutí vlastní extrahovat z metaznačky optimalizace pro vyhledávací weby články [featurization kód](https://github.com/Microsoft/mwt-ds/tree/master/Crawl):

- `title` z `<meta property="og:title" content="..." />` nebo `<meta property="twitter:title" content="..." />` nebo `<title>...</title>`
- `description` z `<meta property="og:description" ... />` nebo `<meta property="twitter:description" content="..." />` nebo `<meta property="description" content="..." />`
- `image` z `<meta property="og:image" content="..." />`
- `ds_id` z `<meta name=”microsoft:ds_id” content="..." />`

Odpověď HTTP:

```json
callback({
   "ranking":[{"id":"url1","details":<details>},
              {"id":"url2","details":<details>},
              {"id":"url3","details":<details>}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

`<details>` Element:

```json
[{"guid":"123"}, {"description":"some text", "ds_id":"234", "image":"ImageUrl1", "title":"some text"}]
```

## <a name="reward-api"></a>Potřebu rozhraní API

Vlastní rozhodnutí služby používá klikne pouze na nejvyšší pozici. Každý kliknutím interpretována jako potřebu 1. Nedostatečná kliknutí interpretována jako potřebu 0. Klikne na tlačítko se splní s pořadím odpovídající pomocí ID událostí, které jsou generované [řazení rozhraní API](#ranking-api) volání. V případě potřeby událost ID lze předat prostřednictvím soubory cookie relace.

Tento kód pro zpracování a klikněte na nejvyšší pozici, vložte na titulní stránky:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Zde `data` je argument `callback()` fungovat, jak je popsáno výše. Pomocí `data` v klikněte na kód pro zpracování vyžaduje některé pozor. Příklad je uveden v tomto [kurzu](custom-decision-service-tutorial-news.md#use-the-apis).

Pro testování pouze, je možné volat rozhraní API potřebu prostřednictvím [cURL](https://en.wikipedia.org/wiki/CURL):

```sh
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST -d 1 -H "Content-Type: application/json"
```

Očekávaný účinek se odpovědi HTTP 200 (OK). Potřebu 1 pro tuto událost v protokolu zobrazí (Pokud je klíč účtu úložiště Azure byla zadána na portálu.).

## <a name="action-set-api-customer-provided"></a>Akce nastavit API (zadat zákazníka)

Na vysoké úrovni rozhraní API nastavit akce vrátí seznam článků (akce). Každý článek je určena jeho adresa URL a (volitelně) název článku a datum publikace. Můžete zadat že několik akci se nastaví na portálu. Jiný účet akce nastavit API má být použit pro každou sadu akcí, jako odlišné adresy URL.

Každé rozhraní akce nastavit API lze implementovat dvěma způsoby: jako informačního kanálu RSS, nebo jako informačního kanálu Atom. Buď jeden by měl odpovídat standardní a vrátit správný kód XML. Technologie RSS tady je příklad:

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <guid>guid (could be a your internal database id)</guid>
      <pubDate>date</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Každý nejvyšší úrovně `<item>` element popisuje akce:

- `<link>` je povinná a slouží jako akce ID.
- `<date>` je ignorována, pokud je menší než nebo rovna hodnotě 15 položky; jinak je povinné.
  - Pokud existuje více než 15 položky, 15 nejnovější ty, které se používají.
  - Musí být ve formátu standard pro informační kanály RSS nebo Atom, v uvedeném pořadí:
    - [RFC 822](https://tools.ietf.org/html/rfc822) pro RSS: například `"Fri, 28 Apr 2017 18:02:06 GMT"`
    - [RFC 3339](https://tools.ietf.org/html/rfc3339) pro Atom: například `"2016-12-19T16:39:57-08:00"`
- `<title>` je volitelný a slouží ke generování funkce, které popisují článek.
- `<guid>` je volitelný a předaný prostřednictvím systému funkci zpětného volání (Pokud `?details` je zadán parametr ve volání rozhraní API řazení).

Další prvky uvnitř `<item>` jsou ignorovány.

Atom informačního kanálu verze používá stejná syntaxe XML a konvence.

> [!TIP]
> Pokud systém využívá vlastní ID článku, se mohou být předány do funkce zpětného volání ve pomocí `<guid>`.