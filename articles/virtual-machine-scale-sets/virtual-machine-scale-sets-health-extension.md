---
title: Použití rozšíření stav aplikací – to se škálovacími sadami virtuálních počítačů Azure | Dokumentace Microsoftu
description: Další informace o použití rozšíření stav aplikace pro monitorování stavu vaší aplikace nasazené do škálovací sady virtuálních počítačů.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: manayar
ms.openlocfilehash: 34f1b023b2ea2451f3308666d156278e92afb4aa
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55565968"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Použití stavu aplikace rozšíření virtuálního počítače škálovací sad
Monitorování stavu vaší aplikace je důležité signál pro správu a upgrade vašeho nasazení. Škálovací sady virtuálních počítačů Azure poskytují podporu pro [postupné upgrady](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) včetně [automatické upgrady operačního systému image](virtual-machine-scale-sets-automatic-upgrade.md), které využívají monitorování stavu jednotlivých instancí při upgradu nasazení .

Tento článek popisuje, jak můžete pomocí rozšíření stav aplikace tak, aby monitorovala vaše aplikace nasazené do škálovací sady virtuálních počítačů.

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte zkušenosti s:
-   Virtuální počítač Azure [rozšíření](../virtual-machines/extensions/overview.md)
-   [Úprava](virtual-machine-scale-sets-upgrade-scale-set.md) škálovací sady virtuálních počítačů

## <a name="when-to-use-the-application-health-extension"></a>Kdy použít rozšíření stavu aplikace
Rozšíření stav aplikací – to je nasazený v instanci virtuálního počítače škálovací sady a sestavy o stavu virtuálních počítačů v rámci instance ve škálovací sadě. Můžete nakonfigurovat rozšíření na koncový bod aplikace pro zjišťování a aktualizovat stav žádosti pro tuto instanci. Tento stav instance se kontroluje Azure k určení, zda je instance vhodné k upgradu operations.

Jako rozšíření sestavy stavu z v rámci virtuálního počítače, rozšíření lze použít v situacích, kde externí testy, jako je například sondy stavu aplikace (které využívají vlastní Azure Load Balancer [sondy](../load-balancer/load-balancer-custom-probe-overview.md)) nelze použít.

## <a name="extension-schema"></a>Schéma rozšíření

Následující kód JSON ukazuje schéma pro rozšíření stavu aplikace. Rozšíření vyžaduje minimálně "tcp" nebo "http" žádost s přidružené portu nebo cesta žádosti v uvedeném pořadí.

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

| Název | Hodnota / příklad | Typ dat
| ---- | ---- | ---- | ----
| apiVersion | `2018-10-01` | date |
| vydavatele | `Microsoft.ManagedServices` | řetězec |
| type | `ApplicationHealthLinux` (Linux), `ApplicationHealthWindows` (Windows) | řetězec |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Nastavení

| Název | Hodnota / příklad | Typ dat
| ---- | ---- | ----
| Protokol | `http` nebo `tcp` | řetězec |
| port | Nepovinné, pokud je protokol `http`, povinné, když je protokol `tcp` | int |
| requestPath | Povinné, když je protokol `http`, není povolený, pokud je protokol `tcp` | řetězec |

## <a name="deploy-the-application-health-extension"></a>Nasazení rozšíření aplikace zdraví
Nasazení aplikace stav rozšíření škálovací nastaví popsané v následujících příkladech několika způsoby.

### <a name="rest-api"></a>REST API

Následující příklad přidá rozšíření stavu aplikace (s názvem myHealthExtension) k extensionProfile v modelu škálovací sady založené na Windows škálovací sady.

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
Použití `PATCH` upravit již nasazenou rozšíření.

### <a name="azure-powershell"></a>Azure PowerShell

Použití [přidat AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) definice modelu rutiny pro přidání rozšíření stav aplikací do škálovací sady.

Následující příklad přidá rozšíření stavu aplikace, které `extensionProfile` v škálovací sady modelu na základě Windows škálovací sady. V příkladu se používá nový modul prostředí PowerShell Az.

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

Použití [az vmss extension set](/cli/azure/vmss/extension#az-vmss-extension-set) přidat rozšíření stav aplikací do škálovací sady definice modelu.

Následující příklad přidá do modelu škálovací sady založené na Windows škálovací sady rozšíření stavu aplikace.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Řešení potíží
Rozšíření provádění výstup je zaznamenán soubory nalezené v následujících adresářích:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/apphealth
```

Protokoly také pravidelně zaznamenat stav aplikace.

## <a name="next-steps"></a>Další postup
Zjistěte, jak [při nasazování aplikace](virtual-machine-scale-sets-deploy-app.md) nastaví na škálování virtuálního počítače.
