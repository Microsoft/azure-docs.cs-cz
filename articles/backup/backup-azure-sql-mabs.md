---
title: Zálohování serveru SQL Server pomocí služby Azure Backup Server
description: V tomto článku se dozvíte, konfigurace zálohovat SQL Server databází pomocí Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 4a4d4b7e70e2df0e014ea4b4d23027aa7c48f2fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505943"
---
# <a name="back-up-sql-server-to-azure-by-using-azure-backup-server"></a>Zálohování SQL Serveru do Azure pomocí Azure Backup Serveru

Tento článek vám pomůže nastavit zálohy databází SQL Serveru pomocí Microsoft Azure Backup Server (MABS).

Zálohování databáze serveru SQL Server a její obnovení z Azure:

1. Vytvořte zásady zálohování pro ochranu databází SQL Serveru v Azure.
1. Vytvořte záložní kopie na vyžádání v Azure.
1. Obnovte databázi v Azure.

## <a name="before-you-start"></a>Než začnete

Než začnete, ujistěte se, že jste [nainstalovali a připravili Server zálohování Azure](backup-azure-microsoft-azure-backup.md).

## <a name="create-a-backup-policy"></a>Vytvoření zásad zálohování 

Chcete-li chránit databáze SERVERU SQL Server v Azure, nejprve vytvořte zásady zálohování:

1. V Azure Backup Server vyberte pracovní prostor **Ochrana.**
1. Chcete-li vytvořit skupinu ochrany, vyberte **možnost Nový.**

    ![Vytvoření skupiny ochrany na Azure Backup Serveru](./media/backup-azure-backup-sql/protection-group.png)
1. Na úvodní stránce si přečtěte pokyny k vytvoření skupiny ochrany. Pak vyberte **Další**.
1. Pro typ skupiny ochrany vyberte **servery**.

    ![Výběr typu skupiny ochrany serverů](./media/backup-azure-backup-sql/pg-servers.png)
1. Rozbalte sql server počítač, kde jsou umístěny databáze, které chcete zálohovat. Zobrazí se zdroje dat, které lze zálohovat z tohoto serveru. Rozbalte **všechny sdílené složky SQL** a vyberte databáze, které chcete zálohovat. V tomto příkladu vybereme ReportServer$MSDPM2012 a ReportServer$MSDPM2012TempDB. Vyberte **další**.

    ![Výběr databáze serveru SQL Server](./media/backup-azure-backup-sql/pg-databases.png)
1. Pojmenujte skupinu ochrany a vyberte **požadovanou ochranu online**.

    ![Zvolte metodu ochrany dat – krátkodobou ochranu disků nebo online ochranu Azure](./media/backup-azure-backup-sql/pg-name.png)
