---
title: Kontinuita podnikových procesů – Microsoft Genomics | Dokumentace Microsoftu
titleSuffix: Azure
description: Tento přehled popisuje možnosti, které poskytuje Microsoft Genomics pro provozní kontinuitu a zotavení po havárii. Další informace o možnosti pro zotavení z ničivých událostí, jako je například kvůli výpadku oblasti Azure, který by mohl způsobit ztrátu dat.
keywords: kontinuita podnikových procesů, zotavení po havárii
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: article
ms.date: 04/06/2018
ms.openlocfilehash: be678648ab93fcbdfd0a0baa1b01dcb273060ce2
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729448"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Přehled kontinuity se službou Microsoft Genomics
Tento přehled popisuje možnosti, které poskytuje Microsoft Genomics pro provozní kontinuitu a zotavení po havárii. Další informace o možnosti pro zotavení z ničivých událostí, jako je například kvůli výpadku oblasti Azure, který by mohl způsobit ztrátu dat. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Microsoft Genomics funkce kontinuity této podpory 
I když se taková situace vzácná, datové centrum Azure může dojít k výpadku, což by mohlo způsobit narušení provozu, které může trvat několik minut až několik hodin. Když dojde k výpadku, všechny úlohy aktuálně spuštěné v datovém centru se nezdaří a ve službě Microsoft Genomics nebude automaticky znovu úlohy do sekundární oblasti. 

* Jednou z možností je počkat datové centrum vrátí do režimu online, když výpadek datového centra je nad. Pokud je krátký výpadek, ve službě Microsoft Genomics automaticky zjistí neúspěšné úlohy a pracovní postup se automaticky restartuje.

* Další možností je aktivně odešlete pracovní postup v jiné oblasti dat. Microsoft Genomics nasadí instance v několika [oblastí Azure](https://azure.microsoft.com/regions/services/), a každá instance oblast je nezávislé. Pokud některé z instancí Microsoft Genomics docházet k selhání místní oblasti, ostatní oblasti spuštěné instance Microsoft Genomics budou dál ke zpracování úloh. Kdykoli je tento přenos do alternativní oblast pod kontrolou uživatele a k dispozici.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Ruční převzetí služeb při selhání Microsoft Genomics pracovních postupů do jiné oblasti
V případě výpadku oblasti datového centra můžete se rozhodnout pro odeslání Microsoft Genomics pracovních postupů v sekundární oblasti, na základě požadavků jednotlivé datové suverenity a obchodní kontinuity podnikových procesů. Ruční převzetí služeb při selhání Microsoft Genomics pracovní postupy můžete využít různé konkrétní oblasti. Genomics účtu a odeslání úlohy s příslušnou oblast Genomics a přihlašovací údaje účtu úložiště.

Konkrétně je potřeba:
* V sekundární oblasti, pomocí webu Azure portal vytvořte účet Genomics. 
* Migrovat vstupní data do účtu úložiště v sekundární oblasti a nastavit výstupní složka v sekundární oblasti.
* Odeslání pracovního postupu v sekundární oblasti.

Když se obnoví původní oblast, ve službě Microsoft Genomics neprovádí migraci dat ze sekundární oblasti zpět do původní oblasti. Můžete se rozhodnout pro přesunutí vstupní a výstupní soubory ze sekundární oblasti zpět do původní oblast.  Pokud se rozhodnete přesunout svá data, toto je mimo službu Genomics a všechny poplatky za související s přesouvání dat by vaše odpovědnosti. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Příprava na možný výpadek oblast
Pokud máte obavy o rychlejší obnovení v případě výpadku datového centra, existuje několik kroků, které si můžete zkrátit čas potřebný pro vás se ručně znovu spustit pracovní postupy Microsoft Genomics do sekundární oblasti:

* Určit příslušné sekundární oblasti a aktivně v dané oblasti vytvořit účet Genomics
* Duplicitní vašich dat v primární a sekundární oblasti tak, aby vaše data jsou okamžitě dostupná v sekundární oblasti. Důvodem může být Hotovo ručně nebo pomocí [geograficky redundantní úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy) funkce je k dispozici ve službě Azure storage. 

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jaké máte možnosti pro provozní kontinuitu a zotavení po havárii při použití služby Microsoft Genomics. Další informace o provozní kontinuitu a zotavení po havárii v rámci Azure obecně najdete v tématu [technické pokyny k odolnosti Azure.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region) 