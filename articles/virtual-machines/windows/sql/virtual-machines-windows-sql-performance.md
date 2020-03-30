---
title: Pokyny pro výkon pro SQL Server v Azure | Dokumenty společnosti Microsoft
description: Obsahuje pokyny pro optimalizaci výkonu serveru SQL Server ve virtuálních počítačích Microsoft Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/18/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 880f1c601cf4132fdec9e5d25b1bf1f2ff175ab7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650533"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Průvodce výkonem SQL Serveru ve službě Azure Virtual Machines

## <a name="overview"></a>Přehled

Tento článek obsahuje pokyny pro optimalizaci výkonu serveru SQL Server ve virtuálním počítači Microsoft Azure. Pokud provozujete SQL Server ve službě Azure Virtual Machines, doporučujeme dál používat stejné možnosti ladění výkonu databáze, které se dají použít pro SQL Server v místním serverovém prostředí. Výkon relační databáze ve veřejném cloudu však závisí na řadě faktorů, jako je velikost virtuálního počítače nebo konfigurace datových disků.

[Image SQL Serveru zřízené na webu Azure Portal](quickstart-sql-vm-create-portal.md) postupujte podle obecných doporučených postupů konfigurace úložiště (další informace o konfiguraci úložiště najdete v [tématu Konfigurace úložiště pro virtuální počítače SQL Server).](virtual-machines-windows-sql-server-storage-configuration.md) Po zřizování zvažte použití jiných optimalizací popsaných v tomto článku. Založte své volby na své pracovní zátěži a ověřte pomocí testování.

> [!TIP]
> Obvykle existuje kompromis mezi optimalizací nákladů a optimalizací výkonu. Tento článek se zaměřuje na získání *nejlepšího* výkonu pro SQL Server na virtuálních počítačích Azure. Pokud je vaše úloha méně náročná, nemusí vyžadovat všechny optimalizace uvedené níže. Při vyhodnocování těchto doporučení zvažte vaše potřeby výkonu, náklady a vzory pracovního vytížení.

## <a name="quick-check-list"></a>Seznam rychlé kontroly

Následuje rychlý kontrolní seznam pro optimální výkon SQL Server na virtuálních počítačích Azure:

