---
title: Azure IoT Hub zařízení streamy C# rychlý start pro SSH a protokolu RDP (preview) | Dokumentace Microsoftu
description: V tomto rychlém startu spustíte dvě ukázkové C# aplikace, které umožňují scénáře SSH a protokolu RDP přes proud zařízení služby IoT Hub.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 1d5fbb410a61419f6f6d2e80cdb1a16c07672fe9
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733346"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Rychlý start: Povolit SSH a protokolu RDP přes proud zařízení služby IoT Hub s využitím C# proxy aplikací (preview)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub v současné době podporuje datové proudy zařízení jako [funkce ve verzi preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Datové proudy zařízení služby IoT Hub](iot-hub-device-streams-overview.md) povolit zařízení a služeb aplikacím komunikovat způsobem, firewallem procházející a zabezpečené. Tato příručka rychlý start zahrnuje dvě C# aplikace, které umožňují provoz aplikaci klient server (například Secure Shell [SSH] a Remote Desktop Protocol [RDP] odešlou přes zařízení datového proudu, který je navázané prostřednictvím služby IoT hub. Přehled nastavení, naleznete v tématu [ukázkové aplikace místní proxy pro SSH nebo RDP](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Tento článek nejprve popisuje nastavení pro SSH (s použitím port 22) a pak popisuje, jak změnit nastavení portů pro protokol RDP. Protože zařízení datové proudy nezávisejí na aplikaci a protokol-, se stejným vzorkem lze upravit tak, aby vyhovovaly jiné typy provozu aplikací. Tato změna obvykle zahrnuje pouze změna portů pro komunikaci na ten, který je používán příslušnou aplikaci.

## <a name="how-it-works"></a>Jak to funguje

Následující obrázek ukazuje, jak povolit místní zařízení a místní služby proxy aplikace v této ukázce začátku do konce připojení mezi klienta SSH a procesy démon procesu SSH. Tady předpokládáme, že démon je spuštěn na stejném zařízení jako zařízení – místní proxy aplikací.

