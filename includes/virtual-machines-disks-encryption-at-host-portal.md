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
ms.openlocfilehash: f0a706cc51027bc503da9ee3bc637216e9e52853
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171375"
---
## <a name="restrictions"></a>Omezení

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Podporované oblasti

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Podporované velikosti virtuálních počítačů

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Požadavky

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

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="Snímek obrazovky s oknem základy vytváření virtuálních počítačů, oblast a velikost M jsou zvýrazněné.":::

1. V okně **disky** vyberte **Ano** pro **šifrování na hostiteli**.
1. Vyberte zbývající výběr.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="Snímek obrazovky okna s disky pro vytvoření virtuálního počítače, šifrování na hostiteli je zvýrazněné.":::

1. Dokončete proces nasazení virtuálního počítače a proveďte výběry, které odpovídají vašemu prostředí.

Nyní jste nasadili virtuální počítač s povoleným šifrováním na hostiteli, všechny jeho přidružené disky budou šifrovány pomocí šifrování na hostiteli.