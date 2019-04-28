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
ms.openlocfilehash: 40a5416f15b0e2d66d6ce4b4787573560ee4af00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60346121"
---
## <a name="create-a-device-identity"></a>Vytvoření identity zařízení

V této části použijete rozhraní příkazového řádku Azure k vytvoření identity zařízení pro účely tohoto kurzu. Rozhraní příkazového řádku Azure je předinstalován v [Azure Cloud Shell](~/articles/cloud-shell/overview.md), nebo můžete [nainstalujte ho místně](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). V ID zařízení se rozlišují malá a velká písmena.

1. Spusťte následující příkaz prostředí příkazového řádku, kde jsou k instalaci rozšíření IoT pomocí Azure CLI:

    ```cmd/sh
    az extension add --name azure-cli-iot-ext
    ```

1. Pokud používáte Azure CLI místně, použijte následující příkaz pro přihlášení ke svému účtu Azure (Pokud používáte Cloud Shell, jste přihlášení automaticky a není nutné ke spuštění tohoto příkazu):

    ```cmd/sh
    az login
    ```

1. Nakonec vytvořte novou identitu zařízení volá `myDeviceId` a načíst připojovací řetězec zařízení pomocí následujících příkazů:

    ```cmd/sh
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Poznamenejte si připojovací řetězec zařízení z výsledku. Tento připojovací řetězec zařízení používá aplikace pro zařízení pro připojení ke službě IoT Hub jako zařízení.

<!-- images and links -->