![Nastavení místní proxy aplikací](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. Místní služba proxy aplikace připojí k centru IoT a zahájí datovém proudu zařízení na cílové zařízení.

1. Místní zařízení proxy aplikace dokončí handshake zahájení datového proudu a vytvoří tunel začátku do konce datového proudu přes koncový bod streamování služby IoT hub na straně služby.

1. Místní zařízení proxy aplikace se připojí k proces démon programu SSH, který naslouchá na portu 22 na zařízení. Toto nastavení je možné konfigurovat, jak je popsáno v části "Spuštění zařízení místní proxy aplikace".

1. Místní služba proxy aplikace čeká na nová připojení SSH od uživatele prostřednictvím naslouchání na určeném portu, který v tomto případě je port 2222. Toto nastavení je možné konfigurovat, jak je popsáno v části "Spuštění místní služby proxy aplikace". Když uživatel připojí pomocí klienta SSH, tunelové propojení umožňuje provoz SSH aplikace přenos mezi SSH klientských a serverových aplikací.

> [!NOTE]
> Provoz SSH, který je odeslán přes zařízení datový proud je tunelovým propojením přes koncový bod streamování služby IoT hub spíše než odeslána přímo mezi službou a zařízení. Další informace najdete v tématu [výhody použití datových proudů zařízení služby Iot Hub](iot-hub-device-streams-overview.md#benefits).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Ve verzi preview streamovaných zařízení je momentálně podporována pouze pro IoT hub, které jsou vytvořeny v těchto oblastech:

  * USA – střed
  * Střed USA – EUAP

* Dvě ukázkové aplikace, které běží v rámci tohoto rychlého startu jsou zapsány pomocí C#. Budete potřebovat sadu .NET Core SDK 2.1.0 nebo novějším na vývojovém počítači.

  Můžete stáhnout [.NET Core SDK pro různé platformy z .NET](https://www.microsoft.com/net/download/all).

* Zkontrolujte aktuální verzi C# na vývojovém počítači s použitím následujícího příkazu:

    ```
    dotnet --version
    ```

* Spusťte následující příkaz pro přidání rozšíření Azure IoT pro Azure CLI do instance služby Cloud Shell. Přidá rozšíření IOT služby IoT Hub, IoT Edge a IoT zařízení zřizování služby (DPS)-konkrétní příkazy rozhraní příkazového řádku Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

* [Stáhněte si ukázku C# projektu](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)a extrahujte archiv ZIP.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu použijete Azure Cloud Shell zaregistrovat simulované zařízení.

1. K vytvoření identity zařízení, spusťte následující příkaz ve službě Cloud Shell:

   > [!NOTE]
   > * Nahradit *YourIoTHubName* zástupný symbol název, který zvolíte pro službu IoT hub.
   > * Použití *Mojezařízení*, jak je znázorněno. Jedná se o název pro registrovaná zařízení. Pokud zvolíte jiný název pro vaše zařízení, použijte tento název v rámci tohoto článku a aktualizujte název zařízení v ukázkové aplikace před spuštěním je.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Chcete-li získat *připojovací řetězec zařízení* pro zařízení, které jste právě zaregistrovali, spusťte následující příkazy ve službě Cloud Shell:

   > [!NOTE]
   > Nahradit *YourIoTHubName* zástupný symbol název, který zvolíte pro službu IoT hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení pro pozdější použití v rámci tohoto rychlého startu. Soubor bude vypadat jako v následujícím příkladu:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

1. Připojení ke službě IoT hub a vytvořit datový proud zařízení, musíte také *připojovací řetězec služby* ze služby IoT hub, aby aplikace na straně služby. Následující příkaz načte tuto hodnotu pro službu IoT hub:

   > [!NOTE]
   > Nahradit *YourIoTHubName* zástupný symbol název, který zvolíte pro službu IoT hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Všimněte si, vrácená hodnota pro pozdější použití v rámci tohoto rychlého startu. Soubor bude vypadat jako v následujícím příkladu:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>Přístup k zařízení prostřednictvím datových proudů zařízení přes SSH

V této části vytvoříte začátku do konce datového proudu k tunelování provoz SSH.

### <a name="run-the-device-local-proxy-application"></a>Spuštění aplikace proxy pro místní zařízení

Přejděte *zařízení – datové proudy – proxy serveru/zařízení* ve složce rozzipovaný projekt. Zachovat po ruce následující informace:

| Název argumentu | Hodnota argumentu |
|----------------|-----------------|
| `deviceConnectionString` | Připojovací řetězec zařízení, které jste vytvořili dříve. |
| `targetServiceHostName` | IP adresa, ve kterém naslouchá SSH server. Tato adresa by být `localhost` by šlo stejnou IP Adresou, ve kterém je spuštěná aplikace proxy pro místní zařízení. |
| `targetServicePort` | Port, který používá aplikační protokol (pro SSH, ve výchozím nastavení, by toto byl port 22).  |

Kompilace a spuštění kódu následujícím způsobem:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run $deviceConnectionString localhost 22

# In Windows
dotnet run %deviceConnectionString% localhost 22
```

### <a name="run-the-service-local-proxy-application"></a>Spuštění místní služby proxy aplikace

Přejděte na `device-streams-proxy/service` ve složce rozzipovaný projekt. Budete potřebovat následující informace užitečné:

| Název parametru | Hodnota parametru |
|----------------|-----------------|
| `iotHubConnectionString` | Připojovací řetězec služby IoT hub. |
| `deviceId` | Identifikátor zařízení, které jste vytvořili dříve. |
| `localPortNumber` | Místní port, který váš klient SSH se připojí k. Port 2222 v této ukázce používáme, ale můžete použít další libovolného čísla. |

Kompilace a spuštění kódu následujícím způsobem:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run $serviceConnectionString MyDevice 2222

# In Windows
dotnet run %serviceConnectionString% MyDevice 2222
```

### <a name="run-the-ssh-client"></a>Spuštění klienta SSH

Nyní použijte svou aplikaci klienta SSH a připojte se k místní služby proxy aplikace na port 2222 (namísto démon procesu SSH přímo).

```
ssh <username>@localhost -p 2222
```

V tomto okamžiku okno přihlášení SSH vás vyzve k zadání přihlašovacích údajů.

Výstup na konzole na straně služby (místní služby proxy aplikace naslouchá na portu 2222):

![Výstup aplikace místní služby proxy](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

Výstup na aplikaci proxy pro místní zařízení, které se připojí k proces démon programu SSH v konzole *IP_address:22*:

![Výstup aplikace proxy pro místní zařízení](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

Výstup konzoly z aplikace klienta SSH. Klient SSH komunikuje se démon procesu SSH propojením na port 22, který naslouchá místní služby proxy aplikace:

![Výstup aplikace klienta SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>Připojení RDP k zařízení prostřednictvím datových proudů zařízení

Instalační program pro protokol RDP je velmi podobné nastavení pro SSH (popsaných výše). Použití RDP cílová IP adresa a port 3389 místo a použití klienta RDP (místo klienta SSH).

### <a name="run-the-device-local-proxy-application-rdp"></a>Spuštění aplikace proxy pro místní zařízení (RDP)

Přejděte *zařízení – datové proudy – proxy serveru/zařízení* ve složce rozzipovaný projekt. Zachovat po ruce následující informace:

| Název argumentu | Hodnota argumentu |
|----------------|-----------------|
| `DeviceConnectionString` | Připojovací řetězec zařízení, které jste vytvořili dříve. |
| `targetServiceHostName` | Název hostitele nebo IP adresu, kde běží server protokolu RDP. Tato adresa by být `localhost` by šlo stejnou IP Adresou, ve kterém je spuštěná aplikace proxy pro místní zařízení. |
| `targetServicePort` | Port je používán aplikační protokol (pro protokol RDP, ve výchozím nastavení, by toto byl portu 3389).  |

Kompilace a spuštění kódu následujícím způsobem:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run $DeviceConnectionString localhost 3389

# In Windows
dotnet run %DeviceConnectionString% localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>Spuštění místní služby proxy aplikace (RDP)

Přejděte na `device-streams-proxy/service` ve složce rozzipovaný projekt. Budete potřebovat následující informace užitečné:

| Název parametru | Hodnota parametru |
|----------------|-----------------|
| `iotHubConnectionString` | Připojovací řetězec služby IoT hub. |
| `deviceId` | Identifikátor zařízení, které jste vytvořili dříve. |
| `localPortNumber` | Místní port, který váš klient SSH se připojí k. Port 2222 v této ukázce používáme, ale můžete to změnit na jiné libovolného čísla. |

Kompilace a spuštění kódu následujícím způsobem:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run $serviceConnectionString MyDevice 2222

# In Windows
dotnet run %serviceConnectionString% MyDevice 2222
```

### <a name="run-rdp-client"></a>Spuštění klienta protokolu RDP

Nyní pomocí RDP klientskou aplikaci a připojte se k místní služby proxy aplikace na port 2222 (to byla libovolného dostupný port, který jste zvolili dříve).

![Protokol RDP se připojí k místní služby proxy aplikace](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nastavení služby IoT hub, zaregistrovali zařízení, nasazené místních zařízení a místní služby proxy aplikace vytvořit datový proud zařízení prostřednictvím služby IoT hub a použít k tunelování SSH nebo RDP provoz proxy aplikací. Stejné paradigma může obsahovat jiné protokoly klient server, ve kterém server běží na zařízení (například, že démon procesu SSH).

Další informace o zařízení datových proudů, naleznete v tématu:

> [!div class="nextstepaction"]
> [Přehled datových proudů zařízení](./iot-hub-device-streams-overview.md)
