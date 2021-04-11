---
title: Azure Service Bus zasílání zpráv – fronty, témata a odběry
description: Tento článek poskytuje přehled entit zasílání zpráv Azure Service Bus (fronty, témata a odběry).
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: ccd91d5091dbbba072cf222e5a73c3db1d48c321
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107209820"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Fronty, témata a odběry služby Service Bus
Azure Service Bus podporuje sadu cloudových technologií orientovaných na zprávy, včetně spolehlivé služby Řízení front zpráv a trvalého publikování/odběru zpráv. Tyto možnosti zprostředkujícího zasílání zpráv se dají představit jako funkce odděleného zasílání zpráv, které podporují publikování a dočasná odhlašování a vyrovnávání zatížení pomocí úlohy Service Busho zasílání zpráv. Oddělená komunikace má mnoho výhod. Například klienti a servery se mohou připojit podle potřeby a provádět jejich operace asynchronním způsobem.

Mezi entity zasílání zpráv, které tvoří základní možnosti zasílání zpráv v Service Bus, patří **fronty**, **témata a odběry** a pravidla a akce.

## <a name="queues"></a>Fronty
Fronty nabízejí doručování zpráv **First in, First out** (FIFO) do jednoho nebo více konkurenčních zákazníků. To znamená, že příjemci obvykle obdrží a zpracovávají zprávy v pořadí, ve kterém byly přidány do fronty. A pouze jeden příjemce zprávy obdrží a zpracuje každou zprávu. Klíčovou výhodou použití front je zajištění dočasného odznamenání **komponent aplikace**. Jinými slovy, producenti (odesílatelé) a příjemci (příjemci) nemusejí současně odesílat a přijímat zprávy. Důvodem je, že ve frontě jsou uloženy zprávy trvale. Kromě toho nemusí producent čekat na odpověď od příjemce, aby mohl dál zpracovávat a odesílat zprávy.

Související výhodou je **Vyrovnávání zatížení**, které výrobcům a příjemcům umožňuje odesílat a přijímat zprávy s různou sazbou. V mnoha aplikacích se zatížení systému v průběhu času mění. Doba zpracování požadovaná pro každou pracovní jednotku je však obvykle konstantní. Producenti a spotřebitelé propojovací zprávy mají za to, že spotřebovávající aplikace musí být schopná jenom zpracovávat průměrné zatížení namísto zatížení ve špičce. S měnící se příchozí zátěží se mění hloubka fronty. Tato schopnost přímo šetří peníze v souvislosti s množstvím infrastruktury, která je nutná k provozu zatížení aplikace. Po zvýšení zátěže je možné přidat další pracovní procesy pro čtení z fronty. Každou zprávu zpracovává jen jeden pracovní proces. Toto vyrovnávání zatížení na základě vyžádání obsahu navíc umožňuje nejlepší použití pracovních počítačů i v případě, že pracovní počítače zpracovávají zprávy o přijetí změn v maximální míře. Tomuto chování se často říká **konkurence mezi spotřebiteli**.

Použití front ke mezilehlým výrobcům zpráv a spotřebitelům poskytuje podstatné propojení mezi komponentami. Vzhledem k tomu, že výrobci a příjemci si vzájemně nevědí, může být příjemce upgradován, aniž by to mělo žádný vliv na producenta.

