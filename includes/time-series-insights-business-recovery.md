---
title: zahrnout soubor
description: zahrnout soubor
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 04/29/2019
ms.openlocfilehash: e3e7044148e262e6977ae3be96f7cb61218114a3
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388745"
---
## <a name="business-disaster-recovery"></a>Zotavení po havárii firmy

Tato část popisuje funkce služby Azure Time Series Insights, který zachovat aplikace a služby spuštěné i v případě, že dojde k havárii. Tento koncept se označuje jako provozní zotavení po havárii.

### <a name="high-availability"></a>Vysoká dostupnost

Jako službu Azure Time Series Insights poskytuje některé funkce vysokou dostupnost prostřednictvím redundance na úrovni oblasti Azure. Například Microsoft Azure podporuje možnosti zotavení po havárii prostřednictvím služby Azure mezi oblastmi dostupnosti funkce.

Další funkce vysoké dostupnosti prostřednictvím Azure a že k dispozici jsou také k dispozici na jakoukoli instanci služby Time Series Insights, patří:

* **Převzetí služeb při selhání**: Azure poskytuje [geografickou replikaci a vyrovnávání zatížení](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
* **Obnovení dat** a **úložiště obnovení**: Azure poskytuje [celou řadu možností pro zachování a obnovení dat](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
* **Obnovení lokality**: Funkce, které jsou k dispozici prostřednictvím [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Ujistěte se, že chcete povolit tyto funkce Azure pro zajištění vysoké dostupnosti globální, mezi různými oblastmi pro zařízení a uživatelů.

> [!NOTE]
> Pokud Azure je nakonfigurované tak, aby mezi oblastmi dostupnosti, není nutná v rámci Azure Time Series Insights žádná konfigurace větší dostupnost mezi oblastmi.

### <a name="iot-and-event-hubs"></a>IoT a centra událostí

Některé služby Azure IoT také zahrnují integrované funkce pro zotavení po havárii:

* [Azure IoT Hub vysoké dostupnosti zotavení po havárii](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), což zahrnuje redundance uvnitř oblasti.
* [Zásady služby Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).
* [Azure redundance úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

Integrování tyto služby Time Series Insights poskytuje možnosti obnovení Další po havárii. Telemetrická data odesílaná do vašeho centra událostí může například nastavit jako trvalý, k záloze databáze úložiště objektů Blob v Azure.

### <a name="time-series-insights"></a>Time Series Insights

Zachováte vaše data služby Time Series Insights, aplikací a služeb spuštěných i v případě, že přerušit několika způsoby. Lze také určit, že je vyžadována kompletní záložní kopie vašeho prostředí Azure Time Series:

* Jako instanci převzetí služeb při selhání specifické pro Time Series Insights k přesměrování dat a provoz do.
* Pro účely auditu a data a zachovávání s rozlišením.

Obecně platí nejlepší způsob, jak duplikovat prostředí Time Series Insights je vytvoření druhého prostředí Time Series Insights ve službě backup oblasti Azure. Události jsou také odesílány do tohoto sekundárního prostředí ze zdroje událostí primární. Ujistěte se, použijte druhý vyhrazenou skupinu spotřebitelů a postupujte podle tohoto zdroje firmy po havárii obnovení pokyny, jak už k dispozici.

Konkrétně k vytvoření duplicitní prostředí:

1. Vytvořte prostředí v druhé oblasti. Pokyny najdete v tématu [vytvořit nové prostředí Time Series Insights na webu Azure Portal](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Vytvoření druhého vyhrazenou skupinu spotřebitelů pro váš zdroj událostí.
1. Připojte tento zdroj událostí do nového prostředí. Ujistěte se, že k určení druhé skupině příjemců.
1. Projděte si Time Series Insights [služby IoT hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) a [centra událostí](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) dokumentaci.

Nakonec:

1. Pokud primární oblast je ovlivněné incidentem po havárii, přesměrování operací zálohování prostředí Time Series Insights.
1. Zálohujte a Obnovujte všechny telemetrických dat a dotazování dat Time Series Insights pomocí druhé oblasti.

> [!IMPORTANT]
> * Všimněte si, že ke zpoždění může setkat v případě selhání.
> * Převzetí služeb při selhání může také způsobit prudký nárůst momentální zpracování zprávy, jako jsou přesměrovány operace.
> * Další informace najdete v tématu [zmírnění latence v Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).
