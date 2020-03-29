---
title: Publikování oblastí & koncových bodů – LUIS
description: Oblast zadaná na portálu Azure je stejná, kde publikujete aplikaci LUIS a pro stejnou oblast se vygeneruje adresa URL koncového bodu.
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: a7709d15a51637772d7a4775cd71c307dc21c52b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292090"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Vytváření a publikování oblastí a přidružených klíčů

Tři oblasti vytváření jsou podporovány odpovídajícími portály LUIS. Chcete-li publikovat aplikaci LUIS do více než jedné oblasti, potřebujete alespoň jeden klíč pro oblast.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>Oblasti vytváření služby LUIS
Existují tři portály pro vytváření LUIS založené na oblasti. Vytvářet a publikovat je nutné ve stejné oblasti.

|LUIS|Vývojová oblast|Název oblasti Azure|
|--|--|--|
|[www.luis.ai][www.luis.ai] <br>[preview.luis.ai](https://preview.luis.ai)|USA:<br>ne evropa<br>ne Austrálie| `westus`|
|[au.luis.ai][au.luis.ai] <br>[preview.au.luis.ai](https://preview.au.luis.ai)|Austrálie| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] <br>[preview.eu.luis.ai](https://preview.eu.luis.ai)|Evropa|`westeurope`|

Oblasti vytváření mají [spárované oblasti převzetí služeb při selhání](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publikování oblastí a prostředků Azure
Aplikace se publikuje do všech oblastí přidružených k prostředkům LUIS přidaným na portálu LUIS. Například pro aplikaci vytvořenou na [www.luis.ai][www.luis.ai], pokud vytvoříte prostředek LUIS nebo cognitive service v **aplikaci westus** a [přidáte ho do aplikace jako prostředek](luis-how-to-azure-subscription.md), aplikace se publikuje v této oblasti.

## <a name="public-apps"></a>Veřejné aplikace
Veřejná aplikace se publikuje ve všech oblastech, takže uživatel s klíčem prostředku LUIS založené na oblasti má přístup k aplikaci v kterékoli oblasti, která je přidružena k jejich klíči prostředků.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Oblasti publikování jsou vázány na oblasti vytváření.

Aplikaci oblasti vytváření lze publikovat pouze do odpovídající oblasti publikování. Pokud je vaše aplikace momentálně v nesprávné oblasti vytváření, exportujte ji a importujte ji do správné oblasti vytváření pro oblast publikování.

Aplikace LUIS https://www.luis.ai vytvořené na můžete publikovat do všech koncových bodů s výjimkou [evropské](#publishing-to-europe) a [australské](#publishing-to-australia) oblasti.

## <a name="publishing-to-europe"></a>Publikování do Evropy

Chcete-li publikovat do evropských oblastí, můžete vytvořit aplikace LUIS pouze na. https://eu.luis.ai Pokud se pokusíte publikovat kdekoli jinde pomocí klíče v oblasti Evropa, LUIS zobrazí varovnou zprávu. Místo toho https://eu.luis.aipoužijte . Aplikace LUIS [https://eu.luis.ai][eu.luis.ai] vytvořené v aplikaci se automaticky nemigrují do jiných oblastí. Exportujte a pak importujte aplikaci LUIS, abyste ji mohli migrovat.

## <a name="europe-publishing-regions"></a>Evropa publikuje regiony

 Globální oblast | Vývoj oblasti rozhraní API & webu pro vytváření| Publikování oblasti dotazování &<br>`API region name`   |  Formát adresy URL koncového bodu   |
|-----|------|------|------|
| [Evropa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Francie – střed<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Evropa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Severní Evropa<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Evropa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Západní Evropa<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Evropa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Spojené království – jih<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Publikování do Austrálie

Chcete-li publikovat do australských oblastí, můžete vytvořit aplikace LUIS pouze na. https://au.luis.ai Pokud se pokusíte publikovat kdekoli jinde pomocí klíče v oblasti Austrálie, LUIS zobrazí varovnou zprávu. Místo toho https://au.luis.aipoužijte . Aplikace LUIS [https://au.luis.ai][au.luis.ai] vytvořené v aplikaci se automaticky nemigrují do jiných oblastí. Exportujte a pak importujte aplikaci LUIS, abyste ji mohli migrovat.

## <a name="australia-publishing-regions"></a>Austrálie publikování regiony

 Globální oblast | Vývoj oblasti rozhraní API & webu pro vytváření| Publikování oblasti dotazování &<br>`API region name`   |  Formát adresy URL koncového bodu   |
|-----|------|------|------|
| [Austrálie](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Austrálie – východ<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-other-regions"></a>Publikování do jiných oblastí

Chcete-li publikovat do jiných oblastí, [https://www.luis.ai](https://www.luis.ai) můžete vytvořit aplikace LUIS pouze na.

## <a name="other-publishing-regions"></a>Ostatní publikační regiony

 Globální oblast | Vývoj oblasti rozhraní API & webu pro vytváření| Publikování oblasti dotazování &<br>`API region name`   |  Formát adresy URL koncového bodu   |
|-----|------|------|------|
| Afrika | `westus`<br>[www.luis.ai][www.luis.ai]| Jižní Afrika – sever<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Indie – střed<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Východní Asie<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Japonsko – východ<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Japonsko – západ<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Jižní Korea – střed<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Jihovýchodní Asie<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Střední Kanada<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA – střed<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA – východ<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA – východ 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA – středosever<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA – středojih<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA – středozápad<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA – západ<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA – západ 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| Jižní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Brazílie – jih<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="endpoints"></a>Koncové body

Další informace o [koncových bodech pro vytváření a předpověď](developer-reference-resource.md).

## <a name="failover-regions"></a>Oblasti převzetí služeb při selhání

Každá oblast má sekundární oblast převzetí služeb při selhání. Evropa selhává uvnitř Evropy a Austrálie se zklame uvnitř Austrálie.

Oblasti vytváření mají [spárované oblasti převzetí služeb při selhání](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Odkaz na předem sestavené entity](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
