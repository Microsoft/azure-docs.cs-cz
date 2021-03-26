---
title: 'Shromáždění standardních hodnot: osvědčené postupy pro výkon & pokyny'
description: Poskytuje kroky pro shromažďování standardních hodnot výkonu jako pravidla pro optimalizaci výkonu SQL Server na virtuálním počítači Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b1909d5fd5e3c02f104c73acb515740cb6ff65f6
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572343"
---
# <a name="collect-baseline-performance-best-practices-for-sql-server-on-azure-vm"></a>Shromáždění standardních hodnot: osvědčené postupy výkonu pro SQL Server na virtuálním počítači Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek poskytuje informace pro shromažďování standardních hodnot výkonu jako řady osvědčených postupů a pokynů pro optimalizaci výkonu SQL Server v Azure Virtual Machines (virtuálních počítačích).

Mezi optimalizací a optimalizací výkonu je typicky kompromis. Tato řada osvědčených postupů pro výkon se zaměřuje na získání *nejlepšího* výkonu pro SQL Server v Azure Virtual Machines. Pokud je vaše úloha méně náročná, možná nebudete potřebovat při každé doporučené optimalizaci. Při hodnocení těchto doporučení Vezměte v úvahu požadavky na výkon, náklady a vzory úloh.

## <a name="overview"></a>Přehled

Pro účely předběžného přístupu Shromážděte čítače výkonu pomocí PerfMon/LogMan a zachyťte SQL Server statistiky čekání, abyste lépe porozuměli obecným tlakům a potenciálním kritickým bodům zdrojového prostředí. 

