---
title: Ukázek Azure Event Hubs | Microsoft Docs
description: Ukázek Azure Event Hubs
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2018
ms.author: sethm
ms.openlocfilehash: 9d2c38ac589e5120441daf972217e61738fd57a1
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131351"
---
# <a name="event-hubs-samples"></a>Ukázky centra událostí 

Sada ukázek Azure Event Hubs ukazuje klíčové funkce v [Azure Event Hubs](/azure/event-hubs/). Tento článek rozděluje a popisuje, k dispozici, s odkazy na všechny ukázky.

V době psaní tohoto textu Event Hubs ukázky umístěny v několika různých místech:

- [Ukázky kódu vývojáře MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5)
- [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)

Další informace o různé verze rozhraní .NET Framework najdete v tématu [architektury a cíle](/dotnet/articles/standard/frameworks).

Další ukázky bude přidán v čase, tak zkontrolujte, vraťte se sem často aktualizací.

## <a name="net-standard"></a>Standardní rozhraní .NET

Následující ukázky ukazují, jak odesílat a přijímat události pomocí [klienta služby Event Hubs](https://github.com/Azure/azure-event-hubs-dotnet/blob/master/readme.md) pro [.NET standardní knihovna](/dotnet/articles/standard/library).

### <a name="send-events"></a>Odesílání událostí 

[Začínáme odesílání](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) příklad ukazuje, jak psát aplikace konzoly .NET Core, která zasílá události do centra událostí.

### <a name="receive-events"></a>Příjem událostí 

[Začít pracovat s Event Processor Host přijetí](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) ukázka je aplikace konzoly .NET Core, která přijímá zprávy z centra událostí pomocí Event Processor Host.

## <a name="net-framework"></a>.NET Framework   

Tyto ukázky demonstrují různým funkcím služby Azure Event Hubs, cílení [rozhraní .NET Framework – knihovna](/dotnet/framework/index).
 
### <a name="notify-users-of-events-received"></a>Upozorněte uživatele událostí přijatých

[AppToNotifyUsers](https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications) ukázka upozorní uživatele z dat přijatých ze senzorů nebo jinými systémy.

### <a name="get-started-with-event-hubs"></a>Začínáme se službou Event Hubs 

[Event Hubs Začínáme](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097) příklad ukazuje základní možnosti služby Event Hubs, například vytvoření centra událostí, odesílání událostí do centra událostí a využívat událostí pomocí [Event Processor Host](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) .

### <a name="scale-out-event-processing"></a>Horizontální navýšení kapacity zpracování událostí 

[Horizontální navýšení kapacity zpracování událostí](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3) příklad ukazuje způsob použití [Event Processor Host](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) distribuovat zatížení spotřeby Event Hubs datového proudu. Ukazuje, jak implementovat **EventProcessor** a **EventProcessorFactory** objektů ke správě datového proudu událostí. 

## <a name="next-steps"></a>Další postup

Další informace o rozhraní .NET Framework verze když přejdete na následujících odkazech:

- [Rozhraní a cíle](/dotnet/articles/standard/frameworks)
- [Rozhraní .NET framework 4.6 a 4.5](/dotnet/framework/index)

Se více o službě Event Hubs v těchto článcích:

- [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
- [Funkce Event Hubs](event-hubs-features.md)
- [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)