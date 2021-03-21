---
title: Jak sestavit a nasadit IoT technologie Plug and Play most | Microsoft Docs
description: Identifikujte součásti IoT technologie Plug and Play mostu. Naučte se, jak ji spustit na zařízeních IoT, branách a jako modulu IoT Edge.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: c89427f83600d3b8091d5293b1757fa6f1a15ef1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202480"
---
# <a name="build-and-deploy-the-iot-plug-and-play-bridge"></a>Sestavení a nasazení mostu IoT technologie Plug and Play

[Most iot technologie Plug and Play](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture) umožňuje připojit stávající zařízení připojená k bráně ke službě IoT Hub. Pomocí tohoto mostu namapujete rozhraní IoT technologie Plug and Play na připojená zařízení. Rozhraní IoT technologie Plug and Play definuje telemetrii, kterou zařízení odesílá, vlastnosti synchronizované mezi zařízením a cloudem a příkazy, na které zařízení reaguje. Můžete nainstalovat a nakonfigurovat aplikaci Open-Source mostu v bránách systému Windows nebo Linux. Most se navíc dá spustit jako modul modulu runtime Azure IoT Edge.

Tento článek podrobně vysvětluje, jak:

- Nakonfigurujte most.
- Postup sestavení a spuštění mostu v různých prostředích.

Jednoduchý příklad, který ukazuje, jak používat most, najdete v tématu [Postup připojení ukázky služby IoT technologie Plug and Play Bridge, která je spuštěna v systému Linux nebo Windows a IoT Hub](howto-use-iot-pnp-bridge.md).

Pokyny a ukázky v tomto článku předpokládají základní znalost pomocí [digitálních vláken Azure](../digital-twins/overview.md) a [technologie Plug and Play IoT](overview-iot-plug-and-play.md).

## <a name="general-prerequisites"></a>Obecné požadavky

### <a name="supported-os-platforms"></a>Podporované platformy operačního systému

Podporovány jsou následující platformy a verze operačního systému:

|Platforma  |Podporované verze  |
|---------|---------|
|Windows 10 |     Všechny SKU systému Windows jsou podporovány. Například: IoT Enterprise, server, Desktop, IoT Core. *Pro funkci sledování stavu kamery se doporučuje sestavení 20H1 nebo novějším. Všechny ostatní funkce jsou k dispozici ve všech sestaveních Windows 10.*  |
|Linux     |Testováno a podporováno na Ubuntu 18,04, funkce v jiných distribucích nebyly testovány.         |
||

### <a name="hardware"></a>Hardware

- Každá hardwarová platforma, která podporuje výše uvedené SKU a verze operačního systému.
- Sériová periferní zařízení, USB, Bluetooth a kamery se nativně podporují. Most IoT technologie Plug and Play můžete rozšířit tak, aby podporoval jakékoli vlastní periferní zařízení nebo senzor.

### <a name="azure-iot-products-and-tools"></a>Produkty a nástroje Azure IoT

- **Azure IoT Hub** – v předplatném Azure potřebujete [Azure IoT Hub](../iot-hub/index.yml) , ke kterému se má vaše zařízení připojit. Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/). Pokud Centrum IoT nemáte, [vytvořte ho podle těchto pokynů](../iot-hub/iot-hub-create-using-cli.md). Podpora IoT technologie Plug and Play není součástí centra IoT v základní úrovni.
- **Azure IoT Explorer** – Chcete-li komunikovat se zařízením IoT technologie Plug and Play, můžete použít nástroj Azure IoT Explorer. [Stáhněte a nainstalujte si nejnovější verzi Azure IoT Exploreru](./howto-use-iot-explorer.md) pro váš operační systém. Nakonfigurujte nástroj Azure IoT Explorer pro připojení ke službě IoT Hub a vyhledejte definice modelů ve složce *pnpbridge\docs\schemas* v adresáři **IoT-Plug-and-Play-most** , který jste naklonoval.

## <a name="configure-a-bridge"></a>Konfigurace mostu

Existují dva způsoby, jak nakonfigurovat most:

- Pokud je most spuštěný nativně na zařízení IoT nebo bráně, použijte konfigurační soubor. Tento scénář může používat počítač se systémem Linux nebo Windows.
- Pokud je most spuštěný jako modul IoT Edge v modulu IoT Edge runtime, použijte vlastnost s příchodem vlákna. Tento scénář předpokládá, že modul runtime IoT Edge je hostovaný v prostředí Linux.

### <a name="configuration-file"></a>Konfigurační soubor

