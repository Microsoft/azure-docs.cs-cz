---
title: Automatické přidání uživatele testovacího prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak automatizovat přidávání uživatele testovacího prostředí do testovacího prostředí ve službě Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 2ad81ae97414abbf3266cc5728febf9abe836151
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65522950"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatické přidání uživatele testovacího prostředí do testovacího prostředí ve službě Azure DevTest Labs
Azure DevTest Labs umožňuje rychle vytvořit samoobslužné vývojových / testovacích prostředí pomocí webu Azure portal. Nicméně pokud máte několik týmů a několik instancí DevTest Labs, automatizace procesu vytváření ušetřit čas. [Šablony Azure Resource Manageru](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) vám umožňují vytvářet testovací prostředí, testovací prostředí virtuálních počítačů, vlastní Image, vzorce a přidání uživatelů automatizovaně. Tento článek se zaměřuje konkrétně na přidání uživatelů do instance DevTest Labs.

Přidání uživatele do testovacího prostředí, můžete uživatele přidat **uživatel služby DevTest Labs** role pro testovací prostředí. Tento článek popisuje, jak k automatizaci přidání uživatele do testovacího prostředí pomocí jednoho z následujících způsobů:

- Šablony Azure Resource Manageru
- Rutiny Azure PowerShellu 
- Azure CLI.

## <a name="use-azure-resource-manager-templates"></a>Použití šablon Azure Resource Manageru
Následující ukázka šablony Resource Manageru určuje uživateli přidat do **uživatel služby DevTest Labs** role testovacího prostředí. 

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

Pokud přiřazujete roli v stejnou šablonu, která je vytvoření testovacího prostředí, nezapomeňte přidat závislost mezi prostředek přiřazení role a testovací prostředí. Další informace najdete v tématu [definování závislostí v šablonách Azure Resource Manageru](../azure-resource-manager/resource-group-define-dependencies.md) článku.

### <a name="role-assignment-resource-information"></a>Informace o prostředku přiřazení role
Prostředek přiřazení role musí určovat typ a název.

První věc, kterou si je, že typ prostředku není `Microsoft.Authorization/roleAssignments` jako by šlo pro skupinu prostředků.  Místo toho typ prostředku odpovídá vzoru `{provider-namespace}/{resource-type}/providers/roleAssignments`. V takovém případě budou mít typ prostředku `Microsoft.DevTestLab/labs/providers/roleAssignments`.

Samotný název přiřazení role musí být globálně jedinečný.  Název přiřazení používá vzor `{labName}/Microsoft.Authorization/{newGuid}`. `newGuid` Je hodnota parametru šablony. Zajišťuje, že je jedinečný název přiřazení role. Neexistují žádné šablony funkce pro vytváření identifikátory GUID, je potřeba vygenerovat identifikátor GUID pomocí libovolného nástroje Generátor identifikátor GUID.  

V šabloně, název přiřazení role definované `fullDevTestLabUserRoleName` proměnné. Přesné řádek z této šablony je:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Vlastnosti prostředku přiřazení role
Přiřazení role, samotný definuje tři vlastnosti. Rozpojuje `roleDefinitionId`, `principalId`, a `scope`.

### <a name="role-definition"></a>Definice role
ID definice role je identifikátor řetězce pro existující definice role. Role je ID ve formě `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`. 

Předplatné se pomocí získat ID `subscription().subscriptionId` šablony funkce.  

