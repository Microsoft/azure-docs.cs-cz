---
title: Co je Azure Event Hubs Event Processor Host a proč ji používat | Microsoft Docs
description: Přehled a úvod do Azure Event Hubs Event Processor Host
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2018
ms.author: shvija
ms.openlocfilehash: 4907004f4bb88cf0fe9fb799cab236ebf98bba7a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132862"
---
# <a name="azure-event-hubs-event-processor-host-overview"></a>Přehled služby Azure Event Hubs třídy Eventprocessorhost

Azure Event Hubs je služba přijímání výkonné telemetrická data, která slouží k datového proudu miliony událostí za nízkou cenu. Tento článek popisuje, jak využívat ingestovaný událostí pomocí *Event Processor Host* (EPH); inteligentní agent příjemce, zjednodušuje správu vytváření kontrolních bodů, leasing a čtenářů paralelní událostí.  

Klíč, který chcete škálovat pro Event Hubs je nápad oddílů příjemců. Rozdíl k [konkurenčních spotřebitelů](http://msdn.microsoft.com/en-us/library/dn568101.aspx) vzoru vzoru oddílů umožňuje vysoce škálované odebráním kolizí kritická místa a usnadnění koncová paralelismus.

## <a name="home-security-scenario"></a>Scénář domácí zabezpečení

Jako ukázkový scénář vezměte v úvahu domácí zabezpečení společnosti, která monitoruje 100 000 domácnostech. Každou minutu získává data z různých senzorů například detektor pohybu, senzor otevřete dveře nebo okna, detektor zalomení skla, atd., nainstalovaná v každé domovské. Společnost poskytuje webovou stránku pro obyvatele k monitorování aktivit s jejich domovské skoro v reálném čase.

Každý senzor by vložil data do centra událostí. Centrum událostí je nakonfigurované s 16 oddíly. Na konci náročná je nutné mechanismus, který může číst tyto události, konsolidovat je (filtr, agregační, atd.) a dump agregace do objektu blob storage, který se potom promítá do uživatelsky přívětivý webové stránky.

## <a name="write-the-consumer-application"></a>Zapište aplikaci příjemce

Při navrhování příjemce v distribuovaném prostředí, musí tento scénář zpracovávat následující požadavky:

1. **Škálování:** vytvořit více příjemců s jednotliví spotřebitelé převzetí vlastnictví čtení z několika oddílů služby Event Hubs.
2. **Vyrovnávání zatížení:** zvýšit nebo snížit uživatelé dynamicky. Například při přidání nového typu senzor (například oxidu uhelnatého detektor) na každém domovskou stránku, zvyšuje počet událostí. V takovém případě operátor (lidské) zvyšuje počet instancí příjemce. Pak fondu příjemců můžete znovu vyvážit počet oddílů, které vlastní, sdílení zatížení se nově přidané příjemci.
3. **Bezproblémové obnovení o selhání:** Pokud příjemce (**příjemce A**) selže (například hosting virtuálních počítačů najednou dojde k chybě příjemce), pak ostatní uživatelé musí být schopen vyzvednutí oddíly, které vlastní **příjemce A** a pokračovat. Navíc pokračování bodem, názvem *kontrolního bodu* nebo *posun*, musí být přesně okamžiku, kdy **příjemce A** selhání, nebo mírně dříve.
4. **Přijímat události:** při předchozí tři body se týkají správy příjemce, musí být kód využívat události a dělat něco užitečné s ním; například ho agregovat a nahrajte ho do úložiště objektů blob.

Místo vytváření vlastních řešení pro tuto, Event Hubs poskytuje tuto funkci prostřednictvím [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) rozhraní a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) třídy.

## <a name="ieventprocessor-interface"></a>IEventProcessor rozhraní

Nejprve využívání aplikací implementace [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) rozhraní, které se má čtyři metody: [OpenAsync, CloseAsync, ProcessErrorAsync a ProcessEventsAsnyc](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Toto rozhraní obsahuje skutečný kód zpracovávat události, které odesílá Event Hubs. Následující kód ukazuje jednoduchý implementace:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
       Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
       return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
       Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
       return Task.CompletedTask;
     }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
       Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
       return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
       foreach (var eventData in messages)
       {
          var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
             Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
       }
       return context.CheckpointAsync();
    }
}
```

Dále vytvořte instanci [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) instance. V závislosti na přetížení, při vytváření [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) instance v konstruktoru, se používají tyto parametry:

- **název hostitele:** název instance každý příjemce. Každá instance **EventProcessorHost** musí mít jedinečnou hodnotu této proměnné v rámci skupiny příjemců, proto je nejlepší pevného kód tuto hodnotu.
- **eventHubPath:** název centra událostí.
- **consumerGroupName:** Event Hubs využívá **$Default** jako název výchozí skupina příjemců, ale je dobrým zvykem vytvořte skupinu uživatelů pro vaše konkrétní aspekt zpracování.
- **eventHubConnectionString:** připojovací řetězec do centra událostí, které lze načíst z portálu Azure. Tento připojovací řetězec by měl mít **naslouchání** oprávnění v Centru událostí.
- **storageConnectionString:** účet úložiště používané pro správu interní prostředků.

Nakonec příjemci zaregistrovat [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) instance se službou Event Hubs. Registrace dá pokyn služby Event Hubs se očekává, že příjemce aplikace využívá události z některé z jejích oddílů a má být vyvolán [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementaci kódu vždy, když se doručí události využívat.

### <a name="example"></a>Příklad:

Jako příklad Představte si, že je 5 virtuálních počítačů (VM) vyhrazené pro použití události a jednoduché konzolové aplikace v jednotlivých virtuálních počítačů, které skutečné spotřebě funguje. Každá konzole aplikace pak vytvoří jednu [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) instance a zaregistruje ho se službou Event Hubs.

V tomto ukázkovém scénáři Řekněme, že 16 oddíly jsou přiděleny 5 **EventProcessorHost** instance. Některé **EventProcessorHost** instance může vlastní několik oddílů více než jiné. U každého oddílu, který **EventProcessorHost** vlastní instanci, vytvoří instanci `SimpleEventProcessor` třídy. Proto existují 16 instance `SimpleEventProcessor` celkové s jedním přiřazené každý oddíl.

Následující seznam shrnuje v tomto příkladu:

- 16 event Hubs oddíly.
- 5 virtuálních počítačů, 1 příjemce aplikace (například Consumer.exe) v jednotlivých virtuálních počítačů.
- 5 EPH instance registrován, 1 v jednotlivých virtuálních počítačů pomocí Consumer.exe.
- 16 `SimpleEventProcessor` objekty vytvořené 5 EPH instance.
- 1 Consumer.exe aplikace může obsahovat 4 `SimpleEventProcessor` objekty, protože 1 EPH instance může vlastní 4 oddíly.

## <a name="partition-ownership-tracking"></a>Oddíl vlastnictví sledování

Vlastnictví oddílu instanci EPH (nebo příjemce) je sledovat prostřednictvím účtu úložiště Azure, který je zadán pro sledování. Sledování jako jednoduchý, můžete vizualizovat následujícím způsobem. Prověřením objektů BLOB v části zadat účet úložiště, můžete zjistit skutečný implementace:

| **Název skupiny uživatelů** | **ID oddílu** | **Název hostitele (Vlastník)** | **Čas zapůjčení (nebo vlastnictví) získali** | **Posun v oddílu (kontrolního bodu)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | Příjemce\_VM3 | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | Příjemce\_VM4 | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | Příjemce\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | Příjemce\_VM3 | 2018-04-15T01:22:56 | 976 |

Každý hostitel tady, získá vlastnictví oddílu určitou dobu (trvání zápůjčky). Pokud hostitel selže (virtuální počítač vypne dolů), pak zapůjčení vyprší platnost. Jiní hostitelé se pokusí získat vlastnictví oddílu a jednoho z hostitelů úspěšné. Tento proces obnoví zapůjčení na oddíl se nového vlastníka. Tímto způsobem pouze jediný Čtenář současně může číst z libovolného daného oddílu v rámci skupiny příjemců.

## <a name="receive-messages"></a>Příjem zpráv

Každé volání [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) nabízí kolekci událostí. Je vaší povinností zpracování těchto událostí. Doporučujeme, abyste provedli věcí relativně rychlé; To znamená proveďte jako málo zpracování nejblíže. Místo toho použijte skupiny příjemců. Pokud potřebujete k zápisu do úložiště a provést některé směrování, je obecně lepší použít dvě skupiny uživatelů a mít dva [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementací, které spustit odděleně.

V určitém okamžiku během zpracování můžete ke sledování co máte číst a dokončit. Sledování je velmi důležité, pokud je nutné restartovat čtení, takže nemáte návratu na začátek datového proudu. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) zjednodušuje tento sledování pomocí *kontrolní body*. Kontrolní bod je umístění, nebo posun v daném oddílu v rámci skupiny dané příjemce, na bod, který se ujistí, že máte zpracování zprávy. Označení kontrolní bod v **EventProcessorHost** provádí volání [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metodu [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) objektu. Tato operace se obvykle provádí v rámci [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) metoda, ale můžete také provést [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Vytváření kontrolních bodů

[CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metoda má dva přetížení: první bez parametrů, kontrolních bodů do nejvyšší Posun události v rámci kolekce vrácené [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Tento posun je "vysoká" mez; předpokládá se, že byl zpracován všechny poslední události připojení. Pokud použijete tuto metodu tímto způsobem, mějte na paměti, předpokládá se, že je volána po vrátila jiný kód zpracování událostí. Druhý přetížení umožňuje určit [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) instance kontrolního bodu. Tato metoda umožňuje pomocí jiného typu vodoznak kontrolního bodu. Díky této vodoznak, můžete používat "nízká" mez: nejnižší sekvencované událost, která jste si jisti, byla zpracována. Toto přetížení je zajistit flexibilitu při posunutí správy.

Při provádění kontrolního bodu soubor JSON s informace specifické pro oddíl (konkrétně, posun), je zapsán do účtu úložiště, zadaný v konstruktoru [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Tento soubor se průběžně aktualizuje. Je důležité vzít v úvahu vytváření kontrolních bodů v kontextu – by se odpojuje od kontrolního bodu, všechny zprávy. Účet úložiště používané pro vytváření kontrolních bodů pravděpodobně nebude zpracování této zátěže, ale důležitější vytváření kontrolních bodů každou jednotlivou událost svědčí o zařazených do fronty zasílání zpráv vzor, pro který fronty Service Bus může být vhodnější než centra událostí. Cílem Event Hubs je získat "alespoň jednou" dodání v skvělé škálování. Tím, že vaše idempotent podřízené systémy, je snadné obnovení v případě selhání nebo restartuje tento výsledek v stejné události přijímání vícekrát.

## <a name="thread-safety-and-processor-instances"></a>Zabezpečení a procesor instancí přístup z více vláken

Ve výchozím nastavení [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) je bezpečný přístup z více vláken a chová synchronním způsobem s ohledem na instanci [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Pokud události dorazí pro oddíl, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) se volá na **IEventProcessor** instance, pro který oddílu a bude blokovat další volání **ProcessEventsAsync**pro oddíl. Následné zprávy a volání **ProcessEventsAsync** fronty na pozadí, message pump se stále spustili jiná vlákna na pozadí. Tato zabezpečení vlákna odebírá potřebu, kolekce se zabezpečenými vlákny a výrazně zvyšuje výkon.

## <a name="shut-down-gracefully"></a>Řádné ukončení

Nakonec [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) umožňuje provést čisté vypnutí všechny čtečky oddílu a by měla být volána vždy při vypínání instanci [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Tak neučiníte, může vést k prodlevám při spouštění ostatní instance **EventProcessorHost** z důvodu vypršení platnosti zapůjčení a Epoch konflikty. Správa epoch je podrobně v tomto [příspěvku na blogu](https://blogs.msdn.microsoft.com/gyan/2014/09/02/event-hubs-receiver-epoch/)

## <a name="lease-management"></a>Správa zapůjčení

Jak je popsáno dříve, v tabulce sledování výrazně zjednodušuje automatickému škálování povaha [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). Jako instanci **EventProcessorHost** spustí, co získá tolik zapůjčení a zahájí čtení událostí. Jako zapůjčení brzký konec platnosti **EventProcessorHost** pokusí obnovit je tím, že umístíte rezervace. Pokud je k dispozici pro obnovení zapůjčení, procesor pokračuje čtení, ale pokud není, zavřené čtečce a [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) je volána. **CloseAsync** je vhodná doba k provedení konečné vyčištění pro tento oddíl.

**EventProcessorHost** zahrnuje [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) vlastnost. Tato vlastnost umožňuje kontrolu nad správou zapůjčení. Tyto možnosti nastavit před registrací vaše [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementace.

## <a name="control-event-processor-host-options"></a>Možnosti řízení Event Processor Host

Kromě toho jedním přetížením [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) trvá [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) objekt jako parametr. Pomocí tohoto parametru se řídí chování [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) sám sebe. [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) definuje čtyři vlastnosti a jedna událost:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): maximální velikost kolekce, které chcete dostávat v k vyvolání [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Tato velikost není minimální, maximální velikost. Pokud jsou méně zpráv k obdržení, **ProcessEventsAsync** provede s tolik, kolik nebyly k dispozici.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): hodnota, která základní kanál AMQP slouží k určení horní limit počtu kolik zpráv klienta by měly dostávat. Tato hodnota by měla být větší než nebo rovna hodnotě [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Pokud je tento parametr **true**, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) je volána, když vyprší základní volání přijímat události na oddíl. Tato metoda je užitečná pro provádění akcí založené na čase době nečinnosti v oddílu.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): umožňuje ukazatel nebo lambda výraz funkce být nastavena, kterému se říká zajistit počáteční posun při čtečku zahájí čtení oddílu. Bez zadání tento posun, čtečka začíná na nejstarší událost, pokud soubor JSON s posunem byl uložen v zadané v rámci účtu úložiště [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) konstruktor. Tato metoda je užitečná, pokud chcete změnit chování čtečky při spuštění. Když tato metoda je volána, obsahuje parametr objektu ID oddílu, pro který bude spuštěn program pro čtení.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): vám umožní přijímat oznámení všechny základní výjimek, které se vyskytují v [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Pokud věcí nefungují podle očekávání, tato událost je vhodná k spuštění vyhledávání.

## <a name="next-steps"></a>Další postup

Teď, když jste obeznámeni s Event Processor Host, najdete další informace o službě Event Hubs v následujících článcích:

* Úvodní [Kurz služby Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Průvodce programováním pro službu Event Hubs](event-hubs-programming-guide.md)
* [Dostupnost a konzistence ve službě Event Hubs](event-hubs-availability-and-consistency.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Ukázky centra událostí na Githubu](https://github.com/Azure/azure-event-hubs/tree/master/samples)