---
title: Dotaz mezi službami mezi Azure Monitor a Azure Průzkumník dat (Preview)
description: Dotazování dat Azure Průzkumník dat prostřednictvím Azure Log Analytics Tools naopak pro připojení a analýzu všech dat na jednom místě.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 5aadd9d96f538f92e1b9e0100b2c1055ee0b0633
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065265"
---
# <a name="cross-service-query---azure-monitor-and-azure-data-explorer-preview"></a>Dotaz na různé služby – Azure Monitor a Azure Průzkumník dat (Preview)
Vytvářejte dotazy mezi službami mezi [Azure Průzkumník dat](https://docs.microsoft.com/azure/data-explorer/), [Application Insights](/azure/azure-monitor/app/app-insights-overview)a [Log Analytics](/azure/azure-monitor/platform/data-platform-logs).
## <a name="azure-monitor-and-azure-data-explorer-cross-service-querying"></a>Azure Monitor a Azure Průzkumník dat dotazování mezi službami
Díky tomuto prostředí můžete [vytvářet dotazy mezi službami Azure Průzkumník dat a Azure monitor](https://docs.microsoft.com/azure/data-explorer/query-monitor-data) a [vytvářet dotazy na více služeb mezi Azure Monitor a Azure Průzkumník dat](https://docs.microsoft.com/azure/azure-monitor/platform/azure-monitor-data-explorer-proxy).

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="Tok proxy serveru Azure Data Explorer.":::

Například (dotazování Azure Průzkumník dat z Log Analytics):
```kusto
CustomEvents | where aField == 1
| join (adx("Help/Samples").TableName | where secondField == 3) on $left.Key == $right.key
```
Kde se vnější dotaz dotazuje na tabulku v pracovním prostoru a pak se připojí k jiné tabulce v clusteru Azure Průzkumník dat (v tomto případě název_clusteru = Help, DatabaseName = Samples) pomocí nové funkce "ADX ()", jako je například to, jak můžete provádět dotazování na jiný pracovní prostor z textu v dotazu.

> [!NOTE]
> * Možnost dotazování dat Azure Monitor z Azure Průzkumník dat, a to buď přímo z klientských nástrojů Azure Průzkumník dat, nebo nepřímo spuštěním dotazu na cluster Průzkumník dat Azure je v režimu náhledu.
> * Kontaktujte tým [dotazů na vzájemné služby](mailto:adxproxy@microsoft.com) s případnými dotazy.

## <a name="query-exported-log-analytics-data-from-azure-blob-storage-account"></a>Dotaz na exportovaná data Log Analytics z účtu služby Azure Blob Storage

Export dat z Azure Monitor do účtu úložiště Azure umožňuje snížit náklady a možnost znovu přidělit protokoly různým oblastem.

Použijte Azure Průzkumník dat k dotazování na data, která byla exportována z vašich Log Analytics pracovních prostorů. Po nakonfigurování budou podporované tabulky, které se odesílají z vašich pracovních prostorů do účtu služby Azure Storage, k dispozici jako zdroj dat pro Azure Průzkumník dat. [Dotaz na exportovaná data z Azure monitor pomocí Azure Průzkumník dat (Preview)](https://docs.microsoft.com/azure/azure-monitor/platform/azure-data-explorer-query-storage).

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-query.png" alt-text="Dotaz na Azure Průzkumník dat z toku úložiště":::

>[!tip] 
> * Pokud chcete exportovat všechna data z vašeho pracovního prostoru Log Analytics do účtu služby Azure Storage nebo centra událostí, použijte funkci exportu dat Log Analytics pracovního prostoru Azure Monitor protokolů. [Viz téma Export dat pracovního prostoru Log Analytics v Azure monitor (Preview)](https://docs.microsoft.com/azure/data-explorer/query-monitor-data).

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace:
* [Vytvářejte dotazy mezi službami mezi Azure Průzkumník dat a Azure monitor](https://docs.microsoft.com/azure/data-explorer/query-monitor-data). Dotazování na data Azure Monitor z Azure Průzkumník dat
* [Vytvářejte dotazy mezi službami mezi Azure monitor a Azure Průzkumník dat](https://docs.microsoft.com/azure/azure-monitor/platform/azure-monitor-data-explorer-proxy). Dotazování dat Azure Průzkumník dat z Azure Monitor
* [Export dat pracovního prostoru Log Analytics v Azure monitor (Preview)](https://docs.microsoft.com/azure/data-explorer/query-monitor-data). Připojení a dotazování účtu služby Azure Blob Storage pomocí Log Analytics exportovaných dat
