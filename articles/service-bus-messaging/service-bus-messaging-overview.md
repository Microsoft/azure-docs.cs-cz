---
title: Přehled přenosu zpráv ve službě Azure Service Bus | Dokumentace Microsoftu
description: Tento článek poskytuje přehled Azure Service Bus, plně spravovaného zprostředkovatele zpráv pro podnikovou integraci.
ms.topic: overview
ms.date: 06/23/2020
ms.openlocfilehash: 478dd0debb5117e76cf8d0ab6599dcf363c12ab3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87501470"
---
# <a name="what-is-azure-service-bus"></a>Co je Azure Service Bus?

Microsoft Azure Service Bus je plně spravovaný zprostředkovatel zpráv podnikové integrace. Service Bus může oddělit aplikace a služby. Service Bus nabízí spolehlivou a bezpečnou platformu pro asynchronní přenos dat a stavu.

Data se mezi různými aplikacemi a službami přenáší pomocí *zpráv*. Zpráva je v binárním formátu a může obsahovat JSON, XML nebo jenom text. Další informace najdete v tématu [integrační služby](https://azure.com/integration).

Mezi běžné scénáře zasílání zpráv patří:

* *Zasílání zpráv*. Přenos obchodních dat, jako jsou prodejní nebo nákupní objednávky, deníky nebo pohyby inventáře.
* *Oddělit aplikace*. Zlepšení spolehlivosti a škálovatelnosti aplikací a služeb. Klient a služba nemusí být online ve stejnou dobu.
* *Témata a odběry*. Povolit vztah 1:*n* mezi vydavateli a předplatiteli.
* *Relace zpráv*. Implementujte pracovní postupy, které vyžadují řazení zpráv nebo odložení zpráv.

## <a name="namespaces"></a>Jmenné prostory

Obor názvů je kontejner pro všechny součásti zasílání zpráv. V jednom oboru názvů může být několik front a témat a obory názvů často slouží jako kontejnery aplikací.

## <a name="queues"></a>Fronty

Zprávy se odesílají do *front* a přijímají se z nich. Fronty uchovávají zprávy, dokud nebude přijímající aplikace k dispozici pro příjem a zpracování.

![Fronta](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Zprávy ve frontách jsou seřazené a mají časové razítko při příjezdu. Jakmile je zpráva přijata, uchovává se bezpečně v redundantním úložišti. Zprávy jsou doručovány v režimu *Pull* , pouze doručování zpráv v případě vyžádání.

## <a name="topics"></a>Témata

K odesílání a přijímání zpráv můžete také použít *témata*. Zatímco fronta se často používá pro komunikaci point-to-point, témata jsou užitečná ve scénářích publikování nebo přihlášení k odběru.

![Téma](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Témata mohou mít několik nezávislých odběrů. Odběratel tématu může přijímat kopie všech zpráv zaslaných do daného tématu. Předplatná jsou pojmenovány entity. Předplatná jsou trvalá, ale můžou vypršet platnost nebo se může autoodstranit.

Nesmíte chtít, aby jednotlivá předplatná přijímala všechny zprávy odeslané do tématu. Pokud ano, můžete pomocí *pravidel* a *filtrů* definovat podmínky, které aktivují volitelné *Akce*. Můžete filtrovat zadané zprávy a nastavit nebo upravit vlastnosti zprávy. Další informace najdete v tématu [filtry a akce tématu](topic-filters.md).

## <a name="advanced-features"></a>Pokročilé funkce

Service Bus zahrnují pokročilé funkce, které vám umožní řešit složitější problémy se zasíláním zpráv. V následujících částech jsou popsány některé z těchto funkcí.

### <a name="message-sessions"></a>Relace zpráv

Chcete-li v Service Bus vytvořit záruku typu first-in, First-out (FIFO), použijte relace. Relace zpráv umožňují společné a seřazené zpracování sekvencí souvisejících zpráv bez vazby. Další informace najdete v tématu [relace zpráv: první v, první (FIFO)](message-sessions.md).

### <a name="autoforwarding"></a>Autopřesměrovává

Funkce dopředně zřetězí frontu nebo předplatné do jiné fronty nebo tématu. Musí být součástí stejného oboru názvů. Díky automatickému přeposílání Service Bus automaticky odebere zprávy z fronty nebo předplatného a umístí je do jiné fronty nebo tématu. Další informace najdete v tématu [zřetězení Service Bus entit pomocí procesu autopřesměrovávání](service-bus-auto-forwarding.md).

### <a name="dead-letter-queue"></a>Fronta nedoručených zpráv

Service Bus podporuje frontu nedoručených zpráv (DLQ). DLQ obsahuje zprávy, které nemohou být doručeny žádnému příjemci. Obsahuje zprávy, které nelze zpracovat. Service Bus umožňuje odebrat zprávy z DLQ a zkontrolovat je. Další informace najdete v tématu [přehled Service Bus front nedoručených zpráv](service-bus-dead-letter-queues.md).

### <a name="scheduled-delivery"></a>Naplánované doručení

Pro zpožděné zpracování můžete odesílat zprávy do fronty nebo tématu. Můžete naplánovat úlohu, která bude k dispozici pro zpracování systémem v určitou dobu. Další informace najdete v tématu [naplánované zprávy](message-sequencing.md#scheduled-messages).

### <a name="message-deferral"></a>Odložení zpráv

Klient fronty nebo předplatného může odložit načtení zprávy do pozdějšího času. Tato odložení může být způsobena zvláštními okolnostmi v aplikaci. Zpráva zůstane ve frontě nebo předplatném, ale je vyhrazená. Další informace najdete v tématu [odložení zprávy](message-deferral.md).

### <a name="batching"></a>Dávkování

Dávkování na straně klienta umožňuje, aby klient fronty nebo tématu zpozdil odeslání zprávy po určené časové období. Pokud během tohoto časového období klient odešle další zprávy, přenese zprávy v jedné dávce. Další informace najdete v tématu [dávkování na straně klienta](service-bus-performance-improvements.md#client-side-batching).

### <a name="transactions"></a>Transakce

Transakce seskupí dvě nebo více operací dohromady do *oboru provádění*. Service Bus podporuje operace seskupení na jedné entitě zasílání zpráv v rámci jedné transakce. Entita zprávy může být frontou, tématem nebo předplatným. Další informace najdete v tématu [Přehled zpracování transakcí Service Bus](service-bus-transactions.md).

### <a name="filtering-and-actions"></a>Filtrování a akce

Odběratelé mohou definovat zprávy, které chtějí z tématu přijímat. Tyto zprávy se určují ve formě jednoho nebo více pojmenovaných pravidel odběru. U každé vyhovující podmínky pravidla vytvoří předplatné kopii zprávy, která může být pro každé pravidlo pro porovnání poznámkou jinak. Další informace najdete v tématu [filtry a akce tématu](topic-filters.md).

### <a name="autodelete-on-idle"></a>Autodelete při nečinnosti

Automatické odstranění při nečinnosti umožňuje zadat interval nečinnosti, po kterém se fronta automaticky odstraní. Minimální doba trvání je 5 minut. Další informace najdete v tématu [vlastnost QueueDescription. AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle).

### <a name="duplicate-detection"></a>Vyhledávání duplicit

Chyba by mohla způsobit pochybnosti o výsledku operace odeslání. Zjišťování duplicitních dat umožňuje odesílateli znovu odeslat stejnou zprávu. Další možností je, že se ve frontě nebo tématu zahodí všechny duplicitní kopie. Další informace najdete v tématu [zjištění duplicit](duplicate-detection.md).

### <a name="security-protocols"></a>Protokoly zabezpečení
<a name="sas-rbac-and-managed-identities-for-azure-resources"></a>

Service Bus podporuje protokoly zabezpečení, jako je například SAS ( [Shared Access signatury](service-bus-sas.md) ), [řízení přístupu na základě role Azure (Azure RBAC)](authenticate-application.md) a [spravované identity pro prostředky Azure](service-bus-managed-service-identity.md).

### <a name="geo-disaster-recovery"></a>Geografické zotavení po havárii

Pokud v oblastech nebo datových centrech Azure dojde k výpadku, geografické zotavení po havárii umožní, aby zpracování dat pokračovalo v jiné oblasti nebo datovém centru. Další informace najdete v tématu [Azure Service Busho geografického zotavení po havárii](service-bus-geo-dr.md).

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

* Pokud chcete porovnat službu Azure Messaging Services, přečtěte si téma [porovnání služeb](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json).
* Vyzkoušejte rychlé starty pro [.NET](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md)nebo [JMS](service-bus-java-how-to-use-jms-api-amqp.md).
* Pokud chcete spravovat prostředky Service Bus, přečtěte si téma [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases).
* Další informace o úrovních Standard a Premium a jejich cenách najdete v tématu [Service Bus ceny](https://azure.microsoft.com/pricing/details/service-bus/).
* Další informace o výkonu a latenci pro úroveň Premium najdete v tématu [zasílání zpráv](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722)na úrovni Premium.
