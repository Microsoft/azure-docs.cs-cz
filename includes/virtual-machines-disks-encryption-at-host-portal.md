---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c5ea31217bd3088ec123281b36f8578b08ea25b2
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136135"
---
## <a name="restrictions"></a>Omezení

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Podporované oblasti

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Podporované velikosti virtuálních počítačů

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Předpoklady

Aby bylo možné používat šifrování na hostiteli pro vaše virtuální počítače nebo služby Virtual Machine Scale Sets, musíte ve svém předplatném mít povolenou funkci. Odešlete email na adresu encryptionAtHost@microsoft. com s ID předplatného, abyste získali funkci povolenou pro vaše předplatná.

Přihlaste se k Azure Portal pomocí [poskytnutého odkazu](https://aka.ms/diskencryptionupdates).

> [!IMPORTANT]
> Pro přístup k Azure Portal je nutné použít [poskytnutý odkaz](https://aka.ms/diskencryptionupdates) . Šifrování na hostiteli není aktuálně viditelné ve veřejném Azure Portal bez použití tohoto odkazu.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Vytvoření sady pro šifrování Azure Key Vault a disku

Jakmile je tato funkce povolená, budete muset nastavit Azure Key Vault a sadu Disk Encryption (Pokud jste to ještě neudělali).

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Nasazení virtuálního počítače

Pokud chcete povolit šifrování na hostiteli, musíte nasadit nový virtuální počítač. tuto službu nejde na stávajících virtuálních počítačích povolit.

1. Vyhledejte **Virtual Machines** a vyberte **+ Přidat** a vytvořte virtuální počítač.
1. Vytvořte nový virtuální počítač, vyberte příslušnou oblast a podporovanou velikost virtuálního počítače.
1. Do okna **základní** zadejte další hodnoty, které chcete, a pak přejděte k oknu **disky** .

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="ukázkový text":::

1. V okně **disky** vyberte **Ano** pro **šifrování na hostiteli**.
1. Vyberte zbývající výběr.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="ukázkový text":::

1. Dokončete proces nasazení virtuálního počítače a proveďte výběry, které odpovídají vašemu prostředí.

Nyní jste nasadili virtuální počítač s povoleným šifrováním na hostiteli, všechny jeho přidružené disky budou šifrovány pomocí šifrování na hostiteli.