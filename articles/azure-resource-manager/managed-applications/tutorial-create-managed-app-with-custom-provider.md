---
title: Výuka – vlastní akce & zdroje
description: Tento kurz popisuje, jak vytvořit spravovanou aplikaci Azure s vlastním zprostředkovatelem Azure.
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.openlocfilehash: c3750da6bd76c8cb3908fbdc71ba676f09d77def
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75650076"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>Kurz: Vytvoření spravované aplikace s vlastními akcemi a prostředky

V tomto kurzu vytvoříte vlastní spravovanou aplikaci s vlastními akcemi a prostředky. Spravovaná aplikace bude obsahovat vlastní akci na `Overview` stránce, vlastní typ `Table of Content` prostředku zobrazený jako samostatná položka nabídky a vlastní kontextovou akci na stránce vlastního prostředku.

Tento kurz obsahuje následující kroky:

> [!div class="checklist"]
> * Soubor definice uživatelského rozhraní autora pro vytvoření instance spravované aplikace
> * Šablona nasazení autora s [vlastním poskytovatelem Azure](../custom-providers/overview.md), účtem úložiště Azure a funkcí Azure
> * Artefakt definice zobrazení zobrazení autora s vlastními akcemi a prostředky
> * Nasazení definice spravované aplikace
> * Nasazení instance spravované aplikace
> * Provádění vlastních akcí a vytváření vlastních zdrojů

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento výukový program, musíte vědět:

* Jak [vytvořit a publikovat definici spravované aplikace](publish-service-catalog-app.md).
* Jak [nasadit aplikaci Katalog služeb prostřednictvím portálu Azure](deploy-service-catalog-quickstart.md).
* Jak [vytvořit uživatelské rozhraní portálu Azure pro spravovanou aplikaci](create-uidefinition-overview.md).
* Zobrazit možnosti [artefaktů definice.](concepts-view-definition.md)
* [Možnosti vlastního zprostředkovatele Azure.](../custom-providers/overview.md)

## <a name="user-interface-definition"></a>Definice uživatelského rozhraní

V tomto kurzu vytvoříte spravovanou aplikaci a její skupina spravovaných prostředků bude obsahovat vlastní instanci zprostředkovatele, účet úložiště a funkci. Funkce Azure použitá v tomto příkladu implementuje rozhraní API, které zpracovává operace vlastního zprostředkovatele pro akce a prostředky. Účet úložiště Azure se používá jako základní úložiště pro vaše vlastní prostředky poskytovatele.

Definice uživatelského rozhraní pro vytvoření `funcname` instance `storagename` spravované aplikace obsahuje a vstupní prvky. Název účtu úložiště a název funkce musí být globálně jedinečný. Ve výchozím nastavení budou soubory funkcí nasazeny z [balíčku ukázkových funkcí](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip), ale můžete je změnit přidáním vstupního prvku pro propojení balíčku v *souboru createUIDefinition.json*:

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

a výstup v *createUIDefinition.json*:

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

Kompletní *createUIDefinition.json* ukázku lze nalézt na [odkaz: Artefakty prvků uživatelského rozhraní](reference-createuidefinition-artifact.md).

## <a name="template-with-custom-provider"></a>Šablona s vlastním poskytovatelem

Chcete-li vytvořit instanci spravované aplikace s vlastním zprostředkovatelem, musíte definovat vlastní prostředek zprostředkovatele s názvem **public** a zadat **Microsoft.CustomProviders/resourceProviders** ve vašem **mainTemplate.json**. V tomto prostředku definujete typy prostředků a akce pro vaši službu. Chcete-li nasadit instance Azure Function a `Microsoft.Web/sites` `Microsoft.Storage/storageAccounts` Azure Storage Account, definujte prostředky typu a příslušně.

