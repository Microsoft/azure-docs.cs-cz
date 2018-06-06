---
title: Pomocí zásad Azure můžete omezit instalace rozšíření virtuálního počítače | Microsoft Docs
description: Pomocí zásad Azure můžete omezit nasazení rozšíření virtuálních počítačů.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: danis;cynthn
ms.openlocfilehash: 8e65b82730884947633688db9ed50080b96e0b8e
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "33942743"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Pomocí zásad Azure můžete omezit instalaci rozšíření na virtuální počítače s Linuxem

Pokud chcete, aby se zabránilo použití nebo instalace určité rozšíření na virtuální počítače Linux, můžete vytvořit zásady služby Azure pomocí rozhraní příkazového řádku omezit rozšíření pro virtuální počítače ve skupině prostředků. 

Tento kurz používá rozhraní příkazového řádku v prostředí cloudu Azure, která je neustále aktualizuje na nejnovější verzi. Pokud chcete spustit místně rozhraní příkazového řádku Azure, budete muset nainstalovat verzi 2.0.26 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Vytvoření souboru pravidel

Chcete-li omezit, jaká rozšíření se dá nainstalovat, je potřeba mít [pravidlo](/azure/azure-policy/policy-definition#policy-rule) zajistit logiku k identifikaci rozšíření.

Tento příklad ukazuje, jak tak, aby odepřel instalace rozšíření publikováno 'Microsoft.OSTCExtensions' pomocí souboru pravidel v prostředí cloudu Azure, ale pokud pracujete v rozhraní příkazového řádku místně, můžete také vytvořit místního souboru a nahraďte cestu k cestě (~/clouddrive) místní soubor na váš počítač.

V [cloudové prostředí bash](https://shell.azure.com/bash), zadejte:

```azurecli-interactive 
vim ~/clouddrive/azurepolicy.rules.json
```

Zkopírujte a vložte následující .json do souboru.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.OSTCExtensions/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/publisher",
                "equals": "Microsoft.OSTCExtensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Až skončíte, stiskněte tlačítko **Esc** klíče a potom zadejte **: QW** uložte a zavřete soubor.


## <a name="create-a-parameters-file"></a>Vytvořte soubor parametrů

Musíte taky [parametry](/azure/azure-policy/policy-definition#parameters) soubor, který vytvoří strukturu, musíte použít pro předávání v seznamu rozšíření blokování. 

Tento příklad ukazuje, jak vytvořit soubor parametrů pro virtuální počítače s Linuxem v prostředí cloudu, ale pokud pracujete v rozhraní příkazového řádku místně, můžete také vytvořit místního souboru a cesta (~/clouddrive) nahraďte cestu k souboru místní na váš počítač.

V [cloudové prostředí bash](https://shell.azure.com/bash), zadejte:

```azurecli-interactive
vim ~/clouddrive/azurepolicy.parameters.json
```

Zkopírujte a vložte následující .json do souboru.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied. Example: CustomScriptForLinux, VMAccessForLinux etc.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

Až skončíte, stiskněte tlačítko **Esc** klíče a potom zadejte **: QW** uložte a zavřete soubor.

## <a name="create-the-policy"></a>Vytvoření zásad

Definice zásady je objekt se používá k ukládání konfigurace, který chcete použít. Definice zásad používá soubory pravidel a parametry, můžete definovat zásady. Vytvořte pomocí definice zásady [vytvoření definice zásady az](/cli/azure/role/assignment?view=azure-cli-latest#az_role_assignment_create).

V tomto příkladu pravidel a parametry, jsou soubory, které jste vytvořili a uloženy jako soubory .json ve své cloudové prostředí.

```azurecli-interactive
az policy definition create \
   --name 'not-allowed-vmextension-linux' \
   --display-name 'Block VM Extensions' \
   --description 'This policy governs which VM extensions that are blocked.' \
   --rules '~/clouddrive/azurepolicy.rules.json' \
   --params '~/clouddrive/azurepolicy.parameters.json' \
   --mode All
```


## <a name="assign-the-policy"></a>Přiřaďte zásady

Tento příklad přiřadí zásady skupiny prostředků pomocí [na vytvoření přiřazení zásady az](/cli/azure/policy/assignment#az_policy_assignment_create). Všechny virtuální počítač vytvořený na **myResourceGroup** skupinu prostředků, nebude možné nainstalovat přístup virtuálních počítačů Linux nebo rozšíření vlastních skriptů pro Linux. Skupina prostředků musí existovat můžete přiřadit zásady.

Použít [seznamu účtů az](/cli/azure/account?view=azure-cli-latest#az_account_list) k získání ID předplatného používejte místo v příkladu.


```azurecli-interactive
az policy assignment create \
   --name 'not-allowed-vmextension-linux' \
   --scope /subscriptions/<subscription Id>/resourceGroups/myResourceGroup \
   --policy "not-allowed-vmextension-linux" \
   --params '{
        "notAllowedExtensions": {
            "value": [
                "VMAccessForLinux",
                "CustomScriptForLinux"
            ]
        }
    }'
```

## <a name="test-the-policy"></a>Testování zásad

Testování zásady vytvořením nového virtuálního počítače a při přidání nového uživatele.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Pokuste se vytvořit nového uživatele s názvem **myNewUser** pomocí rozšíření virtuálního počítače přístup.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --password 'mynewuserpwd123!'
```



## <a name="remove-the-assignment"></a>Odebrat přiřazení

```azurecli-interactive
az policy assignment delete --name 'not-allowed-vmextension-linux' --resource-group myResourceGroup
```
## <a name="remove-the-policy"></a>Odebrání zásady

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```


## <a name="next-steps"></a>Další postup
Další informace najdete v tématu [zásad Azure](../../azure-policy/azure-policy-introduction.md).
