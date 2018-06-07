---
title: Zálohování serveru Exchange server Azure Backup pomocí serveru Azure Backup
description: Zjistěte, jak pro zálohování serveru Exchange server do Azure Backup pomocí serveru Azure Backup
services: backup
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 03/24/2017
ms.author: pullabhk
ms.openlocfilehash: d64c273a189b1fe2337c4430b156874e0adf54b2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605956"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-azure-backup-server"></a>Zálohování serveru Exchange server Azure Backup pomocí serveru Azure Backup
Tento článek popisuje, jak nakonfigurovat Microsoft Azure Backup Server (MABS) na server Microsoft Exchange zálohovat do Azure.  

## <a name="prerequisites"></a>Požadavky
Než budete pokračovat, ujistěte se, zda je Azure Backup Server [nainstalované a připravené](backup-azure-microsoft-azure-backup.md).

## <a name="mabs-protection-agent"></a>Agent ochrany MABS
Chcete-li nainstalovat agenta ochrany MABS na serveru Exchange, postupujte takto:

1. Ujistěte se, zda jsou správně nakonfigurovány bránu firewall. V tématu [konfigurace výjimek brány firewall pro agenta](https://technet.microsoft.com/library/Hh758204.aspx).
2. Nainstalujte agenta na serveru Exchange kliknutím **správy > agenti > nainstalujte** v konzole pro správu MABS. V tématu [nainstalovat agenta ochrany MABS](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) podrobné pokyny.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Vytvořte skupinu ochrany pro Exchange server
1. V konzole správce MABS, klikněte na tlačítko **ochrany**a potom klikněte na **nový** na pásu karet otevřete **vytvořením nové skupiny ochrany** průvodce.
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

    Když vyberete tuto možnost, kontrola konzistence zálohovaných souborů se spustí na MABS, aby se zabránilo vstupně-výstupní provoz, který je generovaný systémem **eseutil** příkaz na serveru Exchange.

   > [!NOTE]
   > Chcete-li použít tuto možnost, musíte zkopírovat soubory Ese.dll a Eseutil.exe do adresáře C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin na serveru MAB. Jinak se aktivuje k následující chybě:  
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
12. Vyberte dobu, kdy serveru MAB bude vytvoření počáteční replikace a pak klikněte na tlačítko **Další**.
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
1. Chcete-li obnovit databáze serveru Exchange, klikněte na tlačítko **obnovení** v konzole správce MABS.
2. Vyhledejte databázi serveru Exchange, kterou chcete obnovit.
3. Vyberte bod obnovení online z *čas obnovení* rozevíracího seznamu.
4. Klikněte na tlačítko **obnovit** spustit **Průvodce obnovením**.

Pro body obnovení online se pět typů obnovení:

* **Obnovit na původní umístění serveru Exchange Server:** data budou obnoveny do původního serveru Exchange.
* **Obnovit do jiné databáze na serveru Exchange Server:** data budou obnoveny do jiné databáze na jiný server Exchange.
* **Obnovit do databáze obnovení:** data bude obnovena obnovení databáze Exchange (RDB).
* **Kopírovat do síťové složky:** data budou obnoveny do síťové složky.
* **Kopírovat na pásku:** Pokud máte knihovnu pásků nebo samostatné páskové jednotky připojené a nakonfigurováno na MABS, vytvoření bodu obnovení se zkopírují na volnou pásku.

    ![Zvolte online replikace](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Další postup
* [Azure Backup – nejčastější dotazy](backup-azure-backup-faq.md)
