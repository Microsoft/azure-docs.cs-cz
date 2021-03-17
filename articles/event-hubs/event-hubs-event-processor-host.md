---
title: Příjem událostí pomocí procesoru událostí hostitel – Azure Event Hubs | Microsoft Docs
description: Tento článek popisuje hostitele procesoru událostí v Azure Event Hubs, který zjednodušuje správu kontrolních bodů, zapůjčení a čtení událostí s paralelním hostováním.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: de5d8f0f8bf9f64a473b18a50434cac83e8e38c3
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98622058"
---
# <a name="event-processor-host"></a>EventProcessorHost
> [!NOTE]
> Tento článek se týká starší verze sady Azure Event Hubs SDK. Aktuální verzi sady SDK najdete v tématu [Vyrovnávání zatížení oddílu napříč několika instancemi vaší aplikace](event-processor-balance-partition-load.md). Informace o tom, jak migrovat kód do novější verze sady SDK, najdete v těchto příručkách k migraci. 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)
> - [Skript Java](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)

Azure Event Hubs je výkonná služba pro příjem telemetrie, která se dá použít ke streamování milionů událostí s nízkými náklady. Tento článek popisuje, jak používat příjmové události pomocí modulu *Event Processor Host* (EPH); Inteligentní Agent pro příjemce, který zjednodušuje správu kontrolních bodů, leasingu a paralelních čtecích zařízení událostí.  

Klíčem k horizontálnímu navýšení kapacity pro Event Hubs je nápad rozdělit uživatele na oddíly. Na rozdíl od vzorce [konkurenčních spotřebitelů](/previous-versions/msp-n-p/dn568101(v=pandp.10)) umožňuje vzor rozděleného uživatele vysoké škálování odebráním kritických bodů pro spor a zjednodušením koncového paralelismu.

## <a name="home-security-scenario"></a>Scénář zabezpečení domácích

Jako ukázkový scénář si představte firemní zabezpečení, které monitoruje 100 000 domů. Každou minutu získává data z různých senzorů, jako je detektor pohybu, dveř/okno Open snímač, detektor skla atd., nainstalovaný v každé domácnosti. Společnost poskytuje web pro rezidenty, které sledují činnost svých domů téměř v reálném čase.

Každý senzor nahraje data do centra událostí. Centrum událostí je nakonfigurované s 16 oddíly. Na náročném cíli budete potřebovat mechanismus, který může číst tyto události, konsolidovat je (filtrovat, agregovat atd.) a vypsat agregovanou hodnotu do objektu BLOB úložiště, který se pak prochází na uživatelsky přívětivou webovou stránku.

## <a name="write-the-consumer-application"></a>Zápis aplikace příjemce

Při návrhu spotřebitele v distribuovaném prostředí musí tento scénář splňovat následující požadavky:

1. **Měřítko:** Vytvořte více uživatelů, přičemž každý příjemce převezme vlastnictví čtení z několika oddílů Event Hubs.
2. **Vyrovnávání zatížení:** Dynamické zvýšení nebo snížení počtu příjemců Například při přidání nového typu snímače (např. oxidu uhelnat) do každé domů se zvýší počet událostí. V takovém případě operátor (lidské) zvyšuje počet instancí příjemců. Fond příjemců pak může znovu vyrovnávat počet oddílů, které vlastní, a sdílet zatížení s nově přidanými spotřebiteli.
3. **Bezproblémové obnovení při selhání:** Pokud se příjemce (**uživatel a**) nepodaří (například virtuální počítač, který hostuje uživatele), musí být schopný vybírat oddíly vlastněné **zákazníkem** a a pokračovat. Bod pokračování, označovaný jako *kontrolní bod* nebo *posun*, by měl být v přesném okamžiku, kdy se **příjemce a** nezdařil, nebo mírně předtím.
4. **Spotřebovávat události:** Zatímco předchozí tři body se týkají správy příjemce, musí existovat kód pro využívání událostí a co nejužitečnější je s ním. například agreguje a nahrajte ho do úložiště objektů BLOB.

Namísto sestavování vlastního řešení pro tento Event Hubs poskytuje tuto funkci prostřednictvím rozhraní [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) a třídy [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) .

## <a name="ieventprocessor-interface"></a>Rozhraní IEventProcessor

