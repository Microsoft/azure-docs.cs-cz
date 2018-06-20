---
title: Přehled fronty, témata a odběry pro zasílání zpráv Azure Service Bus | Microsoft Docs
description: Přehled služby Service Bus entity pro zasílání zpráv.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 06/18/2018
ms.author: sethm
ms.openlocfilehash: 424004a2a39bd0d05bce515dc17685e60f7a0c9b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231572"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Fronty, témata a odběry služby Service Bus

Microsoft Azure Service Bus podporuje sadu založené na cloudu, orientovaný na zprávy middleware technologie včetně služby Řízení front zpráv spolehlivé a trvanlivé publikování a přihlášení k odběru zasílání zpráv. Tyto funkce "zprostředkovaného" přenosu zpráv můžete představit jako odpojené zasílání zpráv funkce, které podpora publikování a odběru, časové oddělení a scénáře s využitím zatížení zasílání zpráv Service Bus Vyrovnávání zatížení. Oddělená komunikace má mnoho výhod – klienti a servery se například můžou spojit podle potřeby a provádět své operace asynchronním způsobem.

Entit pro zasílání zpráv, které tvoří základní možnosti zasílání zpráv ve sběrnici Service Bus jsou fronty, témata a odběry a pravidla nebo akce.

## <a name="queues"></a>Fronty

Fronty nabízejí *First In, Out první* doručování zpráv (metodou FIFO) na jeden nebo několik konkurenčních spotřebitelů. To znamená, že příjemci obvykle obdrží a zpracování zpráv v pořadí, ve kterém byly přidány do fronty, a jenom jeden spotřebitel zprávy obdrží a zpracuje každou zprávu. Klíčovou výhodou použití front je dosáhnout "časové oddělení" součástí aplikace. Jinými slovy producenti (odesílatelé) a spotřebitelé (příjemci) nemusí být odesílání a přijímání zpráv ve stejnou dobu, protože zprávy jsou bezpečně uložené ve frontě. Kromě toho Autor nemusí čekat na odpověď od příjemce, aby bylo možné pokračovat se zpracováním a odesláním zprávy.

Výhodou je, "vyrovnávání zátěže," což umožňuje odesílatelům a spotřebitelům umožňuje odesílat a přijímat zprávy různými rychlostmi. V mnoha aplikacích zatížení systému se liší v čase; ale zpracování čas potřebný pro jednotlivé jednotky práce je obvykle stálá. Propojovací producenti a spotřebitelé zpráv s frontou znamená, že spotřebitelskou aplikaci pouze musí být zřízená být schopna zpracovávat průměrnou zátěž místo zátěž ve špičce. S měnící se příchozí zátěží se mění hloubka fronty. Tato možnost znamená přímou úsporu nákladů s ohledem na množství infrastruktury nutné pro zvládání zatížení aplikace. Při rostoucí zátěži, lze přidat další pracovní procesy ke čtení z fronty. Každou zprávu zpracovává jen jeden pracovní proces. Kromě toho tato Vyrovnávání zatížení založené na operaci pull umožňuje optimální využívání pracovních počítačů i v případě, že pracovní počítače liší s ohledem na výpočetní výkon, jak se budou načítat zprávy na svou vlastním maximální rychlostí. Toto chování se často říká vzor "neslučitelných příjemce".

Pomocí fronty pro zprostředkující mezi producenti a spotřebitelé zpráv poskytuje vyplývajících volné párování mezi součástmi. Protože producenti a spotřebitelé nemají informace o sobě navzájem, bez nutnosti nijak neprojeví na Autor lze upgradovat příjemce.

### <a name="create-queues"></a>Vytvoření fronty

Vytvoření fronty pomocí [portál Azure](service-bus-quickstart-portal.md), [prostředí PowerShell](service-bus-quickstart-powershell.md), [rozhraní příkazového řádku](service-bus-quickstart-cli.md), nebo [šablony Resource Manageru](service-bus-resource-manager-namespace-queue.md). Pak odesílat a přijímat zprávy pomocí [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) objektu. 

Rychle zjistěte, jak vytvořit frontu, pak odesílat a přijímat zprávy do a z fronty, najdete v článku [– elementy QuickStart](service-bus-quickstart-portal.md) pro každou metodu. Více podrobný kurz o tom, jak používat fronty, najdete v části [začít pracovat s fronty Service Bus](service-bus-dotnet-get-started-with-queues.md). 

Ukázku práce, najdete [BasicSendReceiveUsingQueueClient ukázka](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) na Githubu.

### <a name="receive-modes"></a>Zobrazí režimy

Můžete zadat dvou odlišných režimů, ve kterých Service Bus přijme zprávy: *ReceiveAndDelete* nebo *PeekLock*. V [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) režim, operace příjmu je jednorázová; to znamená, když Service Bus přijme požadavek, označí zprávu jako spotřebovávanou a vrátí ji do aplikace. **ReceiveAndDelete** režimu je nejjednodušší model a funguje nejlépe ve scénářích, kde aplikace může tolerovat se zpráva nezpracuje, pokud dojde k chybě. Zjistit, tento scénář, vezměte v úvahu scénář, ve kterém spotřebitel vyšle požadavek na přijetí a pak dojde k chybě před zpracováním ho. Vzhledem k tomu, že Service Bus, označí zprávu jako spotřebovávanou, když se aplikace restartuje a začne znovu přijímat zprávy, se neuskutečnily zprávu, která se spotřebovala před havárii.

