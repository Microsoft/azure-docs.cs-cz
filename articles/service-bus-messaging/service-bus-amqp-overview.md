---
title: Přehled AMQP 1,0 v Azure Service Bus
description: Přečtěte si, jak Azure Service Bus podporuje rozšířený protokol řízení front zpráv (AMQP) (AMQP), což je otevřený standardní protokol.
ms.topic: article
ms.date: 02/17/2021
ms.openlocfilehash: b2ca126312f5fc3da2a7ff6e20a9ade252f489f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100653879"
---
# <a name="amqp-10-support-in-service-bus"></a>Podpora AMQP 1,0 v Service Bus
Cloudová služba Azure Service Bus jako svůj primární způsob komunikace používá [Rozšířený Protokol AMQP (Message Queueing Protocol) 1,0](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html) . Společnost Microsoft spolupracuje s partnery v celém odvětví, od zákazníků a dodavatelů konkurenčních zprostředkovatelů zasílání zpráv, aby bylo možné vyvíjet a vyvíjet AMQP za poslední desetiletí, a to s novými rozšířeními vyvinutými v [technickém výboru Oasis AMQP](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp). AMQP 1,0 je standard ISO a IEC ([iso 19464:20149](https://www.iso.org/standard/64955.html)). 

AMQP vám umožňuje sestavovat hybridní aplikace pro různé platformy s využitím nezávislého standardního protokolu, který je nezávislý na dodavatelích a implementaci. Můžete sestavovat aplikace pomocí komponent sestavených pomocí různých jazyků a platforem, které běží v různých operačních systémech. Všechny tyto komponenty se mohou připojit k Service Bus a hladce si vyměňovat strukturované obchodní zprávy efektivně a s plnou věrností.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Úvod: co je AMQP 1,0 a proč je důležité?
Aplikace middleware zaměřené na zprávy používají pro komunikaci mezi klientskými aplikacemi a zprostředkovateli speciální protokoly. To znamená, že po výběru zprostředkovatele zasílání zpráv určitého dodavatele je nutné použít knihovny tohoto dodavatele k připojení klientských aplikací k tomuto zprostředkovateli. Výsledkem je míra závislosti u tohoto dodavatele, protože portování aplikace pro jiný produkt vyžaduje změny kódu ve všech připojených aplikacích. V komunitě Java se standardy rozhraní API specifické pro konkrétní jazyk, jako je JMS (Java Message Service) a abstrakce pro rozhraní pružiny, zmírnily trochu, ale mají velmi úzký rozsah funkcí a vyloučí vývojáře v jiných jazycích.

Připojení zprostředkovatelů zasílání zpráv od různých dodavatelů je navíc obtížné. To obvykle vyžaduje přemostění na úrovni aplikace, aby bylo možné přesouvat zprávy z jednoho systému do druhého a překládat je mezi jejich proprietárními formáty zpráv. Toto je běžný požadavek. například když musíte zadat nové sjednocené rozhraní pro starší různorodé systémy nebo integrovat IT systémy po fúzi. AMQP umožňuje propojení přímo připojujících se zprostředkovatelů, například pomocí směrovačů jako [Apache Qpid Dispatch router](https://qpid.apache.org/components/dispatch-router/index.html) nebo Broker-Native "shovels", jako je ten, který [RabbitMQ](service-bus-integrate-with-rabbitmq.md).

Softwarový oborový je rychlý přesun firmy. nové programovací jazyky a aplikační architektury se zavádějí na někdy bewildering tempo. Podobně se požadavky na systémy IT vyvíjí v průběhu času a vývojáři chtějí využít výhod nejnovějších funkcí platformy. Někdy ale vybraný dodavatel zasílání zpráv nepodporuje tyto platformy. Pokud jsou chráněné protokoly pro zasílání zpráv, není možné ostatním uživatelům poskytnout knihovny pro tyto nové platformy. Proto je nutné použít přístupy, jako je například vytváření bran nebo mostů, které vám umožní nadále používat produkt pro zasílání zpráv.

Vývoj rozšířený protokol řízení front zpráv (AMQP) (AMQP) 1,0 byl motivován těmito problémy. Pochází z JP Morgan podstatou, který jako většina firem finančních služeb je těžké uživatelé middlewaru orientovaný na zprávy. Tento cíl je jednoduchý: k vytvoření protokolu zasílání zpráv Open-Standard, který umožňuje vytvářet aplikace založené na zprávách pomocí komponent sestavených pomocí různých jazyků, platforem a operačních systémů, a to všechno s využitím špičkových komponent z celé řady dodavatelů.

## <a name="amqp-10-technical-features"></a>Technické funkce AMQP 1,0
AMQP 1,0 je efektivní a spolehlivý přenosový protokol zasílání zpráv, který můžete použít k vytváření robustních aplikací zasílání zpráv pro více platforem. Protokol má jednoduchý cíl: k definování mechanismu zabezpečeného, spolehlivého a efektivního přenosu zpráv mezi dvěma stranami. Samotné zprávy jsou kódované pomocí přenosné reprezentace dat, která umožňuje heterogenním odesílatelům a přijímačům vyměňovat strukturované obchodní zprávy s plnou věrností. Toto je souhrn nejdůležitějších funkcí:

* **Efektivní**: AMQP 1,0 je protokol orientovaný na připojení, který používá binární kódování pro pokyny protokolu a přenesené do něj odeslané obchodní zprávy. Zahrnuje sofistikované schémata řízení toku pro maximalizaci využití sítě a připojených součástí. Tento protokol byl navržen tak, aby provedl rovnováhu mezi efektivitou, flexibilitou a interoperabilitou.
* **Spolehlivé**: protokol AMQP 1,0 umožňuje vyměňovat zprávy s řadou záruk spolehlivosti, od požáru a zapomenutého po spolehlivém doručení, který je právě jednou potvrzen.
* **Flexibilní**: AMQP 1,0 je flexibilní protokol, který se dá použít k podpoře různých topologií. Stejný protokol se dá použít pro komunikaci mezi klientem a klientem a komunikace mezi klientem a zprostředkovatelem.
* **Zprostředkovatel – nezávislé na modelu**: specifikace AMQP 1,0 neprovádí žádné požadavky na model zasílání zpráv, který používá zprostředkovatel. To znamená, že je možné snadno přidat podporu AMQP 1,0 do stávajících zprostředkovatelů zasílání zpráv.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1,0 je Standard (se kapitálkou ').
AMQP 1,0 je mezinárodní standard schválený normou ISO a IEC jako ISO/IEC 19464:2014.

AMQP 1,0 se v vývoji od 2008 podle základní skupiny o více než 20 společnostech, jak dodavatelé technologií, tak i podniky koncových uživatelů. Během této doby vyvinuli firemní firmy své reálné obchodní požadavky a výrobci technologií vyvinuli protokol, aby splnil tyto požadavky. V průběhu tohoto procesu se dodavatelé účastnili v pracovních konferencích, ve kterých spolupracovali, aby ověřili interoperabilitu mezi jejich implementacemi.

V říjnu 2011 se vývojová práce převedla na technický výbor v rámci organizace pro účely vývoje OASIS (Structured Information Standards) a Standard OASIS AMQP 1,0 byl vydán v říjnu 2012. Následující firmy se účastnily technického výboru během vývoje standardu:

* **Dodavatelé technologií**: Axway software, technologie Huawei, IIT software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, software pro průběh, Red Hat, SITA, Software AG, Solace Systems, VMware, WSO2, Zenika.
* **Firmy pro uživatele**: Bank of America, úvěrové Suisse, německý Boerse, Goldman Sachs, JPMorgan podstatou.

Aktuální židle [technického výboru Oasis AMQP](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp) reprezentují Red Hat a Microsoft.

Mezi běžně citované výhody otevřených standardů patří:

* Menší pravděpodobnost uzamčení dodavatele
* Vzájemná funkční spolupráce
* Široká dostupnost knihoven a nástrojů
* Ochrana proti obsolescence
* Dostupnost znalostních zaměstnanců
* Nižší a spravovatelné riziko

## <a name="amqp-10-and-service-bus"></a>AMQP 1,0 a Service Bus
Podpora AMQP 1,0 v Azure Service Bus znamená, že pomocí efektivního binárního protokolu můžete využít Service Bus služby Řízení front zpráv a publikování/přihlášení k odběru služby z řady platforem. Kromě toho můžete sestavovat aplikace tvořené komponentami sestavenými pomocí kombinace jazyků, platforem a operačních systémů.

Následující obrázek znázorňuje ukázkové nasazení, ve kterém klienti Java běžící na systému Linux, napsané pomocí standardního rozhraní API služby JMS (Java Message Service) a klientů .NET běžících na Windows, zprávy Exchange prostřednictvím Service Bus pomocí AMQP 1,0.

![Diagram znázorňující jednu Service Bus výměnu zpráv se dvěma prostředími Linux a dvěma prostředími Windows.][0]

**Obrázek 1: Příklad scénáře nasazení znázorňujícího zasílání zpráv pro různé platformy pomocí Service Bus a AMQP 1,0**

Všechny podporované Service Bus klientské knihovny dostupné prostřednictvím sady Azure SDK používají AMQP 1,0.

- [Azure Service Bus pro .NET](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Knihovny Azure Service Bus pro Java](/java/api/overview/azure/servicebus?preserve-view=true)
- [Poskytovatel Azure Service Bus pro Java JMS 2,0](how-to-use-java-message-service-20.md)
- [Azure Service Bus moduly pro JavaScript a TypeScript](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Knihovny Azure Service Bus pro Python](/python/api/overview/azure/servicebus?preserve-view=true)

[!INCLUDE [service-bus-websockets-options](../../includes/service-bus-websockets-options.md)]

Kromě toho můžete Service Bus použít z libovolného zásobníku protokolu kompatibilního s AMQP 1,0:

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="summary"></a>Souhrn
* AMQP 1,0 je otevřený a spolehlivý protokol pro zasílání zpráv, který můžete použít k vytváření hybridních aplikací pro různé platformy. AMQP 1,0 je OASIS Standard.

## <a name="next-steps"></a>Další kroky
Jste připraveni na další informace? Navštivte následující odkazy:

* [Použití Service Bus z rozhraní .NET s AMQP]
* [Použití Service Bus z Java s AMQP]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Použití Service Bus z rozhraní .NET s AMQP]: service-bus-amqp-dotnet.md
[Použití Service Bus z Java s AMQP]: ./service-bus-java-how-to-use-jms-api-amqp.md

