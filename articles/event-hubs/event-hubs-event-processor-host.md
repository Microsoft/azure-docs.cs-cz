---
title: Přijímat události pomocí hostitele procesoru událostí – Centra událostí Azure | Dokumenty společnosti Microsoft
description: Tento článek popisuje hostitele procesoru událostí v Azure Event Hubs, což zjednodušuje správu vytváření kontrolních bodů, leasing u a čtení událostí iontové paralelní.
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
ms.openlocfilehash: 485f51e45e342ca28d54d609fd975bef5b204f7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372226"
---
# <a name="event-processor-host"></a>EventProcessorHost
> [!NOTE]
> Tento článek se vztahuje na starou verzi sady Azure Event Hubs SDK. Informace o migraci kódu do novější verze sady SDK naleznete v těchto průvodcích migrace. 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)
> - [Java skript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)
>
> Viz [Také vtématit zatížení oddílu mezi více instancí aplikace](event-processor-balance-partition-load.md).

Azure Event Hubs je výkonná služba příjem telemetrie, kterou se dá použít k streamování milionů událostí s nízkými náklady. Tento článek popisuje, jak využívat ingestované události pomocí *hostitele procesoru událostí* (EPH); inteligentní ho agent a spotřebitel, který zjednodušuje správu kontrolních bodů, leasingu a paralelních čteček událostí.  

