---
title: Zasílání zpráv Azure Service Bus – fronty, témata a předplatná
description: Tento článek obsahuje přehled entit zasílání zpráv Azure Service Bus (fronty, témata a odběry).
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/16/2020
ms.author: aschhab
ms.openlocfilehash: 3dc78a22e0e596d812d90fec63475a0b21e9164f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259510"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Fronty, témata a odběry služby Service Bus

Microsoft Azure Service Bus podporuje sadu cloudových middlewarových technologií orientovaných na zprávy, včetně spolehlivého řízení front zpráv a trvalého zasílání zpráv o publikování a odběru. Tyto "zprostředkované" možnosti zasílání zpráv si lze myslet jako oddělené funkce zasílání zpráv, které podporují publikování a přihlášení, časové oddělení a vyrovnávání zatížení scénáře pomocí úlohy zasílání zpráv Service Bus. Oddělená komunikace má mnoho výhod – klienti a servery se například můžou spojit podle potřeby a provádět své operace asynchronním způsobem.

Entity zasílání zpráv, které tvoří jádro možností zasílání zpráv v service bus jsou fronty, témata a odběry a pravidla/akce.

## <a name="queues"></a>Fronty

Fronty nabízejí doručení zpráv *FIFO první dovnitř, první ven* (FIFO) jednomu nebo více konkurenčním spotřebitelům. To znamená, že příjemci obvykle přijímat a zpracovávat zprávy v pořadí, ve kterém byly přidány do fronty a pouze jeden příjemce zprávy přijímá a zpracovává každou zprávu. Klíčovou výhodou použití front je dosažení "časového oddělení" součástí aplikace. Jinými slovy, výrobci (odesílatelé) a spotřebitelé (příjemci) nemusí odesílat a přijímat zprávy současně, protože zprávy jsou uloženy trvale ve frontě. Kromě toho výrobce nemusí čekat na odpověď od spotřebitele, aby mohl pokračovat ve zpracování a odesílání zpráv.

Související výhodou je "vyrovnávání zatížení", které umožňuje výrobcům a spotřebitelům odesílat a přijímat zprávy různými sazbami. V mnoha aplikacích se zatížení systému v průběhu času mění; doba zpracování požadovaná pro každou jednotku práce je však obvykle konstantní. Zprostředkování výrobců zpráv a spotřebitelů s frontou znamená, že náročné aplikace musí být zřízena pouze pro zpracování průměrné zatížení namísto zatížení ve špičce. S měnící se příchozí zátěží se mění hloubka fronty. Tato funkce přímo šetří peníze s ohledem na množství infrastruktury potřebné k provozu zatížení aplikace. Jak se zvyšuje zatížení, lze přidat další pracovní procesy ke čtení z fronty. Každou zprávu zpracovává jen jeden pracovní proces. Kromě toho toto vyrovnávání zatížení na základě vytahování umožňuje optimální využití pracovních počítačů i v případě, že pracovní počítače se liší s ohledem na výpočetní výkon, protože vytahovat zprávy na vlastní maximální rychlost. Tento vzorec se často nazývá "konkurenční spotřebitel" vzor.

Použití fronty mezimezi mezi výrobci zpráv a příjemci poskytuje vlastní volné párování mezi součástmi. Vzhledem k tomu, že výrobci a spotřebitelé o sobě navzájem nevědí, může být spotřebitel modernizován, aniž by to mělo vliv na výrobce.

### <a name="create-queues"></a>Vytvoření front

