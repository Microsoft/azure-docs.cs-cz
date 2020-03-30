---
title: Zálohování a obnovení šifrovaných virtuálních počítačů Azure
description: Popisuje, jak zálohovat a obnovovat šifrované virtuální počítače Azure pomocí služby Azure Backup.
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: a3976cc83f749b1abe00cef3f5bf867ffbc30ab6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206686"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Zálohování a obnovení šifrovaného virtuálního počítače Azure

Tento článek popisuje, jak zálohovat a obnovovat virtuální počítače Windows nebo Linux Azure (VM) se šifrovanými disky pomocí služby [Azure Backup.](backup-overview.md)

Pokud se chcete dozvědět víc o tom, jak Azure Backup spolupracuje s virtuálními počítači Azure, než začnete, zkontrolujte tyto prostředky:

- [Zkontrolujte](backup-architecture.md#architecture-built-in-azure-vm-backup) architekturu zálohování virtuálních počítačových virtuálních počítače Azure.
- [Další informace o](backup-azure-vms-introduction.md) Zálohování virtuálních počítačových virtuálních počítačových společností Azure a rozšíření Azure Backup.

## <a name="encryption-support"></a>Podpora šifrování

Azure Backup podporuje zálohování virtuálních počítačů Azure, které mají své operační sypa/datové disky zašifrované pomocí Azure Disk Encryption (ADE). ADE používá nástroj BitLocker pro šifrování virtuálních počítačů se systémem Windows a funkci dm-crypt pro virtuální počítače s Linuxem. Služba ADE se integruje s azure key vaultpro správu klíčů a tajných klíčů pro šifrování disku. Key Vault Key Encryption Keys (KEKs) can be used to add an additional layer of security, encrypting encryption secrets before writing them to Key Vault.

Azure Backup můžete zálohovat a obnovovat virtuální počítače Azure pomocí ADE s a bez aplikace Azure AD, jak je shrnuto v následující tabulce.

**Typ disku virtuálního počítače** | **ADE (BEK/dm-crypt)** | **ADE a KEK**
--- | --- | ---
**Nespravovaný** | Ano | Ano
**Spravované**  | Ano | Ano

- Další informace o [ade](../security/azure-security-disk-encryption-overview.md), [Trezor klíčů](../key-vault/key-vault-overview.md)a [keks](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-key-vault#set-up-a-key-encryption-key-kek).
- Přečtěte si [nejčastější dotazy](../security/azure-security-disk-encryption-faq.md) k šifrování disku virtuálního počítače Azure.

### <a name="limitations"></a>Omezení

- Šifrované virtuální ms můžete zálohovat a obnovovat v rámci stejného předplatného a oblasti.
- Azure Backup podporuje virtuální počítače šifrované pomocí samostatných klíčů. Žádný klíč, který je součástí certifikátu používaného k šifrování virtuálního virtuálního mísy, není momentálně podporovaný.
- Šifrované virtuální počítače můžete zálohovat a obnovovat v rámci stejného předplatného a oblasti jako trezor zálohování služby Recovery Services.
- Šifrované virtuální aplikace nelze obnovit na úrovni souborů nebo složek. Chcete-li obnovit soubory a složky, musíte obnovit celý virtuální virtuální ms.
- Při obnovení virtuálního virtuálního ms nelze použít [možnost nahradit existující virtuální hod](backup-azure-arm-restore-vms.md#restore-options) pro šifrované virtuální chod. Tato možnost je podporována pouze pro nešifrované spravované disky.

## <a name="before-you-start"></a>Než začnete

Než začnete, postupujte takto:

1. Ujistěte se, že máte jeden nebo více virtuálních počítačích [se systémem Windows](../security/azure-security-disk-encryption-windows.md) nebo [Linux](../virtual-machines/linux/disk-encryption-overview.md) s povolenou službou ADE.
2. [Kontrola matice podpory](backup-support-matrix-iaas.md) pro zálohování virtuálních počítačích Azure
3. [Pokud](backup-azure-arm-vms-prepare.md#create-a-vault) trezor zálohování služby Recovery Services nemáte, vytvořte jej.
4. Pokud povolíte šifrování pro virtuální počítače, které jsou již povoleny pro zálohování, stačí poskytnout zálohování s oprávněními pro přístup k trezoru klíčů, aby zálohy mohly pokračovat bez přerušení. [Přečtěte si další informace](#provide-permissions) o přiřazení těchto oprávnění.

Kromě toho existuje několik věcí, které budete muset udělat za určitých okolností:

- **Nainstalujte agenta virtuálního počítače na virtuální počítač:** Azure Backup zálohuje virtuální počítače Azure instalací rozšíření pro agenta virtuálního počítače Azure spuštěného v počítači. Pokud váš virtuální počítač byl vytvořen z image Azure Marketplace, agent je nainstalovaný a spuštěný. Pokud vytvoříte vlastní virtuální počítač nebo migrujete místní počítač, možná budete muset [nainstalovat agenta ručně](backup-azure-arm-vms-prepare.md#install-the-vm-agent).

## <a name="configure-a-backup-policy"></a>Konfigurace zásad zálohování

1. Pokud jste ještě nevytvořili trezor zálohování služby Recovery Services, postupujte [podle těchto pokynů.](backup-azure-arm-vms-prepare.md#create-a-vault)
2. Otevřete úschovnu na portálu a v části **Začínáme** vyberte **Zálohovat.**

    ![Záložní okno](./media/backup-azure-vms-encryption/select-backup.png)

3. V **Azure** **cíli** > **zálohování: Kde běží vaše úloha?**
4. V **části Co chcete zálohovat?** vyberte Virtuální **počítač** > **OK**.

      ![Okno scénáře](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. V **zásadách** > zálohování**Zvolte zásady zálohování**vyberte zásadu, kterou chcete přidružit k úschovně. Pak klikněte na **OK**.
    - Zásady zálohování určuje, kdy jsou zálohy přijata a jak dlouho jsou uloženy.
    - Podrobnosti výchozí zásady jsou uvedené pod rozevírací nabídkou.

    ![Otevřené okno Scénář](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Pokud nechcete používat výchozí zásadu, vyberte **Vytvořit nový**a [vytvořte vlastní zásadu](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

7. Vyberte šifrované virtuální aplikace, které chcete zálohovat pomocí zásad výběru, a vyberte **OK**.

      ![Výběr šifrovaných virtuálních mís](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Pokud používáte Azure Key Vault, na stránce úložiště se zobrazí zpráva, že Azure Backup potřebuje přístup jen pro čtení ke klíčům a tajným kódům v trezoru klíčů.

    - Pokud se zobrazí tato zpráva, není vyžadována žádná akce.

        ![Přístup OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Pokud se zobrazí tato zpráva, je třeba nastavit oprávnění, jak je popsáno v [postupu níže](#provide-permissions).

        ![Upozornění na přístup](./media/backup-azure-vms-encryption/access-warning.png)

9. Kliknutím na **Povolit zálohování** nasaďte zásady zálohování do trezoru a povolte zálohování pro vybrané virtuální počítače.

## <a name="trigger-a-backup-job"></a>Spuštění úlohy zálohování

Počáteční záloha bude spuštěna v souladu s plánem, ale můžete ji spustit okamžitě následujícím způsobem:

1. V nabídce úschovny klikněte na **Zálohovat položky**.
2. V **položkách zálohování**klikněte na **položku Virtuální počítač Azure**.
3. V seznamu **Položky zálohování** klikněte na tři tečky (...).
4. Klepněte na tlačítko **Zálohovat .**
5. V **aplikaci Backup Now**vyberte pomocí ovládacího prvku kalendář poslední den, kdy má být bod obnovení zachován. Pak klikněte na **OK**.
6. Sledujte oznámení portálu. Průběh úlohy můžete sledovat na řídicím panelu trezoru >**probíhající** **úlohy** > zálohování . V závislosti na velikosti virtuálního počítače může vytváření prvotní zálohy chvíli trvat.

## <a name="provide-permissions"></a>Poskytnutí oprávnění

Virtuální počítač Azure potřebuje přístup jen pro čtení k zálohování klíčů a tajných kódů spolu s přidruženými virtuálními počítači.

- Trezor klíčů je přidružený k tenantovi Azure AD předplatného Azure. Pokud jste **členem uživatele**, Azure Backup získá přístup k trezoru klíčů bez další akce.
- Pokud jste **uživatel typu Host**, musíte pro azure backup poskytnout oprávnění pro přístup k trezoru klíčů.

Nastavení oprávnění:

1. Na webu Azure Portal vyberte **Všechny služby**a vyhledejte **trezory klíčů**.
2. Vyberte trezor klíčů přidružený k šifrovanému virtuálnímu virtuálnímu mněmu, který zálohujete.
3. Vyberte **zásady** > **přístupu Přidat nový**.
4. Vyberte **možnost Vybrat hlavní objekt**a zadejte příkaz Správa **zálohování**.
5. Vyberte**možnost Výběr** **služby správy** > zálohování .

    ![Výběr zálohovací služby](./media/backup-azure-vms-encryption/select-backup-service.png)

6. V **části Přidat zásady přístupu** > **Konfigurace ze šablony (volitelné)** vyberte **Azure Backup**.
    - Požadovaná oprávnění jsou předvyplněna pro **klíčová oprávnění** a **tajná oprávnění**.
    - Pokud váš virtuální počítač je šifrována **pomocí bek pouze**, odebrat výběr pro **oprávnění klíče,** protože potřebujete pouze oprávnění pro tajné klíče.

    ![Výběr záloh y Azure](./media/backup-azure-vms-encryption/select-backup-template.png)

7. Klikněte na tlačítko **OK**. **Služba správy zálohování** je přidána do **zásad aplikace Access**.

    ![Zásady přístupu](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

8. Kliknutím na **Uložit** poskytněte azure backupu oprávnění.

## <a name="restore-an-encrypted-vm"></a>Obnovení šifrovaného virtuálního mísy

Šifrované virtuální aplikace obnovíte následujícím způsobem:

1. [Obnovte disk virtuálního počítače](backup-azure-arm-restore-vms.md#restore-disks).
2. Znovu vytvořte instanci virtuálního počítače jedním z následujících akcí:
    1. Pomocí šablony, která je generována během operace obnovení přizpůsobit nastavení virtuálních počítače a aktivovat nasazení virtuálního počítače. [Další informace](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    2. Vytvořte nový virtuální počítače z obnovených disků pomocí Prostředí PowerShell. [Další informace](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).
3. U virtuálních počítačů s Linuxem přeinstalujte rozšíření ADE, aby byly datové disky otevřené a připojené.

## <a name="next-steps"></a>Další kroky

Pokud narazíte na nějaké problémy, přečtěte si tyto články:

- [Běžné chyby](backup-azure-vms-troubleshoot.md) při zálohování a obnovení šifrovaných virtuálních počítačích Azure.
- [Problémy s rozšířením agenta/zálohování virtuálního počítače Azure.](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
