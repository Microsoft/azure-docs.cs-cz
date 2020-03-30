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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78892952"
---
V této části použijete rozhraní příkazového příkazu K vytvoření identity zařízení pro tento článek. V ID zařízení se rozlišují malá a velká písmena.

1. Otevřete službu [Azure Cloud Shell](https://shell.azure.com/).

1. V Prostředí Azure Cloud Shell spusťte následující příkaz k instalaci rozšíření Microsoft Azure IoT Extension pro rozhraní příkazového příkazu Azure:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. Vytvořte novou identitu `myDeviceId` zařízení volanou a načtěte připojovací řetězec zařízení pomocí těchto příkazů:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Poznamenejte si připojovací řetězec zařízení z výsledku. Tento připojovací řetězec zařízení používá aplikace zařízení pro připojení k centru IoT Hub jako zařízení.

<!-- images and links -->
