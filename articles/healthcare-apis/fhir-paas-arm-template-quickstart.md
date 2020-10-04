---
title: 'Rychlý Start: nasazení rozhraní Azure API pro FHIR pomocí šablony ARM'
description: V tomto rychlém startu se dozvíte, jak nasadit rozhraní API Azure pro rychlé interoperability zdravotnictví (FHIR®) pomocí Azure Resource Manager šablony.
author: mgblythe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mblythe
ms.date: 09/14/2020
ms.openlocfilehash: 393678342ad1e8e6e2ff5d3c38dab68ce29ee7e7
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711715"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-api-for-fhir"></a>Rychlý Start: použití šablony ARM k nasazení rozhraní Azure API pro FHIR

V tomto rychlém startu se dozvíte, jak použít šablonu Azure Resource Manager (šablonu ARM) k nasazení rozhraní Azure API pro rychlé provázání prostředků pro zajištění zdravotní péče (FHIR®). Rozhraní Azure API pro FHIR můžete nasadit prostřednictvím Azure Portal, PowerShellu nebo rozhraní příkazového řádku.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Po přihlášení se šablona otevře v Azure Portal.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Nasaďte do Azure rozhraní Azure API pro službu FHIR pomocí šablony ARM v Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

## <a name="prerequisites"></a>Požadované součásti

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).
* Pokud chcete spustit kód místně, [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).
* Pokud chcete spustit kód místně:
    * Prostředí bash (například Git bash, které je součástí [Gitu pro Windows](https://gitforwindows.org)).
    * Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli)

---

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-azure-api-for-fhir/).

:::code language="json" source="~/quickstart-templates/101-azure-api-for-fhir/azuredeploy.json":::

Šablona definuje jeden prostředek Azure:

* **Microsoft. HealthcareApis/Services**

<!--

Replace the line above with the following line once https://docs.microsoft.com/azure/templates/microsoft.healthcareapis/services goes live:

* [**Microsoft.HealthcareApis/services**](/azure/templates/microsoft.healthcareapis/services)

-->

Další ukázkové šablony Azure API pro FHIR najdete v [galerii šablon rychlý Start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Healthcareapis&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Nasazení šablony

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Vyberte následující odkaz pro nasazení rozhraní API Azure pro FHIR pomocí šablony ARM v Azure Portal:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Nasaďte do Azure rozhraní Azure API pro službu FHIR pomocí šablony ARM v Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

Na stránce **nasazení rozhraní API Azure pro FHIR** :

1. Pokud chcete, změňte **předplatné** z výchozí hodnoty na jiné předplatné.

2. V poli **Skupina prostředků**vyberte **vytvořit novou**, zadejte název nové skupiny prostředků a vyberte **OK**.

3. Pokud jste vytvořili novou skupinu prostředků, vyberte **oblast** pro skupinu prostředků.

4. Zadejte nový **název služby** a vyberte **umístění** rozhraní API Azure pro FHIR. Umístění může být stejné nebo jiné než z oblasti skupiny prostředků.

    :::image type="content" source="./media/fhir-paas-arm-template-quickstart/deploy-azure-api-fhir.png" alt-text="Nasaďte do Azure rozhraní Azure API pro službu FHIR pomocí šablony ARM v Azure Portal.":::

5. Vyberte **Zkontrolovat a vytvořit**.

6. Přečtěte si podmínky a ujednání a potom vyberte **vytvořit**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Pokud chcete spustit PowerShellové skripty místně, zadejte nejdřív, `Connect-AzAccount` abyste si nastavili svoje přihlašovací údaje Azure.

Pokud `Microsoft.HealthcareApis` poskytovatel prostředků ještě není pro vaše předplatné zaregistrovaný, můžete ho zaregistrovat s následujícím interaktivním kódem. Chcete-li spustit kód v Azure Cloud Shell, vyberte **vyzkoušet** v horním rohu libovolného bloku kódu.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

Pomocí následujícího kódu nasaďte službu Azure API for FHIR pomocí šablony ARM. Kód vás vyzve k zadání nového názvu služby FHIR, názvu nové skupiny prostředků a umístění pro každý z nich.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure API for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json `
    -serviceName $serviceName `
    -location $serviceLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

Pokud `Microsoft.HealthcareApis` poskytovatel prostředků ještě není pro vaše předplatné zaregistrovaný, můžete ho zaregistrovat s následujícím interaktivním kódem. Chcete-li spustit kód v Azure Cloud Shell, vyberte **vyzkoušet** v horním rohu libovolného bloku kódu.

```azurecli-interactive
az extension add --name healthcareapis
```

Pomocí následujícího kódu nasaďte službu Azure API for FHIR pomocí šablony ARM. Kód vás vyzve k zadání nového názvu služby FHIR, názvu nové skupiny prostředků a umístění pro každý z nich.

```azurecli-interactive
read -p "Enter a name for the new Azure API for FHIR service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
params='serviceName='$serviceName' location='$serviceLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure API for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> Dokončení nasazení trvá několik minut. Poznamenejte si názvy pro Azure API pro službu FHIR a skupinu prostředků, které použijete k revizi nasazených prostředků později.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pomocí těchto kroků si můžete zobrazit přehled nové služby Azure API pro službu FHIR:

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **Azure API pro FHIR**.

2. V seznamu FHIR vyberte novou službu. Zobrazí se stránka s **přehledem** pro nové služby Azure API pro FHIR.

3. Pokud chcete ověřit, jestli je nový účet rozhraní FHIR API zřízený, vyberte odkaz vedle **koncového bodu metadat FHIR** a načtěte příkaz funkce FHIR API. Odkaz má formát `https://<service-name>.azurehealthcareapis.com/metadata` . Pokud je účet zřízený, zobrazí se velký soubor JSON.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Spuštěním následujícího interaktivního kódu zobrazíte podrobnosti o rozhraní API Azure pro službu FHIR. Bude nutné zadat název nové služby a skupiny prostředků.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned"

$requestUri="https://" + $serviceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

Spuštěním následujícího interaktivního kódu zobrazíte podrobnosti o rozhraní API Azure pro službu FHIR. Bude nutné zadat název nové služby a skupiny prostředků.

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$serviceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, která odstraní prostředky ve skupině prostředků.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **skupiny prostředků**.

2. V seznamu Skupina prostředků vyberte název vaší skupiny prostředků.

3. Na stránce **Přehled** vaší skupiny prostředků vyberte **Odstranit skupinu prostředků**.

4. V potvrzovacím dialogovém okně zadejte název vaší skupiny prostředků a pak vyberte **Odstranit**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="next-steps"></a>Další kroky

Podrobný kurz, který vás provede procesem vytvoření šablony ARM, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [ Kurz: vytvoření a nasazení první šablony ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
