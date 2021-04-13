---
title: Azure Service Bus přenosů, zámků a vyrovnání zpráv
description: Tento článek poskytuje přehled Azure Service Bus přenosů zpráv, zámků a operací vyrovnání.
ms.topic: article
ms.date: 04/12/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 6fbbcbf4a1920ee0e66a956443dcfb8a6a17af43
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306768"
---
# <a name="message-transfers-locks-and-settlement"></a>Přenosy zpráv, zámky a vyrovnání

Ústřední schopností zprostředkovatele zpráv, jako je například Service Bus, je přijmout zprávy do fronty nebo tématu a umístit je k dispozici pro pozdější načtení. *Odeslání* je termín, který se běžně používá k přenosu zprávy do zprostředkovatele zpráv. *Přijetí* je termín, který se běžně používá k přenosu zprávy do načítání klienta.

Když klient pošle zprávu, obvykle chce zjistit, jestli je zpráva správně přenesená a přijatá zprostředkovatelem nebo jestli došlo k nějaké chybě. Toto kladné nebo záporné potvrzení sestaví klienta a Broker porozumění o stavu přenosu zprávy. To znamená, že se označuje jako *vyrovnání*.

Podobně platí, že když zprostředkovatel přenáší zprávu do klienta, zprostředkovatel a klient si budou vědomi, zda byla zpráva úspěšně zpracována a že je možné ji odebrat nebo zda se nezdařilo doručení nebo zpracování zprávy, a proto může být nutné zprávu znovu doručit.

## <a name="settling-send-operations"></a>Vypořádání operací odeslání

Pomocí kteréhokoli z podporovaných klientů rozhraní Service Bus API se operace odeslání do Service Bus vždycky odúčtují, což znamená, že operace rozhraní API čeká na přijetí výsledku ze Service Bus a pak dokončí operaci odeslání.

Pokud je zpráva odmítnuta Service Bus, odmítání obsahuje indikátor chyby a text s **ID sledování** . Odmítání taky obsahuje informace o tom, jestli se operace může opakovat s jakýmkoli očekáváním úspěchu. V klientovi jsou tyto informace přeměněny na výjimku a jsou vyvolány volajícímu operace Send. Pokud byla zpráva přijata, operace se tiše dokončí.

Pokud používáte protokol AMQP, což je exkluzivní protokol pro klienty .NET Standard, Java, JavaScript, Python a přejít a [možnost klienta .NET Framework](service-bus-amqp-dotnet.md), přenosy zpráv a jejich vyrovnání jsou zřetězené a asynchronní. Doporučujeme použít varianty rozhraní API asynchronního programování modelu.

Odesilatel může do přenosu v rychlém přenosu umístit několik zpráv, aniž by musel čekat na potvrzení každé zprávy, jako by to jinak mohlo být případ s protokolem SBMP nebo s HTTP 1,1. Tyto asynchronní operace odeslání jsou dokončeny, protože příslušné zprávy jsou přijímány a uloženy, v dělených entitách nebo při překrytí operace odeslání do různých entit. Doplňování se taky může vyskytnout mimo původní objednávku odeslání.

Strategie pro zpracování výstupu operací odeslání může mít okamžitý a významný dopad na výkon vaší aplikace. Příklady v této části jsou napsané v jazyce C# a platí pro futures, Java mono, JavaScript příslibů a ekvivalentní pojmy v jiných jazycích.

Pokud aplikace vytvoří shluky zpráv, které jsou znázorněny v jednoduché smyčce, a museli očekávat dokončení každé operace odeslání před odesláním další zprávy, synchronních nebo asynchronních tvarů rozhraní API podobně, odesílání 10 zpráv se dokončí až po 10 sekvenčních cyklech pro vyrovnání.

S předpokládanou délkou latence 70-milisekunda z místního serveru na Service Bus a pro Service Bus k přijetí a uložení každé zprávy se dá použít jenom 10 MS, následující smyčka zabere aspoň 8 sekund, nepočítá dobu přenosu dat nebo potenciální důsledky zahlcení tras:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Pokud aplikace spouští 10 operací asynchronního odeslání ihned po sobě a očekává, že se jejich splnění dokončí samostatně, doba odezvy těchto 10 operací odeslání se překrývá. 10 zpráv se přenáší okamžitým úspěchem, potenciálně dokonce sdílením rámců TCP a celková doba trvání přenosu v podstatě závisí na čase souvisejícím se sítí, který je potřebný k získání zpráv přenesených do zprostředkovatele.

