---
title: Správa a monitorování - aktivita dotazů, využití zdrojů
description: Zjistěte, jaké funkce jsou k dispozici pro správu a monitorování Azure Synapse Analytics. Pomocí portálu Azure a zobrazení dynamické správy (DMVs) můžete pochopit aktivitu dotazů a využití prostředků vašeho datového skladu.
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/11/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 9d2a9bb0fcaab38f897987a1922e9c95497821d4
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350672"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Monitorování využití prostředků a aktivity dotazů v Azure Synapse Analytics
Azure Synapse Analytics poskytuje bohaté možnosti monitorování v rámci portálu Azure, abyste získali přehledy o zatížení datového skladu. Portál Azure je doporučený nástroj při monitorování datového skladu, protože poskytuje konfigurovatelné doby uchovávání, výstrahy, doporučení a přizpůsobitelné grafy a řídicí panely pro metriky a protokoly. Portál také umožňuje integraci s dalšími službami monitorování Azure, jako je Azure Monitor (protokoly) s analýzou protokolů, abyste poskytli komplexní monitorování nejen pro váš datový sklad, ale i pro celou analytickou platformu Azure pro integrovanou zkušenosti s monitorováním. Tato dokumentace popisuje, jaké možnosti monitorování jsou k dispozici pro optimalizaci a správu analytické platformy pomocí služby SQL Analytics. 

## <a name="resource-utilization"></a>Využití prostředků 
Následující metriky jsou k dispozici na webu Azure Portal for SQL Analytics. Tyto metriky se objevují prostřednictvím [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics).


| Název metriky             | Popis                                                  | Typ agregace |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Procento CPU          | Využití procesoru ve všech uzlech pro datový sklad      | Vztaž, Min, Max    |
| Procento datových V/V      | Využití vi ve všech uzlů pro datový sklad       | Vztaž, Min, Max    |
| Procento paměti       | Využití paměti (SQL Server) ve všech uzlech pro datový sklad | Vztaž, Min, Max   |
| Aktivní dotazy          | Počet aktivních dotazů spuštěných v systému             | Součet              |
| Dotazy ve frontě          | Počet dotazů ve frontě čekajících na spuštění provádění          | Součet              |
| Úspěšná připojení  | Počet úspěšných připojení k datům                 | Součet, počet       |
| Neúspěšná připojení      | Počet neúspěšných připojení k datovému skladu           | Součet, počet       |
| Blokováno bránou firewall     | Počet přihlášení do datového skladu, který byl zablokován     | Součet, počet       |
| DWU limit               | Cíl úrovně služeb datového skladu                | Vztaž, Min, Max    |
| Procento DWU          | Maximální mezi procentem procesoru a vprocentem vyvázlových dat        | Vztaž, Min, Max    |
| Použitý DWU                | Limit DWU * Procento DWU                                   | Vztaž, Min, Max    |
| Procento přístupů do mezipaměti    | (cache hity / cache miss) * 100, kde cache hity je součet všech columnstore segmentů hity v místní mezipaměti SSD a cache miss je columnstore segmenty mine v místní mezipaměti SSD sečteny ve všech uzlech | Vztaž, Min, Max    |
| Procento použité mezipaměti   | (využitá vyrovnávací paměť / kapacita mezipaměti) * 100, kde se používá mezipaměť, je součet všech bajtů v místní mezipaměti SSD ve všech uzlech a kapacita mezipaměti je součtem úložné kapacity místní mezipaměti SSD ve všech uzlech | Vztaž, Min, Max    |
| Procento místní databáze tempdb | Místní využití databáze tempdb ve všech výpočetních uzlech – hodnoty jsou vydávány každých pět minut | Vztaž, Min, Max    |
| Velikost úložiště dat | Celková velikost dat načtených do databáze. To zahrnuje data s bydlištěm v tabulkách CCI a mimo CCI, kde se velikost tabulek mimo CCI měří podle celkové velikosti souboru databáze | Součet |
| Velikost zotavení po havárii | Celková velikost geografické zálohy zachycované každých 24 hodin | Součet |
| Velikost úložiště snímků | Celková velikost snímků pořízených za účelem poskytnutí bodů obnovení databáze. To zahrnuje automatické a uživatelem definované snímky. | Součet |

Co je třeba vzít v úvahu při zobrazování metrik a nastavení upozornění:

- DWU používá představuje pouze **vysoké úrovni reprezentace využití** v celém fondu SQL a není určen jako komplexní ukazatel využití. Chcete-li zjistit, zda vertikálně navýšit nebo snížit kapacitu, zvažte všechny faktory, které mohou být ovlivněny DWU, jako je souběžnost, paměť, tempdb a adaptivní kapacita mezipaměti. Doporučujeme [spustit úlohu v různých nastaveních DWU,](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-compute-overview#finding-the-right-size-of-data-warehouse-units) abyste zjistili, co nejlépe vyhovuje vašim obchodním cílům.
- Selhání a úspěšná připojení jsou hlášena pro konkrétní datový sklad - nikoli pro logický server
- Procento paměti odráží využití i v případě, že datový sklad je v nečinnosti stavu - neodráží spotřebu paměti aktivní úlohy. Pomocí a sledování této metriky spolu s ostatními (tempdb, mezipaměť gen2) provést holistické rozhodnutí o tom, zda škálování pro další kapacitu mezipaměti zvýší výkon pracovního vytížení, aby vyhovovalvašim požadavkům.


## <a name="query-activity"></a>Aktivita dotazu
Pro programové prostředí při sledování SQL Analytics přes T-SQL poskytuje služba sadu zobrazení dynamické správy (DMVs). Tato zobrazení jsou užitečné při aktivním řešení problémů a identifikaci kritických bodů výkonu s vaší úlohou.

Chcete-li zobrazit seznam dmvs, které poskytuje SQL Analytics, naleznete v této [dokumentaci](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Metriky a diagnostické protokolování
Metriky i protokoly lze exportovat do Azure Monitor, konkrétně součást [protokoly Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) a lze programově přistupovat prostřednictvím [dotazů protokolu](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata). Latence protokolu pro SQL Analytics je asi 10-15 minut. Další podrobnosti o faktorech ovlivňujících latenci naleznete v následující dokumentaci.


## <a name="next-steps"></a>Další kroky
Následující návody popisují běžné scénáře a případy použití při monitorování a správě datového skladu:

- [Sledování úlohy datového skladu pomocí zařízení DMV](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)
