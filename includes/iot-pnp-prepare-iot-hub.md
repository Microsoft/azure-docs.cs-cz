---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: a3340eb9e53afa83c35109bad7d22f81413dd644
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80234221"
---
## <a name="prepare-an-iot-hub"></a>Příprava služby IoT Hub

K dokončení tohoto rychlého startu budete také potřebovat službu Azure IoT Hub v rámci vašeho předplatného Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. Pokud Centrum IoT nemáte, [vytvořte ho podle těchto pokynů](../articles/iot-hub/iot-hub-create-using-cli.md).

Pokud používáte Azure CLI místně, přihlaste se ke svému předplatnému Azure pomocí `az login`. Pokud tyto příkazy spouštíte v Azure Cloud Shell, jste přihlášeni automaticky.

Pokud používáte Azure CLI místně, `az` verze by měla být **2.0.73** nebo novější. Azure Cloud Shell používá nejnovější verzi. Pomocí `az --version` příkazu ověřte verzi nainstalovanou na vašem počítači.

Spuštěním následujícího příkazu přidejte do své instance Microsoft Azure rozšíření IoT pro Azure CLI:

```azurecli-interactive
az extension add --name azure-iot
```

Spuštěním následujícího příkazu vytvořte identitu zařízení ve službě IoT Hub. Zástupné symboly **YourIoTHubName** a **YourDeviceID** nahraďte vlastním _názvem IoT Hub_ a _ID zařízení_ podle vašeho výběru.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Spuštěním následujícího příkazu Získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali (Poznámka pro pozdější použití):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```
