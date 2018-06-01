---
title: Nejlepší postupy z hlediska výkonu pro SQL Server v zásobníku virtuálních počítačích Azure
description: Obsahuje osvědčené postupy pro optimalizaci výkonu serveru SQL Server v Microsoft Azure zásobníku virtuálních počítačů.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: mabrigg
ms.reviewer: anajod
ms.openlocfilehash: 00c67503f5b9e0027cbb62520e392f56420a75e6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34701372"
---
# <a name="optimize-sql-server-performance"></a>Optimalizace výkonu serveru SQL

Tento článek obsahuje pokyny pro optimalizaci výkonu systému SQL Server na virtuálních počítačích Microsoft Azure zásobníku. Při spuštění systému SQL Server v Azure zásobníku virtuálních počítačů, použijte stejnou databázi optimalizace výkonu možnosti pro SQL Server v prostředí místní server. Výkon relační databáze ve cloudu Azure zásobníku závislá na mnoha faktorech. Faktory patří rodiny velikost virtuálního počítače a konfigurace datových disků.

Při vytváření bitové kopie systému SQL Server, [zvažte zřizování virtuálních počítačů na portálu Azure zásobníku](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). Stáhněte si rozšíření IaaS SQL z Marketplace správu na portálu Azure zásobníku správce a stáhnout zvoleného virtuální pevné disky SQL virtuálního počítače (VHD). Mezi ně patří SQL2014SP2, SQL2016SP1 a SQL2017.

> [!NOTE]  
> Při článek popisuje, jak pro zřízení virtuálního počítače s SQL serverem pomocí portálu Azure globální, pokyny platí také pro Azure zásobníku se ale tyto věci: SSD není k dispozici pro disk operačního systému, spravované disky nejsou k dispozici, a existují malé rozdíly v konfiguraci úložiště.

Získávání *nejlepší* výkonu pro SQL Server na virtuálních počítačích Azure zásobníku je hlavním cílem tohoto článku. Pokud vaše úlohy je méně náročné, nemusejí být nutné každých doporučené optimalizace. Zvažte požadavkům na výkon a vzory úlohy, jak vyhodnotit tato doporučení.

> [!NOTE]  
> Pokyny výkonu pro SQL Server na virtuálních počítačích Azure, najdete v tématu [v tomto článku](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).

## <a name="before-you-begin"></a>Než začnete
Následující kontrolní seznam je pro optimální výkon systému SQL Server na virtuálních počítačích Azure zásobníku:


|Oblast|Optimalizace|
|-----|-----|
|Velikost virtuálního počítače |[DS3](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) nebo vyšší pro SQL Server Enterprise edition.<br><br>[DS2](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) nebo vyšší pro SQL Server Standard edition a Web edition.|
|Úložiště |Použít rodina virtuální počítač, který podporuje [storage úrovně Premium](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-acs-differences).|
|Disky |Použití minimálně dvě datové disky (jeden pro soubory protokolů) a jeden pro datový soubor a databáze TempDB a zvolte velikost disku podle požadavků na kapacitu. Nastavení výchozího umístění souborů data na těchto discích, během instalace systému SQL Server.<br><br>Vyhněte se použití operačního systému nebo dočasné disků pro úložiště databáze nebo protokolování.<br>Prokládané více Azure datových disků získat vyšší propustnost vstupně-výstupní operace použití prostorů úložiště.<br><br>Formát s velikostí zdokumentovaných přidělení.|
|VSTUPNĚ-VÝSTUPNÍCH OPERACÍ|Povolte rychlé soubor inicializace pro datové soubory.<br><br>Omezit autogrow u databází s pevnou přiměřeně malých přírůstcích (64 MB - 256 MB).<br><br>Zakažte autoshrink v databázi.<br><br>Nastavte výchozí zálohování a databáze umístění souborů na datové disky, ne na disk operačního systému.<br><br>Povolte uzamčených stránek.<br><br>Použití systému SQL Server service Pack a kumulativní aktualizace.|
|Konkrétní funkce|Zálohování se přímo do úložiště objektů blob (Pokud je podporovaná verze systému SQL Server používá).|
|||

