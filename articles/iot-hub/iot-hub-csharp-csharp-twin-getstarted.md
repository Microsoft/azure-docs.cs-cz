---
title: Začínáme s neznámkou zařízení s Azure IoT Hub (.NET/.NET) | Microsoft Docs
description: Jak používat vlákna v zařízeních Azure IoT Hub k přidávání značek a k následnému použití dotazu IoT Hub. Pomocí sady SDK pro zařízení Azure IoT pro .NET implementujete aplikaci simulovaného zařízení a sadu SDK služby Azure IoT pro .NET k implementaci aplikace služby, která přidá značky a spustí dotaz IoT Hub.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 267a69486dc91ef95c0de704346eeb1d1780ef48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89013754"
---
# <a name="get-started-with-device-twins-net"></a>Začínáme se zdvojením zařízení (.NET)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

V tomto kurzu vytvoříte tyto aplikace konzoly .NET:

* **AddTagsAndQuery**. Tato back-end aplikace přidá značky a dotaz na vlákna zařízení.

* **ReportConnectivity**. Tato aplikace zařízení simuluje zařízení, které se připojuje ke službě IoT Hub s identitou zařízení vytvořenou dříve a hlásí podmínku připojení.

> [!NOTE]
> V článku sady [SDK Azure IoT](iot-hub-devguide-sdks.md) najdete informace o sadách SDK Azure IoT, které můžete použít k vytvoření zařízení i back-endové aplikace.
>

## <a name="prerequisites"></a>Požadavky

* Visual Studio

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT (neboli IoT Hubu)

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Vytvoření aplikace služby

V této části vytvoříte konzolovou aplikaci .NET pomocí jazyka C#, která přidá metadata umístění do vlákna v zařízení přidruženého k **myDeviceId**. Pak se dotazuje na vlákna uložená ve službě IoT Hub a vybere zařízení umístěná v USA a potom ty, které nahlásily mobilní připojení.

1. V aplikaci Visual Studio vyberte možnost **vytvořit nový projekt**. V poli **vytvořit nový projekt**vyberte **Konzolová aplikace (.NET Framework)** a pak vyberte **Další**.

1. V části **Konfigurovat nový projekt**pojmenujte projekt **AddTagsAndQuery**.

    ![Konfigurace projektu AddTagsAndQuery](./media/iot-hub-csharp-csharp-twin-getstarted/config-addtagsandquery-app.png)

1. V Průzkumník řešení klikněte pravým tlačítkem na projekt **AddTagsAndQuery** a pak vyberte **Spravovat balíčky NuGet**.

1. Vyberte **Procházet** a Hledat a vyberte **Microsoft. Azure. Devices**. Vyberte **Nainstalovat**.

    ![Okno Správce balíčků NuGet](./media/iot-hub-csharp-csharp-twin-getstarted/nuget-package-addtagsandquery-app.png)

   Tento krok stáhne a nainstaluje balíček NuGet [sady SDK služby Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) a jeho závislosti a přidá odkaz na něj.

1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

