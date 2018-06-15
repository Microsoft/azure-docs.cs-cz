---
title: Vyloučení disků z ochrany pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, proč a jak vyloučit disky virtuálních počítačů z replikace pro scénáře z Hyper-V do Azure.
services: site-recovery
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: nisoneji
ms.openlocfilehash: 131c98c6772a68c8617df3160b159ece62bd1fc9
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2018
ms.locfileid: "29467181"
---
# <a name="exclude-disks-from-replication"></a>Vyloučení disků z replikace
Tento článek popisuje, jak vyloučit disky z replikace. Takové vyloučení může optimalizovat jak využití šířky pásma potřebné pro replikaci, tak i prostředků využívaných na cílové straně těmito disky.

## <a name="supported-scenarios"></a>Podporované scénáře
**Funkce** | **Z VMware do Azure** | **Z Hyper-V do Azure** | **Z Azure do Azure**| **Z Hyper-V do Hyper-V** 
--|--|--|--|--
Vyloučení disku | Ano | Ano | Ne | Ne

## <a name="why-exclude-disks-from-replication"></a>Proč vylučovat disky z replikace?
Vyloučení disků z replikace je často nutné z těchto důvodů:

- Často měněná data uložená na vyloučených discích nejsou důležitá nebo nemusí být replikována.

- Odstraněním potřeby replikace těchto častých změn dat chcete šetřit prostředky úložiště a síťové prostředky.

## <a name="what-are-the-typical-scenarios"></a>Jaké jsou typické scénáře?
Můžete určit konkrétní příklady častých změn dat, které jsou skvělými kandidáty na vyloučení. Příklady můžou zahrnovat zapisování do stránkovacího souboru (pagefile.sys) nebo zapisování Microsoft SQL Serveru do souboru databáze tempdb. V závislosti na zatížení a podsystému úložiště může stránkovací soubor zaznamenat značné množství změn dat. Replikace těchto dat z primární lokality do Azure by však byla náročná na prostředky. Proto můžete pomocí následujícího postupu optimalizovat replikaci virtuálního počítače s jedním virtuálním diskem, který obsahuje operační systém i stránkovací soubor:

1. Rozdělíte jeden virtuální disk na dva virtuální disky. Jeden virtuální disk obsahuje operační systém, druhý stránkovací soubor.
2. Disk se stránkovacím souborem vyloučíte z replikace.

Podobně můžete pomocí následujícího postupu optimalizovat disk, který obsahuje jak soubor tempdb Microsoft SQL Serveru, tak soubor se systémovou databází:

1. Systémovou databázi a databázi tempdb budete uchovávat na dvou různých discích.
2. Disk s databází tempdb vyloučíte z replikace.

## <a name="how-to-exclude-disks"></a>Vyloučení disků
Pokud chcete chránit virtuální počítač z portálu Azure Site Recovery, postupujte podle pracovního postupu pro [povolení replikace](site-recovery-hyper-v-site-to-azure.md). Ve čtvrtém kroku pracovního postupu můžete pomocí sloupce **DISK TO REPLICATE** vyloučit disky z replikace. Ve výchozím nastavení jsou pro replikaci vybrány všechny disky. Zrušte výběr disků, které chcete vyloučit z replikace, a potom dokončete postup povolení replikace.

![Vyloučení disků z replikace a povolení replikace pro navrácení služeb po obnovení z Hyper-V do Azure](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)

