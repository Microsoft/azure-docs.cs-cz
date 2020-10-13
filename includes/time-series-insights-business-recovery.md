---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.date: 07/09/2020
ms.openlocfilehash: de5d3f8f32e928c77ffd6028ec764793ab7229ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86495329"
---
## <a name="business-disaster-recovery"></a>Zotavení po havárii podniku

Tato část popisuje funkce Azure Time Series Insights, které udržují spuštěné aplikace a služby, a to i v případě, že dojde k havárii (označované jako *obnovení obchodních havárií*).

### <a name="high-availability"></a>Vysoká dostupnost

Jako služba Azure Azure Time Series Insights poskytuje určité funkce *vysoké dostupnosti* , které využívají redundance na úrovni oblasti Azure. Azure například podporuje možnosti zotavení po havárii prostřednictvím funkce *dostupnosti napříč oblastmi* Azure.

Další funkce s vysokou dostupností poskytované prostřednictvím Azure (a také dostupné pro libovolnou instanci Azure Time Series Insights) zahrnují:

- **Převzetí služeb při selhání**: Azure zajišťuje [geografickou replikaci a vyrovnávání zatížení](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
- Obnovení **dat** a **obnovení úložiště**: Azure nabízí [několik možností, jak uchovávat a obnovovat data](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
- **Azure Site Recovery**: Azure poskytuje funkce Site Recovery prostřednictvím [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).
- **Azure Backup**: [Azure Backup](https://docs.microsoft.com/azure/backup/backup-architecture) podporuje místní i cloudové zálohování virtuálních počítačů Azure.

Ujistěte se, že jste povolili příslušné funkce Azure a zajistili globální a vysokou dostupnost pro vaše zařízení a uživatele v různých oblastech.

> [!NOTE]
> Pokud je Azure nakonfigurovaný tak, aby povoloval dostupnost mezi oblastmi, v Azure Time Series Insights není nutná žádná další konfigurace dostupnosti mezi oblastmi.

### <a name="iot-and-event-hubs"></a>Centra IoT a Event hub

Mezi některé služby Azure IoT patří také integrované funkce pro obnovení obchodních havárií:

- [Zotavení po havárii s vysokou dostupností v Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), což zahrnuje redundanci uvnitř oblasti
- [Zásady Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Azure Storage redundance](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

Integrace Azure Time Series Insights s ostatními službami nabízí další možnosti zotavení po havárii. Například telemetrii poslaná do centra událostí může být trvalá v zálohování databáze úložiště objektů BLOB v Azure.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

Existuje několik způsobů, jak uchovávat data Azure Time Series Insights, aplikace a služby spuštěné, i když jsou přerušeny. 

Je ale možné, že se vyžaduje také kompletní záložní kopie prostředí Azure Time Series, a to z těchto důvodů:

- Jako *instance převzetí služeb při selhání* specifickou pro Azure Time Series Insights pro přesměrování dat a provozu do
- Zachování informací o datech a auditování

Obecně platí, že nejlepším způsobem, jak duplikovat Azure Time Series Insights prostředí, je vytvořit druhé prostředí Azure Time Series Insights v rámci zálohování v oblasti Azure. Události se také odesílají do tohoto sekundárního prostředí z primárního zdroje událostí. Ujistěte se, že používáte druhou vyhrazenou skupinu uživatelů. Postupujte podle pokynů pro zotavení po havárii tohoto zdroje, jak je popsáno výše.

Vytvoření duplicitního prostředí:

1. Vytvořte prostředí ve druhé oblasti. Další informace najdete v tématu [Vytvoření nového Azure Time Series Insightsho prostředí v Azure Portal](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Vytvořte druhou vyhrazenou skupinu uživatelů pro zdroj události.
1. Připojte tento zdroj událostí k novému prostředí. Ujistěte se, že určíte druhou vyhrazenou skupinu uživatelů.
1. Přečtěte si dokumentaci Azure Time Series Insights [IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) a [Event Hubs](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) .

Pokud dojde k události:

1. Pokud je vaše primární oblast ovlivněná během incidentu po havárii, přesměrujte operace do prostředí zálohování Azure Time Series Insights.
1. Pomocí druhé oblasti můžete zálohovat a obnovovat všechna Azure Time Series Insights telemetrie a data dotazů.

> [!IMPORTANT]
> Pokud dojde k převzetí služeb při selhání:
> 
> * Může dojít také ke zpoždění.
> * V průběhu přesměrování operací se může vyskytnout chvilkový špička zpracování zpráv.
> 
> Další informace najdete v tématu [zmírnění latence v Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).

