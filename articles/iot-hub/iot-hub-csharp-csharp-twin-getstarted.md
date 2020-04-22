---
title: Začínáme s dvojčaty zařízení Azure IoT Hub (.NET/.NET) | Dokumenty společnosti Microsoft
description: Jak použít dvojčata zařízení Azure IoT Hub k přidání značek a pak použít dotaz služby IoT Hub. Pomocí sady Azure IoT zařízení SDK pro rozhraní .NET implementovat aplikaci simulované zařízení a služby Azure IoT SDK pro rozhraní .NET k implementaci aplikace služby, která přidá značky a spustí dotaz služby IoT Hub.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 620e0213733d278a28ec1bcad4b031f5764ccda9
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733132"
---
# <a name="get-started-with-device-twins-net"></a>Začínáme s dvojčaty zařízení (.NET)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

V tomto kurzu vytvoříte tyto aplikace konzoly .NET:

* **CreateDeviceIdentity**. Tato aplikace vytvoří identitu zařízení a přidružený bezpečnostní klíč pro připojení aplikace simulovaných zařízení.

* **AddTagsAndQuery**. Tato back-endová aplikace přidává značky a dotazuje dvojčata zařízení.

* **ReportConnectivity**. Tato aplikace zařízení simuluje zařízení, které se připojuje k vašemu centru IoT s identitou zařízení vytvořenou dříve a hlásí jeho stav připojení.

> [!NOTE]
> Článek [sady Azure IoT SDK obsahuje](iot-hub-devguide-sdks.md) informace o sadách Azure IoT SDK, které můžete použít k vytváření aplikací pro zařízení i back-end.
>

## <a name="prerequisites"></a>Požadavky

* Visual Studio.

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení v centru IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Vytvoření aplikace služby

V této části vytvoříte aplikaci konzoly .NET pomocí jazyka C#, která přidá metadata umístění do dvojčete zařízení přidruženého k **myDeviceId**. Potom se dotazuje dvojčata zařízení uložená v centru IoT výběru zařízení umístěných v USA a potom ty, které ohlásily mobilní připojení.

1. V sadě Visual Studio vyberte **Vytvořit nový projekt**. V **okně Vytvořit nový projekt**vyberte Console App **(.NET Framework)** a pak vyberte **Další**.

1. V **konfigurovat nový projekt**, název projektu **AddTagsAndQuery**.

    ![Konfigurace projektu AddTagsAndQuery](./media/iot-hub-csharp-csharp-twin-getstarted/config-addtagsandquery-app.png)

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt **AddTagsAndQuery** a pak vyberte **spravovat balíčky NuGet**.

