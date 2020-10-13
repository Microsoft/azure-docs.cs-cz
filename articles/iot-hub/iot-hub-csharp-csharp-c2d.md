---
title: Zprávy z cloudu na zařízení pomocí Azure IoT Hub (.NET) | Microsoft Docs
description: Postup posílání zpráv z cloudu na zařízení ze služby Azure IoT Hub pomocí sad SDK Azure IoT pro .NET. Aplikaci zařízení upravíte tak, aby přijímala zprávy z cloudu na zařízení a upravila back-end aplikaci pro posílání zpráv z cloudu na zařízení.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-csharp
ms.openlocfilehash: ff6153abb3e930e3268ed7768e4ab44c9b5824cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449568"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Posílání zpráv z cloudu do zařízení pomocí IoT Hub (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub je plně spravovaná služba, která pomáhá zajistit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-endu řešení. [Možnost Odeslat telemetrii ze zařízení do služby IoT Hub](quickstart-send-telemetry-dotnet.md) – rychlý Start ukazuje, jak vytvořit centrum IoT, zřídit v něm identitu zařízení a vytvořit kód aplikace pro zařízení odesílající zprávy typu zařízení-Cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tento kurz sestaví na [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-dotnet.md). Ukazuje, jak provádět následující úlohy:

* Z back-endu vašeho řešení odesílají zprávy typu cloud-zařízení do jediného zařízení prostřednictvím IoT Hub.

* Příjem zpráv typu cloud-zařízení na zařízení.

* Z back-endu vašeho řešení požadavek na doručení zpráv*feedback*odesílaných do zařízení z IoT Hub.

Další informace o zprávách z cloudu na zařízení najdete v [D2C a C2D zasílání zpráv pomocí IoT Hub](iot-hub-devguide-messaging.md).

Na konci tohoto kurzu spustíte dvě konzolové aplikace .NET.

* **SimulatedDevice**. Tato aplikace se připojí ke službě IoT Hub a přijímá zprávy z cloudu na zařízení. Tato aplikace je upravená verze aplikace vytvořená v [rámci odesílání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-dotnet.md).

* **SendCloudToDevice**. Tato aplikace pošle zprávu typu cloud-zařízení do aplikace zařízení prostřednictvím IoT Hub a potom obdrží potvrzení o doručení.

> [!NOTE]
> IoT Hub podporuje sadu SDK pro mnoho platforem a jazyků zařízení, včetně C, Java, Pythonu a JavaScriptu, prostřednictvím [sad SDK pro zařízení Azure IoT](iot-hub-devguide-sdks.md). Podrobné pokyny, jak připojit zařízení k kódu tohoto kurzu a obecně k Azure IoT Hub, najdete v [příručce pro vývojáře IoT Hub](iot-hub-devguide.md).
>

## <a name="prerequisites"></a>Požadavky

* Visual Studio

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-device-app"></a>Příjem zpráv v aplikaci zařízení

V této části Upravte aplikaci zařízení, kterou jste vytvořili v části [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-dotnet.md) pro příjem zpráv z cloudu na zařízení ze služby IoT Hub.

1. V aplikaci Visual Studio v projektu **SimulatedDevice** přidejte následující metodu do třídy **SimulatedDevice** .

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

1. Do metody **Main** přidejte následující metodu, která je přímo před `Console.ReadLine()` řádkem:

   ```csharp
   ReceiveC2dAsync();
   ```

`ReceiveAsync`Metoda asynchronně vrátí přijatou zprávu v okamžiku, kdy zařízení obdrží. Po určitém časovém limitu vrátí *hodnotu null* . V tomto příkladu je použita výchozí hodnota 1 minuta. Když aplikace obdrží *hodnotu null*, měla by nadále čekat na nové zprávy. Tento požadavek je důvodem pro `if (receivedMessage == null) continue` řádek.

Volání `CompleteAsync()` upozorňující na IoT Hub, že zpráva byla úspěšně zpracována a že zprávu lze bezpečně odebrat z fronty zařízení. Zařízení by mělo zavolat tuto metodu, když se její zpracování úspěšně dokončí bez ohledu na to, který protokol používá.

V případě AMQP a HTTPS, ale ne MQTT, může zařízení také:

* Opuštění zprávy, která má za následek IoT Hub uchování zprávy ve frontě zařízení pro budoucí spotřebu.
* Odmítněte zprávu, která trvale odstraní zprávu z fronty zařízení.

Pokud dojde k nějakému problému, který zabrání zařízení v dokončení, zrušení nebo odmítnutí zprávy, IoT Hub po pevném časovém limitu zařadí do fronty zprávu pro doručení znovu. Z tohoto důvodu musí být logika zpracování zpráv v aplikaci zařízení *idempotentní*, aby se stejná zpráva zobrazovala vícekrát, což má stejný výsledek.

Podrobnější informace o tom, jak IoT Hub zpracovává zprávy typu cloud-zařízení, včetně podrobností o životním cyklu zpráv z cloudu na zařízení, najdete v tématu [posílání zpráv z cloudu na zařízení ze služby IoT Hub](iot-hub-devguide-messages-c2d.md).

> [!NOTE]
> Při použití protokolu HTTPS namísto MQTT nebo AMQP jako přenosu `ReceiveAsync` vrátí metoda okamžitě. Podporovaným vzorům pro zprávy typu cloud-zařízení pomocí protokolu HTTPS jsou občas připojená zařízení, která kontrolují zprávy zřídka (nejméně každých 25 minut). Výsledkem vydávání většího protokolu HTTPS je IoT Hub omezování požadavků. Další informace o rozdílech mezi podporou MQTT, AMQP a HTTPS najdete v tématu [pokyny pro komunikaci z cloudu na zařízení](iot-hub-devguide-c2d-guidance.md) a [Vyberte komunikační protokol](iot-hub-devguide-protocols.md).
>

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

