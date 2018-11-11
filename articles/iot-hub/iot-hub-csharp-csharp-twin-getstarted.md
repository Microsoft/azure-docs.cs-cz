---
title: Začínáme s dvojčaty zařízení Azure IoT Hub (.NET/.NET) | Dokumentace Microsoftu
description: Jak používat dvojče zařízení Azure IoT Hub pro přidání značek a následné použití k dotazu služby IoT Hub. Implementace aplikace simulovaného zařízení a služby Azure IoT SDK pro .NET k implementaci app service, které přidá značky a spustí dotaz služby IoT Hub pomocí zařízení Azure IoT SDK pro .NET.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: dobett
ms.openlocfilehash: 1921395ed11c23ddb3d64d9d53124df7b7c8fd82
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2018
ms.locfileid: "51514855"
---
# <a name="get-started-with-device-twins-netnet"></a>Začínáme s dvojčaty zařízení (.NET/.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na konci tohoto kurzu budete mít tyto aplikace konzoly .NET:

* **CreateDeviceIdentity**, aplikace .NET, která vytvoří identitu zařízení a přiřazený bezpečnostní klíč k připojení simulované aplikace zařízení.

* **AddTagsAndQuery**, .NET back endové aplikace, které přidá značky a dotazy dvojčata zařízení.

* **ReportConnectivity**, aplikace .NET zařízení, která simuluje zařízení, která se připojuje ke službě IoT hub s dříve vytvořenou identitou zařízení a sestav stavu připojení.

> [!NOTE]
> Tento článek [sad SDK Azure IoT](iot-hub-devguide-sdks.md) poskytuje informace o Azure IoT SDK, že vám pomůže vytvářet aplikace pro zařízení i back-end.
> 

K dokončení tohoto kurzu budete potřebovat následující:

* Sada Visual Studio 2017.
* Aktivní účet Azure. (Pokud účet nemáte, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) během několika minut.)

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Načtení připojovacího řetězce pro službu IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Zaregistrujte nové zařízení ve službě IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-the-service-app"></a>Vytvoření aplikace služby

