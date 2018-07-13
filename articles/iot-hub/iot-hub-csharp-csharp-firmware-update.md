---
title: Aktualizace firmwaru zařízení s Azure IoT Hub (.NET/.NET) | Dokumentace Microsoftu
description: Jak zahájit aktualizaci firmwaru zařízení pomocí správy zařízení ve službě Azure IoT Hub. Použijete k implementaci aplikace simulovaného zařízení a služby Azure IoT SDK pro .NET k implementaci app service, která spustí aktualizaci firmwaru zařízení Azure IoT SDK pro .NET.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 10/19/2017
ms.author: dobett
ms.openlocfilehash: 1bf7a647ab2fdc175231133b0cfd8abdd51b6d43
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723928"
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnet"></a>Zahájit aktualizaci firmwaru zařízení (.NET/.NET) pomocí správy zařízení
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>Úvod
V [Začínáme se správou zařízení] [ lnk-dm-getstarted] výukový program, jste viděli, jak používat [dvojče zařízení] [ lnk-devtwin] a [přímé metody ] [ lnk-c2dmethod] primitiv vzdálené restartování zařízení. Tento kurz používá stejný základní služby IoT Hub a popisuje, jak provádět aktualizace firmwaru simulované začátku do konce.  Tento model se používá k provedení aktualizace firmwaru pro [ukázková implementace zařízení Raspberry Pi][lnk-rpi-implementation].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvoření konzolové aplikace .NET, která volá **firmwareUpdate** přímé metody v aplikaci simulovaného zařízení prostřednictvím služby IoT hub.
* Vytvoření aplikace simulovaného zařízení, která implementuje **firmwareUpdate** přímá metoda. Tato metoda zahájí vícefázový proces, který čeká na stažením image firmwaru, stáhne image firmwaru a nakonec použije image firmwaru. Během každé fáze aktualizace zařízení využívá ohlášené vlastnosti k podávat zprávy o pokroku.

