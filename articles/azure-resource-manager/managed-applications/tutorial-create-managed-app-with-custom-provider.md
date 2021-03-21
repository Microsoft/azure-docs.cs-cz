---
title: Kurz – vlastní akce & prostředky
description: V tomto kurzu se dozvíte, jak vytvořit spravovanou aplikaci Azure pomocí vlastního poskytovatele Azure.
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 50a65583c41dd94b174a33432afcf42b31e67df0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96437044"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>Kurz: vytvoření spravované aplikace s vlastními akcemi a prostředky

V tomto kurzu vytvoříte vlastní spravovanou aplikaci s vlastními akcemi a prostředky. Spravovaná aplikace bude na stránce obsahovat vlastní akci `Overview` , vlastní typ prostředku zobrazený jako samostatná položka nabídky v aplikaci `Table of Content` a vlastní kontextovou akci na stránce vlastní prostředek.

Tento kurz obsahuje následující kroky:

> [!div class="checklist"]
> * Vytvořit soubor definice uživatelského rozhraní pro vytvoření instance spravované aplikace
> * Vytvořit šablonu nasazení pomocí [vlastního zprostředkovatele Azure](../custom-providers/overview.md), účtu Azure Storage a funkce Azure Functions
> * Vytvořit artefakt definice zobrazení s vlastními akcemi a prostředky
> * Nasadit definici spravované aplikace
> * Nasazení instance spravované aplikace
> * Provádění vlastních akcí a vytváření vlastních prostředků

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete znát tyto informace:

* Jak [vytvořit a publikovat definici spravované aplikace](publish-service-catalog-app.md)
* Postup [nasazení aplikace Service Catalog prostřednictvím Azure Portal](deploy-service-catalog-quickstart.md).
* [Vytvoření Azure Portal uživatelského rozhraní pro spravovanou aplikaci](create-uidefinition-overview.md).
* Umožňuje [Zobrazit možnosti artefaktů definice](concepts-view-definition.md) .
* Možnosti [vlastního zprostředkovatele Azure](../custom-providers/overview.md) .

## <a name="user-interface-definition"></a>Definice uživatelského rozhraní

V tomto kurzu vytvoříte spravovanou aplikaci a její spravovanou skupinu prostředků, která bude obsahovat vlastní instanci poskytovatele, účet úložiště a funkci. Funkce Azure použitá v tomto příkladu implementuje rozhraní API, které zpracovává vlastní operace poskytovatele pro akce a prostředky. Účet Azure Storage se používá jako základní úložiště pro prostředky vlastního zprostředkovatele.

Definice uživatelského rozhraní pro vytvoření instance spravované aplikace zahrnuje `funcname` a `storagename` vstupní prvky. Název a název funkce účtu úložiště musí být globálně jedinečné. Ve výchozím nastavení se soubory funkcí nasazují z [balíčku ukázkové funkce](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip), ale můžete ho změnit přidáním elementu Input pro odkaz na balíček v *createUiDefinition.js*:

```json
{
  "name": "funcname",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the function to be created",
  "toolTip": "Name of the function to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "storagename",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the storage to be created",
  "toolTip": "Name of the storage to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "zipFileBlobUri",
  "type": "Microsoft.Common.TextBox",
  "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
  "label": "The Uri to the uploaded function zip file",
  "toolTip": "The Uri to the uploaded function zip file",
  "visible": true
}
```

a výstup v *createUiDefinition.js*:

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

Kompletní *createUiDefinition.jspro* ukázku lze nalézt v [referenci: artefakty prvků uživatelského rozhraní](reference-createuidefinition-artifact.md).

## <a name="template-with-custom-provider"></a>Šablona s vlastním zprostředkovatelem

Chcete-li vytvořit instanci spravované aplikace s vlastním poskytovatelem, je třeba definovat prostředek vlastního zprostředkovatele s názvem **Public** a do svého **mainTemplate.js** zadat **Microsoft. CustomProviders/resourceProviders** . V tomto prostředku definujete typy prostředků a akce pro vaši službu. Pro nasazení funkce Azure functions a Azure Storage instance účtů definují prostředky typu `Microsoft.Web/sites` a `Microsoft.Storage/storageAccounts` v uvedeném pořadí.

