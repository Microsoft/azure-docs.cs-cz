---
title: 'Rychlý Start: Vytvoření šablony služby signalizace Azure – ARM'
description: V tomto rychlém startu se dozvíte, jak vytvořit službu Azure Signal Service pomocí šablony Azure Resource Manager.
author: mgblythe
ms.service: signalr
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mblythe
ms.date: 10/02/2020
ms.openlocfilehash: 04d0a98863dded93216f5fc669b8148f710f5f0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858851"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-signalr-service"></a>Rychlý Start: použití šablony ARM k nasazení služby signalizace Azure

V tomto rychlém startu se dozvíte, jak pomocí šablony Azure Resource Manager (šablony ARM) vytvořit službu Azure Signal. Službu Azure Signaler můžete nasadit pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Po přihlášení se šablona otevře v Azure Portal.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Tlačítko pro nasazení služby signalizace Azure do Azure pomocí šablony ARM v Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

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

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-signalr/).

:::code language="json" source="~/quickstart-templates/101-signalr/azuredeploy.json":::

Šablona definuje jeden prostředek Azure:

* [**Microsoft. SignalRService/Signaler**](/azure/templates/microsoft.signalrservice/signalr)

## <a name="deploy-the-template"></a>Nasazení šablony

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Výběrem následujícího odkazu nasaďte službu Azure Signal Service pomocí šablony ARM v Azure Portal:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Tlačítko pro nasazení služby signalizace Azure do Azure pomocí šablony ARM v Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

Na stránce **nasadit službu signalizace Azure** :

1. Pokud chcete, změňte **předplatné** z výchozí hodnoty.

2. V poli **Skupina prostředků**vyberte **vytvořit novou**, zadejte název nové skupiny prostředků a vyberte **OK**.

3. Pokud jste vytvořili novou skupinu prostředků, vyberte **oblast** pro skupinu prostředků.

4. Pokud chcete, zadejte nový **název** a **umístění** (například **eastus2**) služby Azure Signal. Pokud název nezadáte, automaticky se vygeneruje. Umístění služby signalizace Azure může být stejné nebo jiné než v oblasti skupiny prostředků. Pokud nezadáte umístění, bude nastaveno na stejnou oblast jako skupina prostředků.

5. Vyberte **cenovou úroveň** (**Free_F1** nebo **Standard_S1**), zadejte **kapacitu** (počet jednotek signalizace) a vyberte **režim** **výchozí** (vyžaduje server centrálního serveru), bez **serveru** (nepovoluje žádné připojení k serveru) nebo **Classic** (Přesměrováno na hub Server pouze v případě, že rozbočovač má připojení k serveru). Pak zvolte, jestli se mají **Povolit protokoly připojení** , nebo **Povolit protokoly zasílání zpráv**.

    > [!NOTE]
    > U **Free_F1** cenové úrovně je kapacita omezená na 1 jednotku.

    :::image type="content" source="./media/signalr-quickstart-azure-signalr-service-arm-template/deploy-azure-signalr-service-arm-template-portal.png" alt-text="Tlačítko pro nasazení služby signalizace Azure do Azure pomocí šablony ARM v Azure Portal.":::

6. Vyberte **Zkontrolovat a vytvořit**.

7. Přečtěte si podmínky a ujednání a potom vyberte **vytvořit**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Pokud chcete spustit PowerShellové skripty místně, zadejte nejdřív, `Connect-AzAccount` abyste si nastavili svoje přihlašovací údaje Azure.

Pomocí následujícího kódu nasaďte službu Azure Signal Service pomocí šablony ARM. Kód vás vyzve k zadání následujících položek:

* Název a oblast nové služby signalizace Azure
* Název a oblast nové skupiny prostředků
* Cenová úroveň Azure (**Free_F1** nebo **Standard_S1**)
* Kapacita jednotky signálu (1, 2, 5, 10, 20, 50 nebo 100)
  > [!NOTE]
  > U **Free_F1** cenové úrovně je kapacita omezená na 1 jednotku.
