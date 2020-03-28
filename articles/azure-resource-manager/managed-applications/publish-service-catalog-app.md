---
title: Publikovat spravovanou aplikaci katalogu služeb
description: Ukazuje, jak vytvořit spravovanou aplikaci Azure, která je určená pro členy vaší organizace.
author: tfitzmac
ms.topic: tutorial
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 13c45bc6e67d9d3d06a70b7cf3326cc112cd7829
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79473009"
---
# <a name="tutorial-create-and-publish-a-managed-application-definition"></a>Kurz: Vytvoření a publikování definice spravované aplikace

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Můžete vytvořit a publikovat [spravovanou aplikaci](overview.md) Azure, která je určená pro členy vaší organizace. Oddělení IT může například publikovat spravované aplikace, které vyhovují standardům organizace. Tyto spravované aplikace jsou k dispozici prostřednictvím katalogu služeb, ne prostřednictvím Azure Marketplace.

Pokud chcete publikovat spravovanou aplikaci do katalogu služeb Azure, musíte:

* Vytvořte šablonu, která definuje prostředky pro nasazení se spravovanou aplikací.
* Definujte prvky uživatelského rozhraní portálu pro nasazení spravované aplikace.
* Vytvořte balíček ZIP, který obsahuje požadované soubory šablony.
* Rozhodněte, který uživatel, skupina nebo aplikace potřebují přístup ke skupině prostředků v rámci předplatného uživatele.
* Vytvořte definici spravované aplikace, která odkazuje na balíček ZIP a požaduje přístup pro příslušnou identitu.

V tomto článku má spravovaná aplikace jenom účet úložiště. Jejím cílem je ilustrovat postup publikování spravované aplikace. Úplné příklady najdete v tématu [Ukázkové projekty pro spravované aplikace Azure](sample-projects.md).

Příklady PowerShellu v tomto článku vyžadují prostředí Azure PowerShell verze 6.2 nebo novější. V případě potřeby [aktualizujte verzi](/powershell/azure/install-Az-ps).

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

## <a name="defining-your-create-experience-using-createuidefinitionjson"></a>Definování prostředí pro vytváření pomocí souboru CreateUiDefinition.json

Jako vydavatel definujete prostředí pro vytváření pomocí souboru **createUiDefinition.json,** který generuje rozhraní pro uživatele vytvářející spravované aplikace. Můžete definovat, jak uživatelé poskytují vstup pro každý parametr pomocí [ovládacích prvků,](create-uidefinition-elements.md) včetně rozevíracích seznamů, textových polí a hesel.

Vytvoření souboru s názvem **createUiDefinition.json** (Tento název rozlišuje malá a velká písmena)

Přidejte do souboru následující počáteční JSON a uložte jej.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
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

Další informace najdete [v tématu Začínáme s createuidefinition](create-uidefinition-overview.md).

## <a name="package-the-files"></a>Zabalení souborů

Přidejte oba soubory do souboru ZIP a s názvem app.zip. Oba soubory musí být na kořenové úrovni souboru ZIP. Pokud je umístíte do složky, zobrazí se při vytváření definice spravované aplikace chyba s informacemi, že požadované soubory nejsou k dispozici. 

Nahrajte balíček do přístupného umístění, ze kterého je možné použít ho. 

```powershell
New-AzResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>Vytvoření definice spravované aplikace

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Vytvoření skupiny uživatelů nebo aplikace Azure Active Directory

Dalším krokem je výběr skupiny uživatelů nebo aplikace pro správu prostředků jménem zákazníka. Tato skupina uživatelů nebo aplikace má oprávnění pro skupinu spravovaných prostředků podle přiřazené role. Touto rolí může být kterákoli předdefinovaná role řízení přístupu na základě role (RBAC), například role vlastníka nebo přispěvatele. Oprávnění ke správě prostředků můžete udělit i jednotlivým uživatelům, obvykle se ale toto oprávnění přiřazuje skupině uživatelů. Pokud chcete vytvořit novou skupinu uživatelů služby Active Directory, přečtěte si téma [Vytvoření skupiny a přidání členů v Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Potřebujete ID objektu skupiny uživatelů, které se má používat pro správu zdrojů. 

```powershell
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

### <a name="get-the-role-definition-id"></a>Získání ID definici role

Dál potřebujete ID definice role pro předdefinovanou roli řízení přístupu na základě role (RBAC), pro které chcete uživateli, skupině uživatelů nebo aplikaci udělit přístup. Obvykle se používá role vlastníka, přispěvatele nebo čtenáře. Následující příkaz ukazuje, jak získat ID definice role pro roli vlastníka:

```powershell
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>Vytvoření definice spravované aplikace

Pokud ještě nemáte skupinu prostředků pro uložení definice spravované aplikace, vytvořte ji:

```powershell
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

Teď vytvoříte prostředek definice spravované aplikace.

