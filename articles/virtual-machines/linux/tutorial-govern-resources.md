---
title: Kurz řízení virtuálních počítačů Azure pomocí Azure CLI 2.0 | Microsoft Docs
description: V tomto kurzu zjistíte, jak pomocí Azure CLI 2.0 spravovat virtuální počítače Azure s využitím RBAC, zásad, zámků a značek.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: aeb94a430e633299637d4e9b96ef6b2c0309b163
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38722911"
---
# <a name="tutorial-learn-about-linux-virtual-machine-governance-with-azure-cli-20"></a>Kurz: Informace o řízení virtuálních počítačů Linux pomocí Azure CLI 2.0

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte pro tento kurz mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="understand-scope"></a>Orientace v oborech

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

V tomto kurzu se nastavení pro správu aplikují na skupinu prostředků, takže je po skončení můžete snadno odebrat.

Vytvoříme skupinu prostředků.

```azurecli-interactive
az group create --name myResourceGroup --location "East US"
```

V tuto chvíli je skupina prostředků prázdná.

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Potřebujete zajistit, aby uživatelé ve vaší organizaci měli správnou úroveň přístupu k těmto prostředkům. Nechcete uživatelům dát neomezený přístup, ale zároveň jim potřebujete umožnit dělat svou práci. Pomocí [řízení přístupu na základě rolí](../../role-based-access-control/overview.md) můžete spravovat oprávnění uživatelů k provádění určitých akcí v daném oboru.

K vytváření a odebírání přiřazení rolí musí mít uživatelé přístup `Microsoft.Authorization/roleAssignments/*`. Tento přístup se poskytuje prostřednictvím role vlastníka nebo správce uživatelských přístupů.

Pro správu řešení virtuálních počítačů existují v závislosti na prostředcích tři role, které poskytují běžný přístup:

* [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Přispěvatel sítě](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Přispěvatel účtů úložiště](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Místo přiřazení rolí jednotlivým uživatelům je často jednodušší [vytvořit skupinu Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) pro uživatele, kteří potřebují provádět podobné akce. Potom této skupině přiřaďte odpovídající role. Pro názornost tohoto článku stačí vytvořit skupinu Azure Active Directory bez členů. Této skupině můžete přiřadit role pro některý obor. 

Následující příklad vytvoří skupinu Azure Active Directory s názvem *VMDemoContributors* a přezdívkou pro poštu *vmDemoGroup*. Přezdívka pro poštu slouží jako alias pro skupinu.

```azurecli-interactive
adgroupId=$(az ad group create --display-name VMDemoContributors --mail-nickname vmDemoGroup --query objectId --output tsv)
```

Po odeslání z příkazového řádku chvíli trvá, než se skupina rozšíří v rámci Azure Active Directory. Po 20 nebo 30 sekundách čekání přiřaďte pomocí příkazu [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) novou skupinu Azure Active Directory v dané skupině prostředků do role Přispěvatel virtuálních počítačů.  Pokud následující příkaz spustíte dřív, než se skupina rozšíří, zobrazí se chybová zpráva s informací, že **objekt zabezpečení <guid> neexistuje v adresáři**. Zkuste příkaz znovu spustit.

```azurecli-interactive
az role assignment create --assignee-object-id $adgroupId --role "Virtual Machine Contributor" --resource-group myResourceGroup
```

Obvykle tento postup zopakujete pro role *Přispěvatel sítě* a *Přispěvatel účtů úložiště*, abyste zajistili přiřazení uživatelů ke správě nasazených prostředků. V tomto článku můžete tyto kroky vynechat.

## <a name="azure-policies"></a>Zásady Azure

[!INCLUDE [Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Použití zásad

Vaše předplatné už obsahuje několik definic zásad. Pokud chcete zobrazit definice dostupných zásad, použijte příkaz [az policy definition list](/cli/azure/policy/definition#az_policy_definition_list):

```azurecli-interactive
az policy definition list --query "[].[displayName, policyType, name]" --output table
```

Zobrazí se definice existujících zásad. Typ zásad je buď **Předdefinované** nebo **Vlastní**. Najděte definice zásad popisující podmínku, kterou chcete přiřadit. V tomto článku můžete přiřadit zásady s těmito funkcemi:

* Omezení umístění pro všechny prostředky
* Omezení SKU pro virtuální počítače
* Audit virtuálních počítačů, které nepoužívají spravované disky

V následujícím příkladu načtete definice tří zásad na základě zobrazovaného názvu. K přiřazení těchto definic do skupiny prostředků můžete použít příkaz [az policy assignment create](/cli/azure/policy/assignment#az_policy_assignment_create). U některých zásad určíte povolené hodnoty zadáním hodnot parametrů.

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

V předchozím příkladu se předpokládá, že už parametry pro zásadu znáte. K zobrazení parametrů použijte následující příkaz:

```azurecli-interactive
az policy definition show --name $locationDefinition --query parameters
```

## <a name="deploy-the-virtual-machine"></a>Nasazení virtuálního počítače

Přiřadili jste role a zásady, takže jste připravení nasadit řešení. Výchozí velikost je Standard_DS1_v2, což je jedno z povolených SKU. Příkaz vytvoří klíče SSH, pokud neexistují ve výchozím umístění.

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

Po dokončení nasazování můžete použít další nastavení pro správu řešení.

## <a name="lock-resources"></a>Uzamčení prostředků

[Zámky prostředků](../../azure-resource-manager/resource-group-lock-resources.md) zabraňují tomu, aby uživatelé ve vaší organizaci neúmyslně odstranili nebo změnili důležité prostředky. Na rozdíl od řízení přístupu na základě role používají zámky prostředků omezení pro všechny uživatele a role. Zámek můžete nastavit na úroveň *CanNotDelete* nebo *ReadOnly*.

K vytvoření nebo odstranění zámků správy musíte mít přístup k akcím `Microsoft.Authorization/locks/*`. Z předdefinovaných rolí má tyto akce povolené pouze **vlastník** a **správce uživatelských přístupů**.

Pokud chcete zamknout virtuální počítač a skupinu zabezpečení sítě, použijte příkaz [az lock create](/cli/azure/lock#az_lock_create):

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

Zámky otestujete pomocí následujícího příkazu:

```azurecli-interactive 
az group delete --name myResourceGroup
```

Zobrazí se chyba s oznámením, že operaci odstranění nelze provést z důvodu zámku. Skupinu prostředků odstraníte, pouze pokud skutečně odeberete zámky. Tento krok najdete v části o [vyčištění prostředků](#clean-up-resources).

## <a name="tag-resources"></a>Označení prostředků

K logickému uspořádání prostředků Azure podle kategorií slouží [značky](../../azure-resource-manager/resource-group-using-tags.md). Každá značka se skládá z názvu a hodnoty. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí.

[!INCLUDE [Resource Manager governance tags CLI](../../../includes/resource-manager-governance-tags-cli.md)]

K virtuálnímu počítači přidáte značky pomocí příkazu [az resource tag](/cli/azure/resource#az_resource_tag). Nezachovají se žádné existující značky prostředku.

```azurecli-interactive
az resource tag -n myVM \
  -g myResourceGroup \
  --tags Dept=IT Environment=Test Project=Documentation \
  --resource-type "Microsoft.Compute/virtualMachines"
```

### <a name="find-resources-by-tag"></a>Hledání prostředků podle značky

K vyhledání prostředků pomocí názvu značky a hodnoty použijte příkaz [az resource list](/cli/azure/resource#az_resource_list):

```azurecli-interactive
az resource list --tag Environment=Test --query [].name
```

Vrácené hodnoty můžete použít pro úlohy správy, jako je zastavení všech virtuálních počítačů s určitou hodnotou značky.

```azurecli-interactive
az vm stop --ids $(az resource list --tag Environment=Test --query "[?type=='Microsoft.Compute/virtualMachines'].id" --output tsv)
```

### <a name="view-costs-by-tag-values"></a>Zobrazení nákladů podle hodnoty značky

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Zamčená skupina zabezpečení sítě nejde odstranit, dokud neodstraníte zámek. Zámek odeberete načtením ID zámků a jejich zadáním do příkazu [az lock delete](/cli/azure/lock#az_lock_delete):

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

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group#az_group_delete). Ukončete relaci SSH k vašemu virtuálnímu počítači a pak odstraňte prostředky následujícím způsobem:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili vlastní image virtuálního počítače. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Přiřazení role uživateli
> * Použití zásad, které vynucují standardy
> * Ochrana důležitých prostředků pomocí zámků
> * Označení prostředků pro fakturaci a správu

Přejděte k dalšímu kurzu, kde se seznámíte s virtuálními počítači s vysokou dostupností.

> [!div class="nextstepaction"]
> [Monitorování virtuálních počítačů](tutorial-monitoring.md)

