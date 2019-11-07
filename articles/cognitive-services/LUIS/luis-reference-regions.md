---
title: Publikování oblastí & koncových bodů – LUIS
titleSuffix: Azure Cognitive Services
description: Oblast zadaná v Azure Portal je stejná, kde budete publikovat aplikaci LUIS a adresa URL koncového bodu se vygeneruje pro tuto oblast.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: diberry
ms.openlocfilehash: fe4436afe56e631ae308b70ae35b1fb4a7c32e11
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669184"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Vytváření a publikování oblastí a přidružených klíčů

Příslušné portály LUIS podporují tři oblasti vytváření obsahu. K publikování aplikace LUIS do více než jedné oblasti potřebujete alespoň jeden klíč na oblast. 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>Oblasti vytváření LUIS
Existují tři portály pro vytváření LUIS na základě oblasti. Musíte vytvořit a publikovat ve stejné oblasti. 

|LUIS|Oblast vytváření|Název oblasti Azure|
|--|--|--|
|[www.luis.ai][www.luis.ai]|Menší odlehlé ostrovy<br>neevropa<br>neaustrálie| `westus`|
|[au.luis.ai][au.luis.ai]|Austrálie| `australiaeast`|
|[eu.luis.ai][eu.luis.ai]|Evropa|`westeurope`|

Oblasti vytváření obsahu mají [spárované oblasti převzetí služeb při selhání](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publikování oblastí a prostředků Azure
Aplikace je publikovaná ve všech oblastech přidružených k prostředkům LUIS přidaným na portálu LUIS. Například pro aplikaci vytvořenou v [www.Luis.AI][www.luis.ai], pokud vytvoříte prostředek Luis nebo vnímání služby v **westus** a [přidáte ho do aplikace jako prostředek](luis-how-to-azure-subscription.md), aplikace se publikuje v této oblasti. 

## <a name="public-apps"></a>Veřejné aplikace
Veřejná aplikace je publikovaná ve všech oblastech, aby uživatel s klíčem prostředků LUIS založeným na oblasti měl přístup k aplikaci v jakékoli oblasti, která je přidružená ke svému klíči prostředků.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Oblasti publikování jsou vázané na oblasti vytváření obsahu

Aplikaci oblasti vytváření obsahu je možné publikovat pouze do odpovídající oblasti pro publikování. Pokud je vaše aplikace momentálně v nesprávné oblasti vytváření obsahu, exportujte ji a naimportujte ji do správné oblasti vytváření obsahu pro vaši oblast publikování. 

Aplikace LUIS vytvořené v https://www.luis.ai můžete publikovat do všech koncových bodů s výjimkou [Evropské](#publishing-to-europe) a [australské](#publishing-to-australia) oblasti. 

## <a name="publishing-to-europe"></a>Publikování do Evropy

K publikování v rámci evropských oblastí vytváříte aplikace LUIS jenom na https://eu.luis.ai. Pokud se pokusíte o publikování odkudkoli jinde pomocí klíče v oblasti Evropa, LUIS zobrazí zprávu s upozorněním. Místo toho použijte https://eu.luis.ai. Aplikace LUIS vytvořené v [https://eu.luis.ai][eu.luis.ai] nemigrují automaticky do jiných oblastí. Exportujte a pak importujte aplikaci LUIS, aby ji bylo možné migrovat.

## <a name="europe-publishing-regions"></a>Oblasti publikování v Evropě

 Globální oblast | Vytváření obsahu oblasti rozhraní API & tvorbě webu| Publikování & oblasti dotazování<br>`API region name`   |  Formát adresy URL koncového bodu   |
|-----|------|------|------|
| [Evropa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Francie – střed<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Evropa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Severní Evropa<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Evropa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Západní Evropa<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Evropa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Spojené království – jih<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>Publikování do Austrálie

K publikování do australských oblastí můžete vytvářet aplikace LUIS jenom na https://au.luis.ai. Pokud se pokusíte publikovat odkudkoli jinde pomocí klíče v australské oblasti Austrálie, LUIS zobrazí zprávu s upozorněním. Místo toho použijte https://au.luis.ai. Aplikace LUIS vytvořené v [https://au.luis.ai][au.luis.ai] nemigrují automaticky do jiných oblastí. Exportujte a pak importujte aplikaci LUIS, aby ji bylo možné migrovat.

## <a name="australia-publishing-regions"></a>Oblasti publikování v Austrálii

 Globální oblast | Vytváření obsahu oblasti rozhraní API & tvorbě webu| Publikování & oblasti dotazování<br>`API region name`   |  Formát adresy URL koncového bodu   |
|-----|------|------|------|
| [Austrálie](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Austrálie – východ<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Publikování do jiných oblastí

K publikování v ostatních oblastech vytvoříte aplikace LUIS jenom na [https://www.luis.ai](https://www.luis.ai) . 

## <a name="other-publishing-regions"></a>Jiné oblasti publikování

 Globální oblast | Vytváření obsahu oblasti rozhraní API & tvorbě webu| Publikování & oblasti dotazování<br>`API region name`   |  Formát adresy URL koncového bodu   |
|-----|------|------|------|
| Afrika | `westus`<br>[www.luis.ai][www.luis.ai]| Jižní Afrika – sever<br>`southafricanorth` |  https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Střed Indie<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Východní Asie<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Japonsko – východ<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Japonsko – západ<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Jižní Korea – střed<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Jihovýchodní Asie<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Střední Kanada<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Střední USA<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Východ USA<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Východ USA 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Středoseverní USA<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Středojižní USA<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Středozápadní USA<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Západní USA<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Západní USA 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Jižní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Brazílie – jih<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>Koncové body

LUIS má aktuálně 2 koncové body: jeden pro vytváření obsahu a jeden pro analýzu prediktivních dotazů.

|Účel|zprostředkovatele identity|
|--|--|
|Vytváření obsahu|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Analýza textu (prediktivní dotazování)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

Následující tabulka popisuje parametry, které jsou označeny složenými závorkami `{}`v předchozí tabulce.

|Parametr|Účel|
|--|--|
|.|Oblast Azure – vytváření a publikování mají různé oblasti|
|Identifikátor|ID aplikace LUIS použité v trase URL a Nalezeno na řídicím panelu aplikace|
|č|utterance text odeslaný z klientské aplikace, jako je třeba robota chatu|

## <a name="failover-regions"></a>Oblasti převzetí služeb při selhání

Každá oblast má sekundární oblast pro převzetí služeb při selhání. Evropa převezme služeb při selhání uvnitř Evropy a Austrálie převezme přes Austrálii.

Oblasti vytváření obsahu mají [spárované oblasti převzetí služeb při selhání](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference předem sestavených entit](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
