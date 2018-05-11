---
title: Zálohování a obnovení šifrovaných virtuálních počítačů pomocí Azure Backup
description: Tento článek komunikuje o činnosti zálohování a obnovení pro virtuální počítače, šifrován pomocí Azure Disk Encryption.
services: backup
documentationcenter: ''
author: JPallavi
manager: vijayts
editor: ''
ms.assetid: 8387f186-7d7b-400a-8fc3-88a85403ea63
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/13/2017
ms.author: pajosh;markgal;trinadhk; sogup
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c788720e046c2efef954ef77f7b52854439b7515
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>Zálohování a obnovení šifrovaných virtuálních počítačů s Azure Backup
V tomto článku bude zmíněn kroky pro zálohování a obnovení virtuálních počítačů (VM) pomocí Azure Backup. Také poskytuje podrobnosti o podporovaných scénářích, požadavky a řešení potíží pro chybových případech.

## <a name="supported-scenarios"></a>Podporované scénáře

 * Zálohování a obnovení šifrovaných virtuálních počítačů je podporována pouze pro virtuální počítače, které používají model nasazení Azure Resource Manager. Není podporována u virtuálních počítačů, které používají model nasazení classic. <br>
 * Zálohování a obnovení šifrovaných virtuálních počítačů je podporována pro systém Windows a virtuální počítače s Linuxem, použít Azure Disk Encryption. K poskytování šifrování disků používá šifrování disku oborový standard BitLocker funkce systému Windows a dm-crypt funkce systému Linux. <br>
 
 Následující tabulka uvádí podporované scénáře pro nástroj BitLocker šifrovací klíč (BEK) – jenom a klíč šifrovacího klíče (KEK) - šifrované virtuální počítače:
 
 
   |  | BEK + KEK virtuální počítače | Virtuální počítače jen BEK |
   | --- | --- | --- |
   | **Nespravovaná virtuální počítače**  | Ano | Ano  |
   | **Spravovaných virtuálních počítačů**  | Ano | Ano  |

## <a name="prerequisites"></a>Požadované součásti
* Virtuální počítač byl zašifrován pomocí [Azure Disk Encryption](../security/azure-security-disk-encryption.md).

* Byl vytvořen trezoru služeb zotavení a replikace úložiště byla nastavena pomocí následujících kroků v [Příprava prostředí pro zálohování](backup-azure-arm-vms-prepare.md).

