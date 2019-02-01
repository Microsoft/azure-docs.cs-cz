---
title: Zálohování serveru Exchange do služby Azure Backup s Azure Backup serveru
description: Zjistěte, jak k zálohování serveru Exchange server do Azure Backup pomocí Azure Backup serveru
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 03/24/2017
ms.author: kasinh
ms.openlocfilehash: 40541596b4da9e0590d497785afd7d6d7f4cbcb4
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55495390"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Zálohování serveru Exchange server do Azure pomocí Azure Backup serveru
Tento článek popisuje, jak nakonfigurovat Microsoft Azure Backup Server (MABS) pro zálohování serveru Microsoft Exchange server do Azure.  

## <a name="prerequisites"></a>Požadavky
Než budete pokračovat, ujistěte se, že je Azure Backup serveru [nainstalované a připravené](backup-azure-microsoft-azure-backup.md).

## <a name="mabs-protection-agent"></a>Agent ochrany MABS
K instalaci agenta ochrany MABS na serveru Exchange, postupujte podle těchto kroků:

1. Ujistěte se, že je správně nakonfigurované bránu firewall. Zobrazit [konfigurace výjimek brány firewall pro agenta](https://technet.microsoft.com/library/Hh758204.aspx).
2. Nainstalujte agenta na serveru Exchange kliknutím **správy > agentů > instalovat** v konzole pro správu MABS. Zobrazit [nainstalujte agenta ochrany MABS](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) podrobné pokyny.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Vytvořit skupinu ochrany pro Exchange server
1. V konzole pro správu MABS, klikněte na tlačítko **ochrany**a potom klikněte na tlačítko **nový** na pásu karet, otevřete **vytvořením nové skupiny ochrany** průvodce.
2. Na **úvodní** obrazovce průvodce klikněte na tlačítko **Další**.
3. Na **vybrat typ skupiny ochrany** vyberte **servery** a klikněte na tlačítko **Další**.
4. Vyberte databázi serveru Exchange server, který chcete chránit a klikněte na tlačítko **Další**.

   > [!NOTE]
   > Pokud chráníte Exchange 2013, zkontrolujte, [požadavky serveru Exchange 2013](https://technet.microsoft.com/library/dn751029.aspx).
   >
   >

    V následujícím příkladu je vybrána databáze systému Exchange 2010.

    ![Vybrat členy skupiny](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Vyberte způsob ochrany dat.

    Název skupiny ochrany a potom vyberte obě z následujících možností:

   * Chci krátkodobou ochranu pomocí disku.
   * Chci online ochranu.
6. Klikněte na **Další**.
7. Vyberte **spuštěním programu Eseutil zkontrolujete integritu dat** možnost, pokud chcete zkontrolovat integritu databází Exchange serveru.

    Po výběru této možnosti kontroly konzistence se spustí na MABS, aby se zabránilo vstupně-výstupní provoz, který je generován spuštěním **eseutil** příkaz na serveru Exchange.

   > [!NOTE]
   > Pokud chcete použít tuto možnost, musíte zkopírovat soubory Ese.dll a Eseutil.exe do adresáře C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin na MAB server. V opačném případě se aktivuje následující chybu:  
   > ![Chyba programu eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Klikněte na **Další**.
9. Vyberte databázi pro **záložní kopii**a potom klikněte na tlačítko **Další**.

   > [!NOTE]
   > Pokud nevyberete "Úplného zálohování" alespoň jeden DAG kopii databáze, protokoly nebudou zkráceny.
   >
   >
10. Konfigurace cílů pro **krátkodobé zálohování**a potom klikněte na tlačítko **Další**.
11. Zkontrolujte dostupné místo na disku a klikněte na **Další**.
12. Vyberte čas, kdy bude MAB Server vytvořit počáteční replikace a klikněte na **Další**.
13. Vyberte nastavení kontroly konzistence a potom klikněte na tlačítko **Další**.
14. Zvolte databázi, kterou chcete zálohovat do Azure a potom klikněte na tlačítko **Další**. Příklad:

    ![Zadat data online ochrany](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Definujte plán pro **Azure Backup**a potom klikněte na tlačítko **Další**. Příklad:

    ![Zadejte plán online zálohování.](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Mějte na paměti, body obnovení Online jsou založeny na expresní úplné body obnovení. Proto je třeba naplánovat bod obnovení online po čase, který je zadán pro expresní úplné bodu obnovení.
    >
    >
16. Konfigurace zásady uchovávání informací pro **Azure Backup**a potom klikněte na tlačítko **Další**.
17. Zvolit online replikace možnost a klikněte na tlačítko **Další**.

    Pokud máte velké databázi, může trvat dlouhou dobu prvotní zálohování, který se má vytvořit v síti. K tomuto problému vyhnout, můžete vytvořit v režimu offline zálohování.  

    ![Zadejte zásady online uchovávání dat.](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Potvrďte nastavení a potom klikněte na tlačítko **vytvořit skupinu**.
19. Klikněte na **Zavřít**.

## <a name="recover-the-exchange-database"></a>Obnovení databáze systému Exchange
1. Chcete-li obnovit databáze serveru Exchange, klikněte na tlačítko **obnovení** v konzole pro správu MABS.
2. Vyhledejte databázi serveru Exchange, kterou chcete obnovit.
3. Vyberte bod obnovení online z *čas obnovení* rozevíracího seznamu.
4. Klikněte na tlačítko **obnovit** spustit **Průvodce obnovením**.

Pro body obnovení online je pět typů obnovení:

* **Obnovte do původního umístění serveru Exchange Server:** Data se obnoví do původního serveru Exchange.
* **Obnovte do jiné databáze na serveru Exchange Server:** Data budou obnoveny do jiné databáze na jiném serveru Exchange.
* **Obnovte do databáze obnovení:** Data budou obnoveny obnovení databáze systému Exchange (RDB).
* **Kopírovat do síťové složky:** Data budou obnoveny do síťové složky.
* **Kopírovat na pásku:** Pokud máte knihovnu pásků nebo samostatnou páskovou jednotku, připojit a nakonfigurovat na MABS, bodu obnovení se zkopírují na volnou pásku.

    ![Zvolit online replikaci](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Další postup
* [Azure Backup – nejčastější dotazy](backup-azure-backup-faq.md)
