---
title: Správa úloh
description: Pokyny pro implementaci správy úloh v Azure Synapse Analytics.
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
ms.openlocfilehash: dd867d4aa9a9ef5ed73e78a46826a8cd5239039b
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744236"
---
# <a name="what-is-workload-management"></a>Co je správa pracovního vytížení?

Spuštění smíšených úloh může představovat problémy s prostředky v zaneprázdněných systémech.  Architekti řešení hledají způsoby, jak oddělit klasické aktivity datových skladů (například načítání, transformace a dotazování dat), aby bylo zajištěno, že existuje dostatek prostředků pro přístup k sla.  

Fyzická izolace serveru může vést k části infrastruktury, které jsou nevyužité, overbooked nebo ve stavu, kdy mezipaměti jsou neustále základním nátěrem s hardware min.  Úspěšné schéma správy pracovního vytížení efektivně spravuje zdroje, zajišťuje vysoce efektivní využití zdrojů a maximalizuje návratnost investic (ROI).

Úloha datového skladu odkazuje na všechny operace, které se promítají ve vztahu k datovému skladu. Hloubka a šířka těchto součástí závisí na úrovni vyspělosti datového skladu.  Úloha datového skladu zahrnuje:

- Celý proces načítání dat do skladu
- Provádění analýzy datového skladu a vytváření sestav
- Správa dat v datovém skladu
- Export dat z datového skladu

Výkonová kapacita datového skladu je určena [jednotkami datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md).

- Informace o přidělení prostředků pro všechny profily výkonu naleznete v [tématu Limity paměti a souběžnosti](memory-concurrency-limits.md).
- Chcete-li nastavit kapacitu, můžete [vertikálně navýšit nebo snížit kapacitu](quickstart-scale-compute-portal.md).

## <a name="workload-management-concepts"></a>Koncepty správy úloh

V minulosti jste pro SQL Analytics v Azure Synapse spravovali výkon dotazů prostřednictvím [tříd prostředků](resource-classes-for-workload-management.md).  Třídy prostředků povolené pro přiřazení paměti k dotazu na základě členství v roli.  Primární výzvou s třídami prostředků je, že po konfiguraci neexistovalo žádné zásady správného řízení nebo schopnost řídit úlohu.  

Například udělení členství v roli uživatele ad hoc smallrc povoleno, že uživatel spotřebuje 100 % paměti v systému.  S třídami prostředků neexistuje žádný způsob, jak rezervovat a zajistit, aby prostředky byly k dispozici pro kritické úlohy.

Správa úloh fondu SQL synapse v Azure Synapse se skládá ze tří konceptů vysoké úrovně: [Klasifikace pracovního vytížení](sql-data-warehouse-workload-classification.md), [Důležitost pracovního vytížení](sql-data-warehouse-workload-importance.md) a [Izolace pracovního vytížení](sql-data-warehouse-workload-isolation.md).  Tyto funkce vám poskytují větší kontrolu nad tím, jak vaše úloha využívá systémové prostředky.

Klasifikace pracovního vytížení je koncept přiřazení požadavku skupině pracovního vytížení a nastavení úrovní důležitosti.  Historicky toto přiřazení bylo provedeno prostřednictvím členství v rolích pomocí [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  To lze nyní provést prostřednictvím [VYTVOŘIT PRACOVNÍ ZÁTĚŽ CLASSIFER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Funkce klasifikace poskytuje bohatší sadu možností, jako je popisek, relace a čas pro klasifikaci požadavků.

Důležitost pracovního vytížení ovlivňuje pořadí, ve kterém požadavek získá přístup k prostředkům.  V zaneprázdněném systému má požadavek s vyšší důležitostí první přístup k prostředkům.  Důležitost může také zajistit objednaný přístup ke zámkům.

Izolace pracovního vytížení rezervuje prostředky pro skupinu úloh.  Prostředky rezervované ve skupině úloh jsou drženy výhradně pro tuto skupinu úloh k zajištění spuštění.  Skupiny úloh také umožňují definovat množství prostředků, které jsou přiřazeny na požadavek, podobně jako třídy prostředků.  Skupiny úloh vám možnost rezervovat nebo zastropovat množství prostředků, které může sada požadavků spotřebovat.  Nakonec skupiny úloh jsou mechanismus pro použití pravidel, jako je například časový limit dotazu, na požadavky.  

## <a name="next-steps"></a>Další kroky

- Další informace o klasifikaci pracovního vytížení naleznete v [tématu Klasifikace pracovního vytížení](sql-data-warehouse-workload-classification.md).  
- Další informace o izolaci pracovního vytížení naleznete v [tématu Izolace pracovního vytížení](sql-data-warehouse-workload-isolation.md).  
- Další informace o důležitosti pracovního vytížení naleznete v [tématu Důležitost pracovního vytížení](sql-data-warehouse-workload-importance.md).  
- Další informace o monitorování správy pracovních vytížení naleznete v [tématu Sledování portálu pro správu pracovních vytížení](sql-data-warehouse-workload-management-portal-monitor.md).  
