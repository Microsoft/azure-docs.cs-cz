---
title: Zprávy typu cloud zařízení Azure IoT Hub (.NET) | Dokumentace Microsoftu
description: Jak odesílat zprávy typu cloud zařízení na zařízení ze služby Azure IoT hub pomocí sad Azure IoT SDK pro .NET. Můžete upravit zařízení aplikaci pro příjem zpráv z cloudu do zařízení a upravovat back endové aplikace odesílat zprávy typu cloud zařízení.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: elioda
ms.openlocfilehash: e2090977813f5335f2170ff543bde19cd8bf123b
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185789"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Odesílání zpráv z cloudu do zařízení pomocí IoT Hub (.NET)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Úvod
Azure IoT Hub je plně spravovaná služba, která pomáhá povolit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-endu řešení. [Začínáme se službou IoT Hub] kurz ukazuje postupy při vytvoření služby IoT hub, zřídit identitu zařízení v něm a kódu aplikace pro zařízení, která odesílá zprávy typu zařízení cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tento kurz vychází [Začínáme se službou IoT Hub]. To se dozvíte, jak do:

* Z back-end vašeho řešení odesílat zprávy typu cloud zařízení na jediné zařízení prostřednictvím služby IoT Hub.
* Příjem zpráv typu cloud zařízení na zařízení.
* Z back-end vašeho řešení, požádat o doručení potvrzení (*zpětnou vazbu*) pro zprávy odeslané do zařízení ze služby IoT Hub.

Můžete najít další informace o zprávy typu cloud zařízení v [Příručka vývojáře pro IoT Hub][IoT Hub developer guide - C2D].

Na konci tohoto kurzu spusťte dvě konzolové aplikace .NET:

* **SimulatedDevice**, upravenou verzi aplikaci vytvořenou v [Začínáme se službou IoT Hub], který se připojí ke službě IoT hub a přijímá zprávy typu cloud zařízení.
* **SendCloudToDevice**, která odesílá zprávy typu cloud zařízení do aplikace pro zařízení prostřednictvím služby IoT Hub a potom přijímá jeho doručení potvrzení.

> [!NOTE]
> IoT Hub má sady SDK podporují mnoho platforem zařízení a jazycích (včetně C, Javy a JavaScriptu) prostřednictvím [Azure IoT SDK pro zařízení]. Podrobné pokyny o tom, jak připojit zařízení ke kódu v tomto kurzu a obecně pro službu Azure IoT Hub, najdete v článku [Příručka vývojáře pro IoT Hub].
> 
> 

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio 2015 nebo Visual Studio 2017
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

## <a name="receive-messages-in-the-device-app"></a>Příjem zpráv do aplikace pro zařízení
V této části upravíte zařízení aplikaci, kterou jste vytvořili v [Začínáme se službou IoT Hub] pro příjem zpráv typu cloud zařízení ze služby IoT hub.

1. V sadě Visual Studio v **SimulatedDevice** projektu, přidejte následující metodu do **Program** třídy.
   
        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();
   
                await deviceClient.CompleteAsync(receivedMessage);
            }
        }
   
    `ReceiveAsync` Metody asynchronně vrací přijaté zprávy v době, které se získaly v zařízení. Vrátí *null* po specifiable časový limit (v tomto případě je použita výchozí jednu minutu). Když aplikace obdrží *null*, by měly být nadále čekat pro nové zprávy. Tento požadavek není důvod `if (receivedMessage == null) continue` řádku.
   
    Volání `CompleteAsync()` upozorní služby IoT Hub, že zpráva byla úspěšně zpracována. Zpráva může bezpečně odebrána z fronty zařízení. Pokud se něco stalo, která znemožňuje dokončení zpracování zprávy aplikace pro zařízení služby IoT Hub doručí ho znovu. Pak je důležité zprávy zpracování logiky v aplikaci zařízení *idempotentní*tak, aby příjem stejná zpráva několikrát vytváří stejný výsledek. Aplikace můžete také dočasně opustit zprávu, což vede k zachování zprávu ve frontě pro budoucí využití služby IoT hub. Nebo aplikaci můžete odmítnout zprávu, která se trvale odstraní zprávu z fronty. Další informace o životním cyklu zprávy typu cloud zařízení, najdete v článku [Příručka vývojáře pro IoT Hub][IoT Hub developer guide - C2D].
   
   > [!NOTE]
   > Při použití protokolu HTTPS místo protokol MQTT nebo AMQP jako přenosového mechanismu, `ReceiveAsync` metoda vrátí hodnotu okamžitě. Podporovaný model pro zprávy typu cloud zařízení pomocí protokolu HTTPS se přerušovaně připojeným zařízením, které kontrolují zřídka zprávy (méně než každých 25 minut). Vydávání další HTTPS přijímá výsledky ve službě IoT Hub omezování požadavků. Další informace o rozdílech mezi podpora MQTT, AMQP a protokolu HTTPS a omezení šířky pásma služby IoT Hub, najdete v článku [Příručka vývojáře pro IoT Hub][IoT Hub developer guide - C2D].
   > 
   > 
2. Přidejte následující metodu v **hlavní** metody, těsně před `Console.ReadLine()` řádku:
   
        ReceiveC2dAsync();

> [!NOTE]
> Pro saké pro zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu by měly implementovat zásady opakování (například exponenciální regresí), jak je navrženo v článku na webu MSDN [zpracování přechodných chyb].
> 
> 

