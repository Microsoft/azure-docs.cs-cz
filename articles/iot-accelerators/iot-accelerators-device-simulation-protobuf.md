---
title: Protocol Buffers pomocí simulace zařízení – Azure | Dokumentace Microsoftu
description: V této příručce s postupy se dozvíte, jak k serializaci telemetrická data odesílaná z akcelerátoru řešení simulace zařízení pomocí Protocol Buffers.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: 64470a1497a287f4cc2c3ef3ed29986382aeac9b
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285029"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Serializace pomocí Protocol Buffers telemetrie

Protocol Buffers (Protobuf) je binární serializační formát pro strukturovaná data. Protobuf je navržen pro zdůrazňují jednoduché a výkonu s cílem ještě menší a rychlejší než XML.

Simulace zařízení podporuje **proto3** verzi protokolu vyrovnávací paměti jazyka.

Protože Protobuf vyžaduje zkompilovaného kódu k serializaci dat, musíte vytvořit vlastní verzi simulaci zařízení.

Kroky v tomto postupy-k-Průvodci ukazují, jak do:

1. Příprava vývojového prostředí
1. Zadejte ve formátu Protobuf v modelu zařízení
1. Definování Protobuf formát
1. Generování tříd Protobuf
1. Místní test

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle kroků v této příručce s postupy, musíte:

* Visual Studio Code. Můžete si stáhnout [Visual Studio Code pro Mac, Linux a Windows](https://code.visualstudio.com/download).
* .NET core. Můžete si stáhnout [.NET Core pro Windows, Mac a Linux](https://www.microsoft.com/net/download).
* Postman. Můžete si stáhnout [Postman pro Mac, windows nebo Linux](https://www.getpostman.com/apps).
* [Nasadí do vašeho předplatného Azure IoT hub](../iot-hub/iot-hub-create-through-portal.md). Potřebujete připojovací řetězec služby IoT hub k dokončení kroků v této příručce. Získání připojovacího řetězce z webu Azure portal.
* A [nasadí do vašeho předplatného Azure databázi Cosmos DB](../cosmos-db/create-sql-api-dotnet.md#create-a-database-account) , která používá rozhraní SQL API a, který je nakonfigurovaný pro [silnou konzistenci](../cosmos-db/manage-account.md). Potřebujete připojovací řetězec databáze Cosmos DB k dokončení kroků v této příručce. Získání připojovacího řetězce z webu Azure portal.
* [Účtu úložiště Azure, které jsou nasazené do vašeho předplatného Azure](../storage/common/storage-quickstart-create-account.md). Potřebujete připojovací řetězec účtu úložiště k dokončení kroků v této příručce. Získání připojovacího řetězce z webu Azure portal.

## <a name="prepare-your-development-environment"></a>Příprava vývojového prostředí

Proveďte následující úkoly Příprava vývojového prostředí:

* Stáhněte zdroj pro mikroslužby simulaci zařízení.
* Stáhněte zdroj pro mikroslužby adaptér úložiště.
* Místní spuštění mikroslužeb adaptér úložiště.

Pokyny v tomto článku se předpokládá, že používáte Windows. Pokud používáte jiný operační systém, budete muset upravit některé cesty k souborům a příkazů podle vašich potřeb.

### <a name="download-the-microservices"></a>Stáhněte si mikroslužby

Stáhněte a rozbalte [vzdálené monitorování Mikroslužeb](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) z Githubu do vhodného umístění na místním počítači. Toto úložiště obsahuje mikroslužeb adaptér úložiště, které potřebujete pro tento návod.

Stáhněte a rozbalte [mikroslužeb simulace zařízení](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) z Githubu do vhodného umístění na místním počítači.

### <a name="run-the-storage-adapter-microservice"></a>Spustit adaptér mikroslužeb úložiště

V sadě Visual Studio Code otevřete **remote-monitoring-services-dotnet-master\storage-adapter** složky. Získáte po kliknutí na **obnovení** tlačítka opravit nevyřešené závislosti.

Otevřít **.vscode/launch.json** soubor a přiřadit k připojovací řetězec služby Cosmos DB **počítače\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** prostředí Proměnná.

> [!NOTE]
> Při spuštění mikroslužbách místně na svém počítači, stále vyžaduje instanci databáze Cosmos DB v Azure a správně fungovat.

Místní spuštění mikroslužeb adaptér úložiště, klikněte na tlačítko **ladění \> spustit ladění**.

**Terminálu** okně ve Visual Studio Code se zobrazí výstup ze spuštěné mikroslužeb, včetně adresy URL pro kontrolu stavu webové služby: <http://127.0.0.1:9022/v1/status>. Když přejdete na tuto adresu, musí být stav "OK: aktivní a dobře".

Ponechte mikroslužeb adaptér úložiště, který je spuštěn v této instanci aplikace Visual Studio Code, a proveďte následující kroky.

## <a name="define-your-device-model"></a>Definovat model zařízení

Otevřít **zařízení simulace dotnet-master** složky, které jste si stáhli z Githubu v nové instanci sady Visual Studio Code. Získáte po kliknutí na **obnovení** tlačítka a opravte všechna nevyřešené závislosti.

V tomto postupy-k-Průvodci vytvoříte nový model zařízení pro sledování prostředku:

1. Vytvořte nový soubor modelu zařízení **assettracker 01.json** v **Services\data\devicemodels** složky.

1. Definovat funkci zařízení v modelu zařízení **assettracker 01.json** souboru. Část telemetrických dat model zařízení Protobuf musí:

    * Zahrnout název třídy Protobuf, které vytvoříte pro vaše zařízení. Následující části se dozvíte, jak vygenerovat tuto třídu.
    * Zadejte Protobuf jako formát zprávy.

    ```json
    {
      "SchemaVersion": "1.0.0",
      "Id": "assettracker-01",
      "Version": "0.0.1",
      "Name": "Asset Tracker",
      "Description": "An asset tracker with location, temperature, and humidity",
      "Protocol": "AMQP",
      "Simulation": {
        "InitialState": {
          "online": true,
          "latitude": 47.445301,
          "longitude": -122.296307,
          "temperature": 38.0,
          "humidity": 62.0
        },
        "Interval": "00:01:00",
        "Scripts": [
          {
            "Type": "javascript",
            "Path": "assettracker-01-state.js"
          }
        ]
      },
      "Properties": {
        "Type": "AssetTracker",
        "Location": "Field",
        "Latitude": 47.445301,
        "Longitude": -122.296307
      },
      "Telemetry": [
        {
          "Interval": "00:00:10",
          "MessageTemplate": "{\"latitude\":${latitude},\"longitude\":${longitude},\"temperature\":${temperature},\"humidity\":${humidity}}",
          "MessageSchema": {
            "Name": "assettracker-sensors;v1",
            "ClassName": "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf.AssetTracker",
            "Format": "Protobuf",
            "Fields": {
              "latitude": "double",
              "longitude": "double",
              "temperature": "double",
              "humidity": "double"
            }
          }
        }
      ]
    }
    ```

### <a name="create-device-behaviors-script"></a>Vytvoření skriptu chování zařízení

Skript chování, která definuje chování zařízení. Další informace najdete v tématu [vytvoření pokročilých simulovaného zařízení](iot-accelerators-device-simulation-advanced-device.md).

## <a name="define-your-protobuf-format"></a>Definování Protobuf formát

Když máte model zařízení a jestli formát zprávy, můžete vytvořit **proto** souboru. V **proto** souboru, je přidat:

* A `csharp_namespace` , který odpovídá **ClassName** vlastnost v modelu zařízení.
* Zpráva pro každou strukturu dat k serializaci.
* Název a typ pro každé pole ve zprávě.

1. Vytvořte nový soubor s názvem **assettracker.proto** v **Services\Models\Protobuf\proto** složky.

1. Definovat syntaxe, obor názvů a schéma zprávy v **proto** to následujícím způsobem:

    ```proto
    syntax = "proto3";

    option csharp_namespace = "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf";

    message AssetTracker {
      double latitude=1;
      double longitude=2;
      double temperature=5;
      double humidity=6;
    }
    ```

`=1`, `=2` Značky na každý prvek v binární kódování zadat jedinečný kód používá pole. Čísla 1 až 15 vyžadují jeden nižší bajt ke kódování než vyšší čísla.

## <a name="generate-the-protobuf-class"></a>Vygenerovat třídu Protobuf

Pokud máte **proto** souboru, dalším krokem je ke generování třídy potřebné pro čtení a zápis zpráv. K dokončení tohoto kroku, je nutné **Protoc** Protobuf kompilátoru.

1. [Kompilátor Protobuf stáhnout z webu GitHub](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Spuštění kompilátor zdrojový adresář, cílový adresář a název vaší **proto** souboru. Příklad:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    Tento příkaz vygeneruje **Assettracker.cs** soubor **Services\Models\Protobuf** složky.

## <a name="test-protobuf-locally"></a>Test Protobuf místně

V této části Testování sledování zařízení asset, který jste vytvořili v předchozích částech místně.

### <a name="run-the-device-simulation-microservice"></a>Spustit mikroslužeb simulace zařízení

Otevřít **.vscode/launch.json** souboru a přiřaďte vaše:

* Připojovací řetězec služby IoT Hub do **počítače\_IOTHUB\_CONNSTRING** proměnné prostředí.
* Připojovací řetězec účtu úložiště do **počítače\_AZURE\_úložiště\_účet** proměnné prostředí.
* Připojovací řetězec služby cosmos DB má **počítače\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** proměnné prostředí.

Otevřít **WebService/Properties/launchSettings.json** souboru a přiřaďte vaše:

* Připojovací řetězec služby IoT Hub do **počítače\_IOTHUB\_CONNSTRING** proměnné prostředí.
* Připojovací řetězec účtu úložiště do **počítače\_AZURE\_úložiště\_účet** proměnné prostředí.
* Připojovací řetězec služby cosmos DB má **počítače\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** proměnné prostředí.

Otevřít **WebService\appsettings.ini** soubor a upravit nastavení následujícím způsobem:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>Nakonfigurujte řešení, které chcete zahrnout nové soubory modelu zařízení

Ve výchozím nastavení nový model zařízení JSON a soubory JS se nezkopírují do sestavení řešení. Musíte je výslovně zahrnout.

Přidání položky do **services\services.csproj** souboru pro každý soubor, které chcete zahrnout. Příklad:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Chcete-li spustit místně mikroslužbách, klikněte na tlačítko **ladění \> spustit ladění**.

**Terminálu** okně ve Visual Studio Code se zobrazí výstup ze spuštěné mikroslužeb.

Ponechte mikroslužeb simulace zařízení běží v této instanci sady Visual Studio Code při dokončování dalších kroků.

### <a name="set-up-a-monitor-for-device-events"></a>Nastavení monitorování pro události zařízení

V této části použijete rozhraní příkazového řádku Azure k nastavení monitorování událostí, chcete-li zobrazit telemetrická data odesílaná ze zařízení připojená ke službě IoT hub.

Následující skript předpokládá, že je název služby IoT hub **test simulace zařízení**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Ponechte monitorování událostí při testování simulovaných zařízení.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Vytvořit simulaci s typem zařízení sledování prostředků

V této části použijete nástroj Postman k vyžádání mikroslužeb simulace zařízení ke spuštění simulace použití typu prostředku sledování zařízení. Postman je nástroj, který vám umožní odeslat požadavky REST k webové službě.

Nastavení nástroje Postman:

1. Otevřete nástroj Postman na místním počítači.

1. Klikněte na tlačítko **souboru \> Import**. Pak klikněte na tlačítko **vybrat soubory**.

1. Vyberte **simulace zařízení Azure IoT řešení accelerator.postman\_kolekce** a **simulace zařízení Azure IoT řešení accelerator.postman\_prostředí** a Klikněte na tlačítko **otevřít**.

1. Rozbalte **akcelerátor řešení simulace zařízení IoT Azure** zobrazíte požadavky můžete odesílat.

1. Klikněte na tlačítko **prostředí bez** a vyberte **akcelerátor řešení simulace zařízení IoT Azure**.

Teď máte kolekci a prostředí načteny ve vašem pracovním prostoru, který slouží k interakci s mikroslužeb simulace zařízení Postman.

Pro konfiguraci a spuštění simulace:

1. V kolekci Postman, vyberte **vytvořit asset sledování simulaci** a klikněte na tlačítko **odeslat**. Tento požadavek vytvoří čtyři instance daného typu simulované asset sledování zařízení.

1. Výstup monitorování událostí v okně rozhraní příkazového řádku Azure zobrazí telemetrie ze simulovaných zařízení.

Pro zastavení simulace, vyberte **zastavit simulaci** žádost do Postman a klikněte na **odeslat**.

### <a name="clean-up-resources"></a>Vyčištění prostředků

Můžete zastavit dvěma místně spuštěné mikroslužby v jejich instance aplikace Visual Studio Code (**ladění \> Zastavit ladění**).

Pokud už nepotřebujete instance služby IoT Hub a Cosmos DB, můžete je odstraňte z vašeho předplatného Azure, aby se zabránilo zbytečným poplatkům.

## <a name="iot-hub-support"></a>Podpora služby IoT Hub

Mnoho funkcí služby IoT Hub nativně nepodporují Protobuf nebo jiných binární formáty. Například nemůžete provádět směrování založen na datovou část zprávy, protože služby IoT Hub nebude moci zpracovat datovou část zprávy. Můžete ale směrovat podle záhlaví zpráv.

## <a name="next-steps"></a>Další postup

Nyní jste zjistili, jak přizpůsobit simulace zařízení pomocí Protobuf odesílání telemetrických dat, dalším krokem je nyní se naučíte, jak [nasadit vlastní image do cloudu](iot-accelerators-device-simulation-deploy-image.md).
