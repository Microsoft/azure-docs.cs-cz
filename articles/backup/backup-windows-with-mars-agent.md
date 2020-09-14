---
title: Zálohování počítačů s Windows pomocí agenta MARS
description: K zálohování počítačů s Windows použijte agenta Microsoft Azure Recovery Services (MARS).
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: d2cdd5d1fa98462a70d72fd9f8723685952b665a
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90052218"
---
# <a name="back-up-windows-server-files-and-folders-to-azure"></a>Zálohování souborů a složek Windows serveru do Azure

Tento článek vysvětluje, jak zálohovat počítače s Windows pomocí služby [Azure Backup](backup-overview.md) a agenta Microsoft Azure Recovery Services (MARS). MARS se také označuje jako agent Azure Backup.

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
>
> * Ověření předpokladů
> * Vytvořte zásady zálohování a plán.
> * Proveďte zálohování na vyžádání.

## <a name="before-you-start"></a>Než začnete

* Přečtěte si, jak [Azure Backup používá agenta Mars k zálohování počítačů s Windows](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
* Seznamte se s [architekturou zálohování](backup-architecture.md#architecture-back-up-to-dpmmabs) , která spouští agenta Mars na sekundárním serveru MABS nebo data Protection Manager.
* Přečtěte si [, co je podporováno a co můžete zálohovat](backup-support-matrix-mars-agent.md) pomocí agenta Mars.
* [Ověřte přístup k Internetu](install-mars-agent.md#verify-internet-access) na počítačích, které chcete zálohovat.
* Pokud není nainstalovaný agent MARS, zjistěte, jak ho nainstalovat [tady](install-mars-agent.md).

## <a name="create-a-backup-policy"></a>Vytvoření zásady zálohování

Zásady zálohování určují, kdy pořizovat snímky dat k vytvoření bodů obnovení. Také určuje, jak dlouho mají být body obnovení uchovávány. Pomocí agenta MARS nakonfigurujete zásady zálohování.

Azure Backup automaticky nebere v úvahu letní čas (letní čas). Toto výchozí nastavení může způsobit nesoulad mezi skutečným časem a plánovaným časem zálohování.

Vytvoření zásady zálohování:

1. Po stažení a registraci agenta MARS otevřete konzolu agenta. Najdete ho vyhledáním **Microsoft Azure Backup** ve svém počítači.  

1. V části **Akce**vyberte **naplánovat zálohování**.

    ![Naplánování zálohování Windows Serveru](./media/backup-configure-vault/schedule-first-backup.png)
1. V Průvodci plánováním **zálohování vyberte začít**  >  **dál**.
1. V části **Vybrat položky k zálohování**vyberte **Přidat položky**.

    ![Přidat položky k zálohování](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. V poli **Vybrat položky** vyberte položky, které chcete zálohovat, a pak vyberte **OK**.

    ![Vyberte položky, které chcete zálohovat.](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. Na stránce **Vyberte položky, které chcete zálohovat** vyberte **Další**.
1. Na stránce **zadání plánu zálohování** určete, kdy se mají provést denní nebo týdenní zálohování. Pak vyberte **Další**.

    * Bod obnovení se vytvoří při vytvoření zálohy.
    * Počet bodů obnovení vytvořených ve vašem prostředí závisí na plánu zálohování.
    * Můžete naplánovat až tři denní zálohy za den. V následujícím příkladu se ke dvěma denním zálohám objevuje jedna o půlnoci a druhá v 6:00./odp..

        ![Nastavení denního plánu zálohování](./media/backup-configure-vault/day-schedule.png)

    * Můžete spustit i týdenní zálohování. V následujícím příkladu jsou zálohy pořízeny každou jinou neděli a středu v rozmezí od 9:30 do 1:00 dop.

        ![Nastavení týdenního plánu zálohování](./media/backup-configure-vault/week-schedule.png)

1. Na stránce **Vybrat zásady uchovávání informací** určete, jak se mají ukládat historické kopie vašich dat. Pak vyberte **Další**.

    * Nastavení uchovávání určují, které body obnovení se mají ukládat a jak dlouho se mají ukládat.
    * V případě denního nastavení uchování značíte, že v době určené pro denní uchovávání se bude za zadaný počet dnů uchovávat nejnovější bod obnovení. Případně můžete zadat měsíční zásady uchovávání informací, které označují, že bod obnovení vytvořený na 30. den v měsíci by měl být uložen po dobu 12 měsíců.
    * Uchovávání denních a týdenních bodů obnovení se obvykle shoduje s plánem zálohování. Takže když plán aktivuje zálohování, bude bod obnovení, který záloha vytvoří, uložený po dobu, po kterou určuje denní nebo týdenní zásady uchovávání informací.
    * V následujícím příkladu:

        * Denní zálohy s půlnocí a 6:00 PM se uchovávají po dobu sedmi dnů.
        * Zálohy provedené v sobotu s půlnocí a 6:00 PM se uchovávají po dobu čtyř týdnů.
        * Zálohy provedené na poslední sobotu v měsíci s půlnocí a 6:00 PM se uchovávají po dobu 12 měsíců.
        * Zálohy provedené na poslední sobotu v březnu se uchovávají po dobu 10 let.

        ![Příklad zásady uchovávání informací](./media/backup-configure-vault/retention-example.png)

1. Na stránce **zvolit typ prvotní zálohy** rozhodněte, jestli chcete provést prvotní zálohování přes síť, nebo použijte offline zálohování. Chcete-li provést prvotní zálohování sítě, vyberte možnost **automaticky v síti**  >  **Další**.

    Další informace o offline zálohování najdete v tématu [použití Azure Data box pro zálohování offline](offline-backup-azure-data-box.md).

    ![Zvolit typ prvotní zálohy](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. Na stránce **potvrzení** zkontrolujte informace a pak vyberte **Dokončit**.

    ![Potvrďte typ zálohování.](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. Až průvodce dokončí vytváření plánu zálohování, vyberte **Zavřít**.

    ![Zobrazit průběh plánu zálohování](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Vytvořte zásadu na každém počítači, kde je nainstalován agent.

### <a name="do-the-initial-backup-offline"></a>Proveďte počáteční zálohování offline

Počáteční zálohu můžete spustit automaticky přes síť, nebo můžete provést zálohování offline. Dosazení offline pro prvotní zálohování je užitečné, pokud máte velké množství dat, které bude vyžadovat přenos větší šířky pásma sítě.

Postup při offline přenosu:

1. Zápis zálohovaných dat do pracovního umístění.
1. Pomocí nástroje AzureOfflineBackupDiskPrep zkopírujte data z pracovního umístění na jeden nebo více disků SATA.

    Nástroj vytvoří úlohu importu Azure. Další informace najdete v tématu [co je služba Azure import/export](../storage/common/storage-import-export-service.md).
1. Odešlete disky SATA do datacentra Azure.

    V datacentru se data disku zkopírují do účtu služby Azure Storage. Azure Backup zkopíruje data z účtu úložiště do trezoru a naplánují se přírůstkové zálohy.

Další informace o tom, jak [používat Azure Data box pro offline zálohování](offline-backup-azure-data-box.md), najdete v tématu použití.

### <a name="enable-network-throttling"></a>Povolit omezení sítě

To, jak agent MARS používá šířku pásma sítě, můžete řídit povolením omezení sítě. Omezování je užitečné, pokud potřebujete zálohovat data během pracovní doby, ale chcete určit, jak velkou šířku pásma používá aktivita zálohování a obnovení.

Omezování sítě v Azure Backup používá [technologii QoS (Quality of Service)](/windows-server/networking/technologies/qos/qos-policy-top) v místním operačním systému.

Omezení sítě pro zálohy je k dispozici v systémech Windows Server 2012 a novějších a ve Windows 8 a novějších verzích. Operační systémy by měly používat nejnovější aktualizace Service Pack.

Postup při povolení omezení sítě:

1. V části agent MARS vyberte **změnit vlastnosti**.
1. Na kartě **omezování** vyberte **Povolit omezování šířky pásma internetu u operací zálohování**.

    ![Nastavení omezení sítě pro operace zálohování](./media/backup-configure-vault/throttling-dialog.png)
1. Určete povolenou šířku pásma během pracovní doby a nepracovních hodin. Hodnoty šířky pásma začínají v 512 kbps a jdou až 1 023 MB/s. Pak vyberte **OK**.

## <a name="run-an-on-demand-backup"></a>Spuštění zálohování na vyžádání

1. V části agent MARS vyberte **Zálohovat nyní**.

    ![Zálohovat nyní ve Windows serveru](./media/backup-configure-vault/backup-now.png)

1. Pokud je verze agenta MARS 2.0.9169.0 nebo novější, můžete nastavit vlastní datum uchovávání. V části **uchování zálohy** vyberte datum z kalendáře.

   ![Přizpůsobení data uchování pomocí kalendáře](./media/backup-configure-vault/mars-ondemand.png)

1. Na stránce **potvrzení** zkontrolujte nastavení a vyberte **zálohovat**.
1. Kliknutím na **Zavřít** zavřete průvodce. Pokud průvodce zavřete před dokončením zálohování, průvodce bude nadále běžet na pozadí.

Po dokončení prvotní zálohy se v konzole zálohování zobrazí stav **Dokončená úloha** .

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>Nastavení chování uchovávání zásad zálohování na vyžádání

> [!NOTE]
> Tyto informace se vztahují jenom na verze agenta MARS, které jsou starší než 2.0.9169.0.
>

| Možnost zálohování – plán | Doba uchovávání dat
| -- | --
| Den | **Výchozí uchování**: odpovídá "uchování ve dnech pro denní zálohy". <br/><br/> **Výjimka**: Pokud dojde k selhání denního zálohování naplánovaného pro dlouhodobé uchování (týdny, měsíce nebo roky), zálohování na vyžádání, které se aktivuje hned po selhání, se považuje za dlouhodobé uchovávání. V opačném případě se další naplánované zálohování považuje za dlouhodobou dobu uchovávání.<br/><br/> **Ukázkový scénář**: naplánované zálohování ve čtvrtek v 8:00 se nezdařilo. Tato záloha se bude považovat za týdenní, měsíční nebo roční uchování. Takže první zálohování na vyžádání aktivované před dalším plánovaným zálohováním v pátek ve 8:00 se automaticky označilo pro týdenní, měsíční nebo roční uchování. Tato záloha nahrazuje zálohu 8:00.
| Týden | **Výchozí doba uchování**: jeden den. Zálohy na vyžádání, které jsou pořízeny pro zdroj dat s týdenními zásadami zálohování, se odstraní v příštím dni. Odstraní se i v případě, že se jedná o nejaktuálnější zálohy pro zdroj dat. <br/><br/> **Výjimka**: Pokud se týdenní plánované zálohování nastavilo pro dlouhodobé uchovávání dat (týdny, měsíce nebo roky), zálohování na vyžádání, které se aktivuje hned po selhání, se považuje za dlouhodobé uchovávání. V opačném případě se další naplánované zálohování považuje za dlouhodobou dobu uchovávání. <br/><br/> **Ukázkový scénář**: naplánované zálohování ve čtvrtek v 8:00 se nezdařilo. Tato záloha se bude považovat za měsíční nebo roční uchování. Takže první záloha na vyžádání, která se aktivovala před dalším plánovaným zálohováním ve čtvrtek v 8:00, je automaticky označená pro měsíční nebo roční uchování. Tato záloha nahrazuje zálohu 8:00.

Další informace najdete v tématu [Vytvoření zásady zálohování](#create-a-backup-policy).

## <a name="next-steps"></a>Další kroky

* Naučte se, jak [obnovit soubory v Azure](backup-azure-restore-windows-server.md).
* Vyhledání [běžných dotazů týkajících se zálohování souborů a složek](backup-azure-file-folder-backup-faq.md)
