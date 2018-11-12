---
title: Zálohování serveru Exchange do služby Azure Backup pomocí nástroje System Center 2012 R2 DPM
description: Zjistěte, jak k zálohování serveru Exchange server do Azure Backup pomocí System Center 2012 R2 – DPM
services: backup
author: adigan
manager: NKolli1
ms.service: backup
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: adigan
ms.openlocfilehash: 7e95fa0cc6b5f9476df68ab44e5dbc8bb5432d52
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234443"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Zálohování serveru Exchange do služby Azure Backup pomocí nástroje System Center 2012 R2 DPM
Tento článek popisuje, jak nakonfigurovat server System Center 2012 R2 Data Protection Manager (DPM) k zálohování serveru Microsoft Exchange server do služby Azure Backup.  

## <a name="updates"></a>Aktualizace
Zajištění úspěšné registrace serveru DPM pomocí služby Azure Backup, je nutné nainstalovat nejnovější kumulativní aktualizaci pro System Center 2012 R2 – DPM a nejnovější verzi agenta Azure Backup. Získejte nejnovější kumulativní aktualizaci z [Microsoft Catalog](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> Příklady v tomto článku je nainstalovaná verze 2.0.8719.0 agenta Azure Backup a kumulativní aktualizací 6 je nainstalovaný na System Center 2012 R2 – DPM.
>
>

## <a name="prerequisites"></a>Požadavky
Než budete pokračovat, ujistěte se, že všechny [požadavky](backup-azure-dpm-introduction.md#prerequisites-and-limitations) používání Microsoft Azure Backup k ochraně úloh byly splněny. Tyto požadavky zahrnují následující:

* Vytvoření trezoru záloh na webu Azure.
* Agent a přihlašovací údaje trezoru se stáhly k serveru DPM.
* Agent je nainstalovaný na serveru DPM.
* Přihlašovací údaje trezoru byly použity k registraci serveru DPM.
* Pokud chráníte Exchange 2016, upgradujte na DPM 2012 R2 UR9 nebo novější

## <a name="dpm-protection-agent"></a>Agent ochrany aplikace DPM
Pokud chcete nainstalovat agenta ochrany aplikace DPM na serveru Exchange, postupujte takto:

1. Ujistěte se, že je správně nakonfigurované bránu firewall. Zobrazit [konfigurace výjimek brány firewall pro agenta](https://technet.microsoft.com/library/Hh758204.aspx).
2. Nainstalujte agenta na serveru Exchange kliknutím **správy > agentů > instalovat** v konzoli správce aplikace DPM. Zobrazit [nainstalovat agenta ochrany aplikace DPM](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) podrobné pokyny.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Vytvořit skupinu ochrany pro Exchange server
1. V konzole správce aplikace DPM klikněte na tlačítko **ochrany**a potom klikněte na tlačítko **nový** na pásu karet, otevřete **vytvořením nové skupiny ochrany** průvodce.
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

    Po výběru této možnosti kontroly konzistence se spustí na serveru DPM, aby se zabránilo vstupně-výstupní provoz, který je generován spuštěním **eseutil** příkaz na serveru Exchange.

   > [!NOTE]
   > Pokud chcete použít tuto možnost, musíte zkopírovat soubory Ese.dll a Eseutil.exe do adresáře C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin na serveru DPM. V opačném případě se aktivuje následující chybu:  
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
12. Vyberte čas, kdy DPM server bude vytvoření počáteční replikace a klikněte na **Další**.
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
1. Chcete-li obnovit databáze serveru Exchange, klikněte na tlačítko **obnovení** v konzole správce aplikace DPM.
2. Vyhledejte databázi serveru Exchange, kterou chcete obnovit.
3. Vyberte bod obnovení online z *čas obnovení* rozevíracího seznamu.
4. Klikněte na tlačítko **obnovit** spustit **Průvodce obnovením**.

Pro body obnovení online je pět typů obnovení:

* **Obnovit do původního umístění serveru Exchange Server:** data budou obnoveny do původního serveru Exchange.
* **Obnovit do jiné databáze na serveru Exchange Server:** data bude možné obnovit do jiné databáze na jiném serveru Exchange.
* **Obnovit do databáze obnovení:** dat bude obnovena obnovení databáze systému Exchange (RDB).
* **Kopírovat do síťové složky:** data bude možné obnovit do síťové složky.
* **Kopírovat na pásku:** Pokud máte knihovnu pásků nebo samostatnou páskovou jednotku, připojit a nakonfigurovat na serveru DPM, bodu obnovení se zkopírují na volnou pásku.

    ![Zvolit online replikaci](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Další postup
* [Azure Backup – nejčastější dotazy](backup-azure-backup-faq.md)
