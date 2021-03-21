---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: f002fec531a0355e803a1545990fc0b13b535742
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96581797"
---
Pokud se setkáte s problémy zařízení, můžete vytvořit balíček pro podporu ze systémových protokolů. Podpora Microsoftu používá tento balíček k řešení problémů. Pomocí těchto kroků vytvořte balíček pro podporu:

1. [Připojte se k rozhraní PowerShell vašeho zařízení](#connect-to-the-powershell-interface).
2. Pomocí `Get-HcsNodeSupportPackage` příkazu vytvořte balíček pro podporu. Použití rutiny je následující:

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    Rutina shromažďuje protokoly ze zařízení a kopíruje tyto protokoly do zadané sítě nebo místní sdílené složky.

    Použijí se následující parametry:

    - `-Path` -Zadejte síť nebo místní cestu ke zkopírování balíčku pro podporu. požadovanou
    - `-Credential` -Zadejte přihlašovací údaje pro přístup k chráněné cestě.
    - `-Zip` – Určete, aby se vygeneroval soubor zip.
    - `-Include` – Určuje, že se mají zahrnout součásti, které se mají zahrnout do balíčku pro podporu. Pokud není zadaný, `Default` předpokládá se.
    - `-IncludeArchived` – Určuje, že se mají do balíčku pro podporu zahrnout archivované protokoly.
    - `-IncludePeriodicStats` – Určuje, že se mají do balíčku pro podporu zahrnout protokoly pravidelného oznámení.

    
