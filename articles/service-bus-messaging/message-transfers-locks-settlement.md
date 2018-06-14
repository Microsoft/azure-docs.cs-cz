---
title: Azure Service Bus zprávu přenosy, zámků a vyrovnání | Microsoft Docs
description: Přehled přenosu zpráv Service Bus a vyrovnání operací
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: sethm
ms.openlocfilehash: 4789da3c84d52b2615bf4250a36093a74154e1d4
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2018
ms.locfileid: "28199028"
---
# <a name="message-transfers-locks-and-settlement"></a>Přenosy, zámky a vyrovnání zpráv

Centrální funkce zprostředkovatele zpráv, jako je Service Bus je přijímala zprávy do fronty nebo téma a uložení je k dispozici novější. *Odeslat* je termín, který se běžně používá pro přenos zpráv do zprostředkovatele zpráv. *Přijímat* je termín, často používá pro přenos zpráv pro stahování klienta.

Když klient odešle zprávu, obvykle chce vědět, zda zpráva byla správně převede na a přijata zprostředkovatelem, nebo jestli se stala nějaká chyba. Tato kladné a záporné potvrzení vyrovná klienta a zprostředkovatele pochopení týkající se stavu přenos zprávy a je proto označovány jako *vyrovnání*.

Podobně, pokud zprostředkovatel přenáší zprávy ke klientovi, zprostředkovatele a klienta chcete vytvořit představu o tom, zda zpráva byla úspěšně zpracována a proto může být odebrán nebo jestli doručení zpráv na zpracování se nezdařilo a proto zpráva může být nutné znovu doručit.

## <a name="settling-send-operations"></a>Vyrovnání operace odesílání

Pomocí kteréhokoli z podporovaných klientů rozhraní API služby Service Bus, odesílání se operace do služby Service Bus vždy explicitně vyrovnávají, což znamená, že operace rozhraní API čeká na výsledek přijetí ze služby Service Bus příchod a pak dokončí operaci odeslání.

Pokud zpráva je odmítnut Service Bus, obsahuje zamítnutí označení chyb a textu pomocí "-id sledování" uvnitř této. Zamítnutí také obsahuje informace o tom, jestli můžete operaci opakovat jakékoli předpokladu úspěch. Tyto informace se v klientovi převedena na výjimku a vyvolá volajícímu operaci odeslání. Pokud zpráva byla přijata, bezobslužně dokončení operace.

Při použití protokolu AMQP, což je protokol výhradní pro .NET Standard klienta a klient Java a [tedy možnost pro klienta rozhraní .NET Framework](service-bus-amqp-dotnet.md), jsou zřetězena přenosu zpráv a vyrovnání a úplně asynchronní, a doporučujeme použít asynchronní programování variant rozhraní API modelu.

Odesílatele můžete vložit několik zpráv v drátové síti rychle za sebou aniž byste museli čekat na každou zprávu potvrzení, jako by se jinak mohly případ protokol SBMP nebo s HTTP 1.1. Tyto operace asynchronní odesílání dokončit, protože odpovídající zprávy jsou přijatá a uložená na dělené entity nebo po operaci poslat překrývají různých entit. Dokončených může také dojít mimo pořadí původní odeslání.

Strategie pro zpracování výsledku operace odesílání může mít vliv na výkon okamžité a důležité pro vaši aplikaci. Příklady v této části jsou napsané v C# a použít ekvivalentně pro Java Futures.

Pokud aplikace vytváří shluky zpráv, zde znázorněný s prostý smyčky a byly kde čekají na dokončení každé odeslání operace před odesláním další zprávy synchronní nebo asynchronní rozhraní API tvarů agentem, odesílání zpráv 10 pouze dokončena po 10 sekvenční úplné zpátečních cest k vyrovnání.

S předpokládané 70 milisekundu TCP vzdálenost umožňujícím zpětnou transformaci latence z místního serveru k Service Bus a poskytnete jenom 10 ms pro Service Bus přijmout a uložte každou zprávu následující zacyklení zabírají nejméně 8 sekund, ne počítání dobu přenosu datová část nebo potenciální trasy zahlcení důsledky:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Pokud se aplikace spustí 10 operace asynchronní odesílání bezprostředně za sebou a jejich odpovídajících dokončení čeká samostatně, doba odezvy pro těchto 10 operace odesílání se překrývá. 10 zprávy jsou přeneseny bezprostředně za sebou, potenciálně i sdílení rámce TCP a celková doba trvání přenos do značné míry závisí na související se sítí čas potřebný k získání zprávy přenést do zprostředkovatele.

Odhad stejné jako u předchozí smyčky, celkový čas provádění překryté pro následující zacyklení může zůstat dobře v části sekundu:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks.ToArray());
```

Je důležité si uvědomit, že všechny modely asynchronní programování pomocí určitou formu na základě paměti, skrytý pracovní fronty, který obsahuje čekající operace. Když [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) nebo **odeslat** vrátit (Java), odesílání úloh je zařazen do fronty v této pracovní fronty, ale gesto protokol zahájí pouze po zapnout úkolu spuštění. Pro kód, který se obvykle push shluky zprávy a kde spolehlivost obavu potřeba dát pozor, jsou příliš mnoho zpráv put "v cestě" najednou, protože všechny odeslané zprávy zabírají paměť, dokud věcně přenášená byla pozastavena.

Semaforů, jak je znázorněno v následující fragment kódu v jazyce C#, jsou synchronizace objekty, které povolit takové úrovni aplikace omezení v případě potřeby. Toto použití semafor umožňuje maximálně 10 zpráv, které mají být v cestě najednou. Jeden z 10 zámky. k dispozici semafor provedena před odesílání a jeho vydání dokončení odesílání. 11 předávání smyčky počká, dokud alespoň jeden z předchozího odešle byla dokončena a potom zpřístupní její uzamčení:

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks.ToArray());
```

