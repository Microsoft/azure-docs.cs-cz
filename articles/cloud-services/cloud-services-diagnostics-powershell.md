---
title: Povolení diagnostiky v Azure Cloud Services pomocí Powershellu | Dokumentace Microsoftu
description: Zjistěte, jak povolit diagnostiku pro cloud services pomocí Powershellu
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 66e08754-8639-4022-ae18-4237749ba17d
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: jeconnoc
ms.openlocfilehash: c46c0665eefd7615bf90aeca7b918ddf9195237f
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004859"
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Povolení diagnostiky v Azure Cloud Services pomocí Powershellu
Můžete shromažďovat diagnostická data, jako jsou protokoly aplikací, čítače výkonu z cloudové služby pomocí rozšíření Azure Diagnostics atd. Tento článek popisuje, jak povolit rozšíření diagnostiky Azure pro cloudové služby pomocí prostředí PowerShell.  Zobrazit [instalace a konfigurace Azure Powershellu](/powershell/azure/overview) pro součásti potřebné pro účely tohoto článku.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Povolit rozšíření diagnostiky jako součást nasazení cloudové služby
Tento přístup se vztahuje na typ kontinuální integrace scénářů, kde lze povolit rozšíření diagnostiky jako součást nasazení cloudové služby. Při vytváření nového nasazení cloudové služby můžete povolit rozšíření diagnostiky předáním *ExtensionConfiguration* parametr [New-AzureDeployment](/powershell/module/azure/new-azuredeployment?view=azuresmps-3.7.0) rutiny. *ExtensionConfiguration* parametr přijímá pole Konfigurace diagnostiky, které je možné vytvořit [New-AzureServiceDiagnosticsExtensionConfig](/powershell/module/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0) rutiny.

Následující příklad ukazuje, jak můžete povolit diagnostiku pro cloudovou službu s WebRole a WorkerRole nichž každý má konfiguraci různých diagnostiky.

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

Pokud konfigurační soubor diagnostiky má `StorageAccount` element s názvem účtu úložiště, pak bude `New-AzureServiceDiagnosticsExtensionConfig` rutiny automaticky použije tento účet úložiště. Aby to fungovalo musí být ve stejném předplatném jako cloudová služba nasazovaný účet úložiště.

Z Azure SDK 2.6 dále konfigurační soubory s příponou generovaných MSBuild publikovat výstup cíle zahrne název účtu úložiště založené na řetězci konfigurace diagnostiky zadané v konfiguračním souboru služby (.cscfg). Skript uvedený níže ukazuje, jak analyzovat rozšíření konfigurační soubory v cílovém výstup publikování a nakonfigurovat diagnostické rozšíření pro každou roli při nasazení cloudové služby.

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

Visual Studio Online používá podobný přístup pro automatizovaná nasazení cloudových služeb s diagnostickým rozšířením. Zobrazit [publikovat AzureCloudDeployment.ps1](https://github.com/Microsoft/vso-agent-tasks/blob/master/Tasks/AzureCloudPowerShellDeployment/Publish-AzureCloudDeployment.ps1) kompletní příklad.

Pokud ne `StorageAccount` byl zadaný v konfiguraci diagnostiky, je nutné předat *StorageAccountName* do rutiny parametr. Pokud *StorageAccountName* zadaný parametr, bude rutina bude vždy používat účet úložiště, který je zadaný v parametru a není ten, který je zadaný v konfigurační soubor diagnostiky.

Pokud je účet úložiště diagnostiky v jiném předplatném z cloudové služby, je nutné explicitně předávat *StorageAccountName* a *StorageAccountKey* parametry rutiny. *StorageAccountKey* parametr není nutná, pokud účet úložiště diagnostiky je ve stejném předplatném, protože rutina automaticky můžete zadávat dotazy a nastavte hodnotu klíče při povolování rozšíření diagnostiky. Pokud je účet úložiště diagnostiky v jiném předplatném, pak rutina nemusí být možné získat klíč automaticky a je potřeba explicitně zadat klíč prostřednictvím *StorageAccountKey* parametru.

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Povolit rozšíření diagnostiky na existující službu Cloud
Můžete použít [Set-AzureServiceDiagnosticsExtension](/powershell/module/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) rutina pro povolení nebo aktualizovat konfiguraci diagnostiky v cloudové službě, která je již spuštěna.

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
Použití [Get-AzureServiceDiagnosticsExtension](/powershell/module/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) rutiny získejte aktuální konfiguraci diagnostiky pro cloudovou službu.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Odeberte rozšíření diagnostiky
Chcete-li vypnout diagnostiky v cloudové službě, můžete použít [odebrat AzureServiceDiagnosticsExtension](/powershell/module/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0) rutiny.

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Pokud jste povolili rozšíření diagnostiky pomocí *Set-AzureServiceDiagnosticsExtension* nebo *New-AzureServiceDiagnosticsExtensionConfig* bez *Role*parametr, pak je můžete rozšíření odebrat pomocí *odebrat AzureServiceDiagnosticsExtension* bez *Role* parametru. Pokud *Role* byl použit parametr při povolování rozšíření pak musí být rovněž použita při odebírání rozšíření.

Chcete-li odebrat rozšíření diagnostiky pro každou jednotlivou roli:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Další kroky
* Další informace o použití diagnostiky Azure a další techniky k řešení problémů naleznete v tématu [povolení diagnostiky v Azure Cloud Services a Virtual Machines](cloud-services-dotnet-diagnostics.md).
* [Schéma konfigurace diagnostiky](https://msdn.microsoft.com/library/azure/dn782207.aspx) vysvětluje různé možnosti konfigurace xml pro rozšíření diagnostiky.
* Zjistěte, jak povolit rozšíření diagnostiky pro virtuální počítače, naleznete v tématu [vytvořit Windows virtuální počítač s monitorováním a diagnostikou pomocí šablony Azure Resource Manageru](../virtual-machines/windows/extensions-diagnostics-template.md)
