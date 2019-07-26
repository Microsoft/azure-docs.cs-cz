---
title: Začínáme se správou zařízení Azure IoT Hub (.NET/.NET) | Microsoft Docs
description: Jak používat správu zařízení v Azure IoT Hub k zahájení restartování vzdáleného zařízení. Pomocí sady SDK pro zařízení Azure IoT pro .NET implementujete aplikaci simulovaného zařízení, která zahrnuje přímou metodu a sadu SDK služby Azure IoT pro .NET k implementaci aplikace služby, která vyvolá přímou metodu.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: robinsh
ms.openlocfilehash: 40db247dba1d55b5121f95a4d69ca853f3d7ee56
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404574"
---
# <a name="get-started-with-device-management-netnet"></a>Začínáme se správou zařízení (.NET/.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

V tomto kurzu získáte informace o následujících postupech:

* Pomocí Azure Portal můžete vytvořit IoT Hub a vytvořit identitu zařízení ve službě IoT Hub.

* Vytvořte aplikaci simulovaného zařízení, která obsahuje přímou metodu, která toto zařízení restartuje. Přímé metody jsou vyvolány z cloudu.

* Vytvořte konzolovou aplikaci .NET, která v aplikaci simulovaného zařízení pomocí služby IoT Hub zavolá přímou metodu restart.

Na konci tohoto kurzu budete mít dvě konzolové aplikace .NET:

* **SimulateManagedDevice**, která se připojí ke službě IoT Hub s dříve vytvořenou identitou zařízení, obdrží přímou metodu restart, simuluje fyzické restartování a oznamuje čas posledního restartování.

* **TriggerReboot**, která volá přímou metodu v aplikaci simulovaného zařízení, zobrazuje odpověď a zobrazuje aktualizované hlášené vlastnosti.

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio.

* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) .)

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Aktivace vzdáleného restartování zařízení pomocí přímé metody

V této části vytvoříte konzolovou aplikaci .NET (pomocí C#), která inicializuje vzdálené restartování zařízení pomocí přímé metody. Aplikace pomocí dvojitých dotazů na zařízení zjistí čas posledního restartování tohoto zařízení.

1. V sadě Visual Studio přidejte k novému řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace (.NET Framework)** . Zkontrolujte, zda máte verzi rozhraní .NET Framework 4.5.1 nebo novější. Pojmenujte projekt **TriggerReboot**.

    ![Nový klasický desktopový projekt Visual C# pro systém Windows](./media/iot-hub-csharp-csharp-device-management-get-started/createserviceapp.png)

2. V Průzkumník řešení klikněte pravým tlačítkem na projekt **TriggerReboot** a pak klikněte na **Spravovat balíčky NuGet**.

3. V okně **Správce balíčků NuGet** vyberte **Procházet**, vyhledejte **Microsoft. Azure. Devices**, vyberte **instalovat** a nainstalujte balíček **Microsoft. Azure. Devices** a přijměte podmínky použití. Tímto postupem se stáhne a nainstaluje balíček NuGet [sady SDK služby Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) a jeho závislosti a přidá se na něj odkaz.

    ![Okno Správce balíčků NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/servicesdknuget.png)

4. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:
   
   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```
        
5. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem IoT Hub, který jste dříve zkopírovali v [části získání připojovacího řetězce centra IoT Hub](#get-the-iot-hub-connection-string).
   
   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

6. Do třídy **Program** přidejte následující metodu.  Tento kód získá pro restartování zařízení dvojitou dobu a vypíše hlášené vlastnosti.
   
   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```
        
7. Do třídy **Program** přidejte následující metodu.  Tento kód inicializuje restart na zařízení pomocí přímé metody.

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

7. Nakonec do metody **Main** přidejte následující řádky:
   
   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

8. Sestavte řešení.

> [!NOTE]
> Tento kurz provede pouze jeden dotaz pro hlášené vlastnosti zařízení. V produkčním kódu doporučujeme dotazování na detekci změn v hlášených vlastnostech.

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části provedete následující operace:

* Vytvořte konzolovou aplikaci .NET, která reaguje na přímou metodu volanou cloudem.

* Aktivujte simulované restartování zařízení.

* Pomocí hlášených vlastností můžete povolit, aby se v zařízeních s dvojitými dotazy identifikovala zařízení a při jejich posledním restartování.

1. V sadě Visual Studio přidejte k stávajícímu řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace**. Pojmenujte projekt **SimulateManagedDevice**.
   
    ![Nová aplikace C# Visual v klasickém zařízení s Windows](./media/iot-hub-csharp-csharp-device-management-get-started/createdeviceapp.png)
    
2. V Průzkumník řešení klikněte pravým tlačítkem na projekt **SimulateManagedDevice** a potom klikněte na **Spravovat balíčky NuGet...** .

3. V okně **Správce balíčků NuGet** vyberte **Procházet** a vyhledejte **Microsoft. Azure. Devices. Client**. Vyberte **instalovat** a nainstalujte balíček **Microsoft. Azure. Devices. Client** a přijměte podmínky použití. Tímto postupem se stáhne a nainstaluje balíček NuGet [sady SDK pro zařízení Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) a jeho závislosti a přidá se na něj odkaz.
   
    ![Klientská aplikace okna Správce balíčků NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/clientsdknuget.png)
    
4. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem zařízení, který jste si poznamenali v předchozí části.

    ```csharp
    static string DeviceConnectionString = 
      "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```
6. Přidejte následující pro implementaci přímé metody na zařízení:

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
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
   ```

7. Nakonec do metody **Main** přidejte následující kód, který otevře připojení ke službě IoT Hub a inicializuje naslouchací proces metody:

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

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
   ```
        
8. V Průzkumníku řešení sady Visual Studio klikněte pravým tlačítkem na své řešení a pak klikněte na **Nastavit projekty po spuštění...** . Vyberte **jeden spouštěný projekt**a pak v rozevírací nabídce vyberte projekt **SimulateManagedDevice** . Sestavte řešení.       

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování (například exponenciální omezení rychlosti), jak je navrženo v článku, [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni aplikaci spustit.

1. Pro spuštění aplikace zařízení .NET **SimulateManagedDevice**. Klikněte pravým tlačítkem na projekt **SimulateManagedDevice** , vyberte **ladit**a pak vyberte **spustit novou instanci**. Mělo by začít naslouchat voláním metod ze služby IoT Hub. 

2. Když je teď zařízení připojené a čeká se na vyvolání metod, spusťte aplikaci .NET **TriggerReboot** , která vyvolá metodu restart v aplikaci simulovaného zařízení. Provedete to tak, že kliknete pravým tlačítkem na projekt **TriggerReboot** , vyberete **ladit**a pak vyberete **spustit novou instanci**. Měla by se zobrazit zpráva "restartování!". napsané v konzole **SimulatedManagedDevice** a v hlášených vlastnostech zařízení, což zahrnuje čas posledního restartování, který je zapsaný v konzole **TriggerReboot** .
   
    ![Spuštění aplikace služby a zařízení](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
