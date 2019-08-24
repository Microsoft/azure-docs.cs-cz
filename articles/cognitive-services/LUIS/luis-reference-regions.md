---
title: Publikování oblastí & koncových bodů – LUIS
titleSuffix: Azure Cognitive Services
description: 3 oblasti vytváření obsahu a jejich portály podporují celou řadu oblastí publikování. Oblast, ve kterém můžete publikovat aplikaci LUIS odpovídá oblast nebo umístění, ve kterém zadáte na webu Azure Portal, při vytváření klíče koncového bodu Azure LUIS. Při publikování aplikace LUIS automaticky generuje adresu URL koncového bodu pro oblast spojené s klíči.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: diberry
ms.openlocfilehash: f859047caf64d27ef63783ecd42f12fc9b79b4fd
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933348"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Vytváření a publikování oblastí a přidružených klíčů

Příslušné portály LUIS podporují tři oblasti vytváření obsahu. K publikování aplikace LUIS do více než jedné oblasti, budete potřebovat alespoň jeden klíč v jedné oblasti. 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>Oblasti vytváření LUIS
Existují tři portály pro vytváření LUIS na základě oblasti. Musí se vytvářet a publikovat ve stejné oblasti. 

|LUIS|Oblast pro tvorbu|Název oblasti Azure|
|--|--|--|
|[www.luis.ai][www.luis.ai]|USA<br>není Evropa<br>není Austrálie| `westus`|
|[au.luis.ai][au.luis.ai]|Austrálie| `australiaeast`|
|[eu.luis.ai][eu.luis.ai]|Evropa|`westeurope`|

Oblasti vytváření obsahu mají [spárované oblasti převzetí služeb při selhání](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publikování oblastí a prostředků Azure
Publikování aplikace do všech oblastí, které jsou spojené s prostředky služby LUIS přidat na portálu služby LUIS. Například pro aplikaci vytvořenou v [www.Luis.AI][www.luis.ai], pokud vytvoříte prostředek Luis nebo vnímání služby v **westus** a [přidáte ho do aplikace jako prostředek](luis-how-to-azure-subscription.md), aplikace se publikuje v této oblasti. 

## <a name="public-apps"></a>Veřejné aplikace
Aplikace z veřejného app je publikována ve všech oblastech, tak, aby uživatel s klíčem služby LUIS prostředků na základě oblasti můžete přistupovat k aplikaci v oblasti podle toho, která souvisí s jejich klíč prostředku.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Oblasti publikování jsou vázané na oblasti vytváření obsahu

Pro vytváření oblasti aplikace lze publikovat pouze na odpovídající publikovat oblasti. Pokud vaše aplikace je aktuálně v nesprávné oblasti pro vytváření, export aplikace a importujte ho do správné zdrojové oblasti pro publikování oblast. 

LUIS aplikací vytvořených v https://www.luis.ai lze publikovat na všechny koncové body s výjimkou [Evropské](#publishing-to-europe) a [australské](#publishing-to-australia) oblastech. 

## <a name="publishing-to-europe"></a>Publikování v Evropě

Chcete-li publikovat do všech evropských oblastech, vytvoříte LUIS aplikace na https://eu.luis.ai pouze. Při pokusu publikovat kdekoli jinde pomocí klíče v oblasti Evropa, LUIS se zobrazí zpráva s upozorněním. Místo toho použijte https://eu.luis.ai. Aplikace Luis vytvořené v [https://eu.luis.ai][eu.luis.ai] tuto době nemigrují automaticky do jiných oblastí. Exportovat a importovat aplikaci LUIS k její migraci.

## <a name="europe-publishing-regions"></a>Oblasti publikování v Evropě

 Globální oblast | Vytváření obsahu oblasti rozhraní API & tvorbě webu| Publikování a dotazování na oblast<br>`API region name`   |  Formát adresy URL koncového bodu   |
|-----|------|------|------|
| [Evropa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Francie – střed<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Evropa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Severní Evropa<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Evropa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Západní Evropa<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Evropa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Velká Británie – jih<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>Publikování do Austrálie

Chcete-li publikovat do různých oblastí, vytvoříte LUIS aplikace na https://au.luis.ai pouze. Při pokusu publikovat kdekoli jinde pomocí klíče v oblasti Austrálie, LUIS se zobrazí zpráva s upozorněním. Místo toho použijte https://au.luis.ai. Aplikace Luis vytvořené v [https://au.luis.ai][au.luis.ai] tuto době nemigrují automaticky do jiných oblastí. Exportovat a importovat aplikaci LUIS k její migraci.

## <a name="australia-publishing-regions"></a>Oblasti publikování v Austrálii

 Globální oblast | Vytváření obsahu oblasti rozhraní API & tvorbě webu| Publikování a dotazování na oblast<br>`API region name`   |  Formát adresy URL koncového bodu   |
|-----|------|------|------|
| [Austrálie](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Austrálie – východ<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Publikování do jiných oblastí

K publikování v ostatních oblastech vytvoříte aplikace Luis jenom na [https://www.luis.ai](https://www.luis.ai) . 

## <a name="other-publishing-regions"></a>Jiné oblasti publikování

 Globální oblast | Vytváření obsahu oblasti rozhraní API & tvorbě webu| Publikování a dotazování na oblast<br>`API region name`   |  Formát adresy URL koncového bodu   |
|-----|------|------|------|
| Afrika | `westus`<br>[www.luis.ai][www.luis.ai]| Jižní Afrika – sever<br>`southafricanorth` |  https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Střed Indie<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Východní Asie<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Japonsko – východ<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Japonsko – západ<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Jižní Korea – střed<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Jihovýchodní Asie<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Kanada – střed<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Střední USA<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | East US<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Východní USA 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Středoseverní USA<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Středojižní USA<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Středozápadní USA<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA – západ<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA – západ 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Jižní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Brazílie – jih<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>Koncové body

LUIS má aktuálně 2 koncové body: jeden pro vytváření obsahu a jeden pro analýzu prediktivních dotazů.

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

## <a name="failover-regions"></a>Oblasti převzetí služeb při selhání

Každá oblast má sekundární oblast pro převzetí služeb při selhání. Evropa převezme služeb při selhání uvnitř Evropy a Austrálie převezme přes Austrálii.

Oblasti vytváření obsahu mají [spárované oblasti převzetí služeb při selhání](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Odkaz na předem připravených entit](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
