---
title: Automatizace přidávání uživatele testovacího prostředí v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak automatizovat přidání uživatele do testovacího prostředí v Azure DevTest Labs pomocí šablon Azure Resource Manager, PowerShellu a rozhraní příkazového příkazového příkazu.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 70a6359923734c83590d4677bb2c93966c925d14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718135"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatizace přidání uživatele testovacího prostředí do testovacího prostředí v laboratořích Azure DevTest Labs
Azure DevTest Labs umožňuje rychle vytvářet samoobslužná prostředí pro testování a testování pomocí portálu Azure. Pokud však máte několik týmů a několik instancí DevTest Labs, automatizace procesu vytváření může ušetřit čas. [Šablony Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) umožnou vytvářet testovací prostředí, virtuální počítače testovacího prostředí, vlastní image, vzorce a přidávat uživatele automatizovaným způsobem. Tento článek se konkrétně zaměřuje na přidání uživatelů do instance DevTest Labs.

Chcete-li přidat uživatele do testovacího prostředí, přidejte uživatele do role **Uživatel DevTest Labs** pro testovací prostředí. Tento článek ukazuje, jak automatizovat přidání uživatele do testovacího prostředí pomocí jednoho z následujících způsobů:

- Šablony Azure Resource Manageru
- Rutiny Azure PowerShellu 
- Azure CLI.

## <a name="use-azure-resource-manager-templates"></a>Použití šablon Azure Resource Manageru
Následující ukázková šablona Správce prostředků určuje uživatele, který má být přidán do role **uživatele devTest Labs** testovacího prostředí. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

Pokud přiřazujete roli ve stejné šabloně, která vytváří testovací prostředí, nezapomeňte přidat závislost mezi zdrojem přiřazení role a testovacím prostředím. Další informace najdete [v tématu Definování závislostí v](../azure-resource-manager/templates/define-resource-dependency.md) článku Šablony Správce prostředků Azure.

### <a name="role-assignment-resource-information"></a>Informace o zdroji přiřazení role
Zdroj přiřazení role musí zadat typ a název.

První věc, kterou je třeba poznamenat, `Microsoft.Authorization/roleAssignments` je, že typ zdroje není takový, jaký by byl pro skupinu prostředků.  Místo toho typ prostředku následuje `{provider-namespace}/{resource-type}/providers/roleAssignments`podle vzoru . V takovém případě bude `Microsoft.DevTestLab/labs/providers/roleAssignments`typ prostředku .

Samotný název přiřazení role musí být globálně jedinečný.  Název přiřazení používá vzor `{labName}/Microsoft.Authorization/{newGuid}`. Je `newGuid` hodnota parametru pro šablonu. Zajišťuje, že název přiřazení role je jedinečný. Vzhledem k tomu, že neexistují žádné funkce šablony pro vytváření identifikátorů GUID, musíte sami vygenerovat identifikátor GUID pomocí libovolného nástroje generátoru IDENTIFIKÁTORŮ GUID.  

V šabloně je název přiřazení role definován `fullDevTestLabUserRoleName` proměnnou. Přesný řádek ze šablony je:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Vlastnosti zdroje přiřazení role
Samotné přiřazení role definuje tři vlastnosti. Potřebuje `roleDefinitionId`, `principalId`a `scope`.

### <a name="role-definition"></a>Definice role
ID definice role je identifikátor řetězce pro existující definici role. ID role je ve `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`formuláři . 

ID předplatného se získá `subscription().subscriptionId` pomocí funkce šablony.  

