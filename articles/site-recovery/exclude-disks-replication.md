---
title: Vyloučení disků z replikace pomocí Azure Site Recovery
description: Postup vyloučení disků z replikace do Azure pomocí Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 15989fbfd65f758eb777c5170c217aba8707e0be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008255"
---
# <a name="exclude-disks-from-disaster-recovery"></a>Vyloučení disků z zotavení po havárii

Tento článek popisuje, jak vyloučit disky z replikace během zotavení po havárii z místního prostředí do Azure pomocí [Azure Site Recovery](site-recovery-overview.md). Disky můžete z replikace vyloučit z několika důvodů:

- Proto se nereplikují neimportovaná data na vyloučeném disku.
- K optimalizaci spotřebované šířky pásma replikace nebo prostředků na cílové straně.
- Uložení prostředků úložiště a sítě tím, že se nereplikují data, která nepotřebujete.
- Virtuální počítače Azure dosáhly Site Recovery omezení replikace.


## <a name="supported-scenarios"></a>Podporované scénáře

Z replikace můžete vyloučit disky, které jsou shrnuté v tabulce.

**Z Azure do Azure** | **Z VMware do Azure** | **Z Hyper-V do Azure** | **Fyzický server do Azure**
--- | --- | --- | ---
Yes | Yes | Yes | Yes

## <a name="exclude-limitations"></a>Vyloučit omezení

**Omezení** | **Virtuální počítače Azure** | **Virtuální počítače VMware** | **Virtuální počítače Hyper-V**
--- | --- | ---
**Typy disků** | Z replikace můžete vyloučit základní disky.<br/><br/> Nemůžete vyloučit disky operačního systému ani dynamické disky. Dočasné disky jsou ve výchozím nastavení vyloučené. | Z replikace můžete vyloučit základní disky.<br/><br/> Nemůžete vyloučit disky operačního systému ani dynamické disky. | Z replikace můžete vyloučit základní disky.<br/><br/> Nemůžete vyloučit disky operačního systému. Doporučujeme, abyste nevylučovali dynamické disky. Site Recovery nemůže zjistit, které VHS je na virtuálním počítači hosta základní nebo dynamické. Pokud se nevylučují všechny závislé disky dynamických svazků, bude chráněný dynamický disk na virtuálním počítači s podporou převzetí služeb při selhání diskem, který není přístupný, a data na tomto disku nejsou dostupná.
**Replikace disku** | Nemůžete vyloučit disk, který se replikuje.<br/><br/> Zakažte a znovu povolte replikaci pro virtuální počítač. |  Nemůžete vyloučit disk, který se replikuje. |  Nemůžete vyloučit disk, který se replikuje.
**Služba mobility (VMware)** | Není relevantní | Disky můžete vyloučit jenom na virtuálních počítačích, na kterých je nainstalovaná služba mobility.<br/><br/> To znamená, že musíte ručně nainstalovat službu mobility na virtuální počítače, pro které chcete vyloučit disky. Nemůžete použít mechanismus nabízené instalace, protože nainstaluje službu mobility jenom po povolení replikace. | Není relevantní.
**Přidat nebo odebrat** | Spravované disky můžete do virtuálních počítačů Azure s podporou replikace přidat pomocí spravovaných disků. Disky nejde odebrat na virtuálních počítačích Azure s podporou replikace. | Po povolení replikace nelze disky přidat ani odebrat. Zakažte a znovu povolte replikaci a přidejte disk. | Po povolení replikace nelze disky přidat ani odebrat. Zakažte a znovu povolte replikaci.
**Převzetí služeb při selhání** | Pokud aplikace potřebuje disk, který jste vyloučili, po převzetí služeb při selhání budete muset disk vytvořit ručně, aby se mohla spustit replikovaná aplikace.<br/><br/> Případně můžete disk vytvořit během převzetí služeb při selhání virtuálního počítače integrací služby Azure Automation do plánu obnovení. | Pokud vyloučíte disk, který aplikace potřebuje, po převzetí služeb při selhání ho ručně vytvořte v Azure. | Pokud vyloučíte disk, který aplikace potřebuje, po převzetí služeb při selhání ho ručně vytvořte v Azure.
**Místní navrácení služeb po obnovení – disky vytvořené ručně** | Není relevantní | **Virtuální počítače s Windows**: disky vytvořené ručně v Azure se nepovedlo vrátit zpátky. Pokud například při selhání převezmete tři disky a vytvoříte dva disky přímo na virtuálním počítači Azure, navrátí se po obnovení pouze tři disky, u kterých došlo k převzetí služeb při selhání.<br/><br/> **Virtuální počítače Linux**: disky vytvořené ručně v Azure se nepovedlo obnovit. Pokud například při selhání převezmete tři disky a na virtuálním počítači Azure vytvoříte dva disky, navrátí se všechna pět. Ručně vytvořené disky nemůžete vyloučit z navrácení služeb po obnovení. | Disky vytvořené ručně v Azure se nepovedlo vrátit zpátky. Pokud například při selhání převezmete tři disky a vytvoříte dva disky přímo na virtuálním počítači Azure, navrátí se zpět pouze tři disky, u kterých došlo k převzetí služeb při selhání.
**Místní navrácení služeb po obnovení – vyloučené disky** | Není relevantní | Pokud navrátíte navrácení služeb po obnovení původnímu počítači, konfigurace disku pro navrácení služeb po obnovení neobsahuje vyloučené disky. Disky vyloučené z replikace z VMware do Azure nejsou k dispozici na virtuálním počítači pro navrácení služeb po obnovení. | Po navrácení služeb po obnovení do původního umístění technologie Hyper-V zůstane konfigurace disku pro navrácení služeb po obnovení stejná jako u původního zdrojového disku virtuálního počítače. Disky vyloučené z replikace z lokality Hyper-V do Azure jsou dostupné na virtuálním počítači pro navrácení služeb po obnovení.



