---
title: Použití Průzkumník služby Azure Storage ke správě Azure Managed disks
description: Naučte se nahrávat, stahovat a migrovat Azure Managed disk v různých oblastech a pomocí Průzkumník služby Azure Storage vytvořit snímek spravovaného disku.
author: roygara
ms.author: rogarana
ms.date: 09/25/2019
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 9dfce7b76eed5bfc9f4979c0e3041b6c65c28422
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88749377"
---
# <a name="use-azure-storage-explorer-to-manage-azure-managed-disks"></a>Použití Průzkumník služby Azure Storage ke správě Azure Managed disks

Průzkumník služby Storage 1.10.0 umožňuje uživatelům nahrávat, stahovat a kopírovat spravované disky a také vytvářet snímky. Z důvodu těchto dalších možností můžete pomocí Průzkumník služby Storage migrovat data z místního prostředí do Azure a migrovat data mezi oblastmi Azure.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku budete potřebovat následující:
- Předplatné Azure
- Jeden nebo více Azure Managed disks
- Nejnovější verze [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/)

## <a name="connect-to-an-azure-subscription"></a>Připojení k předplatnému Azure

Pokud váš Průzkumník služby Storage není připojený k Azure, nebudete ho moct používat ke správě prostředků. Tato část se přehraje na účet Azure, abyste mohli spravovat prostředky pomocí Průzkumník služby Storage.

1. Spusťte Průzkumník služby Azure Storage a klikněte na ikonu **modulu plug-in** na levé straně.

    ![Klikněte na ikonu modulu plug-in.](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. Vyberte **Přidat účet Azure**a pak klikněte na **Další**.

    ![Přidání účtu Azure](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. V dialogovém okně **přihlášení do Azure** zadejte svoje přihlašovací údaje Azure.

    ![Přihlašovací dialogové okno Azure](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. Vyberte ze seznamu své předplatné a klikněte na **Použít**.

    ![Vyberte své předplatné.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>Nahrání spravovaného disku z virtuálního pevného disku s Prem

1. V levém podokně rozbalte položku **disky** a vyberte skupinu prostředků, do které chcete disk odeslat.

    ![Vybrat skupinu prostředků 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Vyberte **Nahrát**.

    ![Výběr nahrání](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. V části **nahrát virtuální pevný** disk zadejte zdrojový virtuální pevný disk, název disku, typ operačního systému, oblast, do které chcete disk odeslat, a také typ účtu. V některých oblastech jsou podporovány zóny dostupnosti, a to pro tyto oblasti můžete vybrat zónu podle vašeho výběru.
1. Vyberte **vytvořit** a začněte nahrávat disk.

    ![Dialogové okno nahrát virtuální pevný disk](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. Stav nahrávání se nyní zobrazí v **aktivitách**.

    ![Stav odeslání](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. Pokud se nahrávání dokončilo a v pravém podokně se disk nezobrazuje, vyberte **aktualizovat**.

## <a name="download-a-managed-disk"></a>Stažení spravovaného disku

Následující postup vysvětluje, jak stáhnout spravovaný disk na virtuální pevný disk Prem. Stav disku musí být **nepřipojen** , aby jej bylo možné stáhnout, nelze stáhnout **připojený** disk.

1. V levém podokně, pokud ještě není rozbalený, rozbalte položku **disky** a vyberte skupinu prostředků, ze které chcete disk stáhnout.

    ![Vybrat skupinu prostředků 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. V pravém podokně vyberte disk, který chcete stáhnout.
1. Vyberte **Stáhnout** a pak zvolte, kam chcete disk uložit.

    ![Stažení spravovaného disku](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. Vyberte **Uložit** a disk se začne stahovat. Stav stahování se zobrazí v části **aktivity**.

    ![Stav stahování](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>Kopírování spravovaného disku

Pomocí Průzkumník služby Storage můžete kopírovat spravovaných disk v rámci nebo napříč oblastmi. Zkopírování disku:

1. V rozevíracím seznamu **disky** vlevo vyberte skupinu prostředků, která obsahuje disk, který chcete zkopírovat.

    ![Vybrat skupinu prostředků 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. V pravém podokně vyberte disk, který chcete zkopírovat, a vyberte **Kopírovat**.

    ![Kopírování spravovaného disku](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. V levém podokně vyberte skupinu prostředků, do které chcete disk vložit.

    ![Vybrat skupinu prostředků 2](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. V pravém podokně vyberte **Vložit** .

    ![Vložení spravovaného disku](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. V dialogovém okně **vložit disk** vyplňte hodnoty. Můžete také zadat zónu dostupnosti v podporovaných oblastech.

    ![Dialog vložit disk](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. Vyberte **Vložit** a disk se začne kopírovat. stav se zobrazí v **aktivitách**.

    ![Kopírovat stav vložení](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>Vytvoření snímku

1. V rozevíracím seznamu **disky** vlevo vyberte skupinu prostředků, která obsahuje disk, který chcete snímek.

    ![Vybrat skupinu prostředků 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Na pravé straně vyberte disk, na který chcete vytvořit snímek, a vyberte **vytvořit snímek**.

    ![Vytvoření snímku](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. V části **vytvořit snímek**zadejte název snímku a skupinu prostředků, ve které chcete vytvořit. Potom vyberte **Vytvořit**.

    ![Dialog vytvořit snímek](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. Po vytvoření snímku můžete vybrat **otevřít v portálu** v části **aktivity** a zobrazit snímek v Azure Portal.

    ![Otevřít snímek na portálu](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>Další kroky


Naučte se, jak [vytvořit virtuální počítač z VHD pomocí Azure Portal](windows/create-vm-specialized-portal.md).

Naučte se, jak [připojit spravovaný datový disk k virtuálnímu počítači s Windows pomocí Azure Portal](windows/attach-managed-disk-portal.md).