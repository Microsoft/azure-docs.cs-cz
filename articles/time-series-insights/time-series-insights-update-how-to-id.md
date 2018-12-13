---
title: Azure Time Series Insights ve verzi Preview – osvědčené postupy pro výběr ID řady času | Dokumentace Microsoftu
description: Pochopení osvědčených postupů při výběru ID řady času v Azure čas Series Insights ve verzi Preview.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 766bd0c838ead707e5af3f43bf7be9f1ffd9658e
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53272537"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Osvědčené postupy pro výběr ID řady času

Tento článek se týká klíč oddílu Azure čas Series Insights ve verzi Preview, ID řady času a osvědčené postupy pro výběr jedné.

## <a name="choose-a-time-series-id"></a>Volba ID služby Time Series

Volba ID řady času je třeba zvolit klíč oddílu pro databázi. Je důležité rozhodnutí, která by měla být provedeno v době návrhu. Nelze aktualizovat stávající prostředí čas Series Insights ve verzi Preview použít jiné ID řady čas Jinými slovy po vytvoření prostředí s ID řady času zásady jsou neměnné vlastnost, která se nedá změnit.

> [!IMPORTANT]
> ID řady čas je velká a malá písmena a neměnné (ho nelze změnit po nastavení).

To na paměti výběrem příslušné ID řady čas je velmi důležité. Když vyberete ID řady času, vezměte v úvahu následující tyto osvědčené postupy:
* Vyberte název vlastnosti, která má široký rozsah hodnot a má i vzorce přístupu. Je osvědčeným postupem je mít klíč oddílu s mnoha jedinečných hodnot (například stovky nebo tisíce). Pro mnoho zákazníků to bude něco jako ID zařízení nebo SensorID v JSON.
* ID řady čas musí být jedinečný na úrovni uzel typu list vaše [modelu časové řady](./time-series-insights-update-tsm.md).
* Řetězec znaků ID řady času vlastnost název může mít maximálně 128 znaků a hodnot vlastností řady ID času může mít maximálně 1024 znaků.
* Pokud chybí některé jedinečné hodnoty vlastnosti ID času řady, jsou považovány za hodnoty null, které jsou zahrnuty v omezení jedinečnosti.

Kromě toho můžete vybrat až *tři* (3) klíčové vlastnosti jako ID řady čas

  > [!NOTE]
  > Vaše *tři* (3) klíčové vlastnosti musí být řetězce.

Následující scénáře popisují výběr více než jednu klíčovou vlastnost jako vaše ID řady čas:  

### <a name="scenario-1"></a>Scénář 1

* Máte starší verzi flotily nebo prostředků, každá má jedinečný klíč. 
* Například jeden fleet jednoznačně identifikují podle vlastnosti *deviceId* a jiný jedinečný vlastností je *objectId*. Ani fleet obsahuje jiné flotily jedinečné vlastnosti. V tomto příkladu by dva klíče, ID zařízení a ID objektu, vyberte jako jedinečné klíče. 
* Můžeme přijmout hodnoty null a chybějící vlastnosti přítomnost v datové části události se započítává `null` hodnotu. Toto je také vhodný způsob pro odesílání dat do dvou zdrojů různých událostí, kde data v jednotlivých zdroj události má jedinečné ID řady čas zpracování

### <a name="scenario-2"></a>Scénář 2

* Budete potřebovat více vlastností být jedinečný v rámci stejného počtu prostředků. 
* Například Řekněme, že jste výrobce inteligentní vytváření a nasazení senzorů v každé místnosti. V každé místnosti, obvykle mají stejné hodnoty *sensorId*, jako například *sensor1*, *sensor2*, a *sensor3*.
* Kromě toho vaše sestavení má překrývající se dolní mez a místo čísla mezi lokalitami ve vlastnosti *flrRm*, které mají hodnoty jako *1a*, *2b*, *3a* , a tak dále.
* A konečně, má-li vlastnost *umístění*, který obsahuje hodnoty jako *Redmond*, *Barcelona*, a *Tokio*. Jedinečnost vytvoříte by určit následujícími třemi vlastnostmi jako čas řady ID klíče: *sensorId*, *flrRm*, a *umístění*.

## <a name="next-steps"></a>Další postup

* Další informace o [modelování dat](./time-series-insights-update-tsm.md).
* Plánování vašeho [prostředí Azure Time Series Insights (preview)](./time-series-insights-update-plan.md).
