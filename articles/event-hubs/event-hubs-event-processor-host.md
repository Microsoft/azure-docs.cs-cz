---
title: Co je Azure Event Hubs Event Processor Host a proč ji používat | Dokumentace Microsoftu
description: Přehled a úvod do služby Azure Event Hubs Event Processor Host
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
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 236103861ce8a296c77f708dbb4a7cc7e03f10f3
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258948"
---
# <a name="azure-event-hubs-event-processor-host-overview"></a>Přehled služby Azure Event Hubs Event Processor Host

Azure Event Hubs je služba pro ingestování výkonné telemetrická data, která slouží k streamujte miliony událostí za nízkou cenu. Tento článek popisuje, jak využívat přijaté události pomocí *Event Processor Host* (EPH); inteligentní agent příjemce, který zjednodušuje správu vytváření kontrolních bodů, pronájem a čtenáři paralelní události.  

Klíč, který chcete škálovat pro službu Event Hubs je dělené příjemců. Rozdíl od [konkurenčních spotřebitelů](https://msdn.microsoft.com/library/dn568101.aspx) vzor, vzor oddělených příjemců je pravidlo umožňuje vysoce škálované odebráním kritický bod kolize a usnadnění začátku do konce paralelismu.

## <a name="home-security-scenario"></a>Scénář domácí zabezpečení

Jako ukázkový scénář vezměte v úvahu domácí zabezpečení společnosti, která monitoruje 100 000 nemovitostí. Každou minutu získá data z různých senzorů například motion detectoru, otevřete dveře/okno senzor, detektor konec lupy, atd., nainstalovaná v každé Domovská stránka. Společnost poskytuje webovou stránku pro obyvatele monitorovat aktivitu jejich domovské téměř v reálném čase.

Každý ze senzorů odesílá data do centra událostí. Centrum událostí je nakonfigurované s 16 oddíly. Na konci náročné potřebujete mechanismus, který může číst tyto události, sloučit (filtrování, agregace, atd.) a vypsat agregace do objektu blob storage, který je pak promítá do uživatelsky přívětivé webové stránky.

## <a name="write-the-consumer-application"></a>Napíšeme aplikaci příjemce

Při navrhování příjemce v distribuovaném prostředí, tento scénář musí zpracovávat následující požadavky:

1. **Škálování:** vytvořit několik příjemců, s všichni příjemci převzetí vlastnictví čtení z několika oddílů služby Event Hubs.
2. **Vyrovnávání zatížení:** zvětšete nebo zmenšete dynamicky spotřebitele. Například při přidání nového typu senzor (například uhelnatého k oxidu detektor) na každý domovskou stránku, zvyšuje počet událostí. V takovém případě – operátor (lidské) zvýší počet instancí příjemce. Potom fond příjemců dokáží obnovit rovnováhu počet oddílů, které vlastní, sdílení zatížení se nově přidané zákazníky.
3. **Bezproblémové obnovení na selhání:** Pokud příjemce (**příjemce A**) selže (třeba virtuální počítač hostování náhle dojde k chybě příjemce), pak ostatní uživatelé musí mít ke sbírání oddíly vlastněné **příjemce A** a pokračovat. Navíc pokračování bod, volá se *kontrolního bodu* nebo *posun*, by měla být v okamžiku, kdy **příjemce A** neúspěšné, nebo o něco dříve.
4. **Zpracování událostí:** při předchozí tři body starat o správu spotřebitele, musí být kód používat události a užitečné s ním něco udělat; například ho agregovat a nahrajte ho do úložiště objektů blob.

Místo vytváření vlastních řešení pro to, Event Hubs dokáže tuto funkci prostřednictvím [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) rozhraní a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) třídy.

## <a name="ieventprocessor-interface"></a>Rozhraní IEventProcessor

Nejprve využívání aplikace implementovat [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) rozhraní, která má čtyři metody: [OpenAsync, CloseAsync, ProcessErrorAsync a ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Toto rozhraní obsahuje skutečný kód pro zpracování událostí, které odešle služby Event Hubs. Následující kód ukazuje jednoduchý implementace:

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

V dalším kroku vytvoření instance [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) instance. V závislosti na přetížení, při vytváření [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) instance v konstruktoru, se používají následující parametry:

- **název hostitele:** název instance jednotlivých uživatelů. Každá instance **EventProcessorHost** musí mít jedinečnou hodnotu této proměnné v rámci skupiny příjemců, takže je vhodné nechcete pevně kód tuto hodnotu.
- **eventHubPath:** název centra událostí.
- **Název:** Služba Event Hubs využívá **$Default** jako název výchozí skupinu příjemců, ale je vhodné vytvořit skupinu uživatelů pro specifické aspekty zpracování.
- **eventHubConnectionString:** připojovací řetězec do centra událostí, která se dá načíst z portálu Azure portal. Tento připojovací řetězec by měl mít **naslouchání** oprávnění v Centru událostí.
- **storageConnectionString:** účet úložiště používané pro správu vnitřních prostředků.

Nakonec příjemci zaregistrovat [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) instance se službou Event Hubs. Registrace procesoru třídě události s instancí třídy EventProcessorHost spustí zpracování událostí. Registrace služby Event Hubs můžete očekávat, že příjemce aplikace využívá službu události z některé z jejích oddílů a který má být vyvolán nastaví [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementace kódu pokaždé, když se toho odesílá události využívat. 


### <a name="example"></a>Příklad:

Jako příklad, představte si, že je 5 virtuálních počítačů (VM) vyhrazený pro použití událostí a jednoduchou konzolovou aplikaci v každém virtuálním počítači, který skutečného využití, které funguje. Každý konzolovou aplikaci pak vytvoří jedna [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) instance a zaregistruje ho ve službě Event Hubs.

V tomto ukázkovém scénáři, Řekněme, že 16 oddíly jsou přiděleny 5 **EventProcessorHost** instancí. Některé **EventProcessorHost** instance může vlastnit pár dalších oddílů než jiné. Pro každý oddíl, který **EventProcessorHost** vlastní instance, vytvoří instanci `SimpleEventProcessor` třídy. Proto existují 16 instance `SimpleEventProcessor` celkové s jedním přiřazené do jednotlivých oddílů.

Následující seznam shrnuje v tomto příkladu:

- 16 oddílů služby event Hubs.
- 5 virtuálních počítačů, 1 spotřebitele aplikací (například Consumer.exe) do každého virtuálního počítače.
- 5 instancí EPH zaregistrovaný do každého virtuálního počítače podle Consumer.exe 1.
- 16 `SimpleEventProcessor` objekty vytvořené 5 EPH instancí.
- 1 Consumer.exe aplikace může obsahovat 4 `SimpleEventProcessor` objekty, protože 1 EPH instance může vlastnit 4 oddíly.

## <a name="partition-ownership-tracking"></a>Oddíl vlastnictví sledování

Vlastnictví oddílu na instanci EPH (nebo příjemce) jsou sledovány pomocí účtu služby Azure Storage, která je k dispozici pro sledování. Pro sledování jako jednoduchou tabulku, můžete vizualizovat takto. Zobrazí se skutečná implementace porovnáním objektů BLOB v účtu úložiště k dispozici:

| **Název skupiny uživatelů** | **ID oddílu** | **Název hostitele (Vlastník)** | **Doba zapůjčení (nebo vlastnictví) získat** | **Posun v oddílu (kontrolního bodu)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | Příjemce\_VM3 | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | Příjemce\_VM4 | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | Příjemce\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | Příjemce\_VM3 | 2018-04-15T01:22:56 | 976 |

Každý hostitel, získá vlastnictví oddílu určitou dobu (doba trvání zapůjčení). Pokud z hostitelů selže (virtuální počítač vypne), pak vyprší platnost zapůjčení. Jiní hostitelé pokusit získat vlastnictví oddílu a jednoho z hostitelů proběhne úspěšně. Tento proces obnoví zapůjčení na oddíl s nového vlastníka. Tímto způsobem pouze jediný Čtenář najednou můžete číst z libovolného daného oddílu v rámci skupiny příjemců.

## <a name="receive-messages"></a>Příjem zpráv

Každé volání [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) nabízí kolekci událostí. Je vaší odpovědností, abyste zpracování těchto událostí. Doporučujeme, abyste udělali poměrně rychle; co potřebujete To znamená proveďte jako zpracování co nejvíc. Místo toho použijte skupiny příjemců. Pokud potřebujete k zápisu do úložiště a provést některé směrování, je obecně vhodnější použít dvě skupiny uživatelů a mít dvě [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementace, které spustit samostatně.

V určitém okamžiku během zpracování můžete sledovat, co jste přečetli a dokončit. Sledování je velmi důležité, pokud je nutné restartovat čtení, takže se nemusíte vrátit k začátku datového proudu. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) zjednodušuje tento sledování pomocí *kontrolní body*. Kontrolní bod je umístění, nebo posunutí pro daný oddíl, v rámci konkrétní skupiny příjemců, v tom okamžiku se ujistí, že mají zpracovat zprávy. Označení kontrolní bod v **EventProcessorHost** provádí volání [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metodu [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) objektu. Tato operace se provádí v rámci [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) metody, ale je možné provést [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Vytváření kontrolních bodů

[CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metoda má dvě přetížení: první, bez parametrů, kontrolních bodů do nejvyšší události posun v kolekci vrácené poskytovatelem [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Tento posun je znak "nejvyšší hodnota"; předpokládá se, že byly zpracovány všechny nedávné události při jeho volání. Pokud použijete tuto metodu tímto způsobem, mějte na paměti, že se očekává, je volána po vrátila jiný kód zpracování události. Druhé přetížení umožňuje určit [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) instance se kontrolní bod. Tuto metodu můžete použít jiný typ vodoznaku kontrolního bodu. S tuto mez můžete implementovat "dolní meze" mark: nejnižší sekvencované událost, která jste si jisti, se zpracovalo. Toto přetížení umožňující flexibilitu při posunu správy.

Při provádění kontrolního bodu na soubor JSON s informace specifické pro oddíl (konkrétně posun), je zapsána do účtu úložiště zadaný v konstruktoru [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Tento soubor se průběžně aktualizuje. Je důležité vzít v úvahu vytváření kontrolních bodů v kontextu – se odpojuje od kontrolního bodu, všechny zprávy. Účet úložiště používané pro vytváření kontrolních bodů pravděpodobně nebude tato zátěž zvládla, ale ještě důležitější vytváření kontrolních bodů každou jednotlivou událost je indikátorem zařazených do fronty zasílání zpráv vzor, pro kterou fronty Service Bus může být vhodnější než centra událostí. Myšlenku za služby Event Hubs je, že dostanete "alespoň jednou" ve velmi velkém měřítku. Tím, že vaše podřízených systémů idempotentní, je snadné zotavení z chyb nebo restartuje výsledek ve stejné události, které přijímají více než jednou.

## <a name="thread-safety-and-processor-instances"></a>Vlákno bezpečnosti a procesor instance

Ve výchozím nastavení [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) je vlákno v bezpečí a chová způsobem synchronní, s ohledem na instanci [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Pokud události dorazí pro oddíl, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) je volán na **IEventProcessor** instance, která oddílu a další volání budou blokovat **ProcessEventsAsync**pro oddíl. Následné zprávy a volání **ProcessEventsAsync** fronty na pozadí pumpu zpráv se stále běžet na pozadí v jiných vláknech. Zabezpečení tohoto vlákna eliminuje potřebu kolekce bezpečné pro vlákna a výrazně zvyšuje výkon.

## <a name="shut-down-gracefully"></a>Řádné ukončení

Nakonec [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) umožňuje čistého vypnutí všech oddílů čtenářů a by měla být volána vždy při vypínání instance [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Pokud tak neučiníte, může vést k prodlevám při spuštění další výskyty **EventProcessorHost** kvůli vypršení platnosti zapůjčení a epocha konflikty. Unixový správy je podrobně popsána v to [blogový příspěvek](https://blogs.msdn.microsoft.com/gyan/2014/09/02/event-hubs-receiver-epoch/)

## <a name="lease-management"></a>Řízení pronájmu
Registrace procesoru třídě události s instancí třídy EventProcessorHost spustí zpracování událostí. Instance hostitele získává zapůjčení u některých oddílů centra událostí, případně kliknete na některé z dalších hostitelské instance, tak, aby sladila na rozdělení oddílů napříč všemi instancemi hostitele. Pro každý oddíl pronajatých instance hostitele vytvoří instanci třídy procesoru zadané události, pak přijímá události z tohoto oddílu a předává je do instance procesoru událostí. Nechejte se přidat více instancí a jsou obstaral větší počet zapůjčení, EventProcessorHost nakonec vyrovnává zatížení mezi všechny uživatele.

Jak jsme vysvětlili dříve, tabulky sledování výrazně zjednodušuje automatického škálování povaze [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). Jako instanci **EventProcessorHost** spustí, získá zapůjčení tolik nejrychleji a zahájí čtení událostí. Jako zapůjčení brzký konec platnosti **EventProcessorHost** pokusí obnovit tak, že rezervaci. Pokud je k dispozici pro obnovení zapůjčení, procesor bude pokračovat ve čtení, ale pokud není, zavření čtečky a [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) je volána. **CloseAsync** je vhodná doba provést libovolné finální čištění pro tento oddíl.

**EventProcessorHost** zahrnuje [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) vlastnost. Tato vlastnost umožňuje kontrolu nad správou zapůjčení. Tyto možnosti nastavit před registrací vašeho [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementace.

## <a name="control-event-processor-host-options"></a>Možnosti Event Processor Host ovládacího prvku

Kromě toho jednomu přetížení [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) přebírá [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) objektu jako parametr. Tento parametr slouží k řízení chování [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) samotný. [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) definuje čtyři vlastnosti a jednu událost:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): maximální velikost kolekce vám mají zobrazovat v vyvolání [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Tato velikost není minimální, maximální velikost. Pokud jsou méně zpráv pro další přijetí, **ProcessEventsAsync** pomocí provádí tolik, kolik byly k dispozici.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): mělo by se zobrazit hodnotu v základním kanálu AMQP použít k určení horní limit počtu zpráv klienta. Tato hodnota by měla být větší než nebo rovna hodnotě [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Pokud je tento parametr **true**, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) se volá, když podkladové volání pro příjem událostí v oddílu vyprší časový limit. Tato metoda je užitečná pro provádění akcí založených na čase během období nečinnosti v oddílu.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): Povolí funkce ukazatel nebo lambda výraz, který má nastavení, která je volána k poskytnutí počáteční posun při čtečku zahájí čtení oddílu. Bez zadání tento posun, čtečky začíná nejstarší událost, pokud soubor JSON s posunem již byla uložena v účtu úložiště zadaný pro [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) konstruktoru. Tato metoda je užitečná, pokud chcete změnit chování čtečky při spuštění. Po vyvolání tato metoda obsahuje parametr objektu ID oddílu, pro který se spouští čtecí modul.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): umožňuje dostávat upozornění na jakékoli základní výjimky, ke kterým dochází v [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Pokud kroky nefungují podle očekávání, tato událost je dobrým začátkem hledání.

## <a name="next-steps"></a>Další postup

Teď, když jste obeznámeni s Event Processor Host, naleznete v následujících článcích se dozvíte víc o službě Event Hubs:

* Úvodní [Kurz služby Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Průvodce programováním pro službu Event Hubs](event-hubs-programming-guide.md)
* [Dostupnost a konzistence ve službě Event Hubs](event-hubs-availability-and-consistency.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Ukázky služby Event Hubs na Githubu](https://github.com/Azure/azure-event-hubs/tree/master/samples)
