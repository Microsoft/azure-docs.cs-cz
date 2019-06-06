---
title: Zaregistrujte si nové zařízení z příkazového řádku – Azure IoT Edge | Dokumentace Microsoftu
description: Použití rozšíření IoT pro rozhraní příkazového řádku Azure k registraci nového zařízení IoT Edge a načtení připojovacího řetězce
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: de00c317483da9bcd93bb2e2505350d787385925
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495394"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli"></a>Pomocí Azure CLI zaregistrujte nové zařízení Azure IoT Edge

Než budete moct použít zařízení IoT pomocí Azure IoT Edge, budete muset zaregistrovat u služby IoT hub. Po registraci zařízení se zobrazí připojovací řetězec, který slouží k nastavení zařízení pro úlohy IoT Edge.

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) je open source pro různé platformy nástroj příkazového řádku pro správu prostředků Azure, jako je například IoT Edge. Umožňuje spravovat prostředky Azure IoT Hub, instance služby device provisioning a propojená centra úprav. Nové rozšíření IoT vylepšuje Azure CLI s funkcemi, jako je Správa zařízení a úplné možnosti služby IoT Edge.

Tento článek ukazuje, jak registrovat nová zařízení IoT Edge pomocí Azure CLI.

## <a name="prerequisites"></a>Požadavky

* [Služby IoT hub](../iot-hub/iot-hub-create-using-cli.md) ve vašem předplatném Azure.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) ve vašem prostředí. Minimálně musí být vaše Azure CLI verze 2.0.24 nebo novější. Ke kontrole použijte příkaz `az –-version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack.
* [Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>Vytvoření zařízení

Použití [az iot hub-identity zařízení vytvořit](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) příkazu vytvoříte novou identitu zařízení ve službě IoT hub. Příklad:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Tento příkaz zahrnuje tři parametry:

* **device-id**: Zadejte popisný název, který je jedinečný pro své Centrum IoT.

* **název centra**: Zadejte název služby IoT hub.

* **edge-enabled**: Tento parametr deklaruje, že zařízení je pro použití se službou IoT Edge.

   ![vytvořit výstup, az iot hub-identity zařízení](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>Zobrazit všechna zařízení

Použití [az iot hub identitu zařízení seznamu](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) příkazu zobrazíte všechna zařízení ve službě IoT hub. Příklad:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Jakékoli zařízení, které je zaregistrované jako zařízení IoT Edge bude mít vlastnost **capabilities.iotEdge** nastavena na **true**.

## <a name="retrieve-the-connection-string"></a>Načtení připojovacího řetězce

Až budete připravení nastavit vaše zařízení, budete potřebovat připojovací řetězec, který propojí vaše fyzické zařízení do jeho identitu ve službě IoT hub. Použití [az iot hub identitu zařízení show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) příkaz, který vrátí připojovací řetězec pro jedno zařízení:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

Hodnota `device-id` parametr je velká a malá písmena. Nekopírujte uvozovky kolem připojovací řetězec.

## <a name="next-steps"></a>Další postup

Zjistěte, jak [nasadit moduly do zařízení pomocí Azure CLI](how-to-deploy-modules-cli.md).
