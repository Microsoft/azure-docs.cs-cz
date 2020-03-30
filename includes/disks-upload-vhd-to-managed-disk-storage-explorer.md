---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5104c3b4446a8d3747ce7cc1648ef05dd117eb3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74013776"
---
Průzkumník úložiště 1.10.0 umožňuje uživatelům nahrávat, stahovat a kopírovat spravované disky a vytvářet snímky. Díky těmto dalším možnostem můžete pomocí Průzkumníka úložiště migrovat data z místního do Azure a migrovat data napříč oblastmi Azure.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku budete potřebovat následující:
- Předplatné Azure
- Jeden nebo více spravovaných disků Azure
- Nejnovější verze [Průzkumníka azure storage exploreru](https://azure.microsoft.com/features/storage-explorer/)

## <a name="connect-to-an-azure-subscription"></a>Připojení k předplatnému Azure

Pokud průzkumník úložiště není připojený k Azure, nebudete ho moct použít ke správě prostředků. Tato část přejde k jeho připojení k účtu Azure, takže můžete spravovat prostředky pomocí Průzkumníka úložiště.

1. Spusťte Průzkumníka úložiště Azure a klikněte na ikonu **modulu plug-in** vlevo.

    ![Klikněte na ikonu modulu plug-in.](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. Vyberte **Přidat účet Azure**a klikněte na **Další**.

    ![Přidání účtu Azure](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. V dialogovém okně **Přihlášení do Azure** zadejte svoje přihlašovací údaje Azure.

    ![Dialogové okno Přihlášení do Azure](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. Vyberte ze seznamu své předplatné a klikněte na **Použít**.

    ![Vyberte své předplatné.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>Nahrání spravovaného disku z virtuálního pevného disku prem

1. V levém podokně rozbalte **položku Disky** a vyberte skupinu prostředků, do které chcete disk nahrát.

    ![Vybrat skupinu prostředků 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Vyberte **Nahrát**.

    ![Výběr nahrání](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. V **uploadu virtuálního pevného disku** zadejte zdrojový virtuální pevný disk, název disku, typ operačního systému, oblast, do které chcete disk nahrát, a také typ účtu. V některých oblastech jsou podporovány zóny dostupnosti, pro tyto oblasti můžete vybrat zónu podle vašeho výběru.
1. Chcete-li začít nahrávat disk, vyberte **Vytvořit.**

    ![Dialogové okno Nahrát vhd](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. Stav nahrávání se nyní zobrazí v **části Aktivity**.

    ![Stav nahrávání](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. Pokud nahrávání skončilo a disk se nezobrazí v pravém podokně, vyberte **Aktualizovat**.

## <a name="download-a-managed-disk"></a>Stažení spravovaného disku

Následující kroky vysvětlují, jak stáhnout spravovaný disk do virtuálního pevného disku v premu. Aby bylo možné stáhnout, musí být stav disku **Odpojen,** nelze **Attached** jej stáhnout.

1. V levém podokně rozbalte v levém podokně **položku Disky** a vyberte skupinu prostředků, ze které chcete disk stáhnout.

    ![Vybrat skupinu prostředků 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. V pravém podokně vyberte disk, který chcete stáhnout.
1. Vyberte **Stáhnout** a pak zvolte, kam chcete disk uložit.

    ![Stažení spravovaného disku](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. Vyberte **Uložit** a disk se začne stahovat. Stav stahování se zobrazí v **části Aktivity**.

    ![Stav stažení](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>Kopírování spravovaného disku

Pomocí Průzkumníka úložiště můžete zkopírovat manged disk v rámci nebo mezi oblastmi. Kopírování disku:

1. V rozevíracím seznamu **Disky** vlevo vyberte skupinu prostředků obsahující disk, který chcete zkopírovat.

    ![Vybrat skupinu prostředků 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. V pravém podokně vyberte disk, který chcete zkopírovat, a vyberte **Kopírovat**.

    ![Kopírování spravovaného disku](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. V levém podokně vyberte skupinu prostředků, do které chcete disk vložit.

    ![Vybrat skupinu prostředků 2](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. V pravém podokně vyberte **Vložit.**

    ![Vložení spravovaného disku](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. V dialogovém okně **Vložit disk** vyplňte hodnoty. Můžete také určit zónu dostupnosti v podporovaných oblastech.

    ![Dialogové okno Vložit disk](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. Vyberte **Vložit** a disk se začne kopírovat, stav se zobrazí v **části Aktivity**.

    ![Kopírovat stav vložení](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>Vytvoření snímku

1. V rozevíracím seznamu **Disky** vlevo vyberte skupinu prostředků obsahující disk, který chcete snímek.

    ![Vybrat skupinu prostředků 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Vpravo vyberte disk, který chcete snímek, a vyberte **Vytvořit snímek**.

    ![Vytvoření snímku](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. V **poli Vytvořit snímek**zadejte název snímku a také skupinu prostředků, ve které ji chcete vytvořit. Pak vyberte **Vytvořit**.

    ![Dialogové okno Vytvořit snímek](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. Po vytvoření snímku můžete vybrat **Otevřít na portálu** v **aktivitách** a zobrazit snímek na webu Azure Portal.

    ![Otevřít snímek na portálu](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>Další kroky