| Oblast | Optimalizace |
| --- | --- |
| [Velikost virtuálního počítače](#vm-size-guidance) | - Použijte velikosti virtuálních zařízení se 4 nebo více virtuálními procesory, jako [je E4S_v3](../../ev3-esv3-series.md) nebo vyšší nebo [DS12_v2](../../dv2-dsv2-series-memory.md) nebo vyšší.<br/><br/> - [Řady Es, Eas, Ds a Das](../../sizes-general.md) nabízejí optimální poměr paměti k virtuálnímu procesoru potřebný pro výkon úlohy OLTP. <br/><br/> - [Řada M](../../m-series.md) nabízí nejvyšší poměr paměti k virtuálnímu procesoru potřebný pro kritický výkon a je ideální pro úlohy datového skladu. <br/><br/> - Shromážděte požadavky na [vstupně-výstupní operace](../premium-storage-performance.md#iops)cílové úlohy , [propustnost](../premium-storage-performance.md#throughput) a [latenci](../premium-storage-performance.md#latency) ve špičce podle [kontrolního seznamu požadavků na výkon aplikace](../premium-storage-performance.md#application-performance-requirements-checklist) a pak vyberte velikost [virtuálního počítače,](../sizes-general.md) který lze škálovat podle požadavků na výkon vašeho pracovního vytížení.|
| [Úložiště](#storage-guidance) | - Podrobné testování výkonu serveru SQL Server na virtuálních počítačích Azure s tpc-e a TPC_C benchmarky najdete v blogu [Optimalizace výkonu OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). <br/><br/> - Používejte [prémiové SSD pro](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) nejlepší výhody ceny / výkonu. Konfigurace [mezipaměti Jen pro čtení](../premium-storage-performance.md#disk-caching) pro datové soubory a žádná mezipaměť pro soubor protokolu. <br/><br/> - Použijte [Ultra Disky,](../disks-types.md#ultra-disk) pokud jsou menší než 1 ms latence úložiště jsou vyžadovány zatížení. <br/><br/> - Shromažďovat požadavky na latenci úložiště pro data serveru SQL Server, protokol a Temp DB soubory [sledováním aplikace](../premium-storage-performance.md#application-performance-requirements-checklist) před výběrem typu disku. Pokud jsou požadovány <latence úložiště 1 ms, použijte Ultra Disky, jinak použijte premium SSD. Pokud nízké latence jsou požadovány pouze pro soubor protokolu a nikoli pro datové soubory, pak [zřídit Ultra Disk](../disks-enable-ultra-ssd.md) na požadované iOPS a úrovně propustnost pouze pro soubor protokolu. <br/><br/> -  [Sdílené složky Premium](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) se doporučují jako sdílené úložiště pro instanci clusteru s podporou převzetí služeb při selhání serveru SQL Server. Sdílené složky Premium nepodporují ukládání do mezipaměti a nabízejí omezený výkon ve srovnání s prémiovými disky SSD. Zvolte prémiové disky spravované ssd spravovaným i přes sdílené složky premium pro samostatné instance SQL. ale využijte sdílené úložiště s podporou převzetí služeb při selhání pro sdílené úložiště s podporou převzetí služeb při selhání pro snadnou údržbu a flexibilní škálovatelnost. <br/><br/> - Standardní úložiště se doporučuje pouze pro účely vývoje a testování nebo pro záložní soubory a neměly by být používány pro produkční úlohy. <br/><br/> - Udržujte [účet úložiště](../../../storage/common/storage-create-storage-account.md) a virtuální modul SQL Server ve stejné oblasti.<br/><br/> - Zakázat [Azure geograficky redundantní úložiště](../../../storage/common/storage-redundancy.md) (geografická replikace) na účtu úložiště.  |
| [Disky](#disks-guidance) | - Použijte minimálně 2 [prémiové SSD disky](../disks-types.md#premium-ssd) (1 pro soubor protokolu a 1 pro datové soubory). <br/><br/> - Pro úlohy, které vyžadují <latence vi 1 ms, povolte akcelerátor zápisu pro řadu M a zvažte použití disků Ultra SSD pro řady Es a DS. <br/><br/> - Povolit ukládání pouze pro čtení do [mezipaměti](../premium-storage-performance.md#disk-caching) na disku (disky) hostování datových souborů.<br/><br/> - Přidejte další 20% prémiovou kapacitu IOPS / propustnosti, než vaše pracovní zátěž vyžaduje [při konfiguraci úložiště pro data serveru SQL Server, log a TempDB soubory](virtual-machines-windows-sql-server-storage-configuration.md) <br/><br/> - Vyhněte se použití operačního systému nebo dočasné disky pro ukládání databáze nebo protokolování.<br/><br/> - Nepovolujte ukládání do mezipaměti na discích, které jsou hostitelem souboru protokolu.  **Důležité:** Při změně nastavení mezipaměti pro disk virtuálního počítače Azure zastavte službu SQL Server.<br/><br/> - Prokládejte více datových disků Azure, abyste získali větší propustnost úložiště.<br/><br/> - Formát s dokumentovanými velikostmi přidělení. <br/><br/> - Umístěte TempDB na místní `D:\` jednotku SSD pro kritické úlohy SQL Serveru (po výběru správné velikosti virtuálního počítače). Pokud vytvoříte virtuální počítač z portálu Azure nebo azure quickstart šablony a [umístit Temp DB na místní disk](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583) pak nepotřebujete žádné další akce; pro všechny ostatní případy postupujte podle kroků v blogu pro [použití SSD pro ukládání TempDB,](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) aby se zabránilo selhání po restartování. Pokud kapacita místní jednotky není dostatečná pro velikost temp db, umístěte teplotu DB na fond úložiště [odebraný](../premium-storage-performance.md) na disky s prémiovým disky SSD s [ukládáním do mezipaměti jen pro čtení](../premium-storage-performance.md#disk-caching). |
| [I/O](#io-guidance) |- Povolit kompresi stránky databáze.<br/><br/> - Povolit okamžitou inicializaci souborů pro datové soubory.<br/><br/> - Omezte automatické růst databáze.<br/><br/> - Zakázat automatické zmenšení databáze.<br/><br/> - Přesunout všechny databáze na datové disky, včetně systémových databází.<br/><br/> - Přesuňte protokol chyb serveru SQL Server a adresáře trasovacích souborů na datové disky.<br/><br/> - Nakonfigurujte výchozí umístění záloh a databázových souborů.<br/><br/> - [Povolte zamknuté stránky v paměti](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows?view=sql-server-2017).<br/><br/> - Použít opravy výkonu serveru SQL Server. |
| [Specifické pro funkce](#feature-specific-guidance) | - Zálohovat přímo do úložiště objektů blob.<br/><br/>- Použijte [zálohy snímků souborů](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) pro databáze větší než 12 TB. <br/><br/>- Použijte více souborů Temp DB, 1 soubor na jádro, až 8 souborů.<br/><br/>- Nastavte maximální paměť serveru na 90% nebo až 50 GB vlevo pro operační systém. <br/><br/>- Povolit měkké NUMA. |

Další informace o tom, *jak* a *proč* provádět tyto optimalizace, naleznete podrobnosti a pokyny uvedené v následujících částech.

## <a name="vm-size-guidance"></a>Pokyny pro velikost virtuálního počítače

Začněte shromažďováním požadavků na propustnost procesoru, paměti a úložiště zatížení v době špičky. \LogicalDisk\Disk Reads/Sec a \LogicalDisk\Disk Zápisy/s výkončíčí čítače lze použít ke shromažďování požadavků na čtení a zápis VOPS a \LogicalDisk\Disk Bajtů/s čítač lze použít ke shromažďování [požadavků na propustnost úložiště](../premium-storage-performance.md#disk-caching) pro soubory Data, Log a Temp DB. Po VOPS a požadavky na propustnost ve špičce jsou definovány pak vyhodnotit velikosti virtuálních počítače nabízí tuto kapacitu. Například pokud vaše úloha vyžaduje 20 K číst IOPS a 10 K zápis VOPS ve špičce, můžete buď zvolit E16s_v3 (s až 32 K cache a 25600 uncached IOPS) nebo M16_s (s až 20 K cache daps a 10K uncached IOPS) s 2 P30 disky. Ujistěte se, že rozumíte požadavkům na propustnost i vstupně-up zatížení, protože virtuální virtuální paměť má různá omezení škálování pro vstupně-up a propustnost.<br/><br/>[DSv_3](../../dv3-dsv3-series.md) a [Es_v3 jsou](../../ev3-esv3-series.md) hostovány na hardwaru pro všeobecné použití s procesory Intel Haswell nebo Broadwell. [Řada M](../../m-series.md) nabízí nejvyšší počet virtuálních procesorů a paměť pro největší úlohy serveru SQL Server a hostovaná na hardwaru optimalizovaném pro paměť s řadou procesorů Skylake. Tyto řady virtuálních her podporují úložiště premium, které se doporučuje pro nejlepší výkon s mezipamětí pro čtení na úrovni hostitele. Řada Es_v3 i M jsou k dispozici také v [omezených velikostech jádra](../../windows/constrained-vcpu.md), což šetří peníze na úlohy s nižšími výpočetními a vysokými nároky na kapacitu úložiště. 

## <a name="storage-guidance"></a>Pokyny k ukládání

Podrobné testování výkonu SERVERU SQL Server na virtuálních počítačích Azure s tpc-e a TPC_C benchmarky najdete v blogu [Optimalizace výkonu OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). 

Azure blob cache s prémiovými ssd disy se doporučuje pro všechny produkční úlohy. 

> [!WARNING]
> Standardní pevné disky a ssd disky mají různé latence a šířku pásma a jsou doporučeny pouze pro úlohy pro vývoj a testování. Produkční úlohy by měly používat prémiové ssd d.s.

Kromě toho doporučujeme vytvořit účet úložiště Azure ve stejném datovém centru jako vaše virtuální počítače SQL Server, abyste snížili zpoždění přenosu. Při vytváření účtu úložiště, zakázat geografické replikace jako konzistentní pořadí zápisu na více disků není zaručena. Místo toho zvažte konfiguraci technologie zotavení po havárii serveru SQL Server mezi dvěma datovými centry Azure. Další informace najdete v tématu [vysoká dostupnost a zotavení po havárii pro SQL Server ve virtuálních počítačích Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Navádění k diskům

Na virtuálním počítači Azure existují tři hlavní typy disků:

* **Disk operačního systému:** Při vytváření virtuálního počítače Azure platforma připojí k virtuálnímu počítači pro váš disk operačního systému alespoň jeden disk (označený jako jednotka **C).** Tento disk je virtuální pevný disk uložený jako objekt blob stránky v úložišti.
* **Dočasný disk**: Virtuální počítače Azure obsahují jiný disk nazývaný dočasný disk (označený jako **Jednotka D).** Jedná se o disk na uzlu, který lze použít pro odkládací místo.
* **Datové disky**: Další disky můžete k virtuálnímu počítači připojit také jako datové disky a ty se uloží do úložiště jako objekty BLOB stránky.

Následující části popisují doporučení pro použití těchto různých disků.

### <a name="operating-system-disk"></a>Disk operačním systému

Disk operačního systému je virtuální pevný disk, který můžete spustit a připojit jako spuštěnou verzi operačního systému a je označen jako jednotka **C.**

Výchozí zásadou ukládání do mezipaměti na disku operačního systému je **čtení a zápis**. U aplikací citlivých na výkon doporučujeme použít datové disky namísto disku operačního systému. Viz část o datových discích níže.

### <a name="temporary-disk"></a>Dočasný disk

Jednotka dočasného úložiště, označená jako jednotka **D,** není trvalá na úložišti objektů blob Azure. Neukládejte soubory uživatelské databáze nebo soubory protokolu uživatelských transakcí na jednotku **D**: .

Umístěte TempDB na místní `D:\` jednotku SSD pro kritické úlohy SQL Serveru (po výběru správné velikosti virtuálního počítače). Pokud vytvoříte virtuální počítač z portálu Azure nebo azure quickstart šablony a [umístit Temp DB na místní disk](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583), pak nepotřebujete žádné další akce; pro všechny ostatní případy postupujte podle kroků v blogu pro [použití SSD pro ukládání TempDB,](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) aby se zabránilo selhání po restartování. Pokud kapacita místní jednotky není dostatečná pro velikost temp db, umístěte teplotu DB na fond úložiště [odebraný](../premium-storage-performance.md) na disky s prémiovým disky SSD s [ukládáním do mezipaměti jen pro čtení](../premium-storage-performance.md#disk-caching).

U virtuálních počítačů, které podporují prémiové disky SSD, můžete také ukládat tempdb na disk, který podporuje prémiové disky SSD s povoleným ukládáním do mezipaměti čtení.


### <a name="data-disks"></a>Datové disky

* **Použití disků Premium SSD pro datové soubory a soubory protokolu**: Pokud nepoužíváte prokládání disků, použijte dva disky s disky premium SSD, kde jeden disk obsahuje soubor protokolu a druhý obsahuje data. Každý premium SSD poskytuje řadu VOPS a šířku pásma (MB/s) v závislosti na jeho velikosti, jak je znázorněno v článku [Vyberte typ disku](../disks-types.md). Pokud používáte techniku prokládání disků, například prostory úložiště, dosáhnete optimálního výkonu tím, že budete mít dva fondy, jeden pro soubory protokolu a druhý pro datové soubory. Pokud však plánujete použít instance clusteru s podporou převzetí služeb při selhání serveru SQL Server (FCI), musíte nakonfigurovat jeden fond nebo místo toho použít [sdílené složky premium.](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md)

   > [!TIP]
   > - Výsledky testů na různých konfiguracích disku a pracovního vytížení najdete v následujícím příspěvku blogu: [Pokyny pro konfiguraci úložiště pro SQL Server na virtuálním počítači Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/).
   > - Pro kritický výkon pro sql servery, které potřebují ~ 50 000 VOPS, zvažte nahrazení 10-P30 disků ultra ssd. Další informace naleznete v následujícím příspěvku blogu: [Kritický výkon s Ultra SSD](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Při zřizování virtuálního počítače SQL Server na portálu máte možnost upravit konfiguraci úložiště. V závislosti na konfiguraci Azure nakonfiguruje jeden nebo více disků. Více disků jsou kombinovány do jednoho fondu úložiště s prokládání. V této konfiguraci jsou data i soubory protokolu umístěny společně. Další informace naleznete v [tématu Konfigurace úložiště pro virtuální počítače SQL Server](virtual-machines-windows-sql-server-storage-configuration.md).

* **Prokládání disků**: Pro větší propustnost můžete přidat další datové disky a použít prokládání disků. Chcete-li zjistit počet datových disků, je třeba analyzovat počet vodících ops a šířku pásma požadovanou pro soubory protokolu a pro data a soubory TempDB. Všimněte si, že různé velikosti virtuálních počítačen mají různá omezení počtu vipů a šířky pásma podporované, viz tabulky na VOPS na [velikost virtuálního počítače](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Postupujte takto:

  * Pro Windows 8/Windows Server 2012 nebo novější použijte [prostory úložiště](https://technet.microsoft.com/library/hh831739.aspx) s následujícími pokyny:

      1. Nastavte prokládání (velikost prokládání) na 64 KB (65 536 bajtů) pro úlohy OLTP a 256 kB (262 144 bajtů) pro úlohy ukládání dat, aby se zabránilo dopadu na výkon z důvodu nesouososti oddílů. To musí být nastavena pomocí prostředí PowerShell.
      2. Nastavte počet sloupců = počet fyzických disků. Při konfiguraci více než 8 disků (ne u uj. Správce serveru) použijte prostředí PowerShell. 

    Například následující Prostředí PowerShell vytvoří nový fond úložiště s velikostí prokládání na 64 kB a počtem sloupců na 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Pro systém Windows 2008 R2 nebo starší můžete použít dynamické disky (prokládané svazky operačního systému) a velikost proklápěcí je vždy 64 kB. Tato možnost je od Windows 8/Windows Server 2012 zastaralá. Další informace naleznete v prohlášení o podpoře ve [službě Virtual Disk Service, která přechází na rozhraní API pro správu úložiště systému Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Pokud používáte [storage spaces direct (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) s [instancemi clusteru s podporou převzetí služeb při selhání serveru SQL Server](virtual-machines-windows-portal-sql-create-failover-cluster.md), je nutné nakonfigurovat jeden fond. Přestože různé svazky mohou být vytvořeny v tomto jednom fondu, budou všechny mít stejné charakteristiky, jako je například stejné zásady ukládání do mezipaměti.

  * Určete počet disků přidružených k fondu úložiště na základě očekávání zatížení. Mějte na paměti, že různé velikosti virtuálních počítačů umožňují různé počty připojených datových disků. Další informace naleznete v [tématu Velikosti pro virtuální počítače](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Pokud nepoužíváte disky SSD pro premium (scénáře vývoj/testování), doporučujeme přidat maximální počet datových disků podporovaných [velikostí virtuálního počítače](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a použít prokládání disků.

* **Zásady ukládání do mezipaměti**: Všimněte si následujících doporučení pro zásady ukládání do mezipaměti v závislosti na konfiguraci úložiště.

  * Pokud používáte samostatné disky pro data a soubory protokolu, povolte ukládání do mezipaměti na datových discích hostujících datové soubory a datové soubory TempDB. To může mít za následek významné výhody výkonu. Nepovolujte ukládání do mezipaměti na disku, který obsahuje soubor protokolu, protože to způsobuje menší snížení výkonu.

  * Pokud používáte prokládání disků v jednom fondu úložiště, většina úloh bude mít prospěch z ukládání do mezipaměti čtení. Pokud máte samostatné fondy úložiště pro protokol a datové soubory, povolte ukládání do mezipaměti pouze ve fondu úložiště pro datové soubory. V některých úlohách náročné zápisu může být dosaženo lepšího výkonu bez ukládání do mezipaměti. To lze určit pouze testováním.

  * Předchozí doporučení se vztahují na prémiové SSD. Pokud nepoužíváte prémiové disky SSD, nepovolujte ukládání do mezipaměti na žádných datových discích.

  * Pokyny ke konfiguraci ukládání disku do mezipaměti naleznete v následujících článcích. Klasický model nasazení (ASM) viz: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) a [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Model nasazení Azure Resource Manager uvidíme: [Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) a [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Při změně nastavení mezipaměti disků virtuálních počítačů Azure zastavte službu SQL Server, abyste se vyhnuli možnosti poškození databáze.

* **Velikost alokační jednotky NTFS**: Při formátování datového disku se doporučuje použít velikost alokační jednotky 64 KB pro datové a protokolové soubory a také pro funkci TempDB. Pokud je na dočasný disk umístěn a umístěn dočasnou databázi TempDB (D:\ pohonu) výkon získaný využitím tohoto disku převažuje nad potřebou velikosti alokační jednotky 64 K. 

* **Osvědčené postupy pro správu disků**: Při odebírání datového disku nebo změně typu mezipaměti zastavte během změny službu SQL Server. Když se změní nastavení ukládání do mezipaměti na disku s os, Azure zastaví virtuální počítač, změní typ mezipaměti a restartuje virtuální počítač. Při změně nastavení mezipaměti datového disku není virtuální modul zastaven, ale datový disk je odpojen od virtuálního počítače během změny a znovu připojen.

  > [!WARNING]
  > Selhání zastavení služby SQL Server během těchto operací může způsobit poškození databáze.


## <a name="io-guidance"></a>I/O pokyny

* Nejlepších výsledků s prémiovými SSD disteři je dosaženo při paralelizaci vaší aplikace a požadavků. Ssd s ssd s úrovněmi Premium jsou určeny pro scénáře, kde je hloubka fronty vi větší než 1, takže u vás uvidíte malé nebo žádné zvýšení výkonu pro sériové požadavky s jedním podprocesem (i když jsou náročné na úložiště). Například to může mít vliv na výsledky testů s jedním podprocesem nástroje pro analýzu výkonu, jako je například SQLIO.

* Zvažte použití [komprese stránky databáze,](https://msdn.microsoft.com/library/cc280449.aspx) protože může pomoci zlepšit výkon vstupně-v intenzivní úlohy. Komprese dat však může zvýšit spotřebu procesoru na databázovém serveru.

* Zvažte povolení okamžité inicializace souboru, abyste zkrátili čas potřebný pro počáteční přidělení souboru. Chcete-li využít výhod okamžité inicializace souborů, udělte účtu služby SQL Server (MSSQLSERVER) SE_MANAGE_VOLUME_NAME a přidejte jej do zásadzabezpečení **Provádět úlohy hromadné údržby.** Pokud používáte image platformy SQL Server pro Azure, výchozí účet služby (NT Service\MSSQLSERVER) není přidán do zásady zabezpečení **Provádět úlohy hromadné údržby.** Jinými slovy okamžité inicializace souboru není povolena v image platformy SQL Server Azure. Po přidání účtu služby SQL Server do zásady zabezpečení **Provádět úlohy hromadné údržby** restartujte službu SQL Server. Použití této funkce může obsahovat důležité informace o zabezpečení. Další informace naleznete v [tématu Inicializace souboru databáze](https://msdn.microsoft.com/library/ms175935.aspx).

* **autogrow** je považován za pouze nepředvídané události pro neočekávaný růst. Nespravujte data a protokolujte růst na každodenní bázi s autogrow. Pokud se používá autogrow, pre-grow soubor pomocí přepínač velikosti.

* Ujistěte se, že **autoshrink** je zakázáno, aby se zabránilo zbytečné režie, které mohou negativně ovlivnit výkon.

* Přesuňte všechny databáze na datové disky, včetně systémových databází. Další informace naleznete v tématu [Přesunutí systémových databází](https://msdn.microsoft.com/library/ms345408.aspx).

* Přesuňte protokol chyb serveru SQL Server a adresáře souborů trasování na datové disky. To lze provést ve správci konfigurace serveru SQL Server klepnutím pravým tlačítkem myši na instanci serveru SQL Server a výběrem vlastností. Nastavení protokolu chyb a trasovacího souboru lze změnit na kartě **Parametry spuštění.** Adresář výpisu je určen na kartě **Upřesnit.** Následující snímek obrazovky ukazuje, kde hledat parametr spuštění protokolu chyb.

    ![Snímek obrazovky protokolu errorlog SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Nastavte výchozí umístění záloh a souborů databáze. Použijte doporučení v tomto článku a proveďte změny v okně vlastností serveru. Pokyny naleznete v [tématu Zobrazení nebo změna výchozích umístění pro datové soubory a soubory protokolu (SQL Server Management Studio).](https://msdn.microsoft.com/library/dd206993.aspx) Následující snímek obrazovky ukazuje, kde tyto změny provést.

    ![Soubory protokolu dat sql a zálohování](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Povolte uzamčené stránky, abyste snížili vatoa a všechny aktivity stránkování. Další informace naleznete [v tématu Povolení možnosti zamknout stránky v paměti (Windows).](https://msdn.microsoft.com/library/ms190730.aspx)

* Pokud používáte SQL Server 2012, nainstalujte aktualizaci Service Pack 1 Kumulativní aktualizace 10. Tato aktualizace obsahuje opravu pro nízký výkon na vstupně-va při spuštění select do dočasné tabulky příkazu v SQL Server 2012. Další informace naleznete v tomto [článku znalostní báze Knowledge Base](https://support.microsoft.com/kb/2958012).

* Při přenosu do a z Azure zvažte kompresi všech datových souborů.

## <a name="feature-specific-guidance"></a>Pokyny pro konkrétní funkce

Některá nasazení mohou dosáhnout dalších výhod výkonu pomocí pokročilejších konfiguračních technik. Následující seznam upozorňuje na některé funkce serveru SQL Server, které vám mohou pomoci dosáhnout lepšího výkonu:

### <a name="back-up-to-azure-storage"></a>Zálohování do Azure Storage
Při zálohování pro SQL Server spuštěné ve virtuálních počítačích Azure můžete použít [SQL Server Backup na adresu URL](https://msdn.microsoft.com/library/dn435916.aspx). Tato funkce je k dispozici od SQL Server 2012 SP1 CU2 a doporučuje se pro zálohování na připojené datové disky. Při zálohování nebo obnovení úložiště Azure postupujte podle doporučení poskytnutých na [webu SQL Server Backup na doporučené postupy pro adresy URL a řešení potíží a obnovení ze záloh uložených ve službě Azure Storage](https://msdn.microsoft.com/library/jj919149.aspx). Tyto zálohy můžete také automatizovat pomocí [automatického zálohování pro SQL Server ve virtuálních počítačích Azure](virtual-machines-windows-sql-automated-backup.md).

Před SQL Server 2012 můžete použít [SQL Server Backup na Azure Tool](https://www.microsoft.com/download/details.aspx?id=40740). Tento nástroj může pomoci zvýšit propustnost zálohování pomocí více cílů proklápěcí zálohování.

### <a name="sql-server-data-files-in-azure"></a>Datové soubory SQL Serveru v Azure

Tato nová [funkce, SQL Server datové soubory v Azure](https://msdn.microsoft.com/library/dn385720.aspx), je k dispozici od SQL Server 2014. Spuštění SQL Serveru s datovými soubory v Azure ukazuje srovnatelné charakteristiky výkonu jako použití datových disků Azure.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Instance clusteru s podporou převzetí služeb při selhání a prostory úložiště

Pokud používáte prostory úložiště, zrušte při přidávání uzlů do clusteru na stránce **Potvrzení** zaškrtnutí políčka **Přidat do clusteru všechna způsobilá úložiště**. 

![Zrušení zaškrtnutí způsobilého úložiště](media/virtual-machines-windows-sql-performance/uncheck-eligible-cluster-storage.png)

Pokud používáte prostory úložiště a neodškrtejte **políčko Přidat do clusteru všechna způsobilá úložiště**, systém Windows odpojí virtuální disky během procesu clusteringu. V důsledku toho se nezobrazují ve Správci disků nebo v Průzkumníkovi, dokud nebudou prostory úložiště odebrány z clusteru a znovu připojeny pomocí prostředí PowerShell. Prostory úložiště seskupí více disků do fondů úložiště. Další informace naleznete [v tématu Storage Spaces](/windows-server/storage/storage-spaces/overview).

## <a name="next-steps"></a>Další kroky

Další informace o úložišti a výkonu najdete v [tématu Pokyny pro konfiguraci úložiště pro SQL Server na virtuálním počítači Azure.](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

Doporučené postupy zabezpečení najdete [v tématu Důležité informace o zabezpečení pro SQL Server ve virtuálních počítačích Azure](virtual-machines-windows-sql-security.md).

Prohlédněte si další články virtuálního počítače sql serveru na [SQL Serveru ve virtuálních počítačích Azure Přehled](virtual-machines-windows-sql-server-iaas-overview.md). Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md).