## <a name="typical-scenarios"></a>Typické scénáře

Příklady četnosti změn dat, které jsou skvělými kandidáty pro vyloučení, zahrnují zápisy do stránkovacího souboru (pagefile.sys) a zapisuje do souboru tempdb Microsoft SQL Server. V závislosti na zatížení a subsystému úložiště může stránkovací soubor a soubory tempdb registrovat značné množství změn. Replikace tohoto typu dat do Azure je náročná na prostředky.

- K optimalizaci replikace pro virtuální počítač s jedním virtuálním diskem, který obsahuje operační systém i stránkovací soubor, můžete:
    1. Rozdělíte jeden virtuální disk na dva virtuální disky. Jeden virtuální disk obsahuje operační systém, druhý stránkovací soubor.
    2. Disk se stránkovacím souborem vyloučíte z replikace.

- K optimalizaci replikace pro disk, který obsahuje Microsoft SQL Server soubor tempdb i soubor databáze systému, můžete:
    1. Systémovou databázi a databázi tempdb budete uchovávat na dvou různých discích.
    2. Disk s databází tempdb vyloučíte z replikace.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>Příklad 1: Vyloučení disku s databází tempdb systému SQL Server

Pojďme se podívat na to, jak zpracovat vyloučení disku, převzetí služeb při selhání a převzetí služeb při selhání pro zdroj SQL Server Windows VM-* * SalesDB * * *, pro které chceme databázi tempdb vyloučit. 

### <a name="exclude-disks-from-replication"></a>Vyloučení disků z replikace

Tyto disky máme na zdrojovém virtuálním počítači s Windows SalesDB.

**Název disku** | **Disk hostovaného operačního systému** | **Písmeno jednotky** | **Datový typ disku**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Disk s operačním systémem.
DB-Disk1| Disk1 | D:\ | Systémová databáze SQL a uživatel Databáze1.
DB-Disk2 (disk vyloučený z ochrany) | Disk2 | E:\ | Dočasné soubory.
DB-Disk3 (disk vyloučený z ochrany) | Disk3 | F:\ | Databáze tempdb SQL<br/><br/> Cesta ke složce – F:\MSSQL\Data\. Před převzetím služeb při selhání si poznamenejte cestu ke složce.
DB Disk4 | Disk4 |G:\ | Uživatelská databáze 2