V tomto kurzu vytvoříte `users` jeden `ping` typ prostředku, `users/contextAction` vlastní akci a vlastní akci, `users` která bude provedena v kontextu vlastního prostředku. Pro každý typ prostředku a akce zadejte koncový bod ukazující na funkci s názvem v [createUIDefinition.json](#user-interface-definition). Zadejte **směr** `Proxy,Cache` Typ jako `Proxy` pro typy prostředků a pro akce:

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

Kompletní *ukázku mainTemplate.json* naleznete na [adrese Reference: Deployment template artifact](reference-main-template-artifact.md).

## <a name="view-definition-artifact"></a>Zobrazení definičních artefaktů

Chcete-li definovat uživatelské rozhraní, které obsahuje vlastní akce a vlastní prostředky ve spravované aplikaci, je třeba vytvořit **artefakt viewDefinition.json.** Další informace o artefaktu definice zobrazení najdete [v tématu Zobrazení artefaktu definice ve spravovaných aplikacích Azure](concepts-view-definition.md).

V tomto kurzu definujete:
* Stránka *Přehled* s tlačítkem panelu `TestAction` nástrojů, která představuje vlastní akci se základním zadáváním textu.
* Stránka *Uživatelé,* která představuje `users`vlastní typ prostředku .
* Vlastní akce `users/contextAction` prostředku na stránce *Uživatelé,* která bude provedena `users`v kontextu vlastního prostředku typu .

Následující příklad ukazuje konfiguraci zobrazení pro stránku "Přehled":

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

Níže uvedený příklad zahrnuje konfiguraci stránky prostředků "Uživatelé" s vlastní akcí prostředků:

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

Kompletní ukázku *viewDefinition.json* naleznete na [adrese Reference: View definition artifact](reference-view-definition-artifact.md).

## <a name="managed-application-definition"></a>Definice spravované aplikace

Zabalte následující artefakty spravované aplikace do ziparchivu a nahrajte je do úložiště:

* createUiDefinition.json
* mainTemplate.json
* viewDefinition.json

Všechny soubory musí být na kořenové úrovni. Balíček s artefakty lze uložit do libovolného úložiště, například objekt blob GitHub nebo účet úložiště Azure objektu blob. Zde je skript pro nahrání balíčku aplikace do účtu úložiště: 

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

Spusťte skript Azure CLI níže nebo postupujte podle pokynů na webu Azure Portal a nasaďte definici spravované aplikace katalogu služeb:

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

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Na webu Azure Portal vyberte **Všechny služby**. V seznamu prostředků zadejte a vyberte **Centrum spravovaných aplikací**.
2. V **Centru spravovaných aplikací**zvolte **definici aplikace Katalog ustavoven služeb** a klepněte na tlačítko **Přidat**. 
    
    ![Přidat katalog služeb](./media/tutorial-create-managed-app-with-custom-provider/service-catalog-managed-application.png)

3. Zadejte hodnoty pro vytvoření definice katalogu služeb:

    * Zadejte jedinečný **název** definice katalogu služeb, **zobrazované jméno** a *popis*(volitelné).
    * Vyberte **předplatné**, **skupinu prostředků**a **umístění,** kde bude vytvořena definice aplikace. Můžete použít stejnou skupinu prostředků, která se používá pro balíček zip nebo vytvořit novou skupinu prostředků.
    * U **identifikátoru Uri souboru balíčku**zadejte cestu k souboru ZIP, který jste vytvořili v předchozím kroku.

    ![Zadat hodnoty](./media/tutorial-create-managed-app-with-custom-provider/add-service-catalog-managed-application.png)

4. Až se dostanete do části Úroveň ověřování a uzamčení, vyberte **Přidat autorizaci**.

    ![Přidání autorizace](./media/tutorial-create-managed-app-with-custom-provider/add-authorization.png)

5. Vyberte skupinu Azure Active Directory pro správu prostředků a vyberte **OK**.

   ![Přidat skupinu autorizací](./media/tutorial-create-managed-app-with-custom-provider/add-auth-group.png)

6. Po zadání všech hodnot vyberte **příkaz Vytvořit**.

   ![Vytvoření definice spravované aplikace](./media/tutorial-create-managed-app-with-custom-provider/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>Instance spravované aplikace

Když se nasadí definice spravované aplikace, spusťte níže uvedený skript nebo postupujte podle pokynů na webu Azure Portal a nasaďte instanci spravované aplikace s vlastním poskytovatelem:

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

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Na webu Azure Portal vyberte **Všechny služby**. V seznamu prostředků zadejte a vyberte **Centrum spravovaných aplikací**.
2. V **Centru spravovaných aplikací**zvolte **Aplikace katalogu služeb** a klepněte na tlačítko **Přidat**. 

    ![Přidání spravované aplikace](./media/tutorial-create-managed-app-with-custom-provider/add-managed-application.png)

3. Na stránce **Aplikace katalogu služeb** zadejte název definice definice katalogu služeb ve vyhledávacím poli. Vyberte definici vytvořenou v předchozím kroku a klepněte na **vytvořit**.

    ![Výběr katalogu služeb](./media/tutorial-create-managed-app-with-custom-provider/select-service-catalog-definition.png)

4. Zadejte hodnoty pro vytvoření instance spravované aplikace z definice katalogu služeb:

    * Vyberte **předplatné**, **skupinu prostředků**a **umístění,** kde bude vytvořena instance aplikace.
    * Zadejte jedinečný název funkce Azure a název účtu úložiště Azure.

    ![Nastavení aplikace](./media/tutorial-create-managed-app-with-custom-provider/application-settings.png)

5. Po schválení ověření klikněte na **TLAČÍTKO OK** a nasadit instanci spravované aplikace. 
    
    ![Nasazení spravované aplikace](./media/tutorial-create-managed-app-with-custom-provider/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>Vlastní akce a zdroje

Po nasazení instance aplikace katalogu služeb máte dvě nové skupiny prostředků. První skupina `applicationGroup` prostředků obsahuje instanci spravované `managedResourceGroup` aplikace, druhá skupina prostředků obsahuje prostředky pro spravovanou aplikaci, včetně **vlastního zprostředkovatele**.

![Skupiny prostředků aplikace](./media/tutorial-create-managed-app-with-custom-provider/application-resource-groups.png)

Můžete přejít na spravovanou instanci aplikace a provést **vlastní akci** na stránce "Přehled", vytvořit **vlastní** prostředek uživatelů na stránce "Uživatelé" a spustit **vlastní kontextovou akci** s vlastním prostředkem.

* Přejděte na stránku "Přehled" a klikněte na tlačítko "Ping Action":

![Provést vlastní akci](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-action.png)

* Přejděte na stránku "Uživatelé" a klikněte na tlačítko Přidat. Zadejte vstupy pro vytvoření zdroje a odešlete formulář:

![Vytvořit vlastní zdroj](./media/tutorial-create-managed-app-with-custom-provider/create-custom-resource.png)

* Přejděte na stránku Uživatelé, vyberte prostředek "uživatelé" a klikněte na "Vlastní kontextová akce":

![Vytvořit vlastní zdroj](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>Hledání pomoci

Pokud máte dotazy týkající se spravovaných aplikací Azure, zkuste se zeptat na [přetečení zásobníku](https://stackoverflow.com/questions/tagged/azure-managedapps). Podobná otázka již mohla být položena a zodpovězena, takže nejprve zkontrolujte před odesláním. Přidejte `azure-managedapps` značku, abyste získali rychlou odpověď!

## <a name="next-steps"></a>Další kroky

Informace o publikování spravované aplikace na webu Azure Marketplace najdete v tématu [Spravované aplikace Azure v Marketplace](publish-marketplace-app.md).

Další informace o [vlastních zprostředkovatelích Azure](../custom-providers/overview.md).