Když technologie Plug and Play most běží nativně na zařízení nebo bráně IoT, používá konfigurační soubor k těmto akcím:

- Získá IoT Central nebo IoT Hub informace o připojení.
- Nakonfigurujte zařízení, pro která jsou publikovaná rozhraní IoT technologie Plug and Play.

K zadání konfiguračního souboru do mostu použijte jednu z následujících možností:

- Předat cestu ke konfiguračnímu souboru jako parametr příkazového řádku pro spustitelný soubor mostu.
- V souboru ve složce *pnpbridge\cmake\ pnpbridge_x86 \src\pnpbridge\samples\console* použijte existující *config.js* .

[Schéma konfiguračního souboru](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/pnpbridge/src/pnpbridge_config_schema.json) je dostupné v úložišti GitHubu.

> [!TIP]
> Pokud upravíte konfigurační soubor mostu v VS Code, můžete použít tento soubor schématu k ověření souboru.

### <a name="iot-edge-module-configuration"></a>Konfigurace modulu IoT Edge

Když je most spuštěn jako modul IoT Edge v modulu IoT Edge runtime, je konfigurační soubor odeslán z cloudu jako aktualizace `PnpBridgeConfig` požadované vlastnosti. Most počká na aktualizaci této vlastnosti před konfigurací adaptérů a součástí.

## <a name="build-and-run-the-bridge-on-an-iot-device-or-gateway"></a>Sestavení a spuštění mostu na zařízení nebo bráně IoT

| Platforma | Podporováno |
| :-----------: | :-----------: |
| Windows |  Yes |
| Linux | Yes |

### <a name="prerequisites"></a>Předpoklady

K dokončení této části je potřeba na váš místní počítač nainstalovat následující software:

- Vývojové prostředí, které podporuje kompilování C++, jako je například [Visual Studio (Community, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) – nezapomeňte při instalaci sady Visual Studio zahrnout komponentu správce balíčků NuGet a **vývoj desktopových aplikací pomocí C++** .
- [Cmake](https://cmake.org/download/) – při instalaci cmake vyberte možnost **Přidat cmake do systémové cesty**.
- Pokud pracujete v systému Windows, musíte také stáhnout [sadu Windows 17763 SDK](https://developer.microsoft.com/windows/downloads/windows-10-sdk).

### <a name="source-code"></a>Zdrojový kód

Naklonujte úložiště [IoT technologie Plug and Play mostu](https://github.com/Azure/iot-plug-and-play-bridge) do místního počítače:

```console
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

Očekává, že spuštění předchozího příkazu trvá několik minut.

> [!NOTE]
> Pokud narazíte na problémy s `git submodule update` selháním ve Windows, zkuste problém vyřešit pomocí následujícího příkazu: `git config --system core.longpaths true` .

### <a name="build-the-bridge-on-windows"></a>Sestavení mostu ve Windows

Otevřete **Developer Command Prompt pro VS 2019** a přejděte do složky, která obsahuje úložiště, které jste naklonoval, a spusťte následující příkazy:

```console
cd pnpbridge\scripts\windows

build.cmd
```

Očekává, že spuštění předchozího příkazu trvá několik minut.

Volitelně můžete otevřít vygenerovaný soubor řešení *pnpbridge\cmake\ pnpbridge_x86 \ azure_iot_pnp_bridge. sln* v aplikaci Visual Studio pro úpravu, opětovné sestavení a ladění kódu.

> [!TIP]
> Tento projekt pomocí CMAKe generuje soubory projektu. Pokud se příslušné soubory CMAKe neaktualizují, může dojít ke ztrátě všech změn, které provedete v projektu v sadě Visual Studio.

### <a name="build-the-bridge-on-linux"></a>Sestavení mostu v systému Linux

Pomocí prostředí bash přejděte do složky, která obsahuje úložiště, které jste naklonoval, a spusťte následující příkazy:

```bash
cd scripts/linux

./setup.sh

./build.sh
```

### <a name="configure-the-generic-sensors"></a>Konfigurace obecných senzorů

Upravte následující parametry pod `pnp_bridge_connection_parameters` uzlem v *config.jsna* souboru pnpbridge_x86 ve složce *\src\pnpbridge\samples\console* ve Windows nebo ve složce *IoT-Plug-and-Play-Play/pnpbridge/cmake/pnpbridge_linux \ src/pnpbridge/Samples/Console* v systému Windows:

Pokud k připojení ke službě IoT Hub používáte připojovací řetězec:

```JSON
{
  "connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "root_interface_model_id": "[To fill in]",
    "auth_parameters": {
      "auth_type": "symmetric_key",
      "symmetric_key": "[To fill in]"
    }
  }
}
```

> [!TIP]
> `symmetric_key`Hodnota musí odpovídat klíči SAS v připojovacím řetězci.

Pokud se připojujete ke službě IoT Hub nebo k IoT Central aplikaci pomocí DPS:

```JSON
{
  "connection_parameters": {
    "connection_type" : "dps",
    "root_interface_model_id": "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "auth_parameters" : {
      "auth_type" : "symmetric_key",
      "symmetric_key" : "[DEVICE KEY]"
    },
    "dps_parameters" : {
      "global_prov_uri" : "[GLOBAL PROVISIONING URI] - typically it is global.azure-devices-provisioning.net",
      "id_scope": "[IoT Central / IoT Hub ID SCOPE]",
      "device_id": "[DEVICE ID]"
    }
  }
}
```

Zkontrolujte zbývající část konfiguračního souboru a podívejte se, které součásti rozhraní a globální parametry jsou v této ukázce nakonfigurované.

### <a name="start-the-bridge-in-windows"></a>Spustit most ve Windows

Spusťte most spuštěním příkazu na příkazovém řádku:

```console
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

Debug\pnpbridge_bin.exe
```

> [!TIP]
> Pokud chcete použít konfigurační soubor z jiného umístění, předejte jeho cestu jako parametr příkazového řádku při spuštění mostu.
  
> [!TIP]
> Pokud máte k počítači připojenou kameru nebo fotoaparát USB, můžete spustit aplikaci, která používá kameru, například vestavěnou aplikaci **kamera** . Když je aplikace **kamery** spuštěná, zobrazí se ve výstupu konzoly pro předávání Statistika monitorování a frekvence snímků je hlášena prostřednictvím digitálního vlákna v rámci služby IoT Hub.

## <a name="build-and-run-the-bridge-as-an-iot-edge-module"></a>Sestavit a spustit most jako modul IoT Edge

| Platforma | Podporováno |
| :-----------: | :-----------: |
| Windows |  No |
| Linux | Yes |

### <a name="prerequisites"></a>Předpoklady

K dokončení této části potřebujete službu Azure IoT Hub úrovně Free nebo Standard. Informace o tom, jak vytvořit centrum IoT, najdete v tématu [vytvoření centra IoT](../iot-hub/iot-hub-create-through-portal.md).

Kroky v této části předpokládají, že máte na počítači s Windows 10 následující vývojové prostředí. Tyto nástroje vám umožní vytvořit a nasadit modul IoT Edge do zařízení IoT Edge:

- Subsystém Windows pro Linux (WSL) 2 se systémem Ubuntu 18,04 LTS. Další informace najdete v [instalační příručce k systému Windows pro Linux pro systém Windows 10](/windows/wsl/install-win10).
- Docker Desktop pro Windows nakonfigurovaný tak, aby používal WSL 2. Další informace najdete v tématu [Docker Desktop WSL 2 back-end](https://docs.docker.com/docker-for-windows/wsl/).
- [Visual Studio Code nainstalované v prostředí Windows](https://code.visualstudio.com/docs/setup/windows) s následujícími třemi nainstalovanými rozšířeními:

  - [Balíček rozšíření pro vzdálenou vývoj](https://aka.ms/vscode-remote/download/extension) – toto rozšíření umožňuje sestavit a spustit kód v WSL 2.
  - [Docker pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) – toto rozšíření musí být povolené v prostředí vs Code **WSL: Ubuntu-18,04** .
  - [Azure IoT Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) – toto rozšíření musí být povolené v prostředí vs Code **WSL: Ubuntu-18,04** .

- Spusťte následující příkazy v prostředí WSL 2 k instalaci požadovaných nástrojů sestavení:

  ```bash
  sudo apt-get update
  sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
  ```

- Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli-apt) nainstalované ve vašem prostředí WSL 2 pro správu prostředků Azure.

  > [!TIP]
  > Pokud budete chtít, můžete spustit `az` příkazy v [Azure Cloud Shell](https://shell.azure.com/) , kde je rozhraní příkazového řádku předem nainstalováno.

### <a name="create-an-iot-edge-device"></a>Vytvoříte zařízení IoT Edge.

Tyto příkazy vytvoří IoT Edge zařízení běžící na virtuálním počítači Azure. Spuštění zařízení IoT Edge na virtuálním počítači je užitečné pro testování vašeho nasazení, pokud nemáte přístup k reálnému zařízení.

Pokud chcete ve službě IoT Hub vytvořit registraci zařízení IoT Edge, spusťte ve svém prostředí WSL 2 následující příkazy. Pomocí `az login` příkazu se přihlaste ke svému předplatnému Azure:

```azurecli
az iot hub device-identity create --device-id bridge-edge-device --edge-enabled true --hub-name {your IoT hub name}
```

Pokud chcete vytvořit virtuální počítač Azure s nainstalovaným modulem runtime IoT Edge, spusťte následující příkazy. Aktualizujte zástupné symboly pomocí vhodných hodnot:

```azurecli
az group create --name bridge-edge-resources --location eastus
az deployment group create \
--resource-group bridge-edge-resources \
--template-uri "https://aka.ms/iotedge-vm-deploy" \
--parameters dnsLabelPrefix='{unique DNS name for virtual machine}' \
--parameters adminUsername='{admin user name}' \
--parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name} -o tsv) \
--parameters authenticationType='password' \
--parameters adminPasswordOrKey="{admin password for virtual machine}"
```

Teď je IoT Edge modul runtime spuštěný ve virtuálním počítači. Pomocí následujícího příkazu můžete ověřit, že **$edgeAgent** a **$edgeHub** na zařízení běží:

```azurecli
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

