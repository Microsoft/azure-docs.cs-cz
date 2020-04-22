---
title: Vytvoření pracovního prostoru pomocí šablony Azure Resource Manager
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí šablony Azure Resource Manager vytvořit nový pracovní prostor Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.custom: seoapril2019
ms.openlocfilehash: b802a9c9df7e7f0c44ea66ee0061efb517b80050
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682762"
---
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Vytvoření pracovního prostoru pro Azure Machine Learning pomocí šablony Azure Resource Manageru

V tomto článku se dozvíte několik způsobů, jak vytvořit pracovní prostor Azure Machine Learning pomocí šablon Azure Resource Manager. Šablona Správce prostředků usnadňuje vytváření prostředků jako jedné koordinované operace. Šablona je dokument JSON, který definuje prostředky, které jsou potřebné pro nasazení. Může také určit parametry nasazení. Parametry se používají k zadání vstupních hodnot při použití šablony.

Další informace najdete [v tématu Nasazení aplikace pomocí šablony Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné **Azure**. Pokud ho nemáte, vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree).

* Pokud chcete použít šablonu z příkazového příkazového příkazu, potřebujete buď [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) nebo [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Šablona Resource Manageru

Následující šablonu Správce prostředků lze použít k vytvoření pracovního prostoru Azure Machine Learning a přidružených prostředků Azure:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Tato šablona vytvoří následující služby Azure:

* Skupina prostředků Azure
* Účet služby Azure Storage
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Pracovní prostor služby Azure Machine Learning

Skupina prostředků je kontejner, který obsahuje služby. Různé služby jsou vyžadovány pracovního prostoru Azure Machine Learning.

Ukázková šablona má dva parametry:

* **Umístění,** kde bude vytvořena skupina prostředků a služby.

    Šablona použije umístění, které vyberete pro většinu zdrojů. Výjimkou je služba Application Insights, která není k dispozici ve všech umístěních, ve kterých jsou ostatní služby. Pokud vyberete umístění, kde není k dispozici, služba bude vytvořena v umístění –– usa.

* **Název pracovního prostoru**, což je popisný název pracovního prostoru Azure Machine Learning.

    > [!NOTE]
    > Název pracovního prostoru nerozlišuje malá a velká písmena.

    Názvy ostatních služeb jsou generovány náhodně.

> [!TIP]
> Zatímco šablona přidružená k tomuto dokumentu vytvoří nový registr kontejnerů Azure, můžete také vytvořit nový pracovní prostor bez vytvoření registru kontejnerů. Jeden bude vytvořen při provádění operace, která vyžaduje registr kontejneru. Například školení nebo nasazení modelu.
>
> Můžete také odkazovat na existující registr kontejneru nebo účet úložiště v šabloně Azure Resource Manager, namísto vytvoření nového.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Další informace o šablonách naleznete v následujících článcích:

* [Autor šablony Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Nasazení aplikace pomocí šablon Azure Resource Manageru](../azure-resource-manager/templates/deploy-powershell.md)
* [Typy zdrojů Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>Pokročilá šablona

Následující ukázková šablona ukazuje, jak vytvořit pracovní prostor se třemi nastaveními:

* Povolit nastavení vysoké důvěrnosti pracovního prostoru
* Povolení šifrování pro pracovní prostor
* Používá existující Azure Key Vault k načtení klíčů spravovaných zákazníkem.

Další informace naleznete [v tématu Šifrování v klidovém stavu](concept-enterprise-security.md#encryption-at-rest).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus",
      "allowedValues": [
        "eastus",
        "eastus2",
        "southcentralus",
        "southeastasia",
        "westcentralus",
        "westeurope",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku":{
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred to as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "high_confidentiality":{
      "type": "string",
      "defaultValue": "false",
      "allowedValues": [
        "false",
        "true"
      ],
      "metadata": {
        "description": "Specifies that the Azure Machine Learning workspace holds highly confidential data."
      }
    },
    "encryption_status":{
      "type": "string",
      "defaultValue": "Disabled",
      "allowedValues": [
        "Enabled",
        "Disabled"
      ],
      "metadata": {
        "description": "Specifies if the Azure Machine Learning workspace should be encrypted with the customer managed key."
      }
    },
    "cmk_keyvault":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyvault Resource Manager ID."
      }
    },
    "resource_cmk_uri":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyvault key uri."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
    "containerRegistryName": "[concat('cr',uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2015-05-01",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2017-10-01",
      "name": "[variables('containerRegistryName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "adminUserEnabled": true
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2020-01-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]",
        "[resourceId('Microsoft.ContainerRegistry/registries', variables('containerRegistryName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "containerRegistry": "[resourceId('Microsoft.ContainerRegistry/registries',variables('containerRegistryName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]",
         "encryption": {
                "status": "[parameters('encryption_status')]",
                "keyVaultProperties": {
                    "keyVaultArmId": "[parameters('cmk_keyvault')]",
                    "keyIdentifier": "[parameters('resource_cmk_uri')]"
                  }
            },
        "hbiWorkspace": "[parameters('high_confidentiality')]"
      }
    }
  ]
}
```

Chcete-li získat ID trezoru klíčů a identifikátor URI klíče, který tato šablona potřebuje, můžete použít azure CLI. Následující příkaz získá ID trezoru klíčů:

```azurecli-interactive
az keyvault show --name mykeyvault --resource-group myresourcegroup --query "id"
```

Tento příkaz vrátí hodnotu podobnou . `"/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault"`

Chcete-li získat identifikátor URI pro klíč spravovaný zákazníkem, použijte následující příkaz:

```azurecli-interactive
az keyvault key show --vault-name mykeyvault --name mykey --query "key.kid"
```

Tento příkaz vrátí hodnotu podobnou . `"https://mykeyvault.vault.azure.net/keys/mykey/{guid}"`

> [!IMPORTANT]
> Po vytvoření pracovního prostoru nelze změnit nastavení důvěrných dat, šifrování, ID trezoru klíčů nebo identifikátorů klíčů. Chcete-li tyto hodnoty změnit, musíte vytvořit nový pracovní prostor pomocí nových hodnot.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

1. Postupujte podle pokynů v [části Nasazení prostředků z vlastní šablony](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Když se dostanete na obrazovku __Upravit šablonu,__ vložte ji do šablony z tohoto dokumentu.
1. Vyberte __Uložit,__ chcete-li šablonu použít. Uveďte následující informace a souhlasíte s uvedenými podmínkami:

   * Předplatné: Vyberte předplatné Azure, které chcete použít pro tyto prostředky.
   * Skupina prostředků: Vyberte nebo vytvořte skupinu prostředků, která bude obsahovat služby.
   * Název pracovního prostoru: Název, který se má použít pro pracovní prostor Azure Machine Learning, který se vytvoří. Název pracovního prostoru musí být mezi 3 a 33 znaky. Může obsahovat pouze alfanumerické znaky a '-'.
   * Umístění: Vyberte umístění, kde budou zdroje vytvořeny.

Další informace naleznete v [tématu Nasazení prostředků z vlastní šablony](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Použití Azure Powershell

Tento příklad předpokládá, že jste uložili `azuredeploy.json` šablonu do souboru pojmenovaného v aktuálním adresáři:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

Další informace najdete [v tématu Nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShellu](../azure-resource-manager/templates/deploy-powershell.md) a [Nasazení privátní šablony Správce prostředků s tokenem SAS a Azure PowerShellem.](../azure-resource-manager/templates/secure-template-with-sas-token.md)

## <a name="use-the-azure-cli"></a>Použití Azure CLI

Tento příklad předpokládá, že jste uložili `azuredeploy.json` šablonu do souboru pojmenovaného v aktuálním adresáři:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

Další informace najdete [v tématu Nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../azure-resource-manager/templates/deploy-cli.md) a [nasazení privátní šablony Správce prostředků s tokenem SAS a Azure CLI](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="troubleshooting"></a>Poradce při potížích

### <a name="resource-provider-errors"></a>Chyby zprostředkovatele prostředků

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Zásady přístupu k úložišti Azure Key Vault a šablony Azure Resource Manageru

Když použijete šablonu Azure Resource Manager k vytvoření pracovního prostoru a přidružených prostředků (včetně Azure Key Vault), vícekrát. Například použití šablony vícekrát se stejnými parametry jako součást průběžné integrace a nasazení kanálu.

Většina operací vytváření prostředků prostřednictvím šablon je idempotentní, ale trezor klíčů vymaže zásady přístupu při každém použití šablony. Vymazáním zásad přístupu přerušíte přístup k trezoru klíčů pro všechny existující pracovní prostory, které jej používají. Například stop/create funkce virtuálního počítače Azure poznámkových bloků může selhat.  

Chcete-li se tomuto problému vyhnout, doporučujeme jeden z následujících přístupů:

* Nenasazujte šablonu více než jednou pro stejné parametry. Nebo odstraňte existující prostředky před použitím šablony k jejich opětovnému vytvoření.

* Zkontrolujte zásady přístupu trezoru klíčů `accessPolicies` a potom pomocí těchto zásad nastavte vlastnost šablony. Chcete-li zobrazit zásady přístupu, použijte následující příkaz příkazu Azure CLI:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Další informace o `accessPolicies` použití části šablony naleznete v [odkazu na objekt AccessPolicyEntry](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

* Zkontrolujte, zda prostředek trezoru klíčů již existuje. Pokud ano, nevytvářejte ji znovu prostřednictvím šablony. Chcete-li například použít existující trezor klíčů namísto vytvoření nového, proveďte v šabloně následující změny:

    * **Přidejte** parametr, který přijímá ID existujícího prostředku trezoru klíčů:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Odeberte** oddíl, který vytváří prostředek trezoru klíčů:

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```

    * **Odeberte** `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` čáru `dependsOn` z části pracovního prostoru. Změňte `keyVault` **také** položku `properties` v části pracovního `keyVaultId` prostoru tak, aby odkazovala na parametr:

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    Po těchto změnách můžete při spuštění šablony určit ID existujícího prostředku trezoru klíčů. Šablona pak znovu použije trezor klíčů `keyVault` nastavením vlastnosti pracovního prostoru na jeho ID.

    Chcete-li získat ID trezoru klíčů, můžete odkazovat na výstup původní šablony spustit nebo použít Azure CLI. Následující příkaz je příkladem použití azure CLI získat ID prostředku trezoru klíčů:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Tento příkaz vrátí hodnotu podobnou následujícímu textu:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>Další kroky

* [Nasazení prostředků pomocí šablon Správce prostředků a rozhraní REST API správce prostředků](../azure-resource-manager/templates/deploy-rest.md).
* [Vytváření a nasazování skupin prostředků Azure prostřednictvím sady Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
