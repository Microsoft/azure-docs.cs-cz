---
title: Začínáme s neznámkou zařízení s Azure IoT Hub (.NET/.NET) | Microsoft Docs
description: Jak používat vlákna v zařízeních Azure IoT Hub k přidávání značek a k následnému použití dotazu IoT Hub. Pomocí sady SDK pro zařízení Azure IoT pro .NET implementujete aplikaci simulovaného zařízení a sadu SDK služby Azure IoT pro .NET k implementaci aplikace služby, která přidá značky a spustí dotaz IoT Hub.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: robinsh
ms.openlocfilehash: c1b8f60fd155cf9bce0b999da7459299b6f3c7aa
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404523"
---
# <a name="get-started-with-device-twins-netnet"></a>Začínáme se zdvojením zařízení (.NET/.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na konci tohoto kurzu budete mít tyto aplikace konzoly .NET:

* **CreateDeviceIdentity**, aplikace .NET, která vytvoří identitu zařízení a přiřazený bezpečnostní klíč k připojení simulované aplikace zařízení.

* **AddTagsAndQuery**, aplikace back-end v .NET, která přidává značky a dotaz na vlákna zařízení.

* **ReportConnectivity**, aplikace pro zařízení .NET, která simuluje zařízení, které se připojuje ke službě IoT Hub s identitou zařízení vytvořenou dříve, a hlásí stav připojení.

> [!NOTE]
> V článku sady [SDK Azure IoT](iot-hub-devguide-sdks.md) najdete informace o sadách SDK Azure IoT, které můžete použít k vytvoření zařízení i back-endové aplikace.
> 

K dokončení tohoto kurzu potřebujete následující:

* Visual Studio.
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) .)

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Vytvoření aplikace služby

