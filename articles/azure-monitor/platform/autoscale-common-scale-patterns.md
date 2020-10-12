---
title: Přehled obecných vzorů automatického škálování
description: Seznamte se s některými běžnými vzory pro automatické škálování prostředků v Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: adbfecd05bfd4330e7c7c972cfdb9fe7021eec31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87073505"
---
# <a name="overview-of-common-autoscale-patterns"></a>Přehled obecných vzorů automatického škálování
Tento článek popisuje některé běžné vzory škálování prostředků v Azure.

Automatické škálování Azure Monitor platí jenom pro služby [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)a [API Management](../../api-management/api-management-key-concepts.md).

## <a name="lets-get-started"></a>Umožňuje začít

V tomto článku se předpokládá, že máte zkušenosti s automatickým škálováním. Můžete začít [škálovat svůj prostředek][1]. Níže jsou uvedeny některé běžné vzory škálování.

## <a name="scale-based-on-cpu"></a>Škálování na základě procesoru

Máte webovou aplikaci (role služby/VMSS/cloud) a

- Pro horizontální navýšení kapacity můžete škálovat v závislosti na procesoru.
- Kromě toho je potřeba zajistit minimální počet instancí.
- Také je potřeba zajistit, aby bylo nastaveno maximální omezení počtu instancí, na které můžete škálovat.

![Škálování na základě procesoru][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Jiné škálování v pracovních dnech a víkendech

Máte webovou aplikaci (role služby/VMSS/cloud) a

- Ve výchozím nastavení potřebujete 3 instance (v pracovních dnech).
- Neočekáváte provoz na víkendech, takže chcete škálovat dolů na 1 instanci na víkendech.

![Jiné škálování v pracovních dnech a víkendech][3]

## <a name="scale-differently-during-holidays"></a>Škálování odlišně během svátků

Máte webovou aplikaci (role služby/VMSS/cloud) a

- Chcete škálovat nahoru/dolů na základě využití procesoru ve výchozím nastavení.
- Během období svátků (nebo konkrétních dnů, které jsou důležité pro vaši firmu) ale chcete přepsat výchozí nastavení a mít větší kapacitu při vyřazení.

![Odlišná škála při dovolené][4]

## <a name="scale-based-on-custom-metric"></a>Škálování na základě vlastní metriky

Máte webový front-end a vrstvu rozhraní API, která komunikuje s back-endu.

- Chcete škálovat vrstvu rozhraní API na základě vlastních událostí v front-endu (příklad: chcete škálovat proces rezervace na základě počtu položek v nákupním košíku).

![Škálování na základě vlastní metriky][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png
