---
title: Vytvoření a publikování spravované aplikace katalogu služeb Azure | Microsoft Docs
description: Ukazuje, jak vytvořit spravovanou aplikaci Azure, která je určená pro členy vaší organizace.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 05/15/2018
ms.author: tomfitz
ms.openlocfilehash: b7f8bbcad39000e7e71149824535a6a82b26c758
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305306"
---
# <a name="publish-a-managed-application-for-internal-consumption"></a>Publikování spravované aplikace pro interní účely

Můžete vytvořit a publikovat [spravovanou aplikaci](overview.md) Azure, která je určená pro členy vaší organizace. Oddělení IT může například publikovat spravované aplikace, které zajišťují dodržování standardů organizace. Tyto spravované aplikace jsou k dispozici prostřednictvím katalogu služeb, ne prostřednictvím Azure Marketplace.

Pokud chcete publikovat spravovanou aplikaci pro katalog služeb, je třeba provést tyto kroky:

* Vytvořte šablonu, která definuje prostředky pro nasazení se spravovanou aplikací.
* Definujte prvky uživatelského rozhraní portálu pro nasazení spravované aplikace.
* Vytvořte balíček ZIP, který obsahuje požadované soubory šablony.
* Rozhodněte, který uživatel, skupina nebo aplikace potřebují přístup ke skupině prostředků v rámci předplatného uživatele.
* Vytvořte definici spravované aplikace, která odkazuje na balíček ZIP a požaduje přístup pro příslušnou identitu.

V tomto článku obsahuje spravovaná aplikace jenom účet úložiště. Je určená k ilustraci kroků publikování spravované aplikace. Úplné příklady najdete v tématu [Ukázkové projekty pro spravované aplikace Azure](sample-projects.md).

## <a name="create-the-resource-template"></a>Vytvoření šablony prostředků

Každá definice spravované aplikace obsahuje soubor s názvem **mainTemplate.json**. V něm se definují prostředky Azure, které se mají zřídit. Šablona se nijak neliší od běžné šablony Resource Manageru.

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

Přidejte oba soubory do souboru ZIP a s názvem app.zip. Oba soubory musí být na kořenové úrovni souboru ZIP. Pokud je umístíte do složky, při vytváření definice spravované aplikace se zobrazí chyba s informací, že požadované soubory nejsou k dispozici. 

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

Dalším krokem je výběr skupiny uživatelů nebo aplikace pro správu prostředků jménem zákazníka. Tato skupina uživatelů nebo aplikace má oprávnění pro skupinu spravovaných prostředků podle přiřazené role. Touto rolí může být kterákoli předdefinovaná role řízení přístupu na základě role (RBAC), například role vlastníka nebo přispěvatele. Oprávnění ke správě prostředků můžete udělit i jednotlivým uživatelům, obvykle se ale toto oprávnění přiřazuje skupině uživatelů. Pokud chcete vytvořit novou skupinu uživatelů služby Active Directory, přečtěte si téma [Vytvoření skupiny a přidání členů v Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

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

Pokud skupinu prostředků pro uložení definice spravované aplikace ještě nemáte, vytvořte ji:

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

## <a name="create-the-managed-application"></a>Vytvoření spravované aplikace

Spravovanou aplikaci můžete nasadit prostřednictvím portálu, prostředí PowerShell nebo rozhraní příkazového řádku Azure.

### <a name="powershell"></a>PowerShell

Nejdřív použijeme k nasazení spravované aplikace prostředí PowerShell.

```powershell
# Create resource group
New-AzureRmResourceGroup -Name applicationGroup -Location westcentralus

# Get ID of managed application definition
$appid=(Get-AzureRmManagedApplicationDefinition -ResourceGroupName appDefinitionGroup -Name ManagedStorage).ManagedApplicationDefinitionId

# Create the managed application
New-AzureRmManagedApplication `
  -Name storageApp `
  -Location westcentralus `
  -Kind ServiceCatalog `
  -ResourceGroupName applicationGroup `
  -ManagedApplicationDefinitionId $appid `
  -ManagedResourceGroupName "InfrastructureGroup" `
  -Parameter "{`"storageAccountNamePrefix`": {`"value`": `"demostorage`"}, `"storageAccountType`": {`"value`": `"Standard_LRS`"}}"
```

Spravovaná aplikace a spravovaná infrastruktura teď existuje v rámci předplatného.

### <a name="portal"></a>Portál

Teď použijeme k nasazení spravované aplikace portál. Zobrazí se uživatelské rozhraní, které jste vytvořili v balíčku.

1. Přejděte na web Azure Portal. Vyberte **+ Vytvořit prostředek** a vyhledejte **katalog služeb**.

   ![Hledání katalogu služeb](./media/publish-service-catalog-app/create-new.png)

1. Vyberte **Spravovaná aplikace katalogu služeb**.

   ![Výběr katalogu služeb](./media/publish-service-catalog-app/select-service-catalog-managed-app.png)

1. Vyberte **Vytvořit**.

   ![Výběr možnosti vytvoření](./media/publish-service-catalog-app/select-create.png)

1. Najděte spravovanou aplikaci, kterou chcete vytvořit ze seznamu dostupných řešení, a vyberte ji. Vyberte **Vytvořit**.

   ![Nalezení spravované aplikace](./media/publish-service-catalog-app/find-application.png)

1. Zadejte základní informace, které jsou potřeba pro spravovanou aplikaci. Zadejte předplatné a novou skupinu prostředků, která má spravovanou aplikaci obsahovat. Jako umístění vyberte **USA – středozápad**. Až budete hotovi, vyberte **OK**.

   ![Zadání parametrů spravované aplikace](./media/publish-service-catalog-app/add-basics.png)

1. Zadejte hodnoty, které jsou specifické pro prostředky ve spravované aplikaci. Až budete hotovi, vyberte **OK**.

   ![Zadání parametrů prostředků](./media/publish-service-catalog-app/add-storage-settings.png)

1. Šablona ověří hodnoty, které jste zadali. V případě úspěšného ověření spusťte nasazení výběrem **OK**.

   ![Ověření spravované aplikace](./media/publish-service-catalog-app/view-summary.png)

Po dokončení nasazení existuje spravovaná aplikace ve skupině prostředků s názvem applicationGroup. Účet úložiště existuje ve skupině prostředků s názvem applicationGroup a připojenou hodnotou hash řetězce.

## <a name="next-steps"></a>Další kroky

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací](overview.md).
* Příklady projektů najdete v tématu [Ukázkové projekty pro spravované aplikace Azure](sample-projects.md).
* Pokud chcete zjistit, jak vytvořit definiční soubor uživatelského rozhraní pro spravovanou aplikaci, přečtěte si téma [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
