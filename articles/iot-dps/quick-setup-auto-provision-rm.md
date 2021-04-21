---
title: Rychlý Start – vytvoření Azure IoT Hub Device Provisioning Service (DPS) pomocí šablony Azure Resource Manager (šablona ARM)
description: Rychlý Start Azure – Přečtěte si, jak vytvořit Azure IoT Hub Device Provisioning Service (DPS) pomocí šablony Azure Resource Manager (šablona ARM).
author: wesmc7777
ms.author: wesmc
ms.date: 01/27/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, subject-armqs, devx-track-azurecli
ms.openlocfilehash: bcb37c624f53d961020de022569a621ca1dfaba1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788957"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-dps-with-an-arm-template"></a>Rychlý Start: nastavení IoT Hub Device Provisioning Service (DPS) pomocí šablony ARM

K programovému nastavení cloudových prostředků Azure potřebných ke zřízení vašich zařízení můžete použít šablonu [Azure Resource Manager](../azure-resource-manager/management/overview.md) (šablonu ARM). Tyto kroky ukazují, jak vytvořit centrum IoT a nový IoT Hub Device Provisioning Service se šablonou ARM. Centrum IoT je také propojeno s prostředkem DPS pomocí šablony. Toto propojení umožňuje prostředku DPS přiřazovat zařízením do centra na základě zásad přidělování, které nakonfigurujete.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

V tomto rychlém startu se používá [Azure Portal](../azure-resource-manager/templates/deploy-portal.md)a [Azure CLI](../azure-resource-manager/templates/deploy-cli.md) k provádění programových kroků nezbytných k vytvoření skupiny prostředků a nasazení šablony, ale můžete snadno použít [prostředí PowerShell](../azure-resource-manager/templates/deploy-powershell.md), .NET, Ruby nebo jiné programovací jazyky k provedení těchto kroků a nasazení šablony. 

Pokud vaše prostředí splňuje požadavky a už jste obeznámeni s používáním šablon ARM, kliknutím na níže uvedené tlačítko **nasadit do Azure** otevřete šablonu pro nasazení v Azure Portal.

[![Nasazení do Azure v přehledu](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-iothub-device-provisioning%2fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-iothub-device-provisioning/).

> [!NOTE]
> V současné době není k dispozici žádná podpora šablon ARM pro vytváření registrací s využitím nových prostředků DPS. Jedná se o běžnou a srozumitelnou žádost, která se považuje za implementaci.

:::code language="json" source="~/quickstart-templates/101-iothub-device-provisioning/azuredeploy.json":::

V šabloně výše jsou definované dva prostředky Azure:

* [**Microsoft. Devices/iothubs**](/azure/templates/microsoft.devices/iothubs): Vytvoří novou službu Azure IoT Hub.
* [**Microsoft. Devices/provisioningservices**](/azure/templates/microsoft.devices/provisioningservices): Vytvoří novou službu Azure IoT Hub Device Provisioning Service s novou IoT Hub již propojenou.


## <a name="deploy-the-template"></a>Nasazení šablony

#### <a name="deploy-with-the-portal"></a>Nasazení s využitím portálu

1. Vyberte následující obrázek pro přihlášení do Azure a otevřete šablonu pro nasazení. Šablona vytvoří nový prostředek IoT Hub a DPS. Centrum bude propojeno v prostředku DPS.

    [![Nasazení do Azure v krocích portálu](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-iothub-device-provisioning%2fazuredeploy.json)

2. Vyberte nebo zadejte následující hodnoty a klikněte na tlačítko **zkontrolovat + vytvořit**.

    ![Parametry nasazení šablony ARM na portálu](./media/quick-setup-auto-provision-rm/arm-template-deployment-parameters-portal.png)    

    Pokud není uvedeno níže, použijte výchozí hodnotu pro vytvoření prostředku IoT Hub a DPS.

    | Pole | Popis |
    | :---- | :---------- |
    | **Předplatné** | Vyberte své předplatné Azure. |
    | **Skupina prostředků** | Klikněte na **vytvořit nový** a zadejte jedinečný název pro skupinu prostředků a pak klikněte na **OK**. |
    | **Oblast** | Vyberte oblast pro vaše prostředky. Například **východní USA**. |
    | **Název centra IoT Hub** | Zadejte název IoT Hub, který musí být globálně jedinečný v rámci oboru názvů *. Azure-Devices.NET* . Název centra budete potřebovat v další části při ověřování nasazení. |
    | **Název služby zřizování** | Zadejte název nového prostředku služby Device Provisioning Service (DPS). Název musí být globálně jedinečný v rámci oboru názvů *. Azure-Devices-Provisioning.NET* . Název DPS budete potřebovat v další části při ověřování nasazení. |
    
3. Na další obrazovce si přečtěte článek s podmínkami. Pokud souhlasíte se všemi podmínkami, klikněte na **vytvořit**. 

    Dokončení nasazení bude chvíli trvat. 

    Kromě Azure Portal můžete použít také Azure PowerShell, Azure CLI a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../azure-resource-manager/templates/deploy-powershell.md).


#### <a name="deploy-with-the-azure-cli"></a>Nasazení pomocí Azure CLI

Použití rozhraní příkazového řádku Azure vyžaduje verzi 2,6 nebo novější. Pokud používáte Azure CLI místně, ověřte svoji verzi spuštěním: `az --version`

Přihlaste se ke svému účtu Azure a vyberte své předplatné.

1. Pokud používáte rozhraní příkazového řádku Azure CLI místně a nemusíte ho spouštět na portálu, budete se muset přihlásit. Pokud se chcete přihlásit z příkazového řádku, spusťte [příkaz pro přihlášení](/cli/azure/get-started-with-az-cli2):
    
    ```azurecli
    az login
    ```

    Postupujte podle pokynů pro ověření pomocí kódu a přihlaste se ke svému účtu Azure ve webovém prohlížeči.

2. Pokud máte více předplatných Azure, přihlášením k Azure získáte přístup ke všem účtům Azure přidruženým k vašim přihlašovacím údajům. Pomocí následujícího [příkazu zobrazte výpis účtů Azure](/cli/azure/account), které můžete použít:
    
    ```azurecli
    az account list -o table
    ```

    Pomocí následujícího příkazu vyberte předplatné, které chcete použít ke spuštění příkazů pro vytvoření služby IoT Hub a prostředků DPS. Můžete použít název nebo ID předplatného z výstupu předchozího příkazu:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Zkopírujte a vložte následující příkazy do příkazového řádku CLI. Pak spusťte příkazy stisknutím klávesy **ENTER**.
   
    > [!TIP]
    > Příkazy zobrazí výzvu k zadání umístění skupiny prostředků. Seznam dostupných umístění můžete zobrazit tak, že nejprve spustíte příkaz:
    >
    > `az account list-locations -o table`
    >
    >
    
    ```azurecli-interactive
    read -p "Enter a project name that is used for generating resource names:" projectName &&
    read -p "Enter the location (i.e. centralus):" location &&
    templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-device-provisioning/azuredeploy.json" &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
    echo "Press [ENTER] to continue ..." &&
    read
    ```

4. V příkazech se zobrazí výzva k zadání následujících informací. Zadejte každou hodnotu a stiskněte klávesu **ENTER**.

    | Parametr | Popis |
    | :-------- | :---------- |
    | **Název projektu** | Hodnota tohoto parametru se použije k vytvoření skupiny prostředků pro uložení všech prostředků. Řetězec `rg` se přidá na konec hodnoty pro název skupiny prostředků. |
    | **oblasti** | Tato hodnota je oblast, kde se budou nacházet všechny prostředky. |
    | **iotHubName** | Zadejte název IoT Hub, který musí být globálně jedinečný v rámci oboru názvů *. Azure-Devices.NET* . Název centra budete potřebovat v další části při ověřování nasazení. |
    | **provisioningServiceName** | Zadejte název nového prostředku služby Device Provisioning Service (DPS). Název musí být globálně jedinečný v rámci oboru názvů *. Azure-Devices-Provisioning.NET* . Název DPS budete potřebovat v další části při ověřování nasazení. |

    Azure CLI se používá k nasazení šablony. Kromě rozhraní příkazového řádku Azure můžete také použít Azure PowerShell, Azure Portal a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../azure-resource-manager/templates/deploy-powershell.md).


## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

1. Pokud chcete toto nasazení ověřit, spusťte následující [příkaz, který vypíše prostředky](/cli/azure/resource#az_resource_list) a vyhledá novou službu zřizování a centrum IoT ve výstupu:

    ```azurecli
     az resource list -g "${projectName}rg"
    ```

2. Chcete-li ověřit, zda je rozbočovač již připojen k prostředku DPS, spusťte následující [příkaz pro rozšíření DPS](/cli/azure/iot/dps#az_iot_dps_show).

    ```azurecli
     az iot dps show --name <Your provisioningServiceName>
    ```

    Všimněte si rozbočovačů, které jsou propojeny na `iotHubs` členu.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty v této kolekci jsou postavené na tomto rychlém startu. Pokud chcete pokračovat v práci s dalšími rychlými starty nebo kurzy, nevyčišťujte prostředky vytvořené v rámci tohoto rychlého startu. Pokud pokračovat nechcete, můžete pomocí Azure Portal nebo Azure CLI odstranit skupinu prostředků a všechny její prostředky.

Pokud chcete odstranit skupinu prostředků a všechny její prostředky z Azure Portal, stačí otevřít skupinu prostředků a kliknout na **Odstranit skupinu prostředků** a horní.

Pokud chcete odstranit skupinu prostředků nasazenou pomocí Azure CLI:

```azurecli
az group delete --name "${projectName}rg"
```

Skupiny prostředků a jednotlivé prostředky můžete také odstranit pomocí Azure Portal, PowerShellu nebo rozhraní REST API a také pomocí podporovaných sad SDK platformy publikovaných pro Azure Resource Manager nebo IoT Hub Device Provisioning Service.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili službu IoT Hub a instanci služby Device Provisioning a propojili jste tyto dva prostředky. Informace o tom, jak pomocí tohoto nastavení zřídit zařízení, můžete pokračovat v rychlém startu pro vytvoření zařízení.

> [!div class="nextstepaction"]
> [Rychlý Start k zřízení zařízení](./quick-create-simulated-device-symm-key.md)
