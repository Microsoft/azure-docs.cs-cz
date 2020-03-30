---
title: Správa zařízení StorSimple pomocí skriptů AzureRM PowerShell
description: Zjistěte, jak pomocí skriptů Azure Resource Manageru automatizovat úlohy StorSimple
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: 03a5ef49b2d58d351d882b30b5d11e4a5ba90264
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471954"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Ke správě zařízení StorSimple použijte skripty založené na sdk azure resource manageru.

Tento článek popisuje, jak lze skripty založené na azure resource manageru SDK použít ke správě zařízení řady StorSimple 8000. Ukázkový skript je také zahrnuta vás provede kroky konfigurace prostředí pro spuštění těchto skriptů.

Tento článek se vztahuje na zařízení řady StorSimple 8000 spuštěná jenom na webu Azure Portal.

## <a name="sample-scripts"></a>Ukázkové skripty

Následující ukázkové skripty jsou k dispozici pro automatizaci různých úloh StorSimple.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Tabulka ukázkových skriptů založených na sdk správce prostředků Azure

| Skript Správce prostředků Azure                    | Popis                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Autorizovat-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Tento skript umožňuje autorizovat zařízení StorSimple ke změně šifrovacího klíče dat služby.                                                                                                           |
| [Vytvořit-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Tento skript vytvoří 8010 nebo 8020 StorSimple Cloud Appliance. Cloudové zařízení pak můžete nakonfigurovat a zaregistrovat pomocí služby StorSimple Data Manager.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Tento skript vytvoří nebo upraví svazky StorSimple.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Tento skript obsahuje seznam všech záloh pro zařízení registrované ve službě Správce zařízení StorSimple.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Tento skript všechny zásady zálohování pro vaše zařízení StorSimple.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Tento skript získá všechny úlohy StorSimple spuštěné ve službě Správce zařízení StorSimple.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Tento skript prohledá aktualizační server a informuje vás, zda jsou k dispozici aktualizace pro instalaci do zařízení StorSimple.                                                                                          |
| [Instalace deviceupdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Tento skript nainstaluje dostupné aktualizace do zařízení StorSimple.                                                                                                                                           |
| [Správa cloudových snímků.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Tento skript spustí ruční snímek cloudu a odstraní snímky cloudu starší než zadané dny uchovávání.                                                                                                   |
| [Monitor-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Tento skript Prostředí Azure Automation Runbook PowerShell hlásí stav všech úloh zálohování.                                                                                                              |
| [Odebrat-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Tento skript odstraní jeden záložní objekt.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Tento skript spustí ruční zálohování na zařízení StorSimple.                                                                                                                                       |
| [Aktualizace CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Tento skript aktualizuje šifrovací klíč dat služby pro všechna cloudová zařízení 8010/8020 StorSimple registrovaná ve službě StorSimple Device Manager.                                     |
| [Ověřit-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Tento skript zvýrazní chybějící zálohy po analýze všech plánů přidružených k zásadám zálohování. Také ověří katalog záloh se seznamem dostupných záloh.             |




## <a name="configure-and-run-a-sample-script"></a>Konfigurace a spuštění ukázkového skriptu

Tato část obsahuje ukázkový skript a podrobně popisuje různé kroky potřebné ke spuštění skriptu.

### <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte:

*   Nainstalovaný Azure PowerShell. Instalace modulů Azure PowerShellu:
    * V prostředí Windows postupujte podle pokynů v [části Instalace a konfigurace Azure PowerShellu](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). Azure PowerShell můžete nainstalovat na hostitele Windows Serveru pro váš StorSimple, pokud ho používáte.
    * V prostředí Linuxu nebo MacOS postupujte podle pokynů v [části Instalace a konfigurace Azure PowerShellu na MacOS nebo Linuxu](https://docs.microsoft.com/powershell/azure/azurerm/install-azurermps-maclinux).

Další informace o používání Azure PowerShellu najdete [v najdete v začínáme s používáním Azure PowerShellu](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="run-azure-powershell-script"></a>Spuštění skriptu Azure PowerShellu

Skript použitý v tomto příkladu uvádí všechny úlohy na zařízení StorSimple. To zahrnuje úlohy, které byly úspěšné, neúspěšné nebo probíhají. Chcete-li skript stáhnout a spustit, proveďte následující kroky.

1. Spusťte Azure PowerShell. Vytvořte novou složku a změňte adresář na novou složku.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Stáhněte si rozhraní se zástupcem vazby NuGet](https://www.nuget.org/downloads) pod složku vytvořenou v předchozím kroku. Existují různé verze _nuget.exe_. Zvolte verzi odpovídající vaší sadě SDK. Každý odkaz ke stažení odkazuje přímo na soubor _EXE._ Nezapomeňte soubor klepnout pravým tlačítkem myši a uložit jej do počítače, nikoli jej spustit z prohlížeče.

    Můžete také spustit následující příkaz ke stažení a uložení skriptu ve stejné složce, kterou jste vytvořili dříve.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Stáhněte závislou sadu SDK.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Stáhněte si skript z ukázkového projektu GitHub.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Spusťte skript. Po zobrazení výzvy k ověření zadejte svá pověření Azure. Tento skript by měl výstup filtrovaný seznam všech úloh na zařízení StorSimple.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Ukázkový výstup

Následující výstup je uveden při spuštění ukázkového skriptu. Výstup obsahuje všechny úlohy, které běžely na registrovaném zařízení, které začalo 25.

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

[Ke správě zařízení StorSimple použijte službu StorSimple](storsimple-8000-manager-service-administration.md)Device Manager .
