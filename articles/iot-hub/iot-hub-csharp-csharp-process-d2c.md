---
title: Směrování zpráv službou Azure IoT Hub (.Net) | Microsoft Docs
description: Postupy zpracování zpráv typu zařízení cloud Azure IoT Hub pomocí pravidel směrování a vlastní koncové body k odeslání zprávy do dalších služeb back-end.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 5177bac9-722f-47ef-8a14-b201142ba4bc
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 84a74a59417d3d1b9ebe0e2ede6c105b6fb3a576
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="routing-messages-with-iot-hub-net"></a>Směrování zpráv s centra IoT (.NET)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

V tomto kurzu vychází [Začínáme se službou IoT Hub] kurzu. Kurz:

* Ukazuje, jak používat směrování pravidla k odesílání zpráv typu zařízení cloud způsobem snadno, založené na konfiguraci.
* Ukazuje, jak izolovat interaktivní zprávy, které vyžadují okamžitou akci z back-end řešení pro další zpracování. Zařízení může například odešle zprávu výstrahy, která aktivuje vkládání lístek do systému CRM. Naproti tomu datového bodu zprávy, jako je například teplotní telemetrie informačního kanálu do modul analytics.

Na konci tohoto kurzu můžete spustit tři aplikace konzoly .NET:

* **SimulatedDevice**, upravenou verzi aplikace vytvořená v [Začínáme se službou IoT Hub] kurzu se odešle zprávy typu zařízení cloud datového bodu za sekundu a interaktivní zařízení cloud zprávy každých 10 sekund.
* **ReadDeviceToCloudMessages** který zobrazí nekritické telemetrické zprávy odesílané aplikace zařízení.
* **ReadCriticalQueue** zrušte fronty kritické zprávy odeslané aplikace zařízení z fronty Service Bus. Tato fronta je připojený ke službě IoT hub.

> [!NOTE]
> IoT Hub je podpora v sadě SDK pro mnoho zařízení platformy a jazyky, včetně C, Java a JavaScript. Zjistěte, jak nahradit simulované zařízení v tomto kurzu fyzické zařízení, najdete v článku [Azure střediska pro vývojáře IoT].

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio 2015 nebo Visual Studio 2017.
* Aktivní účet Azure. <br/>Pokud účet nemáte, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free/) si během několika minut.

Doporučujeme také výklad o [Azure Storage] a [Azure Service Bus].

## <a name="send-interactive-messages"></a>Odesílat interaktivní zprávy

Upravit aplikaci zařízení, kterou jste vytvořili v [Začínáme se službou IoT Hub] kurzu příležitostně odesílat interaktivní zprávy.

V sadě Visual Studio v **SimulatedDevice** projektu, nahraďte `SendDeviceToCloudMessagesAsync` metoda následujícím kódem:

```csharp
private static async void SendDeviceToCloudMessagesAsync()
{
    double minTemperature = 20;
    double minHumidity = 60;
    Random rand = new Random();

    while (true)
    {
        double currentTemperature = minTemperature + rand.NextDouble() * 15;
        double currentHumidity = minHumidity + rand.NextDouble() * 20;

        var telemetryDataPoint = new
        {
            deviceId = "myFirstDevice",
            temperature = currentTemperature,
            humidity = currentHumidity
        };
        var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
        string levelValue;

        if (rand.NextDouble() > 0.7)
        {
            if (rand.NextDouble() > 0.5)
            {
                messageString = "This is a critical message";
                levelValue = "critical";
            }
            else 
            {
                messageString = "This is a storage message";
                levelValue = "storage";
            }
        }
        else
        {
            levelValue = "normal";
        }
        
        var message = new Message(Encoding.ASCII.GetBytes(messageString));
        message.Properties.Add("level", levelValue);
        
        await deviceClient.SendEventAsync(message);
        Console.WriteLine("{0} > Sent message: {1}", DateTime.Now, messageString);

        await Task.Delay(1000);
    }
}
```

Tato metoda náhodně přidá vlastnost `"level": "critical"` a `"level": "storage"` na zprávy odeslané zařízením, která simuluje zprávu, která vyžaduje okamžitý zásah back-end aplikace nebo ten, který musí být trvale uložená. Aplikace podporuje směrování zpráv podle tělo zprávy.

> [!NOTE]
> Vlastnosti zprávy pro směrování zpráv můžete použít pro různé scénáře, včetně studený cesty při zpracování, kromě zde ukazuje příklad hot-path.

> [!NOTE]
> Důrazně doporučujeme implementovat zásady opakování například exponenciálního omezení rychlosti dle pokynů v článku na webu MSDN [přechodných chyb].

## <a name="route-messages-to-a-queue-in-your-iot-hub"></a>Směrování zpráv do fronty ve službě IoT hub

V této části:

* Vytvoření fronty Service Bus.
* Připojí ke službě IoT hub.
* Konfigurace služby IoT hub odesílat zprávy do fronty na základě přítomnosti vlastnosti na zprávu.

Další informace o tom, jak zpracování zpráv z fronty služby Service Bus najdete v tématu [začít pracovat s fronty][Service Bus queue].

1. Vytvoření fronty Service Bus, jak je popsáno v [začít pracovat s fronty][Service Bus queue]. Fronty musí být ve stejném předplatném, oblasti jako službu IoT hub. Poznamenejte si název oboru názvů a fronty.

    > [!NOTE]
    > Fronty sběrnice a témata použít jako koncové body centra IoT nesmí mít **relací** nebo **duplicitní detekce** povolena. Pokud některá z těchto možností jsou povolené, koncový bod se zobrazí jako **Unreachable** na portálu Azure.

2. Na portálu Azure otevřete své služby IoT hub a klikněte na tlačítko **koncové body**.
    
    ![Koncové body v centru IoT][30]

3. V **koncové body** okně klikněte na tlačítko **přidat** v horní části přidat fronty do služby IoT hub. Název koncového bodu **CriticalQueue** a použijte rozevírací seznamy a vyberte **fronty Service Bus**, oboru názvů Service Bus, ve které je umístěn fronty a názvu fronty. Až budete hotovi, klikněte na tlačítko **Uložit** dole.
    
    ![Přidání koncového bodu][31]
    
4. Klikněte na tlačítko **trasy** ve službě IoT Hub. Klikněte na tlačítko **přidat** v horní části okna Vytvořit pravidlo směrování pro směrování zpráv do fronty jste právě přidali. Vyberte **DeviceTelemetry** jako zdroj dat. Zadejte `level="critical"` jako podmínka a vyberte fronty, který jste právě přidali jako vlastní koncový bod jako koncový bod směrování pravidlo. Až budete hotovi, klikněte na tlačítko **Uložit** dole.
    
    ![Přidání trasu][32]
    
    Ujistěte se, že záložní cesta je nastavena na **ON**. Tato hodnota je výchozí konfiguraci pro Centrum IoT.
    
    ![Záložní trasy][33]

## <a name="read-from-the-queue-endpoint"></a>Čtení z fronty koncového bodu

V této části čtení zpráv z fronty koncového bodu.

1. V sadě Visual Studio přidejte ke stávajícímu řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace (.NET Framework)**. Název projektu **ReadCriticalQueue**.

2. V Průzkumníku řešení klikněte pravým tlačítkem myši **ReadCriticalQueue** projektu a pak klikněte na **spravovat balíčky NuGet**. Zobrazí tuto operaci **Správce balíčků NuGet** okno.

3. Vyhledejte **WindowsAzure.ServiceBus**, klikněte na tlačítko **nainstalovat**a přijměte podmínky použití. Tato operace stáhne, nainstaluje a přidá odkaz na Azure Service Bus, se všemi jeho závislostmi.

4. Přidejte následující **pomocí** příkazy v horní části **Program.cs** souboru:
   
    ```csharp
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Nakonec přidejte následující řádky, které se **hlavní** metoda. Nahraďte připojovacího řetězce s **naslouchání** oprávnění pro fronty:
   
    ```csharp
    Console.WriteLine("Receive critical messages. Ctrl-C to exit.\n");
    var connectionString = "{service bus listen string}";
    var queueName = "{queue name}";
    
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);

    client.OnMessage(message =>
        {
            Stream stream = message.GetBody<Stream>();
            StreamReader reader = new StreamReader(stream, Encoding.ASCII);
            string s = reader.ReadToEnd();
            Console.WriteLine(String.Format("Message body: {0}", s));
        });
        
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Spuštění aplikací
Nyní můžete spustit aplikace.

1. V sadě Visual Studio v Průzkumníku řešení klikněte pravým tlačítkem na řešení a vyberte **nastavit projekty po spuštění**. Vyberte **více projektů po spuštění**, pak vyberte **spustit** jako akce pro **ReadDeviceToCloudMessages**, **SimulatedDevice**, a **ReadCriticalQueue** projekty.
2. Stiskněte klávesu **F5** zahájíte jsou tři konzole aplikace. **ReadDeviceToCloudMessages** aplikace má pouze nekritické zpráv odeslaných z **SimulatedDevice** aplikace a **ReadCriticalQueue** aplikace má pouze kritických zprávy.
   
   ![Tři aplikace konzoly][50]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Volitelné) Přidat kontejner úložiště pro IoT hub a směrování zpráv do ní

V této části vytvořit účet úložiště, připojte ho do služby IoT hub a konfigurace služby IoT hub pro odesílání zpráv pro účet, na základě přítomnosti vlastnosti na zprávu. Další informace o tom, jak spravovat úložiště najdete v tématu [Začínáme s Azure Storage][Azure Storage].

 > [!NOTE]
   > Pokud si nejste omezená na jedno **koncový bod**, může instalace **StorageContainer** kromě **CriticalQueue** a spusťte obě simulatneously.

1. Vytvořte účet úložiště, jak je popsáno v [dokumentaci úložiště Azure] [lnk úložiště]. Poznamenejte si název účtu.

2. Na portálu Azure otevřete své služby IoT hub a klikněte na tlačítko **koncové body**.

3. V **koncové body** okně, vyberte **CriticalQueue** koncový bod a klikněte na tlačítko **odstranit**. Klikněte na tlačítko **Ano**a potom klikněte na **přidat**. Název koncového bodu **StorageContainer** a použijte rozevírací seznamy a vyberte **kontejneru úložiště Azure**a vytvořte **účet úložiště** a **úložiště kontejner**.  Poznamenejte si názvy.  Až budete hotovi, klikněte na tlačítko **OK** dole. 

 > [!NOTE]
   > Pokud si nejste omezená na jedno **koncový bod**, není potřeba odstranit **CriticalQueue**.

4. Klikněte na tlačítko **trasy** ve službě IoT Hub. Klikněte na tlačítko **přidat** v horní části okna Vytvořit pravidlo směrování pro směrování zpráv do fronty jste právě přidali. Vyberte **zprávy zařízení** jako zdroj dat. Zadejte `level="storage"` jako podmínka a zvolte **StorageContainer** jako vlastní koncový bod jako koncový bod směrování pravidlo. Klikněte na tlačítko **Uložit** dole.  

    Ujistěte se, že záložní cesta je nastavena na **ON**. Toto nastavení je výchozí konfigurace služby IoT hub.

1. Ujistěte se, že předchozí aplikace stále běží. 

1. Na portálu Azure přejděte na svůj účet úložiště, v části **služby objektů Blob**, klikněte na tlačítko **procházet objekty BLOB...** .  Vyberte vašeho kontejneru, přejděte na a klikněte na soubor JSON a klikněte na tlačítko **Stáhnout** chcete zobrazit data.

## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak spolehlivě odesláním zprávy typu zařízení cloud pomocí funkce směrování zpráv služby IoT Hub.

[Odesílání zpráv typu cloud zařízení s centrem IoT] [ lnk-c2d] ukazuje, jak k odesílání zpráv do vašeho zařízení z back end vašeho řešení.

Příklady dokončení začátku do konce řešení, které pomocí služby IoT Hub, najdete v sekci [akcelerátoru řešení Azure IoT vzdálené monitorování][lnk-suite].

Další informace o vývoji řešení službou IoT Hub, najdete v článku [Příručka vývojáře pro službu IoT Hub].

Další informace o směrování zpráv do služby IoT Hub, najdete v části [odesílat a přijímat zprávy službou IoT Hub][lnk-devguide-messaging].

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[30]: ./media/iot-hub-csharp-csharp-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/route-creation.png
[33]: ./media/iot-hub-csharp-csharp-process-d2c/fallback-route.png

<!-- Links -->
[Service Bus queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/
[Příručka vývojáře pro službu IoT Hub]: iot-hub-devguide.md
[Začínáme se službou IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Azure střediska pro vývojáře IoT]: https://azure.microsoft.com/develop/iot
[přechodných chyb]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-c2d]: iot-hub-csharp-csharp-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