V rámci stejných předpokladů jako u předchozí smyčky by celková překrytá doba spuštění pro následující smyčka mohla zůstat v průběhu jedné sekundy:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

Je důležité si uvědomit, že všechny asynchronní programovací modely používají určitou formu skryté pracovní fronty založené na paměti, která obsahuje čekající operace. Když se vrátí rozhraní API pro odeslání, je úloha odeslání zařazená do fronty v této pracovní frontě, ale gesto protokolu se spustí až po spuštění úlohy. V případě kódu, který se pokouší zasílat shluky zpráv a kde je spolehlivost, je třeba dbát na to, aby nedošlo k příliš velkému počtu zpráv v jednom letu, protože všechny odeslané zprávy zabírají paměť, dokud nebudou věcně do sítě.

Semafory, jak je znázorněno v následujícím fragmentu kódu v jazyce C#, jsou objekty synchronizace, které v případě potřeby povolují omezení na úrovni aplikace. Použití semaforu umožňuje nejvýše 10 zpráv v jednom letu. Jedna z 10 dostupných zámků semaforu se před odesláním uvolní a uvolní se, až se dokončí odeslání. 11. průchod smyčkou počká, dokud se nedokončí aspoň jedno z předchozích odeslání, a pak je k dispozici její uzamčení:

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

Aplikace by **nikdy** neměly iniciovat asynchronní operace odeslání ve formě "ohně a zapomenout" bez načtení výsledku operace. V takovém případě může dojít k načtení interní a neviditelné fronty úloh do vyčerpání paměti a zabránění aplikaci v detekci chyb odeslání:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

U klienta AMQP s nižší úrovní se Service Bus přijímá také "předem vyrovnaný" přenos. Předem vyrovnaný přenos je operace typu oheň a zapomenutí, pro kterou je výsledek buď nehlášený klientovi, a zpráva se při odeslání považuje za vyrovnanou. Nedostatečná odezva na klienta také znamená, že pro diagnostiku nejsou k dispozici žádná data k dispozici. to znamená, že tento režim není způsobil pro pomoc prostřednictvím podpory Azure.

## <a name="settling-receive-operations"></a>Vyrovnání operací příjmu

Pro operace Receive umožňují klienti rozhraní API Service Bus dva různé explicitní režimy: *přijetí a odstranění* a *prohlížení zámku*.

### <a name="receiveanddelete"></a>ReceiveAndDelete

Režim **přijetí a odstranění** říká zprostředkovateli, aby posuzuje všechny zprávy, které odesílá klientovi, který je při odeslání vyrovnaný. To znamená, že zpráva se považuje za spotřebou, jakmile ji zprostředkovatel umístí na síťový kabel. Pokud přenos zprávy selhává, zpráva se ztratí.

Na straně tohoto režimu je to, že příjemce nemusí ve zprávě provádět další akce a zároveň se nezpomaluje čekáním na výsledek vyrovnání. Pokud mají data obsažená v jednotlivých zprávách nízkou hodnotu a/nebo jsou smysluplné jenom pro velmi krátkou dobu, je tento režim přiměřenou volbou.

### <a name="peeklock"></a>PeekLock

