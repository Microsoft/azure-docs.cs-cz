---
title: Zálohování Exchange serveru pomocí Azure Backup Server
description: Naučte se, jak zálohovat Exchange Server pro Azure Backup pomocí Azure Backup Server
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 2e3e89d93ab35724a09138d48863badad6a41b71
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86514114"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Zálohování serveru Exchange do Azure pomocí Azure Backup Server

Tento článek popisuje, jak nakonfigurovat Microsoft Azure Backup Server (MABS) pro zálohování Microsoft Exchange serveru do Azure.  

## <a name="prerequisites"></a>Předpoklady

Než budete pokračovat, ujistěte se, že jsou [nainstalované a připravené](backup-azure-microsoft-azure-backup.md)Azure Backup Server.

## <a name="mabs-protection-agent"></a>MABS Protection Agent

Chcete-li nainstalovat agenta MABS Protection na server Exchange, postupujte podle následujících kroků:

1. Ujistěte se, že brány firewall jsou správně nakonfigurované. Viz [Konfigurace výjimek brány firewall pro agenta](/system-center/dpm/configure-firewall-settings-for-dpm?view=sc-dpm-2019).
2. Nainstalujte agenta na server Exchange kliknutím na **správa > agenti > nainstalovat** v konzole pro správu MABS. Podrobné pokyny najdete v tématu [instalace agenta MABS Protection](/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) .

## <a name="create-a-protection-group-for-the-exchange-server"></a>Vytvoření skupiny ochrany pro server Exchange

1. V konzole pro správu MABS klikněte na **ochrana**a pak na pásu karet nástroje klikněte na **Nový** . otevře se průvodce **vytvořením nové skupiny ochrany** .
2. Na **úvodní** obrazovce průvodce klikněte na tlačítko **Další**.
3. Na obrazovce **Vybrat typ skupiny ochrany** vyberte **servery** a klikněte na **Další**.
4. Vyberte databázi systému Exchange Server, kterou chcete chránit, a klikněte na tlačítko **Další**.

   > [!NOTE]
   > Pokud chráníte Exchange 2013, podívejte se na [požadavky exchange 2013](/system-center/dpm/back-up-exchange).
   >
   >

    V následujícím příkladu je vybraná databáze Exchange 2010.

    ![Vybrat členy skupiny](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Vyberte metodu ochrany dat.

    Pojmenujte skupinu ochrany a pak vyberte obě z následujících možností:

   * Chci krátkodobou ochranu pomocí disku.
   * Chci online ochranu.
6. Klikněte na **Next** (Další).
7. Zaškrtněte možnost **Spustit Eseutil pro kontrolu integrity dat** , pokud chcete ověřit integritu databází serveru Exchange.

    Po výběru této možnosti se kontrola konzistence zálohy spustí na MABS, aby se předešlo vstupně-výstupnímu přenosu generovanému spuštěním příkazu **eseutil** na serveru Exchange.

   > [!NOTE]
   > Pokud chcete použít tuto možnost, musíte zkopírovat Ese.dll a Eseutil.exe soubory do adresáře C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin na serveru MABS. V opačném případě se aktivuje následující chyba:  
   > ![Chyba programu Eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Klikněte na **Next** (Další).
9. Vyberte databázi pro **zálohování kopírováním**a pak klikněte na **Další**.

   > [!NOTE]
   > Pokud nevyberete možnost Úplná záloha pro alespoň jednu DAG kopii databáze, protokoly se nezkrátí.
   >
   >
10. Nakonfigurujte cíle pro **krátkodobé zálohování**a pak klikněte na **Další**.
11. Zkontrolujte dostupné místo na disku a potom klikněte na tlačítko **Další**.
12. Vyberte čas, kdy bude server MABS vytvořit počáteční replikaci, a poté klikněte na tlačítko **Další**.
13. Vyberte možnosti kontroly konzistence a potom klikněte na tlačítko **Další**.
14. Zvolte databázi, kterou chcete zálohovat do Azure, a potom klikněte na **Další**. Příklad:

    ![Zadat data online ochrany](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Zadejte plán pro **Azure Backup**a potom klikněte na tlačítko **Další**. Příklad:

    ![Zadat plán online zálohování](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Poznámka: body obnovení online jsou založené na expresním úplném bodu obnovení. Proto je nutné naplánovat online bod obnovení po uplynutí času zadaného pro expresní úplný bod obnovení.
    >
    >
16. Nakonfigurujte zásady uchovávání informací pro **Azure Backup**a pak klikněte na **Další**.
17. Vyberte možnost online replikace a klikněte na **Další**.

    Pokud máte rozsáhlou databázi, může trvat dlouhou dobu, než se počáteční záloha vytvoří po síti. Chcete-li se tomuto problému vyhnout, můžete vytvořit offline zálohování.  

    ![Zadat zásady uchovávání informací online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Potvrďte nastavení a potom klikněte na **vytvořit skupinu**.
19. Klikněte na **Zavřít**.

## <a name="recover-the-exchange-database"></a>Obnovení databáze Exchange

1. Databázi Exchange obnovíte kliknutím na **obnovení** v konzole pro správu MABS.
2. Vyhledejte databázi serveru Exchange, kterou chcete obnovit.
3. V rozevíracím seznamu *čas obnovení* vyberte bod obnovení online.
4. Kliknutím na tlačítko **obnovit** spusťte **Průvodce obnovením**.

Pro body obnovení online existuje pět typů obnovení:

* **Obnovit na původní umístění serveru Exchange Server:** Data budou obnovena do původního serveru Exchange.
* **Obnovit do jiné databáze na serveru Exchange Server:** Data budou obnovena do jiné databáze na jiném serveru Exchange.
* **Obnovit do databáze obnovení:** Data budou obnovena do databáze obnovení systému Exchange (RDB).
* **Kopírovat do síťové složky:** Data budou obnovena do síťové složky.
* **Kopírovat na pásku:** Pokud máte páskovou knihovnu nebo samostatnou páskovou jednotku připojenou a nakonfigurovanou v MABS, bod obnovení se zkopíruje na volnou pásku.

    ![Zvolit online replikaci](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Další kroky

* [Nejčastější dotazy k Azure Backup](backup-azure-backup-faq.md)
