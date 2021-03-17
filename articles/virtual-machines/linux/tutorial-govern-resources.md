---
title: Kurz – Správa virtuálních počítačů pomocí rozhraní příkazového řádku
description: V tomto kurzu se naučíte používat rozhraní příkazového řádku Azure ke správě virtuálních počítačů Azure pomocí Azure RBAC, zásad, zámků a značek.
services: virtual-machines
documentationcenter: virtual-machines
author: tfitzmac
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: tomfitz
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c724f41172ef675f94139cea0ed77fc0232b126b
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561751"
---
# <a name="tutorial-learn-about-linux-virtual-machine-management-with-azure-cli"></a>Kurz: informace o správě virtuálních počítačů s Linux pomocí Azure CLI

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- V tomto kurzu se vyžaduje verze rozhraní příkazového řádku Azure 2.0.30 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="understand-scope"></a>Orientace v oborech

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

V tomto kurzu se nastavení pro správu aplikují na skupinu prostředků, takže je po skončení můžete snadno odebrat.

Vytvoříme skupinu prostředků.

```azurecli-interactive
az group create --name myResourceGroup --location "East US"
```

V tuto chvíli je skupina prostředků prázdná.

## <a name="azure-role-based-access-control"></a>Řízení přístupu na základě role v Azure

Potřebujete zajistit, aby uživatelé ve vaší organizaci měli správnou úroveň přístupu k těmto prostředkům. Nechcete uživatelům dát neomezený přístup, ale zároveň jim potřebujete umožnit dělat svou práci. [Řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md) umožňuje spravovat uživatele, kteří mají oprávnění k provádění konkrétních akcí v oboru.

K vytváření a odebírání přiřazení rolí musí mít uživatelé přístup `Microsoft.Authorization/roleAssignments/*`. Tento přístup se poskytuje prostřednictvím role vlastníka nebo správce uživatelských přístupů.

Pro správu řešení virtuálních počítačů existují v závislosti na prostředcích tři role, které poskytují běžný přístup:

* [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Přispěvatel sítě](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Přispěvatel účtů úložiště](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Místo přiřazování rolí jednotlivým uživatelům je často jednodušší použít skupinu Azure Active Directory obsahující uživatele, kteří potřebují provádět podobné akce. Potom této skupině přiřaďte odpovídající role. Pro účely tohoto článku použijte buď existující skupinu pro správu virtuálního počítače, nebo pomocí portálu [vytvořte skupinu Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Po vytvoření nové skupiny nebo vyhledání existující skupiny pomocí příkazu [az role assignment create](/cli/azure/policy/assignment#az_policy_assignment_create) přiřaďte novou skupinu Azure Active Directory k roli Přispěvatel virtuálních počítačů v dané skupině prostředků.

```azurecli-interactive
adgroupId=$(az ad group show --group <your-group-name> --query objectId --output tsv)

az role assignment create --assignee-object-id $adgroupId --role "Virtual Machine Contributor" --resource-group myResourceGroup
```

Pokud se zobrazí chyba s oznámením, že **objekt zabezpečení \<guid> v adresáři neexistuje**, znamená to, že se nová skupina ještě nerozšířila napříč Azure Active Directory. Zkuste příkaz znovu spustit.

Obvykle tento postup zopakujete pro role *Přispěvatel sítě* a *Přispěvatel účtů úložiště*, abyste zajistili přiřazení uživatelů ke správě nasazených prostředků. V tomto článku můžete tyto kroky vynechat.

## <a name="azure-policy"></a>Azure Policy

[Azure Policy](../../governance/policy/overview.md) pomáhá zajistit, aby všechny prostředky v předplatném splňovaly firemní standardy. Vaše předplatné už obsahuje několik definic zásad. Pokud chcete zobrazit definice dostupných zásad, použijte příkaz [az policy definition list](/cli/azure/policy/definition#az_policy_definition_list):

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

[Zámky prostředků](../../azure-resource-manager/management/lock-resources.md) zabraňují tomu, aby uživatelé ve vaší organizaci neúmyslně odstranili nebo změnili důležité prostředky. Na rozdíl od řízení přístupu na základě role používají zámky prostředků omezení pro všechny uživatele a role. Zámek můžete nastavit na úroveň *CanNotDelete* nebo *ReadOnly*.

K vytvoření nebo odstranění zámků správy musíte mít přístup k akcím `Microsoft.Authorization/locks/*`. Z předdefinovaných rolí má tyto akce povolené pouze **vlastník** a **správce uživatelských přístupů**.

Pokud chcete zamknout virtuální počítač a skupinu zabezpečení sítě, použijte příkaz [az lock create](/cli/azure/resource/lock#az_resource_lock_create):

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

Zobrazí se chyba s oznámením, že operaci odstranění nelze kvůli zámku dokončit. Skupinu prostředků odstraníte, pouze pokud skutečně odeberete zámky. Tento krok najdete v části o [vyčištění prostředků](#clean-up-resources).

## <a name="tag-resources"></a>Označení prostředků

[Značky](../../azure-resource-manager/management/tag-resources.md) můžete použít pro prostředky Azure, abyste je logicky uspořádali podle kategorií. Každá značka se skládá z názvu a hodnoty. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí.

[!INCLUDE [Resource Manager governance tags CLI](../../../includes/resource-manager-governance-tags-cli.md)]

K virtuálnímu počítači přidáte značky pomocí příkazu [az resource tag](/cli/azure/resource#az_resource_list). Nezachovají se žádné existující značky prostředku.

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

Zamčená skupina zabezpečení sítě nejde odstranit, dokud neodstraníte zámek. Zámek odeberete načtením ID zámků a jejich zadáním do příkazu [az lock delete](/cli/azure/resource/lock#az_resource_lock_delete):

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

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít příkaz [AZ Group Delete](/cli/azure/group#az_group_delete) . Ukončete relaci SSH k vašemu virtuálnímu počítači a pak odstraňte prostředky následujícím způsobem:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili vlastní image virtuálního počítače. Naučili jste se:

> [!div class="checklist"]
> * Přiřazení role uživateli
> * Použití zásad, které vynucují standardy
> * Ochrana důležitých prostředků pomocí zámků
> * Označení prostředků pro fakturaci a správu

Přejděte k dalšímu kurzu, kde se dozvíte, jak identifikovat změny a spravovat aktualizace balíčků na virtuálním počítači.

> [!div class="nextstepaction"]
> [Správa virtuálních počítačů](tutorial-config-management.md)
