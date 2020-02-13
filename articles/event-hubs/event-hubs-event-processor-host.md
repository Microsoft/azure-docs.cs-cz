---
title: Příjem událostí pomocí třídy Event Processor Host – Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek popisuje třídy Event Processor Host v Azure Event Hubs, což zjednodušuje správu vytváření kontrolních bodů, pronájem a ikona paralelní čtení událostí.
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
ms.custom: seodec18
ms.date: 01/10/2020
ms.author: shvija
ms.openlocfilehash: 414179d62970315a7575be0411bf1cb152349fdc
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162289"
---
# <a name="event-processor-host"></a>EventProcessorHost
> [!NOTE]
> Tento článek se týká starší verze sady Azure Event Hubs SDK. Informace o tom, jak migrovat kód do novější verze sady SDK, najdete v těchto příručkách k migraci. 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MIGRATIONGUIDE.md)
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)
> - [Skript Java](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)
>
> Viz také [Vyrovnávání zatížení oddílu napříč několika instancemi aplikace](event-processor-balance-partition-load.md).

Azure Event Hubs je služba pro ingestování výkonné telemetrická data, která slouží k streamujte miliony událostí za nízkou cenu. Tento článek popisuje, jak používat příjmové události pomocí modulu *Event Processor Host* (EPH); Inteligentní Agent pro příjemce, který zjednodušuje správu kontrolních bodů, leasingu a paralelních čtecích zařízení událostí.  

Klíč, který chcete škálovat pro službu Event Hubs je dělené příjemců. Na rozdíl od vzorce [konkurenčních spotřebitelů](https://msdn.microsoft.com/library/dn568101.aspx) umožňuje vzor rozděleného uživatele vysoké škálování odebráním kritických bodů pro spor a zjednodušením koncového paralelismu.

## <a name="home-security-scenario"></a>Scénář domácí zabezpečení

Jako ukázkový scénář vezměte v úvahu domácí zabezpečení společnosti, která monitoruje 100 000 nemovitostí. Každou minutu získá data z různých senzorů například motion detectoru, otevřete dveře/okno senzor, detektor konec lupy, atd., nainstalovaná v každé Domovská stránka. Společnost poskytuje webovou stránku pro obyvatele monitorovat aktivitu jejich domovské téměř v reálném čase.

Každý ze senzorů odesílá data do centra událostí. Centrum událostí je nakonfigurované s 16 oddíly. Na konci náročné potřebujete mechanismus, který může číst tyto události, sloučit (filtrování, agregace, atd.) a vypsat agregace do objektu blob storage, který je pak promítá do uživatelsky přívětivé webové stránky.

## <a name="write-the-consumer-application"></a>Napíšeme aplikaci příjemce

Při navrhování příjemce v distribuovaném prostředí, tento scénář musí zpracovávat následující požadavky:

1. **Měřítko:** Vytvořte více uživatelů, přičemž každý příjemce převezme vlastnictví čtení z několika oddílů Event Hubs.
2. **Vyrovnávání zatížení:** Dynamické zvýšení nebo snížení počtu příjemců Například při přidání nového typu senzor (například uhelnatého k oxidu detektor) na každý domovskou stránku, zvyšuje počet událostí. V takovém případě – operátor (lidské) zvýší počet instancí příjemce. Potom fond příjemců dokáží obnovit rovnováhu počet oddílů, které vlastní, sdílení zatížení se nově přidané zákazníky.
3. **Bezproblémové obnovení při selhání:** Pokud se příjemce (**uživatel a**) nepodaří (například virtuální počítač, který hostuje uživatele), musí být schopný vybírat oddíly vlastněné **zákazníkem** a a pokračovat. Bod pokračování, označovaný jako *kontrolní bod* nebo *posun*, by měl být v přesném okamžiku, kdy se **příjemce a** nezdařil, nebo mírně předtím.
4. **Spotřebovávat události:** Zatímco předchozí tři body se týkají správy příjemce, musí existovat kód pro využívání událostí a co nejužitečnější je s ním. například agreguje a nahrajte ho do úložiště objektů BLOB.

Namísto sestavování vlastního řešení pro tento Event Hubs poskytuje tuto funkci prostřednictvím rozhraní [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) a třídy [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) .

## <a name="ieventprocessor-interface"></a>Rozhraní IEventProcessor

Nejprve spotřebovávají aplikace, které implementují rozhraní [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) , které má čtyři metody: [openAsync, CloseAsync, ProcessErrorAsync a ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Toto rozhraní obsahuje skutečný kód pro zpracování událostí, které odešle služby Event Hubs. Následující kód ukazuje jednoduchý implementace:

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

Dále vytvořte instanci instance [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) . V závislosti na přetížení se při vytváření instance [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) v konstruktoru používají následující parametry:

- název **hostitele:** název každé instance příjemce. Každá instance **EventProcessorHost** musí mít jedinečnou hodnotu pro tuto proměnnou v rámci skupiny příjemců, takže tuto hodnotu neprovádějte.
- **eventHubPath:** Název centra událostí
- **consumerGroupName:** Event Hubs používá **$Default** jako název výchozí skupiny uživatelů, ale je dobrým zvykem vytvořit skupinu uživatelů pro konkrétní aspekt zpracování.
- **eventHubConnectionString:** Připojovací řetězec k centru událostí, který lze načíst z Azure Portal. Tento připojovací řetězec by měl mít oprávnění k **naslouchání** v centru událostí.
- **storageConnectionString:** Účet úložiště používaný pro interní správu prostředků

Nakonec budou spotřebitelé registrovat instanci [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) ve službě Event Hubs. Registrace procesoru třídě události s instancí třídy EventProcessorHost spustí zpracování událostí. Registrace vydá pokyn službě Event Hubs k tomu, aby čekala, že aplikace příjemce spotřebovává události z některých jeho oddílů, a vyvolá implementační kód [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) pokaždé, když vloží události, které se mají spotřebovat. 


### <a name="example"></a>Příklad

Jako příklad, představte si, že je 5 virtuálních počítačů (VM) vyhrazený pro použití událostí a jednoduchou konzolovou aplikaci v každém virtuálním počítači, který skutečného využití, které funguje. Každá Konzolová aplikace potom vytvoří jednu instanci [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) a registruje ji ve službě Event Hubs.

V tomto ukázkovém scénáři řekněme, že pro 5 instancí **EventProcessorHost** jsou přiděleny 16 oddílů. Některé instance **EventProcessorHost** můžou vlastnit několik dalších oddílů než jiné. Pro každý oddíl, který vlastní instance **EventProcessorHost** , vytvoří instanci třídy `SimpleEventProcessor`. Proto existuje 16 instancí `SimpleEventProcessor` celkově, přičemž jedna je přiřazena každému oddílu.

Následující seznam shrnuje v tomto příkladu:

- 16 oddílů služby event Hubs.
- 5 virtuálních počítačů, 1 spotřebitele aplikací (například Consumer.exe) do každého virtuálního počítače.
- 5 instancí EPH zaregistrovaný do každého virtuálního počítače podle Consumer.exe 1.
- 16 `SimpleEventProcessor` objektů vytvořených pomocí 5 instancí EPH.
- 1 aplikace příjemce. exe může obsahovat 4 `SimpleEventProcessor` objekty, protože 1 instance EPH může vlastnit 4 oddíly.

## <a name="partition-ownership-tracking"></a>Oddíl vlastnictví sledování

Vlastnictví oddílu na instanci EPH (nebo příjemce) jsou sledovány pomocí účtu služby Azure Storage, která je k dispozici pro sledování. Pro sledování jako jednoduchou tabulku, můžete vizualizovat takto. Zobrazí se skutečná implementace porovnáním objektů BLOB v účtu úložiště k dispozici:

| **Název skupiny uživatelů** | **ID oddílu** | **Název hostitele (Vlastník)** | **Zapůjčení (nebo vlastnictví) – čas získání** | **Posun v oddílu (kontrolní bod)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | Uživatel\_VM3 | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | Uživatel\_VM4 | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | Uživatel\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | Uživatel\_VM3 | 2018-04-15T01:22:56 | 976 |

Každý hostitel, získá vlastnictví oddílu určitou dobu (doba trvání zapůjčení). Pokud z hostitelů selže (virtuální počítač vypne), pak vyprší platnost zapůjčení. Jiní hostitelé pokusit získat vlastnictví oddílu a jednoho z hostitelů proběhne úspěšně. Tento proces obnoví zapůjčení na oddíl s nového vlastníka. Tímto způsobem pouze jediný Čtenář najednou můžete číst z libovolného daného oddílu v rámci skupiny příjemců.

## <a name="receive-messages"></a>Příjem zpráv

Každé volání [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) poskytuje kolekci událostí. Je vaší odpovědností, abyste zpracování těchto událostí. Pokud se chcete ujistit, že hostitel procesoru zpracuje každou zprávu alespoň jednou, je nutné napsat vlastní kód, který se bude pokoušet o opakovaný pokus. Buďte ale opatrní v souvislosti s poškozenými zprávami.

Doporučujeme, abyste udělali poměrně rychle; co potřebujete To znamená proveďte jako zpracování co nejvíc. Místo toho použijte skupiny příjemců. Pokud potřebujete zapisovat do úložiště a udělat si nějaké směrování, je lepší použít dvě skupiny uživatelů a mít dvě [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementace, které se spouštějí samostatně.

V určitém okamžiku během zpracování můžete sledovat, co jste přečetli a dokončit. Sledování je velmi důležité, pokud je nutné restartovat čtení, takže se nemusíte vrátit k začátku datového proudu. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) zjednodušuje toto sledování pomocí *kontrolních bodů*. Kontrolní bod je umístění, nebo posunutí pro daný oddíl, v rámci konkrétní skupiny příjemců, v tom okamžiku se ujistí, že mají zpracovat zprávy. Označení kontrolního bodu v **EventProcessorHost** je provedeno voláním metody [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) na objektu [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) . Tato operace se provádí v rámci metody [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) , ale lze ji také provést v [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Vytváření kontrolních bodů

Metoda [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) má dvě přetížení: první, bez parametrů, kontrolní body na nejvyšší posun události v rámci kolekce vrácené funkcí [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Tento posun je znak "nejvyšší hodnota"; předpokládá se, že byly zpracovány všechny nedávné události při jeho volání. Pokud použijete tuto metodu tímto způsobem, mějte na paměti, že se očekává, je volána po vrátila jiný kód zpracování události. Druhé přetížení umožňuje zadat instanci [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) k kontrolnímu bodu. Tuto metodu můžete použít jiný typ vodoznaku kontrolního bodu. S tuto mez můžete implementovat "dolní meze" mark: nejnižší sekvencované událost, která jste si jisti, se zpracovalo. Toto přetížení umožňující flexibilitu při posunu správy.

Po provedení kontrolního bodu se soubor JSON, který má informace specifické pro oddíl (konkrétně posun), zapíše do účtu úložiště, který je zadaný v konstruktoru, do [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Tento soubor se průběžně aktualizuje. Je důležité vzít v úvahu vytváření kontrolních bodů v kontextu – se odpojuje od kontrolního bodu, všechny zprávy. Účet úložiště používané pro vytváření kontrolních bodů pravděpodobně nebude tato zátěž zvládla, ale ještě důležitější vytváření kontrolních bodů každou jednotlivou událost je indikátorem zařazených do fronty zasílání zpráv vzor, pro kterou fronty Service Bus může být vhodnější než centra událostí. Myšlenku za služby Event Hubs je, že dostanete "alespoň jednou" ve velmi velkém měřítku. Tím, že vaše podřízených systémů idempotentní, je snadné zotavení z chyb nebo restartuje výsledek ve stejné události, které přijímají více než jednou.

## <a name="thread-safety-and-processor-instances"></a>Vlákno bezpečnosti a procesor instance

Ve výchozím nastavení je [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) vlákn bezpečný a pracuje synchronním způsobem s ohledem na instanci [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Po doručení událostí pro oddíl se [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) volá v instanci **IEventProcessor** pro daný oddíl a zablokuje se další volání **ProcessEventsAsync** pro oddíl. Následné zprávy a volání **ProcessEventsAsync** zařadí do fronty na pozadí, protože čerpadlo zpráv nadále běží na pozadí v jiných vláknech. Zabezpečení tohoto vlákna eliminuje potřebu kolekce bezpečné pro vlákna a výrazně zvyšuje výkon.

## <a name="shut-down-gracefully"></a>Řádné ukončení

Nakonec [EventProcessorHost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) umožňuje čisté vypnutí všech čtenářů oddílů a mělo by být vždy voláno při vypínání instance [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). V takovém případě může dojít k prodlevám při spouštění jiných instancí **EventProcessorHost** z důvodu vypršení platnosti zapůjčení a konfliktů epocha. Správa Epocha se podrobněji zabývá v části [epocha](#epoch) článku. 

## <a name="lease-management"></a>Řízení pronájmu
Registrace procesoru třídě události s instancí třídy EventProcessorHost spustí zpracování událostí. Instance hostitele získává zapůjčení u některých oddílů centra událostí, případně kliknete na některé z dalších hostitelské instance, tak, aby sladila na rozdělení oddílů napříč všemi instancemi hostitele. Pro každý oddíl pronajatých instance hostitele vytvoří instanci třídy procesoru zadané události, pak přijímá události z tohoto oddílu a předává je do instance procesoru událostí. Nechejte se přidat více instancí a jsou obstaral větší počet zapůjčení, EventProcessorHost nakonec vyrovnává zatížení mezi všechny uživatele.

Jak bylo vysvětleno dřív, tabulka sledování značně zjednodušuje charakter automatického škálování [EventProcessorHost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). Když se spustí instance **EventProcessorHost** , získá co nejvíce zapůjčených prostředků a začne číst události. Když se zapůjčení blíží k vypršení platnosti, **EventProcessorHost** se pokusí je obnovit umístěním rezervace. Pokud je zapůjčení k dispozici pro obnovení, procesor pokračuje ve čtení, ale pokud není, čtecí modul se zavře a zavolá se [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) . **CloseAsync** je vhodný čas k provedení konečného vyčištění tohoto oddílu.

**EventProcessorHost** zahrnuje vlastnost [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) . Tato vlastnost umožňuje kontrolu nad správou zapůjčení. Před registrací implementace [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) nastavte tyto možnosti.

## <a name="control-event-processor-host-options"></a>Možnosti Event Processor Host ovládacího prvku

Kromě toho jedno přetížení [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) přebírá objekt [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) jako parametr. Tento parametr použijte k řízení chování samotného [EventProcessorHost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) . [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) definuje čtyři vlastnosti a jednu událost:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): maximální velikost kolekce, kterou chcete přijmout při vyvolání [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Tato velikost není minimální, maximální velikost. Pokud je k dispozici méně zpráv, **ProcessEventsAsync** se spustí s tolik, kolik bylo k dispozici.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): hodnota, kterou používá podkladový kanál AMQP k určení horního limitu počtu zpráv, které by měl klient přijímat. Tato hodnota by měla být větší nebo rovna [maxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Pokud je tento parametr **true**, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) se volá, když se v podkladovém volání dostanou události do oddílu vyprší časový limit. Tato metoda je užitečná pro provádění akcí založených na čase během období nečinnosti v oddílu.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): umožňuje nastavit ukazatel na funkci nebo výraz lambda, který se volá za účelem poskytnutí počátečního posunu, když čtenář začne číst oddíl. Bez zadání tohoto posunu čtenář spustí nejstarší událost, pokud už soubor JSON s posunem není uložený v účtu úložiště, který je zadaný do konstruktoru [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) . Tato metoda je užitečná, pokud chcete změnit chování čtečky při spuštění. Po vyvolání tato metoda obsahuje parametr objektu ID oddílu, pro který se spouští čtecí modul.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): umožňuje dostávat oznámení o všech základních výjimkách, ke kterým dochází v [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Pokud kroky nefungují podle očekávání, tato událost je dobrým začátkem hledání.

## <a name="epoch"></a>Epocha

Tady je postup, jak epocha Receive funguje:

### <a name="with-epoch"></a>S epocha
Epocha je jedinečný identifikátor (hodnota epocha), kterou služba používá, aby se vynutilo vlastnictví oddílu nebo zapůjčení. K vytvoření přijímače založeného na epocha použijte metodu [CreateEpochReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver?view=azure-dotnet) . Tato metoda vytvoří přijímač založené na epocha. Příjemce se vytvoří pro konkrétní oddíl centra událostí ze zadané skupiny uživatelů.

Funkce epocha poskytuje uživatelům možnost zajistit, že ve skupině příjemců je v libovolném časovém okamžiku pouze jeden přijímač, a to s následujícími pravidly:

- Pokud není ve skupině příjemců žádný příjemce, může uživatel vytvořit přijímač s libovolnou hodnotou epocha.
- Pokud je k dispozici přijímač s hodnotou epocha E1 a vytvoří se nový přijímač s hodnotou epocha E2, kde E1 < = E2, bude se přijímač s E1 automaticky odpojit a bude se úspěšně vytvářet přijímač s objektem E2.
- Pokud je k dispozici přijímač s hodnotou epocha E1 a vytvoří se nový příjemce s hodnotou epocha E2, kde E1 > E2, pak vytvoření objektu E2 s chybou se nezdaří a dojde k chybě: příjemce s epocha E1 již existuje.

### <a name="no-epoch"></a>Žádné epocha
Pomocí metody [CreateReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver?view=azure-dotnet) vytvoříte přijímač, který není založen na epocha. 

V případě zpracování datových proudů existuje několik scénářů, kdy uživatelé chtějí vytvořit více přijímačů v jedné skupině příjemců. Pro podporu takových scénářů máme možnost vytvořit přijímač bez epocha a v tomto případě povolíme až 5 souběžných přijímačů ve skupině příjemců.

### <a name="mixed-mode"></a>Smíšený režim
Nedoporučujeme používat aplikace, kde jste vytvořili přijímač s epocha a pak na stejné skupině příjemců přešli na No-epocha nebo naopak. Nicméně když dojde k tomuto chování, služba ho zpracuje pomocí následujících pravidel:

- Pokud již byl vytvořen přijímač s epocha E1 a aktivně přijímá události a vytvoří se nový příjemce bez epocha, vytvoření nového přijímače se nezdaří. Epocha příjemci mají vždy přednost v systému.
- Pokud byl již vytvořen přijímač s epocha E1 a byl odpojen a vytvoří se nový příjemce bez epocha na novém MessagingFactory, vytváření nového přijímače bude úspěšné. Tady se dozvíte, že po uplynutí 10 minut náš systém zjistí "odpojení přijímače".
- Pokud je vytvořeno jedno nebo více přijímačů bez epocha a vytvoří se nový příjemce s epocha E1, všichni původní příjemci se odpojí.


> [!NOTE]
> Pro aplikace, které používají epochs, doporučujeme používat různé skupiny uživatelů, které nepoužívají epochs, aby se předešlo chybám. 


## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámeni s Event Processor Host, naleznete v následujících článcích se dozvíte víc o službě Event Hubs:

- Začínáme se službou Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Průvodce programováním pro službu Event Hubs](event-hubs-programming-guide.md)
* [Dostupnost a konzistence ve službě Event Hubs](event-hubs-availability-and-consistency.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Ukázky Event Hubs na GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples)
