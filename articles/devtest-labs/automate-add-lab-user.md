---
title: Automatizace přidávání uživatele testovacího prostředí v Azure DevTest Labs | Microsoft Docs
description: V tomto článku se dozvíte, jak automatizovat přidávání uživatelů do testovacího prostředí v Azure DevTest Labs pomocí šablon Azure Resource Manager, PowerShellu a rozhraní příkazového řádku.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6dddf06289da79e16cbd7e64869fa77f0a40dd22
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508822"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatizace přidání uživatele testovacího prostředí do testovacího prostředí v Azure DevTest Labs
Azure DevTest Labs umožňuje rychle vytvářet samoobslužná prostředí pro vývoj a testování pomocí Azure Portal. Pokud ale máte několik týmů a několik instancí DevTest Labs, automatizace procesu vytváření může ušetřit čas. [Šablony Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) umožňují vytvářet laboratoře, testovací virtuální počítače, vlastní image, vzorce a přidávat uživatele automatizovaným způsobem. Tento článek se zaměřuje především na přidávání uživatelů do instance DevTest Labs.

Pokud chcete přidat uživatele do testovacího prostředí, přidejte uživatele do role **uživatele DevTest Labs** pro testovací prostředí. V tomto článku se dozvíte, jak automatizovat přidání uživatele do testovacího prostředí jedním z následujících způsobů:

- Šablony Azure Resource Manageru
- Rutiny Azure PowerShellu 
- Azure CLI

## <a name="use-azure-resource-manager-templates"></a>Použití šablon Azure Resource Manageru
Následující vzorová Správce prostředků šablona určuje uživatele, který má být přidán do role **uživatele DevTest Labs** v testovacím prostředí. 

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

Pokud přiřazujete roli ve stejné šabloně, která vytváří testovací prostředí, nezapomeňte přidat závislost mezi prostředkem přiřazení role a testovacím prostředím. Další informace najdete v článku [Definování závislostí v Azure Resource Manager šablonách](../azure-resource-manager/templates/define-resource-dependency.md) .

### <a name="role-assignment-resource-information"></a>Informace o prostředku přiřazení role
Prostředek přiřazení role musí zadat typ a název.

První věcí, kterou si všimněte, je, že typ prostředku není `Microsoft.Authorization/roleAssignments` tak, jak by byl pro skupinu prostředků.  Místo toho typ prostředku odpovídá vzoru `{provider-namespace}/{resource-type}/providers/roleAssignments` . V takovém případě bude typ prostředku `Microsoft.DevTestLab/labs/providers/roleAssignments` .

Samotný název přiřazení role musí být globálně jedinečný.  Název přiřazení používá vzorek `{labName}/Microsoft.Authorization/{newGuid}` . `newGuid`Je hodnota parametru pro šablonu. Zajišťuje, aby byl název přiřazení role jedinečný. Jelikož nejsou k dispozici žádné funkce šablon pro vytváření identifikátorů GUID, je nutné vygenerovat identifikátor GUID sami pomocí nástroje generátoru GUID.  

V šabloně je název přiřazení role definovaný `fullDevTestLabUserRoleName` proměnnou. Přesný řádek ze šablony:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Vlastnosti prostředku přiřazení role
Vlastní přiřazení role definuje tři vlastnosti. Potřebuje `roleDefinitionId` , `principalId` a `scope` .

### <a name="role-definition"></a>Definice role
ID definice role je identifikátor řetězce pro existující definici role. ID role je ve formátu `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}` . 

ID předplatného se získává pomocí `subscription().subscriptionId` funkce šablony.  

