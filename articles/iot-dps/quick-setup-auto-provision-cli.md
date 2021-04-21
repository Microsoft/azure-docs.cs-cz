---
title: Rychlý Start – Nastavení IoT Hub Device Provisioning Service Azure pomocí Azure CLI
description: Rychlý Start – Nastavení Azure IoT Hub Device Provisioning Service (DPS) pomocí rozhraní příkazového řádku Azure
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 3d52a83c8c0920c4d85aa5b4b6b89fd8d36e5fea
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774948"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Rychlý Start: nastavení IoT Hub Device Provisioning Service pomocí Azure CLI

Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. Tento rychlý Start podrobně popisuje použití Azure CLI k vytvoření centra IoT a IoT Hub Device Provisioning Service a k propojení obou služeb dohromady. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Služba IoT Hub i služba zřizování, které vytvoříte v tomto rychlém startu, budou veřejně zjistitelné jako koncové body DNS. Pokud se rozhodnete změnit názvy těchto prostředků, ujistěte se, že nepoužíváte žádné citlivé údaje.
>

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Následující příklad vytvoří skupinu prostředků *my-sample-resource-group* v umístění *westus*.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> Tento příklad vytvoří skupinu prostředků v umístění USA – západ. Seznam dostupných umístění můžete zobrazit spuštěním příkazu `az account list-locations -o table`.
>
>

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Vytvořte centrum IoT pomocí příkazu [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create).

Následující příklad vytvoří centrum IoT *my-sample-hub* v umístění *westus*. Název služby IoT Hub musí být globálně jedinečný v Azure, takže můžete chtít přidat jedinečnou předponu nebo příponu k názvu příkladu nebo úplně zvolit nový název. Ujistěte se, že název splňuje správné konvence pojmenování pro IoT Hub: musí mít 3-50 znaků a může obsahovat jenom horní nebo malé alfanumerické znaky nebo spojovníky (-). 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Vytvořit službu Device Provisioning Service

Pomocí příkazu [AZ IoT DPS Create](/cli/azure/iot/dps#az_iot_dps_create) vytvořte službu Device Provisioning. 

Následující příklad vytvoří službu zřizování s názvem *My-Sample-DPS* v umístění *westus* . Budete také muset zvolit globálně jedinečný název pro vlastní službu zřizování. Ujistěte se, že splňuje správné konvence pojmenování pro IoT Hub Device Provisioning Service: mělo by být 3-64 znaků a může obsahovat pouze velké nebo malé alfanumerické znaky nebo spojovníky (-).

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> Tento příklad vytvoří službu zřizování v umístění USA – západ. Seznam dostupných umístění můžete zobrazit spuštěním příkazu `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` nebo na stránce [Stav Azure](https://azure.microsoft.com/status/) vyhledáním výrazu Služba Device Provisioning. V příkazech lze umístění zadat buď v jednom nebo více slovovém formátu; například: westus, Západní USA, západ USA atd. Hodnota nerozlišuje velká a malá písmena. Pokud k zadání umístění použijete víceslovný formát, uveďte hodnotu v uvozovkách, například `--location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>Získání připojovacího řetězce pro centrum IoT

K propojení vašeho centra IoT se službou Device Provisioning potřebujete připojovací řetězec centra IoT. Získejte připojovací řetězec pomocí příkazu [az iot hub show-connection-string](/cli/azure/iot/hub#az_iot_hub_show_connection_string) a výstup příkazu použijte k nastavení proměnné, kterou použijete při propojování těchto dvou prostředků. 

Následující příklad nastaví proměnnou *hubConnectionString* na hodnotu připojovacího řetězce pro primární klíč zásad *iothubownere* centra ( `--policy-name` parametr lze použít k určení jiné zásady). Využívejte si pro jedinečný název služby IoT Hub, který jste zvolili v části *můj ukázkový hub* . Tento příkaz pomocí možností [query](/cli/azure/query-azure-cli) a [output](/cli/azure/format-output-azure-cli#tsv-output-format) v Azure CLI extrahuje připojovací řetězec z výstupu příkazu.

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

Připojovací řetězec můžete zobrazit pomocí příkazu `echo`.

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> Tyto dva příkazy jsou platné pro hostitele spuštěného v prostředí Bash.
> 
> Pokud používáte místní prostředí Windows/CMD nebo hostitele PowerShellu, upravte příkazy tak, aby pro toto prostředí používaly správnou syntaxi.
>
> Pokud používáte Azure Cloud Shell, ověřte, že se v rozevíracím seznamu prostředí na levé straně okna prostředí říká **bash**.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Propojení centra IoT a služby zřizování

Propojte centrum IoT a službu zřizování pomocí příkazu [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az_iot_dps_linked_hub_create). 

Následující příklad propojuje službu IoT Hub s názvem *My-Sample-hub* v umístění *Westus* a službou Device Provisioning s názvem *My-Sample-DPS*. Využívejte si tyto názvy pro jedinečné názvy služeb IoT Hub a Device Provisioning, které jste si zvolili dříve. Příkaz používá připojovací řetězec pro Centrum IoT, který byl uložen v proměnné *hubConnectionString* v předchozím kroku.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

Dokončení příkazu může trvat několik minut.

## <a name="verify-the-provisioning-service"></a>Ověření služby zřizování

Získejte podrobnosti o své službě zřizování pomocí příkazu [az iot dps show](/cli/azure/iot/dps#az_iot_dps_show).

Následující příklad získá podrobnosti o službě zřizování *my-sample-dps*. Využívejte si tento název pro vlastní název služby Device Provisioning.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
Propojené centrum IoT se zobrazí v kolekci *properties.iotHubs*.

![Ověřit službu zřizování](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty v této kolekci jsou postavené na tomto rychlém startu. Pokud chcete pokračovat v práci s dalšími rychlými starty nebo kurzy, nevyčišťujte prostředky vytvořené v rámci tohoto rychlého startu. Pokud pokračovat nechcete, můžete pomocí následujících příkazů odstranit službu zřizování, centrum IoT nebo skupinu prostředků a všechny její prostředky. Názvy prostředků napsaných níže nahraďte názvy vašich vlastních prostředků.

Pokud chcete odstranit službu zřizování, spusťte příkaz [az iot dps delete](/cli/azure/iot/dps#az_iot_dps_delete):

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Pokud chcete odstranit centrum IoT, spusťte příkaz [az iot hub delete](/cli/azure/iot/hub#az_iot_hub_delete):

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Pokud chcete odstranit skupinu prostředků a všechny její prostředky, spusťte příkaz [az group delete](/cli/azure/group#az_group_delete):

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili službu IoT Hub a instanci služby Device Provisioning a propojili jste tyto dva prostředky. Informace o tom, jak pomocí tohoto nastavení zřídit simulované zařízení, najdete v rychlém startu pro vytvoření simulovaného zařízení.

> [!div class="nextstepaction"]
> [Rychlý Start k vytvoření simulovaného zařízení](./quick-create-simulated-device.md)
