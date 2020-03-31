---
title: Azure IoT Hub zařízení datové proudy C# rychlý start pro SSH a RDP
description: V tomto rychlém startu spustíte dvě ukázkové aplikace Jazyka C#, které umožňují scénáře SSH a RDP přes datový proud zařízení ioT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: c8ef6a87e839e6d8dfb296e7b24f3d0d95843d1c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675463"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Úvodní příručka: Povolení SSH a RDP přes datový proud zařízení služby IoT Hub pomocí proxy aplikace Jazyka C# (preview)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub aktuálně podporuje datové proudy zařízení jako [funkci náhledu](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Datové proudy zařízení služby IoT Hub umožňují aplikacím služby](iot-hub-device-streams-overview.md) a zařízení komunikovat bezpečným způsobem a způsobem, který je vhodný pro bránu firewall. Tento průvodce rychlým startem zahrnuje dvě aplikace jazyka C#, které umožňují odesílat přenosy aplikací klient-server (například zabezpečené prostředí [SSH] a protokol RDP vzdálené plochy [RDP] prostřednictvím datového proudu zařízení vytvořeného prostřednictvím služby IoT hub. Přehled nastavení naleznete v tématu [Ukázka místní proxy aplikace pro SSH nebo RDP](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Tento článek nejprve popisuje nastavení pro SSH (pomocí portu 22) a potom popisuje, jak upravit port instalace pro RDP. Vzhledem k tomu, že datové proudy zařízení jsou nezávislá na aplikacích a protokolech, lze stejnou ukázku upravit tak, aby vyhovovala jiným typům provozu aplikací. Tato změna obvykle zahrnuje pouze změnu komunikačníport na ten, který používá zamýšlené aplikace.

## <a name="how-it-works"></a>Jak to funguje

Následující obrázek znázorňuje, jak místní a místní proxy aplikace zařízení v této ukázce umožňují koncové připojení mezi klientem SSH a procesy demonu SSH. Zde předpokládáme, že daemon běží na stejném zařízení jako místní proxy aplikace zařízení.