1. Na stránce **Zadat krátkodobé cíle** zahrňte potřebné vstupy k vytvoření záložních bodů na disk.

    V tomto příkladu je **rozsah uchovávání** nastaven na *5 dní*. Frekvence **synchronizace** zálohování je nastavena na jednou za *15 minut*. **Expresní úplné zálohování** je nastaveno na *20:00*.

    ![Nastavení krátkodobých cílů pro ochranu zálohování](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > V tomto příkladu je každý den ve 20:00 vytvořen bod zálohování. Data, která byla změněna od předchozího dne 20:00 bodu zálohování je převedena. Tento proces se nazývá **Expresní úplné zálohování**. Přestože protokoly transakcí jsou synchronizovány každých 15 minut, pokud potřebujeme obnovit databázi v 9:00 pm, pak je bod vytvořen přehráníprotokoly z posledního expresní úplné záložní bod, který je 8:00 PM v tomto příkladu.
   >
   >

1. Vyberte **další**. MABS zobrazuje celkový dostupný úložný prostor. Zobrazuje také potenciální využití místa na disku.

    ![Nastavit přidělení disku v MABS](./media/backup-azure-backup-sql/pg-storage.png)

    Ve výchozím nastavení mabs vytvoří jeden svazek na zdroj dat (databáze serveru SQL Server). Svazek se používá pro počáteční záložní kopii. V této konfiguraci správce logických disků (LDM) omezuje ochranu MABS na 300 zdrojů dat (databáze serveru SQL Server). Chcete-li toto omezení obejít, vyberte **možnost Co-locate data ve fondu úložiště DPM**. Pokud použijete tuto možnost, MABS používá jeden svazek pro více zdrojů dat. Toto nastavení umožňuje MABS chránit až 2 000 databází serveru SQL Server.

    Pokud vyberete **automaticky zvětšit svazky**, pak MABS může účet pro zvýšení objemu zálohování jako produkční data roste. Pokud nevyberete **Možnost Automaticky zvětšit svazky**, omezí MABS úložiště záloh na zdroje dat ve skupině ochrany.
1. Pokud jste správce, můžete tuto počáteční zálohu **přenést automaticky po síti** a zvolit čas přenosu. Nebo zvolte **ruční** přenos zálohy. Pak vyberte **Další**.

    ![Zvolte metodu vytvoření repliky v MABS](./media/backup-azure-backup-sql/pg-manual.png)

    Počáteční záložní kopie vyžaduje přenos celého zdroje dat (databáze serveru SQL Server). Záložní data se přesunou z produkčního serveru (SQL Server machine) do mabs. Pokud je tato záloha velká, přenos dat po síti může způsobit přetížení šířky pásma. Z tohoto důvodu mohou správci zvolit použití vyměnitelného média k ručnímu přenosu počáteční **zálohy**. Nebo mohou přenášet data **automaticky po síti** v určeném čase.

    Po dokončení počáteční zálohy zálohy pokračovat postupně na počáteční záložní kopie. Přírůstkové zálohy mají tendenci být malé a lze je snadno přenášet po síti.
1. Zvolte, kdy chcete spustit kontrolu konzistence. Pak vyberte **Další**.

    ![Volba volby, kdy se má spustit kontrola konzistence.](./media/backup-azure-backup-sql/pg-consistent.png)

    MABS může spustit kontrolu konzistence na integritu bodu zálohování. Vypočítá kontrolní součet záložního souboru na produkčním serveru (počítač SQL Server v tomto příkladu) a zálohovaná data pro tento soubor v MABS. Pokud kontrola zjistí konflikt, předpokládá se, že zálohovaný soubor v MABS je poškozen. MABS opravuje zálohovaná data odesláním bloků, které odpovídají neshodě kontrolního součtu. Vzhledem k tomu, že kontrola konzistence je operace náročná na výkon, správci mohou naplánovat kontrolu konzistence nebo ji spustit automaticky.
1. Vyberte zdroje dat, které chcete chránit v Azure. Pak vyberte **Další**.

    ![Výběr zdrojů dat, které chcete chránit v Azure](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Pokud jste správce, můžete zvolit plány zálohování a zásady uchovávání informací, které vyhovují zásadám vaší organizace.

    ![Výběr plánů a zásad uchovávání informací](./media/backup-azure-backup-sql/pg-schedule.png)

    V tomto příkladu jsou zálohy přijímány denně ve 12:00 a 20:00.

    > [!TIP]
    > Pro rychlé obnovení, zachovat několik krátkodobých bodů obnovení na disku. Tyto body obnovení se používají pro provozní využití. Azure slouží jako dobré umístění mimo pracoviště a poskytuje vyšší sla a garantovanou dostupnost.
    >
    > Pomocí Správce ochrany dat (DPM) naplánujte zálohování Azure po dokončení zálohování místního disku. Když budete postupovat podle tohoto průvodce, nejnovější záloha disku se zkopíruje do Azure.
    >


1. Zvolte plán zásad uchovávání informací. Další informace o tom, jak zásady uchovávání informací fungují, najdete [v tématu Použití služby Azure Backup k nahrazení páskové infrastruktury](backup-azure-backup-cloud-as-tape.md).

    ![Zvolte zásady uchovávání informací v MABS](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    V tomto příkladu:

    * Zálohy se uskutečují denně ve 12:00 a 20:00. Jsou drženy 180 dní.
    * Záloha v sobotu ve 12:00 je uchovávána po dobu 104 týdnů.
    * Záloha z poslední soboty v měsíci ve 12:00 je uchovávána po dobu 60 měsíců.
    * Záloha z poslední sobotu v březnu ve 12:00 hodin je uchovávána po dobu 10 let.

    Po výběru zásad uchovávání informací vyberte **Další**.
1. Zvolte, jak přenést počáteční záložní kopii do Azure.

    * Možnost **Automaticky přes síť** následuje plán zálohování pro přenos dat do Azure.
    * Další informace o **zálohování offline**naleznete v [tématu Přehled zálohování offline](offline-backup-overview.md).

    Po výběru mechanismu přenosu vyberte **Další**.
1. Na stránce **Souhrn** zkontrolujte podrobnosti o zásadách. Pak vyberte **Vytvořit skupinu**. Můžete vybrat **Zavřít** a sledovat průběh úlohy v pracovním prostoru **monitorování.**

    ![Pokrok při vytváření skupiny ochrany](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>Vytvoření záložních kopií databáze serveru SQL Server na vyžádání

Bod obnovení je vytvořen při první zálohy dojde. Místo čekání na spuštění plánu můžete ručně spustit vytvoření bodu obnovení:

1. Ve skupině ochrany zkontrolujte, zda je stav databáze **v pořádku**.

    ![Skupina ochrany zobrazující stav databáze](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Klepněte pravým tlačítkem myši na databázi a vyberte **příkaz Vytvořit bod obnovení**.

    ![Zvolte vytvoření online bodu obnovení.](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. V rozevírací nabídce vyberte **Online ochranu**. Pak vyberte **OK** a spusťte vytváření bodu obnovení v Azure.

    ![Začněte vytvářet bod obnovení v Azure](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. Průběh úlohy můžete zobrazit v pracovním prostoru **monitorování.** 

    ![Zobrazit průběh úlohy v monitorovací konzoli](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Obnovení databáze SERVERU SQL Server z Azure

Obnovení chráněné entity, jako je například databáze SERVERU SQL Server, z Azure:

1. Otevřete konzolu pro správu serveru DPM. Přejděte do pracovního prostoru **pro obnovení** a podívejte se na servery, které aplikace DPM zálohovala. Vyberte databázi (v tomto příkladu ReportServer$MSDPM2012). Vyberte **čas obnovení,** který končí **online**.

    ![Výběr bodu obnovení](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Klepněte pravým tlačítkem myši na název databáze a vyberte **příkaz Obnovit**.

    ![Obnovení databáze z Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. Aplikace DPM zobrazuje podrobnosti bodu obnovení. Vyberte **další**. Chcete-li databázi přepsat, vyberte typ obnovení **Recover na původní instanci serveru SQL Server**. Pak vyberte **Další**.

    ![Obnovení databáze do původního umístění](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    V tomto příkladu aplikace DPM umožňuje obnovení databáze do jiné instance serveru SQL Server nebo do samostatné síťové složky.
1. Na stránce **Zadat možnosti obnovení** můžete vybrat možnosti obnovení. Můžete například zvolit **omezení využití šířky pásma sítě,** abyste omezili šířku pásma, kterou obnovení používá. Pak vyberte **Další**.
1. Na stránce **Souhrn** se zobrazí aktuální konfigurace obnovení. Vyberte **Obnovit**.

    Stav obnovení zobrazuje databázi, která je obnovována. Chcete-li průvodce zavřít a zobrazit průběh v pracovním prostoru **monitorování,** můžete vybrat **Zavřít.**

    ![Spuštění procesu obnovení](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Po dokončení obnovení obnovené databáze je konzistentní s aplikací.

### <a name="next-steps"></a>Další kroky

Další informace najdete v [tématu Nejčastější dotazy k zálohování v Azure](backup-azure-backup-faq.md).
