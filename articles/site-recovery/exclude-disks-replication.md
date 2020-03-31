---
title: Vyloučení disků z replikace pomocí azure site recovery
description: Jak vyloučit disky z replikace do Azure pomocí Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 57bf06f0fde85714530c06cbd008db08de7460d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281844"
---
# <a name="exclude-disks-from-disaster-recovery"></a>Vyloučit disky z zotavení po havárii

Tento článek popisuje, jak vyloučit disky z replikace během zotavení po havárii z místního do Azure s [Azure Site Recovery](site-recovery-overview.md). Disky z replikace můžete vyloučit z mnoha důvodů:

- Tak, aby nedůležitá data na vyloučeném disku není replikována.
- Optimalizace spotřebované šířky pásma replikace nebo prostředků na straně cíle.
- Chcete-li uložit úložiště a síťové prostředky tím, že není replikovat data, která nepotřebujete.
- Virtuální počítače Azure dosáhly limitů replikace obnovení lokality.


## <a name="supported-scenarios"></a>Podporované scénáře

Disky můžete vyloučit z replikace jako sumarizované v tabulce.

**Z Azure do Azure** | **Z VMware do Azure** | **Z Hyper-V do Azure** 
--- | --- | ---
Ano (pomocí PowerShellu) | Ano | Ano 

## <a name="exclude-limitations"></a>Vyloučit omezení

**Omezení** | **Virtuální počítače Azure** | **Virtuální počítače VMware** | **Virtuální počítače Hyper-V**
--- | --- | ---
**Typy disků** | Z replikace můžete vyloučit základní disky.<br/><br/> Nelze vyloučit disky operačního systému nebo dynamické disky. Dočasné disky jsou ve výchozím nastavení vyloučeny. | Z replikace můžete vyloučit základní disky.<br/><br/> Nelze vyloučit disky operačního systému nebo dynamické disky. | Z replikace můžete vyloučit základní disky.<br/><br/> Nemůžete vyloučit disky operačního systému. Doporučujeme, abyste nevylučovali dynamické disky. Obnovení webu nelze určit, které VHS je základní nebo dynamické ve virtuálním virtuálním virtuálním ms hosta. Pokud nejsou vyloučeny všechny závislé disky dynamického svazku, chráněný dynamický disk se stane neúspěšným diskem na virtuálním počítači s podporou převzetí služeb při selhání a data na tomto disku nejsou přístupná.
**Replikace disku** | Nelze vyloučit disk, který se replikuje.<br/><br/> Zakázat a znovu povolit replikaci pro virtuální ho. |  Nelze vyloučit disk, který se replikuje. |  Nelze vyloučit disk, který se replikuje.
**Služba mobility (VMware)** | Není relevantní | Disky můžete vyloučit jenom na virtuálních počítačích, na kterých je nainstalovaná služba Mobility.<br/><br/> To znamená, že je nutné ručně nainstalovat službu Mobility na virtuální počítače, pro které chcete vyloučit disky. Mechanismus nabízené instalace nelze použít, protože nainstaluje službu Mobility až po povolení replikace. | Není to relevantní.
**Přidat nebo odebrat** | Disky můžete přidávat a odebírat na virtuálních počítačích Azure se spravovanými disky. | Po povolení replikace nelze disky přidávat ani odebírat. Zakažte a znovu povolte replikaci a přidejte disk. | Po povolení replikace nelze disky přidávat ani odebírat. Zakázat a znovu povolit replikaci.
**Zabezpečení před selháním** | Pokud aplikace potřebuje disk, který jste vyloučili, po převzetí služeb při selhání je třeba vytvořit disk ručně, aby se replikovaná aplikace mohla spustit.<br/><br/> Případně můžete vytvořit disk během převzetí služeb při selhání virtuálního počítače integrací automatizace Azure do plánu obnovení. | Pokud vyloučíte disk, který aplikace potřebuje, vytvořte ho ručně v Azure po převzetí služeb při selhání. | Pokud vyloučíte disk, který aplikace potřebuje, vytvořte ho ručně v Azure po převzetí služeb při selhání.
**Místní disky navrácení služeb po selhání vytvořené ručně** | Není relevantní | **Virtuální počítače windows**: Disky vytvořené ručně v Azure se nepolykly zpět. Například pokud převzetí služeb při selhání přes tři disky a vytvořit dva disky přímo na virtuálním počítači Azure, pouze tři disky, které byly převzetí služeb při selhání přes pak se nezdařilo zpět.<br/><br/> **Virtuální počítače s Linuxem**: Disky vytvořené ručně v Azure se nezdařily zpět. Například pokud převzetí služeb při selhání přes tři disky a vytvořit dva disky na virtuálním počítači Azure, všech pět se nezdaří zpět. Ručně vytvořené disky nemůžete vyloučit z navrácení služeb po obnovení. | Disky vytvořené ručně v Azure se nevracejí. Pokud například převezmete služby při selhání tři disky a vytvoříte dva disky přímo na virtuálním počítači Azure, budou se vrátit zpět pouze tři disky, které byly převzetí služeb při selhání.
**Místní disky vyloučené z navrácení služeb po selhání** | Není relevantní | Pokud jste navrácení služeb po selhání do původního počítače, konfigurace disku virtuálního počítače navrácení služeb po selhání nezahrnuje vyloučené disky. Disky, které byly vyloučeny z Replikace VMware do Azure, nejsou na virtuálním počítači navrácení služeb po službě azure k dispozici. | Pokud je navrácení služeb po selhání původní umístění Hyper-V, konfigurace disku virtuálního počítače navrácení služeb po selhání zůstane stejná jako u původního zdrojového disku virtuálního počítače. Disky, které byly vyloučeny z lokality Hyper-V do replikace Azure, jsou dostupné na virtuálním počítači navrácení služeb po služby.



