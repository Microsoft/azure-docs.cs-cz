---
title: Začínáme s modulem identity IoT Hub identit modulu & modulu (.NET)
description: Zjistěte, jak vytvořit identitu modulu a aktualizovat dvojče modulu pomocí sad IoT SDK pro .NET.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: menchi
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: daba400b9daadf464c4c125ad266745237e71367
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92142515"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>Začínáme s identitou modulu IoT Hub a modulem s dvojitou identitou (.NET)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Identity modulů a dvojčata modulů](iot-hub-devguide-module-twins.md) se podobají identitě zařízení a dvojčeti zařízení služby Azure IoT Hub, ale poskytují větší úroveň členitosti. I když se v Azure IoT Hub identita zařízení a zařízení dokončí v back-endové aplikaci pro konfiguraci zařízení a zajištění viditelnosti podmínek zařízení, identita modulu a funkční vlákna poskytují tyto možnosti pro jednotlivé součásti zařízení. V zařízeních s více součástmi, jako jsou zařízení s operačním systémem nebo zařízení firmwaru, identity modulů a vlákna modulu, umožňují pro jednotlivé komponenty izolovanou konfiguraci a podmínky.

Na konci tohoto kurzu budete mít dvě konzolové aplikace .NET:

* **CreateIdentities**. Tato aplikace vytvoří identitu zařízení, identitu modulu a přiřazený bezpečnostní klíč pro připojení klientů zařízení a modulů.

* **UpdateModuleTwinReportedProperties**. Tato aplikace pošle do služby IoT Hub aktualizované nahlášené vlastnosti nedokončeného modulu.

> [!NOTE]
> Informace o sadách SDK služby Azure IoT Hub, s jejichž pomocí můžete sestavit aplikace, které poběží v zařízení, i back-end vašeho řešení, najdete v tématu [Sady SDK služby IoT Hub](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Předpoklady

* Visual Studio

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-hub"></a>Vytvoření rozbočovače

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Aktualizace dvojčete modulu pomocí sady SDK pro zařízení .NET

V této části vytvoříte na simulovaném zařízení konzolovou aplikaci .NET, která aktualizuje hlášené vlastnosti dvojčete modulu.

Než začnete, Získejte připojovací řetězec modulu. Přihlaste se na [Azure Portal](https://portal.azure.com/). Přejděte do svého centra a vyberte **zařízení IoT**. Vyhledejte **myFirstDevice**. Vyberte **myFirstDevice** a otevřete ho tak, že vyberete **myFirstModule** . V **podrobnostech o identitě modulu** zkopírujte **připojovací řetězec (primární klíč)** , pokud je to potřeba v následujícím postupu.

   ![Podrobnosti o modulu na webu Azure Portal](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. V aplikaci Visual Studio přidejte do svého řešení nový projekt tak, že vyberete **soubor**  >  **Nový**  >  **projekt**. V v vytvořit nový projekt vyberte **Konzolová aplikace (.NET Framework)** a pak vyberte **Další**.

1. Pojmenujte projekt *UpdateModuleTwinReportedProperties*. V případě **řešení** vyberte možnost **Přidat do řešení**. Ujistěte se, že máte .NET Framework verze 4.6.1 nebo novější.

    ![Vytvoření projektu ve Visual Studiu](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. Funkci vytvoříte pomocí **Vytvořit**.

1. V aplikaci Visual Studio otevřete **nástroje**  >  **Správce balíčků NuGet**  >  **Spravovat balíčky NuGet pro řešení**. Vyberte kartu **Procházet**.

1. Vyhledejte a vyberte **Microsoft. Azure. Devices. Client** a pak vyberte **nainstalovat**.

    ![Snímek obrazovky s vybraným názvem Microsoft. Azure. Devices. Client a zvýrazněným tlačítkem nainstalovat](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

1. Do třídy **Program** přidejte následující pole. Nahraďte zástupnou hodnotu připojovacím řetězcem modulu.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

1. Do třídy **Program** přidejte následující metodu **OnDesiredPropertyChanged**:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

1. Do metody **Main** přidejte následující řádky:

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    Tento vzorový kód ukazuje, jak načíst dvojče modulu a aktualizovat hlášené vlastnosti pomocí protokolu AMQP. Ve verzi Public Preview podporujeme operace s dvojčetem modulu pouze pomocí protokolu AMQP.

1. Volitelně můžete přidat tyto příkazy do metody **Main** k odeslání události IoT Hub z vašeho modulu. Umístěte tyto řádky pod `try catch` blok.

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní můžete aplikace spouštět.

1. V sadě Visual Studio v **Průzkumník řešení** klikněte pravým tlačítkem na své řešení a pak vyberte **nastavit projekty po spuštění**.

1. V části **společné vlastnosti** vyberte možnost **projekt po spuštění.**

1. Vyberte **více projektů po spuštění** a pak vyberte **Spustit** jako akci pro aplikace a **kliknutím na OK** potvrďte provedené změny.

1. Aplikace spustíte stisknutím klávesy **F5** .

## <a name="next-steps"></a>Další kroky

Chcete-li pokračovat v seznamování se službou IoT Hub a prozkoumat další scénáře IoT, podívejte se na tato témata:

* [Začínáme se správou zařízení](iot-hub-node-node-device-management-get-started.md)

* [Začínáme se službou IoT Edge](../iot-edge/quickstart-linux.md)