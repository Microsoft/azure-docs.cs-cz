---
title: Použití rozšíření Azure IoT pro Azure CLI k interakci se zařízeními IoT technologie Plug and Play Preview | Microsoft Docs
description: Nainstalujte rozšíření Azure IoT pro Azure CLI a použijte ho k interakci se zařízeními IoT technologie Plug and Play připojenými ke službě IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 3699213fe61c64d7677ba026a8df54ccbbfe4b33
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352158"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Instalace a použití rozšíření Azure IoT pro rozhraní příkazového řádku Azure

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) je open source nástroj příkazového řádku pro různé platformy, který slouží ke správě prostředků Azure, jako je IoT Hub. Rozhraní příkazového řádku Azure je dostupné v systémech Windows, Linux a MacOS. Rozhraní příkazového řádku Azure je také předem nainstalováno v [Azure Cloud Shell](https://shell.azure.com). Rozhraní příkazového řádku Azure umožňuje spravovat prostředky Azure IoT Hub, instance služby Device Provisioning a propojená centra bez nutnosti instalovat jakákoli rozšíření.

Rozšíření Azure IoT pro Azure CLI je nástroj příkazového řádku pro interakci s a testování zařízení IoT technologie Plug and Play ve verzi Preview. Rozšíření můžete použít k těmto akcím:

- Připojte se k zařízení.
- Podívejte se na telemetrii, kterou zařízení odesílá.
- Práce s vlastnostmi zařízení
- Zavolejte příkazy zařízení.

V tomto článku se dozvíte, jak:

- Nainstalujte a nakonfigurujte rozšíření Azure IoT pro rozhraní příkazového řádku Azure CLI.
- Použijte rozšíření pro interakci a testování vašich zařízení.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Instalace rozšíření Azure IoT pro rozhraní příkazového řádku Azure

### <a name="step-1---install-the-azure-cli"></a>Krok 1 – instalace rozhraní příkazového řádku Azure

Podle [pokynů k instalaci](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nastavte Azure CLI ve vašem prostředí. Pro dosažení optimálního prostředí by verze Azure CLI měla být verze 2.9.1 nebo vyšší. Ke kontrole použijte příkaz `az -–version`.

### <a name="step-2---install-iot-extension"></a>Krok 2 – instalace rozšíření IoT

Soubor [Readme rozšíření IoT](https://github.com/Azure/azure-iot-cli-extension) obsahuje popis několika způsobů instalace rozšíření. Nejjednodušším způsobem je spustit příkaz `az extension add --name azure-iot`. Po instalaci můžete pomocí příkazu `az extension list` ověřit aktuálně nainstalovaná rozšíření nebo pomocí příkazu `az extension show --name azure-iot` zobrazit podrobnosti o rozšíření IoT. V době psaní je číslo verze rozšíření `0.9.7` .

K odebrání rozšíření můžete použít příkaz `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Použití rozšíření Azure IoT pro rozhraní příkazového řádku Azure

### <a name="prerequisites"></a>Požadavky

Pokud se chcete přihlásit ke svému předplatnému Azure, spusťte následující příkaz:

```azurecli
az login
```

> [!NOTE]
> Pokud používáte Azure Cloud Shell, jste přihlášeni automaticky a nemusíte spouštět předchozí příkaz.

Pokud chcete používat rozšíření Azure IoT pro rozhraní příkazového řádku Azure CLI, budete potřebovat:

- Azure IoT Hub. Existuje mnoho způsobů, jak přidat službu IoT Hub k předplatnému Azure, jako je například [vytvoření centra IoT pomocí Azure CLI](../iot-hub/iot-hub-create-using-cli.md). Abyste mohli spouštět příkazy rozšíření Azure IoT, potřebujete připojovací řetězec služby IoT Hub. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Zařízení zaregistrované ve službě IoT Hub. K registraci zařízení můžete použít následující příkaz rozhraní příkazového řádku Azure, nezapomeňte nahradit `{YourIoTHubName}` `{YourDeviceID}` zástupné symboly a pomocí vašich hodnot:

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

### <a name="interact-with-a-device"></a>Interakce se zařízením

Rozšíření můžete použít k zobrazení a interakci se zařízeními IoT technologie Plug and Play, která jsou připojená ke službě IoT Hub. Toto rozšíření spolupracuje s digitálním vlákna, které představuje zařízení IoT technologie Plug and Play.

#### <a name="list-devices"></a>Získejte seznam zařízení.

Vypsat všechna zařízení v IoT Hub:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

#### <a name="view-digital-twin"></a>Zobrazení digitálního vlákna

Zobrazení digitálního vlákna zařízení:

```azurecli
az iot pnp twin show -n {iothub_name} -d {device_id}
```

#### <a name="properties"></a>Vlastnosti

Nastavte hodnotu vlastnosti pro čtení i zápis:

```azurecli
az iot pnp twin update --hub-name {iothub_name} --device-id {device_id} --json-patch '{"op":"add", "path":"/thermostat1/targetTemperature", "value": 54}'
```

#### <a name="commands"></a>Příkazy

Vyvolat příkaz:

```azurecli
az iot pnp twin invoke-command --cn getMaxMinReport -n {iothub_name} -d {device_id} --component-path thermostat1
```

#### <a name="digital-twin-events"></a>Digitální zdvojené události

Monitorujte všechny události IoT technologie Plug and Play digitálních událostí z konkrétního zařízení a rozhraní, které se přesměrují do skupiny uživatelů centra událostí **$Default** :

```azurecli
az iot hub monitor-events -n {iothub_name} -d {device_id} -i {interface_id}
```

## <a name="next-steps"></a>Další kroky

V tomto článku se naučíte, jak nainstalovat a používat rozšíření Azure IoT pro rozhraní příkazového řádku Azure pro interakci s technologie Plug and Playmi zařízeními. Navržený další krok se naučíte používat [Azure IoT Explorer se svými zařízeními](./howto-use-iot-explorer.md).
