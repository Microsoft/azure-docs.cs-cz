---
title: Zálohování úloh SQL serveru v Azure zásobníku
description: Pomocí serveru Azure Backup chránit zatížení serveru SQL v Azure zásobníku.
services: backup
author: pvrk
manager: Shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: pullabhk
ms.openlocfilehash: ca7da7ab048b6f7bfdba81aac9bc7702b20ff967
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751793"
---
# <a name="back-up-sql-server-on-stack"></a>Zálohování serveru SQL v zásobníku
Tento článek slouží ke konfiguraci Microsoft Azure Backup Server (MABS) pro ochranu databází systému SQL Server v Azure zásobníku.

Správa zálohování databáze serveru SQL Server do Azure a obnovení z Azure zahrnuje tři kroky:

1. Vytvořit zásady zálohování pro ochranu databází systému SQL Server
2. Vytvořit kopie zálohy na vyžádání
3. Obnovit databázi z disků a z Azure

## <a name="before-you-start"></a>Než začnete

[Instalace a Příprava serveru Azure Backup](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Vytvořit zásady zálohování pro ochranu databází systému SQL Server do Azure
1. V Uživatelském rozhraní Azure Backup Server, klikněte **ochrany** pracovního prostoru.

2. Na pásu karet nástroje klikněte na tlačítko **nový** k vytvoření nové skupiny ochrany.

    ![Vytvořit skupinu ochrany](./media/backup-azure-backup-sql/protection-group.png)

    Azure Backup Server spustí Průvodce skupiny ochrany, které vás provede procesem vytvoření **skupiny ochrany**. Klikněte na **Další**.

3. V **vybrat typ skupiny ochrany** vyberte **servery**.

    ![Vybrat typ skupiny ochrany – 'servery.](./media/backup-azure-backup-sql/pg-servers.png)

4. V **vybrat členy skupiny** různých zdrojů dat. zobrazí obrazovky seznamu Dostupní členové. Klikněte na tlačítko **+** rozbalte složky a odhalit podsložky. Klikněte na zaškrtávací políčko Vybrat položku.

    ![Vyberte databázi SQL](./media/backup-azure-backup-sql/pg-databases.png)

    Všechny vybrané položky se zobrazí v seznamu vybrané členy. Po výběru serverů nebo databází, které chcete chránit, klikněte na **Další**.

5. V **vyberte způsob ochrany dat** obrazovky, zadejte název pro skupinu ochrany a vyberte **chci online ochranu** zaškrtávací políčko.

    ![Způsob ochrany dat – krátkodobé disku & Online Azure](./media/backup-azure-backup-sql/pg-name.png)

6. V **zadat krátkodobé cíle** obrazovky, zahrnují nezbytné vstupy pro vytvoření body zálohy na disku, a klikněte na **Další**.

    V příkladu **rozsah uchování** je **5 dní**, **četnost synchronizace** je jednou každých **15 minut**, což je zálohování četnost. **Expresní úplná záloha** je nastaven na **východního**.

    ![Krátkodobé cíle](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Z příkladu v 8:00 PM každý den bodu zálohy vytvoří přenosu upravených dat z bodu zálohy 8:00 PM předchozí den. Tento proces se nazývá **expresní úplné zálohování**. Protokoly transakcí se synchronizují každých 15 minut. Pokud potřebujete obnovit databázi na 21:00:00, vytvoří se bodem z protokolů za posledních expresní úplné zálohování bod (20: 00 v tomto případě).
   >
   >

7. Na **zkontrolovat přidělení disku** obrazovky, ověřte celkového prostoru úložiště, která je k dispozici a potenciální místo na disku. Klikněte na **Další**.

8. V **vyberte způsob vytvoření repliky**, jak vytvořit první bod obnovení. Aby se zabránilo zahlcení šířky pásma se můžou přenášet prvotní zálohování ručně (vypnuto síť) nebo přes síť. Pokud si zvolíte čekání na přenos první zálohy, můžete pro přenos počáteční čas. Klikněte na **Další**.

    ![Metodu počáteční replikace](./media/backup-azure-backup-sql/pg-manual.png)

    Kopie prvotní zálohy vyžaduje přenosu celého datového zdroje (databázi systému SQL Server) z provozního serveru (počítači serveru SQL Server) na Azure Backup Server. Tato data mohou být velký a přenos dat přes síť může překročit šířky pásma. Z tohoto důvodu můžete přenést prvotní zálohování: **ručně** (pomocí vyměnitelného média) předejdete zahlcení šířky pásma, nebo **automaticky prostřednictvím sítě** (v určený čas).

    Po dokončení prvotní zálohování zbývající zálohy jsou přírůstkových záloh v kopie prvotní zálohy. Přírůstkové zálohování jsou obvykle malé a snadno přenést přes síť.

9. Vyberte, pokud chcete kontrolu konzistence, spusťte a klikněte na tlačítko **Další**.

    ![Kontrola konzistence](./media/backup-azure-backup-sql/pg-consistent.png)

    Azure Backup Server provede kontrolu konzistence na integritě bodu zálohy. Azure Backup Server vypočítá kontrolního součtu záložní soubor na provozním serveru (počítači serveru SQL Server v tomto scénáři) a zálohovaná data pro tento soubor. Pokud dojde ke konfliktu, předpokládá se, že soubor zálohovaná na serveru Azure Backup je poškozený. Azure Backup Server rectifies zálohovaná data odesláním bloky odpovídající neshoda kontrolního součtu. Protože kontroly konzistence jsou náročné na výkon, můžete naplánovat kontrolu konzistence nebo automatické spouštění.

10. K určení online ochrany zdroje dat, vyberte databáze chránit do služby Azure a klikněte na tlačítko **Další**.

    ![Vyberte zdroje dat](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Zvolte plánů zálohování a zásady uchovávání informací, která vyhovuje zásady organizace.

    ![Plán a jejich uchovávání](./media/backup-azure-backup-sql/pg-schedule.png)

    V tomto příkladu jsou provedeny zálohy jednou za den v 12:00 PM a 20: 00 (dolní části obrazovky)

    > [!NOTE]
    > Je vhodné mít několik krátkodobé body obnovení na disku pro rychlé obnovení. Tyto body obnovení se používají pro provozní obnovení. Azure slouží jako dobrý mimo pracoviště s vyšší SLA a záruku dostupnosti.
    >
    >

    **Nejvhodnější**: při plánování zálohování na Azure spustit po dokončení zálohování místního disku, nejnovější zálohy disku se vždy zkopírují do Azure.

12. Zvolte plán zásad uchovávání informací. Podrobnosti o způsobu fungování zásady uchovávání informací jsou k dispozici v [pomocí Azure Backup k nahrazení váš článek infrastruktury pásky](backup-azure-backup-cloud-as-tape.md).

    ![Zásady uchovávání informací](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    V tomto příkladu:

    * Zálohování se pořídí jednou denně na 12:00 PM a 20: 00 (dolní části obrazovky) a jsou uchovány na 180 dní.
    * Zálohování na sobotu ve 12:00 se zachovává kvůli 104 týdny
    * Zálohování na poslední sobotu ve 12:00 se uchovávají po dobu 60 měsíců
    * Zálohování na poslední sobotu dne ve 12:00 se uchovávají 10 let.
13. Klikněte na tlačítko **Další** a vyberte příslušnou možnost pro přenesení kopie prvotní zálohy do Azure. Můžete zvolit **automaticky přes síť**

14. Po prostudování podrobnosti zásady v **Souhrn** obrazovky, klikněte na tlačítko **vytvořit skupinu** k dokončení pracovního postupu. Můžete kliknout na **Zavřít** a sledovat průběh úlohy v pracovním prostoru monitorování.

    ![Vytvoření skupiny ochrany v průběhu](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Zálohování na vyžádání databáze systému SQL Server
Když v předchozím postupu vytvořili zásadu zálohování, "bodu obnovení" se vytvoří jenom v případě, že dochází k první zálohování. Místo čekání plánovače na nové, kroků aktivační událost vytvoření obnovení bodu ručně.

1. Počkejte, až se zobrazí stav skupiny ochrany **OK** pro databázi před vytvořením bodu obnovení.

    ![Členové skupiny ochrany](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Klikněte pravým tlačítkem na databázi a vyberte **vytvořit bod obnovení**.

    ![Vytvořit bod obnovení Online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Zvolte **Online ochrany** rozevírací nabídky a klikněte na **OK** zahájíte vytváření bodu obnovení v Azure.

    ![Vytvoření bodu obnovení](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Průběh úlohy v zobrazení **monitorování** pracovního prostoru.

    ![Konzola monitorování](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Obnovení databáze systému SQL Server z Azure
Následující kroky jsou potřebné k obnovení chráněné entity (databázi systému SQL Server) ze služby Azure.

1. Otevřete konzolu pro správu Azure Backup Server. Přejděte na **obnovení** prostoru, kde se můžete podívat chráněných serverech. Procházejte požadovaná databáze (v této rozlišují ReportServer$ MSDPM2012). Vyberte **obnovení z** čas, který je zadán jako **Online** bodu.

    ![Vyberte bod obnovení](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Klikněte pravým tlačítkem na název databáze a klikněte na tlačítko **obnovit**.

    ![Obnovení z Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. MABS zobrazuje podrobnosti o vytvoření bodu obnovení. Klikněte na **Další**. Chcete-li přepsat databázi, vyberte typ obnovení **obnovit na původní instanci systému SQL Server**. Klikněte na **Další**.

    ![Obnovení do původního umístění](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    V tomto příkladu MABS obnoví databáze do jiné instance systému SQL Server nebo do samostatné síťové složky.

4. V **možnosti obnovení zadejte** obrazovce můžete vybrat možnosti obnovení jako omezení využití šířky pásma šířky pásma sítě používané při obnovení. Klikněte na **Další**.

5. V **Souhrn** obrazovky, uvidíte všechny konfigurace obnovení dosavadní zadat. Klikněte na tlačítko **obnovit**.

    Stav obnovení zobrazuje databázi obnovení. Můžete kliknout na **zavřete** zavřete průvodce a sledovat průběh v **monitorování** pracovního prostoru.

    ![Zahájení obnovení](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Po dokončení obnovení je obnovenou databázi aplikace, které jsou konzistentní.

## <a name="next-steps"></a>Další kroky

Najdete v článku [zálohovat soubory a aplikace](backup-mabs-files-applications-azure-stack.md) článku.
Najdete v článku [zálohování služby SharePoint v zásobníku Azure](backup-mabs-sharepoint-azure-stack.md) článku.