![Nastavení místní proxy aplikace](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. Aplikace proxy místní služby se připojí k centru IoT a zahájí datový proud zařízení do cílového zařízení.

1. Aplikace proxy místní zařízení dokončí handshake zahájení datového proudu a vytvoří tunel streamování začátku do konce prostřednictvím koncového bodu streamování služby IoT hub na straně služby.

1. Aplikace proxy místní zařízení se připojí k daemonu SSH, který naslouchá na portu 22 v zařízení. Toto nastavení je konfigurovatelné, jak je popsáno v části Spuštění aplikace proxy místní zařízení.

1. Aplikace proxy místní služby čeká na nová připojení SSH od uživatele nasloucháním na určeném portu, což je v tomto případě port 2222. Toto nastavení je konfigurovatelné, jak je popsáno v části Spustit aplikaci proxy místní služby. Když se uživatel připojí prostřednictvím klienta SSH, tunelové propojení umožňuje přenos aplikací SSH, který má být přenášen mezi klientem SSH a serverovou aplikací.

> [!NOTE]
> Provoz SSH, který je odeslán prostřednictvím datového proudu zařízení, je tunelován přes koncový bod streamování služby IoT hub, nikoli odesílány přímo mezi službou a zařízením. Další informace najdete v [tématu výhody používání datových proudů zařízení služby Iot Hub](iot-hub-device-streams-overview.md#benefits).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

* Náhled datových proudů zařízení je aktuálně podporován pouze pro centra IoT, které jsou vytvořeny v následujících oblastech:

  * USA – střed
  * Centrální US EUAP
  * Jihovýchodní Asie
  * Severní Evropa

* Dvě ukázkové aplikace, které spustíte v tomto rychlém startu jsou zapsány v C#. Potřebujete .NET Core SDK 2.1.0 nebo novější ve vývojovém počítači.

  [Sada .NET Core SDK pro více platforem](https://www.microsoft.com/net/download/all)můžete stáhnout z rozhraní .NET .

* Ověřte aktuální verzi jazyka C# ve vývojovém počítači pomocí následujícího příkazu:

    ```
    dotnet --version
    ```

* Spusťte následující příkaz a přidejte rozšíření Azure IoT pro Azure CLI do instance Cloud Shellu. Rozšíření IOT přidá do rozhraní příkazového příkazu Azure CLI služby IoT Hub, IoT Edge a Služby zřizování zařízení IoT (DPS).

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   ```azurecli-interactive
   az extension add --name azure-iot
   ```
[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* [Stáhněte si ukázky Azure IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)a extrahujte archiv ZIP.

* Platný uživatelský účet a pověření v zařízení (Windows nebo Linux) použité k ověření uživatele.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu použijete Azure Cloud Shell k registraci simulované zařízení.

1. Chcete-li vytvořit identitu zařízení, spusťte v prostředí Cloud Shell následující příkaz:

   > [!NOTE]
   > * Nahraďte zástupný symbol *YourIoTHubName* názvem, který jste zvolili pro centrum IoT.
   > * Pro název zařízení, které registrujete, je doporučeno použít *MyDevice,* jak je znázorněno. Pokud pro své zařízení zvolíte jiný název, použijte tento název v celém tomto článku a aktualizujte název zařízení v ukázkových aplikacích před jejich spuštěním.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Chcete-li získat *připojovací řetězec zařízení* pro zařízení, které jste právě zaregistrovali, spusťte v prostředí Cloud Shell následující příkazy:

   > [!NOTE]
   > Nahraďte zástupný symbol *YourIoTHubName* názvem, který jste zvolili pro centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Všimněte si vrácené zařízení připojovací řetězec pro pozdější použití v tomto rychlém startu. Soubor bude vypadat jako v následujícím příkladu:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

1. Chcete-li se připojit k centru IoT hub a vytvořit datový proud zařízení, potřebujete také *připojovací řetězec služby* z centra IoT hub, abyste povolili aplikaci na straně služby. Následující příkaz načte tuto hodnotu pro vaše služby IoT hub:

   > [!NOTE]
   > Nahraďte zástupný symbol *YourIoTHubName* názvem, který jste zvolili pro centrum IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Všimněte si vrácené služby připojovací řetězec pro pozdější použití v tomto rychlém startu. Soubor bude vypadat jako v následujícím příkladu:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH do zařízení prostřednictvím datových proudů zařízení

V této části vytvoříte end-to-end datový proud pro tunelové propojení s provozem SSH.

### <a name="run-the-device-local-proxy-application"></a>Spuštění aplikace proxy místního zařízení

V místním okně terminálu `device-streams-proxy/device` přejděte do adresáře ve složce projektu, který je rozepnut. Mějte po ruce následující informace:

| Název argumentu | Hodnota argumentu |
|----------------|-----------------|
| `DeviceConnectionString` | Připojovací řetězec zařízení, které jste vytvořili dříve. |
| `targetServiceHostName` | IP adresa, na které naslouchá server SSH. Adresa by `localhost` byla, pokud by se jednalo o stejnou adresu IP, kde je spuštěna místní proxy aplikace zařízení. |
| `targetServicePort` | Port, který používá protokol aplikace (pro SSH, ve výchozím nastavení by to byl port 22).  |

Zkompilujte a spusťte kód pomocí následujících příkazů:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 22

# In Windows
dotnet run {DeviceConnectionString} localhost 22
```

### <a name="run-the-service-local-proxy-application"></a>Spuštění aplikace proxy místní služby

V jiném okně místního `iot-hub/quickstarts/device-streams-proxy/service` terminálu přejděte do rozepnuté složky projektu. Mějte po ruce následující informace:

| Název parametru | Hodnota parametru |
|----------------|-----------------|
| `ServiceConnectionString` | Připojovací řetězec služby služby služby ioT hub. |
| `MyDevice` | Identifikátor zařízení, které jste vytvořili dříve. |
| `localPortNumber` | Místní port, ke kterému se klient SSH připojí. V této ukázce používáme port 2222, ale můžete použít jiná libovolná čísla. |

Zkompilujte a spusťte kód pomocí následujících příkazů:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-the-ssh-client"></a>Spuštění klienta SSH

Nyní použijte klientskou aplikaci SSH a připojte se k místní proxy aplikaci na portu 2222 (namísto přímo daemonu SSH).

```
ssh {username}@localhost -p 2222
```

V tomto okamžiku okno přihlášení SSH vyzve k zadání pověření.

Výstup konzoly na straně služby (aplikace proxy místní služby naslouchá na portu 2222):

![Výstup aplikace proxy místní služby](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

Výstup konzoly v místní proxy aplikaci zařízení, která se připojuje k daemonu SSH v *IP_address:22*:

![Výstup aplikace proxy místního zařízení](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

Výstup konzoly klientské aplikace SSH. Klient SSH komunikuje s daemonem SSH připojením k portu 22, na kterém naslouchá místní proxy aplikace služby:

![Výstup klientské aplikace SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>RDP do zařízení prostřednictvím datových proudů zařízení

Nastavení pro RDP je podobné nastavení pro SSH (popsané výše). Místo toho použijete cílovou IP adresu RDP a port 3389 a místo klienta SSH (místo klienta SSH).

### <a name="run-the-device-local-proxy-application-rdp"></a>Spuštění místní proxy aplikace (RDP)

V místním okně terminálu `device-streams-proxy/device` přejděte do adresáře ve složce projektu, který je rozepnut. Mějte po ruce následující informace:

| Název argumentu | Hodnota argumentu |
|----------------|-----------------|
| `DeviceConnectionString` | Připojovací řetězec zařízení, které jste vytvořili dříve. |
| `targetServiceHostName` | Název hostitele nebo adresa IP, na které je spuštěn server RDP. Adresa by `localhost` byla, pokud by se jednalo o stejnou adresu IP, kde je spuštěna místní proxy aplikace zařízení. |
| `targetServicePort` | Port používaný protokolem aplikace (pro protokol RDP by ve výchozím nastavení byl port 3389).  |

Zkompilujte a spusťte kód pomocí následujících příkazů:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 3389

# In Windows
dotnet run {DeviceConnectionString} localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>Spuštění aplikace proxy místní služby (RDP)

V jiném okně místního `device-streams-proxy/service` terminálu přejděte do rozepnuté složky projektu. Mějte po ruce následující informace:

| Název parametru | Hodnota parametru |
|----------------|-----------------|
| `ServiceConnectionString` | Připojovací řetězec služby služby služby ioT hub. |
| `MyDevice` | Identifikátor zařízení, které jste vytvořili dříve. |
| `localPortNumber` | Místní port, ke kterému se klient SSH připojí. V této ukázce používáme port 2222, ale můžete jej upravit na jiná libovolná čísla. |

Zkompilujte a spusťte kód pomocí následujících příkazů:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-rdp-client"></a>Spustit klienta RDP

Nyní použijte klientskou aplikaci RDP a připojte se k místní aplikaci proxy služby na portu 2222 (jednalo se o libovolný dostupný port, který jste zvolili dříve).

![Rdp se připojuje k aplikaci proxy místní služby](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu nastavíte službu IoT hub, zaregistrujete zařízení, nasadíte místní a místní proxy aplikace zařízení k vytvoření datového proudu zařízení prostřednictvím služby IoT hub a použijete proxy aplikace k tunelování přenosů SSH nebo RDP. Stejné paradigma může pojmout další protokoly klient-server, kde server běží na zařízení (například daemon SSH).

Další informace o streamech zařízení najdete v tématu:

> [!div class="nextstepaction"]
> [Přehled datových proudů zařízení](./iot-hub-device-streams-overview.md)