## <a name="typical-scenarios"></a>Typické scénáře

Příklady změn dat, které jsou skvělými kandidáty pro vyloučení, zahrnují zápisy do stránkovacího souboru (pagefile.sys) a zápisy do souboru tempdb serveru Microsoft SQL Server. V závislosti na zatížení a podsystému úložiště může stránkovací a tempdb soubory zaregistrovat značné množství změn. Replikace tohoto typu dat do Azure je náročná na prostředky.

- Chcete-li optimalizovat replikaci pro virtuální počítač s jedním virtuálním diskem, který obsahuje operační systém i stránkovací soubor, můžete:
    1. Rozdělíte jeden virtuální disk na dva virtuální disky. Jeden virtuální disk obsahuje operační systém, druhý stránkovací soubor.
    2. Disk se stránkovacím souborem vyloučíte z replikace.

- Chcete-li optimalizovat replikaci pro disk, který obsahuje soubor tempdb serveru Microsoft SQL Server i soubor systémové databáze, můžete:
    1. Systémovou databázi a databázi tempdb budete uchovávat na dvou různých discích.
    2. Disk s databází tempdb vyloučíte z replikace.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>Příklad 1: Vyloučení disku s databází tempdb systému SQL Server

Podívejme se na to, jak zpracovat vyloučení disku, převzetí služeb při selhání a převzetí služeb při selhání pro zdrojový virtuální modul SQL Server Windows - **SalesDB***, pro který chceme vyloučit tempdb. 

### <a name="exclude-disks-from-replication"></a>Vyloučení disků z replikace

Máme tyto disky na zdroj Windows VM SalesDB.

**Název disku** | **Disk hostovaného operačního systému** | **Písmeno jednotky** | **Datový typ disku**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Disk operačního systému.
DB-Disk1| Disk1 | D:\ | Databáze systému SQL a databáze uživatelů1.
DB-Disk2 (disk vyloučený z ochrany) | Disk2 | E:\ | Dočasné soubory.
DB-Disk3 (disk vyloučený z ochrany) | Disk3 | F:\ | Databáze tempdb SQL.<br/><br/> Cesta ke složce - F:\MSSQL\Data\. Před převzetím služeb při selhání si poznamenejte cestu ke složce.
DB Disk4 | Disk4 |G:\ | Uživatelská databáze 2

