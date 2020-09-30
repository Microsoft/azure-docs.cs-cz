---
title: Použití rozšíření Azure IoT pro Azure CLI k interakci se zařízeními IoT technologie Plug and Play | Microsoft Docs
description: Nainstalujte rozšíření Azure IoT pro Azure CLI a použijte ho k interakci se zařízeními IoT technologie Plug and Play připojenými ke službě IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 680cd4ef4f73c63850a2137b344fd0af6b27c673
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577454"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Instalace a použití rozšíření Azure IoT pro rozhraní příkazového řádku Azure

[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&preserve-view=true) je open source nástroj příkazového řádku pro různé platformy, který slouží ke správě prostředků Azure, jako je IoT Hub. Rozhraní příkazového řádku Azure je dostupné v systémech Windows, Linux a macOS. Rozhraní příkazového řádku Azure umožňuje spravovat prostředky Azure IoT Hub, instance služby Device Provisioning a propojená centra bez nutnosti instalovat jakákoli rozšíření.

Rozšíření Azure IoT pro Azure CLI je nástroj příkazového řádku pro interakci s a testování zařízení IoT technologie Plug and Play. Rozšíření můžete použít k těmto akcím:

- Připojte se k zařízení.
- Podívejte se na telemetrii, kterou zařízení odesílá.
- Práce s vlastnostmi zařízení
- Zavolejte příkazy zařízení.

V tomto článku se dozvíte, jak:

- Nainstalujte a nakonfigurujte rozšíření Azure IoT pro rozhraní příkazového řádku Azure CLI.
- Použijte rozšíření pro interakci a testování vašich zařízení.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Instalace rozšíření Azure IoT pro rozhraní příkazového řádku Azure

### <a name="step-1---install-the-azure-cli"></a>Krok 1 – instalace rozhraní příkazového řádku Azure

Podle [pokynů k instalaci](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) nastavte Azure CLI ve vašem prostředí. Pro dosažení optimálního prostředí by verze Azure CLI měla být verze 2.9.1 nebo vyšší. Ke kontrole použijte příkaz `az -–version`.

### <a name="step-2---install-iot-extension"></a>Krok 2 – instalace rozšíření IoT

Soubor [Readme rozšíření IoT](https://github.com/Azure/azure-iot-cli-extension) obsahuje popis několika způsobů instalace rozšíření. Nejjednodušším způsobem je spustit příkaz `az extension add --name azure-iot`. Po instalaci můžete pomocí příkazu `az extension list` ověřit aktuálně nainstalovaná rozšíření nebo pomocí příkazu `az extension show --name azure-iot` zobrazit podrobnosti o rozšíření IoT. V době psaní je číslo verze rozšíření `0.10.0` .

K odebrání rozšíření můžete použít příkaz `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Použití rozšíření Azure IoT pro rozhraní příkazového řádku Azure

### <a name="prerequisites"></a>Předpoklady

Pokud se chcete přihlásit ke svému předplatnému Azure, spusťte následující příkaz:

```azurecli
az login
```

Pokud chcete používat rozšíření Azure IoT pro rozhraní příkazového řádku Azure CLI, budete potřebovat:

- Azure IoT Hub. Existuje mnoho způsobů, jak přidat službu IoT Hub k předplatnému Azure, jako je například [vytvoření centra IoT pomocí Azure CLI](../iot-hub/iot-hub-create-using-cli.md). Abyste mohli spouštět příkazy rozšíření Azure IoT, potřebujete připojovací řetězec služby IoT Hub. Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

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

### <a name="manage-models-in-the-model-repository"></a>Správa modelů v úložišti modelu

Příkazy úložiště modelu Azure CLI můžete použít ke správě modelů v úložišti.

#### <a name="create-model-repository"></a>Vytvořit úložiště modelu

Pokud jste prvním uživatelem ve vašem tenantovi, vytvořte nové úložiště IoT technologie Plug and Play společnosti pro vašeho tenanta:

```azurecli
az iot pnp repo create
```

#### <a name="manage-model-repository-tenant-roles"></a>Správa rolí tenanta úložiště modelu

Umožňuje vytvořit přiřazení role pro uživatele nebo instanční objekt ke konkrétnímu prostředku.

Poskytněte například user@consoso.com roli **ModelsCreator** pro tenanta:

```azurecli
az iot pnp role-assignment create --resource-id {tenant_id} --resource-type Tenant --subject-id {user@contoso.com} --subject-type User --role ModelsCreator
```

Nebo poskytněte user@consoso.com roli **ModelAdministrator** pro určitý model:

```azurecli
az iot pnp role-assignment create --resource-id {model_id} --resource-type Model --subject-id {user@contoso.com} --subject-type User --role ModelAdministrator
```

#### <a name="create-a-model"></a>Vytvoření modelu

Vytvořte nový model v úložišti společnosti:

```azurecli
az iot pnp model create --model {model_json or path_to_file}
```

#### <a name="search-a-model"></a>Hledání v modelu

Vypíše modely, které odpovídají konkrétnímu klíčovému slovu:

```azurecli
az iot pnp model list -q {search_keyword}
```

#### <a name="publish-a-model"></a>Publikování modelu

Publikujte model zařízení umístěný v úložišti společnosti do veřejného úložiště.

Například proveďte veřejný model s ID `dtmi:com:example:ClimateSensor;1` :

```azurecli
az iot pnp model publish --dtmi "dtmi:com:example:ClimateSensor;1"
```

Chcete-li publikovat model, musí být splněny následující požadavky:

- Tenant společnosti nebo organizace musí být partnerem Microsoftu. 
- Uživatel nebo instanční objekt musí být členem role **vydavatele** tenanta úložiště.

## <a name="next-steps"></a>Další kroky

V tomto článku se naučíte, jak nainstalovat a používat rozšíření Azure IoT pro rozhraní příkazového řádku Azure CLI pro interakci s technologie Plug and Playmi zařízeními IoT. Navržený další krok se naučíte používat [Azure IoT Explorer se svými zařízeními](./howto-use-iot-explorer.md).
