---
title: Vytvoření trezorů Recovery Services
description: V tomto článku se dozvíte, jak vytvořit Recovery Services trezory, které ukládají zálohy a body obnovení.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 682bf26c1485bd9e2ae288a6947e9ad573463069
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705458"
---
# <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Recovery Services trezor je entita, která ukládá zálohy a body obnovení vytvořené v průběhu času. Trezor Recovery Services obsahuje také zásady zálohování, které jsou přidruženy k chráněným virtuálním počítačům.

Chcete-li vytvořit trezor Služeb zotavení:

1. Přihlaste se k předplatnému v [Azure Portal](https://portal.azure.com/).

2. V nabídce vlevo vyberte **všechny služby**.

    ![vybrat všechny služby](./media/backup-create-rs-vault/click-all-services.png)

3. V dialogovém okně **všechny služby** zadejte **Recovery Services**. Seznam prostředků se filtruje podle vašeho zadání. V seznamu prostředků vyberte **Recovery Services trezory**.

    ![Zadejte a vyberte trezory Recovery Services.](./media/backup-create-rs-vault/all-services.png)

    Zobrazí se seznam trezorů Recovery Services v předplatném.

4. Na řídicím panelu **trezorů Recovery Services** vyberte **Přidat**.

    ![Přidání trezoru Recovery Services](./media/backup-create-rs-vault/add-button-create-vault.png)

    Otevře se dialogové okno **Recovery Services trezor** . Zadejte hodnoty pro **název**, **předplatné**, **skupinu prostředků**a **umístění**.

    ![Konfigurace trezoru Recovery Services](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Název**: zadejte popisný název pro identifikaci trezoru. Název musí být jedinečný pro předplatné Azure. Zadejte název, který obsahuje alespoň 2 znaky, ale ne více než 50 znaků. Název musí začínat písmenem a obsahovat jenom písmena, číslice a spojovníky.
   - **Předplatné**: vyberte předplatné, které chcete použít. Pokud jste členem jenom jednoho předplatného, uvidíte tento název. Pokud si nejste jistí, které předplatné se má použít, použijte výchozí (navrhované) předplatné. K dispozici je více možností pouze v případě, že je váš pracovní nebo školní účet spojen s více než jedním předplatným Azure.
   - **Skupina prostředků**: použijte existující skupinu prostředků nebo vytvořte novou. Pokud chcete zobrazit seznam dostupných skupin prostředků ve vašem předplatném, vyberte **použít existující**a pak v rozevíracím seznamu vyberte prostředek. Pokud chcete vytvořit novou skupinu prostředků, vyberte **vytvořit novou** a zadejte název. Úplné informace o skupinách prostředků najdete v tématu [přehled Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   - **Umístění**: vyberte zeměpisnou oblast trezoru. K vytvoření trezoru pro ochranu virtuálních počítačů **musí** být trezor ve stejné oblasti jako virtuální počítače.

      > [!IMPORTANT]
      > Pokud si nejste jisti umístěním virtuálního počítače, zavřete dialogové okno. Přejít na seznam virtuálních počítačů na portálu. Pokud máte virtuální počítače v několika oblastech, vytvořte v každé oblasti Recovery Services trezor. Vytvořte trezor v prvním umístění, než vytvoříte trezor pro jiné umístění. Není nutné zadávat účty úložiště pro ukládání zálohovaných dat. Recovery Services trezor a popisovač služby Azure Backup, který se automaticky.
      >
      >

5. Až budete připraveni k vytvoření trezoru Recovery Services, vyberte **vytvořit**.

    ![Vytvoření trezoru Recovery Services](./media/backup-create-rs-vault/click-create-button.png)

    Vytvoření trezoru Recovery Services může chvíli trvat. Sledujte oznámení o stavu v oblasti **oznámení** v pravém horním rohu portálu. Když je trezor vytvořený, zobrazí se v seznamu trezorů Recovery Services. Pokud váš trezor nevidíte, vyberte **aktualizovat**.

     ![Aktualizuje seznam trezorů služby Backup.](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Nastavit redundanci úložiště

Azure Backup automaticky zpracovává úložiště pro trezor. Musíte určit způsob replikace tohoto úložiště.

1. V okně **Trezory služby Recovery Services** klikněte na nový trezor. V části **Nastavení** klikněte na **vlastnosti**.
2. V části **vlastnosti**v části **Konfigurace zálohování**klikněte na **aktualizovat**.

3. Vyberte typ replikace úložiště a klikněte na **Uložit**.

     ![Nastavení konfigurace úložiště pro nový trezor](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Pokud používáte Azure jako primární koncový bod úložiště záloh, doporučujeme, abyste používali výchozí **geograficky redundantní** nastavení.
   - Pokud Azure nepoužíváte jako primární koncový bod úložiště záloh, vyberte **Místně redundantní** – snížíte tím náklady na úložiště Azure.
   - Přečtěte si další informace o [geografické](../storage/common/storage-redundancy-grs.md) a [místní](../storage/common/storage-redundancy-lrs.md) redundanci.

> [!NOTE]
> Změna **typu replikace úložiště** (místně redundantní/geograficky redundantní) pro trezor služby Recovery Services je potřeba provést před konfigurací záloh v trezoru. Když nakonfigurujete zálohování, možnost změnit je zakázaná a **typ replikace úložiště**nemůžete změnit.

## <a name="set-cross-region-restore"></a>Nastavení obnovení mezi oblastmi

Jedna z možností obnovení (CRR) umožňuje obnovení virtuálních počítačů Azure v sekundární oblasti, která je [spárována se službou Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Tato možnost vám umožní:

- postupovat v případě, že dojde k auditu nebo požadavkům na dodržování předpisů
- Pokud dojde k havárii v primární oblasti, obnovte virtuální počítač nebo jeho disk.

Tuto funkci zvolíte tak, že v okně **Konfigurace zálohování** vyberete **Povolit obnovení mezi oblastmi** .

Pro tento proces se na úrovni úložiště účtují cenové dopady.

>[!NOTE]
>Než začnete:
>
>- Seznam podporovaných spravovaných typů a oblastí najdete v [matici podpory](backup-support-matrix.md#cross-region-restore) .
>- Funkce obnovení mezi oblastmi (CRR) je aktuálně dostupná jenom v oblasti WCUS.
>- CRR je funkce výslovných přihlášení na úrovni trezoru pro libovolný trezor GRS (ve výchozím nastavení vypnutý).
>- K připojení předplatného pro tuto funkci použijte prosím následující příkaz:<br>
>  `Register-AzProviderFeature -FeatureName CrossRegionRestore -ProviderNamespace Microsoft.RecoveryServices`
>- Pokud jste se k této funkci připojili ve verzi Public Limited Preview, bude e-mail s potvrzením schválení obsahovat podrobnosti o cenové zásadě.
>- Po odsouhlasení může trvat až 48 hodin, než se zálohované položky zpřístupní v sekundárních oblastech.
>- Aktuálně se podporuje jenom CRR typu správy zálohování – ARM Azure (klasický virtuální počítač Azure se nepodporuje).  Když další typy správy podporují CRR, pak se **automaticky** zaregistrují.

### <a name="configure-cross-region-restore"></a>Konfigurace obnovení mezi oblastmi

Trezor vytvořený s redundancí GRS zahrnuje možnost konfigurace funkce obnovení mezi oblastmi. Každý trezor GRS bude obsahovat banner, který bude odkazovat na dokumentaci. Pokud chcete nakonfigurovat CRR pro trezor, otevřete okno Konfigurace zálohování, které obsahuje možnost povolit tuto funkci.

 ![Banner konfigurace zálohování](./media/backup-azure-arm-restore-vms/banner.png)

1. Na portálu klikněte na Recovery Services trezor > Nastavení > Vlastnosti.
2. Pokud chcete povolit funkci, klikněte na **Povolit obnovení mezi oblastmi v tomto trezoru** .

   ![Než kliknete na Povolit obnovení mezi oblastmi v tomto trezoru](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![Po kliknutí na Povolit obnovení mezi oblastmi v tomto trezoru](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

Přečtěte si, jak [Zobrazit zálohované položky v sekundární oblasti](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region).

Naučte se [obnovit v sekundární oblasti](backup-azure-arm-restore-vms.md#restore-in-secondary-region).

Naučte se [monitorovat úlohy obnovení sekundární oblasti](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs).

## <a name="modifying-default-settings"></a>Úprava výchozích nastavení

Důrazně doporučujeme před konfigurací záloh v trezoru zkontrolovat výchozí nastavení pro **typ replikace úložiště** a **nastavení zabezpečení** .

- **Typ replikace úložiště** je ve výchozím nastavení nastaven na **geograficky redundantní**. Po nakonfigurování zálohy bude možnost úprav zakázána. Chcete-li zkontrolovat a upravit nastavení, postupujte podle těchto [kroků](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) .

- **Obnovitelné odstranění** je ve výchozím nastavení **povolené** u nově vytvořených trezorů za účelem ochrany zálohovaných dat před náhodnými nebo škodlivými odstraněními. Chcete-li zkontrolovat a upravit nastavení, postupujte podle těchto [kroků](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#disabling-soft-delete) .

## <a name="next-steps"></a>Další kroky

[Další informace](backup-azure-recovery-services-vault-overview.md) Recovery Services trezory.
[Další informace](backup-azure-delete-vault.md) Odstraňte trezory Recovery Services.
