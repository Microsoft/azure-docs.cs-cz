---
title: Použití sekvencování rozšíření pomocí škálovacích sad virtuálních strojů Azure
description: Zjistěte, jak seřadit zřizování rozšíření při nasazování více rozšíření na škálovací sady virtuálních strojů.
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: mimckitt
ms.openlocfilehash: 737040699dd62d722b9a9ad4d8915ccb270c2d06
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273745"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Zřizování rozšíření sekvence ve škálovacích sadách virtuálních strojů
Rozšíření virtuálních počítačů Azure poskytují funkce, jako je konfigurace a správa po nasazení, monitorování, zabezpečení a další. Produkční nasazení obvykle používají kombinaci více rozšíření nakonfigurovaných pro instance virtuálních aplikací k dosažení požadovaných výsledků.

Při použití více rozšíření na virtuálním počítači, je důležité zajistit, že rozšíření, které vyžadují stejné prostředky operačního serveru se nesnaží získat tyto prostředky současně. Některá rozšíření také závisí na jiných rozšířeních, aby poskytovala požadované konfigurace, jako je nastavení prostředí a tajné kódy. Bez správné řazení a sekvencování na místě, závislé rozšíření nasazení může selhat.

Tento článek podrobně popisuje, jak můžete sekvenční rozšíření, která mají být nakonfigurována pro instance virtuálních počítačů ve škálovacích sadách virtuálních strojů.

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste obeznámeni s:
-   [Rozšíření virtuálních](../virtual-machines/extensions/overview.md) strojů Azure
-   [Úprava škálovacích](virtual-machine-scale-sets-upgrade-scale-set.md) sad virtuálních strojů

## <a name="when-to-use-extension-sequencing"></a>Kdy použít sekvencování rozšíření
Sekvencování rozšíření v není povinné pro škálovací sady a pokud není zadáno, rozšíření lze zřídit na instanci škálovací sady v libovolném pořadí.

Například pokud model škálovací sady má dvě rozšíření – ExtensionA a ExtensionB – zadané v modelu, pak může dojít k jedné z následujících zřizování sekvence:
-   ExtensionA -> ExtensionB
-   ExtensionB -> ExtensionA

Pokud vaše aplikace vyžaduje rozšíření A vždy zřídit před rozšíření m, pak byste měli použít rozšíření řazení, jak je popsáno v tomto článku. Při sekvenování rozšíření nyní dojde pouze k jedné posloupnosti:
-   ExtensionA - > ExtensionB

Všechna rozšíření, která nejsou specifikována v definované posloupnosti zřizování, mohou být zřízena kdykoli, včetně před, po nebo během definované sekvence. Pořadí rozšíření pouze určuje, že konkrétní rozšíření bude zřízena po jiné konkrétní rozšíření. Nemá vliv na zřizování jakékoli jiné rozšíření definované v modelu.

Například pokud váš model škálovací sady má tři rozšíření – rozšíření A, rozšíření B a rozšíření C – zadané v modelu a rozšíření C je nastavena na zřízení po rozšíření A, pak může dojít k jedné z následujících zřizovacích sekvencí:
-   ExtensionA -> ExtensionC -> ExtensionB
-   ExtensionB -> ExtensionA -> ExtensionC
-   ExtensionA -> ExtensionB -> ExtensionC

Pokud potřebujete zajistit, že žádné jiné rozšíření je zřízena při provádění definované sekvence rozšíření, doporučujeme sekvencování všechna rozšíření v modelu škálovací sady. Ve výše uvedeném příkladu rozšíření B lze nastavit na zřízení po rozšíření C tak, aby mohlo dojít pouze k jedné posloupnosti:
-   ExtensionA -> ExtensionC -> ExtensionB


## <a name="how-to-use-extension-sequencing"></a>Jak používat sekvencování rozšíření
Chcete-li posloupnost rozšíření zřizování, je nutné aktualizovat definici rozšíření v modelu škálovací sady zahrnout vlastnost "provisionAfterExtensions", který přijímá pole názvů rozšíření. Rozšíření uvedená v hodnotě pole vlastností musí být plně definována v modelu škálovací sady.

