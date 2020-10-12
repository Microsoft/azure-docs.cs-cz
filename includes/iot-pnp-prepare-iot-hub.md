---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/17/2020
ms.custom: references_regions
ms.openlocfilehash: adc621f932462422202d9f16fd539f5ecc7c3d8e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87336888"
---
## <a name="prepare-an-iot-hub"></a>Příprava služby IoT Hub

Abyste mohli dokončit kroky v tomto článku, potřebujete ve svém předplatném Azure službu Azure IoT Hub. Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

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

> [!NOTE]
> Služba IoT technologie Plug and Play je aktuálně dostupná ve službě IoT Hub vytvořených v oblastech Střed USA, Severní Evropa a Východní Japonsko. Podpora IoT technologie Plug and Play není součástí centra IoT na úrovni Basic.

Spuštěním následujícího příkazu vytvořte identitu zařízení ve službě IoT Hub. `<YourIoTHubName>`Zástupné symboly a nahraďte `<YourDeviceID>` vlastním _názvem IoT Hub_ a _ID zařízení_ podle vašeho výběru.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```