V tomto kurzu vytvoříte jeden `users` typ prostředku, `ping` vlastní akci a `users/contextAction` vlastní akci, která se provede v kontextu `users` vlastního prostředku. Pro každý typ prostředku a akci zadejte koncový bod, který odkazuje na funkci s názvem, který je zadaný v [createUiDefinition.js](#user-interface-definition). Zadejte **routingType** jako `Proxy,Cache` pro typy prostředků a `Proxy` pro akce:

```json
{
  "apiVersion": "[variables('customrpApiversion')]",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[variables('customProviderName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      },
      {
        "name": "users/contextAction",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ]
  },
  "dependsOn": [
    "[concat('Microsoft.Web/sites/',parameters('funcname'))]"
  ]
}
```

Kompletní *mainTemplate.jspro* ukázku najdete v části [Reference: artefakt šablony nasazení](reference-main-template-artifact.md).

## <a name="view-definition-artifact"></a>Zobrazení definičních artefaktů

Chcete-li definovat uživatelské rozhraní, které zahrnuje vlastní akce a vlastní prostředky ve spravované aplikaci, je třeba vytvořit **viewDefinition.js** artefaktem. Další informace o artefaktu definice zobrazení naleznete [v tématu artefakt definice zobrazení v Azure Managed Applications](concepts-view-definition.md).

V tomto kurzu nadefinujete:
* Stránka s *přehledem* s tlačítkem panelu nástrojů, která představuje vlastní akci `TestAction` se základním zadáním textu.
* Stránka *uživatele* , která představuje vlastní typ prostředku `users` .
* Vlastní akce prostředku `users/contextAction` na stránce *Uživatelé* , která bude provedena v kontextu vlastního prostředku typu `users` .

Následující příklad ukazuje zobrazení konfigurace pro stránku Přehled:

```json
{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  }
```

Níže uvedený příklad obsahuje "uživatelé" – konfigurace stránky prostředky s vlastní akcí prostředku:

```json
{
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }
```

Kompletní *viewDefinition.jspro* ukázku najdete v části [Reference: artefakt definice zobrazení](reference-view-definition-artifact.md).

## <a name="managed-application-definition"></a>Definice spravované aplikace

Zabalit následující artefakty spravovaných aplikací do archivu zip a nahrát je do úložiště:

* createUiDefinition.jsna
* mainTemplate.jsna
* viewDefinition.jsna

Všechny soubory musí být na kořenové úrovni. Balíček s artefakty může být uložený v jakémkoli úložišti, například v objektu BLOB GitHubu nebo v objektu BLOB účtu Azure Storage. Tady je skript pro nahrání balíčku aplikace do účtu úložiště: 

```powershell
$resourceGroup="appResourcesGroup"
$storageName="mystorageaccount$RANDOM"

# Sign in to your Azure subscription
Connect-AzAccount
# Create resource group for managed application definition and application package
New-AzResourceGroup -Name $resourceGroup -Location eastus

# Create storage account for a package with application artifacts
$storageAccount=New-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -SkuName Standard_LRS `
  -Location eastus `
$ctx=$storageAccount.Context

# Create storage container and upload zip to blob
New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob
Set-AzStorageBlobContent `
  -File "path_to_your_zip_package" `
  -Container appcontainer `
  -Blob app.zip `
  -Context $ctx 

# Get blob absolute uri
$blobUri=(Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx).ICloudBlob.uri.AbsoluteUri
```

Spusťte níže uvedený skript Azure CLI nebo podle pokynů v části Azure Portal nasaďte definici spravované aplikace katalogu služeb:

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-interactive)

```azurecli-interactive
resourceGroup="appResourcesGroup"
# Select subscription and create resource group (if you have not created yet)
az account set --subscription <subscriptionID>
az group create --name $resourceGroup --location eastus

# Get object ID of your identity
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
# Get role definition ID for the Owner role
roleid=$(az role definition list --name Owner --query [].name --output tsv)

# Create managed application definition resource
az managedapp definition create \
  --name "ManagedUsersAppDefinition" \
  --location "eastus" \
  --resource-group $resourceGroup \
  --lock-level ReadOnly \
  --display-name "Managed users app definition" \
  --description "Managed application with Azure Custom Provider" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "path to your app.zip package"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V Azure Portal vyberte **všechny služby**. V seznamu prostředků zadejte a vyberte **spravované aplikace Centrum**.
2. V **centru spravovaných aplikací** vyberte **definice aplikace katalogu služeb** a klikněte na **Přidat**. 
    
    ![Přidat katalog služeb](./media/tutorial-create-managed-app-with-custom-provider/service-catalog-managed-application.png)

3. Zadejte hodnoty pro vytvoření definice katalogu služeb:

    * Zadejte jedinečný **název** pro definici katalogu služeb, **Zobrazovaný název** a *Popis*(volitelné).
    * Vyberte **předplatné**, **skupinu prostředků** a **umístění** , kde se vytvoří definice aplikace. Můžete použít stejnou skupinu prostředků, která se používá pro balíček zip, nebo vytvořit novou skupinu prostředků.
    * V případě **identifikátoru URI souboru balíčku** zadejte cestu k souboru zip, který jste vytvořili v předchozím kroku.

    ![Zadat hodnoty](./media/tutorial-create-managed-app-with-custom-provider/add-service-catalog-managed-application.png)

4. Až se dostanete do části ověřování a úroveň zámku, vyberte **Přidat autorizaci**.

    ![Přidat autorizaci](./media/tutorial-create-managed-app-with-custom-provider/add-authorization.png)

5. Vyberte skupinu Azure Active Directory pro správu prostředků a vyberte **OK**.

   ![Přidat skupinu autorizací](./media/tutorial-create-managed-app-with-custom-provider/add-auth-group.png)

6. Pokud jste zadali všechny hodnoty, vyberte **vytvořit**.

   ![Vytvořit definici spravované aplikace](./media/tutorial-create-managed-app-with-custom-provider/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>Instance spravované aplikace

Pokud je nasazená definice spravované aplikace, spusťte skript níže nebo postupujte podle pokynů v části Azure Portal a nasaďte instanci spravované aplikace pomocí vlastního zprostředkovatele:

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-interactive)

```azurecli-interactive
appResourcesGroup="appResourcesGroup"
applicationGroup="usersApplicationGroup"

