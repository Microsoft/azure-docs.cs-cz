---
title: Správa úloh
description: Pokyny pro implementaci správy úloh v Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 10/30/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 2563ea4ab498c11c846cfe79f0e668f7d491c2e7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692353"
---
# <a name="what-is-workload-management"></a>Co je Správa úloh?

Spouštění smíšených úloh může představovat výzvy k prostředkům v zaneprázdněných systémech.  Architekti řešení hledají způsoby, jak oddělit klasické aktivity datových skladů (například načítání, transformování a dotazování na data), aby se zajistilo, že existuje dostatek prostředků pro přístup k SLA.  

Izolace fyzického serveru může vést k kapsy infrastruktury, která je nevyužitá, provedená nebo ve stavu, ve kterém se při spouštění a zastavování mezipamětí průběžně ukládají do mezipaměti.  Úspěšné schéma správy úloh efektivně spravuje prostředky, zajišťuje vysoce efektivní využití prostředků a maximalizuje návratnost investic.

Zatížení datového skladu odkazuje na všechny operace, které se zobrazí ve vztahu k datovému skladu. Hloubka a Šířka těchto komponent závisí na úrovni vyspělosti datového skladu.  Zatížení datového skladu zahrnuje: 
- Celý proces načítání dat do datového skladu 
- Provádění analýzy a vytváření sestav datového skladu
- Správa dat v datovém skladu 
- Export dat z datového skladu

Kapacita výkonu datového skladu je určena [jednotkami datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md).
- Chcete-li zobrazit prostředky přidělené pro všechny profily výkonu, přečtěte si téma [limity paměti a souběžnosti] paměť-Concurrency-limits.md).
- Pokud chcete nastavit kapacitu, můžete [škálovat směrem nahoru nebo dolů](quickstart-scale-compute-portal.md).


## <a name="workload-management-concepts"></a>Koncepty správy úloh
V minulosti jste spravovali výkon dotazů v SQL Data Warehouse prostřednictvím [tříd prostředků](resource-classes-for-workload-management.md).  Třídy prostředků, které jsou povoleny pro přiřazení paměti k dotazu na základě členství v rolích.  Primární výzvou s třídami Resources je, že po nakonfigurování neexistovala žádná oprávnění ke správě úloh.  

Například udělení členství role uživatele ad hoc, aby smallrc povoleno, aby tento uživatel mohl využívat 100% paměti v systému.  S třídami prostředků neexistuje žádný způsob, jak rezervovat a zajistit dostupnost prostředků pro kritické úlohy.

Správa úloh na SQL Data Warehouse se skládá ze tří konceptů vysoké úrovně: [klasifikace úloh](sql-data-warehouse-workload-classification.md), [důležité úlohy](sql-data-warehouse-workload-importance.md) a [izolace úloh](sql-data-warehouse-workload-isolation.md).  Tyto funkce poskytují větší kontrolu nad tím, jak vaše zatížení využívají systémové prostředky.

Klasifikace úloh je koncept přiřazení žádosti do skupiny úloh a nastavení úrovní důležitosti.  Historická Tato přiřazení byla provedena prostřednictvím členství role pomocí [sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class).  To se teď dá udělat pomocí [CLASSIFERu vytvořit úlohu](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql).  Funkce klasifikace poskytuje bohatou sadu možností, jako je například popisek, relace a čas pro klasifikaci požadavků.

Důležité informace o úlohách mají vliv na pořadí, ve kterém požadavek získá přístup k prostředkům.  V zaneprázdněném systému má žádost s vyšší důležitost prvním přístup k prostředkům.  Důležitost taky může zajistit, aby byl přístup k zámkům uspořádaný. 

Izolace úloh rezervuje prostředky pro skupinu úloh.  Prostředky rezervované ve skupině úloh se uchovávají výhradně pro danou skupinu úloh, aby se zajistilo provádění.  Skupiny úloh také umožňují definovat množství prostředků, které jsou přiřazeny podle požadavků, podobně jako třídy prostředků.  Skupiny úloh poskytují možnost rezervovat nebo rušit množství prostředků, které může sada požadavků spotřebovat.  Skupiny úloh jsou nakonec mechanismem, jak použít pravidla, například časový limit dotazu na požadavky.  


## <a name="next-steps"></a>Další kroky

- Další informace o klasifikaci úloh najdete v tématu [klasifikace úloh](sql-data-warehouse-workload-classification.md).  
- Další informace o izolaci úloh najdete v tématu věnovaném [izolaci úloh](sql-data-warehouse-workload-isolation.md).  
- Další informace o důležitosti úloh najdete v tématu [důležitost úloh](sql-data-warehouse-workload-importance.md).  