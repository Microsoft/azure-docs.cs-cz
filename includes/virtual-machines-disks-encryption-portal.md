---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a8702f14bfb9aed5a3595203dde1d86ea56a151b
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133807"
---
[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

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

    ![Snímek obrazovky s hlavním překryvem pro ukázkový virtuální počítač Se zvýrazněným tlačítkem STOP](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. Po ukončení činnosti virtuálního počítače vyberte **disky** a pak vyberte disk, který chcete zašifrovat.

    ![Snímek obrazovky s ukázkovým VIRTUÁLNÍm počítačem a otevře se okno disky. Disk s operačním systémem se zvýrazní, jako příklad disku, který chcete vybrat.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. Vyberte **šifrování** a vyberte možnost **šifrování v klidovém kódu pomocí klíče spravovaného zákazníkem** a potom v rozevíracím seznamu vyberte svou sadu Disk Encryption.
1. Vyberte **Uložit**.

    ![Snímek obrazovky s ukázkovým diskem s operačním systémem. Otevře se okno šifrování, vybral se šifrování v klidovém formátu s klíčem spravovaným zákazníkem a také vaše příklad Azure Key Vault. Po provedení těchto výběrů je vybráno tlačítko Uložit.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. Tento postup opakujte pro všechny ostatní disky připojené k virtuálnímu počítači, který chcete zašifrovat.
1. Pokud disky dokončí přepínání na klíče spravované zákazníkem a žádné další připojené disky nechcete zašifrovat, můžete svůj virtuální počítač spustit.