Další informace o *jak* a *proč* Chcete-li tyto optimalizace, přečtěte si podrobné informace a pokyny, které jsou uvedeny v následující části.

## <a name="virtual-machine-size-guidance"></a>Pokyny velikost virtuálního počítače

Pro aplikace náročné na výkon, následující [velikostí virtuálních počítačů](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) doporučují:

- **SQL Server Enterprise edition:** DS3 nebo vyšší

- **SQL Server Standard edition a Web edition:** DS2 nebo vyšší

S Azure zásobníku není žádné rozdíly ve výkonnosti mezi DS a DS_v2 rodiny řady virtuálního počítače.

## <a name="storage-guidance"></a>Pokynů pro virtualizované úložiště

DS-series (spolu s DSv2-series) virtuálních počítačů v Azure zásobníku poskytují maximální operačního systému disku a data propustnost disku (IOPS). Virtuální počítač z řady DS nebo DSv2 poskytuje až 1 000 IOPS pro disk operačního systému a až 2,300 IOPS na datový disk, bez ohledu na to, typ nebo velikost disku pro vybrané.

Propustnost dat disku je určen podle jednoznačně rodiny řady virtuálního počítače. Můžete [najdete v tomto článku](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) k identifikaci disku propustnost dat na řadu rodiny virtuálního počítače.

> [!NOTE]  
> Pro úlohy v produkčním prostředí vyberte virtuální počítač DS-series nebo DSv2-series zajistit maximální možné IOPS na operačním systému disku a datovými disky.

Při vytváření účtu úložiště v Azure zásobníku, možnost geografická replikace nemá žádný vliv, protože tato funkce není k dispozici v zásobníku Azure.

## <a name="disks-guidance"></a>Disky pokyny

Existují tři typy hlavní disku na virtuálním počítači Azure zásobníku:

- **Disk operačního systému:** při vytváření služby Azure zásobník virtuálního počítače platformě připojí alespoň jeden disk (označený jako **C** jednotku) k virtuálnímu počítači pro disk operačního systému. Tento disk je virtuální pevný disk uložený jako objekt blob stránky v úložišti.

- **Dočasným diskovým:** Azure zásobníku virtuálních počítačů obsahovat jiné disku s názvem dočasným diskovým (označené jako **D** jednotku). Toto je disk na uzlu, který lze použít pro pomocné místo.

- **Datové disky:** dalších disků můžete připojit k virtuálnímu počítači jako datové disky, a tyto disky jsou uložené v úložišti jako objekty BLOB stránky.

Následující části popisují doporučení pro používání těchto různých disků.

### <a name="operating-system-disk"></a>Disk operačním systému

Disk operačního systému je virtuální pevný disk, který můžete spustit a připojit jako spuštěné verze operačního systému a je označený jako **C** jednotky.

### <a name="temporary-disk"></a>Dočasné disku

Na jednotce dočasné úložiště s názvem bez přípony, jako **D** disk, není trvalý. Neukládejte žádná data, která jste ochotni ztratíte, například databázové soubory uživatelů nebo souborů protokolů transakci uživatele, na **D** jednotky.

Doporučujeme uložit databázi TempDB na datový disk, protože každý datový disk se poskytuje maximálně až 2,300 IOPS na datový disk.

### <a name="data-disks"></a>Datové disky

- **Pomocí datových disků pro soubory protokolu a data.** Pokud nepoužíváte prokládání disků, použijte dva disky data z virtuálního počítače, který podporuje službu Premium storage, kde jeden disk obsahuje soubory protokolu a dalších obsahuje data a soubory databáze TempDB. Každý datový disk poskytuje řadu IOPS a šířky pásma (MB/s) v závislosti na virtuální počítač rodiny, jak je popsáno v [velikostí virtuálních počítačů, které jsou podporovány v zásobníku Azure](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). Pokud používáte disk proložení techniku, třeba prostory úložiště, umístěte všechny soubory protokolu a data na stejné jednotce (včetně databáze TempDB). Tato konfigurace poskytuje maximální počet procesorů, které jsou k dispozici pro systém SQL Server využívat, bez ohledu na to, které musí soubor je v kterémkoli okamžiku.

