---
title: Nastavení Microsoft Azure Data Box Disku| Microsoft Docs
description: V tomto výukovém kurzu se naučíte, jak nastavit Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 10/31/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: b3b686815b86e28c0473668825b89736b9af5bf1
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740480"
---
# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>Kurz: Rozbalení, připojení a odemknutí Azure Data Box Disku

Tento kurz popisuje, jak rozbalit, připojit a odemknout Azure Data Box Disk.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Rozbalit váš Data Box Disk
> * Připojení k diskům a získání klíče
> * Odemknout disky v klientovi pro Windows
> * Odemknutí disků v klientovi pro Linux

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [Kurz: Objednání Azure Data Box Disku](data-box-disk-deploy-ordered.md).
2. Obdrželi jste disky a stav úlohy na portálu je aktualizovaný na **Dodáno**.
3. Máte klientský počítač, na který můžete nainstalovat odemykací nástroj Data Box Disku. Klientský počítač musí splňovat tyto požadavky:
    - Musí na něm běžet [podporovaný operační systém](data-box-disk-system-requirements.md#supported-operating-systems-for-clients).
    - Musí na něm být nainstalovaný [požadovaný software](data-box-disk-system-requirements.md#other-required-software-for-windows-clients), pokud jde o klienta pro Windows.  

## <a name="unpack-your-disks"></a>Rozbalení vašich disků

 Disky rozbalíte pomocí následujících kroků.

1. Disky Data Box jsou zasílány v malém přepravním obalu. Otevřete obal a vyjměte jeho obsah. Zkontrolujte, že obal obsahuje 1 až 5 disků SSD (solid-state drive) a připojovací kabel USB pro každý disk. Zkontrolujte obal, zda nenese stopy porušení nebo není zjevně poškozen. 

    ![Přepravní obal Data Box Disku](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. Pokud je přepravní obal porušen nebo vážně poškozen, neotevírejte ho. Obraťte se na podporu Microsoftu, aby vám pomohla určit, jestli jsou disky v dobrém funkčním stavu nebo zda je potřeba poslat náhradní.
3. Ověřte, že obal obsahuje průhlednou obálku s expedičním štítkem (pod aktuálním štítkem) pro zpáteční zásilku. Pokud se tento štítek ztratí nebo poškodí, můžete vždy stáhnout a vytisknout nový z webu Azure Portal. 

    ![Expediční štítek Data Box Disku](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. Uschovejte si obal a výplňový materiál pro zpáteční odeslání disků.

## <a name="connect-to-disks-and-get-the-passkey"></a>Připojení k diskům a získání klíče 

1. Pomocí dodaného kabelu připojte disk ke klientskému počítači s podporovaným operačním systémem uvedeným v požadavcích. 

    ![Připojení Data Box Disku](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. Na webu Azure Portal přejděte na **Obecné > Podrobnosti zařízení**. Pomocí ikony kopírování zkopírujte klíč. Tento klíč se použije k odemknutí disků.

    ![Klíč k odemknutí Data Box Disku](media/data-box-disk-deploy-set-up/data-box-disk-get-passkey.png) 

Postup odemknutí disků se liší v závislosti na tom, jestli jste připojeni ke klientovi pro Windows nebo Linux.

## <a name="unlock-disks-on-windows-client"></a>Odemknutí disků v klientovi pro Windows

Disky připojíte a odemknete pomocí následujících kroků.
     
1. Na webu Azure Portal přejděte na **Obecné > Podrobnosti zařízení**. 
2. Stáhněte si sadu nástrojů Data Box Disk odpovídající klientovi pro Windows. Tato sada nástrojů obsahuje 3 nástroje: Data Box Disk Unlock, Data Box Disk Validation a Data Box Disk Split Copy. 

    V tomto postupu použijete jenom nástroj Data Box Disk Unlock. Ostatní dva nástroje použijeme později.

    > [!div class="nextstepaction"]
    > [Stáhnout sadu nástrojů Data Box Disk pro Windows](http://aka.ms/databoxdisktoolswin)         

3. Rozbalte sadu nástrojů na stejném počítači, který budete používat ke kopírování dat. 
4. Ve stejném počítači otevřete okno příkazového řádku nebo spusťte Windows PowerShell jako správce.
5. (Volitelné) Chcete-li ověřit, že počítač, který používáte k odemknutí disku, splňuje požadavky na operační systém, spusťte příkaz kontroly systému. Ukázkový výstup najdete níž. 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

6. Spusťte soubor `DataBoxDiskUnlock.exe` a zadejte klíč, který jste získali v části [Připojení k diskům a získání klíče](#Connect-to-disks-and-get-the-passkey). Zobrazí se písmeno jednotky přiřazené k danému disku. Ukázkový výstup najdete níž.

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkey :
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

7. Opakujte tento postup odemknutí vždy, když v budoucnu znovu vložíte disk. Pokud potřebujete pomoc s odemykacím nástrojem Data Box Disku, použijte příkaz `help`.   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKey:<passkey_from_Azure_portal>
    
    Example: DataBoxUnlock /PassKey:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKey:        Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
8. Po odemknutí disku můžete zobrazit obsah disku.    

    ![Obsah Data Box Disku](media/data-box-disk-deploy-set-up/data-box-disk-content.png) 

## <a name="unlock-disks-on-linux-client"></a>Odemknutí disků v klientovi pro Linux

1. Na webu Azure Portal přejděte na **Obecné > Podrobnosti zařízení**. 
2. Stáhněte si sadu nástrojů Data Box Disk odpovídající klientovi pro Linux.  

    > [!div class="nextstepaction"]
    > [Stáhnout sadu nástrojů Data Box Disk pro Linux](http://aka.ms/databoxdisktoolslinux) 

3. Na klientovi pro Linux otevřete terminál. Přejděte do složky, kam jste tento software stáhli. Změňte oprávnění souboru tak, abyste tyto soubory mohli spouštět. Zadejte následující příkaz: 

    `chmod +x DataBoxDiskUnlock_x86_64` 
    
    `chmod +x DataBoxDiskUnlock_Prep.sh` 
 
    Ukázkový výstup najdete níž. Po spuštění příkazu chmod můžete spuštěním příkazu `ls` ověřit, že se oprávnění souboru změnila. 
 
    ```
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_x86_64  
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_Prep.sh   
        [user@localhost Downloads]$ ls -l  
        -rwxrwxr-x. 1 user user 1152664 Aug 10 17:26 DataBoxDiskUnlock_x86_64  
        -rwxrwxr-x. 1 user user 795 Aug 5 23:26 DataBoxDiskUnlock_Prep.sh
    ```
4. Spusťte tento skript tak, aby nainstaloval všechny binární soubory potřebné pro odemykací software Data Box Disk. Pomocí `sudo` spusťte příkaz jako uživatel root. Jakmile jsou binární soubory úspěšně nainstalované, zobrazí se na terminálu příslušné sdělení.

    `sudo ./DataBoxDiskUnlock_Prep.sh`

    Skript nejprve zkontroluje, jestli má klientský počítač podporovaný operační systém. Ukázkový výstup najdete níž. 
 
    ```
    [user@localhost Documents]$ sudo ./DataBoxDiskUnlock_Prep.sh 
        OS = CentOS Version = 6.9 
        Release = CentOS release 6.9 (Final) 
        Architecture = x64 
    
        The script will install the following packages and dependencies. 
        epel-release 
        dislocker 
        ntfs-3g 
        fuse-dislocker 
        Do you wish to continue? y|n :|
    ```
    
 
5. Zadáním `y` pokračujte v instalaci. Skript nainstaluje tyto balíčky: 
    - **epel-release** – Úložiště, které obsahuje následující tři balíčky. 
    - **dislocker a fuse-dislocker** – Tento nástroj umožňuje dešifrovat disky zašifrované nástrojem BitLocker. 
    - **ntfs-3g** – Balíček, který umožňuje připojení svazků NTFS. 
 
    Po úspěšné instalaci těchto balíčků se na terminálu zobrazí příslušné oznámení.     
    ```
    Dependency Installed: compat-readline5.x86 64 0:5.2-17.I.el6 dislocker-libs.x86 64 0:0.7.1-8.el6 mbedtls.x86 64 0:2.7.4-l.el6        ruby.x86 64 0:1.8.7.374-5.el6 
    ruby-libs.x86 64 0:1.8.7.374-5.el6 
    Complete! 
    Loaded plugins: fastestmirror, refresh-packagekit, security 
    Setting up Remove Process 
    Resolving Dependencies 
    --> Running transaction check 
    ---> Package epel-release.noarch 0:6-8 will be erased --> Finished Dependency Resolution 
    Dependencies Resolved 
    Package        Architecture        Version        Repository        Size 
    Removing:  epel-release        noarch         6-8        @extras        22 k 
    Transaction Summary                                 
    Remove        1 Package(s) 
    Installed size: 22 k 
    Downloading Packages: 
    Running rpmcheckdebug 
    Running Transaction Test 
    Transaction Test Succeeded 
    Running Transaction 
    Erasing : epel-release-6-8.noarch 
    Verifying : epel-release-6-8.noarch 
    Removed: 
    epel-release.noarch 0:6-8 
    Complete! 
    Dislocker is installed by the script. 
    OpenSSL is already installed.
    ```

6. Spusťte odemykací nástroj Data Box Disku. Zadejte klíč z webu Azure Portal, který jste získali v části [Připojení k diskům a získání klíče](#Connect-to-disks-and-get-the-passkey). Volitelně zadejte seznam svazků zašifrovaných nástrojem BitLocker, které se mají odemknout. Klíč a seznam svazků musí být zadané v jednoduchých uvozovkách. 

    Zadejte následující příkaz.
 
    `sudo ./DataBoxDiskUnlock_x86_64 /PassKey:’<Your passkey from Azure portal>’ /Volumes:’<list of volumes>’`         

    Ukázkový výstup je uvedený níže. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Passkey:’qwerqwerqwer’ /Volumes:’/dev/sdbl’ 
    
    START: Mon Aug 13 14:25:49 2018 
    Volumes: /dev/sdbl 
    Passkey: qwerqwerqwer 
    
    Volumes for data copy : 
    /dev/sdbl: /mnt/DataBoxDisk/mountVoll/ 
    END: Mon Aug 13 14:26:02 2018
    ```
    Zobrazí se přípojný bod svazku, na který můžete data zkopírovat.

7. Opakujte postup odemknutí vždy, když v budoucnu znovu vložíte disk. Pokud potřebujete pomoc s odemykacím nástrojem Data Box Disku, použijte příkaz `help`. 
    
    `sudo ./DataBoxDiskUnlock_x86_64 /Help` 

    Ukázkový výstup je uvedený níže. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Help  
    START: Mon Aug 13 14:29:20 2018 
    USAGE: 
    sudo DataBoxDiskUnlock /PassKey:’<passkey from Azure_portal>’ 
    
    Example: sudo DataBoxDiskUnlock /PassKey:’passkey’ 
    Example: sudo DataBoxDiskUnlock /PassKey:’passkey’ /Volumes:’/dev/sdbl’ 
    Example: sudo DataBoxDiskUnlock /Help Example: sudo DataBoxDiskUnlock /Clean 
    
    /PassKey: This option takes a passkey as input and unlocks all of your disks. 
    Get the passkey from your Data Box Disk order in Azure portal. 
    /Volumes: This option is used to input a list of BitLocker encrypted volumes. 
    /Help: This option provides help on the tool usage and examples. 
    /Unmount: This option unmounts all the volumes mounted by this tool. 
   
    END: Mon Aug 13 14:29:20 2018 [user@localhost Downloads]$
    ```
    
8. Po odemknutí disku můžete přejít na tento přípojný bod a zobrazit obsah disku. Nyní jste připraveni ke kopírování dat do složek *BlockBlob* nebo *PageBlob*. 

    ![Obsah Data Box Disku](media/data-box-disk-deploy-set-up/data-box-disk-content-linux.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených s Azure Data Box Diskem, například jste se naučili:

> [!div class="checklist"]
> * Rozbalit váš Data Box Disk
> * Připojení k diskům a získání klíče
> * Odemknout disky v klientovi pro Windows
> * Odemknutí disků v klientovi pro Linux


V dalším kurzu se dozvíte, jak na Data Box Disk zkopírovat data.

> [!div class="nextstepaction"]
> [Kopírování dat na Data Box Disk](./data-box-disk-deploy-copy-data.md)

