---
title: Zálohování a obnovení šifrovaných virtuálních počítačů Azure pomocí Azure Backup
description: Popisuje postup zálohování a obnovení šifrovaných virtuálních počítačů Azure pomocí služby Azure Backup.
ms.reviewer: geg
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 4/3/2019
ms.author: dacurwin
ms.openlocfilehash: 71a33b7263cd7a3b2f5e39ce9d24df11a7d6283c
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688605"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Zálohování a obnovení šifrovaného virtuálního počítače Azure

Tento článek popisuje, jak zálohovat a obnovovat virtuální počítače Azure se systémem Windows nebo Linux pomocí šifrovaných disků pomocí služby [Azure Backup](backup-overview.md) .

Pokud chcete získat další informace o tom, jak Azure Backup interaktivně pracovat s virtuálními počítači Azure, než začnete, Projděte si tyto materiály:

- [Zkontrolujte](backup-architecture.md#architecture-direct-backup-of-azure-vms) architekturu zálohování virtuálních počítačů Azure.
- [Další informace](backup-azure-vms-introduction.md) Zálohování virtuálního počítače Azure a rozšíření Azure Backup.

## <a name="encryption-support"></a>Podpora šifrování

Azure Backup podporuje zálohování virtuálních počítačů Azure, které mají své disky s operačním systémem nebo daty šifrované pomocí Azure Disk Encryption (ADE). ADE používá BitLocker pro šifrování virtuálních počítačů s Windows a funkci dm-crypt pro virtuální počítače se systémem Linux. ADE se integruje s Azure Key Vault pro správu šifrovacích klíčů a tajných klíčů na disku. Šifrovací klíče Key Vault (KEK) se dají použít k přidání další úrovně zabezpečení, šifrování šifrovacích tajných klíčů před jejich zápisem do Key Vault.

Azure Backup můžou zálohovat a obnovovat virtuální počítače Azure pomocí ADE s aplikací služby Azure AD a bez ní, jak je shrnuto v následující tabulce.

**Typ disku virtuálního počítače** | **ADE (klíče bek/dm-crypt)** | **ADE a KEK**
--- | --- | ---
**Spravovateln** | Ano | Ano
**Starosti**  | Ano | Ano

- Přečtěte si další informace o [ADE](../security/azure-security-disk-encryption-overview.md), [Key Vault](../key-vault/key-vault-overview.md)a [KEK](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/).
- Přečtěte si [Nejčastější dotazy](../security/azure-security-disk-encryption-faq.md) k šifrování disků virtuálních počítačů Azure.



### <a name="limitations"></a>Omezení

- Můžete zálohovat a obnovit šifrované virtuální počítače v rámci stejného předplatného a oblasti.
- Azure Backup podporuje virtuální počítače šifrované pomocí samostatných klíčů. Libovolný klíč, který je součástí certifikátu používaného k šifrování virtuálního počítače, se v tuto chvíli nepodporuje.
- Můžete zálohovat a obnovit šifrované virtuální počítače v rámci stejného předplatného a oblasti jako úložiště záloh služby Recovery Services.
- Šifrované virtuální počítače nelze obnovit na úrovni souboru nebo složky. K obnovení souborů a složek je potřeba obnovit celý virtuální počítač.
- Při obnovování virtuálního počítače nemůžete použít možnost [nahradit existující virtuální počítač](backup-azure-arm-restore-vms.md#restore-options) pro šifrované virtuální počítače. Tato možnost je podporována pouze pro nešifrované spravované disky.




## <a name="before-you-start"></a>Než začnete

Než začnete, udělejte toto:

1. Ujistěte se, že máte jeden nebo více virtuálních počítačů se [systémem Windows](../security/azure-security-disk-encryption-windows.md) nebo [Linux](../security/azure-security-disk-encryption-linux.md) s povoleným ADE.
2. [Seznamte se s maticí podpory](backup-support-matrix-iaas.md) pro zálohování virtuálních počítačů Azure.
3. Pokud ho nemáte, [vytvořte](backup-azure-arm-vms-prepare.md#create-a-vault) Recovery Services úložiště záloh.
4. Pokud povolíte šifrování pro virtuální počítače, které jsou už povolené pro zálohování, stačí, když zadáte zálohu s oprávněním pro přístup k Key Vault tak, aby zálohy mohly pokračovat bez přerušení. [Přečtěte si další informace](#provide-permissions) o přiřazení těchto oprávnění.

Kromě toho je možné, že v některých případech budete muset udělat několik věcí:

- **Nainstalujte agenta virtuálního počítače na virtuální počítač**: Azure Backup zálohuje virtuální počítače Azure tím, že nainstaluje rozšíření na agenta virtuálního počítače Azure, který běží na počítači. Pokud byl váš virtuální počítač vytvořený z image Azure Marketplace, Agent se nainstaluje a spustí. Pokud vytvoříte vlastní virtuální počítač nebo migrujete místní počítač, možná budete muset [agenta nainstalovat ručně](backup-azure-arm-vms-prepare.md#install-the-vm-agent).
- **Explicitně povolí odchozí přístup**: Obecně platí, že nemusíte explicitně povolit odchozí síťový přístup k virtuálnímu počítači Azure, aby mohl komunikovat s Azure Backup. U některých virtuálních počítačů ale může docházet k problémům s připojením a při pokusu o připojení se zobrazí chyba **ExtensionSnapshotFailedNoNetwork** . Pokud k tomu dojde, měli byste [výslovně umožnit odchozí přístup](backup-azure-arm-vms-prepare.md#explicitly-allow-outbound-access), takže rozšíření Azure Backup může komunikovat s veřejnými IP adresami Azure pro přenos dat zálohování.



## <a name="configure-a-backup-policy"></a>Konfigurace zásady zálohování

1. Pokud jste ještě nevytvořili úložiště záloh Recovery Services, postupujte podle [těchto pokynů](backup-azure-arm-vms-prepare.md#create-a-vault) .
2. Otevřete trezor na portálu a v části **Začínáme** vyberte **zálohovat** .

    ![Okno zálohování](./media/backup-azure-vms-encryption/select-backup.png)

3. V **cíli** > zálohování **, kde je spuštěná vaše úloha?** vyberte **Azure**.
4. V **Možnosti co chcete zálohovat?** vyberte **virtuální počítač** > **OK**.

      ![Okno scénáře](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. V části **zásady** > zálohování**Zvolte zásady zálohování**a vyberte zásadu, kterou chcete přidružit k trezoru. Pak klikněte na **OK**.
    - Zásady zálohování určují, kdy se mají vytvářet zálohy a jak dlouho se budou ukládat.
    - Podrobnosti výchozí zásady jsou uvedené pod rozevírací nabídkou.

    ![Otevřené okno Scénář](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Pokud nechcete používat výchozí zásady, vyberte **vytvořit novou**a [vytvořte vlastní zásadu](backup-azure-arm-vms-prepare.md#create-a-custom-policy).


7. Vyberte šifrované virtuální počítače, které chcete zálohovat, pomocí možnosti vybrat zásadu a vyberte **OK**.

      ![Výběr šifrovaných virtuálních počítačů](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Pokud používáte Azure Key Vault, zobrazí se na stránce trezor zpráva, kterou Azure Backup potřebuje k klíčům a tajným klíčům v Key Vault přístup jen pro čtení.

    - Pokud se zobrazí tato zpráva, není vyžadována žádná akce.

        ![Přístup v pořádku](./media/backup-azure-vms-encryption/access-ok.png)

    - Pokud se zobrazí tato zpráva, je nutné nastavit oprávnění, jak je popsáno níže v následujícím [postupu](#provide-permissions).

        ![Upozornění přístupu](./media/backup-azure-vms-encryption/access-warning.png)

9. Kliknutím na **Povolit zálohování** nasaďte zásady zálohování do trezoru a povolte zálohování pro vybrané virtuální počítače.


## <a name="trigger-a-backup-job"></a>Aktivace úlohy zálohování

Počáteční zálohování se spustí podle plánu, ale můžete ho spustit hned takto:

1. V nabídce trezoru klikněte na položku **zálohované položky**.
2. V nabídce **zálohované položky** klikněte na **virtuální počítač Azure**.
3. V seznamu **zálohované položky** klikněte na tři tečky (...).
4. Klikněte na **Zálohovat nyní**.
5. V části **Zálohovat nyní**pomocí ovládacího prvku kalendáře vyberte poslední den, kdy se má bod obnovení zachovat. Pak klikněte na **OK**.
6. Monitorujte oznámení na portálu. Průběh úlohy můžete monitorovat na řídicím panelu trezoru > probíhající **úlohy** > zálohování **.** V závislosti na velikosti virtuálního počítače může vytváření prvotní zálohy chvíli trvat.


## <a name="provide-permissions"></a>Poskytnout oprávnění

Virtuální počítač Azure potřebuje přístup jen pro čtení k zálohování klíčů a tajných kódů spolu s přidruženými virtuálními počítači.

- Vaše Key Vault je přidružená k tenantovi Azure AD předplatného Azure. Pokud jste uživatel s **uživatelským členem**, Azure Backup získá přístup k Key Vault bez další akce.
- Pokud jste **uživatelem typu Host**, musíte poskytnout oprávnění Azure Backup pro přístup k trezoru klíčů.

Nastavení oprávnění:

1. V Azure Portal vyberte **všechny služby**a vyhledejte **trezory klíčů**.
2. Vyberte Trezor klíčů přidružený k zašifrovanému virtuálnímu počítači, který jste zálohovali.
3. Vyberte **zásady** > přístupu**Přidat nový**.
4. Vyberte **Vybrat objekt zabezpečení**a potom zadejte **Správa zálohování**.
5. Vyberte možnost >  **Služba správy zálohování** **.**

    ![Výběr služby zálohování](./media/backup-azure-vms-encryption/select-backup-service.png)

6. V nastavení **Přidat zásadu** > přístupu**Konfigurovat ze šablony (volitelné)** vyberte **Azure Backup**.
    - Požadovaná oprávnění jsou předem vyplněna pro **klíčová oprávnění** a **oprávnění tajných**kódů.
    - Pokud je váš virtuální počítač zašifrovaný **jenom pomocí klíče bek**, odeberte výběr pro **klíčová oprávnění** , protože potřebujete jenom přístupová tajemství.

    ![Výběr služby Azure Backup](./media/backup-azure-vms-encryption/select-backup-template.png)

6. Klikněte na **OK**. Do **zásad přístupu**se přidá **Služba správy zálohování** .

    ![Zásady přístupu](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

7. Kliknutím na **Uložit** zadejte Azure Backup s oprávněními.

## <a name="restore-an-encrypted-vm"></a>Obnovení šifrovaného virtuálního počítače

Šifrované virtuální počítače obnovíte takto:

1. [Obnovte disk virtuálního počítače](backup-azure-arm-restore-vms.md#restore-disks).
2. Pak proveďte jednu z následujících akcí:
    - Použijte šablonu generovanou během operace obnovení k přizpůsobení nastavení virtuálního počítače a aktivaci nasazení virtuálního počítače. [Další informace](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    - Vytvořte nový virtuální počítač z obnovených disků pomocí PowerShellu. [Další informace](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="next-steps"></a>Další postup

Pokud narazíte na nějaké problémy, přečtěte si

- [Běžné chyby](backup-azure-vms-troubleshoot.md) při zálohování a obnovení šifrovaných virtuálních počítačů Azure.
- Problémy s [agentem virtuálního počítače Azure nebo rozšířením zálohování](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
