---
title: Přehled protokolů prostředků Azure | Microsoft Docs
description: Pochopte podporované služby a schéma událostí pro protokoly prostředků Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 10/22/2019
ms.openlocfilehash: e1bf6a55568671ddb8f6999356cc9be43ce6a728
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72804020"
---
# <a name="azure-resource-logs-overview"></a>Přehled protokolů prostředků Azure
Protokoly prostředků Azure jsou [protokoly platforem](platform-logs-overview.md) vydávané prostředky Azure, které popisují jejich vnitřní provoz. Všechny protokoly prostředků sdílejí společné schéma nejvyšší úrovně s flexibilitou pro každou službu, aby vygenerovala jedinečné vlastnosti pro vlastní události.

> [!NOTE]
> Protokoly prostředků se dříve nazývaly diagnostické protokoly.

## <a name="collecting-resource-logs"></a>Shromažďování protokolů prostředků
Protokoly prostředků se automaticky generují pomocí podporovaných prostředků Azure, ale neshromažďují se, pokud je nenakonfigurujete pomocí [nastavení diagnostiky](diagnostic-settings.md). Vytvořte nastavení diagnostiky pro každý prostředek Azure a předejte protokoly do následujících umístění:

| Cíl | Scénář |
|:---|:---|:---|
| [Pracovní prostor služby Log Analytics](resource-logs-collect-storage.md) | Analyzujte protokoly s dalšími daty monitorování a využijte Azure Monitorch funkcí, jako jsou dotazy protokolů a výstrahy protokolu. |
| [Úložiště Azure](archive-diagnostic-logs.md) | Archivujte protokoly pro auditování nebo zálohování. |
| [Centrum událostí](resource-logs-stream-event-hubs.md) | Streamujte protokoly do systémů protokolování a telemetrie třetích stran.  |

## <a name="compute-resources"></a>Výpočetní prostředky
Protokoly prostředků se liší od protokolů na úrovni operačního systému hosta ve výpočetních prostředcích Azure. Výpočetní prostředky vyžadují, aby agent mohl shromažďovat protokoly a metriky ze svého hostovaného operačního systému, včetně takových dat, protokolů událostí, syslogu a čítačů výkonu. Pomocí [diagnostického rozšíření](agents-overview.md#azure-diagnostic-extension) můžete směrovat data protokolu z virtuálních počítačů Azure a [agenta Log Analytics](agents-overview.md#log-analytics-agent) ke shromáždění protokolů a metrik z virtuálních počítačů v Azure, v jiných cloudech a místních do pracovního prostoru Log Analytics. Podrobnosti najdete v tématu [zdroje dat monitorování pro Azure monitor](data-sources.md) .

## <a name="resource-logs-schema"></a>Schéma protokolů prostředků
Další informace o schématu a kategoriích protokolů prostředků najdete v tématu [schéma protokolu prostředků](diagnostic-logs-schema.md). 

## <a name="next-steps"></a>Další kroky

* [Přečtěte si další informace o protokolech platformy Azure](platform-logs-overview.md) , které můžete použít k monitorování Azure.
