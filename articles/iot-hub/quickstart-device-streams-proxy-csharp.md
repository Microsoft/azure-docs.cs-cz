---
title: Azure IoT Hub zařízení datové proudy C pro rychlý start pro SSH nebo RDP (preview) | Dokumentace Microsoftu
description: V tomto rychlém startu spustíte dvě ukázkové C# aplikace, které umožňují scénáře SSH nebo RDP přes proud zařízení služby IoT Hub.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: e7cb8b2d699418b4d70d60f19a3a60ce0c7b8d38
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888664"
---
# <a name="quickstart-sshrdp-over-iot-hub-device-streams-using-c-proxy-applications-preview"></a>Rychlý start: SSH nebo RDP over zařízení služby IoT Hub toků pomocí C# proxy aplikací (preview)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

[Datové proudy zařízení služby IoT Hub](./iot-hub-device-streams-overview.md) povolit zařízení a služeb aplikacím komunikovat způsobem, firewallem procházející a zabezpečené. Tato příručka rychlý start zahrnuje dvě C# programům, které umožňují provoz aplikace klient/server (třeba RDP nebo SSH) k odeslání v rámci datového proudu zařízení, který je navázané prostřednictvím služby IoT Hub. Zobrazit [tady](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) přehledné informace o nastavení.

Nejprve popisujeme nastavení pro SSH (s použitím port 22). Pak zjistíte, jak změnit nastavení portů pro protokol RDP. Protože zařízení datové proudy jsou aplikace a bez ohledu na protokol, se stejným vzorkem lze upravit tak, aby vyhovovaly jiné typy provozu aplikace. Tento postup obvykle pouze zahrnuje Změna portu, který používají příslušnou aplikaci.


## <a name="how-it-works"></a>Jak to funguje?

Na následujícím obrázku vidíte nastavení proxy sady místních zařízení a služby v této ukázce se jak povolit začátku do konce připojení mezi klientem SSH a proces démon programu SSH. Tady předpokládáme, že démon je spuštěn na stejném zařízení jako proxy pro místní zařízení.

![Alternativní text](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg "nastavení místní proxy server")

1. Místní služba proxy připojí k centru IoT a zahájí datovém proudu zařízení na cílové zařízení pomocí jeho ID zařízení.

2. Místní zařízení proxy dokončení inicializace handshake datového proudu a vytvoří tunel začátku do konce datového proudu přes koncový bod streamování služby IoT Hub na straně služby.

