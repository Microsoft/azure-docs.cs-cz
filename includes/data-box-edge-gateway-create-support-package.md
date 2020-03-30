---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175466"
---
Pokud dojde k problémům se zařízením, můžete vytvořit balíček podpory ze systémových protokolů. Podpora společnosti Microsoft používá tento balíček k řešení problémů. Chcete-li vytvořit balíček podpory, postupujte takto:

1. [Připojte se k rozhraní prostředí PowerShell vašeho zařízení](#connect-to-the-powershell-interface).
2. Pomocí `Get-HcsNodeSupportPackage` příkazu vytvořte balíček podpory. Použití rutiny je následující:

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

    Rutina shromažďuje protokoly z vašeho zařízení a zkopíruje tyto protokoly do zadané sítě nebo místní sdílené složky.

    Použité parametry jsou následující:

    - `-Path`- Zadejte síť nebo místní cestu ke kopírování balíčku podpory. (povinné)
    - `-Credential`- Zadejte pověření pro přístup k chráněné cestě.
    - `-Zip`- Zadejte pro generování souboru zip.
    - `-Include`- Zadejte zahrnout součásti, které mají být zahrnuty do balíčku podpory. Pokud není `Default` zadán, předpokládá se.
    - `-IncludeArchived`- Zadejte zahrnout archivované protokoly do balíčku podpory.
    - `-IncludePeriodicStats`- Zadejte zahrnout periodické stat protokoly v balíčku podpory.

    
