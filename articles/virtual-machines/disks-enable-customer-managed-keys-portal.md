---
title: Azure Portal – povolení klíčů spravovaných zákazníkem pomocí disků spravovaných SSE
description: Povolte na svých spravovaných discích pomocí Azure Portal klíče spravované zákazníky.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 7fbcf37620f9d6edfb0f312c2eb09ef2d7994232
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731632"
---
# <a name="use-the-azure-portal-to-enable-server-side-encryption-with-customer-managed-keys-for-managed-disks"></a>Použití Azure Portal k povolení šifrování na straně serveru pomocí klíčů spravovaných zákazníkem pro spravované disky

Pokud zvolíte možnost použití šifrování na straně serveru (SSE) pro spravované disky, umožňuje vám Azure Disk Storage spravovat vlastní klíče. Koncepční informace o SSE se spravovanými klíči zákazníků a s jinými typy šifrování spravovaného disku najdete v části **klíče spravované zákazníkem** v článku šifrování disku:

- Pro Linux: [klíče spravované zákazníkem](./disk-encryption.md#customer-managed-keys)
- Pro Windows: [klíče spravované zákazníkem](./disk-encryption.md#customer-managed-keys)

## <a name="restrictions"></a>Omezení

Klíče spravované zákazníkem teď mají následující omezení:

- Pokud je tato funkce pro disk povolená, nemůžete ji zakázat.
    Pokud potřebujete tento problém obejít, musíte všechna data zkopírovat na zcela jiný spravovaný disk, který nepoužívá klíče spravované zákazníkem:

    - Pro Linux: [zkopírování spravovaného disku](./linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)

    - Pro Windows: [zkopírování spravovaného disku](./windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)

[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

Následující části obsahují informace o tom, jak povolit a používat klíče spravované zákazníkem pro spravované disky:

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Nasazení virtuálního počítače

Teď, když jste vytvořili a nastavili Trezor klíčů a sadu šifrování disků, můžete nasadit virtuální počítač pomocí šifrování.
Proces nasazení virtuálního počítače se podobá standardnímu procesu nasazení. jediným rozdílem je, že je potřeba nasadit virtuální počítač ve stejné oblasti, ve které se nacházejí vaše další prostředky, a Vy se rozhodnete použít spravovaný klíč zákazníka.

1. Vyhledejte **Virtual Machines** a vyberte **+ Přidat** a vytvořte virtuální počítač.
1. V okně **základní** vyberte stejnou oblast jako nastavení šifrování disku a Azure Key Vault.
1. V okně **Basic** vyplňte jiné hodnoty, jak chcete.

    ![Snímek obrazovky s možností vytváření virtuálních počítačů s zvýrazněnou hodnotou oblasti](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. V okně **disky** vyberte možnost **šifrování v klidovém kódu s klíčem spravovaným zákazníkem**.
1. V rozevíracím seznamu **sada Encryption disk** vyberte sadu šifrování disku.
1. Vyberte zbývající výběr.

    ![Snímek obrazovky s prostředím pro vytváření virtuálních počítačů, okno disky Zvýrazněný rozevírací seznam sady Disk Encryption](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>Povolit na stávajícím disku

> [!CAUTION]
> Povolení šifrování disku na všech discích připojených k VIRTUÁLNÍm počítačům bude vyžadovat, abyste virtuální počítač zastavili.
    
1. Přejděte do virtuálního počítače, který je ve stejné oblasti jako jedna ze sad pro šifrování disků.
1. Otevřete virtuální počítač a vyberte **zastavit**.

    ![Snímek obrazovky hlavního překryvu pro ukázkový virtuální počítač se zvýrazněným tlačítkem STOP](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. Po ukončení činnosti virtuálního počítače vyberte **disky** a pak vyberte disk, který chcete zašifrovat.

    ![Snímek obrazovky s ukázkovým VIRTUÁLNÍm počítačem a otevře se okno disky. Disk s operačním systémem se zvýrazní, jako příklad disku, který chcete vybrat.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. Vyberte **šifrování** a vyberte možnost **šifrování v klidovém kódu pomocí klíče spravovaného zákazníkem** a potom v rozevíracím seznamu vyberte svou sadu Disk Encryption.
1. Vyberte **Uložit**.

    ![Snímek obrazovky s ukázkovým diskem s operačním systémem. Otevře se okno šifrování, vybral se šifrování v klidovém formátu s klíčem spravovaným zákazníkem a také vaše příklad Azure Key Vault. Po provedení těchto výběrů je vybráno tlačítko Uložit.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. Tento postup opakujte pro všechny ostatní disky připojené k virtuálnímu počítači, který chcete zašifrovat.
1. Pokud disky dokončí přepínání na klíče spravované zákazníkem a žádné další připojené disky nechcete zašifrovat, můžete svůj virtuální počítač spustit.

> [!IMPORTANT]
> Klíče spravované zákazníkem spoléhají na spravované identity prostředků Azure, což je funkce Azure Active Directory (Azure AD). Při konfiguraci klíčů spravovaných zákazníkem se spravovaná identita automaticky přiřadí k vašim prostředkům v rámci pokrývání. Pokud později přesunete předplatné, skupinu prostředků nebo spravovaný disk z jednoho adresáře služby Azure AD do jiného, nepřesune se do nového tenanta spravovaná identita přidružená ke spravovaným diskům, takže klíče spravované zákazníkem už možná nebudou fungovat. Další informace najdete v tématu [přenos předplatného mezi adresáři služby Azure AD](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Další kroky

- [Prozkoumejte šablony Azure Resource Manager pro vytváření šifrovaných disků pomocí klíčů spravovaných zákazníkem](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Co je Azure Key Vault?](../key-vault/general/overview.md)
- [Replikace počítačů s povolenými disky spravovanými zákazníky](../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Nastavení zotavení po havárii virtuálních počítačů VMware do Azure s využitím PowerShellu](../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Nastavení zotavení po havárii do Azure pro virtuální počítače Hyper-V s využitím PowerShellu a Azure Resource Manageru](../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)