---
title: Osvědčené postupy při výběru ID řady času v Azure Time Series Insights | Dokumentace Microsoftu
description: Pochopení osvědčených postupů při výběru ID řady času v Azure Time Series Insights
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 86a23e96a0f9b92b4b0551e41c44cf3249b0f7e3
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52873389"
---
# <a name="best-practices-when-choosing-a-time-series-id"></a>Osvědčené postupy při výběru ID řady času

Tento dokument popisuje klíč oddílu Azure Time Series Insights (Preview) **ID řady času**a doporučené postupy pro zvolte jednu.

## <a name="choose-a-time-series-id"></a>Volba ID služby Time Series

Volba **ID řady času** je jako výběr klíče oddílu pro databázi. Proto je důležité rozhodnutí, která by měla být provedeno v době návrhu. Nelze aktualizovat stávající prostředí TSI (Preview) Chcete-li použít jinou **ID řady času**. Jinými slovy po vytvoření prostředí s **ID řady času**, zásady nelze změnit, protože je neměnné vlastnosti.

> [!IMPORTANT]
> **ID řady času** je velká a malá písmena a neměnné (ho nelze změnit po nastavení).

To na paměti, že vyberete vhodné **ID řady času** je velmi důležité. Vezměte v úvahu následující vlastnosti při výběru **ID řady času**:

> [!div class="checklist"]
> * Vyberte název vlastnosti, která má široký rozsah hodnot a má i vzorce přístupu. Je osvědčeným postupem je mít klíč oddílu s mnoha jedinečných hodnot (například stovky nebo tisíce). Pro mnoho zákazníků to bude vypadat **DeviceID**, **SensorID**, atd., v JSON.
> * Musí být jedinečný na úrovni uzel typu list vaše [modelu časové řady](./time-series-insights-update-tsm.md).
> * A **ID řady času** řetězec znaků vlastnost název může být maximálně 128 znaků a **ID řady času** hodnoty vlastností můžou být až 1024 znaků.
> * Pokud některé jedinečné **ID řady času** nebyly nalezeny hodnoty vlastností, jsou považovány za hodnoty null, které jsou zahrnuty v omezení jedinečnosti.

Kromě toho můžete vybrat až **tři** klíčové vlastnosti (3) jako vaše **ID řady času**.

  > [!NOTE]
  > Vaše **tři** (3) klíčové vlastnosti musí být řetězce.

Následující scénáře popisují vyberete více než jednu klíčovou vlastnost jako vaše **ID řady času**:  

* Scénář 1:

  * Máte starší verzi flotily nebo prostředků, že mají jedinečné klíče. 
  * Například jeden fleet jednoznačně identifikují podle vlastnosti **deviceId** a jiný jedinečný vlastností je **objectId**.  V obou flotily nebo jiných flotily jedinečný vlastnost není k dispozici. V tomto příkladu byste měli vybrat dva klíče, **deviceId**, a **objectId** jako jedinečné klíče. 
  * Můžeme přijmout hodnoty null a chybějící vlastnosti přítomnost v datové části události se bude počítat jako `null` hodnotu.  To může být také vhodný způsob pro zpracování odesílání dat do dvou zdrojů různých událostí, kde data v jednotlivých zdroj události má jiný jedinečný **ID řady času**.

* Scénář 2:

  * Budete potřebovat více vlastností zobrazíte jedinečnost ve stejném počtu prostředků. Řekněme například, jsou Chytré budovy výrobce a nasadit senzorů v každé místnosti. V každé místnosti, obvykle mají stejné hodnoty **sensorId**, včetně **sensor1**, **sensor2**, **sensor3**, a tak dále.
  * Kromě toho máte překrývající se dolní mez a místo čísla mezi lokalitami ve vlastnosti **flrRm** obsahující hodnoty jako `1a`, `2b`, `3a`, a tak dále.
  * A konečně, má-li vlastnost **umístění** , který obsahuje hodnoty jako `Redmond`, `Barcelona`, `Tokyo`, a tak dále. Jedinečnost vytvoříte by určit všechny tři z těchto vlastností jako vaše **ID řady času** klíčů – **sensorId**, **flrRm**, a **umístění**.

## <a name="next-steps"></a>Další postup

Další informace o [modelování dat](./time-series-insights-update-tsm.md).

Plánování vašeho [prostředí Azure Time Series Insights (Preview)](./time-series-insights-update-plan.md).