1. Povolujeme replikaci pro virtuální mm SalesDB.
2. Z replikace vylučujeme disk2 a disk3, protože změny dat na těchto discích jsou dočasné. 


### <a name="handle-disks-during-failover"></a>Zpracování disků během převzetí služeb při selhání

Vzhledem k tomu, že disky nejsou replikované, při převzetí služeb při selhání do Azure tyto disky nejsou k dispozici na virtuálním počítači Azure vytvořené po převzetí služeb při selhání. Virtuální počítač Azure má disky shrnuté v této tabulce.

**Disk hostovaného operačního systému** | **Písmeno jednotky** | **Datový typ disku**
--- | --- | ---
Disk0 | C:\ | Disk operačního systému.
Disk1 | E:\ | Dočasné skladování<br/><br/>Azure přidá tento disk. Vzhledem k tomu, že disk2 a disk3 byly vyloučeny z replikace, E: je první písmeno jednotky z dostupného seznamu. Azure přiřadí písmeno jednotky E: svazku dočasného úložiště. Ostatní písmena jednotek pro replikované disky zůstávají stejné.
Disk2 | D:\ | Databáze systému SQL a uživatelská databáze 1
Disk3 | G:\ | Uživatelská databáze 2

V našem příkladu, protože Disk3, sql tempdb disk, byl vyloučen z replikace a není k dispozici na virtuálním počítači Azure, služba SQL je v zastaveném stavu a potřebuje F:\MSSQL\Data cestu. Tuto cestu můžete vytvořit několika způsoby: 

- Po převzetí služeb při selhání přidejte nový disk a přiřaďte cestu ke složce tempdb.
- Použijte pro cestu ke složce s databází tempdb stávající disk dočasného úložiště.

#### <a name="add-a-new-disk-after-failover"></a>Přidání nového disku po převzetí služeb při selhání

1. Poznamenejte si cesty k souborům SQL tempdb.mdf a tempdb.ldf před převzetím služeb při selhání.
2. Z webu Azure Portal přidejte nový disk do virtuálního počítače Azure s podporou převzetí služeb při selhání. Disk by měl mít stejnou velikost (nebo větší) jako zdrojový sql db disk (Disk3).
3. Přihlaste se k virtuálnímu počítači Azure.
4. Z konzoly pro správu disků (diskmgmt.msc) inicializujte a naformátujte nově přidaný disk.
5. Přiřaďte stejné písmeno jednotky, které používal disk sql tempdb (F:)
6. Vytvořte složku databáze tempdb na svazku F: (F:\MSSQL\Data).
7. Spusťte službu SQL z konzoly služby.

#### <a name="use-an-existing-temporary-storage-disk"></a>Použití existujícího disku dočasného úložiště 

1. Otevřete příkazový řádek.
2. Spusťte z příkazového řádku SQL Server v režimu obnovení.

        Net start MSSQLSERVER /f / T3608

3. Spuštěním následujícího příkazu sqlcmd změňte cestu k databázi tempdb na novou cestu.

        sqlcmd -A -S SalesDB        **Use your SQL DBname**
        USE master;     
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
        GO


4. Zastavte službu Microsoft SQL Server.

        Net stop MSSQLSERVER
5. Spusťte službu Microsoft SQL Server.

        Net start MSSQLSERVER



### <a name="vmware-vms-disks-during-failback-to-original-location"></a>Virtuální počítače VMware: Disky během navrácení služeb po selhání do původního umístění

Teď se podívejme, jak zpracovat disky na virtuálních počítačích VMware při převzetí služeb při selhání zpět do původního místního umístění.

- **Disky vytvořené v Azure**: Vzhledem k tomu, že náš příklad používá virtuální počítač s Windows, disky, které vytvoříte ručně v Azure, se při opětovném navrácení nebo opětovném zabezpečení virtuálního počítače nereplikují zpět na váš web.
- **Disk dočasného úložiště v Azure**: Disk dočasného úložiště se nereplikuje zpět do místních hostitelů.
- **Vyloučené disky**: Disky, které byly vyloučeny z replikace VMware do Azure, nejsou po navrácení služeb po službě po službě na po službě na po službě na po službě na po službě na po službě na po službě na po službě na po službě na po službě na po službě na poslužbě k dispozici.

