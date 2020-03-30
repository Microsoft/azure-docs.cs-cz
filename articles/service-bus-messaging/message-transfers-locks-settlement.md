---
title: Přenosy, zámky a vyrovnání zpráv Azure Service Bus
description: Tento článek obsahuje přehled přenosů zpráv Azure Service Bus, uzamykat a operace vyrovnání.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2019
ms.author: aschhab
ms.openlocfilehash: a2c353d612280981a83b32463d34efdc70878495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260992"
---
# <a name="message-transfers-locks-and-settlement"></a>Přenosy zpráv, zámky a vyrovnání

Centrální funkce zprostředkovatele zpráv, jako je například Service Bus je přijímat zprávy do fronty nebo tématu a podržte je k dispozici pro pozdější načtení. *Odeslat* je termín, který se běžně používá pro přenos zprávy do zprostředkovatele zpráv. *Příjem* je termín běžně používaný pro přenos zprávy do klienta načítání.

Když klient odešle zprávu, obvykle chce vědět, zda byla zpráva správně přenesena a přijata zprostředkovatelem nebo zda došlo k nějaké chybě. Toto kladné nebo záporné potvrzení usadí klienta a makléře porozumění o stavu převodu zprávy a je proto označován jako *vypořádání*.

Podobně když makléř přenese zprávu klientovi, zprostředkovatel a klient chtějí zjistit, zda byla zpráva úspěšně zpracována a může být proto odebrána, nebo zda se doručení nebo zpracování zprávy nezdařilo, a tedy zprávu pravděpodobně bude muset být doručena znovu.

## <a name="settling-send-operations"></a>Vyrovnání operací odeslání

Pomocí některého z podporovaných klientů rozhraní API služby Service Bus jsou operace odesílání odesílat do služby Service Bus vždy explicitně vyrovnány, což znamená, že operace rozhraní API čeká na přijetí výsledku ze služby Service Bus a potom dokončí operaci odeslání.

Pokud je zpráva odmítnuta service bus, odmítnutí obsahuje indikátor chyby a text s "sledování id" uvnitř něj. Odmítnutí také obsahuje informace o tom, zda operace může být opakována s očekáváním úspěchu. V klientovi jsou tyto informace převedeny na výjimku a vyvolány volajícímu operace odeslání. Pokud byla zpráva přijata, operace tiše dokončí.

Při použití protokolu AMQP, který je výhradním protokolem pro klienta .NET Standard a klientem jazyka Java a [který je možností pro klienta rozhraní .NET Framework](service-bus-amqp-dotnet.md), jsou přenosy zpráv a vyrovnání kanálovány a zcela asynchronní a doporučujeme použít varianty rozhraní API asynchronního programovacího modelu.

Odesílatel může umístit několik zpráv na drát v rychlém sledu, aniž by museli čekat na každou zprávu, které mají být potvrzeny, jako by tomu bylo jinak s protokolem SBMP nebo s HTTP 1.1. Tyto operace asynchronního odesílání jsou dokončeny, protože příslušné zprávy jsou přijímány a uloženy, na rozdělených entitách nebo při odesílání operace různým entitám překrývají. Dokončení může také dojít mimo původní pořadí odeslání.

Strategie pro zpracování výsledků operace odesílání může mít okamžitý a významný dopad na výkon pro vaši aplikaci. Příklady v této části jsou napsány v jazyce C# a platí ekvivalentní pro Java Futures.

Pokud aplikace vytváří shluky zpráv, ilustrované zde s prostý smyčky, a měly čekat na dokončení každé operace odeslání před odesláním další zprávy, synchronní nebo asynchronní OBRAZCE ROZHRANÍ API podobně, odesílání 10 zpráv pouze dokončí po 10 sekvenční plné zpáteční lety pro vypořádání.

S předpokládanou vzdáleností latence odezvy tcp 70 milisekund z místního webu na service bus a poskytnutím pouhých 10 ms pro service bus pro přijetí a uložení každé zprávy trvá následující smyčka nejméně 8 sekund, nepočítá čas přenosu datové části nebo potenciální účinky přetížení trasy:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Pokud aplikace spustí 10 asynchronní odesílání operací v bezprostředním posobě a čeká na jejich příslušné dokončení samostatně, doba odezvy pro těchto 10 operace odesílání překrývá. 10 zpráv jsou přenášeny v bezprostřední min. po sobě, potenciálně i sdílení rámců TCP a celková doba přenosu do značné míry závisí na době související se sítí, která trvá k přenosu zpráv do zprostředkovatele.

Provedení stejné předpoklady jako pro předchozí smyčky, celkový překrývající čas provádění pro následující smyčky může zůstat i pod jednu sekundu:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

Je důležité si uvědomit, že všechny asynchronní programovací modely používají nějakou formu skryté pracovní fronty založené na paměti, která obsahuje čekající operace. Když [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) nebo **Odeslat** (Java) vrátí, odeslat úlohu je zařazen do fronty v této pracovní fronty, ale gesto protokolu spustí pouze po spuštění úlohy. Pro kód, který má tendenci tlačit shluky zpráv a kde spolehlivost je problém, je třeba dbát na to, že není příliš mnoho zpráv jsou uvedeny "v letu" najednou, protože všechny odeslané zprávy zabírají paměť, dokud byly věcně uvedeny na drát.

Semafory, jak je znázorněno v následujícím fragmentu kódu v c#, jsou synchronizační objekty, které v případě potřeby umožňují takové omezení na úrovni aplikace. Toto použití semaforu umožňuje maximálně 10 zpráv, které mají být v letu najednou. Jeden z 10 dostupných zámků semaforu je přijata před odesláním a je uvolněna jako dokončení odeslání. 11. průchod smyčky čeká, dokud alespoň jeden z předchozích odešle byl dokončen a pak zpřístupní jeho zámek:

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

Aplikace by **nikdy** aniciatou operaci asynchronního odeslání způsobem "fire and forget" bez načtení výsledku operace. To může načíst vnitřní a neviditelné fronty úloh až do vyčerpání paměti a zabránit aplikaci z detekci chyb y odeslání:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

S nízkoúrovňovým klientem AMQP service bus také přijímá "předem vyrovnané" převody. Předvyrovnaný převod je operace fire-and-forget, pro kterou výsledek, ať tak či onak, není hlášen zpět klientovi a zpráva je považována za vyrovnána při odeslání. Nedostatek zpětné vazby pro klienta také znamená, že pro diagnostiku nejsou k dispozici žádná dostupná data, což znamená, že tento režim není způsobilý pro pomoc prostřednictvím podpory Azure.

## <a name="settling-receive-operations"></a>Vyrovnání operací příjmu

Pro operace příjmu klienti rozhraní API služby Service Bus umožňují dva různé explicitní režimy: *Příjem a odstranění* a *Peek-Lock*.

### <a name="receiveanddelete"></a>ReceiveAndDelete

Režim [příjem a odstranění](/dotnet/api/microsoft.servicebus.messaging.receivemode) informuje zprostředkovatele, aby zvážil všechny zprávy, které odesílá přijímajícímu klientovi jako vyrovnané při odeslání. To znamená, že zpráva je považována za spotřebované, jakmile makléř dal do drátu. Pokud se přenos zprávy nezdaří, zpráva bude ztracena.

Výhodou tohoto režimu je, že příjemce nemusí přijmout další akce na zprávu a také není zpomalen čekáním na výsledek vyrovnání. Pokud data obsažená v jednotlivých zprávách mají nízkou hodnotu a/nebo jsou smysluplná pouze velmi krátkou dobu, je tento režim rozumnou volbou.

### <a name="peeklock"></a>PeekLock