* Režim služby: ve **výchozím nastavení** vyžaduje server rozbočovače, bez **serveru** , který umožňuje zakázat žádné připojení k serveru, nebo **klasický** směr směrování na server hub jenom v případě, že má centrum připojení k serveru.
* Jestli se mají povolit protokoly pro připojení nebo zasílání zpráv (**true** nebo **false**)

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure SignalR service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

$priceTier = Read-Host -Prompt "Enter the pricing tier (Free_F1 or Standard_S1)"
$unitCapacity = Read-Host -Prompt "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100)"
$servicingMode = Read-Host -Prompt "Enter the service mode (Default, Serverless, or Classic)"
$enableConnectionLogs = Read-Host -Prompt "Specify whether to enable connectivity logs (true or false)"
$enableMessageLogs = Read-Host -Prompt "Specify whether to enable messaging logs (true or false)"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion

$paramObjHashTable = @{
    name = $serviceName
    location = $serviceLocation
    pricingTier = $priceTier
    capacity = [int]$unitCapacity
    serviceMode = $servicingMode
    enableConnectivityLogs = $enableConnectionLogs
    enableMessagingLogs = $enableMessageLogs
}

Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure SignalR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateParameterObject $paramObjHashTable `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

Pomocí následujícího kódu nasaďte službu Azure Signal Service pomocí šablony ARM. Kód vás vyzve k zadání následujících položek:

* Název a oblast nové služby signalizace Azure
* Název a oblast nové skupiny prostředků
* Cenová úroveň Azure (**Free_F1** nebo **Standard_S1**)
* Kapacita jednotky signálu (1, 2, 5, 10, 20, 50 nebo 100)
    > [!NOTE]
    > U **Free_F1** cenové úrovně je kapacita omezená na 1 jednotku.
* Režim služby: ve **výchozím nastavení** vyžaduje server rozbočovače, bez **serveru** , který umožňuje zakázat žádné připojení k serveru, nebo **klasický** směr směrování na server hub jenom v případě, že má centrum připojení k serveru.
* Jestli se mají povolit protokoly pro připojení nebo zasílání zpráv (**true** nebo **false**)

```azurecli-interactive
read -p "Enter a name for the new Azure SignalR service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter the pricing tier (Free_F1 or Standard_S1): " priceTier &&
read -p "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100): " unitCapacity &&
read -p "Enter the service mode (Default, Serverless, or Classic): " servicingMode &&
read -p "Specify whether to enable connectivity logs (true or false): " enableConnectionLogs &&
read -p "Specify whether to enable messaging logs (true or false): " enableMessageLogs &&
params='name='$serviceName' location='$serviceLocation' pricingTier='$priceTier' capacity='$unitCapacity' serviceMode='$servicingMode' enableConnectivityLogs='$enableConnectionLogs' enableMessagingLogs='$enableMessageLogs &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure SignalR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> Dokončení nasazení může trvat několik minut. Poznamenejte si názvy služby signalizace Azure a skupiny prostředků, které použijete ke kontrole nasazených prostředků později.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pomocí těchto kroků si můžete prohlédnout novou službu Azure Signal Service:

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **Signal**.

2. V seznamu Signaler vyberte novou službu. Zobrazí se stránka s **přehledem** pro novou službu Azure Signal.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Pokud si chcete zobrazit podrobnosti o službě Azure Signaler, spusťte následující interaktivní kód. Bude nutné zadat název nové služby a skupiny prostředků.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure SignalR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

Pokud si chcete zobrazit podrobnosti o službě Azure Signaler, spusťte následující interaktivní kód. Bude nutné zadat název nové služby a skupiny prostředků.

```azurecli-interactive
read -p "Enter the name of your Azure SignalR service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az signalr show --resource-group $resourceGroupName --name $serviceName" &&
az signalr show --resource-group $resourceGroupName --name $serviceName &&
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
