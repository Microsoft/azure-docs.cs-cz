---
title: Zálohování úloh SQL Serveru v Azure Stacku
description: V tomto článku se dozvíte, jak nakonfigurovat Microsoft Azure Backup Server (MABS) k ochraně databází SERVERU SQL Server v Azure Stacku.
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: 03211e1147f96429a8406c4c95654161ed2bf308
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172308"
---
# <a name="back-up-sql-server-on-azure-stack"></a>Zálohování sql serveru v azure zásobníku

Tento článek slouží ke konfiguraci Microsoft Azure Backup Server (MABS) k ochraně databází SERVERU SQL Server v Azure Stack.

Správa zálohování databáze SQL Server do Azure a obnovení z Azure zahrnuje tři kroky:

1. Vytvoření zásady zálohování pro ochranu databází serveru SQL Server
2. Vytvoření záložních kopií na vyžádání
3. Obnovení databáze z disků a z Azure

## <a name="before-you-start"></a>Než začnete

[Nainstalujte a připravte server Azure Backup Server](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Vytvoření zásad zálohování pro ochranu databází SQL Serveru do Azure

1. V uzu Azure Backup Server klikněte na pracovní prostor **Ochrana.**

2. Na pásu karet nástrojů klikněte na **Nový** a vytvořte novou skupinu ochrany.

    ![Vytvořit skupinu ochrany](./media/backup-azure-backup-sql/protection-group.png)

    Azure Backup Server spustí průvodce skupinou ochrany, který vás provede vytvořením **skupiny ochrany**. Klikněte na **Další**.

3. Na obrazovce **Vybrat typ skupiny ochrany** vyberte **položku Servery**.

    ![Vybrat typ skupiny ochrany - "Servery"](./media/backup-azure-backup-sql/pg-servers.png)

4. Na obrazovce **Vybrat členy skupiny** se v seznamu Dostupné členy zobrazují různé zdroje dat. Klepnutím **+** rozbalíte složku a zjevete podsložky. Klepnutím na zaškrtávací políčko vyberte položku.

    ![Vybrat databázi SQL](./media/backup-azure-backup-sql/pg-databases.png)

    Všechny vybrané položky se zobrazí v seznamu Vybrané členy. Po výběru serverů nebo databází, které chcete chránit, klepněte na tlačítko **Další**.

5. Na obrazovce **Vybrat metodu ochrany dat** zadejte název skupiny ochrany a zaškrtněte políčko **Požadovaná ochrana online.**

    ![Metoda ochrany dat – krátkodobá disková & Online Azure](./media/backup-azure-backup-sql/pg-name.png)

6. Na obrazovce **Zadat krátkodobé cíle** zahrňte potřebné vstupy pro vytvoření záložních bodů na disk a klepněte na tlačítko **Další**.

    V příkladu je **rozsah uchování** **5 dní**, **frekvence synchronizace** je jednou za **15 minut**, což je frekvence zálohování. **Expresní úplné zálohování** je nastaveno na **20:00**.

    ![Krátkodobé cíle](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > V zobrazeném příkladu je každý den ve 20:00 vytvořen záložní bod přenosem upravených dat z bodu zálohování předchozího dne 20:00. Tento proces se nazývá **Expresní úplné zálohování**. Transakční protokoly jsou synchronizovány každých 15 minut. Pokud potřebujete obnovit databázi v 21:00, bod je vytvořen z protokolů z posledního expresního úplného bodu zálohování (v tomto případě 20:00).
   >
   >

7. Na obrazovce **Zkontrolovat přidělení disku** ověřte celkový dostupný úložný prostor a potenciální místo na disku. Klikněte na **Další**.

8. V **metodě výběru vytvoření repliky**zvolte, jak vytvořit první bod obnovení. Počáteční zálohu můžete přenést ručně (mimo síť), abyste zabránili přetížení šířky pásma nebo přes síť. Pokud se rozhodnete počkat na přenos první zálohy, můžete zadat čas počátečního přenosu. Klikněte na **Další**.

    ![Metoda počáteční replikace](./media/backup-azure-backup-sql/pg-manual.png)

    Počáteční záložní kopie vyžaduje přenos celého zdroje dat (databáze SERVERU SQL Server) z produkčního serveru (SQL Server machine) na Azure Backup Server. Tato data mohou být velká a přenos dat po síti může překročit šířku pásma. Z tohoto důvodu můžete převést počáteční zálohu: **Ručně** (pomocí vyměnitelného média), abyste zabránili přetížení šířky pásma, nebo **Automaticky v síti** (v určený čas).

    Po dokončení počáteční zálohy jsou zbývající zálohy přírůstkové zálohy na počáteční záložní kopii. Přírůstkové zálohy mají tendenci být malé a lze je snadno přenášet po síti.

9. Zvolte, kdy má být kontrola konzistence spuštěna, a klepněte na tlačítko **Další**.

    ![Kontrola konzistence](./media/backup-azure-backup-sql/pg-consistent.png)

    Azure Backup Server provádí kontrolu konzistence integrity bodu zálohování. Azure Backup Server vypočítá kontrolní součet záložního souboru na produkčním serveru (sql server v tomto scénáři) a zálohovaná data pro tento soubor. Pokud dojde ke konfliktu, předpokládá se, že zálohovaný soubor na serveru Azure Backup Server je poškozený. Azure Backup Server opraví zálohovaná data odesláním bloků odpovídajících neshodě kontrolního součtu. Vzhledem k tomu, že kontroly konzistence jsou náročné na výkon, můžete naplánovat kontrolu konzistence nebo ji spustit automaticky.

10. Chcete-li určit online ochranu zdrojů dat, vyberte databáze, které mají být chráněny do Azure a klepněte na tlačítko **Další**.

    ![Vybrat zdroje dat](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Zvolte plány zálohování a zásady uchovávání informací, které vyhovují zásadám organizace.

    ![Plán a uchovávání](./media/backup-azure-backup-sql/pg-schedule.png)

    V tomto příkladu jsou zálohy přijímány jednou denně ve 12:00 a 20:00 (spodní část obrazovky)

    > [!NOTE]
    > Je vhodné mít několik krátkodobých bodů obnovení na disku pro rychlé obnovení. Tyto body obnovení se používají pro provozní využití. Azure slouží jako dobré umístění mimo pracoviště s vyššími sla a garantovanou dostupností.
    >
    >

    **Osvědčený postup:** Pokud naplánujete zálohování do Azure, aby se spustilo po dokončení zálohování místního disku, budou nejnovější zálohy disků vždy zkopírovány do Azure.

12. Zvolte plán zásad uchovávání informací. Podrobnosti o tom, jak zásady uchovávání informací funguje jsou k dispozici na [použití Azure Backup nahradit tape infrastruktury článku](backup-azure-backup-cloud-as-tape.md).

    ![Zásady uchovávání](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    V tomto příkladu:

    * Zálohy se uskutečňují jednou denně ve 12:00 a 20:00 (spodní část obrazovky) a jsou uchovávány po dobu 180 dnů.
    * Záloha v sobotu ve 12:00. je uchováván po dobu 104 týdnů
    * Záloha minulou sobotu ve 12:00. je uchováván po dobu 60 měsíců
    * Záloha v sobotu v březnu ve 12:00. je uchováván po dobu 10 let
13. Klikněte na **Další** a vyberte příslušnou možnost pro přenos počáteční záložní kopie do Azure. Můžete zvolit **Automaticky přes síť**

14. Po kontrole podrobností zásad na obrazovce **Souhrn** dokončete pracovní postup kliknutím na **Vytvořit skupinu.** Můžete **klepnout** na zavřít a sledovat průběh úlohy v pracovním prostoru monitorování.

    ![Vytvoření provázlé ochranné skupiny](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Zálohování databáze serveru SQL Server na vyžádání

Zatímco předchozí kroky vytvořily zásady zálohování, "bod obnovení" je vytvořen pouze v případě, že dojde k první zálohě. Místo čekání na plánovač e-do, níže uvedené kroky spustit vytvoření bodu obnovení ručně.

1. Před vytvořením bodu obnovení počkejte, až se stav skupiny ochrany zobrazí **v pořádku** pro databázi.

    ![Členové ochranné skupiny](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Klepněte pravým tlačítkem myši na databázi a vyberte **příkaz Vytvořit bod obnovení**.

    ![Vytvořit online bod obnovení](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. V rozevírací nabídce zvolte **Online ochrana** a kliknutím na **OK** spusťte vytváření bodu obnovení v Azure.

    ![Vytvořit bod obnovení](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Zobrazení průběhu úlohy v pracovním prostoru **monitorování.**

    ![Monitorovací konzola](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Obnovení databáze SERVERU SQL Server z Azure

Následující kroky jsou nutné k obnovení chráněné entity (databáze SQL Server) z Azure.

1. Otevřete Konzolu pro správu serveru zálohování Azure. Přejděte do pracovního prostoru **obnovení,** kde můžete zobrazit chráněné servery. Procházet požadovanou databázi (v tomto případě ReportServer$MSDPM2012). Vyberte **zotavení z** doby, která je určena jako **online** bod.

    ![Vybrat bod obnovení](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Klepněte pravým tlačítkem myši na název databáze a klepněte na příkaz **Obnovit**.

    ![Obnovení z Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. MABS zobrazuje podrobnosti o bodu obnovení. Klikněte na **Další**. Chcete-li databázi přepsat, vyberte typ obnovení **Recover na původní instanci serveru SQL Server**. Klikněte na **Další**.

    ![Obnovit do původního umístění](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    V tomto příkladu MABS obnoví databázi do jiné instance serveru SQL Server nebo do samostatné síťové složky.

4. Na obrazovce **Určit možnosti obnovení** můžete vybrat možnosti obnovení, jako je omezení využití šířky pásma sítě, abyste omezili šířku pásma používanou obnovením. Klikněte na **Další**.

5. Na obrazovce **Souhrn** se zobrazí všechny dosud poskytnuté konfigurace obnovení. Klepněte na **tlačítko Obnovit**.

    Stav obnovení zobrazuje databázi, která se obnovuje. Klepnutím na tlačítko **Zavřít** zavřete průvodce a zobrazte průběh v pracovním prostoru **monitorování.**

    ![Zahájit proces obnovení](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Po dokončení obnovení je obnovená databáze konzistentní v aplikaci.

## <a name="next-steps"></a>Další kroky

Viz [článek Záložní soubory a aplikace.](backup-mabs-files-applications-azure-stack.md)
Podívejte se na článek [Zálohování SharePointu v Azure Stacku.](backup-mabs-sharepoint-azure-stack.md)
