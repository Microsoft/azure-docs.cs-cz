---
title: Osvědčené postupy z hlediska výkonu pro SQL Server na virtuálních počítačích Azure Stack
description: Poskytuje osvědčené postupy pro optimalizaci výkonu SQL serveru v Microsoft Azure Stack Virtual Machines.
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
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.openlocfilehash: 8e577a95fc3cda3aafe1273cbc6b4e3c4fbb0317
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304348"
---
# <a name="optimize-sql-server-performance"></a>Optimalizace výkonu SQL serveru

Tento článek obsahuje pokyny pro optimalizaci výkonu systému SQL Server na virtuálních počítačích Microsoft Azure Stack. Při spuštění systému SQL Server na virtuálních počítačích Azure Stack, použijte stejnou databázi optimalizace výkonu možnosti pro SQL Server server v místním prostředí. Výkon relační databáze do cloudu Azure Stack je závislá na mnoha faktorech. Mezi faktory patří řady velikostí virtuálních počítačů a konfigurace datových disků.

Při vytváření imagí SQL serveru, [zvažte zřízení virtuálních počítačů na portálu Azure Stack](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). Stáhněte si rozšíření SQL IaaS z Marketplace správy portálu pro správu Azure Stack a stáhněte si podle vašeho výběru virtuální pevné disky SQL virtuálního počítače (VHD). Patří mezi ně SQL2014SP2 SQL2016SP1 a SQL2017.

> [!NOTE]  
> Přestože tento článek popisuje, jak zřizovat virtuální počítače s SQL serverem pomocí globální webu Azure portal, pokyny platí také pro Azure Stack s těmito rozdíly: SSD není k dispozici pro disk s operačním systémem, spravované disky nejsou k dispozici a jsou drobné rozdíly v konfiguraci úložiště.

Začínáme *nejlepší* výkonu pro SQL Server na virtuálních počítačích Azure Stack je hlavním cílem tohoto článku. Pokud vaše úloha méně náročné, nemusejí být nutné každý doporučený optimalizace. Vezměte v úvahu požadavkům na výkon a vzory zatížení při hodnocení těchto doporučení.

> [!NOTE]  
> Průvodce výkonem pro SQL Server na virtuálních počítačích Azure, najdete v tématu [v tomto článku](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).

## <a name="before-you-begin"></a>Před zahájením
Následující kontrolní seznam je pro zajištění optimálního výkonu systému SQL Server na virtuálních počítačích Azure Stack:


|Oblast|Optimalizace|
|-----|-----|
|Velikost virtuálního počítače |[DS3](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) nebo vyšší pro SQL Server Enterprise edition.<br><br>[DS2](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) nebo vyšší pro SQL Server Standard edition a verze Web edition.|
|Storage |Použití virtuálních počítačů řady, který podporuje [storage úrovně Premium](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-acs-differences).|
|Disky |Používají minimálně dva datové disky (jeden pro soubory protokolů) a jeden pro datový soubor a databázi TempDB a zvolte velikost disku, na základě vašich potřeb kapacity. Nastavte výchozí umístění souborů dat na tyto disky, během instalace systému SQL Server.<br><br>Vyhněte se použití operačního systému nebo dočasné disky úložiště databáze nebo protokolování.<br>Prokládané více datové disky Azure a zajistit si vyšší propustnost vstupně-výstupních operací pomocí prostorů úložiště.<br><br>Formátovat dokument přidělení velikosti.|
|VSTUPNĚ-VÝSTUPNÍCH OPERACÍ|Povolte rychlé soubor inicializace pro datové soubory.<br><br>Omezit automatické zvětšování databáze přírůstkem měly být přiměřeně malé pevné (64 MB - 256 MB).<br><br>Zakážete automatické zmenšování v databázi.<br><br>Nastavte výchozí zálohování a databáze umístění souborů pro datové disky, nikoli disk s operačním systémem.<br><br>Povolte uzamčených stránek.<br><br>Použijte aktualizace systému SQL Server service Pack a kumulativním aktualizacím.|
|Konkrétní funkce|Zálohování se přímo do úložiště objektů blob (Pokud je podporovaná ve verzi systému SQL Server používá).|
|||

