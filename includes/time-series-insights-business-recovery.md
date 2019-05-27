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
ms.openlocfilehash: e87a82e985ed1d1794f9da00546f167ef01e1779
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66147453"
---
## <a name="business-disaster-recovery"></a>Zotavení po havárii firmy

Tato část popisuje funkce služby Azure Time Series Insights, který aplikace a služby spuštěné i v případě, že dojde k havárii zůstaly (**zotavení po havárii obchodní**).

### <a name="high-availability"></a>Vysoká dostupnost

Služby Azure Time Series Insights poskytuje určité **vysoké dostupnosti** funkce pomocí redundance na úrovni oblasti Azure. Například Microsoft Azure podporuje možnosti zotavení po havárii prostřednictvím služby Azure **dostupnosti mezi různými oblastmi** funkce.

Další vysokou dostupnost funkce poskytované prostřednictvím Azure (a také k dispozici na jakoukoli instanci služby Time Series Insights):

1. **Převzetí služeb při selhání**: Azure poskytuje [geografickou replikaci a načítají vyrovnávání](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
1. **Obnovení dat** a **úložiště obnovení**: Azure poskytuje [celou řadu možností pro zachování a obnovení dat](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
1. **Site Recovery** funkce prostřednictvím [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Ujistěte se, že chcete povolit tyto funkce Azure k zajištění globální, mezi různými oblastmi, vysoké dostupnosti pro zařízení a uživatelů.

> [!NOTE]
> Pokud Azure je nakonfigurované tak, aby **dostupnosti mezi různými oblastmi**, v rámci Azure Time Series Insights nevyžaduje se žádná konfigurace větší dostupnost mezi oblastmi.

### <a name="iot-and-event-hubs"></a>IoT a centra událostí

Některé služby Azure IoT také zahrnují integrované funkce pro zotavení po havárii:

1. [Zotavení po havárii vysokou dostupnost služby IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr) včetně redundance uvnitř oblasti.
1. [Zásady centra událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).
1. [Azure redundance úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

Integrování tyto služby Time Series Insights poskytuje možnosti obnovení Další po havárii. Telemetrická data odesílaná do vašeho centra událostí může například nastavit jako trvalý, k záloze databáze služby Azure Blob Storage.

### <a name="time-series-insights"></a>Time Series Insights

Zachováte vaše data služby Time Series Insights, aplikací a služeb spuštěných i v případě, že přerušit několika způsoby. Lze také určit, že je vyžadována, duplicitní, kompletní záložní kopie vašeho prostředí Azure Time Series:

1. Time Series Insights – specifická **převzetí služeb při selhání instance** k přesměrování dat a provoz do.
1. Pro účely auditu a data a zachovávání s rozlišením.

Obecně platí nejlepší způsob, jak duplikovat prostředí Time Series Insights je vytvoření druhého prostředí Time Series Insights ve službě backup oblasti Azure. Události jsou také odesílány do tohoto sekundárního prostředí ze zdroje událostí primární. Ujistěte se, že chcete používat skupiny konzumentů druhou, vyhrazené a postupovat podle tohoto zdroje firmy po havárii obnovení pokynů (výše).

Konkrétně k vytvoření duplicitní prostředí:

1. Vytvořením prostředí v druhé oblasti ([vytvořit nové prostředí Time Series Insights na webu Azure Portal](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)).
1. Vytvoření druhého vyhrazenou skupinu spotřebitelů pro váš zdroj událostí.
1. Připojení tohoto zdroje událostí do nového prostředí, nezapomeňte určit druhé, vyhrazená skupina příjemců.
1. Projděte si Time Series Insights [služby IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) a [centra událostí](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) dokumentaci.

Nakonec:

* Pokud primární oblast je ovlivněné incidentem po havárii, přesměrování operací zálohování prostředí Time Series Insights.
* Zálohujte a Obnovujte všechny telemetrických dat a dotazování dat Time Series Insights pomocí druhé oblasti.

> [!IMPORTANT]
> * Všimněte si, že ke zpoždění může setkat v případě selhání.
> * Převzetí služeb při selhání může také způsobit prudký nárůst momentální zpracování zprávy, jako jsou přesměrovány operace.
> * Další informace najdete v tématu [zmírnění latence v Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).