---
title: "Osvědčené postupy z hlediska výkonu pro SQL Server v Azure | Microsoft Docs"
description: "Obsahuje osvědčené postupy pro optimalizaci výkonu serveru SQL Server ve virtuálních počítačích Microsoft Azure."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: 
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2018
ms.author: jroth
ms.openlocfilehash: 3458e2f1a09b597c50c01d59eb6522b3fa521310
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="performance-best-practices-for-sql-server-in-azure-virtual-machines"></a>Osvědčené postupy z hlediska výkonu pro SQL Server na Azure Virtual Machines

## <a name="overview"></a>Přehled

Tento článek obsahuje osvědčené postupy pro optimalizaci výkonu systému SQL Server na virtuálním počítači Microsoft Azure. Při spuštění systému SQL Server v Azure Virtual Machines, doporučujeme pokračovat pomocí stejného výkonu databáze ladění možnosti, které se vztahují k systému SQL Server v prostředí místní server. Výkon relační databáze ve veřejném cloudu však závisí na mnoha faktorech, například velikost virtuálního počítače a konfiguraci datových disků.

Při vytváření bitové kopie systému SQL Server, [zvažte zřizování virtuálních počítačů na portálu Azure](virtual-machines-windows-portal-sql-server-provision.md). Zřízené v portálu s Resource Managerem virtuálním počítačům systému SQL Server dodržujte doporučené postupy.

Tento článek se zaměřuje na získávání *nejlepší* výkonu pro SQL Server na virtuálních počítačích Azure. Pokud vaše úlohy je méně náročné, nemusejí být nutné každých optimalizace uvedené níže. Zvažte požadavkům na výkon a vzory úlohy, jak vyhodnotit tato doporučení.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="quick-check-list"></a>Rychlá kontrola seznamu

Následuje seznam Rychlá kontrola pro optimální výkon systému SQL Server na virtuálních počítačích Azure:

