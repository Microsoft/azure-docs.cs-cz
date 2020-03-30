---
title: Nastavení služby Azure IoT Hub Deviceprovisioning Service pomocí rozhraní příkazového příkazu Azure
description: Úvodní příručka – nastavení služby Azure IoT Hub DeviceProvisioning Service (DPS) pomocí rozhraní příkazového příkazového příkazu Azure
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: ea1cae1f5a30d4cd76df39fec43f3818178fc213
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77484192"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Úvodní příručka: Nastavení služby zřizování zařízení služby IoT Hub pomocí rozhraní příkazového příkazového příkazu Azure

Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. Tento podrobný start pomocí rozhraní příkazového příkazového příkazu k Azure k vytvoření služby IoT hub a služby zřizování zařízení služby IoT Hub a k propojení obou služeb dohromady. 

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

> [!IMPORTANT]
> Centrum IoT a zřizovací služba, kterou vytvoříte v tomto rychlém startu, budou veřejně zjistitelné jako koncové body DNS. Pokud se rozhodnete změnit názvy těchto prostředků, ujistěte se, že nepoužíváte žádné citlivé údaje.
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Následující příklad vytvoří skupinu prostředků *my-sample-resource-group* v umístění *westus*.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> Tento příklad vytvoří skupinu prostředků v umístění USA – západ. Seznam dostupných umístění můžete zobrazit spuštěním příkazu `az account list-locations -o table`.
>
>

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Vytvořte centrum IoT pomocí příkazu [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create).

Následující příklad vytvoří centrum IoT *my-sample-hub* v umístění *westus*. Název centra IoT musí být v Azure globálně jedinečný, takže můžete k názvu příkladu přidat jedinečnou předponu nebo příponu nebo zvolit úplně nový název. Ujistěte se, že vaše jméno se řídí správné konvence pojmenování pro centrum IoT: by měla být 3-50 znaků na délku a může obsahovat pouze velká nebo malá písmena alfanumerické znaky nebo pomlčky ('-'). 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Vytvoření služby zřizování zařízení

Vytvořte službu zřizování zařízení pomocí příkazu [az iot dps create.](/cli/azure/iot/dps#az-iot-dps-create) 

Následující příklad vytvoří zřizovací službu s názvem *my-sample-dps* v umístění *westus.* Budete také muset zvolit globálně jedinečný název pro vlastní zřizovací služby. Ujistěte se, že se řídí správné konvence pojmenování pro službu zřizování zařízení služby IoT Hub: měla by mít délku 3 až 64 znaků a může obsahovat pouze velká nebo malá alfanumerická písmena nebo pomlčky ('-").

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> Tento příklad vytvoří službu zřizování v umístění USA – západ. Seznam dostupných umístění můžete zobrazit spuštěním příkazu `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` nebo na stránce [Stav Azure](https://azure.microsoft.com/status/) vyhledáním výrazu Služba Device Provisioning. V příkazech lze umístění zadat buď v jednom formátu slova nebo ve formátu s více slovy; například: westus, west US, WEST US atd. Hodnota není rozlišována malá a velká písmena. Pokud k zadání umístění použijete víceslovný formát, uveďte hodnotu v uvozovkách, například `--location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>Získání připojovacího řetězce pro centrum IoT

K propojení vašeho centra IoT se službou Device Provisioning potřebujete připojovací řetězec centra IoT. Získejte připojovací řetězec pomocí příkazu [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string) a výstup příkazu použijte k nastavení proměnné, kterou použijete při propojování těchto dvou prostředků. 

Následující příklad nastaví proměnnou *hubConnectionString* na hodnotu připojovacího řetězce pro primární klíč `--policy-name` *zásady iothubowner* centra (parametr lze použít k určení jiné zásady). Vyměňte *můj ukázkový rozbočovač* za jedinečný název centra IoT, který jste si vybrali dříve. Tento příkaz pomocí možností [query](/cli/azure/query-azure-cli) a [output](/cli/azure/format-output-azure-cli#tsv-output-format) v Azure CLI extrahuje připojovací řetězec z výstupu příkazu.

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

Následující příklad propojí centrum IoT s názvem *my-sample-hub* v umístění *westus* a službu zřizování zařízení s názvem *my-sample-dps*. Vyměňte tyto názvy za jedinečné názvy služby IoT hub a služby zřizování zařízení, které jste zvolili dříve. Příkaz používá připojovací řetězec pro váš rozbočovač IoT hub, který byl uložen v proměnné *hubConnectionString* v předchozím kroku.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

Dokončení příkazu může trvat několik minut.

## <a name="verify-the-provisioning-service"></a>Ověření služby zřizování

Získejte podrobnosti o své službě zřizování pomocí příkazu [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show).

Následující příklad získá podrobnosti o službě zřizování *my-sample-dps*. Vyměnítento název za svůj vlastní název služby Device Provisioning Service.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
Propojené centrum IoT se zobrazí v kolekci *properties.iotHubs*.

![Ověřit zřizovací službu](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty v této kolekci jsou postavené na tomto rychlém startu. Pokud chcete pokračovat v práci s dalšími rychlými starty nebo kurzy, nevyčišťujte prostředky vytvořené v rámci tohoto rychlého startu. Pokud pokračovat nechcete, můžete pomocí následujících příkazů odstranit službu zřizování, centrum IoT nebo skupinu prostředků a všechny její prostředky. Nahraďte názvy prostředků napsaných níže názvy vlastních zdrojů.

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

V tomto rychlém startu jste nasadili službu IoT hub a instanci služby Zřizování zařízení a propojili jste tyto dva prostředky. Chcete-li se dozvědět, jak pomocí tohoto nastavení zřídit simulované zařízení, pokračujte na rychlý start pro vytvoření simulované zařízení.

> [!div class="nextstepaction"]
> [Rychlý start pro vytvoření simulovaného zařízení](./quick-create-simulated-device.md)