### <a name="create-queues"></a>Vytvořit fronty
Fronty můžete vytvářet pomocí šablon [Azure Portal](service-bus-quickstart-portal.md), [PowerShellu](service-bus-quickstart-powershell.md), [CLI](service-bus-quickstart-cli.md)nebo [Správce prostředků](service-bus-resource-manager-namespace-queue.md). Pak můžete odesílat a přijímat zprávy pomocí klientů napsaných v [jazycích C#](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md), [Python](service-bus-python-how-to-use-queues.md), [JavaScript](service-bus-nodejs-how-to-use-queues.md), [php](service-bus-php-how-to-use-queues.md)a [Ruby](service-bus-ruby-how-to-use-queues.md). 

### <a name="receive-modes"></a>Režimy příjmu
Můžete určit dva různé režimy, v nichž Service Bus přijímá zprávy.

- **Přijmout a odstranit**. Když v tomto režimu Service Bus přijme požadavek od příjemce, označí zprávu jako spotřebou a vrátí ji do aplikace příjemce. Tento režim je nejjednodušším modelem. Funguje nejlépe ve scénářích, ve kterých může aplikace tolerovat nezpracovávání zprávy, pokud dojde k selhání. Pro pochopení tohoto scénáře Vezměte v úvahu scénář, ve kterém příjemce vystaví žádost o přijetí, a poté dojde k chybě před jejím zpracováním. Jakmile Service Bus označí zprávu jako spotřebou, aplikace zahájí zpracování zpráv po restartování. Neobjeví se mu zpráva, kterou spotřeboval před selháním.
- **Prohlížet zámek**. V tomto režimu se operace Receive stane dvěma fázemi, což umožňuje podporovat aplikace, které neumožňují tolerovat chybějící zprávy. 
    1. Najde další zprávu, která se má spotřebovat, **zamkne** ji, aby zabránila ostatním uživatelům v jejich přijetí, a potom vrátí zprávu do aplikace. 
    1. Poté, co aplikace dokončí zpracování zprávy, požádá službu Service Bus, aby dokončila druhou fázi procesu příjmu. Potom služba **označí zprávu jako spotřebovaná**. 

        Pokud aplikace z nějakého důvodu nemůže zprávu zpracovat, může požádat službu Service Bus o **opuštění** zprávy. Service Bus **odemkne** zprávu a zpřístupní ji pro opětovné přijetí, a to buď stejným příjemcem, nebo jiným konkurenčním zákazníkem. Za druhé je **časový limit** přidružený k zámku. Pokud aplikace nedokáže zpracovat zprávu před vypršením časového limitu zámku, Service Bus zprávu odemkne a zpřístupní ji, aby ji bylo možné znovu přijmout.

        Pokud aplikace po zpracování zprávy dojde k chybě, ale před tím, než požádá službu Service Bus o dokončení zprávy, Service Bus zprávu po restartování znovu doručí do aplikace. Tento proces se často nazývá aspoň **po** zpracování. To znamená, že každá zpráva se zpracuje alespoň jednou. V některých případech ale může být stejná zpráva doručena znovu. Pokud váš scénář nemůže tolerovat duplicitní zpracování, přidejte do aplikace další logiku, která bude zjišťovat duplicity. Další informace najdete v tématu [zjištění duplicit](duplicate-detection.md). Tato funkce se označuje stejně jako zpracování **právě jednou** .

        > [!NOTE]
        > Další informace o těchto dvou režimech najdete v tématu věnovaném [Vyrovnávání operací příjmu](message-transfers-locks-settlement.md#settling-receive-operations).

## <a name="topics-and-subscriptions"></a>Témata a předplatná
Fronta umožňuje zpracování zprávy jedním příjemcem. Na rozdíl od front, témat a předplatných nabízí ve vzoru **publikování a odběru** formu typu 1: n. Je užitečné pro škálování na velký počet příjemců. Každá publikovaná zpráva je zpřístupněna pro každé předplatné registrované v tématu. Vydavatel pošle zprávu do tématu a jednomu nebo více předplatitelům obdrží kopii zprávy v závislosti na pravidlech filtru nastavených u těchto předplatných. Odběry mohou použít další filtry k omezení zpráv, které chtějí přijímat. Vydavatelé odesílají zprávy do tématu stejným způsobem, jako odesílají zprávy do fronty. Ale uživatelé neobdrží zprávy přímo z tématu. Místo toho příjemci obdrží zprávy z předplatných tématu. Předplatné tématu se podobá virtuální frontě, která přijímá kopie zpráv odesílaných do tématu. Příjemci obdrží zprávy z předplatného stejně, jako jim obdrží zprávy z fronty.

Funkce odesílání zpráv, které jsou mapovány ve frontě, přímo do tématu a její funkce pro přijímání zpráv se mapují na předplatné. Tato funkce mimo jiné znamená, že odběry podporují stejné vzory popsané dříve v této části, s ohledem na fronty: konkurenční příjemce, dočasné oddělení, Vyrovnávání zatížení a vyrovnávání zatížení.

### <a name="create-topics-and-subscriptions"></a>Vytvoření témat a odběrů
Vytvoření tématu je podobné jako vytvoření fronty, jak je popsáno v předchozí části. Témata a odběry můžete vytvořit pomocí šablon [Azure Portal](service-bus-quickstart-topics-subscriptions-portal.md), [PowerShellu](service-bus-quickstart-powershell.md), [CLI](service-bus-tutorial-topics-subscriptions-cli.md)nebo [Správce prostředků](service-bus-resource-manager-namespace-topic.md). Pak můžete posílat zprávy do tématu a přijímat zprávy z předplatných pomocí klientů napsaných v [jazycích C#](service-bus-dotnet-how-to-use-topics-subscriptions.md), [Java](service-bus-java-how-to-use-topics-subscriptions.md), [Python](service-bus-python-how-to-use-topics-subscriptions.md), [JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions.md), [php](service-bus-php-how-to-use-topics-subscriptions.md)a [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md). 

### <a name="rules-and-actions"></a>Pravidla a akce
V mnoha scénářích musí být zprávy, které mají specifické vlastnosti, zpracovávány různými způsoby. Chcete-li povolit toto zpracování, můžete nakonfigurovat odběry a vyhledat zprávy s požadovanými vlastnostmi a následně provést určité úpravy těchto vlastností. I když Service Bus odběry uvidí všechny zprávy odeslané do tématu, můžete zkopírovat pouze podmnožinu těchto zpráv do fronty virtuálních předplatných. Toto filtrování je provedeno pomocí filtrů předplatného. Takové úpravy se nazývají **akce filtru**. Po vytvoření předplatného můžete dodat výraz filtru, který bude fungovat ve vlastnostech zprávy. Vlastnosti mohou být vlastnosti systému (například **Label**) a vlastní vlastnosti aplikace (například **StoreName**). Výraz filtru SQL je v tomto případě volitelný. Bez výrazu filtru SQL se provede veškerá akce filtru definovaná v rámci předplatného ve všech zprávách pro toto předplatné.

Úplný pracovní příklad najdete na webu GitHub [TopicSubscriptionWithRuleOperationsSample Sample](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) .

Další informace o filtrech najdete v tématu [filtry a akce tématu](topic-filters.md).

## <a name="java-message-service-jms-20-entities"></a>Entity služby zprávy Java (JMS) 2,0
K dispozici jsou tyto entity prostřednictvím rozhraní JMS (Java Message Service) 2,0 API.

  * Dočasné fronty
  * Dočasná témata
  * Sdílená trvalá předplatná
  * Nesdílené trvalé odběry
  * Sdílená netrvalá předplatná
  * Nesdílené odběry, které nejsou trvalé

Přečtěte si další informace o [entitách JMS 2,0](java-message-service-20-entities.md) a o tom, jak [je používat](how-to-use-java-message-service-20.md).

## <a name="next-steps"></a>Další kroky

Další informace a příklady použití zasílání zpráv Service Bus najdete v následujících tématech pokročilých:

* [Přehled Service Busho zasílání zpráv](service-bus-messaging-overview.md)
* [Rychlý start: Odesílání a přijímání zpráv pomocí webu Azure Portal a architektury .NET](service-bus-quickstart-portal.md)
* [Kurz: Aktualizace zásob pomocí portálu Azure Portal a témat/odběrů](service-bus-tutorial-topics-subscriptions-portal.md)