1. Povolujeme replikaci pro virtuální počítač SalesDB.
2. Vyloučíme disk2 a Disk3 z replikace, protože data na těchto discích jsou dočasná. 


### <a name="handle-disks-during-failover"></a>Zpracování disků během převzetí služeb při selhání

Vzhledem k tomu, že se disky při převzetí služeb při selhání do Azure nereplikují, neexistují tyto disky ve virtuálním počítači Azure vytvořeném po převzetí Virtuální počítač Azure obsahuje disky shrnuté v této tabulce.

**Disk hostovaného operačního systému** | **Písmeno jednotky** | **Datový typ disku**
--- | --- | ---
Disk0 | C:\ | Disk s operačním systémem.
Disk1 | E:\ | Dočasné úložiště<br/><br/>Azure přidá tento disk. Vzhledem k tomu, že disk2 a Disk3 byly vyloučeny z replikace, je první písmeno jednotky ze seznamu dostupných. Azure přiřadí písmeno jednotky E: svazku dočasného úložiště. Další písmena jednotek pro replikované disky zůstanou stejná.
Disk2 | D:\ | Databáze systému SQL a uživatelská databáze 1
Disk3 | G:\ | Uživatelská databáze 2

V našem příkladu se Disk3 disk SQL tempdb z replikace vyloučil a není k dispozici na virtuálním počítači Azure, služba SQL je v zastaveném stavu a potřebuje cestu F:\MSSQL\Data. Tuto cestu můžete vytvořit několika způsoby: 

- Po převzetí služeb při selhání přidejte nový disk a přiřaďte cestu ke složce tempdb.
- Použijte pro cestu ke složce s databází tempdb stávající disk dočasného úložiště.

#### <a name="add-a-new-disk-after-failover"></a>Přidání nového disku po převzetí služeb při selhání

1. Poznamenejte si cesty k souborům SQL tempdb.mdf a tempdb.ldf před převzetím služeb při selhání.
2. Z Azure Portal přidejte nový disk do virtuálního počítače Azure s podporou převzetí služeb při selhání. Disk by měl mít stejnou velikost (nebo větší) jako zdrojový disk SQL tempdb (Disk3).
3. Přihlaste se k virtuálnímu počítači Azure.
4. Z konzoly pro správu disků (diskmgmt.msc) inicializujte a naformátujte nově přidaný disk.
5. Přiřaďte stejné písmeno jednotky, které používal disk SQL databáze tempdb (F:).
6. Vytvořte složku databáze tempdb na svazku F: (F:\MSSQL\Data).
7. Spusťte službu SQL z konzoly služby.

#### <a name="use-an-existing-temporary-storage-disk"></a>Použít stávající disk dočasného úložiště 

1. Otevřete příkazový řádek.
2. Spusťte z příkazového řádku SQL Server v režimu obnovení.

    ```console
    Net start MSSQLSERVER /f / T3608
    ```

3. Spuštěním následujícího příkazu sqlcmd změňte cestu k databázi tempdb na novou cestu.

    ```sql
    sqlcmd -A -S SalesDB        **Use your SQL DBname**
    USE master;     
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
    GO
    ```

4. Zastavte službu Microsoft SQL Server.

    ```console
    Net stop MSSQLSERVER
    ```

5. Spusťte službu Microsoft SQL Server.

    ```console
    Net start MSSQLSERVER
    ```

### <a name="vmware-vms-disks-during-failback-to-original-location"></a>Virtuální počítače VMware: disky během navrácení služeb po obnovení do původního umístění

Teď se podíváme na to, jak zpracovávat disky na virtuálních počítačích VMware při navrácení služeb po obnovení do původního místního umístění.

- **Disky vytvořené v Azure**: vzhledem k tomu, že náš příklad používá virtuální počítač s Windows, disky, které ručně vytvoříte v Azure, se po navrácení služeb po obnovení nebo opětovném zapnutí ochrany virtuálního počítače replikují zpátky do vaší lokality.
- **Disk dočasného úložiště v Azure**: dočasný disk úložiště se nereplikuje zpátky na místní hostitele.
- **Vyloučené disky**: disky vyloučené z replikace z VMware do Azure nejsou po navrácení služeb po obnovení k dispozici na místním virtuálním počítači.

