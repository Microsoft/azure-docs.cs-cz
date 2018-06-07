---
title: Zálohování Azure pro úlohy SQL serveru pomocí serveru Azure Backup
description: Úvod k zálohování databáze SQL serveru pomocí serveru Azure Backup
services: backup
author: pvrk
manager: Shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 03/24/2017
ms.author: pullabhk
ms.openlocfilehash: 5d0fc66ff81672116d3d98ee9970456515ab3c9e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606388"
---
# <a name="back-up-sql-server-to-azure-with-azure-backup-server"></a>Zálohování serveru SQL Server Azure s Azure Backup
Tento článek vás provede kroky konfigurace pro zálohování databáze SQL serveru pomocí Microsoft Azure Backup Server (MABS).

Správa zálohování databáze serveru SQL Server do Azure a obnovení z Azure zahrnuje tři kroky:

1. Vytvořte zásady zálohování pro ochranu databází systému SQL Server do Azure.
2. Vytvořte záložní kopie na vyžádání do Azure.
3. Obnovte databázi z Azure.

## <a name="before-you-start"></a>Než začnete
Než začnete, ujistěte se, že máte [nainstalované a připravené serveru Azure Backup](backup-azure-microsoft-azure-backup.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Vytvořit zásady zálohování pro ochranu databází systému SQL Server do Azure
1. V Uživatelském rozhraní Azure Backup Server, klikněte **ochrany** pracovního prostoru.
2. Na pásu karet nástroje klikněte na tlačítko **nový** k vytvoření nové skupiny ochrany.

    ![Vytvořit skupinu ochrany](./media/backup-azure-backup-sql/protection-group.png)
3. MABS zobrazí na úvodní obrazovce s pokyny k vytváření **skupiny ochrany**. Klikněte na **Další**.
4. Vyberte **servery**.

    ![Vybrat typ skupiny ochrany – 'servery.](./media/backup-azure-backup-sql/pg-servers.png)
5. Rozbalte počítači serveru SQL Server, kde nejsou databáze k zálohování. MABS zobrazí různé zdroje dat, které lze zálohovat z tohoto serveru. Rozbalte **všechny sdílené složky SQL** a vyberte k zálohování databáze (v tomto případě jsme vybrali ReportServer$ MSDPM2012 a ReportServer$ MSDPM2012TempDB). Klikněte na **Další**.

    ![Vyberte databázi SQL](./media/backup-azure-backup-sql/pg-databases.png)
6. Zadejte název pro skupinu ochrany a vyberte **chci online ochranu** zaškrtávací políčko.

    ![Způsob ochrany dat – krátkodobou disku & Online Azure](./media/backup-azure-backup-sql/pg-name.png)
7. V **zadat krátkodobé cíle** obrazovky, zahrnují nezbytné vstupy pro vytvoření body zálohy na disk.

    Tady vidíte, **rozsah uchování** je nastaven na *5 dní*, **četnost synchronizace** je nastavena na jednou každých *15 minut* tedy frekvenci, kdy se provede zálohování. **Expresní úplná záloha** je nastaven na *východního*.

    ![Krátkodobé cíle](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > V 8:00 PM (podle vstup obrazovky) je bod zálohy vytvořené přenosu dat, která byla změněna z bodu zálohy 8:00 PM předchozí den každý den. Tento proces se nazývá **expresní úplné zálohování**. Při transakce, které protokoly se synchronizují každých 15 minut Pokud je třeba obnovit databázi na 21:00:00 – bod je vytvořen pomocí přehrání protokoly z poslední expresní úplné zálohování bod (20: 00 v tomto případě).
   >
   >

8. Klikněte na **Další**

    MABS zobrazuje celkového prostoru úložiště, která je k dispozici a potenciální využití místa na disku.

    ![Přidělení disku](./media/backup-azure-backup-sql/pg-storage.png)

    Ve výchozím nastavení vytvoří MABS jeden svazek na zdroj dat (databázi systému SQL Server), který se používá pro kopie prvotní zálohy. Tento přístup Správce logických disků (LDM) omezuje MABS ochranu zdroje dat 300 (databáze systému SQL Server). Chcete-li toto omezení obejít, vyberte **společně umísťovat data do fondu úložiště DPM**, možnost. Pokud použijete tuto možnost, MABS používá na jednom svazku více zdrojů dat, která umožňuje MABS chránit až 2 000 databází SQL.

    Pokud **automaticky rozšiřovat svazky** je vybraná možnost, MABS můžete účet vyšší zálohování svazku s růstem provozními daty. Pokud **automaticky rozšiřovat svazky** možnost není vybraná, MABS omezuje zálohování úložiště používá ke zdrojům dat ve skupině ochrany.
9. Správci mají možnost vyhnout zahlcení šířky pásma přenosu tuto prvotní zálohování ručně (vypnuto síť) nebo přes síť. Mohou také nakonfigurovat čas, kdy může dojít počáteční přenos. Klikněte na **Další**.

    ![Metodu počáteční replikace](./media/backup-azure-backup-sql/pg-manual.png)

    Kopie prvotní zálohy vyžaduje přenos celého datového zdroje (databázi systému SQL Server) z provozního serveru (počítači serveru SQL Server) do MABS. Tato data mohou být velký a přenos dat přes síť může překročit šířky pásma. Z tohoto důvodu se mohou správci k přenosu prvotní zálohování: **ručně** (pomocí vyměnitelného média) předejdete zahlcení šířky pásma, nebo **automaticky prostřednictvím sítě** (v určený čas).

    Po dokončení prvotní zálohování zbývající zálohy jsou přírůstkových záloh v kopie prvotní zálohy. Přírůstkové zálohování jsou obvykle malé a snadno přenést přes síť.
10. Vyberte, pokud chcete kontrolu konzistence, spusťte a klikněte na tlačítko **Další**.

    ![Kontrola konzistence](./media/backup-azure-backup-sql/pg-consistent.png)

    MABS může kontrola konzistence kontroly k integritu bodu zálohy. Vypočte kontrolního součtu záložní soubor na provozním serveru (počítači serveru SQL Server v tomto scénáři) a zálohovaná data pro tento soubor na MABS. V případě konfliktu se předpokládá, že soubor zálohy v MABS je poškozený. MABS rectifies zálohovaná data odesláním bloky odpovídající neshoda kontrolního součtu. Kontrola konzistence je operace náročné na výkon, mají správci možnost plánovaná kontrola konzistence nebo spuštěním automaticky.
11. K určení online ochrany zdroje dat, vyberte databáze chránit do služby Azure a klikněte na tlačítko **Další**.

    ![Vyberte zdroje dat](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. Správci mohou plánů zálohování a zásady uchovávání informací, která vyhovuje své zásady organizace.

    ![Plán a jejich uchovávání](./media/backup-azure-backup-sql/pg-schedule.png)

    V tomto příkladu jsou provedeny zálohy jednou za den v 12:00 PM a 20: 00 (dolní části obrazovky)

    > [!NOTE]
    > Je vhodné mít několik krátkodobé body obnovení na disku pro rychlé obnovení. Tyto body obnovení se používají pro "provozní obnovení". Azure slouží jako dobrý mimo pracoviště s vyšší SLA a záruku dostupnosti.
    >
    >

    **Nejvhodnější**: Ujistěte se, že zálohy Azure jsou naplánovány po dokončení zálohování místního disku pomocí aplikace DPM. To umožňuje nejnovější zálohu disku zkopírovány do Azure.

13. Zvolte plán zásad uchovávání informací. Podrobnosti o způsobu fungování zásady uchovávání informací jsou k dispozici v [pomocí Azure Backup k nahrazení váš článek infrastruktury pásky](backup-azure-backup-cloud-as-tape.md).

    ![Zásady uchovávání informací](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    V tomto příkladu:

    * Zálohování se pořídí jednou denně na 12:00 PM a 20: 00 (dolní části obrazovky) a jsou uchovány na 180 dní.
    * Zálohování na sobotu ve 12:00 se zachovává kvůli 104 týdny
    * Zálohování na poslední sobotu ve 12:00 se uchovávají po dobu 60 měsíců
    * Zálohování na poslední sobotu dne ve 12:00 se uchovávají 10 let.
14. Klikněte na tlačítko **Další** a vyberte příslušnou možnost pro přenesení kopie prvotní zálohy do Azure. Můžete zvolit **automaticky přes síť** nebo **Offline zálohování**.

    * **Automaticky prostřednictvím sítě** přenosy dat zálohování do Azure podle plánu zvolené pro zálohování.
    * Jak **Offline zálohování** funguje je vysvětleno v [pracovní postup Offline zálohování v Azure Backup](backup-azure-backup-import-export.md).

    Vyberte relevantní přenos mechanismus odeslat kopie prvotní zálohy do Azure a klikněte na tlačítko **Další**.
15. Po prostudování podrobnosti zásady v **Souhrn** obrazovky, klikněte na **vytvořit skupinu** tlačítko dokončení pracovního postupu. Můžete kliknout na **Zavřít** tlačítko a sledovat průběh úlohy v pracovním prostoru monitorování.

    ![Vytvoření skupiny ochrany v průběhu](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Zálohování na vyžádání databáze systému SQL Server
Když v předchozím postupu vytvořili zásadu zálohování, "bodu obnovení" se vytvoří jenom v případě, že dochází k první zálohování. Místo čekání plánovače na nové, kroků aktivační událost vytvoření obnovení bodu ručně.

1. Počkejte, až se zobrazí stav skupiny ochrany **OK** pro databázi před vytvořením bodu obnovení.

    ![Členové skupiny ochrany](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Klikněte pravým tlačítkem na databázi a vyberte **vytvořit bod obnovení**.

    ![Vytvořit bod obnovení Online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Zvolte **Online ochrany** rozevírací nabídky a klikněte na **OK**. Vytvoření bodu obnovení se spustí v Azure.

    ![Vytvoření bodu obnovení](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Můžete zobrazit průběh úlohy v **monitorování** prostoru, kde najdete v průběhu úlohy, jako je ta, které popsané v následující obrázek.

    ![Konzola monitorování](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Obnovení databáze systému SQL Server z Azure
Následující kroky jsou potřebné k obnovení chráněné entity (databázi systému SQL Server) ze služby Azure.

1. Otevřete konzolu pro správu serveru aplikace DPM. Přejděte na **obnovení** prostoru, kde můžete sledovat servery zálohované aplikací DPM. Procházejte požadovaná databáze (v této rozlišují ReportServer$ MSDPM2012). Vyberte **obnovení z** čas, který končí **Online**.

    ![Vyberte bod obnovení](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Klikněte pravým tlačítkem na název databáze a klikněte na tlačítko **obnovit**.

    ![Obnovení z Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. Aplikace DPM zobrazuje podrobnosti o vytvoření bodu obnovení. Klikněte na **Další**. Chcete-li přepsat databázi, vyberte typ obnovení **obnovit na původní instanci systému SQL Server**. Klikněte na **Další**.

    ![Obnovení do původního umístění](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    V tomto příkladu aplikace DPM umožňuje obnovení databáze do jiné instance systému SQL Server nebo do samostatné síťové složky.
4. V **možnosti obnovení zadejte** obrazovce můžete vybrat možnosti obnovení jako omezení využití šířky pásma šířky pásma sítě používané při obnovení. Klikněte na **Další**.
5. V **Souhrn** obrazovky, uvidíte všechny konfigurace obnovení dosavadní zadat. Klikněte na tlačítko **obnovit**.

    Stav obnovení zobrazuje databázi obnovení. Můžete kliknout na **zavřete** zavřete průvodce a sledovat průběh v **monitorování** pracovního prostoru.

    ![Zahájení obnovení](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Po dokončení obnovení je obnovenou databázi aplikace, které jsou konzistentní.

### <a name="next-steps"></a>Další kroky:
• [Azure Backup – nejčastější dotazy](backup-azure-backup-faq.md)
