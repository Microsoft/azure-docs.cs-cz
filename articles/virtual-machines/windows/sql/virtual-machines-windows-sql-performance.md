---
title: Pokyny k výkonu pro SQL Server v Azure | Microsoft Docs
description: Poskytuje pokyny pro optimalizaci SQL Server výkonu v Microsoft Azure virtuálních počítačích.
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
ms.date: 09/26/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d81c1941f114efbfd4ede559152317e907edeaea
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882393"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Pokyny k výkonu pro SQL Server v Azure Virtual Machines

## <a name="overview"></a>Přehled

Tento článek poskytuje pokyny pro optimalizaci SQL Server výkonu v Microsoft Azurem virtuálním počítači. Při spouštění SQL Server v Azure Virtual Machines doporučujeme, abyste dál používali stejné možnosti optimalizace výkonu databáze, které platí pro SQL Server v prostředí místního serveru. Výkon relační databáze ve veřejném cloudu však závisí na řadě faktorů, jako je velikost virtuálního počítače nebo konfigurace datových disků.

[SQL Server imagí zřízených v Azure Portal](quickstart-sql-vm-create-portal.md) postupují v části Obecné osvědčené postupy konfigurace úložiště (Další informace o konfiguraci úložiště najdete v tématu [konfigurace úložiště pro SQL Server virtuální počítače](virtual-machines-windows-sql-server-storage-configuration.md)). Po zřízení zvažte použití dalších optimalizací popsaných v tomto článku. Založte své možnosti na vaše úlohy a ověřte je prostřednictvím testování.

> [!TIP]
> Mezi optimalizací a optimalizací výkonu je typicky kompromis. Tento článek se zaměřuje na získání *nejlepšího* výkonu pro SQL Server virtuálních počítačů Azure. Pokud vaše úloha je méně náročná, možná nebudete potřebovat každou níže uvedenou optimalizaci. Při hodnocení těchto doporučení Vezměte v úvahu požadavky na výkon, náklady a vzory úloh.

## <a name="quick-check-list"></a>Seznam rychlých kontrol

Toto je rychlý kontrolní seznam pro optimální výkon SQL Server v Azure Virtual Machines:

| Oblast | Optimalizace |
| --- | --- |
| [Velikost virtuálního počítače](#vm-size-guidance) | - [DS3_v2](../sizes-general.md) nebo vyšší pro SQL Enterprise Edition.<br/><br/> - [DS2_v2](../sizes-general.md) nebo vyšší pro SQL Standard a webové edice |
| [Storage](#storage-guidance) | – Použijte [prémiové SSD](../disks-types.md). Storage úrovně Standard se doporučuje jenom pro vývoj a testování.<br/><br/> – Udržujte [účet úložiště](../../../storage/common/storage-create-storage-account.md) a SQL Server virtuální počítač ve stejné oblasti.<br/><br/> * V účtu úložiště zakažte [geograficky redundantní úložiště](../../../storage/common/storage-redundancy.md) Azure (geografickou replikaci). |
| [Disky](#disks-guidance) | – Použijte minimálně 2 [P30 disky](../disks-types.md#premium-ssd) (1 pro soubory protokolů a 1 pro datové soubory včetně databáze tempdb). Pro úlohy, které vyžadují ~ 50 000 IOPS, zvažte použití SSD úrovně Ultra. <br/><br/> – Nepoužívejte operační systém nebo dočasné disky pro úložiště databáze nebo protokolování.<br/><br/> -Povolit ukládání do mezipaměti pro čtení na discích, které hostují datové soubory a datové soubory TempDB.<br/><br/> – Nepovolujte ukládání do mezipaměti na discích hostujících soubor protokolu.  **Důležité**informace: Při změně nastavení mezipaměti pro disk virtuálního počítače Azure zastavte službu SQL Server.<br/><br/> – Proložení více datových disků Azure za účelem získání vyšší propustnosti v/v<br/><br/> – Formát s dokumentovanými velikostmi přidělení. <br/><br/> Na místní jednotku SSD `D:\` umístěte databázi tempdb pro klíčové SQL Server úlohy (po výběru správné velikosti virtuálního počítače). Další informace najdete v blogu [pomocí SSD k ukládání databáze tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).  |
| [I/O](#io-guidance) |-Povolit kompresi stránky databáze.<br/><br/> – Povolí okamžitou inicializaci souborů pro datové soubory.<br/><br/> – Omezuje automatické zvětšování databáze.<br/><br/> -Zakázat automatického zmenšení databáze.<br/><br/> – Přesuňte všechny databáze na datové disky, včetně systémových databází.<br/><br/> – SQL Server přesunutí adresářů protokolů chyb a trasovacích souborů do datových disků.<br/><br/> – Nastavení výchozí zálohy a umístění souborů databáze.<br/><br/> -Povolit uzamčené stránky.<br/><br/> – Použijte opravy výkonu SQL Server. |
| [Specifické pro jednotlivé funkce](#feature-specific-guidance) | – Zálohování přímo do úložiště objektů BLOB. |

Další informace o *tom, jak* a *Proč* provádět tyto optimalizace, najdete v podrobnostech a pokynech uvedených v následujících částech.

## <a name="vm-size-guidance"></a>Pokyny pro velikost virtuálního počítače

U aplikací s citlivým výkonem doporučujeme používat následující [velikosti virtuálních počítačů](../sizes.md):

* **SQL Server Enterprise Edition**: DS3_v2 nebo vyšší
* **SQL Server Standard a webové edice**: DS2_v2 nebo vyšší

[DSv2-Series](../sizes-general.md#dsv2-series) Virtuální počítače podporují Prémiové úložiště, což se doporučuje pro nejlepší výkon. Doporučené velikosti jsou směrné plány, ale skutečná velikost počítače, kterou vyberete, závisí na vašich požadavcích na zatížení. Virtuální počítače řady DSv2-Series jsou virtuální počítače pro obecné účely, které jsou vhodné pro nejrůznější úlohy, zatímco jiné velikosti počítačů jsou optimalizované pro konkrétní typy úloh. Například [řada M-Series](../sizes-memory.md#m-series) nabízí nejvyšší počet vCPU a paměť pro největší SQL Server úlohy. [Řady GS-Series](../sizes-previous-gen.md#gs-series) a [DSv2-Series 11-15](../sizes-memory.md#dsv2-series-11-15) jsou optimalizované pro velké nároky na paměť. Obě tyto řady jsou také dostupné v [omezených základních velikostech](../../windows/constrained-vcpu.md), což šetří peníze pro úlohy s nižšími nároky na výpočetní výkon. Počítače [řady ls-series](../sizes-storage.md) jsou optimalizované pro vysokou propustnost disku a v/v. Je důležité vzít v úvahu konkrétní úlohu SQL Server a použít ji pro výběr řady a velikosti virtuálního počítače.

## <a name="storage-guidance"></a>Pokyny k ukládání

Virtuální počítače řady DS-Series (spolu s DSv2-Series a GS-Series) podporují [prémiové SSD](../disks-types.md). Premium SSD se doporučují pro všechny produkční úlohy.

> [!WARNING]
> Standardní HDD a SSD mají různou latenci a šířku pásma a jsou doporučovány jenom pro úlohy pro vývoj a testování. Produkční úlohy by měly používat Premium SSD.

Kromě toho doporučujeme vytvořit účet služby Azure Storage ve stejném datovém centru jako virtuální počítače s SQL Server, abyste snížili zpoždění přenosu. Při vytváření účtu úložiště není zaručená geografická replikace jako konzistentní pořadí zápisu na více discích. Místo toho zvažte konfiguraci SQL Server technologie zotavení po havárii mezi dvěma datovými centry Azure. Další informace najdete v tématu [Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Doprovodné materiály pro disky

Na virtuálním počítači Azure existují tři hlavní typy disků:

* **Disk s operačním systémem**: Když vytvoříte virtuální počítač Azure, bude platforma k VIRTUÁLNÍmu počítači pro disk s operačním systémem připojovat aspoň jeden disk (označený jako jednotka **C** ). Tento disk je virtuální pevný disk uložený jako objekt blob stránky v úložišti.
* **Dočasný disk**: Virtuální počítače Azure obsahují další disk nazvaný dočasný disk (označený jako jednotka **D**:). Toto je disk na uzlu, který lze použít pro pomocné místo.
* **Datové disky**: K virtuálnímu počítači můžete také připojit další disky jako datové disky a ty budou uloženy v úložišti jako objekty blob stránky.

Následující části popisují doporučení pro používání těchto různých disků.

### <a name="operating-system-disk"></a>Disk operačního systému

Disk s operačním systémem je virtuální pevný disk, který můžete spustit a připojit jako běžící verzi operačního systému a který je označený jako jednotka **C** .

Výchozí zásady ukládání do mezipaměti na disku s operačním systémem jsou **jen pro čtení a zápis**. Pro aplikace s citlivým výkonem doporučujeme místo disku operačního systému používat datové disky. Viz část na datových discích níže.

### <a name="temporary-disk"></a>Dočasný disk

Dočasná Úložná jednotka označená jako jednotka **D**: není trvalá v úložišti objektů BLOB v Azure. Neukládejte soubory uživatelské databáze ani soubory protokolů transakcí uživatele na jednotce **D**:.

Pro virtuální počítače řady D-Series, Dv2-Series a G-series je dočasná jednotka na těchto virtuálních počítačích založená na SSD. Pokud vaše úloha využívá rozsáhlé použití databáze TempDB (například dočasné objekty nebo složitá spojení), může ukládání databáze TempDB na jednotce **D** způsobit vyšší propustnost databáze tempdb a nižší latenci databáze tempdb. Ukázkový scénář najdete v diskuzi v databázi TempDB v následujícím blogovém příspěvku: [Pokyny pro konfiguraci úložiště pro SQL Server na virtuálním počítači Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm)

Pro virtuální počítače, které podporují prémiové SSD (DS-Series, DSv2-Series a GS-Series), doporučujeme uložit databázi TempDB na disk, který podporuje Premium SSD s povoleným ukládáním do mezipaměti pro čtení.

K tomuto doporučení existuje jedna výjimka: _Pokud je využití databáze tempdb náročné na zápis, můžete dosáhnout vyššího výkonu ukládáním databáze tempdb na místní jednotce **D** , která je na těchto velikostech počítačů založená na jednotkách SSD._ Další informace najdete v blogu věnovaném [použití SSD s databází tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) . 

### <a name="data-disks"></a>Datové disky

* **Použití datových disků pro data a soubory protokolu**: Pokud nepoužíváte diskový disk, použijte dva disky P30 úrovně Premium SSD, kde jeden disk obsahuje soubory protokolu a druhý obsahuje data a databázi TempDB (s výjimkou nejdůležitějších úloh a zatížení, které jsou uvedeny výše). Každý disk SSD úrovně Premium poskytuje množství vstupně-výstupních operací a šířky pásma (MB/s) v závislosti na velikosti, jak je znázorněno v článku, a [Vyberte typ disku](../disks-types.md). Pokud používáte techniku rozložení disku, například prostory úložiště, dosáhnete optimálního výkonu tím, že máte dva fondy, jeden pro soubory protokolu a druhý pro datové soubory. Pokud ale plánujete použít SQL Server instance clusterů s podporou převzetí služeb při selhání (FCI), musíte nakonfigurovat jeden fond.

   > [!TIP]
   > - Výsledky testů různých konfigurací disků a úloh najdete v tomto blogovém příspěvku: [Pokyny pro konfiguraci úložiště pro SQL Server na virtuálním počítači Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)
   > - Pro zajištění důležitého výkonu pro SQL servery, které potřebují ~ 50 000 IOPS, zvažte nahrazení 10 P30 disků pomocí SSD úrovně Ultra. Další informace najdete v tomto blogovém příspěvku: [Nejdůležitější výkon se SSD úrovně Ultra](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Když zřizujete SQL Server virtuální počítač na portálu, máte možnost upravit si konfiguraci úložiště. V závislosti na konfiguraci Azure nakonfiguruje jeden nebo víc disků. Více disků je sloučeno do jednoho fondu úložiště s použitím Stripe. Data i soubory protokolů se v této konfiguraci nacházejí společně. Další informace najdete v tématu [Konfigurace úložiště pro virtuální počítače s SQL Server](virtual-machines-windows-sql-server-storage-configuration.md).

* **Diskové svazky**: Pro zvýšení propustnosti můžete přidat další datové disky a používat diskové svazky. Chcete-li zjistit počet datových disků, je nutné analyzovat počet vstupně-výstupních operací a šířky pásma požadované pro soubory protokolu a pro vaše data a soubory databáze TempDB. Všimněte si, že různé velikosti virtuálních počítačů mají různá omezení počtu vstupně-výstupních operací za sekundu a šířky pásma, viz tabulky v IOPS podle [velikosti virtuálního počítače](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Použijte následující pokyny:

  * U systémů Windows 8/Windows Server 2012 a novějších používejte [prostory úložiště](https://technet.microsoft.com/library/hh831739.aspx) s následujícími pokyny:

      1. Nastavte prokládání (velikost Stripe) na 64 KB (65536 bajtů) pro úlohy OLTP a 256 KB (262144 bajty) pro úlohy datového skladu, aby se předešlo dopadu na výkon v důsledku chybného zarovnání oddílu. Tato nastavení se musí nastavit pomocí PowerShellu.
      2. Nastavte počet sloupců = počet fyzických disků. Použijte PowerShell při konfiguraci více než 8 disků (ne Správce serveru uživatelského rozhraní). 

    Například následující PowerShell vytvoří nový fond úložiště s velikostí proložení na 64 KB a počet sloupců 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Pro Windows 2008 R2 nebo starší můžete použít dynamické disky (svazky Stripe OS) a velikost Stripe je vždycky 64 KB. Všimněte si, že tato možnost je zastaralá od Windows 8/Windows Serveru 2012. Informace najdete v tématu věnovaném podpoře na [virtuální diskové službě přechod na rozhraní API pro správu úložiště systému Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Pokud pro [SQL Server instancí clusteru s podporou převzetí služeb při selhání](virtual-machines-windows-portal-sql-create-failover-cluster.md)používáte [prostory úložiště s přímým přístupem (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) , musíte nakonfigurovat jeden fond. Mějte na paměti, že i když se na tomto jediném fondu dají vytvořit různé svazky, budou všechny sdílet stejné vlastnosti, například stejné zásady pro ukládání do mezipaměti.

  * Určete počet disků přidružených k vašemu fondu úložiště na základě očekávání zatížení. Mějte na paměti, že různé velikosti virtuálních počítačů umožňují různé počty připojených datových disků. Další informace najdete v tématu [velikosti pro Virtual Machines](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Pokud nepoužíváte Premium SSD (scénáře pro vývoj a testování), doporučujeme přidat maximální počet datových disků, které podporuje [Velikost virtuálního počítače](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , a použít diskové svazky.

* **Zásady ukládání do mezipaměti**: Všimněte si následujících doporučení pro ukládání zásad do mezipaměti v závislosti na konfiguraci úložiště.

  * Pokud používáte samostatné disky pro data a soubory protokolů, povolte ukládání do mezipaměti pro čtení na datových discích hostujících datové soubory a datové soubory TempDB. To může mít za následek značný přínos na výkon. Nepovolujte ukládání do mezipaměti na disku, který soubor protokolu uchovává, protože to způsobí menší snížení výkonu.

  * Pokud používáte diskové obložení na jednom fondu úložiště, většina úloh bude využívat ukládání do mezipaměti při čtení. Pokud máte samostatné fondy úložiště pro soubory protokolů a dat, povolte ukládání do mezipaměti pro čtení jenom pro datové soubory ve fondu úložiště. V některých těžkých zátěžích pro zápis se může dosáhnout lepšího výkonu bez ukládání do mezipaměti. To lze určit pouze pomocí testování.

  * Předchozí doporučení se vztahují na SSD Premium. Pokud nepoužíváte prémiové SSD, nepovolujte žádné datové disky do mezipaměti.

  * Pokyny ke konfiguraci ukládání do mezipaměti disku najdete v následujících článcích. Pro model nasazení Classic (ASM) viz: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) a [set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Azure Resource Manager modelu nasazení najdete zde: [Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) a [set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Při změně nastavení mezipaměti disků virtuálních počítačů Azure zastavte službu SQL Server, aby nedocházelo k možnosti poškození databáze.

* **Velikost alokační jednotky NTFS**: Při formátování datového disku se doporučuje použít velikost alokační jednotky 64-KB pro data a soubory protokolů i pro databázi TempDB.

* **Doporučené postupy pro správu disků**: Když odeberete datový disk nebo změníte jeho typ mezipaměti, zastavte během změny službu SQL Server. Při změně nastavení ukládání do mezipaměti na disku s operačním systémem Azure zastaví virtuální počítač, změní typ mezipaměti a restartuje virtuální počítač. Když se změní nastavení mezipaměti datového disku, virtuální počítač se neukončí, ale datový disk se během změny odpojí z virtuálního počítače a pak se znovu připojí.

  > [!WARNING]
  > Nepovedlo se zastavit službu SQL Server během těchto operací může způsobit poškození databáze.


## <a name="io-guidance"></a>Doprovodné materiály k v/v

* Nejlepší výsledky s Premium SSD se dosáhnou při paralelizovat své aplikace a požadavků. Premium SSD jsou navržené pro scénáře, ve kterých je hloubka front/v v/v větší než 1, takže se vám pro sériové požadavky s jedním vláknem zobrazí malý nebo žádný nárůst výkonu (i v případě, že se jedná o náročné úložiště). To může mít vliv na výsledky testů nástroje pro analýzu výkonu, jako je například SQLIO, na jeden podproces.

* Zvažte použití [Komprese stránky databáze](https://msdn.microsoft.com/library/cc280449.aspx) , protože může pomoci zlepšit výkon úloh náročných na vstupně-výstupní operace. Komprese dat ale může zvýšit spotřebu procesoru na databázovém serveru.

* Zvažte možnost Povolit okamžitou inicializaci souborů, aby se zkrátila doba potřebná k počátečnímu přidělení souborů. Pokud chcete využít výhod okamžité inicializace souborů, udělíte účtu služby SQL Server (MSSQLSERVER) pomocí SE_MANAGE_VOLUME_NAME a přidáte ho do zásad zabezpečení **provádět úlohy údržby multilicence** . Pokud používáte image platformy SQL Server pro Azure, výchozí účet služby (NT Service\MSSQLSERVER) se nepřidá do zásad zabezpečení **provádět úlohy údržby multilicence** . Jinými slovy, okamžitá inicializace souborů není povolená v SQL Server Image platformy Azure. Po přidání účtu služby SQL Server do zásad zabezpečení **úlohy údržby multilicence proveďte** restart služby SQL Server. Při použití této funkce se mohly vycházet z bezpečnostních důvodů. Další informace najdete v tématu [inicializace souboru databáze](https://msdn.microsoft.com/library/ms175935.aspx).

* **autogrow** se považuje za jenom jako pohotovostní pro neočekávaný nárůst. Nespravujte svoje data a protokolujte na každodenní bázi pomocí autogrow. Pokud se používá autogrow, soubor předem Rozšiřte pomocí přepínače Size.

* Ujistěte se, že je možnost automaticky **zmenšit** zakázaná, aby nedocházelo k zbytečné režii, která může negativně ovlivnit výkon

* Přesuňte všechny databáze na datové disky, včetně systémových databází. Další informace najdete v tématu [přesunutí systémových databází](https://msdn.microsoft.com/library/ms345408.aspx).

* Přesuňte protokoly chyb SQL Server a trasovací soubory do datových disků. To se dá udělat v SQL Server Configuration Manager tak, že kliknete pravým tlačítkem na instanci SQL Server a vyberete vlastnosti. Nastavení protokolu chyb a trasovacího souboru můžete změnit na kartě **parametry spuštění** . Adresář výpisu se zadává na kartě **Upřesnit** . Následující snímek obrazovky ukazuje, kde vyhledat parametr spuštění protokolu chyb.

    ![Snímek obrazovky s chybou SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Nastavte výchozí zálohu a umístění souborů databáze. Použijte doporučení v tomto článku a proveďte změny v okně Vlastnosti serveru. Pokyny najdete v tématu [zobrazení nebo změna výchozích umístění pro data a soubory protokolů (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Následující snímek obrazovky ukazuje, kde provést tyto změny.

    ![Soubory protokolu a zálohování dat SQL](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Povolte uzamčené stránky, aby se snížila vstupně-výstupní operace a jakékoli aktivity stránkování. Další informace najdete v tématu [Povolení možnosti Uzamknout stránky v paměti (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Pokud používáte SQL Server 2012, nainstalujte aktualizaci Service Pack 1 – kumulativní aktualizace 10. Tato aktualizace obsahuje opravu špatného výkonu při vstupu a výstupu při spuštění příkazu SELECT INTO dočasnou tabulku v SQL Server 2012. Informace najdete v tomto [článku znalostní báze](https://support.microsoft.com/kb/2958012).

* Při převádění do Azure zvažte komprimaci všech datových souborů.

## <a name="feature-specific-guidance"></a>Doprovodné materiály k jednotlivým funkcím

Některá nasazení mohou dosáhnout dalších výhod výkonu s využitím pokročilejších technik konfigurace. V následujícím seznamu se vysvětlují některé funkce SQL Server, které vám pomůžou dosáhnout lepšího výkonu:

### <a name="backup-to-azure-storage"></a>Zálohování do Azure Storage
Při provádění zálohování pro SQL Server běžící na virtuálních počítačích Azure můžete použít [zálohování SQL Server na adresu URL](https://msdn.microsoft.com/library/dn435916.aspx). Tato funkce je k dispozici od verze SQL Server 2012 SP1 CU2 a doporučuje se pro zálohování na připojené datové disky. Při zálohování nebo obnovení do služby Azure Storage se řiďte doporučeními, která jsou k dispozici na [webu SQL Server Backup, Doporučené postupy a řešení potíží a obnovení ze záloh uložených v Azure Storage](https://msdn.microsoft.com/library/jj919149.aspx). Tyto zálohy můžete také automatizovat pomocí [automatizovaného zálohování pro SQL Server ve službě Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).

Před SQL Server 2012 můžete použít [nástroj SQL Server Backup do Azure](https://www.microsoft.com/download/details.aspx?id=40740). Tento nástroj může přispět ke zvýšení propustnosti záloh pomocí více cílů záložního Stripe.

### <a name="sql-server-data-files-in-azure"></a>SQL Server datových souborů v Azure

Tato nová funkce, [SQL Server datových souborů v Azure](https://msdn.microsoft.com/library/dn385720.aspx), je k dispozici od SQL Server 2014. Spuštění SQL Server s datovými soubory v Azure ukazuje srovnatelné charakteristiky výkonu jako při použití datových disků Azure.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Instance clusteru s podporou převzetí služeb při selhání a prostory úložiště

Pokud používáte prostory úložiště, při přidávání uzlů do clusteru na stránce **potvrzení** zrušte zaškrtnutí políčka **Přidat do clusteru veškeré oprávněné úložiště**. 

![Zrušit kontrolu oprávněného úložiště](media/virtual-machines-windows-sql-performance/uncheck-eligible-cluster-storage.png)

Pokud používáte prostory úložiště a nechcete zrušit kontrolu **Přidat do clusteru všechny opravňující úložiště**, systém Windows virtuální disky během procesu clusteringu odpojí. V důsledku toho se neobjeví ve Správci disků nebo v Průzkumníkovi, dokud se prostory úložiště z clusteru neodstraní a znovu nepřipojí přes PowerShell. Prostory úložiště seskupují více disků do fondů úložiště. Další informace najdete v tématu [prostory úložiště](/windows-server/storage/storage-spaces/overview).

## <a name="next-steps"></a>Další postup

Další informace o úložišti a výkonu najdete v tématu [pokyny ke konfiguraci úložiště pro SQL Server na virtuálním počítači Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/) .

Osvědčené postupy zabezpečení najdete v tématu [požadavky na zabezpečení pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-security.md).

Další informace o SQL Server článcích o virtuálních počítačích najdete v článku [SQL Server na Azure Virtual Machines přehled](virtual-machines-windows-sql-server-iaas-overview.md). Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md).