* Byl zadán zálohování [oprávnění pro přístup k trezoru klíčů](#provide-permissions-to-backup) obsahující klíče a tajné klíče pro šifrovaná virtuálních počítačů.

## <a name="backup-encrypted-vm"></a>Šifrované zálohování virtuálních počítačů
Pomocí následujících kroků nastavte cíle zálohování, definovat zásady, konfigurovat položky a spustit zálohování.

### <a name="configure-backup"></a>Konfigurace zálohování
1. Pokud již máte otevřete trezoru služeb zotavení, pokračujte dalším krokem. Pokud nemáte trezoru služeb zotavení otevřít, ale jste na portálu Azure, vyberte **všechny služby**.

   a. V seznamu prostředků zadejte **Recovery Services**.

   b. Seznam se průběžně filtruje podle zadávaného textu. Až se zobrazí **trezory služeb zotavení**, vyberte ho.

      ![Trezor služby Recovery Services](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

    c. Objeví se seznam trezorů Služeb zotavení. Ze seznamu vyberte trezor.

     Otevře se řídicí panel vybraného trezoru.
2. Ze seznamu položek, která se zobrazí pod trezoru, vyberte **zálohování** spustit zálohování šifrované virtuálního počítače.

      ![Okno zálohování](./media/backup-azure-vms-encryption/select-backup.png)
3. Na **zálohování** dlaždice, vyberte **cíl zálohování**.

      ![Okno scénář](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
4. V části **kde běží vaše úloha?**, vyberte **Azure**. V části **co chcete zálohovat?**, vyberte **virtuální počítač**. Pak vyberte **OK**.

   ![Otevřené okno Scénář](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
5. V části **vyberte zásady zálohování**, vyberte zásadu zálohování, které chcete použít pro trezor. Pak vyberte **OK**.

      ![Výběr zásady zálohování](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    Jsou uvedeny podrobnosti o výchozí zásady. Pokud chcete vytvořit zásadu, vyberte **vytvořit nový** z rozevíracího seznamu. Po výběru **OK**, je zásada zálohování přidružená k trezoru.

6. Vyberte šifrované virtuální počítače, který chcete přidružit k určené zásadě a vyberte **OK**.

      ![Vyberte šifrované virtuální počítače](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. Tato stránka zobrazuje zprávu o trezorů klíčů, které jsou přidružené k šifrované virtuální počítače jste vybrali. Zálohování vyžaduje přístup jen pro čtení k klíčů a tajných klíčů v trezoru klíčů. Používá tato oprávnění k zálohování klíče a tajné klíče, společně s přidružené virtuální počítače.<br>
Pokud jste **uživatelské členských**, proces povolení zálohování bude bezproblémově získat přístup k trezoru klíčů k zálohování šifrované virtuálních počítačů bez nutnosti zásahu uživatele.

   ![Šifrované zprávy virtuální počítače](./media/backup-azure-vms-encryption/member-user-encrypted-vm-warning-message.png)

   Pro **uživatele Guest**, je nutné zadat oprávnění ke službě zálohování přístup k trezoru klíčů pro zálohy pro práci. Tato oprávnění můžete zadat pomocí následujících [kroků uvedených v následující části](#provide-permissions-to-backup)

   ![Šifrované zprávy virtuální počítače](./media/backup-azure-vms-encryption/guest-user-encrypted-vm-warning-message.png)
 
    Teď, když jste definovali všechna nastavení trezoru, vyberte **povolit zálohování** v dolní části stránky. **Povolení zálohování** nasadí zásadu pro trezor a virtuální počítače.
  
8. V další fázi v rámci přípravy je instalace agenta virtuálního počítače nebo zajistit, že Agent virtuálního počítače je nainstalovaný. Stejné, postupujte podle kroků v [Příprava prostředí pro zálohování](backup-azure-arm-vms-prepare.md).

### <a name="trigger-a-backup-job"></a>Aktivuje úlohu zálohování
Postupujte podle kroků v [zálohování virtuálních počítačů Azure do trezoru služeb zotavení](backup-azure-arm-vms.md) se spustit úlohu zálohování.

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>Zálohování virtuálních počítačů už zálohovaná pokračujte šifrování povoleno.  
Pokud máte virtuální počítače již probíhá zálohování do trezoru služeb zotavení, které jsou povolené pro šifrování později, musí udělit oprávnění k zálohování pro přístup k trezoru klíčů pro zálohování pokračovat. Tato oprávnění můžete zadat pomocí následujících [kroky v následující části](#provide-permissions-to-azure-backup). Nebo můžete provést kroky pro prostředí PowerShell v části "Povolit zálohování" [prostředí PowerShell dokumentaci](backup-azure-vms-automation.md). 

## <a name="provide-permissions-to-backup"></a>Zadejte oprávnění k zálohování
Pomocí následujících kroků k poskytování odpovídající oprávnění pro přístup k trezoru klíčů a zálohování virtuálních počítačů šifrované zálohování.
1. Vyberte **všechny služby**a vyhledejte **klíče trezory**.

    ![Trezory klíčů](./media/backup-azure-vms-encryption/search-key-vault.png)
    
2. Ze seznamu trezorů klíčů vyberte trezor klíčů, které jsou přidružené k šifrované virtuální počítač, který je možné zálohovat.

     ![Výběr trezoru klíčů](./media/backup-azure-vms-encryption/select-key-vault.png)
     
3. Vyberte **zásady přístupu**a potom vyberte **přidat nový**.

    ![Přidat nový](./media/backup-azure-vms-encryption/select-key-vault-access-policy.png)
    
4. Vyberte **vyberte objekt**a pak zadejte **služba správy zálohování** do vyhledávacího pole. 

    ![Vyhledávání služby zálohování](./media/backup-azure-vms-encryption/search-backup-service.png)
    
5. Vyberte **služba správy zálohování**a potom vyberte **vyberte**.

    ![Výběr zálohování služby](./media/backup-azure-vms-encryption/select-backup-service.png)
    
6. V části **konfigurace z šablony (volitelné)**, vyberte **Azure Backup**. Požadovaná oprávnění jsou předem pro **klíče oprávnění** a **tajný oprávnění**. Pokud virtuální počítač se šifrují pomocí **BEK pouze**, oprávnění jen pro tajné klíče jsou povinné, takže je nutné odebrat výběr **klíče oprávnění**.

    ![Azure výběru zálohování](./media/backup-azure-vms-encryption/select-backup-template.png)
    
7. Vyberte **OK**. Všimněte si, že **služba správy zálohování** získá přidali v **zásady přístupu**. 

    ![Zásady přístupu](./media/backup-azure-vms-encryption/backup-service-access-policy.png)
    
8. Vyberte **Uložit** umožňuje potřebná oprávnění k zálohování.

    ![Zásady zálohování přístupu](./media/backup-azure-vms-encryption/save-access-policy.png)

Po poskytované úspěšně oprávnění, můžete pokračovat s povolením zálohování pro virtuální počítače šifrovaná.

## <a name="restore-an-encrypted-vm"></a>Obnovení šifrovaných virtuálních počítačů
Pro obnovení šifrovaných virtuálních počítačů, nejprve obnovit disky pomocí kroků v části "Obnovení zálohovaných disky" v [zvolte obnovení konfigurace virtuálního počítače](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Potom můžete použít jednu z následujících možností:

* Postupujte podle kroků prostředí PowerShell v [vytvoření virtuálního počítače z obnovené disků](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) vytvoření úplné virtuálního počítače z obnovené disků.
* Nebo, [pomocí šablony můžete upravit obnovený virtuální počítač](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm) k vytvoření virtuálních počítačů z obnovené disků. Šablony lze použít pouze pro body obnovení vytvořené po 26. dubna 2017.

## <a name="troubleshooting-errors"></a>Řešení chyb
| Operace | Podrobnosti o chybě | Řešení |
| --- | --- | --- |
|Backup | Zálohování nemá dostatečná oprávnění k trezoru klíčů pro zálohování virtuálních počítačů. šifrovaná. | Zálohování je nutné zadat položek tato oprávnění podle [kroky v předchozí části](#provide-permissions-to-azure-backup). Nebo můžete provést kroky pro prostředí PowerShell v části "Povolení ochrany" v dokumentaci k prostředí PowerShell na [AzureRM.RecoveryServices.Backup použití rutiny pro zálohování virtuálních počítačů](backup-azure-vms-automation.md#back-up-azure-vms). |  
| Obnovit |Tento šifrovaný virtuální počítač nelze obnovit, protože neexistuje v trezoru klíčů, které jsou přidružené k tento virtuální počítač. |Vytvoření trezoru klíčů pomocí [Začínáme s Azure Key Vault](../key-vault/key-vault-get-started.md). V tématu [obnovení klíče trezoru klíčů a tajný klíč pomocí Azure Backup](backup-azure-restore-key-secret.md) obnovit klíč a tajný klíč, pokud jejich nejsou k dispozici. |
| Obnovit |Tento šifrovaný virtuální počítač nelze obnovit, protože klíč a tajný klíč přidružený tohoto virtuálního počítače neexistují. |V tématu [obnovení klíče trezoru klíčů a tajný klíč pomocí Azure Backup](backup-azure-restore-key-secret.md) obnovit klíč a tajný klíč, pokud jejich nejsou k dispozici. |
| Obnovit |Zálohování nemá oprávnění pro přístup k prostředkům ve vašem předplatném. |Jak je uvedeno nahoře, obnovit disky nejprve pomocí kroků v části "Obnovení zálohovaných disky" v [zvolte obnovení konfigurace virtuálního počítače](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Potom pomocí prostředí PowerShell k [vytvoření virtuálního počítače z obnovené disků](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). |