Je potřeba získat definice role pro `DevTest Labs User` předdefinovaná role. Chcete-li získat GUID pro [uživatel služby DevTest Labs](../role-based-access-control/built-in-roles.md#devtest-labs-user) roli, můžete použít [rozhraní REST API pro přiřazení Role](/rest/api/authorization/roleassignments) nebo [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0) rutiny.

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

Role ID je definovaný v sekci proměnných a s názvem `devTestLabUserRoleId`. V šabloně role ID je nastaven na: 111111111-0000-0000-11111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>ID objektu zabezpečení
ID objektu zabezpečení je ID objektu uživatele služby Active Directory, skupinu nebo instanční objekt, který chcete přidat jako uživatele testovacího prostředí pro testovací prostředí. Šablona používá `ObjectId` jako parametr.

ObjectId můžete získat pomocí [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup nebo [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) rutin prostředí PowerShell. Tyto rutiny vrátí jeden nebo seznam objektů služby Active Directory, které mají vlastnost ID, která je ID objektu, které potřebujete. Následující příklad ukazuje, jak získat ID objektu jenom jednoho konkrétního uživatele ve společnosti.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

Můžete také použít rutiny Powershellu pro Azure Active Directory, které zahrnují [Get-MsolUser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0), [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0), a [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>Scope
Obor určuje prostředek nebo skupinu prostředků, pro který by se měly používat přiřazení role. Pro prostředky, obor je ve formě: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`. Šablona používá `subscription().subscriptionId` funkce vyplnit `subscription-id` část a `resourceGroup().name` funkce šablony vyplnit `resource-group-name` část. Použití těchto funkcí znamená, že testovací prostředí, ke kterému jste přiřazení role musí existovat v aktuálním předplatném ve stejné skupině prostředků, ke které je k nasazení šablony. Poslední část `resource-name`, je název testovacího prostředí. Tato hodnota je přijatých prostřednictvím parametru šablony v tomto příkladu. 

Obor role v šabloně: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Nasazení šablony
Nejprve vytvořte soubor s parametry (například: azuredeploy.parameters.json), který předává hodnoty pro parametry v šabloně Resource Manageru. 

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

Potom použijte [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) rutiny Powershellu pro nasazení šablony Resource Manageru. Následující ukázkový příkaz přiřadí osoby, skupiny nebo instanční objekt služby k roli uživatel služby DevTest Labs pro testovací prostředí.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

Je důležité si uvědomit, že nasazení název a roli přiřazení skupiny GUID musí být jedinečný. Pokud při pokusu nasadit přiřazení prostředků s jedinečný identifikátor GUID, pak dostanete `RoleAssignmentUpdateNotPermitted` chyby.

Pokud máte v plánu použít šablonu několikrát přidat několik objektů služby Active Directory k roli uživatel služby DevTest Labs pro testovací prostředí, zvažte použití dynamických objektů v příkazu prostředí PowerShell. V následujícím příkladu [nový identifikátor Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) rutiny k určení prostředků nasazení název a roli přiřazení skupiny GUID dynamicky.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Použití Azure Powershell
Jak je popsáno v úvodu, vytvořte nové přiřazení Azure role pro přidání uživatele do **uživatel služby DevTest Labs** role pro testovací prostředí. V prostředí PowerShell, můžete to provést pomocí [New-AzureRMRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) rutiny. Tato rutina má mnoho volitelné parametry, které umožňují flexibilitu. `ObjectId`, `SigninName`, Nebo `ServicePrincipalName` lze zadat jako objekt udělením oprávnění.  

Tady je ukázka příkazu prostředí Azure PowerShell, který přidá uživatele k roli uživatel služby DevTest Labs v zadané testovacího prostředí.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

K určení prostředků, ke kterému se oprávnění udělenou je možné zadat tak kombinaci `ResourceName`, `ResourceType`, `ResourceGroup` , nebo `scope` parametru. Jakékoli kombinace parametrů se používá, poskytnout dostatek informací pro rutinu k jednoznačné identifikaci objektu služby Active Directory (uživatele, skupinu nebo instanční objekt služby), rozsah (skupiny prostředků nebo prostředek) a definice role.

## <a name="use-azure-command-line-interface-cli"></a>Použití rozhraní příkazového řádku Azure (CLI)
V rozhraní příkazového řádku Azure, přidejte uživatele labs do testovacího prostředí se provádí pomocí `az role assignment create` příkazu. Další informace o rutinách rozhraní příkazového řádku Azure najdete v tématu [spravovat přístup k prostředkům Azure pomocí RBAC a rozhraní příkazového řádku Azure](../role-based-access-control/role-assignments-cli.md).

Objekt, který je udělení přístupu je možné zadat tak `objectId`, `signInName`, `spn` parametry. Testovací prostředí, ke které je objekt udělením přístupu lze identifikovat podle `scope` adresu url nebo ke kombinaci komponent `resource-name`, `resource-type`, a `resource-group` parametry.

Následující příklad rozhraní příkazového řádku Azure ukazuje, jak přidat uživatele k roli uživatel služby DevTest Labs pro zadaný testovací prostředí.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Další postup
Viz následující články:

- [Vytvoření a správa virtuálních počítačů s DevTest Labs pomocí Azure CLI](devtest-lab-vmcli.md)
- [Vytvoření virtuálního počítače s DevTest Labs s využitím Azure Powershellu](devtest-lab-vm-powershell.md)
- [Pomocí nástrojů příkazového řádku spouštět a zastavovat virtuální počítače Azure DevTest Labs](use-command-line-start-stop-virtual-machines.md)