1. Vyberte **Procházet** a vyhledejte a vyberte **Microsoft.Azure.Devices**. Vyberte **Install** (Nainstalovat).

    ![Okno Správce balíčků NuGet](./media/iot-hub-csharp-csharp-twin-getstarted/nuget-package-addtagsandquery-app.png)

   Tento krok stáhne, nainstaluje a přidá odkaz na balíček [Služby Azure IoT SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet a jeho závislosti.

1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

1. Do třídy **Program** přidejte následující pole. Nahraďte `{iot hub connection string}` připojovacím řetězcem IoT Hub, který jste zkopírovali v [části Získat připojovací řetězec centra IoT](#get-the-iot-hub-connection-string)Hub .

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. Do třídy **Program** přidejte následující metodu:

    ```csharp  
    public static async Task AddTagsAndQuery()
    {
        var twin = await registryManager.GetTwinAsync("myDeviceId");
        var patch =
            @"{
                tags: {
                    location: {
                        region: 'US',
                        plant: 'Redmond43'
                    }
                }
            }";
        await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);

        var query = registryManager.CreateQuery(
          "SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
        var twinsInRedmond43 = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43: {0}", 
          string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

        query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
        var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43 using cellular network: {0}", 
          string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
    }
    ```

    Třída **RegistryManager** zveřejňuje všechny metody potřebné k interakci s dvojčaty zařízení ze služby. Předchozí kód nejprve inicializuje objekt **registryManager,** pak načte dvojče zařízení pro **myDeviceId**a nakonec aktualizuje jeho značky s informacemi o požadovaném umístění.

    Po aktualizaci provede dva dotazy: první vybere pouze dvojčata zařízení umístěných v závodě **Redmond43** a druhý zpřesní dotaz a vybere pouze zařízení, která jsou také připojena prostřednictvím mobilní sítě.

    Předchozí kód při vytvoření objektu **dotazu** určuje maximální počet vrácených dokumentů. Objekt **dotazu** obsahuje logickou vlastnost **HasMoreResults,** kterou můžete použít k vyvolání metod **GetNextAsTwinAsync** vícekrát k načtení všech výsledků. Metoda s názvem **GetNextAsJson** je k dispozici pro výsledky, které nejsou dvojčata zařízení, například výsledky agregačnídotazy.

1. Nakonec přidejte do metody **Main** následující řádky:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. Spusťte tuto aplikaci kliknutím pravým tlačítkem myši na projekt **AddTagsAndQuery** a výběrem **možnosti Ladění**, následovanou **příkazem Start new instance**. Ve výsledcích dotazu byste měli vidět jedno zařízení s žádostí o všechna zařízení umístěná v **Redmond43** a žádné pro dotaz, který omezuje výsledky na zařízení, která používají mobilní síť.

    ![Výsledky dotazu v okně](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

V další části vytvoříte aplikaci pro zařízení, která hlásí informace o připojení a změní výsledek dotazu v předchozí části.

## <a name="create-the-device-app"></a>Vytvoření aplikace pro zařízení

V této části vytvoříte aplikaci konzoly .NET, která se připojí k rozbočovači jako **myDeviceId**a potom aktualizuje jeho hlášené vlastnosti, aby obsahovaly informace, které je připojen pomocí mobilní sítě.

1. V sadě Visual Studio vyberte **Soubor** > **nový** > **projekt**. V **okně Vytvořit nový projekt**zvolte Console App **(.NET Framework)** a pak vyberte **Další**.

1. V **části Konfigurace nového projektu**pojmenujte projekt **ReportConnectivity**. V **řešení**zvolte **Přidat do řešení**a pak vyberte **Vytvořit**.

1. V Průzkumníku řešení klepněte pravým tlačítkem myši na projekt **ReportConnectivity** a pak vyberte **spravovat balíčky NuGet**.

1. Vyberte **Procházet** a vyhledejte a zvolte **Microsoft.Azure.Devices.Client**. Vyberte **Install** (Nainstalovat).

   Tento krok stáhne, nainstaluje a přidá odkaz na balíček SDK NuGet [zařízení Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) a jeho závislosti.

1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Do třídy **Program** přidejte následující pole. Nahraďte `{device connection string}` připojovacím řetězcem zařízení, který jste si poznamenali v [registru nového zařízení v centru IoT](#register-a-new-device-in-the-iot-hub)hub .

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

1. Do třídy **Program** přidejte následující metodu:

    ```csharp
    public static async void InitClient()
    {
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
              TransportType.Mqtt);
            Console.WriteLine("Retrieving twin");
            await Client.GetTwinAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

    Client **Client** objekt zveřejňuje všechny metody, které potřebujete k interakci s dvojčaty zařízení ze zařízení. Výše uvedený kód inicializuje objekt **Klienta** a potom načte dvojče zařízení pro **myDeviceId**.

1. Do třídy **Program** přidejte následující metodu:

    ```csharp  
    public static async void ReportConnectivity()
    {
        try
        {
            Console.WriteLine("Sending connectivity data as reported property");

            TwinCollection reportedProperties, connectivity;
            reportedProperties = new TwinCollection();
            connectivity = new TwinCollection();
            connectivity["type"] = "cellular";
            reportedProperties["connectivity"] = connectivity;
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

   Výše uvedený kód aktualizuje ohlášenou vlastnost **myDeviceId** s informacemi o připojení.

1. Nakonec přidejte do metody **Main** následující řádky:

    ```csharp
    try
    {
        InitClient();
        ReportConnectivity();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na své řešení a vyberte **Nastavit počáteční projekty**.

1. V**projektu po spuštění** **běžných vlastností** > vyberte **možnost Více projektů při spuštění**. V **části ReportConnectivity**vyberte **možnost Spustit** jako **akci**. Výběrem **OK** uložte změny.  

1. Spusťte tuto aplikaci kliknutím pravým tlačítkem myši na projekt **ReportConnectivity** a výběrem **možnosti Ladění**a potom **spuštění mno ství nové instance**. Měli byste vidět aplikaci získávání informací dvojčete a následné odeslání připojení jako ***ohlášené vlastnosti***.

    ![Spuštění aplikace zařízení pro nahlášení připojení](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)

   Poté, co zařízení ohlásilo informace o připojení, mělo by se zobrazit v obou dotazech.

1. Klikněte pravým tlačítkem myši na projekt **AddTagsAndQuery** a vyberte **možnost Ladění** > **spustit novou instanci** a spusťte dotazy znovu. Tentokrát **myDeviceId** by se měl zobrazit v obou výsledcích dotazu.

    ![Připojení zařízení bylo hlášeno úspěšně.](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali novou službu IoT Hub na webu Azure Portal a potom jste vytvořili identitu zařízení v registru identit ve službě IoT Hub. Přidali jste metadata zařízení jako značky z back-endové aplikace a napsali jste simulovanou aplikaci zařízení, která nahlásí informace o připojení zařízení v dvojčeti zařízení. Také jste se naučili, jak dotaz ovat tyto informace pomocí dotazovacího jazyka ioT hub u verze SQL.

Další informace naleznete v následujících zdrojích:

* Informace o tom, jak odesílat telemetrická data ze zařízení, najdete v tématu [Odesílání telemetrie ze zařízení do kurzu služby IoT hub.](quickstart-send-telemetry-dotnet.md)

* Informace o konfiguraci zařízení pomocí požadovaných vlastností dvojčete zařízení najdete v tématu [Použití požadovaných vlastností ke konfiguraci zařízení.](tutorial-device-twins.md)

* Informace o interaktivním ovládání zařízení, jako je například zapnutí ventilátoru z uživatelem řízené aplikace, najdete v tématu [Použití přímých metod](quickstart-control-device-dotnet.md) kurzu.
