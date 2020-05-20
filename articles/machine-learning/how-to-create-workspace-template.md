---
title: Vytvoření pracovního prostoru s Azure Resource Manager šablonou
titleSuffix: Azure Machine Learning
description: Naučte se používat šablonu Azure Resource Manager k vytvoření nového pracovního prostoru Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 05/19/2020
ms.custom: seoapril2019
ms.openlocfilehash: eae10b7ae8cd14fd120e969c39c05a8ba2525003
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681546"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Použití šablony Azure Resource Manager k vytvoření pracovního prostoru pro Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

V tomto článku se dozvíte několik způsobů, jak vytvořit pracovní prostor Azure Machine Learning pomocí šablon Azure Resource Manager. Šablona Správce prostředků usnadňuje vytváření prostředků jako jediné koordinované operace. Šablona je dokument JSON, který definuje prostředky, které jsou potřebné pro nasazení. Může také specifikovat parametry nasazení. Parametry slouží k poskytnutí vstupních hodnot při použití šablony.

Další informace najdete v tématu [nasazení aplikace pomocí šablony Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Pokud ho nemáte, vyzkoušejte [bezplatnou nebo placená verzi Azure Machine Learning](https://aka.ms/AMLFree).

* Pokud chcete použít šablonu z CLI, potřebujete buď [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) , nebo rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

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

Skupina prostředků je kontejner, který obsahuje služby. Jednotlivé služby jsou vyžadovány pracovním prostorem Azure Machine Learning.

Příklad šablony má dva parametry:

* **Umístění** , kde se vytvoří skupina prostředků a služby.

    Šablona bude používat umístění, které jste vybrali pro většinu prostředků. Výjimkou je služba Application Insights, která není dostupná ve všech umístěních, ve kterých jsou jiné služby. Pokud vyberete umístění, kde není k dispozici, bude služba vytvořena v umístění Střed USA – jih.

* **Název pracovního prostoru**, což je popisný název pracovního prostoru Azure Machine Learning.

    > [!NOTE]
    > V názvu pracovního prostoru se nerozlišují malá a velká písmena.

    Názvy ostatních služeb jsou vygenerovány náhodně.

> [!TIP]
> Zatímco šablona přidružená k tomuto dokumentu vytvoří novou Azure Container Registry, můžete také vytvořit nový pracovní prostor bez vytvoření registru kontejneru. Ten se vytvoří při provedení operace, která vyžaduje Registry kontejneru. Například školení nebo nasazení modelu.
>
> Místo vytvoření nové služby můžete také odkazovat na existující registr kontejnerů nebo účet úložiště v šabloně Azure Resource Manager.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Další informace o šablonách najdete v následujících článcích:

* [Vytváření šablon Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Nasazení aplikace pomocí šablon Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)
* [Typy prostředků Microsoft. MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>Pokročilá šablona

Následující příklad šablony ukazuje, jak vytvořit pracovní prostor se třemi nastaveními:

* Povolit nastavení vysoké důvěrnosti pro pracovní prostor
* Povolení šifrování pro pracovní prostor
* Použije existující Azure Key Vault k načtení klíčů spravovaných zákazníkem.

Další informace najdete v tématu věnovaném [šifrování v klidovém umístění](concept-enterprise-security.md#encryption-at-rest).

> [!IMPORTANT]
> Než použijete tuto šablonu, musí vaše předplatné splňovat tyto požadavky:
> * Aplikace __Azure Machine Learning__ musí být __přispěvatelem__ vašeho předplatného Azure.
> * Musíte mít existující Azure Key Vault, která obsahuje šifrovací klíč.
> * V Azure Key Vault musíte mít zásadu přístupu, která uděluje přístup k aplikaci __Azure Cosmos DB__ k __získání__, __zabalení__a __rozbalení__ .
> * Azure Key Vault musí být ve stejné oblasti, ve které plánujete vytvořit pracovní prostor Azure Machine Learning.

Pokud __chcete přidat aplikaci Azure Machine Learning jako přispěvatele__, použijte následující příkazy:

1. K ověření v Azure pomocí rozhraní příkazového řádku použijte následující příkaz:

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [subscription-login](../../includes/machine-learning-cli-subscription.md)]

1. K získání ID objektu aplikace Azure Machine Learning použijte následující příkaz. Hodnota se může lišit pro každé z vašich předplatných Azure:

    ```azurecli-interactive
    az ad sp list --display-name "Azure Machine Learning" --query '[].[appDisplayName,objectId]' --output tsv
    ```

    Tento příkaz vrátí ID objektu, což je identifikátor GUID.

1. Pokud chcete přidat ID objektu jako přispěvatele k vašemu předplatnému, použijte následující příkaz. Nahraďte `<object-ID>` identifikátorem GUID z předchozího kroku. Nahraďte `<subscription-ID>` názvem nebo ID vašeho předplatného Azure:

    ```azurecli-interactive
    az role assignment create --role 'Contributor' --assignee-object-id <object-ID> --subscription <subscription-ID>
    ```

Pokud chcete __do Azure Key Vault přidat klíč__, použijte informace v článku [Přidání klíče, tajného klíče nebo certifikátu do trezoru klíčů](../key-vault/general/manage-with-cli2.md#adding-a-key-secret-or-certificate-to-the-key-vault) tématu __Správa Key Vault pomocí Azure CLI__ .

Pokud chcete __do trezoru klíčů přidat zásady přístupu, použijte následující příkazy__:

1. K získání ID objektu aplikace Azure Cosmos DB použijte následující příkaz. Hodnota se může lišit pro každé z vašich předplatných Azure:

    ```azurecli-interactive
    az ad sp list --display-name "Azure Cosmos DB" --query '[].[appDisplayName,objectId]' --output tsv
    ```
    
    Tento příkaz vrátí ID objektu, což je identifikátor GUID.

1. Chcete-li nastavit zásadu, použijte následující příkaz. Nahraďte `<keyvault-name>` názvem existující Azure Key Vault. Nahraďte `<object-ID>` identifikátorem GUID z předchozího kroku:

    ```azurecli-interactive
    az keyvault set-policy --name <keyvault-name> --object-id <object-ID> --key-permissions get unwrapKey wrapKey
    ```

__Chcete-li získat hodnoty__ pro `cmk_keyvault` (ID Key Vault) a `resource_cmk_uri` parametry (identifikátor URI klíče) vyžadované touto šablonou, použijte následující postup:

1. Chcete-li získat ID Key Vault, použijte následující příkaz:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query "id"
    ```

    Tento příkaz vrátí hodnotu podobnou `/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault` .

1. Chcete-li získat hodnotu identifikátoru URI pro spravovaný klíč zákazníka, použijte následující příkaz:

    ```azurecli-interactive
    az keyvault key show --vault-name mykeyvault --name mykey --query "key.kid"
    ```

    Tento příkaz vrátí hodnotu podobnou `https://mykeyvault.vault.azure.net/keys/mykey/{guid}` .

__Příklad šablony__

:::code language="json" source="~/quickstart-templates/201-machine-learning-encrypted-workspace/azuredeploy.json":::

> [!IMPORTANT]
> Po vytvoření pracovního prostoru nemůžete změnit nastavení pro důvěrná data, šifrování, ID trezoru klíčů nebo identifikátory klíčů. Chcete-li tyto hodnoty změnit, je nutné vytvořit nový pracovní prostor s použitím nových hodnot.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

1. Postupujte podle kroků v části [nasazení prostředků z vlastní šablony](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Po přijetí na obrazovku __Upravit šablonu__ vložte do šablony z tohoto dokumentu šablonu.
1. Vyberte __Uložit__ a použijte šablonu. Zadejte následující informace a vyjádřete souhlas s uvedenými podmínkami a ujednáními:

   * Předplatné: vyberte předplatné Azure, které chcete použít pro tyto prostředky.
   * Skupina prostředků: vyberte nebo vytvořte skupinu prostředků, která bude obsahovat služby.
   * Název pracovního prostoru: název, který se má použít pro pracovní prostor Azure Machine Learning, který se vytvoří. Název pracovního prostoru musí být dlouhý 3 až 33 znaků. Může obsahovat pouze alfanumerické znaky a znak "-".
   * Umístění: vyberte umístění, kde se budou prostředky vytvářet.

Další informace najdete v tématu [nasazení prostředků z vlastní šablony](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Použití Azure Powershell

V tomto příkladu se předpokládá, že jste šablonu uložili do souboru s názvem `azuredeploy.json` v aktuálním adresáři:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

Další informace najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) a [nasazení privátní šablony Správce prostředků pomocí tokenu SAS a Azure PowerShell](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="use-the-azure-cli"></a>Použití Azure CLI

V tomto příkladu se předpokládá, že jste šablonu uložili do souboru s názvem `azuredeploy.json` v aktuálním adresáři:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

Další informace najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../azure-resource-manager/templates/deploy-cli.md) a [nasazení privátních správce prostředků šablony s tokenem SAS a](../azure-resource-manager/templates/secure-template-with-sas-token.md)rozhraním příkazového řádku Azure CLI.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="resource-provider-errors"></a>Chyby poskytovatele prostředků

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault zásady přístupu a šablony Azure Resource Manager

Když použijete šablonu Azure Resource Manager k vytvoření pracovního prostoru a přidružených prostředků (včetně Azure Key Vault), několikrát. Například použití šablony několikrát se stejnými parametry jako součást kanálu průběžné integrace a nasazení.

Většina operací vytváření prostředků prostřednictvím šablon je idempotentní, ale Key Vault neodstraní zásady přístupu pokaždé, když se šablona používá. Vymazání zásad přístupu přeruší přístup k Key Vault pro libovolný existující pracovní prostor, který ho používá. Například zastavení/vytvoření funkcí Azure Notebooks virtuálního počítače může selhat.  

Chcete-li se tomuto problému vyhnout, doporučujeme jeden z následujících přístupů:

* Nesaďte šablonu více než jednou pro stejné parametry. Nebo odstraňte existující prostředky, abyste je mohli znovu vytvořit pomocí šablony.

* Zkontrolujte zásady přístupu Key Vault a pak tyto zásady použijte k nastavení `accessPolicies` Vlastnosti šablony. Zásady přístupu zobrazíte pomocí následujícího příkazu rozhraní příkazového řádku Azure:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Další informace o použití `accessPolicies` části šablony naleznete v tématu [AccessPolicyEntry Object reference](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

* Ověřte, zda prostředek Key Vault již existuje. Pokud tomu tak není, nevytvářejte ho znovu prostřednictvím šablony. Chcete-li například použít existující Key Vault místo vytvoření nové, proveďte následující změny šablony:

    * **Přidejte** parametr, který přijímá ID existujícího prostředku Key Vault:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Odeberte** část, která vytvoří prostředek Key Vault:

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

    * **Odeberte** `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` řádek z `dependsOn` části pracovního prostoru. Také **změňte** `keyVault` položku v `properties` části pracovního prostoru tak, aby odkazovala na `keyVaultId` parametr:

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

    Po těchto změnách můžete zadat ID existujícího prostředku Key Vault při spuštění šablony. Šablona pak znovu použije Key Vault nastavením `keyVault` vlastnosti pracovního prostoru na jeho ID.

    Pokud chcete získat ID Key Vault, můžete odkazovat na výstup původní šablony a použít Azure CLI. Následující příkaz je příkladem použití rozhraní příkazového řádku Azure k získání ID Key Vault prostředku:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Tento příkaz vrátí hodnotu podobnou následujícímu textu:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>Další kroky

* [Nasazení prostředků pomocí Správce prostředků šablon a Správce prostředků REST API](../azure-resource-manager/templates/deploy-rest.md).
* [Vytvoření a nasazení skupin prostředků Azure pomocí sady Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
