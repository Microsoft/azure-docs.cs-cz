---
title: Kurz – řízení virtuálních počítačů Azure pomocí Azure CLI | Microsoft Docs
description: V tomto kurzu se naučíte používat Azure CLI ke správě virtuálních počítačů Azure pomocí nastavení RBAC, policies, zámků a značek.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tfitzmac
manager: gwallace
editor: tysonn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 5fa14ef30d45a9a28cc690761ec33b5bfaaac6a7
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676508"
---
# <a name="tutorial-learn-about-linux-virtual-machine-governance-with-azure-cli"></a>Kurz: Přečtěte si o správě virtuálních počítačů se systémem Linux pomocí Azure CLI

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku Azure CLI místně, musíte mít spuštěnou verzi Azure CLI 2.0.30 nebo novější. Pokud chcete zjistit verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="understand-scope"></a>Pochopení oboru

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

V tomto kurzu použijete všechna nastavení správy na skupinu prostředků, abyste mohli tato nastavení po dokončení snadno odebrat.

Pojďme tuto skupinu prostředků vytvořit.

```azurecli-interactive
az group create --name myResourceGroup --location "East US"
```

V současné době je skupina prostředků prázdná.

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Chcete zajistit, aby uživatelé ve vaší organizaci měli správnou úroveň přístupu k těmto prostředkům. Nechcete uživatelům udělit neomezený přístup, ale musíte také zajistit, aby mohli provádět své práce. [Řízení přístupu na základě rolí](../../role-based-access-control/overview.md) umožňuje spravovat uživatele, kteří mají oprávnění k provádění konkrétních akcí v oboru.

Aby uživatelé mohli vytvořit a odebrat přiřazení rolí, musí mít přístup `Microsoft.Authorization/roleAssignments/*`. Tento přístup je udělen prostřednictvím rolí vlastník nebo správce přístupu uživatelů.

Pro správu řešení virtuálních počítačů jsou k dispozici tři role specifické pro prostředky, které poskytují běžně potřebný přístup:

* [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Přispěvatel sítě](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Přispěvatel účtu úložiště](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Místo přiřazování rolí jednotlivým uživatelům je často snazší použít Azure Active Directory skupinu s uživateli, kteří potřebují provést podobné akce. Pak tuto skupinu přiřaďte k příslušné roli. V tomto článku buď použijte existující skupinu pro správu virtuálního počítače, nebo [vytvořte skupinu Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)pomocí portálu.

Po vytvoření nové skupiny nebo vyhledání existující skupiny použijte příkaz [AZ role Assignment Create](https://docs.microsoft.com/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) a přiřaďte novou skupinu Azure Active Directory k roli Přispěvatel virtuálních počítačů pro skupinu prostředků.

```azurecli-interactive
adgroupId=$(az ad group show --group <your-group-name> --query objectId --output tsv)

az role assignment create --assignee-object-id $adgroupId --role "Virtual Machine Contributor" --resource-group myResourceGroup
```

Pokud se zobrazí chyba oznamující, že **objekt zabezpečení \<guid > v adresáři neexistuje**, nová skupina se v celém Azure Active Directory nerozšíří. Zkuste znovu spustit příkaz.

Obvykle tento proces zopakujete pro přispěvatele *sítě* a *přispěvatele účtu úložiště* , abyste se ujistili, že uživatelé mají přiřazenou správu nasazených prostředků. V tomto článku můžete tyto kroky přeskočit.

## <a name="azure-policy"></a>Azure Policy

[Azure Policy](../../governance/policy/overview.md) vám pomůže zajistit, že všechny prostředky v předplatném splňují podnikové standardy. Vaše předplatné už má několik definic zásad. Dostupné definice zásad zobrazíte pomocí příkazu [AZ Policy list list](https://docs.microsoft.com/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-list) :

```azurecli-interactive
az policy definition list --query "[].[displayName, policyType, name]" --output table
```

Zobrazí se existující definice zásad. Typ zásady je buď **Builtin** , nebo **vlastní**. Prohlédněte si definice pro ty, které popisují podmínku, kterou chcete přiřadit. V tomto článku přiřadíte zásady, které:

* Omezte umístění pro všechny prostředky.
* Omezte počet SKU pro virtuální počítače.
* Auditujte virtuální počítače, které nepoužívají spravované disky.

V následujícím příkladu načtete tři definice zásad na základě zobrazovaného názvu. Pomocí příkazu [AZ Policy Assignment Create](https://docs.microsoft.com/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) můžete přiřadit tyto definice ke skupině prostředků. U některých zásad poskytujete hodnoty parametrů pro určení povolených hodnot.

```azurecli-interactive
# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
locationDefinition=$(az policy definition list --query "[?displayName=='Allowed locations'].name | [0]" --output tsv)
skuDefinition=$(az policy definition list --query "[?displayName=='Allowed virtual machine SKUs'].name | [0]" --output tsv)
auditDefinition=$(az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks'].name | [0]" --output tsv)

# Assign policy for allowed locations
az policy assignment create --name "Set permitted locations" \
  --resource-group myResourceGroup \
  --policy $locationDefinition \
  --params '{ 
      "listOfAllowedLocations": {
        "value": [
          "eastus", 
          "eastus2"
        ]
      }
    }'

# Assign policy for allowed SKUs
az policy assignment create --name "Set permitted VM SKUs" \
  --resource-group myResourceGroup \
  --policy $skuDefinition \
  --params '{ 
      "listOfAllowedSKUs": {
        "value": [
          "Standard_DS1_v2", 
          "Standard_E2s_v2"
        ]
      }
    }'

# Assign policy for auditing unmanaged disks
az policy assignment create --name "Audit unmanaged disks" \
  --resource-group myResourceGroup \
  --policy $auditDefinition
```

Předchozí příklad předpokládá, že už znáte parametry pro zásadu. Pokud potřebujete zobrazit parametry, použijte:

```azurecli-interactive
az policy definition show --name $locationDefinition --query parameters
```

## <a name="deploy-the-virtual-machine"></a>Nasazení virtuálního počítače

Máte přiřazené role a zásady, takže jste připravení nasadit své řešení. Výchozí velikost je Standard_DS1_v2, což je jedna z vašich povolených SKU. Příkaz vytvoří klíče SSH, pokud neexistují ve výchozím umístění.

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

Po dokončení nasazení můžete v řešení použít další nastavení správy.

## <a name="lock-resources"></a>Uzamčení prostředků

[Zámky prostředků](../../azure-resource-manager/resource-group-lock-resources.md) zabraňují uživatelům ve vaší organizaci v neúmyslném odstranění nebo úpravě důležitých prostředků. Na rozdíl od řízení přístupu na základě rolí používají zámky prostředků omezení napříč všemi uživateli a rolemi. Úroveň zámku můžete nastavit na *CanNotDelete* nebo *jen pro čtení*.

Chcete-li vytvořit nebo odstranit zámky pro správu, je nutné mít přístup k akcím `Microsoft.Authorization/locks/*`. Z vestavěných rolí jsou tyto akce uděleny pouze **vlastník** a **Správce přístupu uživatelů** .

Chcete-li zamknout virtuální počítač a skupinu zabezpečení sítě, použijte příkaz [AZ Lock Create](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest#az-resource-lock-create) :

```azurecli-interactive
# Add CanNotDelete lock to the VM
az lock create --name LockVM \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVM \
  --resource-type Microsoft.Compute/virtualMachines

# Add CanNotDelete lock to the network security group
az lock create --name LockNSG \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVMNSG \
  --resource-type Microsoft.Network/networkSecurityGroups
```

Chcete-li otestovat zámky, zkuste spustit následující příkaz:

```azurecli-interactive 
az group delete --name myResourceGroup
```

Zobrazí se chyba oznamující, že operaci odstranění nelze dokončit z důvodu zámku. Skupinu prostředků lze odstranit pouze v případě, že zámky odstraníte výslovně. Tento krok se zobrazuje v [vyčištění prostředků](#clean-up-resources).

## <a name="tag-resources"></a>Označení prostředků

[Značky](../../azure-resource-manager/resource-group-using-tags.md) můžete použít pro prostředky Azure, abyste je logicky uspořádali podle kategorií. Každá značka se skládá z názvu a hodnoty. Můžete například použít název "prostředí" a hodnotu "produkce" na všechny prostředky v produkčním prostředí.

[!INCLUDE [Resource Manager governance tags CLI](../../../includes/resource-manager-governance-tags-cli.md)]

Chcete-li použít značky na virtuálním počítači, použijte příkaz [AZ Resource tag](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-list) . Všechny existující značky na prostředku nejsou zachovány.

```azurecli-interactive
az resource tag -n myVM \
  -g myResourceGroup \
  --tags Dept=IT Environment=Test Project=Documentation \
  --resource-type "Microsoft.Compute/virtualMachines"
```

### <a name="find-resources-by-tag"></a>Najít prostředky podle značky

Pokud chcete najít prostředky s názvem a hodnotou značky, použijte příkaz [AZ Resource list](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-list) :

```azurecli-interactive
az resource list --tag Environment=Test --query [].name
```

Můžete použít vrácené hodnoty pro úlohy správy, jako je například zastavení všech virtuálních počítačů s hodnotou značky.

```azurecli-interactive
az vm stop --ids $(az resource list --tag Environment=Test --query "[?type=='Microsoft.Compute/virtualMachines'].id" --output tsv)
```

### <a name="view-costs-by-tag-values"></a>Zobrazit náklady podle hodnot značek

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Uzamčenou skupinu zabezpečení sítě nelze odstranit, dokud se zámek neodebere. Pokud chcete zámek odebrat, načtěte ID zámků a poskytněte je příkazem [AZ LOCK Delete](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest#az-resource-lock-delete) :

```azurecli-interactive
vmlock=$(az lock show --name LockVM \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Compute/virtualMachines \
  --resource-name myVM --output tsv --query id)
nsglock=$(az lock show --name LockNSG \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Network/networkSecurityGroups \
  --resource-name myVMNSG --output tsv --query id)
az lock delete --ids $vmlock $nsglock
```

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít příkaz [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) . Ukončete relaci SSH k VIRTUÁLNÍmu počítači a pak tyto prostředky odstraňte následujícím způsobem:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili vlastní image virtuálního počítače. Zjistili jste, jak:

> [!div class="checklist"]
> * Přiřazení uživatelů k roli
> * Použití zásad, které vynutily standardy
> * Ochrana kritických prostředků pomocí zámků
> * Označení prostředků pro fakturaci a správu

Přejděte k dalšímu kurzu, kde se dozvíte, jak identifikovat změny a spravovat aktualizace balíčků na virtuálním počítači.

> [!div class="nextstepaction"]
> [Správa virtuálních počítačů](tutorial-config-management.md)

