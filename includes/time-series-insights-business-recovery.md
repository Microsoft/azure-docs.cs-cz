---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.date: 04/01/2021
ms.openlocfilehash: 6529aa49d06e64947deb5ae54db0c39ad2575569
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106288583"
---
## <a name="business-disaster-recovery"></a>Zotavení po havárii podniku

Tato část popisuje funkce Azure Time Series Insights, které udržují spuštěné aplikace a služby, a to i v případě, že dojde k havárii (označované jako *obnovení obchodních havárií*).

### <a name="high-availability"></a>Vysoká dostupnost

Jako služba Azure Azure Time Series Insights poskytuje určité funkce *vysoké dostupnosti* , které využívají redundance na úrovni oblasti Azure. Azure například podporuje možnosti zotavení po havárii prostřednictvím funkce *dostupnosti napříč oblastmi* Azure.

Další funkce s vysokou dostupností poskytované prostřednictvím Azure (a také dostupné pro libovolnou instanci Azure Time Series Insights) zahrnují:

- **Převzetí služeb při selhání**: Azure zajišťuje [geografickou replikaci a vyrovnávání zatížení](/azure/architecture/resiliency/recovery-loss-azure-region).
- Obnovení **dat** a **obnovení úložiště**: Azure nabízí [několik možností, jak uchovávat a obnovovat data](/azure/architecture/resiliency/recovery-data-corruption).
- **Azure Site Recovery**: Azure poskytuje funkce pro obnovení prostřednictvím [Azure Site Recovery](../articles/site-recovery/index.yml).
- **Azure Backup**: [Azure Backup](../articles/backup/backup-architecture.md) podporuje místní i cloudové zálohování virtuálních počítačů Azure.

Ujistěte se, že jste povolili příslušné funkce Azure a zajistili globální a vysokou dostupnost pro vaše zařízení a uživatele v různých oblastech.

> [!NOTE]
> Pokud je Azure nakonfigurovaný tak, aby povoloval dostupnost mezi oblastmi, v Azure Time Series Insights není nutná žádná další konfigurace dostupnosti mezi oblastmi.

### <a name="iot-and-event-hubs"></a>Centra IoT a Event hub

Mezi některé služby Azure IoT patří také integrované funkce pro obnovení obchodních havárií:

- [Zotavení po havárii s vysokou dostupností v Azure IoT Hub](../articles/iot-hub/iot-hub-ha-dr.md), což zahrnuje redundanci uvnitř oblasti
- [Zásady Azure Event Hubs](../articles/event-hubs/event-hubs-geo-dr.md)
- [Redundance Azure Storage](../articles/storage/common/storage-redundancy.md)

Integrace Azure Time Series Insights s ostatními službami nabízí další možnosti zotavení po havárii. Například telemetrii poslaná do centra událostí může být trvalá v zálohování databáze úložiště objektů BLOB v Azure.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

Existuje několik způsobů, jak uchovávat data Azure Time Series Insights, aplikace a služby spuštěné, i když jsou přerušeny.

Je ale možné, že se vyžaduje také kompletní záložní kopie prostředí Azure Time Series, a to z těchto důvodů:

- Jako *instance převzetí služeb při selhání* specifickou pro Azure Time Series Insights pro přesměrování dat a provozu do
- Zachování informací o datech a auditování

Obecně platí, že nejlepším způsobem, jak duplikovat Azure Time Series Insights prostředí, je vytvořit druhé prostředí Azure Time Series Insights v rámci zálohování v oblasti Azure. Události se také odesílají do tohoto sekundárního prostředí z primárního zdroje událostí. Ujistěte se, že používáte druhou vyhrazenou skupinu uživatelů. Postupujte podle pokynů pro zotavení po havárii tohoto zdroje, jak je popsáno výše.

Vytvoření duplicitního prostředí:

1. Vytvořte prostředí ve druhé oblasti. Další informace najdete v tématu [Vytvoření nového Azure Time Series Insightsho prostředí v Azure Portal](../articles/time-series-insights/time-series-insights-get-started.md).
1. Vytvořte druhou vyhrazenou skupinu uživatelů pro zdroj události.
1. Připojte tento zdroj událostí k novému prostředí. Ujistěte se, že určíte druhou vyhrazenou skupinu uživatelů.
1. Přečtěte si dokumentaci Azure Time Series Insights [IoT Hub](../articles/time-series-insights/how-to-ingest-data-iot-hub.md) a [Event Hubs](../articles/time-series-insights/concepts-access-policies.md) .

Pokud dojde k události:

1. Pokud je vaše primární oblast ovlivněná během incidentu po havárii, přesměrujte operace do prostředí zálohování Azure Time Series Insights.
1. Vzhledem k tomu, že pořadová čísla centra se po převzetí služeb při selhání restartují z 0, vytvořte znovu zdroj událostí v obou oblastech nebo prostředích s různými skupinami uživatelů, abyste se vyhnuli vytváření co by
1. Pomocí druhé oblasti můžete zálohovat a obnovovat všechna Azure Time Series Insights telemetrie a data dotazů.

> [!IMPORTANT]
> Pokud dojde k převzetí služeb při selhání:
>
> - Může dojít také ke zpoždění.
> - V průběhu přesměrování operací se může vyskytnout chvilkový špička zpracování zpráv.
>
> Další informace najdete v tématu [zmírnění latence v Azure Time Series Insights](../articles/time-series-insights/time-series-insights-environment-mitigate-latency.md).