---
title: Rychlé zprovoznění zařízení Azure IoT Hub streamy v C# pro SSH a RDP
description: V tomto rychlém startu spustíte dvě ukázkové aplikace v C#, které umožňují scénáře SSH a RDP přes datový proud zařízení IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 3e53937122b8721aff5db435ac447b686ea16643
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92748692"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Rychlý Start: povolení SSH a RDP přes datový proud zařízení IoT Hub pomocí aplikace proxy v jazyce C# (Preview)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub aktuálně podporuje streamy zařízení jako [funkci ve verzi Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub datové proudy zařízení](iot-hub-device-streams-overview.md) umožňují aplikacím služeb a zařízením komunikovat zabezpečeným způsobem a bránou firewall. Tato příručka pro rychlý Start zahrnuje dvě aplikace v jazyce C#, které umožňují odeslání provozu aplikace na straně klienta (například Secure Shell [SSH] a protokol RDP (Remote Desktop Protocol) [RDP], aby se odesílaly přes datový proud zařízení, který je vytvořený prostřednictvím služby IoT Hub. Přehled nastavení najdete v tématu [Ukázka místní proxy aplikace pro SSH nebo RDP](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Tento článek nejdřív popisuje nastavení pro SSH (pomocí portu 22) a pak popisuje, jak změnit port instalace pro protokol RDP. Vzhledem k tomu, že proudy zařízení jsou aplikace a protokol nezávislá, je možné stejný vzorek upravit tak, aby vyhovoval jiným typům provozu aplikací. Tato změna obvykle zahrnuje pouze změnu komunikačního portu na ten, který používá zamýšlená aplikace.

## <a name="how-it-works"></a>Jak to funguje

Následující obrázek ukazuje, jak jsou v této ukázce místní aplikace proxy serveru a místní služby pro místní zařízení, umožňují koncové připojení mezi klientem SSH a procesy démona SSH. Tady předpokládáme, že démon běží na stejném zařízení jako aplikace pro místní proxy zařízení.

![Instalace místní proxy aplikace](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.png)

1. Místní proxy aplikace služby se připojí ke službě IoT Hub a inicializuje datový proud zařízení do cílového zařízení.

1. Místní proxy aplikace pro zařízení dokončí modul handshake iniciace streamování a vytvoří komplexní tunelové propojení prostřednictvím koncového bodu streamování služby IoT Hub na straně služby.

1. Místní proxy aplikace zařízení se připojí k procesu démon SSH, který naslouchá na portu 22 na zařízení. Toto nastavení je možné konfigurovat, jak je popsáno v části spuštění aplikace pro místní proxy zařízení.

1. Místní proxy aplikace služby čeká na nové připojení SSH od uživatele tím, že naslouchá na určeném portu, což je v tomto případě port 2222. Toto nastavení lze konfigurovat, jak je popsáno v části spuštění aplikace pro místní proxy server služby. Když se uživatel připojí přes klienta SSH, tunel umožňuje přenos aplikace SSH mezi klientem a serverovou aplikací SSH.

