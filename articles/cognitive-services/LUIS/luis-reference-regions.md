---
title: Publikování oblastí & koncových bodů – LUIS
description: Oblast zadaná v Azure Portal je stejná, kde budete publikovat aplikaci LUIS a adresa URL koncového bodu se vygeneruje pro tuto oblast.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: f7c6c3f9c3f468c7b55baf2b0cc45c094d693de5
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018646"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Vytváření a publikování oblastí a přidružených klíčů

Příslušné portály LUIS podporují tři oblasti vytváření obsahu. Pokud chcete publikovat aplikaci LUIS ve více než jedné oblasti, potřebujete alespoň jeden klíč na oblast.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>Oblasti vytváření LUIS
Existují tři portály pro vytváření LUIS na základě oblasti. Vytvářet a publikovat je nutné ve stejné oblasti.

|LUIS|Oblast vytváření|Název oblasti Azure|
|--|--|--|
|[www.luis.ai][www.luis.ai] |USA:<br>neevropa<br>neaustrálie| `westus`|
|[au.luis.ai][au.luis.ai] |Austrálie| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] |Evropa|`westeurope`|

Oblasti vytváření obsahu mají [spárované oblasti převzetí služeb při selhání](../../best-practices-availability-paired-regions.md).

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publikování oblastí a prostředků Azure
Aplikace se publikuje ve všech oblastech přidružených k prostředkům LUIS přidaným na portálu LUIS. Například pro aplikaci vytvořenou v [www.Luis.AI][www.luis.ai], pokud vytvoříte prostředek Luis nebo vnímání služby v **westus** a [přidáte ho do aplikace jako prostředek](luis-how-to-azure-subscription.md), aplikace se publikuje v této oblasti.

## <a name="public-apps"></a>Veřejné aplikace
Veřejná aplikace je publikovaná ve všech oblastech, aby uživatel s klíčem prostředků LUIS založeným na oblasti měl přístup k aplikaci v jakékoli oblasti, která je přidružená ke svému klíči prostředků.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Oblasti publikování jsou vázané na oblasti vytváření obsahu

Aplikaci oblasti vytváření obsahu je možné publikovat pouze do odpovídající oblasti pro publikování. Pokud je vaše aplikace momentálně v nesprávné oblasti vytváření, exportujte ji a importujte ji do správné oblasti vytváření pro vaši oblast publikování.

Aplikace LUIS vytvořené na https://www.luis.ai je možné publikovat do všech koncových bodů s výjimkou [Evropské](#publishing-to-europe) a [australské](#publishing-to-australia) oblasti.

## <a name="publishing-to-europe"></a>Publikování do Evropy

K publikování v rámci evropských oblastí vytvoříte jenom aplikace LUIS https://eu.luis.ai . Pokud se pokusíte o publikování odkudkoli jinde pomocí klíče v oblasti Evropa, LUIS zobrazí zprávu s upozorněním. Místo toho použijte https://eu.luis.ai . Aplikace LUIS vytvořené v tuto době [https://eu.luis.ai][eu.luis.ai] nemigrují automaticky do jiných oblastí. Exportujte a pak importujte aplikaci LUIS, aby ji bylo možné migrovat.

## <a name="europe-publishing-regions"></a>Oblasti publikování v Evropě

 Globální oblast | Vytváření obsahu oblasti rozhraní API & tvorbě webu| Publikování & oblasti dotazování<br>`API region name`   |  Formát adresy URL koncového bodu   |
|-----|------|------|------|
| [Evropa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Francie – střed<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Evropa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Severní Evropa<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Evropa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| West Europe<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Evropa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Spojené království – jih<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Publikování do Austrálie

K publikování do australských oblastí vytvoříte jenom aplikace LUIS https://au.luis.ai . Pokud se pokusíte publikovat odkudkoli jinde pomocí klíče v australské oblasti Austrálie, LUIS zobrazí zprávu s upozorněním. Místo toho použijte https://au.luis.ai . Aplikace LUIS vytvořené v tuto době [https://au.luis.ai][au.luis.ai] nemigrují automaticky do jiných oblastí. Exportujte a pak importujte aplikaci LUIS, aby ji bylo možné migrovat.

## <a name="australia-publishing-regions"></a>Oblasti publikování v Austrálii

 Globální oblast | Vytváření obsahu oblasti rozhraní API & tvorbě webu| Publikování & oblasti dotazování<br>`API region name`   |  Formát adresy URL koncového bodu   |
|-----|------|------|------|
| [Austrálie](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Austrálie – východ<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-other-regions"></a>Publikování do jiných oblastí

K publikování v ostatních oblastech vytvoříte aplikace LUIS [https://www.luis.ai](https://www.luis.ai) jenom na.

## <a name="other-publishing-regions"></a>Jiné oblasti publikování

 Globální oblast | Vytváření obsahu oblasti rozhraní API & tvorbě webu| Publikování & oblasti dotazování<br>`API region name`   |  Formát adresy URL koncového bodu   |
|-----|------|------|------|
| Afrika | `westus`<br>[www.luis.ai][www.luis.ai]| Jižní Afrika – sever<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Indie – střed<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Východní Asie<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Japan East<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Japonsko – západ<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Jižní Korea – střed<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Southeast Asia<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Střední Kanada<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Střední USA<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | East US<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA – východ 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA – středosever<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Středojižní USA<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA – středozápad<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA – západ<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Západní USA 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| Jižní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Brazil South<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="endpoints"></a>Koncové body

Přečtěte si další informace o [koncových bodech vytváření a předpovědi](developer-reference-resource.md).

## <a name="failover-regions"></a>Oblasti převzetí služeb při selhání

Každá oblast má sekundární oblast pro převzetí služeb při selhání. Evropa převezme služeb při selhání uvnitř Evropy a Austrálie převezme přes Austrálii.

Oblasti vytváření obsahu mají [spárované oblasti převzetí služeb při selhání](../../best-practices-availability-paired-regions.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference předem sestavených entit](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai