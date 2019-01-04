---
title: Zaregistrujte si nové zařízení z příkazového řádku – Azure IoT Edge | Dokumentace Microsoftu
description: Použití rozšíření IoT pro rozhraní příkazového řádku Azure k registraci nového zařízení IoT Edge a načtení připojovacího řetězce
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/03/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 45b05498702042c931df3765b9e1bd79489dbb6e
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53972137"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli"></a>Pomocí Azure CLI zaregistrujte nové zařízení Azure IoT Edge

Než budete moct použít zařízení IoT pomocí Azure IoT Edge, budete muset zaregistrovat u služby IoT hub. Po registraci zařízení se zobrazí připojovací řetězec, který slouží k nastavení zařízení pro hraniční úlohy.

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) je open source pro různé platformy nástroj příkazového řádku pro správu prostředků Azure, jako je například IoT Edge. Umožňuje spravovat prostředky Azure IoT Hub, instance služby device provisioning a propojená centra úprav. Nové rozšíření IoT vylepšuje Azure CLI s funkcemi, jako je Správa zařízení a úplné možnosti služby IoT Edge.

Tento článek ukazuje, jak registrovat nová zařízení IoT Edge pomocí Azure CLI.

## <a name="prerequisites"></a>Požadavky

* [Služby IoT hub](../iot-hub/iot-hub-create-using-cli.md) ve vašem předplatném Azure.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) ve vašem prostředí. Minimálně musí být vaše Azure CLI verze 2.0.24 nebo novější. Ke kontrole použijte příkaz `az –-version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack.
* [Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>Vytvoření zařízení

Následujícím příkazem vytvořte novou identitu zařízení ve službě IoT hub:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Tento příkaz zahrnuje tři parametry:

* **id zařízení**: Zadejte popisný název, který je jedinečný pro své Centrum IoT.

* **název centra**: Zadejte název služby IoT hub.

* **povolené Edge**: Tento parametr deklaruje, že zařízení je pro použití se službou IoT Edge.

   ![vytvořit výstup, az iot hub-identity zařízení](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>Zobrazit všechna zařízení

Pomocí následujícího příkazu zobrazíte všechna zařízení ve službě IoT hub:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Jakékoli zařízení, které je zaregistrované jako zařízení IoT Edge bude mít vlastnost **capabilities.iotEdge** nastavena na **true**.

## <a name="retrieve-the-connection-string"></a>Načtení připojovacího řetězce

Až budete připravení nastavit vaše zařízení, budete potřebovat připojovací řetězec, který propojí vaše fyzické zařízení do jeho identitu ve službě IoT hub. Chcete-li vrátit připojovací řetězec pro jedno zařízení, použijte následující příkaz:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

Hodnota `device-id` parametr je velká a malá písmena. Nekopírujte uvozovky kolem připojovací řetězec.

## <a name="next-steps"></a>Další postup

Zjistěte, jak [nasadit moduly do zařízení pomocí Azure CLI](how-to-deploy-modules-cli.md)
