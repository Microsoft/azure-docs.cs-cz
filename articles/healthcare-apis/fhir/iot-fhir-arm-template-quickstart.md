---
title: 'Rychlý Start: nasazení konektoru Azure IoT Connector pro FHIR (Preview) pomocí šablony ARM'
description: V tomto rychlém startu se dozvíte, jak nasadit Azure IoT Connector pro FHIR (Preview) pomocí šablony Azure Resource Manager (šablona ARM).
author: rbhaiya
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.author: rabhaiya
ms.date: 01/21/2021
ms.openlocfilehash: b241484b8e8d981036fff4998d475b8b80fae42d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018387"
---
# <a name="quickstart-use-an-azure-resource-manager-arm-template-to-deploy-azure-iot-connector-for-fhir-preview"></a>Rychlý Start: použití šablony Azure Resource Manager (ARM) k nasazení služby Azure IoT Connector pro FHIR (Preview)

V tomto rychlém startu se dozvíte, jak pomocí šablony Azure Resource Manager (šablony ARM) nasadit Azure IoT Connector pro rychlé provázání prostředků pro interoperabilitu zdravotní péče (FHIR&#174;) *, což je funkce Azure API pro FHIR. K nasazení funkční instance služby Azure IoT Connector pro FHIR Tato šablona také nasadí nadřazené rozhraní API Azure pro službu FHIR a aplikaci Azure IoT Central, která exportuje telemetrii z simulátoru zařízení do Azure IoT Connector pro FHIR. Pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku můžete spustit šablonu ARM pro nasazení konektoru Azure IoT pro FHIR.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Po přihlášení se šablona otevře v Azure Portal.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Nasaďte do Azure konektor Azure IoT pro FHIR pomocí šablony ARM v Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

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

[Šablona](https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json) definuje následující prostředky Azure:

* **Microsoft. HealthcareApis/Services**
* **Microsoft. HealthcareApis/Services/iomtconnectors**
* **Microsoft. IoTCentral/IoTApps**

## <a name="deploy-the-template"></a>Nasazení šablony

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Výběrem následujícího odkazu nasaďte Azure IoT Connector pro FHIR pomocí šablony ARM v Azure Portal:

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Nasaďte do Azure konektor Azure IoT pro službu FHIR pomocí šablony ARM v Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

Na stránce **nasazení rozhraní API Azure pro FHIR** :

1. Pokud chcete, změňte **předplatné** z výchozí hodnoty na jiné předplatné.

2. V poli **Skupina prostředků** vyberte **vytvořit novou**, zadejte název nové skupiny prostředků a vyberte **OK**.

3. Pokud jste vytvořili novou skupinu prostředků, vyberte **oblast** pro skupinu prostředků.

4. Zadejte název nové instance Azure API pro instanci FHIR v **názvu služby FHIR**.

5. Vyberte **umístění** rozhraní API Azure pro FHIR. Umístění může být stejné nebo jiné než z oblasti skupiny prostředků.

6. Zadejte název služby Azure IoT Connector pro instanci FHIR v **názvu konektoru IoT**.

7. Zadejte název připojení vytvořeného v rámci Azure IoT Connector pro FHIR v **názvu připojení**. Toto připojení používá aplikace Azure IoT Central k nabízení simulace simulovaného zařízení do služby Azure IoT Connector pro FHIR.

8. Zadejte název pro novou aplikaci Azure IoT Central v názvu služby **IoT Central**. Tato aplikace bude k simulaci zařízení používat šablonu *monitorování nepřetržitého pacienta* .

9. Z rozevíracího seznamu **umístění IoT Central** vyberte umístění aplikace IoT Central. 

10. Vyberte **Zkontrolovat a vytvořit**.

11. Přečtěte si podmínky a ujednání a potom vyberte **vytvořit**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Pokud chcete spustit PowerShellové skripty místně, zadejte nejdřív, `Connect-AzAccount` abyste si nastavili svoje přihlašovací údaje Azure.

Pokud `Microsoft.HealthcareApis` poskytovatel prostředků ještě není pro vaše předplatné zaregistrovaný, můžete ho zaregistrovat s následujícím interaktivním kódem. Chcete-li spustit kód v Azure Cloud Shell, vyberte **vyzkoušet** v horním rohu libovolného bloku kódu.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

Pokud `Microsoft.IoTCentral` poskytovatel prostředků ještě není pro vaše předplatné zaregistrovaný, můžete ho zaregistrovat s následujícím interaktivním kódem. Chcete-li spustit kód v Azure Cloud Shell, vyberte **vyzkoušet** v horním rohu libovolného bloku kódu.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.IoTCentral
```

Pomocí následujícího kódu nasaďte službu Azure IoT Connector for FHIR pomocí šablony ARM.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$fhirServiceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$location = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$iotConnectorName = Read-Host -Prompt "Enter a name for the new Azure IoT Connector for FHIR"
$connectionName = Read-Host -Prompt "Enter a name for the connection with Azure IoT Connector for FHIR"
$iotCentralName = Read-Host -Prompt "Enter a name for the new Azure IoT Central Application"
$iotCentralLocation = Read-Host -Prompt "Enter a location for the new Azure IoT Central Application"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure IoT Connector for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json `
    -fhirServiceName $fhirServiceName `
    -location $location
    -iotConnectorName $iotConnectorName
    -connectionName $connectionName
    -iotCentralName $iotCentralName
    -iotCentralLocation $iotCentralLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

Pokud `Microsoft.HealthcareApis` poskytovatel prostředků ještě není pro vaše předplatné zaregistrovaný, můžete ho zaregistrovat s následujícím interaktivním kódem. Chcete-li spustit kód v Azure Cloud Shell, vyberte **vyzkoušet** v horním rohu libovolného bloku kódu.

```azurecli-interactive
az extension add --name healthcareapis
```

Pokud `Microsoft.IoTCentral` poskytovatel prostředků ještě není pro vaše předplatné zaregistrovaný, můžete ho zaregistrovat s následujícím interaktivním kódem.

```azurecli-interactive
az extension add --name azure-iot
```

Pomocí následujícího kódu nasaďte službu Azure IoT Connector for FHIR pomocí šablony ARM.

```azurecli-interactive
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter a name for the new Azure API for FHIR service: " fhirServiceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " location &&
read -p "Enter a name for the new Azure IoT Connector for FHIR: " iotConnectorName &&
read -p "Enter a name for the connection with Azure IoT Connector for FHIR: " connectionName &&
read -p "Enter a name for the new Azure IoT Central Application: " iotCentralName &&
read -p "Enter a location for the new Azure IoT Central Application: " iotCentralLocation &&

params='fhirServiceName='$fhirServiceName' location='$location' iotConnectorName='$iotConnectorName' connectionName='$connectionName' iotCentralName='$iotCentralName' iotCentralLocation='$iotCentralLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure IoT Connector for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> Dokončení nasazení trvá několik minut. Poznamenejte si názvy pro Azure API for FHIR Service, Azure IoT Central Application a skupinu prostředků, kterou použijete ke kontrole nasazených prostředků později.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pomocí těchto kroků si můžete zobrazit přehled nové služby Azure API pro službu FHIR:

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **Azure API pro FHIR**.

2. V seznamu FHIR vyberte novou službu. Zobrazí se stránka s **přehledem** pro nové služby Azure API pro FHIR.

3. Pokud chcete ověřit, jestli je nový účet rozhraní FHIR API zřízený, vyberte odkaz vedle **koncového bodu metadat FHIR** a načtěte příkaz funkce FHIR API. Odkaz má formát `https://<service-name>.azurehealthcareapis.com/metadata` . Pokud je účet zřízený, zobrazí se velký soubor JSON.

4. Pokud chcete ověřit, jestli je nový konektor Azure IoT pro FHIR zřízený, vyberte v navigační nabídce vlevo možnost **konektor IoT (Preview)** a otevřete stránku **konektory IoT** . Stránka musí zobrazovat zřízený službu Azure IoT Connector pro FHIR s hodnotou *stavu* jako *online*, hodnota *připojení* jako *1* a *mapování zařízení* i *mapování FHIR* zobrazit ikonu *úspěchu* .

5. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **IoT Central aplikace**.

6. V seznamu aplikací IoT Central vyberte novou službu. Zobrazí se stránka s **přehledem** pro novou aplikaci IoT Central.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Spuštěním následujícího interaktivního kódu zobrazíte podrobnosti o rozhraní API Azure pro službu FHIR. Bude nutné zadat název nové služby FHIR a skupiny prostředků.

```azurepowershell-interactive
$fhirServiceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new FHIR service has been provisioned"

$requestUri="https://" + $fhirServiceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

> [!NOTE]
> Azure IoT Connector pro FHIR v tuto chvíli neposkytuje příkazy PowerShellu. Pokud chcete ověřit, že je váš konektor Azure IoT pro FHIR správně zřízený, použijte proces ověření, který je k dispozici na kartě **portál** .

Spuštěním následujícího interaktivního kódu zobrazíte podrobnosti o vaší aplikaci Azure IoT Central. Bude nutné zadat název nové aplikace IoT Central a skupiny prostředků.

```azurepowershell-interactive
$iotCentralName = Read-Host -Prompt "Enter the name of your Azure IoT Central application"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName" -Verbose
Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

Spuštěním následujícího interaktivního kódu zobrazíte podrobnosti o rozhraní API Azure pro službu FHIR. Bude nutné zadat název nové služby FHIR a skupiny prostředků.

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " fhirServiceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$fhirServiceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
read -p "Press [ENTER] to continue: "
```

> [!NOTE]
> Azure IoT Connector pro FHIR neposkytuje v tuto chvíli příkazy rozhraní příkazového řádku. Pokud chcete ověřit, že je váš konektor Azure IoT pro FHIR správně zřízený, použijte proces ověření, který je k dispozici na kartě **portál** .

Spuštěním následujícího interaktivního kódu zobrazíte podrobnosti o vaší aplikaci Azure IoT Central. Bude nutné zadat název nové aplikace IoT Central a skupiny prostředků.

```azurecli-interactive
read -p "Enter the name of your IoT Central application: " iotCentralName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az iot central app show -g $resourceGroupName -n $iotCentralName" &&
az iot central app show -g $resourceGroupName -n $iotCentralName &&
```

---

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>Připojení dat IoT k Azure IoT Connectoru pro FHIR (Preview)
> [!IMPORTANT]
> Šablona mapování zařízení uvedená v této příručce je navržená pro práci s exportem dat (starší verze) v rámci IoT Central.

IoT Central aplikace momentálně neposkytuje pro nastavení exportu dat šablonu ARM ani příkazy PowerShellu a rozhraní příkazového řádku. Postupujte proto podle následujících pokynů pomocí Azure Portal.  

Po nasazení IoT Central aplikace začnou vaše dvě předem připravená simulovaná zařízení generovat telemetrii. V tomto kurzu budeme ingestovat telemetrii z *inteligentního* simulátoru opravy do FHIR prostřednictvím služby Azure IoT Connector pro FHIR. Pokud chcete exportovat data IoT do služby Azure IoT Connector pro FHIR, chceme [v rámci IoT Central nastavit export dat (starší verze)](../../iot-central/core/howto-export-data-legacy.md). Na stránce Export dat (starší verze):
- Jako cíl exportu vyberte *Azure Event Hubs* .
- Vyberte *použít hodnotu připojovacího řetězce* pro **Event Hubs pole obor názvů** .
- Poskytněte konektor Azure IoT Connector pro připojovací řetězec FHIR, který jste získali v předchozím kroku pro pole **připojovací řetězec** .
- *Možnost zachování* **telemetrie** pro **data pro export** pole

---

## <a name="view-device-data-in-azure-api-for-fhir"></a>Zobrazení dat zařízení v rozhraní Azure API pro FHIR

Prostředky pro sledování založené na FHIR, které vytvořila služba Azure IoT Connector pro FHIR, můžete zobrazit na serveru FHIR pomocí post. Nastavte svého poskytovatele [pro přístup k rozhraní API Azure pro FHIR](access-fhir-postman-tutorial.md) a `GET` vyžádejte si požadavek na `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` zobrazení informací o prostředcích FHIR pro sledování pomocí hodnoty srdcových sazeb.

> [!TIP]
> Ujistěte se, že váš uživatel má odpovídající přístup k rozhraní Azure API pro rovinu dat FHIR. Pomocí [řízení přístupu na základě role Azure (Azure RBAC)](configure-azure-rbac.md) přiřaďte požadované role roviny dat.

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

Podrobný kurz, který vás provede procesem vytvoření šablony ARM, najdete v [kurzu Vytvoření a nasazení první šablony ARM](../../azure-resource-manager/templates/template-tutorial-create-first-template.md) .

## <a name="next-steps"></a>Další kroky

V tomto průvodci rychlým startem jste nasadili funkci Azure IoT Connector for FHIR do svého prostředku Azure API pro FHIR. Pokud chcete získat další informace o službě Azure IoT Connector pro FHIR, vyberte níže v následujících krocích:

Pochopení různých fází toku dat v rámci služby Azure IoT Connector pro FHIR.

>[!div class="nextstepaction"]
>[Azure IoT Connector pro tok dat FHIR](iot-data-flow.md)

Naučte se konfigurovat konektor IoT pomocí šablon pro mapování zařízení a FHIR.

>[!div class="nextstepaction"]
>[Azure IoT Connector pro šablony mapování FHIR](iot-mapping-templates.md)

* V Azure Portal se konektor Azure IoT pro FHIR označuje jako IoT Connector (Preview). FHIR je registrovaná ochranná známka změněného HL7 a používá se s oprávněním změněného HL7.