V této části vytvoříte konzolovou aplikaci .NET (s použitím jazyka C#), která přidává umístění metadat do dvojčete zařízení spojené s **myDeviceId**. Následně se dotazuje dvojčata zařízení, které jsou uložené ve službě IoT hub výběrem umístěné v USA a ty, kteří hlásili mobilní připojení zařízení.

1. V sadě Visual Studio přidejte k stávajícímu řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace**. Pojmenujte projekt **AddTagsAndQuery**.
   
    ![Nový klasický desktopový projekt Visual C# pro systém Windows](./media/iot-hub-csharp-csharp-twin-getstarted/createnetapp.png)

2. V Průzkumníku řešení klikněte pravým tlačítkem myši **AddTagsAndQuery** projektu a pak klikněte na tlačítko **spravovat balíčky NuGet...** .

3. V **Správce balíčků NuGet** okně **Procházet** a vyhledejte **Microsoft.Azure.Devices**. Vyberte **nainstalovat** k instalaci **Microsoft.Azure.Devices** balíček a přijměte podmínky použití. Tento postup stáhne, nainstaluje a přidá odkaz na [pro službu Azure IoT SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet balíček a jeho závislosti.
   
    ![Okno Správce balíčků NuGet](./media/iot-hub-csharp-csharp-twin-getstarted/servicesdknuget.png)

4. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

5. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem pro službu IoT Hub, kterou jste vytvořili v předchozí části.

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

6. Přidejte následující metodu do třídy **Program**:

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
   
    **RegistryManager** třída zveřejňuje všechny metody, které jsou nutné k interakci s dvojčaty zařízení ze služby. Předchozí kód nejprve inicializuje **registryManager** a pak načte dvojče zařízení pro **myDeviceId**a nakonec aktualizuje její klíčová slova s informacemi o požadované umístění.
   
    Po aktualizaci se provede dva dotazy: první vybere pouze dvojčata zařízení nachází v zařízení **Redmond43** kategorie plant a druhá zpřesnění dotaz pro výběr pouze do zařízení, které jsou také připojené přes mobilní síť.
   
    Všimněte si, že předchozí kód, když vytváří **dotazu** objektu, určuje maximální počet vrácených dokumentů. **Dotazu** obsahuje objekt **HasMoreResults** logická vlastnost, která můžete použít k vyvolání **GetNextAsTwinAsync** metody víc než jednou načíst všechny výsledky. Volána metoda **GetNextAsJson** je k dispozici pro výsledky, které není dvojčata zařízení, třeba výsledky dotazů agregace.

7. Nakonec do metody **Main** přidejte následující řádky:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

8. V Průzkumníku řešení otevřete **nastavit projekty po spuštění...**  a ujistěte se, že **akce** pro **AddTagsAndQuery** projekt je **Start**. Sestavte řešení.

9. Tuto aplikaci spustit kliknutím pravým tlačítkem na **AddTagsAndQuery** projekt a výběrem **ladění**následovaný **zahájit novou instanci**. Měli byste vidět jedno zařízení ve výsledcích pro kladení dotazů pro všechna zařízení nachází v **Redmond43** a žádné dotaz, který omezuje výsledky na zařízení, která používají mobilní síti.
   
    ![Výsledky dotazu v okně](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

V další části můžete vytvořit aplikace pro zařízení, která hlásí informace o připojení a změní výsledek dotazu v předchozí části.

## <a name="create-the-device-app"></a>Vytvoření aplikace pro zařízení

V této části vytvoříte konzolovou aplikaci .NET, která se připojuje k vašemu centru jako **myDeviceId**a pak aktualizuje ohlášených vlastností obsahují informace, že je připojený pomocí mobilní síti.

1. V sadě Visual Studio přidejte k stávajícímu řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace**. Pojmenujte projekt **ReportConnectivity**.
   
    ![Nový Visual C# Windows klasické aplikace pro zařízení](./media/iot-hub-csharp-csharp-twin-getstarted/createdeviceapp.png)
    
2. V Průzkumníku řešení klikněte pravým tlačítkem myši **ReportConnectivity** projektu a pak klikněte na tlačítko **spravovat balíčky NuGet...** .

3. V **Správce balíčků NuGet** okně **Procházet** a vyhledejte **Microsoft.Azure.Devices.Client**. Vyberte **nainstalovat** k instalaci **Microsoft.Azure.Devices.Client** balíček a přijměte podmínky použití. Tento postup stáhne, nainstaluje a přidá odkaz na [zařízení Azure IoT SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet balíček a jeho závislosti.
   
    ![Klientská aplikace okno Správce balíčků NuGet](./media/iot-hub-csharp-csharp-twin-getstarted/clientsdknuget.png)

4. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem zařízení, který jste si poznamenali v předchozí části.

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;
      DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. Přidejte následující metodu do třídy **Program**:

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

    **Klienta** objekt poskytuje všechny metody, které budete potřebovat pro interakci s dvojčaty zařízení ze zařízení. Inicializuje kódu uvedeného výše **klienta** objekt a potom načte dvojče zařízení pro **myDeviceId**.

7. Přidejte následující metodu do třídy **Program**:

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

   Kód výše aktualizace **myDeviceId**je uvedena vlastnost s informacemi o připojení.

8. Nakonec do metody **Main** přidejte následující řádky:

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

9. V Průzkumníku řešení otevřete **nastavit projekty po spuštění...**  a ujistěte se, že **akce** pro **ReportConnectivity** projekt je **Start**. Sestavte řešení.

10. Tuto aplikaci spustit kliknutím pravým tlačítkem na **ReportConnectivity** projekt a výběrem **ladění**následovaný **zahájit novou instanci**. Měli byste vidět, získávání dvojčete informace a potom odešlete připojení jako *hlášené vlastnost*.
   
    ![Spuštění aplikace pro zařízení pro připojení k sestavě](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)
       
11. Teď, když zařízení uvádět jeho informace o připojení, měl by se zobrazit v obou dotazech. Spuštění rozhraní .NET **AddTagsAndQuery** aplikaci znovu spustit dotazy. Tentokrát **myDeviceId** by se měla objevit v obou výsledky dotazu.
   
    ![Připojení zařízení úspěšně hlášené](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nakonfigurovali novou službu IoT Hub na webu Azure Portal a potom jste vytvořili identitu zařízení v registru identit ve službě IoT Hub. Přidá metadata zařízení jako značky z back endové aplikace a aplikace s Simulovaná zařízení zapsáno do sestavy informací o připojení k zařízení ve dvojčeti zařízení. Také jste zjistili, jak zadávat dotazy na tyto informace pomocí dotazovacího jazyka podobného SQL IoT Hub.

Pomocí následujících zdrojích se dozvíte jak:

* odesílání telemetrie ze zařízení s [odesílání telemetrických dat ze zařízení do služby IoT hub](quickstart-send-telemetry-dotnet.md) kurzu

* Konfigurace zařízení pomocí požadované vlastnosti dvojčete zařízení s [použití požadované vlastnosti ke konfiguraci zařízení](tutorial-device-twins.md) kurzu

* Ovládací prvek zařízení interaktivně (například zapnutí ventilátor z aplikace řízené uživatelem) [použití přímých metod](quickstart-control-device-dotnet.md) kurzu.
