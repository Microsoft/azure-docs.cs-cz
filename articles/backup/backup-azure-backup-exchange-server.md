---
title: Zálohování serveru Exchange do služby Azure Backup pomocí nástroje System Center 2012 R2 DPM
description: Zjistěte, jak pro zálohování serveru Exchange server do Azure Backup pomocí System Center 2012 R2 DPM
services: backup
author: MaanasSaran
manager: NKolli1
ms.service: backup
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: adigan
ms.openlocfilehash: 4edec499d12261add398e5a9297f039ecfb252e9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605096"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Zálohování serveru Exchange do služby Azure Backup pomocí nástroje System Center 2012 R2 DPM
Tento článek popisuje postup konfigurace serveru System Center 2012 R2 Data Protection Manager (DPM) pro server Microsoft Exchange zálohovat do služby Azure Backup.  

## <a name="updates"></a>Aktualizace
Chcete-li úspěšně registrace serveru DPM pomocí zálohování Azure, musíte nainstalovat nejnovější kumulativní aktualizace pro System Center 2012 R2 DPM a nejnovější verzi Azure Backup Agent. Získat nejnovější kumulativní aktualizaci z [Microsoft Catalog](http://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> Příklady v tomto článku je nainstalována verze 2.0.8719.0 Azure Backup Agent a kumulativní aktualizací 6 je nainstalovaná na System Center 2012 R2 DPM.
>
>

## <a name="prerequisites"></a>Požadavky
Než budete pokračovat, ujistěte se, že všechny [požadavky](backup-azure-dpm-introduction.md#prerequisites) pro použití Microsoft Azure Backup k ochraně byly splněny úlohy. Tyto součásti zahrnují následující:

* Byla vytvořena trezor záloh na webu Azure.
* K serveru aplikace DPM byly staženy agenta a přihlašovací údaje trezoru.
* Agent byl nainstalován na serveru DPM.
* Přihlašovací údaje trezoru jste použili k registrace serveru DPM.
* Pokud chráníte Exchange 2016, proveďte upgrade na DPM 2012 R2 UR9 nebo novější

## <a name="dpm-protection-agent"></a>Agent ochrany aplikace DPM
Chcete-li nainstalovat agenta ochrany DPM na serveru Exchange, postupujte takto:

1. Ujistěte se, zda jsou správně nakonfigurovány bránu firewall. V tématu [konfigurace výjimek brány firewall pro agenta](https://technet.microsoft.com/library/Hh758204.aspx).
2. Nainstalujte agenta na serveru Exchange kliknutím **správy > agenti > nainstalujte** v konzoli správce aplikace DPM. V tématu [nainstalovat agenta ochrany DPM](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) podrobné pokyny.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Vytvořte skupinu ochrany pro Exchange server
1. V konzole správce aplikace DPM klikněte na **ochrany**a potom klikněte na **nový** na pásu karet otevřete **vytvořením nové skupiny ochrany** průvodce.
2. Na **úvodní** obrazovce průvodce, klikněte na tlačítko **Další**.
3. Na **vybrat typ skupiny ochrany** vyberte **servery** a klikněte na tlačítko **Další**.
4. Vyberte databázi serveru Exchange, který chcete chránit a klikněte na tlačítko **Další**.

   > [!NOTE]
   > Pokud chráníte Exchange 2013, podívejte se [požadavky serveru Exchange 2013](https://technet.microsoft.com/library/dn751029.aspx).
   >
   >

    V následujícím příkladu je vybraná databáze Exchange 2010.

    ![Vybrat členy skupiny](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Vyberte způsob ochrany dat.

    Název skupiny ochrany a poté vyberte možnost z následujících možností:

   * Chci krátkodobou ochranu pomocí disku.
   * Chci online ochranu.
6. Klikněte na **Další**.
7. Vyberte **spuštěním programu Eseutil zkontrolujete integritu dat** možnost, pokud chcete provést kontrolu integrity databází systému Exchange Server.

    Když vyberete tuto možnost, bude na serveru DPM, aby se zabránilo vstupně-výstupní provoz, který je generovaný systémem spustit kontrolu konzistence zálohy **eseutil** příkaz na serveru Exchange.

   > [!NOTE]
   > Chcete-li použít tuto možnost, musíte zkopírovat soubory Ese.dll a Eseutil.exe do adresáře C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin na serveru DPM. Jinak se aktivuje k následující chybě:  
   > ![Chyba eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Klikněte na **Další**.
9. Vyberte databázi pro **zálohování kopírováním**a potom klikněte na **Další**.

   > [!NOTE]
   > Pokud nevyberete "Úplného zálohování" pro alespoň jednu DAG kopii databáze, nebudou zkráceny protokoly.
   >
   >
10. Konfigurace cílů pro **krátkodobé zálohování**a potom klikněte na **Další**.
11. Zkontrolujte dostupné místo na disku a pak klikněte na tlačítko **Další**.
12. Vyberte dobu, kdy DPM server bude počáteční replikace a potom klikněte na **Další**.
13. Vyberte možnosti kontroly konzistence a pak klikněte na tlačítko **Další**.
14. Vyberte databázi, která chcete zálohovat do Azure a pak klikněte na tlačítko **Další**. Příklad:

    ![Zadejte data pro online ochranu](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Definujte plán pro **Azure Backup**a potom klikněte na **Další**. Příklad:

    ![Zadejte plán online zálohování](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Všimněte si, body obnovení Online jsou založené na expresní úplné body obnovení. Po dobu, která je určená pro expresní úplné bod obnovení, proto musí naplánovat bodu obnovení online.
    >
    >
16. Konfigurace zásad uchovávání informací pro **Azure Backup**a potom klikněte na **Další**.
17. Zvolte možnost online replikace a klikněte na tlačítko **Další**.

    Pokud máte velké databáze, může trvat dlouhou dobu pro prvotní zálohování, který se má vytvořit přes síť. K tomuto problému vyhnout, můžete vytvořit zálohu do offline režimu.  

    ![Zadejte zásady online uchovávání dat.](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Potvrďte nastavení a pak klikněte na tlačítko **vytvořit skupinu**.
19. Klikněte na **Zavřít**.

## <a name="recover-the-exchange-database"></a>Obnovení databáze systému Exchange
1. Chcete-li obnovit databáze serveru Exchange, klikněte na tlačítko **obnovení** v konzole správce aplikace DPM.
2. Vyhledejte databázi serveru Exchange, kterou chcete obnovit.
3. Vyberte bod obnovení online z *čas obnovení* rozevíracího seznamu.
4. Klikněte na tlačítko **obnovit** spustit **Průvodce obnovením**.

Pro body obnovení online se pět typů obnovení:

* **Obnovit na původní umístění serveru Exchange Server:** data budou obnoveny do původního serveru Exchange.
* **Obnovit do jiné databáze na serveru Exchange Server:** data budou obnoveny do jiné databáze na jiný server Exchange.
* **Obnovit do databáze obnovení:** data bude obnovena obnovení databáze Exchange (RDB).
* **Kopírovat do síťové složky:** data budou obnoveny do síťové složky.
* **Kopírovat na pásku:** Pokud máte knihovnu pásků nebo samostatné páskové jednotky připojené a nakonfigurováno na serveru aplikace DPM, vytvoření bodu obnovení se zkopírují na volnou pásku.

    ![Zvolte online replikace](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Další postup
* [Azure Backup – nejčastější dotazy](backup-azure-backup-faq.md)
