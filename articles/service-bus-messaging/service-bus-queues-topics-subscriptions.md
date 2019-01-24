---
title: Přehled zasílání zpráv fronty, témata a odběry služby Azure Service Bus | Dokumentace Microsoftu
description: Přehled entity pro zasílání zpráv Service Bus.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/18/2018
ms.author: aschhab
ms.openlocfilehash: 7cacabf4f171189810e943043b5513e20113d962
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54847027"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Fronty, témata a odběry služby Service Bus

Microsoft Azure Service Bus podporuje sady založené na cloudu, objektově orientovaný middleware technologií, jako jsou služby Řízení front zpráv spolehlivého a trvalý publikování/odběr zpráv. Tyto "zprostředkovaného" přenosu zpráv můžete představit jako odpojené funkce zasílání zpráv, které podporu publikování a odběru, časové oddělení a scénářům pomocí úlohy pro zasílání zpráv služby Service Bus pro vyrovnávání zatížení. Oddělená komunikace má mnoho výhod – klienti a servery se například můžou spojit podle potřeby a provádět své operace asynchronním způsobem.

Entity zasílání zpráv, které tvoří jádro možnosti zasílání zpráv ve službě Service Bus jsou fronty, témata a odběry a pravidla a akce.

## <a name="queues"></a>Fronty

Fronty nabízejí *First In, Out první* doručování zpráv (metodou FIFO) na jeden nebo několik konkurenčních spotřebitelů. To znamená, že příjemci obvykle příjem a zpracování zpráv v pořadí, ve kterém byly přidány do fronty, a jenom jeden spotřebitel zprávy obdrží a zpracuje každou zprávu. Klíčovou výhodou použití front je dosáhnout "časové oddělení" komponent aplikace. Jinými slovy producenti (odesílatelé) a spotřebitelé (příjemci) nemají k odesílání a přijímání zpráv ve stejnou dobu, protože zprávy se ukládají odolným způsobem ve frontě. Producent navíc není nutné čekat na odpověď od příjemce, aby bylo možné pokračovat se zpracováním a odesláním zprávy.

Výhodou je, "vyrovnávání zátěže," umožňující odesílatelům a spotřebitelům umožňuje odesílat a přijímat zprávy různými rychlostmi. V mnoha aplikacích zatížení systému může postupně měnit; Nicméně zpracování čas potřebný pro každou jednotku práce je obvykle stálá. Propojovací producenti a spotřebitelé zpráv s frontou znamená, že spotřebitelskou aplikací pouze musí být zřízená být schopná zpracovat průměrné zatížení namísto zátěž ve špičce. S měnící se příchozí zátěží se mění hloubka fronty. Tato možnost znamená přímou úsporu nákladů s ohledem na velikost infrastruktury nutné pro zvládání zatížení aplikace. Při rostoucí zátěži, lze přidat další pracovní procesy pro čtení z fronty. Každou zprávu zpracovává jen jeden pracovní proces. Kromě toho tato Vyrovnávání zatížení založené na operaci pull umožňuje optimální využívání pracovních počítačů i v případě, že jde o výpočetní výkon se liší pracovní počítače podle jejich vlastní maximální rychlostí o přijetí změn zprávy. Tento model se často označovány jako vzor "soutěžit příjemce".

Použití front pro zprostředkující mezi producenti a spotřebitelé zpráv poskytuje vlastní volné párování mezi komponentami. Protože producenti a spotřebitelé nemají mezi sebou, je možné upgradovat příjemce bez nutnosti žádný vliv na výrobce.

### <a name="create-queues"></a>Vytvoření fronty

Vytvoření fronty pomocí [webu Azure portal](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [rozhraní příkazového řádku](service-bus-quickstart-cli.md), nebo [šablon Resource Manageru](service-bus-resource-manager-namespace-queue.md). Potom odesílat a přijímat zprávy pomocí [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) objektu.

Rychle se naučíte, jak vytvořit frontu, pak odesílání a příjem zpráv z fronty a, najdete v článku [rychlých startů](service-bus-quickstart-portal.md) pro jednotlivé metody. Více podrobný kurz o tom, jak používat fronty, naleznete v tématu [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md).

Pracovní ukázku najdete v tématu [BasicSendReceiveUsingQueueClient ukázka](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) na Githubu.

### <a name="receive-modes"></a>Zobrazí režimy

Můžete zadat dva různé režimy, ve kterých Service Bus přijme zprávy: *ReceiveAndDelete* nebo *PeekLock*. V [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) režimu operace receive je jednorázová; to znamená, když Service Bus přijme požadavek, označí zprávu jako spotřebovávanou a vrátí ji do aplikace. **ReceiveAndDelete** režimu je nejjednodušší model a funguje nejlépe pro scénáře, ve kterých aplikace může tolerovat možnost, není zpracovává zprávu, pokud dojde k chybě. Informace o tom tento scénář, vezměte v úvahu scénář, ve kterém spotřebitel požadavek na přijetí a poté dojde k chybě před její zpracování. Vzhledem k tomu, že Service Bus, označí zprávu jako spotřebovávanou, když se aplikace restartuje a začne znovu přijímat zprávy, ji budou pravděpodobně nenalezlo zprávu, která se spotřebovala před pádem vynechá.

V [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) režimu operace obdržení stane dvoufázová, takže je možné to podporuje aplikace, které nemůžou tolerovat vynechání zpráv. Když Service Bus přijme požadavek, najde zprávu, který se má používat, uzamkne ji ostatní uživatelé z dešifrujete proti a vrátí ji do aplikace. Poté, co aplikace dokončí zpracování zprávy (nebo spolehlivě uloží pro pozdější zpracování), dokončení druhé fáze přijetí voláním [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) na přijatou zprávu. Když Service Bus uvidí **CompleteAsync** volání, označí zprávu jako spotřebovávanou.

Pokud aplikace nedokáže zpracovat zprávu z nějakého důvodu, může zavolat [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) metoda na přijatou zprávu (místo [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)). Tato metoda umožňuje využívat služby Service Bus zprávu odemkne a zpřístupní ji pro další přijetí, stejný příjemce nebo jiných konkurenčních spotřebitelů. Za druhé časový limit přidružené k uzamčení a pokud se aplikaci nepodaří zprávu zpracovat před zámku vyprší časový limit (například pokud aplikace spadne), pak služby Service Bus zprávu odemkne a zpřístupňuje je k dispozici bude přijetí) v podstatě provádění [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) operace ve výchozím nastavení).

