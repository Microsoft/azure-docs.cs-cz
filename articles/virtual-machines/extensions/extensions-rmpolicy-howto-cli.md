---
title: Omezení instalace rozšíření virtuálních počítače pomocí zásad Azure
description: Pomocí zásad Azure můžete omezit nasazení rozšíření virtuálních počítače.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 3c660f7e05af43c2aad6f7283e32cfc1d85571ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066827"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Použití zásad Azure k omezení instalace rozšíření na virtuálních počítačích s Linuxem

Pokud chcete zabránit použití nebo instalaci určitých rozšíření na virtuálních počítačích s Linuxem, můžete vytvořit zásadu Azure pomocí příkazového příkazu k omezení rozšíření pro virtuální počítače v rámci skupiny prostředků. 

Tento kurz používá vykreslování příkazového příkazu v rámci prostředí Azure Cloud Shell, který se neustále aktualizuje na nejnovější verzi. Pokud chcete spustit Azure CLI místně, budete muset nainstalovat verzi 2.0.26 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Vytvoření souboru pravidel

Chcete-li omezit, jaká rozšíření lze nainstalovat, musíte mít [pravidlo,](../../governance/policy/concepts/definition-structure.md#policy-rule) které poskytuje logiku k identifikaci rozšíření.

Tento příklad ukazuje, jak odepřít instalaci rozšíření publikovaných "Microsoft.OSTCExtensions" vytvořením souboru pravidel v prostředí Azure Cloud Shell, ale pokud pracujete v rozhraní příkazového příkazu místně, můžete také vytvořit místní soubor a nahradit cestu (~/clouddrive) s cestou k místnímu souboru v počítači.

V [bash Cloud Shell](https://shell.azure.com/bash), zadejte:

```bash
vim ~/clouddrive/azurepolicy.rules.json
```

Zkopírujte a vložte do souboru následující soubor JSon.

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

Až budete hotovi, stiskněte klávesu **Esc** a zadejte **:wq** pro uložení a zavření souboru.


## <a name="create-a-parameters-file"></a>Vytvoření souboru parametrů

Potřebujete také soubor [parametrů,](../../governance/policy/concepts/definition-structure.md#parameters) který vytvoří strukturu, kterou můžete použít pro předávání v seznamu rozšíření, která chcete blokovat. 

Tento příklad ukazuje, jak vytvořit soubor parametrů pro virtuální počítače s Linuxem v prostředí Cloud Shell, ale pokud pracujete v rozhraní CLI místně, můžete také vytvořit místní soubor a nahradit cestu (~/clouddrive) s cestou k místnímu souboru v počítači.

V [bash Cloud Shell](https://shell.azure.com/bash), zadejte:

```bash
vim ~/clouddrive/azurepolicy.parameters.json
```

Zkopírujte a vložte do souboru následující soubor JSon.

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

Až budete hotovi, stiskněte klávesu **Esc** a zadejte **:wq** pro uložení a zavření souboru.

## <a name="create-the-policy"></a>Vytvořit zásadu

Definice zásadje objekt používaný k uložení konfigurace, kterou chcete použít. Definice zásad používá k definování zásad soubory pravidel a parametrů. Vytvořte definici zásadpomocí vytvoření [definice zásada az](/cli/azure/role/assignment?view=azure-cli-latest).

V tomto příkladu jsou pravidly a parametry soubory, které jste vytvořili a uložili jako soubory JSON ve vašem cloudovém prostředí.

```azurecli-interactive
az policy definition create \
   --name 'not-allowed-vmextension-linux' \
   --display-name 'Block VM Extensions' \
   --description 'This policy governs which VM extensions that are blocked.' \
   --rules '~/clouddrive/azurepolicy.rules.json' \
   --params '~/clouddrive/azurepolicy.parameters.json' \
   --mode All
```


## <a name="assign-the-policy"></a>Přiřazení zásady

Tento příklad přiřadí zásadu skupině prostředků pomocí [vytvoření přiřazení zásad az](/cli/azure/policy/assignment). Žádný virtuální počítač vytvořený ve skupině prostředků **myResourceGroup** nebude možné nainstalovat linuxový přístup k virtuálním počítači nebo rozšíření vlastního skriptu pro Linux. Skupina prostředků musí existovat před přiřazením zásady.

Pomocí [seznamu účtů az](/cli/azure/account?view=azure-cli-latest) získáte ID předplatného, které chcete použít místo id v příkladu.


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

Otestujte zásady vytvořením nového virtuálního uživatele a pokusem o přidání nového uživatele.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Pokuste se vytvořit nového uživatele s názvem **myNewUser** pomocí rozšíření Přístup k virtuálnímu připojení.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --password 'mynewuserpwd123!'
```



## <a name="remove-the-assignment"></a>Odebrání přiřazení

```azurecli-interactive
az policy assignment delete --name 'not-allowed-vmextension-linux' --resource-group myResourceGroup
```
## <a name="remove-the-policy"></a>Odebrat zásadu

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Azure Policy](../../governance/policy/overview.md).