### <a name="template-deployment"></a>Nasazení šablony
Následující příklad definuje šablonu, kde škálovací sada má tři rozšíření – ExtensionA, ExtensionB a ExtensionC – tak, aby rozšíření jsou zřízeny v pořadí:
-   ExtensionA -> ExtensionB -> ExtensionC

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

Vzhledem k tomu, že vlastnost "provisionAfterExtensions" přijímá pole názvů rozšíření, výše uvedený příklad lze upravit tak, že ExtensionC je zřízena po ExtensionA a ExtensionB, ale žádné řazení je vyžadováno mezi ExtensionA a ExtensionB. K dosažení tohoto scénáře lze použít následující šablonu:

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

### <a name="rest-api"></a>REST API
Následující příklad přidá nové rozšíření s názvem ExtensionC do modelu škálovací sady. ExtensionC má závislosti na ExtensionA a ExtensionB, které již byly definovány v modelu škálovací sady.

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

Pokud ExtensionC byla definována dříve v modelu škálovací sady a nyní `PATCH` chcete přidat jeho závislosti, můžete spustit a upravit vlastnosti již nasazené rozšíření.

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
Změny existujících instancí škálovací sady se použijí při dalším [upgradu](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-powershell"></a>Azure PowerShell
Pomocí rutiny [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) přidejte rozšíření Stavu aplikace do definice modelu škálovací sady. Sekvenování rozšíření vyžaduje použití prostředí Az PowerShell 1.2.0 nebo vyšší.

Následující příklad přidá [rozšíření Stav](virtual-machine-scale-sets-health-extension.md) `extensionProfile` aplikace do modelu škálovací sady škálovací sady systému Windows. Rozšíření stavu aplikace bude zřízena po zřízení [rozšíření vlastního skriptu](../virtual-machines/extensions/custom-script-windows.md), již definované v škálovací sadě.

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
Pomocí [sady rozšíření az vmss](/cli/azure/vmss/extension#az-vmss-extension-set) přidejte rozšíření Stavu aplikace do definice modelu škálovací sady. Řazení rozšíření vyžaduje použití Azure CLI 2.0.55 nebo vyšší.

Následující příklad přidá [rozšíření Stav aplikace](virtual-machine-scale-sets-health-extension.md) do modelu škálovací sady škálovací sady systému Windows. Rozšíření stavu aplikace bude zřízena po zřízení [rozšíření vlastního skriptu](../virtual-machines/extensions/custom-script-windows.md), již definované v škálovací sadě.

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

### <a name="not-able-to-add-extension-with-dependencies"></a>Nelze přidat rozšíření se závislostmi?
1. Ujistěte se, že rozšíření zadaná v provisionAfterExtensions jsou definovány v modelu škálovací sady.
2. Ujistěte se, že nejsou zaváděny žádné cyklické závislosti. Například následující sekvence není povolena: ExtensionA -> ExtensionB -> ExtensionC -> ExtensionA
3. Ujistěte se, že všechna rozšíření, která budete mít závislosti na, mají vlastnost "nastavení" pod rozšíření "vlastnosti". Například pokud ExtentionB musí být zřízena po ExtensionA, pak ExtensionA musí mít pole "nastavení" v části ExtensionA "vlastnosti". Můžete zadat prázdnou vlastnost "nastavení", pokud rozšíření nenařizuje žádné požadované nastavení.

### <a name="not-able-to-remove-extensions"></a>Nemůžete odebrat rozšíření?
Ujistěte se, že rozšíření odebrané nejsou uvedeny v provisionAfterExtensions pro další rozšíření.

## <a name="next-steps"></a>Další kroky
Zjistěte, jak [nasadit aplikaci](virtual-machine-scale-sets-deploy-app.md) na škálovací sady virtuálních strojů.