Musíte získat definici role `DevTest Labs User` pro předdefinovanou roli. Chcete-li získat identifikátor GUID pro roli [uživatele DevTest Labs,](../role-based-access-control/built-in-roles.md#devtest-labs-user) můžete použít [rozhraní REST API přiřazení rolí](/rest/api/authorization/roleassignments) nebo rutinu [Get-AzRoleDefinition.](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0)

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

ID role je definováno v části `devTestLabUserRoleId`proměnné a pojmenováno . V šabloně je ID role nastavena na: 111111111-0000-0000-1111111111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>ID jistiny
ID objektu je ID objektu uživatele, skupiny nebo instančního objektu služby Active Directory, které chcete přidat jako uživatele testovacího prostředí do testovacího prostředí. Šablona používá `ObjectId` jako parametr.

ObjectId můžete získat pomocí rutin [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup nebo [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) PowerShell rutiny. Tyto rutiny vrátí jeden nebo seznam objektů služby Active Directory, které mají vlastnost ID, což je ID objektu, které potřebujete. Následující příklad ukazuje, jak získat ID objektu jednoho uživatele ve společnosti.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

Můžete také použít rutiny prostředí Azure Active Directory PowerShell, které zahrnují [Get-MsolUser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0), [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)a [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>Rozsah
Obor určuje zdroj nebo skupinu prostředků, pro kterou by se mělo přiřazení role použít. U prostředků je obor ve `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`formě: . Šablona používá `subscription().subscriptionId` funkci k `subscription-id` vyplnění dílu a funkci `resourceGroup().name` `resource-group-name` šablony k vyplnění dílu. Použití těchto funkcí znamená, že testovací prostředí, kterému přiřazujete roli, musí existovat v aktuálním předplatném a stejné skupině prostředků, do které je nasazení šablony provedeno. Poslední část, `resource-name`, je název laboratoře. Tato hodnota je přijata prostřednictvím parametru šablony v tomto příkladu. 

Rozsah role v šabloně: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Nasazení šablony
Nejprve vytvořte soubor parametrů (například: azuredeploy.parameters.json), který předá hodnoty pro parametry v šabloně Správce prostředků. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

Potom použijte rutinu [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) PowerShell k nasazení šablony Správce prostředků. Následující příklad příkazu přiřadí osobu, skupinu nebo instanční objekt roli DevTest Labs uživatele pro testovací prostředí.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

Je důležité si uvědomit, že název nasazení skupiny a guid přiřazení role musí být jedinečný. Pokud se pokusíte nasadit přiřazení prostředků s nejedinečným identifikátorem `RoleAssignmentUpdateNotPermitted` GUID, zobrazí se chyba.

Pokud chcete šablonu použít několikrát k přidání několika objektů služby Active Directory do role Uživatel devTest Labs pro testovací prostředí, zvažte použití dynamických objektů v příkazu PowerShell. Následující příklad používá rutinu [New-Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) k dynamickému určení názvu identifikátoru GUID nasazení skupiny prostředků a přiřazení role.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Použití Azure Powershell
Jak je popsáno v úvodu, můžete vytvořit nové přiřazení role Azure přidat uživatele do role **DevTest Labs uživatele** pro testovací prostředí. V prostředí PowerShell uděláte to pomocí rutiny [New-AzureRMRoleAssignment.](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) Tato rutina má mnoho volitelných parametrů, které umožňují flexibilitu. V `ObjectId` `SigninName`, `ServicePrincipalName` , nebo lze zadat jako objekt udělují oprávnění.  

Tady je ukázkový příkaz Azure PowerShell, který přidá uživatele do role uživatele DevTest Labs v zadaném testovacím prostředí.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Chcete-li určit prostředek, ke kterému jsou udělena `ResourceName`oprávnění `ResourceType` `ResourceGroup` lze zadat `scope` kombinací , nebo parametr. Bez ohledu na použití kombinace parametrů zadejte rutině dostatek informací k jednoznačné identifikaci objektu služby Active Directory (uživatele, skupiny nebo objektu zabezpečení), oboru (skupiny prostředků nebo prostředku) a definice role.

## <a name="use-azure-command-line-interface-cli"></a>Použití rozhraní příkazového řádku Azure (CLI)
V rozhraní příkazového příkazu Azure přidání uživatele testovacího `az role assignment create` prostředí do testovacího prostředí se provádí pomocí příkazu. Další informace o rutinách Azure CLI najdete v [tématu Správa přístupu k prostředkům Azure pomocí RBAC a Azure CLI](../role-based-access-control/role-assignments-cli.md).

Objekt, kterému je udělen přístup, `objectId`může `signInName` `spn` být určen parametry , . Testovací prostředí, ke kterému je objektu udělen `scope` přístup, lze `resource-name`identifikovat `resource-type`pomocí `resource-group` adresy URL nebo kombinace , a parametry.

Následující příklad rozhraní příkazového systému Azure ukazuje, jak přidat osobu do role uživatele DevTest Labs pro zadaný testovací prostředí.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Vytváření a správa virtuálních počítačů pomocí devtest labs pomocí azure cli](devtest-lab-vmcli.md)
- [Vytvoření virtuálního počítače pomocí devTest Labs pomocí Azure PowerShellu](devtest-lab-vm-powershell.md)
- [Spuštění a zastavení virtuálních počítačů Azure DevTest Labs pomocí nástrojů příkazového řádku](use-command-line-start-stop-virtual-machines.md)

