---
title: Zálohování serveru Exchange pomocí aplikace System Center DPM
description: Zjistěte, jak zálohovat exchange server do Azure Backup pomocí System Center 2012 R2 DPM
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 389713767409ff49c6fc83a4d6e8dc3c87272fc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614374"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Zálohování serveru Exchange do služby Azure Backup pomocí nástroje System Center 2012 R2 DPM

Tento článek popisuje, jak nakonfigurovat server System Center 2012 R2 Data Protection Manager (DPM) pro zálohování serveru Microsoft Exchange do služby Azure Backup.  

## <a name="updates"></a>Aktualizace

Chcete-li úspěšně zaregistrovat server DPM pomocí služby Azure Backup, je nutné nainstalovat nejnovější kumulativní aktualizaci pro system center 2012 R2 DPM a nejnovější verzi agenta zálohování Azure. Získejte nejnovější kumulativní aktualizaci z [katalogu společnosti Microsoft](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> Příklady v tomto článku verze 2.0.8719.0 agenta zálohování Azure je nainstalována a kumulativní aktualizace 6 je nainstalována na System Center 2012 R2 DPM.
>
>

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat, ujistěte se, že byly splněny všechny [předpoklady](backup-azure-dpm-introduction.md#prerequisites-and-limitations) pro použití microsoft azure backupu k ochraně úloh. Mezi tyto požadavky patří následující:

* Byl vytvořen trezor zálohování na webu Azure.
* Pověření agenta a trezoru byla stažena na server DPM.
* Agent je nainstalován na serveru DPM.
* Pověření trezoru byla použita k registraci serveru DPM.
* Pokud chráníte Exchange 2016, upgradujte na DPM 2012 R2 UR9 nebo novější.

## <a name="dpm-protection-agent"></a>Agent ochrany DPM

Chcete-li nainstalovat agenta ochrany DPM na server Exchange, postupujte takto:

1. Zkontrolujte, zda jsou brány firewall správně nakonfigurovány. Viz [Konfigurace výjimek brány firewall pro agenta](https://docs.microsoft.com/system-center/dpm/configure-firewall-settings-for-dpm?view=sc-dpm-2019).
2. Nainstalujte agenta na server Exchange klepnutím na **položku Agenti > správy > Nainstalovat** v konzole pro správu aplikace DPM. Podrobné kroky [najdete v tématu Instalace agenta ochrany DPM.](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019)

## <a name="create-a-protection-group-for-the-exchange-server"></a>Vytvoření skupiny ochrany pro server Exchange

1. V konzole správce aplikace DPM klepněte na tlačítko **Ochrana**a potom klepnutím na tlačítko **Nový** na pásu karet nástrojů otevřete průvodce **vytvořit novou skupinu ochrany.**
2. Na **úvodní** obrazovce průvodce klepněte na tlačítko **Další**.
3. Na obrazovce **Vybrat typ skupiny ochrany** vyberte **položku Servery** a klepněte na tlačítko **Další**.
4. Vyberte databázi serveru Exchange, kterou chcete chránit, a klepněte na tlačítko **Další**.

   > [!NOTE]
   > Pokud chráníte Exchange 2013, zkontrolujte [požadavky Exchange 2013](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/dn751029(v=sc.12)).
   >
   >

    V následujícím příkladu je vybrána databáze Exchange 2010.

    ![Výběr členů skupiny](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Vyberte metodu ochrany dat.

    Pojmenujte skupinu ochrany a vyberte obě následující možnosti:

   * Chci krátkodobou ochranu pomocí disku.
   * Chci online ochranu.
6. Klikněte na **Další**.
7. Pokud chcete zkontrolovat integritu databází serveru Exchange Server, vyberte možnost **Spustit Eseutil a zkontrolujte integritu dat.**

    Po výběru této možnosti bude kontrola konzistence zálohování spuštěna na serveru DPM, aby se zabránilo vstupně-no provozu, který je generován spuštěním příkazu **eseutil** na serveru Exchange.

   > [!NOTE]
   > Chcete-li použít tuto možnost, musíte zkopírovat soubory Ese.dll a Eseutil.exe do adresáře C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin na serveru DPM. V opačném případě se spustí následující chyba:  
   > ![chyba eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Klikněte na **Další**.
9. Vyberte databázi pro **kopírovat zálohu**a klepněte na tlačítko **Další**.

   > [!NOTE]
   > Pokud nevyberete "Úplné zálohování" pro alespoň jednu kopii DAG databáze, protokoly nebudou zkráceny.
   >
   >
10. Nakonfigurujte cíle pro **krátkodobé zálohování**a klepněte na tlačítko **Další**.
11. Zkontrolujte dostupné místo na disku a klepněte na tlačítko **Další**.
12. Vyberte čas, kdy server DPM vytvoří počáteční replikaci, a klepněte na tlačítko **Další**.
13. Vyberte možnosti kontroly konzistence a klepněte na tlačítko **Další**.
14. Vyberte databázi, kterou chcete zálohovat do Azure, a klikněte na **Další**. Například:

    ![Určení dat ochrany online](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Definujte plán **pro zálohování Azure**a klikněte na **další**. Například:

    ![Určení plánu zálohování online](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Poznámka: Online body obnovení jsou založeny na expresní úplné body obnovení. Proto je nutné naplánovat bod obnovení online po čase, který je určen pro expresní úplný bod obnovení.
    >
    >
16. Nakonfigurujte zásady uchovávání informací pro **azure backup**a potom klepněte na tlačítko **Další**.
17. Zvolte možnost replikace online a klepněte na tlačítko **Další**.

    Pokud máte velkou databázi, může trvat dlouhou dobu pro počáteční zálohování, které mají být vytvořeny v síti. Chcete-li se tomuto problému vyhnout, můžete vytvořit zálohu offline.  

    ![Určení zásad uchovávání informací online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Potvrďte nastavení a klepněte na tlačítko **Vytvořit skupinu**.
19. Klikněte na **Zavřít**.

## <a name="recover-the-exchange-database"></a>Obnovení databáze Exchange

1. Chcete-li obnovit databázi serveru Exchange, klepněte na tlačítko **Obnovení** v konzole správce aplikace DPM.
2. Vyhledejte databázi Serveru Exchange, kterou chcete obnovit.
3. V rozevíracím seznamu *doba obnovení vyberte* online bod obnovení.
4. Klepnutím na **tlačítko Obnovit** spusťte **Průvodce obnovením**.

Pro online body obnovení existuje pět typů obnovení:

* **Recover do původního umístění serveru Exchange Server:** Data budou obnovena na původní server Exchange.
* **Recover do jiné databáze na serveru Exchange:** Data budou obnovena do jiné databáze na jiném serveru Exchange.
* **Obnovit do databáze obnovení:** Data budou obnovena do databáze obnovení serveru Exchange (RDB).
* **Kopírování do síťové složky:** Data budou obnovena do síťové složky.
* **Kopírovat na pásku:** Pokud máte na serveru DPM připojenou páskovou knihovnu nebo samostatnou páskovou jednotku, bod obnovení se zkopíruje na volnou pásku.

    ![Volba online replikace](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Další kroky

* [Nejčastější dotazy k zálohování v Azure](backup-azure-backup-faq.md)
