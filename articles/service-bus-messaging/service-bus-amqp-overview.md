---
title: Přehled AMQP 1,0 v Azure Service Bus
description: Přečtěte si, jak Azure Service Bus podporuje rozšířený protokol řízení front zpráv (AMQP) (AMQP), což je otevřený standardní protokol.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: c91c7965b94216f3f3bcb47e0cb652ce22a0217a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88066334"
---
# <a name="amqp-10-support-in-service-bus"></a>Podpora AMQP 1,0 v Service Bus
Azure Service Bus cloudová služba i místní [Service Bus pro Windows Server (Service Bus 1,1)](/previous-versions/service-bus-archive/dn282144(v=azure.100)) podporují protokol AMQP (Advanced Message Queueing Protocol) 1,0. AMQP umožňuje sestavovat hybridní aplikace pro různé platformy s využitím otevřeného standardního protokolu. Můžete sestavovat aplikace pomocí komponent sestavených pomocí různých jazyků a platforem, které běží v různých operačních systémech. Všechny tyto komponenty se mohou připojit k Service Bus a hladce si vyměňovat strukturované obchodní zprávy efektivně a s plnou věrností.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Úvod: co je AMQP 1,0 a proč je důležité?
Aplikace middleware zaměřené na zprávy používají pro komunikaci mezi klientskými aplikacemi a zprostředkovateli speciální protokoly. To znamená, že po výběru zprostředkovatele zasílání zpráv určitého dodavatele je nutné použít knihovny tohoto dodavatele k připojení klientských aplikací k tomuto zprostředkovateli. Výsledkem je míra závislosti u tohoto dodavatele, protože portování aplikace pro jiný produkt vyžaduje změny kódu ve všech připojených aplikacích. 

Připojení zprostředkovatelů zasílání zpráv od různých dodavatelů je navíc obtížné. To obvykle vyžaduje přemostění na úrovni aplikace, aby bylo možné přesouvat zprávy z jednoho systému do druhého a překládat je mezi jejich proprietárními formáty zpráv. Toto je běžný požadavek. například když musíte zadat nové sjednocené rozhraní pro starší různorodé systémy nebo integrovat IT systémy po fúzi.

Softwarový oborový je rychlý přesun firmy. nové programovací jazyky a aplikační architektury se zavádějí na někdy bewildering tempo. Podobně se požadavky na systémy IT vyvíjí v průběhu času a vývojáři chtějí využít výhod nejnovějších funkcí platformy. Někdy ale vybraný dodavatel zasílání zpráv nepodporuje tyto platformy. Vzhledem k tomu, že jsou k dispozici protokoly zasílání zpráv, není možné ostatním uživatelům poskytnout knihovny pro tyto nové platformy. Proto je nutné použít přístupy, jako je například vytváření bran nebo mostů, které vám umožní nadále používat produkt pro zasílání zpráv.

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

Mezi běžně citované výhody otevřených standardů patří:

* Menší pravděpodobnost uzamčení dodavatele
* Interoperabilita
* Široká dostupnost knihoven a nástrojů
* Ochrana proti obsolescence
* Dostupnost znalostních zaměstnanců
* Nižší a spravovatelné riziko

## <a name="amqp-10-and-service-bus"></a>AMQP 1,0 a Service Bus
Podpora AMQP 1,0 v Azure Service Bus znamená, že teď můžete využít Service Bus služby Řízení front zpráv a publikování/přihlášení k odběru funkcí v různých platformách pomocí efektivního binárního protokolu. Kromě toho můžete sestavovat aplikace tvořené komponentami sestavenými pomocí kombinace jazyků, platforem a operačních systémů.

Následující obrázek znázorňuje ukázkové nasazení, ve kterém klienti Java běžící na systému Linux, napsané pomocí standardního rozhraní API služby JMS (Java Message Service) a klientů .NET běžících na Windows, zprávy Exchange prostřednictvím Service Bus pomocí AMQP 1,0.

![Diagram znázorňující jednu Service Bus výměnu zpráv se dvěma prostředími Linux a dvěma prostředími Windows.][0]

**Obrázek 1: Příklad scénáře nasazení znázorňujícího zasílání zpráv pro různé platformy pomocí Service Bus a AMQP 1,0**

V tuto chvíli jsou známy následující klientské knihovny pro práci s Service Bus:

| Jazyk | Knihovna |
| --- | --- |
| Java |Klient JMS (Java Message Service) Apache Qpid<br/>Klient Java software SwiftMQ v IIT softwaru |
| C |Apache Qpid Proton-C |
| PHP |Apache Qpid Proton – PHP |
| Python |Proton-Python Apache Qpid |
| C# |AMQP .NET Lite |

**Obrázek 2: tabulka klientských knihoven AMQP 1,0**

## <a name="summary"></a>Shrnutí
* AMQP 1,0 je otevřený a spolehlivý protokol pro zasílání zpráv, který můžete použít k vytváření hybridních aplikací pro různé platformy. AMQP 1,0 je OASIS Standard.
* Podpora AMQP 1,0 je teď k dispozici v Azure Service Bus a také Service Bus pro Windows Server (Service Bus 1,1). Ceny jsou stejné jako u existujících protokolů.

## <a name="next-steps"></a>Další kroky
Jste připraveni na další informace? Navštivte následující odkazy:

* [Použití Service Bus z rozhraní .NET s AMQP]
* [Použití Service Bus z Java s AMQP]
* [Instalace Apache Qpid Proton-C na virtuální počítač Azure Linux]
* [AMQP v Service Bus pro Windows Server]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Použití Service Bus z rozhraní .NET s AMQP]: service-bus-amqp-dotnet.md
[Použití Service Bus z Java s AMQP]: ./service-bus-java-how-to-use-jms-api-amqp.md
[Instalace Apache Qpid Proton-C na virtuální počítač Azure Linux]: 
[AMQP in Service Bus for Windows Server]: /previous-versions/service-bus-archive/dn574799(v=azure.100)