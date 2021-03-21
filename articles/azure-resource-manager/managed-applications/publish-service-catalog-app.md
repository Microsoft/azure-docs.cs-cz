---
title: Publikování spravované aplikace katalogu služeb
description: Ukazuje, jak vytvořit spravovanou aplikaci Azure, která je určená pro členy vaší organizace.
author: tfitzmac
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.date: 04/14/2020
ms.author: tomfitz
ms.openlocfilehash: b255cafb9040f87c902fe6c094c3e0db3d461e4b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101093642"
---
# <a name="quickstart-create-and-publish-a-managed-application-definition"></a>Rychlé zprovoznění: Vytvoření a publikování definice spravované aplikace

V tomto rychlém startu najdete Úvod k práci s [Azure Managed Applications](overview.md). Můžete vytvořit a publikovat spravovanou aplikaci, která je určena pro členy vaší organizace.

Pokud chcete publikovat spravovanou aplikaci do katalogu služeb, musíte:

* Vytvořte šablonu, která definuje prostředky pro nasazení se spravovanou aplikací.
* Definujte prvky uživatelského rozhraní portálu pro nasazení spravované aplikace.
* Vytvořte balíček _zip_ , který obsahuje požadované soubory šablon.
* Rozhodněte, který uživatel, skupina nebo aplikace potřebují přístup ke skupině prostředků v rámci předplatného uživatele.
* Vytvořte definici spravované aplikace, která odkazuje na balíček _zip_ a požádá o přístup k identitě.

## <a name="create-the-arm-template"></a>Vytvoření šablony ARM

Každá definice spravované aplikace obsahuje soubor s názvem _mainTemplate.json_. V něm se definují prostředky Azure, které se mají nasadit. Šablona se neliší od běžné šablony ARM.

Vytvořte soubor s názvem _mainTemplate.json_. V názvu se rozlišují velká a malá písmena.

Přidejte do souboru následující kód JSON. Definuje parametry pro vytvoření účtu úložiště a určuje vlastnosti pro účet úložiště.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageEndpoint": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
    }
  }
}
```

Uložte _mainTemplate.jsdo_ souboru.

## <a name="define-your-create-experience"></a>Definice prostředí pro vytváření

Jako vydavatel definujete prostředí portálu pro vytváření spravovaných aplikací. _createUiDefinition.jsv_ souboru vygeneruje rozhraní portálu. Definujete, jak uživatelé poskytují vstup pro každý parametr pomocí [ovládacích prvků](create-uidefinition-elements.md) , včetně rozevíracích seznamů, textových polí a polí pro hesla.

Vytvořit soubor s názvem _createUiDefinition.jsv_ (u tohoto názvu se rozlišují velká a malá písmena)

Do souboru přidejte následující spouštěcí kód JSON a uložte ho.

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

Další informace najdete v tématu Začínáme [s CreateUiDefinition](create-uidefinition-overview.md).

## <a name="package-the-files"></a>Zabalení souborů

Přidejte tyto dva soubory do souboru _. zip_ s názvem _app.zip_. Tyto dva soubory musí být na kořenové úrovni souboru _. zip_ . Pokud je umístíte do složky, zobrazí se při vytváření definice spravované aplikace chyba s informacemi, že požadované soubory nejsou k dispozici.

Nahrajte balíček do přístupného umístění, ze kterého je možné použít ho. Budete muset zadat jedinečný název pro účet úložiště.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name storageGroup -Location eastus

$storageAccount = New-AzStorageAccount `
  -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent `
  -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name storageGroup --location eastus

az storage account create \
    --name mystorageaccount \
    --resource-group storageGroup \
    --location eastus \
    --sku Standard_LRS \
    --kind StorageV2

az storage container create \
    --account-name mystorageaccount \
    --name appcontainer \
    --public-access blob

az storage blob upload \
    --account-name mystorageaccount \
    --container-name appcontainer \
    --name "app.zip" \
    --file "D:\myapplications\app.zip"

```

---

## <a name="create-the-managed-application-definition"></a>Vytvoření definice spravované aplikace

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Vytvoření skupiny uživatelů nebo aplikace Azure Active Directory

