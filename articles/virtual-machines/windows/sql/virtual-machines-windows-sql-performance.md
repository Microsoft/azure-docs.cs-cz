---
title: Osvědčené postupy z hlediska výkonu pro SQL Server v Azure | Dokumentace Microsoftu
description: Poskytuje osvědčené postupy pro optimalizaci výkonu systému SQL Server na virtuálních počítačích Microsoft Azure.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/19/2018
ms.author: jroth
ms.openlocfilehash: a7a24bde6cc34befee7de3bcbf13b96c8b641af2
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888904"
---
# <a name="performance-best-practices-for-sql-server-in-azure-virtual-machines"></a>Osvědčené postupy z hlediska výkonu pro SQL Server na Azure Virtual Machines

## <a name="overview"></a>Přehled

Tento článek obsahuje pokyny pro optimalizaci výkonu systému SQL Server na virtuálním počítači Microsoft Azure. Při spuštění systému SQL Server ve službě Azure Virtual Machines, doporučujeme dál používat stejný výkon databáze možností, které se vztahují k serveru SQL Server v prostředí serveru v místním ladění. Výkon relační databáze ve veřejném cloudu, ale závisí na mnoha faktorech, jako je například velikost virtuálního počítače a konfigurace datových disků.

[Image SQL serveru na webu Azure Portal zřídit](quickstart-sql-vm-create-portal.md) řídit se doporučenými postupy konfigurace úložiště. Další informace o konfiguraci úložiště, najdete v části [konfiguraci úložiště pro virtuální počítače s SQL serverem](virtual-machines-windows-sql-server-storage-configuration.md). Po zřízení, jestli nebude lepší uplatňovat další optimalizace popisovaných v tomto článku. Založit vaše volby na vašich úloh a ověření pomocí testování.

> [!TIP]
> Tento článek se zaměřuje na získání *nejlepší* výkonu pro SQL Server na virtuálních počítačích Azure. Pokud vaše úloha méně náročné, nemusejí být nutné každý optimalizace uvedených níže. Vezměte v úvahu požadavkům na výkon a vzory zatížení při hodnocení těchto doporučení.

## <a name="quick-check-list"></a>Rychlá kontrola seznamu

Následuje seznam Rychlá kontrola pro optimální výkon systému SQL Server na virtuálních počítačích Azure:

