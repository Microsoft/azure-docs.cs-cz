---
title: Přehled přenosu zpráv ve službě Azure Service Bus | Dokumentace Microsoftu
description: Tento článek poskytuje podrobný přehled Azure Service Bus, plně spravovaného zprostředkovatele zpráv Enterprise Integration.
ms.topic: overview
ms.date: 11/20/2020
ms.openlocfilehash: 7453e8dd300ad754fb58489f059670af209314ab
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881597"
---
# <a name="what-is-azure-service-bus"></a>Co je Azure Service Bus?
Microsoft Azure Service Bus je plně spravovaný zprostředkovatel podnikových zpráv s frontami zpráv a tématy pro publikování a odběr. Service Bus slouží k tomu, aby bylo možné oddělit aplikace a služby od sebe od sebe, a nabízí následující výhody:

- Vyrovnávání zatížení v rámci konkurenčních pracovních procesů
- Bezpečné směrování a přenos dat a kontrola nad hranicemi služby a aplikace
- Koordinace transakční práce, která vyžaduje vysokou úroveň spolehlivosti 

## <a name="overview"></a>Přehled
Data se mezi různými aplikacemi a službami přenáší pomocí *zpráv*. Zpráva je kontejner upravený pomocí metadat a obsahuje data. Data můžou být libovolný druh informací, včetně strukturovaných dat zakódovaných pomocí běžných formátů, jako jsou například JSON, XML, Apache Avro, prostý text.

Mezi běžné scénáře zasílání zpráv patří:

* *Zasílání zpráv*. Přenos obchodních dat, jako jsou prodejní nebo nákupní objednávky, deníky nebo pohyby inventáře.
* *Oddělit aplikace*. Zlepšení spolehlivosti a škálovatelnosti aplikací a služeb. Producent a zákazník nemusí být online nebo k dispozici ve stejnou dobu. [Zatížení je na úrovni](/azure/architecture/patterns/queue-based-load-leveling) , takže špičky provozu overtax službu. 
* *Vyrovnávání zatížení*. Umožněte, aby více [konkurenčních příjemců](/azure/architecture/patterns/competing-consumers) četlo z fronty současně a každý z nich získal exkluzivní vlastnictví pro konkrétní zprávy. 
* *Témata a odběry*. Povolit vztahy 1:*n* mezi [vydavateli a předplatiteli](/azure/architecture/patterns/publisher-subscriber)a umožnit tak odběratelům vybrat konkrétní zprávy z publikovaného datového proudu zpráv.
* *Transakce*. Umožňuje provést několik operací, a to vše v oboru atomické transakce. Například následující operace lze provést v oboru transakce.  

    1. Získá zprávu z jedné fronty.
    2. Vystavení výsledků zpracování do jedné nebo více různých front.
    3. Přesune vstupní zprávu z původní fronty. 
    
    Výsledky budou viditelné pro příjemce pro příjem dat, a to i po úspěšném dokončení, včetně úspěšného vyrovnání vstupní zprávy a umožňující jenom sémantiku zpracování jenom jednou. Tento model transakce je robustní základ pro vzor [kompenzačních transakcí](/azure/architecture/patterns/compensating-transaction) v kontextu většího řešení. 
* *Relace zpráv*. Implementace vysoce škálovatelné koordinace pracovních postupů a multiplexových přenosů, které vyžadují striktní řazení zpráv nebo odložení zpráv.

Pokud jste obeznámeni s dalšími zprostředkovateli zpráv, jako je Apache ActiveMQ, Service Bus koncepce podobné těm, co znáte. Jelikož je Service Bus nabídka typu platforma jako služba (PaaS), jedná se o klíčový rozdíl, který se nemusíte starat o následující akce. Azure se za vás postará o tyto rutinní. 

- Umístění protokolů a Správa místa na disku
- Zpracování záloh
- Udržování operačních systémů nebo produktů opraveny
- Obava o selhání hardwaru 
- Převzetí služeb při selhání do rezervovaného počítače

## <a name="compliance-with-standards-and-protocols"></a>Dodržování standardů a protokolů

