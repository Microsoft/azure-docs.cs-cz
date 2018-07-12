---
title: Začínáme se správou zařízení Azure IoT Hub (.NET/.NET) | Dokumentace Microsoftu
description: Jak používat správu zařízení Azure IoT Hub k zahajte restartování vzdáleném zařízení. Implementace aplikace s Simulovaná zařízení, která obsahuje metodu s přímým přístupem a službu Azure IoT SDK pro .NET k implementaci app service, která vyvolá přímou metodu pomocí zařízení Azure IoT SDK pro .NET.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: c1cee32e3ee60ce229308055cca7f0e9832ddc49
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38578896"
---
# <a name="get-started-with-device-management-netnet"></a>Začínáme se správou zařízení (.NET/.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

V tomto kurzu získáte informace o následujících postupech:

* Pomocí webu Azure portal k vytvoření služby IoT Hub a vytvoření identity zařízení ve službě IoT hub.
* Vytvoření aplikace simulovaného zařízení, která obsahuje přímé metody, která toto zařízení restartuje. Přímé metody jsou vyvolány z cloudu.
* Vytvoření konzolové aplikace .NET, která volá metodu restartování s přímým přístupem v aplikaci simulovaného zařízení prostřednictvím služby IoT hub.

Na konci tohoto kurzu budete mít dvě konzolové aplikace .NET:

* **SimulateManagedDevice**, propojuje službu IoT hub s identitou zařízení vytvořenou dříve, obdrží restartování přímé metody, simuluje fyzické restartování a ohlásí, čas poslední restartování.
* **TriggerReboot**, která volá metodu s přímým přístupem v aplikaci simulovaného zařízení, zobrazí odpověď a zobrazí aktualizovaný ohlášené vlastnosti.

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio 2015 nebo Visual Studio 2017.
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

<a id="DeviceIdentity_csharp"></a>
[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Aktivační událost vzdálené restartování zařízení pomocí přímé metody
V této části vytvoříte konzolovou aplikaci .NET (s použitím jazyka C#), které vyvolává vzdálené restartování zařízení pomocí přímé metody. Aplikace používá dotazů na dvojčata zařízení ke zjištění poslední čas restartování pro dané zařízení.

1. V sadě Visual Studio přidejte k novému řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace (.NET Framework)**. Zkontrolujte, zda máte verzi rozhraní .NET Framework 4.5.1 nebo novější. Pojmenujte projekt **TriggerReboot**.

    ![Nový klasický desktopový projekt Visual C# pro systém Windows][img-createserviceapp]

2. V Průzkumníku řešení klikněte pravým tlačítkem myši **TriggerReboot** projektu a pak klikněte na tlačítko **spravovat balíčky NuGet**.
3. V okně **Správce balíčků NuGet** vyberte **Procházet**, vyhledejte **microsoft.azure.devices**, vyberte možnost **Instalovat**, nainstalujte balíček  **Microsoft.Azure.Devices** a přijměte podmínky používání. Tímto postupem se stáhne a nainstaluje [balíček NuGet sady SDK pro službu Azure IoT][lnk-nuget-service-sdk] a jeho závislosti a přidá se na něj odkaz.

    ![Okno Správce balíčků NuGet][img-servicenuget]
4. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
5. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem služby IoT Hub pro rozbočovač, kterou jste vytvořili v části "Vytvoření služby IoT hub." 
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static string targetDevice = "myDeviceId";
        
6. Do třídy **Program** přidejte následující metodu.  Tento kód získá dvojčeti zařízení restartuje zařízení a vypíše ohlášené vlastnosti.
   
        public static async Task QueryTwinRebootReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. Do třídy **Program** přidejte následující metodu.  Tento kód inicializuje restartování na zařízení s využitím přímé metody.

        public static async Task StartReboot()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

7. Nakonec do metody **Main** přidejte následující řádky:
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartReboot().Wait();
        QueryTwinRebootReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
8. Sestavte řešení.

> [!NOTE]
> V tomto kurzu provádí pouze pomocí jediného dotazu pro ohlášených vlastností zařízení. V produkčním kódu doporučujeme dotazování zjišťovat změny v ohlášené vlastnosti.

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení
V této části provedete

* Vytvoření konzolové aplikace .NET, která bude reagovat na přímou metodu volanou cloudem.
* Aktivovat restartování simulovaného zařízení
* Pomocí ohlášených vlastností umožníte dotazů na dvojčata zařízení identifikovat zařízení a kdy se naposledy restartování

1. V sadě Visual Studio přidejte k stávajícímu řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace**. Pojmenujte projekt **SimulateManagedDevice**.
   
    ![Nový Visual C# Windows klasické aplikace pro zařízení][img-createdeviceapp]
    
2. V Průzkumníku řešení klikněte pravým tlačítkem myši **SimulateManagedDevice** projektu a pak klikněte na tlačítko **spravovat balíčky NuGet...** .
3. V **Správce balíčků NuGet** okně **Procházet** a vyhledejte **microsoft.azure.devices.client**. Vyberte **nainstalovat** k instalaci **Microsoft.Azure.Devices.Client** balíček a přijměte podmínky použití. Tento postup stáhne, nainstaluje a přidá odkaz na [zařízení Azure IoT SDK] [ lnk-nuget-client-sdk] NuGet balíček a jeho závislosti.
   
    ![Klientská aplikace okno Správce balíčků NuGet][img-clientnuget]
4. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;

5. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem zařízení, který jste si poznamenali v předchozí části.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

6. Přidejte následující k implementaci přímé metody v zařízení:

        static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
        {
            // In a production device, you would trigger a reboot scheduled to start after this method returns
            // For this sample, we simulate the reboot by writing to the console and updating the reported properties 
            try
            {
                Console.WriteLine("Rebooting!");

                // Update device twin with reboot time. 
                TwinCollection reportedProperties, reboot, lastReboot;
                lastReboot = new TwinCollection();
                reboot = new TwinCollection();
                reportedProperties = new TwinCollection();
                lastReboot["lastReboot"] = DateTime.Now;
                reboot["reboot"] = lastReboot;
                reportedProperties["iothubDM"] = reboot;
                Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }

            string result = "'Reboot started.'";
            return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
        }

7. Nakonec přidejte následující kód, který **hlavní** metody k otevření připojení do služby IoT hub a inicializovat naslouchací proces – metoda:
   
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

            // setup callback for "reboot" method
            Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
            Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
            Console.ReadLine();

            Console.WriteLine("Exiting...");

            // as a good practice, remove the "reboot" handler
            Client.SetMethodHandlerAsync("reboot", null, null).Wait();
            Client.CloseAsync().Wait();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
        
8. V Průzkumníku řešení sady Visual Studio klikněte pravým tlačítkem na své řešení a pak klikněte na **Nastavit projekty po spuštění...**. Vyberte **jeden spouštěný projekt**a pak vyberte **SimulateManagedDevice** projektu v rozevírací nabídce. Sestavte řešení.       

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byte měli implementovat zásady opakování (například exponenciální opakování), jak je navrženo v článku [Řešení přechodných chyb][lnk-transient-faults] na webu MSDN.


## <a name="run-the-apps"></a>Spouštění aplikací
Nyní jste připraveni aplikaci spustit.
1. Spusťte aplikaci .NET pro zařízení **SimulateManagedDevice**.  Klikněte pravým tlačítkem myši **SimulateManagedDevice** projekt, vyberte **ladění**a pak vyberte **zahájit novou instanci**. By se měl spustit naslouchání pro volání metody ze služby IoT Hub: 

2. Teď, když je zařízení připojené a čeká volání metod, spusťte .NET **TriggerReboot** aplikace k vyvolání metody restartování v aplikaci simulovaného zařízení. Klikněte pravým tlačítkem myši **TriggerReboot** projekt, vyberte **ladění**a pak vyberte **zahájit novou instanci**. Měli byste vidět "Restartování!" napsané v **SimulatedManagedDevice** konzoly a ohlášených vlastností zařízení, mezi které patří poslední čas napsané v restartování **TriggerReboot** konzoly.
   
    ![Běh aplikace služby a zařízení][img-combinedrun]

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png
[img-servicenuget]: media/iot-hub-csharp-csharp-device-management-get-started/servicesdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-device-management-get-started/createserviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-device-management-get-started/clientsdknuget.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-device-management-get-started/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
