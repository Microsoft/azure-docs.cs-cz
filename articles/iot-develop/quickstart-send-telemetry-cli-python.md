---
title: Posílání telemetrie zařízení do Azure IoT Hub rychlý Start (Python)
description: V tomto rychlém startu použijete sadu SDK pro zařízení Azure IoT Hub pro Python k posílání telemetrie ze zařízení do služby IoT Hub.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 03/24/2021
ms.openlocfilehash: f28ad8f93769bc95c87095a545f608827c319dd3
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105106815"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-azure-iot-hub-python"></a>Rychlý Start: odeslání telemetrie ze zařízení do služby Azure IoT Hub (Python)

**Platí pro**: [vývoj aplikací pro zařízení](about-iot-develop.md#device-application-development)

V tomto rychlém startu se naučíte základní pracovní postup vývoje aplikací pro zařízení IoT. Pomocí Azure CLI vytvoříte Azure IoT Hub a zařízení a pak použijete sadu SDK Azure IoT Python k sestavení simulovaného klientského zařízení a odeslání telemetrie do centra. 

## <a name="prerequisites"></a>Požadavky
- Pokud ještě nemáte předplatné Azure, [vytvořte ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ještě před tím, než začnete.
- Azure CLI Všechny příkazy v tomto rychlém startu můžete spustit pomocí Azure Cloud Shell interaktivního prostředí CLI, které běží v prohlížeči. Pokud používáte Cloud Shell, nemusíte nic instalovat. Pokud dáváte přednost používání rozhraní příkazového řádku místně, musíte použít Azure CLI verze 2.0.76 nebo novější. Pokud chcete zjistit verzi, spusťte příkaz az --version. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).
- [Python 3.7 +](https://www.python.org/downloads/). Další podporované verze Pythonu najdete v tématu [funkce zařízení Azure IoT](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).
    
    Pokud chcete zajistit aktuálnost verze Pythonu, spusťte příkaz `python --version` . Pokud máte nainstalované Python 2 i Python 3 a používáte prostředí Python 3, nainstalujte všechny knihovny pomocí nástroje `pip3` . Tento příkaz zajistí, že se knihovny nainstalují do modulu runtime Python 3.
    > [!IMPORTANT]
    > V instalačním programu Python vyberte možnost **Přidání Pythonu k cestě**. Pokud už máte nainstalovaný Python 3,7 nebo vyšší verzi, potvrďte, že jste přidali instalační složku Pythonu do `PATH` proměnné prostředí.

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-python-sdk-to-send-messages"></a>Posílání zpráv pomocí sady Python SDK
V této části použijete sadu Python SDK k posílání zpráv ze simulovaného zařízení do služby IoT Hub.

1. Otevřete nové okno terminálu. Pomocí tohoto terminálu nainstalujete sadu Python SDK a budete pracovat s ukázkovým kódem v Pythonu. Nyní byste měli mít otevřený dva terminály: tu, kterou jste právě otevřeli pro práci s Pythonem, a prostředí CLI, které jste použili v předchozích částech, k zadávání příkazů rozhraní příkazového řádku Azure CLI.       

1. Zkopírujte [ukázky zařízení sady SDK služby Azure IoT Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) do místního počítače:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```
1. Přejděte do adresáře *Azure-IoT-SDK-Python/Azure-IoT-Device/Samples/PNP* :

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples/pnp
    ```
1. Instalace sady Azure IoT Python SDK:

    ```console
    pip install azure-iot-device
    ```
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

1. V otevřeném prostředí CLI spusťte příkaz [AZ IoT Hub monitor-Events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) a začněte monitorovat události na simulovaném zařízení IoT.  Zprávy událostí se tisknou v terminálu při jejich doručení.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. V terminálu Python spusťte kód nainstalovaného ukázkového souboru *simple_thermostat. py*. Tento kód přistupuje k simulovanému zařízení IoT a pošle zprávu do centra IoT.

    Spuštění ukázky Pythonu z terminálu:
    ```console
    python ./simple_thermostat.py
    ```
    > [!NOTE]
    > Tato ukázka kódu používá technologie Plug and Play Azure IoT, která umožňuje integrovat do řešení inteligentní zařízení bez jakékoli ruční konfigurace.  Ve výchozím nastavení používá většina ukázek v této dokumentaci technologie Plug and Play IoT. Další informace o výhodách služby IoT PnP a případech jejich použití nebo nepoužití najdete v tématu [co je IoT technologie Plug and Play?](../iot-pnp/overview-iot-plug-and-play.md)

 V případě, že kód Pythonu pošle zprávu ze zařízení do služby IoT Hub, zpráva se zobrazí v prostředí CLI, které sleduje události:

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: ''
  interface: dtmi:com:example:Thermostat;1
  module: ''
  origin: <your device name>
  payload:
    temperature: 35
```

Vaše zařízení je teď bezpečně připojené a odesílá telemetrii do Azure IoT Hub.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už nepotřebujete prostředky Azure vytvořené v rámci tohoto rychlého startu, můžete je odstranit pomocí Azure CLI.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky.

Odstranění skupiny prostředků podle názvu:
1. Spusťte příkaz [AZ Group Delete](/cli/azure/group#az-group-delete) . Tento příkaz odebere skupinu prostředků, IoT Hub a registraci zařízení, kterou jste vytvořili.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Spuštěním příkazu [AZ Group list](/cli/azure/group#az-group-list) potvrďte odstranění skupiny prostředků.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste se naučili základní pracovní postup aplikace Azure IoT pro bezpečné připojení zařízení ke cloudu a odesílání telemetrie typu zařízení-Cloud. Pomocí Azure CLI jste vytvořili centrum IoT a zařízení, pak jste použili sadu Azure IoT Python SDK k sestavení simulovaného zařízení a k odeslání telemetrie do centra. 

V dalším kroku Prozkoumejte sadu Azure IoT Python SDK prostřednictvím ukázek aplikací.

- [Asynchronní ukázky](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios): Tento adresář obsahuje asynchronní ukázky Pythonu pro další IoT Hub scénáře.
- [Synchronní ukázky](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples): Tento adresář obsahuje ukázky Pythonu pro použití s pythonem 2,7 nebo se synchronními scénáři kompatibility pro Python 3.6 +.
- [Ukázky IoT Edge](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios): Tento adresář obsahuje ukázky Pythonu pro práci s hraničními moduly a zařízeními pro příjem dat.
