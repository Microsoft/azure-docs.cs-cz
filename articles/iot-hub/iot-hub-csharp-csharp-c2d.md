---
title: Zprávy z cloudu na zařízení pomocí služby Azure IoT Hub (.NET) | Dokumenty společnosti Microsoft
description: Jak odesílat zprávy z cloudu na zařízení do zařízení z centra Azure IoT pomocí sad Azure IoT SDK pro .NET. Můžete upravit aplikaci zařízení pro příjem zpráv z cloudu na zařízení a upravit back-end aplikace pro odesílání zpráv z cloudu na zařízení.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: robinsh
ms.openlocfilehash: 7805b9b3f000b2bc2e45272ab9ff469d5711e581
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110198"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Odesílání zpráv z cloudu do zařízení pomocí služby IoT Hub (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub je plně spravovaná služba, která pomáhá umožnit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-endem řešení. Rychlý start [Odeslat telemetrii ze zařízení do](quickstart-send-telemetry-dotnet.md) služby IoT hub ukazuje, jak vytvořit centrum IoT, zřídit v něm identitu zařízení a kódovat aplikaci zařízení, která odesílá zprávy mezi zařízeními.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tento kurz staví na [odesílání telemetrie ze zařízení do služby IoT hub](quickstart-send-telemetry-dotnet.md). Ukazuje, jak provést následující úkoly:

* Z back-endu vašeho řešení můžete odesílat zprávy z cloudu na zařízení do jednoho zařízení prostřednictvím služby IoT Hub.

* Příjem zpráv z cloudu na zařízení na zařízení.

* Z back-endu vašeho řešení požádejte o potvrzení doručení *(zpětnou vazbu)* pro zprávy odeslané do zařízení ze služby IoT Hub.

Další informace o zprávách mezi cloudy na jdete v [D2C a C2D Messaging s IoT Hub](iot-hub-devguide-messaging.md).

Na konci tohoto kurzu spustíte dvě konzolové aplikace .NET.

* **SimulatedDevice**. Tato aplikace se připojuje k centru IoT hub a přijímá zprávy z cloudu na zařízení. Tato aplikace je upravená verze aplikace vytvořené v [send telemetrie ze zařízení do služby IoT hub](quickstart-send-telemetry-dotnet.md).

* **SendCloudToDevice**. Tato aplikace odešle zprávu z cloudu na zařízení do aplikace zařízení prostřednictvím služby IoT Hub a pak obdrží potvrzení o doručení.

> [!NOTE]
> IoT Hub má podporu sady SDK pro mnoho platforem a jazyků zařízení, včetně C, Java, Pythonu a Javascriptu, prostřednictvím [sad SDK zařízení Azure IoT](iot-hub-devguide-sdks.md). Podrobné pokyny, jak připojit zařízení ke kódu tohoto kurzu a obecně k azure iot hubu, najdete v [průvodci vývojářem služby IoT Hub](iot-hub-devguide.md).
>

## <a name="prerequisites"></a>Požadavky

* Visual Studio

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-device-app"></a>Příjem zpráv v aplikaci pro zařízení

V této části upravte aplikaci zařízení, kterou jste vytvořili v [aplikaci Send telemetrie ze zařízení do centra IoT,](quickstart-send-telemetry-dotnet.md) abyste přijímali zprávy z cloudu na zařízení z centra IoT.

1. V sadě Visual Studio v projektu **SimulatedDevice** přidejte do třídy **Program** následující metodu.

   ```csharp
    private static async void ReceiveC2dAsync()
    {
        Console.WriteLine("\nReceiving cloud to device messages from service");
        while (true)
        {
            Message receivedMessage = await s_deviceClient.ReceiveAsync();
            if (receivedMessage == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received message: {0}", 
            Encoding.ASCII.GetString(receivedMessage.GetBytes()));
            Console.ResetColor();

            await s_deviceClient.CompleteAsync(receivedMessage);
        }
    }
   ```

1. Přidejte následující metodu v **Main** `Console.ReadLine()` metoda, těsně před řádek:

   ```csharp
   ReceiveC2dAsync();
   ```

Metoda `ReceiveAsync` asynchronně vrátí přijatou zprávu v době, kdy je přijata zařízením. Vrátí *hodnotu null* po určité době časového prostoje. V tomto příkladu se používá výchozí hodnota jedné minuty. Když aplikace obdrží *hodnotu null*, měla by nadále čekat na nové zprávy. Tento požadavek je `if (receivedMessage == null) continue` důvodem řádku.

Volání `CompleteAsync()` upozorní službu IoT Hub, že zpráva byla úspěšně zpracována. Zprávu lze bezpečně odebrat z fronty zařízení. Pokud se stalo něco, co bránilo aplikaci zařízení v dokončení zpracování zprávy, služba IoT Hub ji znovu doručí. Logika zpracování zpráv v aplikaci zařízení musí být *idempotentní*, takže přijetí stejné zprávy vícekrát vytvoří stejný výsledek.

Aplikace může také dočasně opustit zprávu, což má za následek služby IoT hub zachování zprávy ve frontě pro budoucí spotřebu. Nebo aplikace může odmítnout zprávu, která trvale odebere zprávu z fronty. Další informace o životním cyklu zpráv mezi cloudy na zařízení najdete v [tématu zasílání zpráv D2C a C2D pomocí služby IoT Hub](iot-hub-devguide-messaging.md).

   > [!NOTE]
   > Při použití protokolu HTTPS namísto MQTT nebo AMQP jako přenos, `ReceiveAsync` metoda vrátí okamžitě. Podporovaný vzor pro zprávy typu cloud zařízení s protokolem HTTPS je občas připojená zařízení, která zřídka kontrolují zprávy (méně než každých 25 minut). Vydávání více HTTPS obdrží výsledky v Centru IoT omezení požadavků. Další informace o rozdílech mezi podporou MQTT, AMQP a HTTPS a omezením služby IoT Hub naleznete v [tématu zasílání zpráv D2C a C2D pomocí služby IoT Hub](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

V tomto článku vytvoříte back-endovou službu pro odesílání zpráv z cloudu na zařízení prostřednictvím služby IoT hub, kterou jste vytvořili v [aplikaci Send telemetrie ze zařízení do služby IoT hub](quickstart-send-telemetry-dotnet.md). K odesílání zpráv z cloudu na zařízení potřebuje vaše služba oprávnění **k připojení služby.** Ve výchozím nastavení je každé centrum IoT Hub vytvořeno pomocí zásady sdíleného přístupu s názvem **služba,** která uděluje toto oprávnění.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Odeslání zprávy z cloudu na zařízení

Teď napíšete aplikaci konzoly .NET, která odesílá zprávy z cloudu na zařízení do aplikace zařízení.

1. V aktuálním řešení Sady Visual Vyberte **soubor** > **nový** > **projekt**. V **okně Vytvořit nový projekt**vyberte Console App **(.NET Framework)** a pak vyberte **Další**.

1. Název projektu *SendCloudToDevice*. V části **Řešení**vyberte **Přidat do řešení** a přijměte nejnovější verzi rozhraní .NET Framework. Vyberte **Vytvořit** a vytvořte projekt.

   ![Konfigurace nového projektu v sadě Visual Studio](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na nové řešení a potom vyberte **Spravovat balíčky NuGet**.

1. V **části Správa balíčků NuGet**vyberte **Procházet**a pak vyhledejte a vyberte **Microsoft.Azure.Devices**. Vyberte **Instalovat**.

   Tento krok stáhne, nainstaluje a přidá odkaz na [balíček SDK NuGet služby Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

1. V horní `using` části **Program.cs** souboru přidejte následující příkaz.

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. Do třídy **Program** přidejte následující pole. Nahraďte zástupnou hodnotu připojovacím řetězcem služby IoT hub, který jste dříve zkopírovali v [připojovacím řetězci Služby IoT hub](#get-the-iot-hub-connection-string).

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

1. Do třídy **Program** přidejte následující metodu. Nastavte název zařízení na to, co jste použili při definování zařízení v [odesílání telemetrie ze zařízení do služby IoT hub](quickstart-send-telemetry-dotnet.md).

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   Tato metoda odešle novou zprávu cloud zařízení do zařízení `myFirstDevice`s ID , . Tento parametr změňte pouze v případě, že jste ho upravili z parametru použitého v [aplikaci Send telemetrie ze zařízení do služby IoT hub](quickstart-send-telemetry-dotnet.md).

1. Nakonec přidejte následující řádky do **Main** metoda.

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na vaše řešení a vyberte **Nastavit počáteční projekty**.

1. V**projektu spuštění** **běžných vlastností** > vyberte více projektů **při spuštění**a vyberte akci **Spustit** pro **ReadDeviceToCloudMessages**, **SimulatedDevice**a **SendCloudToDevice**. Výběrem **OK** uložte změny.

1. Stiskněte **klávesu F5**. Všechny tři aplikace by měly být zahájeny. Vyberte okna **SendCloudToDevice** a stiskněte **Enter**. Měla by se zobrazit zpráva, kterou přijímá aplikace zařízení.

   ![Zpráva o přijetí aplikace](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Získejte zpětnou vazbu o doručení

Je možné požádat o potvrzení o doručení (nebo vypršení platnosti) z ioT hubu pro každou zprávu cloud zařízení. Tato možnost umožňuje back-end řešení snadno informovat opakování nebo kompenzace logiky. Další informace o zpětné vazbě mezi cloudy na zařízení najdete v [tématu Zasílání zpráv D2C a C2D pomocí služby IoT Hub](iot-hub-devguide-messaging.md).

V této části upravíte aplikaci **SendCloudToDevice,** abyste požadovali zpětnou vazbu, a obdržíte ji z centra IoT hub.

1. V sadě Visual Studio v projektu **SendCloudToDevice** přidejte do třídy **Program** následující metodu.

   ```csharp
   private async static void ReceiveFeedbackAsync()
   {
        var feedbackReceiver = serviceClient.GetFeedbackReceiver();

        Console.WriteLine("\nReceiving c2d feedback from service");
        while (true)
        {
            var feedbackBatch = await feedbackReceiver.ReceiveAsync();
            if (feedbackBatch == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received feedback: {0}",
              string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
            Console.ResetColor();

            await feedbackReceiver.CompleteAsync(feedbackBatch);
        }
    }
    ```

    Všimněte si, že tento vzor příjmu je stejný jako ten, který se používá k přijímání zpráv typu cloud zařízení z aplikace zařízení.

1. Přidejte následující řádek v **Main** `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`metoda, hned za .

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. Chcete-li požádat o zpětnou vazbu pro doručení zprávy typu cloud zařízení, musíte zadat vlastnost v metodě **SendCloudToDeviceMessageAsync.** Přidejte následující řádek, `var commandMessage = new Message(...);` hned za řádek.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. Spusťte aplikace stisknutím **klávesy F5**. Měli byste vidět všechny tři aplikace start. Vyberte okna **SendCloudToDevice** a stiskněte **Enter**. Měli byste vidět zprávu, která je přijímána aplikací zařízení, a po několika sekundách, zpráva zpětné vazby, která je přijímána aplikací **SendCloudToDevice.**

   ![Zpráva o přijetí aplikace](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Pro jednoduchost tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování, jako je exponenciální backoff, jak je navrženo v [přechodové zpracování chyb](/azure/architecture/best-practices/transient-faults).
>

## <a name="next-steps"></a>Další kroky

V tomto návodu jste se naučili odesílat a přijímat zprávy z cloudu na zařízení.

Příklady kompletních komplexních řešení, která používají službu IoT Hub, najdete v [tématu Akcelerátor řešení vzdáleného monitorování Azure IoT](https://docs.microsoft.com/azure/iot-suite/).

Další informace o vývoji řešení pomocí služby IoT Hub najdete v [průvodci vývojáři služby IoT Hub](iot-hub-devguide.md).
