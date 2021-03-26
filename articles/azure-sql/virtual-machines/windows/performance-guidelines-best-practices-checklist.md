---
title: 'Kontrolní seznam: osvědčené postupy & pokyny pro výkon'
description: Poskytuje rychlý kontrolní seznam, ve kterém si můžete projít osvědčené postupy a pokyny pro optimalizaci výkonu SQL Server na virtuálním počítači Azure (VM).
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.custom: contperf-fy21q3
ms.reviewer: jroth
ms.openlocfilehash: 0b88884576a47db871c78b874104d4973ee9ba9a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572344"
---
# <a name="checklist-performance-best-practices-for-sql-server-on-azure-vms"></a>Kontrolní seznam: osvědčené postupy výkonu pro SQL Server na virtuálních počítačích Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek poskytuje rychlý kontrolní seznam jako řadu osvědčených postupů a pokynů k optimalizaci výkonu SQL Server v Azure Virtual Machines (virtuálních počítačích). 

Podrobné informace najdete v dalších článcích v této sérii: [Velikost virtuálního počítače](performance-guidelines-best-practices-vm-size.md), [úložiště](performance-guidelines-best-practices-storage.md), [shromáždění směrného plánu](performance-guidelines-best-practices-collect-baseline.md). 


## <a name="overview"></a>Přehled

Při spouštění SQL Server v Azure Virtual Machines dál používejte stejné možnosti ladění výkonu databáze, které platí pro SQL Server v místních serverových prostředích. Výkon relační databáze ve veřejném cloudu je ale závislý na mnoha faktorech, třeba na velikosti virtuálního počítače a na konfiguraci datových disků.

Mezi optimalizací a optimalizací výkonu je typicky kompromis. Tato řada osvědčených postupů pro výkon se zaměřuje na získání *nejlepšího* výkonu pro SQL Server v Azure Virtual Machines. Pokud je vaše úloha méně náročná, možná nebudete potřebovat při každé doporučené optimalizaci. Při hodnocení těchto doporučení Vezměte v úvahu požadavky na výkon, náklady a vzory úloh.

## <a name="vm-size"></a>Velikost virtuálního počítače

Následuje rychlý kontrolní seznam osvědčených postupů pro velikost virtuálního počítače pro spuštění SQL Server na VIRTUÁLNÍm počítači Azure: 

- Používejte velikosti virtuálních počítačů 4 nebo více vCPU, jako je [Standard_M8-4ms](/../../virtual-machines/m-series), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)nebo [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) nebo vyšší. 
- Využijte [paměťově optimalizované](../../../virtual-machines/sizes-memory.md) velikosti virtuálních počítačů pro dosažení optimálního výkonu SQL Server úloh. 
- [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) Series, [M-](../../../virtual-machines/m-series.md)a [Mv2-](../../../virtual-machines/mv2-series.md) Series nabízejí optimální poměr mezi pamětí a Vcore potřebný pro úlohy OLTP. Oba virtuální počítače řady M nabízejí nejvyšší poměr paměti k vCore, který je potřeba pro důležité úlohy, a je taky ideální pro úlohy datového skladu. 
- Zvažte vyšší poměr paměti k vCore pro důležité úlohy a úlohy datového skladu. 
- Použijte image Azure Virtual Machine Marketplace jako nastavení SQL Server a možnosti úložiště jsou nakonfigurovány pro optimální výkon SQL Server. 
- Shromážděte charakteristiky výkonu cílového zatížení a použijte je k určení vhodné velikosti virtuálních počítačů pro vaši firmu.

Další informace najdete v tématu komplexní osvědčené [postupy pro velikost virtuálního počítače](performance-guidelines-best-practices-vm-size.md). 

## <a name="storage"></a>Storage

Následuje rychlý kontrolní seznam osvědčených postupů konfigurace úložiště pro spuštění SQL Server na VIRTUÁLNÍm počítači Azure: 

