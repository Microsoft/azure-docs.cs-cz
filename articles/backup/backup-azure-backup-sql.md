---
title: Zálohování SQL Server do Azure jako úlohy DPM
description: Úvod k zálohování databází SQL Server pomocí služby Azure Backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: ea55081d6f3b58c6c64c16e64c7a9d0f673ec196
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025397"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Zálohování SQL Server do Azure jako úlohy DPM

Tento článek vás provede kroky konfigurace pro zálohování SQL Server databází pomocí Azure Backup.

K zálohování databází SQL Server do Azure potřebujete účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v tématu [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

Správa zálohování SQL Server databáze do Azure a obnovení z Azure zahrnuje tři kroky:

1. Vytvořte zásady zálohování pro ochranu SQL Serverch databází do Azure.
2. Vytváření záložních kopií na vyžádání v Azure.
3. Obnovte databázi z Azure.

## <a name="before-you-start"></a>Než začnete

Než začnete, ujistěte se, že byly splněny všechny [požadavky](backup-azure-dpm-introduction.md#prerequisites-and-limitations) pro použití Microsoft Azure Backup k ochraně úloh. Požadavky zahrnují úlohy, jako například: vytvoření trezoru služby Backup, stažení přihlašovacích údajů trezoru, instalace agenta Azure Backup a registrace serveru v trezoru.

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Vytvoření zásady zálohování pro ochranu SQL Serverch databází do Azure

1. Na serveru DPM klikněte na pracovní prostor **ochrana** .
2. Na pásu karet nástroje klikněte na **Nový** a vytvořte novou skupinu ochrany.

    ![Vytvořit skupinu ochrany](./media/backup-azure-backup-sql/protection-group.png)
3. Aplikace DPM zobrazí úvodní obrazovku s pokyny k vytvoření **skupiny ochrany**. Klikněte na **Další**.
4. Vyberte **servery**.

    ![Vybrat typ skupiny ochrany – servery](./media/backup-azure-backup-sql/pg-servers.png)
5. Rozbalte SQL Server počítač, ve kterém se nacházejí databáze, které mají být zálohovány. DPM zobrazuje různé zdroje dat, které je možné zálohovat z tohoto serveru. Rozbalte **všechny sdílené složky SQL** a vyberte databáze (v tomto případě jsme vybrali možnost REPORTSERVER $ MSDPM2012 a ReportServer $ MSDPM2012TempDB), aby se zálohoval. Klikněte na **Další**.

    ![Výběr databáze SQL](./media/backup-azure-backup-sql/pg-databases.png)
6. Zadejte název skupiny ochrany a zaškrtněte políčko **Chci online ochranu** .

    ![Metoda ochrany dat – krátkodobý & disku online Azure](./media/backup-azure-backup-sql/pg-name.png)
7. Na obrazovce **zadat krátkodobé cíle** uveďte nezbytné vstupy pro vytváření záložních bodů na disku.

    Tady vidíte, že **Rozsah uchování** je nastavený na *5 dní*, **Frekvence synchronizace** je nastavená na každých *15 minut*, což je frekvence, při které se provádí zálohování. **Expresní úplné zálohování** je nastaveno na *8:00 P. M*.

    ![Krátkodobé cíle](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > V 8:00. odp. (podle vstupu obrazovky) se bod zálohování vytvoří každý den tak, že se přenese data, která byla upravena z bodu zálohování v předchozím dnu v 8:00 odp. Tento proces se nazývá **expresní úplné zálohování**. I když jsou protokoly transakcí synchronizovány každých 15 minut, je-li nutné obnovit databázi v 9:00. odp., pak je bod vytvořen přehráním protokolů z posledního expresního bodu úplného zálohování (8pm v tomto případě).
   >
   >

8. Klikněte na **Další**

    DPM zobrazuje celkové dostupné místo v úložišti a možné využití místa na disku.

    ![Přidělení disku](./media/backup-azure-backup-sql/pg-storage.png)

    Ve výchozím nastavení aplikace DPM vytvoří jeden svazek na zdroj dat (SQL Server databázi), který se používá pro prvotní záložní kopii. Pomocí tohoto přístupu omezuje Správce logických disků (LDM) ochranu DPM na 300 zdrojů dat (SQL Server databází). Toto omezení můžete obejít tak, že vyberete možnost **společně najít data ve fondu úložiště DPM**, možnost. Použijete-li tuto možnost, aplikace DPM použije jeden svazek pro více zdrojů dat, což umožňuje aplikaci DPM chránit až 2000 databází SQL.

    Pokud je vybraná možnost **automaticky zvětšit svazky** , může aplikace DPM při zvětšování produkčních dat brát v úvahu zvýšený svazek zálohy. Pokud není vybraná možnost **automaticky zvětšit svazky** , aplikace DPM omezí úložiště zálohování používané pro zdroje dat ve skupině ochrany.
9. Správcům je dána volba pro přenos tohoto počátečního zálohování ručně (vypnutá síť), aby se předešlo zahlcení šířky pásma nebo přes síť. Můžou taky nakonfigurovat čas, kdy se má počáteční přenos provést. Klikněte na **Další**.

    ![Metoda počáteční replikace](./media/backup-azure-backup-sql/pg-manual.png)

    Prvotní záložní kopie vyžaduje přenos celého zdroje dat (SQL Server databáze) z provozního serveru (SQL Server počítače) na server DPM. Tato data můžou být velká a přenos dat přes síť by mohl překročit šířku pásma. Z tohoto důvodu můžou správci přenést počáteční zálohu **ručně** (pomocí vyměnitelného média), aby se předešlo zahlcení šířky pásma nebo **automaticky přes síť** (v určitou dobu).

    Po dokončení prvotního zálohování jsou zbývající zálohy přírůstkové zálohy v prvotní záložní kopii. Přírůstkové zálohování je obvykle malé a snadno se přenáší přes síť.
10. Vyberte, kdy chcete spustit kontrolu konzistence, a klikněte na tlačítko **Další**.

    ![Kontrola konzistence](./media/backup-azure-backup-sql/pg-consistent.png)

    DPM může provést kontrolu konzistence a ověřit integritu bodu zálohování. Vypočítá kontrolní součet záložního souboru na provozním serveru (SQL Server počítač v tomto scénáři) a zálohovaná data pro tento soubor v aplikaci DPM. V případě konfliktu se předpokládá, že zálohovaný soubor v aplikaci DPM je poškozený. DPM odpraví zálohovaná data odesláním bloků odpovídajících neshodě kontrolního součtu. Vzhledem k tomu, že je kontrola konzistence náročná na výkon, mají správci možnost naplánovat kontrolu konzistence nebo ji spustit automaticky.
11. Chcete-li určit online ochranu zdrojů dat, vyberte databáze, které chcete chránit pomocí Azure a klikněte na tlačítko **Další**.

    ![Vybrat zdroje dat](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. Správci můžou zvolit plány zálohování a zásady uchovávání informací, které odpovídají zásadám organizace.

    ![Plán a uchovávání](./media/backup-azure-backup-sql/pg-schedule.png)

    V tomto příkladu se zálohy odebírají jednou denně v 12:00 PM a 8 ODP. (dolní část obrazovky)

    > [!NOTE]
    > Je dobrým zvykem, aby na disku bylo několik krátkodobých bodů obnovení pro rychlé obnovení. Tyto body obnovení se používají pro "provozní obnovení". Azure slouží jako dobré umístění mimo pracoviště s vyšší Slaou a zaručenou dostupností.
    >
    >

    **Osvědčený postup**: Ujistěte se, že jsou zálohy Azure naplánované po dokončení zálohování místních disků pomocí DPM. To umožňuje zkopírovat nejnovější zálohu disku do Azure.

13. Vyberte plán zásad uchovávání informací. Podrobnosti o tom, jak fungují zásady uchovávání informací, najdete v [článku použití Azure Backup k nahrazení vaší páskové infrastruktury](backup-azure-backup-cloud-as-tape.md).

    ![Zásady uchovávání informací](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    V tomto příkladu:

    * Zálohy se ponechají jednou denně v 12:00 PM a 8 PM (dolní část obrazovky) a uchovávají se po dobu 180 dnů.
    * Záloha v sobotu v 12:00. odp. je uchováno po dobu 104 týdnů
    * Záloha na poslední sobotu v 12:00. odp. je uchováno po dobu 60 měsíců
    * Záloha na poslední sobotu v březnu v 12:00. odp. je uchováno po dobu 10 let
14. Klikněte na **Další** a vyberte odpovídající možnost pro přenos prvotní záložní kopie do Azure. Můžete vybrat možnost **automaticky prostřednictvím sítě** nebo **offline zálohování**.

    * **Automaticky přes síť** přenáší zálohovaná data do Azure podle plánu vybraného pro zálohování.
    * Způsob, jakým funguje **offline zálohování** , je vysvětleno v tématu [Přehled zálohování offline](offline-backup-overview.md).

    Vyberte příslušný přenosový mechanismus pro odeslání prvotní záložní kopie do Azure a klikněte na **Další**.
15. Po kontrole podrobností zásad na obrazovce **souhrnu** dokončete pracovní postup kliknutím na tlačítko **vytvořit skupinu** . Můžete kliknout na tlačítko **Zavřít** a monitorovat průběh úlohy v pracovním prostoru monitorování.

    ![Probíhá vytváření skupiny ochrany.](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Zálohování databáze SQL Server na vyžádání

Zatímco předchozí kroky vytvořily zásady zálohování, vytvoří se bod obnovení jenom v případě, že dojde k prvnímu zálohování. Místo čekání na vystavení plánovače pak níže uvedené kroky aktivují vytvoření bodu obnovení ručně.

1. Před vytvořením bodu obnovení počkejte, až se stav skupiny ochrany zobrazí v poli **OK** pro databázi.

    ![Členové skupiny ochrany](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Klikněte pravým tlačítkem na databázi a vyberte **vytvořit bod obnovení**.

    ![Vytvořit bod obnovení online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. V rozevírací nabídce vyberte možnost **online ochrana** a klikněte na tlačítko **OK**. Tím se spustí vytvoření bodu obnovení v Azure.

    ![Vytvořit bod obnovení](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Průběh úlohy si můžete prohlédnout v pracovním prostoru **monitorování** , ve kterém se nachází probíhající úloha, jako je ta, která je znázorněna na následujícím obrázku.

    ![Konzola monitorování](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Obnovení databáze SQL Server z Azure

K obnovení chráněné entity (SQL Server databáze) z Azure se vyžadují následující kroky.

1. Otevřete konzolu pro správu serveru DPM. Přejděte do pracovního prostoru **obnovení** , kde vidíte servery zálohované aplikací DPM. Vyhledejte požadovanou databázi (v tomto případě ReportServer $ MSDPM2012). Vyberte **obnovení z** času, který končí na **online**.

    ![Vybrat bod obnovení](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Klikněte pravým tlačítkem myši na název databáze a pak klikněte na tlačítko **obnovit**.

    ![Obnovení z Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. DPM zobrazuje podrobnosti bodu obnovení. Klikněte na **Další**. Chcete-li přepsat databázi, vyberte typ obnovení **obnovit do původní instance SQL Server**. Klikněte na **Další**.

    ![Obnovit do původního umístění](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    V tomto příkladu aplikace DPM umožňuje obnovení databáze do jiné instance SQL Server nebo do samostatné síťové složky.
4. Na obrazovce **zadat možnosti obnovení** můžete vybrat možnosti obnovení, jako je omezování využití šířky pásma sítě, a omezit tak šířku pásma použitou obnovením. Klikněte na **Další**.
5. Na obrazovce **Souhrn** uvidíte všechny konfigurace obnovení, které jsou doposud k dispozici. Klikněte na tlačítko **obnovit**.

    Stav obnovení ukazuje obnovenou databázi. Kliknutím na **Zavřít** můžete Průvodce zavřít a zobrazit průběh v pracovním prostoru **monitorování** .

    ![Zahájit proces obnovení](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Po dokončení obnovení je obnovená databáze konzistentní vzhledem k aplikacím.

## <a name="next-steps"></a>Další kroky

* [Nejčastější dotazy k Azure Backup](backup-azure-backup-faq.md)