Začněte shromažďováním výkonu procesoru, paměti, [vstupně](../../../virtual-machines/premium-storage-performance.md#iops)-výstupních operací, [propustnosti](../../../virtual-machines/premium-storage-performance.md#throughput)a [latence](../../../virtual-machines/premium-storage-performance.md#latency) zdrojové úlohy v době špičky podle [kontrolního seznamu výkonu aplikace](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist). 

Shromažďovat data během špičky, jako jsou úlohy v průběhu běžného pracovního dne, ale také i jiné procesy vysokého zatížení, jako je například každodenní zpracování, a víkendové úlohy ETL. Zvažte horizontální navýšení kapacity prostředků pro velmi náročné úlohy, jako je například zpracování na konci čtvrtletí, a po dokončení úlohy se pak provede škálování. 

Pomocí analýzy výkonu můžete vybrat [Velikost virtuálního počítače](../../../virtual-machines/sizes-memory.md) , která se může škálovat na požadavky na výkon vašich úloh.


## <a name="storage"></a>Storage

SQL Server výkon závisí silně na vstupně-výstupním subsystému a výkon úložiště se měří podle IOPS a propustnosti. Pokud se vaše databáze nevejde do fyzické paměti, SQL Server trvale přinášejí stránky databáze do fondu vyrovnávacích pamětí a z něj. Datové soubory pro SQL Server by měly být zpracovány jinak. Přístup k souborům protokolu je sekvenční s výjimkou případů, kdy je potřeba transakci vrátit, kde se náhodně přistupuje k datovým souborům, včetně tempdb. Pokud máte pomalý vstupně-výstupní podsystém, mohou uživatelé zaznamenat problémy s výkonem, jako jsou například pomalé doby odezvy a úlohy, které nejsou dokončeny kvůli časovým limitům. 

Virtuální počítače s Azure Marketplace mají soubory protokolu na fyzickém disku, který je ve výchozím nastavení oddělený od datových souborů. Počet a velikost datových souborů databáze tempdb vyhovují osvědčeným postupům a jsou zaměřeny na dočasný `D:\` disk. 

Následující čítače výkonu mohou napomoci ověření propustnosti vstupně-výstupních operací vyžadovaných vaším SQL Server: 
* Čtení **\LogicalDisk\Disk/s** (čtení IOPS)
* **\LogicalDisk\Disk zápisy na disk/s** (zápis IOPS) 
* **\LogicalDisk\Disk přečtených bajtů/s** (požadavky na propustnost čtení pro soubory dat, protokol a databázi tempdb)
* **\LogicalDisk\Disk zapsaných bajtů/s** (požadavky na propustnost zápisu pro soubory dat, protokol a databázi tempdb)

Pomocí požadavků na vstupně-výstupní operace a propustnosti na úrovních špičky vyhodnoťte velikosti virtuálních počítačů, které odpovídají kapacitě z vašich měření. 

Pokud vaše úloha vyžaduje 20 tisíc čtení IOPS a 10 000 IOPS, můžete buď zvolit E16s_v3 (s až 32 000 a 25600 neuloženými podpaměťmi IOPS), nebo M16_s (s až 20 tisíc uloženými v mezipaměti a 10 000 IOPS) a 2 P30 disky vykládané pomocí prostorů úložiště. 

Ujistěte se, že rozumíte požadavkům na propustnost a IOPS zatížení, protože virtuální počítače mají jiné limity škálování pro vstupně-výstupní operace a propustnost.

## <a name="memory"></a>Memory (Paměť)

Sledujte jak externí paměť použitou v operačním systému, tak i paměť, kterou používá interně SQL Server. Identifikace přítlaku pro jednu z komponent bude mít za problém s velikostí virtuálních počítačů a k identifikaci příležitostí pro optimalizaci. 

Následující čítače výkonu vám pomůžou ověřit stav paměti virtuálního počítače s SQL Server: 
* [\Memory\Available MB](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer: paměť Manager\Target serveru paměti (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: paměť Manager\Total serveru paměti (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: buffer Manager\Lazy zápisy paměti/s](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: buffer \ přečtené stránky Life očekávané](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

## <a name="compute"></a>Compute

Výpočetní prostředí v Azure se spravuje jinak než v místním prostředí. Místní servery jsou během několika let sestaveny bez upgradu z důvodu režie správy a nákladů na získání nového hardwaru. Virtualizace zmírnit některé z těchto problémů, ale aplikace jsou optimalizované tak, aby využívaly největší výhody základního hardwaru, což znamená, že jakákoli významná změna spotřeby prostředků vyžaduje nové vyrovnávání zatížení celého fyzického prostředí. 

Nejedná se o výzvu v Azure, kde je snadné dosáhnout nového virtuálního počítače na jiné sérii hardwaru a dokonce i v jiné oblasti. 

V Azure chcete využít výhod co nejvíc prostředků virtuálních počítačů, proto by měly být virtuální počítače Azure nakonfigurované tak, aby co nejvíce ovlivnily co největší procesor, aniž by to mělo vliv na zatížení. 

Následující čítače výkonu vám pomůžou ověřit výpočetní stav SQL Server virtuálního počítače:
* **Čas procesoru \Processor Information (_Total) \%**
* **Čas procesoru \Process (soubor sqlservr) \%**

> [!NOTE] 
> V ideálním případě se snažte vyzkoušet používání 80% vašich výpočetních prostředků s špičkami vyššími než 90%, ale nedosáhnou 100% za jakékoli trvalé časové období. V podstatě budete chtít jenom zřídit výpočetní výkon aplikací a potom naplánovat horizontální nebo nižší navýšení kapacity podle potřeb firmy. 


## <a name="next-steps"></a>Další kroky

Další informace najdete v dalších článcích v této sérii:
- [Rychlý kontrolní seznam](performance-guidelines-best-practices-checklist.md)
- [Velikost virtuálního počítače](performance-guidelines-best-practices-vm-size.md)
- [Storage](performance-guidelines-best-practices-storage.md)


Osvědčené postupy zabezpečení najdete v tématu [požadavky na zabezpečení pro SQL Server v Azure Virtual Machines](security-considerations-best-practices.md).

Další informace o SQL Server článcích o virtuálních počítačích najdete v článku [SQL Server na Azure Virtual Machines přehled](sql-server-on-azure-vm-iaas-what-is-overview.md). Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](frequently-asked-questions-faq.md).
