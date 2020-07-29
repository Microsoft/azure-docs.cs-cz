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
ms.openlocfilehash: 8313259d5375bb19e7caa08a01a658707453d133
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87177010"
---
## <a name="supported-regions"></a>Podporované oblasti

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="getting-started"></a>Začínáme

1. Přihlaste se na [Azure Portal](https://aka.ms/diskencryptionupdates).

    > [!IMPORTANT]
    > Pro přístup k Azure Portal je nutné použít [poskytnutý odkaz](https://aka.ms/diskencryptionupdates) . Dvojité šifrování v klidovém umístění není aktuálně viditelné ve veřejném Azure Portal bez použití odkazu.

1. Vyhledejte a vyberte možnost **sady šifrování disku**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="Snímek obrazovky hlavního Azure Portal je na panelu hledání zvýrazněné sady Disk Encryption.":::

1. Vyberte **+ Přidat**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="Snímek obrazovky s oknem nastavení šifrování disku + přidat je zvýrazněný.":::

1. Vyberte jednu z podporovaných oblastí.
1. Jako **typ šifrování**vyberte možnost **dvojité šifrování u klíčů spravovaných platformou a zákazníkem**.

    > [!NOTE]
    > Jakmile vytvoříte sadu šifrování disků s určitým typem šifrování, nelze ji změnit. Pokud chcete použít jiný typ šifrování, musíte vytvořit novou sadu šifrování disku.

1. Vyplňte zbývající informace.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="Snímek obrazovky okna pro vytvoření sady šifrování disku, oblasti a dvojité šifrování pomocí klíčů spravovaných platformou a zákazníkem, jsou zvýrazněny.":::

1. Vyberte Azure Key Vault a klíč nebo v případě potřeby vytvořte nový.

    > [!NOTE]
    > Pokud vytvoříte instanci Key Vault, musíte povolit ochranu pomocí obnovitelného odstranění a vyprázdnění. Tato nastavení jsou povinná při použití Key Vault k šifrování spravovaných disků a k ochraně před ztrátou dat kvůli náhodnému odstranění.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="Snímek obrazovky okna pro vytváření Key Vault":::

1. Vyberte **Vytvořit**.
1. Přejděte na sadu šifrování disku, kterou jste vytvořili, a vyberte chybu, která se zobrazí. Tím se nakonfiguruje sada Encryption disk nastavená na fungovat.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="Snímek obrazovky s chybou, která se zobrazila v sadě šifrování disků, text chyby je: Pokud chcete přidružit disk, image nebo snímek k této sadě pro šifrování disků, musíte udělit oprávnění k trezoru klíčů.":::

    Oznámení by se mělo zobrazit a úspěšně zobrazit. Díky tomu budete moct použít sadu Disk Encryption s vaším trezorem klíčů.
    
    ![Snímek obrazovky s úspěšným oprávněním a přiřazením role pro váš Trezor klíčů](media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)

1. Přejděte na disk.
1. Vyberte **šifrování**.
1. Jako **typ šifrování**vyberte možnost **dvojité šifrování u klíčů spravovaných platformou a zákazníkem**.
1. Vyberte sadu šifrování disku.
1. Vyberte **Uložit**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="Snímek obrazovky okna šifrování pro váš spravovaný disk. zvýrazní se výše uvedený typ šifrování.":::

Na spravovaném disku jste teď povolili dvojité šifrování v klidovém prostředí.
