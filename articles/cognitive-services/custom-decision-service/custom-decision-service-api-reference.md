---
title: Reference k rozhraní API – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Kompletní příručka rozhraní API pro Custom Decision Service.
services: cognitive-services
author: slivkins
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: slivkins
ms.openlocfilehash: 3476c353a2b5c5eeaaa7406712bebd178ac5c1e7
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227739"
---
# <a name="api"></a>Rozhraní API

Azure Custom Decision Service nabízí dvě rozhraní API, které jsou volány pro každé rozhodnutí: [hodnocení API](#ranking-api) zadání hodnocení akce a [Reward API](#reward-api) výstup potřebu. Kromě toho poskytují [API nastavit akci](#action-set-api-customer-provided) k určení akce, které Azure Custom Decision Service. Tento článek se týká těchto tří rozhraní API. Typický scénář se používá níže má zobrazit, když Custom Decision Service optimalizuje hodnocení článků.

## <a name="ranking-api"></a>Hodnocení rozhraní API

Hodnocení API používá standardní [JSONP](https://en.wikipedia.org/wiki/JSONP)– vzor komunikace styl optimalizovat latenci a obejít [zásada stejného zdroje](https://en.wikipedia.org/wiki/Same-origin_policy). Druhá možnost zakazuje JavaScript načítání dat z mimo na stránce původu.

Tento fragment kódu vložte do HTML head přední stránky (kde se zobrazují přizpůsobený seznam článků):

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
> Funkce zpětného volání musí být definovaná před volání rozhraní API pro hodnocení.

> [!TIP]
> Pokud chcete zlepšit latenci, hodnocení API je zveřejněné prostřednictvím protokolu HTTP místo protokolu HTTPS, stejně jako v `http://ds.microsoft.com/api/v2/<appId>/rank/*`.
> Koncový bod HTTPS musí ale použijí, pokud je na přední stránce poskytované přes HTTPS.

Při použití parametrů nejsou odpověď HTTP z rozhraní API hodnocení je řetězec ve formátu JSONP:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<A2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]});
```

Prohlížeč pak spustí tento řetězec jako volání funkce `callback()`.

Parametr pro funkci zpětného volání v předchozím příkladu má následující schéma:

- `ranking` poskytuje pořadí adres URL, který se má zobrazit.
- `eventId` se používá interně pomocí služby Custom Decision Service tak, aby odpovídaly toto pořadí odpovídající kliknutími.
- `appId` povolí používání funkce zpětného volání k rozlišení mezi více aplikacemi Custom Decision Service běží na stejné webové stránce.
- `actionSets` obsahuje všechny akce sada použitá v rámci volání rozhraní API hodnocení, spolu s časové razítko UTC poslední úspěšná aktualizace. Custom Decision Service pravidelně aktualizuje informační kanály sady akcí. Například pokud některá akce sady nejsou aktuální, funkce zpětného volání může být nutné, aby jejich výchozím nastavení se používá.

> [!IMPORTANT]
> Nastaví zadanou akci zpracování a pravděpodobně vyřazen a vytvoří výchozí řazení článků. Výchozí pořadí pak získá pořadí změníte a vrátila v odpovědi HTTP. Výchozí pořadí je definováno zde:
>
> - V rámci jednotlivých sad akce jsou vyřazeny článcích na 15 nejnovější články (Pokud se vrátí víc než 15).
> - Pokud jsou zadány více sad akce, jsou sloučeny ve stejném pořadí jako při volání rozhraní API. Původní pořadí článků je zachováno v rámci jednotlivých sad akce. Duplicity se odeberou a místo toho použití starších kopií.
> - První `n` články jsou uchovány z sloučený seznam článků, kde `n=20` ve výchozím nastavení.

### <a name="ranking-api-with-parameters"></a>Hodnocení rozhraní API s parametry

Rozhraní API pro hodnocení umožňuje tyto parametry:

- `details=1` a `details=2` vloží další podrobnosti o jednotlivých článku uvedené v `ranking`.
- `limit=<n>` Určuje maximální počet články ve výchozím nastavení se používá. `n` musí být v rozmezí `2` a `30` (nebo jinak se zkrátí na `2` nebo `30`v uvedeném pořadí).
- `dnt=1` Zakáže souborů cookie.

Parametry lze spojit do standardní, syntaxe řetězce dotazu, třeba `details=2&dnt=1`.

> [!IMPORTANT]
> Výchozí nastavení v Evropě by mělo být `dnt=1` dokud zákazník souhlasí s hlavičky souboru cookie. Také je třeba výchozí nastavení pro weby, které cílí nezletilé osoby. Další informace najdete v tématu [podmínky použití](https://www.microsoft.com/cognitive-services/en-us/legal/CognitiveServicesTerms20160804).

`details=1` Element vloží každého článku `guid`, pokud je poskytovaný rozhraním API nastavit akci. Odpověď HTTP:

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

`details=2` Element přidá další podrobnosti, které služby Custom Decision Service může extrahovat z článků optimalizace pro vyhledávací weby metaznačky [snadné kód](https://github.com/Microsoft/mwt-ds/tree/master/Crawl):

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

## <a name="reward-api"></a>Účet rozhraní API

Vlastní Decision Service uživatel klikne na pouze na nejvyšší pozici. Každý kliknutím je interpretován jako reward 1. Chybějící kliknutí, je interpretován jako reward 0. Kliknutí jsou porovnány s odpovídající pořadí s použitím ID událostí, které jsou generované [hodnocení API](#ranking-api) volání. V případě potřeby event ID je možné předat pomocí souborů cookie relace.

Pro zpracování klepněte na horní pozici, vložte tento kód na přední stránce:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Tady `data` je argumentem `callback()` fungovat, jak je popsáno výše. Pomocí `data` v klikněte na kód pro zpracování vyžaduje některé péči. Příklad v tomto [kurzu](custom-decision-service-tutorial-news.md#use-the-apis).

Pouze pro testování, je možné volat rozhraní API Reward prostřednictvím [cURL](https://en.wikipedia.org/wiki/CURL):

```sh
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST -d 1 -H "Content-Type: application/json"
```

Očekávaný výsledek je odpověď HTTP 200 (OK). Zobrazí se reward 1 pro tuto událost v protokolu a (Pokud se klíče účtu služby Azure storage byla zadána na portálu).

## <a name="action-set-api-customer-provided"></a>Akce – nastavit API (zajišťované zákazníkem)

Na vysoké úrovni akci nastavení rozhraní API vrátí seznam článků (akce). Každý článek je určený její adresu URL a (volitelně) název článku a datum publikace. Můžete určit, že nastaví několik akcí na portálu. Jiný účet akce nastavení API by měla sloužit pro každou sadu akcí, jako odlišné adresy URL.

Každý API nastavit akci lze provést dvěma způsoby: jako kanál RSS nebo informační kanál Atom. Buď jeden by měl odpovídat standardu a vrátit správný kód XML. Informační kanály RSS tady je příklad:

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

- `<link>` je povinný a slouží jako akce ID.
- `<date>` se ignoruje, pokud je menší než nebo rovna hodnotě 15 položky. v opačném případě je povinný.
  - Pokud existuje více než 15 položky, 15 nejnovější ty se používají.
  - Musí být ve formátu standard pro informační kanály RSS nebo Atom, v uvedeném pořadí:
    - [RFC 822](https://tools.ietf.org/html/rfc822) RSS: například `"Fri, 28 Apr 2017 18:02:06 GMT"`
    - [RFC 3339](https://tools.ietf.org/html/rfc3339) pro Atom: například `"2016-12-19T16:39:57-08:00"`
- `<title>` je volitelný a slouží ke generování funkcí, které popisují článku.
- `<guid>` je volitelný a předaných funkci zpětného volání systému (Pokud `?details` je zadán parametr ve volání rozhraní API pořadí).

Další prvky uvnitř `<item>` jsou ignorovány.

Informační kanál Atom nebyl verze používá stejnou syntaxi XML a konvence.

> [!TIP]
> Pokud váš systém používá vlastní ID článku, mohou být předány do funkce zpětného volání ve pomocí `<guid>`.