Klíčem k škálování pro event huby je myšlenka rozdělených spotřebitelů. Na rozdíl od [konkurenčních spotřebitelů](https://msdn.microsoft.com/library/dn568101.aspx) vzor, rozdělený spotřebitelský vzor umožňuje vysoké měřítko odstraněním konflikty úzkým hrdlem a usnadnění min.-end paralelismus.

## <a name="home-security-scenario"></a>Scénář zabezpečení domácnosti

Jako příklad scénáře zvažte domácí bezpečnostní společnost, která monitoruje 100 000 domácností. Každou minutu získává data z různých senzorů, jako je detektor pohybu, senzor otevřených dveří / oken, detektor rozbití skla atd., instalovaný v každé domácnosti. Společnost poskytuje webové stránky pro obyvatele sledovat činnost svého domova v téměř reálném čase.

Každý senzor odesílá data do centra událostí. Centrum událostí je nakonfigurováno s 16 oddíly. Na náročném konci potřebujete mechanismus, který může číst tyto události, konsolidovat je (filtr, agregaci atd.) a vypisovat agregaci do objektu blob úložiště, který se pak promítá na uživatelsky přívětivou webovou stránku.

## <a name="write-the-consumer-application"></a>Napsat spotřebitelskou aplikaci

Při navrhování příjemce v distribuovaném prostředí scénář musí zpracovat následující požadavky:

1. **Měřítko:** Vytvořte více spotřebitelů, přičemž každý spotřebitel převezme vlastnictví čtení z několika oddílů Centra událostí.
2. **Vyvážení zatížení:** Zvyšujte nebo snižujte spotřebitele dynamicky. Například při přidání nového typu senzoru (například detektoru oxidu uhelnatého) do každé domácnosti se počet událostí zvyšuje. V takovém případě operátor (člověk) zvyšuje počet instancí příjemce. Potom fond spotřebitelů můžete vyvážit počet oddílů, které vlastní, sdílet zatížení s nově přidané spotřebitele.
3. **Bezproblémový životopis při selhání:** Pokud spotřebitel (**spotřebitel A)** selže (například virtuální počítač hostující spotřebitele náhle havaruje), musí být ostatní spotřebitelé schopni vyzvednout oddíly vlastněné **spotřebitelem A** a pokračovat. Také bod pokračování, nazvaný *kontrolní bod* nebo *posun*, by měl být v přesném bodě, ve kterém **příjemce A** selhal, nebo mírně před tím.
4. **Spotřebovávat události:** Zatímco předchozí tři body se zabývají řízením spotřebitele, musí existovat kód, který spotřebuje události a udělá s ním něco užitečného; agregujte ho například a nahrajte do úložiště objektů blob.

Místo vytváření vlastního řešení pro toto, Event Hubs poskytuje tuto funkci prostřednictvím rozhraní [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) třídy.

## <a name="ieventprocessor-interface"></a>Rozhraní IEventProcessor

Za prvé, náročné aplikace implementovat rozhraní [IEventProcessor,](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) který má čtyři metody: [OpenAsync, CloseAsync, ProcessErrorAsync a ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Toto rozhraní obsahuje skutečný kód využívat události, které odesílá centra událostí. Následující kód ukazuje jednoduchou implementaci:

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

Dále vytvořte instanci [EventProcessorHost.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) V závislosti na přetížení se při vytváření instance [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) v konstruktoru používají následující parametry:

- **hostName:** název každé instance příjemce. Každá instance **EventProcessorHost** musí mít jedinečnou hodnotu pro tuto proměnnou v rámci skupiny spotřebitelů, takže tuto hodnotu nezakódujte.
- **eventHubPath:** Název centra událostí.
- **consumerGroupName:** Centra událostí používá **$Default** jako název výchozí skupiny spotřebitelů, ale je vhodné vytvořit skupinu spotřebitelů pro konkrétní aspekt zpracování.
- **eventHubConnectionString:** Připojovací řetězec do centra událostí, které lze načíst z portálu Azure. Tento připojovací řetězec by měl mít oprávnění **naslouchat** v centru událostí.
- **storageConnectionString:** Účet úložiště používaný pro interní správu prostředků.

Nakonec spotřebitelé zaregistrovat [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) instance se službou Event Hubs. Registrace třídy procesoru událostí s instancí EventProcessorHost spustí zpracování událostí. Registrace instruuje službu Event Hubs očekávat, že aplikace příjemce spotřebovává události z některých svých oddílů a vyvolat kód implementace [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) vždy, když tlačí události využívat. 


### <a name="example"></a>Příklad

Jako příklad si představte, že existuje 5 virtuálních počítačů (VM) určených pro náročné události a jednoduché konzolové aplikace v každém virtuálním počítači, který provádí skutečnou spotřebu práce. Každá konzolová aplikace pak vytvoří jednu instanci [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) a zaregistruje ji pomocí služby Event Hubs.

V tomto příkladu scénáře řekněme, že 16 oddílů jsou přiděleny 5 **EventProcessorHost** instance. Některé instance **EventProcessorHost** mohou vlastnit několik dalších oddílů než jiné. Pro každý oddíl, který vlastní instance **EventProcessorHost,** `SimpleEventProcessor` vytvoří instanci třídy. Proto existuje 16 instancí celkově, `SimpleEventProcessor` s jedním přiřazena každý oddíl.

Následující seznam shrnuje tento příklad:

- 16 oddílů event hubů.
- 5 virtuálních připojení, 1 spotřebitelská aplikace (například Consumer.exe) v každém virtuálním jevu.
- 5 registrovaných instancí EPH, 1 v každém virtuálním virtuálním ms podle consumer.exe.
- 16 `SimpleEventProcessor` objektů vytvořených 5 instancemi EPH.
- 1 Aplikace Consumer.exe `SimpleEventProcessor` může obsahovat 4 objekty, protože instance 1 EPH může vlastnit 4 oddíly.

## <a name="partition-ownership-tracking"></a>Sledování vlastnictví oddílu

Vlastnictví oddílu k instanci EPH (nebo spotřebiteli) se sleduje prostřednictvím účtu Azure Storage, který je k dispozici pro sledování. Sledování můžete vizualizovat jako jednoduchou tabulku následujícím způsobem. Můžete zobrazit skutečné implementace kontrolou objekty BLOB pod účet úložiště za předpokladu:

| **Název skupiny uživatelů** | **ID oddílu** | **Jméno hostitele (vlastník)** | **Pronájem (nebo vlastnictví) nabytý čas** | **Posun v oddílu (kontrolní bod)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | Spotřebitel\_VM3 | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | Spotřebitel\_Virtuální VM4 | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | Spotřebitel\_Virtuální měl nauce 0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | Spotřebitel\_VM3 | 2018-04-15T01:22:56 | 976 |

Zde každý hostitel získá vlastnictví oddílu po určitou dobu (doba trvání zapůjčení). Pokud hostitel selže (virtuální hod se vypne), vyprší platnost zapůjčení. Ostatní hostitelé se pokusí získat vlastnictví oddílu a jeden z hostitelů uspěje. Tento proces obnoví zapůjčení na oddíl u nového vlastníka. Tímto způsobem pouze jeden čtenář najednou může číst z libovolného oddílu v rámci skupiny spotřebitelů.

## <a name="receive-messages"></a>Příjem zpráv

Každé volání [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) poskytuje kolekci událostí. Je vaší odpovědností zvládnout tyto události. Pokud se chcete ujistit, že hostitel procesoru zpracovává každou zprávu alespoň jednou, musíte napsat vlastní kód keep retrying. Ale buďte opatrní ohledně otrávených zpráv.

Doporučuje se dělat věci poměrně rychle; to znamená, že dělat co nejméně zpracování, jak je to možné. Místo toho použijte skupiny spotřebitelů. Pokud potřebujete zapisovat do úložiště a provést některé směrování, je lepší použít dvě skupiny spotřebitelů a mít dvě implementace [IEventProcessor,](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) které běží samostatně.

V určitém okamžiku během zpracování můžete chtít sledovat, co jste četli a dokončili. Sledování je důležité, pokud je nutné restartovat čtení, takže se nemusíte vracet na začátek datového proudu. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) zjednodušuje toto sledování pomocí *kontrolních bodů*. Kontrolní bod je umístění nebo posun pro daný oddíl v rámci dané skupiny spotřebitelů, v tomto okamžiku jste přesvědčeni, že jste zpracovali zprávy. Označení kontrolního bodu v **EventProcessorHost** se provádí voláním metody [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) na objektu [PartitionContext.](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) Tato operace se provádí v rámci [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) metoda, ale lze provést také v [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Vytváření kontrolních bodů

[CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) Metoda má dvě přetížení: první, bez parametrů, kontrolní body na nejvyšší posun události v rámci kolekce vrácené [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Tento posun je značka "vysoké vody"; předpokládá, že jste při volání zpracovali všechny nedávné události. Pokud použijete tuto metodu tímto způsobem, uvědomte si, že se očekává, že ji zavoláte po vrácení kódu zpracování jiné události. Druhé přetížení umožňuje zadat instanci [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) do kontrolního bodu. Tato metoda umožňuje použít jiný typ vodoznaku kontrolního bodu. Pomocí tohoto vodoznaku můžete implementovat značku "nízká voda": nejnižší sekvenční událost, o které jste si jisti, že byla zpracována. Toto přetížení je k dispozici umožnit flexibilitu v správě posunu.

Při provádění kontrolního bodu je soubor JSON s informacemi specifickými pro oddíl (konkrétně posun) zapsán do účtu úložiště dodaného v konstruktoru [eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Tento soubor je průběžně aktualizován. Je důležité zvážit kontrolní body v kontextu - by bylo nerozumné kontrolní bod každé zprávy. Účet úložiště používaný pro vytváření kontrolních bodů pravděpodobně nebude zpracovávat toto zatížení, ale co je důležitější, kontrolní body každou jednotlivou událost svědčí o vzorku zasílání zpráv ve frontě, pro který fronta service bus může být lepší volbou než centrum událostí. Myšlenka event hubů spoáčita je, že dostanete doručení "alespoň jednou" ve velkém měřítku. Tím, že vaše navazující systémy idempotentní, je snadné obnovit z chyb nebo restartování, které mají za následek stejné události přijaté vícekrát.

## <a name="thread-safety-and-processor-instances"></a>Instance bezpečnosti vláken a procesoru

Ve výchozím nastavení je [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) bezpečný pro přístup z více vláken a chová se synchronně s ohledem na instanci [iEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Když události dorazí pro oddíl, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) je volána na instanci **IEventProcessor** pro tento oddíl a bude blokovat další volání **ProcessEventsAsync** pro oddíl. Následné zprávy a volání **ProcessEventsAsync** fronty na pozadí jako čerpadlo zprávy nadále běžet na pozadí v jiných vláknech. Tato bezpečnost podprocesu odstraňuje potřebu kolekce bezpečné pro přístup z více vláken a výrazně zvyšuje výkon.

## <a name="shut-down-gracefully"></a>Řádně vypnout

Nakonec [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) umožňuje čisté vypnutí všech čteček oddílů a měl by být vždy volán při vypínání instance [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Pokud tak neučiníte, může způsobit zpoždění při spuštění jiných instancí **EventProcessorHost** z důvodu vypršení platnosti zapůjčení a epochy konflikty. Epocha řízení je podrobně popsáno v sekci [Epocha](#epoch) článku. 

## <a name="lease-management"></a>Správa pronájmu
Registrace třídy procesoru událostí s instancí EventProcessorHost spustí zpracování událostí. Instance hostitele získá zapůjčení na některé oddíly Event Hub, případně popadat některé z jiných instancí hostitele, a to způsobem, který se konverguje na rovnoměrné rozdělení oddílů ve všech instancích hostitele. Pro každý zapůjčený oddíl instance hostitele vytvoří instanci zapředpokladuované třídy procesoru událostí, pak obdrží události z tohoto oddílu a předá je instanci procesoru událostí. Jak více instancí získat přidány a další zapůjčení jsou zachyceny, EventProcessorHost nakonec vyrovnává zatížení mezi všemi spotřebiteli.

Jak již bylo vysvětleno dříve, tabulka sledování výrazně zjednodušuje automatické škálování povahu [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). Jako instance **EventProcessorHost** spustí, získá co nejvíce zapůjčení, jak je to možné a začne čtení událostí. Jako zapůjčení blízko vypršení platnosti **EventProcessorHost** se pokusí obnovit jejich umístěním rezervace. Pokud zapůjčení je k dispozici pro obnovení, procesor pokračuje ve čtení, ale pokud tomu tak není, čtečka je uzavřena a [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) je volána. **CloseAsync** je vhodná doba k provedení konečné vyčištění pro tento oddíl.

**EventProcessorHost** obsahuje vlastnost [PartitionManagerOptions.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) Tato vlastnost umožňuje kontrolu nad správou zapůjčení. Před registrací implementace [procesoru IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) nastavte tyto možnosti.

## <a name="control-event-processor-host-options"></a>Možnosti hostitele procesoru událostí

Navíc jedno přetížení [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) trvá [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) objekt jako parametr. Tento parametr slouží k řízení chování samotného [eventprocessoruHost.UnregisterEventProcessorAsync.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) definuje čtyři vlastnosti a jednu událost:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): Maximální velikost kolekce, kterou chcete obdržet v vyvolání [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Tato velikost není minimální, pouze maximální velikost. Pokud je méně zpráv, které mají být přijaty, **ProcessEventsAsync** provede s tolika, kolik bylo k dispozici.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): Hodnota používaná základní kanál AMQP k určení horní limit, kolik zpráv by měl klient přijímat. Tato hodnota by měla být větší nebo rovna [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Pokud je tento parametr **true**, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) je volána při základní volání přijímat události na oddíl out. Tato metoda je užitečná pro přijetí akce založené na čase během období nečinnosti na oddíl.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): Umožňuje nastavit ukazatel funkce nebo výraz lambda, který je volán tak, aby poskytoval počáteční posun, když čtenář začne číst oddíl. Bez zadání tohoto posunu čtecí zařízení začíná na nejstarší události, pokud soubor JSON s posunem již byla uložena v účtu úložiště dodané [konstruktoru EventProcessorHost.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) Tato metoda je užitečná, pokud chcete změnit chování spuštění čtečky. Při vyvolání této metody obsahuje parametr objektu ID oddílu, pro které je spuštěna čtečka.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): Umožňuje přijímat oznámení o všech základních výjimkách, ke kterým dochází v [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Pokud věci nefungují tak, jak očekáváte, je tato událost dobrým místem, kde začít hledat.

## <a name="epoch"></a>Epocha

Zde je, jak funguje epocha pro příjem:

### <a name="with-epoch"></a>S Epochou
Epocha je jedinečný identifikátor (hodnota epochy), který služba používá k vynucení vlastnictví oddílu/zapůjčení. Vytvoření příjemce založené epochy pomocí [CreateEpochReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver?view=azure-dotnet) metoda. Tato metoda vytvoří přijímač založený na Epochu. Příjemce je vytvořen pro konkrétní oddíl centra událostí ze zadané skupiny příjemců.

Funkce epocha poskytuje uživatelům možnost zajistit, že ve skupině příjemců je pouze jeden příjemce v libovolném okamžiku, s následujícími pravidly:

- Pokud neexistuje žádný příjemce ve skupině příjemců, uživatel může vytvořit příjemce s libovolnou hodnotou epochy.
- Pokud je k dispozici přijímač s epochovou hodnotou e1 a vytvoří se nový přijímač s epochovou hodnotou e2, kde e1 <= e2, přijímač s e1 se automaticky odpojí, přijímač s e2 se úspěšně vytvoří.
- Pokud je k dispozici přijímač s epochovou hodnotou e1 a nový přijímač je vytvořen s epochovou hodnotou e2, kde e1 > e2, pak vytvoření e2 s chybou: Přijímač s epochou e1 již existuje.

### <a name="no-epoch"></a>Žádná epocha
Můžete vytvořit přijímač na bázi Epochy pomocí [CreateReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver?view=azure-dotnet) metoda. 

Existují některé scénáře ve zpracování datového proudu, kde uživatelé by chtěli vytvořit více příjemců v jedné skupině příjemců. Pro podporu těchto scénářů máme možnost vytvořit přijímač bez epochy a v tomto případě umožňujeme až 5 souběžných přijímačů ve skupině příjemců.

### <a name="mixed-mode"></a>Smíšený režim
Nedoporučujeme použití aplikací, kde vytvoříte přijímač s epochou a pak přepnete na no-epochu nebo naopak ve stejné skupině příjemců. Však když dojde k tomuto chování, služba zpracovává pomocí následujících pravidel:

- Pokud je přijímač již vytvořen s epochou e1 a aktivně přijímá události a nový přijímač je vytvořen bez epochy, vytvoření nového přijímače se nezdaří. Epochové přijímače mají v systému vždy přednost.
- Pokud byl již vytvořen přijímač s epochou e1 a odpojil se a nový přijímač je vytvořen bez epochy na nové MessagingFactory, vytvoření nového přijímače se podaří. Tam je upozornění zde, že náš systém detekuje "přijímač odpojení" po ~ 10 minut.
- Pokud existuje jeden nebo více přijímačů vytvořených bez epochy a nový přijímač je vytvořen s epochou e1, všechny staré přijímače se odpojí.


> [!NOTE]
> Doporučujeme používat různé skupiny spotřebitelů pro aplikace, které používají epochy a pro ty, které nepoužívají epochy, aby se zabránilo chybám. 


## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámeni s hostitelem procesoru událostí, najdete v následujících článcích další informace o centru událostí:

- Začínáme se službou Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
* [Průvodce programováním pro službu Event Hubs](event-hubs-programming-guide.md)
* [Dostupnost a konzistence ve službě Event Hubs](event-hubs-availability-and-consistency.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Ukázky centra událostí na GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples)
