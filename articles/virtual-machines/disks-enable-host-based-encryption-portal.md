---
title: Povolení kompletního šifrování pomocí šifrování na hostitelských discích Azure Portal – spravované disky
description: Pomocí šifrování na hostiteli povolte komplexní šifrování na Azure Managed disks – Azure Portal.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: fd74872bc1c46f3dddda1b6d15f14f26dc2187cb
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499369"
---
# <a name="use-the-azure-portal-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Použití Azure Portal k povolení kompletního šifrování pomocí šifrování na hostiteli

Pokud povolíte šifrování na hostiteli, data uložená na hostiteli virtuálního počítače se zašifrují v klidovém stavu a toky se zašifrují do služby úložiště. Koncepční informace o šifrování na hostiteli a také o dalších typech šifrování spravovaného disku najdete v těchto tématech:

* Linux: [šifrování v rámci hostitele – komplexní šifrování pro data virtuálních počítačů](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

* Windows: [šifrování v rámci hostitele – komplexní šifrování pro data virtuálních počítačů](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Omezení

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Podporované oblasti

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Podporované velikosti virtuálních počítačů

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Předpoklady

Aby bylo možné používat šifrování na hostiteli pro vaše virtuální počítače nebo služby Virtual Machine Scale Sets, musíte ve svém předplatném mít povolenou funkci. Odešlete email na adresu encryptionAtHost@microsoft. com s ID předplatného, abyste získali funkci povolenou pro vaše předplatná.

Přihlaste se k Azure Portal pomocí [poskytnutého odkazu](https://aka.ms/diskencryptionupdates).

> [!IMPORTANT]
> Pro přístup k Azure Portal je nutné použít [poskytnutý odkaz](https://aka.ms/diskencryptionupdates) . Šifrování na hostiteli není aktuálně viditelné ve veřejném Azure Portal bez použití tohoto odkazu.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Vytvoření sady pro šifrování Azure Key Vault a disku

Jakmile je tato funkce povolená, budete muset nastavit Azure Key Vault a sadu Disk Encryption (Pokud jste to ještě neudělali).

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Nasazení virtuálního počítače

Pokud chcete povolit šifrování na hostiteli, musíte nasadit nový virtuální počítač. tuto službu nejde na stávajících virtuálních počítačích povolit.

1. Vyhledejte **Virtual Machines** a vyberte **+ Přidat** a vytvořte virtuální počítač.
1. Vytvořte nový virtuální počítač, vyberte příslušnou oblast a podporovanou velikost virtuálního počítače.
1. Do okna **základní** zadejte další hodnoty, které chcete, a pak přejděte k oknu **disky** .

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="Snímek obrazovky s oknem základy vytváření virtuálních počítačů, oblast a velikost M jsou zvýrazněné.":::

1. V okně **disky** vyberte **Ano** pro **šifrování na hostiteli**.
1. Vyberte zbývající výběr.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="Snímek obrazovky okna s disky pro vytvoření virtuálního počítače, šifrování na hostiteli je zvýrazněné.":::

1. Dokončete proces nasazení virtuálního počítače a proveďte výběry, které odpovídají vašemu prostředí.

Nyní jste nasadili virtuální počítač s povoleným šifrováním na hostiteli, všechny jeho přidružené disky budou šifrovány pomocí šifrování na hostiteli.

## <a name="next-steps"></a>Další kroky

[Ukázky šablon Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)