V případě, že aplikace spadne po zpracování zprávy, ale předtím, než **CompleteAsync** požadavku, zprávu je víckrát do aplikace při restartování. Tento proces se často nazývá *nejméně jednou* zpracování; to znamená, že každá zpráva se zpracuje alespoň jednou. Ale v určitých situacích může doručit víckrát. Pokud scénář nejde tolerovat duplicitní zpracování, pak se vyžaduje další logiku v aplikaci duplicity, které lze dosáhnout na základě [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) vlastnosti zprávy, která zůstává konstantní napříč pokusy o doručení. Tato funkce se označuje jako *právě jednou* zpracování.

## <a name="topics-and-subscriptions"></a>Témata a předplatná

Na rozdíl od front, ve kterých každou zprávu zpracuje jeden spotřebitel, *témata* a *předplatná* zadat jeden mnoho forma komunikace, *publikování/odběr* vzor. Užitečné pro škálování na velký počet příjemců, každá publikovaná zpráva je k dispozici všem odběrům registrovaným pro příslušné téma. Zprávy odeslané do tématu a doručí do jednoho nebo více přidružených předplatných, v závislosti na pravidel filtrů, které lze nastavit na základě za předplatné. Předplatná můžete použít další filtry pro omezení, které chtějí dostávat zprávy. Se odesílají zprávy do tématu stejným způsobem jsou odeslána do fronty, ale nejsou přijaty zprávy z tématu přímo. Místo toho byly přijaty z předplatných. Předplatné tématu se podobá virtuální frontě, která obdrží kopii zprávy odeslané do tématu. Dostávají zprávy z odběru identicky způsobu, jakým dostali z fronty.

Mimo jiné porovnání funkce zasílání zpráv pro fronty mapuje přímo na téma a jeho funkce přijímají zprávy se mapuje na předplatné. Tato funkce mimo jiné znamená, že předplatné podporovat stejné vzorce popsané dříve v této části s ohledem na front: konkurence, časové oddělení, Vyrovnávání zatížení a vyrovnávání zatížení.

### <a name="create-topics-and-subscriptions"></a>Vytvoření témata a odběry

Vytvoření tématu je podobné jako vytvoření fronty, jak je popsáno v předchozí části. Potom odesílání zpráv s použitím [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient) třídy. Pokud chcete přijímat zprávy, můžete vytvořit jeden nebo více odběrů na téma. Podobně jako u front, jsou zprávy přijímány pomocí předplatného [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) místo objektu [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) objektu. Vytvoření klienta předplatného, předejte název tématu, název předplatného a (volitelně) režim receive jako parametry.

Pro úplný práce příkladu najdete v článku [BasicSendReceiveUsingTopicSubscriptionClient ukázka](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) na Githubu.

### <a name="rules-and-actions"></a>Pravidla a akce

V mnoha případech je nutné zpracovat zprávy, které mají určité charakteristiky různými způsoby. Pokud chcete povolit toto zpracování, můžete nakonfigurovat odběry najít zprávy, které mají požadované vlastnosti a pak provést některé změny na tyto vlastnosti. Zatímco odběry služby Service Bus zobrazit všechny zprávy odeslané do tématu, kopírovat můžete pouze podmnožinu těchto zpráv do fronty virtuální odběru. Toto filtrování se provádí pomocí filtry předplatných. Tyto změny se nazývají *akce filtru*. Když se předplatné, můžete zadat výraz filtru, který funguje ve vlastnostech zprávy systému vlastnosti (například **popisek**) a vlastní aplikace (například  **StoreName**.) V tomto případě; je volitelný výraz filtru SQL bez výraz filtru SQL se provede u všech zpráv pro dané předplatné libovolný filtr akce definované v rámci předplatného.

Pro úplný práce příkladu najdete v článku [TopicSubscriptionWithRuleOperationsSample ukázka](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) na Githubu.

Další informace o hodnotách filtru je to možné, naleznete v dokumentaci pro [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) a [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) třídy.

## <a name="next-steps"></a>Další postup

Další informace a příklady použití zasílání zpráv Service Bus najdete v následujících Pokročilá témata:

* [Přehled přenosu zpráv ve službě Service Bus](service-bus-messaging-overview.md)
* [Rychlé zprovoznění: Odesílání a příjem zpráv pomocí webu Azure portal a .NET](service-bus-quickstart-portal.md)
* [Kurz: Aktualizovat inventáře pomocí webu Azure portal a témata nebo předplatná](service-bus-tutorial-topics-subscriptions-portal.md)