Než navrátíte virtuální počítače VMware zpět do původního umístění, nastavení disku virtuálního počítače Azure jsou následující.

**Disk hostovaného operačního systému** | **Písmeno jednotky** | **Datový typ disku**
--- | --- | ---
Disk0 | C:\ | Disk operačního systému.
Disk1 | E:\ | Dočasné úložiště –
Disk2 | D:\ | Databáze systému SQL a databáze uživatelů1.
Disk3 | G:\ | Databáze uživatelů2.

Po navrácení služeb po selhání má virtuální modul VMware v původním umístění disky shrnuté v tabulce.

**Disk hostovaného operačního systému** | **Písmeno jednotky** | **Datový typ disku**
--- | --- | ---
Disk0 | C:\ | Disk operačního systému.
Disk1 | D:\ | Databáze systému SQL a databáze uživatelů1.
Disk2 | G:\ | Databáze uživatelů2.


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Virtuální počítače Hyper-V: Disky během navrácení služeb po selhání do původního umístění

Teď se podívejme, jak zpracovat disky na virtuálních počítačích Hyper-V při převzetí služeb při selhání zpět do původního místního umístění.

- **Disky vytvořené v Azure**: Disky, které vytvoříte ručně v Azure, se při navrácení služeb po obnovení nebo opětovném zabezpečení virtuálního počítače nereplikují zpět na váš web.
- **Disk dočasného úložiště v Azure**: Disk dočasného úložiště se nereplikuje zpět do místních hostitelů.
- **Vyloučené disky**: Po navrácení služeb po selhání je konfigurace disku virtuálního počítače stejná jako původní konfigurace disku virtuálního počítače. Disky, které byly vyloučeny z replikace z Hyper-V do Azure jsou k dispozici na navrácení služeb po služby Navrácení služeb po služby.

Než navrátíte virtuální počítače Hyper-V zpět do původního umístění, nastavení disku virtuálního počítače Azure jsou následující.

**Disk hostovaného operačního systému** | **Písmeno jednotky** | **Datový typ disku**
--- | --- | ---
Disk0 | C:\ | Disk operačního systému.
Disk1 | E:\ | Dočasné úložiště –
Disk2 | D:\ | Databáze systému SQL a databáze uživatelů1.
Disk3 | G:\ | Databáze uživatelů2.

Po plánovaném převzetí služeb při selhání (navrácení služeb po selhání) z Azure do místního hyper-v, hyper-V virtuální počítač v původním umístění má disky sumarované v tabulce.

**Název disku** | **Č. disku hostovaného OS** | **Písmeno jednotky** | **Datový typ disku**
 --- | --- | --- | ---
DB-Disk0-OS | Disk0 |   C:\ | Disk operačního systému.
DB-Disk1 | Disk1 | D:\ | Databáze systému SQL a databáze uživatelů1.
DB-Disk2 (vyloučený disk) | Disk2 | E:\ | Dočasné soubory.
DB-Disk3 (vyloučený disk) | Disk3 | F:\ | Databáze tempdb SQL<br/><br/> Cesta ke složce (F:\MSSQL\Data\).
DB Disk4 | Disk4 | G:\ | Uživatelská databáze 2


## <a name="example-2-exclude-the-paging-file-disk"></a>Příklad 2: Vyloučení disku stránkovacího souboru

Podívejme se na to, jak zpracovat vyloučení disku, převzetí služeb při selhání a převzetí služeb při selhání pro zdrojový virtuální počítač se systémem Windows, pro který chceme vyloučit disk souboru pagefile.sys na jednotce D i alternativní jednotce.


### <a name="paging-file-on-the-d-drive"></a>Stránkovací soubor na jednotce D

Máme tyto disky na zdrojovém virtuálním počítači.

