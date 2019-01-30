---
title: 'Kurz: Přizpůsobení článku – Custom Decision Service'
titlesuffix: Azure Cognitive Services
description: Tento kurz je určen pro přizpůsobení článků pro kontextové rozhodování.
services: cognitive-services
author: slivkins
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: slivkins
ms.openlocfilehash: 1589ef94946a6396ee51b8fecfe2ed3e80db08e5
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55212218"
---
# <a name="tutorial-article-personalization-for-contextual-decision-making"></a>Kurz: Přizpůsobení článku kontextové rozhodování

Tento kurz se zaměřuje na přizpůsobení výběru článků na úvodní stránce webu. Služba Custom Decision Service například ovlivňuje *množství* seznamů článků na přední stránce. Stránka může být webovou stránkou se zprávami, která se zaměřuje pouze na politiku a sport. Měla by zobrazovat tři seřazené seznamy článků: politika, sport a nejnovější.

## <a name="applications-and-action-sets"></a>Aplikace a sady akcí

Toto je postup, jak scénář vhodně zařadit do architektury. Představme si tři aplikace, jednu pro každý seznam, který se optimalizuje: app-politics (aplikace pro politiku), app-sports (aplikace pro sport) a app-recent (aplikace pro nejnovější zprávy). Pro určení případných článků pro jednotlivé aplikace existují dvě sady akcí: jedna pro politiku a jedna pro sport. Sada akcí pro app-recent (aplikaci pro nejnovější zprávy) se vytvoří automaticky jako spojení dvou ostatních sad.

> [!TIP]
> Sady akcí se ve službě Custom Decision Service můžou sdílet napříč aplikacemi.

## <a name="prepare-action-set-feeds"></a>Příprava informačních kanálů sad akcí

Služba Custom Decision Service využívá sady akcí prostřednictvím informačních kanálů RSS a Atom poskytovaných zákazníkem. Vy poskytujete dva informační kanály: jeden pro politiku a jeden pro sport. Předpokládejme, že jsou obsluhovány z webu `http://www.domain.com/feeds/<feed-name>`.

Každý informační kanál poskytuje seznam článků. V RSS je každý z nich určen elementem `<item>` následujícím způsobem:

```xml
<rss version="2.0"><channel>
   <item>
      <title><![CDATA[article title]]></title>
      <link>"article url"</link>
      <pubDate>publication date</pubDate>
    </item>
</channel></rss>
```

