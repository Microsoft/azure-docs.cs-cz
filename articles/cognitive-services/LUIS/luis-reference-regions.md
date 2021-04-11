---
title: Publikování oblastí & koncových bodů – LUIS
description: Oblast zadaná v Azure Portal je stejná, kde budete publikovat aplikaci LUIS a adresa URL koncového bodu se vygeneruje pro tuto oblast.
ms.service: cognitive-services
ms.subservice: language-understanding
author: aahill
ms.author: aahi
ms.topic: reference
ms.date: 04/07/2021
ms.custom: references_regions
ms.openlocfilehash: fd02bf5c3291569b71416392b651967e0da701d9
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226663"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Vytváření a publikování oblastí a přidružených klíčů

LUIS oblasti vytváření obsahu jsou podporovány portálem LUIS. Pokud chcete publikovat aplikaci LUIS ve více než jedné oblasti, potřebujete alespoň jeden klíč na oblast.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>Oblasti vytváření LUIS

[!INCLUDE [portal consolidation](includes/portal-consolidation.md)]

LUIS má jeden portál, který můžete použít bez ohledu na oblast [www.Luis.AI](https://www.luis.ai). Pořád musíte vytvářet a publikovat ve stejné oblasti.

Oblasti vytváření obsahu mají [spárované oblasti převzetí služeb při selhání](../../best-practices-availability-paired-regions.md) .

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publikování oblastí a prostředků Azure

Aplikace se publikuje ve všech oblastech přidružených k prostředkům LUIS přidaným na portálu LUIS. Například pro aplikaci vytvořenou v [www.Luis.AI][www.luis.ai], pokud vytvoříte prostředek Luis nebo vnímání služby v **westus** a [přidáte ho do aplikace jako prostředek](luis-how-to-azure-subscription.md), aplikace se publikuje v této oblasti.

## <a name="public-apps"></a>Veřejné aplikace
Veřejná aplikace je publikovaná ve všech oblastech, aby uživatel s klíčem prostředků LUIS založeným na oblasti měl přístup k aplikaci v jakékoli oblasti, která je přidružená ke svému klíči prostředků.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Oblasti publikování jsou vázané na oblasti vytváření obsahu

Aplikaci oblasti vytváření obsahu je možné publikovat pouze do odpovídající oblasti pro publikování. Pokud je vaše aplikace momentálně v nesprávné oblasti vytváření, exportujte ji a importujte ji do správné oblasti vytváření pro vaši oblast publikování.

> [!NOTE]
> Aplikace LUIS vytvořené v se https://www.luis.ai teď dají publikovat do všech koncových bodů, včetně oblastí [Evropské](#publishing-to-europe) a [australské](#publishing-to-australia) oblasti.

## <a name="publishing-to-europe"></a>Publikování do Evropy

 Globální oblast | Oblast rozhraní API pro vytváření obsahu | Publikování & oblasti dotazování<br>`API region name`   |  Formát adresy URL koncového bodu   |
|-----|------|------|------|
| Evropa | `westeurope`| Francie – střed<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Evropa | `westeurope`| Severní Evropa<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Evropa | `westeurope`| West Europe<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Evropa | `westeurope`| Spojené království – jih<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Evropa | `westeurope`| Švýcarsko – sever<br>`switzerlandnorth`    |  `https://switzerlandnorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Publikování do Austrálie

 Globální oblast | Oblast rozhraní API pro vytváření obsahu | Publikování & oblasti dotazování<br>`API region name`   |  Formát adresy URL koncového bodu   |
|-----|------|------|------|
| Austrálie | `australiaeast` | Austrálie – východ<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="other-publishing-regions"></a>Jiné oblasti publikování

 Globální oblast | Oblast rozhraní API pro vytváření obsahu | Publikování & oblasti dotazování<br>`API region name`   |  Formát adresy URL koncového bodu   |
|-----|------|------|------|
| Afrika | `westus`<br>[www.luis.ai][www.luis.ai]| Jižní Afrika – sever<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Indie – střed<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Východní Asie<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Japan East<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Japonsko – západ<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Jižní Korea – střed<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Southeast Asia<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Sever Spojené arabské emiráty<br>`northuae`     |   `https://northuae.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Střední Kanada<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA – střed<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | East US<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA – východ 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA – středosever<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Středojižní USA<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA – středozápad<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA – západ<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Západní USA 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Jižní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Brazílie – jih<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |

## <a name="endpoints"></a>Koncové body

Přečtěte si další informace o [koncových bodech vytváření a předpovědi](developer-reference-resource.md).

## <a name="failover-regions"></a>Oblasti převzetí služeb při selhání

Každá oblast má sekundární oblast pro převzetí služeb při selhání. Evropa převezme služeb při selhání uvnitř Evropy a Austrálie převezme přes Austrálii.

Oblasti vytváření obsahu mají [spárované oblasti převzetí služeb při selhání](../../best-practices-availability-paired-regions.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference předem sestavených entit](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai