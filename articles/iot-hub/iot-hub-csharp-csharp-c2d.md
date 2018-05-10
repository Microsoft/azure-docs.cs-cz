---
title: Zprávy typu cloud zařízení s Azure IoT Hub (.NET) | Microsoft Docs
description: Postupy pro odesílání zpráv typu cloud zařízení pro zařízení ze služby Azure IoT hub pomocí sady Azure IoT SDK pro .NET. Můžete upravit aplikaci zařízení příjem zpráv typu cloud zařízení a úpravě back-end aplikace k odesílání zpráv typu cloud zařízení.
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''
ms.assetid: a31c05ed-6ec0-40f3-99ab-8fdd28b1a89a
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: elioda
ms.openlocfilehash: b867976c637cdd4dd9b696382103c63f1af2e8b3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Odesílání zpráv z cloudu do zařízení s centra IoT (.NET)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Úvod
Azure IoT Hub je plně spravovaná služba, která pomáhá povolit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-end řešení. [Začínáme se službou IoT Hub] kurz ukazuje, jak k vytvoření služby IoT hub, zřídit identitu zařízení v ní a kódu aplikaci ze zařízení, která odesílá zprávy typu zařízení cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu vychází [Začínáme se službou IoT Hub]. Jak ukazuje na:

* Z back-end vašeho řešení odesílání zpráv typu cloud zařízení na jedno zařízení prostřednictvím služby IoT Hub.
* Příjem zpráv typu cloud zařízení na zařízení.
* Z back-end vašeho řešení, žádosti o potvrzení o doručení (*zpětné vazby*) pro zprávy odeslané do zařízení ze služby IoT Hub.

Můžete najít další informace o zprávy typu cloud zařízení v [Příručka vývojáře pro službu IoT Hub][IoT Hub developer guide - C2D].

Na konci tohoto kurzu můžete spustit dvě aplikace konzoly .NET:

* **SimulatedDevice**, upravenou verzi aplikace vytvořená v [Začínáme se službou IoT Hub], který se připojuje ke službě IoT hub a přijímá zprávy typu cloud zařízení.
* **SendCloudToDevice**, který odešle zprávu cloud zařízení do aplikace zařízení prostřednictvím služby IoT Hub a pak obdrží jeho potvrzení o doručení.

> [!NOTE]
> IoT Hub je podpora v sadě SDK pro mnoho zařízení platformy a jazyky (například C, Javy a JavaScriptu) prostřednictvím [sady SDK pro zařízení Azure IoT]. Podrobné pokyny o tom, jak připojit zařízení ke kódu v tomto kurzu a obecně do služby Azure IoT Hub, najdete v článku [Příručka vývojáře pro službu IoT Hub].
> 
> 

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio 2015 nebo Visual Studio 2017
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

## <a name="receive-messages-in-the-device-app"></a>Příjem zpráv v aplikaci zařízení
V této části upravíte zařízení aplikaci, kterou jste vytvořili v [Začínáme se službou IoT Hub] pro příjem zpráv typu cloud zařízení ze služby IoT hub.

1. V sadě Visual Studio v **SimulatedDevice** projekt, přidejte následující metodu do **Program** třídy.
   
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
   
    `ReceiveAsync` Metoda přijaté zprávy asynchronně vrátí v čase, který je přijatých zařízení. Vrátí *null* po specifiable časový limit (v takovém případě se používá výchozí minutu). Pokud aplikace obdrží *null*, by měly být nadále čekat na nové zprávy. Tento požadavek je důvody, proč `if (receivedMessage == null) continue` řádku.
   
    Volání `CompleteAsync()` IoT Hub upozorní, že zpráva byla úspěšně zpracována. Zprávu lze bezpečně odebrat z fronty zařízení. Pokud se něco stalo, která zabránila aplikace zařízení dokončení zpracování zprávy, IoT Hub zajišťuje ho znovu. Je důležité, že zpráva zpracování logiky v aplikaci zařízení *idempotent*tak, aby přijímá stejnou zprávu vícekrát vytváří stejný výsledek. Aplikace můžete také dočasně abandon zprávu, což vede k zachování zprávu ve frontě pro budoucí používání služby IoT hub. Nebo můžete aplikaci odmítnout zprávu, která trvale odstraní zprávu z fronty. Další informace o životním cyklu zprávy typu cloud zařízení najdete v tématu [Příručka vývojáře pro službu IoT Hub][IoT Hub developer guide - C2D].
   
   > [!NOTE]
   > Při použití protokolu HTTPS místo MQTT nebo AMQP přenos, `ReceiveAsync` metoda vrátí okamžitě. Podporované vzor pro zprávy typu cloud zařízení s protokolem HTTPS je občas připojená zařízení, které zkontrolujte zprávy zřídka (méně než každých 25 minut). Vydání další HTTPS přijme výsledky v IoT Hub, omezení požadavků. Další informace o rozdílech mezi podpora MQTT, AMQP a HTTPS a omezení služby IoT Hub, najdete v článku [Příručka vývojáře pro službu IoT Hub][IoT Hub developer guide - C2D].
   > 
   > 
2. Přidejte následující metodu v **hlavní** metoda, těsně před `Console.ReadLine()` řádku:
   
        ReceiveC2dAsync();

> [!NOTE]
> Pro saké na jednoduchost tento kurz neimplementuje žádné zásady opakování. V produkčním kódu, měli byste implementovat zásady opakování (například exponenciálního omezení rychlosti), dle pokynů v článku na webu MSDN [přechodných chyb].
> 
> 

## <a name="send-a-cloud-to-device-message"></a>Odeslání zprávy typu cloud zařízení
V této části napíšete konzolovou aplikaci .NET, která odesílá zprávy typu cloud zařízení na zařízení aplikaci.

1. V aktuálním řešení sady Visual Studio, vytvoření projektu Visual C# plochy aplikace pomocí **konzolové aplikace** šablona projektu. Název projektu **SendCloudToDevice**.
   
    ![Nový projekt v sadě Visual Studio][20]
2. V Průzkumníku řešení klikněte pravým tlačítkem na řešení a pak klikněte na tlačítko **spravovat balíčky NuGet pro řešení...** . 
   
    Tato akce otevře **spravovat balíčky NuGet** okno.
3. Vyhledejte **Microsoft.Azure.Devices**, klikněte na tlačítko **nainstalovat**a přijměte podmínky použití. 
   
    To stáhne, nainstaluje a přidá odkaz na [balíček NuGet sady SDK služby Azure IoT].

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
   
    Tato metoda odesílá novou zprávu typu cloud zařízení na zařízení s ID, `myFirstDevice`. Tento parametr změnit, pouze v případě, že se změnil z používaný v [Začínáme se službou IoT Hub].
7. Nakonec do metody **Main** přidejte následující řádky:
   
        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
   
        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();
8. V sadě Visual Studio, klikněte pravým tlačítkem na řešení a vyberte **nastavit projekty po spuštění...** . Vyberte **více projektů po spuštění**, vyberte **spustit** akce pro **ReadDeviceToCloudMessages**, **SimulatedDevice**, a **SendCloudToDevice**.
9. Stiskněte klávesu **F5**. Všechny tři aplikace by se měl spustit. Vyberte **SendCloudToDevice** windows a stiskněte klávesu **Enter**. Měli byste vidět zprávu přijímá aplikace zařízení.
   
   ![Zprávy přijímající aplikace][21]

## <a name="receive-delivery-feedback"></a>Přijímat zpětnou vazbu o doručení
Je možné k potvrzení žádosti o doručení (nebo vypršení platnosti) ze služby IoT Hub pro každou zprávu cloud zařízení. Tato možnost umožňuje back-end řešení snadno informovat opakovat, nebo kompenzace logiku. Další informace o zpětné vazbě z cloudu do zařízení, najdete v článku [Příručka vývojáře pro službu IoT Hub][IoT Hub developer guide - C2D].

V této části upravíte **SendCloudToDevice** aplikaci žádosti o zpětnou vazbu a přijímat ze služby IoT Hub.

1. V sadě Visual Studio v **SendCloudToDevice** projekt, přidejte následující metodu do **Program** třídy.
   
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
   
    Všimněte si, že tento vzor receive se shoduje s klíčem pro příjem zpráv typu cloud zařízení z aplikace zařízení.
2. Přidejte následující metodu v **hlavní** metoda hned po `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` řádku:
   
        ReceiveFeedbackAsync();
3. K žádosti o zpětnou vazbu pro doručení zprávy typu cloud zařízení, budete muset určit na vlastnost ve **SendCloudToDeviceMessageAsync** metoda. Přidejte následující řádek, hned po `var commandMessage = new Message(...);` řádku:
   
        commandMessage.Ack = DeliveryAcknowledgement.Full;
4. Spuštění aplikace stisknutím **F5**. Měli byste vidět všechny tři aplikace spustit. Vyberte **SendCloudToDevice** windows a stiskněte klávesu **Enter**. Měli byste vidět zprávu přijímání aplikace zařízení a za několik sekund, přijímá zprávy zpětnou vazbu vaše **SendCloudToDevice** aplikace.
   
   ![Zprávy přijímající aplikace][22]

> [!NOTE]
> Pro saké na jednoduchost tento kurz neimplementuje žádné zásady opakování. V produkčním kódu, měli byste implementovat zásady opakování (například exponenciálního omezení rychlosti), dle pokynů v článku na webu MSDN [přechodných chyb].
> 
> 

## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak odesílat a přijímat zprávy typu cloud zařízení. 

Příklady dokončení začátku do konce řešení, které pomocí služby IoT Hub, najdete v sekci [akcelerátoru řešení Azure IoT vzdálené monitorování].

Další informace o vývoji řešení službou IoT Hub, najdete v článku [Příručka vývojáře pro službu IoT Hub].

<!-- Images -->
[20]: ./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png
[21]: ./media/iot-hub-csharp-csharp-c2d/sendc2d1.png
[22]: ./media/iot-hub-csharp-csharp-c2d/sendc2d2.png

<!-- Links -->

[balíček NuGet sady SDK služby Azure IoT]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[přechodných chyb]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md

[Příručka vývojáře pro službu IoT Hub]: iot-hub-devguide.md
[Začínáme se službou IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[akcelerátoru řešení Azure IoT vzdálené monitorování]: https://docs.microsoft.com/azure/iot-suite/
[sady SDK pro zařízení Azure IoT]: iot-hub-devguide-sdks.md