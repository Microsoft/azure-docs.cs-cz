---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 165a115f35810c1bfe463a571affb2e44ed74205
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893721"
---
### <a name="portal"></a>Portál

Nastavení klíčů spravovaných zákazníkem pro vaše disky bude vyžadovat, abyste vytvořili prostředky v určitém pořadí, pokud je v tuto chvíli vytváříte poprvé. Nejprve budete muset vytvořit a nastavit Azure Key Vault.

#### <a name="setting-up-your-azure-key-vault"></a>Nastavení Azure Key Vault

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) a vyhledejte Key Vault
1. Vyhledejte a vyberte **trezory klíčů**.

![SSE-Key-Vault-Portal-Search. png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)

> [!IMPORTANT]
> Váš Trezor klíčů Azure, sada šifrování disků, virtuální počítač, disky a snímky musí být všechny ve stejné oblasti a předplatném, aby nasazení bylo úspěšné.

1. Vyberte **+ Přidat** a vytvořte novou Key Vault.
1. Vytvořte novou skupinu prostředků
1. Zadejte název trezoru klíčů, vyberte oblast a vyberte cenovou úroveň.
1. Vyberte **zkontrolovat + vytvořit**, ověřte své volby a pak vyberte **vytvořit**.

![SSE-Create-a-Key-Vault. png](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. Až se váš Trezor klíčů dokončí, vyberte ho.
1. V části **Nastavení**vyberte **klíče** .
1. Vybrat **vygenerovat/importovat**

![SSE-Key-Vault-Generate-Settings. png](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. U obou **typů klíčů** ponechte nastavenou hodnotu **RSA** a **velikost klíče RSA** na hodnotu **2080**.
1. Vyplňte zbývající výběr podle vašich pokynů a pak vyberte **vytvořit**.

![SSE-Create-a-Key-Generate. png](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>Nastavení sady pro šifrování disku

Chcete-li vytvořit a nakonfigurovat sady pro šifrování disků, je nutné použít následující odkaz: https://aka.ms/diskencryptionsets. Vytvoření sady šifrování disku ještě není k dispozici ve veřejném Azure Portal.

1. Otevřete [odkaz sady Disk Encryption](https://aka.ms/diskencryptionsets).
1. Vyberte **+ Přidat**.

![SSE-Create-Disk-Encryption-set. png](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. Vyberte skupinu prostředků, pojmenujte sadu šifrování a vyberte stejnou oblast jako Trezor klíčů.
1. Vyberte **Trezor klíčů a klíč**.
1. Vyberte Trezor klíčů a klíč, který jste vytvořili dříve, a také verzi.
1. Stiskněte **Vybrat**.
1. Vyberte **zkontrolovat + vytvořit** a pak **vytvořit**.

![SSE-disk-ENC-set-Blade-Key. png](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. Jakmile se dokončí vytváření, otevřete sadu šifrování disku a vyberte výstrahu, která se objeví.

![SSE-disk-ENC-Alert-Fix. png](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

Měla by se zobrazit a úspěšně zobrazit dvě oznámení. Díky tomu budete moct použít sadu s vaším trezorem klíčů.

![disk-ENC-Notification-success. png](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>Nasazení virtuálního počítače

Teď, když jste vytvořili a nastavili Trezor klíčů a sadu šifrování disků, můžete nasadit virtuální počítač pomocí šifrování.
Proces nasazení virtuálního počítače se podobá standardnímu procesu nasazení. jediným rozdílem je, že je potřeba nasadit virtuální počítač ve stejné oblasti, ve které se nacházejí vaše další prostředky, a Vy se rozhodnete použít spravovaný klíč zákazníka.

1. Vyhledejte **Virtual Machines** a vyberte **+ Přidat** a vytvořte virtuální počítač.
1. Na kartě **základní** vyberte stejnou oblast jako sadu šifrování disku a Azure Key Vault.
1. Zadejte další hodnoty na kartě **Basic** , jak chcete.

![SSE-Create-a-VM-region. png](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. Na kartě **disky** vyberte možnost **šifrování v klidovém kódu s klíčem spravovaným zákazníkem**.
1. V rozevíracím seznamu **sada Encryption disk** vyberte sadu šifrování disku.
1. Vyberte zbývající výběr.

![SSE-Create-VM-Select-CMK-Encryption-set. png](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)