Aplikace by měla **nikdy** zahájení operace asynchronní odesílání způsobem "fire a zapomněli" bez načítání výsledek operace. Díky tomu můžete načíst fronty úloh interní a neviditelná až vyčerpání paměti a zabránit aplikaci v zjišťování odesílání chyb:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

U klientů nižší úrovně AMQP Service Bus přijme také "předem vyrovnané" přenosů. Předem vyrovnané přenosu je ještě efektivněji a zapomněli operaci, pro který výsledek v obou případech není hlášena zpět klientovi a zpráva se považuje za vyrovnané při odeslání. Nedostatek zpětnou vazbu klientovi také znamená, že je k dispozici pro diagnostiku, což znamená, že tento režim není způsobilá o pomoc prostřednictvím podpory Azure žádná řešitelné data.

## <a name="settling-receive-operations"></a>Operace vyrovnání příjmu.

Pro příjem operace, rozhraní API služby Service Bus klientů povolte dvou různých režimech explicitní: *přijetí a odstranění* a *funkce Náhled zámku*.

[Přijetí a odstranění](/dotnet/api/microsoft.servicebus.messaging.receivemode) režimu informuje zprostředkovatele vzít v úvahu všechny zprávy odešle přijímající klientovi jako vyrovnané při zpracování odeslaných. To znamená, že zpráva považuje za spotřebované co nejrychleji zprostředkovatel má umístí jej do sítě. Pokud se přenos zpráv nezdaří, dojde ke ztrátě zprávy.

Vzhůru tento režim je, že příjemce není třeba provádět další akce na zprávu, není také zpomalit tím, že pro výsledek vyrovnání. Pokud data obsažená v jednotlivých zpráv nízkou hodnotu nebo jsou pouze pro velmi krátké době smysluplný, tento režim je vhodná volba.

[Funkce Náhled zámku](/dotnet/api/microsoft.servicebus.messaging.receivemode) režimu informuje zprostředkovatele, aby přijímající klient chce explicitně vyrovnat přijatých zpráv. Zpráva je k dispozici pro příjemce ke zpracování, zatímco uchovávat v části výhradní zámek v rámci služby, aby příjemci, které konkurence mezi nelze zobrazit. Doba trvání zámku původně definovanou na úrovni fronty nebo předplatné a je možné rozšířit pomocí vlastnícího zámek, prostřednictvím klienta [RenewLock](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) operaci.

Pokud zprávu uzamčen, ostatní klienti přijímající ze stejné fronty nebo předplatné můžete provádět zámky a načítat další zprávy k dispozici není v části active zámku. Až bude zámek ve zprávě explicitně vydaná nebo když vyprší platnost zámek, zpráva se zobrazí zpět nebo blízko před pořadí načtení pro opakované dodání.

Pokud zpráva opakovaně vydání příjemci nebo umožňují zámek uplynout pro definovaný počet ([maxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)), automaticky odebrána z fronty nebo předplatné a umístí do přidruženého zprávy frontu nedoručených zpráv.

Přijímající klient zahájí vyrovnání přijaté zprávy s kladné potvrzení při volání [Complete](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) na úrovni rozhraní API. To znamená pro zprostředkovatele, že zpráva byla úspěšně zpracována a zpráva je odebrána z fronty nebo předplatné. Odpovědi zprostředkovatele k příjemce vyrovnání záměr odpovědi, která určuje, zda může být provedeno vyrovnání.

Pokud přijímající klient nepodaří zpracovat zprávu, ale chce, aby se zpráva, která má být víckrát, můžete pro zprávu, která se neuvolní a odemčený okamžitě voláním explicitně požádat [Abandon](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) nebo nemusí provádět žádnou akci a nechat uplynout zámek.

Pokud přijímající klient nepodaří zprávu zpracovat a ví, že redelivering zprávy a opakováním operace nepomůže, ho odmítnout zprávy, která přesune ho do fronty nedoručených zpráv voláním [nedoručených zpráv](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter), což také Umožňuje nastavení vlastní vlastnost včetně důvod kód, který se dá načíst pomocí zpráv z fronty nedoručených zpráv.

Zvláštním případem vyrovnání je odložení, která je popsána v věnovaný samostatný článek.

**Complete** nebo **nedoručených zpráv** operace a taky **RenewLock** operace mohou selhat kvůli problémům se sítí, pokud vypršela platnost udržovaných zámek, nebo existuje jiné podmínky straně služby, které brání vyrovnání. V jednom z posledních případech službu odešle záporné potvrzení této povrchy jako výjimku v klientech rozhraní API. Pokud z důvodu je porušený síťového připojení, zámek je vyřazeno, protože sběrnice nepodporuje obnovení existující AMQP odkazy na jiné připojení.

Pokud **Complete** selže, které dochází obvykle na konci velmi zpracování zpráv a v některých případech po minutách práce zpracování má přijímající aplikace můžete rozhodnout, jestli zachovává stav práce a ignoruje stejné zprávy při doručení podruhé, nebo zda tosses na výsledek pracovní a opakuje jako zprávu, která je víckrát.

Typické mechanizmus pro identifikaci dodávky duplicitní zpráva se kontrolou zpráva id, které můžete a musí být nastavena odesílatele, aby jedinečnou hodnotu, může být v souladu s identifikátorem z původní procesu. Plánovač úloh by pravděpodobně nastaví id zprávy na identifikátor úkol, který se pokouší přiřadit k pracovnímu procesu se daný pracovní proces a pracovní proces by druhého výskytu přiřazení úlohy ignorovat, pokud tuto úlohu je už hotové.

## <a name="next-steps"></a>Další postup

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