- Před volbou typu disku Sledujte aplikaci a [Určete požadavky na šířku pásma a latenci úložiště](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) pro SQL Server dat, protokolů a souborů tempdb. 
- Pokud chcete optimalizovat výkon úložiště, naplánujte si nejvyšší dostupný počet vstupně-výstupních operací a používejte ukládání dat do mezipaměti jako funkci výkonu pro čtení dat, zatímco se vyhnete [virtuálnímu počítači a diskům capping/omezování](../../../virtual-machines/premium-storage-performance.md#throttling).
- Data, protokoly a soubory tempdb umístěte na samostatné jednotky.
    - Pro datovou jednotku používejte jenom [disky Premium P30 a P40](../../../virtual-machines/disks-types.md#premium-ssd) , abyste zajistili dostupnost podpory mezipaměti.
    - Pro plán jednotky protokolů pro kapacitu a výkon testování oproti nákladům při vyhodnocování [P30 disků úrovně Premium P80](../../../virtual-machines/disks-types.md#premium-ssd).
      - Pokud se vyžaduje latence úložiště v milisekundách, použijte pro protokol transakcí [Azure Ultra disks](../../../virtual-machines/disks-types.md#ultra-disk) . 
      - Pro nasazení virtuálních počítačů řady M-Series zvažte použití [akcelerátor zápisu](../../../virtual-machines/how-to-enable-write-accelerator.md) přes disky Azure Ultra.
    - Po výběru optimální velikosti virtuálního počítače umístěte [databázi tempdb](/sql/relational-databases/databases/tempdb-database) do místní dočasné `D:\` jednotky SSD pro většinu SQL Server úloh. 
      - Pokud kapacita místního disku není pro databázi tempdb dostatečná, zvažte možnost navýšení velikosti virtuálního počítače. Další informace najdete v tématu [zásady ukládání do mezipaměti datových souborů](performance-guidelines-best-practices-storage.md#data-file-caching-policies) .
- Proložením několika datových disků Azure pomocí [prostorů úložiště](/windows-server/storage/storage-spaces/overview) zvyšte šířku pásma vstupně-výstupních operací cílového virtuálního počítače a omezení propustnosti.
- Nastavte [ukládání hostitelů do mezipaměti](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) pro disky datových souborů jen pro čtení.
- Nastavte [mezipaměť hostitele](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) na hodnotu žádné pro soubory protokolu.
    - Nepovolujte ukládání do mezipaměti pro čtení a zápis na discích, které obsahují soubory SQL Server. 
    - Před změnou nastavení mezipaměti disku vždycky zastavte službu SQL Server.
- Pro úlohy vývoje a testování zvažte použití služby Storage úrovně Standard. Pro produkční úlohy se nedoporučuje používat HDD úrovně Standard/SDD.
- [Diskové shlukování na základě kreditu](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) by se mělo považovat jenom za úlohy pro vývoj a testování a systémy oddělení.
- Zřídí účet úložiště ve stejné oblasti jako virtuální počítač SQL Server. 
- Zakažte geograficky redundantní úložiště Azure (geografickou replikaci) a použijte LRS (místní redundantní úložiště) v účtu úložiště.
- Naformátujte datový disk tak, aby používal velikost alokační jednotky 64 KB pro všechny datové soubory umístěné na jiné jednotce než na dočasném `D:\` disku (ve výchozím nastavení je 4 KB). SQL Server virtuální počítače nasazené prostřednictvím Azure Marketplace se dodávají s datovými disky formátovanými velikostí alokační jednotky a prokládání pro fond úložiště s nastavením 64 KB. 

Další informace najdete v tématu kompletní [osvědčené postupy pro úložiště](performance-guidelines-best-practices-storage.md). 


## <a name="azure--sql-feature-specific"></a>Specifická funkce Azure & SQL

Následuje rychlý kontrolní seznam osvědčených postupů pro SQL Server a konfigurace specifické pro Azure při spuštění SQL Server na VIRTUÁLNÍm počítači Azure: 

- Zaregistrujte se pomocí [rozšíření agenta SQL IaaS](sql-agent-extension-manually-register-single-vm.md) a odemkněte řadu [výhod funkcí](sql-server-iaas-agent-extension-automate-management.md#feature-benefits). 
- Povolit kompresi stránky databáze.
- Povolí okamžitou inicializaci souborů pro datové soubory.
- Omezte automatické zvětšování databáze.
- Zakáže automaticky zmenšení databáze.
- Přesuňte všechny databáze na datové disky, včetně systémových databází.
- Přesuňte protokoly chyb SQL Server a trasovací soubory do datových disků.
- Nakonfigurujte výchozí zálohu a umístění souborů databáze.
- [Povolí uzamčené stránky v paměti](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).
- Vyhodnoťte a nainstalujte [nejnovější kumulativní aktualizace](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) pro nainstalovanou verzi SQL Server.
- Zálohujte se přímo do Azure Blob Storage.
- Použijte více souborů [databáze tempdb](/sql/relational-databases/databases/tempdb-database#optimizing-tempdb-performance-in-sql-server) , 1 soubor na jádro a až 8 souborů.



## <a name="next-steps"></a>Další kroky

Další informace najdete v dalších článcích v této sérii:
- [Velikost virtuálního počítače](performance-guidelines-best-practices-vm-size.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [Shromáždit směrný plán](performance-guidelines-best-practices-collect-baseline.md)

Osvědčené postupy zabezpečení najdete v tématu [požadavky na zabezpečení pro SQL Server v Azure Virtual Machines](security-considerations-best-practices.md).

Další informace o SQL Server článcích o virtuálních počítačích najdete v článku [SQL Server na Azure Virtual Machines přehled](sql-server-on-azure-vm-iaas-what-is-overview.md). Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](frequently-asked-questions-faq.md).