[Peek-Lock](/dotnet/api/microsoft.servicebus.messaging.receivemode) režimu informuje zprostředkovatele, že přijímající klient chce explicitně vyrovnat přijaté zprávy. Zpráva je k dispozici pro příjemce ke zpracování, zatímco drženy pod výhradní zámek ve službě tak, aby ostatní, konkurenční příjemci nemůže vidět. Doba trvání zámku je zpočátku definována na úrovni fronty nebo předplatného a může být prodloužena klientem, který vlastní zámek, prostřednictvím operace [RenewLock.](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Pokud je zpráva uzamčena, ostatní klienti přijímající ze stejné fronty nebo předplatného mohou převzít zámky a načíst další dostupné zprávy, které nejsou pod aktivním zámkem. Při uzamčení zprávy je explicitně uvolněna nebo při vypršení platnosti zámku, zpráva se objeví zpět na nebo v blízkosti přední pořadí načítání pro opětovné doručení.

Pokud je zpráva opakovaně uvolněna příjemci nebo umožňují, aby zámek uplynul pro definovaný počet časů ([maxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)), zpráva je automaticky odebrána z fronty nebo odběr a umístěndo přidružené fronty nedoručených zpráv.

Přijímající klient iniciuje vyrovnání přijaté zprávy s kladným potvrzením při volání [Complete](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) na úrovni rozhraní API. To znamená zprostředkovateli, že zpráva byla úspěšně zpracována a zpráva je odebrána z fronty nebo odběr. Zprostředkovatel odpoví na záměr vyrovnání příjemce s odpovědí, která označuje, zda bylo možné provést vyrovnání.

Pokud přijímající klient nepodaří zpracovat zprávu, ale chce zprávu znovu doručit, může explicitně požádat o zprávu, která má být uvolněna a odemčena okamžitě voláním [Abandon](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) nebo nemůže nedělat nic a nechat zámek uplynout.

Pokud přijímající klient nepodaří zpracovat zprávu a ví, že opětovné doručení zprávy a opakování operace nepomůže, může odmítnout zprávu, která přesune do fronty nedoručených zpráv voláním [DeadLetter](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter), který také umožňuje nastavení vlastní vlastnosti včetně kódu důvodu, který lze načíst se zprávou z fronty nedoručených zpráv.

Zvláštním případem vypořádání je odklad, který je popsán v samostatném článku.

**Operace Complete** nebo **Deadletter,** stejně jako operace **RenewLock** může selhat z důvodu problémů se sítí, pokud vypršela platnost podrženého zámku nebo existují jiné podmínky na straně služby, které brání vyrovnání. V jednom z posledních případů služba odešle negativní potvrzení, že povrchy jako výjimka v klientech rozhraní API. Pokud je důvodem přerušené síťové připojení, zámek je zrušen, protože service bus nepodporuje obnovení existujících propojení AMQP na jiné připojení.

Pokud **Complete** selže, ke kterému dochází obvykle na samém konci zpracování zpráv a v některých případech po minutách zpracování práce, přijímající aplikace může rozhodnout, zda zachová stav práce a ignoruje stejnou zprávu při doručení podruhé, nebo zda to přejde mimo výsledek práce a opakování při opětovném doručení zprávy.

Typický mechanismus pro identifikaci duplicitní zprávy doručování je kontrolou id zprávy, která může a měla by být nastavena odesílatelem na jedinečnou hodnotu, případně zarovnány s identifikátorem z původního procesu. Plánovač úloh by pravděpodobně nastavil id zprávy na identifikátor úlohy, kterou se pokouší přiřadit pracovníkovi s daným pracovníkem, a pracovník by ignoroval druhý výskyt přiřazení úlohy, pokud je tato úloha již hotová.

> [!IMPORTANT]
> Je důležité si uvědomit, že zámek, který PeekLock získá na zprávu je volatilní a může dojít ke ztrátě v následujících podmínkách
>   * Aktualizace služby
>   * Aktualizace operačního systému
>   * Změna vlastností entity (Fronta, Téma, Odběr) při držení zámku.
>
> Při ztrátě zámku Azure Service Bus vygeneruje LockLostException, který se zobrazí na kódu klientské aplikace. V takovém případě by měla být automaticky nastartována logika zpětného opakování klienta a operace by měla být zopakování.

## <a name="next-steps"></a>Další kroky

Další informace o zasílání zpráv služby Service Bus najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
