---
ms.topic: include
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 04/29/2019
ms.openlocfilehash: 8a3c630b54ff95a9b1200e2421c787a514a0aa52
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431034"
---
## <a name="business-disaster-recovery"></a>Zotavení po havárii firmy

Tato část popisuje funkce služby Azure Time Series Insights, který zachovat aplikace a služby spuštěné, i když dojde k havárii (označované jako *zotavení po havárii obchodní*).

### <a name="high-availability"></a>Vysoká dostupnost

Služby Azure Time Series Insights poskytuje určité *vysoké dostupnosti* funkcí pomocí redundance na úrovni oblasti Azure. Například Azure podporuje možnosti zotavení po havárii prostřednictvím služby Azure *dostupnosti mezi různými oblastmi* funkce.

Další funkce vysoké dostupnosti k dispozici prostřednictvím Azure (a také k dispozici na jakoukoli instanci služby Time Series Insights):

- **Převzetí služeb při selhání**: Azure poskytuje [geografickou replikaci a vyrovnávání zatížení](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
- **Obnovení dat** a **úložiště obnovení**: Azure poskytuje [celou řadu možností pro zachování a obnovení dat](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
- **Obnovení lokality**: Azure poskytuje funkce pro obnovení lokality pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Ujistěte se, že jste povolili relevantní funkce Azure pro zajištění vysoké dostupnosti globální, mezi různými oblastmi pro zařízení a uživatelů.

> [!NOTE]
> Pokud Azure je nakonfigurované tak, aby mezi oblastmi dostupnosti, není nutná žádná konfigurace větší dostupnost mezi různými oblastmi v Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>IoT a centra událostí

Některé služby Azure IoT také zahrnují integrované funkce pro zotavení po havárii:

- [Zotavení po havárii vysokou dostupnost služby IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), včetně uvnitř oblasti redundance
- [Zásady centra událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Azure redundance úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

Integrace s dalšími službami Time Series Insights poskytuje další po havárii příležitosti pro obnovení. Telemetrická data odesílaná do vašeho centra událostí může například nastavit jako trvalý, k záloze databáze úložiště objektů Blob v Azure.

### <a name="time-series-insights"></a>Time Series Insights

Existuje několik způsobů, jak zachovat vaše data služby Time Series Insights, aplikace a služby spuštěné, i v případě, že přerušit. 

Ale možná zjistíte, že kompletní záložní kopie vašeho prostředí Azure Time Series také vyžaduje, k těmto účelům:

- Jako *převzetí služeb při selhání instance* speciálně pro Time Series Insights k přesměrování dat a provoz do
- Chcete-li zachovat data a informace o auditování

Obecně platí nejlepší způsob, jak duplikovat prostředí Time Series Insights je vytvoření druhého prostředí Time Series Insights ve službě backup oblasti Azure. Události jsou také odesílány do tohoto sekundárního prostředí ze zdroje událostí primární. Ujistěte se, že používáte skupinu příjemců druhou, vyhrazené. Postupujte podle pokyny k obnovení po havárii firmy tohoto zdroje, jak je popsáno výše.

K vytvoření duplicitní prostředí:

1. Vytvořte prostředí v druhé oblasti. Další informace najdete v tématu [vytvořit nové prostředí Time Series Insights na webu Azure Portal](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Vytvoření druhého vyhrazenou skupinu spotřebitelů pro váš zdroj událostí.
1. Připojte tento zdroj událostí do nového prostředí. Ujistěte se, že určíte druhé, vyhrazená skupina příjemců.
1. Projděte si Time Series Insights [služby IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) a [Event Hubs](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) dokumentaci.

Pokud dojde k události:

1. Pokud primární oblast je ovlivněné incidentem po havárii, přesměrování operací zálohování prostředí Time Series Insights.
1. Zálohujte a Obnovujte všechny telemetrických dat a dotazování dat Time Series Insights pomocí druhé oblasti.

> [!IMPORTANT]
> Pokud dojde k selhání:
> 
> * Může také dojít ke zpoždění.
> * Momentální Špička při zpracování zprávy může dojít, jako jsou přesměrovány operace.
> 
> Další informace najdete v tématu [zmírnění latence v Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).