Musíte získat definici role pro `DevTest Labs User` předdefinovanou roli. K získání identifikátoru GUID pro roli [uživatele DevTest Labs](../role-based-access-control/built-in-roles.md#devtest-labs-user) můžete použít [přiřazení rolí REST API](/rest/api/authorization/roleassignments) nebo rutinu [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) .

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

ID role je definováno v oddílu proměnné a pojmenované `devTestLabUserRoleId` . V šabloně je ID role nastavené na: 111111111-0000-0000-11111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>ID objektu zabezpečení
ID objektu zabezpečení je ID objektu uživatele, skupiny nebo instančního objektu služby Active Directory, které chcete přidat jako uživatele testovacího prostředí do testovacího prostředí. Šablona používá `ObjectId` jako parametr.

Identifikátor ObjectId můžete získat pomocí rutin [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup nebo [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) PowerShell. Tyto rutiny vrátí jeden nebo seznam objektů služby Active Directory, které mají vlastnost ID, což je ID objektu, které potřebujete. Následující příklad ukazuje, jak získat ID objektu jednoho uživatele ve společnosti.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

Můžete také použít rutiny prostředí PowerShell pro Azure Active Directory, které zahrnují rutiny [Get-MsolUser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0), [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)a [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>Obor
Obor Určuje prostředek nebo skupinu prostředků, pro které by se mělo přiřazení role použít. V případě prostředků je rozsah ve formátu: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}` . Šablona používá `subscription().subscriptionId` funkci k vyplnění `subscription-id` části a `resourceGroup().name` funkci šablony, která se má vyplnit `resource-group-name` . Pomocí těchto funkcí se znamená, že testovací prostředí, ke kterému přiřadíte roli, musí existovat v aktuálním předplatném a stejnou skupinu prostředků, ve které se nasazování šablony provádí. Poslední část `resource-name` je název testovacího prostředí. Tato hodnota je přijímána prostřednictvím parametru šablony v tomto příkladu. 

Rozsah rolí v šabloně: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Nasazení šablony
Nejprve vytvořte soubor parametrů (například azuredeploy.parameters.json), který předává hodnoty pro parametry v šabloně Správce prostředků. 

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

Pak použijte rutinu [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) prostředí PowerShell k nasazení šablony Správce prostředků. Následující vzorový příkaz přiřadí uživateli, skupině nebo instančnímu objektu roli uživatele DevTest Labs pro testovací prostředí.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

Je důležité si uvědomit, že název nasazení skupiny a identifikátor GUID přiřazení role musí být jedinečné. Pokud se pokusíte nasadit přiřazení prostředku s nejedinečným identifikátorem GUID, zobrazí se `RoleAssignmentUpdateNotPermitted` Chyba.

Pokud plánujete používat šablonu několikrát pro přidání několika objektů služby Active Directory do role uživatele DevTest Labs pro testovací prostředí, zvažte použití dynamických objektů v příkazu PowerShellu. Následující příklad používá rutinu [New-GUID](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) k dynamickému určení názvu nasazení skupiny prostředků a identifikátoru GUID přiřazení role.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Použití Azure Powershell
Jak je popsáno v úvodu, vytvoříte nové přiřazení role Azure, ve kterém přidáte uživatele do role **uživatele DevTest Labs** pro testovací prostředí. V PowerShellu to uděláte pomocí rutiny [New-AzureRMRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) . Tato rutina má mnoho volitelných parametrů, které umožňují flexibilitu. `ObjectId`Jako objekt, který má `SigninName` `ServicePrincipalName` udělena oprávnění, lze zadat, nebo.  

Tady je ukázkový příkaz Azure PowerShell, který přidá uživatele do role uživatele DevTest Labs v zadaném testovacím prostředí.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Chcete-li určit prostředek, pro který mají být udělena oprávnění, lze zadat kombinací `ResourceName` , `ResourceType` `ResourceGroup` nebo pomocí `scope` parametru. Bez ohledu na to, jakou kombinaci parametrů použijete, poskytněte rutině dostatek informací, aby jednoznačně identifikovala objekt služby Active Directory (uživatel, skupinu nebo instanční objekt), obor (skupinu prostředků nebo prostředek) a definici role.

## <a name="use-azure-command-line-interface-cli"></a>Použití rozhraní příkazového řádku Azure (CLI)
V Azure CLI se do testovacího prostředí přidá uživatel Labs pomocí `az role assignment create` příkazu. Další informace o rutinách Azure CLI najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure CLI](../role-based-access-control/role-assignments-cli.md).

Objekt, kterému je udělen přístup, lze určit pomocí `objectId` `signInName` parametrů,, `spn` . Testovacímu prostředí, ke kterému má být objekt udělen přístup, lze identifikovat pomocí `scope` adresy URL nebo kombinace `resource-name` `resource-type` parametrů, a `resource-group` .

Následující příklad rozhraní příkazového řádku Azure vám ukáže, jak přidat osobu do role uživatele DevTest Labs pro zadané testovací prostředí.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Vytváření a správa virtuálních počítačů pomocí DevTest Labs pomocí Azure CLI](devtest-lab-vmcli.md)
- [Vytvoření virtuálního počítače s DevTest Labs pomocí Azure PowerShell](devtest-lab-vm-powershell.md)
- [Spuštění a zastavení Azure DevTest Labs virtuálních počítačů pomocí nástrojů příkazového řádku](use-command-line-start-stop-virtual-machines.md)

