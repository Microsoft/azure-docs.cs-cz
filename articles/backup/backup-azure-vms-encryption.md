---
title: Zálohování a obnovení šifrovaných virtuálních počítačů Azure
description: Popisuje postup zálohování a obnovení šifrovaných virtuálních počítačů Azure pomocí služby Azure Backup.
ms.topic: conceptual
ms.date: 08/18/2020
ms.openlocfilehash: db06b64fba203fb3d2ed54d34235504ac6aa4e2d
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223453"
---
# <a name="back-up-and-restore-encrypted-azure-virtual-machines"></a>Zálohování a obnovení šifrovaných virtuálních počítačů Azure

Tento článek popisuje, jak zálohovat a obnovovat virtuální počítače Azure se systémem Windows nebo Linux pomocí šifrovaných disků pomocí služby [Azure Backup](backup-overview.md) . Další informace najdete v tématu [šifrování záloh virtuálních počítačů Azure](backup-azure-vms-introduction.md#encryption-of-azure-vm-backups).

## <a name="encryption-using-platform-managed-keys"></a>Šifrování pomocí klíčů spravovaných platformou

Ve výchozím nastavení jsou všechny disky ve vašich virtuálních počítačích automaticky zašifrované, a to pomocí klíčů (PMK) spravovaných platformou (PMK), které používají [šifrování služby Storage](../storage/common/storage-service-encryption.md). Tyto virtuální počítače můžete zálohovat pomocí Azure Backup bez jakýchkoli konkrétních akcí nutných k podpoře šifrování na konci. Další informace o šifrování pomocí klíčů spravovaných platformou [najdete v tomto článku](../virtual-machines/disk-encryption.md#platform-managed-keys).

![Šifrované disky](./media/backup-encryption/encrypted-disks.png)

## <a name="encryption-using-customer-managed-keys"></a>Šifrování s využitím klíčů spravovaných zákazníky

Při šifrování disků pomocí klíčů spravovaných zákazníkem (CMK) se klíč používaný k šifrování disků ukládá do Azure Key Vault a spravuje je. Šifrování služby Storage (SSE) používající CMK se liší od šifrování Azure Disk Encryption (ADE). ADE používá nástroje pro šifrování operačního systému. SSE šifruje data ve službě úložiště a umožňuje pro vaše virtuální počítače používat libovolný operační systém nebo Image.

Nemusíte provádět žádné explicitní akce pro zálohování nebo obnovení virtuálních počítačů, které používají klíče spravované zákazníkem k šifrování svých disků. Data záloh pro tyto virtuální počítače uložená v trezoru se budou šifrovat stejnými metodami jako [šifrování použité v trezoru](encryption-at-rest-with-cmk.md).

Další informace o šifrování spravovaných disků pomocí klíčů spravovaných zákazníkem najdete v [tomto článku](../virtual-machines/disk-encryption.md#customer-managed-keys).

## <a name="encryption-support-using-ade"></a>Podpora šifrování pomocí ADE

Azure Backup podporuje zálohování virtuálních počítačů Azure, které mají své disky s operačním systémem nebo daty šifrované pomocí Azure Disk Encryption (ADE). ADE používá BitLocker pro šifrování virtuálních počítačů s Windows a funkci dm-crypt pro virtuální počítače se systémem Linux. ADE se integruje s Azure Key Vault pro správu šifrovacích klíčů a tajných klíčů na disku. Šifrovací klíče Key Vault (KEK) se dají použít k přidání další úrovně zabezpečení, šifrování šifrovacích tajných klíčů před jejich zápisem do Key Vault.

Azure Backup můžou zálohovat a obnovovat virtuální počítače Azure pomocí ADE s aplikací služby Azure AD a bez ní, jak je shrnuto v následující tabulce.

**Typ disku virtuálního počítače** | **ADE (klíče bek/dm-crypt)** | **ADE a KEK**
--- | --- | ---
**Nespravovaný** | Yes | Yes
**Spravované**  | Yes | Yes

- Přečtěte si další informace o [ADE](../security/fundamentals/azure-disk-encryption-vms-vmss.md), [Key Vault](../key-vault/general/overview.md)a [KEK](../virtual-machine-scale-sets/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek).
- Přečtěte si [Nejčastější dotazy](../security/fundamentals/azure-disk-encryption-vms-vmss.md) k šifrování disků virtuálních počítačů Azure.

### <a name="limitations"></a>Omezení

- Můžete zálohovat a obnovit šifrované virtuální počítače ADE v rámci stejného předplatného a oblasti.
- Azure Backup podporuje virtuální počítače šifrované pomocí samostatných klíčů. Libovolný klíč, který je součástí certifikátu používaného k šifrování virtuálního počítače, se v tuto chvíli nepodporuje.
- Můžete zálohovat a obnovit šifrované virtuální počítače ADE v rámci stejného předplatného a oblasti jako úložiště záloh Recovery Services.
- Šifrované virtuální počítače ADE nelze obnovit na úrovni souboru nebo složky. K obnovení souborů a složek je potřeba obnovit celý virtuální počítač.
- Při obnovování virtuálního počítače nemůžete použít možnost [nahradit existující virtuální počítač](backup-azure-arm-restore-vms.md#restore-options) pro šifrované virtuální počítače ADE. Tato možnost je podporována pouze pro nešifrované spravované disky.

## <a name="before-you-start"></a>Než začnete

Než začnete, udělejte toto:

1. Ujistěte se, že máte jeden nebo více virtuálních počítačů se [systémem Windows](../virtual-machines/linux/disk-encryption-overview.md) nebo [Linux](../virtual-machines/linux/disk-encryption-overview.md) s povoleným ADE.
2. [Seznamte se s maticí podpory](backup-support-matrix-iaas.md) pro zálohování virtuálních počítačů Azure.
3. Pokud ho nemáte, [vytvořte](backup-create-rs-vault.md) Recovery Services úložiště záloh.
4. Pokud povolíte šifrování pro virtuální počítače, které jsou už povolené pro zálohování, stačí, když zadáte zálohu s oprávněním pro přístup k Key Vault tak, aby zálohy mohly pokračovat bez přerušení. [Přečtěte si další informace](#provide-permissions) o přiřazení těchto oprávnění.

Kromě toho je možné, že v některých případech budete muset udělat několik věcí:

- **Instalace agenta virtuálního počítače na virtuální počítač**: Azure Backup zálohuje virtuální počítače Azure tím, že nainstaluje rozšíření na agenta virtuálního počítače Azure, který běží na počítači. Pokud byl váš virtuální počítač vytvořen z bitové kopie Azure Marketplace, je agent nainstalovaný a spuštěný. Pokud vytvoříte vlastní virtuální počítač nebo migrujete místní počítač, možná budete muset [agenta nainstalovat ručně](backup-azure-arm-vms-prepare.md#install-the-vm-agent).

## <a name="configure-a-backup-policy"></a>Konfigurace zásady zálohování

1. Pokud jste ještě nevytvořili úložiště záloh Recovery Services, postupujte podle [těchto pokynů](backup-create-rs-vault.md).
1. Otevřete trezor na portálu a v části **Přehled** vyberte **+ Backup** .

    ![Podokno zálohování](./media/backup-azure-vms-encryption/select-backup.png)

1. V **cíli zálohování**  >  **, kde je spuštěná vaše úloha?** vyberte **Azure**.
1. V **Možnosti co chcete zálohovat?** vyberte **virtuální počítač**. Pak vyberte **zálohovat**.

      ![Podokno scénáře](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

1. V části **zásady zálohování**  >  **Zvolte zásady zálohování** a vyberte zásadu, kterou chcete přidružit k trezoru. Pak vyberte **OK**.
    - Zásady zálohování určují, kdy se mají vytvářet zálohy a jak dlouho se budou ukládat.
    - Podrobnosti výchozí zásady jsou uvedené pod rozevírací nabídkou.

    ![Zvolit zásady zálohování](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

1. Pokud nechcete používat výchozí zásady, vyberte **vytvořit novou** a [vytvořte vlastní zásadu](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

1. V části **Virtual Machines** vyberte **Přidat**.

    ![Přidat virtuální počítače](./media/backup-azure-vms-encryption/add-virtual-machines.png)

1. Vyberte šifrované virtuální počítače, které chcete zálohovat, pomocí možnosti vybrat zásadu a vyberte **OK**.

      ![Výběr šifrovaných virtuálních počítačů](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

1. Pokud používáte Azure Key Vault, zobrazí se na stránce trezor zpráva, že Azure Backup potřebuje přístup jen pro čtení k klíčům a tajným klíčům v Key Vault.

    - Pokud se zobrazí tato zpráva, není vyžadována žádná akce.

        ![Přístup v pořádku](./media/backup-azure-vms-encryption/access-ok.png)

    - Pokud se zobrazí tato zpráva, je nutné nastavit oprávnění, jak je popsáno níže v následujícím [postupu](#provide-permissions).

        ![Upozornění přístupu](./media/backup-azure-vms-encryption/access-warning.png)

1. Vyberte **Povolit zálohování** a nasaďte zásady zálohování do trezoru a povolte zálohování pro vybrané virtuální počítače.

## <a name="trigger-a-backup-job"></a>Aktivace úlohy zálohování

Počáteční zálohování se spustí podle plánu, ale můžete ho spustit hned takto:

1. V nabídce trezoru vyberte **zálohované položky**.
2. V položku **zálohované položky** vyberte **virtuální počítač Azure**.
3. V seznamu **zálohované položky** vyberte tři tečky (...).
4. Vyberte **Zálohovat nyní**.
5. V části **Zálohovat nyní** pomocí ovládacího prvku kalendáře vyberte poslední den, kdy se má bod obnovení zachovat. Pak vyberte **OK**.
6. Monitorujte oznámení na portálu. Průběh úlohy můžete monitorovat na řídicím panelu trezoru > probíhající **úlohy zálohování**  >  . V závislosti na velikosti virtuálního počítače může vytváření prvotní zálohy chvíli trvat.

## <a name="provide-permissions"></a>Poskytnout oprávnění

Azure Backup potřebuje přístup jen pro čtení k zálohování klíčů a tajných kódů spolu s přidruženými virtuálními počítači.

- Vaše Key Vault je přidružená k tenantovi Azure AD předplatného Azure. Pokud jste uživatel s **uživatelským členem**, Azure Backup získá přístup k Key Vault bez další akce.
- Pokud jste **uživatelem typu Host**, musíte poskytnout oprávnění Azure Backup pro přístup k trezoru klíčů.

Nastavení oprávnění:

1. V Azure Portal vyberte **všechny služby** a vyhledejte **trezory klíčů**.
1. Vyberte Trezor klíčů přidružený k zašifrovanému virtuálnímu počítači, který jste zálohovali.

    >[!TIP]
    >K identifikaci úložiště klíčů přidruženého k virtuálnímu počítači použijte následující příkaz PowerShellu. Nahraďte název skupiny prostředků a název virtuálního počítače:
    >
    >`Get-AzVm -ResourceGroupName "MyResourceGroup001" -VMName "VM001" -Status`
    >
    > Vyhledejte název trezoru klíčů na tomto řádku:
    >
    >`SecretUrl            : https://<keyVaultName>.vault.azure.net`
    >

1. Vyberte **zásady přístupu**  >  **Přidat zásady přístupu**.

    ![Přidat zásady přístupu](./media/backup-azure-vms-encryption/add-access-policy.png)

1. V nastavení **Přidat zásadu přístupu**  >  **Konfigurovat ze šablony (volitelné)** vyberte **Azure Backup**.
    - Požadovaná oprávnění jsou předem vyplněna pro **klíčová oprávnění** a **oprávnění tajných** kódů.
    - Pokud je váš virtuální počítač zašifrovaný **jenom pomocí klíče bek**, odeberte výběr pro **klíčová oprávnění** , protože potřebujete jenom přístupová tajemství.

    ![Azure Backup výběr](./media/backup-azure-vms-encryption/select-backup-template.png)

1. Vyberte **Přidat**. Do **zásad přístupu** se přidá **Služba správy zálohování** .

    ![Zásady přístupu](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

1. Vyberte **Save (Uložit** ) a zadejte Azure Backup s oprávněními.

## <a name="restore-an-encrypted-vm"></a>Obnovení šifrovaného virtuálního počítače

Šifrované virtuální počítače se dají obnovit jenom obnovením disku virtuálního počítače, jak je vysvětleno níže. **Nahradit existující** a **obnovit virtuální počítač** není podporovaný.

Následujícím způsobem obnovte šifrované virtuální počítače:

1. [Obnovte disk virtuálního počítače](backup-azure-arm-restore-vms.md#restore-disks).
2. Vytvořte instanci virtuálního počítače znovu tak, že provedete jednu z následujících akcí:
    1. Použijte šablonu generovanou během operace obnovení k přizpůsobení nastavení virtuálního počítače a aktivaci nasazení virtuálního počítače. [Přečtěte si další informace](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    2. Vytvořte nový virtuální počítač z obnovených disků pomocí PowerShellu. [Přečtěte si další informace](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).
3. Pro virtuální počítače se systémem Linux přeinstalujte rozšíření ADE, aby byly datové disky otevřené a připojené.

## <a name="next-steps"></a>Další kroky

Pokud narazíte na nějaké problémy, přečtěte si tyto články:

- [Běžné chyby](backup-azure-vms-troubleshoot.md) při zálohování a obnovení šifrovaných virtuálních počítačů Azure.
- Problémy s [agentem virtuálního počítače Azure nebo rozšířením zálohování](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
