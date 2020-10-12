---
title: Začínáme se správou zařízení Azure IoT Hub (.NET/.NET) | Microsoft Docs
description: Jak používat správu zařízení v Azure IoT Hub k zahájení restartování vzdáleného zařízení. Pomocí sady SDK pro zařízení Azure IoT pro .NET implementujete aplikaci simulovaného zařízení, která zahrnuje přímou metodu a sadu SDK služby Azure IoT pro .NET k implementaci aplikace služby, která vyvolá přímou metodu.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 5760c574e64a3b3b4a1df12092cff44374790a90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89018395"
---
# <a name="get-started-with-device-management-net"></a>Začínáme se správou zařízení (.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

V tomto kurzu získáte informace o následujících postupech:

* Použijte Azure Portal k vytvoření centra IoT a vytvoření identity zařízení ve službě IoT Hub.

* Vytvořte aplikaci simulovaného zařízení, která obsahuje přímou metodu, která toto zařízení restartuje. Přímé metody jsou vyvolány z cloudu.

* Vytvořte konzolovou aplikaci .NET, která v aplikaci simulovaného zařízení pomocí služby IoT Hub zavolá přímou metodu restart.

Na konci tohoto kurzu budete mít dvě konzolové aplikace .NET:

* **SimulateManagedDevice**. Tato aplikace se připojuje ke službě IoT Hub s dříve vytvořenou identitou zařízení, přijímá přímou metodu restartování, simuluje fyzické restartování a oznamuje čas posledního restartování.

* **TriggerReboot**. Tato aplikace volá přímou metodu v aplikaci simulovaného zařízení, zobrazí odpověď a zobrazí aktualizované hlášené vlastnosti.

## <a name="prerequisites"></a>Požadavky

* Visual Studio

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT (neboli IoT Hubu)

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Aktivace vzdáleného restartování zařízení pomocí přímé metody

V této části vytvoříte konzolovou aplikaci .NET pomocí jazyka C#, která inicializuje vzdálené restartování na zařízení pomocí přímé metody. Aplikace pomocí dvojitých dotazů na zařízení zjistí čas posledního restartování tohoto zařízení.

1. V aplikaci Visual Studio vyberte možnost **vytvořit nový projekt**.

1. V části **vytvořit nový projekt**vyhledejte a vyberte šablonu projektu **aplikace konzoly (.NET Framework)** a pak vyberte **Další**.