Před navrácením služeb po obnovení virtuálních počítačů VMware do původního umístění jsou nastavení disku virtuálního počítače Azure následující.

**Disk hostovaného operačního systému** | **Písmeno jednotky** | **Datový typ disku**
--- | --- | ---
Disk0 | C:\ | Disk s operačním systémem.
Disk1 | E:\ | Dočasné úložiště –
Disk2 | D:\ | Systémová databáze SQL a uživatel Databáze1.
Disk3 | G:\ | Uživatel databáze 2.

Po navrácení služeb po obnovení budou mít virtuální počítač VMware v původním umístění disky shrnuté v tabulce.

**Disk hostovaného operačního systému** | **Písmeno jednotky** | **Datový typ disku**
--- | --- | ---
Disk0 | C:\ | Disk s operačním systémem.
Disk1 | D:\ | Systémová databáze SQL a uživatel Databáze1.
Disk2 | G:\ | Uživatel databáze 2.


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Virtuální počítače Hyper-V: disky během navrácení služeb po obnovení do původního umístění

Teď se podíváme na to, jak zpracovávat disky na virtuálních počítačích Hyper-V při navrácení služeb po obnovení do původního místního umístění.

- **Disky vytvořené v Azure**: disky, které ručně vytvoříte v Azure, se po navrácení služeb po obnovení nebo opětovném zapnutí ochrany virtuálního počítače replikují zpátky do vaší lokality.
- **Disk dočasného úložiště v Azure**: dočasný disk úložiště se nereplikuje zpátky na místní hostitele.
- **Vyloučené disky**: po navrácení služeb po obnovení bude konfigurace disku virtuálního počítače stejná jako u původní konfigurace disku virtuálního počítače. Disky vyloučené z replikace z Hyper-V do Azure jsou dostupné na virtuálním počítači pro navrácení služeb po obnovení.

Před navrácením služeb virtuálních počítačů Hyper-V do původního umístění, nastavení disku virtuálního počítače Azure jsou následující.

**Disk hostovaného operačního systému** | **Písmeno jednotky** | **Datový typ disku**
--- | --- | ---
Disk0 | C:\ | Disk s operačním systémem.
Disk1 | E:\ | Dočasné úložiště –
Disk2 | D:\ | Systémová databáze SQL a uživatel Databáze1.
Disk3 | G:\ | Uživatel databáze 2.

Po plánovaném převzetí služeb při selhání (navrácení služeb po obnovení) z Azure do místní technologie Hyper-v má virtuální počítač Hyper-V v původním umístění disky shrnuté v tabulce.

**Název disku** | **Č. disku hostovaného OS** | **Písmeno jednotky** | **Datový typ disku**
 --- | --- | --- | ---
DB-Disk0-OS | Disk0 |   C:\ | Disk s operačním systémem.
DB-Disk1 | Disk1 | D:\ | Systémová databáze SQL a uživatel Databáze1.
DB-Disk2 (vyloučený disk) | Disk2 | E:\ | Dočasné soubory.
DB-Disk3 (vyloučený disk) | Disk3 | F:\ | Databáze tempdb SQL<br/><br/> Cesta ke složce ( \) F:\MSSQL\Data)
DB Disk4 | Disk4 | G:\ | Uživatelská databáze 2


## <a name="example-2-exclude-the-paging-file-disk"></a>Příklad 2: vyloučení disku se stránkovacím souborem

Pojďme se podívat na to, jak zpracovat vyloučení disku, převzetí služeb při selhání a převzetí služeb při selhání pro zdrojový virtuální počítač s Windows, pro který chceme, aby se soubor pagefile.sys na disku D a na alternativním disku vyloučí.


### <a name="paging-file-on-the-d-drive"></a>Stránkovací soubor na jednotce D

Na zdrojovém virtuálním počítači máme tyto disky.

**Název disku** | **Disk hostovaného operačního systému** | **Písmeno jednotky** | **Datový typ disku**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Disk operačním systému
DB-Disk1 (vyloučit z replikace) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Uživatelská data 1
DB-Disk3 | Disk3 | F:\ | Uživatelská data 2

