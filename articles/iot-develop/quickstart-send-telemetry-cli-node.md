---
title: Odesílání telemetrie zařízení do služby Azure IoT Hub rychlý Start (Node.js)
description: V tomto rychlém startu použijete sadu Azure IoT Hub Device SDK pro Node.js k odeslání telemetrie ze zařízení do služby IoT Hub.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: 0e1c99124228da9490abaa17ecc41b931631d9fb
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876971"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>Rychlý Start: odeslání telemetrie ze zařízení do služby IoT Hub (Node.js)

**Platí pro**: [vývoj aplikací pro zařízení](about-iot-develop.md#device-application-development)

V tomto rychlém startu se naučíte základní pracovní postup vývoje aplikací pro zařízení IoT. Pomocí Azure CLI vytvoříte Azure IoT Hub a simulované zařízení, potom pro přístup k zařízení a odeslání telemetrie do centra použijete sadu Azure IoT Node.js SDK.

## <a name="prerequisites"></a>Požadavky
- Pokud ještě nemáte předplatné Azure, [vytvořte ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ještě před tím, než začnete.
- Azure CLI Všechny příkazy v tomto rychlém startu můžete spustit pomocí Azure Cloud Shell interaktivního prostředí CLI, které běží v prohlížeči. Pokud používáte Cloud Shell, nemusíte nic instalovat. Pokud dáváte přednost používání rozhraní příkazového řádku místně, musíte použít Azure CLI verze 2.0.76 nebo novější. Pokud chcete zjistit verzi, spusťte příkaz az --version. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).
- [Node.js 10 +](https://nodejs.org). Pokud používáte Azure Cloud Shell, neaktualizujte nainstalovanou verzi Node.js. Azure Cloud Shell už má nejnovější Node.js verzi.

    Ověřte aktuální verzi Node.js na vývojovém počítači pomocí následujícího příkazu:

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>Odesílání zpráv pomocí sady Node.js SDK
V této části použijete sadu SDK Node.js k posílání zpráv ze simulovaného zařízení do služby IoT Hub. 

1. Otevřete nové okno terminálu. Pomocí tohoto terminálu nainstalujete sadu Node.js SDK a budete pracovat s ukázkovým kódem Node.js. Nyní byste měli mít otevřený dva terminály: tu, kterou jste právě otevřeli pro práci se Node.js, a prostředí CLI, které jste použili v předchozích částech, k zadávání příkazů rozhraní příkazového řádku Azure CLI.

1. Zkopírujte [ukázky zařízení Azure IoT Node.js SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) do místního počítače:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Přejděte do adresáře *Azure-IoT-SDK-Node/Device/Samples/PNP* :

    ```console
    cd azure-iot-sdk-node/device/samples/pnp
    ```

1. Nainstalujte sadu Azure IoT Node.js SDK a nezbytné závislosti:

    ```console
    npm install
    ```

    Tento příkaz nainstaluje správné závislosti uvedené v souboru *package.json* v adresáři ukázek zařízení.

1. Nastavte obě následující proměnné prostředí, abyste mohli simulované zařízení připojit k Azure IoT.
    * Nastavte proměnnou prostředí s názvem `IOTHUB_DEVICE_CONNECTION_STRING` . Pro hodnotu proměnné použijte připojovací řetězec zařízení, který jste uložili v předchozí části.
    * Nastavte proměnnou prostředí s názvem `IOTHUB_DEVICE_SECURITY_TYPE` . Pro proměnnou použijte řetězcovou hodnotu literálu `connectionString` .

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```
    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```

    > [!NOTE]
    > Pro Windows CMD nejsou kolem řetězcových hodnot pro každou proměnnou žádné uvozovky.

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_CONNECTION_STRING='<your connection string here>'
    ```
    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='connectionString'
    ```

    **Bash (Linux nebo Windows)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    ```
    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```
1. V otevřeném prostředí CLI spusťte příkaz [AZ IoT Hub monitor-Events](/cli/azure/iot/hub#az_iot_hub_monitor_events) a začněte monitorovat události na simulovaném zařízení IoT.  Zprávy o událostech budou vytištěny v terminálu po doručení.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. V Node.js terminálu spusťte kód instalovaného ukázkového souboru *simple_thermostat.js* . Tento kód přistupuje k simulovanému zařízení IoT a pošle zprávu do centra IoT.

    Spuštění ukázky Node.js z terminálu:
    ```console
    node ./simple_thermostat.js
    ```
    > [!NOTE]
    > Tato ukázka kódu používá technologie Plug and Play Azure IoT, která umožňuje integrovat do řešení inteligentní zařízení bez jakékoli ruční konfigurace.  Ve výchozím nastavení používá většina ukázek v této dokumentaci technologie Plug and Play IoT. Další informace o výhodách služby IoT PnP a případech jejich použití nebo nepoužití najdete v tématu [co je IoT technologie Plug and Play?](../iot-pnp/overview-iot-plug-and-play.md)

Jelikož kód Node.js odesílá simulovanou zprávu telemetrie ze zařízení do služby IoT Hub, zobrazí se tato zpráva v prostředí CLI, které sleduje události:

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: ''
  interface: dtmi:com:example:Thermostat;1
  module: ''
  origin: <your device ID>
  payload:
    temperature: 36.87027777131555
```

Vaše zařízení je teď bezpečně připojené a odesílá telemetrii do Azure IoT Hub.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už nepotřebujete prostředky Azure vytvořené v rámci tohoto rychlého startu, můžete je odstranit pomocí Azure CLI.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. 

Odstranění skupiny prostředků podle názvu:
1. Spusťte příkaz [AZ Group Delete](/cli/azure/group#az_group_delete) . Tento příkaz odebere skupinu prostředků, IoT Hub a registraci zařízení, kterou jste vytvořili.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Spuštěním příkazu [AZ Group list](/cli/azure/group#az_group_list) potvrďte odstranění skupiny prostředků.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili základní pracovní postup aplikace Azure IoT pro bezpečné připojení zařízení ke cloudu a odesílání telemetrie typu zařízení-Cloud. Pomocí Azure CLI jste vytvořili centrum IoT a simulované zařízení. potom jste použili sadu Azure IoT Node.js SDK pro přístup k zařízení a odesílání telemetrie do centra. 

V dalším kroku Prozkoumejte sadu Azure IoT Node.js SDK prostřednictvím ukázek aplikací.

- [Další Node.js ukázky](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples): Tento adresář obsahuje další ukázky z úložiště Node.js SDK, které prezentují IoT Hub scénáře.