## <a name="send-a-cloud-to-device-message"></a>Odeslání zprávy typu cloud zařízení
V této části napíšete konzolovou aplikaci .NET, která odesílá zprávy typu cloud zařízení do aplikace pro zařízení.

1. V aktuálním řešení sady Visual Studio vytvořte projekt desktopové aplikace Visual C# s použitím **konzolovou aplikaci** šablony projektu. Pojmenujte projekt **SendCloudToDevice**.
   
    ![Nový projekt v sadě Visual Studio][20]
2. V Průzkumníku řešení klikněte pravým tlačítkem myši na řešení a potom klikněte na tlačítko **spravovat balíčky NuGet pro řešení...** . 
   
    Tato akce otevře **spravovat balíčky NuGet** okna.
3. Vyhledejte **Microsoft.Azure.Devices**, klikněte na tlačítko **nainstalovat**a přijměte podmínky použití. 
   
    Toto stáhne, nainstaluje a přidá odkaz na [balíček NuGet sady SDK služby Azure IoT].

4. Do horní části souboru **Program.cs** přidejte následující příkaz `using`:
   
        using Microsoft.Azure.Devices;
5. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem IoT hub z [Začínáme se službou IoT Hub]:
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Přidejte následující metodu do třídy **Program**:
   
        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }
   
    Tato metoda odesílá novou zprávu typu cloud zařízení na zařízení s ID, `myFirstDevice`. Tento parametr měnit, jenom v případě, že jste změnili než jaký se používá v [Začínáme se službou IoT Hub].
7. Nakonec do metody **Main** přidejte následující řádky:
   
        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
   
        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();
8. Ze sady Visual Studio, klikněte pravým tlačítkem na řešení a vyberte **nastavit projekty po spuštění...** . Vyberte **více projektů po spuštění**a pak **Start** akce pro **ReadDeviceToCloudMessages**, **SimulatedDevice**, a **SendCloudToDevice**.
9. Stisknutím klávesy **F5**. Všechny tři aplikace by měla začít. Vyberte **SendCloudToDevice** windows a klávesu **Enter**. Měli byste vidět zprávy přijímají aplikací zařízení.
   
   ![Přijímající aplikaci zprávu][21]

## <a name="receive-delivery-feedback"></a>Přijímat zpětnou vazbu o doručení
Je možné žádost o potvrzení doručování (nebo vypršení platnosti) ze služby IoT Hub pro každou zprávu typu cloud zařízení. Tato možnost povolí back-end řešení snadno informovat logiku opakování nebo kompenzaci. Další informace o zpětnou vazbu typu cloud zařízení, najdete v článku [Příručka vývojáře pro IoT Hub][IoT Hub developer guide - C2D].

V této části můžete změnit **SendCloudToDevice** aplikaci žádost o zpětnou vazbu a přijímat je ze služby IoT Hub.

1. V sadě Visual Studio v **SendCloudToDevice** projektu, přidejte následující metodu do **Program** třídy.
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();
   
            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();
   
                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }
   
    Všimněte si, že tento model receive je stejný, slouží k přijímání zpráv z cloudu do zařízení z aplikace pro zařízení.
2. Přidejte následující metodu v **hlavní** metodu, `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` řádku:
   
        ReceiveFeedbackAsync();
3. Na žádost o zpětnou vazbu k doručení zpráv typu cloud zařízení, je nutné zadat vlastnost **SendCloudToDeviceMessageAsync** metody. Přidejte následující řádek hned po `var commandMessage = new Message(...);` řádku:
   
        commandMessage.Ack = DeliveryAcknowledgement.Full;
4. Spouštění aplikací stisknutím kombinace kláves **F5**. Měli byste vidět všechny tři aplikace spustit. Vyberte **SendCloudToDevice** windows a klávesu **Enter**. By se zobrazit zpráva přijímají aplikací zařízení a po několika sekundách se přijal zprávu zpětnou vazbu vaše **SendCloudToDevice** aplikace.
   
   ![Přijímající aplikaci zprávu][22]

> [!NOTE]
> Pro saké pro zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu by měly implementovat zásady opakování (například exponenciální regresí), jak je navrženo v článku na webu MSDN [zpracování přechodných chyb].
> 
> 

## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak posílat a přijímat zprávy typu cloud zařízení. 

Příklady kompletní řešení začátku do konce, které používají služby IoT Hub najdete v tématu [Akcelerátor řešení Azure IoT vzdálené monitorování].

Další informace o vývoji řešení s využitím služby IoT Hub, najdete v článku [Příručka vývojáře pro IoT Hub].

<!-- Images -->
[20]: ./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png
[21]: ./media/iot-hub-csharp-csharp-c2d/sendc2d1.png
[22]: ./media/iot-hub-csharp-csharp-c2d/sendc2d2.png

<!-- Links -->

[Balíček NuGet sady SDK služby Azure IoT]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Zpracování přechodných chyb]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md

[Příručka vývojáře pro IoT Hub]: iot-hub-devguide.md
[Začínáme se službou IoT Hub]: quickstart-send-telemetry-dotnet.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Akcelerátor řešení Azure IoT vzdálené monitorování]: https://docs.microsoft.com/azure/iot-suite/
[Azure IoT SDK pro zařízení]: iot-hub-devguide-sdks.md