V [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) režimu operace příjmu stane dvoufázová, které umožňuje podporuje aplikace, které nemůžou tolerovat vynechání zpráv. Když Service Bus přijme požadavek, najde zprávu, který se má používat, uzamkne ji proti spotřebování jinými odběrateli příjem ho a vrátí ji do aplikace. Když aplikace dokončí zpracování zprávy (nebo ji bezpečně uloží pro pozdější zpracování), tím potvrdí dokončení druhé fáze přijetí volání [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) na přijatou zprávu. Když Service Bus uvidí **CompleteAsync** volání, označí zprávu jako spotřebovávanou.

Pokud aplikace nemůže zpracovat zprávu z nějakého důvodu, může zavolat [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) metoda na přijatou zprávu (místo [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)). Tato metoda umožňuje Service Bus zprávu odemkne a zpřístupní ji pro další přijetí, příjemci stejné nebo jiné konkurence mezi spotřebiteli. Za druhé je vypršení časového limitu přidružené zámek a aplikace nepodaří zprávu zpracovat vyprší časový limit uzamčení (například pokud aplikace spadne), pak Service Bus zprávu odemkne a je-li k dispozici být přijata znovu) v podstatě provádění [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) operace ve výchozím nastavení).

V případě, že aplikace spadne po zpracování zprávy, ale předtím, než **CompleteAsync** požadavku, zpráva se víckrát do aplikace odešle znovu. Tento proces se často nazývá *nejméně jednou* zpracování; to znamená, že každá zpráva se zpracuje alespoň jednou. Ale v některých situacích může doručit víckrát stejnou zprávu. Pokud scénář nemůže tolerovat duplicitní zpracování, pak je potřeba další logiku v aplikaci duplicity, které lze dosáhnout na základě [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) vlastnosti zprávy, která zůstává konstantní napříč pokusy o doručení. Tato funkce se označuje jako *právě jednou* zpracování.

## <a name="topics-and-subscriptions"></a>Témata a předplatná

Na rozdíl od front, ve kterých každou zprávu zpracuje jeden spotřebitel, *témata* a *odběry* poskytovat ve formuláři na více komunikace, *publikování a přihlášení k odběru* vzor. Užitečné pro škálování na velký počet příjemců, každá publikovaná zpráva je k dispozici všem odběrům registrovaným pro příslušné téma. Zprávy odeslané do tématu se doručí na jeden nebo více přidružených odběrů, v závislosti na pravidla filtru, které lze nastavit na základě za předplatné. Odběry můžete použít další filtry a omezit zprávy, které chcete dostávat. Se odesílají zprávy do tématu stejným způsobem, se odešlou do fronty, ale nejsou v tématu přímo přijaty zprávy. Místo toho jsou přijímány z předplatného. Předplatné tématu se podobá virtuální frontě, která obdrží kopii zprávy, které jsou odeslány do tématu. Zprávy se přijaté z odběru stejně jako způsob příjmu z fronty.

Prostřednictvím porovnání funkci zasílání zpráv fronty mapuje přímo do tématu a jeho funkce příjem zpráv se mapuje na předplatné. Kromě jiných věcí, tato funkce znamená, že předplatná podporovat stejné vzory dříve popisované v této části s ohledem na fronty: konkurence mezi spotřebiteli, časové oddělení, Vyrovnávání zatížení a vyrovnávání zatížení.

### <a name="create-topics-and-subscriptions"></a>Vytvoření témat a odběrů

Vytvoření tématu je podobná vytváření fronty, jak je popsáno v předchozí části. Pak odesílat zprávy pomocí [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient) třídy. Chcete-li přijímat zprávy, vytvořte jeden nebo více odběrů na téma. Podobně jako u front, jsou přijaty zprávy pomocí předplatného [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) objektu místo [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) objektu. Vytvoření klienta předplatné, předávání název tématu, názvu předplatné a (volitelně) režimu receive jako parametry. 

Pro úplnou práce příkladu najdete v článku [BasicSendReceiveUsingTopicSubscriptionClient ukázka](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) na Githubu.

### <a name="rules-and-actions"></a>Pravidla a akce

V mnoha případech je nutné zpracovat zprávy, které mají určité charakteristické vlastnosti různými způsoby. Pokud chcete povolit zpracování, můžete nakonfigurovat odběry najít zprávy, které mají požadovaných vlastností a pak provést určité úpravy tyto vlastnosti. Při odběry služby Service Bus zobrazit všechny zprávy odeslané do tématu, kopírovat můžete pouze podmnožinu těchto zpráv do fronty virtuální odběru. Tento filtrování se provádí pomocí filtrů odběrů. Tyto změny se nazývají *akce filtru*. Když je vytvořen předplatné, můžete zadat výraz filtru, který funguje na vlastnosti zprávy, vlastnosti systému (například **popisek**) a vlastností vlastní aplikaci (například  **StoreName**.) Výraz filtru SQL je volitelné v tomto případě; bez výraz filtru SQL filtru akce definované na předplatné bude třeba provést na všechny zprávy pro toto předplatné.

Pro úplnou práce příkladu najdete v článku [TopicSubscriptionWithRuleOperationsSample ukázka](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) na Githubu.

Další informace o možných filtru hodnoty, najdete v dokumentaci pro [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) a [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) třídy. 

## <a name="next-steps"></a>Další postup

Další informace a příklady použití zasílání zpráv Service Bus najdete v následující témata:

* [Přehled přenosu zpráv ve službě Service Bus](service-bus-messaging-overview.md)
* [Rychlý úvod: Odesílat a přijímat zprávy pomocí portálu Azure a rozhraní .NET](service-bus-quickstart-portal.md)
* [Kurz: Aktualizace inventáře pomocí portálu Azure a témata nebo odběrů](service-bus-tutorial-topics-subscriptions-portal.md)


