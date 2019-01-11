---
title: Pomocí skriptů Azure Resource Manageru můžete spravovat zařízení StorSimple | Dokumentace Microsoftu
description: Zjistěte, jak pomocí Azure Resource Manageru skriptů pro automatizaci úloh StorSimple
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
ms.openlocfilehash: d9c428e3fa8d9fe964b83ae345bb70fd49a6ce1a
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54215299"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Použití skriptů založených na Azure Resource Manageru SDK ke správě zařízení StorSimple

Tento článek popisuje, jak založené na Azure Resource Manageru SDK skriptů můžete použít ke správě vašeho zařízení StorSimple řady 8000. Ukázkový skript je také součástí vás provede kroky pro konfiguraci prostředí svých ke spouštění těchto skriptů.

Tento článek se týká zařízení řady StorSimple 8000 se systémem na webu Azure portal pouze.

## <a name="sample-scripts"></a>Ukázkové skripty

Následující ukázkové skripty jsou k dispozici pro automatizaci různých úloh StorSimple.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Tabulka založené na Azure Resource Manageru SDK ukázkové skripty

| Skript Azure Resource Manageru                    | Popis                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Povolit ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Tento skript můžete autorizovat zařízení StorSimple, chcete-li změnit šifrovací klíč dat služby.                                                                                                           |
| [Vytvoření StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Tento skript vytvoří 8010 nebo 8020 řešení StorSimple Cloud Appliance. Cloudové zařízení můžete pak nakonfigurovat a registraci ve službě StorSimple Data Manageru.                                                       |
| [CreateOrUpdate Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Tento skript vytvoří nebo upraví svazky zařízení StorSimple.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Tento skript obsahuje všechny zálohy zařízení registraci ve službě Správce zařízení StorSimple.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Tento skript všechny zásady zálohování pro zařízení StorSimple.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Tento skript načte všechny StorSimple úlohy spuštěné ve službě Správce zařízení StorSimple.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Tento skript kontroluje server aktualizací a vás informuje, pokud jsou aktualizace dostupné k instalaci zařízení StorSimple.                                                                                          |
| [Instalace DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Tento skript nainstaluje na zařízení StorSimple dostupné aktualizace.                                                                                                                                           |
| [Správa CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Tento skript spustí ruční cloudový snímek a odstraní cloudové snímky, které jsou starší než dnů uchovávání.                                                                                                   |
| [Monitorování Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Tento skript prostředí PowerShell pro Azure Automation Runbook hlásí stav všech úloh zálohování.                                                                                                              |
| [Odebrat DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Tento skript odstraní jeden záložní objekt.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Tento skript spouští ruční zálohy na zařízení StorSimple.                                                                                                                                       |
| [Aktualizace CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Tento skript aktualizuje šifrovací klíč dat služby pro všechno, co registraci ve službě Správce zařízení StorSimple 8010/8020 řešení StorSimple Cloud Appliance.                                     |
| [Ověřte BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Tento skript ukazuje chybí zálohy po analýze všechny plány, které jsou přidružené k zásadám zálohování. Také ověří katalog záloh se seznam dostupných záloh.             |




## <a name="configure-and-run-a-sample-script"></a>Nakonfigurovat a spustit ukázkový skript

Tato část používá ukázkový skript a podrobně popisuje různé kroky potřebné ke spuštění skriptu.

### <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte:

*   Azure PowerShell nainstalovaný. Instalace modulů Azure Powershellu:
    * V prostředí Windows, postupujte podle kroků v [nainstalovat a nakonfigurovat Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0). Prostředí Azure PowerShell můžete nainstalovat na hostiteli s Windows serverem pro StorSimple Pokud pomocí jedné.
    * V prostředí Linuxu nebo MacOS, postupujte podle kroků v [instalace a konfigurace Azure Powershellu v systému MacOS nebo Linux](https://docs.microsoft.com/powershell/azure/azurerm/install-azurermps-maclinux?view=azurermps-4.4.0).

Další informace o použití Azure Powershellu najdete v části [začít pracovat s využitím Azure Powershellu](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azurermps-4.4.0).

### <a name="run-azure-powershell-script"></a>Spuštění skriptu Azure Powershellu

Skript, které v tomto příkladu obsahuje seznam všech úloh na zařízení StorSimple. To zahrnuje úlohy, které bylo úspěšné, se nezdařilo nebo jsou v průběhu. Proveďte následující kroky a stáhněte a spusťte skript.

1. Spusťte Azure PowerShell. Vytvoření nové složky a přejděte do nové složky.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Stáhněte si rozhraní příkazového řádku NuGet](http://www.nuget.org/downloads) v rámci složky vytvořené v předchozím kroku. Existují různé verze _nuget.exe_. Zvolte verzi odpovídající sady SDK. Každý odkaz ke stažení odkazuje přímo _.exe_ souboru. Nezapomeňte klikněte pravým tlačítkem a uložte soubor do počítače, spíše než jeho spuštění v prohlížeči.

    Můžete také spustit následující příkaz ke stažení a uložení skriptu ve stejné složce, kterou jste vytvořili dříve.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Stáhněte sadu SDK závislá.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Stáhněte si skript z ukázkového projektu Githubu.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Spusťte skript. Po zobrazení výzvy k ověření, zadejte své přihlašovací údaje Azure. Tento skript by měl výstupu filtrovaný seznam všech úloh na zařízení StorSimple.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Ukázkový výstup

Při spuštění ukázkového skriptu se zobrazí následující výstup. Výstup obsahuje všechny úlohy, které byly spuštěny na registrovaná zařízení, které na 25. září 2017 spustit a dokončit 2. října 2017.

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


## <a name="next-steps"></a>Další postup

[Služba Správce zařízení StorSimple používá ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).