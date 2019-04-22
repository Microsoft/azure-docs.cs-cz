---
title: Zálohování a obnovení šifrovaných virtuálních počítačů Azure pomocí služby Azure Backup
description: Popisuje postup zálohování a obnovení šifrovaných virtuálních počítačů Azure pomocí služby Azure Backup.
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 4/3/2019
ms.author: geetha
ms.openlocfilehash: 893a22fb9f325625707869c8f6571d572b8f6b33
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59358229"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Zálohování a obnovení šifrovaných virtuálních počítačů Azure

Tento článek popisuje, jak zálohovat a obnovovat virtuální počítače Windows nebo Linux Azure (VM) se šifrovanými disky pomocí [Azure Backup](backup-overview.md) služby.

Pokud chcete informace o tom, jak Azure Backup komunikuje virtuální počítače Azure než začnete, projděte si tyto prostředky:

- [Kontrola](backup-architecture.md#architecture-direct-backup-of-azure-vms) architektura zálohování virtuálního počítače Azure.
- [Další informace o](backup-azure-vms-introduction.md) zálohování virtuálních počítačů Azure a Azure Backup rozšíření.

## <a name="encryption-support"></a>Podpora šifrování

Azure Backup podporuje zálohování virtuálních počítačů Azure, které mají jejich operačního systému a datové disky šifrované pomocí Azure Disk Encryption (ADE). ADE používá nástroj BitLocker k šifrování virtuálních počítačů Windows a funkci dm-crypt pro virtuální počítače s Linuxem. ADE se integruje se službou Azure Key Vault a Správa disku šifrovacích klíčů a tajných kódů. Key Vault klíč šifrovací klíče (KEKs) je možné přidat další vrstvu zabezpečení, šifrování tajných kódů šifrování před zápisem do služby Key Vault.

Azure Backup můžete zálohovat a obnovit virtuální počítače Azure a nemusíte aplikace Azure AD pomocí ADE dle souhrnu v následující tabulce.

**Typ disku virtuálního počítače** | **ADE (BEK/dm-crypt)** | **ADE a KEK**
--- | --- | ---
**nespravované** | Ano | Ano
**Spravované**  | Ano | Ano

- Další informace o [ADE](../security/azure-security-disk-encryption-overview.md), [služby Key Vault](../key-vault/key-vault-overview.md), a [KEKs](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/).
- Přečtěte si [nejčastější dotazy k](../security/azure-security-disk-encryption-faq.md) pro šifrování disku virtuálního počítače Azure.



### <a name="limitations"></a>Omezení

- Můžete zálohování a obnovení šifrovaných virtuálních počítačů ve stejném předplatném a oblasti.
- Azure Backup podporuje virtuální počítače jsou šifrované pomocí klíče samostatné. Libovolné klávesy, která je součástí certifikát použitý k šifrování virtuálního počítače se momentálně nepodporuje.
- Můžete zálohování a obnovení šifrovaných virtuálních počítačů ve stejném předplatném a oblasti jako trezor služby Recovery Services zálohování.
- Šifrované virtuální počítače nelze obnovit na úrovni souboru nebo složky. Budete muset obnovit celý virtuální počítač pro obnovení souborů a složek.
- Při obnovování virtuálního počítače, nelze použít [nahrazení existujícího virtuálního počítače](backup-azure-arm-restore-vms.md#restore-options) možnost pro šifrované virtuální počítače. Tato možnost je podporována pouze pro nešifrované spravované disky.




## <a name="before-you-start"></a>Než začnete

Než začnete, postupujte takto:

1. Ujistěte se, že máte jeden nebo více [Windows](../security/azure-security-disk-encryption-windows.md) nebo [Linux](../security/azure-security-disk-encryption-linux.md) virtuálních počítačů s ADE povolena.
2. [Zkontrolujte podporu](backup-support-matrix-iaas.md) pro zálohování virtuálních počítačů Azure
3. [Vytvoření](backup-azure-arm-vms-prepare.md#create-a-vault) trezoru služby Recovery Services Backup pokud ho nemáte.
4. Pokud povolíte šifrování pro virtuální počítače, které jsou již povolena pro zálohování, stačí jednoduše poskytnout oprávnění pro přístup ke službě Key Vault tak, aby zálohování můžete bez přerušení pokračovat v zálohování. [Další informace](#provide-permissions) o přiřazování oprávnění.

Kromě toho existuje několik věcí, které možná bude potřeba provést v některých případech:

- **Nainstalujte agenta virtuálního počítače na virtuálním počítači**: Azure Backup zálohuje virtuální počítače Azure po instalaci rozšíření virtuálního počítače Azure agenta spuštěného na počítači. Pokud byl váš virtuální počítač vytvořen z image Azure marketplace, je agent nainstalován a spuštěn. Pokud vytváříte vlastní virtuální počítač nebo migrovat místní počítač, možná budete muset [nainstalujte agenta ručně](backup-azure-arm-vms-prepare.md#install-the-vm-agent).
- **Výslovně povolit odchozí přístup**: Obecně platí není nutné výslovně povolit odchozí síťový přístup pro virtuální počítač Azure mohla komunikovat s Azure Backup. Však několik virtuálních počítačů setkat s problémy s připojením, zobrazuje **ExtensionSnapshotFailedNoNetwork** při pokusu o připojení došlo k chybě. Pokud k tomu dojde, měli byste [výslovně povolit odchozí přístup](backup-azure-arm-vms-prepare.md#explicitly-allow-outbound-access), takže rozšíření Azure Backup může komunikovat s Azure veřejné IP adresy pro provoz zálohování.



## <a name="configure-a-backup-policy"></a>Nakonfigurujte zásady zálohování

1. Pokud jste ještě nevytvořili zálohování trezoru služby Recovery Services, postupujte podle [těchto pokynů](backup-azure-arm-vms-prepare.md#create-a-vault)
2. Otevřete trezor na portálu a vyberte **zálohování** v **Začínáme** oddílu.

    ![Okno zálohování](./media/backup-azure-vms-encryption/select-backup.png)

3. V **cíl zálohování** > **ve kterém je spuštěná vaše úloha?** vyberte **Azure**.
4. V **co chcete zálohovat?** vyberte **virtuálního počítače** > **OK**.

      ![Okno scénář](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. V **zásady zálohování** > **výběr zásady zálohování**, vyberte zásadu, kterou chcete přidružit k trezoru. Pak klikněte na **OK**.
    - Zásady zálohování Určuje, kdy zálohy jsou prováděny, a jak dlouho se uchovávají.
    - Podrobnosti výchozí zásady jsou uvedené pod rozevírací nabídkou.

    ![Otevřené okno Scénář](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Pokud už nechcete používat výchozí zásadu, vyberte **vytvořit nový**, a [vytvořit vlastní zásadu](backup-azure-arm-vms-prepare.md#create-a-custom-policy).


7. Zvolte šifrovaných virtuálních počítačů, které chcete zálohovat, vyberte zásadu nepoužívají a vyberte **OK**.

      ![Vyberte šifrovaných virtuálních počítačů](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Pokud používáte Azure Key Vault, na stránce úložiště, zobrazí se zpráva, že Azure Backup měl přístup jen pro čtení pro klíče a tajné kódy ve službě Key Vault.

    - Pokud se zobrazí tato zpráva, nemusíte nic dělat.

        ![Access OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Pokud se zobrazí tato zpráva, je potřeba nastavit oprávnění, jak je popsáno v [níže uvedeného postupu](#provide-permissions).

        ![Přístup k upozornění](./media/backup-azure-vms-encryption/access-warning.png)

9. Klikněte na tlačítko **povolit zálohování** nasazovat zásady zálohování v trezoru a povolení zálohování pro vybrané virtuální počítače.


## <a name="trigger-a-backup-job"></a>Aktivace úlohy zálohování

Prvotní zálohování se spustí podle plánu, ale může spouštět je okamžitě následujícím způsobem:

1. V nabídce trezoru klikněte na tlačítko **zálohování položek**.
2. V **zálohování položek** klikněte na tlačítko **virtuálního počítače Azure**.
3. V **zálohování položek** klikněte na symbol tří teček (...).
4. Klikněte na tlačítko **zálohovat nyní**.
5. V **zálohovat nyní**, pomocí ovládacího prvku kalendáře vyberte poslední den uchování bodu obnovení. Pak klikněte na **OK**.
6. Sledujte oznámení o portálu. Můžete sledovat průběh úlohy v řídicím panelu trezoru > **úlohy zálohování** > **probíhá**. V závislosti na velikosti virtuálního počítače může vytváření prvotní zálohy chvíli trvat.


## <a name="provide-permissions"></a>Zadejte oprávnění

Virtuální počítač Azure potřebuje přístup jen pro čtení k zálohování klíčů a tajných kódů, spolu s přidružených virtuálních počítačích.

- Key Vault je přidružený k tenantovi Azure AD předplatného Azure. Pokud jste **uživatel členem**, Azure Backup získává přístup ke službě Key Vault bez další akce.
- Pokud jste **uživatele typu Host**, je nutné zadat oprávnění pro službu Azure Backup pro přístup k trezoru klíčů.

Chcete-li nastavit oprávnění:

1. Na webu Azure Portal, vyberte **všechny služby**a vyhledejte **trezory klíčů**.
2. Vyberte trezor klíčů, které jsou přidružené k šifrovaný virtuální počítač zálohujete.
3. Vyberte **zásady přístupu** > **přidat nový**.
4. Vyberte **vybrat objekt zabezpečení**a pak zadejte **správy zálohování**.
5. Vyberte **správu Služba Backup** > **vyberte**.

    ![Výběr služby Backup](./media/backup-azure-vms-encryption/select-backup-service.png)

6. V **přidat zásady přístupu** > **konfigurace ze šablony (volitelné)** vyberte **Azure Backup**.
    - Požadovaná oprávnění jsou předem pro **oprávnění klíče** a **oprávnění tajného klíče**.
    - Pokud je váš virtuální počítač šifrovaný pomocí **pouze klíče BEK**, odebrat výběr **oprávnění klíče** vzhledem k tomu potřebujete oprávnění pouze pro tajné kódy.

    ![Azure backup výběr](./media/backup-azure-vms-encryption/select-backup-template.png)

6. Klikněte na **OK**. **Služba správy zálohování** se přidá do **zásady přístupu**.

    ![Zásady přístupu](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

7. Klikněte na tlačítko **Uložit** Azure Backup poskytnout oprávnění.

## <a name="restore-an-encrypted-vm"></a>Obnovení šifrovaných virtuálních počítačů

Obnovení šifrovaných virtuálních počítačů následujícím způsobem:

1. [Obnovení disku virtuálního počítače](backup-azure-arm-restore-vms.md#restore-disks).
2. Udělejte jednu z následujících akcí:
    - Pomocí šablony, který je generován během operace obnovení nastavení virtuálního počítače, a aktivovat nasazení virtuálního počítače. [Další informace](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    - Vytvořte nový virtuální počítač z obnovených disků pomocí Powershellu. [Další informace](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="next-steps"></a>Další postup

Pokud narazíte na nějaké problémy, projděte si

- [Běžné chyby](backup-azure-vms-troubleshoot.md) při zálohování a obnovení šifrovaných virtuálních počítačů Azure.
- [Rozšíření Azure VM agent/záloha](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) problémy.