> [!CAUTION]
> Účtují se vám, dokud je tento virtuální počítač spuštěný. Ujistěte se, že jste ho vypnuli, až ho nepoužíváte, a po dokončení práce ho odeberte.

### <a name="download-the-source-code"></a>Stažení zdrojového kódu

V následujících krocích sestavíte image Docker ve vašem prostředí WSL 2. Pokud chcete klonovat úložiště **IoT-Plug-and-Play-most** , spusťte následující příkazy v prostředí WSL 2 bash ve vhodné složce:

```bash
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

Očekává, že spuštění předchozího příkazu trvá několik minut.

### <a name="update-the-dockerfile"></a>Aktualizace *souboru Dockerfile*

Spusťte VS Code, otevřete paletu příkazů, zadejte *Remote WSL: otevřít složku v WSL* a pak otevřete složku *IoT-Plug-and-Play-most* , která obsahuje klonované úložiště.

Otevřete soubor *pnpbridge\Dockerfile.amd64* . Upravte definice proměnných prostředí následujícím způsobem:

```dockerfile
ENV PNP_BRIDGE_ROOT_MODEL_ID="dtmi:com:example:RootPnpBridgeSampleDevice;1"
ENV PNP_BRIDGE_HUB_TRACING_ENABLED="false"
ENV IOTEDGE_WORKLOADURI="something"
```

> [!TIP]
> K načtení připojovacího řetězce zařízení můžete použít následující příkaz: `az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name}`

Pro jiné architektury jsou k dispozici ukázková *fázemi* .

### <a name="build-the-iot-plug-and-play-bridge-module-image"></a>Vytvoření image modulu IoT technologie Plug and Play Bridge

V VS Code otevřete paletu příkazů, zadejte *Registry Docker: Přihlaste se k Docker CLI* a pak vyberte předplatné Azure a registr kontejnerů. Tento příkaz umožňuje Docker připojit se k registru kontejneru a nahrát image modulu.

Otevřete *pnpbridge/module.jsv* souboru. Přidejte `{your container registry name}.azurecr.io/iotpnpbridge` jako úložiště imagí kontejneru a `v1` jako verzi.

V VS Code klikněte pravým tlačítkem na *pnpbridge/module.js* v souboru v zobrazení **Průzkumník** , vyberte **Build a push IoT Edge image modulu** a jako platformu vyberte **amd64** .

V VS Code můžete v zobrazení **Docker** procházet obsah registru kontejneru, který teď obsahuje image modulu **iotpnpbridge: V1-amd64** .

### <a name="create-a-container-registry"></a>Vytvoření registru kontejneru

Zařízení IoT Edge stahuje své image modulu z registru kontejneru. Tento příklad používá službu Azure Container Registry.

Vytvořte službu Azure Container Registry ve skupině prostředků **mostu-Edge--prostředky** . Pak povolte přístup správce k registru kontejneru a získejte přihlašovací údaje, které vaše zařízení IoT Edge potřebuje ke stažení imagí modulu:

```azurecli
az acr create -g bridge-edge-resources --sku Basic -n {your container registry name}
az acr update --admin-enabled true -n {your container registry name}
az acr credential show -n {your container registry name}
```

### <a name="create-the-deployment-manifest"></a>Vytvoření manifestu nasazení

Manifest nasazení IoT Edge Určuje moduly a konfigurační hodnoty, které se mají nasadit do IoT Edgeho zařízení.

V VS Code otevřete *pnpbridge/deployment.template.jsv* souboru:

- Aktualizujte `registryCredentials` hodnoty pomocí hodnot z předchozího příkazu. `address`Hodnota vypadá jako `{your container registry name}.azurecr.io` .
- Aktualizujte `image` hodnotu pro `ModulePnpBridge` . Obrázek modulu vypadá takto: `{your container registry}.azurecr.io/iotpnpbridge:v1-amd64` .
- `PnPBridgeConfig`Chcete-li nakonfigurovat adaptér pro rozpoznávání CO2, nahraďte následujícím kódem JSON:

  ```json
  "PnpBridgeConfig": {
    "pnp_bridge_interface_components": [
      {
        "_comment": "Component 1 - Modbus Device",
        "pnp_bridge_component_name": "Co2Detector1",
        "pnp_bridge_adapter_id": "modbus-pnp-interface",
        "pnp_bridge_adapter_config": {
          "unit_id": 1,
          "tcp": {
            "host": "10.159.29.2",
            "port": 502
          },
          "modbus_identity": "DL679"
        }
      }
    ],
    "pnp_bridge_adapter_global_configs": {
      "modbus-pnp-interface": {
        "DL679": {
          "telemetry": {
            "co2": {
              "startAddress": "40001",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 5000,
              "conversionCoefficient": 1
            },
            "temperature": {
              "startAddress": "40003",
              "length": 1,
              "dataType": "decimal",
              "defaultFrequency": 5000,
              "conversionCoefficient": 0.01
            }
          },
          "properties": {
            "firmwareVersion": {
              "startAddress": "40482",
              "length": 1,
              "dataType": "hexstring",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "modelName": {
              "startAddress": "40483",
              "length": 2,
              "dataType": "string",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmStatus_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "boolean",
              "defaultFrequency": 1000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmThreshold_co2": {
              "startAddress": "40225",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 30000,
              "conversionCoefficient": 1,
              "access": 2
            }
          },
          "commands": {
            "clearAlarm_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "flag",
              "conversionCoefficient": 1
            }
          }
        }
      }
    }
  }
  ```

  Uložte změny.

V VS Code klikněte pravým tlačítkem na *pnpbridge/deployment.template.js* v souboru v zobrazení **Průzkumník** , vyberte **Generovat IoT Edge manifest nasazení**. Tento příkaz vygeneruje *pnpbridge/config/deployment.amd64.jsv* manifestu nasazení.

### <a name="deploy-the-bridge-to-your-iot-edge-device"></a>Nasazení mostu do zařízení IoT Edge

V VS Code otevřete paletu příkazů, zadejte *Azure IoT Hub: vyberte IoT Hub* a pak vyberte své předplatné a centrum IoT.

V VS Code klikněte pravým tlačítkem na soubor *pnpbridge/config/deployment.amd64.js* v zobrazení **Průzkumník** , vyberte **vytvořit nasazení pro jedno zařízení** a vyberte **most – hraniční zařízení**.

Chcete-li zobrazit stav modulů v zařízení, spusťte následující příkaz:

```azurecli
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

Seznam spuštěných modulů teď obsahuje modul **ModulePnpBridge** , který je nakonfigurovaný tak, aby používal adaptér pro rozpoznávání CO2.

### <a name="tidy-up"></a>Uklizený nahoru

Pokud chcete virtuální počítač a registr kontejnerů odebrat z předplatného Azure, spusťte následující příkaz:

```azurecli
az group delete -n bridge-edge-resources
```

## <a name="folder-structure"></a>Struktura složek

*pnpbridge\deps\azure-IoT-SDK-c-PNP*: podmoduly Git, které obsahují sadu SDK pro zařízení Azure IoT pro jazyk c SDK.

*pnpbridge\scripts*: vytváření skriptů.

*pnpbridge\src*: zdrojový kód pro IoT technologie Plug and Play mostu.

*pnpbridge\src\adapters*: zdrojový kód pro různé adaptéry pro mosty IoT technologie Plug and Play.

## <a name="next-steps"></a>Další kroky

Další informace o službě IoT technologie Plug and Play Bridge najdete v úložišti GitHub [technologie Plug and Play mostu IoT](https://github.com/Azure/iot-plug-and-play-bridge) .