Nastavení stránkovacího souboru na zdrojovém virtuálním počítači je následující:

![Snímek obrazovky dialogového okna virtuální paměti se zvýrazněným řádkem D: Drive [Svazek svazku] ukazující velikost stránkovacího souboru (MB) 3000-7000.](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. Povolujeme replikaci pro virtuální počítač.
2. Vyloučíme DB-Disk1 z replikace.

#### <a name="disks-after-failover"></a>Disky po převzetí služeb při selhání

Po převzetí služeb při selhání virtuální počítač Azure obsahuje disky shrnuté v tabulce.

**Název disku** | **Označení disku v hostovaném operačním systému** | **Písmeno jednotky** | **Typ dat na disku**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Disk operačním systému
DB-Disk1 | Disk1 | D:\ | Dočasné úložiště/pagefile.sys <br/><br/> Protože DB-Disk1 (D:) bylo vyloučeno, D: je první písmeno jednotky z dostupného seznamu.<br/><br/> Azure přiřadí písmeno jednotky D: svazku dočasného úložiště.<br/><br/> Vzhledem k tomu, že D: je k dispozici, nastavení stránkovacího souboru virtuálního počítače zůstane stejné).
DB-Disk2 | Disk2 | E:\ | Uživatelská data 1
DB-Disk3 | Disk3 | F:\ | Uživatelská data 2

Nastavení stránkovacího souboru na virtuálním počítači Azure je následující:

![Nastavení stránkovacího souboru na virtuálním počítači Azure](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>Stránkovací soubor na jiné jednotce (ne D:)

Pojďme se podívat na příklad, ve kterém se stránkovací soubor nenachází na jednotce D.  

Na zdrojovém virtuálním počítači máme tyto disky.

**Název disku** | **Disk hostovaného operačního systému** | **Písmeno jednotky** | **Datový typ disku**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Disk operačním systému
DB-Disk1 (vyloučit z replikace) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Uživatelská data 1
DB-Disk3 | Disk3 | F:\ | Uživatelská data 2

Nastavení stránkovacího souboru na místním virtuálním počítači je následující:

![Nastavení stránkovacího souboru na místním virtuálním počítači](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. Povolujeme replikaci pro virtuální počítač.
2. Vyloučíme DB-Disk1 z replikace.

#### <a name="disks-after-failover"></a>Disky po převzetí služeb při selhání

Po převzetí služeb při selhání virtuální počítač Azure obsahuje disky shrnuté v tabulce.

**Název disku** | **Č. disku hostovaného OS** | **Písmeno jednotky** | **Datový typ disku**
--- | --- | --- | ---
DB-Disk0-OS | Disk0  |C:\ | Disk operačním systému
DB-Disk1 | Disk1 | D:\ | Dočasné úložiště<br/><br/> Protože D: je první volné písmeno jednotky, Azure ho přiřadí svazku dočasného úložiště.<br/><br/> Pro všechny replikované disky zůstala písmena jednotek stejná.<br/><br/> Vzhledem k tomu, že disk G: není k dispozici, systém použije pro stránkovací soubor jednotku C:.
DB-Disk2 | Disk2 | E:\ | Uživatelská data 1
DB-Disk3 | Disk3 | F:\ | Uživatelská data 2

Nastavení stránkovacího souboru na virtuálním počítači Azure je následující:

![Snímek obrazovky dialogového okna virtuální paměť se zvýrazněnou jednotkou C: jednotka ukazující nastavení velikosti stránkovacího souboru na spravované systémem.](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o pokynech pro dočasný disk úložiště:
    - [Další informace o](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) použití SSD ve virtuálních počítačích Azure k ukládání SQL serverch rozšíření tempdb a fondu vyrovnávací paměti
    - [Projděte si ](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) osvědčené postupy výkonu pro SQL Server ve virtuálních počítačích Azure.
- Po nasazení a zprovoznění nasazení si můžete přečíst [další informace](failover-failback-overview.md) o různých typech převzetí služeb při selhání.
