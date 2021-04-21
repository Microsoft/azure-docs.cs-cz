---
title: Použití pořadí rozšíření s Azure Virtual Machine Scale Sets
description: Naučte se, jak sekvencovat zřizování rozšíření při nasazování více rozšíření na Virtual Machine Scale Sets.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 01/30/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 1b5aea1f0f0101231408dc9ad7b57a30f2c86256
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788152"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Zřizování rozšíření sekvence ve virtuálních počítačích Virtual Machine Scale Sets
Rozšíření virtuálních počítačů Azure poskytují možnosti, jako je například konfigurace po nasazení a správa, monitorování, zabezpečení a další. Produkční nasazení obvykle používají kombinaci více rozšíření nakonfigurovaných pro instance virtuálních počítačů, aby dosáhla požadovaných výsledků.

Při používání více rozšíření na virtuálním počítači je důležité zajistit, aby se rozšíření vyžadující stejné prostředky operačního systému nepokoušela získat tyto prostředky ve stejnou dobu. Některá rozšíření závisí také na dalších rozšířeních, která poskytují požadované konfigurace, jako je například nastavení prostředí a tajné kódy. Bez správného řazení a pořadí řazení můžou neúspěšná nasazení závislých rozšíření.

Tento článek podrobně popisuje, jak můžete sekvencování rozšíření nakonfigurovat pro instance virtuálních počítačů ve službě Virtual Machine Scale Sets.

## <a name="prerequisites"></a>Požadavky
V tomto článku se předpokládá, že jste obeznámeni s:
-   [Rozšíření](../virtual-machines/extensions/overview.md) virtuálních počítačů Azure
-   [Úprava](virtual-machine-scale-sets-upgrade-scale-set.md) virtuálních počítačů Scale Sets

## <a name="when-to-use-extension-sequencing"></a>Kdy použít sekvencování rozšíření
Přípona sekvencování není pro sady škálování nutná, a pokud tuto možnost nezadáte, můžete rozšíření zřídit v instanci sady škálování v libovolném pořadí.

Například pokud model sady škálování má dvě rozšíření – rozšíření a ExtensionB – v modelu, může dojít k jedné z následujících sekvencí zřizování:
-   Přípona-> ExtensionB
-   ExtensionB-> – rozšíření

Pokud vaše aplikace vyžaduje rozšíření A, aby bylo vždy zřízené před rozšířením B, měli byste použít sekvencování rozšíření, jak je popsáno v tomto článku. Při sekvencování rozšíření se teď provede jenom jedna sekvence:
-   Přípona-> ExtensionB

Všechna rozšíření, která nejsou specifikována v definované sekvenci zřizování, lze zřídit kdykoli, včetně před, po nebo během definovaného pořadí. Sekvence rozšíření určuje pouze to, že konkrétní rozšíření bude zřízené po jiné konkrétní příponě. Nemá vliv na zřizování žádného jiného rozšíření definovaného v modelu.

Pokud má například model sady škálování tři rozšíření – rozšíření A, rozšíření B a rozšíření C – zadané v modelu a rozšíření C se nastaví jako zřízené po rozšíření A, může dojít k jedné z následujících sekvencí zřizování:
-   Přípona-> ExtensionC-> ExtensionB
-   ExtensionB-> přípona-> ExtensionC
-   Přípona-> ExtensionB-> ExtensionC

Pokud potřebujete zajistit, aby se při provádění definované sekvence rozšíření nezřídilo žádné jiné rozšíření, doporučujeme sekvencování všech rozšíření v modelu sady škálování. Ve výše uvedeném příkladu lze rozšíření B nastavit tak, aby se po rozšíření C zřídilo, že může probíhat jenom jedna sekvence:
-   Přípona-> ExtensionC-> ExtensionB


## <a name="how-to-use-extension-sequencing"></a>Jak používat sekvencování rozšíření
Aby bylo zajišťování rozšíření pořadí, je nutné aktualizovat definici rozšíření v modelu sady škálování tak, aby zahrnovala vlastnost "provisionAfterExtensions", která přijímá pole názvů přípon. Rozšíření uvedená v hodnotě pole vlastností musí být plně definovaná v modelu sady škálování.

