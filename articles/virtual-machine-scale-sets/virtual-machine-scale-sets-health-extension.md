---
title: Použití rozšíření pro stav aplikací se službou Azure Virtual Machine Scale Sets
description: Naučte se používat rozšíření pro stav aplikací ke sledování stavu aplikací nasazených ve službě Virtual Machine Scale Sets.
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: mimckitt
ms.openlocfilehash: cb5f1d48bb1a95db004d9da553e19a35071c73b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81273728"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Použití rozšíření Stav aplikace se škálovacími sadami virtuálních počítačů
Monitorování stavu vaší aplikace je důležitým signálem pro správu a upgrade nasazení. Azure Virtual Machine Scale Sets poskytují podporu pro průběžné [upgrady](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) včetně [automatických upgradů BITOVÝCH kopií operačního systému](virtual-machine-scale-sets-automatic-upgrade.md), které jsou závislé na monitorování stavu jednotlivých instancí při upgradu vašeho nasazení.

Tento článek popisuje, jak můžete pomocí rozšíření pro stav aplikace monitorovat stav aplikací nasazených ve službě Virtual Machine Scale Sets.

## <a name="prerequisites"></a>Požadavky
V tomto článku se předpokládá, že máte zkušenosti s nástrojem:
-   [Rozšíření](../virtual-machines/extensions/overview.md) virtuálních počítačů Azure
-   [Úprava](virtual-machine-scale-sets-upgrade-scale-set.md) virtuálních počítačů Scale Sets

## <a name="when-to-use-the-application-health-extension"></a>Kdy použít rozšíření pro stav aplikace
Rozšíření pro stav aplikace se nasadí do instance sady škálování virtuálních počítačů a sestavuje zprávy o stavu virtuálního počítače v rámci instance sady škálování. Můžete nakonfigurovat rozšíření na testování koncového bodu aplikace a aktualizovat stav aplikace v této instanci. Tento stav instance kontroluje Azure, aby zjistil, jestli je instance způsobilá pro operace upgradu.

Jelikož rozšíření hlásí stav z virtuálního počítače, dá se použít v situacích, kdy nejde použít externí testy, jako je třeba sondy stavu aplikací (které využívají vlastní [testy](../load-balancer/load-balancer-custom-probe-overview.md)Azure Load Balancer).

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma pro rozšíření pro stav aplikace. Přípona vyžaduje minimálně požadavek TCP nebo http s přiřazeným portem nebo cestou k požadavku.

```json
{
  "type": "extensions",
  "name": "HealthExtension",
  "apiVersion": "2018-10-01",
  "location": "<location>",  
  "properties": {
    "publisher": "Microsoft.ManagedServices",
    "type": "< ApplicationHealthLinux or ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}  
```

### <a name="property-values"></a>Hodnoty vlastností

| Název | Hodnota/příklad | Typ dat
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | date |
| vydavatel | `Microsoft.ManagedServices` | řetězec |
| type | `ApplicationHealthLinux`(Linux), `ApplicationHealthWindows` (Windows) | řetězec |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Nastavení

| Název | Hodnota/příklad | Typ dat
| ---- | ---- | ----
| protokol | `http` nebo `tcp` | řetězec |
| port | Volitelné, pokud je `http`protokol povinný, pokud je protokol`tcp` | int |
| requestPath | Povinné, pokud je `http`protokol nepovolený, když je protokol`tcp` | řetězec |

## <a name="deploy-the-application-health-extension"></a>Nasazení rozšíření stavu aplikace
Rozšíření pro stav aplikace můžete nasadit do sady škálování, jak je popsáno níže v následujících příkladech.

### <a name="rest-api"></a>REST API

Následující příklad přidá rozšíření stavu aplikace (s názvem myHealthExtension) do extensionProfile v modelu sady škálování sady Windows se škálováním na více systémů.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/myHealthExtension?api-version=2018-10-01`
```

```json
{
  "name": "myHealthExtension",
  "properties": {
    "publisher": " Microsoft.ManagedServices",
    "type": "< ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}
```
Slouží `PATCH` k úpravě již nasazeného rozšíření.

### <a name="azure-powershell"></a>Azure PowerShell

Pomocí rutiny [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) přidejte rozšíření pro stav aplikace do definice modelu sady škálování.

Následující příklad přidá rozšíření stavu aplikace do `extensionProfile` v modelu sady škálování nastaveného na základě systému Windows. V příkladu se používá nový modul AZ PowerShell.

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```


### <a name="azure-cli-20"></a>Azure CLI 2.0

Pomocí [AZ VMSS Extension set](/cli/azure/vmss/extension#az-vmss-extension-set) přidejte rozšíření pro stav aplikace do definice modelu sady škálování.

Následující příklad přidá rozšíření stavu aplikace do modelu škálované sady pro sadu škálování na bázi systému Linux.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthLinux \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```
Obsah souboru extension. JSON.

```json
{
  "protocol": "<protocol>",
  "port": "<port>",
  "requestPath": "</requestPath>"
}
```


## <a name="troubleshoot"></a>Řešení potíží
Výstup spuštění rozšíření se protokoluje do souborů, které se nacházejí v následujících adresářích:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/apphealth
```

Protokoly také pravidelně zaznamenávají stav aplikace.

## <a name="next-steps"></a>Další kroky
Naučte se, jak [nasadit vaši aplikaci do služby](virtual-machine-scale-sets-deploy-app.md) Virtual Machine Scale Sets.
