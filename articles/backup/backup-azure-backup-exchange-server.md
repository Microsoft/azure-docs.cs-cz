---
title: Zálohování Exchange serveru přes System Center DPM
description: Naučte se, jak zálohovat Exchange Server pro Azure Backup pomocí softwaru System Center 2012 R2 DPM.
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: ee89af311619922fa6ca585381d70ca66955f36a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91271643"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Zálohování serveru Exchange do služby Azure Backup pomocí nástroje System Center 2012 R2 DPM

Tento článek popisuje, jak nakonfigurovat server System Center 2012 R2 Data Protection Manager (DPM) pro zálohování serveru Microsoft Exchange pro Azure Backup.  

## <a name="updates"></a>Aktualizace

Chcete-li úspěšně zaregistrovat server aplikace DPM pomocí Azure Backup, je nutné nainstalovat nejnovější kumulativní aktualizaci pro System Center 2012 R2 DPM a nejnovější verzi agenta Azure Backup. Získejte nejnovější kumulativní aktualizaci z [katalogu Microsoftu](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> V příkladech v tomto článku je nainstalována verze 2.0.8719.0 agenta Azure Backup a kumulativní aktualizace 6 je nainstalována na portálu System Center 2012 R2 DPM.
>
>

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat, ujistěte se, že byly splněny všechny [požadavky](backup-azure-dpm-introduction.md#prerequisites-and-limitations) pro použití Microsoft Azure Backup k ochraně úloh. Mezi tyto požadavky patří následující:

* Vytvořili jste úložiště záloh na webu Azure.
* Přihlašovací údaje agenta a trezoru se stáhly na server DPM.
* Agent je nainstalován na serveru DPM.
* Přihlašovací údaje trezoru se použily k registraci serveru DPM.
* Pokud chráníte Exchange 2016, upgradujte na DPM 2012 R2 UR9 nebo novější.

## <a name="dpm-protection-agent"></a>Agent ochrany aplikace DPM

Chcete-li nainstalovat agenta ochrany aplikace DPM na server Exchange, postupujte podle následujících kroků:

1. Ujistěte se, že brány firewall jsou správně nakonfigurované. Viz [Konfigurace výjimek brány firewall pro agenta](/system-center/dpm/configure-firewall-settings-for-dpm).
2. Nainstalujte agenta na server Exchange, a to tak, že vyberete **> agenti pro správu > instalaci** v konzola správce aplikace DPM. Podrobné pokyny najdete v tématu [instalace agenta ochrany aplikace DPM](/system-center/dpm/deploy-dpm-protection-agent) .

## <a name="create-a-protection-group-for-the-exchange-server"></a>Vytvoření skupiny ochrany pro server Exchange

1. V Konzola správce aplikace DPM vyberte **ochrana** a pak na pásu karet nástroje vyberte **Nový** . otevře se průvodce **vytvořením nové skupiny ochrany** .
2. Na **úvodní** obrazovce průvodce vyberte **Další**.
3. Na obrazovce **Vybrat typ skupiny ochrany** vyberte **servery** a vyberte **Další**.
4. Vyberte databázi systému Exchange Server, kterou chcete chránit, a vyberte možnost **Další**.

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
6. Vyberte **Další**.
7. Zaškrtněte možnost **Spustit Eseutil pro kontrolu integrity dat** , pokud chcete ověřit integritu databází serveru Exchange.

    Po výběru této možnosti se kontrola konzistence zálohy spustí na serveru DPM, aby se zabránilo vstupně-výstupnímu přenosu generovanému spuštěním příkazu **eseutil** na serveru Exchange.

   > [!NOTE]
   > Pokud chcete použít tuto možnost, musíte zkopírovat Ese.dll a Eseutil.exe soubory do adresáře C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin na serveru DPM. V opačném případě se aktivuje následující chyba:  
   > ![Chyba programu Eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Vyberte **Další**.
9. Vyberte databázi pro **zálohování kopírováním** a pak vyberte **Další**.

   > [!NOTE]
   > Pokud nevyberete možnost Úplná záloha pro alespoň jednu DAG kopii databáze, protokoly se nezkrátí.
   >
   >
10. Nakonfigurujte cíle pro **krátkodobé zálohování** a pak vyberte **Další**.
11. Zkontrolujte dostupné místo na disku a pak vyberte **Další**.
12. Vyberte čas, kdy bude server DPM vytvořit počáteční replikaci, a pak vyberte **Další**.
13. Vyberte možnosti kontroly konzistence a pak vyberte **Další**.
14. Zvolte databázi, kterou chcete zálohovat do Azure, a pak vyberte **Další**. Například:

    ![Zadat data online ochrany](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Definujte plán pro **Azure Backup** a pak vyberte **Další**. Například:

    ![Zadat plán online zálohování](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Poznámka: body obnovení online jsou založené na expresním úplném bodu obnovení. Proto je nutné naplánovat online bod obnovení po uplynutí času zadaného pro expresní úplný bod obnovení.
    >
    >
16. Nakonfigurujte zásady uchovávání informací pro **Azure Backup** a pak vyberte **Další**.
17. Zvolte možnost pro online replikaci a vyberte **Další**.

    Pokud máte rozsáhlou databázi, může trvat dlouhou dobu, než se počáteční záloha vytvoří po síti. Chcete-li se tomuto problému vyhnout, můžete vytvořit offline zálohování.  

    ![Zadat zásady uchovávání informací online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Potvrďte nastavení a pak vyberte **vytvořit skupinu**.
19. Vyberte **Zavřít**.

## <a name="recover-the-exchange-database"></a>Obnovení databáze Exchange

1. Chcete-li obnovit databázi systému Exchange, vyberte v Konzola správce aplikace DPM možnost **obnovení** .
2. Vyhledejte databázi serveru Exchange, kterou chcete obnovit.
3. V rozevíracím seznamu *čas obnovení* vyberte bod obnovení online.
4. Kliknutím na tlačítko **obnovit** spusťte **Průvodce obnovením**.

Pro body obnovení online existuje pět typů obnovení:

* **Obnovit na původní umístění serveru Exchange Server:** Data budou obnovena do původního serveru Exchange.
* **Obnovit do jiné databáze na serveru Exchange Server:** Data budou obnovena do jiné databáze na jiném serveru Exchange.
* **Obnovit do databáze obnovení:** Data budou obnovena do databáze obnovení systému Exchange (RDB).
* **Kopírovat do síťové složky:** Data budou obnovena do síťové složky.
* **Kopírovat na pásku:** Pokud máte páskovou knihovnu nebo samostatnou páskovou jednotku připojenou a nakonfigurovanou na serveru DPM, bod obnovení se zkopíruje na volnou pásku.

    ![Zvolit online replikaci](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Další kroky

* [Nejčastější dotazy k Azure Backup](backup-azure-backup-faq.md)