3. Místní zařízení proxy serveru se připojí k démon procesu SSH (SSHD) naslouchá na portu 22 na zařízení (Tento port jde nakonfigurovat, jak je popsáno [níže](#run-the-device-side-application)).

4. Místní služba proxy čeká pro nová připojení SSH od uživatele prostřednictvím naslouchání na určeném portu, který v tomto případě je port 2222 (Toto se taky dají konfigurovat, jak je popsáno [níže](#run-the-service-side-application)). Když uživatel připojí pomocí klienta SSH, tunelové propojení umožňuje aplikační provoz se vyměňují mezi programy klientem a serverem SSH.

> [!NOTE]
> Provoz SSH odesílání datového proudu se tunelové propojení prostřednictvím služby IoT Hub koncový bod streamování spíše než odesílaných přímo mezi službou a zařízení. To poskytuje [tyto výhody](./iot-hub-device-streams-overview.md#benefits).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Dvě ukázkové aplikace, které spustíte v tomto rychlém startu, jsou napsány pomocí C#. Na počítači používaném pro vývoj musíte mít .NET Core SDK 2.1.0 nebo vyšší.

Sadu .NET Core SDK pro různé platformy si můžete stáhnout z webu [.NET](https://www.microsoft.com/net/download/all).

Aktuální verzi C# na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

```
dotnet --version
```

Stáhněte si ukázkový projekt C# z https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip a extrahujte archiv ZIP.


## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Ve službě Azure Cloud Shell spusťte následující příkazy pro přidání rozšíření rozhraní příkazového řádku IoT Hub a vytvoření identity zařízení. 

   **YourIoTHubName**: Nahraďte tento zástupný text pod názvem, který jste vybrali pro službu IoT hub.

   **Mojezařízení**: Toto je název pro registrovaná zařízení. Použijte Mojezařízení, jak je znázorněno. Pokud si zvolíte jiný název zařízení, budete ho muset používat v průběhu celého článku a aktualizovat název zařízení v ukázkových aplikacích, než je spustíte.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Spuštěním následujícího příkazu ve službě Azure Cloud Shell získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

   **YourIoTHubName**: Nahraďte tento zástupný text pod názvem, který jste vybrali pro službu IoT hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, která vypadá jako v následujícím příkladu:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu.

3. Musíte také _připojovací řetězec služby_ ze služby IoT hub umožňuje aplikace na straně služby pro připojení ke službě IoT hub a vytvořit datový proud zařízení. Následující příkaz načte tuto hodnotu pro službu IoT hub:

   **YourIoTHubName**: Nahraďte tento zástupný text pod názvem, který jste vybrali pro službu IoT hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --hub-name YourIoTHubName
    ```

    Poznamenejte si vrácené hodnoty, který vypadá takto:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`
    

## <a name="ssh-to-a-device-via-device-streams"></a>Přístup k zařízení prostřednictvím datových proudů zařízení přes SSH

### <a name="run-the-service-side-proxy"></a>Spusťte službu na straně serveru proxy

Přejděte na `device-streams-proxy/service` ve složce rozzipovaný projekt. Budete potřebovat následující informace užitečné:

| Název parametru | Hodnota parametru |
|----------------|-----------------|
| `iotHubConnectionString` | Připojovací řetězec služby IoT hub. |
| `deviceId` | Identifikátor zařízení, které jste vytvořili dříve. |
| `localPortNumber` | Místní port, kde se váš klient SSH připojit ke. Port 2222 v této ukázce používáme, ale můžete to změnit na jiné libovolného čísla. |

Kompilace a spuštění kódu následujícím způsobem:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run $serviceConnectionString MyDevice 2222

# In Windows
dotnet run %serviceConnectionString% MyDevice 2222
```

### <a name="run-the-device-local-proxy"></a>Spusťte místní zařízení proxy serveru

Přejděte na `device-streams-proxy/device` ve složce rozzipovaný projekt. Budete potřebovat následující informace užitečné:

| Název argumentu | Hodnota argumentu |
|----------------|-----------------|
| `deviceConnectionString` | Připojovací řetězec zařízení, které jste vytvořili dříve. |
| `targetServiceHostName` | IP adresa, ve kterém naslouchá SSH server (to může být `localhost` Pokud stejnou IP Adresou, ve kterém je spuštěný proxy server místní zařízení). |
| `targetServicePort` | Port je používán aplikační protokol (ve výchozím nastavení, jde port 22 pro SSH).  |

Kompilace a spuštění kódu následujícím způsobem:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run $deviceConnectionString localhost 22

# In Windows
dotnet run %deviceConnectionString% localhost 22
```

Teď pomocí programu klienta SSH a připojte se k proxy serveru místní služby na port 2222 (namísto démon procesu SSH přímo). 

```
ssh <username>@localhost -p 2222
```

V tomto okamžiku zobrazí se výzva k přihlášení SSH k zadání přihlašovacích údajů.

Výstup na straně služby (service místní proxy server naslouchá na portu 2222) konzoly:

![Alternativní text](./media/quickstart-device-streams-proxy-csharp/service-console-output.png "výstup místní službu proxy serveru")

Výstup na místní zařízení proxy serveru, který se připojuje k proces démon programu SSH v konzole `IP_address:22`:

![Alternativní text](./media/quickstart-device-streams-proxy-csharp/device-console-output.png "výstupní zařízení místní proxy server")

Výstup programu klienta SSH konzole (klient SSH komunikuje se proces démon programu SSH na port 22, kde služba místní proxy server naslouchá připojením):

![Alternativní text](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png "výstup programu klienta SSH")

## <a name="rdp-to-a-device-via-device-streams"></a>Připojení RDP k zařízení prostřednictvím datových proudů zařízení

Instalační program pro protokol RDP je velmi podobný SSH (popsaných výše). V podstatě musíme místo toho použít RDP cílová IP adresa a port 3389 a použití klienta RDP (místo klienta SSH).

### <a name="run-the-service-side-application"></a>Spuštění aplikace straně služby

Přejděte na `device-streams-proxy/service` ve složce rozzipovaný projekt. Budete potřebovat následující informace užitečné:

| Název parametru | Hodnota parametru |
|----------------|-----------------|
| `iotHubConnectionString` | Připojovací řetězec služby IoT hub. |
| `deviceId` | Identifikátor zařízení, které jste vytvořili dříve. |
| `localPortNumber` | Místní port, kde se váš klient SSH připojit ke. Port 2222 v této ukázce používáme, ale můžete to změnit na jiné libovolného čísla. |

Kompilace a spuštění kódu následujícím způsobem:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run $serviceConnectionString MyDevice 2222

# In Windows
dotnet run %serviceConnectionString% MyDevice 2222
```

### <a name="run-the-device-side-application"></a>Spuštění aplikace straně zařízení

Přejděte na `device-streams-proxy/device` ve složce rozzipovaný projekt. Budete potřebovat následující informace užitečné:

| Název argumentu | Hodnota argumentu |
|----------------|-----------------|
| `DeviceConnectionString` | Připojovací řetězec zařízení, které jste vytvořili dříve. |
| `targetServiceHostName` | Název hostitele nebo IP adresu, kde běží server protokolu RDP (to může být `localhost` Pokud stejnou IP Adresou, ve kterém je spuštěný proxy server místní zařízení). |
| `targetServicePort` | Port je používán aplikační protokol (ve výchozím nastavení, jde portu 3389 pro RDP).  |

Kompilace a spuštění kódu následujícím způsobem:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux/MacOS
dotnet run $DeviceConnectionString localhost 3389

# In Windows
dotnet run %DeviceConnectionString% localhost 3389
```

Teď pomocí programu klienta protokolu RDP a připojení k proxy serveru místní služby na port 2222 (bylo k dispozici libovolného portu, který jste zvolili dříve).

![Alternativní text](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.PNG "protokolu RDP se připojí k místní službě proxy")

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nastavení služby IoT hub, zaregistrovali zařízení, nasazení zařízení – a místní službu proxy program vytvořit datový proud zařízení prostřednictvím služby IoT Hub a používají proxy servery na tunel SSH nebo provoz protokolu RDP. Stejné paradigma může obsahovat jiné protokoly klient/server (Pokud server běží na zařízení, například proces démon programu SSH).

Další informace o datových proudů zařízení pomocí odkazů níže:

> [!div class="nextstepaction"]
> [Přehled datových proudů zařízení](./iot-hub-device-streams-overview.md)