Další informace o *jak* a *proč* Pokud chcete, aby tyto optimalizace, zkontrolujte podrobnosti a pokyny k dispozici v následujících částech.

## <a name="virtual-machine-size-guidance"></a>Pokyny pro velikost virtuálního počítače

Pro aplikace náročné na výkon, následující [velikostí virtuálních počítačů](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) doporučují:

- **SQL Server Enterprise edition:** DS3 nebo vyšší

- **Verze Web edition a SQL Server Standard edition:** DS2 nebo vyšší

S využitím Azure stacku neexistuje žádné rozdíly ve výkonnosti mezi řadu řady virtuálních počítačů DS a DS_v2.

## <a name="storage-guidance"></a>Pokynů pro Storage

Virtuální počítače řady DS-series (spolu s DSv2-series) ve službě Azure Stack poskytují maximální verzi operačního systému disku a diskových propustnost disku (IOPS). Virtuální počítač z řady DS nebo DSv2 poskytuje až 1 000 vstupně-výstupních operací pro disk s operačním systémem a až 2,300 IOPS na datový disk, bez ohledu na typ nebo velikost zvolené disku.

Propustnost datového disku se určuje podle jednoznačně řady řady virtuálních počítačů. Je možné [najdete v tomto článku](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) k identifikaci dat propustnost disků jednotlivých virtuálních počítačů řady řady.

> [!NOTE]  
> Pro produkční úlohy vyberte virtuální počítač řady DS nebo DSv2-series, zajistit maximální možné vstupně-výstupních operací na operačním systému disku a datové disky.

Při vytváření účtu úložiště ve službě Azure Stack, geografickou replikaci možnost nemá žádný vliv, protože tato funkce není k dispozici ve službě Azure Stack.

## <a name="disks-guidance"></a>Pokyny k disky

Existují tři typy hlavní disku na virtuálním počítači Azure Stack:

- **Disk s operačním systémem:** Při vytváření virtuálního počítače s Azure Stack platformě připojí alespoň jeden disk (označené jako **C** jednotky) k virtuálnímu počítači pro disk operačního systému. Tento disk je virtuální pevný disk uložený jako objekt blob stránky v úložišti.

- **Dočasný disk:** Virtuální počítače Azure Stack obsahovat jiný disk říká dočasný disk (označené jako **D** disku). Toto je disku v uzlu, který lze použít pro pomocné místo.

- **Datové disky:** Další disky můžete připojit k virtuálnímu počítači jako datové disky, a tyto disky se ukládají v úložišti jako objekty BLOB stránky.

Následující části popisují doporučení pro používání těchto různých discích.

### <a name="operating-system-disk"></a>Disk operačním systému

Disk s operačním systémem je virtuální pevný disk, který může bootovat a připojit jako spuštěnou verzi operačního systému a je označená jako **C** jednotky.

### <a name="temporary-disk"></a>Dočasný disk

Jednotky dočasného úložiště označených jako **D** jednotka, není trvalý. Neukládejte žádná data, jste ochotni ztratíte, jako jsou databázové soubory uživatelů nebo soubory protokolů transakcí uživatele, na **D** jednotky.

Doporučujeme ukládat databázi TempDB na datový disk, protože každý datový disk poskytuje maximálně až 2,300 IOPS na data disk.

### <a name="data-disks"></a>Datové disky

- **Použití datových disků pro soubory protokolu a data.** Pokud nepoužíváte prokládání disků, použijte dva datové disky z virtuálního počítače podporující Premium storage, ve kterém jeden disk obsahuje soubory protokolů a druhý obsahuje data a soubory databáze TempDB. Každý datový disk nabízí celou řadu IOPS a šířky pásma (MB/s) v závislosti na virtuální počítač řady, jak je popsáno v [velikostí virtuálních počítačů, které jsou podporované ve službě Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). Pokud používáte prokládání disků techniku, jako je například prostory úložiště, umístěte všechny soubory protokolu a data na stejné jednotce (včetně TempDB). Tato konfigurace poskytuje maximální počet procesorů, které jsou k dispozici pro SQL Server používat, bez ohledu na to, která potřebuje soubor je v určitém čase.

