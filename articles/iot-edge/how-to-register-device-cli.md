---
title: Registrovat nové zařízení Azure IoT okraj (CLI) | Microsoft Docs
description: Použití rozšíření IoT 2.0 rozhraní příkazového řádku Azure k registraci nového zařízení IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 05/30/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a6935dfe5b32364e402017cd3005ab17969ce1ce
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035788"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli-20"></a>Registrovat nové zařízení Azure IoT Edge s Azure CLI 2.0

Zařízení IoT můžete používat s Azure IoT Edge, budete muset registraci je službou IoT hub. Po registraci zařízení se zobrazí připojovací řetězec, který umožňuje nastavit zařízení pro úlohy okraj. 

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) je open source nástroj příkazového řádku pro různé platformy určený ke správě prostředků Azure, jako je služba IoT Edge. Umožňuje spravovat prostředky Azure IoT Hub, instance služby zřizování zařízení a propojené centra mimo pole. Nové rozšíření IoT vylepšuje rozhraní Azure CLI 2.0 o funkce, jako je správa zařízení a plně funkční IoT Edge.

Tento článek ukazuje, jak registrovat nové zařízení IoT Edge pomocí Azure CLI 2.0.

## <a name="prerequisites"></a>Požadavky

* [Služby IoT hub](../iot-hub/iot-hub-create-using-cli.md) ve vašem předplatném Azure. 
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) ve vašem prostředí. Vaše verze Azure CLI 2.0 musí být minimálně 2.0.24 nebo novější. Ke kontrole použijte příkaz `az –-version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack. 
* [IoT rozšíření pro Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>Vytvoření zařízení

Chcete-li vytvořit novou identitu zařízení ve službě IoT hub, použijte následující příkaz: 

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Tento příkaz zahrnuje tři parametry:
* **id zařízení**: Zadejte popisný název, který je jedinečný do služby IoT hub.
* **název centra**: Zadejte název své služby IoT hub.
* **povolené hraniční**: Tento parametr deklaruje, že zařízení je pro použití se službou IoT okraj.

   ![Vytvoření zařízení IoT Edge](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>Zobrazit všechna zařízení

Chcete-li zobrazit všechna zařízení ve službě IoT hub, použijte následující příkaz:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Jakékoli zařízení, které je registrované, IoT hraniční zařízení bude mít vlastnost **capabilities.iotEdge** nastavena na **true**. 

## <a name="retrieve-the-connection-string"></a>Načtení připojovacího řetězce

Pokud jste připravení nastavit vaše zařízení, je potřeba připojovací řetězec, který odkazuje fyzického zařízení s svou identitu ve službě IoT hub. Vrátit připojovací řetězec pro jedno zařízení, použijte následující příkaz:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name] 
   ```

Parametr id zařízení rozlišuje velká a malá písmena. 

## <a name="next-steps"></a>Další postup

Zjistěte, jak [moduly nasadit do zařízení s Azure CLI 2.0](how-to-deploy-modules-cli.md)