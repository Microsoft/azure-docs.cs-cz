---
title: Pokyny k výkonu pro SQL Server v Azure | Microsoft Docs
description: Poskytuje pokyny pro optimalizaci výkonu SQL Server v Microsoft Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
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
ms.openlocfilehash: 9abc6574117b194a626c2697f5297a13566e0447
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89481786"
---
# <a name="performance-guidelines-for-sql-server-on-azure-virtual-machines"></a>Průvodce výkonem SQL Serveru ve službě Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek poskytuje pokyny pro optimalizaci SQL Serverho výkonu v Microsoft Azure Virtual Machines.

## <a name="overview"></a>Přehled

 Při spouštění SQL Server v Azure Virtual Machines doporučujeme, abyste dál používali stejné možnosti optimalizace výkonu databáze, které platí pro SQL Server v místních serverových prostředích. Výkon relační databáze ve veřejném cloudu však závisí na řadě faktorů, jako je velikost virtuálního počítače nebo konfigurace datových disků.

[SQL Server Image zřízené v Azure Portal](sql-vm-create-portal-quickstart.md) postupují v části Obecné doporučené postupy konfigurace úložiště (Další informace o tom, jak je úložiště nakonfigurované) najdete v tématu [konfigurace úložiště pro SQL Server virtuální počítače (VM)](storage-configuration.md). Po zřízení zvažte použití dalších optimalizací popsaných v tomto článku. Založte své možnosti na vaše úlohy a ověřte je prostřednictvím testování.

> [!TIP]
> Mezi optimalizací a optimalizací výkonu je typicky kompromis. Tento článek se zaměřuje na získání *nejlepšího* výkonu pro SQL Server v Azure Virtual Machines. Pokud vaše úloha je méně náročná, možná nebudete potřebovat každou níže uvedenou optimalizaci. Při hodnocení těchto doporučení Vezměte v úvahu požadavky na výkon, náklady a vzory úloh.

## <a name="quick-checklist"></a>Rychlý kontrolní seznam

Následuje rychlý kontrolní seznam pro optimální výkon SQL Server v Azure Virtual Machines:

| Oblast | Optimalizace |
| --- | --- |
| [Velikost virtuálního počítače](#vm-size-guidance) | – Používejte velikosti virtuálních počítačů 4 nebo více vCPU, jako je [E4S_v3](../../../virtual-machines/ev3-esv3-series.md) nebo vyšší nebo [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md) nebo vyšší.<br/><br/> - [Řada ES, EAS, DS a Das](../../../virtual-machines/sizes-general.md) nabízí optimální vCPU poměr paměti nutný pro výkon úloh OLTP. <br/><br/> - [Řada M](../../../virtual-machines/m-series.md) nabízí nejvyšší vCPU poměr paměti nutný k zajištění kritického výkonu a je ideální pro úlohy datového skladu. <br/><br/> – Pomocí [kontrolního seznamu požadavků na výkon aplikace](../../../virtual-machines/windows/premium-storage-performance.md#application-performance-requirements-checklist) Shromážděte požadavky na [vstupně](../../../virtual-machines/windows/premium-storage-performance.md#iops)-výstupní operace cílového zatížení, [propustnost](../../../virtual-machines/windows/premium-storage-performance.md#throughput) a [latence](../../../virtual-machines/windows/premium-storage-performance.md#latency) v době špičky a pak vyberte [Velikost virtuálního počítače](../../../virtual-machines/sizes-general.md) , která se může škálovat na požadavky výkonu vaší úlohy.|
| [Storage](#storage-guidance) | – Podrobné testování výkonu SQL Server v Azure Virtual Machines pomocí srovnávacích testů TPC-E a TPC_C najdete v blogu věnovaném [optimalizaci výkonu OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). <br/><br/> – Použijte [prémiové SSD](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) pro nejlepší ceny a výkonnostní výhody. Nakonfigurujte [mezipaměť jen pro čtení](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching) pro datové soubory a žádnou mezipaměť pro soubor protokolu. <br/><br/> – Použijte [disky Ultra](../../../virtual-machines/disks-types.md#ultra-disk) , pokud zatížení vyžaduje méně než 1 MS úložiště. Další informace najdete v tématu [migrace na disk s Ultra](storage-migrate-to-ultradisk.md) . <br/><br/> – Shromažďování požadavků na latenci úložiště pro soubory SQL Server dat, protokolů a dočasné databáze [monitorováním aplikace](../../../virtual-machines/windows/premium-storage-performance.md#application-performance-requirements-checklist) před volbou typu disku. Pokud se vyžadují <latence úložiště 1 ms, použijte Ultra disks, v opačném případě použijte disk SSD úrovně Premium. Pokud se pro soubor protokolu a ne pro datové soubory vyžadují nízké latence, [zajistěte, aby byl Ultra disk](../../../virtual-machines/disks-enable-ultra-ssd.md) v požadovaném IOPS a úrovně propustnosti jenom pro soubor protokolu. <br/><br/> -  Pro SQL Server instanci clusteru s podporou převzetí služeb při selhání se u [souborů úrovně Premium](failover-cluster-instance-premium-file-share-manually-configure.md) doporučuje sdílené úložiště. Soubory úrovně Premium nepodporují ukládání do mezipaměti a nabízejí omezený výkon v porovnání s disky SSD úrovně Premium. Pro samostatné instance SQL vyberte na discích úrovně Premium disky spravované přes prémiové sdílené složky. ale Využijte prémiové sdílené složky pro sdílené úložiště instance clusteru s podporou převzetí služeb při selhání, aby se usnadnila údržba a flexibilní škálovatelnost. <br/><br/> – Standardní úložiště se doporučuje jenom pro účely vývoje a testování nebo pro záložní soubory a neměla by se používat pro produkční úlohy. <br/><br/> – Udržujte [účet úložiště](../../../storage/common/storage-create-storage-account.md) a SQL Server virtuální počítač ve stejné oblasti.<br/><br/> – Zakažte v účtu úložiště [geograficky redundantní úložiště](../../../storage/common/storage-redundancy.md) Azure (geografickou replikaci).  |
| [Disky](#disks-guidance) | – Použijte minimálně 2 [disky SSD úrovně Premium](../../../virtual-machines/disks-types.md#premium-ssd) (1 pro soubor protokolu a 1 pro datové soubory). <br/><br/> – Pro úlohy, které vyžadují <1 MS/v v/v, povolte akcelerátor zápisu pro řady M a zvažte použití SSD úrovně Ultra disků pro řady ES a DS. <br/><br/> -Povolit [ukládání do mezipaměti jen pro čtení](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching) na discích, které hostují datové soubory.<br/><br/> – Přidání dalších 20% kapacity IOPS/propustnosti, než kolik jich vyžaduje při [konfiguraci úložiště pro SQL Server dat, protokolů a souborů tempdb](storage-configuration.md) <br/><br/> – Nepoužívejte operační systém nebo dočasné disky pro úložiště databáze nebo protokolování.<br/><br/> – Nepovolujte ukládání do mezipaměti na discích hostujících soubor protokolu.  **Důležité**: při změně nastavení mezipaměti pro disk Azure Virtual Machines zastavte službu SQL Server.<br/><br/> – Proložením několika datových disků Azure získáte vyšší propustnost úložiště.<br/><br/> – Formát s dokumentovanými velikostmi přidělení. <br/><br/> Na místní jednotku SSD umístěte databázi TempDB `D:\` pro klíčové SQL Server úlohy (po výběru správné velikosti virtuálního počítače). Pokud vytvoříte virtuální počítač z Azure Portal nebo šablon Azure pro rychlý Start a [umístíte dočasnou databázi na místní disk](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583) , pak nebudete potřebovat žádnou další akci. pro všechny ostatní případy postupujte podle pokynů v blogu pro  [použití SSD k uložení databáze tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) , aby nedocházelo k chybám po restartování. Pokud kapacita místního disku není dostatečná pro velikost dočasné databáze, umístěte dočasnou databázi [do fondu úložiště](../../../virtual-machines/windows/premium-storage-performance.md) , který je umístěn na discích SSD úrovně Premium s [ukládáním do mezipaměti jen pro čtení](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching). |
| [I/O](#io-guidance) |-Povolit kompresi stránky databáze.<br/><br/> – Povolí okamžitou inicializaci souborů pro datové soubory.<br/><br/> – Omezuje automatické zvětšování databáze.<br/><br/> -Zakázat automatického zmenšení databáze.<br/><br/> – Přesuňte všechny databáze na datové disky, včetně systémových databází.<br/><br/> – SQL Server přesunutí adresářů protokolů chyb a trasovacích souborů do datových disků.<br/><br/> – Nakonfigurujte výchozí zálohu a umístění souborů databáze.<br/><br/> - [Povolí uzamčené stránky v paměti](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows?view=sql-server-2017).<br/><br/> – Použijte opravy výkonu SQL Server. |
| [Specifické pro jednotlivé funkce](#feature-specific-guidance) | – Zálohování přímo do Azure Blob Storage.<br/><br/>– Použijte [zálohy snímků souborů](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) pro databáze větší než 12 TB. <br/><br/>– Použijte více souborů dočasné databáze, 1 soubor na jádro a až 8 souborů.<br/><br/>-Nastavit maximální velikost paměti serveru v 90% nebo až 50 GB zbývajících pro operační systém. <br/><br/>– Povolte měkký NUMA. |

Další informace o *tom, jak* a *Proč* provádět tyto optimalizace, najdete v podrobnostech a pokynech uvedených v následujících částech.

## <a name="vm-size-guidance"></a>Pokyny pro velikost virtuálního počítače

Začněte shromažďováním požadavků na propustnost procesoru, paměti a úložiště v časech špičky. Čítače výkonu \LogicalDisk\Disk čtení/s a \LogicalDisk\Disk zápisy na disk/s se dají použít ke shromažďování požadavků na čtení a zápis IOPS a čítač \LogicalDisk\Disk bajtů/s se dá použít ke shromažďování [požadavků na propustnost úložiště](../../../virtual-machines/premium-storage-performance.md#disk-caching) pro soubory dat, protokolů a dočasné databáze. Po definování požadavků na vstupně-výstupní operace a propustnost ve špičce jsou vyhodnoceny velikosti virtuálních počítačů, které tuto kapacitu nabízí. Pokud například vaše úloha vyžaduje 20 000 vstupně-výstupních vstupně-výstupních operací a 10 000 vstupně-výstupních operací zápisu ve špičce, můžete buď vybrat možnost E16s_v3 (s až 32 KB a 25600 neuložených vstupně-výstupních operací), nebo M16_s (s až 20 KB a 10 000 IOPS) a 2 P30 disky. Ujistěte se, že rozumíte požadavkům na propustnost a IOPS zatížení, protože virtuální počítače mají jiné limity škálování pro vstupně-výstupní operace a propustnost.<br/><br/>[DSv_3](../../../virtual-machines/dv3-dsv3-series.md) a [Es_v3-Series](../../../virtual-machines/ev3-esv3-series.md) se hostují na hardware pro obecné účely s procesory Intel Haswell nebo Broadwell. [Řada M-Series](../../../virtual-machines/m-series.md) nabízí nejvyšší počet vCPU a paměť pro největší SQL Server zatížení a je hostovaný na paměťově optimalizovaném hardwaru s využitím řady procesorů Skylake. Tyto řady virtuálních počítačů podporují Prémiové úložiště, což se doporučuje pro nejlepší výkon s mezipamětí pro čtení na úrovni hostitele. Řady Es_v3 i M jsou také dostupné v [omezených základních velikostech](../../../virtual-machines/constrained-vcpu.md), což šetří peníze pro úlohy s nižšími nároky na výpočetní výkon a vysokou kapacitu úložiště. 

## <a name="storage-guidance"></a>Pokyny k ukládání

Podrobné testování výkonu SQL Server v Azure Virtual Machines pomocí srovnávacích testů TPC-E a TPC_C najdete v blogu věnovaném [optimalizaci výkonu OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). 

Azure Blob cache s Premium SSD se doporučuje pro všechny produkční úlohy. 

> [!WARNING]
> Standardní HDD a SSD mají různou latenci a šířku pásma a jsou doporučovány jenom pro úlohy pro vývoj a testování. Produkční úlohy by měly používat Premium SSD.

Kromě toho doporučujeme vytvořit účet služby Azure Storage ve stejném datovém centru jako virtuální počítače s SQL Server, abyste snížili zpoždění přenosu. Při vytváření účtu úložiště není zaručená geografická replikace jako konzistentní pořadí zápisu na více discích. Místo toho zvažte konfiguraci SQL Server technologie zotavení po havárii mezi dvěma datovými centry Azure. Další informace najdete v tématu [Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="disks-guidance"></a>Doprovodné materiály pro disky

Na virtuálních počítačích Azure existují tři hlavní typy disků:

* **Disk s operačním**systémem: Když vytváříte virtuální počítač Azure, bude platforma k virtuálnímu počítači pro disk s operačním systémem připojovat aspoň jeden disk (označený jako jednotka **C** ). Tento disk je virtuální pevný disk uložený jako objekt blob stránky v úložišti.
* **Dočasný disk**: virtuální počítače Azure obsahují další disk nazvaný dočasný disk (označený jako jednotka **D**:). Toto je disk na uzlu, který lze použít pro pomocné místo.
* **Datové disky**: k virtuálnímu počítači můžete připojit také další disky jako datové disky a ty budou uloženy v úložišti jako objekty blob stránky.

Následující části popisují doporučení pro používání těchto různých disků.

### <a name="operating-system-disk"></a>Disk operačním systému

Disk s operačním systémem je virtuální pevný disk, který můžete spustit a připojit jako běžící verzi operačního systému a který je označený jako jednotka **C** .

Výchozí zásady ukládání do mezipaměti na disku s operačním systémem jsou **jen pro čtení a zápis**. Pro aplikace s citlivým výkonem doporučujeme místo disku operačního systému používat datové disky. Viz část na datových discích níže.

### <a name="temporary-disk"></a>Dočasný disk

Dočasná Úložná jednotka označená jako jednotka **D** není trvale nastavená na službu Azure Blob Storage. Neukládejte soubory uživatelské databáze ani soubory protokolů transakcí uživatele na jednotce **D**:.

Pro nejdůležitější SQL Server úlohy umístěte databázi TempDB na místní `D:\` jednotku SSD (po výběru správné velikosti virtuálního počítače). Pokud vytvoříte virtuální počítač z Azure Portal nebo šablon Azure pro rychlý Start a [umístíte dočasnou databázi na místní disk](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583), pak nebudete potřebovat žádnou další akci. pro všechny ostatní případy postupujte podle pokynů v blogu pro  [použití SSD k uložení databáze tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) , aby nedocházelo k chybám po restartování. Pokud kapacita místního disku není dostatečná pro velikost dočasné databáze, umístěte dočasnou databázi [do fondu úložiště](../../../virtual-machines/premium-storage-performance.md) , který je umístěn na discích SSD úrovně Premium s [ukládáním do mezipaměti jen pro čtení](../../../virtual-machines/premium-storage-performance.md#disk-caching).

Pro virtuální počítače, které podporují prémiové SSD, můžete databázi TempDB taky ukládat na disk, který podporuje Premium SSD s povoleným ukládáním do mezipaměti pro čtení.


### <a name="data-disks"></a>Datové disky

* **Použít disky SSD úrovně Premium pro data a soubory protokolu**: Pokud nepoužíváte diskové svazky, použijte dva disky SSD úrovně Premium, kde jeden disk obsahuje soubor protokolu a druhý obsahuje data. Každý disk SSD úrovně Premium poskytuje množství vstupně-výstupních operací a šířky pásma (MB/s) v závislosti na velikosti, jak je znázorněno v článku, a [Vyberte typ disku](../../../virtual-machines/disks-types.md). Pokud používáte techniku rozložení disku, například prostory úložiště, dosáhnete optimálního výkonu tím, že máte dva fondy, jeden pro soubory protokolu a druhý pro datové soubory. Pokud ale plánujete použít SQL Server instance clusterů s podporou převzetí služeb při selhání (FCI), musíte místo toho nakonfigurovat jeden fond nebo využít [prémiové sdílené soubory](failover-cluster-instance-premium-file-share-manually-configure.md) .

   > [!TIP]
   > - Výsledky testů různých konfigurací disků a úloh najdete v tomto blogovém příspěvku: [pokyny pro konfiguraci úložiště pro SQL Server v Azure Virtual Machines](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/).
   > - Pro zajištění důležitého výkonu pro SQL servery, které potřebují ~ 50 000 IOPS, zvažte nahrazení 10 P30 disků pomocí SSD úrovně Ultra. Další informace najdete v tomto blogovém příspěvku: [rozhodující výkon s SSD úrovně Ultra](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Když zřizujete SQL Server virtuální počítač na portálu, máte možnost upravit si konfiguraci úložiště. V závislosti na konfiguraci Azure nakonfiguruje jeden nebo víc disků. Více disků je sloučeno do jednoho fondu úložiště s použitím Stripe. Data i soubory protokolů se v této konfiguraci nacházejí společně. Další informace najdete v tématu [Konfigurace úložiště pro virtuální počítače s SQL Server](storage-configuration.md).

* **Diskové svazky**: pro větší propustnost můžete přidat další datové disky a použít diskové svazky. Chcete-li zjistit počet datových disků, je nutné analyzovat počet vstupně-výstupních operací a šířky pásma požadované pro soubory protokolu a pro vaše data a soubory databáze TempDB. Všimněte si, že různé velikosti virtuálních počítačů mají různá omezení počtu vstupně-výstupních operací za sekundu a šířky pásma, viz tabulky v IOPS podle [velikosti virtuálního počítače](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Postupujte takto:

  * U systémů Windows 8/Windows Server 2012 a novějších používejte [prostory úložiště](https://technet.microsoft.com/library/hh831739.aspx) s následujícími pokyny:

      1. Nastavte prokládání (velikost Stripe) na 64 KB (65 536 bajtů) pro úlohy OLTP a 256 KB (262 144 bajty) pro úlohy datového skladu, aby se předešlo dopadu na výkon v důsledku chybného zarovnání oddílu. Tato nastavení se musí nastavit pomocí PowerShellu.
      2. Nastavte počet sloupců = počet fyzických disků. Použijte PowerShell při konfiguraci více než 8 disků (ne Správce serveru uživatelského rozhraní). 

    Například následující PowerShell vytvoří nový fond úložiště s velikostí prokládání na 64 KB a počet sloupců rovný velikosti fyzického disku ve fondu úložiště:

    ```powershell
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
    
    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
        -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
        -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
        –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
        -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
        -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Pro Windows 2008 R2 nebo starší můžete použít dynamické disky (svazky Stripe OS) a velikost Stripe je vždycky 64 KB. Tato možnost je zastaralá od Windows 8/Windows Serveru 2012. Informace najdete v tématu věnovaném podpoře na [virtuální diskové službě přechod na rozhraní API pro správu úložiště systému Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Pokud pro [SQL Server instancí clusteru s podporou převzetí služeb při selhání](failover-cluster-instance-storage-spaces-direct-manually-configure.md)používáte [prostory úložiště s přímým přístupem (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) , musíte nakonfigurovat jeden fond. I když se na tomto jediném fondu dají vytvořit různé svazky, budou všechny sdílet stejné charakteristiky, například stejné zásady pro ukládání do mezipaměti.

  * Určete počet disků přidružených k vašemu fondu úložiště na základě očekávání zatížení. Mějte na paměti, že různé velikosti virtuálních počítačů umožňují různé počty připojených datových disků. Další informace najdete v tématu [velikosti pro virtuální počítače](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Pokud nepoužíváte Premium SSD (scénáře pro vývoj a testování), doporučujeme přidat maximální počet datových disků, které podporuje [Velikost virtuálního počítače](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , a použít diskové svazky.

* **Zásady ukládání do mezipaměti**: Všimněte si následujících doporučení pro ukládání zásad do mezipaměti v závislosti na konfiguraci úložiště.

  * Pokud používáte samostatné disky pro data a soubory protokolů, povolte ukládání do mezipaměti pro čtení na datových discích hostujících datové soubory a datové soubory TempDB. To může mít za následek značný přínos na výkon. Nepovolujte ukládání do mezipaměti na disku, který soubor protokolu uchovává, protože to způsobí menší snížení výkonu.

  * Pokud používáte diskové obložení na jednom fondu úložiště, většina úloh bude využívat ukládání do mezipaměti při čtení. Pokud máte samostatné fondy úložiště pro soubory protokolů a dat, povolte ukládání do mezipaměti pro čtení jenom pro datové soubory ve fondu úložiště. V některých těžkých zátěžích pro zápis se může dosáhnout lepšího výkonu bez ukládání do mezipaměti. To lze určit pouze pomocí testování.

  * Předchozí doporučení se vztahují na SSD Premium. Pokud nepoužíváte prémiové SSD, nepovolujte žádné datové disky do mezipaměti.

  * Pokyny ke konfiguraci ukládání do mezipaměti disku najdete v následujících článcích. Pro model nasazení Classic (ASM) viz: [set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) a [set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Model nasazení Azure Resource Manager naleznete v tématu: [set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) a [set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Při změně nastavení mezipaměti disků Azure Virtual Machines zastavte službu SQL Server, abyste se vyhnuli možnosti poškození databáze.

* **Velikost alokační jednotky NTFS**: při formátování datového disku se doporučuje použít velikost alokační jednotky 64-KB pro data a soubory protokolů i pro databázi tempdb. Pokud je databáze TempDB umístěná na dočasném disku (D:\ jednotka) výkon získaný využitím této jednotky převažuje nad nutností velikosti alokační jednotky na 64 KB. 

* **Osvědčené postupy správy disků**: při odebrání datového disku nebo změně jeho typu mezipaměti zastavte službu SQL Server během změny. Při změně nastavení ukládání do mezipaměti na disku s operačním systémem Azure zastaví virtuální počítač, změní typ mezipaměti a restartuje virtuální počítač. Když se změní nastavení mezipaměti datového disku, virtuální počítač se neukončí, ale datový disk se během změny odpojí z virtuálního počítače a pak se znovu připojí.

  > [!WARNING]
  > Nepovedlo se zastavit službu SQL Server během těchto operací může způsobit poškození databáze.


## <a name="io-guidance"></a>Doprovodné materiály k v/v

* Nejlepší výsledky s Premium SSD se dosáhnou při paralelizovat své aplikace a požadavků. Premium SSD jsou navržené pro scénáře, ve kterých je hloubka front/v v/v větší než 1, takže se vám pro sériové požadavky s jedním vláknem zobrazí malý nebo žádný nárůst výkonu (i v případě, že se jedná o náročné úložiště). To může mít vliv na výsledky testů nástroje pro analýzu výkonu, jako je například SQLIO, na jeden podproces.

* Zvažte použití [Komprese stránky databáze](https://msdn.microsoft.com/library/cc280449.aspx) , protože může pomoci zlepšit výkon úloh náročných na vstupně-výstupní operace. Komprese dat ale může zvýšit spotřebu procesoru na databázovém serveru.

* Zvažte možnost Povolit okamžitou inicializaci souborů, aby se zkrátila doba potřebná k počátečnímu přidělení souborů. Pokud chcete využít výhod okamžité inicializace souborů, udělíte účtu služby SQL Server (MSSQLSERVER) SE_MANAGE_VOLUME_NAME a přidáte ho do zásad zabezpečení **provádět úlohy údržby multilicence** . Pokud používáte image platformy SQL Server pro Azure, výchozí účet služby (NT Service\MSSQLSERVER) se nepřidá do zásad zabezpečení **provádět úlohy údržby multilicence** . Jinými slovy, okamžitá inicializace souborů není povolená v SQL Server Image platformy Azure. Po přidání účtu služby SQL Server do zásad zabezpečení **úlohy údržby multilicence proveďte** restart služby SQL Server. Při použití této funkce se mohly vycházet z bezpečnostních důvodů. Další informace najdete v tématu [inicializace souboru databáze](https://msdn.microsoft.com/library/ms175935.aspx).

* Mějte na paměti, že **autogrow** se považuje za neočekávaný nárůst. Nespravujte svoje data a protokolujte na každodenní bázi pomocí autogrow. Pokud se používá autogrow, soubor předem Rozšiřte pomocí přepínače Size.

* Ujistěte se, že je možnost automaticky **zmenšit** zakázaná, aby nedocházelo k zbytečné režii, která může negativně ovlivnit výkon

* Přesuňte všechny databáze na datové disky, včetně systémových databází. Další informace najdete v tématu [přesunutí systémových databází](https://msdn.microsoft.com/library/ms345408.aspx).

* Přesuňte protokoly chyb SQL Server a trasovací soubory do datových disků. To se dá udělat v SQL Server Configuration Manager tak, že kliknete pravým tlačítkem na instanci SQL Server a vyberete vlastnosti. Nastavení protokolu chyb a trasovacího souboru můžete změnit na kartě **parametry spuštění** . Adresář výpisu se zadává na kartě **Upřesnit** . Následující snímek obrazovky ukazuje, kde vyhledat parametr spuštění protokolu chyb.

    ![Snímek obrazovky s chybou SQL](./media/performance-guidelines-best-practices/sql_server_error_log_location.png)

* Nastavte výchozí zálohu a umístění souborů databáze. Použijte doporučení v tomto článku a proveďte změny v okně Vlastnosti serveru. Pokyny najdete v tématu [zobrazení nebo změna výchozích umístění pro data a soubory protokolů (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Následující snímek obrazovky ukazuje, kde provést tyto změny.

    ![Soubory protokolu a zálohování dat SQL](./media/performance-guidelines-best-practices/sql_server_default_data_log_backup_locations.png)
* Povolte uzamčené stránky, aby se snížila vstupně-výstupní operace a jakékoli aktivity stránkování. Další informace najdete v tématu [Povolení možnosti Uzamknout stránky v paměti (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Pokud používáte SQL Server 2012, nainstalujte aktualizaci Service Pack 1 – kumulativní aktualizace 10. Tato aktualizace obsahuje opravu špatného výkonu při vstupu a výstupu při spuštění příkazu SELECT INTO dočasnou tabulku v SQL Server 2012. Informace najdete v tomto [článku znalostní báze](https://support.microsoft.com/kb/2958012).

* Při převádění do Azure zvažte komprimaci všech datových souborů.

## <a name="feature-specific-guidance"></a>Doprovodné materiály k jednotlivým funkcím

Některá nasazení mohou dosáhnout dalších výhod výkonu s využitím pokročilejších technik konfigurace. V následujícím seznamu se vysvětlují některé funkce SQL Server, které vám pomůžou dosáhnout lepšího výkonu:

### <a name="back-up-to-azure-storage"></a>Zálohování do Azure Storage
Při provádění zálohování pro SQL Server běžící v Azure Virtual Machines můžete použít [SQL Server zálohování na adresu URL](https://msdn.microsoft.com/library/dn435916.aspx). Tato funkce je k dispozici od verze SQL Server 2012 SP1 CU2 a doporučuje se pro zálohování na připojené datové disky. Když provádíte zálohování nebo obnovení do nebo z Azure Storage, postupujte podle doporučení uvedených v článku [SQL Server zálohování pro osvědčené postupy a řešení potíží a obnovení ze záloh uložených v Azure Storage](https://msdn.microsoft.com/library/jj919149.aspx). Tyto zálohy můžete také automatizovat pomocí [automatizovaného zálohování pro SQL Server v Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md).

Před SQL Server 2012 můžete použít [nástroj SQL Server Backup do Azure](https://www.microsoft.com/download/details.aspx?id=40740). Tento nástroj může přispět ke zvýšení propustnosti záloh pomocí více cílů záložního Stripe.

### <a name="sql-server-data-files-in-azure"></a>SQL Server datových souborů v Azure

Tato nová funkce, [SQL Server datových souborů v Azure](https://msdn.microsoft.com/library/dn385720.aspx), je k dispozici od SQL Server 2014. Spuštění SQL Server s datovými soubory v Azure ukazuje srovnatelné charakteristiky výkonu jako při použití datových disků Azure.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Instance clusteru s podporou převzetí služeb při selhání a prostory úložiště

Pokud používáte prostory úložiště, při přidávání uzlů do clusteru na stránce **potvrzení** zrušte zaškrtnutí políčka **Přidat do clusteru veškeré oprávněné úložiště**. 

![Zrušit kontrolu oprávněného úložiště](./media/performance-guidelines-best-practices/uncheck-eligible-cluster-storage.png)

Pokud používáte prostory úložiště a nechcete zrušit kontrolu **Přidat do clusteru všechny opravňující úložiště**, systém Windows virtuální disky během procesu clusteringu odpojí. V důsledku toho se neobjeví ve Správci disků nebo v Průzkumníkovi, dokud se prostory úložiště z clusteru neodstraní a znovu nepřipojí přes PowerShell. Prostory úložiště seskupují více disků do fondů úložiště. Další informace najdete v tématu [prostory úložiště](/windows-server/storage/storage-spaces/overview).

## <a name="multiple-instances"></a>Několik instancí 

Při nasazování více instancí SQL Server do jednoho virtuálního počítače Vezměte v úvahu následující osvědčené postupy: 

- Nastavte maximální velikost paměti serveru pro každou instanci SQL Server a zajistěte, aby existovala zbývající paměť pro operační systém. Nezapomeňte aktualizovat omezení paměti pro instance SQL Server, pokud změníte, kolik paměti je přiděleno virtuálnímu počítači. 
- Mít samostatné logické jednotky (LUN) pro data, protokoly a databázi TempDB, protože všechny mají různé vzory úloh a nechcete je navzájem ovlivňovat. 
- Důkladně otestujte své prostředí v rámci velkých produkčních úloh, abyste se ujistili, že dokáže zvládnout kapacitu zátěže ve špičce v rámci vaší aplikace jako SLA. 

Příznaky přetížených systémů mohou zahrnovat, ale nejsou omezeny na vyčerpání pracovního vlákna, pomalých dob odezvy a/nebo v systémové paměti dispečera. 




## <a name="next-steps"></a>Další kroky

Další informace o úložišti a výkonu najdete v tématu [pokyny ke konfiguraci úložiště pro SQL Server v Azure Virtual Machines](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

Osvědčené postupy zabezpečení najdete v tématu [požadavky na zabezpečení pro SQL Server v Azure Virtual Machines](security-considerations-best-practices.md).

Další informace o SQL Server článcích o virtuálních počítačích najdete v článku [SQL Server na Azure Virtual Machines přehled](sql-server-on-azure-vm-iaas-what-is-overview.md). Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](frequently-asked-questions-faq.md).