Režim **prohlížení zámku** oznamuje zprostředkovateli, že přijímající klient chce vyrovnávat přijaté zprávy explicitně. Zpráva je k dispozici pro příjem přijímače a přitom je držena výhradním zámkem ve službě, aby se mohly ostatní konkurenční přijímače zobrazit. Doba trvání zámku je zpočátku definovaná na úrovni fronty nebo předplatného a dá se rozšířit o klienta vlastnícího zámek. Podrobnosti o obnovení zámků najdete v části [obnovení zámků](#renew-locks) v tomto článku. 

Když je zpráva uzamčená, ostatní klienti, kteří získají ze stejné fronty nebo předplatného, můžou převzít zámky a načíst další dostupné zprávy, které nejsou v aktivním zámku. Když se zámek zprávy explicitně uvolní nebo když platnost zámku vyprší, zpráva se vrátí do začátku nebo téměř v pořadí načítání pro opětovné doručení.

Když je zpráva opakovaně uvolněna příjemci nebo může zámek prodloužit o stanovený počet pokusů ([maximální počet doručení](service-bus-dead-letter-queues.md#maximum-delivery-count)), zpráva se automaticky odebere z fronty nebo odběru a umístí se do přidružené fronty nedoručených zpráv.

Přijímající klient iniciuje vypořádání přijaté zprávy s pozitivním potvrzením, když volá `Complete` rozhraní API pro zprávu. Oznamuje zprostředkovateli, že zpráva byla úspěšně zpracována a zpráva je odebrána z fronty nebo odběru. Zprostředkovatel odpoví na záměr pro vyrovnání přijímače s odpovědí, která označuje, zda může být toto vyrovnání provedeno.

Když přijímajícímu klientovi se nepovede zpracovat zprávu, ale chce, aby se zpráva znovu doručovat, může explicitně požádat, aby se zpráva uvolnila a odemkla okamžitě tím, že zavolá `Abandon` rozhraní API pro zprávu, nebo může dělat nic a nechat zámek.

Pokud přijímajícímu klientovi se nepovede zpracovat zprávu a ví, že znovu doručí zprávu a opakuje ji, může zprávu odmítnout, která ji přesune do fronty nedoručených zpráv voláním `DeadLetter` rozhraní API ve zprávě, která také umožňuje nastavit vlastní vlastnost včetně kódu důvodu, který lze načíst pomocí zprávy z fronty nedoručených zpráv.

Zvláštní případ vyrovnání je časově rozlišená položka, která je popsána v [samostatném článku](message-deferral.md).

`Complete`Operace, `Deadletter` , nebo `RenewLock` mohou selhat kvůli problémům se sítí, pokud vypršela platnost zámku, nebo existují jiné podmínky na straně služby, které brání v vyrovnání. V jednom z těchto případů služba pošle negativní potvrzení, že povrchy v klientech rozhraní API jako výjimku. Pokud je důvodem poškozené síťové připojení, zámek se zahodil, protože Service Bus nepodporuje obnovení stávajících odkazů AMQP na jiném připojení.

Pokud dojde k `Complete` chybě, která se obvykle vyskytuje na konci zpracování zprávy a v některých případech po minutách zpracování práce, přijímající aplikace může rozhodnout, zda zachovává stav práce, a při druhém doručení ignoruje stejnou zprávu, ať už Tosses výsledek práce, a pokusy o opakování při opětovném doručení zprávy.

Typický mechanismus pro identifikaci duplicitních doručení zpráv je zjištěním ID zprávy, které může a by měl být nastaven odesílatelem na jedinečnou hodnotu, případně musí být zarovnán s identifikátorem z původního procesu. Plánovač úloh by nejspíš nastavil ID zprávy na identifikátor úlohy, kterou se snaží přiřadit k pracovnímu procesu, a pracovní proces bude ignorovat druhý výskyt přiřazení úlohy, pokud je tato úloha již dokončena.

> [!IMPORTANT]
> Je důležité si uvědomit, že zámek, který PeekLock získá ve zprávě, je volatile a může dojít ke ztrátě v následujících podmínkách.
>   * Aktualizace služby
>   * Aktualizace operačního systému
>   * Změna vlastností entity (Queue, téma, Subscription) při držení zámku.
>
> Když dojde ke ztrátě zámku, Azure Service Bus vygeneruje LockLostException, který bude umístěn v klientském kódu aplikace. V takovém případě by výchozí logika klienta měla automaticky zaplatit a operaci zopakovat.

## <a name="renew-locks"></a>Prodloužit zámky
Výchozí hodnota pro trvání zámku je **30 sekund**. Můžete zadat jinou hodnotu trvání zámku na úrovni fronty nebo předplatného. Klient vlastnící zámek může obnovit zámek zprávy pomocí metod na objektu přijímače. Místo toho můžete použít funkci automatického obnovení zámků, kde můžete zadat dobu, po kterou si přejete, aby se zámek obnovil. 

## <a name="next-steps"></a>Další kroky
- Zvláštní případ vyrovnání je odložení. Podrobnosti najdete v tématu o [odložení zprávy](message-deferral.md) . 
- Další informace o nedoručených písmenech najdete v tématu [fronty nedoručených zpráv](service-bus-dead-letter-queues.md).
- Další informace o Service Bus zasílání zpráv obecně najdete v tématu [Service Bus fronty, témata a předplatná](service-bus-queues-topics-subscriptions.md) .