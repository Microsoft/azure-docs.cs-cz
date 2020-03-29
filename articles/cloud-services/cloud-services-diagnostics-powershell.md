---
title: Povolení diagnostiky v Cloudových službách Azure pomocí PowerShellu | Dokumenty společnosti Microsoft
description: Zjistěte, jak povolit diagnostiku pro cloudové služby pomocí PowerShellu
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: tagore
ms.openlocfilehash: 76cdffed813fd182980b36f848e0ae42f3226539
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386540"
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Povolení diagnostiky v Cloudových službách Azure pomocí PowerShellu
Můžete shromažďovat diagnostická data, jako jsou protokoly aplikací, čítače výkonu atd. Tento článek popisuje, jak povolit rozšíření Diagnostika Azure pro cloudovou službu pomocí Prostředí PowerShell.  Přečtěte [si téma Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview) pro požadavky potřebné pro tento článek.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Povolit rozšíření diagnostiky jako součást nasazení cloudové služby
Tento přístup je použitelný pro typ průběžné integrace scénářů, kde rozšíření diagnostiky lze povolit jako součást nasazení cloudové služby. Při vytváření nového nasazení cloudové služby můžete povolit rozšíření diagnostiky předáním parametru *ExtensionConfiguration* do rutiny [New-AzureDeployment.](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-3.7.0) Parametr *ExtensionConfiguration* přebírá pole konfigurací diagnostiky, které lze vytvořit pomocí rutiny [New-AzureServiceDiagnosticsExtensionConfig.](/powershell/module/servicemanagement/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0)

Následující příklad ukazuje, jak můžete povolit diagnostiku pro cloudovou službu s WebRole a WorkerRole, z nichž každá má jinou konfiguraci diagnostiky.

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

Pokud konfigurační soubor `StorageAccount` diagnostiky určuje prvek s `New-AzureServiceDiagnosticsExtensionConfig` názvem účtu úložiště, rutina tento účet úložiště automaticky použije. Aby to fungovalo, musí být účet úložiště ve stejném předplatném jako naváděná cloudová služba.

Od Azure SDK 2.6 dále konfigurační soubory rozšíření generované výstupem cíle publikování MSBuild bude obsahovat název účtu úložiště na základě konfiguračního řetězce diagnostiky zadaný v konfiguračním souboru služby (.cscfg). Následující skript ukazuje, jak analyzovat konfigurační soubory rozšíření z výstupu cíle publikování a nakonfigurovat rozšíření diagnostiky pro každou roli při nasazování cloudové služby.

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

Visual Studio Online používá podobný přístup pro automatizované nasazení cloudových služeb s rozšířením diagnostiky. Úplný příklad najdete v [tématu Publish-AzureCloudDeployment.ps1.](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1)

Pokud `StorageAccount` nebyl zadán žádný v diagnostice konfigurace, pak je třeba předat v *StorageAccountName* parametr rutiny. Pokud *storageAccountName* parametr je zadán, pak rutina bude vždy používat účet úložiště, který je zadán v parametru a nikoli ten, který je zadán v diagnostickém konfiguračním souboru.

Pokud je účet úložiště diagnostiky v jiném předplatném než cloudová služba, musíte explicitně předat parametry *StorageAccountName* a *StorageAccountKey* do rutiny. *StorageAccountKey* Parametr není potřeba, pokud je účet úložiště diagnostiky ve stejném předplatném, protože rutina může automaticky dotazovat a nastavit hodnotu klíče při povolení rozšíření diagnostiky. Pokud je však účet úložiště diagnostiky v jiném předplatném, rutina nemusí být schopna získat klíč automaticky a je třeba explicitně zadat klíč prostřednictvím parametru *StorageAccountKey.*

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Povolit rozšíření diagnostiky na existující službu Cloud
Rutina [Set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) můžete použít k povolení nebo aktualizaci konfigurace diagnostiky v cloudové službě, která je již spuštěná.

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
Pomocí rutiny [Get-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) získáte aktuální konfiguraci diagnostiky pro cloudovou službu.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Odeberte rozšíření diagnostiky
Chcete-li vypnout diagnostiku v cloudové službě, můžete použít rutinu [Remove-AzureServiceDiagnosticsExtension.](/powershell/module/servicemanagement/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0)

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Pokud jste povolili rozšíření diagnostiky pomocí *Set-AzureServiceDiagnosticsExtension* nebo *New-AzureServiceDiagnosticsExtensionConfig* bez parametru *Role,* můžete rozšíření odebrat pomocí *Remove-AzureServiceDiagnosticsExtension* bez parametru *role.* Pokud *role* parametr byl použit při povolení rozšíření, pak musí být také použit při odebrání rozšíření.

Chcete-li odebrat rozšíření diagnostiky pro každou jednotlivou roli:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Další kroky
* Další pokyny k použití diagnostiky Azure a dalších technik k řešení problémů najdete [v tématu Povolení diagnostiky v Cloudových službách Azure a virtuálních počítačích](cloud-services-dotnet-diagnostics.md).
* [Schéma konfigurace diagnostiky](/azure/azure-monitor/platform/diagnostics-extension-schema-1dot3) vysvětluje různé možnosti konfigurace XML pro rozšíření diagnostiky.
* Informace o povolení rozšíření diagnostiky pro virtuální počítače najdete [v tématu Vytvoření virtuálního počítače s Windows pomocí monitorování a diagnostiky pomocí šablony Správce prostředků Azure.](../virtual-machines/windows/extensions-diagnostics-template.md)



