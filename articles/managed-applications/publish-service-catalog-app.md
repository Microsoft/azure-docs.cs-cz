---
title: Vytvoření a publikování spravované aplikace katalogu služeb Azure | Microsoft Docs
description: Ukazuje, jak vytvořit spravovanou aplikaci Azure, která je určená pro členy vaší organizace.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: a2e6e78268f97136533b4f72ce28373642b6c394
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801263"
---
# <a name="create-and-publish-a-managed-application-definition"></a>Vytvoření a publikování definice spravované aplikace

Můžete vytvořit a publikovat [spravovanou aplikaci](overview.md) Azure, která je určená pro členy vaší organizace. Oddělení IT může například publikovat spravované aplikace, které vyhovují standardům organizace. Tyto spravované aplikace jsou k dispozici prostřednictvím katalogu služeb, ne prostřednictvím Azure Marketplace.

Pokud chcete publikovat spravovanou aplikaci pro katalog služeb, je třeba provést tyto kroky:

* Vytvořte šablonu, která definuje prostředky pro nasazení se spravovanou aplikací.
* Definujte prvky uživatelského rozhraní portálu pro nasazení spravované aplikace.
* Vytvořte balíček ZIP, který obsahuje požadované soubory šablony.
* Rozhodněte, který uživatel, skupina nebo aplikace potřebují přístup ke skupině prostředků v rámci předplatného uživatele.
* Vytvořte definici spravované aplikace, která odkazuje na balíček ZIP a požaduje přístup pro příslušnou identitu.

V tomto článku má spravovaná aplikace jenom účet úložiště. Jejím cílem je ilustrovat postup publikování spravované aplikace. Úplné příklady najdete v tématu [Ukázkové projekty pro spravované aplikace Azure](sample-projects.md).

Příklady PowerShellu v tomto článku vyžadují prostředí Azure PowerShell verze 6.2 nebo novější. V případě potřeby [aktualizujte verzi](/powershell/azure/install-azurerm-ps).

## <a name="create-the-resource-template"></a>Vytvoření šablony prostředků

Každá definice spravované aplikace obsahuje soubor s názvem **mainTemplate.json**. V něm se definují prostředky Azure, které se mají nasadit. Šablona se nijak neliší od běžné šablony Resource Manageru.

Vytvořte soubor s názvem **mainTemplate.json**. V názvu se rozlišují velká a malá písmena.

Přidejte do souboru následující kód JSON. Definuje parametry pro vytvoření účtu úložiště a určuje vlastnosti pro účet úložiště.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
        }
    }
}
```

Uložte soubor mainTemplate.json.

## <a name="create-the-user-interface-definition"></a>Vytvoření definice uživatelského rozhraní

Azure Portal využívá soubor **createUiDefinition.json** ke generování uživatelského rozhraní pro uživatele, kteří vytvářejí spravovanou aplikaci. Můžete definovat, jak budou uživatelé zadávat vstup pro jednotlivé parametry. Můžete používat možnosti, jako je rozevírací seznam, textové pole, pole pro heslo a další nástroje pro zadávání vstupu. Pokud chcete zjistit, jak vytvořit definiční soubor uživatelského rozhraní pro spravovanou aplikaci, přečtěte si téma [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).

Vytvořte soubor s názvem **createUiDefinition.json**. V názvu se rozlišují velká a malá písmena.

Přidejte do souboru následující kód JSON.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

Uložte soubor createUiDefinition.json.

## <a name="package-the-files"></a>Zabalení souborů

Přidejte oba soubory do souboru ZIP a s názvem app.zip. Oba soubory musí být na kořenové úrovni souboru ZIP. Pokud je umístíte do složky, zobrazí se při vytváření definice spravované aplikace chyba s informacemi, že požadované soubory nejsou k dispozici. 

Nahrajte balíček do přístupného umístění, ze kterého je možné použít ho. 

```powershell
New-AzureRmResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context

New-AzureStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzureStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>Vytvoření definice spravované aplikace

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Vytvoření skupiny uživatelů nebo aplikace Azure Active Directory

Dalším krokem je výběr skupiny uživatelů nebo aplikace pro správu prostředků jménem zákazníka. Tato skupina uživatelů nebo aplikace má oprávnění pro skupinu spravovaných prostředků podle přiřazené role. Touto rolí může být kterákoli předdefinovaná role řízení přístupu na základě role (RBAC), například role vlastníka nebo přispěvatele. Oprávnění ke správě prostředků můžete udělit i jednotlivým uživatelům, obvykle se ale toto oprávnění přiřazuje skupině uživatelů. Pokud chcete vytvořit novou skupinu uživatelů služby Active Directory, přečtěte si téma [Vytvoření skupiny a přidání členů v Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Potřebujete ID objektu skupiny uživatelů, které se má používat pro správu zdrojů. 

```powershell
$groupID=(Get-AzureRmADGroup -DisplayName mygroup).Id
```

### <a name="get-the-role-definition-id"></a>Získání ID definici role

Dál potřebujete ID definice role pro předdefinovanou roli řízení přístupu na základě role (RBAC), pro které chcete uživateli, skupině uživatelů nebo aplikaci udělit přístup. Obvykle se používá role vlastníka, přispěvatele nebo čtenáře. Následující příkaz ukazuje, jak získat ID definice role pro roli vlastníka:

```powershell
$ownerID=(Get-AzureRmRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>Vytvoření definice spravované aplikace

Pokud ještě nemáte skupinu prostředků pro uložení definice spravované aplikace, vytvořte ji:

```powershell
New-AzureRmResourceGroup -Name appDefinitionGroup -Location westcentralus
```

Teď vytvoříte prostředek definice spravované aplikace.

```powershell
$blob = Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzureRmManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

### <a name="make-sure-users-can-see-your-definition"></a>Je potřeba zajistit, že budou uživatelé vidět vaši definici.

Máte přístup k definici spravované aplikace, ale je potřeba zajistit přístup i pro ostatní uživatele ve vaší organizaci. Udělte jim k definici alespoň přístup role Čtenář. Je možné, že tuto úroveň přístupu zdědili z předplatného nebo skupiny prostředků. Informace o tom, kdo má přístup k definici, a o přidání uživatelů nebo skupin najdete v tématu [Použití řízení přístupu na základě rolí ke správě přístupu k prostředkům předplatného Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Další kroky

* Informace o publikování spravované aplikace na webu Azure Marketplace najdete v tématu [Spravované aplikace Azure v Marketplace](publish-marketplace-app.md).
* Informace o nasazení instance spravované aplikace najdete v tématu [Nasazení aplikace z katalogu služeb prostřednictvím webu Azure Portal](deploy-service-catalog-quickstart.md).