# Create resource group for managed application instance
az group create --name $applicationGroup --location eastus

# Get ID of managed application definition
appid=$(az managedapp definition show --name ManagedUsersAppDefinition --resource-group $appResourcesGroup --query id --output tsv)

# Create the managed application
az managedapp create \
  --name ManagedUsersApp \
  --location "eastus" \
  --kind "Servicecatalog" \
  --resource-group $applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id "managedResourcesGroup" \
  --parameters "{\"funcname\": {\"value\": \"managedusersappfunction\"}, \"storageName\": {\"value\": \"managedusersappstorage\"}}"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V Azure Portal vyberte **všechny služby**. V seznamu prostředků zadejte a vyberte **spravované aplikace Centrum**.
2. V **centru spravovaných aplikací** vyberte **aplikace katalogu služeb** a klikněte na **Přidat**. 

    ![Přidat spravovanou aplikaci](./media/tutorial-create-managed-app-with-custom-provider/add-managed-application.png)

3. Na stránce **aplikace katalogu služeb** zadejte do vyhledávacího pole název zobrazení definice katalogu služby. Vyberte definici vytvořenou v předchozím kroku a klikněte na **vytvořit**.

    ![Výběr katalogu služeb](./media/tutorial-create-managed-app-with-custom-provider/select-service-catalog-definition.png)

4. Zadejte hodnoty pro vytvoření instance spravované aplikace z definice katalogu služeb:

    * Vyberte **předplatné**, **skupinu prostředků** a **umístění** , kde se vytvoří instance aplikace.
    * Zadejte jedinečný název funkce Azure a název Azure Storage účtu.

    ![Nastavení aplikace](./media/tutorial-create-managed-app-with-custom-provider/application-settings.png)

5. Po úspěšném ověření kliknutím na tlačítko **OK** nasaďte instanci spravované aplikace. 
    
    ![Nasazení spravované aplikace](./media/tutorial-create-managed-app-with-custom-provider/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>Vlastní akce a prostředky

Po nasazení instance aplikace katalogu služeb máte dvě nové skupiny prostředků. První skupina prostředků `applicationGroup` obsahuje instanci spravované aplikace, druhá skupina prostředků obsahuje `managedResourceGroup` prostředky pro spravovanou aplikaci, včetně **vlastního poskytovatele**.

![Skupiny prostředků aplikace](./media/tutorial-create-managed-app-with-custom-provider/application-resource-groups.png)

Můžete přejít na instanci spravované aplikace a provést **vlastní akci** na stránce Přehled, vytvořit vlastní prostředek **uživatele** na stránce Uživatelé a spustit **vlastní kontextovou akci** u vlastního prostředku.

* Přejděte na stránku Přehled a klikněte na tlačítko Akce příkazového testu:

![Provést vlastní akci](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-action.png)

* Přejděte na stránku Uživatelé a klikněte na tlačítko Přidat. Poskytněte vstupy pro vytvoření prostředku a formulář odešlete:

![Snímek obrazovky se zobrazí tlačítko Přidat vybrané uživatelům.](./media/tutorial-create-managed-app-with-custom-provider/create-custom-resource.png)

* Přejděte na stránku Uživatelé, vyberte prostředek "uživatelé" a klikněte na vlastní akce kontextu. ":

![Snímek obrazovky zobrazuje vybranou vlastní kontextovou akci.](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>Hledáte nápovědu

Pokud máte dotazy týkající se Azure Managed Applications, zkuste požádat o [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps). Podobná otázka již mohla být požádána o zodpovězení a byla zodpovězena, proto nejprve před odesláním zaregistrujte. Přidejte značku `azure-managedapps` , abyste získali rychlou odpověď.

## <a name="next-steps"></a>Další kroky

Informace o publikování spravované aplikace na webu Azure Marketplace najdete v tématu [Spravované aplikace Azure v Marketplace](../../marketplace/create-new-azure-apps-offer.md).

Přečtěte si další informace o [vlastních poskytovatelích Azure](../custom-providers/overview.md).