Primárním přenosovým protokolem pro Service Bus je [Rozšířený Protokol AMQP (Messaging Queueing Protocol) 1,0](service-bus-amqp-overview.md), což je otevřený standard ISO/IEC. Umožňuje zákazníkům psát aplikace, které pracují s Service Bus a místními zprostředkovateli, jako je například ActiveMQ nebo RabbitMQ. [Průvodce protokolem AMQP](service-bus-amqp-protocol-guide.md) poskytuje podrobné informace pro případ, že chcete sestavit abstrakci.

[Service Bus Premium](service-bus-premium-messaging.md) je plně kompatibilní s rozhraním API Java/Jakarta EE [JMS (Java Message Service) 2,0](how-to-use-java-message-service-20.md) . A Service Bus standard podporuje podmnožinu JMS 1,1 zaměřenou na fronty. JMS je společná abstrakce pro zprostředkovatele zpráv a integruje se s mnoha aplikacemi a architekturami, včetně oblíbeného rozhraní pro pružiny. Chcete-li přepnout z jiných zprostředkovatelů na Azure Service Bus, stačí znovu vytvořit topologii front a témat a změnit závislosti a konfiguraci poskytovatele klienta. Příklad najdete v tématu [Průvodce migrací ActiveMQ](migrate-jms-activemq-to-servicebus.md).

## <a name="concepts-and-terminology"></a>Koncepty a terminologie 
Tato část popisuje koncepty a terminologie Service Bus.

### <a name="namespaces"></a>Obory názvů
Obor názvů je kontejner pro všechny součásti zasílání zpráv. V jednom oboru názvů může být několik front a témat a obory názvů často slouží jako kontejnery aplikací. 

Obor názvů lze porovnat s "serverem" v terminologii jiných zprostředkovatelů, ale koncepty nejsou přímo ekvivalentní. Service Bus obor názvů je váš vlastní prostor pro kapacitu velkého clusteru tvořený spoustou aktivních virtuálních počítačů. Může volitelně zahrnovat tři [zóny dostupnosti Azure](../availability-zones/az-overview.md). Získáte tak všechny výhody dostupnosti a odolnosti při spouštění zprostředkovatele zpráv v mimořádně velkém měřítku. A nemusíte se starat o základní složitosti. Service Bus je zasílání zpráv bez serveru.

### <a name="queues"></a>Fronty
Zprávy se odesílají do *front* a přijímají se z nich. Fronty uchovávají zprávy, dokud nebude přijímající aplikace k dispozici pro příjem a zpracování.