**Název disku** | **Disk hostovaného operačního systému** | **Písmeno jednotky** | **Datový typ disku**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Disk operačním systému
DB-Disk1 (Vyloučit z replikace) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Uživatelská data 1
DB-Disk3 | Disk3 | F:\ | Uživatelská data 2

Naše nastavení stránkovacího souboru na zdrojovém virtuálním počítači jsou následující:

![Nastavení stránkovacího souboru na zdrojovém virtuálním počítači](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. Povolujeme replikaci pro virtuální hod.
2. Db-Disk1 z replikace vylučujeme.

#### <a name="disks-after-failover"></a>Disky po převzetí služeb při selhání

Po převzetí služeb při selhání virtuálního počítače Azure má disky shrnuté v tabulce.

**Název disku** | **Označení disku v hostovaném operačním systému** | **Písmeno jednotky** | **Typ dat na disku**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Disk operačním systému
DB-Disk1 | Disk1 | D:\ | Dočasné úložiště/soubor stránek.sys <br/><br/> Protože DB-Disk1 (D:) d: je první písmeno jednotky z dostupného seznamu.<br/><br/> Azure přiřadí písmeno jednotky D: svazku dočasného úložiště.<br/><br/> Vzhledem k tomu, že D: je k dispozici, nastavení stránkovacího souboru virtuálního měn zůstává stejné).
DB-Disk2 | Disk2 | E:\ | Uživatelská data 1
DB-Disk3 | Disk3 | F:\ | Uživatelská data 2

Naše nastavení stránkovacího souboru na virtuálním počítači Azure jsou následující:

![Nastavení stránkovacího souboru na virtuálním počítači Azure](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>Stránkovací soubor na jiné jednotce (ne D:)

Podívejme se na příklad, ve kterém stránkovací soubor není na jednotce D.  

Máme tyto disky na zdrojovém virtuálním počítači.

**Název disku** | **Disk hostovaného operačního systému** | **Písmeno jednotky** | **Datový typ disku**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Disk operačním systému
DB-Disk1 (Vyloučit z replikace) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Uživatelská data 1
DB-Disk3 | Disk3 | F:\ | Uživatelská data 2

Naše nastavení stránkovacího souboru na místním virtuálním počítači jsou následující:

![Nastavení stránkovacího souboru na místním virtuálním počítači](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. Povolujeme replikaci pro virtuální hod.
2. Db-Disk1 z replikace vylučujeme.

#### <a name="disks-after-failover"></a>Disky po převzetí služeb při selhání

Po převzetí služeb při selhání virtuálního počítače Azure má disky shrnuté v tabulce.

**Název disku** | **Č. disku hostovaného OS** | **Písmeno jednotky** | **Datový typ disku**
--- | --- | --- | ---
DB-Disk0-OS | Disk0  |C:\ | Disk operačním systému
DB-Disk1 | Disk1 | D:\ | Dočasné skladování<br/><br/> Protože D: je první volné písmeno jednotky, Azure ho přiřadí svazku dočasného úložiště.<br/><br/> Pro všechny replikované disky zůstala písmena jednotek stejná.<br/><br/> Vzhledem k tomu, že disk G: není k dispozici, systém použije jednotku C: pro stránkovací soubor.
DB-Disk2 | Disk2 | E:\ | Uživatelská data 1
DB-Disk3 | Disk3 | F:\ | Uživatelská data 2

Naše nastavení stránkovacího souboru na virtuálním počítači Azure jsou následující:

![Nastavení stránkovacího souboru na virtuálním počítači Azure](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>Další kroky

- Další informace o pokynech pro disk dočasného úložiště:
    - [Informace o](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) používání ssd dispozicí ve virtuálních počítačích Azure k ukládání rozšíření SQL Server TempDB a fondu vyrovnávacích pamětí
    - [Projděte si](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance) doporučené postupy výkonu pro SQL Server ve virtuálních počítačích Azure.
- Po nasazení a zprovoznění nasazení si můžete přečíst [další informace](failover-failback-overview.md) o různých typech převzetí služeb při selhání.

