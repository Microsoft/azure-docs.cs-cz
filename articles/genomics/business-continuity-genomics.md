---
title: Kontinuita podnikových procesů – Microsoft Genomics | Microsoft Docs
titleSuffix: Azure
description: Zjistěte, jak Microsoft Genomics podporuje kontinuity podnikových procesů
keywords: kontinuita podnikových procesů zotavení po havárii
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 04/06/2018
ms.openlocfilehash: cb3825cb89aff386c4f7c3f3b0d771d73fe637b1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31426904"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Přehled kontinuity podnikových procesů s Genomics Microsoft
Tento přehled popisuje možnosti, které poskytuje Microsoft Genomics pro provozní kontinuitu a zotavení po havárii. Informace o možnostech pro obnovení z rušivý událostmi, například výpadek oblast Azure, který by mohl způsobit ztrátu dat. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Microsoft Genomics funkce kontinuity podnikových procesů tohoto podpory 
I když je taková situace vzácná, může mít datové centrum Azure k výpadku, což by mohlo způsobit narušení firmy, která může trvat několik minut po několik hodin. Když dojde k výpadku, všechny úlohy, které jsou aktuálně spuštěny v datovém centru se nezdaří a službu Microsoft Genomics nebude automaticky znovu úloh v sekundární oblasti. 

* Jednou z možností je čekat na datovém centru vrátí do režimu online, když datovém centru výpadku je přes. Pokud se výpadek krátký, službu Microsoft Genomics automaticky zjistí neúspěšných úloh a pracovní postup bude automaticky restartován.

* Další možností je proaktivně odeslání pracovního postupu v jiné oblasti data. Microsoft Genomics nasadí instancí v několika [oblastí Azure](https://azure.microsoft.com/regions/services/), a je nezávislý na každou instanci oblast. Pokud jedna z instancí Microsoft Genomics dojde k selhání místní oblast, jiných oblastí spuštěné instance Microsoft Genomics bude pokračovat ve zpracování úlohy. Tento přenos do alternativní oblasti se pod kontrolou uživatele a k dispozici v okamžiku.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Ruční převzetí služeb při selhání Microsoft Genomics pracovní postupy pro jiné oblasti
V případě místní výpadku datacentra může zvolit odeslat Microsoft Genomics pracovních postupů v sekundární oblasti, podle potřeb jednotlivých datových suverenity a business kontinuity. Chcete ručně převzetí služeb při selhání Microsoft Genomics pracovní postupy použijte jiný konkrétní oblasti. Genomika účet a odeslání úlohy s příslušnou oblast genomika a přihlašovací údaje účtu úložiště.

Konkrétně budete muset:
* Vytvořte účet genomika v sekundární oblasti, pomocí portálu Azure. 
* Přenést vstupní data na účtu úložiště v sekundární oblasti a nastavit výstupní složky v sekundární oblasti.
* Odešlete pracovního postupu v sekundární oblasti.

Když se obnoví původní oblast službu Microsoft Genomics neprovádí migraci dat ze sekundární oblasti zpět do původní oblasti. Rozhodnete přesunout vstupní a výstupní soubory ze sekundární oblasti zpět do původní oblasti.  Pokud se rozhodnete přesunout svá data, to je mimo službu genomika a všechny poplatky související s přesun dat by vaší povinností. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Příprava možný výpadek oblast
Pokud máte obavy o rychlejší obnovení v případě výpadku datacentra, existuje několik kroků, které můžete použít ke snížení doby trvání můžete ručně znovu odeslat vaše pracovní postupy Microsoft Genomics do sekundární oblasti:

* Určit příslušné sekundární oblasti a aktivně vytvořit účet genomika v této oblasti
* Duplicitní data v oblasti primární a sekundární tak, aby vaše data jsou okamžitě k dispozici v sekundární oblasti. Důvodem může být done ručně nebo pomocí [geograficky redundantní úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy) funkce, které jsou k dispozici v úložišti Azure. 

## <a name="next-steps"></a>Další postup
V tomto článku jste se dozvěděli o možnosti kontinuity podnikových procesů a zotavení po havárii při používání služby Microsoft Genomics. Další informace o kontinuity podnikových procesů a zotavení po havárii v rámci Azure v obecné najdete v tématu [technické pokyny Azure odolnost proti chybám.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region) 