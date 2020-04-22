---
title: Začínáme s identitou identity modulu Azure IoT Hub & dvojče modulu (.NET)
description: Zjistěte, jak vytvořit identitu modulu a aktualizovat dvojče modulu pomocí sad IoT SDK pro .NET.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: menchi
ms.custom: amqp
ms.openlocfilehash: 919d1e37e6066c78e83d58be4fe4667ec67e45ad
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733389"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>Začínáme s identitou modulu IoT Hub a dvojčetem modulu (.NET)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Identity modulů a dvojčata modulů](iot-hub-devguide-module-twins.md) se podobají identitě zařízení a dvojčeti zařízení služby Azure IoT Hub, ale poskytují větší úroveň členitosti. Zatímco identita zařízení Azure IoT Hub a dvojče zařízení umožňují back-endové aplikaci konfigurovat zařízení a poskytovat viditelnost podmínek zařízení, identita modulu a dvojče modulu poskytují tyto funkce pro jednotlivé součásti zařízení. Na schopných zařízeních s více komponentami, jako jsou zařízení založená na operačním systému nebo zařízení firmwaru, umožňují identity modulů a dvojčata modulů izolovanou konfiguraci a podmínky pro každou komponentu.

Na konci tohoto kurzu budete mít dvě konzolové aplikace .NET:

* **CreateIdentities**. Tato aplikace vytvoří identitu zařízení, identitu modulu a přidružený bezpečnostní klíč pro připojení klientů zařízení a modulů.

* **UpdateModuleTwinReportedProperties**. Tato aplikace odesílá aktualizované dvojče modulu hlášené vlastnosti do služby IoT hub.

> [!NOTE]
> Informace o sadách SDK služby Azure IoT Hub, s jejichž pomocí můžete sestavit aplikace, které poběží v zařízení, i back-end vašeho řešení, najdete v tématu [Sady SDK služby IoT Hub](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Požadavky

* Visual Studio.

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-hub"></a>Vytvoření rozbočovače

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Aktualizace dvojčete modulu pomocí sady SDK pro zařízení .NET

V této části vytvoříte na simulovaném zařízení konzolovou aplikaci .NET, která aktualizuje hlášené vlastnosti dvojčete modulu.

Než začnete, získejte připojovací řetězec modulu. Přihlaste se k webu [Azure Portal](https://portal.azure.com/). Přejděte do svého centra a vyberte **zařízení IoT**. Najít **myFirstDevice**. Vyberte **myFirstDevice** otevřít a pak vyberte **myFirstModule** otevřít. V **části Podrobnosti identity modulu**zkopírujte **připojovací řetězec (primární klíč)** v případě potřeby v následujícím postupu.

   ![Podrobnosti o modulu na webu Azure Portal](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. V sadě Visual Studio přidejte do řešení nový projekt výběrem **možnosti Nový** > **New** > **projekt souboru**. V okně Vytvořit nový projekt vyberte **Console App (.NET Framework)** a vyberte **Další**.

1. Pojmenujte projekt *UpdateModuleTwinReportedProperties*. V **řešení**vyberte **Přidat do řešení**. Ujistěte se, že máte .NET Framework verze 4.6.1 nebo novější.

    ![Vytvoření projektu ve Visual Studiu](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. Chcete-li vytvořit projekt, vyberte **vytvořit.**

1. V sadě Visual Studio otevřete **nástroje, který** > **spravuje balíčky** > **NuGet pro řešení**. Vyberte kartu **Procházet**.

1. Vyhledejte a vyberte **Microsoft.Azure.Devices.Client**a pak vyberte **Instalovat**.

    ![Instalace aktuální verze sady Azure IoT Hub .NET service SDK](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

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

1. Do **hlavní** metody přidejte následující řádky:

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

1. Volitelně můžete přidat tyto **příkazy** main metoda odeslat událost do služby IoT Hub z modulu. Umístěte tyto řádky `try catch` pod blok.

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní můžete spouštět aplikace.

1. V sadě Visual Studio klikněte v **Průzkumníku řešení**pravým tlačítkem myši na vaše řešení a potom vyberte **nastavit projekty spuštění**.

1. V části **Společné vlastnosti**vyberte **Projekt po spuštění.**

1. Vyberte **Více projektů při spuštění**a pak vyberte **Spustit** jako akci pro aplikace a **OK,** abyste přijali změny.

1. Stisknutím **klávesy F5** spusťte aplikace.

## <a name="next-steps"></a>Další kroky

Chcete-li pokračovat v seznamování se službou IoT Hub a prozkoumat další scénáře IoT, podívejte se na tato témata:

* [Začínáme se správou zařízení](iot-hub-node-node-device-management-get-started.md)

* [Začínáme se službou IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
