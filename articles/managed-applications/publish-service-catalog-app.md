---
title: Vytvoření a publikování aplikace spravované katalogu služby Azure | Microsoft Docs
description: Ukazuje, jak vytvořit spravovanou aplikaci Azure, která je určená pro členy vaší organizace.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/15/2018
ms.author: tomfitz
ms.openlocfilehash: 57821e9c7ed1ca04aa7442f089268c5e89a017c3
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="publish-a-managed-application-for-internal-consumption"></a>Publikování spravované aplikace pro interní používání

Můžete vytvářet a publikovat Azure [spravované aplikace](overview.md) , jsou určené pro členy vaší organizace. IT oddělení například může publikovat spravovaných aplikací, které bylo možné zajistit kompatibilitu s organizační standardy. Tyto spravované aplikace jsou k dispozici prostřednictvím katalogu služeb, není v Azure marketplace.

Chcete-li publikovat spravované aplikace pro katalogu služeb, postupujte takto:

* Vytvořte šablonu, která definuje prostředky pro nasazení s spravovaných aplikací.
* Prvky uživatelského rozhraní pro portál definujte při nasazování spravovaných aplikací.
* Vytvořte balíček ZIP, který obsahuje soubory požadované šablony.
* Rozhodněte, které uživatele, skupiny nebo aplikace potřebuje přístup ke skupině prostředků v předplatném uživatele.
* Vytvořte definici spravované aplikace, která odkazuje na balíček ZIP a požaduje přístup pro identitu.

Spravované aplikace pro tento článek obsahuje pouze účet úložiště. Je určena k ilustraci kroků publikování spravované aplikace. Dokončení příklady najdete v tématu [ukázkové projekty Azure spravované aplikace](sample-projects.md).

## <a name="create-the-resource-template"></a>Vytvoření šablony prostředků

Každé spravované aplikaci definice obsahuje soubor s názvem **mainTemplate.json**. V něm definovat zřídit prostředky Azure. Šablona se nijak neliší od běžné šablony Resource Manageru.

Vytvořte soubor s názvem **mainTemplate.json**. Název je malá a velká písmena.

Do souboru přidejte následující kód JSON. Definuje parametry pro vytvoření účtu úložiště a určuje vlastnosti pro účet úložiště.

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

## <a name="create-the-user-interface-definition"></a>Vytvořit definici uživatelského rozhraní

Používá portál Azure **createUiDefinition.json** soubor ke generování uživatelského rozhraní pro uživatele, kteří vytvářejí spravované aplikace. Můžete definovat, jak uživatelé zadali vstup pro jednotlivé parametry. Možnosti můžete použít jako rozevíracího seznamu, textové pole, pole pro heslo a další vstupní nástroje. Pokud chcete zjistit, jak vytvořit definiční soubor uživatelského rozhraní pro spravovanou aplikaci, přečtěte si téma [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).

Vytvořte soubor s názvem **createUiDefinition.json**. Název je malá a velká písmena.

Přidejte následující kód JSON do souboru.

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

## <a name="package-the-files"></a>Balíček soubory

Přidejte dva soubory do souboru ZIP a s názvem app.zip. Dva soubory musí být na kořenové úrovni souboru ZIP. Pokud je vložit do složky, obdržíte chybu při vytváření definice spravovaných aplikací, s oznámením, že nejsou k dispozici požadované soubory. 

Nahrání balíčku na dostupné místo z kde ji můžete použít. 

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

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Vytvoření skupiny uživatelů Azure Active Directory nebo aplikace

Dalším krokem je vybrat skupiny uživatelů nebo aplikace pro správu k prostředkům jménem zákazníka. Této skupiny uživatelů nebo aplikací má oprávnění pro skupinu spravovaných prostředků podle role, která je přiřazena. Tato role může být žádné předdefinované role řízení přístupu na základě Role (RBAC) jako vlastníka nebo přispěvatele. Také můžete udělit oprávnění jednotlivého uživatele ke správě prostředků, ale obvykle přiřadit toto oprávnění pro skupinu uživatelů. Chcete-li vytvořit novou skupinu uživatelů služby Active Directory, přečtěte si téma [vytvořte skupinu a přidejte členy v Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

Je třeba ID objektu skupiny uživatelů pro řízení zdrojů. 

```powershell
$groupID=(Get-AzureRmADGroup -DisplayName mygroup).Id
```

### <a name="get-the-role-definition-id"></a>Získání ID definice role

Dále je nutné zadat ID definice role RBAC předdefinovaná role, které chcete udělit přístup pro uživatele, skupiny uživatelů nebo aplikací. Obvykle použijete roli vlastníka nebo přispěvatele nebo čtečky. Následující příkaz ukazuje, jak získat ID definice role pro roli vlastníka:

```powershell
$ownerID=(Get-AzureRmRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>Vytvoření definice spravované aplikace

Pokud již jste skupinu prostředků pro ukládání definice spravované aplikace, vytvořte jeden:

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

Můžete nasadit spravované aplikace prostřednictvím portálu, prostředí PowerShell nebo rozhraní příkazového řádku Azure.

### <a name="powershell"></a>PowerShell

První můžeme nasadit spravované aplikace pomocí prostředí PowerShell.

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

Spravované aplikace a spravované infrastruktury nyní existovat v rámci předplatného.

### <a name="portal"></a>Portál

Teď umožňuje nasadit spravované aplikace pomocí portálu. Zobrazí uživatelské rozhraní, kterou jste vytvořili v balíčku.

1. Přejděte na portálu Azure. Vyberte **+ vytvořit prostředek** a vyhledejte **katalogu služeb**.

   ![Katalog služby vyhledávání](./media/publish-service-catalog-app/create-new.png)

1. Vyberte **katalogu služeb spravované aplikace**.

   ![Vyberte katalogu služeb](./media/publish-service-catalog-app/select-service-catalog-managed-app.png)

1. Vyberte **Vytvořit**.

   ![Vyberte možnost vytvořit](./media/publish-service-catalog-app/select-create.png)

1. Vyhledávání spravované aplikace, které chcete vytvořit ze seznamu dostupných řešení a vyberte jej. Vyberte **Vytvořit**.

   ![Najít spravované aplikace](./media/publish-service-catalog-app/find-application.png)

1. Zadejte základní informace, které jsou potřeba pro spravované aplikace. Zadejte předplatné a novou skupinu prostředků tak, aby obsahovala spravované aplikace. Vyberte **– Západ střední USA** pro umístění. Až budete hotovi, vyberte **OK**.

   ![Zadejte parametry spravované aplikace](./media/publish-service-catalog-app/add-basics.png)

1. Zadejte hodnoty, které jsou specifické pro prostředky ve spravované aplikaci. Až budete hotovi, vyberte **OK**.

   ![Zadejte parametry prostředků](./media/publish-service-catalog-app/add-storage-settings.png)

1. Šablona ověří hodnoty, které jste zadali. V případě úspěšného ověření vyberte **OK** ke spuštění nasazení.

   ![Ověření spravované aplikace](./media/publish-service-catalog-app/view-summary.png)

Po dokončení nasazení v skupinu prostředků s názvem applicationGroup existuje spravované aplikace. Existuje účet úložiště ve skupině prostředků s názvem applicationGroup plus hodnotu hash řetězce.

## <a name="next-steps"></a>Další postup

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací](overview.md).
* Například zobrazit projekty, [ukázkové projekty Azure spravované aplikace](sample-projects.md).
* Pokud chcete zjistit, jak vytvořit definiční soubor uživatelského rozhraní pro spravovanou aplikaci, přečtěte si téma [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