![Fronta](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Zprávy ve frontách jsou seřazené a mají časové razítko při příjezdu. Po přijetí zprostředkovatelem se tato zpráva vždycky uchovává trvale v troje redundantním úložišti, v případě, že obor názvů je povolený v rámci zón. Service Bus nikdy nenechává zprávy v paměti nebo ve stálém úložišti, jakmile je klient nahlásil jako přijatý.

Zprávy jsou doručovány v režimu *Pull* , pouze doručování zpráv v případě vyžádání. Na rozdíl od modelu cyklického cyklického dotazování některých dalších cloudových front může být operace vyžádání dlouho zařízena a dokončena až po zpřístupnění zprávy. 

### <a name="topics"></a>Témata

K odesílání a přijímání zpráv můžete také použít *témata*. Zatímco fronta se často používá pro komunikaci point-to-point, témata jsou užitečná ve scénářích publikování nebo přihlášení k odběru.

![Téma](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Témata mohou mít několik nezávislých předplatných, která se připojují k tématu a jinak pracují přesně jako fronty na straně přijímače. Odběratel tématu může přijímat kopie všech zpráv zaslaných do daného tématu. Předplatná jsou pojmenovány entity. Ve výchozím nastavení jsou předplatná netrvalá, ale je možné je nakonfigurovat tak, aby vyprší a pak se automaticky odstranila. Přes rozhraní JMS API vám Service Bus Premium také umožňuje vytvářet nestálá předplatná, která existují po dobu trvání připojení.

Můžete definovat pravidla pro předplatné. Pravidlo předplatného obsahuje *Filtr* , který definuje podmínku pro zkopírování zprávy do předplatného a volitelnou *akci* , která může upravovat metadata zprávy. Další informace najdete v tématu [filtry a akce tématu](topic-filters.md). Tato funkce je užitečná v následujících scénářích:

- Nechcete, aby se v rámci předplatného přijímaly všechny zprávy odeslané do tématu.
- Chcete označit zprávy s dodatečnými metadaty při průchodu předplatným.

## <a name="advanced-features"></a>Pokročilé funkce

Service Bus zahrnují pokročilé funkce, které vám umožní řešit složitější problémy se zasíláním zpráv. V následujících částech jsou popsány některé z těchto funkcí.

### <a name="message-sessions"></a>Relace zpráv

Chcete-li v Service Bus vytvořit záruku typu first-in, First-out (FIFO), použijte relace. Relace zpráv umožňují exkluzivní, seřazené zpracování neohraničených sekvencí souvisejících zpráv. Aby bylo možné zpracovávat relace v vysoce škálovatelných systémech s vysokou dostupností, funkce relace také umožňuje ukládání stavu relace, což umožňuje, aby relace mezi obslužnými rutinami byly bezpečně přesunuty. Další informace najdete v tématu [relace zpráv: první v, první (FIFO)](message-sessions.md).

### <a name="autoforwarding"></a>Autopřesměrovává

Funkce autopřesměrovávání řetězí frontu nebo předplatné do jiné fronty nebo tématu v rámci stejného oboru názvů. Když použijete tuto funkci, Service Bus automaticky přesune zprávy z fronty nebo předplatného do cílové fronty nebo tématu. Všechna taková přesuna jsou prováděna po přejednání. Další informace najdete v tématu [zřetězení Service Bus entit pomocí procesu autopřesměrovávání](service-bus-auto-forwarding.md).

### <a name="dead-letter-queue"></a>Fronta nedoručených zpráv

Všechny Service Bus fronty a odběry témat mají přidruženou frontu nedoručených zpráv (DLQ). DLQ obsahuje zprávy, které splňují následující kritéria: 

- Nedají se úspěšně doručit všem příjemcům.
- Vypršel časový limit.
- Jsou explicitně sidelined přijímající aplikací. 

Zprávy ve frontě nedoručených zpráv jsou opatřeny odůvodněním, proč tam byly umístěny. Fronta nedoručených zpráv má speciální koncový bod, ale v opačném případě funguje jako jakákoli pravidelná fronta. Aplikace nebo nástroj může procházet DLQ nebo vyřadit z něj. Můžete také znovu vysílat z fronty nedoručených zpráv. Další informace najdete v tématu [přehled Service Bus front nedoručených zpráv](service-bus-dead-letter-queues.md).

### <a name="scheduled-delivery"></a>Naplánované doručení

Můžete odeslat zprávy do fronty nebo tématu pro zpožděné zpracování a nastavit čas, kdy bude zpráva k dispozici pro spotřebu. Naplánované zprávy je také možné zrušit. Další informace najdete v tématu [naplánované zprávy](message-sequencing.md#scheduled-messages).

### <a name="message-deferral"></a>Odložení zpráv

Klient fronty nebo předplatného může odložit načtení přijaté zprávy do pozdějšího času. Zpráva byla pravděpodobně odeslána z očekávaného pořadí a klient chce počkat, dokud neobdrží další zprávu. Odložené zprávy zůstávají ve frontě nebo předplatném a je nutné je znovu aktivovat explicitně pomocí pořadového čísla přiřazeného ke službě. Další informace najdete v tématu [odložení zprávy](message-deferral.md).

### <a name="batching"></a>Dávkování

Dávkování na straně klienta umožňuje klientovi nebo tématu nashromáždit sadu zpráv a přenést je dohromady. Často se to dělá buď na úsporu šířky pásma, nebo na zvýšení propustnosti. Další informace najdete v tématu [dávkování na straně klienta](service-bus-performance-improvements.md#client-side-batching).

### <a name="transactions"></a>Transakce

Transakce seskupí dvě nebo více operací dohromady do *oboru provádění*. Service Bus umožňuje seskupit operace do více entit zasílání zpráv v rámci oboru jedné transakce. Entita zprávy může být frontou, tématem nebo předplatným. Další informace najdete v tématu [Přehled zpracování transakcí Service Bus](service-bus-transactions.md).

### <a name="autodelete-on-idle"></a>Autodelete při nečinnosti

Automatické odstranění při nečinnosti umožňuje zadat interval nečinnosti, po kterém se automaticky odstraní odběr fronty nebo tématu. Minimální doba trvání je 5 minut. Další informace najdete v tématu [vlastnost QueueDescription. AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle).

### <a name="duplicate-detection"></a>Vyhledávání duplicit
Funkce zjišťování duplicit umožňuje odesílateli znovu znovu odeslat stejnou zprávu a, aby zprostředkovatel vynechal potenciální duplicitu. Zjišťování duplicitních dat je založeno na sledování `message-id` vlastnosti zprávy, což znamená, že aplikace musí při opakovaném odesílání zprávy použít stejnou hodnotu, což může být přímo odvozeno z určitého kontextu specifického pro aplikaci. Další informace najdete v tématu [zjištění duplicit](duplicate-detection.md).

### <a name="geo-disaster-recovery"></a>Geografické zotavení po havárii

Když v oblasti Azure dojde k výpadku, funkce zotavení po havárii umožňuje zpracování dat pokračovat v práci v jiné oblasti nebo datovém centru. Funkce udržuje strukturální zrcadlo oboru názvů dostupného v sekundární oblasti a umožňuje identitě oboru názvů přepnout na sekundární obor názvů. Již zaúčtované zprávy zůstávají v bývalém primárním oboru názvů pro obnovení po podobou stranách dostupnosti. Další informace najdete v tématu [Azure Service Busho geografického zotavení po havárii](service-bus-geo-dr.md).

### <a name="security"></a>Zabezpečení

Service Bus podporuje standardní protokoly [AMQP 1,0](service-bus-amqp-overview.md) a [http nebo REST](/rest/api/servicebus/) a jejich příslušná bezpečnostní zařízení, včetně protokolu TLS (Transport-Level Security). Klienti můžou mít oprávnění k přístupu pomocí [sdíleného přístupového podpisu](service-bus-sas.md) nebo [Azure Active Directory](service-bus-authentication-and-authorization.md) zabezpečení na základě rolí. 

Z důvodu ochrany před nežádoucími přenosy Service Bus poskytuje [funkce zabezpečení](network-security.md) , jako je brána firewall protokolu IP a integrace s virtuálními sítěmi. 

## <a name="client-libraries"></a>Klientské knihovny

Plně podporované Service Bus klientské knihovny jsou k dispozici prostřednictvím sady Azure SDK.

- [Azure Service Bus pro .NET](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Knihovny Azure Service Bus pro Java](/java/api/overview/azure/servicebus?preserve-view=true)
- [Poskytovatel Azure Service Bus pro Java JMS 2,0](how-to-use-java-message-service-20.md)
- [Azure Service Bus moduly pro JavaScript a TypeScript](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Knihovny Azure Service Bus pro Python](/python/api/overview/azure/servicebus?preserve-view=true)

[Azure Service Bus ' primární protokol je AMQP 1,0](service-bus-amqp-overview.md) a dá se použít z libovolného klienta protokolu kompatibilního s AMQP 1,0. Několik Open Source klientů AMQP má ukázky, které explicitně ukazují interoperabilitu Service Bus. Přečtěte si [Průvodce protokolem AMQP 1,0](service-bus-amqp-protocol-guide.md) , který vám pomůže pochopit, jak používat funkce Service Bus s klienty AMQP 1,0 přímo.

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="integration"></a>Integrace

Service Bus plně integruje s mnoha službami Microsoftu a Azure, například:

* [Event Grid](https://azure.microsoft.com/services/event-grid/)
* [Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Power Platform](https://powerplatform.microsoft.com/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>Další kroky

Pokud chcete začít používat zasílání zpráv služby Service Bus, podívejte se na následující články:

* Pokud chcete porovnat službu Azure Messaging Services, přečtěte si téma [porovnání služeb](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json).
* Vyzkoušejte rychlé starty pro [.NET](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md)nebo [JMS](service-bus-java-how-to-use-jms-api-amqp.md).
* Pokud chcete spravovat prostředky Service Bus, přečtěte si téma [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases).
* Další informace o úrovních Standard a Premium a jejich cenách najdete v tématu [Service Bus ceny](https://azure.microsoft.com/pricing/details/service-bus/).
* Další informace o výkonu a latenci pro úroveň Premium najdete v tématu [zasílání zpráv](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722)na úrovni Premium.