---
title: Přehled přenosu zpráv ve službě Azure Service Bus | Dokumentace Microsoftu
description: Tento článek poskytuje podrobný přehled Azure Service Bus, plně spravovaného zprostředkovatele zpráv podnikové integrace.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: overview
ms.date: 11/04/2019
ms.custom: mvc
ms.author: aschhab
ms.openlocfilehash: 49a54491c36ef29209d1a53094cc5baf57057557
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240629"
---
# <a name="what-is-azure-service-bus"></a>Co je Azure Service Bus?

Microsoft Azure Service Bus je plně spravovaný zprostředkovatel zpráv podnikové integrace. Service Bus může oddělit aplikace a služby. Service Bus nabízí spolehlivou a bezpečnou platformu pro asynchronní přenos dat a stavu.

Data se mezi různými aplikacemi a službami přenáší pomocí *zpráv*. Zpráva je v binárním formátu a může obsahovat JSON, XML nebo jen text. Další informace naleznete [v tématu Integration Services](https://azure.com/integration).

Mezi běžné scénáře zasílání zpráv patří:

* *Zasílání zpráv*. Přeneste obchodní data, například prodejní nebo nákupní objednávky, deníky nebo pohyby zásob.
* *Oddělit aplikace*. Zlepšete spolehlivost a škálovatelnost aplikací a služeb. Klient a služba nemusí být současně online.
* *Témata a odběry*. Povolit 1:*n* vztahy mezi vydavateli a odběrateli.
* *Relace zpráv*. Implementujte pracovní postupy, které vyžadují řazení zpráv nebo odložení zprávy.

## <a name="namespaces"></a>Jmenné prostory

Obor názvů je kontejner pro všechny součásti zasílání zpráv. Více front a témat může být v jednom oboru názvů a obory názvů často slouží jako kontejnery aplikací.

## <a name="queues"></a>Fronty

Zprávy se odesílají do *front* a přijímají se z nich. Fronty ukládají zprávy, dokud není přijímající aplikace k dispozici pro jejich příjem a zpracování.

![Fronta](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Zprávy ve frontách jsou seřazeny a při příjezdu označeny časovým razítkem. Jakmile je zpráva přijata, uchovává se bezpečně v redundantním úložišti. Zprávy jsou doručovány v režimu *vyžádané,* pouze doručování zpráv na požádání.

## <a name="topics"></a>Témata

K odesílání a přijímání zpráv můžete také použít *témata*. Zatímco fronta se často používá pro komunikaci point-to-point, témata jsou užitečná ve scénářích publikování nebo přihlášení k odběru.

![Téma](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Témata mohou mít několik nezávislých odběrů. Odběratel tématu může přijímat kopie všech zpráv zaslaných do daného tématu. Odběry jsou pojmenované entity. Odběry přetrvávají, ale může vyprší nebo autodelete.

Je možné, že nebudete chtít, aby jednotlivá předplatná přijímali všechny zprávy odeslané na téma. Pokud ano, můžete pomocí *pravidel* a *filtrů* definovat podmínky, které aktivují volitelné *akce*. Můžete filtrovat zadané zprávy a nastavit nebo upravit vlastnosti zprávy. Další informace naleznete [v tématu Filtry a akce tématu](topic-filters.md).

## <a name="advanced-features"></a>Pokročilé funkce

Service Bus obsahuje pokročilé funkce, které umožňují řešit složitější problémy se zasíláním zpráv. Následující části popisují několik těchto funkcí.

### <a name="message-sessions"></a>Relace zpráv

Chcete-li vytvořit záruku fifo (first-in, first-out) v service bus, použijte relace. Relace zpráv umožňují společné a seřazené zpracování sekvencí souvisejících zpráv bez vazby. Další informace naleznete v [tématu Relace zpráv: první dovnitř, první ven (FIFO)](message-sessions.md).

### <a name="autoforwarding"></a>Automatické předávání

Funkce automatického předávání řetězí frontu nebo odběr na jinou frontu nebo téma. Musí být součástí stejného oboru názvů. Při automatickém předávání služby Service Bus automaticky odebere zprávy z fronty nebo předplatného a umístí je do jiné fronty nebo tématu. Další informace naleznete v [tématu Chaining Service Bus entity s automatické předávání](service-bus-auto-forwarding.md).

### <a name="dead-letter-queue"></a>Fronta nedoručených zpráv

Service Bus podporuje frontu nedoručených zpráv (DLQ). DLQ obsahuje zprávy, které nelze doručit žádnému příjemci. Obsahuje zprávy, které nelze zpracovat. Service Bus umožňuje odebrat zprávy z DLQ a zkontrolovat je. Další informace naleznete v [tématu Přehled front nedoručených zpráv service bus](service-bus-dead-letter-queues.md).

### <a name="scheduled-delivery"></a>Naplánované doručení

Zprávy můžete odeslat do fronty nebo tématu pro zpožděné zpracování. Můžete naplánovat, aby byla úloha k dispozici pro zpracování systémem v určitém čase. Další informace naleznete v [tématu Naplánované zprávy](message-sequencing.md#scheduled-messages).

### <a name="message-deferral"></a>Odložení zpráv

Klient fronty nebo předplatného může odložit načtení zprávy na pozdější dobu. Tento odklad může být z důvodu zvláštních okolností v žádosti. Zpráva zůstane ve frontě nebo předplatném, ale je odložena. Další informace naleznete v tématu [Message deferral](message-deferral.md).

### <a name="batching"></a>Dávkování

Dávkování na straně klienta umožňuje, aby klient fronty nebo tématu zpozdil odeslání zprávy po určené časové období. Pokud během tohoto časového období klient odešle další zprávy, přenese zprávy v jedné dávce. Další informace naleznete v [tématu dávkování na straně klienta](service-bus-performance-improvements.md#client-side-batching).

### <a name="transactions"></a>Transakce

Transakce seskupí dvě nebo více operací do *oboru spuštění*. Service Bus podporuje seskupování operace proti jedné entitě zasílání zpráv v rámci jedné transakce. Entita zprávy může být fronty, tématu nebo odběr. Další informace naleznete v [tématu Přehled zpracování transakcí služby Service Bus](service-bus-transactions.md).

### <a name="filtering-and-actions"></a>Filtrování a akce

Odběratelé mohou definovat zprávy, které chtějí z tématu přijímat. Tyto zprávy se určují ve formě jednoho nebo více pojmenovaných pravidel odběru. Pro každou podmínku odpovídající pravidlo, odběr vytvoří kopii zprávy, která může být odlišně anotovány pro každé pravidlo párování. Další informace naleznete [v tématu Filtry a akce tématu](topic-filters.md).

### <a name="autodelete-on-idle"></a>Automatické smazání při nečinnosti

Automatické smazání při nečinnosti umožňuje určit interval nečinnosti, po kterém je fronta automaticky odstraněna. Minimální doba trvání je 5 minut. Další informace naleznete v [vlastnosti QueueDescription.AutoDeleteOnIdle .](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle)

### <a name="duplicate-detection"></a>Vyhledávání duplicit

Chyba může způsobit, že klient bude mít pochybnosti o výsledku operace odeslání. Vyhledávání duplicit umožňuje odesílateli znovu odeslat stejnou zprávu. Další možností je, aby fronta nebo téma zahodily všechny duplicitní kopie. Další informace naleznete v [tématu Vyhledávání duplicit](duplicate-detection.md).

### <a name="security-protocols"></a>Bezpečnostní protokoly
<a name="sas-rbac-and-managed-identities-for-azure-resources"></a>

Service Bus podporuje protokoly zabezpečení, jako jsou [sdílené přístupové podpisy](service-bus-sas.md) (SAS), [řízení přístupu na základě role](authenticate-application.md) (RBAC) a [spravované identity pro prostředky Azure](service-bus-managed-service-identity.md).

### <a name="geo-disaster-recovery"></a>Geografické zotavení po havárii

Pokud v oblastech nebo datových centrech Azure dojde k výpadku, geografické zotavení po havárii umožní, aby zpracování dat pokračovalo v jiné oblasti nebo datovém centru. Další informace najdete [v tématu Azure Service Bus Geo-disaster recovery](service-bus-geo-dr.md).

### <a name="security"></a>Zabezpečení

Service Bus podporuje standardní protokoly [AMQP 1.0](service-bus-amqp-overview.md) a [HTTP/REST](/rest/api/servicebus/).

## <a name="client-libraries"></a>Klientské knihovny

Service Bus podporuje klientské knihovny pro [.NET](https://github.com/Azure/azure-service-bus-dotnet/tree/master), [Java](https://github.com/Azure/azure-service-bus-java/tree/master)a [JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client).

## <a name="integration"></a>Integrace

Service Bus umožňuje úplnou integraci s následujícími službami Azure:

* [Event Grid](https://azure.microsoft.com/services/event-grid/)
* [Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>Další kroky

Pokud chcete začít používat zasílání zpráv služby Service Bus, podívejte se na následující články:

* Porovnání služeb zasílání zpráv Azure najdete [v tématu Porovnání služeb](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json).
* Vyzkoušejte rychlé starty pro [rozhraní .NET](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md)nebo [JMS](service-bus-java-how-to-use-jms-api-amqp.md).
* Informace o správě prostředků služby Service Bus naleznete v [tématu Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases).
* Další informace o úrovních Standard a Premium a jejich cenách najdete v [tématu Ceny service bus](https://azure.microsoft.com/pricing/details/service-bus/).
* Další informace o výkonu a latenci pro úroveň Premium najdete v tématu [Zasílání zpráv premium](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722).