Fronty se vytvářejí pomocí [předlohy](service-bus-resource-manager-namespace-queue.md) [Azure Portal](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [CLI](service-bus-quickstart-cli.md)nebo Resource Manager . Potom odesílat a přijímat zprávy pomocí [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) objektu.

Chcete-li se rychle dozvědět, jak vytvořit frontu, pak odesílat a přijímat zprávy do a z fronty, naleznete [v rychlých startech](service-bus-quickstart-portal.md) pro každou metodu. Podrobnější kurz o používání front najdete v tématu [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md).

Pracovní ukázka najdete v [basicSendReceiveUsingQueueClient ukázka](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) na GitHubu.

### <a name="receive-modes"></a>Režimy příjmu

Můžete určit dva různé režimy, ve kterých service bus přijímá zprávy: *ReceiveAndDelete* nebo *PeekLock*. V režimu [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) je operace příjmu jednorázová; to znamená, že když service bus obdrží požadavek od příjemce, označí zprávu jako spotřebované a vrátí ji spotřebiteli aplikace. **ReceiveAndDelete** režim je nejjednodušší model a funguje nejlépe pro scénáře, ve kterých aplikace může tolerovat není zpracování zprávy, pokud dojde k selhání. Chcete-li pochopit tento scénář, zvažte scénář, ve kterém spotřebitel vydá požadavek na přijetí a pak dojde k chybě před jeho zpracováním. Vzhledem k tomu, že service bus označí zprávu jako spotřebované, když se aplikace restartuje a začne znovu spotřebovávat zprávy, bude mít zmeškané zprávy, která byla spotřebována před selháním.

V režimu [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) se operace příjmu stává dvoustupňovou, což umožňuje podporovat aplikace, které nemohou tolerovat chybějící zprávy. Když service bus obdrží požadavek, najde další zprávu, která má být spotřebována, uzamkne ji zabránit ostatním spotřebitelům v přijetí a vrátí ji do aplikace. Po dokončení aplikace zpracování zprávy (nebo ukládá spolehlivě pro budoucí zpracování), dokončí druhou fázi procesu příjmu voláním [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) na přijaté zprávy. Když service bus vidí **volání CompleteAsync,** označí zprávu jako spotřebované.

Pokud aplikace není schopna zpracovat zprávu z nějakého důvodu, může volat [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) metodu na přijaté zprávy (namísto [CompleteAsync).](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) Tato metoda umožňuje Service Bus odemknout zprávu a zpřístupnit ji k přijetí znovu, buď stejným spotřebitelem nebo jiným konkurenčním spotřebitelem. Za druhé, je časový limit přidružený k zámku a pokud aplikace nezpracuje zprávu před vypršením časového limitu uzamčení (například pokud dojde k chybě aplikace), pak Service Bus odemkne zprávu a zpřístupní ji k přijetí znovu (v podstatě provádí [operaci AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) ve výchozím nastavení).

V případě, že aplikace dojde k chybě po zpracování zprávy, ale před **completeAsync** požadavek je vydán, zpráva je znovu doručena do aplikace při restartování. Tento proces se často nazývá *alespoň jednou* zpracování; to znamená, že každá zpráva je zpracována alespoň jednou. V určitých situacích však může být znovu doručena stejná zpráva. Pokud scénář nemůže tolerovat duplicitní zpracování, pak je v aplikaci vyžadována další logika ke zjištění duplicit, což lze dosáhnout na základě [vlastnosti MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) zprávy, která zůstává konstantní napříč pokusy o doručení. Tato funkce se označuje jako *zpracování přesně jednou.*

## <a name="topics-and-subscriptions"></a>Témata a předplatná

Na rozdíl od fronty, ve kterém je každá zpráva zpracována jedním příjemcem, *témata* a *odběry* poskytují 1:N formu komunikace, ve vzoru *publikování/odběru.* Užitečné pro škálování na velký počet příjemců, každá publikovaná zpráva je k dispozici pro každé předplatné registrované s tématem. Zprávy jsou odesílány do tématu a doručovány do jednoho nebo více přidružených předplatných v závislosti na pravidlech filtru, která lze nastavit na základě předplatného. Odběry můžete použít další filtry omezit zprávy, které chtějí přijímat. Zprávy jsou odesílány do tématu stejným způsobem, jakým jsou odesílány do fronty, ale zprávy nejsou přijímány přímo z tématu. Místo toho jsou přijímány z předplatných. Odběr tématu se podobá virtuální frontě, která přijímá kopie zpráv, které jsou odeslány do tématu. Zprávy jsou přijímány z předplatného stejně jako způsob, jakým jsou přijímány z fronty.

Pro srovnání funkce odesílání zpráv fronty mapuje přímo na téma a jeho funkce přijímání zpráv mapuje na odběr. Tato funkce mimo jiné znamená, že předplatná podporují stejné vzory popsané dříve v této části s ohledem na fronty: konkurenční příjemce, dočasné oddělení, vyrovnávání zatížení a vyrovnávání zatížení.

### <a name="create-topics-and-subscriptions"></a>Vytvoření témat a odběrů

Vytvoření tématu je podobné vytvoření fronty, jak je popsáno v předchozí části. Potom odesílat zprávy pomocí [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient) třídy. Chcete-li přijímat zprávy, vytvořte jedno nebo více odběrů tématu. Podobně jako fronty jsou zprávy přijímány z předplatného pomocí objektu [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) namísto objektu [QueueClient.](/dotnet/api/microsoft.azure.servicebus.queueclient) Vytvořte klienta předplatného, předání název tématu, název předplatného a (volitelně) režim příjmu jako parametry.

Úplný pracovní příklad naleznete [v ukázce BasicSendReceiveUsingTopicSubscriptionClient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) na GitHubu.

### <a name="rules-and-actions"></a>Pravidla a akce

V mnoha scénářích zprávy, které mají specifické vlastnosti musí být zpracovány různými způsoby. Chcete-li povolit toto zpracování, můžete nakonfigurovat odběry najít zprávy, které mají požadované vlastnosti a potom provést určité změny těchto vlastností. Zatímco odběry služby Service Bus zobrazit všechny zprávy odeslané do tématu, můžete pouze zkopírovat podmnožinu těchto zpráv do fronty virtuální odběr. Toto filtrování se provádí pomocí filtrů předplatného. Tyto změny se nazývají *akce filtru*. Po vytvoření předplatného můžete zadat výraz filtru, který pracuje s vlastnostmi zprávy, vlastnosti systému (například **Label)** a vlastní vlastnosti aplikace (například **StoreName**.) Výraz filtru SQL je v tomto případě volitelný. bez výrazu filtru SQL, všechny akce filtru definované na předplatné bude provedena na všechny zprávy pro toto předplatné.

Úplný pracovní příklad najdete v [tématu TopicSubscriptionWithRuleOperationsSample ukázka](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) na GitHubu.

Další informace o možných hodnotách filtrů naleznete v dokumentaci pro třídy [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) a [SqlRuleAction.](/dotnet/api/microsoft.azure.servicebus.sqlruleaction)

## <a name="next-steps"></a>Další kroky

Další informace a příklady použití zasílání zpráv service bus naleznete v následujících pokročilých tématech:

* [Přehled zasílání zpráv service bus](service-bus-messaging-overview.md)
* [Rychlý start: Odesílání a přijímání zpráv pomocí webu Azure Portal a architektury .NET](service-bus-quickstart-portal.md)
* [Kurz: Aktualizace zásob pomocí portálu Azure Portal a témat/odběrů](service-bus-tutorial-topics-subscriptions-portal.md)


