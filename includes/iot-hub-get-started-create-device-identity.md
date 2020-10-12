---
title: zahrnout soubor
description: zahrnout soubor
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 68260bf8aafbbe5afd46ec7dfb763eb88ee2123e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78892952"
---
V této části pomocí Azure CLI vytvoříte identitu zařízení pro tento článek. V ID zařízení se rozlišují malá a velká písmena.

1. Otevřete [Azure Cloud Shell](https://shell.azure.com/).

1. V Azure Cloud Shell spusťte následující příkaz, který nainstaluje Microsoft Azure IoT rozšíření pro Azure CLI:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. Vytvořte novou identitu zařízení s názvem `myDeviceId` a načtěte připojovací řetězec zařízení pomocí těchto příkazů:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Poznamenejte si připojovací řetězec zařízení z výsledku. V aplikaci pro zařízení se používá připojovací řetězec zařízení pro připojení k vašemu IoT Hub jako zařízení.

<!-- images and links -->