> [!NOTE]  
> Při zřizování virtuálního počítače s SQL serverem na portálu, máte možnost úpravy konfigurace úložiště. V závislosti na konfiguraci nakonfiguruje zásobník Azure jeden nebo více disků. Více disků jsou sloučeny do jednoho úložiště fondu. Soubory protokolu a data jsou společně umístěny v této konfiguraci.

- **Prokládání disků:** pro další propustnosti, můžete přidat další datové disky a používat prokládání disků. Pokud chcete určit počet datových disků, které potřebujete, analýza počtu IOPS a šířky pásma požadované pro vaše soubory protokolů a pro data a soubory databáze TempDB. Všimněte si, že IOPS omezení platí za jeden datový disk založený na rodiny řady virtuálního počítače a není založen na velikost virtuálního počítače. Omezení šířky pásma sítě, ale jsou založené na velikost virtuálního počítače. Podívejte se na tabulky na [velikostí virtuálního počítače v Azure zásobníku](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) další podrobnosti. Pomocí následujících pokynů:

    - Pro Windows Server 2012 nebo novější, použijte [prostory úložiště](https://technet.microsoft.com/library/hh831739.aspx) přitom následující pokyny:

        1.  Nastavte prokládání (stripe velikost) na 64 KB (65 536 bajtů) pro online zpracování úlohy (OLTP) a 256 KB (262 144 bajtů) pro úlohy datového skladu předejdete vlivu na výkon z důvodu chybné zarovnání oddílu transakcí. Toto musí být nastavena v prostředí PowerShell.

        2.  Nastavit počet sloupců = počet fyzických disků. Při konfiguraci více než osm disky (ne uživatelského rozhraní správce serveru), pomocí prostředí PowerShell.

            Například následující prostředí PowerShell vytvoří nový fond úložiště s velikostí prokládání nastavena na 64 KB a počet sloupců na 2:

          ```PowerShell  
          $PoolCount = Get-PhysicalDisk -CanPool $True
          $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

          New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
          ```

- Určete počet disků, které jsou přidružené k fondu úložiště podle vašeho očekávání zatížení. Uvědomte si, že velikosti jiný virtuální počítač povolit různý počet připojených datových disků. Další informace najdete v tématu [velikostí virtuálních počítačů, které jsou podporovány v zásobníku Azure](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes).
- Chcete-li získat maximální možné IOPS pro datové disky, doporučuje se přidat maximální počet datových disků, které podporuje vaše [velikost virtuálního počítače](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) a používat prokládání disků.
- **Velikost alokační jednotky systému souborů NTFS:** při formátování datový disk, je doporučeno používat velikost 64 KB alokační jednotky pro data a soubory protokolu, jakož i databáze TempDB.
- **Postupy v oblasti správy na disku:** při odebírání datový disk, zastavte službu serveru SQL Server během změny. Navíc nastavení mezipaměti na discích se nemění neposkytuje žádné vylepšení výkonu.

> [!WARNING]  
> Nepodařilo se zastavit službu SQL během těchto operací může způsobit poškození databáze.

## <a name="io-guidance"></a>Pokyny vstupně-výstupních operací

- Zvažte povolení inicializace rychlých souboru zkrátit dobu, která je požadována pro počáteční soubor přidělení. Abyste mohli využívat inicializace rychlých souboru, udělíte účtu služby SQL Server (MSSQLSERVER) s **SE_MANAGE_VOLUME_NAME** a přidejte ho do **provádět úlohy údržby svazku** zabezpečení zásady. Pokud používáte image platformy SQL Server pro Azure, výchozí účet služby (**NT Service\MSSQLSERVER**) není přidán do **provádět úlohy údržby svazku** zásady zabezpečení. Jinými slovy inicializace rychlých souboru není povoleno v imagi platformy SQL Server Azure. Po přidání účtu služby SQL Server k **provádět úlohy údržby svazku** zásady zabezpečení, restartujte službu SQL Server. Je možné, úvahy o zabezpečení pro použití této funkce. Další informace najdete v tématu [inicializace souboru databáze](https://msdn.microsoft.com/library/ms175935.aspx).
- **Operace Autogrow** je možnost pro neočekávané růstu. Řízení nebyla růstu protokolu a data na základě každodenní s automaticky zvětšovat. Pokud se používá k automatické zvětšování, předem růst souboru pomocí **velikost** přepínače.
- Zajistěte, aby **autoshrink** vypnutá, aby se zabránilo zbytečným režijní náklady, který může negativně ovlivnit výkon.
- Nastavte výchozí zálohování a databáze umístění souborů. Použijte doporučení v tomto článku a proveďte změny v okně vlastností serveru. Pokyny najdete v tématu [zobrazit nebo změnit výchozí umístění pro Data a soubory protokolů (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Následující snímek obrazovky ukazuje, kde tyto změny:

    > ![Zobrazení nebo změna výchozího umístění](./media/sql-server-vm-considerations/image1.png)

- Povolte uzamčených stránek ke snížení vstupně-výstupní operace a všech aktivit stránkování. Další informace najdete v tématu [Povolit zámek stránky v možnosti paměti (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- Vezměte v úvahu všechny datové soubory byla zahájena komprese při přenosu a konec zásobníku Azure, včetně záloh.

## <a name="feature-specific-guidance"></a>Specifické funkce pokyny

Některá nasazení může dosáhnout výhody další výkonu pomocí složitější postupy konfigurace. V následujícím seznamu jsou uvedeny některé funkce serveru SQL Server, které mohou pomoci dosáhnout lepšího výkonu:

- **Zálohovat do Azure** **úložiště.** Při provádění zálohování pro SQL Server běžící ve virtuálních počítačích Azure zásobníku, můžete použít zálohování systému SQL Server na adresu URL. Tato funkce je k dispozici od verze SQL Server 2012 SP1 CU2 a doporučené pro zálohování na disky připojené data.

    Při zálohování nebo obnovení pomocí úložiště Azure, postupujte podle doporučení v [SQL serveru zálohování na adresu URL osvědčené postupy a řešení potíží](https://msdn.microsoft.com/library/jj919149.aspx) a [obnovení ze zálohy uložené ve službě Microsoft Azure](https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017). Můžete také automatizovat tyto zálohy pomocí [automatizované zálohování pro SQL Server v Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup).

-   **Zálohovat do Azure zásobníku úložiště.** Můžete zálohovat do Azure zásobníku úložiště podobným způsobem jako u zálohování na Azure Storage. Když vytvoříte zálohu uvnitř SQL Server Management Studio (SSMS), musíte ručně zadat informace o konfiguraci. Aplikace SSMS nelze použít k vytvoření kontejneru úložiště nebo podpis sdíleného přístupu. Aplikace SSMS pouze připojí k předplatná Azure, ne předplatných Azure zásobníku. Místo toho musíte vytvořit účet úložiště, kontejneru a podpis sdíleného přístupu na portálu Azure zásobníku nebo pomocí prostředí PowerShell.

    Když umístíte informace do dialogu zálohování systému SQL Server:

    ![Zálohování systému SQL Server](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > Podpis sdíleného přístupu je token SAS z portálu Azure zásobníku bez úvodního '?' v řetězci. Pokud používáte funkci kopírování z portálu, budete muset odstranit úvodního '?' token pro práci v systému SQL Server.

    Jakmile budete mít cíl zálohování nastavte a nakonfigurované v systému SQL Server, můžete pak zálohovat do úložiště objektů blob Azure zásobníku.

## <a name="next-steps"></a>Další postup

[Pomocí služby nebo vytváření aplikací pro Azure zásobníku](azure-stack-considerations.md)