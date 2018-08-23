---
title: Začínáme s identitou modulu a dvojčetem modulu služby Azure IoT Hub (portál a .NET) | Microsoft Docs
description: Zjistěte, jak vytvořit identitu modulu a aktualizovat dvojče modulu pomocí portálu a .NET.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: dobett
ms.openlocfilehash: 3de08d9e4a45b842fc921436f855831afb6b9ce0
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2018
ms.locfileid: "42058646"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Začínáme s identitou modulu a dvojčetem modulu služby IoT Hub pomocí portálu a zařízení .NET

> [!NOTE]
> [Identity modulů a dvojčata modulů](iot-hub-devguide-module-twins.md) se podobají identitě zařízení a dvojčeti zařízení služby Azure IoT Hub, ale poskytují větší úroveň členitosti. Zatímco identita zařízení a dvojče zařízení služby Azure IoT Hub umožňují back-endové aplikaci konfigurovat zařízení a poskytují vhled do stavu zařízení, identita modulu a dvojče modulu poskytují tyto možnosti pro jednotlivé součásti zařízení. Na způsobilých zařízeních s několika součástmi, jako jsou zařízení s operačním systémem nebo zařízení s firmwarem, to umožňuje izolovanou konfiguraci a vhled do stavu jednotlivých součástí.

V tomto kurzu se dozvíte: 
1. Jak vytvořit identitu modulu na portálu. 
2. Jak pomocí sady SDK pro zařízení .NET aktualizovat dvojče zařízení z vašeho zařízení.

> [!NOTE]
> Informace o sadách SDK služby Azure IoT Hub, s jejichž pomocí můžete sestavit aplikace, které poběží v zařízení, i back-end vašeho řešení, najdete v tématu [Sady SDK služby IoT Hub][lnk-hub-sdks].

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio 2015 nebo Visual Studio 2017.
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

## <a name="create-a-device-identity-in-the-portal"></a>Vytvoření identity zařízení na portálu

Teď máte službu IoT Hub. Otevřete [portál](https://portal.azure.com) a přejděte do vaší služby IoT Hub. Klikněte na Zařízení IoT a pak kliknutím na Přidat vytvořte identitu zařízení. Pojmenujte ji **MyFirstDevice**. 

![Vytvoření identity zařízení][8]

Po uložení se v seznamu identit zařízení zobrazí úspěšně vytvořená identita MyFirstDevice.

![Vytvoření id zařízení][11]

Teď klikněte na příslušný řádek. Zobrazí se podrobnosti o zařízení.

![Detaily zařízení][10]

## <a name="create-a-module-identity-in-the-portal"></a>Vytvoření identity modulu na portálu

V rámci jedné identity zařízení můžete vytvořit až 20 identit modulů. Kliknutím na tlačítko **Přidat identitu modulu** v horní části vytvořte první identitu modulu s názvem **myFirstModule**. 

![Detaily zařízení][9]

Uložte právě vytvořenou identitu modulu a klikněte na ni. Zobrazí se podrobnosti o identitě modulu. Uložte připojovací řetězec – primární klíč. Použijete ho v další části, kde nastavíte svůj modul na zařízení.

![Detaily zařízení][12]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Aktualizace dvojčete modulu pomocí sady SDK pro zařízení .NET

Úspěšně jste vytvořili identitu modulu ve službě IoT Hub. Teď zkuste navázat komunikaci mezi simulovaným zařízením a cloudem. Po vytvoření identity modulu se ve službě IoT Hub implicitně vytvoří dvojče modulu. V této části vytvoříte na simulovaném zařízení konzolovou aplikaci .NET, která aktualizuje hlášené vlastnosti dvojčete modulu.

1. **Vytvoření projektu v sadě Visual Studio** – V sadě Visual Studio přidejte ke stávajícímu řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace (.NET Framework)**. Ujistěte se, že máte .NET Framework verze 4.6.1 nebo novější. Pojmenujte projekt **UpdateModuleTwinReportedProperties**.

    ![Vytvoření projektu v sadě Visual Studio][13]

2. **Nainstalujte nejnovější zařízení Azure IoT Hub .NET SDK** -dvojče zařízení identit a modul je ve verzi public preview. Jsou k dispozici pouze v předběžných verzích sad SDK pro zařízení IoT Hub. V sadě Visual Studio otevřete Nástroje > Správce balíčků NuGet > Spravovat balíčky NuGet pro řešení. Vyhledejte Microsoft.Azure.Devices.Client. Nezapomeňte zaškrtnout políčko Zahrnout předběžnou verzi. Vyberte nejnovější verzi a instalaci. Teď máte přístup ke všem funkcím modulu. 

    ![Instalace sady SDK služby Azure IoT Hub pro .NET verze V1.16.0-preview-005][14]

3. **Získání připojovacího řetězce modulu** – Teď se přihlaste k webu [Azure Portal][lnk-portal]. Přejděte do vaší služby IoT Hub a klikněte na Zařízení IoT. Vyhledejte identitu zařízení myFirstDevice a otevřete ji. Zobrazí se úspěšně vytvořená identita modulu myFirstModule. Zkopírujte připojovací řetězec modulu. Budete ho potřebovat v dalším kroku.

    ![Podrobnosti o modulu na webu Azure Portal][15]

4. **Vytvoření konzolové aplikace UpdateModuleTwinReportedProperties** – Na začátek souboru **Program.cs** přidejte následující příkazy `using`:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

    Do třídy **Program** přidejte následující pole. Nahraďte zástupnou hodnotu připojovacím řetězcem modulu.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    ```

    Do třídy **Program** přidejte následující metodu **OnDesiredPropertyChanged**:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
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

    Nakonec do metody **Main** přidejte následující řádky:

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin));

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
        Console.ReadKey();
        Client.CloseAsync().Wait();
    }
    
    private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
    {
        Console.WriteLine($"Status {status} changed: {reason}");
    }
    ```

    Tento vzorový kód ukazuje, jak načíst dvojče modulu a aktualizovat hlášené vlastnosti pomocí protokolu AMQP. Ve verzi Public Preview podporujeme operace s dvojčetem modulu pouze pomocí protokolu AMQP.

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni aplikaci spustit. V sadě Visual Studio v Průzkumníku řešení klikněte pravým tlačítkem na řešení a potom klikněte na tlačítko **Nastavit projekty po spuštění**. Vyberte **Více projektů po spuštění** a pak jako akci pro konzolovou aplikaci vyberte **Spustit**. A pak stisknutím klávesy F5 spusťte obě aplikace. 

## <a name="next-steps"></a>Další postup

Chcete-li pokračovat v seznamování se službou IoT Hub a prozkoumat další scénáře IoT, podívejte se na tato témata:

* [Začínáme s IoT Hub identit a modul dvojče zařízení pomocí zálohování .NET a .NET zařízení][lnk-csharp-csharp-getstarted]
* [Začínáme se službou IoT Edge][lnk-iot-edge]


<!-- Images. -->
[8]:./media\iot-hub-portal-csharp-module-twin-getstarted/create-device-id.JPG
[9]:./media\iot-hub-portal-csharp-module-twin-getstarted/create-module-id.JPG
[10]:./media\iot-hub-portal-csharp-module-twin-getstarted/device-details.JPG
[11]:./media\iot-hub-portal-csharp-module-twin-getstarted/device-id-created.JPG
[12]:./media\iot-hub-portal-csharp-module-twin-getstarted/module-details.JPG
[13]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.JPG
[14]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png
[15]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-csharp-csharp-getstarted]: iot-hub-csharp-csharp-module-twin-getstarted.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