Pořadí článků je důležité. Určuje výchozí pořadí, což je váš nejlepší odhad pro uspořádání článků. Výchozí pořadí se pak použije k porovnání výkonu na [řídicím panelu](#performance-dashboard).

Další informace o formátu informačního kanálu najdete v [referenčních informacích k rozhraní API](custom-decision-service-api-reference.md#action-set-api-customer-provided).

## <a name="register-a-new-app"></a>Registrace nové aplikace

1. Přihlaste se pomocí [účtu Microsoft](https://account.microsoft.com/account). Na pásu karet klikněte na **My Portal** (Můj portál).

2. Pokud chcete zaregistrovat novou aplikaci, klikněte na tlačítko **New App** (Nová aplikace).

    ![Portál Custom Decision Service](./media/custom-decision-service-tutorial/portal.png)

3. Do textového pole **App ID** (ID aplikace) zadejte jedinečný název aplikace. Pokud už tento název používá jiný zákazník, systém vás požádá o zadání jiného ID aplikace. Zaškrtněte políčko **Advanced** (Rozšířené) a zadejte [připojovací řetězec](../../storage/common/storage-configure-connection-string.md) pro účet úložiště Azure. Za normálních okolností použijete stejný účet úložiště pro všechny vaše aplikace.

    ![Dialogové okno nové aplikace](./media/custom-decision-service-tutorial/new-app-dialog.png)

    Jakmile si zaregistrujete všechny tři aplikace ve výše uvedeném scénáři, zobrazí se jejich seznam:

    ![Seznam aplikací](./media/custom-decision-service-tutorial/apps.png)

    K tomuto seznamu se můžete vrátit kliknutím na tlačítko **Apps** (Aplikace).

4. V dialogovém okně **New App** (Nová aplikace) určete informační kanál akce. Informační kanály akcí můžete určit také kliknutím na tlačítko **Feeds** (Informační kanály) a pak kliknutím na tlačítko **New Feed** (Nový informační kanál). Zadejte **název** nového informačního kanálu, zadejte **adresu URL** místa, odkud je obsluhován, a pak zadejte **čas aktualizace**. Čas aktualizace určuje, jak často by služba Custom Decision Service měla informační kanál aktualizovat.

    ![Dialogové okno nového informačního kanálu](./media/custom-decision-service-tutorial/new-feed-dialog.png)

    Informační kanály akcí může využívat jakákoli aplikace, bez ohledu na to, kde jsou specifikované. Jakmile určíte oba informační kanály akcí ve scénáři, zobrazí se jejich seznam:

    ![Seznam informačních kanálů](./media/custom-decision-service-tutorial/feeds.png)

    K tomuto seznamu se můžete vrátit kliknutím na tlačítko **Feeds** (Informační kanály).

## <a name="use-the-apis"></a>Použití rozhraní API

Služba Custom Decision Service řadí články pomocí rozhraní Ranking API. Pokud chcete toto rozhraní API použít, vložte následující kód do hlavičky HTML na přední stránce:

```html
<!-- Define the "callback function" to render UI -->
<script> function callback(data) { … } </script>

<!--  call Ranking API after callback() is defined, separately for each app -->
<script src="https://ds.microsoft.com/api/v2/app-politics/rank/feed-politics" async></script>
<script src="https://ds.microsoft.com/api/v2/app-sports/rank/feed-sports" async></script>
<script src="https://ds.microsoft.com/api/v2/app-recent/rank/feed-politics/feed-sports" async></script>
<!-- NB: action feeds for 'app-recent' are listed one after another. -->
```

Odpovědí HTTP z rozhraní Ranking API je řetězec ve formátu JSONP. Například pro app-politics (aplikaci pro politiku) řetězec vypadá takto:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"app-politics",
   "actionSets":[{"id":"feed-politics","lastRefresh":"date"}] });
```

Prohlížeč pak spustí tento řetězec jako volání funkce `callback()`. Argument `data` ve funkci `callback()` obsahuje ID aplikace a hodnocení adres URL, které se mají vykreslit. Konkrétně funkce `callback()` by měla použít `data.appId`, aby došlo k rozlišení těchto tří aplikací. `eventId` se používá interně službou Custom Decision Service, aby se spárovalo poskytnuté hodnocení s odpovídajícím kliknutím, pokud existuje.

> [!TIP]
> `callback()` může pomocí pole `lastRefresh` zkontrolovat všechny informační kanály, jestli jsou aktuální. Pokud daný informační kanál není dostatečně aktuální, `callback()` může ignorovat zadané hodnocení, zavolat tento informační kanál přímo a použít výchozí hodnocení poskytnuté informačním kanálem.

Další informace o specifikacích a dalších možnostech poskytovaných rozhraním Ranking API najdete v [referenčních informacích o rozhraní API](custom-decision-service-api-reference.md).

Nejvhodnější volby článků uživatele se vrátí voláním rozhraní Reward API. Při přijetí nejvhodnější volby článku se by měl vyvolat následující kód na přední stránce:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/<eventId>',
    contentType: "application/json" })
```

Použití `appId` a `eventId` v kódu pro zpracování kliknutí vyžaduje opatrnost. Funkci `callback()` můžete například implementovat následujícím způsobem:

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

V tomto příkladu implementujte funkci `render()` za účelem vykreslení daného článku pro danou aplikaci. Tato funkce poskytuje vstupy ID aplikace a článek (ve formátu z rozhraní Ranking API). Parametr `onClick` je funkce, která by se měla volat z `render()` pro zpracování kliknutí. Zkontroluje, jestli bylo kliknutí provedeno na horní pozici. Potom zavolá rozhraní Reward API s odpovídajícím ID aplikace a ID události.

## <a name="next-steps"></a>Další postup

* Více informací o podporovaných funkcích se dozvíte v [referenční dokumentaci k rozhraní API](custom-decision-service-api-reference.md).