1. V části **Konfigurace nového projektu**zadejte název projektu *TriggerReboot*a vyberte .NET Framework verze 4.5.1 nebo novější. Vyberte **Vytvořit**.

    ![Nový klasický desktopový projekt Visual C# pro systém Windows](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. V **Průzkumník řešení**klikněte pravým tlačítkem na projekt **TriggerReboot** a pak vyberte **Spravovat balíčky NuGet**.

1. Vyberte **Procházet**, vyhledejte a vyberte **Microsoft. Azure. Devices**. Vyberte **nainstalovat** a nainstalujte balíček **Microsoft. Azure. Devices** .

    ![Okno Správce balíčků NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   Tento krok stáhne a nainstaluje balíček NuGet [sady SDK služby Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) a jeho závislosti a přidá odkaz na něj.

1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. Do třídy **Program** přidejte následující pole. Nahraďte `{iot hub connection string}` hodnotu zástupného symbolu připojovacím řetězcem IoT Hub, který jste dříve zkopírovali v [části získání připojovacího řetězce centra IoT Hub](#get-the-iot-hub-connection-string).

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. Do třídy **Program** přidejte následující metodu.  Tento kód získá pro restartování zařízení dvojitou dobu a vypíše hlášené vlastnosti.

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. Do třídy **Program** přidejte následující metodu.  Tento kód inicializuje restart na zařízení pomocí přímé metody.

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

1. Nakonec přidejte do metody **Main** následující řádky:

   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

1. Vyberte **Build**  >  **řešení**sestavení sestavení.

> [!NOTE]
> Tento kurz provede pouze jeden dotaz pro hlášené vlastnosti zařízení. V produkčním kódu doporučujeme dotazování na detekci změn v hlášených vlastnostech.

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části:

* Vytvořte konzolovou aplikaci .NET, která reaguje na přímou metodu volanou cloudem.

* Aktivujte simulované restartování zařízení.

* Pomocí hlášených vlastností můžete povolit, aby se v zařízeních s dvojitými dotazy identifikovala zařízení a při jejich posledním restartování.

K vytvoření aplikace simulovaného zařízení použijte následující postup:

1. V aplikaci Visual Studio v řešení TriggerReboot, které jste již vytvořili, vyberte **soubor**  >  **Nový**  >  **projekt**. V části **vytvořit nový projekt**vyhledejte a vyberte šablonu projektu **aplikace konzoly (.NET Framework)** a pak vyberte **Další**.

1. V části **Konfigurovat nový projekt**, pojmenujte projekt *SimulateManagedDevice*a pro **řešení**vyberte možnost **Přidat do řešení**. Vyberte **Vytvořit**.

    ![Název a přidání projektu do řešení](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. V Průzkumník řešení klikněte pravým tlačítkem na nový projekt **SimulateManagedDevice** a pak vyberte **Spravovat balíčky NuGet**.

1. Vyberte **Procházet**, vyhledejte a vyberte **Microsoft. Azure. Devices. Client**. Vyberte **Nainstalovat**.

    ![Klientská aplikace okna Správce balíčků NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   Tento krok stáhne a nainstaluje balíček NuGet [sady SDK pro zařízení Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) a jeho závislosti a přidá se na něj odkaz.

1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Do třídy **Program** přidejte následující pole. Nahraďte `{device connection string}` hodnotu zástupného symbolu připojovacím řetězcem zařízení, který jste si dříve poznamenali v [části registrace nového zařízení ve službě IoT Hub](#register-a-new-device-in-the-iot-hub).

    ```csharp
    static string DeviceConnectionString = "{device connection string}";
    static DeviceClient Client = null;
    ```

1. Přidejte následující pro implementaci přímé metody na zařízení:

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

       string result = @"{""result"":""Reboot started.""}";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

1. Nakonec do metody **Main** přidejte následující kód, který otevře připojení ke službě IoT Hub a inicializuje naslouchací proces metody:

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

1. V Průzkumník řešení klikněte pravým tlačítkem na své řešení a pak vyberte **nastavit projekty po spuštění**.

1. Pro **běžné vlastnosti**  >  **spouštěný projekt**vyberte **jeden spouštěný projekt**a pak vyberte projekt **SimulateManagedDevice** . Výběrem **OK** uložte změny.

1. Vyberte **Build**  >  **řešení**sestavení sestavení.

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování (například exponenciální omezení rychlosti), jak je navrženo při [zpracování přechodné chyby](/azure/architecture/best-practices/transient-faults).

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni spustit aplikace.

1. Pokud chcete spustit aplikaci zařízení .NET **SimulateManagedDevice**, klikněte v Průzkumník řešení pravým tlačítkem myši na projekt **SimulateManagedDevice** , vyberte **ladit**a pak vyberte **spustit novou instanci**. Aplikace by měla začít naslouchat voláním metod ze služby IoT Hub.

1. Po tom, co je zařízení připojené a čeká se na vyvolání metod, klikněte pravým tlačítkem na projekt **TriggerReboot** , vyberte **ladit**a pak vyberte **spustit novou instanci**.

   Měla by se zobrazit zpráva "restartování!". napsané v konzole **SimulatedManagedDevice** a v hlášených vlastnostech zařízení, což zahrnuje čas posledního restartování, který je zapsaný v konzole **TriggerReboot** .

    ![Spuštění aplikace služby a zařízení](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
