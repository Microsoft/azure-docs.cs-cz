---
title: Vytvoření pracovního prostoru s Azure Resource Manager šablonou
titleSuffix: Azure Machine Learning
description: Naučte se používat šablonu Azure Resource Manager k vytvoření nového pracovního prostoru Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli, devx-track-azurepowershell
ms.author: larryfr
author: Blackmist
ms.date: 09/30/2020
ms.openlocfilehash: 9df8a67fd3dfbf23986f1cc5ed18392463fc7ecb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102522202"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Použití šablony Azure Resource Manager k vytvoření pracovního prostoru pro Azure Machine Learning


<br>

V tomto článku se dozvíte několik způsobů, jak vytvořit pracovní prostor Azure Machine Learning pomocí šablon Azure Resource Manager. Šablona Správce prostředků usnadňuje vytváření prostředků jako jediné koordinované operace. Šablona je dokument JSON, který definuje prostředky, které jsou potřebné pro nasazení. Může také specifikovat parametry nasazení. Parametry slouží k poskytnutí vstupních hodnot při použití šablony.

Další informace najdete v tématu [nasazení aplikace pomocí šablony Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure** Pokud ho nemáte, vyzkoušejte [bezplatnou nebo placená verzi Azure Machine Learning](https://aka.ms/AMLFree).

* Pokud chcete použít šablonu z CLI, potřebujete buď [Azure PowerShell](/powershell/azure/) , nebo rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).

* Některé scénáře vyžadují, abyste otevřeli lístek podpory. Například použití pracovního prostoru s povoleným privátním propojením s klíčem spravovaným zákazníkem. Další informace najdete v tématu [Správa a zvýšení kvót](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

## <a name="limitations"></a>Omezení

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="workspace-resource-manager-template"></a>Šablona Správce prostředků pracovního prostoru

Šablona Azure Resource Manager použitá v tomto dokumentu se nachází v adresáři Azure pro rychlé zprovoznění šablon v úložišti GitHubu [201-Machine-Learning-pokročilé](https://github.com/Azure/azure-quickstart-templates/blob/master/201-machine-learning-advanced/azuredeploy.json) .

Tato šablona vytvoří následující služby Azure:

* Účet služby Azure Storage
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Pracovní prostor služby Azure Machine Learning

Skupina prostředků je kontejner, který obsahuje služby. Jednotlivé služby jsou vyžadovány pracovním prostorem Azure Machine Learning.

Ukázková šablona má dva **požadované** parametry:

* **Místo** , kde budou vytvořeny prostředky.

    Šablona bude používat umístění, které jste vybrali pro většinu prostředků. Výjimkou je služba Application Insights, která není dostupná ve všech umístěních, ve kterých jsou jiné služby. Pokud vyberete umístění, kde není k dispozici, bude služba vytvořena v umístění Střed USA – jih.

* Název **pracovního prostoru**, což je popisný název pracovního prostoru Azure Machine Learning.

    > [!NOTE]
    > V názvu pracovního prostoru se nerozlišují malá a velká písmena.

    Názvy ostatních služeb jsou vygenerovány náhodně.

> [!TIP]
> Zatímco šablona přidružená k tomuto dokumentu vytvoří novou Azure Container Registry, můžete také vytvořit nový pracovní prostor bez vytvoření registru kontejneru. Ten se vytvoří při provedení operace, která vyžaduje Registry kontejneru. Například školení nebo nasazení modelu.
>
> Místo vytvoření nové služby můžete také odkazovat na existující registr kontejnerů nebo účet úložiště v šabloně Azure Resource Manager. Když to uděláte, musíte buď [použít spravovanou identitu](how-to-use-managed-identities.md) (Preview), nebo [Povolit účet správce](../container-registry/container-registry-authentication.md#admin-account) pro registr kontejnerů.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Další informace o šablonách najdete v následujících článcích:

* [Vytváření šablon Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Nasazení aplikace pomocí šablon Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)
* [Typy prostředků Microsoft. MachineLearningServices](/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="deploy-template"></a>Nasazení šablony

Pokud chcete šablonu nasadit, musíte vytvořit skupinu prostředků.

Pokud dáváte přednost použití grafického uživatelského rozhraní, přečtěte si část [Azure Portal](#use-the-azure-portal) .

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az group create --name "examplegroup" --location "eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroup -Name "examplegroup" -Location "eastus"
```

---

Po úspěšném vytvoření skupiny prostředků nasaďte šablonu pomocí následujícího příkazu:

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" location="eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus"
```

---

Ve výchozím nastavení jsou všechny prostředky vytvořené jako součást šablony nové. Můžete ale také využít možnost použít stávající prostředky. Zadáním dalších parametrů do šablony můžete použít stávající prostředky. Pokud třeba chcete použít existující účet úložiště, nastavte hodnotu **storageAccountOption** na **stávající** a v parametru **storageAccountName** zadejte název svého účtu úložiště.

> [!IMPORTANT]
> Pokud chcete použít existující účet Azure Storage, nemůže to být účet Premium (Premium_LRS a Premium_GRS). Nemůže mít také hierarchický obor názvů (používá se s Azure Data Lake Storage Gen2). Ve výchozím účtu úložiště pracovního prostoru není podporován ani obor názvů Premium Storage ani hierarchický obor názvů. V rámci _výchozího_ účtu úložiště pracovního prostoru nejsou podporovány ani úrovně Premium Storage ani hierarchické obory názvů. Můžete použít Storage úrovně Premium nebo hierarchický obor názvů s účty úložiště, _které nejsou výchozí_ .

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      storageAccountOption="existing" \
      storageAccountName="existingstorageaccountname"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -storageAccountOption "existing" `
  -storageAccountName "existingstorageaccountname"
```

---

## <a name="deploy-an-encrypted-workspace"></a>Nasazení šifrovaného pracovního prostoru

Následující příklad šablony ukazuje, jak vytvořit pracovní prostor se třemi nastaveními:

* Povolit nastavení vysoké důvěrnosti pro pracovní prostor. Tím se vytvoří nová instance Cosmos DB.
* Povolte šifrování pro pracovní prostor.
* Používá existující Azure Key Vault k načtení klíčů spravovaných zákazníkem. Klíče spravované zákazníkem slouží k vytvoření nové instance Cosmos DB pracovního prostoru.

    [!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

> [!IMPORTANT]
> Po vytvoření pracovního prostoru nemůžete změnit nastavení pro důvěrná data, šifrování, ID trezoru klíčů nebo identifikátory klíčů. Chcete-li tyto hodnoty změnit, je nutné vytvořit nový pracovní prostor s použitím nových hodnot.

Další informace najdete v tématu věnovaném [šifrování v klidovém umístění](concept-data-encryption.md#encryption-at-rest).

> [!IMPORTANT]
> Než použijete tuto šablonu, musí vaše předplatné splňovat tyto požadavky:
> * Musíte mít existující Azure Key Vault, která obsahuje šifrovací klíč.
> * Azure Key Vault musí být ve stejné oblasti, ve které plánujete vytvořit pracovní prostor Azure Machine Learning.
> * Je nutné zadat ID Azure Key Vault a identifikátor URI šifrovacího klíče.

__Chcete-li získat hodnoty__ pro `cmk_keyvault` (ID Key Vault) a `resource_cmk_uri` parametry (identifikátor URI klíče) vyžadované touto šablonou, použijte následující postup:    

1. Chcete-li získat ID Key Vault, použijte následující příkaz:  

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)   

    ```azurecli 
    az keyvault show --name <keyvault-name> --query 'id' --output tsv   
    ``` 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell) 

    ```azurepowershell  
    Get-AzureRMKeyVault -VaultName '<keyvault-name>'    
    ``` 
    --- 

    Tento příkaz vrátí hodnotu podobnou `/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>` .  

1. Chcete-li získat hodnotu identifikátoru URI pro spravovaný klíč zákazníka, použijte následující příkaz:    

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)   

    ```azurecli 
    az keyvault key show --vault-name <keyvault-name> --name <key-name> --query 'key.kid' --output tsv  
    ``` 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell) 

    ```azurepowershell  
    Get-AzureKeyVaultKey -VaultName '<keyvault-name>' -KeyName '<key-name>' 
    ``` 
    --- 

    Tento příkaz vrátí hodnotu podobnou `https://mykeyvault.vault.azure.net/keys/mykey/{guid}` . 

> [!IMPORTANT]  
> Po vytvoření pracovního prostoru nemůžete změnit nastavení pro důvěrná data, šifrování, ID trezoru klíčů nebo identifikátory klíčů. Chcete-li tyto hodnoty změnit, je nutné vytvořit nový pracovní prostor s použitím nových hodnot.

Pokud chcete povolit použití zákaznických klíčů, nastavte při nasazování šablony následující parametry:

* **Encryption_status** **Povolit**.
* **cmk_keyvault** na `cmk_keyvault` hodnotu získanou v předchozích krocích.
* **resource_cmk_uri** na `resource_cmk_uri` hodnotu získanou v předchozích krocích.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      encryption_status="Enabled" \
      cmk_keyvault="/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" \
      resource_cmk_uri="https://mykeyvault.vault.azure.net/keys/mykey/{guid}" \
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -encryption_status "Enabled" `
  -cmk_keyvault "/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" `
  -resource_cmk_uri "https://mykeyvault.vault.azure.net/keys/mykey/{guid}"
```
---

Při použití klíče spravovaného zákazníkem Azure Machine Learning vytvoří sekundární skupinu prostředků, která obsahuje instanci Cosmos DB. Další informace najdete v tématu [šifrování v klidovém Cosmos DB](concept-data-encryption.md#encryption-at-rest).

Další konfigurací, kterou můžete pro data poskytnout, je nastavení parametru **confidential_data** na **hodnotu true**. Uděláte to takto:

* Spustí šifrování místního pomocného disku pro Azure Machine Learning výpočetních clusterů, takže jste nevytvořili žádné předchozí clustery v rámci vašeho předplatného. Pokud jste předtím vytvořili cluster v rámci předplatného, otevřete lístek podpory, který bude mít povolený šifrovací disk pro vaše výpočetní clustery.
* Vyčistí místní poškrábaný disk mezi spuštěním.
* Zabezpečeně předává přihlašovací údaje účtu úložiště, registru kontejnerů a účtu SSH z vrstvy spouštění do vašich výpočetních clusterů pomocí trezoru klíčů.
* Povolí filtrování protokolu IP, aby se zajistilo, že se nadřazené fondy dávek nedají volat v jiných externích službách než AzureMachineLearningService.

    > [!IMPORTANT]
    > Po vytvoření pracovního prostoru nemůžete změnit nastavení pro důvěrná data, šifrování, ID trezoru klíčů nebo identifikátory klíčů. Chcete-li tyto hodnoty změnit, je nutné vytvořit nový pracovní prostor s použitím nových hodnot.

  Další informace najdete v tématu věnovaném [šifrování v klidovém umístění](concept-data-encryption.md#encryption-at-rest).

## <a name="deploy-workspace-behind-a-virtual-network"></a>Nasazení pracovního prostoru za virtuální sítí

Nastavením `vnetOption` hodnoty parametru na buď `new` nebo `existing` můžete vytvořit prostředky používané pracovním prostorem za virtuální sítí.

> [!IMPORTANT]
> V případě registru kontejneru je podporována pouze SKU Premium.

> [!IMPORTANT]
> Application Insights nepodporuje nasazení za virtuální sítí.

### <a name="only-deploy-workspace-behind-private-endpoint"></a>Nasadit jenom pracovní prostor za privátním koncovým bodem

Pokud vaše přidružené prostředky nejsou za virtuální sítí, můžete nastavit parametr **privateEndpointType** na `AutoAproval` nebo `ManualApproval` pro nasazení pracovního prostoru za soukromým koncovým bodem. To se dá udělat pro nové i existující pracovní prostory. Když aktualizujete existující pracovní prostor, vyplňte parametry šablony informacemi z existujícího pracovního prostoru.

> [!IMPORTANT]
> Použití Azure Machine Learningho pracovního prostoru s privátním odkazem není v oblastech Azure Government k dispozici.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -privateEndpointType "AutoApproval"
```

---

### <a name="use-a-new-virtual-network"></a>Použít novou virtuální síť

Pokud chcete nasadit prostředek za novou virtuální sítí, nastavte **vnetOption** na **New** spolu s nastavením virtuální sítě pro příslušný prostředek. Níže uvedené nasazení ukazuje, jak nasadit pracovní prostor s prostředkem účtu úložiště za novou virtuální sítí.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -privateEndpointType "AutoApproval"
```

---

Alternativně můžete nasadit více nebo všechny závislé prostředky za virtuální síť.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -keyVaultBehindVNet "true" `
  -containerRegistryBehindVNet "true" `
  -containerRegistryOption "new" `
  -containerRegistrySku "Premium"
  -privateEndpointType "AutoApproval"
```

---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with a new virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      privateEndpointType="AutoApproval"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -privateEndpointType "AutoApproval"
```

--- -->

### <a name="use-an-existing-virtual-network--resources"></a>Použít existující prostředky & virtuální sítě

Pokud chcete nasadit pracovní prostor s existujícími přidruženými prostředky, musíte nastavit parametr **vnetOption** na **existující** společně s parametry podsítě. **Před** nasazením ale budete muset vytvořit koncové body služby ve virtuální síti pro každý z prostředků. Podobně jako u nových nasazení virtuální sítě můžete mít jeden nebo všechny prostředky za virtuální sítí.

> [!IMPORTANT]
> Podsíť by měla mít `Microsoft.Storage` koncový bod služby.

> [!IMPORTANT]
> Podsítě neumožňují vytváření privátních koncových bodů. Pokud chcete povolit podsíť, zakažte privátní koncový bod.

1. Povolte koncové body služby pro prostředky.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

    ```azurecli
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.Storage"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.KeyVault"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.ContainerRegistry"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.ContainerRegistry" | Set-AzVirtualNetwork
    ```

    ---

1. Nasazení pracovního prostoru

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

    ```azurecli
    az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="examplegroup" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium" \
      subnetName="examplesubnet" \
      subnetOption="existing"
      privateEndpointType="AutoApproval"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)
    ```azurepowershell
    New-AzResourceGroupDeployment `
      -Name "exampledeployment" `
      -ResourceGroupName "examplegroup" `
      -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
      -workspaceName "exampleworkspace" `
      -location "eastus" `
      -vnetOption "existing" `
      -vnetName "examplevnet" `
      -vnetResourceGroupName "examplegroup" `
      -storageAccountBehindVNet "true"
      -keyVaultBehindVNet "true" `
      -containerRegistryBehindVNet "true" `
      -containerRegistryOption "new" `
      -containerRegistrySku "Premium" `
      -subnetName "examplesubnet" `
      -subnetOption "existing"
      -privateEndpointType "AutoApproval"
    ```
    ---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with an existing virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="rg" \
      privateEndpointType="AutoApproval" \
      subnetName="subnet" \
      subnetOption="existing"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "existing" `
  -vnetName "examplevnet" `
  -vnetResourceGroupName "rg"
  -privateEndpointType "AutoApproval"
  -subnetName "subnet"
  -subnetOption "existing"
```

--- -->

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

1. Postupujte podle kroků v části [nasazení prostředků z vlastní šablony](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template). Až přijdete na obrazovku __Vybrat šablonu__ , v rozevíracím seznamu vyberte šablonu **201-Machine-Learning-Advanced** .
1. Vyberte __možnost vybrat šablonu__ a šablonu použijte. V závislosti na scénáři nasazení zadejte následující požadované informace a všechny další parametry.

   * Předplatné: vyberte předplatné Azure, které chcete použít pro tyto prostředky.
   * Skupina prostředků: vyberte nebo vytvořte skupinu prostředků, která bude obsahovat služby.
   * Oblast: Vyberte oblast Azure, ve které se budou prostředky vytvářet.
   * Název pracovního prostoru: název, který se má použít pro pracovní prostor Azure Machine Learning, který se vytvoří. Název pracovního prostoru musí být dlouhý 3 až 33 znaků. Může obsahovat pouze alfanumerické znaky a znak "-".
   * Umístění: vyberte umístění, kde se budou prostředky vytvářet.
1. Vyberte __Zkontrolovat a vytvořit__.
1. Na obrazovce __Revize + vytvořit__ vyjádřete souhlas s uvedenými podmínkami a ujednáními a vyberte __vytvořit__.

Další informace najdete v tématu [nasazení prostředků z vlastní šablony](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="troubleshooting"></a>Řešení potíží

### <a name="resource-provider-errors"></a>Chyby poskytovatele prostředků

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault zásady přístupu a šablony Azure Resource Manager

Když použijete šablonu Azure Resource Manager k vytvoření pracovního prostoru a přidružených prostředků (včetně Azure Key Vault), několikrát. Například použití šablony několikrát se stejnými parametry jako součást kanálu průběžné integrace a nasazení.

Většina operací vytváření prostředků prostřednictvím šablon je idempotentní, ale Key Vault neodstraní zásady přístupu pokaždé, když se šablona používá. Vymazání zásad přístupu přeruší přístup k Key Vault pro libovolný existující pracovní prostor, který ho používá. Například zastavení/vytvoření funkcí Azure Notebooks virtuálního počítače může selhat.  

Chcete-li se tomuto problému vyhnout, doporučujeme jeden z následujících přístupů:

* Nesaďte šablonu více než jednou pro stejné parametry. Nebo odstraňte existující prostředky, abyste je mohli znovu vytvořit pomocí šablony.

* Zkontrolujte zásady přístupu Key Vault a pak tyto zásady použijte k nastavení `accessPolicies` Vlastnosti šablony. Zásady přístupu zobrazíte pomocí následujícího příkazu rozhraní příkazového řádku Azure:

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Další informace o použití `accessPolicies` části šablony naleznete v tématu [AccessPolicyEntry Object reference](/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

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

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Tento příkaz vrátí hodnotu podobnou následujícímu textu:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

### <a name="virtual-network-not-linked-to-private-dns-zone"></a>Virtuální síť není propojená s privátní zónou DNS.

Při vytváření pracovního prostoru s privátním koncovým bodem vytvoří šablona Privátní DNS zónu s názvem __privatelink.API.AzureML.MS__. __Propojení virtuální sítě__ je automaticky přidáno do této privátní zóny DNS. Odkaz se přidá jenom pro první pracovní prostor a soukromý koncový bod, který vytvoříte ve skupině prostředků. Pokud vytvoříte jinou virtuální síť a pracovní prostor s privátním koncovým bodem ve stejné skupině prostředků, druhá virtuální síť se nemusí přidat do privátní zóny DNS.

Pokud chcete zobrazit odkazy virtuální sítě, které už existují pro privátní zónu DNS, použijte následující příkaz rozhraní příkazového řádku Azure:

```azurecli
az network private-dns link vnet list --zone-name privatelink.api.azureml.ms --resource-group myresourcegroup
```

Chcete-li přidat virtuální síť, která obsahuje jiný pracovní prostor a soukromý koncový bod, použijte následující postup:

1. Chcete-li zjistit ID virtuální sítě pro síť, kterou chcete přidat, použijte následující příkaz:

    ```azurecli
    az network vnet show --name myvnet --resource-group myresourcegroup --query id
    ```
    
    Tento příkaz vrátí hodnotu podobnou hodnotě "/subscriptions/GUID/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet". Uložte tuto hodnotu a použijte ji v dalším kroku.

2. Pokud chcete přidat odkaz virtuální sítě do zóny Privátní DNS privatelink.api.azureml.ms, použijte následující příkaz. Pro `--virtual-network` parametr použijte výstup předchozího příkazu:

    ```azurecli
    az network private-dns link vnet create --name mylinkname --registration-enabled true --resource-group myresourcegroup --virtual-network myvirtualnetworkid --zone-name privatelink.api.azureml.ms
    ```

## <a name="next-steps"></a>Další kroky

* [Nasazení prostředků pomocí Správce prostředků šablon a Správce prostředků REST API](../azure-resource-manager/templates/deploy-rest.md).
* [Vytvoření a nasazení skupin prostředků Azure pomocí sady Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
* [Další šablony týkající se Azure Machine Learning najdete v části úložiště šablon Azure pro rychlý Start.](https://github.com/Azure/azure-quickstart-templates)