---
title: Zaregistrujte nové zařízení Azure IoT Edge (CLI) | Dokumentace Microsoftu
description: Použití rozšíření IoT pro Azure CLI 2.0 k registraci nového zařízení IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 07/27/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 451f4df31cd1c520b14227829923f72fe80c38c3
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325492"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli-20"></a>Zaregistrujte nové zařízení Azure IoT Edge pomocí Azure CLI 2.0

Než budete moct použít zařízení IoT pomocí Azure IoT Edge, budete muset zaregistrovat u služby IoT hub. Po registraci zařízení se zobrazí připojovací řetězec, který slouží k nastavení zařízení pro hraniční úlohy. 

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) je open source nástroj příkazového řádku pro různé platformy určený ke správě prostředků Azure, jako je služba IoT Edge. Umožňuje spravovat prostředky Azure IoT Hub, instance služby device provisioning a propojená centra úprav. Nové rozšíření IoT vylepšuje rozhraní Azure CLI 2.0 o funkce, jako je správa zařízení a plně funkční IoT Edge.

Tento článek ukazuje, jak registrovat nová zařízení IoT Edge pomocí Azure CLI 2.0.

## <a name="prerequisites"></a>Požadavky

* [Služby IoT hub](../iot-hub/iot-hub-create-using-cli.md) ve vašem předplatném Azure. 
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) ve vašem prostředí. Vaše verze Azure CLI 2.0 musí být minimálně 2.0.24 nebo novější. Ke kontrole použijte příkaz `az –-version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack. 
* [Rozšíření IoT pro Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>Vytvoření zařízení

Následujícím příkazem vytvořte novou identitu zařízení ve službě IoT hub: 

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Tento příkaz zahrnuje tři parametry:
* **id zařízení**: Zadejte popisný název, který je jedinečný pro své Centrum IoT.
* **název centra**: Zadejte název služby IoT hub.
* **povolené Edge**: Tento parametr deklaruje, že zařízení je pro použití se službou IoT Edge.

   ![Vytvoření zařízení IoT Edge](./media/how-to-register-device-cli/Create-edge-device.png)

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

Parametr id zařízení rozlišuje velká a malá písmena. Nekopírujte uvozovky kolem připojovací řetězec. 

## <a name="next-steps"></a>Další postup

Zjistěte, jak [nasadit moduly na zařízení s Azure CLI 2.0](how-to-deploy-modules-cli.md)