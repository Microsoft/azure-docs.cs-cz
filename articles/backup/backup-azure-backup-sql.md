---
title: Zálohování SQL Server do Azure jako úlohy DPM
description: Úvod k zálohování SQL Server databází pomocí služby Azure Backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 592a51051a0d02a6c1d491db0fe559e2e62babb2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96327045"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Zálohování SQL Server do Azure jako úlohy DPM

Tento článek vás provede kroky konfigurace pro zálohování SQL Server databází pomocí Azure Backup.

K zálohování databází SQL Server do Azure potřebujete účet Azure. Pokud ho nemáte, můžete si během několika minut vytvořit bezplatný účet. Další informace najdete v tématu [Vytvoření bezplatného účtu Azure](https://azure.microsoft.com/pricing/free-trial/).

Zálohování databáze SQL Server do Azure a její obnovení z Azure:

1. Vytvořte zásady zálohování pro ochranu SQL Serverch databází v Azure.
1. Vytváření záložních kopií na vyžádání v Azure.
1. Obnovte databázi z Azure.

>[!NOTE]
>DPM 2019 UR2 podporuje SQL Server instance clusterů s podporou převzetí služeb při selhání (FCI) pomocí sdílených svazků clusteru (CSV).<br><br>
>Tato funkce podporuje ochranu [SQL Server instance clusteru s podporou převzetí služeb při selhání s prostory úložiště s přímým přístupem v Azure](../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md)  a [SQL Server instanci clusteru s podporou převzetí služeb při selhání se sdílenými disky Azure](../azure-sql/virtual-machines/windows/failover-cluster-instance-azure-shared-disks-manually-configure.md) . Aby bylo možné chránit instanci SQL FCI nasazenou na virtuálních počítačích Azure, musí být server DPM nasazený na virtuálním počítači Azure. 

## <a name="prerequisites-and-limitations"></a>Požadavky a omezení

* Pokud máte databázi se soubory ve vzdálené sdílené složce, ochrana se nezdaří a ID chyby bude 104. DPM nepodporuje ochranu pro SQL Server dat ve vzdálené sdílené složce souborů.
* Aplikace DPM nemůže chránit databáze, které jsou uloženy ve vzdálených sdílených složkách protokolu SMB.
* Ujistěte se, že [repliky skupin dostupnosti jsou nakonfigurovány jen pro čtení](/sql/database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server).
* Účet System **NTAUTHORITY\SYSTEM.** musíte explicitně přidat do skupiny Sysadmin na SQL Server.
* Když provedete obnovení do alternativního umístění pro částečně databázi s omezením, je nutné zajistit, aby byla v cílové instanci SQL povolena funkce [databáze s omezením](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#enable) .
* Když provádíte obnovení do alternativního umístění databáze datového proudu souborů, musíte zajistit, aby cílová instance SQL měla povolenou funkci [databáze streamování souborů](/sql/relational-databases/blob/enable-and-configure-filestream) .
* Ochrana SQL Serveru AlwaysOn:
  * Aplikace DPM při spuštění dotazu při vytváření skupiny ochrany vyhledá skupiny dostupnosti.
  * Aplikace zjistí převzetí služeb při selhání a pokračuje v ochraně databáze.
  * Aplikace DPM podporuje konfigurace clusteru více lokalit pro instanci SQL Serveru.
* Když chráníte databáze využívající funkci AlwaysOn, má nástroj DPM tato omezení:
  * DPM bude dodržovat zásady zálohování pro skupiny dostupnosti, které jsou nastavené v SQL Server na základě předvoleb zálohování, a to takto:
    * Preferovat sekundární – zálohy se budou objevovat na sekundární replice s výjimkou případu, kdy je primární replika jedinou replikou online. Pokud je k dispozici více sekundárních replik, bude pro zálohování vybrán uzel s nejvyšší prioritou zálohování. Pokud je k dispozici pouze primární replika, pak by záloha měla být provedena na primární replice.
    * Pouze sekundární – záloha se nebude provádět na primární replice. Pokud je online jenom primární replika, zálohování neproběhne.
    * Primární – zálohování se musí vždy odehrávat na primární replice.
    * Jakákoli replika – zálohy můžou probíhat na libovolné replice dostupnosti ve skupině dostupnosti. Uzel, z něhož se bude zálohovat, bude založen na prioritách zálohy pro jednotlivé uzly.
  * Všimněte si, že:
    * K zálohování může dojít z jakékoli čitelné repliky – primární, synchronní sekundární, asynchronní sekundární.
    * Pokud je některá z replik vyloučená ze zálohy, například pokud je možnost **vyloučit repliku** povolená nebo je označená jako nečitelná, pak se tato replika v žádné z možností nevybrala k zálohování.
    * Pokud je k dispozici více replik a jejich čtení, bude pro zálohování vybrán uzel s nejvyšší prioritou zálohování.
    * Pokud se zálohování na vybraném uzlu nepovede, operace zálohování se nezdařila.
    * Obnovení do původního umístění není podporováno.
* SQL Server 2014 nebo vyšší problémy se zálohováním:
  * SQL Server 2014 Přidal novou funkci pro vytvoření [databáze pro místní SQL Server v úložišti objektů BLOB v systému Windows Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure). Aplikaci DPM nelze použít k ochraně této konfigurace.
  * U možnosti "Preferovat sekundární" zálohování pro možnost SQL AlwaysOn Existují známé problémy. DPM vždycky provede zálohu ze sekundárního serveru. Pokud není možné najít sekundární, zálohování se nepovede.

## <a name="before-you-start"></a>Než začnete

Než začnete, ujistěte se, že jste splnili [požadavky](backup-azure-dpm-introduction.md#prerequisites-and-limitations) na použití Azure Backup k ochraně úloh. Tady jsou některé z požadovaných úloh:

* Vytvořte úložiště záloh.
* Stáhněte si přihlašovací údaje trezoru.
* Nainstalujte agenta Azure Backup.
* Zaregistrujte server v trezoru.

## <a name="create-a-backup-policy"></a>Vytvoření zásad zálohování

Pokud chcete chránit SQL Server databáze v Azure, vytvořte nejdřív zásady zálohování:

1. Na serveru Data Protection Manager (DPM) vyberte pracovní prostor **ochrana** .
1. Chcete-li vytvořit skupinu ochrany, vyberte možnost **Nový** .

    ![Vytvořte skupinu ochrany](./media/backup-azure-backup-sql/protection-group.png)
1. Na úvodní stránce si přečtěte pokyny k vytvoření skupiny ochrany. Pak vyberte **Další**.
1. Vyberte **servery**.

    ![Vyberte typ skupiny ochrany servery.](./media/backup-azure-backup-sql/pg-servers.png)
1. Rozbalte virtuální počítač SQL Server, kde jsou umístěny databáze, které chcete zálohovat. Zobrazí se zdroje dat, které je možné zálohovat z tohoto serveru. Rozbalte položku **všechny sdílené složky SQL** a pak vyberte databáze, které chcete zálohovat. V tomto příkladu vybereme ReportServer $ MSDPM2012 a ReportServer $ MSDPM2012TempDB. Pak vyberte **Další**.

    ![Vybrat databázi SQL Server](./media/backup-azure-backup-sql/pg-databases.png)
1. Pojmenujte skupinu ochrany a potom vyberte **Chci online ochranu**.

    ![Zvolit metodu ochrany dat – krátkodobá Ochrana disku nebo online ochrana Azure](./media/backup-azure-backup-sql/pg-name.png)
1. Na stránce **zadat Short-Termé cíle** uveďte nezbytné vstupy pro vytváření záložních bodů na disku.

    V tomto příkladu je **Rozsah uchování** nastavený na *5 dní*. **Frekvence synchronizace** záloh je nastavená na každých *15 minut*. **Expresní úplné zálohování** je nastaveno na *8:00 PM*.

    ![Nastavení krátkodobých cílů ochrany záloh](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > V tomto příkladu se bod zálohování vytvoří každý den v 8:00 PM. Data, která byla změněna od posledního dne 8:00. bod zálohování v předchozím dnu, se přenáší. Tento proces se nazývá **expresní úplné zálohování**. I když jsou protokoly transakcí synchronizovány každých 15 minut, potřebujete-li obnovit databázi v 9:00 PM, pak se bod vytvoří přehráním protokolů z posledního expresního bodu úplného zálohování, který je v tomto příkladu 8:00 odp.
   >
   >

1. Vyberte **Další**. Aplikace DPM zobrazuje celkový prostor úložiště k dispozici. Zobrazuje taky možné využití místa na disku.

    ![Nastavení přidělení disku](./media/backup-azure-backup-sql/pg-storage.png)

    Ve výchozím nastavení aplikace DPM vytvoří jeden svazek na zdroj dat (SQL Server databázi). Svazek se používá pro prvotní záložní kopii. V této konfiguraci omezuje Správce logických disků (LDM) ochranu DPM na 300 zdrojů dat (SQL Server databází). Chcete-li toto omezení obejít, vyberte možnost **společně umístit data do fondu úložiště aplikace DPM**. Použijete-li tuto možnost, aplikace DPM použije jeden svazek pro více zdrojů dat. Tato instalace umožňuje DPM chránit až 2 000 SQL Server databází.

    Pokud vyberete možnost **automaticky zvětšit svazky**, může aplikace DPM při zvětšování produkčních dat považovat za účet zvýšeného záložního svazku. Pokud nevyberete možnost **automaticky rozšiřovat svazky**, aplikace DPM omezí úložiště zálohování na zdroje dat ve skupině ochrany.

1. Pokud jste správce, můžete se rozhodnout, že chcete toto prvotní zálohování přenést **automaticky přes síť** a zvolit čas přenosu. Nebo se můžete rozhodnout **ručně** přenést zálohu. Pak vyberte **Další**.

    ![Zvolit metodu vytvoření repliky](./media/backup-azure-backup-sql/pg-manual.png)

    Prvotní záložní kopie vyžaduje přenos celého zdroje dat (SQL Server databáze). Data zálohy se přesunou z provozního serveru (SQL Server počítač) na server DPM. Pokud je tato záloha velká, může přenos dat přes síť způsobit zahlcení šířky pásma. Z tohoto důvodu můžou správci zvolit použití vyměnitelných médií k **ručnímu** přenosu prvotní zálohy. Nebo můžou data přenést **automaticky přes síť** v zadaném čase.

    Po dokončení prvotního zálohování budou zálohy v počátečním záložním kopírování přírůstkově dokončeny. Přírůstkové zálohování je obvykle malé a snadno se přenáší přes síť.

1. Vyberte, kdy se má spustit Kontrola konzistence. Pak vyberte **Další**.

    ![Vyberte, kdy se má spustit Kontrola konzistence.](./media/backup-azure-backup-sql/pg-consistent.png)

    DPM může spustit kontrolu konzistence v rámci integrity záložního bodu. Vypočítá kontrolní součet záložního souboru na provozním serveru (SQL Server počítač v tomto příkladu) a zálohovaná data pro tento soubor v DPM. Pokud tato kontrolu najde konflikt, předpokládá se, že zálohovaný soubor v DPM je poškozený. DPM opraví zálohovaná data odesláním bloků, které odpovídají neshodě kontrolního součtu. Vzhledem k tomu, že je kontrola konzistence náročná na výkon, můžou správci rozhodnout naplánovat kontrolu konzistence nebo ji spouštět automaticky.

1. Vyberte zdroje dat, které chcete chránit v Azure. Pak vyberte **Další**.

    ![Vyberte zdroje dat, které chcete chránit v Azure.](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Pokud jste správce, můžete zvolit plány zálohování a zásady uchovávání informací, které vyhovují zásadám vaší organizace.

    ![Zvolit plány a zásady uchovávání informací](./media/backup-azure-backup-sql/pg-schedule.png)

    V tomto příkladu se zálohování provádí denně v 12:00 PM a 8:00 odp.

    > [!TIP]
    > Pro rychlé obnovení Udržujte na disku několik krátkodobých bodů obnovení. Tyto body obnovení se používají pro provozní obnovení. Azure slouží jako dobrá poloha mimo pracoviště a poskytuje vyšší SLA a zaručenou dostupnost.
    >
    > Použijte DPM k naplánování záloh Azure po dokončení zálohování na místní disk. Po provedení tohoto postupu se poslední záloha disku zkopíruje do Azure.
    >

1. Vyberte plán zásad uchovávání informací. Další informace o tom, jak zásady uchovávání informací fungují, najdete v tématu [použití Azure Backup k nahrazení páskové infrastruktury](backup-azure-backup-cloud-as-tape.md).

    ![Zvolit zásady uchovávání informací](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    V tomto příkladu:

    * Zálohy se účtují každý den v 12:00 PM a 8:00 odp. Uchovávají se po dobu 180 dnů.
    * Záloha v sobotu v 12:00./odp. se uchovává po dobu 104 týdnů.
    * Záloha z poslední sobotu měsíce v 12:00./odp. je uchována po dobu 60 měsíců.
    * Záloha z poslední sobotu v březnu v 12:00 PM je uchována po dobu 10 let.

    Po výběru zásady uchovávání informací vyberte **Další**.

1. Vyberte způsob přenosu prvotní záložní kopie do Azure.

    * Možnost **automaticky přes síť** se řídí vaším plánem zálohování a převádí data do Azure.
    * Další informace o **offline zálohování** najdete v tématu [Přehled zálohování offline](offline-backup-overview.md).

    Po výběru mechanismu přenosu vyberte možnost **Další**.

1. Na stránce **Souhrn** zkontrolujte podrobnosti zásady. Pak vyberte **vytvořit skupinu**. Můžete vybrat **Zavřít** a sledovat průběh úlohy v pracovním prostoru **monitorování** .

    ![Průběh vytváření skupiny ochrany](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>Vytváření záložních kopií databáze SQL Server na vyžádání

Bod obnovení se vytvoří, když dojde k prvnímu zálohování. Místo čekání na spuštění plánu můžete ručně aktivovat vytvoření bodu obnovení:

1. Ve skupině ochrany se ujistěte, že je stav databáze **OK**.

    ![Skupina ochrany, která zobrazuje stav databáze](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Klikněte pravým tlačítkem na databázi a pak vyberte **vytvořit bod obnovení**.

    ![Zvolit vytvoření bodu obnovení online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. V rozevírací nabídce vyberte možnost **online ochrana**. Pak vyberte **OK** a začněte vytvářet bod obnovení v Azure.

    ![Začněte vytvářet bod obnovení v Azure.](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. Průběh úlohy můžete zobrazit v pracovním prostoru **monitorování** .

    ![Zobrazení průběhu úlohy v konzole monitorování](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Obnovení databáze SQL Server z Azure

Chcete-li obnovit chráněnou entitu, jako je například databáze SQL Server, z Azure:

1. Otevřete konzolu pro správu serveru DPM. Přejděte do pracovního prostoru **obnovení** a zobrazte servery, které aplikace DPM zálohuje. Vyberte databázi (v tomto příkladu: ReportServer $ MSDPM2012). Vyberte **čas obnovení** , který končí na **online**.

    ![Výběr bodu obnovení](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Klikněte pravým tlačítkem na název databáze a vyberte **obnovit**.

    ![Obnovení databáze z Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. DPM zobrazuje podrobnosti bodu obnovení. Vyberte **Další**. Chcete-li přepsat databázi, vyberte typ obnovení **obnovit do původní instance SQL Server**. Pak vyberte **Další**.

    ![Obnovení databáze do původního umístění](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    V tomto příkladu aplikace DPM umožňuje obnovení databáze do jiné instance SQL Server nebo do samostatné síťové složky.
1. Na stránce **zadat možnosti obnovení** můžete vybrat možnosti obnovení. Můžete například zvolit **omezení využití šířky pásma sítě** k omezení šířky pásma, kterou obnovení používá. Pak vyberte **Další**.
1. Na stránce **Souhrn** se zobrazí aktuální konfigurace obnovení. Vyberte **obnovit**.

    Stav obnovení ukazuje obnovenou databázi. Kliknutím na tlačítko **Zavřít** můžete Průvodce zavřít a zobrazit průběh v pracovním prostoru **monitorování** .

    ![Spuštění procesu obnovení](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Po dokončení obnovení je obnovená databáze konzistentní s aplikací.

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Azure Backup Nejčastější dotazy](backup-azure-backup-faq.md).