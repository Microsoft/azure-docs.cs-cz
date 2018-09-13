---
title: Azure Service Bus přenosy zpráv, zámky a vyrovnání | Dokumentace Microsoftu
description: Přehled předávání zpráv služby Service Bus a vyrovnání operací
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
ms.author: spelluru
ms.openlocfilehash: de3f23f58ef34bdd5f9769f820d64ed7e00ca7d8
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44715070"
---
# <a name="message-transfers-locks-and-settlement"></a>Přenosy zpráv, zámky a vyrovnání

Centrální funkce zprostředkovatele zpráv, jako je například Service Bus je uložení je dostupné pro pozdější načtení a přijímat zprávy do fronty nebo tématu. *Odeslat* je termín, který se obvykle používá pro přenos zpráv do zprostředkovatele zpráv. *Přijímat* je termín běžně používá pro přenos zpráv načítání klientovi.

Když klient odešle zprávu, obvykle chce vědět, jestli zpráva byla správně přenos a přijal zprostředkovatele nebo zda došlo k nějaké chyby. Toto potvrzení kladné nebo záporné vyrovná zprostředkovatel vysvětlení týkající se stavu přenos zprávy a klienta a je proto označovány jako *vyrovnání*.

Podobně, pokud zprostředkovatele přenáší zprávy do klienta, zprostředkovatel a klient má k vytvoření a pochopení Určuje, zda zpráva se úspěšně zpracovala a proto je možné odebrat, nebo zda doručováním zpráv nebo zpracování se nezdařilo a proto zpráva může být nutné znovu doručit.

## <a name="settling-send-operations"></a>Vyrovnání operace odesílání

Pomocí kteréhokoli z podporovaných klientů rozhraní API služby Service Bus, odesílat provoz do služby Service Bus vždy explicitně usadí, což znamená, že operace rozhraní API čeká na výsledek přijetí ze služby Service Bus můžete přejít a pak dokončí operaci odeslání.

Pokud se ve službě Service Bus zprávu, odmítnutí obsahuje označení chyb a textu s "-id sledování" uvnitř této služby. Odmítnutí obsahuje také informace o Určuje, zda můžete operaci opakovat s jakékoli předpokladem úspěchu. Tyto informace v klientovi, je převedena na výjimku a umocněné na volající operace odeslání. Pokud byla přijata zpráva tiše dokončení operace.

Při použití protokolu AMQP, což je exkluzivní protokol pro .NET Standard klienta a klientskou sadou Java a [což je možnost pro rozhraní .NET Framework client](service-bus-amqp-dotnet.md), jsou zřetězena zpráva převodů a vyrovnání a zcela asynchronní, a doporučuje se, že používáte varianty asynchronní rozhraní API programovacího modelu.

Odesílatel, můžou vložit několik zpráv na lince rychle po sobě aniž byste museli čekat pro každou zprávu potvrzení, protože jinak by tomu bylo u protokolu SBMP nebo pomocí protokolu HTTP 1.1. Tyto operace asynchronního odeslání dokončit, protože přijaty a uložené na dělené entity příslušné zprávy nebo při odesílání operace do překrytí různé entity. Dokončení může také dojít mimo původní pořadí odeslání.

Strategie pro zpracování výsledku operace odesílání může mít dopad na výkon okamžité zásadní a významné pro vaši aplikaci. Příklady v této části jsou napsané v C# a požádat ekvivalentně Java Futures.

Pokud aplikace vytvoří nárůstům zprávy, zde znázorněný s prostý smyčky a byly await pro čekání na dokončení každého odeslat operaci před odesláním další zprávy, která je synchronní nebo asynchronní rozhraní API tvary nabídne, odesílání zpráv 10 pouze dokončí za 10 sekvenční úplné zpátečních cest k vyrovnání.

Pomocí předpokládá, že 70 milisekund TCP vzdálenost umožňujícím zpětnou transformaci latence z místní lokality do služby Service Bus a poskytuje jenom 10 ms pro Service Bus přijmout a uložit každou zprávu následující smyčka zabírá aspoň 8 sekund mimo dobu přenosu datové části nebo potenciální účinky zahlcení trasy:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Pokud se aplikace spustí 10 operací asynchronního odeslání bezprostředně za sebou a čeká na dokončení jejich odpovídajících samostatně, doby odezvy pro těchto 10 operace odeslání se překrývá. 10 zpráv se přenáší bezprostředně za sebou, potenciálně i sdílení TCP snímků a celková doba trvání přenos do značné míry závisí na související se sítí čas potřebný k získání zpráv přenosu do zprostředkovatele.

Odhad stejné jako u předchozí smyčky, celkové doby provádění překrývající následující smyčky může zůstat dobře v části jedné sekundy:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

Je důležité si uvědomit, že všechny asynchronní programovací modely použít nějaký způsob založených na paměti, skrytá pracovní fronty, která obsahuje čekající operace. Když [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) nebo **odeslat** return (Java), odesílání úloh je ve frontě v této fronty pracovních položek, ale protokol gesta začíná pouze po zapnutí úkolu pro spuštění. Pro kód, který obvykle tak, aby nabízel nárůstem zprávy a kde spolehlivost je důležité zajistit je třeba, že jsou příliš mnoho zprávy vložit "za pochodu" najednou, protože všechny odesílané zprávy se paměť až věcně poslána byla pozastavena.