> [!NOTE]
> Provoz SSH, který se odesílá přes datový proud zařízení, se prochází tunelovým propojením přes koncový bod streamování IoT Hub, nikoli přímo mezi službou a zařízením. Další informace najdete v tématu [výhody použití datových proudů zařízení ve službě IoT Hub](iot-hub-device-streams-overview.md#benefits).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="prerequisites"></a>Předpoklady

* Verze Preview datových proudů zařízení je momentálně podporovaná jenom pro centra IoT, která jsou vytvořená v následujících oblastech:

  * Střední USA
  * Střed USA EUAP
  * Southeast Asia
  * Severní Evropa

* Dvě ukázkové aplikace, které spouštíte v rámci tohoto rychlého startu, jsou napsány v jazyce C#. Ve vývojovém počítači potřebujete .NET Core SDK 2.1.0 nebo novější.

  .NET Core SDK můžete stáhnout [pro více platforem od rozhraní .NET](https://www.microsoft.com/net/download/all).

* Ověřte aktuální verzi C# na vývojovém počítači pomocí následujícího příkazu:

    ```
    dotnet --version
    ```

* Spuštěním následujícího příkazu přidejte rozšíření Azure IoT pro Azure CLI do instance Cloud Shell. Rozšíření IOT přidá do Azure CLI příkazy, které jsou specifické pro služby IoT Hub, IoT Edge a IoT Device Provisioning (DPS).

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   ```azurecli-interactive
   az extension add --name azure-iot
   ```
[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* [Stáhněte si ukázky pro Azure IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)a Extrahujte archiv zip.

* Platný uživatelský účet a přihlašovací údaje v zařízení (Windows nebo Linux) používané k ověření uživatele.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrace zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu použijete Azure Cloud Shell k registraci simulovaného zařízení.

1. Chcete-li vytvořit identitu zařízení, spusťte následující příkaz v Cloud Shell:

   > [!NOTE]
   > * Zástupný text *YourIoTHubName* nahraďte názvem, který jste zvolili pro Centrum IoT.
   > * Pro název zařízení, které zaregistrujete, se doporučuje používat *mojezařízení* , jak je znázorněno na obrázku. Pokud pro své zařízení zvolíte jiný název, použijte tento název v celém rámci tohoto článku a aktualizujte název zařízení v ukázkových aplikacích ještě předtím, než je spustíte.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Pokud chcete získat *připojovací řetězec zařízení* pro zařízení, které jste právě zaregistrovali, spusťte v Cloud Shell následující příkazy:

   > [!NOTE]
   > Zástupný text *YourIoTHubName* nahraďte názvem, který jste zvolili pro Centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Poznamenejte si vrácený připojovací řetězec zařízení pro pozdější použití v tomto rychlém startu. Soubor bude vypadat jako v následujícím příkladu:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

1. Pokud se chcete připojit ke službě IoT Hub a vytvořit datový proud zařízení, budete k povolení aplikace na straně služby potřebovat také *připojovací řetězec služby* z vašeho centra IoT. Následující příkaz načte tuto hodnotu služby IoT Hub:

   > [!NOTE]
   > Zástupný text *YourIoTHubName* nahraďte názvem, který jste zvolili pro Centrum IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Všimněte si vráceného připojovacího řetězce služby pro pozdější použití v tomto rychlém startu. Soubor bude vypadat jako v následujícím příkladu:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH pro zařízení prostřednictvím datových proudů zařízení

V této části vytvoříte ucelený Stream pro tunelování provozu SSH.

### <a name="run-the-device-local-proxy-application"></a>Spuštění aplikace místní proxy zařízení

V místním okně terminálu přejděte do `device-streams-proxy/device` adresáře ve složce getproject. Mějte na ruce následující informace:

| Název argumentu | Hodnota argumentu |
|----------------|-----------------|
| `DeviceConnectionString` | Připojovací řetězec zařízení pro zařízení, které jste vytvořili dříve. |
| `targetServiceHostName` | IP adresa, kde naslouchá Server SSH. Tato adresa by byla `localhost` stejná jako IP, kde je spuštěná místní proxy aplikace zařízení. |
| `targetServicePort` | Port, který používá váš aplikační protokol (pro SSH, ve výchozím nastavení je to port 22).  |

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

### <a name="run-the-service-local-proxy-application"></a>Spuštění aplikace místní proxy serveru služby

V jiném místním okně terminálu přejděte do `iot-hub/quickstarts/device-streams-proxy/service` složky projektu unzip. Mějte na ruce následující informace:

| Název parametru | Hodnota parametru |
|----------------|-----------------|
| `ServiceConnectionString` | Připojovací řetězec služby vašeho IoT Hub. |
| `MyDevice` | Identifikátor zařízení, které jste vytvořili dříve. |
| `localPortNumber` | Místní port, ke kterému se bude klient SSH připojovat. V této ukázce používáme port 2222, ale můžete použít i jiná libovolná čísla. |

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

### <a name="run-the-ssh-client"></a>Spustit klienta SSH

Teď použijte klientskou aplikaci SSH a připojte se k místnímu proxy aplikaci služby na portu 2222 (namísto démona SSH přímo).

```
ssh {username}@localhost -p 2222
```

V tomto okamžiku se zobrazí okno přihlášení SSH s výzvou k zadání přihlašovacích údajů.

Výstup na konzole na straně služby (místní proxy aplikace služby naslouchá na portu 2222):

![Výstup místní proxy aplikace služby](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

Výstup na konzole v aplikaci proxy místní zařízení, která se připojí k procesu démon SSH na *IP_address: 22* :

![Výstup aplikace pro místní proxy zařízení](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

Výstup konzoly klientské aplikace SSH Klient SSH komunikuje s démonem SSH připojením k portu 22, na kterém aplikace proxy místní služby naslouchá:

![Výstup klientské aplikace SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>Protokol RDP na zařízení prostřednictvím datových proudů zařízení

Instalace pro protokol RDP je podobná nastavení pro SSH (popsané výše). Místo toho použijte cílovou IP adresu RDP a port 3389 a použijte klienta RDP (místo klienta SSH).

### <a name="run-the-device-local-proxy-application-rdp"></a>Spuštění aplikace místního proxy serveru (RDP) pro zařízení

V místním okně terminálu přejděte do `device-streams-proxy/device` adresáře ve složce getproject. Mějte na ruce následující informace:

| Název argumentu | Hodnota argumentu |
|----------------|-----------------|
| `DeviceConnectionString` | Připojovací řetězec zařízení pro zařízení, které jste vytvořili dříve. |
| `targetServiceHostName` | Název hostitele nebo IP adresa, kde je spuštěn server RDP. Tato adresa by byla `localhost` stejná jako IP, kde je spuštěná místní proxy aplikace zařízení. |
| `targetServicePort` | Port, který používá váš aplikační protokol (pro protokol RDP, ve výchozím nastavení je to port 3389).  |

Zkompilujte a spusťte kód pomocí následujících příkazů:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 3389

# In Windows
dotnet run {DeviceConnectionString} localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>Spuštění aplikace místní proxy serveru služby (RDP)

V jiném místním okně terminálu přejděte do `device-streams-proxy/service` složky projektu unzip. Mějte na ruce následující informace:

| Název parametru | Hodnota parametru |
|----------------|-----------------|
| `ServiceConnectionString` | Připojovací řetězec služby vašeho IoT Hub. |
| `MyDevice` | Identifikátor zařízení, které jste vytvořili dříve. |
| `localPortNumber` | Místní port, ke kterému se bude klient SSH připojovat. V této ukázce používáme port 2222, ale můžete ho změnit na další libovolná čísla. |

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

Teď použijte klientskou aplikaci RDP a připojte se k aplikaci místního proxy serveru služby na portu 2222 (to bylo libovolný dostupný port, který jste zvolili dříve).

![Připojení RDP k aplikaci proxy místní služby](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu nastavíte službu IoT Hub, zaregistrovali zařízení, zavedli místní proxy a místní proxy služby pro zařízení, aby se navázal datový proud zařízení prostřednictvím služby IoT Hub, a používali proxy aplikace pro tunelování provozu SSH nebo RDP. Stejné paradigma může vyhovovat jiným protokolům klienta a serveru, kde server běží na zařízení (například démon SSH).

Další informace o datových proudech zařízení najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Přehled streamů zařízení](./iot-hub-device-streams-overview.md)
