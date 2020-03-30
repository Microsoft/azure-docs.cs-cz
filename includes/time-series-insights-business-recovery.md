---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/03/2020
ms.openlocfilehash: 6a3837d01815306e469a684404ab76506f547f43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77013866"
---
## <a name="business-disaster-recovery"></a>Obnova obchodní chodnící po havárii

Tato část popisuje funkce Azure Time Series Insights, které udržují aplikace a služby spuštěné, i když dojde k havárii (označované jako *obnovení obchodní katastrofy).*

### <a name="high-availability"></a>Vysoká dostupnost

Jako služba Azure poskytuje Time Series Insights určité funkce *s vysokou dostupností* pomocí redundance na úrovni oblasti Azure. Azure například podporuje možnosti zotavení po havárii prostřednictvím funkce *dostupnosti azure napříč oblastmi.*

Mezi další funkce s vysokou dostupností poskytované prostřednictvím Azure (a dostupné také pro všechny instance Time Series Insights) patří:

- **Převzetí služeb při selhání**: Azure poskytuje [geografickou replikaci a vyrovnávání zatížení](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
- **Obnovení dat** a **obnovení úložiště**: Azure nabízí několik možností pro zachování a [obnovení dat](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
- **Azure Site Recovery**: Azure poskytuje funkce obnovení webu prostřednictvím [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).
- **Zálohování Azure:** [Azure Backup](https://docs.microsoft.com/azure/backup/backup-architecture) podporuje místní i cloudové zálohování virtuálních počítačích Azure.

Ujistěte se, že jste povolili příslušné funkce Azure, abyste zajistili globální vysokou dostupnost pro vaše zařízení a uživatele napříč oblastmi.

> [!NOTE]
> Pokud je Azure nakonfigurovaný tak, aby povoloval dostupnost mezi oblastmi, není v Azure Time Series Insights vyžadována žádná další konfigurace dostupnosti mezi oblastmi.

### <a name="iot-and-event-hubs"></a>IoT a centra událostí

Některé služby Azure IoT také obsahují integrované funkce pro zotavení po havárii:

- [Azure IoT Hub zotavení po havárii s vysokou dostupností](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), které zahrnuje redundanci v rámci oblasti
- [Zásady Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Redundance azure úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

Integrace Time Series Insights s ostatními službami poskytuje další příležitosti zotavení po havárii. Telemetrie odeslaná do centra událostí může být například trvalá do záložní databáze úložiště objektů blob Azure.

### <a name="time-series-insights"></a>Time Series Insights

Data, aplikace a služby Time Series Insights jsou spuštěna několika způsoby, i když jsou narušené. 

Můžete však určit, že je vyžadována také úplná záložní kopie prostředí Azure Time Series, a to pro následující účely:

- Jako *instance s podporou převzetí služeb při selhání* speciálně pro Time Series Insights k přesměrování dat a provozu na
- Chcete-li zachovat data a auditovat informace

Obecně platí, že nejlepší způsob, jak duplikovat prostředí Time Series Insights, je vytvořit druhé prostředí Time Series Insights v záložní oblasti Azure. Události jsou také odesílány do tohoto sekundárního prostředí z primárního zdroje událostí. Ujistěte se, že používáte druhou vyhrazenou skupinu spotřebitelů. Postupujte podle pokynů pro obnovení obchodní hospo- zotavení po havárii tohoto zdroje, jak je popsáno výše.

Vytvoření duplicitního prostředí:

1. Vytvořte prostředí v druhé oblasti. Další informace najdete v části [Vytvoření nového prostředí Time Series Insights na webu Azure Portal](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Vytvořte druhou vyhrazenou skupinu spotřebitelů pro zdroj událostí.
1. Připojte tento zdroj událostí k novému prostředí. Ujistěte se, že jste určili druhou vyhrazenou skupinu spotřebitelů.
1. Projděte si dokumentaci centra [IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) a [Centra událostí](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) Time Series Insights.

Pokud dojde k události:

1. Pokud je primární oblast ovlivněna během havárie, přesměrujte operace do zálohovacího prostředí Time Series Insights.
1. Pomocí druhé oblasti můžete zálohovat a obnovit všechny telemetrie a data dotazů Time Series Insights.

> [!IMPORTANT]
> Pokud dojde k převzetí služeb při selhání:
> 
> * Může dojít také ke zpoždění.
> * Může dojít k chvilkové špičce ve zpracování zpráv, protože operace jsou přesměrovány.
> 
> Další informace najdete [v textu Zmírnění latence v přehledech časových řad](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).

