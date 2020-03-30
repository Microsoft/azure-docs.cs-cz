---
title: Vytvořit trezory služby Recovery Services
description: V tomto článku se dozvíte, jak vytvořit trezory služby Recovery Services, ve kterých jsou ukládány zálohy a body obnovení.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: e722996f516d21445d8e0028df925ca44eb02bfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295014"
---
# <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Trezor služby Recovery Services je entita, která ukládá zálohy a body obnovení vytvořené v průběhu času. Trezor služby Recovery Services také obsahuje zásady zálohování, které jsou přidruženy k chráněným virtuálním počítačům.

Chcete-li vytvořit trezor Služeb zotavení:

1. Přihlaste se ke svému předplatnému na [webu Azure Portal](https://portal.azure.com/).

2. V levé nabídce vyberte **Všechny služby**.

    ![Vybrat všechny služby](./media/backup-create-rs-vault/click-all-services.png)

3. V dialogovém okně **Všechny služby** zadejte **služby obnovení**. Seznam zdrojů filtruje podle vašeho vstupu. V seznamu zdrojů vyberte **trezory služby Recovery Services**.

    ![Zadání a výběr trezorů služby Recovery Services](./media/backup-create-rs-vault/all-services.png)

    Zobrazí se seznam trezorů služby Recovery Services v předplatném.

4. Na řídicím panelu **trezorů služby Recovery Services** vyberte **Přidat**.

    ![Přidání trezoru služby Recovery Services](./media/backup-create-rs-vault/add-button-create-vault.png)

    Otevře se dialogové okno **trezoru služby Recovery Services.** Zadejte hodnoty pro **název**, **odběr**, **skupinu prostředků**a **umístění**.

    ![Konfigurace trezoru služby Recovery Services](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Název**: Zadejte popisný název pro identifikaci trezoru. Název musí být jedinečný pro předplatné Azure. Zadejte název, který má alespoň dva, ale ne více než 50 znaků. Název musí začínat písmenem a skládat pouze z písmen, čísel a spojovníků.
   - **Předplatné**: Zvolte předplatné, které chcete použít. Pokud jste členem pouze jednoho předplatného, uvidíte toto jméno. Pokud si nejste jistí, které předplatné použít, použijte výchozí (navrhované) předplatné. Existuje několik možností pouze v případě, že váš pracovní nebo školní účet je přidružený k více než jednomu předplatnému Azure.
   - **Skupina prostředků**: Použijte existující skupinu prostředků nebo vytvořte novou. Pokud chcete zobrazit seznam dostupných skupin prostředků v předplatném, vyberte **Použít existující**a v rozevíracím seznamu vyberte prostředek. Chcete-li vytvořit novou skupinu prostředků, vyberte **Vytvořit nový** a zadejte název. Úplné informace o skupinách prostředků najdete v [tématu Přehled Správce prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   - **Umístění**: Vyberte zeměpisnou oblast pro úschovnu. Chcete-li vytvořit trezor pro ochranu virtuálních počítačů, **musí** být trezor ve stejné oblasti jako virtuální počítače.

      > [!IMPORTANT]
      > Pokud si nejste jistí umístěním virtuálního počítače, zavřete dialogové okno. Přejděte na seznam virtuálních počítačů na portálu. Pokud máte virtuální počítače v několika oblastech, vytvořte trezor služby Recovery Services v každé oblasti. Před vytvořením úschovny pro jiné umístění vytvořte úschovnu na prvním místě. Není nutné zadávat účty úložiště pro ukládání záložních dat. Trezor služby Recovery Services a služba Azure Backup to automaticky zvládnou.
      >
      >

5. Až budete připraveni vytvořit trezor Služby pro obnovení, vyberte **Vytvořit**.

    ![Vytvoření trezoru služby Recovery Services](./media/backup-create-rs-vault/click-create-button.png)

    Vytvoření trezoru služby Recovery Services může chvíli trvat. Sledujte oznámení o stavu v oblasti **Oznámení** v pravém horním rohu portálu. Po vytvoření trezoru je zobrazen v seznamu trezorů služby Recovery Services. Pokud trezor nevidíte, vyberte **Aktualizovat**.

     ![Aktualizace seznamu záložních trezorů](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Nastavení redundance úložiště

Azure Backup automaticky zpracovává úložiště pro trezor. Je třeba zadat způsob replikace tohoto úložiště.

1. V okně **Trezory služby Recovery Services** klikněte na nový trezor. V části **Nastavení** klikněte na **vlastnosti**.
2. V **části Vlastnosti**klepněte v části **Konfigurace zálohování**na tlačítko **Aktualizovat**.

3. Vyberte typ replikace úložiště a klepněte na **tlačítko Uložit**.

     ![Nastavení konfigurace úložiště pro nový trezor](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Pokud používáte Azure jako koncový bod primárního úložiště záloh, doporučujeme dál používat výchozí **geograficky redundantní** nastavení.
   - Pokud Azure nepoužíváte jako primární koncový bod úložiště záloh, vyberte **Místně redundantní** – snížíte tím náklady na úložiště Azure.
   - Přečtěte si další informace o [geografické](../storage/common/storage-redundancy-grs.md) a [místní](../storage/common/storage-redundancy-lrs.md) redundanci.

> [!NOTE]
> Před konfigurací záloh v trezoru je třeba provést změnu **typu Replikace úložiště** (místně redundantní/ geograficky redundantní) pro trezor služeb obnovení. Po konfiguraci zálohování je možnost úpravzakázána a nelze změnit **typ replikace úložiště**.

## <a name="set-cross-region-restore"></a>Nastavit obnovení křížové oblasti

Jako jednu z možností obnovení umožňuje obnovení mezi oblastmi (CRR) obnovení virtuálních počítačích Azure v sekundární oblasti, což je [spárovaná oblast Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Tato možnost umožňuje:

- provádět cvičení, pokud existuje požadavek na audit nebo dodržování předpisů
- obnovení virtuálního počítače nebo jeho disku, pokud dojde k havárii v primární oblasti.

Chcete-li vybrat tuto funkci, vyberte možnost **Povolit obnovení oblasti kříže** v okně **Konfigurace zálohování.**

Pro tento proces existují cenové důsledky, jak je na úrovni úložiště.

>[!NOTE]
>Než začnete:
>
>- Zkontrolujte [matici podpory](backup-support-matrix.md#cross-region-restore) pro seznam podporovaných spravovaných typů a oblastí.
>- Funkce Obnovení mezi oblastmi (CRR) je v současné době k dispozici pouze v následujících oblastech: 
>    - USA – středozápad
>    - USA – západ 2
>    - USA – středojih
>    - USA – východ
>    - USA – východ 2
>    - USA – středosever
>    - Střední Kanada
>    - Kanada – východ
>    - Austrálie – východ
>    - Austrálie – jihovýchod
>    - Indie – střed
>    - Indie – jih
>    - Japonsko – východ
>    - Japonsko – západ
>    - Jihovýchodní Asie
>    - Spojené království – jih
>    - Spojené království – západ
>    - Francie – střed
>    - Jižní Korea – střed
>    - Jižní Korea – jih
>- CRR je funkce opt-in na úrovni úschovny pro všechny trezory GRS (ve výchozím nastavení vypnutá).
>- K přihlášení k předplatnému pro tuto funkci použijte následující příkaz:<br>
>  `Register-AzProviderFeature -FeatureName CrossRegionRestore -ProviderNamespace Microsoft.RecoveryServices`
>- Pokud jste na této funkci zahrnuti během verze Preview s omezenou publicitou, bude e-mail schválení recenze obsahovat podrobnosti o zásadách pro ceny.
>- Po přihlášení může trvat až 48 hodin, než budou položky zálohy k dispozici v sekundárních oblastech.
>- V současné době crr je podporovaná jenom pro typ správy zálohování – ARM Azure VM (klasický virtuální počítač Azure nebude podporován).  Pokud další typy správy podporují CRR, budou **automaticky** zapsány.

### <a name="configure-cross-region-restore"></a>Konfigurovat obnovení průřezu

Úschovna vytvořená s redundancí GRS zahrnuje možnost konfigurace funkce Obnovení oblasti napříč oblastmi. Každý grs trezor bude mít banner, který bude odkaz na dokumentaci. Chcete-li nakonfigurovat crr pro trezor, přejděte do okna Konfigurace zálohování, které obsahuje možnost povolit tuto funkci.

 ![Nápis Konfigurace zálohy](./media/backup-azure-arm-restore-vms/banner.png)

1. Na portálu přejděte do trezoru služby Recovery Services > Nastavení > vlastnosti.
2. Chcete-li tuto funkci povolit, klepněte na tlačítko **Povolit obnovení oblasti kříže v tomto trezoru.**

   ![Před klepnutím na tlačítko Povolit obnovení oblasti kříže v tomto trezoru](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![Po klepnutí na tlačítko Povolit obnovení oblasti kříže v tomto trezoru](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

Přečtěte si, jak [zobrazit položky zálohování v sekundární oblasti](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region).

Přečtěte si, jak [obnovit v sekundární oblasti](backup-azure-arm-restore-vms.md#restore-in-secondary-region).

Přečtěte si, jak [sledovat úlohy obnovení sekundární oblasti](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs).

## <a name="modifying-default-settings"></a>Změna výchozího nastavení

Důrazně doporučujeme před konfigurací záloh v úložišti zkontrolovat výchozí nastavení **typu Replikace úložiště** a **nastavení zabezpečení.**

- **Typ Replikace úložiště** je ve výchozím nastavení nastaven na **geograficky redundantní**. Po konfiguraci zálohy je možnost úpravzakázána. Podle těchto [kroků](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) zkontrolujte a upravte nastavení.

- **Obnovitelné odstranění** ve výchozím nastavení je **povoleno** v nově vytvořených trezorech, které chrání záložní data před náhodnými nebo škodlivými odstraněními. Podle těchto [kroků](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#disabling-soft-delete) zkontrolujte a upravte nastavení.

## <a name="next-steps"></a>Další kroky

[Další informace o](backup-azure-recovery-services-vault-overview.md) Trezory služby obnovení.
[Další informace o](backup-azure-delete-vault.md) Odstranit trezory služby Recovery Services