### <a name="template-deployment"></a>Nasazení šablony
Následující příklad definuje šablonu, ve které má sada škálování tři rozšíření – rozšíření, ExtensionB a ExtensionC – taková rozšíření se zřídí v tomto pořadí:
-   Přípona-> ExtensionB-> ExtensionC

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA"
          ],
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

Vzhledem k tomu, že vlastnost "provisionAfterExtensions" přijímá pole názvů přípon, výše uvedený příklad lze upravit tak, aby ExtensionC byl zřízen po rozšíření a ExtensionB, ale mezi příponou a ExtensionB není vyžadováno žádné řazení. K dosažení tohoto scénáře lze použít následující šablonu:

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA","ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

### <a name="rest-api"></a>Rozhraní REST API
Následující příklad přidá nové rozšíření s názvem ExtensionC do modelu sady škálování. ExtensionC má závislosti na příponách a ExtensionB, které už jsou definované v modelu sady škálování.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "name": "ExtensionC",
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ],
    "publisher": "ExtensionC.Publisher",
    "settings": {},
    "typeHandlerVersion": "3.0",
    "autoUpgradeMinorVersion": true,
    "type": "ExtensionC" 
  }                  
}
```

Pokud jste dříve definovali ExtensionC v modelu sady škálování a teď chcete přidat její závislosti, můžete spustit a `PATCH` Upravit vlastnosti již nasazeného rozšíření.

```
PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ]
  }                  
}
```
Změny stávajících instancí sady škálování se aplikují při dalším [upgradu](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-powershell"></a>Azure PowerShell
Pomocí rutiny [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) přidejte rozšíření pro stav aplikace do definice modelu sady škálování. Sekvence rozšíření vyžaduje použití AZ PowerShell 1.2.0 nebo vyšší.

Následující příklad přidá rozšíření pro [stav aplikace](virtual-machine-scale-sets-health-extension.md) do nástroje `extensionProfile` v modelu sady škálování založeném na systému Windows. Rozšíření pro stav aplikace se zřídí po zřízení [rozšíření vlastních skriptů](../virtual-machines/extensions/custom-script-windows.md), které už je definované v sadě škálování.

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
  -ProvisionAfterExtension "CustomScriptExtension" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Pomocí [AZ VMSS Extension set](/cli/azure/vmss/extension#az_vmss_extension_set) přidejte rozšíření pro stav aplikace do definice modelu sady škálování. Sekvence rozšíření vyžaduje použití Azure CLI 2.0.55 nebo vyšší.

Následující příklad přidá [rozšíření stavu aplikace](virtual-machine-scale-sets-health-extension.md) do modelu škálované sady pro sadu škálování založeného na systému Windows. Rozšíření pro stav aplikace se zřídí po zřízení [rozšíření vlastních skriptů](../virtual-machines/extensions/custom-script-windows.md), které už je definované v sadě škálování.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --provision-after-extensions CustomScriptExtension \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Řešení potíží

### <a name="not-able-to-add-extension-with-dependencies"></a>Nemůžete přidat rozšíření se závislostmi?
1. Zajistěte, aby byla rozšíření určená v provisionAfterExtensions definována v modelu sady škálování.
2. Zajistěte, aby nebyly zavedeny žádné cyklické závislosti. Například následující sekvence není povolená: rozšíření-> ExtensionB-> ExtensionC-> Extension.
3. Zajistěte, aby u všech rozšíření, u kterých procházíte závislosti, bylo vlastnost Settings v části rozšíření "Properties". Například pokud je potřeba zřídit ExtentionB po rozšíření, pak musí mít přípona pole "nastavení" v části přípona "vlastnosti". Pokud rozšíření nevyžaduje žádná požadovaná nastavení, můžete zadat prázdnou vlastnost Settings.

### <a name="not-able-to-remove-extensions"></a>Nemůžete odebrat rozšíření?
Zajistěte, aby se odebraná rozšíření nezobrazovala v části provisionAfterExtensions pro žádná další rozšíření.

## <a name="next-steps"></a>Další kroky
Naučte se, jak [nasadit vaši aplikaci do služby](virtual-machine-scale-sets-deploy-app.md) Virtual Machine Scale Sets.