Na konci tohoto kurzu budete mít zařízení konzolovou aplikaci .NET (C#) a back-end konzolovou aplikaci .NET (C#):

* **SimulatedDeviceFwUpdate**, propojuje službu IoT hub s identitou zařízení vytvořenou dříve, dostane **firmwareUpdate** přímé metody, spuštění prostřednictvím více stavu procesu pro simulaci aktualizace firmwaru včetně: čeká na stažení bitové kopie, stahuje nové image a nakonec použitím bitové kopie.

* **TriggerFWUpdate**, které se vzdáleně vyvolat pomocí sady SDK služby **firmwareUpdate** přímou metoda v simulovaném zařízení zobrazí odpovědi a pravidelně (každých 500ms) zobrazí aktualizovaný hlášené Vlastnosti.

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio 2015 nebo Visual Studio 2017.
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

Postupujte podle [Začínáme se správou zařízení](iot-hub-csharp-csharp-device-management-get-started.md) článek k vytvoření služby IoT hub a získejte připojovací řetězec služby IoT Hub.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Aktivace firmwaru vzdálené aktualizace na zařízení s využitím přímé metody
V této části vytvoříte konzolovou aplikaci .NET (s použitím jazyka C#), které vyvolává vzdálené firmwaru v zařízení. Aplikace používá přímé metody k zahájení aktualizace a používá dotazů na dvojčata zařízení a pravidelně získat stav aktualizace firmwaru aktivní.

1. V sadě Visual Studio přidejte k stávajícímu řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace**. Pojmenujte projekt **TriggerFWUpdate**.

    ![Nový klasický desktopový projekt Visual C# pro systém Windows][img-createserviceapp]

2. V Průzkumníku řešení klikněte pravým tlačítkem myši **TriggerFWUpdate** projektu a pak klikněte na tlačítko **spravovat balíčky NuGet**.
3. V okně **Správce balíčků NuGet** vyberte **Procházet**, vyhledejte **microsoft.azure.devices**, vyberte možnost **Instalovat**, nainstalujte balíček  **Microsoft.Azure.Devices** a přijměte podmínky používání. Tímto postupem se stáhne a nainstaluje [balíček NuGet sady SDK pro službu Azure IoT][lnk-nuget-service-sdk] a jeho závislosti a přidá se na něj odkaz.

    ![Okno Správce balíčků NuGet][img-servicenuget]
4. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

    ```csharp   
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Do třídy **Program** přidejte následující pole. Více zástupné hodnoty nahraďte připojovací řetězec služby IoT Hub, kterou jste vytvořili v předchozí části a ID zařízení centra.
   
    ```csharp   
    static RegistryManager registryManager;
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static string targetDevice = "{deviceIdForTargetDevice}";
    ```
        
6. Do třídy **Program** přidejte následující metodu. Tato metoda dotazuje dvojče zařízení pro aktualizovaný stav každých 500 milisekund. Pouze v případě, že ve skutečnosti změnu stavu, zapíše se do konzoly. Pro tuto ukázku, aby se zabránilo přijímat další zprávy služby IoT Hub v rámci vašeho předplatného, cyklického dotazování zastaví, když zařízení ohlásí a stav **applyComplete** nebo došlo k chybě.  
   
    ```csharp   
    public static async Task QueryTwinFWUpdateReported(DateTime startTime)
    {
        DateTime lastUpdated = startTime;

        while (true)
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);

            if (twin.Properties.Reported.GetLastUpdated().ToUniversalTime() > lastUpdated.ToUniversalTime())
            {
                lastUpdated = twin.Properties.Reported.GetLastUpdated().ToUniversalTime();
                Console.WriteLine("\n" + twin.Properties.Reported["iothubDM"].ToJson());

                var status = twin.Properties.Reported["iothubDM"]["firmwareUpdate"]["status"].Value;
                if ((status == "downloadFailed") || (status == "applyFailed") || (status == "applyComplete"))
                {
                    Console.WriteLine("\nStop polling.");
                    return;
                }
            }
            await Task.Delay(500);
        }
    }
    ```
        
7. Přidejte následující metodu do třídy **Program**:

    ```csharp   
    public static async Task StartFirmwareUpdate()
    {
        client = ServiceClient.CreateFromConnectionString(connString);
        CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
        method.ResponseTimeout = TimeSpan.FromSeconds(30);
        method.SetPayloadJson(
            @"{
               fwPackageUri : 'https://someurl'
            }");

        CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

        Console.WriteLine("Invoked firmware update on device.");
    }
    ```

8. Nakonec přidejte následující řádky do **hlavní** metody. Tím se vytvoří registr správce ke čtení dvojčat zařízení se spustí úloha cyklického dotazování na pracovní podproces a potom aktivuje aktualizace firmwaru.
   
    ```csharp   
    registryManager = RegistryManager.CreateFromConnectionString(connString);

    Task queryTask = Task.Run(() => (QueryTwinFWUpdateReported(DateTime.Now)));

    StartFirmwareUpdate().Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```
        
9. Sestavte řešení.

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení
V této části:

* Vytvoření konzolové aplikace .NET, která bude reagovat na přímou metodu volanou cloudem.
* Simulace aktualizaci firmwaru aktivované back-end službu prostřednictvím přímé metody
* Pomocí ohlášených vlastností umožníte dotazům na dvojčata zařízení identifikovat zařízení a čas jejich poslední dokončené aktualizace firmwaru.

1. V sadě Visual Studio přidejte k stávajícímu řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace**. Pojmenujte projekt **SimulateDeviceFWUpdate**.
   
    ![Nový Visual C# Windows klasické aplikace pro zařízení][img-createdeviceapp]
    
2. V Průzkumníku řešení klikněte pravým tlačítkem myši **SimulateDeviceFWUpdate** projektu a pak klikněte na tlačítko **spravovat balíčky NuGet**.
3. V **Správce balíčků NuGet** okně **Procházet** a vyhledejte **microsoft.azure.devices.client**. Vyberte **nainstalovat** k instalaci **Microsoft.Azure.Devices.Client** balíček a přijměte podmínky použití. Tento postup stáhne, nainstaluje a přidá odkaz na [zařízení Azure IoT SDK] [ lnk-nuget-client-sdk] NuGet balíček a jeho závislosti.
   
    ![Klientská aplikace okno Správce balíčků NuGet][img-clientnuget]
4. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:
   
    ```csharp   
    using Newtonsoft.Json.Linq;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem zařízení, který jste si poznamenali v **vytvoření identity zařízení** oddílu.
   
    ```csharp   
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. Přidejte následující metodu do hlášení stavu zpět do cloudu prostřednictvím dvojčeti zařízení: 

    ```csharp   
    static async Task reportFwUpdateThroughTwin(Twin twin, TwinCollection fwUpdateValue)
    {
        try
        {
            TwinCollection patch = new TwinCollection();
            TwinCollection iothubDM = new TwinCollection();

            iothubDM["firmwareUpdate"] = fwUpdateValue;
            patch["iothubDM"] = iothubDM;

            await Client.UpdateReportedPropertiesAsync(patch);
            Console.WriteLine("Twin state reported: {0}", fwUpdateValue["status"]);
        }
        catch 
        {
            Console.WriteLine("Error updating device twin");
            throw;
        }
    }
    ```

7. Přidejte následující metodu pro simulaci stažením image firmwaru:
        
    ```csharp   
    static async Task<byte[]> simulateDownloadImage(string imageUrl)
    {
        var image = "[fake image data]";

        Console.WriteLine("Downloading image from " + imageUrl);

        await Task.Delay(4000);

        return Encoding.ASCII.GetBytes(image);
            
    }
    ```

8. Přidejte následující metodu pro simulaci aplikování image firmwaru zařízení:
        
    ```csharp   
    static async Task simulateApplyImage(byte[] imageData)
    {
        if (imageData == null)
        {
            throw new ArgumentNullException();
        }

        await Task.Delay(4000);

    }
    ```
 
9.  Přidejte následující metodu pro simulaci čeká se na stažením image firmwaru. Aktualizovat stav na **čekání** a zrušte jiné firmware aktualizovat vlastnosti v dvojčeti. Tyto vlastnosti jsou vymazány odebrat všechny existující hodnoty z předchozího firmware aktualizací. To je nezbytné, protože ohlášené vlastnosti jsou odeslány jako operace opravy (položka delta) a ne operace PUT (úplnou sadu vlastností, který nahrazuje všechna předchozí hodnoty). Zařízení jsou obvykle informována o dostupné aktualizaci a správcem definovaná zásada způsobí, že začnou stahovat a aplikovat aktualizaci firmwaru. V této funkci by měla běžet logika, která povoluje tuto zásadu. 
        
    ```csharp   
    static async Task waitToDownload(Twin twin, string fwUpdateUri)
    {
        var now = DateTime.Now;
        TwinCollection status = new TwinCollection();
        status["fwPackageUri"] = fwUpdateUri;
        status["status"] = "waiting";
        status["error"] = null;
        status["startedWaitingTime"] = DateTime.Now;
        status["downloadCompleteTime"] = null;
        status["startedApplyingImage"] = null;
        status["lastFirmwareUpdate"] = null;

        await reportFwUpdateThroughTwin(twin, status);

        await Task.Delay(2000);
    }
    ```

10. Přidejte následující metodu, který má stažení provést. Aktualizuje stav na **stahování** prostřednictvím dvojčeti zařízení volá metodu stahování simulovat a stavu **downloadComplete** nebo **downloadFailed** pomocí dvojčete v závislosti na výsledcích operaci stahování. 
        
    ```csharp   
    static async Task<byte[]> downloadImage(Twin twin, string fwUpdateUri)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloading";
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            byte[] imageData = await simulateDownloadImage(fwUpdateUri);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadComplete";
            statusUpdate["downloadCompleteTime"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            return imageData;
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

11. Přidejte následující metodu použít bitovou kopii. Aktualizuje stav na **použití** prostřednictvím dvojčeti zařízení simulovat použít volání image metoda a aktualizuje stav na **applyComplete** nebo **applyFailed** prostřednictvím dvojče v závislosti na výsledcích operaci použít. 
        
    ```csharp   
    static async Task applyImage(Twin twin, byte[] imageData)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applying";
            statusUpdate["startedApplyingImage"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            await simulateApplyImage(imageData);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyComplete";
            statusUpdate["lastFirmwareUpdate"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

12. Přidejte následující metodu do sekvence operace aktualizace firmwaru z čekání na stáhnout bitovou kopii prostřednictvím použitím bitové kopie do zařízení:
        
    ```csharp   
    static async Task doUpdate(string fwUpdateUrl)
    {
        try
        {
            Twin twin = await Client.GetTwinAsync();
            await waitToDownload(twin, fwUpdateUrl);
            byte[] imageData = await downloadImage(twin, fwUpdateUrl);
            await applyImage(twin, imageData);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error during update: {0}", ex.Message);
        }
    }
    ```

13. Přidejte následující metodu pro zpracování **updateFirmware** přímá metoda z cloudu. Extrahuje adresu URL k aktualizaci firmwaru z datové části zprávy a předává jej do **doUpdate** úkolu, který se spouští v jiném vláknu fondu vláken. Potom okamžitě vrátí odpověď metody do cloudu.
        
    ```csharp   
    static Task<MethodResponse> onFirmwareUpdate(MethodRequest methodRequest, object userContext)
    {
        string fwUpdateUrl = (string)JObject.Parse(methodRequest.DataAsJson)["fwPackageUri"];
        Console.WriteLine("\nMethod: {0} triggered by service, URI is: {1}", methodRequest.Name, fwUpdateUrl);

        Task updateTask = Task.Run(() => (doUpdate(fwUpdateUrl)));

        string result = "'FirmwareUpdate started.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```
> [!NOTE]
> Tato metoda spustí simulovanou aktualizaci spustit jako **úloh** a okamžitě odpovídá na volání metody, informuje službu spuštění aktualizace firmwaru. Stav aktualizace a dokončení se odešlou do služby pomocí ohlášené vlastnosti dvojčete zařízení. Reagujeme na volání metody, které při zahájení aktualizace, nikoli po jeho dokončení, protože:
> * Proces skutečné aktualizace je velmi pravděpodobné trvat déle než časový limit volání metody.
> * Proces skutečné aktualizace je velmi pravděpodobně vyžadují restartování, které by znovu spustit provádění této aplikace **MethodRequest** objektu není k dispozici. (Aktualizaci ohlášených vlastností, je však možné i po restartování.) 

14. Nakonec přidejte následující kód, který **hlavní** metody k otevření připojení do služby IoT hub a inicializovat naslouchací proces – metoda:
   
    ```csharp   
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
        
        // setup callback for "firmware update" method
        Client.SetMethodHandlerAsync("firmwareUpdate", onFirmwareUpdate, null).Wait();
        Console.WriteLine("Waiting for firmware update direct method call\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        // as a good practice, remove the firmware update handler
        Client.SetMethodHandlerAsync("firmwareUpdate", null, null).Wait();
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
15. Sestavte řešení.       

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byte měli implementovat zásady opakování (například exponenciální opakování), jak je navrženo v článku [Řešení přechodných chyb][lnk-transient-faults] na webu MSDN.


## <a name="run-the-apps"></a>Spouštění aplikací
Nyní jste připraveni aplikaci spustit.
1. Spusťte aplikaci .NET pro zařízení **SimulatedDeviceFWUpdate**.  Klikněte pravým tlačítkem myši **SimulatedDeviceFWUpdate** projekt, vyberte **ladění**a pak vyberte **zahájit novou instanci**. By se měl spustit naslouchání pro volání metody ze služby IoT Hub: 

2. Jakmile je zařízení připojené a čeká volání metod, spusťte .NET **TriggerFWUpdate** aplikaci, která se má vyvolat metodu aktualizace firmwaru v aplikaci simulovaného zařízení. Klikněte pravým tlačítkem myši **TriggerFWUpdate** projekt, vyberte **ladění**a pak vyberte **zahájit novou instanci**. By se měla zobrazit aktualizace napsané v pořadí **SimulatedDeviceFWUpdate** konzoly a také pořadí vykazovat prostřednictvím ohlášených vlastností zařízení **TriggerFWUpdate** konzoly. Všimněte si, že tento proces trvá několik sekund na dokončení. 
   
    ![Běh aplikace služby a zařízení][img-combinedrun]


## <a name="next-steps"></a>Další postup
V tomto kurzu se používá přímé metody k aktivaci vzdáleného firmwaru v zařízení a umožňuje sledovat průběh aktualizace firmwaru ohlášené vlastnosti.

Zjistěte, jak rozšířit vaše IoT řešení a plán metoda volá do různých zařízení, najdete v článku [plánování a vysílání úloh] [ lnk-tutorial-jobs] kurzu.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-firmware-update/servicesdknuget.png
[img-clientnuget]: media/iot-hub-csharp-csharp-firmware-update/clientsdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-firmware-update/createserviceapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-firmware-update/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-firmware-update/combinedrun.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-csharp-csharp-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-csharp-csharp-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