V této části vytvoříte konzolovou aplikaci .NET (pomocí C#), která přidá metadata umístění do vlákna zařízení přidruženého k **myDeviceId**. Pak se dotazuje na vlákna uložená ve službě IoT Hub a vybere zařízení umístěná v USA a potom ty, které nahlásily mobilní připojení.

1. V sadě Visual Studio přidejte k stávajícímu řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace**. Pojmenujte projekt **AddTagsAndQuery**.
   
    ![Nový klasický desktopový projekt Visual C# pro systém Windows](./media/iot-hub-csharp-csharp-twin-getstarted/createnetapp.png)

2. V Průzkumník řešení klikněte pravým tlačítkem na projekt **AddTagsAndQuery** a potom klikněte na **Spravovat balíčky NuGet...** .

3. V okně **Správce balíčků NuGet** vyberte **Procházet** a vyhledejte **Microsoft. Azure. Devices**. Vyberte **instalovat** a nainstalujte balíček **Microsoft. Azure. Devices** a přijměte podmínky použití. Tímto postupem se stáhne a nainstaluje balíček NuGet [sady SDK služby Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) a jeho závislosti a přidá se na něj odkaz.
   
    ![Okno Správce balíčků NuGet](./media/iot-hub-csharp-csharp-twin-getstarted/servicesdknuget.png)

4. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

5. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem IoT Hub, který jste dříve zkopírovali v [části získání připojovacího řetězce centra IoT Hub](#get-the-iot-hub-connection-string).

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
   
    Třída **RegistryManager** zpřístupňuje všechny metody, které jsou potřeba pro interakci se zařízeními ze služby. Předchozí kód nejprve inicializuje objekt **registryManager** , potom načte vlákna zařízení pro **myDeviceId**a nakonec aktualizuje své značky o požadované informace o umístění.
   
    Po aktualizaci se spustí dva dotazy: první vybere jenom zařízení, která se nacházejí v **Redmond43** , a druhá ho vyplní a vybere jenom zařízení, která jsou taky připojená přes mobilní síť.
   
    Všimněte si, že předchozí kód při vytváření objektu **dotazu** určuje maximální počet vrácených dokumentů. Objekt **dotazu** obsahuje vlastnost **HasMoreResults** Boolean, kterou můžete použít k vícenásobnému vyvolání metod **GetNextAsTwinAsync** pro načtení všech výsledků. Metoda s názvem **GetNextAsJson** je k dispozici pro výsledky, které nejsou nevlákenné zařízení, například výsledky agregačních dotazů.

7. Nakonec do metody **Main** přidejte následující řádky:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

8. V Průzkumník řešení otevřete **nastavení projekty po spuštění...** a ujistěte se, že je **spuštěna** **Akce** pro projekt **AddTagsAndQuery** . Sestavte řešení.

9. Spusťte tuto aplikaci tak, že kliknete pravým tlačítkem na projekt **AddTagsAndQuery** a vyberete **ladění**a potom zahájíte **novou instanci**. Ve výsledcích dotazu pro všechna zařízení umístěná v **Redmond43** byste měli vidět jedno zařízení, které pro dotaz neomezuje výsledky na zařízení, která používají mobilní síť.
   
    ![Výsledky dotazu v okně](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

V další části vytvoříte aplikaci pro zařízení, která oznamuje informace o připojení a mění výsledek dotazu v předchozí části.

## <a name="create-the-device-app"></a>Vytvoření aplikace pro zařízení

V této části vytvoříte konzolovou aplikaci .NET, která se připojí k vašemu rozbočovači jako **myDeviceId**, a pak aktualizuje své hlášené vlastnosti tak, aby obsahovala informace, které jsou připojené pomocí mobilní sítě.

1. V sadě Visual Studio přidejte k stávajícímu řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace**. Pojmenujte projekt **ReportConnectivity**.
   
    ![Nová aplikace C# Visual v klasickém zařízení s Windows](./media/iot-hub-csharp-csharp-twin-getstarted/createdeviceapp.png)
    
2. V Průzkumník řešení klikněte pravým tlačítkem na projekt **ReportConnectivity** a potom klikněte na **Spravovat balíčky NuGet...** .

3. V okně **Správce balíčků NuGet** vyberte **Procházet** a vyhledejte **Microsoft. Azure. Devices. Client**. Vyberte **instalovat** a nainstalujte balíček **Microsoft. Azure. Devices. Client** a přijměte podmínky použití. Tímto postupem se stáhne a nainstaluje balíček NuGet [sady SDK pro zařízení Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) a jeho závislosti a přidá se na něj odkaz.
   
    ![Klientská aplikace okna Správce balíčků NuGet](./media/iot-hub-csharp-csharp-twin-getstarted/clientsdknuget.png)

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

    Objekt **klienta** zveřejňuje všechny metody, které vyžadujete pro interakci se zařízeními ze zařízení. Výše zobrazený kód inicializuje objekt **klienta** a potom načte pro **myDeviceId**vlákna zařízení.

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

   Výše uvedený kód aktualizuje vlastnost hlášenou **myDeviceId**informacemi o připojení.

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

9. V Průzkumník řešení otevřete **nastavení projekty po spuštění...** a ujistěte se, že je **spuštěna** **Akce** pro projekt **ReportConnectivity** . Sestavte řešení.

10. Spusťte tuto aplikaci tak, že kliknete pravým tlačítkem na projekt **ReportConnectivity** a vyberete **ladění**a potom zahájíte **novou instanci**. Měli byste vidět, že se dostaly zdvojené informace, a pak poslat připojení jako *hlášenou vlastnost*.
   
    ![Spuštění aplikace zařízení pro hlášení připojení](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)
       
11. Teď, když zařízení oznámilo informace o připojení, by se mělo zobrazit v obou dotazech. Spusťte aplikaci .NET **AddTagsAndQuery** a spusťte dotazy znovu. Toto časové **myDeviceId** by se mělo objevit ve výsledcích dotazu.
   
    ![Připojení zařízení se úspěšně nahlásilo.](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nakonfigurovali novou službu IoT Hub na webu Azure Portal a potom jste vytvořili identitu zařízení v registru identit ve službě IoT Hub. Metadata zařízení jste přidali jako značky z back-endové aplikace a zapsali jste aplikaci simulovaného zařízení, která oznamuje informace o připojení zařízení v zařízení. Zjistili jste také, jak zadat dotaz na tyto informace pomocí dotazovacího jazyka IoT Hub, jako je třeba SQL.

Pomocí následujících zdrojů se naučíte:

* Odeslat telemetrii ze zařízení pomocí [odesílací telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-dotnet.md)

* v kurzu konfigurace zařízení pomocí [požadovaných vlastností pro konfiguraci](tutorial-device-twins.md) zařízení nakonfigurujte zařízení pomocí požadovaných vlastností.

* interaktivní řízení zařízení (například zapnutí ventilátoru z aplikace řízené uživatelem) pomocí kurzu [použití přímých metod](quickstart-control-device-dotnet.md) .
