---
title: Set up Azure IoT Hub Device Provisioning Service using Azure CLI
description: Quickstart - Set up the Azure IoT Hub Device Provisioning Service using Azure CLI
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 6406929c3abc3612da2c27edc45e10fd84883d73
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228548"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Quickstart: Set up the IoT Hub Device Provisioning Service with Azure CLI

Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. This quickstart details using the Azure CLI to create an IoT hub and an IoT Hub Device Provisioning Service, and to link the two services together. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!IMPORTANT]
> Both the IoT hub and the provisioning service you create in this quickstart will be publicly discoverable as DNS endpoints. Pokud se rozhodnete změnit názvy těchto prostředků, ujistěte se, že nepoužíváte žádné citlivé údaje.
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Následující příklad vytvoří skupinu prostředků *my-sample-resource-group* v umístění *westus*.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> Tento příklad vytvoří skupinu prostředků v umístění Západní USA. Seznam dostupných umístění můžete zobrazit spuštěním příkazu `az account list-locations -o table`.
>
>

## <a name="create-an-iot-hub"></a>Vytvoření IoT Hubu

Vytvořte centrum IoT pomocí příkazu [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create).

Následující příklad vytvoří centrum IoT *my-sample-hub* v umístění *westus*. An IoT hub name must be globally unique in Azure, so you may want to add a unique prefix or suffix to the example name, or choose a new name altogether. Make sure your name follows proper naming conventions for an IoT hub: it should be 3-50 characters in length, and can contain only upper or lower case alphanumeric characters or hyphens ('-'). 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Create a Device Provisioning Service

Create a Device Provisioning Service with the [az iot dps create](/cli/azure/iot/dps#az-iot-dps-create) command. 

The following example creates a provisioning service named *my-sample-dps* in the *westus* location. You will also need to choose a globally unique name for your own provisioning service. Make sure it follows proper naming conventions for an IoT Hub Device Provisioning Service: it should be 3-64 characters in length and can contain only upper or lower case alphanumeric characters or hyphens ('-').

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> Tento příklad vytvoří službu zřizování v umístění Západní USA. Seznam dostupných umístění můžete zobrazit spuštěním příkazu `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` nebo na stránce [Stav Azure](https://azure.microsoft.com/status/) vyhledáním výrazu Služba Device Provisioning. In commands, locations can be specified either in one word or multi-word format; for example: westus, West US, WEST US, etc. The value is not case sensitive. Pokud k zadání umístění použijete víceslovný formát, uveďte hodnotu v uvozovkách, například `-- location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>Získání připojovacího řetězce pro centrum IoT

K propojení vašeho centra IoT se službou Device Provisioning potřebujete připojovací řetězec centra IoT. Získejte připojovací řetězec pomocí příkazu [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string) a výstup příkazu použijte k nastavení proměnné, kterou použijete při propojování těchto dvou prostředků. 

The following example sets the *hubConnectionString* variable to the value of the connection string for the primary key of the hub's *iothubowner* policy (the `--policy-name` parameter can be used to specify a different policy). Trade out *my-sample-hub* for the unique IoT hub name you chose earlier. Tento příkaz pomocí možností [query](/cli/azure/query-azure-cli) a [output](/cli/azure/format-output-azure-cli#tsv-output-format) v Azure CLI extrahuje připojovací řetězec z výstupu příkazu.

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

Připojovací řetězec můžete zobrazit pomocí příkazu `echo`.

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> Tyto dva příkazy jsou platné pro hostitele spuštěného v prostředí Bash. Pokud používáte místní prostředí příkazového řádku ve Windows nebo hostitele PowerShellu, musíte tyto příkazy upravit tak, aby používaly správnou syntaxi pro příslušné prostředí.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Propojení centra IoT a služby zřizování

Propojte centrum IoT a službu zřizování pomocí příkazu [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az-iot-dps-linked-hub-create). 

The following example links an IoT hub named *my-sample-hub* in the *westus* location and a Device Provisioning Service named *my-sample-dps*. Trade out these names for the unique IoT hub and Device Provisioning Service names you chose earlier. The command uses the connection string for your IoT hub that was stored in the *hubConnectionString* variable in the previous step.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

The command may take a few minutes to complete.

## <a name="verify-the-provisioning-service"></a>Ověření služby zřizování

Získejte podrobnosti o své službě zřizování pomocí příkazu [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show).

Následující příklad získá podrobnosti o službě zřizování *my-sample-dps*. Trade out this name for your own Device Provisioning Service name.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
Propojené centrum IoT se zobrazí v kolekci *properties.iotHubs*.

![Verify Provisioning Service](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty v této kolekci jsou postavené na tomto rychlém startu. Pokud chcete pokračovat v práci s dalšími rychlými starty nebo kurzy, nevyčišťujte prostředky vytvořené v rámci tohoto rychlého startu. Pokud pokračovat nechcete, můžete pomocí následujících příkazů odstranit službu zřizování, centrum IoT nebo skupinu prostředků a všechny její prostředky. Replace the names of the resources written below with the names of your own resources.

Pokud chcete odstranit službu zřizování, spusťte příkaz [az iot dps delete](/cli/azure/iot/dps#az-iot-dps-delete):

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Pokud chcete odstranit centrum IoT, spusťte příkaz [az iot hub delete](/cli/azure/iot/hub#az-iot-hub-delete):

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Pokud chcete odstranit skupinu prostředků a všechny její prostředky, spusťte příkaz [az group delete](/cli/azure/group#az-group-delete):

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Další kroky

In this quickstart, you’ve deployed an IoT hub and a Device Provisioning Service instance, and linked the two resources. To learn how to use this setup to provision a simulated device, continue to the quickstart for creating a simulated device.

> [!div class="nextstepaction"]
> [Quickstart to create a simulated device](./quick-create-simulated-device.md)
