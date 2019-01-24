---
title: IoT Hub zařízení datové proudy C rychlý start Azure (preview) | Dokumentace Microsoftu
description: V tomto rychlém startu spustíte aplikací na straně služby C, kteří komunikují s zařízení IoT prostřednictvím datového proudu zařízení.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: 149d3e3515c8bbc10798ad26e58dcefbe830f9bc
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830220"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Rychlý start: Komunikace na zařízení aplikaci v jazyce C prostřednictvím datových proudů zařízení služby IoT Hub (preview)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

[Datové proudy zařízení služby IoT Hub](./iot-hub-device-streams-overview.md) povolit zařízení a služeb aplikacím komunikovat způsobem, firewallem procházející a zabezpečené. Ve verzi public preview je v SDK pro jazyk C podporuje pouze datové proudy zařízení na straně zařízení. Tento rychlý start v důsledku toho pokrývá jenom pokyny ke spuštění aplikace straně zařízení. Když spustíte doprovodné aplikace straně služby, která je k dispozici v [ C# rychlý Start](./quickstart-device-streams-echo-csharp.md) nebo [rychlý úvod k Node.js](./quickstart-device-streams-echo-nodejs.md) vodítka.

Aplikace C straně zařízení v rámci tohoto rychlého startu má následující funkce:

* Vytvořte datový proud zařízení na zařízení IoT.

* Získávat data z na straně služby a odezvu ji zpět.

Zahájení procesu zařízení datového proudu, jakož i jak používat k odesílání a příjmu dat vám ukáže, kód.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Nainstalujte [Visual Studio 2017](https://www.visualstudio.com/vs/) s povolenou sadou funkcí [Vývoj desktopových aplikací pomocí C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).
* Nainstalujte nejnovější verzi [Git](https://git-scm.com/download/).

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

Pro účely tohoto rychlého startu budete používat [sadu SDK pro zařízení Azure IoT pro jazyk C](iot-hub-device-sdk-c-intro.md). Připravte vývojové prostředí klonovat a vytvářet [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) z Githubu. Ukázkový kód, který se používá v tomto rychlém startu, je součástí sady SDK na GitHubu. 


1. Stáhněte si verzi 3.11.4 [sestavovací systém CMake](https://cmake.org/download/). Stažený binární soubor ověřte pomocí odpovídající kryptografické hodnoty hash. Následující příklad používá Windows PowerShell k ověření kryptografické hodnoty hash pro verzi 3.11.4 distribuce x64 MSI:

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    V době psaní tohoto textu byly na webu CMake uvedené tyto hodnoty hash pro verzi 3.11.4:

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

2. Otevřete prostředí příkazového řádku nebo Git Bash. Spusťte následující příkaz pro naklonování úložiště GitHub sady [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c):
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Velikost tohoto úložiště je aktuálně přibližně 220 MB.

3. V kořenovém adresáři úložiště Git vytvořte podadresář `cmake` a přejděte do této složky. 

    ```
    cd azure-iot-sdk-c
    git checkout public-preview
    mkdir cmake
    cd cmake
    ```

4. Spuštěním následujícího příkazu sestavte verzi sady SDK určenou pro platformu vašeho vývojového klienta. Ve Windows, řešení sady Visual Studio pro simulované zařízení se vygeneruje `cmake` adresáře. 

```
    # In Linux
    cmake -Denable_streaming=ON ..
    make -j
```

Ve Windows spusťte následující příkazy v Developer Command Prompt pro Visual Studio 2015 nebo 2017 řádku:

```
    # In Windows
    # For VS2015
    cmake -Denable_streaming=ON .. -G "Visual Studio 15 2015"
    
    # Or for VS2017
    cmake -Denable_streaming=ON .. -G "Visual Studio 15 2017

    # Then build the project
    cmake --build . -- /m /p:Configuration=Release
```

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V této části zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell s [rozšířením IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest).

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


## <a name="communicate-between-device-and-service-via-device-streams"></a>Komunikaci mezi zařízením a službou prostřednictvím datových proudů zařízení

### <a name="run-the-device-side-application"></a>Spuštění aplikace straně zařízení

Ke spuštění aplikace straně zařízení, je třeba provést následující kroky:
- Nastavení vývojového prostředí pomocí pokynů v tomto [článek o datové proudy zařízení](https://github.com/Azure/azure-iot-sdk-c-tcpstreaming/blob/master/iothub_client/readme.md#compiling-the-device-sdk-for-c).

- Zadejte svoje přihlašovací údaje zařízení tak, že upravíte soubor zdrojového `iothub_client/samples/iothub_client_c2d_streaming_sample/iothub_client_c2d_streaming_sample.c` a zadejte připojovací řetězec zařízení.
```C
  /* Paste in the your iothub connection string  */
  static const char* connectionString = "[device connection string]";
```

- Kompilujte kód následujícím způsobem:

```
  # In Linux
  # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
  make -j


  # In Windows
  # Go to the cmake folder at the root of repo
  cmake --build . -- /m /p:Configuration=Release
```

- Spusťte zkompilovaný program:

```
  # In Linux
  # Go to sample's folder
  cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
  ./iothub_client_c2d_streaming_sample


  # In Windows
  # Go to sample's release folder
  cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
  iothub_client_c2d_streaming_sample.exe
```

### <a name="run-the-service-side-application"></a>Spuštění aplikace straně služby

Jak už bylo zmíněno dříve, C SDK pro IoT Hub podporuje datové proudy zařízení pouze na straně zařízení. Pro aplikaci straně služby používat k dispozici v programy služby accompaying [ C# rychlý Start](./quickstart-device-streams-echo-csharp.md) nebo [rychlý úvod k Node.js](./quickstart-device-streams-echo-nodejs.md) vodítka.


## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nastavení služby IoT hub, zaregistrovali zařízení, odeslali Simulovaná telemetrická data do centra pomocí aplikace v jazyce C a čtení telemetrických dat z centra pomocí Azure Cloud Shell.

Další informace o datových proudů zařízení pomocí odkazů níže:

> [!div class="nextstepaction"]
> [Přehled datových proudů zařízení](./iot-hub-device-streams-overview.md)