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
ms.date: 07/03/2018
ms.author: shvija
ms.openlocfilehash: 7b5a4298ee4c67f0300bd4aabb7fc6373d8edba0
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005286"
---
# <a name="send-events-to-azure-event-hubs-using-the-net-framework"></a>Odeslání událostí do Azure Event Hubs pomocí rozhraní .NET Framework

Event Hubs je služba, která zpracovává velké objemy dat událostí (telemetrie) z připojených zařízení a aplikací. Data, která shromáždíte pomocí služby Event Hubs, můžete uložit pomocí úložného clusteru nebo transformovat pomocí zprostředkovatele datové analýzy v reálném čase. Schopnost shromažďovat a zpracovávat velké množství událostí je klíčovou komponentou moderních aplikačních architektur, například internetu věcí (Internet of Things – IoT).

Díky tomuto kurzu se dozvíte, jak pomocí webu [Azure Portal](https://portal.azure.com) vytvořit centrum událostí. Také ukazuje, jak odesílat události do centra událostí pomocí konzolové aplikace napsané v jazyce C# s použitím rozhraní .NET Framework. Pokud chcete přijímat události pomocí rozhraní .NET Framework, přečtěte si článek [Příjem událostí pomocí rozhraní .NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md) nebo klikněte na příslušný přijímající jazyk v obsahu vlevo.

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* [Microsoft Visual Studio 2017 nebo vyšší](http://visualstudio.com).
* Aktivní účet Azure. Pokud účet nemáte, můžete si ho bezplatně vytvořit během několika minut. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/free/).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Vytvoření oboru názvů Event Hubs a centra událostí

Prvním krokem je použití webu [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů v [tomto článku](event-hubs-create.md) a pak pokračujte podle následujících pokynů v tomto kurzu.

## <a name="create-a-sender-console-application"></a>Vytvoření konzolové aplikace Odesílatel

V této části napíšete konzolovou aplikaci Windows, která zasílá události do vašeho centra událostí.

1. Pomocí šablony projektu **Konzolová aplikace** vytvořte v sadě Visual Studio nový projekt desktopové aplikace Visual C#. Projekt pojmenujte **Odesílatel**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)
2. V Průzkumníku řešení klikněte pravým tlačítkem na projekt **Sender** a potom klikněte na **Spravovat balíčky NuGet pro řešení**. 
3. Klikněte na kartu **Procházet** a potom najděte `WindowsAzure.ServiceBus`. Klikněte na **Instalovat** a přijměte podmínky použití. 
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    Visual Studio stáhne a nainstaluje [balíček NuGet knihovny Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) a přidá se na něj odkaz.
4. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:
   
  ```csharp
  using System.Threading;
  using Microsoft.ServiceBus.Messaging;
  ```
5. K třídě **Program** přidejte následující pole a zástupné hodnoty nahraďte názvem centra událostí, které jste vytvořili v předchozí části, a připojovacím řetězcem na úrovni oboru názvů, který jste si předtím uložili.
   
  ```csharp
  static string eventHubName = "Your Event Hub name";
  static string connectionString = "namespace connection string";
  ```
6. Přidejte následující metodu do třídy **Program**:
   
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
7. Nakonec do metody **Main** přidejte následující řádky:
   
  ```csharp
  Console.WriteLine("Press Ctrl-C to stop the sender process");
  Console.WriteLine("Press Enter to start now");
  Console.ReadLine();
  SendingRandomMessages();
  ```
8. Spusťte program a zkontrolujte, že nejsou žádné chyby.
  
Blahopřejeme! Nyní jste odeslali zprávy do centra událostí.

## <a name="next-steps"></a>Další postup

Gratulujeme, sestavili jste funkční aplikaci, která vytvoří centrum událostí a odesílá data. Nyní se můžete podívat na některý z následujících scénářů:

* [Příjem událostí pomocí třídy EventProcessorHost](event-hubs-dotnet-framework-getstarted-receive-eph.md)
* [Referenční informace ke třídě EventProcessorHost](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)
* [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

