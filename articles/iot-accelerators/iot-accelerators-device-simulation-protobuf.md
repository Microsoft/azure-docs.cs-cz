---
title: Použití vyrovnávacích pamětí protokolu se simulací zařízení – Azure| Dokumenty společnosti Microsoft
description: V tomto návodu se dozvíte, jak pomocí vyrovnávacích pamětí protokolu serializovat telemetrická data odeslaná z akcelerátoru řešení Simulace zařízení.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: bc08cd5183bcaac6cb77ccb0938b07893f082862
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250212"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Serializace telemetrie pomocí vyrovnávacích pamětí protokolu

Buffers Protocol Buffers (Protobuf) je binární serializační formát pro strukturovaná data. Protobuf je navržen tak, aby zdůraznil jednoduchost a výkon s cílem být menší a rychlejší než XML.

Simulace zařízení podporuje **proto3** verzi jazyka vyrovnávacích pamětí protokolu.

Protože Protobuf vyžaduje zkompilovaný kód serializovat data, musíte vytvořit vlastní verzi simulace zařízení.

Kroky v tomto návodu vám ukážou, jak:

1. Příprava vývojového prostředí
1. Určení použití formátu Protobuf v modelu zařízení
1. Definování formátu Protobuf
1. Generovat třídy Protobuf
1. Testování místně

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle pokynů v tomto návodu, potřebujete:

* Visual Studio Code. Můžete si stáhnout [Visual Studio Code for Mac, Linux a Windows](https://code.visualstudio.com/download).
* Jádro .NET. Zde si můžete stáhnout [.NET Core pro Mac, Linux a Windows](https://www.microsoft.com/net/download).
* Pošťák. Zde si můžete stáhnout [Postman pro Mac, Windows, nebo Linux](https://www.getpostman.com/apps).
* [Centrum IoT nasazené do vašeho předplatného Azure](../iot-hub/iot-hub-create-through-portal.md). K dokončení kroků v této příručce potřebujete připojovací řetězec centra IoT hub. Připojovací řetězec můžete získat z webu Azure Portal.
* [Databáze Cosmos DB nasazená do vašeho předplatného Azure,](../cosmos-db/create-sql-api-dotnet.md#create-account) která používá rozhraní SQL API a která je nakonfigurovaná pro [silnou konzistenci](../cosmos-db/manage-account.md). K dokončení kroků v této příručce potřebujete připojovací řetězec databáze Cosmos DB. Připojovací řetězec můžete získat z webu Azure Portal.
* [Účet úložiště Azure nasazený do vašeho předplatného Azure](../storage/common/storage-account-create.md). K dokončení kroků v této příručce potřebujete připojovací řetězec účtu úložiště. Připojovací řetězec můžete získat z webu Azure Portal.

## <a name="prepare-your-development-environment"></a>Příprava vývojového prostředí

Pro přípravu vývojového prostředí proveďte následující úkoly:

* Stáhněte si zdroj pro mikroslužbu simulace zařízení.
* Stáhněte zdroj mikroslužby adaptéru úložiště.
* Spusťte mikroslužbu adaptéru úložiště místně.

Pokyny v tomto článku předpokládají, že používáte systém Windows. Pokud používáte jiný operační systém, bude pravděpodobně nutné upravit některé cesty a příkazy souborů tak, aby vyhovovaly vašemu prostředí.

### <a name="download-the-microservices"></a>Stažení mikroslužeb

Stáhněte a rozbalte [mikroslužby vzdáleného monitorování](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) z GitHubu do vhodného umístění v místním počítači. Toto úložiště obsahuje mikroslužbu adaptéru úložiště, které potřebujete pro tento postup.

Stáhněte a rozbalte [mikroslužbu simulace zařízení](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) z GitHubu na vhodné místo v místním počítači.

### <a name="run-the-storage-adapter-microservice"></a>Spuštění mikroslužby adaptéru úložiště

V kódu sady Visual Studio otevřete složku **vzdáleného monitorování služeb-dotnet-master\storage-adapter.** Chcete-li opravit nevyřešené závislosti, klepněte na ně tlačítka **obnovení.**

Otevřete soubor **.vscode/launch.json** a přiřaďte připojovací řetězec Cosmos DB proměnné prostředí **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING.**

> [!NOTE]
> Když spustíte mikroslužbu místně na vašem počítači, stále vyžaduje cosmos DB instance v Azure pracovat správně.

Chcete-li mikroslužbu adaptéru úložiště spustit místně, klepněte na tlačítko **Ladění \> ladění spustit**.

Okno **Terminál** v kódu sady Visual Studio zobrazuje výstup ze spuštěné mikroslužby včetně adresy URL pro kontrolu stavu webové služby: <http://127.0.0.1:9022/v1/status>. Při přechodu na tuto adresu by měl být stav "OK: Alive and well".

Ponechte mikroslužbu adaptéru úložiště spuštěnou v této instanci kódu sady Visual Studio, zatímco dokončíte následující kroky.

## <a name="define-your-device-model"></a>Definování modelu zařízení

Otevřete složku **device-simulation-dotnet-master,** kterou jste stáhli z GitHubu v nové instanci kódu Visual Studia. Chcete-li opravit všechny nevyřešené závislosti, klepněte na ně tlačítka **obnovení.**

V tomto návodu vytvoříte nový model zařízení pro nástroj pro sledování majetku:

1. Vytvořte nový soubor modelu zařízení s názvem **assettracker-01.json** ve složce **Services\data\devicemodels.**

1. Definujte funkci zařízení v souboru **assettracker-01.json** modelu zařízení. Telemetrická část modelu zařízení Protobuf musí:

   * Uveďte název třídy Protobuf, kterou pro své zařízení vygenerujete. V následující části se zobrazí způsob generování této třídy.
   * Jako formát zprávy zadejte Protobuf.

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

### <a name="create-device-behaviors-script"></a>Vytvořit skript chování zařízení

Napište skript chování, který definuje, jak se vaše zařízení chová. Další informace naleznete [v tématu Vytvoření rozšířeného simulovaného zařízení](iot-accelerators-device-simulation-advanced-device.md).

## <a name="define-your-protobuf-format"></a>Definování formátu Protobuf

Pokud máte model zařízení a určili formát zprávy, můžete vytvořit **soubor proto.** V souboru **proto** přidáte:

* A, `csharp_namespace` která odpovídá vlastnosti **ClassName** v modelu zařízení.
* Zpráva pro každou datovou strukturu serializovat.
* Název a typ pro každé pole ve zprávě.

1. Vytvořte nový soubor s názvem **assettracker.proto** ve složce **Services\Models\Protobuf\proto.**

1. Syntaxi, obor názvů a schéma zprávy definujte v souboru **proto** následujícím způsobem:

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

`=1`Značky `=2` na každém prvku určují jedinečnou značku, kterou pole používá v binárním kódování. Čísla 1-15 vyžadují pro kódování o jeden bajt méně než vyšší čísla.

## <a name="generate-the-protobuf-class"></a>Generovat třídu Protobuf

pokud máte **soubor proto,** dalším krokem je generovat třídy potřebné ke čtení a zápisu zpráv. K dokončení tohoto kroku potřebujete kompilátor **Protoc** Protobuf.

1. [Stažení kompilátoru Protobuf z GitHubu](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Spusťte kompilátor a určete zdrojový adresář, cílový adresář a název **proto** souboru. Například:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    Tento příkaz vygeneruje **soubor Assettracker.cs** ve složce **Services\Models\Protobuf.**

## <a name="test-protobuf-locally"></a>Test Protobuf lokálně

V této části otestujete zařízení pro sledování majetku, které jste vytvořili v předchozích částech místně.

### <a name="run-the-device-simulation-microservice"></a>Spuštění mikroslužby simulace zařízení

Otevřete soubor **.vscode/launch.json** a přiřaďte:

* Připojovací řetězec IoT Hub k proměnné prostředí **PCS\_\_IOTHUB CONNSTRING.**
* Připojovací řetězec účtu úložiště k proměnné prostředí **účtu ÚLOŽIŠTĚ\_\_\_PCS AZURE.**
* Připojovací řetězec Cosmos DB k proměnné prostředí **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING.**

Otevřete soubor **WebService/Properties/launchSettings.json** a přiřaďte:

* Připojovací řetězec IoT Hub k proměnné prostředí **PCS\_\_IOTHUB CONNSTRING.**
* Připojovací řetězec účtu úložiště k proměnné prostředí **účtu ÚLOŽIŠTĚ\_\_\_PCS AZURE.**
* Připojovací řetězec Cosmos DB k proměnné prostředí **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING.**

Otevřete soubor **WebService\appsettings.ini** a upravte nastavení následujícím způsobem:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>Konfigurace řešení tak, aby zahrnovalo nové soubory modelů zařízení

Ve výchozím nastavení se nové soubory JSON a JS nového modelu zařízení nezkopírují do integrovaného řešení. Musíte je explicitně zahrnout.

Přidejte položku do souboru **services\services.csproj** pro každý soubor, který chcete zahrnout. Například:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Chcete-li spustit mikroslužbu místně, klepněte na tlačítko **Ladění \> začít ladění**.

Okno **Terminálv** kódu sady Visual Studio zobrazuje výstup z běžící mikroslužby.

Ponechte mikroslužbu simulace zařízení spuštěnou v této instanci kódu sady Visual Studio a dokončete další kroky.

### <a name="set-up-a-monitor-for-device-events"></a>Nastavení monitoru pro události zařízení

V této části použijete azure CLI k nastavení monitorování událostí k zobrazení telemetrie odeslané ze zařízení připojených k centru IoT.

Následující skript předpokládá, že název vašeho centra IoT hub je **test simulace zařízení**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Při testování simulovaných zařízení ponechte monitor událostí spuštěný.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Vytvoření simulace s typem zařízení pro sledování majetku

V této části použijete nástroj Postman k vyžádání mikroslužby simulace zařízení ke spuštění simulace pomocí typu zařízení sledování prostředků. Pošťák je nástroj, který umožňuje odesílat požadavky REST webové službě.

Nastavení pošťáka:

1. Otevřete pošťáka na místním počítači.

1. Klepněte na **položku Import souborů \> **. Pak klepněte na **tlačítko Vybrat soubory**.

1. Vyberte **Azure IoT Device Simulation\_solution accelerator.postman collection** a **Azure\_IoT Device Simulation solution accelerator.postman environment** a klikněte na **Otevřít**.

1. Rozbalte **akcelerátor řešení Simulace zařízení Azure IoT** a zobrazte si požadavky, které můžete odeslat.

1. Klikněte na **Žádné prostředí** a vyberte **akcelerátor řešení Simulace zařízení Azure IoT**.

Nyní máte kolekci a prostředí načtené v pracovním prostoru Postman, který můžete použít k interakci s mikroslužbou simulace zařízení.

Konfigurace a spuštění simulace:

1. V kolekci Pošťák vyberte **Vytvořit simulaci sledování majetku** a klepněte na tlačítko **Odeslat**. Tento požadavek vytvoří čtyři instance simulovaného typu zařízení sledování prostředků.

1. Výstup monitorování událostí v okně Azure CLI zobrazuje telemetrii ze simulovaných zařízení.

Chcete-li simulaci zastavit, vyberte v pořštěnu požadavek **na zastavení simulace** a klepněte na tlačítko **Odeslat**.

### <a name="clean-up-resources"></a>Vyčištění prostředků

Můžete zastavit dvě místně spuštěné mikroslužby v jejich instancích kódu sady Visual Studio **(Ladění \> stop ladění).**

Pokud už nepotřebujete instance Služby IoT Hub a Cosmos DB, odstraňte je z předplatného Azure, abyste se vyhnuli zbytečným poplatkům.

## <a name="iot-hub-support"></a>Podpora ioT hubu

Mnoho funkcí služby IoT Hub nativně nepodporuje Protobuf nebo jiné binární formáty. Například nelze směrovat na základě datové části zprávy, protože služba IoT Hub nebude moci zpracovat datovou část zprávy. Můžete však směrovat na základě záhlaví zpráv.

## <a name="next-steps"></a>Další kroky

Nyní jste se naučili, jak přizpůsobit simulaci zařízení pro použití Protobuf k odesílání telemetrie, dalším krokem je naučit se nyní [nasadit vlastní image do cloudu](iot-accelerators-device-simulation-deploy-image.md).
