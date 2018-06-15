---
title: Asynchronní zasílání zpráv Service Bus | Microsoft Docs
description: Popis asynchronní zasílání zpráv Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: e48e95d99847e68bdb218b341ad2fbcd44eb31f4
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2018
ms.locfileid: "28929967"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Asynchronní schémata zasílání zpráv a vysoká dostupnost

Asynchronní zasílání zpráv, můžou se implementovat v mnoha různými způsoby. Pomocí fronty, témata a odběry Azure Service Bus podporuje asynchronism prostřednictvím dopředného mechanismus a úložiště. V běžném provozu (synchronní) odesílání zpráv do front a témat a příjem zpráv z fronty a odběry. Aplikace, které můžete psát závisí na těchto entitách se vždy k dispozici. Když se změní stav entity, z důvodu z různých důvodů, potřebujete způsob, jak poskytnout omezenou schopnost entita, která může stát odpovědí na většinu potřeb.

Aplikace můžete povolit různé scénáře komunikace obvykle pomocí asynchronními vzory zasílání zpráv. Můžete vytvořit aplikace, ve kterých klienti mohou zasílat zprávy do služby, i když služba není spuštěná. Pro aplikace na úrovni tohoto prostředí, které může pomoci shluky komunikace, fronty zatížení tím, že poskytuje místo pro komunikaci mezi vyrovnávací paměti. Nakonec můžete získat Vyrovnávání zatížení jednoduchý, ale efektivní distribuci zprávy mezi různými počítači.

Aby byla zachována dostupnosti kterékoli z těchto entit, vezměte v úvahu počet různé způsoby, ve kterém může vyskytovat tyto entity není k dispozici pro odolný systém zasílání zpráv. Obecně řečeno vidíme entity k dispozici pro aplikace, kterou jsme napsali následující různými způsoby:

* Nelze odeslat zprávy.
* Nelze přijímat zprávy.
* Nelze spravovat entity (vytvořit, získat, aktualizovat nebo odstranit entity).
* Nebylo možné kontaktovat službu.

Pro každý z těchto chyb selhání různé režimy existují, které aplikace mohly pokračovat v práci na určité úrovni snížené schopnosti. Například systém, který může odesílat zprávy, ale není přijímat můžete nadále získávat objednávek zákazníků, ale nemůže zpracovat tyto objednávky. Toto téma popisuje potenciální problémy, ke kterým dochází, a jak jsou omezeny těchto problémů. Service Bus zavedla několika způsoby zmírnění, které musí přihlásíte k odběru a toto téma taky popisuje pravidla pro používání těchto jejich zmírnění výslovný souhlas.

## <a name="reliability-in-service-bus"></a>Spolehlivost v Service Bus
Existuje několik způsobů pro zpracování zprávy a entity problémy a jsou pokyny pro příslušné užívání tyto způsoby zmírnění rizik. Zjistit, podle pokynů, musíte nejprve porozumět, co může selhat v Service Bus. Vzhledem k řešení Azure systémy všechny tyto problémy jsou obvykle krátkodobou. Na vysoké úrovni zobrazit různé příčiny nedostupnosti takto:

* Omezení z externího systému, na kterém závisí Service Bus. Omezení dojde na interakce s úložiště a výpočetní prostředky.
* Problém pro systém, na kterém závisí Service Bus. Například určitá část úložiště může dojít k potížím.
* Chyba služby Service Bus na jednom subsystému. V takovém případě výpočetním uzlu můžete získat v nekonzistentním stavu a musí restartujte samostatně, způsobuje všechny entity, které slouží k jiným uzlům Vyrovnávání zatížení. To zase způsobit na krátkou dobu zpracování pomalé zprávy.
* Chyba služby Service Bus v rámci datového centra Azure. Jedná se o "závažnou chybu" během které systému nedostupný pro mnoho minut nebo několik hodin.

> [!NOTE]
> Termín **úložiště** může zahrnovat Azure Storage a SQL Azure.
> 
> 

Service Bus obsahuje počet jejich zmírnění pro tyto problémy. Následující části popisují jednotlivé problémy a jejich odpovídajících jejich zmírnění.

### <a name="throttling"></a>Omezování
Službou Service Bus omezování umožňuje správu míra spolupráci zprávy. Každý uzel jednotlivé služby Service Bus je umístěno mnoho entit. Každý z těchto entit vytváří požadavky na systém z hlediska využití procesoru, paměti, úložiště a jiné omezující vlastnosti. Když některé z těchto omezující vlastnosti zjistí využití který překračuje definované prahové hodnoty, Service Bus můžete odepřít daného požadavku. Volající obdrží [ServerBusyException] [ ServerBusyException] a opakování za 10 sekund.

Jako omezení rizik musíte kód čtení chybu a zastaví všechny opakování zprávy alespoň 10 sekund. Vzhledem k tomu, že mezi částí aplikace zákazníka může dojít k chybě, očekává se, že každého jednotlivého nezávisle provede logika opakovaných pokusů. Kód může snížit pravděpodobnost omezené tím, že umožňuje vytváření oddílů na se fronta nebo téma.

### <a name="issue-for-an-azure-dependency"></a>Problém pro Azure závislostí
Ostatní součásti v rámci Azure občas může mít problémy služby. Například pokud probíhá upgrade systému, který používá Service Bus, daný systém může dočasně mají omezenou možnosti. Obejít těchto typů problémů, Service Bus pravidelně prověří a implementuje způsoby zmírnění rizik. Vedlejší efekty tyto způsoby zmírnění zobrazí. Service Bus implementuje pro zpracování přechodné problémy s úložištěm, systém, který umožňuje operace odesílání zpráv do pracují konzistentně. Vzhledem k povaze zmírnění odeslaná zpráva může trvat až 15 minut se zobrazí v ovlivněných fronty nebo předplatné a připravený pro operace příjmu. Obecně řečeno většiny entit nebude k tomuto problému dojde. Však získá počet entit v Service Bus v Azure, tohoto omezení je někdy nutné pro malou zákazníků služby Service Bus.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Service Bus selhání v jednom subsystému
S libovolnou aplikací okolností může způsobit interní součást Service Bus nekonzistenci. Když Service Bus to zjistí, shromažďuje data z aplikace, které pomáhají při diagnostice, co se stalo. Jakmile data jsou shromažďována, aplikace se restartuje v pokus vrátit do konzistentního stavu. Tento proces probíhá poměrně rychle a jsou mnohem kratší výsledky v entitě zobrazování být k dispozici pro až několik minut, i když je typické dobu mimo provoz.

V těchto případech klientská aplikace generuje [System.TimeoutException] [ System.TimeoutException] nebo [MessagingException] [ MessagingException] výjimka. Service Bus obsahuje zmírnění dopadů pro tento problém ve formě logika opakovaných pokusů automatizované klienta. Jakmile vyčerpá. doba opakování a zpráva doručeno, můžete prozkoumat pomocí dalších funkcí, jako třeba [spárovat obory názvů][paired namespaces]. Obory názvů spárované mít další upozornění, které jsou popsané v tomto článku.

### <a name="failure-of-service-bus-within-an-azure-datacenter"></a>Selhání v rámci datového centra Azure Service Bus
Nejpravděpodobnější důvod selhání v datovém centru Azure je neúspěšné nasazení upgradu služby Service Bus nebo závislé systému. Jak se používá na platformu, co pravděpodobnost, že tento typ selhání. Selhání datacenter může také vyskytnout z důvodů, které zahrnují následující:

* Elektrický výpadek (napájení a generování power zmizí).
* Připojení (internet zalomení mezi klienty a Azure).

V obou případech havárie přírodních nebo syntetická způsobila problém. Chcete-li tento problém obejít a ujistěte se, že může i dál posílat zprávy, můžete použít [spárovat obory názvů] [ paired namespaces] zpráv k odeslání na jiné místo, zatímco primární umístění se provádí v pořádku znovu povolit. Další informace najdete v tématu [osvědčené postupy pro izolační aplikace proti výpadkům Service Bus a havárií][Best practices for insulating applications against Service Bus outages and disasters].