>[!NOTE]
>
> * Z replikace můžete vyloučit pouze běžné disky. Nemůžete vyloučit disky operačního systému. Doporučujeme, abyste nevylučovali dynamické disky. Azure Site Recovery nemůže zjistit, který virtuální pevný disk (VHD) je v hostovaném virtuálním počítači běžný a který dynamický.  Pokud nejsou vyloučeny všechny disky se závislými dynamickými svazky, z chráněného dynamického disku se na virtuálním počítači po převzetí služeb při selhání stane chybný disk a data na takovém disku budou nepřístupná.
> * Po povolení replikace už není možné přidávat nebo odebírat disky pro replikaci. Pokud chcete přidat nebo vyloučit disk, budete muset zakázat ochranu virtuálního počítače a potom ji znovu povolit.
> * Pokud vyloučíte disk, který je nezbytný pro provoz aplikace, po převzetí služeb při selhání do Azure budete muset v Azure disk znovu ručně vytvořit, aby se replikovaná aplikace mohla spustit. Alternativně můžete do plánu obnovení integrovat službu Azure Automation, která disk vytvoří během převzetí služeb při selhání počítače.
> * Disky, které ručně vytvoříte v Azure, nebude možné po obnovení navrátit. Například pokud provedete převzetí služeb při selhání u tří disků a dva disky vytvoříte přímo ve službě Azure Virtual Machines, po obnovení z Azure do Hyper-V se navrátí pouze tři disky, u nichž se provedlo převzetí služeb při selhání. Ručně vytvořené disky není možné zahrnout do navrácení služeb po obnovení ani do zpětné replikace z Hyper-V do Azure.

## <a name="end-to-end-scenarios-of-exclude-disks"></a>Úplné scénáře vyloučení disků
Pojďme se podívat na dva scénáře, abychom porozuměli funkci vyloučení disku:

- Disk s databází tempdb systému SQL Server
- Disk se stránkovacím souborem (pagefile.sys)

## <a name="excample-1-exclude-the-sql-server-tempdb-disk"></a>Příklad 1: Vyloučení disku s databází tempdb systému SQL Server
Uvažujme virtuální počítač se systémem SQL Server, který používá databázi tempdb, kterou chcete vyloučit z replikace.

Název virtuálního disku je SalesDB.

Na zdrojovém virtuálním počítači jsou následující disky:


**Název disku** | **Označení disku v hostovaném operačním systému** | **Písmeno jednotky** | **Typ dat na disku**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disk operačním systému
DB-Disk1| Disk1 | D:\ | Databáze systému SQL a uživatelská databáze 1
DB-Disk2 (disk vyloučený z ochrany) | Disk2 | E:\ | Dočasné soubory
DB-Disk3 (disk vyloučený z ochrany) | Disk3 | F:\ | Databáze SQL tempdb (cesta ke složce (F:\MSSQL\Data\) </br /> </br /> Poznamenejte si cestu ke složce před převzetím služeb při selhání.
DB Disk4 | Disk4 |G:\ |Uživatelská databáze 2

Protože časté změny dat na dvou discích virtuálního počítače jsou dočasné, při ochraně virtuálního počítače SalesDB vylučte Disk2 a Disk3 z replikace. Azure Site Recovery nebude tyto disky replikovat. Po převzetí služeb při selhání tyto disky nebudou na cílovém virtuálním počítači v Azure připojené.

Ve virtuálním počítači Azure budou po převzetí služeb při selhání tyto disky:

**Označení disku v hostovaném operačním systému** | **Písmeno jednotky** | **Typ dat na disku**
--- | --- | ---
DISK0 | C:\ | Disk operačním systému
Disk1 | E:\ | Dočasné úložiště</br /> </br />Azure tento disk přidá a přiřadí mu první dostupné písmeno jednotky.
Disk2 | D:\ | Databáze systému SQL a uživatelská databáze 1
Disk3 | G:\ | Uživatelská databáze 2

Protože Disk2 a Disk3 byly vyloučeny z virtuálního počítače SalesDB, první volné písmeno jednotky je E:. Azure přiřadí písmeno jednotky E: svazku dočasného úložiště. Pro všechny replikované disky zůstala písmena jednotek stejná.

Disk3, na kterém byla databáze SQL tempdb (cesta ke složce databáze tempdb je F:\MSSQL\Data\), byl z replikace vyloučený. Disk není dostupný na virtuálním počítači po převzetí služeb při selhání. V důsledku toho je služba SQL zastavená a je nutné zajistit cestu F:\MSSQL\Data.

Existují dva způsoby, jak tuto cestu vytvořit:

- Přidejte nový disk a přiřaďte mu cestu ke složce databáze tempdb.
- Použijte pro cestu ke složce s databází tempdb stávající disk dočasného úložiště.

### <a name="add-a-new-disk"></a>Přidání nového disku:

1. Poznamenejte si cesty k souborům SQL tempdb.mdf a tempdb.ldf před převzetím služeb při selhání.
2. Na webu Azure Portal přidejte do virtuálního počítače přebírajícího služby při selhání nový disk se stejnou nebo větší velikostí, jakou měl zdrojový disk s databází SQL tempdb (Disk3).
3. Přihlaste se k virtuálnímu počítači Azure. Z konzoly pro správu disků (diskmgmt.msc) inicializujte a naformátujte nově přidaný disk.
4. Přiřaďte stejné písmeno jednotky, kterou používal disk s databází SQL tempdb (F:).
5. Vytvořte složku databáze tempdb na svazku F: (F:\MSSQL\Data).
6. Spusťte službu SQL z konzoly služby.

### <a name="use-an-existing-temporary-storage-disk-for-the-sql-tempdb-folder-path"></a>Použijte pro cestu ke složce s databází tempdb stávající disk dočasného úložiště:

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

Seznamte se s následujícími pravidly Azure pro dočasné disky úložiště:

* [Použití disků SSD ve virtuálních počítačích Azure k uložení databáze TempDB systému SQL Server a rozšíření fondu vyrovnávací paměti](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)
* [Osvědčené postupy z hlediska výkonu pro SQL Server na Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)

## <a name="failback-from-azure-to-an-on-premises-host"></a>Navrácení služeb po obnovení (z Azure na místního hostitele)
Nyní se pojďme podívat na to, které disky se budou replikovat při převzetí služeb při selhání z Azure na místního hostitele Hyper-V. Disky, které vytvoříte v Azure ručně, se replikovat nebudou. Například když při selhání převezmete tři disky a pak přímo ve službě Azure Virtual Machines vytvoříte další dva, při navrácení služeb po obnovení se přenesou jen tři disky replikované při selhání. Ručně vytvořené disky není možné zahrnout do navrácení služeb po obnovení ani do zpětné replikace z místního počítače do Azure. Také se nereplikují disky s dočasným úložištěm na místní hostitele.

### <a name="failback-to-original-location-recovery"></a>Navrácení služeb po obnovení do původního umístění

V předchozím příkladu vypadá konfigurace disků virtuálního počítače Azure takto:

**Označení disku v hostovaném operačním systému** | **Písmeno jednotky** | **Typ dat na disku**
--- | --- | ---
DISK0 | C:\ | Disk operačním systému
Disk1 | E:\ | Dočasné úložiště</br /> </br />Azure tento disk přidá a přiřadí mu první dostupné písmeno jednotky.
Disk2 | D:\ | Databáze systému SQL a uživatelská databáze 1
Disk3 | G:\ | Uživatelská databáze 2

Pokud se navrácení služeb po obnovení provádí do původního umístění, zůstává konfigurace disků virtuálního počítače stejná jako konfigurace disků původního virtuálního počítače Hyper-V. Disky vyloučené z replikace z Hyper-V do Azure budou dostupné po navrácení služeb po obnovení virtuálního počítače.

Po naplánovaném převzetí služeb při selhání z Azure do místního počítače Hyper-V budou disky na virtuálním počítači Hyper-V (v původním umístění) následující:

**Název disku** | **Označení disku v hostovaném operačním systému** | **Písmeno jednotky** | **Typ dat na disku**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 |   C:\ | Disk operačním systému
DB-Disk1 | Disk1 | D:\ | Databáze systému SQL a uživatelská databáze 1
DB-Disk2 (vyloučený disk) | Disk2 | E:\ | Dočasné soubory
DB-Disk3 (vyloučený disk) | Disk3 | F:\ | SQL databáze tempdb (cesta ke složce (F:\MSSQL\Data\)
DB Disk4 | Disk4 | G:\ | Uživatelská databáze 2

## <a name="example-2-exclude-the-paging-file-pagefilesys-disk"></a>Příklad 2: Vyloučení disku se stránkovacím souborem (pagefile.sys)

Uvažujme virtuální počítač s diskem pro stránkovací soubor, který je možné vyloučit z replikace.
Existují dva možné případy.

### <a name="case-1-the-paging-file-is-configured-on-the-d-drive"></a>Případ 1: Stránkovací soubor je nakonfigurován na jednotce D:
Tady je konfigurace disků:

**Název disku** | **Označení disku v hostovaném operačním systému** | **Písmeno jednotky** | **Typ dat na disku**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disk operačním systému
DB-Disk1 (disk vyloučený z ochrany) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Uživatelská data 1
DB-Disk3 | Disk3 | F:\ | Uživatelská data 2

Tady je nastavení stránkovacího souboru na zdrojovém virtuálním počítači:

![Nastavení stránkovacího souboru na zdrojovém virtuálním počítači](./media/hyper-v-exclude-disk/pagefile-on-d-drive-sourceVM.png)

Po převzetí služeb virtuálního počítače při selhání z Hyper-V do Azure budou disky na virtuálním počítači Azure následující:

**Název disku** | **Označení disku v hostovaném operačním systému** | **Písmeno jednotky** | **Typ dat na disku**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disk operačním systému
DB-Disk1 | Disk1 | D:\ | Dočasné úložiště</br /> </br />pagefile.sys
DB-Disk2 | Disk2 | E:\ | Uživatelská data 1
DB-Disk3 | Disk3 | F:\ | Uživatelská data 2

Protože Disk1 (D:) byl vyloučen, první volné písmeno jednotky je D:. Azure přiřadí písmeno jednotky D: svazku dočasného úložiště. Protože písmeno jednotky D: je dostupné i na virtuálním počítači Azure, nastavení stránkovacího souboru virtuálního počítače zůstane stejné.

Tady je nastavení stránkovacího souboru na virtuálním počítači Azure:

![Nastavení stránkovacího souboru na virtuálním počítači Azure](./media/hyper-v-exclude-disk/pagefile-on-Azure-vm-after-failover.png)

### <a name="case-2-the-paging-file-is-configured-on-another-drive-other-than-d-drive"></a>Případ 2: Stránkovací soubor je nakonfigurovaný na jiné jednotce (než je D:)

Tady je konfigurace disků zdrojového virtuálního počítače:

**Název disku** | **Označení disku v hostovaném operačním systému** | **Písmeno jednotky** | **Typ dat na disku**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disk operačním systému
DB-Disk1 (disk vyloučený z ochrany) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Uživatelská data 1
DB-Disk3 | Disk3 | F:\ | Uživatelská data 2

Tady je nastavení stránkovacího souboru na místním virtuálním počítači:

![Nastavení stránkovacího souboru na místním virtuálním počítači](./media/hyper-v-exclude-disk/pagefile-on-g-drive-sourceVM.png)

Po převzetí služeb virtuálního počítače při selhání z Hyper-V do Azure budou disky na virtuálním počítači Azure následující:

**Název disku**| **Označení disku v hostovaném operačním systému**| **Písmeno jednotky** | **Typ dat na disku**
--- | --- | --- | ---
DB-Disk0-OS | DISK0  |C:\ |Disk operačním systému
DB-Disk1 | Disk1 | D:\ | Dočasné úložiště</br /> </br />pagefile.sys
DB-Disk2 | Disk2 | E:\ | Uživatelská data 1
DB-Disk3 | Disk3 | F:\ | Uživatelská data 2

Protože D: je první volné písmeno jednotky, Azure ho přiřadí svazku dočasného úložiště. Pro všechny replikované disky zůstala písmena jednotek stejná. Protože disk G: není k dispozici, systém použije pro stránkovací soubor jednotku C:.

Tady je nastavení stránkovacího souboru na virtuálním počítači Azure:

![Nastavení stránkovacího souboru na virtuálním počítači Azure](./media/hyper-v-exclude-disk/pagefile-on-Azure-vm-after-failover-2.png)

## <a name="next-steps"></a>Další postup
Po nasazení a zprovoznění nasazení si můžete přečíst [další informace](site-recovery-failover.md) o různých typech převzetí služeb při selhání.
