---
title: Media Services šablona ARM účtu: Azure Media Services popis: v tomto článku se dozvíte, jak pomocí šablony ARM vytvořit účet Media Services.
služby: Media-Services documentationcenter: ' ' Author: IngridAtMicrosoft Manager: femila Editor: ' '

MS. Service: Media-Services MS. rebavování: MS. Subject: rychlý Start MS. Date: 03/23/2021 MS. Author: inhenkel MS. Custom: Subject-armqs

---

# <a name="quickstart-media-services-account-arm-template"></a>Rychlý Start: Šablona ARM účtu Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

V tomto článku se dozvíte, jak pomocí šablony Azure Resource Manager (šablony ARM) vytvořit účet Media Services.

## <a name="introduction"></a>Úvod

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Čtenáři, kteří se setkali s šablonami ARM, můžou pokračovat v [oddílu nasazení](#deploy-the-template).

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-media-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Pokud jste ještě nikdy nenainstalovali šablonu ARM, je vhodné si přečíst o [šablonách Azure ARM](../../azure-resource-manager/templates/index.yml) a Projděte si [kurz](../../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell).

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-media-services-create/).

Syntaxe pro ochranné kódy JSON je:

:::code language="json" source="~/quickstart-templates/101-media-services-create/azuredeploy.json":::

V šabloně jsou definovány tři typy prostředků Azure:

- [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): vytvoření účtu úložiště
- [Microsoft. Media/MediaServices](/azure/templates/microsoft.media/mediaservices): vytvoření účtu Media Services

## <a name="set-the-account"></a>Nastavení účtu

```azurecli-interactive

az account set --subscription {your subscription name or id}

```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

```azurecli-interactive

az group create --name {the name you want to give your resource group} --location "{pick a location}"

```

## <a name="assign-a-variable-to-your-deployment-file"></a>Přiřazení proměnné k souboru nasazení

Zjednodušte si práci tím, že vytvoříte proměnnou, která ukládá cestu k souboru šablony. Tato proměnná vám usnadní spouštění příkazů nasazení, protože nebudete muset znovu zadávat cestu při každém nasazování.

```azurecli-interactive

templateFile="{provide the path to the template file}"

```

## <a name="deploy-the-template"></a>Nasazení šablony

Zobrazí se výzva k zadání názvu účtu Media Services.

```azurecli-interactive

az deployment group create --name {the name you want to give to your deployment} --resource-group {the name of resource group you created} --template-file $templateFile

```

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Měla by se zobrazit odpověď JSON podobnou následující:

```json
{
  "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Resources/deployments/amsarmquickstartdeploy",
  "location": null,
  "name": "amsarmquickstartdeploy",
  "properties": {
    "correlationId": "{correlationid}",
    "debugSetting": null,
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
            "resourceGroup": "amsarmquickstartrg",
            "resourceName": "storagey44cfdmliwatk",
            "resourceType": "Microsoft.Storage/storageAccounts"
          }
        ],
        "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg",
        "resourceName": "{accountname}",
        "resourceType": "Microsoft.Media/mediaServices"
      }
    ],
    "duration": "PT1M10.8615001S",
    "error": null,
    "mode": "Incremental",
    "onErrorDeployment": null,
    "outputResources": [
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg"
      },
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
        "resourceGroup": "amsarmquickstartrg"
      }
    ],
    "outputs": null,
    "parameters": {
      "mediaServiceName": {
        "type": "String",
        "value": "{accountname}"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.Media",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "mediaServices"
          }
        ]
      },
      {
        "id": null,
        "namespace": "Microsoft.Storage",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "storageAccounts"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "templateHash": "{templatehash}",
    "templateLink": null,
    "timestamp": "2020-11-24T23:25:52.598184+00:00",
    "validatedResources": null
  },
  "resourceGroup": "amsarmquickstartrg",
  "tags": null,
  "type": "Microsoft.Resources/deployments"
}

```

V Azure Portal potvrďte, že byly vytvořeny vaše prostředky.

![vytvořené prostředky pro rychlý Start](./media/media-services-arm-template-quickstart/quickstart-arm-template-resources.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud neplánujete použít prostředky, které jste právě vytvořili, můžete odstranit skupinu prostředků.

```azurecli-interactive

az group delete --name {name of the resource group}

```

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc o použití šablony ARM pomocí procesu vytvoření šablony s parametry, proměnnými a dalšími, zkuste

> [!div class="nextstepaction"]
> [Kurz: vytvoření a nasazení první šablony ARM](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)