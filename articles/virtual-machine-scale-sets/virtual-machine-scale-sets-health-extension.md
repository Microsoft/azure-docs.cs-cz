---
title: Použití rozšíření Stav aplikace se škálovacími sadami virtuálních strojů Azure
description: Zjistěte, jak pomocí rozšíření Stav aplikace sledovat stav vašich aplikací nasazených ve škálovacích sadách virtuálních strojů.
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: mimckitt
ms.openlocfilehash: cb5f1d48bb1a95db004d9da553e19a35071c73b0
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273728"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Použití rozšíření Stav aplikace se škálovacími sadami virtuálních počítačů
Sledování stavu aplikace je důležitým signálem pro správu a upgrade nasazení. Škálovací sady virtuálních strojů Azure poskytují podporu pro [postupné upgrady](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) včetně [automatických upgradů image operačního systému](virtual-machine-scale-sets-automatic-upgrade.md), které při upgradu nasazení spoléhají na monitorování stavu jednotlivých instancí.

Tento článek popisuje, jak můžete použít rozšíření Stav aplikace ke sledování stavu vašich aplikací nasazených ve škálovacích sadách virtuálních strojů.

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste obeznámeni s:
-   [Rozšíření virtuálních](../virtual-machines/extensions/overview.md) strojů Azure
-   [Úprava škálovacích](virtual-machine-scale-sets-upgrade-scale-set.md) sad virtuálních strojů

## <a name="when-to-use-the-application-health-extension"></a>Kdy použít rozšíření Stav aplikace
Rozšíření Stav aplikace se nasadí uvnitř instance škálovací sady virtuálních strojů a hlásí stav virtuálního počítače z instance škálovací sady. Můžete nakonfigurovat rozšíření pro sondování na koncovém bodu aplikace a aktualizovat stav aplikace v této instanci. Tento stav instance je kontrolována Azure k určení, zda instance je způsobilá pro operace upgradu.

Jako rozšíření hlásí stav z v rámci virtuálního počítače, rozšíření lze použít v situacích, kdy externí sondy, jako jsou sondy stavu aplikace (které využívají vlastní azure nástroje pro vyrovnávání zatížení [sondy](../load-balancer/load-balancer-custom-probe-overview.md)) nelze použít.

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON ukazuje schéma pro rozšíření stav aplikace. Rozšíření vyžaduje minimálně požadavek "tcp" nebo "http" s přidruženým portem nebo cestou požadavku.

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

| Name (Název) | Hodnota / Příklad | Typ dat
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | date |
| vydavatel | `Microsoft.ManagedServices` | řetězec |
| type | `ApplicationHealthLinux`(Linux), `ApplicationHealthWindows` (Windows) | řetězec |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Nastavení

| Name (Název) | Hodnota / Příklad | Typ dat
| ---- | ---- | ----
| Protokol | `http` nebo `tcp` | řetězec |
| port | Nepovinné, `http`pokud je protokol , povinný, pokud je protokol`tcp` | int |
| requestPath | Povinné, pokud `http`protokol není povolen, pokud je protokol`tcp` | řetězec |

## <a name="deploy-the-application-health-extension"></a>Nasazení rozšíření Stav aplikace
Existuje několik způsobů nasazení rozšíření Stav aplikace pro škálovací sady, jak je podrobně popsáno v následujících příkladech.

### <a name="rest-api"></a>REST API

Následující příklad přidá rozšíření Stav aplikace (s názvem myHealthExtension) do rozšířeníProfil v modelu škálovací sady škálovací sady systému Windows.

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

Pomocí rutiny [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) přidejte rozšíření Stavu aplikace do definice modelu škálovací sady.

Následující příklad přidá rozšíření Stav `extensionProfile` aplikace do modelu škálovací sady škálovací sady systému Windows. Příklad používá nový modul Az PowerShell.

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

Pomocí [sady rozšíření az vmss](/cli/azure/vmss/extension#az-vmss-extension-set) přidejte rozšíření Stavu aplikace do definice modelu škálovací sady.

Následující příklad přidá rozšíření Stav aplikace do modelu škálovací sady škálovací sady škálovací sady založené na Linuxu.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthLinux \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```
Obsah souboru.json.

```json
{
  "protocol": "<protocol>",
  "port": "<port>",
  "requestPath": "</requestPath>"
}
```


## <a name="troubleshoot"></a>Řešení potíží
Výstup spuštění rozšíření je zaznamenán do souborů nalezených v následujících adresářích:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/apphealth
```

Protokoly také pravidelně zaznamenávají stav aplikace.

## <a name="next-steps"></a>Další kroky
Zjistěte, jak [nasadit aplikaci](virtual-machine-scale-sets-deploy-app.md) na škálovací sady virtuálních strojů.
