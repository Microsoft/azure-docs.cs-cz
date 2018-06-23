---
title: Článek přizpůsobení - kognitivní služeb Azure | Microsoft Docs
description: Kurz pro přizpůsobení článku službou Azure vlastní rozhodnutí, rozhraní API založené na cloudu kontextové rozhodnutí.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/08/2018
ms.author: slivkins;marcozo;alekh;marossi
ms.openlocfilehash: 35d0567f81a23d4726461059eb6fd31e04228697
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343433"
---
# <a name="article-personalization"></a>Přizpůsobení článku

Tento kurz se zaměřuje na přizpůsobení výběru článků na titulní stránky webu. Ovlivňuje službu rozhodnutí vlastní *více* seznam článků v přední stránky pro instanci. Možná stránky je zprávy web, který obsahuje pouze politika a sportu. By se zobrazit tři seřazený seznam článků: politika, sportu a poslední.

## <a name="applications-and-action-sets"></a>Aplikace a nastaví akce

Zde je postup vyhovovat vašemu scénáři do rozhraní. Pojďme Představte si tři aplikace, jeden pro každou seznam, který je právě optimalizovaná: politika aplikace, aplikace sportu a nejnovější aplikace. Pokud chcete zadat candidate články pro každou aplikaci, existují dvě akce sady: jeden pro politika a jeden pro sportu. Sada pro aplikaci poslední akci dodává automaticky jako sjednocení dvou sad.

> [!TIP]
> Nastaví akce můžete sdílet mezi aplikací ve službě vlastní rozhodnutí.

## <a name="prepare-action-set-feeds"></a>Příprava akce sadu kanálů

Vlastní rozhodnutí služba využívá Nastaví akce prostřednictvím informačních kanálů RSS nebo Atom poskytované zákazníkovi. Zadejte dvě informační kanály: jeden pro politika a jeden pro sportu. Předpokládejme, že je zobrazovaná `http://www.domain.com/feeds/<feed-name>`.

Každý kanál poskytuje seznam článků. Informační kanály RSS, je zadána každé z nich `<item>` element následujícím způsobem:

```xml
<rss version="2.0"><channel>
   <item>
      <title><![CDATA[article title]]></title>
      <link>"article url"</link>
      <pubDate>publication date</pubDate>
    </item>
</channel></rss>
```

