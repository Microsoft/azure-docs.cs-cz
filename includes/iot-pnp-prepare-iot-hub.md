---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: a3340eb9e53afa83c35109bad7d22f81413dd644
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80234221"
---
## <a name="prepare-an-iot-hub"></a>Příprava centra IoT hub

K dokončení tohoto rychlého startu potřebujete také azure ioT hub ve vašem předplatném Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete. Pokud nemáte centrum IoT hub, postupujte [podle těchto pokynů k vytvoření .](../articles/iot-hub/iot-hub-create-using-cli.md)

Pokud používáte azure cli místně, nejprve se přihlásit `az login`k předplatnému Azure pomocí . Pokud tyto příkazy spouštěte ve službě Azure Cloud Shell, jste přihlášení automaticky.

Pokud používáte Azure CLI místně, `az` verze by měla být **2.0.73** nebo novější; Azure Cloud Shell používá nejnovější verzi. Pomocí `az --version` příkazu zkontrolujte verzi nainstalovanou v počítači.

Spuštěním následujícího příkazu přidejte rozšíření Microsoft Azure IoT extension pro rozhraní příkazového příkazu Azure do instance:

```azurecli-interactive
az extension add --name azure-iot
```

Spusťte následující příkaz a vytvořte identitu zařízení ve vašem centru IoT Hub. Nahraďte zástupné symboly **YourIoTHubName** a **YourDeviceID** vlastním _názvem centra IoT hub_ a _ID zařízení_ podle vašeho výběru.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Spuštěním následujícího příkazu získáte _připojovací řetězec zařízení_ pro právě registrované zařízení (poznámka pro pozdější použití):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```