V tomto článku vytvoříte back-end službu pro posílání zpráv z cloudu na zařízení prostřednictvím služby IoT Hub, kterou jste vytvořili v tématu [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-dotnet.md). K posílání zpráv z cloudu na zařízení potřebuje služba oprávnění **připojit** se. Ve výchozím nastavení se všechny IoT Hub vytvoří pomocí zásad sdíleného přístupu s názvem **Služba** , která toto oprávnění udělí.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Odeslání zprávy typu cloud-zařízení

V této části vytvoříte konzolovou aplikaci .NET, která odesílá zprávy typu cloud-zařízení do aplikace simulovaného zařízení.

1. V aktuálním řešení sady Visual Studio vyberte **soubor**  >  **Nový**  >  **projekt**. V poli **vytvořit nový projekt**vyberte **Konzolová aplikace (.NET Framework)** a pak vyberte **Další**.

1. Pojmenujte projekt *SendCloudToDevice*. V části **řešení**vyberte **Přidat do řešení** a přijměte nejnovější verzi .NET Framework. Vyberte **Vytvořit** a vytvořte projekt.

   ![Konfigurace nového projektu v aplikaci Visual Studio](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. V Průzkumník řešení klikněte pravým tlačítkem myši na nový projekt a vyberte možnost **Spravovat balíčky NuGet**.

1. V **možnosti spravovat balíčky NuGet**vyberte **Procházet**a pak vyhledejte a vyberte **Microsoft. Azure. Devices**. Vyberte  **nainstalovat**.

   Tento krok stáhne, nainstaluje a přidá odkaz na [balíček NuGet sady SDK služby Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

1. Do `using` horní části souboru **program.cs** přidejte následující příkaz.

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. Do třídy **Program** přidejte následující pole. Nahraďte `{iot hub connection string}` hodnotu zástupného symbolu připojovacím řetězcem IoT Hub, který jste si dříve poznamenali v [části získání připojovacího řetězce centra IoT Hub](#get-the-iot-hub-connection-string). Nahraďte `{device id}` hodnotu zástupného symbolu ID zařízení, které jste přidali při [odesílání telemetrie ze zařízení do rychlého startu služby IoT Hub](quickstart-send-telemetry-dotnet.md) .

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   static string targetDevice = "{device id}";
   ```

1. Do třídy **program** přidejte následující metodu, která odešle zprávu do vašeho zařízení.

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync(targetDevice, commandMessage);
   }
   ```

1. Nakonec do metody **Main** přidejte následující řádky.

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. V Průzkumníku řešení klikněte pravým tlačítkem na své řešení a vyberte **nastavit projekty po spuštění**.

1. V **okně běžné vlastnosti**  >  **spouštěný projekt**vyberte **více projektů po spuštění**a pak vyberte akci **spuštění** pro **SimulatedDevice** a **SendCloudToDevice**. Výběrem **OK** uložte změny.

1. Stiskněte klávesu **F5**. Obě aplikace by měly být spuštěny. Vyberte okno **SendCloudToDevice** a stiskněte klávesu **ENTER**. Měla by se zobrazit zpráva přijímaná aplikací zařízení.

   ![Zpráva přijímání aplikace zařízení](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Doručení zpětné vazby

Pro každou zprávu z cloudu na zařízení je možné požádat o potvrzení doručení (nebo vypršení platnosti) z IoT Hub. Tato možnost umožňuje back-end řešení snadno informovat o opakování nebo logiku kompenzace. Další informace o zpětné vazbě z cloudu na zařízení najdete v tématu [zasílání zpráv z D2C a C2D pomocí IoT Hub](iot-hub-devguide-messaging.md).

V této části upravíte aplikaci **SendCloudToDevice** tak, aby požádala o zpětnou vazbu a přijímala ji ze služby IoT Hub.

1. V aplikaci Visual Studio v projektu **SendCloudToDevice** přidejte následující metodu do třídy **program** .

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

    Poznámka: Tento model příjmu je stejný jako použitý k přijímání zpráv z cloudu na zařízení z aplikace zařízení.

1. Do metody **Main** přidejte následující řádek, a to hned po `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` .

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. Chcete-li požádat o zpětnou vazbu k doručování zpráv z cloudu na zařízení, je nutné zadat vlastnost v metodě **SendCloudToDeviceMessageAsync** . Přidejte následující řádek hned za `var commandMessage = new Message(...);` řádek.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. Spusťte aplikace stisknutím klávesy **F5**. Měli byste vidět, jak se obě aplikace spouštějí. Vyberte okno **SendCloudToDevice** a stiskněte klávesu **ENTER**. Měla by se zobrazit zpráva přijímaná aplikací zařízení a po několika sekundách zpráva o zpětné vazbě přijímaná vaší aplikací **SendCloudToDevice** .

   ![Aplikace zařízení, která přijímá zprávu a aplikace služby, obdrží zpětnou vazbu](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Pro zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu by měly být implementovány zásady opakování, například exponenciální omezení rychlosti, navržené při [zpracování přechodné chyby](/azure/architecture/best-practices/transient-faults).
>

## <a name="next-steps"></a>Další kroky

V tomto postupu jste zjistili, jak odesílat a přijímat zprávy z cloudu do zařízení.

Příklady kompletních řešení, která používají IoT Hub, najdete v tématu [akcelerátor řešení vzdáleného monitorování Azure IoT](https://docs.microsoft.com/azure/iot-suite/).

Další informace o vývoji řešení pomocí IoT Hub najdete v příručce pro [vývojáře IoT Hub](iot-hub-devguide.md).
