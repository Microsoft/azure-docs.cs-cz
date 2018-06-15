---
title: Povolte diagnostiku ve službě Azure Cloud Services pomocí prostředí PowerShell | Microsoft Docs
description: Informace o povolení diagnostiky pro cloudové služby pomocí prostředí PowerShell
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: ''
ms.assetid: 66e08754-8639-4022-ae18-4237749ba17d
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: adegeo
ms.openlocfilehash: 8dd9724981860c9cd4ccc443cc2bfdc465811e7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23843424"
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Povolte diagnostiku ve službě Azure Cloud Services pomocí prostředí PowerShell
Můžete shromáždit diagnostických dat, jako jsou protokoly aplikací, čítače výkonu z cloudové služby pomocí rozšíření Azure Diagnostics atd. Tento článek popisuje, jak povolit rozšíření diagnostiky Azure pro cloudové služby pomocí prostředí PowerShell.  V tématu [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview) pro součásti potřebné k tomuto článku.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Povolit rozšíření diagnostiky jako součást nasazení cloudové služby
Tento postup se vztahuje na typ průběžnou integraci scénáře, kde můžete povolit rozšíření diagnostiky jako součást nasazení cloudové služby. Při vytváření nového nasazení cloudové služby můžete povolit rozšíření diagnostiky předávání *ExtensionConfiguration* parametru [New-AzureDeployment](/powershell/module/azure/new-azuredeployment?view=azuresmps-3.7.0) rutiny. *ExtensionConfiguration* parametr přijímá pole Konfigurace diagnostiky, které lze vytvořit pomocí [New-AzureServiceDiagnosticsExtensionConfig](/powershell/module/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0) rutiny.

Následující příklad ukazuje, jak můžete povolit diagnostiku pro cloudové služby, WebRole a WorkerRole, každý s jinou diagnostiky konfigurace.

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

Pokud konfigurační soubor Diagnostika Určuje `StorageAccount` element s názvem účtu úložiště, pak se `New-AzureServiceDiagnosticsExtensionConfig` rutiny budou automaticky používat tento účet úložiště. Tento postup vyžaduje účet úložiště musí být ve stejném předplatném jako cloudová služba nasazení.

Z Azure SDK 2.6 dále publikovat konfigurační soubory s příponou generované MSBuild cílový výstup bude obsahovat název účtu úložiště, podle konfigurace diagnostiky řetězec zadaný v konfiguračním souboru služby (.cscfg). Skript uvedený níže ukazuje, jak analyzovat rozšíření konfigurační soubory z výstupu cíl publikování a konfiguraci rozšíření diagnostiky pro každou roli při nasazení cloudové služby.

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

Visual Studio Online používá podobný postup pro automatické nasazení cloudových služeb s rozšíření diagnostiky. V tématu [publikovat AzureCloudDeployment.ps1](https://github.com/Microsoft/vso-agent-tasks/blob/master/Tasks/AzureCloudPowerShellDeployment/Publish-AzureCloudDeployment.ps1) kompletní příklad.

Pokud žádné `StorageAccount` zadaná v konfiguraci diagnostiky, je třeba předat *StorageAccountName* do rutiny parametr. Pokud *StorageAccountName* je zadán parametr a potom rutinu bude vždy používat účet úložiště, který je zadaný v parametru a není ten, který je uveden v konfiguračním souboru diagnostiky.

Pokud se účet úložiště diagnostiky v jiném předplatném. z cloudové služby, pak je potřeba explicitně předat *StorageAccountName* a *StorageAccountKey* parametry rutiny. *StorageAccountKey* parametr není nutný, pokud je účet úložiště diagnostiky ve stejném předplatném jako rutiny můžete automaticky vyhledat a nastavit hodnotu klíče při povolování rozšíření diagnostiky. Pokud je účet úložiště diagnostiky v jiném předplatném, potom rutinu nemusí být možné získat klíč automaticky a je potřeba explicitně zadat klíč prostřednictvím *StorageAccountKey* parametr.

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Povolit rozšíření diagnostiky na existující službu Cloud
Můžete použít [Set-AzureServiceDiagnosticsExtension](/powershell/module/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) rutiny k povolení nebo aktualizovat konfiguraci diagnostiky na Cloudová služba, která je již spuštěna.

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
Použití [Get-AzureServiceDiagnosticsExtension](/powershell/module/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) rutiny získat aktuální konfiguraci diagnostiky pro cloudové služby.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Odeberte rozšíření diagnostiky
Chcete-li vypnout diagnostiky v cloudové službě, můžete použít [odebrat AzureServiceDiagnosticsExtension](/powershell/module/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0) rutiny.

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Pokud jste povolili rozšíření diagnostiky pomocí *Set-AzureServiceDiagnosticsExtension* nebo *New-AzureServiceDiagnosticsExtensionConfig* bez *Role* parametr pak je můžete rozšíření odebrat pomocí *odebrat AzureServiceDiagnosticsExtension* bez *Role* parametr. Pokud *Role* parametr byla použita při povolení rozšíření mělo musí být rovněž použit při odebírání rozšíření.

Chcete-li odebrat rozšíření diagnostiky pro každou jednotlivou roli:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Další kroky
* Další pokyny k odstraňování problémů pomocí diagnostiky Azure a další metody, najdete v části [povolení diagnostiky Azure Cloud Services a virtuálních počítačů](cloud-services-dotnet-diagnostics.md).
* [Schéma konfigurace diagnostiky](https://msdn.microsoft.com/library/azure/dn782207.aspx) vysvětluje různé možnosti konfigurace xml pro rozšíření diagnostiky.
* Zjistěte, jak povolit rozšíření diagnostiky pro virtuální počítače, najdete v tématu [vytvořit Windows virtuální počítač s monitorování a Diagnostika pomocí šablony Azure Resource Manageru](../virtual-machines/windows/extensions-diagnostics-template.md)
