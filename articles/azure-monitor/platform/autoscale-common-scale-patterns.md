---
title: Přehled obecných vzorů automatického škálování
description: Seznamte se s některými běžnými vzory automatického škálování prostředků v Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: a77cf1704c20abb77d432eab16569071208f6da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75396096"
---
# <a name="overview-of-common-autoscale-patterns"></a>Přehled obecných vzorů automatického škálování
Tento článek popisuje některé běžné vzory škálování prostředků v Azure.

Automatické škálování Azure Monitor uplatní jenom [pro škálovací sady virtuálních strojů](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [cloudové služby](https://azure.microsoft.com/services/cloud-services/), [aplikační služby – webové aplikace](https://azure.microsoft.com/services/app-service/web/)a [služby správy rozhraní API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="lets-get-started"></a>Umožňuje začít

Tento článek předpokládá, že jste obeznámeni s automatické škálování. Můžete [začít zde škálovat svůj prostředek][1]. Následují některé společné vzory měřítka.

## <a name="scale-based-on-cpu"></a>Škálování na základě procesoru

Máte webovou aplikaci (/Roli služby VMSS/cloudservice) a

- Chcete horizontální navýšení kapacity nebo škálování v závislosti na procesoru.
- Kromě toho chcete zajistit, že je minimální počet instancí.
- Chcete také zajistit, že nastavíte maximální limit počtu instancí, na které můžete škálovat.

![Škálování na základě procesoru][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Škálovat odlišně ve všední dny vs víkendy

Máte webovou aplikaci (/Roli služby VMSS/cloudservice) a

- Chcete ve výchozím nastavení 3 instance (ve všední dny)
- Neočekáváte provoz o víkendech, a proto chcete o víkendech snížit kapacitu na 1 instanci.

![Škálovat odlišně ve všední dny vs víkendy][3]

## <a name="scale-differently-during-holidays"></a>Během prázdnin můžete škálovat odlišně

Máte webovou aplikaci (/Roli služby VMSS/cloudservice) a

- Chcete vertikálně vertikálně vertikálně vertikálně vertikálně vertikálně navýšit/snížit kapacitu podle využití procesoru.
- Během prázdnin (nebo konkrétních dnů, které jsou důležité pro vaši firmu) však chcete přepsat výchozí hodnoty a mít k dispozici větší kapacitu.

![O svátcích můžete škálovat jinak][4]

## <a name="scale-based-on-custom-metric"></a>Škálování na základě vlastní metriky

Máte webový front-end a úroveň rozhraní API, která komunikuje s back-endem.

- Chcete škálovat úroveň rozhraní API na základě vlastních událostí v front-endu (příklad: Chcete škálovat proces nákupu na základě počtu položek v nákupním košíku)

![Škálování na základě vlastní metriky][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png

