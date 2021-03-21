---
title: Povolit dvojité šifrování na discích se správou REST Azure Portal
description: Pro data spravovaného disku pomocí Azure Portal zapněte dvojité šifrování v klidovém formátu.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: b5f58b549fc9d879f6c2e5515c679d82c3ff44e4
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560357"
---
# <a name="use-the-azure-portal-to-enable-double-encryption-at-rest-for-managed-disks"></a>K povolení dvojitého šifrování v klidovém umístění pro Managed disks použijte Azure Portal.

Azure Disk Storage podporuje dvojité šifrování v klidovém umístění pro spravované disky. Koncepční informace o šifrovaném šifrování v klidovém umístění a dalších typech šifrování spravovaného disku naleznete v části [dvojité šifrování v klidovém oddílu v](disk-encryption.md#double-encryption-at-rest) článku o šifrování disku.

## <a name="getting-started"></a>Začínáme

1. Přihlaste se na [Azure Portal](https://aka.ms/diskencryptionupdates).

    > [!IMPORTANT]
    > Pro přístup k Azure Portal je nutné použít [poskytnutý odkaz](https://aka.ms/diskencryptionupdates) . Dvojité šifrování v klidovém umístění není aktuálně viditelné ve veřejném Azure Portal bez použití odkazu.

1. Vyhledejte a vyberte možnost **sady šifrování disku**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="Snímek obrazovky hlavního Azure Portal je na panelu hledání zvýrazněné sady Disk Encryption.":::

1. Vyberte **+ Přidat**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="Snímek obrazovky s oknem nastavení šifrování disku + přidat je zvýrazněný.":::

1. Vyberte jednu z podporovaných oblastí.
1. Jako **typ šifrování** vyberte možnost **dvojité šifrování u klíčů spravovaných platformou a zákazníkem**.

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
    
    ![Snímek obrazovky s úspěšným oprávněním a přiřazením role pro váš Trezor klíčů](media/virtual-machines-disks-double-encryption-at-rest-portal/disk-encryption-notification-success.png)

1. Přejděte na disk.
1. Vyberte **šifrování**.
1. Jako **typ šifrování** vyberte možnost **dvojité šifrování u klíčů spravovaných platformou a zákazníkem**.
1. Vyberte sadu šifrování disku.
1. Vyberte **Uložit**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="Snímek obrazovky okna šifrování pro váš spravovaný disk. zvýrazní se výše uvedený typ šifrování.":::

Na spravovaném disku jste teď povolili dvojité šifrování v klidovém prostředí.


## <a name="next-steps"></a>Další kroky

- [Azure PowerShell – povolení klíčů spravovaných zákazníkem pomocí disků spravovaných šifrováním na straně serveru](./windows/disks-enable-customer-managed-keys-powershell.md)
- [Ukázky šablon Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [Povolení klíčů spravovaných zákazníkem pomocí šifrování na straně serveru – příklady](./linux/disks-enable-customer-managed-keys-cli.md#examples)