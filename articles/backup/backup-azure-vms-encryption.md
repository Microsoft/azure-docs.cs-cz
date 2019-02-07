---
title: Zálohování a obnovení šifrovaných virtuálních počítačů pomocí Azure Backup
description: Tento článek pojednává o zálohování a obnovení prostředí pro virtuální počítače jsou šifrované pomocí Azure Disk Encryption.
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 7/10/2018
ms.author: geetha
ms.openlocfilehash: 676c6a45f4a3930d350bbcbdcbb1a0fb47880407
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55809993"
---
# <a name="back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>Zálohování a obnovení šifrovaných virtuálních počítačů pomocí služby Azure Backup
Tento článek se hovoří o tom, jak zálohovat a obnovovat virtuální počítače (VM) s využitím Azure Backup. Poskytuje také informace o podporované scénáře, požadavky a kroky při řešení potíží pro případy chyb.

## <a name="supported-scenarios"></a>Podporované scénáře

 Zálohování a obnovení šifrovaných virtuálních počítačů se podporuje jenom pro virtuální počítače, které používají model nasazení Azure Resource Manageru. Nepodporuje se pro virtuální počítače, které používají model nasazení classic. Zálohování a obnovení šifrovaných virtuálních počítačů je podporováno pro Windows a virtuální počítače s Linuxem používající Azure Disk Encryption. Disk Encryption používá funkci odvětví standardní BitLocker systému Windows a funkci dm-crypt systému Linux pro zajištění šifrování disků. V následující tabulce jsou uvedeny typ šifrování a podporu pro virtuální počítače.

   |  | Klíč BEK a KEK virtuálních počítačů | Klíč BEK jen pro virtuální počítače |
   | --- | --- | --- |
   | **Nespravovaná virtuálních počítačů**  | Ano | Ano  |
   | **Spravované virtuální počítače**  | Ano | Ano  |

   > [!NOTE]
   > Azure Backup podporuje virtuální počítače jsou šifrované pomocí klíče samostatné. Libovolné klávesy, která je součástí certifikát použitý k šifrování virtuálního počítače nepodporuje ještě dnes.
   >   

## <a name="prerequisites"></a>Požadavky
* Virtuální počítač byl zašifrován pomocí [Azure Disk Encryption](../security/azure-security-disk-encryption.md).

* Vytvoření trezoru služby Recovery Services a úložiště replikace byla nastavena podle postupu v [Příprava prostředí pro zálohování](backup-azure-arm-vms-prepare.md).

* Zálohování byla udělena oprávnění pro přístup k trezoru klíčů, který obsahuje klíče a tajné kódy pro šifrované virtuální počítače.

## <a name="backup-encrypted-vm"></a>Zálohování šifrovaných virtuálních počítačů
Pomocí následujících kroků pro nastavení cíle zálohování, definovat zásady, konfigurace položky a aktivujte zálohování.