Nejprve spotřebovávají aplikace, které implementují rozhraní  [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) , které má čtyři metody: [openAsync, CloseAsync, ProcessErrorAsync a ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor#methods). Toto rozhraní obsahuje skutečný kód pro využití událostí, které Event Hubs odesílá. Následující kód ukazuje jednoduchou implementaci:

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

Nakonec budou spotřebitelé registrovat instanci [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) ve službě Event Hubs. Registrace třídy procesoru událostí s instancí EventProcessorHost spustí zpracování událostí. Registrace vydá pokyn službě Event Hubs k tomu, aby čekala, že aplikace příjemce spotřebovává události z některých jeho oddílů, a vyvolá implementační kód [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) pokaždé, když vloží události, které se mají spotřebovat. 

> [!NOTE]
> ConsumerGroupName rozlišuje velká a malá písmena.  Změny consumerGroupName můžou mít za následek čtení všech oddílů od začátku streamu.

### <a name="example"></a>Příklad

Představte si například, že je 5 virtuálních počítačů vyhrazených pro náročné události a v každém virtuálním počítači je jednoduchá Konzolová aplikace, která funguje jako skutečná spotřeba. Každá Konzolová aplikace potom vytvoří jednu instanci [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) a registruje ji ve službě Event Hubs.

V tomto ukázkovém scénáři řekněme, že pro 5 instancí **EventProcessorHost** jsou přiděleny 16 oddílů. Některé instance **EventProcessorHost** můžou vlastnit několik dalších oddílů než jiné. Pro každý oddíl, který vlastní instance **EventProcessorHost** , vytvoří instanci `SimpleEventProcessor` třídy. Proto existuje 16 instancí `SimpleEventProcessor` celkově s přiřazeným jednotlivými oddíly.

Následující seznam shrnuje tento příklad:

- 16 Event Hubs oddílů.
- 5 virtuálních počítačů, 1 aplikace příjemce (například Consumer.exe) v každém virtuálním počítači.
- 5 EPH instancí zaregistrovaných, 1 v každém virtuálním počítači podle Consumer.exe.
- 16 `SimpleEventProcessor` objektů vytvořených pomocí 5 instancí EPH.
- 1 Consumer.exe aplikace může obsahovat 4 `SimpleEventProcessor` objekty, protože 1 instance EPH může vlastnit 4 oddíly.

## <a name="partition-ownership-tracking"></a>Sledování vlastnictví oddílu

Vlastnictví oddílu na instanci EPH (nebo příjemce) se sleduje prostřednictvím účtu Azure Storage, který je k dispozici pro sledování. Sledování můžete vizualizovat jako jednoduchou tabulku, a to následujícím způsobem. Aktuální implementaci můžete zobrazit prozkoumáním objektů BLOB v poskytnutém účtu úložiště:

| **Název skupiny uživatelů** | **ID oddílu** | **Název hostitele (Vlastník)** | **Zapůjčení (nebo vlastnictví) – čas získání** | **Posun v oddílu (kontrolní bod)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | \_VM3 příjemce | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | \_VM4 příjemce | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | \_VM0 příjemce | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | \_VM3 příjemce | 2018-04-15T01:22:56 | 976 |

V tomto případě získá každý hostitel vlastnictví oddílu po určitou dobu (trvání zapůjčení). Pokud dojde k chybě hostitele (virtuální počítač se vypne), zapůjčení vyprší. Jiní hostitelé se pokoušejí získat vlastnictví oddílu a jeden z hostitelů je úspěšný. Tento proces obnoví zapůjčení pro oddíl novým vlastníkem. Tímto způsobem může v jednom okamžiku číst jenom jedno čtecí zařízení v rámci skupiny uživatelů.

## <a name="receive-messages"></a>Příjem zpráv

Každé volání [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) poskytuje kolekci událostí. Je vaše zodpovědnost za zpracování těchto událostí. Pokud se chcete ujistit, že hostitel procesoru zpracuje každou zprávu alespoň jednou, je nutné napsat vlastní kód, který se bude pokoušet o opakovaný pokus. Buďte ale opatrní v souvislosti s poškozenými zprávami.

Doporučujeme, abyste provedete relativně rychle. To znamená co nejmenší zpracování. Místo toho použijte skupiny uživatelů. Pokud potřebujete zapisovat do úložiště a udělat si nějaké směrování, je lepší použít dvě skupiny uživatelů a mít dvě [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementace, které se spouštějí samostatně.

V určitém okamžiku během zpracování si možná budete chtít udržet přehled o tom, co jste si přečetli a dokončili. Sledování je důležité, pokud musíte restartovat čtení, takže se nevrátíte na začátek proudu. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) zjednodušuje toto sledování pomocí *kontrolních bodů*. Kontrolní bod je umístěním nebo posunutím pro daný oddíl v rámci dané skupiny uživatelů, v němž jste spokojeni s tím, že jste zpracovali zprávy. Označení kontrolního bodu v **EventProcessorHost** je provedeno voláním metody [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) na objektu [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) . Tato operace se provádí v rámci metody [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) , ale lze ji také provést v [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Vytváření kontrolních bodů

Metoda [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) má dvě přetížení: první, bez parametrů, kontrolní body na nejvyšší posun události v rámci kolekce vrácené funkcí [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Tento posun představuje "vysokou vodu"; předpokládá, že jste při volání zpracovali všechny nedávné události. Pokud tuto metodu použijete tímto způsobem, počítejte s tím, že se očekává, že ji budete volat poté, co vrátí jiný kód pro zpracování událostí. Druhé přetížení umožňuje zadat instanci [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) k kontrolnímu bodu. Tato metoda umožňuje použít pro kontrolní bod jiný typ vodoznaku. Pomocí tohoto meze můžete implementovat "" nízkou vodu "značku: nejnižší sekvencovaná událost, kterou jste si povedli zpracovat. Toto přetížení je k dispozici pro umožnění flexibility při správě posunu.

Po provedení kontrolního bodu se soubor JSON, který má informace specifické pro oddíl (konkrétně posun), zapíše do účtu úložiště, který je zadaný v konstruktoru, do [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Tento soubor se průběžně aktualizuje. Vytváření kontrolních bodů v kontextu je velmi důležité – proto by bylo vhodné při každé zprávě vytvořit kontrolní bod. Účet úložiště, který se používá pro kontrolní bod, pravděpodobně toto zatížení nezpracovává, ale důležitější je, že každá jediná událost je podrobnější, jako je vzor zasílání zpráv ve frontě, pro který Service Bus frontu může být lepší volbou než centrum událostí. Nápad za Event Hubs je, že se vám dostanete aspoň jednou doručování ve skvělém měřítku. Díky tomu, že vaše systémy pro příjem dat idempotentní, je snadné se zotavit z chyb nebo restartovat, což vede k tomu, že se stejné události přijímají vícekrát.

## <a name="thread-safety-and-processor-instances"></a>Bezpečnost vlákna a instance procesoru

Ve výchozím nastavení je [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) vlákn bezpečný a pracuje synchronním způsobem s ohledem na instanci [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Po doručení událostí pro oddíl se [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) volá v instanci **IEventProcessor** pro daný oddíl a zablokuje se další volání **ProcessEventsAsync** pro oddíl. Následné zprávy a volání **ProcessEventsAsync** zařadí do fronty na pozadí, protože čerpadlo zpráv nadále běží na pozadí v jiných vláknech. Tato bezpečnost vlákna odstraňuje nutnost pro kolekce bezpečné pro přístup z více vláken a významně zvyšuje výkon.

## <a name="shut-down-gracefully"></a>Řádně vypnout

Nakonec [EventProcessorHost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) umožňuje čisté vypnutí všech čtenářů oddílů a mělo by být vždy voláno při vypínání instance [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). V takovém případě může dojít k prodlevám při spouštění jiných instancí **EventProcessorHost** z důvodu vypršení platnosti zapůjčení a konfliktů epocha. Správa Epocha se podrobněji zabývá v části [epocha](#epoch) článku. 

## <a name="lease-management"></a>Správa zapůjčení
Registrace třídy procesoru událostí s instancí EventProcessorHost spustí zpracování událostí. Instance hostitele získá zapůjčení na některých oddílech centra událostí, což může vést k obdržení některých instancí hostitelů, a to způsobem, který se sblížen na rovnoměrné distribuci oddílů napříč všemi instancemi hostitelů. U každého pronajatého oddílu Vytvoří instance hostitele instanci zadané třídy procesoru událostí a pak přijme události z tohoto oddílu a předává je do instance procesoru událostí. Jak se přidávají další instance, a další zapůjčení se EventProcessorHost, nakonec se zatížení rozdělí mezi všechny příjemce.

Jak bylo vysvětleno dřív, tabulka sledování značně zjednodušuje charakter automatického škálování [EventProcessorHost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). Když se spustí instance **EventProcessorHost** , získá co nejvíce zapůjčených prostředků a začne číst události. Když se zapůjčení blíží k vypršení platnosti, **EventProcessorHost** se pokusí je obnovit umístěním rezervace. Pokud je zapůjčení k dispozici pro obnovení, procesor pokračuje ve čtení, ale pokud není, čtecí modul se zavře a zavolá se [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) . **CloseAsync** je vhodný čas k provedení konečného vyčištění tohoto oddílu.

**EventProcessorHost** zahrnuje vlastnost [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) . Tato vlastnost umožňuje řídit správu zapůjčení. Před registrací implementace [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) nastavte tyto možnosti.

## <a name="control-event-processor-host-options"></a>Řízení možností hostitele procesoru událostí

Kromě toho jedno přetížení [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) přebírá objekt [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) jako parametr. Tento parametr použijte k řízení chování samotného [EventProcessorHost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) . [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) definuje čtyři vlastnosti a jednu událost:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): maximální velikost kolekce, kterou chcete přijmout při vyvolání [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Tato velikost není minimální, jenom maximální velikost. Pokud je k dispozici méně zpráv, **ProcessEventsAsync** se spustí s tolik, kolik bylo k dispozici.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): hodnota, kterou používá podkladový kanál AMQP k určení horního limitu počtu zpráv, které by měl klient přijímat. Tato hodnota by měla být větší nebo rovna [maxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Pokud je tento parametr **true**, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) se volá, když se v podkladovém volání dostanou události do oddílu vyprší časový limit. Tato metoda je užitečná pro provádění akcí založených na čase během období nečinnosti v oddílu.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): umožňuje nastavit ukazatel na funkci nebo výraz lambda, který se volá za účelem poskytnutí počátečního posunu, když čtenář začne číst oddíl. Bez zadání tohoto posunu čtenář spustí nejstarší událost, pokud už soubor JSON s posunem není uložený v účtu úložiště, který je zadaný do konstruktoru [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) . Tato metoda je užitečná v případě, že chcete změnit chování při spuštění čtecího zařízení. Při vyvolání této metody obsahuje parametr objektu ID oddílu, pro který se čtecí modul spouští.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): umožňuje dostávat oznámení o všech základních výjimkách, ke kterým dochází v [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Pokud věci nefungují podle očekávání, je tato událost dobrým místem, kde začít hledat.

## <a name="epoch"></a>Epocha

Tady je postup, jak epocha Receive funguje:

### <a name="with-epoch"></a>S epocha
Epocha je jedinečný identifikátor (hodnota epocha), kterou služba používá, aby se vynutilo vlastnictví oddílu nebo zapůjčení. K vytvoření přijímače založeného na epocha použijte metodu [CreateEpochReceiver](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver) . Tato metoda vytvoří přijímač založené na epocha. Příjemce se vytvoří pro konkrétní oddíl centra událostí ze zadané skupiny uživatelů.

Funkce epocha poskytuje uživatelům možnost zajistit, že ve skupině příjemců je v libovolném časovém okamžiku pouze jeden přijímač, a to s následujícími pravidly:

- Pokud není ve skupině příjemců žádný příjemce, může uživatel vytvořit přijímač s libovolnou hodnotou epocha.
- Pokud je k dispozici přijímač s hodnotou epocha E1 a vytvoří se nový přijímač s hodnotou epocha E2, kde E1 <= E2, bude se přijímač s E1 automaticky odpojit a bude se úspěšně vytvářet přijímač s objektem E2.
- Pokud je k dispozici přijímač s hodnotou epocha E1 a vytvoří se nový příjemce s hodnotou epocha E2, kde E1 > E2, pak vytvoření objektu E2 s chybou se nezdaří a dojde k chybě: příjemce s epocha E1 již existuje.

### <a name="no-epoch"></a>Žádné epocha
Pomocí metody [CreateReceiver](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver) vytvoříte přijímač, který není založen na epocha. 

V případě zpracování datových proudů existuje několik scénářů, kdy uživatelé chtějí vytvořit více přijímačů v jedné skupině příjemců. Pro podporu takových scénářů máme možnost vytvořit přijímač bez epocha a v tomto případě povolíme až 5 souběžných přijímačů ve skupině příjemců.

### <a name="mixed-mode"></a>Smíšený režim
Nedoporučujeme používat aplikace, kde jste vytvořili přijímač s epocha a pak na stejné skupině příjemců přešli na No-epocha nebo naopak. Nicméně když dojde k tomuto chování, služba ho zpracuje pomocí následujících pravidel:

- Pokud již byl vytvořen přijímač s epocha E1 a aktivně přijímá události a vytvoří se nový příjemce bez epocha, vytvoření nového přijímače se nezdaří. Epocha příjemci mají vždy přednost v systému.
- Pokud byl již vytvořen přijímač s epocha E1 a byl odpojen a vytvoří se nový příjemce bez epocha na novém MessagingFactory, vytváření nového přijímače bude úspěšné. Tady se dozvíte, že po uplynutí 10 minut náš systém zjistí "odpojení přijímače".
- Pokud je vytvořeno jedno nebo více přijímačů bez epocha a vytvoří se nový příjemce s epocha E1, všichni původní příjemci se odpojí.


> [!NOTE]
> Pro aplikace, které používají epochs, doporučujeme používat různé skupiny uživatelů, které nepoužívají epochs, aby se předešlo chybám. 


## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámeni s hostitelem procesoru událostí, přečtěte si následující články, kde se dozvíte víc o Event Hubs:

- Začínáme se službou Event Hubs
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
* [Průvodce programováním pro službu Event Hubs](event-hubs-programming-guide.md)
* [Dostupnost a konzistence ve službě Event Hubs](event-hubs-availability-and-consistency.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Ukázky Event Hubs na GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples)
