---
title: Query Store osvědčené postupy ve službě Azure Database for PostgreSQL
description: Tento článek popisuje osvědčené postupy pro Query Store ve službě Azure Database for PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: 54a86a7ea1852efba0776451291820f4174a1f1f
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377568"
---
# <a name="best-practices-for-query-store"></a>Osvědčené postupy pro Query Store

**Platí pro:** Azure Database for PostgreSQL 9.6 a 10

> [!IMPORTANT]
> Funkce Query Store je ve verzi Public Preview.


Tento článek popisuje doporučené postupy pro používání Query Store ve službě Azure Database for PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Nastavit režim zachycení optimální dotazu
Query Store vám umožňují zachytit data, které vás zajímají. 

|**pg_qs.query_capture_mode** | **Scénář**|
|---|---|
|_Všechny_  |Analýza úloh důkladně z hlediska všechny dotazy a jejich Frekvence spouštění a další statistiky. Identifikujte nové dotazy ve vašich úloh. Rozpoznat, pokud ad-hoc dotazy se používají k identifikaci příležitostí pro uživatele nebo Parametrizace automaticky. _Všechny_ dodává se náklady na spotřebu zvýšenou prostředků. |
|_nahoru_  |Zaměřte se na nejčastější dotazy – vydávaných klienty vaši pozornost.
|_None_ |Jste zaznamenali sadě dotazu a časový interval, který chcete prozkoumat a chcete vyloučit rozptýlení, které mohou zavést další dotazy. _Žádný_ je vhodný pro testování a zkušebním označení prostředí. _Žádný_ třeba používat opatrně, protože můžete přijít o možnost sledovat a optimalizovat důležité nové dotazy. Nelze obnovit data na těchto posledních časových oken. |

Query Store také úložiště pro statistiky čekání. Není dotaz režimu další snímek, který řídí statistiky čekání: **pgms_wait_sampling.query_capture_mode** může být nastaven na _žádný_ nebo _všechny_. 

> [!NOTE] 
> **pg_qs.query_capture_mode** nahrazuje **pgms_wait_sampling.query_capture_mode**. Pokud je pg_qs.query_capture_mode _žádný_, pgms_wait_sampling.query_capture_mode nastavení nemá žádný vliv. 


## <a name="keep-the-data-you-need"></a>Uchovávání dat, které potřebujete
**Pg_qs.retention_period_in_days** parametr určuje ve dnech období uchovávání dat pro Query Store. Starší data dotazů a statistické údaje se odstraní. Query Store je ve výchozím nastavení nakonfigurovaná uchovávání dat po dobu 7 dní. Vyhněte se uchovávání historických dat, které nechcete použít. Zvyšte hodnotu, pokud je potřeba uchovávat data déle.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>Nastavte četnost statistiky čekání vzorkování 
**Pgms_wait_sampling.history_period** parametr určuje, jak často (v milisekundách) čekání události jsou odebírána data. Čím kratší doby, čím častější vzorkování. Další informace je načtena, ale který se dodává spolu s náklady na vyšší využití prostředků. Pokud je server zatížení nebo nepotřebujete členitost zvýšit toto období


## <a name="get-quick-insights-into-query-store"></a>Získat rychlý přehled o Query Store
Můžete použít [Query Performance Insight](concepts-query-performance-insight.md) na webu Azure Portal Získejte rychlý přehled o datech v Query Store. Vizualizace povrchové nejdelší spuštěné dotazy a nejdelší počkejte události v čase.

## <a name="next-steps"></a>Další postup
- Zjistěte, jak získat nebo nastavit parametry s využitím [webu Azure portal](howto-configure-server-parameters-using-portal.md) nebo [rozhraní příkazového řádku Azure](howto-configure-server-parameters-using-cli.md).