### <a name="configure-backup"></a>Konfigurace zálohování
1. Pokud už máte trezor služby Recovery Services, otevřít, pokračujte k dalšímu kroku. Pokud nemáte trezor služby Recovery Services, otevřít, ale jste na webu Azure Portal, vyberte **všechny služby**.

   a. V seznamu prostředků zadejte **Recovery Services**.

   b. Seznam se průběžně filtruje podle zadávaného textu. Když se zobrazí **trezory služby Recovery Services**, vyberte ji.

      ![Trezor služby Recovery Services](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

    c. Objeví se seznam trezorů Služeb zotavení. Vyberte trezor ze seznamu.

     Otevře se řídicí panel vybraného trezoru.
1. V seznamu položek, které se zobrazí v části trezor, vyberte **zálohování** spustit zálohování šifrovaných virtuálních počítačů.

      ![Okno zálohování](./media/backup-azure-vms-encryption/select-backup.png)
1. Na **zálohování** dlaždice, vyberte **cíl zálohování**.

      ![Okno scénář](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
1. V části **ve kterém je spuštěná vaše úloha?** vyberte **Azure**. V části **co chcete zálohovat?** vyberte **virtuálního počítače**. Pak vyberte **OK**.

   ![Otevřené okno Scénář](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
1. V části **výběr zásady zálohování**, vyberte zásadu zálohování, která má být použita k trezoru. Pak vyberte **OK**.

      ![Výběr zásady zálohování](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    Podrobnosti výchozí zásady jsou uvedené. Pokud chcete vytvořit zásadu, vyberte **vytvořit nový** z rozevíracího seznamu. Po výběru **OK**, zásadu zálohování, která je přidružená k trezoru.

1. Zvolte šifrované virtuální počítače, který chcete přidružit k určené zásadě a vyberte **OK**.

      ![Vyberte šifrovaných virtuálních počítačů](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
1. Tato stránka zobrazuje zpráva o trezorů klíčů, které jsou přidružené k šifrovaných virtuálních počítačů, které jste vybrali. Zálohování vyžaduje, aby přístup jen pro čtení pro klíče a tajné kódy ve službě key vault. Tato oprávnění se používá k zálohování klíčů a tajných kódů, spolu s přidružených virtuálních počítačích.<br>
Pokud jste **uživatel členem**, proces povolení zálohování bude bez problémů získat přístup k trezoru klíčů pro zálohování šifrovaných virtuálních počítačů bez nutnosti zásahu uživatele.

   ![Zprávy šifrované virtuální počítače](./media/backup-azure-vms-encryption/member-user-encrypted-vm-warning-message.png)

   Pro **uživatele typu Host**, je nutné zadat oprávnění ke službě zálohování přístup k trezoru klíčů pro zálohy do práce. Tato oprávnění můžete zadat pomocí následujících kroků uvedených v následující části

   ![Zprávy šifrované virtuální počítače](./media/backup-azure-vms-encryption/guest-user-encrypted-vm-warning-message.png)

    Teď, když jste definovali všechna nastavení trezoru, vyberte **povolit zálohování** v dolní části stránky. **Povolit zálohování** nasadíte tuto zásadu pro trezor a virtuální počítače.

1. V další fázi v rámci přípravy je instalace agenta virtuálního počítače nebo zajistit, že Agent virtuálního počítače je nainstalována. Stejný postup provést, postupujte podle kroků v [Příprava prostředí pro zálohování](backup-azure-arm-vms-prepare.md).

### <a name="trigger-a-backup-job"></a>Aktivace úlohy zálohování
Postupujte podle kroků v [zálohování virtuálních počítačů Azure do trezoru služby Recovery Services](backup-azure-arm-vms.md) k aktivaci úlohy zálohování.

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>Pokračovat v zálohování už zálohovaných virtuálních počítačů s povoleným šifrováním  
Pokud máte virtuální počítače už se zálohovat v trezoru služby Recovery Services, u kterých jde pro šifrování později, je nutné udělit oprávnění k zálohování pro přístup k trezoru klíčů pro zálohování, abyste mohli pokračovat. Tato oprávnění můžete zadat pomocí následujících [kroky v části](#provide-permissions-to-azure-backup). Nebo můžete pomocí prostředí PowerShell kroků v části "Povolit zálohování" [dokumentaci k Powershellu](backup-azure-vms-automation.md).

## <a name="provide-permissions-to-azure-backup"></a>Zadejte oprávnění k zálohování
Následujícím postupem zajistit odpovídající oprávnění k zálohování pro přístup k trezoru klíčů a provést zálohování šifrovaných virtuálních počítačů.
1. Vyberte **všechny služby**a vyhledejte **trezory klíčů**.

    ![Trezory klíčů](./media/backup-azure-vms-encryption/search-key-vault.png)

1. V seznamu trezorů klíčů vyberte trezor klíčů, které jsou přidružené k šifrovaných virtuálních počítačů, které je potřeba zálohovat.

     ![Výběr služby Key vault](./media/backup-azure-vms-encryption/select-key-vault.png)

1. Vyberte **zásady přístupu**a pak vyberte **přidat nový**.

    ![Přidat nové](./media/backup-azure-vms-encryption/select-key-vault-access-policy.png)

1. Vyberte **vybrat objekt zabezpečení**a pak zadejte **Správa služby Backup** do vyhledávacího pole.

    ![Zálohování služby search](./media/backup-azure-vms-encryption/search-backup-service.png)

1. Vyberte **Správa služby Backup**a pak vyberte **vyberte**.

    ![Výběr služby Backup](./media/backup-azure-vms-encryption/select-backup-service.png)

1. V části **konfigurace ze šablony (volitelné)** vyberte **Azure Backup**. Požadovaná oprávnění jsou předem pro **oprávnění klíče** a **oprávnění tajného klíče**. Pokud je váš virtuální počítač šifrovaný pomocí **pouze klíče BEK**, oprávnění pouze pro tajné klíče jsou povinné, proto je nutné odebrat výběr **oprávnění klíče**.

    ![Azure backup výběr](./media/backup-azure-vms-encryption/select-backup-template.png)

1. Vyberte **OK**. Všimněte si, že **Správa služby Backup** přidá v **zásady přístupu**.

    ![Zásady přístupu](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

1. Vyberte **Uložit** poskytnout požadovaná oprávnění k zálohování.

    ![Zásady zálohování přístupu](./media/backup-azure-vms-encryption/save-access-policy.png)

Po úspěšně jsou k dispozici oprávnění, můžete pokračovat s povolením backup pro šifrované virtuální počítače.

## <a name="restore-an-encrypted-vm"></a>Obnovení šifrovaných virtuálních počítačů
Obnovení Azure Backup nyní podporuje [Azure šifrovaný virtuální počítač bez Azure AD](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-prerequisites-aad) kromě předchozí nabídky obnovení podpory k Azure šifrovaný virtuální počítač s Azure AD.<br>

Chcete-li obnovit šifrovaný virtuální počítač, nejdříve obnovit disky podle postupu v části "Obnovte zálohovanou disky" v [zvolte virtuální počítač obnovit konfiguraci](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Potom můžete použít jednu z následujících možností:

* Postupujte podle kroků jako v prostředí PowerShell [vytvořit virtuální počítač z obnovených disků](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) vytvoří úplný virtuální počítač z obnovených disků.
* Nebo, [použijte šablony k přizpůsobení obnovený virtuální počítač](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm) k vytvoření virtuálních počítačů z obnovených disků. Šablony lze používat pouze pro body obnovení vytvořené po 26. dubna 2017.

## <a name="troubleshooting-errors"></a>Řešení chyb
| Operace | Podrobnosti o chybě | Řešení |
| --- | --- | --- |
|Backup | Kód chyby: UserErrorKeyVaultPermissionsNotConfigured<br><br>Chybová zpráva: Služba Azure Backup nemá dostatečná oprávnění pro Key Vault pro zálohování šifrovaných virtuálních počítačů. | Zálohování by měl poskytovat tato oprávnění po [kroků v předchozí části](#provide-permissions-to-azure-backup). Nebo můžete pomocí prostředí PowerShell kroků v části "Povolení ochrany" v článku [použití Powershellu k zálohování a obnovení virtuálních počítačů](backup-azure-vms-automation.md#enable-protection). |  
| Obnovení | Tento šifrovaný virtuální počítač nelze obnovit, protože trezor klíčů, které jsou přidružené k tomuto virtuálnímu počítači neexistuje. |Vytvoření trezoru klíčů pomocí [Začínáme s Azure Key Vault](../key-vault/key-vault-get-started.md). Zobrazit [obnovení služby key vault klíč a tajný klíč, a to pomocí služby Azure Backup](backup-azure-restore-key-secret.md) obnovit klíč a tajný klíč, pokud nejsou k dispozici. |
| Obnovení | Kód chyby: UserErrorKeyVaultKeyDoesNotExist<br><br> Chybová zpráva: Tento šifrovaný virtuální počítač nemůžete obnovit, protože neexistuje klíč přidružený k tomuto virtuálnímu počítači. |Zobrazit [obnovení služby key vault klíč a tajný klíč, a to pomocí služby Azure Backup](backup-azure-restore-key-secret.md) obnovit klíč a tajný klíč, pokud nejsou k dispozici. |
| Obnovení | Kód chyby: ProviderAuthorizationFailed/UserErrorProviderAuthorizationFailed<br><br>Chybová zpráva: Služba zálohování nemá oprávnění pro přístup k prostředkům ve vašem předplatném. |Jak už bylo zmíněno dříve, obnovte disky nejprve podle postupu v části "Obnovte zálohovanou disky" v [zvolte virtuální počítač obnovit konfiguraci](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Potom použijte PowerShell [vytvořit virtuální počítač z obnovených disků](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). |
