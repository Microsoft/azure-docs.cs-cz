---
title: Postup sestavení, nasazení a rozšiřování IoT technologie Plug and Play mostu | Microsoft Docs
description: Identifikujte součásti IoT technologie Plug and Play mostu. Naučte se, jak tento most rozšíříte a jak ho spustit na zařízeních IoT, branách a jako modulu IoT Edge.
author: usivagna
ms.author: ugans
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ece9f62e64eb64b1f34af46b42d57ec583f8f214
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97675833"
---
# <a name="build-deploy-and-extend-the-iot-plug-and-play-bridge"></a>Sestavování, nasazování a rozšiřování technologie Plug and Play mostu IoT

Most IoT technologie Plug and Play umožňuje připojit stávající zařízení připojená k bráně ke službě IoT Hub. Pomocí tohoto mostu namapujete rozhraní IoT technologie Plug and Play na připojená zařízení. Rozhraní IoT technologie Plug and Play definuje telemetrii, kterou zařízení odesílá, vlastnosti synchronizované mezi zařízením a cloudem a příkazy, na které zařízení reaguje. Můžete nainstalovat a nakonfigurovat aplikaci Open-Source mostu v bránách systému Windows nebo Linux.

Tento článek podrobně vysvětluje, jak:

- Nakonfigurujte most.
- Rozšíří most vytvořením nových adaptérů.
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

## <a name="extend-the-bridge"></a>Rozšiřování mostu

Pro rozšiřování schopností mostu můžete vytvářet vlastní adaptéry mostu.

Most používá adaptéry k těmto akcím:

- Navažte spojení mezi zařízením a cloudem.
- Povolí tok dat mezi zařízením a cloudem.
- Povolte správu zařízení z cloudu.

Každý adaptér mostu musí:

- Vytvořte rozhraní Digital vláknas.
- Pomocí rozhraní můžete navazovat funkce na straně zařízení na cloudové možnosti, jako jsou telemetrie, vlastnosti a příkazy.
- Navažte řízení a datovou komunikaci s hardwarem nebo firmwarem zařízení.

Každý adaptér mostu komunikuje s konkrétním typem zařízení podle toho, jak se adaptér připojuje a komunikuje se zařízením. I v případě, že komunikace se zařízením používá protokol handshake, může mít adaptér mostu několik způsobů, jak data ze zařízení interpretovat. V tomto scénáři adaptér mostu používá informace pro adaptér v konfiguračním souboru k určení *Konfigurace rozhraní* , které má adaptér použít k analýze dat.

Aby bylo možné komunikovat se zařízením, adaptér mostu používá komunikační protokol podporovaný zařízením a rozhraními API poskytovanými buď základním operačním systémem, nebo dodavatelem zařízení.

Pro interakci s cloudem adaptér mostu používá rozhraní API poskytovaná sadou SDK pro zařízení Azure IoT, která slouží k odesílání telemetrie, vytváření digitálních vlákenných rozhraní, odesílání aktualizací vlastností a vytváření funkcí zpětného volání pro aktualizace vlastností a příkazy.

### <a name="create-a-bridge-adapter"></a>Vytvoření adaptéru mostu

