---
title: Začínáme s dvojčaty zařízení Azure IoT Hub (.NET/.NET) | Dokumentace Microsoftu
description: Jak používat dvojče zařízení Azure IoT Hub pro přidání značek a následné použití k dotazu služby IoT Hub. Implementace aplikace simulovaného zařízení a služby Azure IoT SDK pro .NET k implementaci app service, které přidá značky a spustí dotaz služby IoT Hub pomocí zařízení Azure IoT SDK pro .NET.
author: dsk-2015
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: dkshir
ms.openlocfilehash: 0ce1a3983ad34882236b175d6b8eec5e538c736b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723900"
---
# <a name="get-started-with-device-twins-netnet"></a>Začínáme s dvojčaty zařízení (.NET/.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na konci tohoto kurzu budete mít tyto aplikace konzoly .NET:

* **CreateDeviceIdentity**, aplikace .NET, která vytvoří identitu zařízení a přiřazený bezpečnostní klíč k připojení simulované aplikace zařízení.
* **AddTagsAndQuery**, .NET back endové aplikace, které přidá značky a dotazy dvojčata zařízení.
* **ReportConnectivity**, aplikace .NET zařízení, která simuluje zařízení, která se připojuje ke službě IoT hub s dříve vytvořenou identitou zařízení a sestav stavu připojení.

> [!NOTE]
> Tento článek [sad SDK Azure IoT] [ lnk-hub-sdks] poskytuje informace o Azure IoT SDK, že vám pomůže vytvářet aplikace pro zařízení i back-end.
> 
> 

K dokončení tohoto kurzu budete potřebovat následující:

* Visual Studio 2015 nebo Visual Studio 2017.
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-service-app"></a>Vytvoření aplikace služby
V této části vytvoříte konzolovou aplikaci .NET (s použitím jazyka C#), která přidává umístění metadat do dvojčete zařízení spojené s **myDeviceId**. Následně se dotazuje dvojčata zařízení, které jsou uložené ve službě IoT hub výběrem umístěné v USA a ty, kteří hlásili mobilní připojení zařízení.

1. V sadě Visual Studio přidejte k stávajícímu řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace**. Pojmenujte projekt **AddTagsAndQuery**.
   
    ![Nový klasický desktopový projekt Visual C# pro systém Windows][img-createapp]
1. V Průzkumníku řešení klikněte pravým tlačítkem myši **AddTagsAndQuery** projektu a pak klikněte na tlačítko **spravovat balíčky NuGet...** .
1. V **Správce balíčků NuGet** okně **Procházet** a vyhledejte **microsoft.azure.devices**. Vyberte **nainstalovat** k instalaci **Microsoft.Azure.Devices** balíček a přijměte podmínky použití. Tímto postupem se stáhne a nainstaluje [balíček NuGet sady SDK pro službu Azure IoT][lnk-nuget-service-sdk] a jeho závislosti a přidá se na něj odkaz.
   
    ![Okno Správce balíčků NuGet][img-servicenuget]
1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:
   
        using Microsoft.Azure.Devices;
1. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem pro službu IoT Hub, kterou jste vytvořili v předchozí části.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. Přidejte následující metodu do třídy **Program**:
   
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
   
            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));
   
            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }
   
    **RegistryManager** třída zveřejňuje všechny metody, které jsou nutné k interakci s dvojčaty zařízení ze služby. Předchozí kód nejprve inicializuje **registryManager** a pak načte dvojče zařízení pro **myDeviceId**a nakonec aktualizuje její klíčová slova s informacemi o požadované umístění.
   
    Po aktualizaci se provede dva dotazy: první vybere pouze dvojčata zařízení nachází v zařízení **Redmond43** kategorie plant a druhá zpřesnění dotaz pro výběr pouze do zařízení, které jsou také připojené přes mobilní síť.
   
    Všimněte si, že předchozí kód, když vytváří **dotazu** objektu, určuje maximální počet vrácených dokumentů. **Dotazu** obsahuje objekt **HasMoreResults** logická vlastnost, která můžete použít k vyvolání **GetNextAsTwinAsync** metody víc než jednou načíst všechny výsledky. Volána metoda **GetNextAsJson** je k dispozici pro výsledky, které není dvojčata zařízení, třeba výsledky dotazů agregace.
1. Nakonec do metody **Main** přidejte následující řádky:
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

1. V Průzkumníku řešení otevřete **nastavit projekty po spuštění...**  a ujistěte se, že **akce** pro **AddTagsAndQuery** projekt je **Start**. Sestavte řešení.
1. Tuto aplikaci spustit kliknutím pravým tlačítkem na **AddTagsAndQuery** projekt a výběrem **ladění**následovaný **zahájit novou instanci**. Měli byste vidět jedno zařízení ve výsledcích pro kladení dotazů pro všechna zařízení nachází v **Redmond43** a žádné dotaz, který omezuje výsledky na zařízení, která používají mobilní síti.
   
    ![Výsledky dotazu v okně][img-addtagapp]