## <a name="paired-namespaces"></a>Spárované obory názvů
[Spárovat obory názvů] [ paired namespaces] funkce podporuje scénáře, ve kterém entity služby sběrnice nebo nasazení v rámci datového centra k dispozici. Pokud k této události dochází zřídka, distribuovaných systémů stále musí být připraveny pro zpracování nejhorší scénářích případů. Obvykle tato událost se stane, protože některé elementu, na kterém závisí Service Bus dochází krátkodobé problém. K udržování dostupnosti aplikace během výpadku, Service Bus mohou uživatelé dva samostatné obory názvů, pokud možno v samostatných datových centrech hostitele jejich entit pro zasílání zpráv. Zbývající část Tato část používá následující terminologií:

* Primární obor názvů: obor názvů, pomocí kterého aplikace komunikuje pro odesílání a přijímat operace.
* Sekundární obor názvů: obor názvů, který slouží jako záložní pro primární obor názvů. Aplikace logiky nekomunikuje s Tento obor názvů.
* Převzetí služeb při selhání interval: množství času, tak, aby přijímal normální selhání, než aplikace přepíná z oboru názvů primární na sekundární obor názvů.

Spárovat obory názvů podporu *odeslat dostupnosti*. Modul Send dostupnosti zachovává umožňuje odesílání zpráv. Pokud chcete používat odesílání dostupnosti, musí aplikace splňovat následující požadavky:

1. Zprávy jsou pouze přijatých z primární oboru názvů.
2. Zprávy odeslané do dané fronty nebo téma dodány ve správném pořadí.
3. Zprávy v rámci relace může být dodány ve správném pořadí. Toto je zalomení z normální funkce relací. To znamená, že vaše aplikace používá relací logicky seskupovat zprávy.
4. Stav relace je zachováno pouze na primární oboru názvů.
5. Primární fronty můžete uvést do režimu online a začít přijímat zprávy před sekundární fronty nabízí všechny zprávy ve frontě primární.

Následující části popisují rozhraní API, jak jsou implementované rozhraní API a zobrazuje ukázkový kód, který používá funkci. Všimněte si, že jsou fakturace důsledky spojené s touto funkcí.

### <a name="the-messagingfactorypairnamespaceasync-api"></a>MessagingFactory.PairNamespaceAsync rozhraní API
Obsahuje funkci spárované obory názvů [PairNamespaceAsync] [ PairNamespaceAsync] metodu [Microsoft.ServiceBus.Messaging.MessagingFactory] [ Microsoft.ServiceBus.Messaging.MessagingFactory] třídy:

```csharp
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

Po dokončení úlohy párování obor názvů je také dokončení a připravena k provedení akce pro libovolný [MessageReceiver][MessageReceiver], [QueueClient][QueueClient], nebo [TopicClient] [ TopicClient] vytvořené pomocí [MessagingFactory] [ MessagingFactory] instance. [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions] [ Microsoft.ServiceBus.Messaging.PairedNamespaceOptions] je základní třídou pro různé typy párování, které jsou k dispozici [MessagingFactory] [ MessagingFactory] objektu. V současné době pouze odvozené třídy je jednu s názvem [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions], který implementuje dostupnosti požadavky na odeslání. [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] obsahuje sadu konstruktory, které sestavení na sobě navzájem. Prohlížení konstruktoru se většina parametry, můžete porozumět chování další konstruktory.

```csharp
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Tyto parametry mají následující významy:

* *secondaryNamespaceManager*: inicializovali [NamespaceManager] [ NamespaceManager] instance pro sekundární obor názvů, který [PairNamespaceAsync] [ PairNamespaceAsync] metodu můžete použít k nastavení sekundární obor názvů. Správce obor názvů se používá k získání seznamu front v oboru názvů a ujistěte se, že existují požadované nevyřízené položky fronty. Pokud tyto fronty nejsou k dispozici, jejich vytvoření. [NamespaceManager] [ NamespaceManager] vyžaduje schopnost vytvářet token se **spravovat** deklarací identity.
* *messagingFactory*: [MessagingFactory] [ MessagingFactory] instance pro sekundární obor názvů. [MessagingFactory] [ MessagingFactory] objekt se používá k odeslání a pokud [EnableSyphon] [ EnableSyphon] je nastavena na **true**, přijímat zprávy z front nevyřízených položek.
* *backlogQueueCount*: počet nevyřízených položek fronty pro vytvoření. Tato hodnota musí být alespoň 1. Při odesílání zpráv k backlogu, jednu z těchto front náhodně vybere. Pokud nastavíte hodnotu na 1, můžete použít někdy tento pouze jedna fronta. Pokud k tomu dojde a jeden nevyřízené položky fronty generuje chyby, klient není schopna zkuste jinou nevyřízené položky fronty a odeslání vaší zprávy se nemusí podařit. Doporučujeme tuto hodnotu nastavíte na některé větší hodnotu a výchozí hodnotu 10. Toto můžete změnit na hodnotu vyšší nebo nižší v závislosti na množství dat, které vaše aplikace odešle za den. Každá fronta nevyřízené položky mohou být uloženy až 5 GB zpráv.
* *failoverInterval*: množství času, během kterého bude přijímat chyb v oboru názvů primární před přepnutím jednu entitu do sekundární oboru názvů. Na základě entity entity dojde k převzetí služeb při selhání. Entity v jeden obor názvů často za provozu v různých uzlech v rámci služby Service Bus. Selhání v jedné entity neznamená selhání v jiném. Tuto hodnotu můžete nastavit [System.TimeSpan.Zero] [ System.TimeSpan.Zero] převzetí služeb při selhání na sekundární okamžitě po selhání vaše první, pouze dočasné. Selhání, které aktivují časovač převzetí služeb při selhání jsou [MessagingException] [ MessagingException] ve kterém [IsTransient] [ IsTransient] vlastnost je nastavena hodnota false, nebo [System.TimeoutException][System.TimeoutException]. Ostatní výjimky, jako například [UnauthorizedAccessException] [ UnauthorizedAccessException] nezpůsobí převzetí služeb při selhání, protože označují, že je klient nakonfigurován nesprávně. A [ServerBusyException] [ ServerBusyException] nemá příčina převzetí služeb při selhání protože správné vzor je 10 sekund čekání zprávu odešlete znovu.
* *enableSyphon*: Určuje, že tato konkrétní párování by také syphon zprávy z oboru názvů sekundární zpět do primární oboru názvů. Obecně platí, nastavte tuto hodnotu na aplikace, které odesílají zprávy **false**; aplikace, které přijímají zprávy měli nastavit hodnotu **true**. Důvodem je, že často, existují méně příjemci zprávy než odesílatelé zpráv. V závislosti na počet příjemců můžete mít jednu aplikaci instanci, zpracování činnosti Trativod. Použití mnoha příjemci má fakturační důsledky pro každou frontu nevyřízených položek.

Chcete-li použít kód, vytvořte primární [MessagingFactory] [ MessagingFactory] instance, sekundární [MessagingFactory] [ MessagingFactory] instance, sekundární [NamespaceManager] [ NamespaceManager] instance a [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] instance. Volání může být stejně jednoduché jako následující:

```csharp
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

Když úloha vrácený [PairNamespaceAsync] [ PairNamespaceAsync] metoda dokončí, je všechno nastavené a připravené k použití. Předtím, než se vrátí úlohu, pravděpodobně nebyly dokončeny, všechny potřebné pro spárování pracovat přímo práce na pozadí. V důsledku toho by neměl spustit, odesílání zpráv, dokud vrátí úlohu. Pokud došlo k jeho selhání, třeba chybná přihlašovací údaje nebo selhání vytvoření nevyřízených položek fronty, budou tyto výjimky vyvolány po dokončení úlohy. Jakmile vrátí úlohu, ověřte, zda byly fronty najít nebo vytvořit tak, že prověří [BacklogQueueCount] [ BacklogQueueCount] vlastnost na vaše [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] instance. Pro předchozí kód, který operaci vypadat takto:

```csharp
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili základy asynchronního zasílání zpráv ve sběrnici Service Bus, přečtěte si další informace o [spárovat obory názvů][paired namespaces].

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PairNamespaceAsync_Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_EnableSyphon
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_BacklogQueueCount
[paired namespaces]: service-bus-paired-namespaces.md