1. Do třídy **Program** přidejte následující pole. Nahraďte `{iot hub connection string}` připojovacím řetězcem IoT Hub, který jste zkopírovali v [části získání připojovacího řetězce centra IoT Hub](#get-the-iot-hub-connection-string).

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. Do třídy **program** přidejte následující metodu:

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

    Předchozí kód při vytváření objektu **dotazu** určuje maximální počet vrácených dokumentů. Objekt **dotazu** obsahuje vlastnost **HasMoreResults** Boolean, kterou můžete použít k vícenásobnému vyvolání metod **GetNextAsTwinAsync** pro načtení všech výsledků. Metoda s názvem **GetNextAsJson** je k dispozici pro výsledky, které nejsou nevlákenné zařízení, například výsledky agregačních dotazů.

1. Nakonec přidejte do metody **Main** následující řádky:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. Spusťte tuto aplikaci tak, že kliknete pravým tlačítkem na projekt **AddTagsAndQuery** a vyberete **ladění**a potom **zahájíte novou instanci**. Ve výsledcích dotazu pro všechna zařízení umístěná v **Redmond43** byste měli vidět jedno zařízení, které pro dotaz neomezuje výsledky na zařízení, která používají mobilní síť.

    ![Výsledky dotazu v okně](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

V další části vytvoříte aplikaci pro zařízení, která oznamuje informace o připojení a mění výsledek dotazu v předchozí části.

## <a name="create-the-device-app"></a>Vytvoření aplikace zařízení

V této části vytvoříte konzolovou aplikaci .NET, která se připojí k vašemu rozbočovači jako **myDeviceId**, a pak aktualizuje své hlášené vlastnosti tak, aby obsahovala informace, které jsou připojené pomocí mobilní sítě.

1. V aplikaci Visual Studio vyberte **soubor**  >  **Nový**  >  **projekt**. V možnosti **vytvořit nový projekt**zvolte **aplikace konzoly (.NET Framework)** a pak vyberte **Další**.

1. V části **Konfigurovat nový projekt**pojmenujte projekt **ReportConnectivity**. V případě **řešení**zvolte možnost **Přidat do řešení**a pak vyberte **vytvořit**.

1. V Průzkumník řešení klikněte pravým tlačítkem na projekt **ReportConnectivity** a pak vyberte **Spravovat balíčky NuGet**.

1. Vyberte **Procházet** a Hledat a zvolte **Microsoft. Azure. Devices. Client**. Vyberte **Nainstalovat**.

   Tento krok stáhne a nainstaluje balíček NuGet [sady SDK pro zařízení Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) a jeho závislosti a přidá se na něj odkaz.

1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Do třídy **Program** přidejte následující pole. Nahraďte `{device connection string}` připojovacím řetězcem zařízení, který jste si poznamenali v [části registrace nového zařízení ve službě IoT Hub](#register-a-new-device-in-the-iot-hub).

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

1. Do třídy **program** přidejte následující metodu:

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

    Objekt **klienta** zveřejňuje všechny metody, které vyžadujete pro interakci se zařízeními ze zařízení. Kód zobrazený výše inicializuje objekt **klienta** a potom načte vlákna zařízení pro **myDeviceId**.

1. Do třídy **program** přidejte následující metodu:

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

   Výše uvedený kód aktualizuje oznámenou vlastnost  **myDeviceId** informacemi o připojení.

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

1. V Průzkumník řešení klikněte pravým tlačítkem na řešení a vyberte **nastavit projekty po spuštění**.

1. V možnosti **běžné vlastnosti**  >  **spouštěný projekt**vyberte **více projektů po spuštění**. Pro **ReportConnectivity**vyberte **Spustit** jako **akci**. Výběrem **OK** uložte změny.  

1. Spusťte tuto aplikaci tak, že kliknete pravým tlačítkem na projekt **ReportConnectivity** a vyberete **ladění**a potom **zahájíte novou instanci**. Měli byste vidět, že aplikace získává informace o zdvojení, a pak odesílat připojení jako ***hlášenou vlastnost***.

    ![Spuštění aplikace zařízení pro hlášení připojení](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)

   Jakmile zařízení nahlásí informace o připojení, musí se zobrazit v obou dotazech.

1. Klikněte pravým tlačítkem na projekt **AddTagsAndQuery** a vyberte **ladit**  >  **spustit novou instanci** a spusťte dotazy znovu. Tentokrát by **myDeviceId** měla být ve výsledcích dotazu.

    ![Připojení zařízení se úspěšně nahlásilo.](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali novou službu IoT Hub na webu Azure Portal a potom jste vytvořili identitu zařízení v registru identit ve službě IoT Hub. Metadata zařízení jste přidali jako značky z back-endové aplikace a zapsali jste aplikaci simulovaného zařízení, která oznamuje informace o připojení zařízení v zařízení. Zjistili jste také, jak zadat dotaz na tyto informace pomocí dotazovacího jazyka IoT Hub, jako je třeba SQL.

Další informace můžete získat z následujících zdrojů:

* Informace o tom, jak odeslat telemetrii ze zařízení, najdete v kurzu [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-dotnet.md) .

* Informace o tom, jak nakonfigurovat zařízení pomocí požadovaných vlastností nevlákenných zařízení, najdete v kurzu [použití požadovaných vlastností ke konfiguraci zařízení](tutorial-device-twins.md) .

* Pokud se chcete dozvědět, jak interaktivně ovládat zařízení, například zapnout ventilátor z aplikace řízené uživateli, přečtěte si kurz [použití přímých metod](quickstart-control-device-dotnet.md) .