Pořadí článků záleží. Určuje výchozí pořadí, což je co nejlepší odhad pro uspořádání články. Výchozí pořadí se pak použije k porovnání výkonu na [řídicí panel](#performance-dashboard).

Další informace o formátu informačního kanálu najdete v tématu [referenční dokumentace rozhraní API](custom-decision-service-api-reference.md#action-set-api-customer-provided).

## <a name="register-a-new-app"></a>Zaregistrujte novou aplikaci

1. Přihlaste se pomocí vaší [účtu Microsoft](https://account.microsoft.com/account). Na pásu karet klikněte na tlačítko **Moje portál**.

2. Chcete-li zaregistrovat nové aplikace, klikněte na tlačítko **novou aplikaci** tlačítko.

    ![Portál Custom Decision Service](./media/custom-decision-service-tutorial/portal.png)

3. Zadejte jedinečný název pro vaši aplikaci ve **ID aplikace** textové pole. Tento název se již používá jiný zákazníka, systém požádá o vyberte ID jiné aplikace. Vyberte **Upřesnit** zaškrtněte políčko a zadejte [připojovací řetězec](../../storage/common/storage-configure-connection-string.md) pro váš účet úložiště Azure. Za normálních okolností použijete stejný účet úložiště pro všechny aplikace.

    ![Dialogové okno Nový aplikace](./media/custom-decision-service-tutorial/new-app-dialog.png)

    Po registraci všech tří aplikací ve výše uvedené scénáře, jsou uvedeny:

    ![Seznam aplikací](./media/custom-decision-service-tutorial/apps.png)

    Vám může vraťte do tohoto seznamu kliknutím **aplikace** tlačítko.

4. V **novou aplikaci** dialogovém okně zadejte akci informačního kanálu. Informační kanály akci lze také kliknutím **kanály** tlačítko a pomocí kliknutím **nový kanál** tlačítko. Zadejte **název** nový kanál, zadejte **adresa URL** z které it je zpracovat a zadejte **aktualizujte čas**. Doba aktualizace Určuje, jak často má vlastní rozhodnutí služby aktualizovat informačního kanálu.

    ![Informačního kanálu dialogové okno Nový](./media/custom-decision-service-tutorial/new-feed-dialog.png)

    Informační kanály akce slouží žádné aplikace, bez ohledu na to, kde zadal. Po zadání obou akce kanály ve scénáři, jsou uvedeny:

    ![Seznam informačních kanálů](./media/custom-decision-service-tutorial/feeds.png)

    Vám může vraťte do tohoto seznamu kliknutím **kanály** tlačítko.

## <a name="use-the-apis"></a>Použití rozhraní API

Službu rozhodnutí vlastní určuje pořadí články prostřednictvím rozhraní API řazení. Pokud chcete používat toto rozhraní API, vložte následující kód do head HTML front stránky:

```html
<!-- Define the "callback function" to render UI -->
<script> function callback(data) { … } </script>

<!--  call Ranking API after callback() is defined, separately for each app -->
<script src="https://ds.microsoft.com/api/v2/app-politics/rank/feed-politics" async></script>
<script src="https://ds.microsoft.com/api/v2/app-sports/rank/feed-sports" async></script>
<script src="https://ds.microsoft.com/api/v2/app-recent/rank/feed-politics/feed-sports" async></script>
<!-- NB: action feeds for 'app-recent' are listed one after another. -->
```

Odpověď HTTP z rozhraní API řazení je řetězec ve formátu JSONP. Pro aplikace politika například řetězec vypadá takto:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"app-politics",
   "actionSets":[{"id":"feed-politics","lastRefresh":"date"}] });
```

Prohlížeč pak provede tento řetězec jako volání `callback()` funkce. `data` Argument `callback()` funkce obsahuje ID aplikace a hodnocení adres URL, k vykreslení. Konkrétně `callback()` by měl používat `data.appId` k rozlišení mezi tři aplikace. `eventId` se používá interně službou rozhodnutí vlastní tak, aby odpovídaly zadaná klasifikace odpovídající kliknutím, pokud existuje.

> [!TIP]
> `callback()` může zkontrolovat každou akci kanálu pro aktuálnosti pomocí `lastRefresh` pole. Pokud daný informačního kanálu není dostatečně aktuální, `callback()` například ignorovat zadané hodnocení, tento informační kanál volat přímo a pomocí hodnocení výchozí obsloužených informačního kanálu.

Další informace o specifikacích a další možnosti poskytovaný rozhraním API řazení, najdete v článku [referenční dokumentace rozhraní API](custom-decision-service-api-reference.md).

Volby důležitý článek od uživatele se vrátí při volání rozhraní API potřebu. Po přijetí důležitý článek volba by měla být na stránce front volána následující kód:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/<eventId>',
    contentType: "application/json" })
```

Pomocí `appId` a `eventId` kód pro zpracování klikněte na tlačítko vyžaduje některé pozor. Například můžete implementovat `callback()` funkce následujícím způsobem:

```javascript
function callback(data) {
    $.map(data.ranking, function (element) {
        //custom rendering function given content info
        render({appId:data.appId,
                article:element,
                onClick: element.id != data.rewardAction ? null :
                   function() {
                       $.ajax({
                       type: "POST",
                       url: '//ds.microsoft.com/api/v2/' + data.appId + '/reward/' + data.eventId,
                       contentType: "application/json" })}
                });
}}
```

V tomto příkladu implementovat `render()` funkce k vykreslení daný článek k dané aplikaci. Tato funkce uvede ID aplikace a v článku (ve formátu z rozhraní API řazení). `onClick` Parametr je funkce, která by měla být volána z `render()` pro zpracování klikněte na tlačítko. Zkontroluje, jestli kliknutím na horní slot. Pak zavolá rozhraní API potřebu s ID příslušné aplikace a ID události.

## <a name="next-steps"></a>Další postup

* Obrátit [referenční dokumentace rozhraní API](custom-decision-service-api-reference.md) Další informace o funkci zadané.