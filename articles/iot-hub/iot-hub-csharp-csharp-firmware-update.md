---
title: Aktualizace firmwaru zařízení s Azure IoT Hub (.NET/.NET) | Microsoft Docs
description: Jak používat správu zařízení v Azure IoT Hub zahájíte aktualizaci firmwaru zařízení. Použití zařízení Azure IoT sady SDK pro .NET k implementaci aplikace simulovaného zařízení a sady SDK pro .NET k implementaci služby aplikaci, která spustí aktualizaci firmwaru služby Azure IoT.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 10/19/2017
ms.author: dobett
ms.openlocfilehash: cd669a9585ac5aecf935202a04065a828a2174be
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736751"
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnet"></a>Použití správy zařízení za účelem zahájení aktualizaci firmwaru zařízení (.NET/.NET)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>Úvod
V [Začínáme se správou zařízení] [ lnk-dm-getstarted] kurz, jste viděli, jak používat [dvojče zařízení] [ lnk-devtwin] a [přímé metody ] [ lnk-c2dmethod] primitiv vzdáleně restartování zařízení. Tento kurz používá stejné primitiv IoT Hub a ukazuje, jak provést aktualizaci firmwaru simulované začátku do konce.  Tento vzor slouží k implementaci firmwaru aktualizace pro [malin platformy zařízení implementace ukázka][lnk-rpi-implementation].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvoření konzolové aplikace .NET, která volá **firmwareUpdate** přímá metoda v aplikaci simulovaného zařízení prostřednictvím služby IoT hub.
* Vytvoření aplikace simulovaného zařízení, která implementuje **firmwareUpdate** přímá metoda. Tato metoda inicializuje více fáze procesu, který čeká na stažení bitové kopie firmwaru, stáhne bitovou kopii firmware a nakonec platí bitovou kopii firmwaru. Během aktualizace v každé fázi používá zařízení hlášené vlastnosti hlásit průběh.

Na konci tohoto kurzu máte zařízení konzolové aplikace .NET (C#) a back-end konzolové aplikace .NET (C#):

* **SimulatedDeviceFwUpdate**, který se připojí ke službě IoT hub s identitou zařízení vytvořenou dříve, obdrží **firmwareUpdate** přímá metoda, spustí prostřednictvím více stavu procesu k simulaci aktualizaci firmwaru včetně: Čekání na stažení bitové kopie, stahování novou bitovou kopii a nakonec použitím bitové kopie.

* **TriggerFWUpdate**, které se vzdáleně vyvolání pomocí sady SDK služby **firmwareUpdate** přímá metoda v simulovaném zařízení zobrazí odpověď a pravidelně (každých 500ms) zobrazí aktualizovaná hlášené Vlastnosti.

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio 2015 nebo Visual Studio 2017.
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

Postupujte podle [Začínáme se správou zařízení](iot-hub-csharp-csharp-device-management-get-started.md) článek k vytvoření služby IoT hub a získat připojovací řetězec služby IoT Hub.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Spustit aktualizaci vzdálené firmwaru v zařízení s přímá metoda
V této části vytvoříte konzolové aplikace .NET (pomocí jazyka C#), zahájí aktualizaci vzdálené firmwaru v zařízení. Přímá metoda používá k zahájení aktualizace a aplikace používá zařízení twin dotazy a pravidelně získat stav aktualizace active firmware.

1. V sadě Visual Studio přidejte k stávajícímu řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace**. Název projektu **TriggerFWUpdate**.

    ![Nový klasický desktopový projekt Visual C# pro systém Windows][img-createserviceapp]

2. V Průzkumníku řešení klikněte pravým tlačítkem myši **TriggerFWUpdate** projektu a pak klikněte na **spravovat balíčky NuGet**.
3. V okně **Správce balíčků NuGet** vyberte **Procházet**, vyhledejte **microsoft.azure.devices**, vyberte možnost **Instalovat**, nainstalujte balíček  **Microsoft.Azure.Devices** a přijměte podmínky používání. Tímto postupem se stáhne a nainstaluje [balíček NuGet sady SDK pro službu Azure IoT][lnk-nuget-service-sdk] a jeho závislosti a přidá se na něj odkaz.

    ![Okno Správce balíčků NuGet][img-servicenuget]
4. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

    ```csharp   
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Do třídy **Program** přidejte následující pole. Více zástupné hodnoty nahraďte připojovací řetězec služby IoT Hub pro rozbočovače, který jste vytvořili v předchozí části a ID vašeho zařízení.
   
    ```csharp   
    static RegistryManager registryManager;
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static string targetDevice = "{deviceIdForTargetDevice}";
    ```
        
6. Do třídy **Program** přidejte následující metodu. Tato metoda dotazuje dvojče zařízení pro aktualizovaný stav každých 500 milisekund. Jenom v případě, že stav se změnil ve skutečnosti, zapíše se do konzoly. Pro tuto ukázku, aby se zabránilo využívání zprávy navíc IoT Hub v rámci vašeho předplatného, dotazování zastaví, když zařízení ohlásí, stav **applyComplete** nebo došlo k chybě.  
   
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

8. Nakonec přidejte následující řádky, které se **hlavní** metoda. Tím se vytvoří soubor registru Správce číst dvojče zařízení se spustí úlohu dotazování na pracovní vlákno a potom spustí aktualizaci firmwaru.
   
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

* Vytvoření konzolové aplikace .NET, která reaguje na přímé metodu s názvem cloudem
* Simulovat aktualizaci firmwaru aktivovány back-end službu prostřednictvím přímé metody
* Pomocí ohlášených vlastností umožníte dotazům na dvojčata zařízení identifikovat zařízení a čas jejich poslední dokončené aktualizace firmwaru.

1. V sadě Visual Studio přidejte k stávajícímu řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace**. Název projektu **SimulateDeviceFWUpdate**.
   
    ![Novou aplikaci Visual C# klasické zařízení][img-createdeviceapp]
    
2. V Průzkumníku řešení klikněte pravým tlačítkem myši **SimulateDeviceFWUpdate** projektu a pak klikněte na **spravovat balíčky NuGet**.
3. V **Správce balíčků NuGet** vyberte **Procházet** a vyhledejte **microsoft.azure.devices.client**. Vyberte **nainstalovat** k instalaci **Microsoft.Azure.Devices.Client** balíček a přijměte podmínky použití. Tento postup stáhne, nainstaluje a přidá odkaz na [zařízení Azure IoT SDK] [ lnk-nuget-client-sdk] NuGet balíček a jeho závislé součásti.
   
    ![Správce balíčků NuGet okno klientské aplikace][img-clientnuget]
4. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:
   
    ```csharp   
    using Newtonsoft.Json.Linq;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem zařízení, kterou jste si poznamenali v **vytvoření identity zařízení** části.
   
    ```csharp   
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. Přidejte následující metodu do zprávy o stavu zpět ke cloudu prostřednictvím dvojče zařízení: 

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

7. Přidejte následující metodu k simulaci stahování firmwaru obrázku:
        
    ```csharp   
    static async Task<byte[]> simulateDownloadImage(string imageUrl)
    {
        var image = "[fake image data]";

        Console.WriteLine("Downloading image from " + imageUrl);

        await Task.Delay(4000);

        return Encoding.ASCII.GetBytes(image);
            
    }
    ```

8. Přidejte následující metodu pro simulaci použití bitové kopie firmwaru zařízení:
        
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
 
9.  Přidejte následující metodu pro simulaci čeká na stažení bitové kopie firmwaru. Aktualizovat stav na **čekání** a dalších vlastností aktualizaci firmwaru twin zrušte. Tyto vlastnosti jsou vymazány odeberete všechny existující hodnoty z předchozí firmware aktualizace. To je nezbytné, protože hlášené vlastnosti se odesílají jako operace opravy (rozdílovou) a není operace PUT (úplnou sadu vlastností který nahrazuje všechny předchozí hodnoty). Zařízení jsou obvykle informována o dostupné aktualizaci a správcem definovaná zásada způsobí, že začnou stahovat a aplikovat aktualizaci firmwaru. V této funkci by měla běžet logika, která povoluje tuto zásadu. 
        
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

10. Přidejte následující metodu, který má stažení provést. Aktualizuje na stavu **stahování** prostřednictvím dvojče zařízení volá metodu Simulovat stahování a hlásí stav **downloadComplete** nebo **downloadFailed** prostřednictvím twin v závislosti na výsledku operace stažení. 
        
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

11. Přidejte následující metodu aplikuje bitovou kopii. Aktualizuje na stavu **použití** prostřednictvím dvojče zařízení volání simulovat použít bitovou kopii metoda a stav aktualizace na **applyComplete** nebo **applyFailed** prostřednictvím Twin v závislosti na výsledcích operaci použít. 
        
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

12. Přidejte následující metodu do pořadí operace aktualizace firmwaru z čekání na stáhnout bitovou kopii prostřednictvím použitím bitové kopie do zařízení:
        
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

13. Přidejte následující metodu pro zpracování **updateFirmware** přímá metoda z cloudu. Extrahuje adresu URL k aktualizaci firmwaru z datovou část zprávy a předává jej do **doUpdate** úkol, který je spouštěn na jiné vlákno fondu. Potom okamžitě vrátí odpověď metody do cloudu.
        
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
> Tato metoda se aktivuje simulované aktualizaci, kterou chcete spustit jako **úloh** a pak se okamžitě reaguje na volání metody informuje o službu spuštění aktualizace firmwaru. Stav aktualizace a dokončení odešle do služby je hlášen vlastnostech dvojče zařízení. Odpovíte k volání metody, které při zahájení aktualizace, nikoli po jeho dokončení, protože:
> * Proces skutečné aktualizace je velmi pravděpodobné, trvá déle, než je časový limit volání metody.
> * Je velmi pravděpodobně vyžadovat restartování počítače, který by opětovném spuštění této aplikace provádění procesu skutečné aktualizace **MethodRequest** objektu není k dispozici. (Aktualizace hlášené vlastností, je však možné i po restartování systému.) 

14. Nakonec přidejte následující kód, který **hlavní** metoda k otevření připojení do služby IoT hub a inicializovat naslouchací proces metoda:
   
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
1. Spuštění aplikace .NET zařízení **SimulatedDeviceFWUpdate**.  Klikněte pravým tlačítkem myši **SimulatedDeviceFWUpdate** projekt, vyberte **ladění**a potom vyberte **spustit novou instanci**. By se měl spustit přijímá metoda volání ze služby IoT Hub: 

2. Jakmile je zařízení připojené a čekání volání metod, spustit rozhraní .NET **TriggerFWUpdate** aplikace k vyvolání metody aktualizace firmwaru v aplikaci simulovaného zařízení. Klikněte pravým tlačítkem myši **TriggerFWUpdate** projekt, vyberte **ladění**a potom vyberte **spustit novou instanci**. Měli byste vidět aktualizací napsaný v pořadí **SimulatedDeviceFWUpdate** konzoly a také pořadí nahlášené prostřednictvím hlášené vlastnosti zařízení v **TriggerFWUpdate** konzoly. Všimněte si, že tento proces trvat několik sekund. 
   
    ![Aplikace služby a zařízení spustit][img-combinedrun]


## <a name="next-steps"></a>Další postup
V tomto kurzu přímá metoda používá k aktivaci aktualizace vzdálené firmwaru v zařízení a umožňuje sledovat průběh aktualizace firmwaru hlášené vlastnosti.

Zjistěte, jak rozšířit vaše IoT řešení a plán metoda volá na několika zařízeních, najdete v článku [plán a všesměrového vysílání úlohy] [ lnk-tutorial-jobs] kurzu.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-firmware-update/servicesdknuget.png
[img-clientnuget]: media/iot-hub-csharp-csharp-firmware-update/clientsdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-firmware-update/createserviceapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-firmware-update/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-firmware-update/combinedrun.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-csharp-csharp-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-csharp-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
