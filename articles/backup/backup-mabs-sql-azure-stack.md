---
title: Zálohování úloh SQL serveru ve službě Azure Stack
description: Použití Azure Backup serveru k ochraně úloh SQL serveru ve službě Azure Stack.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: adigan
ms.openlocfilehash: fb064c39fa014515fb2a3f4ccc96ce216f2f7b2e
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55493503"
---
# <a name="back-up-sql-server-on-stack"></a>Zálohování SQL serveru v zásobníku
Tento článek slouží ke konfiguraci Microsoft Azure Backup Server (MABS) k ochraně databází systému SQL Server ve službě Azure Stack.

Správa zálohování databáze SQL serveru do Azure a obnovení z Azure zahrnuje tři kroky:

1. Vytvoření zásady zálohování pro ochranu databáze SQL serveru
2. Vytvoření kopie záloh na vyžádání
3. Obnovit databázi z disků a z Azure

## <a name="before-you-start"></a>Než začnete

[Instalace a připravit Azure Backup serveru](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Vytvoření zásady zálohování pro ochranu databáze SQL serveru do Azure
1. V Uživatelském rozhraní služby Azure Backup Server, klikněte **ochrany** pracovního prostoru.

2. Na pásu karet nástroje klikněte na tlačítko **nový** k vytvoření nové skupiny ochrany.

    ![Vytvořit skupinu ochrany](./media/backup-azure-backup-sql/protection-group.png)

    Azure Backup serveru spustí Průvodce skupiny ochrany, které vás provede procesem vytvoření **skupiny ochrany**. Klikněte na **Další**.

3. V **vybrat typ skupiny ochrany** vyberte **servery**.

    ![Vybrat typ skupiny ochrany – "servery.](./media/backup-azure-backup-sql/pg-servers.png)

4. V **vybrat členy skupiny** obrazovky, seznam dostupných členů obsahuje různé zdroje dat. Klikněte na tlačítko **+** rozbalte složky a odhalit podsložky. Klikněte na zaškrtávací políčko Vybrat položku.

    ![Vyberte SQL DB](./media/backup-azure-backup-sql/pg-databases.png)

    Všechny vybrané položky se zobrazí v seznamu vybraných členů. Po výběru servery nebo databáze, které chcete chránit, klikněte na **Další**.

5. V **vybrat způsob ochrany dat** obrazovky, zadejte název pro skupinu ochrany a vyberte **chci online ochranu** zaškrtávací políčko.

    ![Způsob ochrany dat – krátkodobý disk & Online Azure](./media/backup-azure-backup-sql/pg-name.png)

6. V **zadat krátkodobé cíle** obrazovku, poskytněte nezbytné vstupy pro vytvoření body záloh na disku, a klikněte na **Další**.

    V tomto příkladu **rozsah uchování** je **5 dní**, **četnost synchronizací** jednou každých **15 minut**, což je zálohování frekvence. **Expresní úplné zálohování** je nastavena na **východního**.

    ![Krátkodobé cíle](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Jak ukazuje příklad ve 20:00:00 každý den bodu zálohy je vytvořen a současný přechod upravených dat z bodu zálohy 20:00:00 předchozího dne. Tento proces se nazývá **expresní úplné zálohování**. Protokoly transakcí se synchronizují každých 15 minut. Pokud je potřeba obnovit databázi ve 21:00:00, bod je vytvořený z protokolů z poslední expresní úplné zálohování bod (20: 00 v tomto případě).
   >
   >

7. Na **Kontrola přidělení disku** obrazovky, ověřte celkový úložiště místa a potenciálního místa na disku. Klikněte na **Další**.

8. V **vyberte způsob vytvoření repliky**, zvolte, jak vytvořit první bod obnovení. Prvotní zálohování ručně (mimo síť) můžou přenášet do vyhnout zahlcení šířky pásma nebo v síti. Pokud budete chtít čekat na přenos první zálohování, můžete zadat čas pro počáteční přenos. Klikněte na **Další**.

    ![Metoda počáteční replikace](./media/backup-azure-backup-sql/pg-manual.png)

    Kopie prvotní zálohy. vyžaduje přenos celý zdroj dat (databáze SQL serveru) z provozního serveru (počítač s SQL serverem) do Azure Backup Server. Tato data můžou být velké a přenášení dat přes síť může být delší než šířka pásma. Z tohoto důvodu můžete přenést prvotní zálohování: **Ručně** (pomocí vyměnitelného média) Chcete-li se vyhnout zahlcení šířky pásma, nebo **automaticky přes síť** (v určený čas).

    Po dokončení prvotní zálohy se rest k zálohování přírůstkových záloh v kopie prvotní zálohy. Přírůstkové zálohování jsou obvykle malé a snadno přenést přes síť.

9. Zvolte, pokud chcete kontrolu konzistence pro spuštění a klikněte na tlačítko **Další**.

    ![Kontrola konzistence](./media/backup-azure-backup-sql/pg-consistent.png)

    Azure Backup Server provede kontrolu konzistence na integritě bodu zálohy. Azure Backup Server vypočítá součet záložní soubor na provozním serveru (počítač s SQL serverem v tomto scénáři) a zálohovaná data pro tento soubor. Pokud dojde ke konfliktu, předpokládá se, že soubor zálohovaná na serveru Azure Backup je poškozený. Azure Backup serveru rectifies zálohovaných dat odesláním bloky odpovídající neshoda kontrolního součtu. Protože kontroly konzistence jsou náročné na výkon, můžete naplánovat kontrolu konzistence nebo jeho automatické spuštění.

10. K určení online ochrany zdrojů dat, vyberte databázi, kterou chcete chránit v Azure a klikněte na tlačítko **Další**.

    ![Vyberte zdroje dat](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Zvolte plány zálohování a zásady uchovávání informací, které vyhovují zásad organizace.

    ![Plán a uchovávání](./media/backup-azure-backup-sql/pg-schedule.png)

    V tomto příkladu zálohy jsou prováděny jednou za den v 12:00 hodin a 20: 00 (dolní části obrazovky)

    > [!NOTE]
    > Je vhodné mít několik krátkodobé body obnovení na disku pro rychlé obnovení. Tyto body obnovení jsou používány pro obnovení. Azure slouží jako dobrý mimo pracoviště s vyšší smlouvy o úrovni služeb a zaručena dostupnost.
    >
    >

    **Osvědčený postup**: Pokud naplánujete zálohování do Azure, spustit po dokončení zálohování místního disku, nejnovější zálohy disku jsou vždy zkopírovány do Azure.

12. Zvolte plán zásad uchovávání informací. Podrobnosti o tom, jak funguje zásady uchovávání informací jsou k dispozici v [použití Azure Backup k nahrazení páskové infrastruktury článku](backup-azure-backup-cloud-as-tape.md).

    ![Zásady uchovávání informací](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    V tomto příkladu:

    * Zálohy jsou provedené jednou za den v 12:00 hodin a 20: 00 (dolní části obrazovky) a se uchovávají po dobu 180 dnů.
    * Zálohování v sobotu v 12:00 je zachován z důvodu 104 týdnů
    * Zálohování na poslední sobota ve 12:00 se uchovávají po dobu 60 měsíců
    * Zálohování na poslední sobota dne ve 12:00 se uchovávají po dobu 10 let
13. Klikněte na tlačítko **Další** a vyberte příslušnou možnost pro přenos počáteční kopie zálohování do Azure. Můžete zvolit **automaticky přes síť**

14. Jakmile zkontrolujete podrobnosti zásad v **Souhrn** obrazovce, klikněte na tlačítko **vytvořit skupinu** dokončení pracovního postupu. Můžete kliknout na **Zavřít** a sledovat průběh úlohy v pracovním prostoru monitorování.

    ![Vytvoření skupiny ochrany v průběhu](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Zálohování na vyžádání k databázi systému SQL Server
Zatímco v předchozích krocích vytvořili zásadu zálohování, "bodu obnovení" se vytvoří pouze v případě, že dojde k první zálohy. Nečekejte na Plánovač zvýšené kroků aktivační událost vytvoření obnovení bodu ručně.

1. Počkejte, až se zobrazí stav skupiny ochrany **OK** pro databázi před vytvořením bodu obnovení.

    ![Členové skupiny ochrany](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Klikněte pravým tlačítkem na databázi a vybrat **vytvořit bod obnovení**.

    ![Vytvořit bod obnovení Online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Zvolte **Online ochrany** v rozevírací nabídce a klepněte na **OK** spusťte vytváření bodu obnovení v Azure.

    ![Vytvoření bodu obnovení](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Zobrazit průběh úlohy v **monitorování** pracovního prostoru.

    ![Monitorování konzoly](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Obnovení z Azure do databáze SQL serveru
Následující kroky jsou nutné k obnovení chráněné entity (databáze SQL serveru) od Azure.

1. Otevřete konzolu pro správu Azure Backup serveru. Přejděte do **obnovení** pracovní prostor, kde se můžete podívat chráněných serverech. Procházejte databáze (v tomto případu ReportServer$ MSDPM2012). Vyberte **obnovení z** čas, který je zadán jako **Online** bodu.

    ![Vyberte bod obnovení](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Klikněte pravým tlačítkem na název databáze a klikněte na tlačítko **obnovit**.

    ![Obnovení z Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. MABS zobrazuje podrobnosti o bodu obnovení. Klikněte na **Další**. Chcete-li přepsat databázi, vyberte typ obnovení **obnovit na původní instanci systému SQL Server**. Klikněte na **Další**.

    ![Obnovit do původního umístění](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    V tomto příkladu MABS obnoví databáze do jiné instance systému SQL Server nebo do samostatné síťové složky.

4. V **možnosti obnovení zadejte** obrazovky, můžete vybrat možnosti obnovení jako omezení využití šířky pásma sítě k omezení šířky pásma používá obnovení. Klikněte na **Další**.

5. V **Souhrn** obrazovku, uvidíte všechny konfigurace obnovení zatím k dispozici. Klikněte na tlačítko **obnovit**.

    Zobrazí se stav obnovení databáze, Probíhá obnovení. Můžete kliknout na **zavřete** zavřete průvodce a zobrazit průběh na **monitorování** pracovního prostoru.

    ![Zahájit proces obnovení](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Po dokončení obnovení obnovené databáze je konzistentní s aplikací.

## <a name="next-steps"></a>Další kroky

Zobrazit [zálohovat soubory a aplikace](backup-mabs-files-applications-azure-stack.md) článku.
Najdete v článku [zálohování Sharepointu v Azure stacku](backup-mabs-sharepoint-azure-stack.md) článku.
