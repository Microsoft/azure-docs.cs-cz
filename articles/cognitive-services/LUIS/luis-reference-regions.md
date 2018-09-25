---
title: Publikování oblasti a koncové body – LUIS
titleSuffix: Azure Cognitive Services
description: Oblast, ve kterém můžete publikovat aplikaci LUIS odpovídá oblast nebo umístění, ve kterém zadáte na webu Azure Portal, při vytváření klíče koncového bodu Azure LUIS. Při publikování aplikace LUIS automaticky generuje adresu URL koncového bodu pro oblast spojené s klíči. K publikování aplikace LUIS do více než jedné oblasti, budete potřebovat alespoň jeden klíč v jedné oblasti.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/11/2018
ms.author: diberry
ms.openlocfilehash: 205a17a985986aab8039afe824e7e872a9885169
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47039407"
---
# <a name="regions-and-keys"></a>Oblasti a klíčů

Oblast, ve kterém můžete publikovat aplikaci LUIS odpovídá oblast nebo umístění, ve kterém zadáte na webu Azure Portal, při vytváření klíče koncového bodu Azure LUIS. Pokud jste [publikování aplikace](./luis-how-to-publish-app.md), LUIS automaticky generuje adresu URL koncového bodu pro oblast spojené s klíči. K publikování aplikace LUIS do více než jedné oblasti, budete potřebovat alespoň jeden klíč v jedné oblasti. 

## <a name="luis-website"></a>Služba LUIS webu
Existují tři websites LUIS, podle oblasti. Musí se vytvářet a publikovat ve stejné oblasti. 

|LUIS|Oblast|
|--|--|
|[www.LUIS.ai][www.luis.ai]|USA<br>není Evropa<br>není Austrálie|
|[AU.LUIS.ai][au.luis.ai]|Austrálie|
|[EU.LUIS.ai][eu.luis.ai]|Evropa|

## <a name="regions-and-azure-resources"></a>Oblasti a prostředků Azure
Publikování aplikace do všech oblastí, které jsou spojené s prostředky služby LUIS přidat na portálu služby LUIS. Například aplikace vytvořená v [www.luis.ai][www.luis.ai], pokud vytvoříte prostředek LUIS v **westus** a přidejte ho do aplikace jako prostředek, publikování aplikace v dané oblasti. 

## <a name="public-apps"></a>Veřejné aplikace
Aplikace z veřejného app je publikována ve všech oblastech, tak, aby uživatel s klíčem služby LUIS prostředků na základě oblasti můžete přistupovat k aplikaci v oblasti podle toho, která souvisí s jejich klíč prostředku.

## <a name="publishing-regions"></a>Publikování oblastí

LUIS aplikací vytvořených v https://www.luis.ai lze publikovat na všechny koncové body s výjimkou [Evropské](#publishing-to-europe) a [australské](#publishing-to-australia) oblastech. 

Pro vytváření oblasti aplikace lze publikovat pouze na odpovídající publikovat oblasti. Pokud vaše aplikace je aktuálně v nesprávné oblasti pro vytváření, export aplikace a importujte ho do správné zdrojové oblasti pro publikování oblast. 

 Globální oblast | Oblast pro tvorbu<br>`API region name` | Publikování a dotazování na oblast<br>`API region name`   |   Služba LUIS webu | Formát adresy URL koncového bodu   |
|-----|------|------|------|------|
| Asie | USA – západ<br>`westus`| Východní Asie<br>`eastasia`     | [www.LUIS.ai][www.luis.ai] |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | USA – západ<br>`westus`| Jihovýchodní Asie<br>`souteastasia`     | [www.LUIS.ai][www.luis.ai] |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Austrálie](#publishing-to-australia) | Austrálie – východ<br>`australiaeast`| Austrálie – východ<br>`australiaeast`     |   [AU.LUIS.ai][au.luis.ai] | https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Evropa](#publishing-to-europe)| Západní Evropa<br>`westeurope`| Severní Evropa<br>`northeurope`     | [EU.LUIS.ai][eu.luis.ai]|  https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[Evropa](#publishing-to-europe) | Západní Evropa<br>`westeurope`| Západní Evropa<br>`westeurope`     | [EU.LUIS.ai][eu.luis.ai]|  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Severní Amerika | USA – západ<br>`westus` | USA – východ<br>`eastus`      |[www.LUIS.ai][www.luis.ai] |   https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika | USA – západ<br>`westus` | Východní USA 2<br>`eastus2`     | [www.LUIS.ai][www.luis.ai] |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika | USA – západ<br>`westus` | Střed USA – jih<br>`southcentralus`     | [www.LUIS.ai][www.luis.ai] |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Severní Amerika | USA – západ<br>`westus` | Západní střed USA<br>`westcentralus`     |[www.LUIS.ai][www.luis.ai] |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika | USA – západ<br>`westus` | USA – západ<br>`westus`  |  [www.LUIS.ai][www.luis.ai] | https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Severní Amerika | USA – západ<br>`westus` | USA – západ 2<br>`westus2`    | [www.LUIS.ai][www.luis.ai] |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Jižní Amerika | USA – západ<br>`westus` | Brazílie – jih<br>`brazilsouth`     | [www.LUIS.ai][www.luis.ai] |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-europe"></a>Publikování v Evropě

Chcete-li publikovat do všech evropských oblastech, vytvoříte LUIS aplikace na https://eu.luis.ai pouze. Při pokusu publikovat kdekoli jinde pomocí klíče v oblasti Evropa, LUIS se zobrazí zpráva s upozorněním. Místo toho použijte https://eu.luis.ai. Služba LUIS aplikací vytvořených v [ https://eu.luis.ai ] [ eu.luis.ai] neproběhne Automatická migrace do jiných oblastí. Exportovat a importovat aplikaci LUIS k její migraci.

## <a name="publishing-to-australia"></a>Publikování do Austrálie

Chcete-li publikovat do různých oblastí, vytvoříte LUIS aplikace na https://au.luis.ai pouze. Při pokusu publikovat kdekoli jinde pomocí klíče v oblasti Austrálie, LUIS se zobrazí zpráva s upozorněním. Místo toho použijte https://au.luis.ai. Služba LUIS aplikací vytvořených v [ https://au.luis.ai ] [ au.luis.ai] neproběhne Automatická migrace do jiných oblastí. Exportovat a importovat aplikaci LUIS k její migraci.

## <a name="endpoints"></a>Koncové body

Služba LUIS má v současné době 2 koncové body: jeden pro vytváření obsahu a jeden pro analýzu textu.

|Účel|zprostředkovatele identity|
|--|--|
|Vytváření obsahu|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Analýza textu (dotaz předpovědi)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

Následující tabulka popisuje parametry, které jsou označeny složených závorek `{}`, v předchozí tabulce.

|Parametr|Účel|
|--|--|
|oblast|Oblasti Azure – pro vytváření a publikování mají různé oblasti|
|ID aplikace|ID aplikace LUIS používaných pro trasu adresy URL a najít na řídicí panel aplikací|
|q|text utterance odeslané z klientské aplikace, jako je chatovací robot|


## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Odkaz na předem připravených entit](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai