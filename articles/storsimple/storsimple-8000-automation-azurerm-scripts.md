---
title: Ke správě zařízení StorSimple použít skripty Azure Resource Manager | Microsoft Docs
description: Naučte se používat Azure Resource Manager skripty pro automatizaci úloh StorSimple
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: f4456200d6f497a87424f12a23034dbff00c75aa
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2017
ms.locfileid: "26373937"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Použití skriptů založených na Azure Resource Manager SDK ke správě zařízení StorSimple

Tento článek popisuje, jak na základě Azure Resource Manager SDK skripty slouží ke správě vašich zařízení řady StorSimple 8000. Ukázkový skript je také součástí vás provede kroky pro konfiguraci vašeho prostředí a spusťte tyto skripty.

Tento článek se týká spuštění na portálu Azure pouze řadu zařízení StorSimple 8000.

## <a name="sample-scripts"></a>Ukázkové skripty

Následující ukázkové skripty jsou k dispozici k automatizaci různých úloh StorSimple.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Tabulka založené na Azure Resource Manager SDK ukázkové skripty

| Azure Resource Manager skriptu                    | Popis                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Autorizovat ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Tento skript můžete pro autorizaci zařízení StorSimple, chcete-li změnit šifrovacího klíče dat služby.                                                                                                           |
| [Vytvoření StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Tento skript vytvoří 8010 nebo 8020 cloudu zařízení StorSimple. Zařízení cloudu můžete pak nakonfigurovat a zaregistrovali služby StorSimple Data Manager.                                                       |
| [CreateOrUpdate Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Tento skript vytvoří nebo upraví svazky zařízení StorSimple.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Tento skript obsahuje seznam všech zálohy pro zařízení registrovaná pomocí služby StorSimple Manager zařízení.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Tento skript všechny zásady zálohování pro zařízení StorSimple.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Tento skript načte všechny úlohy systémem služby StorSimple Manager zařízení StorSimple.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Tento skript kontroluje server aktualizací a umožňuje zjistit, zda jsou aktualizace dostupné pro instalaci do vašeho zařízení StorSimple.                                                                                          |
| [Instalace DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Tento skript nainstaluje dostupné aktualizace zařízení StorSimple.                                                                                                                                           |
| [Spravovat CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Tento skript spustí ruční cloudový snímek a odstraní starší než dní uchovávání cloudových snímků.                                                                                                   |
| [Monitorování Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Tento skript Powershellu pro Azure Automation Runbook hlásí stav všechny úlohy zálohování.                                                                                                              |
| [Odebrat DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Tento skript odstraní jednoho objektu zálohy.                                                                                                                                                           |
| [Počáteční DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Tento skript spustí ruční zálohy zařízení StorSimple.                                                                                                                                       |
| [Aktualizace CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Tento skript aktualizace šifrovacího klíče dat služby pro všechny zaregistrované zařízení 8010/8020 StorSimple cloudu pomocí služby StorSimple Manager zařízení.                                     |
| [Ověřte BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Tento skript označuje chybí zálohy po analýze všechny plány, které jsou přidružené k zásadám zálohování. Také ověřuje katalogu zálohování se seznam dostupných záloh.             |




## <a name="configure-and-run-a-sample-script"></a>Konfiguraci a spuštění ukázkového skriptu

Tato část trvá ukázkový skript a podrobnosti o různých kroky potřebné ke spuštění skriptu.

### <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, zda máte:

*   Nainstalovat Azure PowerShell. Instalace modulů prostředí Azure PowerShell:
    * V prostředí Windows, postupujte podle kroků v [nainstalovat a nakonfigurovat Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0). Azure PowerShell můžete nainstalovat na hostiteli s Windows Server pro vaše zařízení StorSimple a pokud pomocí jedné.
    * V prostředí Linux nebo systému MacOS, postupujte podle kroků v [nainstalovat a nakonfigurovat Azure PowerShell v systému MacOS nebo Linux](https://docs.microsoft.com/powershell/azure/install-azurermps-maclinux?view=azurermps-4.4.0).

Další informace o použití prostředí Azure PowerShell, přejděte na [začít s použitím prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azurermps-4.4.0).

### <a name="run-azure-powershell-script"></a>Spustit skript prostředí Azure PowerShell

Skript použité v tomto příkladu obsahuje seznam všech úloh v zařízení StorSimple. To zahrnuje úlohy, které bylo úspěšné, se nezdařilo nebo jsou v průběhu. Proveďte následující kroky ke stažení a spuštění skriptu.

1. Spusťte Azure PowerShell. Vytvořte novou složku a změnit adresář, do nové složky.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Stáhnout rozhraní příkazového řádku NuGet](http://www.nuget.org/downloads) ve složce vytvořili v předchozím kroku. Existují různé verze _nuget.exe_. Vyberte odpovídající vaší SDK verze. Každý odkaz ke stažení přímo na body _.exe_ souboru. Klikněte pravým tlačítkem myši a uložte soubor do počítače se spíše než spuštěna z prohlížeče.

    Můžete taky spustit následující příkaz ke stažení a uložení skriptu ve stejné složce, kterou jste vytvořili dříve.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Stažení sady SDK závislé.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Stáhněte skript z ukázkového projektu Githubu.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Spusťte skript. Po zobrazení výzvy k ověření, zadejte přihlašovací údaje Azure. Tento skript by měl výstupní filtrovaný seznam všech úloh v zařízení StorSimple.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Ukázkový výstup

Tento výstup se zobrazí při spuštění ukázkového skriptu. Výstup obsahuje všechny úlohy, které byly spuštěny v zaregistrované zařízení, které bylo zahájeno 25 září 2017 a dokončit 2 říjen 2017.

```
-----------------------------------------
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1
PS C:\Scripts\StorSimpleSDKTools> .\Get-DeviceJobs.ps1 -SubscriptionId 1234ab5c-678d-910e-9fc4-0accc9c0166e -TenantId 12a345bc-67d8-91ef-01ab-2c7cd123ef45 -DeviceName 8600-ABC1234567D89EF -ResourceGroupName Contoso -ManagerName ContosoDeviceMgr -FilterByStartTime "09/25/2017 08:10:02" -FilterByEndTime "10/02/2017 08:10:02"


Status            : Succeeded
StartTime         : 10/2/2017 10:30:02 PM
EndTime           : 10/2/2017 10:31:36 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : ss-asr-policy1
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC/jobs/75905c48-b153-4af1-8b21-4b9a2ff9
                    825b
Name              : 75905c48-b153-4af1-8b21-4b9a2ff9825b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000
-------------------------------------------
CUT              CUT  
-------------------------------------------
Status            : Succeeded
StartTime         : 9/26/2017 5:00:02 PM
EndTime           : 9/26/2017 5:01:20 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : 8010 policy
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF/jobs/3cfd8108-db60-4e9a-a8da-6d8fe457
                    8d2b
Name              : 3cfd8108-db60-4e9a-a8da-6d8fe4578d2b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000



PS C:\Scripts\StorSimpleSDKTools>
--------------------------------------------

```


## <a name="next-steps"></a>Další kroky

[Pomocí Správce zařízení StorSimple služby ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).