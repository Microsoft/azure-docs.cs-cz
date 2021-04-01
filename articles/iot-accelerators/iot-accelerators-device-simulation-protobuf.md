---
title: Použití vyrovnávacích pamětí protokolu s simulací zařízení – Azure | Microsoft Docs
description: V této příručce se dozvíte, jak používat vyrovnávací paměti protokolu k serializaci telemetrie odeslané z akcelerátoru řešení pro simulaci zařízení.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc, amqp, devx-track-csharp
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: 77a795b8f704084b612a2dcbd364b8d5be0a3333
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96852145"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Serializace telemetrie pomocí vyrovnávacích pamětí protokolu

Vyrovnávací paměti protokolu (Protobuf) je binární formát serializace pro strukturovaná data. Protobuf je navržený tak, aby zdůraznil jednoduchost a výkon s cílem být menší a rychlejší než XML.

Simulace zařízení podporuje verzi **proto3** jazyka vyrovnávací paměti protokolu.

Vzhledem k tomu, že Protobuf vyžaduje zkompilovaný kód k serializaci dat, je nutné vytvořit vlastní verzi simulace zařízení.

Postup v tomto návodu vám ukáže, jak:

1. Příprava vývojového prostředí
1. Určení použití formátu Protobuf v modelu zařízení
1. Definování formátu Protobuf
1. Generovat třídy Protobuf
1. Test lokálně

## <a name="prerequisites"></a>Požadavky

Pokud chcete postupovat podle kroků v tomto průvodci, budete potřebovat:

* Visual Studio Code. Můžete si stáhnout [Visual Studio Code pro Mac, Linux a Windows](https://code.visualstudio.com/download).
* .NET Core. Můžete stáhnout [.NET Core pro Mac, Linux a Windows](https://www.microsoft.com/net/download).
* Postman. Můžete si stáhnout [pro Mac, Windows nebo Linux](https://www.getpostman.com/apps).
* [Centrum IoT nasazené ve vašem předplatném Azure](../iot-hub/iot-hub-create-through-portal.md). K dokončení kroků v této příručce potřebujete připojovací řetězec centra IoT. Připojovací řetězec můžete získat z Azure Portal.
* [Cosmos DB databáze nasazené do předplatného Azure](../cosmos-db/create-sql-api-dotnet.md#create-account) , které používá rozhraní SQL API a která je nakonfigurovaná pro [silnou konzistenci](../cosmos-db/how-to-manage-database-account.md). K dokončení kroků v této příručce potřebujete připojovací řetězec databáze Cosmos DB. Připojovací řetězec můžete získat z Azure Portal.
* [Účet služby Azure Storage nasazený do vašeho předplatného Azure](../storage/common/storage-account-create.md). K dokončení kroků v této příručce potřebujete připojovací řetězec účtu úložiště. Připojovací řetězec můžete získat z Azure Portal.

## <a name="prepare-your-development-environment"></a>Příprava vývojového prostředí

Při přípravě vývojového prostředí dokončete následující úlohy:

* Stáhněte si zdroj pro mikroslužbu simulace zařízení.
* Stáhněte si zdroj pro mikroslužbu adaptéru úložiště.
* Spusťte mikroslužbu adaptéru úložiště místně.

Pokyny v tomto článku předpokládají, že používáte systém Windows. Pokud používáte jiný operační systém, možná budete muset upravit některé cesty k souboru a příkazy tak, aby vyhovovaly vašemu prostředí.

### <a name="download-the-microservices"></a>Stažení mikroslužeb

Stáhněte a rozbalte [mikroslužby vzdáleného monitorování](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) z GitHubu do vhodného umístění v místním počítači. Toto úložiště zahrnuje mikroslužbu adaptéru úložiště, kterou potřebujete pro tento postup.

Stáhněte a rozbalte [mikroslužbu simulace zařízení](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) z GitHubu do vhodného umístění v místním počítači.

### <a name="run-the-storage-adapter-microservice"></a>Spuštění mikroslužby adaptéru úložiště

V Visual Studio Code otevřete složku **Remote-Monitoring-Services-dotnet-master\storage-Adapter** . Chcete-li opravit nevyřešené závislosti, klikněte na tlačítko **obnovit** .

Otevřete soubor **. VSCode/launch.jsv** souboru a přiřaďte Cosmos DB připojovací řetězec k proměnné prostředí **počítače \_ STORAGEADAPTER \_ DOCUMENTDB \_ CONNSTRING** .

> [!NOTE]
> Když mikroslužbu spustíte místně na svém počítači, vyžaduje se i v Azure instance Cosmos DB, aby fungovala správně.

Pokud chcete spustit mikroslužbu adaptéru úložiště místně, klikněte na **ladění \> Spustit ladění**.

Okno **terminálu** v Visual Studio Code zobrazuje výstup z běžící mikroslužby včetně adresy URL pro kontrolu stavu webové služby: <http://127.0.0.1:9022/v1/status> . Při přechodu na tuto adresu by měl být ve stavu "OK: Alive" a dobře ".

Ponechte mikroslužbu adaptéru úložiště spuštěnou v této instanci Visual Studio Code, zatímco provedete následující kroky.

## <a name="define-your-device-model"></a>Definice modelu zařízení

Otevřete složku **Device-simulace-dotnet-Master** , kterou jste stáhli z GitHubu, do nové instance Visual Studio Code. Kliknutím na jakékoli tlačítko **obnovit** opravíte nevyřešené závislosti.

V tomto průvodci vytvoříte nový model zařízení pro sledování prostředků:

1. Ve složce **Services\data\devicemodels** vytvořte nový soubor modelu zařízení s názvem **assettracker-01.js** .

1. Definujte funkčnost zařízení v modelu zařízení **assettracker-01.jsv** souboru. Oddíl telemetrie modelu zařízení Protobuf musí:

   * Zahrňte název třídy Protobuf, kterou vygenerujete pro vaše zařízení. V následující části se dozvíte, jak vygenerovat tuto třídu.
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

Napište skript chování, který definuje, jak se vaše zařízení chová. Další informace najdete v tématu [vytvoření pokročilého simulovaného zařízení](iot-accelerators-device-simulation-advanced-device.md).

## <a name="define-your-protobuf-format"></a>Definování formátu Protobuf

Když máte model zařízení a určíte formát zprávy, můžete **vytvořit soubor.** Do souboru **proto** přidáte:

* `csharp_namespace`Který se shoduje s vlastností **ClassName** v modelu zařízení.
* Zpráva pro každou strukturu dat k serializaci.
* Název a typ pro každé pole ve zprávě.

1. Ve složce **Services\Models\Protobuf\proto** vytvořte nový soubor s názvem **assettracker.**

1. V souboru **proto** definujte syntaxi, obor názvů a schéma zprávy následujícím způsobem:

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

`=1`Značky, `=2` na jednotlivých prvcích, určují jedinečnou značku, kterou pole používá v binárním kódování. Čísla 1-15 vyžadují jeden méně bajt pro kódování než vyšší čísla.

## <a name="generate-the-protobuf-class"></a>Generovat třídu Protobuf

**v případě souboru s** tímto souborem je dalším krokem vygenerování tříd potřebných pro čtení a zápis zpráv. K dokončení tohoto kroku potřebujete kompilátor **Protoc** Protobuf.

1. [Stažení kompilátoru Protobuf z GitHubu](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Spusťte kompilátor, určete zdrojový adresář, cílový adresář a **název souboru.** Například:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    Tento příkaz vygeneruje soubor **Assettracker. cs** ve složce **Services\Models\Protobuf** .

## <a name="test-protobuf-locally"></a>Místní test Protobuf

V této části otestujete zařízení pro sledování assetu, které jste vytvořili v předchozích částech místně.

### <a name="run-the-device-simulation-microservice"></a>Spuštění mikroslužby simulace zařízení

Otevřete soubor **. VSCode/launch.js** a přiřaďte ho:

* IoT Hub připojovací řetězec k proměnné **prostředí \_ IOTHUB počítače \_ CONNSTRING** .
* Připojovací řetězec účtu úložiště k proměnné **prostředí \_ \_ \_ účtu úložiště Azure** .
* Cosmos DB připojovací řetězec k proměnné **prostředí \_ \_ \_ CONNSTRING počítače s DOCUMENTDB STORAGEADAPTER** .

Otevřete **u souboru WebService/Properties/launchSettings.js** a přiřaďte své:

* IoT Hub připojovací řetězec k proměnné **prostředí \_ IOTHUB počítače \_ CONNSTRING** .
* Připojovací řetězec účtu úložiště k proměnné **prostředí \_ \_ \_ účtu úložiště Azure** .
* Cosmos DB připojovací řetězec k proměnné **prostředí \_ \_ \_ CONNSTRING počítače s DOCUMENTDB STORAGEADAPTER** .

Otevřete soubor **WebService\appsettings.ini** a upravte nastavení následujícím způsobem:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>Nakonfigurujte řešení tak, aby zahrnovalo vaše nové soubory modelu zařízení.

Ve výchozím nastavení se vaše nové soubory JSON a JS modelů zařízení do sestaveného řešení nekopírují. Je nutné je explicitně zahrnout.

Přidejte položku do souboru **services\services.csproj** pro každý soubor, který chcete zahrnout. Například:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Pokud chcete spustit mikroslužbu místně, klikněte na **ladění \> Spustit ladění**.

Okno **terminálu** v Visual Studio Code zobrazuje výstup z běžící mikroslužby.

Ponechte mikroslužbu simulace zařízení spuštěnou v této instanci Visual Studio Code během provádění dalších kroků.

### <a name="set-up-a-monitor-for-device-events"></a>Nastavení monitorování pro události zařízení

V této části použijete rozhraní příkazového řádku Azure CLI k nastavení monitorování událostí pro zobrazení telemetrie odesílané ze zařízení připojených ke službě IoT Hub.

Následující skript předpokládá, že název vašeho centra IoT je typu **zařízení-simulace-test**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Při testování simulovaných zařízení nechte monitorování událostí spuštěné.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Vytvořit simulaci s typem zařízení pro sledování prostředků

V této části vyžádáte pomocí nástroje pro simulaci simulaci zařízení, aby spouštěla simulaci pomocí typu zařízení sledovacího modulu assetu. Post je nástroj, který umožňuje odesílat žádosti REST do webové služby.

Nastavení post:

1. Otevřete post na místním počítači.

1. Klikněte **na \> Import souboru**. Pak klikněte na **zvolit soubory**.

1. Vyberte možnost **akcelerátor řešení simulace zařízení Azure IoT. \_ kolekce pro vystavování** a **řešení pro simulaci zařízení Azure IoT. proúčtujte \_ prostředí** a klikněte na **otevřít**.

1. Rozbalte **akcelerátor řešení pro simulaci zařízení Azure IoT** , abyste viděli požadavky, které můžete odeslat.

1. Klikněte na **žádné prostředí** a vyberte **akcelerátor řešení pro simulaci zařízení Azure IoT**.

Nyní máte v pracovním prostoru pro práci vytvořenou kolekci a prostředí, kterou můžete použít k interakci s mikroslužbou simulace zařízení.

Konfigurace a spuštění simulace:

1. V kolekci post vyberte **vytvořit simulaci sledování assetu** a klikněte na **Odeslat**. Tento požadavek vytvoří čtyři instance typu simulovaného zařízení sledovacího přehledu prostředků.

1. Výstup sledování událostí v okně Azure CLI zobrazuje telemetrii z simulovaných zařízení.

Chcete-li zastavit simulaci, vyberte možnost **zastavit simulaci** v příspěvku a klikněte na **Odeslat**.

### <a name="clean-up-resources"></a>Vyčištění prostředků

V jejich instancích Visual Studio Code můžete zastavit dvě místně běžící mikroslužby (**ladění \> Zastavit ladění**).

Pokud už nepotřebujete instance IoT Hub a Cosmos DB, odstraňte je z předplatného Azure, abyste se vyhnuli zbytečným poplatkům.

## <a name="iot-hub-support"></a>Podpora IoT Hub

Mnoho funkcí IoT Hub neumožňuje nativně podporovat Protobuf nebo jiné binární formáty. Například nelze směrovat na základě datové části zprávy, protože IoT Hub nebude moci zpracovat datovou část zprávy. Můžete ale směrovat na základě záhlaví zpráv.

## <a name="next-steps"></a>Další kroky

Nyní jste se naučili, jak přizpůsobit simulaci zařízení, aby při posílání telemetrie používal Protobuf, další krok je navštívit úložiště GitHub a získat další informace o [simulaci zařízení](https://github.com/Azure/device-simulation-dotnet).
