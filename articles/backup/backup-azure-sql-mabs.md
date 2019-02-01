---
title: Azure Backup pro úlohy SQL serveru pomocí Azure Backup serveru
description: Úvod k zálohování databází systému SQL Server pomocí Azure Backup serveru
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 03/24/2017
ms.author: kasinh
ms.openlocfilehash: 72b2368979f0c9e546e1c7ef7fc462bf1d64c947
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490461"
---
# <a name="back-up-sql-server-to-azure-with-azure-backup-server"></a>Zálohování SQL serveru do Azure s Azure Backup serveru
Tento článek vás provede kroky konfigurace pro zálohování databází systému SQL Server pomocí Microsoft Azure Backup Server (MABS).

Správa zálohování databáze SQL serveru do Azure a obnovení z Azure zahrnuje tři kroky:

1. Vytvoření zásady zálohování pro ochranu databáze SQL serveru do Azure.
2. Vytvoření kopie záloh na vyžádání do Azure.
3. Obnovte databázi z Azure.

## <a name="before-you-start"></a>Než začnete
Než začnete, ujistěte se, že máte [nainstalované a připravené Azure Backup serveru](backup-azure-microsoft-azure-backup.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Vytvoření zásady zálohování pro ochranu databáze SQL serveru do Azure
1. V Uživatelském rozhraní služby Azure Backup Server, klikněte **ochrany** pracovního prostoru.
2. Na pásu karet nástroje klikněte na tlačítko **nový** k vytvoření nové skupiny ochrany.

    ![Vytvořit skupinu ochrany](./media/backup-azure-backup-sql/protection-group.png)
3. MABS zobrazí úvodní obrazovka s pokyny k vytvoření **skupiny ochrany**. Klikněte na **Další**.
4. Vyberte **servery**.

    ![Vybrat typ skupiny ochrany – "servery.](./media/backup-azure-backup-sql/pg-servers.png)
5. Rozbalte počítači SQL serveru, kde jsou k dispozici databáze mají být zazálohovány. MABS ukazuje různé zdroje dat, které se dají zálohovat v tomto serveru. Rozbalte **všechny sdílené složky SQL** a výběr databází (v tomto případě jsme vybrali ReportServer$ MSDPM2012 a ReportServer$ MSDPM2012TempDB) mají být zazálohovány. Klikněte na **Další**.

    ![Vyberte SQL DB](./media/backup-azure-backup-sql/pg-databases.png)
6. Zadejte název pro skupinu ochrany a vyberte **chci online ochranu** zaškrtávací políčko.

    ![Způsob ochrany dat – disk pro krátkodobé & Online Azure](./media/backup-azure-backup-sql/pg-name.png)
7. V **zadat krátkodobé cíle** obrazovku, poskytněte nezbytné vstupy pro vytvoření body záloh na disku.

    Tady vidíme, že **rozsah uchování** je nastavena na *5 dní*, **četnost synchronizací** je nastavena na jednou každých *15 minut* tedy frekvence, ve kterém dochází k zálohování. **Expresní úplné zálohování** je nastavena na *východního*.

    ![Krátkodobé cíle](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Ve 20:00:00 (podle vstupní obrazovky) bodu zálohy vytvoří každý den přenos dat, která byla změněna z bodu zálohy 20:00:00 předchozího dne. Tento proces se nazývá **expresní úplné zálohování**. Během transakce, které protokoly se synchronizují každých 15 minut Pokud je potřeba k obnovení databáze ve 21:00:00 – a bod se vytvoří přehráním protokoly z poslední expresní úplné zálohování bod (20: 00 v tomto případě).
   >
   >

8. Klikněte na **Další**

    MABS ukazuje celkový prostor úložiště, která je k dispozici a potenciální využití místa na disku.

    ![Přidělení disku](./media/backup-azure-backup-sql/pg-storage.png)

    Ve výchozím nastavení vytvoří MABS jeden svazek na jeden zdroj dat (databáze SQL serveru), který se používá pro kopie prvotní zálohy. Tento přístup Správce logických disků (LDM) omezuje MABS ochranu zdroje 300 dat (databáze SQL serveru). Chcete-li toto omezení obejít, vyberte **společně umísťovat data do fondu úložiště DPM**, možnost. Pokud použijete tuto možnost, MABS používá na jednom svazku více zdrojů dat, což umožní MABS chránit až 2000 databází SQL.

    Pokud **automaticky rozšiřovat svazky** je vybraná možnost, MABS lze účet pro zvýšení záložní svazek při nárůstu jejich objemu produkčního prostředí. Pokud **automaticky rozšiřovat svazky** možnost není vybraná, MABS omezuje na úložiště zálohování pro zdroje dat ve skupině ochrany.
9. Správci mít přenosu tomto prvotním zálohování ručně (mimo síť) Chcete-li se vyhnout zahlcení šířky pásma nebo v síti. Mohou také nakonfigurovat čas, kdy se může stát počáteční přenos. Klikněte na **Další**.

    ![Metoda počáteční replikace](./media/backup-azure-backup-sql/pg-manual.png)

    Kopie prvotní zálohy. vyžaduje přenos celý zdroj dat (databáze SQL serveru) z provozního serveru (počítač s SQL serverem) pro MABS. Tato data můžou být velké a přenášení dat přes síť může být delší než šířka pásma. Z tohoto důvodu se mohou správci přenosu prvotní zálohování: **Ručně** (pomocí vyměnitelného média) Chcete-li se vyhnout zahlcení šířky pásma, nebo **automaticky přes síť** (v určený čas).

    Po dokončení prvotní zálohy se rest k zálohování přírůstkových záloh v kopie prvotní zálohy. Přírůstkové zálohování jsou obvykle malé a snadno přenést přes síť.
10. Zvolte, pokud chcete kontrolu konzistence pro spuštění a klikněte na tlačítko **Další**.

    ![Kontrola konzistence](./media/backup-azure-backup-sql/pg-consistent.png)

    MABS může kontrola konzistence kontrolu na integrity bodu zálohy. Vypočítá součet záložní soubor na provozním serveru (počítač s SQL serverem v tomto scénáři) a zálohovaná data pro tento soubor na MABS. V případě konfliktu předpokládá se, že soubor zálohy v MABS je poškozený. MABS rectifies zálohovaných dat odesláním bloky odpovídající neshoda kontrolního součtu. Kontrola konzistence je operace náročné na výkon, Správci mají možnost plánování kontroly konzistence nebo spuštění automaticky.
11. K určení online ochrany zdrojů dat, vyberte databázi, kterou chcete chránit v Azure a klikněte na tlačítko **Další**.

    ![Vyberte zdroje dat](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. Správci můžou plány zálohování a zásady uchovávání informací, které vyhovují jejich zásady organizace.

    ![Plán a uchovávání](./media/backup-azure-backup-sql/pg-schedule.png)

    V tomto příkladu zálohy jsou prováděny jednou za den v 12:00 hodin a 20: 00 (dolní části obrazovky)

    > [!NOTE]
    > Je vhodné mít několik krátkodobé body obnovení na disku pro rychlé obnovení. Tyto body obnovení jsou používány pro "obnovení". Azure slouží jako dobrý mimo pracoviště s vyšší smlouvy o úrovni služeb a zaručena dostupnost.
    >
    >

    **Osvědčený postup**: Ujistěte se, že se po dokončení zálohování místní disk pomocí aplikace DPM naplánované zálohy služby Azure. Díky tomu nejnovější zálohu disku, které se mají zkopírovat do Azure.

13. Zvolte plán zásad uchovávání informací. Podrobnosti o tom, jak funguje zásady uchovávání informací jsou k dispozici v [použití Azure Backup k nahrazení páskové infrastruktury článku](backup-azure-backup-cloud-as-tape.md).

    ![Zásady uchovávání informací](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    V tomto příkladu:

    * Zálohy jsou provedené jednou za den v 12:00 hodin a 20: 00 (dolní části obrazovky) a se uchovávají po dobu 180 dnů.
    * Zálohování v sobotu v 12:00 je zachován z důvodu 104 týdnů
    * Zálohování na poslední sobota ve 12:00 se uchovávají po dobu 60 měsíců
    * Zálohování na poslední sobota dne ve 12:00 se uchovávají po dobu 10 let
14. Klikněte na tlačítko **Další** a vyberte příslušnou možnost pro přenos počáteční kopie zálohování do Azure. Můžete zvolit **automaticky přes síť** nebo **Offline zálohování**.

    * **Automaticky přes síť** přenosu zálohovaných dat do Azure podle plánu, který jste zvolili pro zálohování.
    * Jak **Offline zálohování** funguje je vysvětleno na [pracovní postup Offline zálohování ve službě Azure Backup](backup-azure-backup-import-export.md).

    Zvolit mechanismus relevantní přenos do Azure a kliknutím na tlačítko odeslat kopie prvotní zálohy **Další**.
15. Jakmile zkontrolujete podrobnosti zásad v **Souhrn** obrazovce, klikněte na **vytvořit skupinu** tlačítko pro dokončení pracovního postupu. Můžete kliknout **Zavřít** tlačítko a sledovat průběh úlohy v pracovním prostoru monitorování.

    ![Vytvoření skupiny ochrany v průběhu](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Zálohování na vyžádání k databázi systému SQL Server
Zatímco v předchozích krocích vytvořili zásadu zálohování, "bodu obnovení" se vytvoří pouze v případě, že dojde k první zálohy. Nečekejte na Plánovač zvýšené kroků aktivační událost vytvoření obnovení bodu ručně.

1. Počkejte, až se zobrazí stav skupiny ochrany **OK** pro databázi před vytvořením bodu obnovení.

    ![Členové skupiny ochrany](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Klikněte pravým tlačítkem na databázi a vybrat **vytvořit bod obnovení**.

    ![Vytvořit bod obnovení Online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Zvolte **Online ochrany** v rozevírací nabídce a klepněte na **OK**. Spustí se vytvoření bodu obnovení v Azure.

    ![Vytvoření bodu obnovení](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Můžete zobrazit průběh úlohy v **monitorování** pracovní prostor, kde zjistíte v průběhu úlohy, jako je znázorněn v následujícím obrázku.

    ![Monitorování konzoly](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Obnovení z Azure do databáze SQL serveru
Následující kroky jsou nutné k obnovení chráněné entity (databáze SQL serveru) od Azure.

1. Otevřete konzolu pro správu serveru aplikace DPM. Přejděte do **obnovení** pracovní prostor, kde můžete sledovat servery zálohovat pomocí DPM. Procházejte databáze (v tomto případu ReportServer$ MSDPM2012). Vyberte **obnovení z** čas, který končí **Online**.

    ![Vyberte bod obnovení](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Klikněte pravým tlačítkem na název databáze a klikněte na tlačítko **obnovit**.

    ![Obnovení z Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. Aplikace DPM zobrazuje podrobnosti o bodu obnovení. Klikněte na **Další**. Chcete-li přepsat databázi, vyberte typ obnovení **obnovit na původní instanci systému SQL Server**. Klikněte na **Další**.

    ![Obnovit do původního umístění](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    V tomto příkladu aplikace DPM umožňuje obnovení databáze do jiné instance systému SQL Server nebo do samostatné síťové složky.
4. V **možnosti obnovení zadejte** obrazovky, můžete vybrat možnosti obnovení jako omezení využití šířky pásma sítě k omezení šířky pásma používá obnovení. Klikněte na **Další**.
5. V **Souhrn** obrazovku, uvidíte všechny konfigurace obnovení zatím k dispozici. Klikněte na tlačítko **obnovit**.

    Zobrazí se stav obnovení databáze, Probíhá obnovení. Můžete kliknout na **zavřete** zavřete průvodce a zobrazit průběh na **monitorování** pracovního prostoru.

    ![Zahájit proces obnovení](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Po dokončení obnovení obnovené databáze je konzistentní s aplikací.

### <a name="next-steps"></a>Další kroky:
• [Azure Backup – nejčastější dotazy](backup-azure-backup-faq.md)