Dalším krokem je výběr skupiny uživatelů, uživatele nebo aplikace pro správu prostředků pro zákazníka. Tato identita má oprávnění pro spravovanou skupinu prostředků podle přiřazené role. Role může být libovolná předdefinovaná role Azure, jako je vlastník nebo Přispěvatel. Pokud chcete vytvořit novou skupinu uživatelů služby Active Directory, přečtěte si téma [Vytvoření skupiny a přidání členů v Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Potřebujete ID objektu skupiny uživatelů, které se má používat pro správu zdrojů.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
groupid=$(az ad group show --group mygroup --query objectId --output tsv)
```

---

### <a name="get-the-role-definition-id"></a>Získání ID definici role

Dále potřebujete ID definice role předdefinované role Azure, které chcete udělit přístup uživateli, skupině uživatelů nebo aplikaci. Obvykle se používá role vlastníka, přispěvatele nebo čtenáře. Následující příkaz ukazuje, jak získat ID definice role pro roli vlastníka:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
ownerid=$(az role definition list --name Owner --query [].name --output tsv)
```

---

### <a name="create-the-managed-application-definition"></a>Vytvoření definice spravované aplikace

Pokud ještě nemáte skupinu prostředků pro uložení definice spravované aplikace, vytvořte ji:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

---

Teď vytvoříte prostředek definice spravované aplikace.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
blob=$(az storage blob url --account-name mystorageaccount --container-name appcontainer --name app.zip --output tsv)

az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$groupid:$ownerid" \
  --package-file-uri "$blob"
```

---

Po dokončení příkazu máte definici spravované aplikace ve vaší skupině prostředků.

Některé z parametrů použitých v předchozím příkladu:

* **Skupina prostředků**: název skupiny prostředků, ve které se vytvoří definice spravované aplikace.
* **Úroveň zámku**: typ zámku umístěný do spravované skupiny prostředků. Zabraňuje zákazníkovi v provádění nežádoucích operací s touto skupinou prostředků. Jedinou podporovanou úrovní zámku momentálně je ReadOnly. Prostředky, které jsou ve spravované skupině prostředků dostupné, může při zadání úrovně ReadOnly zákazník jenom číst. Zámek se nevztahuje na identity vydavatelů s uděleným přístupem ke spravované skupině prostředků.
* **authorizations:** Popisuje ID objektu zabezpečení a ID definice role, které slouží k udělení oprávnění pro spravovanou skupinu prostředků. tento parametr je zadaný ve formátu `<principalId>:<roleDefinitionId>`. Pokud je potřeba zadat více hodnot, zadejte je ve formátu `<principalId1>:<roleDefinitionId1>,<principalId2>:<roleDefinitionId2>`. Hodnoty jsou oddělené čárkou.
* **identifikátor URI souboru balíčku**: umístění balíčku _. zip_ , který obsahuje požadované soubory.

## <a name="bring-your-own-storage-for-the-managed-application-definition"></a>Přineste si vlastní úložiště pro definici spravované aplikace

Definici spravované aplikace si můžete uložit v rámci účtu úložiště, který jste zadali během vytváření, aby bylo možné její umístění a přístup plně spravovat podle vašich zákonných potřeb.

> [!NOTE]
> Využití vlastního úložiště je podporované jenom šablonou ARM nebo nasazením REST API definice spravované aplikace.

### <a name="select-your-storage-account"></a>Vyberte svůj účet úložiště.

Musíte [vytvořit účet úložiště](../../storage/common/storage-account-create.md) , který bude obsahovat definici spravované aplikace pro použití s katalogem služeb.

Zkopírujte ID prostředku účtu úložiště. Použije se později při nasazování definice.

### <a name="set-the-role-assignment-for-appliance-resource-provider-in-your-storage-account"></a>Nastavte přiřazení role pro "poskytovatele prostředků zařízení" v účtu úložiště.

Předtím, než bude možné nasadit definici spravované aplikace do svého účtu úložiště, musíte udělit oprávnění přispěvatele k roli **poskytovatele prostředků zařízení** , aby mohla zapisovat definiční soubory do kontejneru účtu úložiště.

1. Na webu [Azure Portal](https://portal.azure.com) přejděte ke svému účtu úložiště.
1. Vyberte **řízení přístupu (IAM)** a zobrazte nastavení řízení přístupu pro účet úložiště. Vyberte kartu **přiřazení rolí** a zobrazte seznam přiřazení rolí.
1. V okně **Přidat přiřazení role** vyberte roli **Přispěvatel** .
1. V poli **přiřadit přístup k** vyberte možnost **uživatel, skupina nebo instanční objekt služby Azure AD**.
1. V části **Vybrat** vyhledejte roli **poskytovatele prostředků zařízení** a vyberte ji.
1. Uložte přiřazení role.

### <a name="deploy-the-managed-application-definition-with-an-arm-template"></a>Nasazení definice spravované aplikace pomocí šablony ARM

Pomocí následující šablony ARM nasaďte zabalené spravované aplikace jako novou definici spravované aplikace v katalogu služeb, jejíž definiční soubory se ukládají a udržují ve vašem vlastním účtu úložiště:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "apiVersion": "2020-08-21-preview",
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

Přidali jsme novou vlastnost s názvem `storageAccountId` do vašich `applicationDefinitions` vlastností a zadáte ID účtu úložiště, do kterého chcete definici uložit, jako její hodnotu:

Můžete ověřit, zda jsou soubory definic aplikace uloženy v zadaném účtu úložiště v kontejneru s názvem `applicationDefinitions` .

> [!NOTE]
> Pro zvýšení zabezpečení můžete vytvořit definici spravovaných aplikací v [objektu BLOB účtu úložiště Azure, kde je šifrování povolené](../../storage/common/storage-service-encryption.md). Obsah definice se šifruje prostřednictvím možností šifrování účtu úložiště. Jenom uživatelé s oprávněními k tomuto souboru uvidí definici v katalogu služeb.

## <a name="make-sure-users-can-see-your-definition"></a>Je potřeba zajistit, že budou uživatelé vidět vaši definici.

Máte přístup k definici spravované aplikace, ale je potřeba zajistit přístup i pro ostatní uživatele ve vaší organizaci. Udělte jim k definici alespoň přístup role Čtenář. Je možné, že tuto úroveň přístupu zdědili z předplatného nebo skupiny prostředků. Pokud chcete zjistit, kdo má přístup k definici a přidat uživatele nebo skupiny, přečtěte si téma [přiřazení rolí Azure pomocí Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Další kroky

Publikovali jste definici spravované aplikace. Teď se dozvíte, jak nasadit instanci této definice.

> [!div class="nextstepaction"]
> [Rychlý start: Nasazení aplikace katalogu služeb](deploy-service-catalog-quickstart.md)
