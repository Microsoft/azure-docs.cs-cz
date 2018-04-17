---
title: Řídí virtuální počítače Azure pomocí rozhraní příkazového řádku Azure | Microsoft Docs
description: Kurz – spravovat virtuální počítače Azure s použitím RBAC, zásady, zámků a značky pomocí rozhraní příkazového řádku Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: tomfitz
ms.openlocfilehash: a7d44e421162cf5784dde58f757e235d12b63cba
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="virtual-machine-governance-with-azure-cli"></a>Zásady správného řízení virtuálního počítače pomocí rozhraní příkazového řádku Azure

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Instalace a použití rozhraní příkazového řádku místně, najdete v části [nainstalovat Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="understand-scope"></a>Pochopení oboru

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

V tomto kurzu použít, můžete snadno odebrat tato nastavení po dokončení všech nastavení pro správu do skupiny prostředků.

Umožňuje vytvořit příslušné skupině prostředků.

```azurecli-interactive
az group create --name myResourceGroup --location "East US"
```

V současné době skupina prostředků je prázdný.

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Chcete Ujistěte se, že uživatelé ve vaší organizaci mají správnou úroveň přístupu na tyto prostředky. Nechcete, aby uživatelům udělit neomezený přístup, ale musíte také zkontrolujte, zda že mohou provádět práci. [Řízení přístupu na základě role](../../role-based-access-control/overview.md) můžete spravovat uživatele, kteří mají oprávnění k dokončení určitých akcí u oboru.

Vytvoření a odeberte přiřazení rolí, uživatelé musí mít `Microsoft.Authorization/roleAssignments/*` přístup. Tento přístup je poskytována prostřednictvím role vlastníka nebo správce uživatelského přístupu.

Pro správu řešení virtuálního počítače, existují tři role konkrétní prostředky, které poskytují běžně potřebné přístup:

* [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Přispěvatel sítě](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Přispěvatel účtu úložiště](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Místo přiřazení rolí pro jednotlivé uživatele, je často jednodušší [vytvoření skupiny Azure Active Directory](../../active-directory/active-directory-groups-create-azure-portal.md) pro uživatele, kteří potřebují k provádění podobných akcí. Potom přiřaďte této skupině do odpovídající role. Ke zjednodušení tohoto článku, vytvořit skupinu služby Azure Active Directory bez členů. Tato skupina stále můžete přiřadit do rolí pro obor. 

Následující příklad vytvoří skupinu Azure Active Directory s názvem *VMDemoContributors* s e-mailu Přezdívka *vmDemoGroup*. E-mailu Přezdívka slouží jako alias pro skupinu.

```azurecli-interactive
adgroupId=$(az ad group create --display-name VMDemoContributors --mail-nickname vmDemoGroup --query objectId --output tsv)
```

Jak dlouho trvá chvíli po příkazového řádku vrátí pro skupinu rozšíří v rámci Azure Active Directory. Po 20 nebo 30 sekund čekání, použijte [vytvořit přiřazení role az](/cli/azure/role/assignment#az_role_assignment_create) příkazu přiřaďte nové skupiny Azure Active Directory k roli Přispěvatel virtuálních počítačů pro skupinu prostředků.  Pokud předtím, než se rozšíří, spusťte následující příkaz, zobrazí se chybová zpráva, **hlavní <guid> neexistuje v adresáři**. Zkuste znovu spustit příkaz.

```azurecli-interactive
az role assignment create --assignee-object-id $adgroupId --role "Virtual Machine Contributor" --resource-group myResourceGroup
```

Obvykle, opakujte tento postup pro *Přispěvatel sítě* a *Přispěvatel účet úložiště* a ujistěte se, uživatelé budou přiřazení ke správě nasazené prostředky. V tomto článku můžete přeskočit těchto kroků.

## <a name="azure-policies"></a>Azure zásady

[!INCLUDE [Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Pomocí zásad

Vaše předplatné už má několik definice zásady. Pokud chcete zobrazit definice dostupné zásady, použijte [seznamu definice zásad az](/cli/azure/policy/definition#az_policy_definition_list) příkaz:

```azurecli-interactive
az policy definition list --query "[].[displayName, policyType, name]" --output table
```

Zobrazí existující definice zásady. Typ zásad je buď **BuiltIn** nebo **vlastní**. Projděte definice pro šablony, které popisují podmínku chcete přiřadit. V tomto článku můžete přiřadit zásady který:

* Omezení umístění pro všechny prostředky.
* Omezte SKU pro virtuální počítače.
* Audit virtuálních počítačů, které nepoužívají spravované disky.

V následujícím příkladu načíst tři definice zásady založené na zobrazovaný název. Můžete použít [na vytvoření přiřazení zásady az](/cli/azure/policy/assignment#az_policy_assignment_create) příkazu přiřaďte tyto definice do skupiny prostředků. Pro některé zásady zadejte hodnoty parametrů k určení povolených hodnot.

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

V předchozím příkladu se předpokládá, že už znáte parametry pro zásadu. Pokud potřebujete zobrazit parametry, použijte:

```azurecli-interactive
az policy definition show --name $locationDefinition --query parameters
```

## <a name="deploy-the-virtual-machine"></a>Nasaďte virtuální počítač

Přiřadili jste role a zásady, takže jste připravení nasadit řešení. Výchozí velikost je Standard_DS1_v2, což je jedno z vaší povolená SKU. Příkaz vytvoří klíče SSH, pokud neexistují v výchozí umístění.

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

Po dokončení nasazení můžete použít další nastavení správy k řešení.

## <a name="lock-resources"></a>Uzamčení prostředků

[Uzamčení prostředků](../../azure-resource-manager/resource-group-lock-resources.md) zabránit uživatelům ve vaší organizaci neúmyslnému odstranění nebo úprava důležitých prostředků. Na rozdíl od řízení přístupu na základě rolí uzamčení prostředků platí omezení ve všech uživatelů a rolí. Můžete nastavit zámek na úrovni *CanNotDelete* nebo *jen pro čtení*.

Vytvořit nebo odstranit zámky správy, musíte mít přístup k `Microsoft.Authorization/locks/*` akce. Z předdefinovaných rolí pouze **vlastníka** a **správce přístupu uživatelů** mají tyto akce.

Pokud chcete zamknout virtuálního počítače a skupiny zabezpečení sítě, použijte [vytvoření zámku az](/cli/azure/lock#az_lock_create) příkaz:

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

K testování zámky, spusťte následující příkaz:

```azurecli-interactive 
az group delete --name myResourceGroup
```

Zobrazí chyba s oznámením, že operaci odstranění nelze provést z důvodu zámek. Skupina prostředků lze odstranit pouze pokud odeberete konkrétně zámky. Tento krok se zobrazí v [vyčištění prostředků](#clean-up-resources).

## <a name="tag-resources"></a>Značka prostředky

Použijete [značky](../../azure-resource-manager/resource-group-using-tags.md) vaše prostředky Azure logicky uspořádat podle kategorií. Každá značka se skládá z názvu a hodnoty. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí.

[!INCLUDE [Resource Manager governance tags CLI](../../../includes/resource-manager-governance-tags-cli.md)]

Pro použití značek k virtuálnímu počítači, použijte [značky prostředku az](/cli/azure/resource#az_resource_tag) příkaz. Všechny existující značky prostředku se nezachovají.

```azurecli-interactive
az resource tag -n myVM \
  -g myResourceGroup \
  --tags Dept=IT Environment=Test Project=Documentation \
  --resource-type "Microsoft.Compute/virtualMachines"
```

### <a name="find-resources-by-tag"></a>Najít prostředky podle značky

K vyhledání prostředků s názvem značky a hodnoty, použijte [seznam zdrojů az](/cli/azure/resource#az_resource_list) příkaz:

```azurecli-interactive
az resource list --tag Environment=Test --query [].name
```

Pro úlohy správy, jako zastavení všechny virtuální počítače s hodnotou značky můžete použít vrácené hodnoty.

```azurecli-interactive
az vm stop --ids $(az resource list --tag Environment=Test --query "[?type=='Microsoft.Compute/virtualMachines'].id" --output tsv)
```

### <a name="view-costs-by-tag-values"></a>Zobrazení nákladů podle hodnoty značky

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Skupina zabezpečení sítě uzamčeném nejde odstranit, dokud se odebere ze zařízení zámek. Odebrat zámek, načíst ID zámky a poskytovat jim [odstranit zámek az](/cli/azure/lock#az_lock_delete) příkaz:

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


## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili vlastní image virtuálního počítače. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Přiřazení uživatele k roli
> * Použít zásady, které vynucují standardy
> * Chránit důležité prostředky s zámky.
> * Označit prostředky pro správu a fakturaci

Přechodu na další informace o tom, jak vysoce dostupné virtuální počítače v dalším kurzu.

> [!div class="nextstepaction"]
> [Monitorování virtuálních počítačů](tutorial-monitoring.md)