> [!NOTE]  
> Při zřizování virtuálního počítače s SQL serverem na portálu máte možnost upravit konfiguraci úložiště. V závislosti na vaší konfiguraci služby Azure Stack nakonfiguruje minimálně jeden disk. Více disků jsou sloučeny do jednoho úložiště fondu. Soubory protokolu a data jsou umístěny společně v této konfiguraci.

- **Prokládání disků:** Pro větší propustnost můžete přidat další datové disky a použijte prokládání disků. Pokud chcete zjistit počet datových disků, které potřebujete, analyzujte počet IOPS a šířka pásma vyžadovaná pro soubory protokolu a pro vaše data a soubory databáze TempDB. Všimněte si, že limity IOPS na disk data podle řady virtuálních počítačů řady a není založen na velikosti virtuálního počítače. Omezení šířky pásma sítě, ale jsou založeny na velikosti virtuálního počítače. Zobrazit tabulky na [velikosti virtuálních počítačů ve službě Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) další podrobnosti. Pomocí následujících pokynů:

    - Systém Windows Server 2012 nebo novější, použijte [prostory úložiště](https://technet.microsoft.com/library/hh831739.aspx) přitom následující pokyny:

        1.  Nastavte prokládání (stripe velikost) na 64 KB (65 536 bajtů) pro online zpracování úlohy (OLTP) a 256 KB (262 144 bajtů) pro úloh datových skladů do negativní dopad na výkon kvůli chybné zarovnání oddílu transakcí. Musí být nastavena v prostředí PowerShell.

        2.  Nastavte počet sloupců = počtem fyzických disků. Při konfiguraci více než osm disky (nikoli uživatelského rozhraní správce serveru), pomocí prostředí PowerShell.

            Například následující příkaz Powershellu vytvoří nový fond úložiště s velikostí prokládání nastavena na 64 KB a počet sloupců na 2:

          ```PowerShell  
          $PoolCount = Get-PhysicalDisk -CanPool $True
          $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

          New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
          ```

- Určete počet disků, které jsou přidružené k fondu úložiště podle vašich očekávání zatížení. Uvědomte si, že různých velikostí virtuálních počítačů povolit různý počet připojené datové disky. Další informace najdete v tématu [velikostí virtuálních počítačů, které jsou podporované ve službě Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes).
- Aby bylo možné získat maximální možné vstupně-výstupních operací datové disky, doporučujeme přidat maximální počet datových disků, nepodporuje váš [velikost virtuálního počítače](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) a použijte prokládání disků.
- **Velikost alokační jednotky systému souborů NTFS:** Při formátování datový disk, se doporučuje použít velikost 64 KB alokační jednotky pro dat a souborů protokolu, stejně jako databázi TempDB.
- **Postupy správy disku:** Při odebrání datového disku, zastavte službu systému SQL Server během změny. Kromě toho nastavení mezipaměti na discích se nemění neposkytuje žádná zlepšení výkonu.

> [!WARNING]  
> Nepodařilo se zastavit službu SQL během těchto operací může způsobit poškození databáze.

## <a name="io-guidance"></a>Pokyny k vstupně-výstupních operací

- Zvažte povolení okamžité soubor inicializace zkrátit čas, který je požadován pro počáteční soubor přidělení. Umožní využít rychlé soubor inicializace udělíte účtu služby SQL Server (MSSQLSERVER) s **SE_MANAGE_VOLUME_NAME** a přidejte ho do **provádět úlohy údržby svazku** zabezpečení zásady. Pokud používáte image platformy SQL Server pro Azure, výchozí účet služby (**NT Service\MSSQLSERVER**) není přidán do **provádět úlohy údržby svazku** zásady zabezpečení. Jinými slovy rychlé soubor inicializace není povolena v imagi platformy SQL Server Azure. Po přidání účtu služby SQL Server na **provádět úlohy údržby svazku** zásady zabezpečení, restartujte službu systému SQL Server. Může existovat aspekty zabezpečení pro používání této funkce. Další informace najdete v tématu [inicializace souboru databáze](https://msdn.microsoft.com/library/ms175935.aspx).
- **Operace Autogrow** je možnost pro neočekávaném růstu. Není spravujte růst svých dat a protokolů na každodenní bázi s automaticky zvětšovat. Pokud se používá automatické zvětšování, předem růst pomocí souboru **velikost** přepnout.
- Ujistěte se, že **automatické zmenšování** je zakázané, aby se zabránilo zbytečnou režii, který může negativně ovlivnit výkon.
- Nastavte výchozí zálohování a databáze umístění souborů. Použijte doporučení v tomto článku a proveďte změny v okně Vlastnosti serveru. Pokyny najdete v tématu [zobrazit nebo změnit výchozí umístění pro Data a soubory protokolu (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Následující snímek obrazovky ukazuje, kam chcete tyto změny:

    > ![Zobrazit nebo změnit výchozí umístění](./media/sql-server-vm-considerations/image1.png)

- Povolte uzamčených stránek ke snížení vstupně-výstupní operace a všech aktivit stránkování. Další informace najdete v tématu [povolit Uzamknout stránky v paměti (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- Vezměte v úvahu při přenosu vstup/výstup služby Azure Stack, včetně záloh komprese všechny datové soubory.

## <a name="feature-specific-guidance"></a>Pokyny týkající se funkcí

Některá nasazení může dosáhnout další zvýšit efektivitu pokročilejších technik konfigurace. V následujícím seznamu jsou uvedeny některé funkce serveru SQL Server, které mohou pomoci dosáhnout vyššího výkonu:

- **Zálohování do Azure** **úložiště.** Při provádění zálohování pro SQL Server běžící na virtuálních počítačích Azure Stack, můžete použít k zálohování SQL serveru na adresu URL. Tato funkce je k dispozici od verze SQL Server 2012 SP1 kumulativní aktualizaci 2 se doporučuje pro zálohování připojenými datovými disky.

    Při zálohování nebo obnovení pomocí úložiště Azure, postupujte podle doporučení v [SQL Server zálohování na adresu URL osvědčené postupy a řešení potíží](https://msdn.microsoft.com/library/jj919149.aspx) a [obnovení ze záloh uložených v Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017). Můžete automatizovat tyto zálohy pomocí [automatizované zálohování pro SQL Server ve službě Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup).

-   **Zálohování do úložiště Azure Stack.** Do úložiště Azure Stack podobným způsobem můžete zálohovat stejně jako u zálohování do Azure Storage. Při vytváření zálohy v SQL Server Management Studio (SSMS), budete muset zadat informace o konfiguraci ručně. SSMS nelze použít k vytvoření kontejneru úložiště nebo sdílený přístupový podpis. SSMS připojuje jenom pro předplatná Azure, nejsou předplatná Azure Stack. Místo toho budete muset vytvořit účet úložiště, kontejner a sdílený přístupový podpis na portálu Azure Stack nebo pomocí Powershellu.

    Když umístíte informace do dialogového okna zálohování SQL serveru:

    ![Zálohování SQL serveru](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > Sdílený přístupový podpis je SAS token z portálu Azure Stack bez úvodního "?" v řetězci. Pokud používáte funkci kopírování z portálu, musíte odstranit úvodního "?" pro token pro práci v rámci SQL serveru.

    Jakmile budete mít cíl zálohování nastavte a nakonfigurován v systému SQL Server, můžete pak zálohovat do úložiště objektů blob v Azure stacku.

## <a name="next-steps"></a>Další postup

[Pomocí služby nebo vytvářet aplikace pro Azure Stack](azure-stack-considerations.md)