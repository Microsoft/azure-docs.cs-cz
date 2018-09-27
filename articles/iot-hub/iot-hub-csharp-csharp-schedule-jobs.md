---
title: Plánování úloh s Azure IoT Hub (.NET/.NET) | Dokumentace Microsoftu
description: Jak naplánovat úlohu služby Azure IoT Hub k vyvolání přímé metody v různá zařízení. Implementace aplikace simulovaného zařízení a app service na spuštění úlohy pomocí zařízení Azure IoT SDK pro .NET.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/06/2018
ms.author: dobett
ms.openlocfilehash: 0e0c398311c5826f8b6c72bf0f39ad2bfde306b2
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223039"
---
# <a name="schedule-and-broadcast-jobs-netnet"></a>Úlohy vysílání a plánování (.NET/.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub použijte k plánování a sledování úloh, které aktualizují miliony zařízení. Použití úloh na:

* Aktualizace požadovaných vlastností
* Aktualizace značky
* Vyvolání přímých metod

Úloha zabalí jednu z těchto akcí a sleduje spuštění proti skupiny zařízení, který je definován v dotazu dvojčete zařízení. Například back endové aplikace úlohu můžete použít k vyvolání přímé metody v 10 000 zařízení, která restartuje zařízení. Určení sady zařízení, s dotazy dvojčete zařízení a naplánovat úlohu pro spuštění někdy v budoucnu. Sleduje průběh úlohy jako každé ze zařízení přijímat a provést restartování přímé metody.

Další informace o každé z těchto možností najdete v tématu:

* Dvojče zařízení a vlastností: [Začínáme s dvojčaty zařízení](iot-hub-csharp-csharp-twin-getstarted.md) a [kurz: jak používat vlastnosti dvojčat zařízení](tutorial-device-twins.md)

* Přímé metody: [Příručka pro vývojáře IoT Hub - přímých metod](iot-hub-devguide-direct-methods.md) a [kurz: použití přímých metod](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvoření aplikace pro zařízení, která implementuje přímé metody volá **LockDoor** , který je možné vyvolat v back endové aplikace.

* Vytvoření back endové aplikace, která vytvoří úlohu k volání **LockDoor** přímá metoda na více zařízeních. Jiná úloha odešle aktualizace požadované vlastnosti na více zařízeních.

Na konci tohoto kurzu budete mít dvě konzolové aplikace .NET (C#):

**SimulateDeviceMethods** , která se připojuje k vaší službě IoT hub a implementuje **LockDoor** přímá metoda.

**ScheduleJob** úlohy, která používá k volání **LockDoor** přímá metoda a aktualizovat požadované vlastnosti dvojčete zařízení na různých zařízeních.

Pro absolvování tohoto kurzu potřebujete:

* Sada Visual Studio 2017.
* Aktivní účet Azure. Pokud účet nemáte, můžete vytvořit [bezplatný účet](http://azure.microsoft.com/pricing/free-trial/) během několika minut.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části vytvoříte konzolovou aplikaci .NET, která bude reagovat přímou metodu volanou řešení zpět end.

1. V sadě Visual Studio přidejte k stávajícímu řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace**. Pojmenujte projekt **SimulateDeviceMethods**.
   
    ![Nový Visual C# Windows klasické aplikace pro zařízení](./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png)
    
2. V Průzkumníku řešení klikněte pravým tlačítkem myši **SimulateDeviceMethods** projektu a pak klikněte na tlačítko **spravovat balíčky NuGet...** .

3. V **Správce balíčků NuGet** okně **Procházet** a vyhledejte **Microsoft.Azure.Devices.Client**. Vyberte **nainstalovat** k instalaci **Microsoft.Azure.Devices.Client** balíček a přijměte podmínky použití. Tento postup stáhne, nainstaluje a přidá odkaz na [zařízení Azure IoT SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet balíček a jeho závislosti.
   
    ![Klientská aplikace okno Správce balíčků NuGet](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

4. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem zařízení, který jste si poznamenali v předchozí části:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

6. Přidejte následující k implementaci přímé metody v zařízení:

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);
            
        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```

7. Přidejte následující k implementaci naslouchací proces dvojčata zařízení na zařízení:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

8. Nakonec přidejte následující kód, který **hlavní** metody k otevření připojení do služby IoT hub a inicializovat naslouchací proces – metoda:
   
    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
          TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
9. Uložte si práci a sestavení vašeho řešení.         

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu by měly implementovat zásady opakování (například připojení opakování), jak je navrženo v článku [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).
> 

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Plánování úloh pro volání přímé metody a odesílání, aktualizace dvojčete zařízení

V této části vytvoříte konzolovou aplikaci .NET (s použitím jazyka C#), který se používá k volání úlohy **LockDoor** přímá metoda a odesílat aktualizace požadované vlastnosti na více zařízeních.

1. V sadě Visual Studio přidejte k stávajícímu řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace**. Pojmenujte projekt **ScheduleJob**.

    ![Nový klasický desktopový projekt Visual C# pro systém Windows](./media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png)

2. V Průzkumníku řešení klikněte pravým tlačítkem myši **ScheduleJob** projektu a pak klikněte na tlačítko **spravovat balíčky NuGet...** .

3. V **Správce balíčků NuGet** okně **Procházet**, vyhledejte **Microsoft.Azure.Devices**vyberte **nainstalovat** k instalaci **Microsoft.Azure.Devices** balíček a přijměte podmínky použití. Tento krok stáhne, nainstaluje a přidá odkaz na [pro službu Azure IoT SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet balíček a jeho závislosti.

    ![Okno Správce balíčků NuGet](./media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png)

4. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Přidejte následující `using` příkazu, pokud tam ještě nejsou ve výchozím nastavení příkazy.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

6. Do třídy **Program** přidejte následující pole. Nahraďte zástupné symboly připojovacím řetězcem služby IoT Hub pro rozbočovač, kterou jste vytvořili v předchozí části a název vašeho zařízení.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

7. Přidejte následující metodu do třídy **Program**:

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && 
          (result.Status != JobStatus.Failed));
    }
    ```

8. Přidejte následující metodu do třídy **Program**:

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = 
          new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), 
          TimeSpan.FromSeconds(5));
       
        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

9. Přidat další metodu **Program** třídy:

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > Další informace o syntaxi dotazů najdete v tématu [dotazovací jazyk služby IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language).
    > 

10. Nakonec do metody **Main** přidejte následující řádky:

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

11. Uložte si práci a sestavení vašeho řešení. 

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni aplikaci spustit.

1. V Průzkumníku řešení Visual Studio, klikněte pravým tlačítkem na řešení a potom klikněte na tlačítko **sestavení**. **Více projektů po spuštění**. Ujistěte se, že `SimulateDeviceMethods` je v horní části seznamu, za nímž následuje `ScheduleJob`. Nastavení obě své akce **Start** a klikněte na tlačítko **OK**.

2. Spusťte projekty kliknutím **Start** nebo přejděte na **ladění** nabídky a klikněte na tlačítko **spustit ladění**.

3. Zobrazí výstup ze zařízení a back endové aplikace.

    ![Spouštění aplikací k plánování úloh](./media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu používají úlohy k plánování přímé metody, která zařízení a aktualizace vlastnosti dvojčete zařízení.

Chcete-li pokračovat v seznamování se službou IoT Hub a schémata správy zařízení jako vzdálené přes aktualizace firmwaru air, přečtěte si [kurz: jak provést upgrade firmwaru](tutorial-firmware-update.md).

Další informace o nasazování AI do hraničních zařízení pomocí služby Azure IoT Edge najdete v tématu [Začínáme s IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).