| Oblast | Optimalizace |
| --- | --- |
| [Velikost virtuálního počítače](#vm-size-guidance) |[DS3](../sizes-memory.md) nebo vyšší pro SQL Enterprise edition.<br/><br/>[DS2](../sizes-memory.md) nebo vyšší verze SQL Standard a Web. |
| [Úložiště](#storage-guidance) |Použití [Storage úrovně Premium](../premium-storage.md). Standardní úložiště se doporučuje jenom pro vývojové a testovací.<br/><br/>Zachovat [účet úložiště](../../../storage/common/storage-create-storage-account.md) a virtuální počítač SQL Server ve stejné oblasti.<br/><br/>Zakázat Azure [geograficky redundantní úložiště](../../../storage/common/storage-redundancy.md) (geografická replikace) na účet úložiště. |
| [Disky](#disks-guidance) |Použití minimálně 2 [P30 disky](../premium-storage.md#scalability-and-performance-targets) (1 pro soubory protokolů, 1 pro datové soubory a databáze TempDB).<br/><br/>Vyhněte se použití operačního systému nebo dočasné disků pro úložiště databáze nebo protokolování.<br/><br/>Povolit čtení ukládání do mezipaměti na disky hostující databázi TempDB a datových souborů.<br/><br/>Nepovolujte ukládání do mezipaměti na disky hostování souboru protokolu.<br/><br/>Důležité: Zastavte službu systému SQL Server při změně nastavení do mezipaměti pro disk pro virtuální počítač Azure.<br/><br/>Prokládané více Azure datových disků získat vyšší propustnost vstupně-výstupní operace.<br/><br/>Formát s velikostí zdokumentovaných přidělení. |
| [I/O](#io-guidance) |Povolte kompresi stránky databáze.<br/><br/>Povolte rychlé soubor inicializace pro datové soubory.<br/><br/>Omezit nebo zakázat automatické zvětšování v databázi.<br/><br/>Zakažte autoshrink v databázi.<br/><br/>Přesunete všechny databáze na datové disky, včetně systémové databáze.<br/><br/>Přesun serveru SQL Server chyba protokolu a trasování souboru adresářů do datových disků.<br/><br/>Nastavte výchozí zálohování a databáze umístění souborů.<br/><br/>Povolte uzamčených stránek.<br/><br/>Použijte opravy výkonu systému SQL Server. |
| [Konkrétní funkce](#feature-specific-guidance) |Zálohovat přímo do úložiště objektů blob. |

Další informace o *jak* a *proč* Chcete-li tyto optimalizace, přečtěte si podrobné informace a pokyny, které jsou uvedeny v následujících částech.

## <a name="vm-size-guidance"></a>Pokyny velikost virtuálního počítače

Výkon citlivých aplikací, je doporučeno používat následující [velikosti virtuálních počítačů](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json):

* **SQL Server Enterprise Edition**: DS3 nebo vyšší
* **SQL Server Standard a Web edice**: DS2 nebo vyšší

## <a name="storage-guidance"></a>Pokynů pro virtualizované úložiště

Podpora virtuálních počítačů služby DS-series (spolu s DSv2-series a GS-series) [Storage úrovně Premium](../premium-storage.md). Storage úrovně Premium se doporučuje pro všechny úlohy v produkčním prostředí.

> [!WARNING]
> Standardní úložiště má různou latenci a šířky pásma a doporučuje se pouze pro procesy vývoje/testování. Úlohy v produkčním prostředí by měl používat úložiště úrovně Premium.

Kromě toho doporučujeme vytvoření účtu úložiště Azure ve stejném datovém centru jako virtuální počítače systému SQL Server k přenosu zpožděním. Při vytváření účtu úložiště, zakážete geografická replikace není zaručena pořadí konzistentní zápisu na více disků. Místo toho zvažte nakonfigurování technologie obnovení po havárii systému SQL Server mezi dvěma datových center Azure. Další informace najdete v tématu [vysokou dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Disky pokyny

Existují tři typy hlavní disku na virtuální počítač Azure:

* **Disk s operačním systémem**: při vytváření virtuálního počítače Azure připojí platformou alespoň jeden disk (označený jako **C** jednotky) na virtuální počítač pro disk operačního systému. Tento disk je virtuální pevný disk uložený jako objekt blob stránky v úložišti.
* **Dočasným diskovým**: virtuální počítače Azure obsahovat jiné disku s názvem dočasným diskovým (označené jako **D**: jednotku). Toto je disk na uzlu, který lze použít pro pomocné místo.
* **Datové disky**: dalších disků. můžete také připojit k virtuálnímu počítači jako datové disky, a ty budou uloženy v úložišti jako objekty BLOB stránky.

Následující části popisují doporučení pro používání těchto různých disků.

### <a name="operating-system-disk"></a>Disk operačním systému

Disk operačního systému je virtuální pevný disk, který můžete spustit a připojit jako spuštěné verze operačního systému a je označený jako **C** jednotky.

Výchozí nastavení ukládání do mezipaměti zásady na disk s operačním systémem je **pro čtení a zápis**. Pro citlivé aplikace výkonu doporučujeme používat datových disků místo disku operačního systému. Najdete v části na datové disky níže.

### <a name="temporary-disk"></a>Dočasné disku

Na jednotce dočasné úložiště s názvem bez přípony, jako **D**: disk, není trvalý do Azure blob storage. Neukládejte databázové soubory uživatelů nebo souborů protokolů transakci uživatele na **D**: jednotky.

D-series, Dv2-series a virtuální počítače G-series jednotku na těchto virtuálních počítačích je založená na SSD. Pokud vaše úlohy provede výrazně využívá databáze TempDB (např. pro dočasné objekty nebo komplexní spojení), ukládání databáze TempDB na **D** jednotky může mít za následek vyšší propustnost databáze TempDB a snížení latence databázi TempDB.

Pro virtuální počítače, které podporují službu Premium Storage (DS-series, DSv2-series a GS-series) doporučujeme uložit databázi TempDB na disk, který podporuje službu Premium Storage s čtení povoleno ukládání do mezipaměti. Existuje jedna výjimka do tohoto doporučení; Pokud vaše databáze TempDB využití je náročné na zápis, můžete dosáhnout vyšší výkon uložením databáze TempDB na místní **D** disku, který je taky založená na SSD na velikosti těchto počítačů.

### <a name="data-disks"></a>Datové disky

* **Použití datových disků pro soubory protokolu a data**: Pokud nepoužíváte prokládání disků, použijte 2 Storage úrovně Premium [P30 disky](../premium-storage.md#scalability-and-performance-targets) kde jeden disk obsahuje soubory protokolu a dalších obsahuje data a soubory databáze TempDB. Každý disk úložiště Premium poskytuje řadu IOPs a šířky pásma (MB/s) v závislosti na jeho velikosti, jak je popsáno v následujícím článku: [pomocí úložiště Premium pro disky](../premium-storage.md). Pokud používáte disk proložení techniku, třeba prostory úložiště, je nutné umístit všechny soubory na stejném disku.

   > [!NOTE]
   > Při zřizování virtuálního počítače s SQL serverem na portálu, máte možnost úpravy konfigurace úložiště. V závislosti na konfiguraci nakonfiguruje Azure jeden nebo více disků. Více disků jsou sloučeny do jednoho úložiště fondu s proložení. Soubory protokolu a data jsou společně umístěny v této konfiguraci. Další informace najdete v tématu [konfiguraci úložiště pro virtuální počítače serveru SQL](virtual-machines-windows-sql-server-storage-configuration.md).

* **Disk proložení**: pro další propustnosti, můžete přidat další datové disky a používat prokládání disků. Pokud chcete určit počet datových disků, potřebujete analyzovat počtu IOPS a šířky pásma požadované pro soubory protokolu a pro data a soubory databáze TempDB. Všimněte si, že různé velikosti virtuálních počítačů mají různé limity počtu IOPs a šířky pásma, které jsou podporovány, podívejte se na tabulky na IOPS na [velikost virtuálního počítače](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Pomocí následujících pokynů:

  * Pro Windows 8 nebo Windows Server 2012 nebo novější, použijte [prostory úložiště](https://technet.microsoft.com/library/hh831739.aspx) přitom následující pokyny:

      1. Prokládání (stripe velikost) nastavte na 64 KB (65536 bajtů) pro zatížení OLTP a 256 KB (262 144 bajtů) pro úlohy datového skladu s cílem vyhnout vlivu na výkon z důvodu chybné zarovnání oddílu. Toto musí být nastavena v prostředí PowerShell.
      1. Nastavit počet sloupců = počet fyzických disků. Při konfiguraci maximálně 8 disky (ne uživatelského rozhraní správce serveru), pomocí prostředí PowerShell. 

    Například následující prostředí PowerShell vytvoří nový fond úložiště s velikostí prokládání 64 KB a počet sloupců na 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Windows 2008 R2 nebo starší můžete použít dynamické disky (OS prokládané svazky) a velikost stripe je vždy 64 KB. Všimněte si, že tato možnost je zastaralé od verze Windows 8 nebo Windows Server 2012. Informace najdete v tématu příkazu podpory v [virtuální disková služba je přechodu do rozhraní API správy úložišť systému Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Pokud používáte [prostory úložiště – přímé (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) se scénářem jako [instance clusteru převzetí služeb při selhání SQL serveru](virtual-machines-windows-portal-sql-create-failover-cluster.md), musíte nakonfigurovat jeden fond. Všimněte si, že i když jiné svazky mohou být vytvořeny na tomto jednoho fondu, budou všechny sdílet stejné vlastnosti, jako je například stejné zásady ukládání do mezipaměti. 

  * Určete počet disků, které jsou přidružené k fondu úložiště podle vašeho očekávání zatížení. Uvědomte si, že různé velikosti virtuálních počítačů povolit různý počet připojených datových disků. Další informace najdete v tématu [velikosti virtuálních počítačů](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Pokud nepoužíváte Storage úrovně Premium (scénáře vývoje/testování), doporučuje se přidat maximální počet datových disků, které podporuje vaše [velikost virtuálního počítače](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a používat prokládání disků.

* **Ukládání do mezipaměti zásad**: datových disků pro Storage úrovně Premium, povolit čtení ukládání do mezipaměti na datové disky pouze hostování datové soubory a databáze TempDB. Pokud nepoužíváte Storage úrovně Premium, nepovolujte žádné ukládání do mezipaměti na všech datových disků. Pokyny ke konfiguraci ukládání do mezipaměti na disku naleznete v následujících článcích. Model nasazení classic (ASM) najdete v tématu: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) a [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Model nasazení Azure Resource Manager najdete v tématu: [Set-AzureRMOSDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk?view=azurermps-4.4.1) a [Set-AzureRMVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmdatadisk?view=azurermps-4.4.1).

  > [!WARNING]
  > Zastavte službu systému SQL Server při změně nastavení mezipaměti disků virtuálního počítače Azure, aby se zabránilo možnost nedošlo k poškození databáze.

* **Velikost alokační jednotky systému souborů NTFS**: při formátování datový disk, je doporučeno používat velikost 64 KB alokační jednotky pro data a soubory protokolu, jakož i databáze TempDB.

* **Osvědčené postupy správy na disku**: Při odebrání datový disk nebo změna jeho typ mezipaměti zastavit službu systému SQL Server během změny. Při nastavení ukládání do mezipaměti se změnilo na disk operačního systému, Azure zastaví virtuální počítač, změní typ mezipaměti a restartuje virtuální počítač. Při změně nastavení mezipaměti na datový disk, virtuálního počítače nebyla zastavena, ale je odpojený od virtuálního počítače během změny a pak znovu připojit datový disk.

  > [!WARNING]
  > Nepodařilo se zastavit službu systému SQL Server během těchto operací může způsobit poškození databáze.

## <a name="io-guidance"></a>Pokyny vstupně-výstupních operací

* Nejlepších výsledků Storage úrovně Premium jsou dosáhnete, když paralelní aplikace a požadavky. Storage úrovně Premium je určen pro scénáře, kde je hloubka fronty vstupně-výstupní operace větší než 1, takže uvidíte žádné nebo téměř žádné zvýšení výkonu pro jednovláknové sériové požadavky (i když jsou náročné na úložiště). Například to by mohlo mít vliv výsledky testů jednovláknové nástrojů pro analýzu výkonu, jako je například SQLIO.

* Zvažte použití [databáze stránky komprese](https://msdn.microsoft.com/library/cc280449.aspx) jako může zvýšit výkon zatížení s intenzivním vstupně-výstupní operace. Komprese dat se však může zvýšit spotřeby procesoru na serveru databáze.

* Zvažte povolení inicializace rychlých souboru zkrátit dobu, která je požadována pro počáteční soubor přidělení. Abyste mohli využívat inicializace rychlých souboru, udělení účtu služby SQL Server (MSSQLSERVER) s SE_MANAGE_VOLUME_NAME a přidejte ho do **provádět úlohy údržby svazku** zásady zabezpečení. Pokud používáte image platformy SQL Server pro Azure, účet služby výchozí (NT Service\MSSQLSERVER) není přidán do **provádět úlohy údržby svazku** zásady zabezpečení. Jinými slovy inicializace rychlých souboru není povoleno v imagi platformy SQL Server Azure. Po přidání účtu služby SQL Server k **provádět úlohy údržby svazku** zásady zabezpečení, restartujte službu SQL Server. Je možné, úvahy o zabezpečení pro použití této funkce. Další informace najdete v tématu [inicializace souboru databáze](https://msdn.microsoft.com/library/ms175935.aspx).

* **operace autogrow** se považuje za jenom možnost pro neočekávané růstu. Řízení nebyla růstu protokolu a data na základě každodenní s automaticky zvětšovat. Pokud se používá k automatické zvětšování, předem zvětšení pomocí přepínače velikost souboru.

* Zajistěte, aby **autoshrink** vypnutá, aby se zabránilo zbytečným režijní náklady, který může negativně ovlivnit výkon.

* Přesunete všechny databáze na datové disky, včetně systémové databáze. Další informace najdete v tématu [přesunout systémové databáze](https://msdn.microsoft.com/library/ms345408.aspx).

* Přesun serveru SQL Server chyba protokolu a trasování souboru adresářů do datových disků. To můžete provést v nástroji SQL Server Configuration Manager tak, že kliknete pravým tlačítkem na instanci serveru SQL a výběr vlastností. Chyba nastavení souboru protokolu a trasování lze změnit v **parametry spuštění** kartě. Adresář Dump je uveden v **Upřesnit** kartě. Následující snímek obrazovky ukazuje, kde má být vyhledán parametr spuštění protokolu chyby.

    ![Snímek obrazovky protokolu chyb SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Nastavte výchozí zálohování a databáze umístění souborů. Použijte doporučení v tomto článku a proveďte změny v okně vlastností serveru. Pokyny najdete v tématu [zobrazit nebo změnit výchozí umístění pro Data a soubory protokolů (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Následující snímek obrazovky ukazuje, kde k provedení těchto změn.

    ![Soubory protokolu SQL dat a zálohování](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Povolte uzamčených stránek ke snížení vstupně-výstupní operace a všech aktivit stránkování. Další informace najdete v tématu [Povolit zámek stránky v možnosti paměti (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Pokud používáte systém SQL Server 2012, nainstalujte Service Pack 1 kumulativní aktualizací 10. Tato aktualizace obsahuje opravy pro nízký výkon na vstupně-výstupní operace při spuštění vyberte Select dočasné tabulky v systému SQL Server 2012. Informace najdete v tématu to [článku znalostní báze](http://support.microsoft.com/kb/2958012).

* Vezměte v úvahu všechny datové soubory byla zahájena komprese při přenosu a konec Azure.

## <a name="feature-specific-guidance"></a>Specifické funkce pokyny

Některá nasazení může dosáhnout výhody další výkonu pomocí složitější postupy konfigurace. V následujícím seznamu jsou uvedeny některé funkce serveru SQL Server, které vám může pomoct můžete dosáhnout lepšího výkonu:

* **Zálohování do úložiště Azure**: při provádění zálohování pro SQL Server běžící na virtuálních počítačích Azure, můžete použít [zálohování systému SQL Server na adresu URL](https://msdn.microsoft.com/library/dn435916.aspx). Tato funkce je k dispozici od verze SQL Server 2012 SP1 CU2 a doporučené pro zálohování na disky připojené data. Pokud je zálohování a obnovení do nebo ze služby Azure storage, postupujte podle doporučení uvedených v [SQL serveru zálohování na adresu URL osvědčené postupy a řešení potíží a obnovení ze zálohy uložené ve službě Azure Storage](https://msdn.microsoft.com/library/jj919149.aspx). Můžete také automatizovat tyto zálohy pomocí [automatizované zálohování pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).

    Starších než SQL Server 2012, můžete použít [zálohování systému SQL Server pro nástroj Azure](https://www.microsoft.com/download/details.aspx?id=40740). Tento nástroj může pomoci zvýšit zálohování propustnost pomocí více cílů zálohování stripe.

* **Datové soubory SQL serveru v Azure**: Tato nová funkce [datových souborů SQL serveru v Azure](https://msdn.microsoft.com/library/dn385720.aspx), je k dispozici od verze SQL Server 2014. Ukazuje vlastnosti porovnatelný z hlediska výkonu jako pomocí Azure datových disků, systémem SQL Server s datovými soubory v Azure.

## <a name="next-steps"></a>Další kroky

Osvědčené postupy zabezpečení, najdete v části [důležité informace o zabezpečení pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-security.md).

Zkontrolovat další články virtuálního počítače systému SQL Server na [SQL Server na Přehled virtuálních počítačů Azure](virtual-machines-windows-sql-server-iaas-overview.md). Pokud máte dotazy týkající se virtuální počítače systému SQL Server, najdete v článku [– nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md).
