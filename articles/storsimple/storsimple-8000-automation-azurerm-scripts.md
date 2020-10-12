---
title: Použití skriptů PowerShellu pro AzureRM ke správě zařízení StorSimple
description: Naučte se používat Azure Resource Manager skripty založené na sadě SDK ke správě zařízení řady StorSimple 8000.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: 4a53dd803b22899b7dd15db0aa1ff411641a11dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88183254"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Použití Azure Resource Manager skriptů založených na sadě SDK ke správě zařízení StorSimple

Tento článek popisuje, jak lze pomocí Azure Resource Managerch skriptů založených na sadě SDK spravovat zařízení řady StorSimple 8000. K dispozici je také ukázkový skript, který vás provede jednotlivými kroky konfigurace prostředí pro spuštění těchto skriptů.

Tento článek se týká jenom zařízení řady StorSimple 8000, která běží jenom v Azure Portal.

## <a name="sample-scripts"></a>Ukázkové skripty

K dispozici jsou následující ukázkové skripty pro automatizaci různých úloh StorSimple.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Tabulka ukázkových skriptů založených na sadě Azure Resource Manager SDK

| Skript Azure Resource Manager                    | Description                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Authorize-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Tento skript vám umožní ověřit, že zařízení StorSimple mění šifrovací klíč dat služby.                                                                                                           |
| [Create-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Tento skript vytvoří StorSimple Cloud Appliance 8010 nebo 8020. Cloudové zařízení se pak dá nakonfigurovat a zaregistrovat ve službě StorSimple Data Manager.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Tento skript vytvoří nebo upraví StorSimple svazky.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Tento skript obsahuje seznam všech záloh pro zařízení zaregistrované ve službě StorSimple Device Manager.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Tento skript všechny zásady zálohování pro zařízení StorSimple.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Tento skript načte všechny úlohy StorSimple běžící ve službě StorSimple Device Manager.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Tento skript zkontroluje server aktualizací a umožní vám zjistit, jestli jsou aktualizace dostupné pro instalaci na zařízení StorSimple.                                                                                          |
| [Install-DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Tento skript nainstaluje dostupné aktualizace na zařízení StorSimple.                                                                                                                                           |
| [Manage-CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Tento skript spustí ruční snímek v cloudu a odstraní snímky cloudu starší než zadané dny uchování.                                                                                                   |
| [Monitor-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Tento Azure Automation skript Runbooku PowerShellu hlásí stav všech úloh zálohování.                                                                                                              |
| [Remove-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Tento skript odstraní jediný objekt zálohování.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Tento skript spustí ruční zálohování na zařízení StorSimple.                                                                                                                                       |
| [Update-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Tento skript aktualizuje šifrovací klíč dat služby pro všechna cloudová zařízení 8010/8020 StorSimple zaregistrovaná ve službě StorSimple Device Manager.                                     |
| [Verify-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Tento skript zvýrazní chybějící zálohy po analýze všech plánů přidružených k zásadám zálohování. Také ověří katalog záloh se seznamem dostupných záloh.             |




## <a name="configure-and-run-a-sample-script"></a>Konfigurace a spuštění ukázkového skriptu

V této části se používá ukázkový skript a podrobnosti o různých krocích potřebných ke spuštění skriptu.

### <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte následující:

*   Azure PowerShell nainstalován. Instalace Azure PowerShellch modulů:
    * V prostředí Windows postupujte podle pokynů v části [instalace a konfigurace Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte StorSimple, můžete na hostitele Windows serveru nainstalovat Azure PowerShell.
    * V prostředí Linux nebo MacOS postupujte podle pokynů v části [instalace a konfigurace Azure PowerShell v MacOS nebo Linux](/powershell/azure/install-az-ps).

Další informace o používání Azure PowerShell najdete na webu [Začínáme s používáním Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="run-azure-powershell-script"></a>Spustit skript Azure PowerShell

Skript použitý v tomto příkladu vypíše všechny úlohy na zařízení StorSimple. To zahrnuje úlohy, které byly úspěšné, neúspěšné nebo probíhající. Chcete-li stáhnout a spustit skript, proveďte následující kroky.

1. Spusťte Azure PowerShell. Vytvořte novou složku a změňte adresář na novou složku.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Stáhněte si NUGET CLI](https://www.nuget.org/downloads) do složky vytvořené v předchozím kroku. Existují různé verze _nuget.exe_. Vyberte verzi odpovídající vaší sadě SDK. Každý odkaz ke stažení odkazuje přímo na soubor _. exe_ . Ujistěte se, že kliknete pravým tlačítkem a uložíte soubor do počítače, ale nebudete ho spouštět z prohlížeče.

    Spuštěním následujícího příkazu můžete také stáhnout a uložit skript ve stejné složce, kterou jste vytvořili dříve.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Stáhněte si závislou sadu SDK.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Stáhněte si skript z ukázkového projektu GitHub.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Spusťte skript. Po zobrazení výzvy k ověření zadejte svoje přihlašovací údaje Azure. Tento skript by měl vypisovat filtrovaný seznam všech úloh na zařízení StorSimple.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Ukázkový výstup

Následující výstup se zobrazí při spuštění ukázkového skriptu. Výstup obsahuje všechny úlohy, které byly spuštěny na registrovaném zařízení, které začalo 25. září 2017 a skončilo 2. října 2017.

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

[Ke správě zařízení StorSimple použijte službu StorSimple Device Manager](storsimple-8000-manager-service-administration.md).
