---
title: Odeslání událostí do Azure Event Hubs pomocí rozhraní .NET Framework | Dokumentace Microsoftu
description: Začínáme s odesíláním událostí do služby Event Hubs pomocí rozhraní .NET Framework
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: shvija
ms.openlocfilehash: 8ee351973dbedab4664d08e9e7a5a7ac67c8416b
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52958474"
---
# <a name="send-events-to-azure-event-hubs-using-the-net-framework"></a>Odeslání událostí do Azure Event Hubs pomocí rozhraní .NET Framework
Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

Tento kurz ukazuje, jak odesílat události do centra událostí pomocí konzolové aplikace napsané v jazyce C# pomocí rozhraní .NET Framework. 

## <a name="prerequisites"></a>Požadavky
Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* [Microsoft Visual Studio 2017 nebo vyšší](https://visualstudio.com).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Vytvoření oboru názvů Event Hubs a centra událostí
Prvním krokem je použití webu [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů v [tomto článku](event-hubs-create.md) a pak pokračujte podle následujících pokynů v tomto kurzu.

Získání připojovacího řetězce pro obor názvů centra událostí podle pokynů v článku: [získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Připojovací řetězec použijete později v tomto kurzu.

## <a name="create-a-console-application"></a>Vytvoření konzolové aplikace

Pomocí šablony projektu **Konzolová aplikace** vytvořte v sadě Visual Studio nový projekt desktopové aplikace Visual C#. Projekt pojmenujte **Odesílatel**.
   
![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)

## <a name="add-the-event-hubs-nuget-package"></a>Přidání balíčku NuGet služby Event Hubs

1. V Průzkumníku řešení klikněte pravým tlačítkem na projekt **Sender** a potom klikněte na **Spravovat balíčky NuGet pro řešení**. 
2. Klikněte na kartu **Procházet** a potom najděte `WindowsAzure.ServiceBus`. Klikněte na **Instalovat** a přijměte podmínky použití. 
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    Visual Studio stáhne a nainstaluje [balíček NuGet knihovny Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) a přidá se na něj odkaz.

## <a name="write-code-to-send-messages-to-the-event-hub"></a>Napsání kódu pro odesílání zpráv do centra událostí

1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:
   
    ```csharp
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```
2. K třídě **Program** přidejte následující pole a zástupné hodnoty nahraďte názvem centra událostí, které jste vytvořili v předchozí části, a připojovacím řetězcem na úrovni oboru názvů, který jste si předtím uložili. Připojovací řetězec můžete zkopírovat z centra událostí **připojovací řetězec – primární** klíč **RootManageSharedAccessKey** na stránce centra událostí na webu Azure Portal. Podrobné pokyny najdete v článku [získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal).
   
    ```csharp
    static string eventHubName = "Your Event Hub name";
    static string connectionString = "namespace connection string";
    ```
3. Přidejte následující metodu do třídy **Program**:
   
      ```csharp
      static void SendingRandomMessages()
      {
          var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
          while (true)
          {
              try
              {
                  var message = Guid.NewGuid().ToString();
                  Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                  eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
              }
              catch (Exception exception)
              {
                  Console.ForegroundColor = ConsoleColor.Red;
                  Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                  Console.ResetColor();
              }
       
              Thread.Sleep(200);
          }
      }
      ```
   
      Tato metoda neustále odesílá události do vašeho centra událostí se zpožděním 200 ms.
4. Nakonec do metody **Main** přidejte následující řádky:
   
      ```csharp
      Console.WriteLine("Press Ctrl-C to stop the sender process");
      Console.WriteLine("Press Enter to start now");
      Console.ReadLine();
      SendingRandomMessages();
      ```
5. Spusťte program a zkontrolujte, že nejsou žádné chyby.
  
Blahopřejeme! Nyní jste odeslali zprávy do centra událostí.

## <a name="next-steps"></a>Další postup
V tomto rychlém startu jste odeslali zprávy do centra událostí pomocí rozhraní .NET Framework. Se naučíte přijímat události z centra událostí pomocí rozhraní .NET Framework, naleznete v tématu [přijímat události z centra událostí - rozhraní .NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md).

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

