---
title: Zálohování serveru Exchange pomocí Azure Backup Server
description: Zjistěte, jak zálohovat exchange server do Azure Backup pomocí Azure Backup Server
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 1d7d28d813df82a5e1ea0fe424bba2ef5a9a2684
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421335"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Zálohování serveru Exchange do Azure pomocí Azure Backup Server

Tento článek popisuje, jak nakonfigurovat Microsoft Azure Backup Server (MABS) zálohovat server Microsoft Exchange do Azure.  

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat, ujistěte se, že je server Azure Backup Server [nainstalovaný a připravený](backup-azure-microsoft-azure-backup.md).

## <a name="mabs-protection-agent"></a>Ochranné činidlo MABS

Chcete-li nainstalovat agenta ochrany MABS na server Exchange, postupujte takto:

1. Zkontrolujte, zda jsou brány firewall správně nakonfigurovány. Viz [Konfigurace výjimek brány firewall pro agenta](https://docs.microsoft.com/system-center/dpm/configure-firewall-settings-for-dpm?view=sc-dpm-2019).
2. Nainstalujte agenta na server Exchange klepnutím na **položku Agenti > > agenti správy v** konzole správce MABS. Podrobné kroky naleznete [v tématu Instalace agenta ochrany MABS.](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019)

## <a name="create-a-protection-group-for-the-exchange-server"></a>Vytvoření skupiny ochrany pro server Exchange

1. V konzole správce MABS klepněte na tlačítko **Ochrana**a potom klepnutím na tlačítko **Nový** na pásu karet nástrojů otevřete průvodce **vytvořit novou skupinu ochrany.**
2. Na **úvodní** obrazovce průvodce klepněte na tlačítko **Další**.
3. Na obrazovce **Vybrat typ skupiny ochrany** vyberte **položku Servery** a klepněte na tlačítko **Další**.
4. Vyberte databázi serveru Exchange, kterou chcete chránit, a klepněte na tlačítko **Další**.

   > [!NOTE]
   > Pokud chráníte Exchange 2013, zkontrolujte [požadavky Exchange 2013](https://docs.microsoft.com/system-center/dpm/back-up-exchange?view=sc-dpm-2016).
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

    Po výběru této možnosti bude kontrola konzistence zálohování spuštěna na MABS, aby se zabránilo vstupně-vaním provozu, který je generován spuštěním příkazu **eseutil** na serveru Exchange.

   > [!NOTE]
   > Chcete-li použít tuto možnost, musíte zkopírovat soubory Ese.dll a Eseutil.exe do adresáře C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin na serveru MABS. V opačném případě se spustí následující chyba:  
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
12. Vyberte čas, kdy server MABS vytvoří počáteční replikaci, a klepněte na tlačítko **Další**.
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

1. Chcete-li obnovit databázi serveru Exchange, klepněte na tlačítko **Obnovení** v konzole správce MABS.
2. Vyhledejte databázi Serveru Exchange, kterou chcete obnovit.
3. V rozevíracím seznamu *doba obnovení vyberte* online bod obnovení.
4. Klepnutím na **tlačítko Obnovit** spusťte **Průvodce obnovením**.

Pro online body obnovení existuje pět typů obnovení:

* **Recover do původního umístění serveru Exchange Server:** Data budou obnovena na původní server Exchange.
* **Recover do jiné databáze na serveru Exchange:** Data budou obnovena do jiné databáze na jiném serveru Exchange.
* **Obnovit do databáze obnovení:** Data budou obnovena do databáze obnovení serveru Exchange (RDB).
* **Kopírování do síťové složky:** Data budou obnovena do síťové složky.
* **Kopírovat na pásku:** Pokud máte páskovou knihovnu nebo samostatnou páskovou jednotku připojenou a nakonfigurovanou na MABS, bod obnovení se zkopíruje na volnou pásku.

    ![Volba online replikace](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Další kroky

* [Nejčastější dotazy k zálohování v Azure](backup-azure-backup-faq.md)
