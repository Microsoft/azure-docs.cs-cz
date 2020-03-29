---
title: Přehled kontinuity provozu
titleSuffix: Microsoft Genomics
description: Tento přehled popisuje funkce, které společnost Microsoft Genomics poskytuje pro kontinuitu podnikání a zotavení po havárii.
keywords: kontinuita provozu, zotavení po havárii
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 04/06/2018
ms.openlocfilehash: 28a4a53851155c56e8d34981862bf52a3a2cf15b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72249179"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Přehled kontinuity podnikání s Microsoft Genomics
Tento přehled popisuje funkce, které společnost Microsoft Genomics poskytuje pro kontinuitu podnikání a zotavení po havárii. Další informace o možnostech obnovení z rušivých událostí, jako je výpadek oblasti Azure, které by mohly způsobit ztrátu dat. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Funkce Microsoft Genomics, které podporují kontinuitu podnikání 
I když vzácné, datové centrum Azure může mít výpadek, což může způsobit přerušení podnikání, které může trvat několik minut až několik hodin. Dojde-li k výpadku, všechny úlohy aktuálně spuštěné v datovém centru se nezdaří a služba Microsoft Genomics nebude automaticky znovu odesílat úlohy do sekundární oblasti. 

* Jednou z možností je počkat, až se datové centrum vrátí do režimu online, až skončí výpadek datového centra. Pokud je výpadek krátký, služba Microsoft Genomics automaticky rozpozná neúspěšné úlohy a pracovní postup bude automaticky restartován.

* Další možností je proaktivní odeslání pracovního postupu v jiné oblasti dat. Microsoft Genomics nasazuje instance v několika [oblastech Azure](https://azure.microsoft.com/regions/services/)a každá instance specifická pro oblast je nezávislá. Pokud se v jedné z instancí Microsoft Genomics vyskytne místní selhání oblasti, budou ostatní oblasti s pouštějící instance Microsoft Genomics pokračovat ve zpracování úloh. Tento převod do alternativní oblasti je pod kontrolou uživatele a kdykoli je k dispozici.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Ruční převzetí služeb při selhání pracovní chod Microsoft Genomics do jiné oblasti
Pokud dojde k výpadku regionálního datového centra, můžete odeslat pracovní postupy Microsoft Genomics v sekundární oblasti na základě vašich individuálních požadavků na suverenitu dat a kontinuitu provozu. Chcete-li ručně převzetí služeb při selhání Microsoft Genomics pracovních postupů, byste použít jinou oblast specifické. Genomics účet a předložit úlohu s příslušnou oblast specifické genomiky a přihlašovací údaje účtu úložiště.

Konkrétně budete muset:
* Vytvořte účet Genomics v sekundární oblasti pomocí portálu Azure. 
* Migrujte vstupní data do účtu úložiště v sekundární oblasti a nastavte výstupní složku v sekundární oblasti.
* Odešlete pracovní postup v sekundární oblasti.

Po obnovení původní oblasti služba Microsoft Genomics nemigruje data ze sekundární oblasti zpět do původní oblasti. Můžete se rozhodnout přesunout vstupní a výstupní soubory ze sekundární oblasti zpět do původní oblasti.  Pokud se rozhodnete přesunout jejich data, je to mimo službu Genomics a všechny poplatky související s pohybem dat by byly vaší odpovědností. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Příprava na možný výpadek pro konkrétní region
Pokud máte obavy z rychlejšího obnovení v případě výpadku datového centra, můžete provést několik kroků, abyste zkrátili dobu, kterou potřebujete k ručnímu opětovnému odeslání pracovních postupů Microsoft Genomics do sekundární oblasti:

* Identifikujte vhodnou sekundární oblast a proaktivně vytvořte účet Genomics v této oblasti
* Duplikujte data v primární a sekundární oblasti, aby byla data okamžitě k dispozici v sekundární oblasti. To se dá provést ručně nebo pomocí [funkce geograficky redundantního úložiště,](https://docs.microsoft.com/azure/storage/common/storage-redundancy) která je dostupná v úložišti Azure. 

## <a name="next-steps"></a>Další kroky
V tomto článku jste se dozvěděli o možnostech kontinuity podnikání a zotavení po havárii při použití služby Microsoft Genomics. Další informace o kontinuitě podnikání a zotavení po havárii v rámci Azure obecně najdete v [tématu technické pokyny k odolnosti azure.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region) 