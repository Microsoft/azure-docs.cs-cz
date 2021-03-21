---
title: Povolení diagnostiky v Azure Cloud Services (Classic) pomocí PowerShellu | Microsoft Docs
description: Naučte se, jak pomocí PowerShellu Povolit shromažďování diagnostických dat z cloudové služby Azure s rozšířením Azure Diagnostics.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 23528190d2eb60cc6ea3fe94bb9f7a2374526f6a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618436"
---
# <a name="enable-diagnostics-in-azure-cloud-services-classic-using-powershell"></a>Povolení diagnostiky v Azure Cloud Services (Classic) pomocí PowerShellu

> [!IMPORTANT]
> [Azure Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md) je nový model nasazení založený na Azure Resource Manager pro produkt Azure Cloud Services.V důsledku této změny se Azure Cloud Services běžící na modelu nasazení založeném na Azure Service Manager přejmenovala jako Cloud Services (Classic) a všechna nová nasazení by měla používat [Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md).

Můžete shromažďovat diagnostická data, jako jsou protokoly aplikací, čítače výkonu atd. z cloudové služby pomocí rozšíření Azure Diagnostics. Tento článek popisuje, jak povolit rozšíření Azure Diagnostics pro cloudovou službu pomocí PowerShellu.  Potřebné předpoklady pro tento článek najdete v tématu [Postup instalace a konfigurace Azure PowerShell](/powershell/azure/) .

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Povolit rozšíření diagnostiky jako součást nasazení cloudové služby
Tento přístup se vztahuje na typ souvislé integrace scénářů, kde diagnostické rozšíření může být povolené v rámci nasazení cloudové služby. Při vytváření nového nasazení cloudové služby můžete povolit rozšíření diagnostiky tak, že do rutiny [New-AzureDeployment](/powershell/module/servicemanagement/azure.service/new-azuredeployment) předáte parametr *ExtensionConfiguration* . Parametr *ExtensionConfiguration* přebírá pole konfigurací diagnostiky, které lze vytvořit pomocí rutiny [New-AzureServiceDiagnosticsExtensionConfig](/powershell/module/servicemanagement/azure.service/new-azureservicediagnosticsextensionconfig) .

Následující příklad ukazuje, jak můžete povolit diagnostiku pro cloudovou službu pomocí webrole a role pracovního procesu, z nichž každá má jinou konfiguraci diagnostiky.

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

Pokud konfigurační soubor diagnostiky Určuje `StorageAccount` element s názvem účtu úložiště, použije `New-AzureServiceDiagnosticsExtensionConfig` rutina automaticky tento účet úložiště. Aby to fungovalo, musí být účet úložiště ve stejném předplatném, jako je nasazená cloudová služba.

Z Azure SDK 2,6 další konfigurační soubory rozšíření generované výstupem cíle publikování MSBuild budou zahrnovat název účtu úložiště na základě konfiguračního řetězce pro diagnostiku, který je zadaný v konfiguračním souboru služby (. cscfg). Skript níže ukazuje, jak analyzovat konfigurační soubory rozšíření z výstupu cíle publikování a nakonfigurovat diagnostické rozšíření pro každou roli při nasazení cloudové služby.

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

Visual Studio Online používá podobný přístup pro automatizované nasazení Cloud Services s diagnostickým rozšířením. Úplný příklad najdete v tématu [Publish-AzureCloudDeployment.ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) .

Pokud se `StorageAccount` v konfiguraci diagnostiky nezadal žádný, pak musíte rutinu předat parametr *StorageAccountName* . Je-li zadán parametr *StorageAccountName* , rutina vždy použije účet úložiště, který je zadán v parametru, a nikoli ten, který je zadán v konfiguračním souboru diagnostiky.

Pokud je účet úložiště diagnostiky v jiném předplatném, než je cloudová služba, musíte explicitně předat parametry *StorageAccountName* a *StorageAccountKey* do rutiny. Parametr *StorageAccountKey* není potřeba, pokud je účet úložiště diagnostiky ve stejném předplatném, protože se rutina může automaticky dotazovat a nastavit hodnotu klíče při povolování diagnostického rozšíření. Pokud je však účet úložiště diagnostiky v jiném předplatném, rutina nemusí být schopna získat klíč automaticky a Vy musíte explicitně zadat klíč prostřednictvím parametru *StorageAccountKey* .

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Povolit rozšíření diagnostiky na existující službu Cloud
Pomocí rutiny [set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azureservicediagnosticsextension) můžete povolit nebo aktualizovat konfiguraci diagnostiky v cloudové službě, která je už spuštěná.

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name
```

## <a name="get-current-diagnostics-extension-configuration"></a>Získejte aktuální konfiguraci rozšíření diagnostiky
Pomocí rutiny [Get-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/get-azureservicediagnosticsextension) získáte aktuální konfiguraci diagnostiky pro cloudovou službu.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Odeberte rozšíření diagnostiky
Pokud chcete vypnout diagnostiku v cloudové službě, můžete použít rutinu [Remove-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/remove-azureservicediagnosticsextension) .

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Pokud jste povolili diagnostické rozšíření pomocí *set-AzureServiceDiagnosticsExtension* nebo *New-AzureServiceDiagnosticsExtensionConfig* bez parametru *role* , můžete rozšíření odebrat pomocí funkce *Remove-AzureServiceDiagnosticsExtension* bez parametru *role* . Pokud byl parametr *role* použit při povolení rozšíření, musí být použit také při odebrání rozšíření.

Chcete-li odebrat rozšíření diagnostiky pro každou jednotlivou roli:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Další kroky
* Další pokyny týkající se používání diagnostiky Azure a dalších postupů k řešení problémů najdete v tématu [Povolení diagnostiky v Azure Cloud Services a Virtual Machines](cloud-services-dotnet-diagnostics.md).
* [Schéma konfigurace diagnostiky](../azure-monitor/agents/diagnostics-extension-schema-windows.md) vysvětluje různé možnosti konfigurace XML pro diagnostické rozšíření.
* Informace o tom, jak povolit rozšíření diagnostiky pro Virtual Machines, najdete v tématu [Vytvoření virtuálního počítače s Windows pomocí monitorování a diagnostiky pomocí šablony Azure Resource Manager](../virtual-machines/extensions/diagnostics-template.md) .