```powershell
$blob = Get-AzStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

## <a name="bring-your-own-storage-for-the-managed-application-definition"></a>Přineste si vlastní úložiště pro definici spravované aplikace
Definici spravované aplikace můžete uložit do účtu úložiště, který jste poskytli během vytváření, aby ji bylo možné plně spravovat pro vaše regulační potřeby.

> [!NOTE]
> Přineste si vlastní úložiště je podporováno pouze s ARM šablony nebo REST API nasazení definice spravované aplikace.

### <a name="select-your-storage-account"></a>Vyberte účet úložiště
Je nutné [vytvořit účet úložiště,](../../storage/common/storage-account-create.md) který bude obsahovat definici spravované aplikace pro použití s katalogem služeb.

Zkopírujte ID prostředku účtu úložiště. Bude použit později při nasazení definice.

### <a name="set-the-role-assignment-for-appliance-resource-provider-in-your-storage-account"></a>Nastavení přiřazení role pro "Zprostředkovatel prostředků zařízení" v účtu úložiště
Před nasazením definice spravované aplikace do vašeho účtu úložiště musíte udělit oprávnění přispěvatele k roli **Zprostředkovatel prostředků zařízení,** aby mohl zapisovat definiční soubory do kontejneru vašeho účtu úložiště.

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na svůj účet úložiště.
1. Vyberte **řízení přístupu (IAM),** chcete-li zobrazit nastavení řízení přístupu pro účet úložiště. Výběrem karty **Přiřazení rolí** zobrazíte seznam přiřazení rolí.
1. V okně **Přidat přiřazení role** vyberte roli **přispěvatele.** 
1. V poli **Přiřadit přístup k** vyberte **uživatele, skupinu nebo instanční objekt Azure AD**.
1. V **části Vyberte** roli Hledat **zprostředkovatele prostředků zařízení** a vyberte ji.
1. Uložte přiřazení role.

### <a name="deploy-the-managed-application-definition-with-an-arm-template"></a>Nasazení definice spravované aplikace pomocí šablony ARM 

Následující šablona ARM slouží k nasazení sbalené spravované aplikace jako nové definice spravované aplikace v katalogu služeb, jejíž definiční soubory jsou uloženy a udržovány ve vašem vlastním účtu úložiště:
   
```json
    {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "applicationName": {
            "type": "string",
            "metadata": {
                "description": "Managed Application name"
            }
        },
        "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
        "definitionStorageResourceID": {
            "type": "string",
            "metadata": {
                "description": "Storage account resource ID for where you're storing your definition"
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located."
            }
        }
    },
    "variables": {
        "lockLevel": "None",
        "description": "Sample Managed application definition",
        "displayName": "Sample Managed application definition",
        "managedApplicationDefinitionName": "[parameters('applicationName')]",
        "packageFileUri": "[parameters('_artifactsLocation')]",
        "defLocation": "[parameters('definitionStorageResourceID')]",
        "managedResourceGroupId": "[concat(subscription().id,'/resourceGroups/', concat(parameters('applicationName'),'_managed'))]",
        "applicationDefinitionResourceId": "[resourceId('Microsoft.Solutions/applicationDefinitions',variables('managedApplicationDefinitionName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Solutions/applicationDefinitions",
            "apiVersion": "2019-07-01",
            "name": "[variables('managedApplicationDefinitionName')]",
            "location": "[parameters('location')]",
            "properties": {
                "lockLevel": "[variables('lockLevel')]",
                "description": "[variables('description')]",
                "displayName": "[variables('displayName')]",
                "packageFileUri": "[variables('packageFileUri')]",
                "storageAccountId": "[variables('defLocation')]"
            }
        }
    ],
    "outputs": {}
}
```

Přidali jsme novou vlastnost s názvem **storageAccountId** do vlastností vaší aplikaceDefintion a poskytnout id účtu úložiště, které chcete uložit definici v jako jeho hodnotu:

Můžete ověřit, že soubory definice aplikace jsou uloženy v zadaný účet úložiště v kontejneru s názvem **applicationdefinitions**.

> [!NOTE]
> Pro zvýšení zabezpečení můžete vytvořit definici spravovaných aplikací, uložte ji do [objektu blob účtu úložiště Azure, kde je povoleno šifrování](../../storage/common/storage-service-encryption.md). Obsah definice je šifrován prostřednictvím možností šifrování účtu úložiště. Definici mohou v katalogu služeb zobrazit pouze uživatelé s oprávněními k souboru.

### <a name="make-sure-users-can-see-your-definition"></a>Je potřeba zajistit, že budou uživatelé vidět vaši definici.

Máte přístup k definici spravované aplikace, ale je potřeba zajistit přístup i pro ostatní uživatele ve vaší organizaci. Udělte jim k definici alespoň přístup role Čtenář. Je možné, že tuto úroveň přístupu zdědili z předplatného nebo skupiny prostředků. Informace o tom, kdo má přístup k definici, a o přidání uživatelů nebo skupin najdete v tématu [Použití řízení přístupu na základě rolí ke správě přístupu k prostředkům předplatného Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Další kroky

* Informace o publikování spravované aplikace na webu Azure Marketplace najdete v tématu [Spravované aplikace Azure v Marketplace](publish-marketplace-app.md).
* Informace o nasazení instance spravované aplikace najdete v tématu [Nasazení aplikace z katalogu služeb prostřednictvím webu Azure Portal](deploy-service-catalog-quickstart.md).
