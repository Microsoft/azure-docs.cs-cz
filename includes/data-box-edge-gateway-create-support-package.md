---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554977"
---
Pokud zaznamenáte problémy zařízení, můžete vytvořit balíček pro podporu ze systémových protokolů. Tento balíček použit Microsoft Support řešit problémy. Postupujte podle těchto kroků můžete vytvořit balíček pro podporu:

1. [Připojení k rozhraní PowerShell vašeho zařízení](#connect-to-the-powershell-interface).
2. Použití `Get-HcsNodeSupportPackage` příkazu vytvořte balíček pro podporu. Použití rutiny je následujícím způsobem:

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

    Rutina shromažďuje protokoly ze zařízení a zkopíruje tyto protokoly pro zadanou síť nebo místní sdílené složky.

    Parametry použité jsou následující:

    - `-Path` -Zadejte síť nebo místní cestu k zkopírujte balíček pro podporu. (požadováno)
    - `-Credential` -Zadejte přihlašovací údaje pro přístup k cestě chráněné.
    - `-Zip` -Zadejte ke generování souboru zip.
    - `-Include` -Zadané k vložení komponent, které mají být zahrnuty do balíčku pro podporu. Pokud není zadán, `Default` se předpokládá, že.
    - `-IncludeArchived` -Zadané k vložení archivované protokoly balíček pro podporu.
    - `-IncludePeriodicStats` -Zadejte zahrnout pravidelné stat – protokoly balíček pro podporu.

    