| Oblast | Optimalizace |
| --- | --- |
| [Velikost virtuálního počítače](#vm-size-guidance) |[DS3_v2](../sizes-general.md) nebo vyšší pro SQL Enterprise edition.<br/><br/>[DS2_v2](../sizes-general.md) nebo vyšší pro edice SQL Standard a Web. |
| [Úložiště](#storage-guidance) |Použití [Storage úrovně Premium](../premium-storage.md). Storage úrovně standard se doporučuje jenom pro vývoj a testování.<br/><br/>Zachovat [účtu úložiště](../../../storage/common/storage-create-storage-account.md) a virtuální počítač s SQL serverem ve stejné oblasti.<br/><br/>Zakázat Azure [geograficky redundantní úložiště](../../../storage/common/storage-redundancy.md) (geografickou replikaci) do účtu úložiště. |
| [Disky](#disks-guidance) |Použijte nejméně 2 [disky P30](../premium-storage.md#scalability-and-performance-targets) (pro soubory protokolů a 1 pro datové soubory a databáze TempDB; nebo stripe dvě nebo více disků a úložiště, všechny soubory na jednom svazku 1).<br/><br/>Vyhněte se použití operačního systému nebo dočasné disky úložiště databáze nebo protokolování.<br/><br/>Povolte čtení ukládání do mezipaměti na discích hostování datové soubory a datové soubory databáze TempDB.<br/><br/>Nepovolujte ukládání do mezipaměti na disky, který je hostitelem souboru protokolu.<br/><br/>Důležité: Zastavte službu systému SQL Server, když Změna nastavení mezipaměti pro disk virtuálního počítače Azure.<br/><br/>Prokládané více datové disky Azure a zajistit si vyšší propustnost vstupně-výstupních operací.<br/><br/>Formátovat dokument přidělení velikosti. |
| [VSTUPNĚ-VÝSTUPNÍCH OPERACÍ](#io-guidance) |Povolte kompresi stránky databáze.<br/><br/>Povolte rychlé soubor inicializace pro datové soubory.<br/><br/>Omezit zahrnout do automatického zvětšování databáze.<br/><br/>Zakážete automatické zmenšování v databázi.<br/><br/>Přesuňte všechny databáze na datové disky, včetně systémových databází.<br/><br/>SQL Server chybu protokolu a trasování adresářů se soubory přesuňte do datových disků.<br/><br/>Nastavte výchozí zálohování a databáze umístění souborů.<br/><br/>Povolte uzamčených stránek.<br/><br/>Použijte opravy výkonu systému SQL Server. |
| [Konkrétní funkce](#feature-specific-guidance) |Zálohovat přímo do úložiště objektů blob. |

Další informace o *jak* a *proč* Pokud chcete, aby tyto optimalizace, zkontrolujte podrobnosti a pokyny uvedené v následujících částech.

## <a name="vm-size-guidance"></a>Pokyny pro velikost virtuálního počítače

Pro citlivé aplikace s výkonem, se doporučuje použít následující [velikostech virtuálních počítačů](../sizes.md):

* **SQL Server Enterprise Edition**: DS3_v2 nebo vyšší
* **Edice Web a SQL Server Standard**: DS2_v2 nebo vyšší

[DSv2-series](../sizes-general.md#dsv2-series) virtuálních počítačů podporují službu premium storage, které doporučujeme pro zajištění nejlepšího výkonu. Velikost doporučená tady jsou směrné plány, ale skutečný velikost, kterou vyberete, závisí na vaše požadavky na pracovní zatížení. Virtuální počítače DSv2-series jsou virtuální počítače, které jsou vhodné pro různé úlohy, zatímco jiné velikosti počítačů optimalizovaných pro konkrétní úlohu typů pro obecné účely. Například [řady M-series](../sizes-memory.md#m-series) nabízí nejvyšší počet virtuálních procesorů a paměti pro i ty největší úlohy SQL serveru. [Řady GS-series](../sizes-memory.md#gs-series) a [DSv2-series 11-15](../sizes-memory.md#dsv2-series-11-15) jsou optimalizované pro paměť velké požadavky. Obě tyto řady jsou také dostupné v [core velikosti omezen](../../windows/constrained-vcpu.md), což šetří peníze pro výpočetní úlohy s nižší nároky. [Řada Ls-series](../sizes-storage.md) počítačů optimalizovaných pro Vysoká propustnost disku a vstupně-výstupních operací. Je důležité zvážit konkrétních úloh SQL serveru a použít tento výběr virtuálních počítačů řady a velikosti.

## <a name="storage-guidance"></a>Pokynů pro Storage

Podpora virtuálních počítačů řady DS-series (spolu s DSv2-series a GS-series) [Premium Storage](../premium-storage.md). Storage úrovně Premium se doporučuje pro všechny úlohy v produkčním prostředí.

> [!WARNING]
> Storage úrovně Standard má různé latencí a šířkou pásma a doporučuje se jen pro vývojové a testovací úlohy. Úlohy v produkčním prostředí by měl používat Premium Storage.

Kromě toho doporučujeme vytvořit účet úložiště Azure ve stejném datovém centru jako vaše virtuální počítače SQL serveru ke snížení prodlev při přenosu. Při vytváření účtu úložiště, zakážete geografická replikace není zaručeno pořadí konzistentní zapisovat na několik disků. Místo toho zvažte nakonfigurování technologie pro zotavení po havárii serveru SQL Server mezi dvěma datovými centry Azure. Další informace najdete v tématu [vysokou dostupnost a zotavení po havárii pro SQL Server ve službě Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Pokyny k disky

Existují tři typy hlavní disku k Virtuálnímu počítači Azure:

* **Disk s operačním systémem**: při vytváření virtuálního počítače Azure připojí platformu alespoň jeden disk (označené jako **C** jednotky) k virtuálnímu počítači pro disk operačního systému. Tento disk je virtuální pevný disk uložený jako objekt blob stránky v úložišti.
* **Dočasný disk**: virtuální počítače Azure obsahovat jiný disk říká dočasný disk (označené jako **D**: jednotka). Toto je disku v uzlu, který lze použít pro pomocné místo.
* **Datové disky**: další disky můžete také připojit k virtuálnímu počítači jako datové disky, a budou totiž uložené v úložišti jako objekty BLOB stránky.

Následující části popisují doporučení pro používání těchto různých discích.

### <a name="operating-system-disk"></a>Disk operačním systému

Disk s operačním systémem je virtuální pevný disk, který může bootovat a připojit jako spuštěnou verzi operačního systému a je označená jako **C** jednotky.

Výchozí nastavení ukládání do mezipaměti zásad na disk s operačním systémem je **r/w**. Pro citlivé aplikace výkonu doporučujeme použití datových disků místo disku s operačním systémem. V části pro datové disky níže.

### <a name="temporary-disk"></a>Dočasný disk

Jednotky dočasného úložiště označených jako **D**: jednotka, není trvale uložena do úložiště objektů blob v Azure. Neukládejte databázové soubory uživatelů nebo soubory protokolů transakcí uživatele na **D**: jednotky.

Pro řady D-series, řada Dv2-series a virtuální počítače řady G-series je dočasné jednotky na tyto virtuální počítače založené na jednotkách SSD. Pokud vaše úloha značně používá databáze tempdb (například dočasné objekty nebo komplexním spojením), uložení databáze TempDB na **D** jednotky může mít za následek větší propustnost v databázi TempDB a nižší latenci na databáze TempDB.

Pro virtuální počítače, které podporují službu Premium Storage (řady DS-series, DSv2-series a GS-series) doporučujeme uložit databázi TempDB na disk, který podporuje službu Premium Storage s čtení povoleno ukládání do mezipaměti. Existuje jedna výjimka tohoto doporučení; Pokud vaše využití databáze TempDB je náročné na zápis, můžete dosáhnout vyššího výkonu uložení databáze TempDB na místním **D** jednotky, což je také SSD podle těchto velikosti počítačů.

### <a name="data-disks"></a>Datové disky

* **Použití datových disků pro soubory protokolu a data**: Pokud nepoužíváte prokládání disků, použijte dvě služby Premium Storage [disky P30](../premium-storage.md#scalability-and-performance-targets) kde jeden disk obsahuje soubory protokolu a druhý obsahuje data a soubory databáze TempDB. Každý disk Storage úrovně Premium nabízí celou řadu IOPs a šířky pásma (MB/s) v závislosti na jejich velikost, jak je popsáno v následujícím článku [používat Premium Storage pro disky](../premium-storage.md). Pokud používáte disk prokládáním techniku, jako je například prostory úložiště, doporučujeme umístit všechna data a soubory protokolu na stejné jednotce.

   > [!NOTE]
   > Při zřizování virtuálního počítače s SQL serverem na portálu máte možnost upravit konfiguraci úložiště. V závislosti na konfiguraci Azure nakonfiguruje minimálně jeden disk. Více disků do fondu úložiště jednoho spolu se prokládání. Soubory protokolu a data jsou umístěny společně v této konfiguraci. Další informace najdete v tématu [konfiguraci úložiště pro virtuální počítače s SQL serverem](virtual-machines-windows-sql-server-storage-configuration.md).

* **Na disku prokládáním**: pro větší propustnost, můžete přidat další datové disky a použijte prokládání disků. Pokud chcete zjistit počet datových disků, potřebujete analyzovat počet IOPS a šířka pásma vyžadovaná pro soubory protokolů a pro vaše data a soubory databáze TempDB. Všimněte si, že různé velikosti virtuálních počítačů mají různá omezení počtu IOPs a šířky pásma podporované, najdete v tabulkách o hodnotách IOPS u [velikost virtuálního počítače](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Pomocí následujících pokynů:

  * Pro Windows 8 nebo Windows Server 2012 nebo novější, použijte [prostory úložiště](https://technet.microsoft.com/library/hh831739.aspx) přitom následující pokyny:

      1. Nastavte prokládání (stripe velikost) pro úlohy OLTP (65 536 bajty) 64 KB a 256 KB (262144 bajtů) pro úloh datových skladů negativní dopad na výkon kvůli chybné zarovnání oddílu. Musí být nastavena v prostředí PowerShell.
      1. Nastavte počet sloupců = počtem fyzických disků. Při konfiguraci víc než 8 disky (nikoli uživatelského rozhraní správce serveru), pomocí prostředí PowerShell. 

    Například následující příkaz Powershellu vytvoří nový fond úložiště s velikostí prokládání 64 KB a počet sloupců na 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Windows 2008 R2 nebo starší můžete použít dynamické disky (operační systém prokládané svazky) a velikost stripe je vždy 64 KB. Všimněte si, že tato možnost se už nepoužívá od verze Windows 8 nebo Windows Server 2012. Informace najdete v prohlášení o odborné pomoci v [virtuální diskovou službu je přechod na rozhraní API správy úložiště Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Pokud používáte [úložiště prostorů s přímým přístupem (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) se scénářem, jako je [instance clusteru převzetí služeb při selhání SQL serveru](virtual-machines-windows-portal-sql-create-failover-cluster.md), musíte nakonfigurovat jeden fond. Všimněte si, že i když různé svazky se dají vytvořit na tento jeden fond, budou všechny sdílet stejné vlastnosti, jako je například stejné zásady ukládání do mezipaměti.

  * Určete počet disků, které jsou přidružené k fondu úložiště podle vašich očekávání zatížení. Uvědomte si, že různé velikosti virtuálních počítačů povolit různý počet připojené datové disky. Další informace najdete v tématu [velikosti virtuálních počítačů](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Pokud nepoužíváte Storage úrovně Premium (scénáře vývoje/testování), doporučuje se přidat maximální počet datových disků, které podporuje vaše [velikost virtuálního počítače](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a použijte prokládání disků.

* **Zásady ukládání do mezipaměti**: Všimněte si následujících doporučení pro zásady v závislosti na konfiguraci úložiště ukládání do mezipaměti.

  * Pokud používáte samostatné disků pro soubory protokolu a data, povolte čtení ukládání do mezipaměti na datové disky hostování datové soubory a datové soubory databáze TempDB. To může způsobit významné zvýšení výkonu. Nepovolujte ukládání do mezipaměti na disku obsahující soubor protokolu, protože způsobuje vedlejší snížení výkonu.

  * Pokud používáte prokládání disků, bude přínosem pro většinu úloh z ukládání do mezipaměti pro čtení. Kvůli zvýšení výkonu se prokládání disků toto doporučení platí i v případě, že soubor protokolu je na stejné jednotce. V některých úloh náročné zápisu může dosáhnout lepšího výkonu s neexistující ukládání do mezipaměti. To se dá určit jenom pomocí testování.

  * Předchozí doporučení platí pro disků Premium Storage. Pokud nepoužíváte služby Premium Storage, nepovolujte žádné ukládání do mezipaměti pro všechny datové disky.

  * Další pokyny ke konfiguraci ukládání do mezipaměti na disku naleznete v následujících článcích. Modelu nasazení classic (ASM) naleznete v tématu: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) a [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Model nasazení Azure Resource Manageru najdete v článku: [Set-AzureRMOSDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk?view=azurermps-4.4.1) a [Set-AzureRMVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmdatadisk?view=azurermps-4.4.1).

     > [!WARNING]
     > Zastavte službu systému SQL Server při změně nastavení mezipaměti disků virtuálního počítače Azure, aby možnost všechny poškození databáze.

* **Velikost alokační jednotky NTFS**: při formátování datový disk, se doporučuje použít velikost 64 KB alokační jednotky pro dat a souborů protokolu, stejně jako databázi TempDB.

* **Osvědčené postupy správy na disku**: Při odebrání datového disku nebo změna jeho typ mezipaměti zastavit službu systému SQL Server během změny. Při změně nastavení ukládání do mezipaměti na disku s operačním systémem, Azure zastaví virtuální počítač, změní typ mezipaměti a restartuje virtuální počítač. Při změně nastavení mezipaměti na datový disk, virtuální počítač se zastaví, ale je odpojena od virtuálního počítače během změny a pak znovu připojit datový disk.

  > [!WARNING]
  > Nepodařilo se zastavit službu systému SQL Server během těchto operací může způsobit poškození databáze.

## <a name="io-guidance"></a>Pokyny k vstupně-výstupních operací

* Nejlepší výsledky díky službě Premium Storage se dosáhne, když paralelní aplikace a požadavků. Storage úrovně Premium je určená pro scénáře, kde je hloubka fronty vstupně-výstupní operace větší než 1, takže uvidíte malou nebo žádnou zvýšení výkonu pro požadavky na jednovláknový sériového portu (i v případě, že jsou náročné na úložiště). Například to může mít vliv výsledky testu s jedním vláknem nástroje pro analýzu výkonu, jako je například SQLIO.

* Zvažte použití [databáze stránky komprese](https://msdn.microsoft.com/library/cc280449.aspx) jak může zvýšit výkon úloh náročných na vstupně-výstupních operací. Komprese dat však může zvýšit využití procesoru na databázovém serveru.

* Zvažte povolení okamžité soubor inicializace zkrátit čas, který je požadován pro počáteční soubor přidělení. Umožní využít rychlé soubor inicializace udělit účtu služby SQL Server (MSSQLSERVER) s SE_MANAGE_VOLUME_NAME a přidejte ho do **provádět úlohy údržby svazku** zásady zabezpečení. Pokud používáte image platformy SQL Server pro Azure, účet služby výchozí (NT Service\MSSQLSERVER) není přidán do **provádět úlohy údržby svazku** zásady zabezpečení. Jinými slovy rychlé soubor inicializace není povolena v imagi platformy SQL Server Azure. Po přidání účtu služby SQL Server na **provádět úlohy údržby svazku** zásady zabezpečení, restartujte službu systému SQL Server. Může existovat aspekty zabezpečení pro používání této funkce. Další informace najdete v tématu [inicializace souboru databáze](https://msdn.microsoft.com/library/ms175935.aspx).

* **operace autogrow** se považuje za pouze možnost pro neočekávaném růstu. Není spravujte růst svých dat a protokolů na každodenní bázi s automaticky zvětšovat. Pokud se používá k automatické zvětšování, předem zvětšení souboru pomocí přepínače velikost.

* Ujistěte se, že **automatické zmenšování** je zakázané, aby se zabránilo zbytečnou režii, který může negativně ovlivnit výkon.

* Přesuňte všechny databáze na datové disky, včetně systémových databází. Další informace najdete v tématu [přesunout systémové databáze](https://msdn.microsoft.com/library/ms345408.aspx).

* SQL Server chybu protokolu a trasování adresářů se soubory přesuňte do datových disků. To můžete udělat v SQL Server Configuration Manager tak, že pravým tlačítkem myši na instanci SQL serveru a výběr vlastností. Nastavení souboru protokolu a trasování chyb lze změnit v **spouštěcí parametry** kartu. Výpis adresáře je zadán v **Upřesnit** kartu. Následující snímek obrazovky ukazuje, kde hledat pro parametr při spuštění protokolu chyby.

    ![Snímek obrazovky protokolu chyb SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Nastavte výchozí zálohování a databáze umístění souborů. Použijte doporučení v tomto článku a proveďte změny v okně Vlastnosti serveru. Pokyny najdete v tématu [zobrazit nebo změnit výchozí umístění pro Data a soubory protokolu (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Následující snímek obrazovky ukazuje, kam chcete tyto změny provést.

    ![Soubory protokolu SQL dat a zálohování](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Povolte uzamčených stránek ke snížení vstupně-výstupní operace a všech aktivit stránkování. Další informace najdete v tématu [povolit Uzamknout stránky v paměti (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Pokud používáte systém SQL Server 2012, nainstalujte Service Pack 1 kumulativní aktualizací 10. Tato aktualizace obsahuje opravy nízký výkon vstupně-výstupní operace při spuštění vyberte Select dočasné tabulky v SQL Server 2012. Informace najdete v tomto [znalostní báze Knowledge base](http://support.microsoft.com/kb/2958012).

* Vezměte v úvahu při přenosu vstup/výstup Azure komprese všechny datové soubory.

## <a name="feature-specific-guidance"></a>Pokyny týkající se funkcí

Některá nasazení může dosáhnout další zvýšit efektivitu pokročilejších technik konfigurace. V následujícím seznamu jsou uvedeny některé funkce serveru SQL Server, které vám mohou pomoci při dosažení lepšího výkonu:

* **Zálohování do Azure storage**: při provádění zálohování pro SQL Server běžící na virtuálních počítačích Azure, můžete použít [zálohování SQL serveru na adresu URL](https://msdn.microsoft.com/library/dn435916.aspx). Tato funkce je k dispozici od verze SQL Server 2012 SP1 kumulativní aktualizaci 2 se doporučuje pro zálohování připojenými datovými disky. Pokud je zálohování a obnovení do a z úložiště Azure, postupujte podle doporučení uvedených v [SQL Server zálohování na adresu URL osvědčené postupy a řešení potíží a obnovení ze zálohy uložené ve službě Azure Storage](https://msdn.microsoft.com/library/jj919149.aspx). Můžete automatizovat tyto zálohy pomocí [automatizované zálohování pro SQL Server ve službě Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).

    Starších než SQL Server 2012, můžete použít [zálohování SQL serveru do Azure nástroje](https://www.microsoft.com/download/details.aspx?id=40740). Tento nástroj může pomoci zvýšit zálohování propustnost pomocí více cílů zálohování stripe.

* **Datové soubory SQL serveru v Azure**: Tato nová funkce [datové soubory SQL serveru v Azure](https://msdn.microsoft.com/library/dn385720.aspx), je k dispozici od verze SQL Server 2014. Ukazuje srovnatelné výkonové charakteristiky jako datové disky Azure s použitím, systémem SQL Server s datovými soubory v Azure.

## <a name="next-steps"></a>Další kroky

Osvědčené postupy zabezpečení, najdete v části [aspekty zabezpečení pro SQL Server ve službě Azure Virtual Machines](virtual-machines-windows-sql-security.md).

Přečtěte si další články virtuálních počítačů s SQL serverem na [SQL Server na Azure Virtual Machines Overview](virtual-machines-windows-sql-server-iaas-overview.md). Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md).
