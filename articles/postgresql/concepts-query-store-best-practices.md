---
title: Osvědčené postupy pro úložiště dotazů Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje osvědčené postupy pro úložiště dotazů na Azure Database for PostgreSQL jednom serveru.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: dd39b7ecd51902f5035b4cd17d59dea964d0c962
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708828"
---
# <a name="best-practices-for-query-store"></a>Osvědčené postupy pro úložiště dotazů

**Platí pro:** Azure Database for PostgreSQL – jeden server verze 9,6, 10, 11

Tento článek popisuje osvědčené postupy pro používání úložiště dotazů v Azure Database for PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Nastavení optimálního režimu zachycení dotazů
Umožněte, aby úložiště dotazů zachytává data, která vás zajímá. 

|**pg_qs pg_qs.query_capture_mode** | **Scénář**|
|---|---|
|_Vše_  |Důkladně Analyzujte své úlohy z pohledu všech dotazů a jejich frekvencí spuštění a dalších statistik. Identifikujte nové dotazy v úlohách. Zjišťuje, jestli se k identifikaci příležitostí pro uživatele nebo automatické Parametrizace používají ad hoc dotazy. _Vše_ přináší zvýšené náklady na spotřebu prostředků. |
|_Nahoře_  |Zaměřte pozornost na nejčastější dotazy, které vystavili klienti.
|_Žádný_ |Již jste zaznamenali sadu dotazů a časové okno, které chcete prozkoumat, a chcete eliminovat, že by mohly zavádět další dotazy. _Žádná_ je vhodná pro testování a označování prostředí. _Žádné_ by se měly používat opatrně, protože byste mohli přijít o příležitost sledovat a optimalizovat důležité nové dotazy. Nemůžete obnovit data v předchozích časových oknech. |

Úložiště dotazů zahrnuje i úložiště pro statistiku čekání. K dispozici je další dotaz na režim sběru dat, který řídí statistiku čekání: **pgms_wait_sampling. query_capture_mode** lze nastavit na _hodnotu None_ nebo _All_. 

> [!NOTE] 
> **pg_qs. query_capture_mode** nahrazuje **pgms_wait_sampling. query_capture_mode**. Pokud pg_qs. query_capture_mode je _none_, nastavení pgms_wait_sampling. query_capture_mode nemá žádný vliv. 


## <a name="keep-the-data-you-need"></a>Zachování potřebných dat
Parametr **pg_qs. retention_period_in_days** určuje ve dnech dobu uchovávání dat pro úložiště dotazů. Data ze staršího dotazu a statistiky se odstranila. Ve výchozím nastavení je úložiště dotazů nakonfigurované tak, aby zachovalo data po dobu 7 dnů. Vyhněte se zachování historických dat, která neplánujete použít. Zvyšte hodnotu, pokud potřebujete zachovat data déle.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>Nastavení četnosti vzorkování statistik čekání 
Parametr **pgms_wait_sampling. history_period** určuje, jak často (v milisekundách) se mají vzorkovat události čekání. Kratší doba je vzorkování častější. Další informace jsou načteny, ale jsou k dispozici s náklady na větší spotřebu prostředků. Tuto dobu zvyšte, pokud je server v zatížení nebo pokud nepotřebujete členitost.


## <a name="get-quick-insights-into-query-store"></a>Získat rychlé přehledy do úložiště dotazů
Pomocí [Query Performance Insight](concepts-query-performance-insight.md) v Azure Portal můžete získat rychlý přehled o datech v úložišti dotazů. Vizualizace povrchují nejdelší běžící dotazy a nejdelší čekací události v průběhu času.

## <a name="next-steps"></a>Další kroky
- Naučte se, jak získat nebo nastavit parametry pomocí [Azure Portal](howto-configure-server-parameters-using-portal.md) nebo rozhraní příkazového [řádku Azure CLI](howto-configure-server-parameters-using-cli.md).