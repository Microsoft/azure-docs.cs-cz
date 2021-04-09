---
title: Izolace úloh
description: Pokyny k nastavení izolace úloh se skupinami úloh ve službě Azure synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 506aed16f1b8a6c631a759bb1367aef8242859ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98734776"
---
# <a name="azure-synapse-analytics-workload-group-isolation"></a>Izolace skupiny úloh Azure synapse Analytics

Tento článek vysvětluje, jak lze pomocí skupin úloh nakonfigurovat izolaci úloh, obsahovat prostředky a použít pravidla modulu runtime pro provádění dotazů.

## <a name="workload-groups"></a>Skupiny úloh

Skupiny úloh jsou kontejnery pro sadu požadavků a jsou základem pro způsob, jakým je Správa úloh, včetně izolace úloh, nakonfigurovaná v systému.  Skupiny úloh se vytvářejí pomocí syntaxe [vytvořit skupinu úloh](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Jednoduchá konfigurace správy úloh může spravovat načítání dat a uživatelské dotazy.  Například skupina úloh s názvem `wgDataLoads` bude určovat aspekty úloh pro data načítaná do systému. Skupina úloh s názvem bude také `wgUserQueries` definovat aspekty úloh pro uživatele, kteří spouštějí dotazy pro čtení dat ze systému.

V následujících částech se dozvíte, jak skupiny úloh poskytují možnost definovat izolaci, omezení, definici prostředků žádosti a dodržovat pravidla spouštění.

## <a name="workload-isolation"></a>Izolace úloh

Izolace úloh znamená, že prostředky jsou rezervované, výhradně pro skupinu úloh.  Izolaci úloh se dosahuje tak, že v syntaxi [vytvořit skupinu úloh](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) nakonfigurujete parametr MIN_PERCENTAGE_RESOURCE na hodnotu větší než nula.  Pro úlohy průběžného spouštění, které musí dodržovat těsné SLA, izolace zajišťuje, aby prostředky byly vždy dostupné pro skupinu úloh.

Konfigurace izolace úloh implicitně definuje zaručenou úroveň souběžnosti. Například skupina úloh s `MIN_PERCENTAGE_RESOURCE` nastavenou na 30% a `REQUEST_MIN_RESOURCE_GRANT_PERCENT` nastavená na 2% se garantuje 15 souběžnosti.  Úroveň souběžnosti je zaručená, protože 15-2% slotů prostředků jsou v rámci skupiny úloh vždy rezervované (bez ohledu na to, jak `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT` je nakonfigurované).  Pokud `REQUEST_MAX_RESOURCE_GRANT_PERCENT` je větší než `REQUEST_MIN_RESOURCE_GRANT_PERCENT` a `CAP_PERCENTAGE_RESOURCE` větší než `MIN_PERCENTAGE_RESOURCE` Další prostředky, přidají se za požadavek.  Pokud `REQUEST_MAX_RESOURCE_GRANT_PERCENT` `REQUEST_MIN_RESOURCE_GRANT_PERCENT` jsou a jsou stejné a jsou `CAP_PERCENTAGE_RESOURCE` větší než `MIN_PERCENTAGE_RESOURCE` , je možné další souběžnost.  Pro určení garantované souběžnosti zvažte následující metodu:

[Garantovaná souběžnost] = [ `MIN_PERCENTAGE_RESOURCE` ]/[ `REQUEST_MIN_RESOURCE_GRANT_PERCENT` ]

> [!NOTE]
> Pro min_percentage_resource existuje určitá minimální hodnota životaschopnosti úrovně služby.  Další informace najdete v tématu [efektivní hodnoty](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json?view=azure-sqldw-latest&preserve-view=true#effective-values) pro další podrobnosti.

V případě neexistence izolace úloh fungují požadavky ve [sdíleném fondu](#shared-pool-resources) prostředků.  Přístup k prostředkům ve sdíleném fondu není zaručen a je přiřazen na základě [důležitosti](sql-data-warehouse-workload-importance.md) .

Konfigurace izolace úloh se musí provádět opatrně, protože prostředky jsou přiděleny do skupiny úloh i v případě, že ve skupině úloh nejsou žádné aktivní požadavky. Překonfigurování izolace může vést k výraznému snížení celkového využití systému.

Uživatelé by se měli vyhnout řešení správy úloh, které konfiguruje 100% izolaci úloh: 100% izolace se dosáhne, když se součet min_percentage_resource nakonfigurovaných napříč všemi skupinami úloh 100 rovná%.  Tento typ konfigurace je nadomezující a tuhý, ale u žádostí o prostředky, které jsou omylem neklasifikované, je málo místa. Existuje zřídit, aby bylo možné spustit jednu žádost ze skupin úloh, které nejsou nakonfigurované pro izolaci. Prostředky přidělené této žádosti se v systémech zobrazení dynamické správy a vypůjčí smallrc úroveň udělení prostředků ze systémových rezervovaných prostředků.

> [!NOTE]
> Pro zajištění optimálního využití prostředků zvažte řešení pro správu úloh, které využívá určitou izolaci, aby se zajistilo, že SLA jsou splněné a smíchány se sdílenými prostředky, ke kterým se dostanete na základě [důležitosti úloh](sql-data-warehouse-workload-importance.md).

## <a name="workload-containment"></a>Zahrnutí úloh

Zahrnutí úloh znamená omezení množství prostředků, které může skupina úloh spotřebovat.  Omezení úloh se dosahuje tak, že v syntaxi [vytvořit skupinu úloh](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  nakonfigurujete parametr CAP_PERCENTAGE_RESOURCE na hodnotu menší než 100.  Vezměte v úvahu scénář, kdy uživatelé potřebují přístup pro čtení do systému, aby mohli spustit analýzu citlivosti pomocí dotazů ad-hoc.  Tyto typy požadavků mohou mít negativní dopad na jiné úlohy spuštěné v systému.  Při konfiguraci omezení se zajistí, že se omezí množství prostředků.

Konfigurace omezení úloh implicitně definuje maximální úroveň souběžnosti.  S CAP_PERCENTAGE_RESOURCE nastavenou na 60% a REQUEST_MIN_RESOURCE_GRANT_PERCENT nastavenou na 1% se pro skupinu úloh povoluje až 60 úroveň souběžnosti.  Zvažte, jak níže uvedená metoda určuje maximální souběžnost:

[Max. Concurrency] = [ `CAP_PERCENTAGE_RESOURCE` ]/[ `REQUEST_MIN_RESOURCE_GRANT_PERCENT` ]

> [!NOTE]
> Pokud se vytvoří skupiny úloh s MIN_PERCENTAGE_RESOURCE na úrovni větší než nula, bude efektivní CAP_PERCENTAGE_RESOURCE skupiny úloh nedosahovat 100%.  Platné běhové hodnoty najdete v tématu [Sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .

## <a name="resources-per-request-definition"></a>Definice prostředků na žádost

Skupiny úloh poskytují mechanismus pro definování minimálních a maximálních objemů prostředků, které jsou přiděleny na žádost, pomocí parametrů REQUEST_MIN_RESOURCE_GRANT_PERCENT a REQUEST_MAX_RESOURCE_GRANT_PERCENT v syntaxi [Create Group](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) Resource.  Prostředky v tomto případě jsou CPU a paměť.  Konfigurace těchto hodnot určuje, kolik prostředků a jakou úroveň souběžnosti lze v systému dosáhnout.

> [!NOTE]
> REQUEST_MAX_RESOURCE_GRANT_PERCENT je volitelný parametr, jehož výchozí hodnota je stejná jako ta, která je určená pro REQUEST_MIN_RESOURCE_GRANT_PERCENT.

Podobně jako při volbě třídy prostředku konfigurace REQUEST_MIN_RESOURCE_GRANT_PERCENT nastaví hodnotu pro prostředky využívané požadavkem.  Množství prostředků, které je uvedené v hodnotě nastavené, je zaručené pro přidělení žádosti před zahájením provádění.  Zákazníci, kteří migrují z tříd prostředků do skupin úloh, považují za výchozí bod podle článku [postup](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) mapování tříd prostředků na skupiny úloh.

Konfigurace REQUEST_MAX_RESOURCE_GRANT_PERCENT na hodnotu větší než REQUEST_MIN_RESOURCE_GRANT_PERCENT umožňuje systému přidělit více prostředků na požadavek.  Při plánování požadavku systém Určuje skutečné přidělení prostředků žádosti, která je mezi REQUEST_MIN_RESOURCE_GRANT_PERCENT a REQUEST_MAX_RESOURCE_GRANT_PERCENT, na základě dostupnosti prostředků ve sdíleném fondu a současného zatížení systému.  Prostředky musí existovat ve [sdíleném fondu](#shared-pool-resources) prostředků, když je dotaz naplánován.  

> [!NOTE]
> REQUEST_MIN_RESOURCE_GRANT_PERCENT a REQUEST_MAX_RESOURCE_GRANT_PERCENT mají platné hodnoty, které jsou závislé na platných MIN_PERCENTAGE_RESOURCE a CAP_PERCENTAGE_RESOURCEch hodnotách.  Platné běhové hodnoty najdete v tématu [Sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .

## <a name="execution-rules"></a>Pravidla spuštění

V systémech generování sestav ad-hoc můžou zákazníci omylem provádět navýšení dotazů, které mají vážně vliv na produktivitu ostatních.  Správci systému jsou nuceni věnovat se dotazům na usmrcování času, aby uvolnili systémové prostředky.  Skupiny úloh nabízejí možnost konfigurovat pravidlo časového limitu spuštění dotazu pro zrušení dotazů, které překračují zadanou hodnotu.  Pravidlo se konfiguruje nastavením `QUERY_EXECUTION_TIMEOUT_SEC` parametru v syntaxi [vytvořit skupinu úloh](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .

## <a name="shared-pool-resources"></a>Prostředky sdíleného fondu

Prostředky sdíleného fondu jsou prostředky, které nejsou nakonfigurované pro izolaci.  Skupiny úloh s MIN_PERCENTAGE_RESOURCE nastavenou na hodnotu nula využívají prostředky ve sdíleném fondu ke spouštění požadavků.  Skupiny úloh s CAP_PERCENTAGE_RESOURCE větší než MIN_PERCENTAGE_RESOURCE také využité sdílené prostředky.  Množství prostředků, které jsou k dispozici ve sdíleném fondu, se vypočte takto.

[Sdílený fond] = 100 – [součet `MIN_PERCENTAGE_RESOURCE` napříč všemi skupinami úloh]

Přístup k prostředkům ve sdíleném fondu se přiděluje na základě [důležitosti](sql-data-warehouse-workload-importance.md) .  Žádosti se stejnou úrovní důležitosti budou přistupovat ke sdíleným prostředkům fondu za prvé a za první.

## <a name="next-steps"></a>Další kroky

- [Rychlý Start: Konfigurace izolace úloh](quickstart-configure-workload-isolation-tsql.md)
- [VYTVOŘIT SKUPINU ÚLOH](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Převeďte třídy prostředků na skupiny úloh](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md).
- [Sledování portál pro správu úloh](sql-data-warehouse-workload-management-portal-monitor.md).  
