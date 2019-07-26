---
title: Zprávy z cloudu na zařízení pomocí Azure IoT Hub (.NET) | Microsoft Docs
description: Postup posílání zpráv z cloudu na zařízení ze služby Azure IoT Hub pomocí sad SDK Azure IoT pro .NET. Aplikaci zařízení upravíte tak, aby přijímala zprávy z cloudu na zařízení a upravila back-end aplikaci pro posílání zpráv z cloudu na zařízení.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: robinsh
ms.openlocfilehash: 5a7cb4ecde599a76b2d42cf874420d9cbcfda3d5
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402649"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Posílání zpráv z cloudu do zařízení pomocí IoT Hub (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Úvod

Azure IoT Hub je plně spravovaná služba, která pomáhá zajistit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-endu řešení. [Možnost Odeslat telemetrii ze zařízení do služby IoT Hub](quickstart-send-telemetry-dotnet.md) – rychlý Start ukazuje, jak vytvořit centrum IoT, zřídit v něm identitu zařízení a vytvořit kód aplikace pro zařízení odesílající zprávy typu zařízení-Cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tento kurz sestaví na [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-dotnet.md). Dozvíte se, jak postupovat podle následujících kroků:

* Z back-endu vašeho řešení odesílají zprávy typu cloud-zařízení do jediného zařízení prostřednictvím IoT Hub.

* Příjem zpráv typu cloud-zařízení na zařízení.

* Z back-endu vašeho řešení požadavek na doručení zprávodesílaných do zařízení z IoT Hub.

Další informace o zprávách z cloudu na zařízení najdete v [D2C a C2D zasílání zpráv pomocí IoT Hub](iot-hub-devguide-messaging.md).

Na konci tohoto kurzu spustíte dvě konzolové aplikace .NET.

* **SimulatedDevice**upravenou verzi aplikace vytvořenou v části [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-dotnet.md), která se připojí ke službě IoT Hub a přijímá zprávy typu cloud-zařízení.

* **SendCloudToDevice**, která pošle zprávu typu cloud-zařízení do aplikace zařízení prostřednictvím IoT Hub a potom obdrží potvrzení o doručení.

> [!NOTE]
> IoT Hub podporuje sadu SDK pro mnoho platforem a jazyků zařízení (včetně C, Java a JavaScriptu) prostřednictvím [sad SDK pro zařízení Azure IoT](iot-hub-devguide-sdks.md). Podrobné pokyny, jak připojit zařízení k kódu tohoto kurzu a obecně k Azure IoT Hub, najdete v [příručce pro vývojáře IoT Hub](iot-hub-devguide.md).
>

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio

* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) .)

## <a name="receive-messages-in-the-device-app"></a>Příjem zpráv v aplikaci zařízení

V této části upravíte aplikaci zařízení, kterou jste vytvořili v části [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-dotnet.md) pro příjem zpráv z cloudu na zařízení ze služby IoT Hub.

1. V aplikaci Visual Studio v projektu **SimulatedDevice** přidejte následující metodu do třídy **program** .

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

   `ReceiveAsync` Metoda asynchronně vrátí přijatou zprávu v okamžiku, kdy zařízení obdrží. Po určitém časovém limitu vrátí *hodnotu null* (v tomto případě je použita výchozí hodnota 1 minuty). Když aplikace obdrží *hodnotu null*, měla by nadále čekat na nové zprávy. Tento požadavek je důvodem pro `if (receivedMessage == null) continue` řádek.

    Volání upozorňující `CompleteAsync()` na IoT Hub, že zpráva byla úspěšně zpracována. Zpráva může být bezpečně odebrána z fronty zařízení. Pokud se něco stalo s tím, že aplikaci pro zařízení zabránilo v dokončování zpracování zprávy, IoT Hub ji znovu doručí. V takovém případě je důležité, aby logika zpracování zpráv v aplikaci zařízení byla idempotentníá, aby se stejná zpráva zobrazovala vícekrát, což znamená stejný výsledek. 

    Aplikace může také dočasně opustit zprávu, což vede k tomu, že centrum IoT uchová zprávu ve frontě pro budoucí spotřebu. Nebo může aplikace zprávu odmítat, což trvale odstraní zprávu z fronty. Další informace o životním cyklu zpráv z cloudu na zařízení najdete v tématu [zasílání zpráv D2C a C2D pomocí IoT Hub](iot-hub-devguide-messaging.md).

   > [!NOTE]
   > Při použití protokolu https namísto MQTT nebo AMQP jako přenosu `ReceiveAsync` vrátí metoda okamžitě. Podporovaným vzorům pro zprávy typu cloud-zařízení pomocí protokolu HTTPS jsou občas připojená zařízení, která kontrolují zprávy zřídka (méně než každých 25 minut). Výsledkem vydávání většího protokolu HTTPS je IoT Hub omezování požadavků. Další informace o rozdílech mezi MQTT, AMQP a podporou protokolu HTTPS a omezením IoT Hub najdete v tématu [zasílání zpráv D2C a C2D pomocí IoT Hub](iot-hub-devguide-messaging.md).
   >