V další části můžete vytvořit aplikace pro zařízení, která hlásí informace o připojení a změní výsledek dotazu v předchozí části.

## <a name="create-the-device-app"></a>Vytvoření aplikace pro zařízení
V této části vytvoříte konzolovou aplikaci .NET, která se připojuje k vašemu centru jako **myDeviceId**a pak aktualizuje ohlášených vlastností obsahují informace, že je připojený pomocí mobilní síti.

1. V sadě Visual Studio přidejte k stávajícímu řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace**. Pojmenujte projekt **ReportConnectivity**.
   
    ![Nový Visual C# Windows klasické aplikace pro zařízení][img-createdeviceapp]
    
1. V Průzkumníku řešení klikněte pravým tlačítkem myši **ReportConnectivity** projektu a pak klikněte na tlačítko **spravovat balíčky NuGet...** .
1. V **Správce balíčků NuGet** okně **Procházet** a vyhledejte **microsoft.azure.devices.client**. Vyberte **nainstalovat** k instalaci **Microsoft.Azure.Devices.Client** balíček a přijměte podmínky použití. Tento postup stáhne, nainstaluje a přidá odkaz na [zařízení Azure IoT SDK] [ lnk-nuget-client-sdk] NuGet balíček a jeho závislosti.
   
    ![Klientská aplikace okno Správce balíčků NuGet][img-clientnuget]
1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using Newtonsoft.Json;

1. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem zařízení, který jste si poznamenali v předchozí části.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

1. Přidejte následující metodu do třídy **Program**:

       public static async void InitClient()
        {
            try
            {
                Console.WriteLine("Connecting to hub");
                Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
                Console.WriteLine("Retrieving twin");
                await Client.GetTwinAsync();
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

    **Klienta** objekt poskytuje všechny metody, které budete potřebovat pro interakci s dvojčaty zařízení ze zařízení. Inicializuje kódu uvedeného výše **klienta** objekt a potom načte dvojče zařízení pro **myDeviceId**.

1. Přidejte následující metodu do třídy **Program**:
   
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

   Kód výše aktualizace **myDeviceId**je uvedena vlastnost s informacemi o připojení.

1. Nakonec do metody **Main** přidejte následující řádky:
   
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

1. V Průzkumníku řešení otevřete **nastavit projekty po spuštění...**  a ujistěte se, že **akce** pro **ReportConnectivity** projekt je **Start**. Sestavte řešení.
1. Tuto aplikaci spustit kliknutím pravým tlačítkem na **ReportConnectivity** projekt a výběrem **ladění**následovaný **zahájit novou instanci**. Měli byste vidět, získávání dvojčete informace a potom odešlete připojení jako *hlášené vlastnost*.
   
    ![Spuštění aplikace pro zařízení pro připojení k sestavě][img-rundeviceapp]
    
    
1. Teď, když zařízení uvádět jeho informace o připojení, měl by se zobrazit v obou dotazech. Spuštění rozhraní .NET **AddTagsAndQuery** aplikaci znovu spustit dotazy. Tentokrát **myDeviceId** by se měla objevit v obou výsledky dotazu.
   
    ![Připojení zařízení úspěšně hlášené][img-tagappsuccess]

## <a name="next-steps"></a>Další postup
V tomto kurzu jste nakonfigurovali novou službu IoT Hub na webu Azure Portal a potom jste vytvořili identitu zařízení v registru identit ve službě IoT Hub. Přidá metadata zařízení jako značky z back endové aplikace a aplikace s Simulovaná zařízení zapsáno do sestavy informací o připojení k zařízení ve dvojčeti zařízení. Také jste zjistili, jak zadávat dotazy na tyto informace pomocí dotazovacího jazyka podobného SQL IoT Hub.

Pomocí následujících zdrojích se dozvíte jak:

* odesílání telemetrie ze zařízení s [Začínáme se službou IoT Hub] [ lnk-iothub-getstarted] kurzu
* Konfigurace zařízení pomocí požadované vlastnosti dvojčete zařízení s [použití požadované vlastnosti ke konfiguraci zařízení] [ lnk-twin-how-to-configure] kurzu
* ovládací prvek zařízení interaktivně (například zapnutí ventilátor z aplikace řízené uživatelem) [použití přímých metod] [ lnk-methods-tutorial] kurzu.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-twin-getstarted/createdeviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-twin-getstarted/clientsdknuget.png
[img-rundeviceapp]: media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png
[img-tagappsuccess]: media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/

[lnk-device-identity-csharp]: iot-hub-csharp-csharp-getstarted.md#DeviceIdentity_csharp
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-csharp-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

