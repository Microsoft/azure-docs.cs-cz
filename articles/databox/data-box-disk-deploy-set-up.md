---
title: Nastavení Microsoft Azure Data Box Disku| Microsoft Docs
description: V tomto výukovém kurzu se naučíte, jak nastavit Azure Data Box Disk.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 7f382e3b6e70aadf8c6a090a3d5c049f6b5c0337
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010357"
---
# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>Kurz: Rozbalení, připojení a odemknutí Azure Data Box Disku

Tento kurz popisuje, jak rozbalit, připojit a odemknout Azure Data Box Disk.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Rozbalit váš Data Box Disk
> * Připojit a odemknout Data Box Disk

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [Kurz: Objednání Azure Data Box Disku](data-box-disk-deploy-ordered.md).
2. Obdrželi jste disky a stav úlohy na portálu je aktualizovaný na **Dodáno**.
3. Máte hostitelský počítač, na který můžete nainstalovat odemykací nástroj Data Box Disku. Hostitelský počítač musí splňovat tyto požadavky:
    - Musí na něm běžet [podporovaný operační systém](data-box-disk-system-requirements.md).
    - Musí na něm být [nainstalované prostředí Windows PowerShell 4](https://www.microsoft.com/download/details.aspx?id=40855).
    - Musí na něm být [nainstalované rozhraní .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653).

## <a name="unpack-your-disks"></a>Rozbalení vašich disků

 Disky rozbalíte pomocí následujících kroků.

1. Disky Data Box jsou zasílány v malém přepravním obalu. Otevřete obal a vyjměte jeho obsah. Zkontrolujte, že obal obsahuje 1 až 5 disků SSD (solid-state drive) a připojovací kabel USB pro každý disk. Zkontrolujte obal, zda nenese stopy porušení nebo není zjevně poškozen. 

    ![Přepravní obal Data Box Disku](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. Pokud je přepravní obal porušen nebo vážně poškozen, neotevírejte ho. Obraťte se na podporu Microsoftu, aby vám pomohla určit, jestli jsou disky v dobrém funkčním stavu nebo zda je potřeba poslat náhradní.
3. Ověřte, že obal obsahuje průhlednou obálku s expedičním štítkem (pod aktuálním štítkem) pro zpáteční zásilku. Pokud se tento štítek ztratí nebo poškodí, můžete vždy stáhnout a vytisknout nový z webu Azure Portal. 

    ![Expediční štítek Data Box Disku](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. Uschovejte si obal a výplňový materiál pro zpáteční odeslání disků.

## <a name="connect-and-unlock-your-disks"></a>Připojení a odemknutí disků

Disky připojíte a odemknete pomocí následujících kroků.

1. Pomocí přiloženého kabelu připojte disk k počítači s podporovanou verzí operačního systému Windows, která je uvedená v požadavcích. 

    ![Připojení Data Box Disku](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. Na webu Azure Portal přejděte na **Obecné > Podrobnosti zařízení**. 
3. Klikněte na **odemykací nástroj Data Box Disku**. 

    ![Stažení odemykacího nástroje Data Box Disku](media/data-box-disk-deploy-set-up/data-box-disk-unload1.png)     

4. Rozbalte nástroj na stejném počítači, který budete používat ke kopírování dat.
5. Ve stejném počítači otevřete okno příkazového řádku nebo spusťte Windows PowerShell jako správce.
6. (Volitelné) Chcete-li ověřit, že počítač, který používáte k odemknutí disku, splňuje požadavky na operační systém, spusťte příkaz kontroly systému. Ukázkový výstup najdete níž. 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

7. Na webu Azure Portal přejděte na **Obecné > Podrobnosti zařízení**. Pomocí ikony kopírování zkopírujte klíč.
8. Spusťte příkaz `DataBoxDiskUnlock.exe` a zadejte klíč. Zobrazí se písmeno jednotky přiřazené k danému disku. Ukázkový výstup najdete níž.

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkeys (format: passkey1;passkey2;passkey3):
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

9. Opakujte kroky 6 až 8 pro všechny budoucí vkládané disky. Pokud potřebujete pomoc s odemykacím nástrojem Data Box Disku, použijte příkaz nápovědy.   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKeys:<passkey_list_separated_by_semicolon>
    
    Example: DataBoxUnlock /PassKeys:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKeys:       Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
10. Po odemknutí disku můžete zobrazit obsah disku.    

    ![Obsah Data Box Disku](media/data-box-disk-deploy-set-up/data-box-disk-content.png) 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených s Azure Data Box Diskem, například jste se naučili:

> [!div class="checklist"]
> * Rozbalit váš Data Box Disk
> * Připojit a odemknout Data Box Disky


V dalším kurzu se dozvíte, jak na Data Box Disk zkopírovat data.

> [!div class="nextstepaction"]
> [Kopírování dat na Data Box Disk](./data-box-disk-deploy-copy-data.md)

