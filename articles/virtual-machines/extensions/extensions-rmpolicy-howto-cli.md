---
title: Omezení instalace rozšíření virtuálních počítačů (Linux) pomocí Azure Policy
description: K omezení nasazení rozšíření virtuálních počítačů použijte Azure Policy.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 03/23/2018
ms.openlocfilehash: 582f690c00ff8bdd06e4d2867724aa482bcacaf3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559966"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Omezení instalace rozšíření na virtuálních počítačích se systémem Linux pomocí Azure Policy

Pokud chcete zabránit použití nebo instalaci určitých rozšíření na virtuální počítače se systémem Linux, můžete vytvořit definici Azure Policy pomocí rozhraní příkazového řádku k omezení rozšíření pro virtuální počítače v rámci skupiny prostředků. 

V tomto kurzu se používá CLI v rámci Azure Cloud Shell, který se průběžně aktualizuje na nejnovější verzi. Pokud chcete rozhraní příkazového řádku Azure spustit místně, musíte nainstalovat verzi 2.0.26 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Vytvoření souboru pravidel

Chcete-li omezit, jaká rozšíření lze nainstalovat, je nutné mít [pravidlo](../../governance/policy/concepts/definition-structure.md#policy-rule) , které poskytne Logic pro identifikaci rozšíření.

V tomto příkladu se dozvíte, jak zamítnout instalaci rozšíření publikovaných souborem ' Microsoft. OSTCExtensions ' vytvořením souboru pravidel v Azure Cloud Shell, ale pokud pracujete v rozhraní příkazového řádku místně, můžete také vytvořit místní soubor a nahradit cestu (~/clouddrive) cestou k místnímu souboru na počítači.

Do [bash Cloud Shell](https://shell.azure.com/bash)zadejte:

```bash
vim ~/clouddrive/azurepolicy.rules.json
```

Zkopírujte následující soubor. JSON a vložte ho do souboru.

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

Až skončíte, stiskněte klávesu **ESC** a potom zadejte **: WQ** a soubor uložte a zavřete.


## <a name="create-a-parameters-file"></a>Vytvoření souboru parametrů

Budete také potřebovat soubor [parametrů](../../governance/policy/concepts/definition-structure.md#parameters) , který vytvoří strukturu, kterou můžete použít pro předání seznamu rozšíření, která chcete zablokovat. 

V tomto příkladu se dozvíte, jak vytvořit soubor parametrů pro virtuální počítače se systémem Linux v Cloud Shell, ale pokud pracujete v rozhraní příkazového řádku místně, můžete také vytvořit místní soubor a nahradit cestu (~/clouddrive) cestou k místnímu souboru v počítači.

Do [Cloud Shell bash](https://shell.azure.com/bash)zadejte:

```bash
vim ~/clouddrive/azurepolicy.parameters.json
```

Zkopírujte následující soubor. JSON a vložte ho do souboru.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied. Example: CustomScriptForLinux, VMAccessForLinux etc.",
            "displayName": "Denied extension"
        }
    }
}
```

Až skončíte, stiskněte klávesu **ESC** a potom zadejte **: WQ** a soubor uložte a zavřete.

## <a name="create-the-policy"></a>Vytvoření zásady

Definice zásady je objekt, který slouží k uložení konfigurace, kterou chcete použít. Definice zásady používá soubory pravidel a parametrů k definování zásad. Pomocí [AZ Policy definition Create vytvořte](/cli/azure/role/assignment)definici zásady.

V tomto příkladu jsou pravidla a parametry soubory, které jste vytvořili a uložili ve službě cloud Shell jako soubory. JSON.

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

Tento příklad přiřadí zásadu ke skupině prostředků pomocí [AZ Policy Assignment Create](/cli/azure/policy/assignment). Libovolný virtuální počítač vytvořený ve skupině prostředků **myResourceGroup** nebude moct nainstalovat přístup k virtuálnímu počítači Linux ani rozšíření vlastních skriptů pro Linux. Aby bylo možné zásadu přiřadit, musí existovat skupina prostředků.

Pomocí [AZ Account list](/cli/azure/account) můžete získat ID předplatného, které se má použít místo v příkladu.


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

Otestujte zásadu tak, že vytvoříte nový virtuální počítač a zkusíte přidat nového uživatele.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Zkuste vytvořit nového uživatele s názvem **myNewUser** pomocí rozšíření přístupu k virtuálnímu počítači.

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
## <a name="remove-the-policy"></a>Odebrat zásadu

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Azure Policy](../../governance/policy/overview.md).
