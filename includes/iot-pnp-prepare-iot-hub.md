---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/17/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: ea5d4ef26fb14e22b871bb4bfa1054cb749d38e8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97673017"
---
## <a name="prepare-an-iot-hub"></a>Příprava služby IoT Hub

Abyste mohli dokončit kroky v tomto článku, potřebujete ve svém předplatném Azure službu Azure IoT Hub. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Pokud používáte Azure CLI místně, přihlaste se ke svému předplatnému Azure pomocí `az login` . Pokud tyto příkazy spouštíte v Azure Cloud Shell, jste přihlášeni automaticky.

Pokud používáte Azure CLI místně, `az` verze by měla být **2.8.0** nebo novější; Azure Cloud Shell používá nejnovější verzi. Pomocí `az --version` příkazu ověřte verzi nainstalovanou na vašem počítači.

Spuštěním následujícího příkazu přidejte do své instance Microsoft Azure rozšíření IoT pro Azure CLI:

```azurecli-interactive
az extension add --name azure-iot
```

Pokud ještě služby IoT Hub nemáte k dispozici, spusťte následující příkazy, abyste ve svém předplatném vytvořili skupinu prostředků a centrum IoT ve volné vrstvě. Nahraďte `<YourIoTHubName>` názvem centra dle vašeho výběru:

```azurecli-interactive
az group create --name my-pnp-resourcegroup \
    --location centralus
az iot hub create --name <YourIoTHubName> \
    --resource-group my-pnp-resourcegroup --sku F1
```

Spuštěním následujícího příkazu vytvořte identitu zařízení ve službě IoT Hub. `<YourIoTHubName>`Zástupné symboly a nahraďte `<YourDeviceID>` vlastním _názvem IoT Hub_ a _ID zařízení_ podle vašeho výběru.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```