2. Do metody **Main** přidejte následující metodu, která je přímo před `Console.ReadLine()` řádkem:

   ```csharp
   ReceiveC2dAsync();
   ```

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

V tomto článku vytvoříte back-end službu pro posílání zpráv z cloudu na zařízení prostřednictvím služby IoT Hub, kterou jste vytvořili v tématu [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-dotnet.md). K posílání zpráv z cloudu na zařízení potřebuje služba oprávnění **připojit** se. Ve výchozím nastavení se všechny IoT Hub vytvoří pomocí zásad sdíleného přístupu s názvem **Služba** , která toto oprávnění udělí.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Odeslání zprávy typu cloud-zařízení

Nyní napíšete konzolovou aplikaci .NET, která odesílá zprávy typu cloud-zařízení do aplikace zařízení.

1. V aktuálním řešení sady Visual Studio klikněte pravým tlačítkem na řešení a vyberte Přidat > Nový projekt. Vyberte **desktopovou aplikaci Windows** a pak **konzolovou aplikaci (.NET Framework)** . Pojmenujte projekt **SendCloudToDevice** a vyberte nejnovější verzi .NET Framework a pak vyberte **OK** a vytvořte projekt.

   ![Nový projekt v aplikaci Visual Studio](./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png)

2. V Průzkumník řešení klikněte pravým tlačítkem na řešení a potom klikněte na **Spravovat balíčky NuGet pro řešení...** .

   Tato akce otevře okno **Spravovat balíčky NuGet** .

3. Vyhledejte **Microsoft. Azure. Devices**, vyberte kartu Procházet. Po nalezení balíčku klikněte na tlačítko **nainstalovat**a přijměte podmínky použití.

   Tím se stáhne, nainstaluje a přidá odkaz na [balíček NuGet sady SDK služby Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

4. Do horní části `using` souboru **program.cs** přidejte následující příkaz.

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

5. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem IoT Hub, který jste zkopírovali dříve v [části získání připojovacího řetězce centra IoT Hub](#get-the-iot-hub-connection-string).

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

6. Do třídy **Program** přidejte následující metodu. Nastavte název zařízení na to, co jste použili při definování zařízení v [odesílání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-dotnet.md).

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   Tato metoda pošle nové zprávě typu cloud-zařízení do zařízení s ID `myFirstDevice`. Tento parametr změňte pouze v případě, že jste ho změnili z toho, který se používá při [odesílání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-dotnet.md).

7. Nakonec do metody **Main** přidejte následující řádky.

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

8. V sadě Visual Studio klikněte pravým tlačítkem na řešení a vyberte **nastavit projekty po spuštění...** . Vyberte **více projektů po spuštění**a pak vyberte akci **spuštění** pro **ReadDeviceToCloudMessages**, **SimulatedDevice**a **SendCloudToDevice**.

9. Stiskněte klávesu **F5**. Všechny tři aplikace by se měly spustit. Vyberte okna **SendCloudToDevice** a stiskněte klávesu **ENTER**. Měla by se zobrazit zpráva přijímaná aplikací zařízení.

   ![Zpráva přijetí aplikace](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

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

2. Do metody **Main** přidejte následující metodu, a to hned za `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` řádkem.

   ``` csharp
   ReceiveFeedbackAsync();
   ```

3. Chcete-li požádat o zpětnou vazbu k doručování zpráv z cloudu na zařízení, je nutné zadat vlastnost v metodě **SendCloudToDeviceMessageAsync** . Přidejte následující řádek hned za `var commandMessage = new Message(...);` řádek.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

4. Spusťte aplikace stisknutím klávesy **F5**. Měli byste vidět, že se spustí všechny tři aplikace. Vyberte okna **SendCloudToDevice** a stiskněte klávesu **ENTER**. Měla by se zobrazit zpráva přijímaná aplikací zařízení a po několika sekundách zpráva o zpětné vazbě přijímaná vaší aplikací **SendCloudToDevice** .

   ![Zpráva přijetí aplikace](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Pro zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování (například exponenciální omezení rychlosti), jak je navrženo v článku, [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).
>

## <a name="next-steps"></a>Další postup

V tomto postupu jste zjistili, jak odesílat a přijímat zprávy z cloudu do zařízení.

Příklady kompletních řešení, která používají IoT Hub, najdete v tématu [akcelerátor řešení vzdáleného monitorování Azure IoT](https://docs.microsoft.com/azure/iot-suite/).

Další informace o vývoji řešení pomocí IoT Hub najdete v příručce pro [vývojáře IoT Hub](iot-hub-devguide.md).