Most očekává, že adaptér mostu implementuje rozhraní API definovaná v rozhraní [_PNP_ADAPTER](https://github.com/Azure/iot-plug-and-play-bridge/blob/9964f7f9f77ecbf4db3b60960b69af57fd83a871/pnpbridge/src/pnpbridge/inc/pnpadapter_api.h#L296) :

```c
typedef struct _PNP_ADAPTER {
  // Identity of the IoT Plug and Play adapter that is retrieved from the config
  const char* identity;

  PNPBRIDGE_ADAPTER_CREATE createAdapter;
  PNPBRIDGE_COMPONENT_CREATE createPnpComponent;
  PNPBRIDGE_COMPONENT_START startPnpComponent;
  PNPBRIDGE_COMPONENT_STOP stopPnpComponent;
  PNPBRIDGE_COMPONENT_DESTROY destroyPnpComponent;
  PNPBRIDGE_ADAPTER_DESTOY destroyAdapter;
} PNP_ADAPTER, * PPNP_ADAPTER;
```

V tomto rozhraní:

- `PNPBRIDGE_ADAPTER_CREATE` Vytvoří adaptér a nastaví prostředky správy rozhraní. Adaptér může také při vytváření adaptéru spoléhat na globální parametry adaptéru. Tato funkce se volá jednou pro jeden adaptér.
- `PNPBRIDGE_COMPONENT_CREATE` Vytvoří digitální dvojitá klientská rozhraní a naváže funkce zpětného volání. Adaptér inicializuje komunikační kanál do zařízení. Adaptér může nastavit prostředky, aby umožnily tok telemetrie, ale nespustil telemetrie pro vytváření sestav `PNPBRIDGE_COMPONENT_START` , dokud se nevolá. Tato funkce se volá jednou pro každou součást rozhraní v konfiguračním souboru.
- `PNPBRIDGE_COMPONENT_START` je volána, aby adaptér mostu mohl začít předávat telemetrii ze zařízení do digitálního vlákna klienta. Tato funkce se volá jednou pro každou součást rozhraní v konfiguračním souboru.
- `PNPBRIDGE_COMPONENT_STOP` zastaví tok telemetrie.
- `PNPBRIDGE_COMPONENT_DESTROY` zničí z digitálního vlákna klienta a přidružených prostředků rozhraní. Tato funkce se volá jednou pro každou součást rozhraní v konfiguračním souboru, když se přestanou zpomalit most nebo když dojde k závažné chybě.
- `PNPBRIDGE_ADAPTER_DESTROY` vyčistí prostředky můstkového adaptéru.

### <a name="bridge-core-interaction-with-bridge-adapters"></a>Přemostění interakce jádra s adaptéry mostu

Následující seznam popisuje, co se stane, když se most spustí:

1. Když se most spustí, správce adaptéru mostu prohledá jednotlivé komponenty rozhraní definované v konfiguračním souboru a zavolá `PNPBRIDGE_ADAPTER_CREATE` příslušný adaptér. Adaptér může použít parametry konfigurace globálního adaptéru k nastavení prostředků pro podporu různých *konfigurací rozhraní*.
1. Pro každé zařízení v konfiguračním souboru inicializuje správce mostu vytvoření rozhraní voláním `PNPBRIDGE_COMPONENT_CREATE` příslušného můstkového adaptéru.
1. Adaptér obdrží všechna volitelná nastavení konfigurace adaptéru pro komponentu rozhraní a použije tyto informace k nastavení připojení k zařízení.
1. Adaptér vytvoří digitální dvojitá klientská rozhraní a naváže funkce zpětného volání pro aktualizace vlastností a příkazy. Navázání připojení zařízení by nemělo po úspěšném vytvoření digitálního vlákna na konci zablokovat návrat zpětných volání. Aktivní připojení zařízení nezávisí na aktivním klientovi rozhraní, který most vytvoří. Pokud dojde k chybě připojení, adaptér předpokládá, že zařízení není aktivní. Adaptér mostu se může rozhodnout pro vytvoření tohoto připojení znovu.
1. Až správce adaptéru mostu vytvoří všechny součásti rozhraní zadané v konfiguračním souboru, zaregistruje všechna rozhraní s Azure IoT Hub. Registrace je blokující asynchronní volání. Po dokončení volání aktivuje zpětné volání v adaptéru mostu, které umožňuje spustit zpracování vlastností a zpětných volání příkazů z cloudu.
1. Správce mostu pak zavolá `PNPBRIDGE_INTERFACE_START` na každou součást a adaptér mostu zahájí oznamování telemetrie do digitálního nevlákenního klienta.

### <a name="design-guidelines"></a>Pokyny pro návrh

Při vývoji nového adaptéru mostu postupujte podle těchto pokynů:

- Určete, které možnosti zařízení jsou podporované a jaká definice rozhraní komponent pomocí tohoto adaptéru vypadá jako.
- Určete, jaké rozhraní a globální parametry je potřeba, aby váš adaptér byl definovaný v konfiguračním souboru.
- Identifikujte komunikaci zařízení nižší úrovně, která je nutná pro podporu vlastností a příkazů komponenty.
- Určete, jak má adaptér analyzovat nezpracovaná data ze zařízení a převést je na typy telemetrie, které definuje definice rozhraní IoT technologie Plug and Play.
- Implementujte rozhraní můstk Adapter popsané výše.
- Přidejte nový adaptér k manifestu adaptéru a sestavte most.

### <a name="enable-a-new-bridge-adapter"></a>Povolit nový adaptér mostu

Adaptéry v mostu povolíte přidáním odkazu do [adapter_manifest. c](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/shared/adapter_manifest.c):

```c
  extern PNP_ADAPTER MyPnpAdapter;
  PPNP_ADAPTER PNP_ADAPTER_MANIFEST[] = {
    .
    .
    &MyPnpAdapter
  }
```

> [!IMPORTANT]
> Zpětná volání adaptérů mostu se volají postupně. Adaptér by neměl zablokovat zpětné volání, protože to brání v jádru mostu v průběhu provádění.

### <a name="sample-camera-adapter"></a>Ukázkový adaptér kamery

[Soubor Readme adaptéru kamery](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/Camera/readme.md) popisuje ukázkový adaptér kamery, který můžete povolit.

## <a name="build-and-run-the-bridge-on-an-iot-device-or-gateway"></a>Sestavení a spuštění mostu na zařízení nebo bráně IoT

| Platforma | Podporováno |
| :-----------: | :-----------: |
| Windows |  Ano |
| Linux | Ano |

### <a name="prerequisites"></a>Požadavky

K dokončení této části je potřeba na váš místní počítač nainstalovat následující software:

- Vývojové prostředí, které podporuje kompilování C++, jako je například [Visual Studio (Community, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) – nezapomeňte při instalaci sady Visual Studio zahrnout komponentu správce balíčků NuGet a **vývoj desktopových aplikací pomocí C++** .
- [Cmake](https://cmake.org/download/) – při instalaci cmake vyberte možnost **Přidat cmake do systémové cesty**.
- Pokud pracujete v systému Windows, musíte také stáhnout [sadu Windows 17763 SDK](https://developer.microsoft.com/windows/downloads/windows-10-sdk).

### <a name="source-code"></a>Zdrojový kód

Naklonujte úložiště [IoT technologie Plug and Play mostu](https://github.com/Azure/iot-plug-and-play-bridge) do místního počítače:

```cmd/sh
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

Očekává, že spuštění předchozího příkazu trvá několik minut.

> [!NOTE]
> Pokud narazíte na problémy s `git submodule update` selháním ve Windows, zkuste problém vyřešit pomocí následujícího příkazu: `git config --system core.longpaths true` .

### <a name="build-the-bridge-on-windows"></a>Sestavení mostu ve Windows

Otevřete **Developer Command Prompt pro VS 2019** a přejděte do složky, která obsahuje úložiště, které jste naklonoval, a spusťte následující příkazy:

```cmd
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

```cmd
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
| Windows |  Ne |
| Linux | Ano |

### <a name="prerequisites"></a>Požadavky

K dokončení této části potřebujete službu Azure IoT Hub úrovně Free nebo Standard. Informace o tom, jak vytvořit centrum IoT, najdete v tématu [vytvoření centra IoT](../iot-hub/iot-hub-create-through-portal.md).

Kroky v této části předpokládají, že máte na počítači s Windows 10 následující vývojové prostředí. Tyto nástroje vám umožní vytvořit a nasadit modul IoT Edge do zařízení IoT Edge:

- Subsystém Windows pro Linux (WSL) 2 se systémem Ubuntu 18,04 LTS. Další informace najdete v [instalační příručce k systému Windows pro Linux pro systém Windows 10](https://docs.microsoft.com/windows/wsl/install-win10).
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

- Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli-apt?view=azure-cli-latest&preserve-view=true) nainstalované ve vašem prostředí WSL 2 pro správu prostředků Azure.

  > [!TIP]
  > Pokud budete chtít, můžete spustit `az` příkazy v [Azure Cloud Shell](https://shell.azure.com/) , kde je rozhraní příkazového řádku předem nainstalováno.

### <a name="create-an-iot-edge-device"></a>Vytvoříte zařízení IoT Edge.

Tyto příkazy vytvoří IoT Edge zařízení běžící na virtuálním počítači Azure. Spuštění zařízení IoT Edge na virtuálním počítači je užitečné pro testování vašeho nasazení, pokud nemáte přístup k reálnému zařízení.

Pokud chcete ve službě IoT Hub vytvořit registraci zařízení IoT Edge, spusťte ve svém prostředí WSL 2 následující příkazy. Pomocí `az login` příkazu se přihlaste ke svému předplatnému Azure:

```bash
az iot hub device-identity create --device-id bridge-edge-device --edge-enabled true --hub-name {your IoT hub name}
```

Pokud chcete vytvořit virtuální počítač Azure s nainstalovaným modulem runtime IoT Edge, spusťte následující příkazy. Aktualizujte zástupné symboly pomocí vhodných hodnot:

```bash
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

```bash
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
ENV IOTHUB_DEVICE_CONNECTION_STRING="{Add your device connection string here}"
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

```bash
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

```bash
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

Seznam spuštěných modulů teď obsahuje modul **ModulePnpBridge** , který je nakonfigurovaný tak, aby používal adaptér pro rozpoznávání CO2.

### <a name="tidy-up"></a>Uklizený nahoru

Pokud chcete virtuální počítač a registr kontejnerů odebrat z předplatného Azure, spusťte následující příkaz:

```bash
az group delete -n bridge-edge-resources
```

## <a name="folder-structure"></a>Struktura složek

*pnpbridge\deps\azure-IoT-SDK-c-PNP*: podmoduly Git, které obsahují sadu SDK pro zařízení Azure IoT pro jazyk c SDK.

*pnpbridge\scripts*: vytváření skriptů.

*pnpbridge\src*: zdrojový kód pro IoT technologie Plug and Play mostu.

*pnpbridge\src\adapters*: zdrojový kód pro různé adaptéry pro mosty IoT technologie Plug and Play.

## <a name="next-steps"></a>Další kroky

Další informace o službě IoT technologie Plug and Play Bridge najdete v úložišti GitHub [technologie Plug and Play mostu IoT](https://github.com/Azure/iot-plug-and-play-bridge) .
