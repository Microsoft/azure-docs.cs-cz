---
title: Izolace úloh
description: Pokyny pro nastavení izolace pracovního vytížení se skupinami úloh v Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: d5acdab9fb6eec585c53cfe0d7149aafa7cdc6f9
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350115"
---
# <a name="azure-synapse-analytics-workload-group-isolation-preview"></a>Azure Synapse Analytics izolace skupiny pracovních vytížek (preview)

Tento článek vysvětluje, jak lze skupiny úloh použít ke konfiguraci izolace pracovního vytížení, obsahují prostředky a používají pravidla za běhu pro spuštění dotazu.

## <a name="workload-groups"></a>Skupiny úloh

Skupiny úloh jsou kontejnery pro sadu požadavků a jsou základem pro konfiguraci správy úloh, včetně izolace pracovního vytížení v systému.  Skupiny úloh se vytvářejí pomocí syntaxe [VYTVOŘIT SKUPINU ÚLOH.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Jednoduchá konfigurace správy úloh může spravovat zatížení dat a uživatelské dotazy.  Například skupina úloh `wgDataLoads` s názvem bude definovat aspekty pracovního vytížení pro data načtená do systému. Skupina úloh s `wgUserQueries` názvem bude také definovat aspekty pracovního vytížení pro uživatele, kteří spouštějí dotazy ke čtení dat ze systému.

V následujících částech se zvýrazní, jak skupiny úloh poskytují možnost definovat izolaci, uzavření, požadovat definici prostředku a dodržovat pravidla provádění.

## <a name="workload-isolation"></a>Izolace úloh

Izolace pracovního vytížení znamená, že prostředky jsou vyhrazeny výhradně pro skupinu úloh.  Izolace pracovního vytížení je dosaženo konfigurací MIN_PERCENTAGE_RESOURCE parametr větší než nula v syntaxi [VYTVOŘIT ÚLOHY GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Pro úlohy průběžného provádění, které je třeba dodržovat úzce sla, izolace zajišťuje prostředky jsou vždy k dispozici pro skupinu úloh. 

Konfigurace izolace pracovního vytížení implicitně definuje garantovanou úroveň souběžnosti. Například skupina úloh s `MIN_PERCENTAGE_RESOURCE` nastavenou na `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 30 % a nastavenou na 2 % je zaručena 15 souběžnost.  Úroveň souběžnosti je zaručena, protože 15-2 % slotů prostředků jsou vyhrazeny v `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT` rámci skupiny úloh y po celou dobu (bez ohledu na to, jak je nakonfigurováno).  Pokud `REQUEST_MAX_RESOURCE_GRANT_PERCENT` je `REQUEST_MIN_RESOURCE_GRANT_PERCENT` větší `CAP_PERCENTAGE_RESOURCE` než `MIN_PERCENTAGE_RESOURCE` a je větší než další prostředky jsou přidány na požadavek.  Pokud `REQUEST_MAX_RESOURCE_GRANT_PERCENT` `REQUEST_MIN_RESOURCE_GRANT_PERCENT` a jsou `CAP_PERCENTAGE_RESOURCE` stejné `MIN_PERCENTAGE_RESOURCE`a je větší než , další souběžnost je možné.  Zvažte níže uvedenou metodu pro určení garantované souběžnosti:

[Garantovaná souběžnost]`MIN_PERCENTAGE_RESOURCE`=`REQUEST_MIN_RESOURCE_GRANT_PERCENT`[ ] / [ ]

> [!NOTE] 
> Existují specifické minimální schůdné hodnoty úrovně služeb pro min_percentage_resource.  Další informace naleznete v [tématu Efektivní hodnoty](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values) pro další podrobnosti.

Při absenci izolace pracovního vytížení požadavky pracovat ve [sdíleném fondu](#shared-pool-resources) prostředků.  Přístup k prostředkům ve sdíleném fondu není zaručen a je přiřazen na základě [důležitosti.](sql-data-warehouse-workload-importance.md)

Konfigurace izolace pracovního vytížení by měla být provedena s opatrností, protože prostředky jsou přiděleny skupině pracovního vytížení i v případě, že ve skupině úloh nejsou žádné aktivní požadavky. Nadměrná konfigurace izolace může vést ke snížení celkového využití systému.

Uživatelé by se měli vyhnout řešení pro správu pracovního vytížení, které konfiguruje 100% izolace pracovního vytížení: 100% izolace je dosaženo, když součet min_percentage_resource nakonfigurovaných ve všech skupinách úloh se rovná 100 %.  Tento typ konfigurace je příliš omezující a rigidní, takže malý prostor pro požadavky na prostředky, které jsou náhodně nesprávně klasifikovány. Existuje ustanovení, které umožňuje jeden požadavek spustit ze skupin úloh, které nejsou nakonfigurovány pro izolaci. Zdroje přidělené na tento požadavek se zobrazí jako nula v systémech DMVs a vypůjčit smallrc úroveň grantu prostředků ze systému vyhrazených prostředků.

> [!NOTE] 
> Chcete-li zajistit optimální využití prostředků, zvažte řešení pro správu úloh, které využívá určitou izolaci, aby bylo zajištěno, že sla a smíšené se sdílenými prostředky, které jsou přístupné na základě [důležitosti pracovního vytížení](sql-data-warehouse-workload-importance.md).

## <a name="workload-containment"></a>Omezení pracovní zátěže

Omezení zatížení odkazuje na omezení množství prostředků, které může skupina pracovního vytížení spotřebovat.  Omezení zatížení je dosaženo konfigurací CAP_PERCENTAGE_RESOURCE parametr na méně než 100 v syntaxi [VYTVOŘIT PRACOVNÍ VYTÍŽENÍ SKUPINY.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Zvažte scénář, kdy uživatelé potřebují přístup pro čtení do systému, aby mohli spustit citlivostní analýzu prostřednictvím dotazů ad-hoc.  Tyto typy požadavků může mít negativní dopad na jiné úlohy, které jsou spuštěny v systému.  Konfigurace omezení zajišťuje omezené množství prostředků.

Konfigurace omezení pracovního vytížení implicitně definuje maximální úroveň souběžnosti.  S CAP_PERCENTAGE_RESOURCE nastavena na 60 % a REQUEST_MIN_RESOURCE_GRANT_PERCENT nastavena na 1 %, až 60-souběžnosti úrovně je povoleno pro skupinu pracovního vytížení.  Zvažte níže uvedenou metodu pro určení maximální souběžnosti:

[Maximální souběžnost]`CAP_PERCENTAGE_RESOURCE`= [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`] / [ ]

> [!NOTE] 
> Efektivní CAP_PERCENTAGE_RESOURCE skupiny pracovního vytížení nedosáhne 100 %, když jsou vytvořeny skupiny úloh s MIN_PERCENTAGE_RESOURCE na úrovni větší než nula.  Efektivní hodnoty běhu naleznete [v souboru sys.dm_workload_management_workload_groups_stats.](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest)

## <a name="resources-per-request-definition"></a>Zdroje na definici požadavku

Skupiny úloh poskytují mechanismus pro definování minimálního a maximálního množství prostředků, které jsou přiděleny na požadavek s parametry REQUEST_MIN_RESOURCE_GRANT_PERCENT a REQUEST_MAX_RESOURCE_GRANT_PERCENT v syntaxi [CREATE WORKLOAD GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Prostředky v tomto případě jsou procesor a paměť.  Konfigurace těchto hodnot určuje, kolik prostředků a jaké úrovně souběžnosti lze dosáhnout v systému.

> [!NOTE] 
> REQUEST_MAX_RESOURCE_GRANT_PERCENT je volitelný parametr, který výchozí hodnotu, která je určena pro REQUEST_MIN_RESOURCE_GRANT_PERCENT.

Stejně jako výběr třídy prostředků, konfigurace REQUEST_MIN_RESOURCE_GRANT_PERCENT nastaví hodnotu prostředků používaných požadavkem.  Množství zdrojů označených nastavenou hodnotou je zaručeno pro přidělení požadavku před zahájením provádění.  Pro zákazníky migrující z tříd prostředků do skupin pracovního vytížení, zvažte následující [jak chcete](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) článek mapovat z tříd prostředků do skupiny pracovního vytížení jako výchozí bod.

Konfigurace REQUEST_MAX_RESOURCE_GRANT_PERCENT na hodnotu větší než REQUEST_MIN_RESOURCE_GRANT_PERCENT umožňuje systému přidělit více prostředků na požadavek.  Při plánování požadavku systém určuje skutečné přidělení zdrojů k požadavku, které je mezi REQUEST_MIN_RESOURCE_GRANT_PERCENT a REQUEST_MAX_RESOURCE_GRANT_PERCENT, na základě dostupnosti zdrojů ve sdíleném fondu a aktuálního zatížení Systému.  Prostředky musí existovat ve [sdíleném fondu](#shared-pool-resources) prostředků při plánování dotazu.  

> [!NOTE] 
> REQUEST_MIN_RESOURCE_GRANT_PERCENT a REQUEST_MAX_RESOURCE_GRANT_PERCENT mají efektivní hodnoty, které jsou závislé na efektivní MIN_PERCENTAGE_RESOURCE a CAP_PERCENTAGE_RESOURCE hodnoty.  Efektivní hodnoty běhu naleznete [v souboru sys.dm_workload_management_workload_groups_stats.](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest)

## <a name="execution-rules"></a>Pravidla provádění

V systémech ad-hoc reportingu mohou zákazníci náhodně provádět neřízené dotazy, které vážně ovlivňují produktivitu ostatních.  Správci systému jsou nuceni trávit čas zabíjením uprchlých dotazů, aby uvolnili systémové prostředky.  Skupiny úloh nabízejí možnost nakonfigurovat pravidlo časového limitu spuštění dotazu pro zrušení dotazů, které překročily zadanou hodnotu.  Pravidlo je konfigurováno `QUERY_EXECUTION_TIMEOUT_SEC` nastavením parametru v syntaxi [CREATE WORKLOAD GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)

## <a name="shared-pool-resources"></a>Prostředky sdíleného fondu

Prostředky sdíleného fondu jsou prostředky, které nejsou nakonfigurovány pro izolaci.  Skupiny úloh s MIN_PERCENTAGE_RESOURCE nastaveny na nulové využití prostředků ve sdíleném fondu ke spuštění požadavků.  Skupiny úloh s CAP_PERCENTAGE_RESOURCE větší než MIN_PERCENTAGE_RESOURCE také používaly sdílené prostředky.  Množství zdrojů dostupných ve sdíleném fondu se vypočítá následujícím způsobem.

[Sdílený fond] = 100 `MIN_PERCENTAGE_RESOURCE` - [součet napříč všemi skupinami úloh]

Přístup k prostředkům ve sdíleném fondu je přidělován na základě [důležitosti.](sql-data-warehouse-workload-importance.md)  Požadavky se stejnou úrovní důležitosti budou mít přístup k prostředkům sdíleného fondu na základě prvního příchozího a prvního outu.

## <a name="next-steps"></a>Další kroky

- [Úvodní příručka: konfigurace izolace pracovního vytížení](quickstart-configure-workload-isolation-tsql.md)
- [VYTVOŘIT SKUPINU PRACOVNÍCH VYTÍŽEK](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
- [Převést třídy prostředků na skupiny úloh](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md).
- [Monitorování portálu pro správu úloh](sql-data-warehouse-workload-management-portal-monitor.md).  
