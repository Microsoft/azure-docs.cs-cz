---
title: Reference k rozhraní API – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Kompletní příručka k rozhraní API pro Custom Decision Service.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 569a1c83562a995f15e12013c864ef4c0447d963
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161693"
---
# <a name="api"></a>API

Azure Custom Decision Service poskytuje dvě rozhraní API, která jsou volána pro každé rozhodnutí: [rozhraní API pro hodnocení](#ranking-api) k zadání pořadí akcí a [API pro odměnu](#reward-api) pro výstup. Kromě toho poskytujeme [rozhraní API sady akcí](#action-set-api-customer-provided) , které určuje akce pro Azure Custom Decision Service. Tento článek se zabývá těmito třemi rozhraními API. Níže se používá Typický scénář, který ukazuje, kdy Custom Decision Service optimalizuje hodnocení článků.

## <a name="ranking-api"></a>Rozhraní API pro řazení

Rozhraní API pro hodnocení používá ke optimalizaci latence a obejití [zásad stejného původu](https://en.wikipedia.org/wiki/Same-origin_policy)standardní způsob komunikace ve stylu [JSONP](https://en.wikipedia.org/wiki/JSONP). Druhá z nich zabrání v JavaScriptu načtení dat mimo počátek stránky.

Vložte tento fragment kódu do HTML head stránky (kde se zobrazí individuální seznam článků):

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
> Funkce zpětného volání musí být definována před voláním rozhraní API pro řazení.

> [!TIP]
> Pro zvýšení latence je rozhraní API pro hodnocení zveřejněné přes protokol HTTP místo protokolu HTTPS, jako v `https://ds.microsoft.com/api/v2/<appId>/rank/*`.
> Koncový bod HTTPS je však nutné použít, pokud je Front-Page obsluhován prostřednictvím protokolu HTTPS.

Pokud se nepoužívají parametry, odpověď HTTP z rozhraní API pro hodnocení je řetězec ve formátu JSONP:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<A2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]});
```

Prohlížeč pak spustí tento řetězec jako volání funkce `callback()`.

Parametr funkce zpětného volání v předchozím příkladu má následující schéma:

- `ranking` poskytuje pořadí adres URL, které se mají zobrazit.
- `eventId` se interně používá Custom Decision Service k tomu, aby odpovídala tomuto hodnocení s odpovídajícími kliknutími.
- `appId` umožňuje funkci zpětného volání rozlišovat mezi několika aplikacemi Custom Decision Service spuštěnými na stejné webové stránce.
- `actionSets` vypíše každou sadu akcí použitou ve volání rozhraní API řazení společně s časovým razítkem UTC poslední úspěšné aktualizace. Custom Decision Service pravidelně aktualizuje informační kanály sady akcí. Například pokud některé sady akcí nejsou aktuální, může být nutné, aby funkce zpětného volání mohla přejít zpět na výchozí hodnocení.

> [!IMPORTANT]
> Zadané sady akcí jsou zpracovávány a pravděpodobně vyřazení, aby bylo možné vytvořit výchozí pořadí článků. Výchozí hodnocení pak bude přeobjednáno a vráceno v odpovědi HTTP. Výchozí hodnocení je definováno zde:
>
> - V rámci každé sady akcí se články vyřadí do 15 nejnovějších článků (Pokud se vrátí víc než 15).
> - Pokud je zadáno více sad akcí, jsou sloučeny ve stejném pořadí jako v volání rozhraní API. Původní pořadí článků se zachová v každé sadě akcí. Duplicity se odeberou namísto předchozích kopií.
> - První `n` články jsou uchovávány ze sloučeného seznamu článků, kde `n=20` ve výchozím nastavení.

### <a name="ranking-api-with-parameters"></a>Rozhraní API pro řazení s parametry

Rozhraní API pro hodnocení umožňuje tyto parametry:

- `details=1` a `details=2` vloží další podrobnosti o každém článku uvedeném v `ranking`.
- `limit=<n>` určuje maximální počet článků ve výchozím hodnocení. `n` musí být mezi `2` a `30` (nebo se zkrátí na `2` nebo `30`v uvedeném pořadí).
- `dnt=1` zakáže soubory cookie uživatele.

Parametry lze kombinovat v kombinaci se standardem, syntaxí řetězce dotazu, například `details=2&dnt=1`.

> [!IMPORTANT]
> Výchozí nastavení v Evropě by mělo být `dnt=1`, dokud zákazník nesouhlasí s hlavičkou souboru cookie. Mělo by se také jednat o výchozí nastavení pro weby, které cílí na nezletilé. Další informace najdete v tématu věnovaném [podmínkám použití](https://www.microsoft.com/cognitive-services/en-us/legal/CognitiveServicesTerms20160804).

Element `details=1` vloží `guid`každého článku, pokud je obsluhován rozhraním API sady akcí. Odpověď HTTP:

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

Element `details=2` přidá další podrobnosti, které Custom Decision Service může extrahovat z článku "SEO MetaTags [featurization Code](https://github.com/Microsoft/mwt-ds/tree/master/Crawl):

- `title` z `<meta property="og:title" content="..." />` nebo `<meta property="twitter:title" content="..." />` nebo `<title>...</title>`
- `description` z `<meta property="og:description" ... />` nebo `<meta property="twitter:description" content="..." />` nebo `<meta property="description" content="..." />`
- `image` z `<meta property="og:image" content="..." />`
- `ds_id` z `<meta name="microsoft:ds_id" content="..." />`

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

Element `<details>`:

```json
[{"guid":"123"}, {"description":"some text", "ds_id":"234", "image":"ImageUrl1", "title":"some text"}]
```

## <a name="reward-api"></a>API pro odměnu

Custom Decision Service používá pouze kliknutí na nejvyšší pozici. Každé kliknutí je interpretováno jako odměna 1. Chybějící kliknutí je interpretováno jako odměňování 0. Kliknutí se shodují s odpovídajícím pořadím pomocí ID událostí, která jsou generována voláním [rozhraní API řazení](#ranking-api) . V případě potřeby lze ID události předat pomocí souborů cookie relace.

Pokud chcete zpracovat kliknutí na horní slot, vložte tento kód na frontu:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Zde `data` je argument funkce `callback()`, jak je popsáno výše. Použití `data` v kódu pro zpracování Click vyžaduje péči. V tomto [kurzu](custom-decision-service-tutorial-news.md#use-the-apis)se zobrazí příklad.

Pro pouze testování je možné rozhraní API pro odměnu volat prostřednictvím [oblé](https://en.wikipedia.org/wiki/CURL):

```sh
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST -d 1 -H "Content-Type: application/json"
```

Očekávaným výsledkem je odpověď HTTP 200 (OK). Pro tuto událost můžete zobrazit odměnu 1 pro tuto událost v protokolu (Pokud se na portálu zadal klíč účtu úložiště Azure).

## <a name="action-set-api-customer-provided"></a>Rozhraní API sady akcí (zadáno zákazníkem)

V případě vysoké úrovně rozhraní API sady akcí vrátí seznam článků (akce). Každý článek je určen jeho adresou URL a (volitelně) názvem článku a datem publikace. Na portálu můžete zadat několik sad akcí. Pro každou sadu akcí by se mělo použít jiné rozhraní API sady akcí jako odlišná adresa URL.

Každé rozhraní API sady akcí se dá implementovat dvěma způsoby: jako kanál RSS nebo jako informační kanál Atom. Jednu z nich by měla odpovídat standardu a vracet správný kód XML. V případě RSS tady je příklad:

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

Každá `<item>` element nejvyšší úrovně popisuje akci:

- `<link>` je povinný a používá se jako ID akce.
- `<date>` se ignoruje, pokud je menší nebo rovno 15 položkám; v opačném případě je povinná.
  - Pokud existuje více než 15 položek, použije se 15 nejnovějších.
  - Musí být ve standardním formátu pro RSS nebo Atom, v uvedeném pořadí:
    - [RFC 822](https://tools.ietf.org/html/rfc822) pro technologii RSS: například `"Fri, 28 Apr 2017 18:02:06 GMT"`
    - [RFC 3339](https://tools.ietf.org/html/rfc3339) pro Atom: například `"2016-12-19T16:39:57-08:00"`
- `<title>` je volitelná a používá se ke generování funkcí popisujících článek.
- `<guid>` je volitelná a předána systémem do funkce zpětného volání (Pokud je parametr `?details` zadán v volání rozhraní API pro řazení).

Ostatní prvky uvnitř `<item>` jsou ignorovány.

Verze informačního kanálu Atom používá stejnou syntaxi a konvence XML.

> [!TIP]
> Pokud váš systém používá vlastní ID článků, mohou být předány do funkce zpětného volání pomocí `<guid>`.