Semafory, jak je znázorněno v následujícím fragmentu kódu v jazyce C#, jsou objekty synchronizace, které umožňují takové omezení úrovni aplikace v případě potřeby. Toto použití semafor umožňuje maximálně 10 zpráv za pochodu najednou. Jeden 10 uzamčení k dispozici pro spolupráci se před odesílání a uvolní se, jak dokončení odesílání. 11 průchodu smyčky počká, dokud alespoň jeden z předchozího odešle byla dokončena a potom zpřístupní platnost zámku:

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks);
```

Aplikace by **nikdy** zahájení operace asynchronního odeslání způsobem "vypal a zapomeň" bez načtení výsledek operace. To můžete načíst interní a neviditelná, úlohy fronty až do vyčerpání paměti a zabránit aplikaci v odhalování chyb odesílání:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

U klientů nižší úrovně AMQP přijímá také služby Service Bus "předem vyrovnané" přenosy. Předem vyrovnané přenosu je fire a zapomenout operace, pro kterou výsledek v obou případech není hlášeny zpět do klienta a zpráva se považuje za vyrovnané při odeslání. Chybějící zpětnou vazbu, která klient také znamená, že nejsou žádná data potřebná k dispozici pro diagnostiku, což znamená, že tento režim nesplňuje o pomoc prostřednictvím podpory Azure.

## <a name="settling-receive-operations"></a>Vyrovnání operace příjmu

Pro operace příjmu, rozhraní API služby Service Bus klientům povolit dvou různých režimech explicitní: *přijmout a odstranit* a *neboli Peek-Lock*.

[Přijmout a odstranit](/dotnet/api/microsoft.servicebus.messaging.receivemode) režimu říká zprostředkovatele vzít v úvahu všechny zprávy, odešle klientovi přijímající jako vyrovnané při odeslání. To znamená, že zprávy se považuje za spotřebované jako zprostředkovatel má umístěny na lince. Pokud přenos zpráv selže, dojde ke ztrátě zprávy.

Vzhůru tento režim je, že příjemce není potřeba provádět další akce na zprávy a také není způsobenému čekáte na výsledek řešení. Pokud data obsažená v jednotlivých zpráv s nízkou hodnotou a/nebo jsou pouze smysl pro velmi krátké době, tento režim je vhodná volba.

[Neboli Peek-Lock](/dotnet/api/microsoft.servicebus.messaging.receivemode) režimu určuje zprostředkovatele, přijímající klienta chce explicitně vyrovnat přijaté zprávy. Zprávu je k dispozici pro příjemce a jejich zpracování, dokud držen pod výhradní zámek ve službě, aby ostatní, konkurenčních příjemců nemůže zobrazit. Doba trvání zámku je zpočátku definované na úrovni fronty nebo odběru a je možné rozšířit pomocí klienta, prostřednictvím vlastnící zámek, [RenewLock](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) operace.

Pokud se zpráva uzamkne, ostatní klienti přijímající ze stejné fronty nebo odběru můžete provádět na zámků a načíst další dostupné zprávy není pod aktivní zámku. Pokud je explicitně uvolněn zámek u zprávy, nebo když vyprší platnost zámku, zobrazí zpráva s zpět na nebo blízko ní přední pořadí načítání pro opakované dodání.

Když příjemci opakovaně vydal zprávu nebo umožňují zámek uplynout pro definovaný počet ([maxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)), automaticky odebrány z fronty nebo odběru a umístí do přidruženého zprávy fronty nedoručených zpráv.

Přijímající klienta iniciuje vyrovnání přijatou zprávu s kladné potvrzení při volání [Complete](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) na úrovni rozhraní API. To znamená do zprostředkovatele, zpráva se úspěšně zpracovala a zpráva je odebrána z fronty nebo odběru. Odpověď zprostředkovatele na záměr vyrovnání příjemce odpověď, která označuje, zda může být provedena vyrovnání.

Při přijímání klienta se nepodaří zpracovat zprávu, ale chce, aby se zpráva, která má být víckrát, můžete explicitně požádat o zpráva, která má být všeobecně dostupné a odemčený okamžitě voláním [opustit](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) nebo nemusí provádět žádnou akci a nechat uplynout zámek.

Pokud přijímající klient nepodaří zprávu zpracovat a ví, že redelivering zprávy a opakováním operace nepomůže, může zamítnout zprávy, které přesouvá ji do fronty nedoručených zpráv voláním [nedoručených zpráv](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter), což také Umožňuje nastavit vlastní vlastnosti, včetně důvodu kód, který je možné načíst zprávy z fronty nedoručených zpráv.

Zvláštním případem vyrovnání je odložení, která je popsána v věnovaný samostatný článek.

**Complete** nebo **nedoručených zpráv** operace také **RenewLock** operací může selhat kvůli problémům se sítí, pokud vlastněnou zámku již vypršela, nebo existují jiné podmínky straně služby, které brání v řešení. V jednom z posledních případech služba odesílá negativní potvrzení tohoto zařízení Surface jako výjimku v klientech rozhraní API. Pokud z důvodu je přerušené síťové připojení, zámek se zahodí od služby Service Bus nepodporuje obnovení existující AMQP odkazy na jiné připojení.

Pokud **Complete** selže, což spadá obvykle na konci velmi zpracování zpráv a v některých případech po minutách práce zpracování přijímající aplikace můžete rozhodnout, zda zachovává stav práce a ignoruje stejné zprávy při doručení podruhé, nebo zda tosses si výsledek práce a zopakují. jako zprávu, která je víckrát.

Typické mechanismus pro identifikaci duplicitních zpráv doručení je tak, že zkontrolujete – id zprávy, které dokáže a nastavte odesílatel na jedinečnou hodnotu, může být zarovnána s identifikátorem z původní procesu. Plánovač úloh by pravděpodobně nastaví id zprávy na identifikátor úlohy, které se pokouší přiřadit k pracovnímu procesu s danou pracovních procesů a pracovní proces by druhým výskytem přiřazení úlohy ignorovat, pokud se tato úloha se už dokončilo.

## <a name="next-steps"></a>Další postup

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
