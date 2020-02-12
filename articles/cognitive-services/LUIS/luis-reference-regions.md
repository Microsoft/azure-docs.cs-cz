---
title: Publikování oblastí & koncových bodů – LUIS
description: Oblast zadaná v Azure Portal je stejná, kde budete publikovat aplikaci LUIS a adresa URL koncového bodu se vygeneruje pro tuto oblast.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 77fd901790582983d75735ac8b66d59c741ced9e
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77151934"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Vytváření a publikování oblastí a přidružených klíčů

Příslušné portály LUIS podporují tři oblasti vytváření obsahu. K publikování aplikace LUIS do více než jedné oblasti, budete potřebovat alespoň jeden klíč v jedné oblasti.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>Oblasti vytváření LUIS
Existují tři portály pro vytváření LUIS na základě oblasti. Musí se vytvářet a publikovat ve stejné oblasti.

|LUIS|Oblast pro tvorbu|Název oblasti Azure|
|--|--|--|
|[www.luis.ai][www.luis.ai] <br>[preview.luis.ai](https://preview.luis.ai)|USA<br>není Evropa<br>není Austrálie| `westus`|
|[au.luis.ai][au.luis.ai] <br>[preview.au.luis.ai](https://preview.au.luis.ai)|Austrálie| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] <br>[preview.eu.luis.ai](https://preview.eu.luis.ai)|Evropa|`westeurope`|

Oblasti vytváření obsahu mají [spárované oblasti převzetí služeb při selhání](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publikování oblastí a prostředků Azure
Publikování aplikace do všech oblastí, které jsou spojené s prostředky služby LUIS přidat na portálu služby LUIS. Například pro aplikaci vytvořenou v [www.Luis.AI][www.luis.ai], pokud vytvoříte prostředek Luis nebo vnímání služby v **westus** a [přidáte ho do aplikace jako prostředek](luis-how-to-azure-subscription.md), aplikace se publikuje v této oblasti.

## <a name="public-apps"></a>Veřejné aplikace
Aplikace z veřejného app je publikována ve všech oblastech, tak, aby uživatel s klíčem služby LUIS prostředků na základě oblasti můžete přistupovat k aplikaci v oblasti podle toho, která souvisí s jejich klíč prostředku.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Oblasti publikování jsou vázané na oblasti vytváření obsahu

Pro vytváření oblasti aplikace lze publikovat pouze na odpovídající publikovat oblasti. Pokud vaše aplikace je aktuálně v nesprávné oblasti pro vytváření, export aplikace a importujte ho do správné zdrojové oblasti pro publikování oblast.

Aplikace LUIS vytvořené v https://www.luis.ai můžete publikovat do všech koncových bodů s výjimkou [Evropské](#publishing-to-europe) a [australské](#publishing-to-australia) oblasti.

## <a name="publishing-to-europe"></a>Publikování v Evropě

K publikování v rámci evropských oblastí vytváříte aplikace LUIS jenom na https://eu.luis.ai. Při pokusu publikovat kdekoli jinde pomocí klíče v oblasti Evropa, LUIS se zobrazí zpráva s upozorněním. Použijte místo toho https://eu.luis.ai. Aplikace LUIS vytvořené v [https://eu.luis.ai][eu.luis.ai] nemigrují automaticky do jiných oblastí. Exportovat a importovat aplikaci LUIS k její migraci.

## <a name="europe-publishing-regions"></a>Oblasti publikování v Evropě

 Globální oblast | Vytváření obsahu oblasti rozhraní API & tvorbě webu| Publikování a dotazování na oblast<br>`API region name`   |  Formát adresy URL koncového bodu   |
|-----|------|------|------|
| [Evropa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Francie – střed<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| [Evropa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Severní Evropa<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| [Evropa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Západní Evropa<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| [Evropa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Velká Británie – jih<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>Publikování do Austrálie

K publikování do australských oblastí můžete vytvářet aplikace LUIS jenom na https://au.luis.ai. Při pokusu publikovat kdekoli jinde pomocí klíče v oblasti Austrálie, LUIS se zobrazí zpráva s upozorněním. Použijte místo toho https://au.luis.ai. Aplikace LUIS vytvořené v [https://au.luis.ai][au.luis.ai] nemigrují automaticky do jiných oblastí. Exportovat a importovat aplikaci LUIS k její migraci.

## <a name="australia-publishing-regions"></a>Oblasti publikování v Austrálii

 Globální oblast | Vytváření obsahu oblasti rozhraní API & tvorbě webu| Publikování a dotazování na oblast<br>`API region name`   |  Formát adresy URL koncového bodu   |
|-----|------|------|------|
| [Austrálie](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Austrálie – východ<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Publikování do jiných oblastí

K publikování v ostatních oblastech vytvoříte aplikace LUIS jenom na [https://www.luis.ai](https://www.luis.ai) .

## <a name="other-publishing-regions"></a>Jiné oblasti publikování

 Globální oblast | Vytváření obsahu oblasti rozhraní API & tvorbě webu| Publikování a dotazování na oblast<br>`API region name`   |  Formát adresy URL koncového bodu   |
|-----|------|------|------|
| Poskytl | `westus`<br>[www.luis.ai][www.luis.ai]| Jižní Afrika – sever<br>`southafricanorth` |  https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Střed Indie<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Východní Asie<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Japonsko – východ<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Japonsko – západ<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Jižní Korea – střed<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Jihovýchodní Asie<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Kanada – střed<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Střední USA<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA – východ<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Východní USA 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Středoseverní USA<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Středojižní USA<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Středozápadní USA<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Západní USA<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Severní Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Západní USA 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Jižní Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Brazílie – jih<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>Koncové body

Přečtěte si další informace o [koncových bodech vytváření a předpovědi](developer-reference-resource.md).

## <a name="failover-regions"></a>Oblasti převzetí služeb při selhání

Každá oblast má sekundární oblast pro převzetí služeb při selhání. Evropa převezme služeb při selhání uvnitř Evropy a Austrálie převezme přes Austrálii.

Oblasti vytváření obsahu mají [spárované oblasti převzetí služeb při selhání](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference předem sestavených entit](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
