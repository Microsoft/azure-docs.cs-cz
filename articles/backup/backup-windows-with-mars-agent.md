---
title: Zálohování počítačů se systémem Windows pomocí agenta MARS
description: K zálohování počítačů s Windows použijte agenta služby Microsoft Azure Recovery Services (MARS).
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 12463f33a6fa97b33e70b77fb2fcf6b0a27b5790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408908"
---
# <a name="back-up-windows-machines-by-using-the-azure-backup-mars-agent"></a>Zálohování počítačů s Windows pomocí agenta Azure Backup MARS

Tento článek vysvětluje, jak zálohovat počítače s Windows pomocí služby [Azure Backup](backup-overview.md) a agenta služby Microsoft Azure Recovery Services (MARS). MARS se také označuje jako agent azure backup.

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
>
> * Ověření předpokladů
> * Vytvořte zásadu a plán zálohování.
> * Proveďte zálohu na vyžádání.

## <a name="before-you-start"></a>Než začnete

* Zjistěte, jak [Azure Backup používá agenta MARS k zálohování počítačů s Windows](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
* Informace o [záložní architektuře,](backup-architecture.md#architecture-back-up-to-dpmmabs) která spouští agenta MARS na sekundárním serveru MABS nebo Data Protection Manager.
* [Zkontrolujte, co je podporováno a co můžete zálohovat](backup-support-matrix-mars-agent.md) agentem MARS.
* [Ověřte přístup k internetu](install-mars-agent.md#verify-internet-access) na počítačích, které chcete zálohovat.
* Pokud agent MARS není nainstalován, přečtěte si, jak jej nainstalovat [zde](install-mars-agent.md).

## <a name="create-a-backup-policy"></a>Vytvoření zásad zálohování

Zásady zálohování určuje, kdy má být pořizovat snímky dat k vytvoření bodů obnovení. Také určuje, jak dlouho zachovat body obnovení. Agent MARS slouží ke konfiguraci zásad zálohování.

Azure Backup nebere automaticky letní čas (DST) v úvahu. Toto výchozí nastavení může způsobit určité nesrovnalosti mezi skutečným časem a plánovaným časem zálohování.

Vytvoření zásadzálohování:

1. Po stažení a registraci agenta MARS otevřete konzolu agenta. Najdete ho vyhledáním **Microsoft Azure Backup** ve svém počítači.  

1. V části **Akce**vyberte **Naplánovat zálohování**.

    ![Naplánování zálohování Windows Serveru](./media/backup-configure-vault/schedule-first-backup.png)
1. V Průvodci plánováním zálohování vyberte **Možnost Začínáme** > **další**.
1. V části **Vybrat položky k zálohování**vyberte Přidat **položky**.

    ![Přidání položek pro zálohování](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. V poli **Vybrat položky** vyberte položky, které chcete zálohovat, a pak vyberte **OK**.

    ![Výběr položek, které chcete zálohovat](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. Na stránce **Vybrat položky k zálohování** vyberte **Další**.
1. Na stránce **Zadat plán zálohování** určete, kdy se mají pobírají denní nebo týdenní zálohy. Pak vyberte **Další**.

    * Bod obnovení je vytvořen při zálohování.
    * Počet bodů obnovení vytvořených ve vašem prostředí závisí na plánu zálohování.
    * Můžete naplánovat až tři denní zálohy denně. V následujícím příkladu dojde ke dvěma dennízálohy, jeden o půlnoci a jeden v 6:00 PM.

        ![Nastavení denního plánu zálohování](./media/backup-configure-vault/day-schedule.png)

    * Můžete také spouštět týdenní zálohování. V následujícím příkladu zálohy jsou převzaty každou alternativní neděli a středu v 9:30 a 1:00 AM.

        ![Nastavení týdenního plánu zálohování](./media/backup-configure-vault/week-schedule.png)

1. Na stránce **Vybrat zásady uchovávání informací** určete způsob ukládání historických kopií dat. Pak vyberte **Další**.

    * Nastavení uchovávání informací určují, které body obnovení mají být ukládány a jak dlouho se mají ukládat.
    * Pro nastavení denní uchovávání informací udáváte, že v době určené pro denní uchovávání informací bude poslední bod obnovení zachován po zadaný počet dní. Nebo můžete zadat měsíční zásady uchovávání informací označující, že bod obnovení vytvořený 30.
    * Uchovávání denních a týdenních bodů obnovení se obvykle shoduje s plánem zálohování. Takže když plán aktivuje zálohu, bod obnovení, který vytvoří záloha je uložen po dobu, kterou určuje denní nebo týdenní zásady uchovávání informací.
    * V následujícím příkladu:

        * Denní zálohy o půlnoci a 18:00 jsou uchovávány po dobu sedmi dnů.
        * Zálohy pořízené v sobotu o půlnoci a 18:00 jsou uchovávány po dobu čtyř týdnů.
        * Zálohy pořízené poslední sobotu v měsíci o půlnoci a 18:00 jsou uchovávány po dobu 12 měsíců.
        * Zálohy přijaté poslední sobotu v březnu jsou uchovávány po dobu 10 let.

        ![Příklad zásad uchovávání informací](./media/backup-configure-vault/retention-example.png)

1. Na stránce **Zvolit typ počátečnízálohy** se rozhodněte, zda chcete provést počáteční zálohu v síti nebo použít zálohování offline. Chcete-li provést počáteční zálohu v síti, vyberte **možnost Automaticky v síti** > **Další**.

    Další informace o zálohování offline najdete [v tématu Použití datovéschránky Azure pro zálohování offline](offline-backup-azure-data-box.md).

    ![Výběr počátečního typu zálohy](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. Na stránce **Potvrzení** zkontrolujte informace a pak vyberte **Dokončit**.

    ![Potvrzení typu zálohy](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. Po dokončení průvodce vytvořením plánu zálohování vyberte **zavřít**.

    ![Zobrazení průběhu plánu zálohování](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Vytvořte zásady pro každý počítač, ve kterém je agent nainstalován.

### <a name="do-the-initial-backup-offline"></a>Do počáteční zálohování offline

Počáteční zálohu můžete spustit automaticky v síti nebo můžete zálohovat offline. Offline výsev pro počáteční zálohu je užitečné, pokud máte velké množství dat, která budou vyžadovat velké šířky pásma sítě k přenosu.

Jak provést offline přenos:

1. Zapište záložní data do pracovního umístění.
1. Pomocí nástroje AzureOfflineBackupDiskPrep zkopírujte data z pracovního umístění na jeden nebo více disků SATA.

    Nástroj vytvoří úlohu importu Azure. Další informace najdete [v tématu Co je služba Import a export Azure](https://docs.microsoft.com/azure/storage/common/storage-import-export-service).
1. Odešlete disky SATA do datového centra Azure.

    V datovém centru se data disku zkopírují do účtu úložiště Azure. Azure Backup zkopíruje data z účtu úložiště do trezoru a jsou naplánovány přírůstkové zálohy.

Další informace o offline osevění najdete [v tématu Použití Azure Data Box pro zálohování offline](offline-backup-azure-data-box.md).

### <a name="enable-network-throttling"></a>Povolení omezení sítě

Povolením omezení sítě můžete řídit, jak agent MARS využívá šířku pásma sítě. Omezení je užitečné, pokud potřebujete zálohovat data během pracovní doby, ale chcete řídit, jakou šířku pásma používá aktivita zálohování a obnovení.

Omezení sítě v Azure Backup používá [quality of Service (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) v místním operačním systému.

Omezení zálohování v síti je dostupné v systému Windows Server 2012 a novějším a v systému Windows 8 a novějším. Operační systémy by měly být spuštěny nejnovější aktualizace Service Pack.

Povolení omezení sítě:

1. V agentovi MARS vyberte **Změnit vlastnosti**.
1. Na kartě **Omezení** vyberte **Povolit omezení využití šířky pásma internetu pro operace zálohování**.

    ![Nastavení omezení sítě pro operace zálohování](./media/backup-configure-vault/throttling-dialog.png)
1. Určete povolenou šířku pásma během pracovní doby a mimo pracovní dobu. Hodnoty šířky pásma začínají rychlostí 512 kb/s a srychlostí 1 023 MB/s. Pak vyberte **OK**.

## <a name="run-an-on-demand-backup"></a>Spuštění zálohy na vyžádání

1. V agentovi MARS vyberte **Zálohovat .**

    ![Zálohování v systému Windows Server](./media/backup-configure-vault/backup-now.png)

1. Pokud je verze agenta MARS 2.0.9169.0 nebo novější, můžete nastavit vlastní datum uchování. V části **Retain Backup Till** zvolte datum z kalendáře.

   ![Přizpůsobení data uchovávání pomocí kalendáře](./media/backup-configure-vault/mars-ondemand.png)

1. Na stránce **Potvrzení** zkontrolujte nastavení a vyberte **Zálohovat**.
1. Chcete-li průvodce zavřít, vyberte **zavřít.** Pokud průvodce zavřete před dokončením zálohování, bude průvodce nadále spuštěn na pozadí.

Po dokončení počáteční zálohy se v konzole zálohování zobrazí stav **Dokončená úloha.**

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>Nastavení chování uchovávání zásad zálohování na vyžádání

> [!NOTE]
> Tyto informace platí pouze pro verze agenta MARS, které jsou starší než 2.0.9169.0.
>

| Možnost plánu zálohování | Doba uchovávání údajů
| -- | --
| Den | **Výchozí uchovávání:** Ekvivalentní "uchovávání ve dnech pro denní zálohy." <br/><br/> **Výjimka**: Pokud se nezdaří denní naplánované zálohování nastavené pro dlouhodobé uchovávání (týdny, měsíce nebo roky), záloha na vyžádání, která se aktivuje hned po selhání, je zvažována pro dlouhodobé uchovávání. V opačném případě je další naplánované zálohování považováno za dlouhodobé uchovávání.<br/><br/> **Příklad scénáře**: Plánované zálohování ve čtvrtek v 8:00 se nezdařilo. Tato záloha měla být zvážena pro týdenní, měsíční nebo roční uchovávání. Takže první záloha na vyžádání spuštěná před dalším plánovaným zálohováním v pátek v 8:00 je automaticky označena pro týdenní, měsíční nebo roční uchovávání. Tato záloha nahrazuje zálohu ve čtvrtek 8:00.
| Týden | **Výchozí uchovávání :** Jeden den. Zálohy na vyžádání, které jsou převzaty pro zdroj dat, který má zásady týdenní zálohování jsou odstraněny následující den. Odstraní se i v případě, že se nepředstavují nejnovější zálohy pro zdroj dat. <br/><br/> **Výjimka**: Pokud týdenní naplánované zálohování, které je nastaveno pro dlouhodobé uchovávání (týdny, měsíce nebo roky), selže, záloha na vyžádání, která se aktivuje hned po selhání, je zvažována pro dlouhodobé uchovávání. V opačném případě je další naplánované zálohování považováno za dlouhodobé uchovávání. <br/><br/> **Příklad scénáře**: Plánované zálohování ve čtvrtek v 8:00 se nezdařilo. Tato záloha měla být zvážena pro měsíční nebo roční uchovávání. Takže první záloha na vyžádání, která se spustí před dalším plánovaným zálohováním ve čtvrtek v 8:00, je automaticky označena pro měsíční nebo roční uchovávání. Tato záloha nahrazuje zálohu ve čtvrtek 8:00.

Další informace naleznete [v tématu Vytvoření zásad zálohování](#create-a-backup-policy).

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [obnovit soubory v Azure](backup-azure-restore-windows-server.md).
* [Vyhledání nejčastějších dotazů týkajících se zálohování souborů a složek](backup-azure-file-folder-backup-faq.md)

