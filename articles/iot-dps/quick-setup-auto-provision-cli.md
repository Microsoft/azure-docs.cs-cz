---
title: Nastavení služby Device Provisioning pomocí Azure CLI | Microsoft Docs
description: Rychlý start Azure – Nastavení služby Azure IoT Hub Device Provisioning pomocí Azure CLI
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: c9e3bbbc4fbe8a9aade3364d6cbe9e93b5798595
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "42023060"
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Nastavení služby Azure IoT Hub Device Provisioning pomocí Azure CLI

Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. Tento rychlý start podrobně popisuje použití Azure CLI k vytvoření centra IoT a služby IoT Hub Device Provisioning a jejich vzájemné propojení. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!IMPORTANT]
> Centrum IoT i služba zřizování, které v tomto rychlém startu vytvoříte, budou veřejně zjistitelné jako koncové body DNS. Pokud se rozhodnete změnit názvy těchto prostředků, ujistěte se, že nepoužíváte žádné citlivé údaje.
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

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Vytvořte centrum IoT pomocí příkazu [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create).

Následující příklad vytvoří centrum IoT *my-sample-hub* v umístění *westus*.  

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-provisioning-service"></a>Vytvoření služby zřizování

Vytvořte službu zřizování pomocí příkazu [az iot dps create](/cli/azure/iot/dps#az-iot-dps-create). 

Následující příklad vytvoří službu zřizování *my-sample-dps* v umístění *westus*.  

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> Tento příklad vytvoří službu zřizování v umístění Západní USA. Seznam dostupných umístění můžete zobrazit spuštěním příkazu `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` nebo na stránce [Stav Azure](https://azure.microsoft.com/status/) vyhledáním výrazu Služba Device Provisioning. V příkazech je možné zadat umístění v jednoslovném nebo víceslovném formátu, například: westus, USA – západ, Západní USA atd. V hodnotě se nerozlišují malá a velká písmena. Pokud k zadání umístění použijete víceslovný formát, uveďte hodnotu v uvozovkách, například `-- location "West US"`.
>


## <a name="get-the-connection-string-for-the-iot-hub"></a>Získání připojovacího řetězce pro centrum IoT

K propojení vašeho centra IoT se službou Device Provisioning potřebujete připojovací řetězec centra IoT. Získejte připojovací řetězec pomocí příkazu [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string) a výstup příkazu použijte k nastavení proměnné, kterou použijete při propojování těchto dvou prostředků. 

Následující příklad nastaví proměnnou *hubConnectionString* na hodnotu připojovacího řetězce pro primární klíč zásady *iothubowner* centra. Pomocí parametru `--policy-name` můžete zadat jinou zásadu. Tento příkaz pomocí možností [query](/cli/azure/query-azure-cli) a [output](/cli/azure/format-output-azure-cli#tsv-output-format) v Azure CLI extrahuje připojovací řetězec z výstupu příkazu.

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

Následující příklad propojí centrum IoT *my-sample-hub* v umístění *westus* se službou Device Provisioning *my-sample-dps*. Pro *my-sample-hub* používá připojovací řetězec uložený v předchozím kroku do proměnné *hubConnectionString*.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

## <a name="verify-the-provisioning-service"></a>Ověření služby zřizování

Získejte podrobnosti o své službě zřizování pomocí příkazu [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show).

Následující příklad získá podrobnosti o službě zřizování *my-sample-dps*. Propojené centrum IoT se zobrazí v kolekci *properties.iotHubs*.

```azurecli-interactive
az iot dps show --name my-sample-dps
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty v této kolekci jsou postavené na tomto rychlém startu. Pokud chcete pokračovat v práci s dalšími rychlými starty nebo kurzy, nevyčišťujte prostředky vytvořené v rámci tohoto rychlého startu. Pokud pokračovat nechcete, můžete pomocí následujících příkazů odstranit službu zřizování, centrum IoT nebo skupinu prostředků a všechny její prostředky.

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

V tomto rychlém startu jste nasadili centrum IoT a instanci služby Device Provisioning a propojili jste tyto dva prostředky. Pokud chcete zjistit, jak pomocí tohoto nastavení zřídit simulované zařízení, pokračujte k rychlému startu pro vytvoření simulovaného zařízení.

> [!div class="nextstepaction"]
> [Rychlý start k vytvoření simulovaného zařízení](./quick-create-simulated-device.md)

