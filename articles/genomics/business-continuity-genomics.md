---
title: Přehled provozní kontinuity
titleSuffix: Microsoft Genomics
description: Tento přehled popisuje možnosti, které Microsoft Genomics poskytuje pro provozní kontinuitu a zotavení po havárii.
keywords: provozní kontinuita, zotavení po havárii
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 04/06/2018
ms.openlocfilehash: 3097041c96f70dda384f0b116bbbc9bc9130be4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "93395746"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Přehled provozní kontinuity pomocí Microsoft Genomics
Tento přehled popisuje možnosti, které Microsoft Genomics poskytuje pro provozní kontinuitu a zotavení po havárii. Přečtěte si o možnostech obnovování z rušivých událostí, jako je výpadek oblasti Azure, které můžou způsobit ztrátu dat. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Microsoft Genomics funkce, které podporují kontinuitu podnikových služeb 
I když může dojít k výpadku datového centra Azure, což může způsobit narušení podniku, které může trvat několik minut. Pokud dojde k výpadku, všechny úlohy aktuálně spuštěné v datovém centru selžou a služba Microsoft Genomics nebude automaticky znovu odesílat úlohy do sekundární oblasti. 

* Jednou z možností je počkat, až se datové centrum vrátí do režimu online, když dojde k výpadku datového centra. Pokud je výpadek krátký, služba Microsoft Genomics automaticky detekuje neúspěšné úlohy a pracovní postup se automaticky restartuje.

* Další možností je proaktivní odeslání pracovního postupu v jiné oblasti dat. Microsoft Genomics nasadí instance v několika [oblastech Azure](https://azure.microsoft.com/regions/services/)a každá instance specifická pro jednotlivé oblasti je nezávislá. Pokud u jedné z Microsoft Genomics instancí dojde k místnímu selhání oblasti, ostatní oblasti spuštěné instance Microsoft Genomics budou pokračovat ve zpracování úloh. Tento přenos do alternativní oblasti je pod kontrolou uživatele a k dispozici kdykoli.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Ruční převzetí služeb při selhání Microsoft Genomics pracovních postupech do jiné oblasti
Pokud dojde k výpadku místního datového centra, můžete se rozhodnout odeslat Microsoft Genomics pracovní postupy v sekundární oblasti, a to na základě vašich individuálních požadavků na svrchovanost dat a provozní kontinuitu. K ručnímu převzetí služeb při selhání Microsoft Genomics workflowy byste měli použít jinou konkrétní oblast. Genomika účtu a odešle úlohu s příslušnými konkrétními Genomikami a přihlašovacími údaji k účtu úložiště.

Konkrétně budete potřebovat:
* Vytvořte účet genomiky v sekundární oblasti pomocí Azure Portal. 
* Migrujte vstupní data do účtu úložiště v sekundární oblasti a nastavte výstupní složku v sekundární oblasti.
* Odešlete pracovní postup do sekundární oblasti.

Po obnovení původní oblasti služba Microsoft Genomics nemigruje data ze sekundární oblasti zpátky do původní oblasti. Můžete se rozhodnout přesunout vstupní a výstupní soubory ze sekundární oblasti zpátky do původní oblasti.  Pokud se rozhodnete přesunout svá data, je to mimo službu genomiky a všechny poplatky související s přesunem dat budou vaší zodpovědností. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Příprava na možný výpadek konkrétní oblasti
Pokud máte obavy o rychlejší obnovení v případě výpadku datového centra, je potřeba provést několik kroků, které vám pomůžou ušetřit čas ručního opětovného odeslání Microsoft Genomics pracovních postupů do sekundární oblasti:

* Identifikujte příslušnou sekundární oblast a pro aktivně vytvořte účet genomiky v této oblasti.
* Duplikujte svá data v primární a sekundární oblasti, aby data byla okamžitě k dispozici v sekundární oblasti. To můžete provést ručně nebo pomocí funkce [geograficky redundantního úložiště](../storage/common/storage-redundancy.md) dostupného ve službě Azure Storage. 

## <a name="next-steps"></a>Další kroky
V tomto článku jste se seznámili s možnostmi pro provozní kontinuitu a zotavení po havárii při používání služby Microsoft Genomics. Další informace o provozní kontinuitě a zotavení po havárii v Azure najdete v tématu [technické doprovodné materiály k odolnosti Azure.](/azure/architecture/resiliency/recovery-loss-azure-region)