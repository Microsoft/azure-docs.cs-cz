---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f96bc753d14d53584aec6d9661e52adcd21ab764
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136104"
---
## <a name="supported-regions"></a>Podporované oblasti

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="getting-started"></a>Začínáme

1. Přihlaste se na portál [Azure Portal](https://aka.ms/diskencryptionupdates).

    > [!IMPORTANT]
    > Pro přístup k Azure Portal je nutné použít [poskytnutý odkaz](https://aka.ms/diskencryptionupdates) . Dvojité šifrování v klidovém umístění není aktuálně viditelné ve veřejném Azure Portal bez použití odkazu.

1. Vyhledejte a vyberte možnost **sady šifrování disku**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="ukázkový text":::

1. Vyberte **+ Přidat**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="ukázkový text":::

1. Vyberte jednu z podporovaných oblastí.
1. Jako **typ šifrování**vyberte možnost **dvojité šifrování u klíčů spravovaných platformou a zákazníkem**.

    > [!NOTE]
    > Jakmile vytvoříte sadu šifrování disků s určitým typem šifrování, nelze ji změnit. Pokud chcete použít jiný typ šifrování, musíte vytvořit novou sadu šifrování disku.

1. Vyplňte zbývající informace.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="ukázkový text":::

1. Vyberte Azure Key Vault a klíč nebo v případě potřeby vytvořte nový.

    > [!NOTE]
    > Pokud vytvoříte instanci Key Vault, musíte povolit ochranu pomocí obnovitelného odstranění a vyprázdnění. Tato nastavení jsou povinná při použití Key Vault k šifrování spravovaných disků a k ochraně před ztrátou dat kvůli náhodnému odstranění.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="ukázkový text":::

1. Vyberte **Vytvořit**.
1. Přejděte na sadu šifrování disku, kterou jste vytvořili, a vyberte chybu, která se zobrazí. Tím se nakonfiguruje sada Encryption disk nastavená na fungovat.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="ukázkový text":::

    Oznámení by se mělo zobrazit a úspěšně zobrazit. Díky tomu budete moct použít sadu Disk Encryption s vaším trezorem klíčů.
    
    ![Snímek obrazovky s úspěšným oprávněním a přiřazením role pro váš Trezor klíčů](media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)

1. Přejděte na disk.
1. Vyberte **šifrování**.
1. Jako **typ šifrování**vyberte možnost **dvojité šifrování u klíčů spravovaných platformou a zákazníkem**.
1. Vyberte sadu šifrování disku.
1. Vyberte **Uložit**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="ukázkový text":::

Na spravovaném disku jste teď povolili dvojité šifrování v klidovém prostředí.
