---
title: Přehled AMQP 1.0 ve službě Azure Service Bus | Dokumentace Microsoftu
description: Další informace o použití pokročilé řízení front zpráv protokolu (AMQP) 1.0 v Azure.
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 0e8d19cc-de36-478e-84ae-e089bbc2d515
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/30/2018
ms.author: spelluru
ms.openlocfilehash: 8fd87897a70b0a7bb629e185473374837cf1026e
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43701930"
---
# <a name="amqp-10-support-in-service-bus"></a>Podpora AMQP 1.0 ve službě Service Bus
Místních i cloudových služeb Azure Service Bus [sběrnice služby pro Windows Server (Service Bus 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) podporu upřesnit jejich zařazování do fronty protokolu zpráv (AMQP) 1.0. AMQP umožňuje vytvářet různé platformy, hybridní aplikace pomocí otevřeného protokolu. Můžete sestavit aplikace pomocí komponenty, které jsou sestaveny na základě různých jazyků a architektur a, na kterých běží v různých operačních systémech. Všechny tyto součásti mohou připojit ke službě Service Bus a bez problémů výměnu strukturovaných obchodní zpráv efektivně a s plnou věrností.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Představení: Co je protokolu AMQP 1.0 a proč je důležité?
Tradičně zpráva middleware zaměřený na produkty používaly speciální protokoly pro komunikaci mezi klientskými aplikacemi a zprostředkovatelů. To znamená, že po dokončení výběru zprostředkovatel zasílání zpráv od dodavatele, musíte použít od dodavatele knihovny pro připojení klientských aplikací pro tohoto zprostředkovatele. V důsledku stupeň závislosti na dodavatele, protože přenesení aplikace do jiný produkt vyžaduje změny kódu v připojených aplikacích. 

Kromě toho propojení zprostředkovatelé zasílání zpráv od různých dodavatelů je velmi obtížné. Obvykle vyžaduje úroveň aplikace přemostění přesunout zpráv v jednom systému a pro převod mezi formáty jejich vlastních zpráv. To je běžné požadavky; například při musí zadat nové sjednocené rozhraní do starší různorodých systémů, nebo integrovat systémy IT po spojení.

Rychlé tempo obchodní; je softwarovém průmyslu Někdy nepřeberné tempem jsou zavedeny nové programovacích jazyků a architektur aplikací. Podobně požadavky systémy IT v průběhu času vyvíjejí a vývojáři chtějí využít výhod funkce nejnovější platformy. Ale někdy vybraného zasílání zpráv dodavatele nepodporuje tyto platformy. Protože zasílání zpráv protokoly jsou speciální, není možné pro ostatní poskytují knihovny pro tyto nové platformy. Proto musíte použít přístupy, například vytváření bran nebo přemostění, které vám umožní pokračovat v používání produktu zasílání zpráv.

Vývojové nástroje pokročilé služby Řízení front protokolu zpráv (AMQP) 1.0 byl motivováno tyto problémy. Vytvoří se v podstatou JP Morgan, kdo jako většina finančních služeb firmami, jsou náročné uživatele zpráva middleware zaměřený na. Cílem bylo jednoduché: Chcete-li vytvořit protokol zasílání zpráv open standard, které přinesla možnost vytvářet aplikace založené na zprávách pomocí komponenty sestavené pomocí různých jazyků, architektur a operační systémy, všechny pomocí komponenty nejlepší druhu z rozsahu Dodavatelé.

## <a name="amqp-10-technical-features"></a>Technické funkce protokolu AMQP 1.0
AMQP 1.0 je efektivní, spolehlivý přenosový protokol zasílání zpráv, který můžete použít k vytvoření robustních a platformy, aplikace pro zasílání zpráv. Protokol má jednoduché cíle: definování mechanics zabezpečené, spolehlivé a efektivní přenos zpráv mezi dvěma stranami. Samotné zprávy jsou zakódovány pomocí přenosné datové reprezentaci, jež umožňuje heterogenní odesílateli a příjemci umožňuje výměnu zpráv strukturované obchodní s plnou věrností. Následuje souhrn nejdůležitější funkce:

* **Efektivní**: protokolu AMQP 1.0 je připojení objektově orientovaný protokol, který používá binární kódování pokyny protokolu a obchodní zpráv přenosu. To zahrnuje schémata sofistikované řízení toku pro maximalizaci využití sítě a připojené součásti. Ale nutné dodat, protokol je navržená hledají rovnováhu mezi efektivity, flexibility a vzájemná funkční spolupráce.
* **Spolehlivé**: protokolu AMQP 1.0 protokol umožňuje zprávy mají vyměnit s celou řadou záruky spolehlivost, fire a zapomenout na spolehlivé, přesně-jednou potvrzení doručování.
* **Flexibilní**: protokolu AMQP 1.0 je flexibilní protokol, který lze použít pro podporu různých topologií. Stejný protokol lze použít pro komunikaci klienta pro klienta, klient zprostředkovatele a zprostředkovatele na zprostředkovatele.
* **Nezávislé na zprostředkovatele modelu**: The AMQP 1.0 – specifikace neprovede žádné požadavky na zasílání zpráv modelu používá zprostředkovatel. To znamená, že je možné snadno přidat do existující zprostředkovatelé zasílání zpráv podpory AMQP 1.0.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 je Standard (s na velké ")
AMQP 1.0 je standard mezinárodních schváleny ISO a IEC jako ISO/IEC 19464:2014.

AMQP 1.0 je vyvíjen od verze 2008 skupinou core pro více než 20 společností, dodavatelů technologie a podniky koncového uživatele. Během této doby zaměstnavatelů uživatele přispět jejich reálných obchodních požadavků a dodavatelé technologií se vyvinula protokol, který se splňují tyto požadavky. Během tohoto procesu se účastnilo dodavatelů cvičení, ve kterých jsou spolupracovali ověření vzájemná funkční spolupráce mezi jejich implementace.

V říjnu 2011 byla vydána vývojové práce postoupí technického výboru organizace pro rozvoj z strukturovaných informace standardy OASIS (Organization) a OASIS AMQP 1.0 Standard v října 2012. Následující podniky účastnili technického výboru během vývoje standard:

* **Dodavatelé technologií**: Axway softwaru Huawei Technologies, IIT softwaru, INETCO systémy, Kaazing, Mitre společnost Microsoft, Primeton technologie, průběh softwaru, Red Hat, SITA, Software AG, Solace systémů, VMware, WSO2, Zenika.
* **Zaměstnavatelů uživatele**: Ameriku Bank, kredit Švýcarská, Deutsche Boerse, Goldman Sachs, JPMorgan podstatou.

Mezi výhody často zmiňovanou otevřených standardů, patří:

* Menší riziko fixaci dodavatele
* Vzájemná funkční spolupráce
* Široký dostupnosti knihovny a nástroje
* Ochrana proti zastarávání
* Dostupnost pracovníků dobře informovaný
* Nižší a spravovat rizika

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 a Service Bus
Podpora AMQP 1.0 ve službě Azure Service Bus znamená, že teď můžete využít služby Řízení front služby Service Bus a publikování/přihlášení k odběru funkce zprostředkovaného zasílání zpráv z celou řadu platforem s využitím efektivní binární protokol. Kromě toho můžete vytvářet aplikace skládá z komponenty sestavené pomocí kombinace jazyků, architektur a operační systémy.

Následující obrázek znázorňuje ukázkové nasazení Java klienty běžící na Linuxu, napsané s využitím standardní Java Message Service JMS () rozhraní API a .NET klientů se systémem Windows, ve kterém vyměňují zprávy přes Service Bus pomocí protokolu AMQP 1.0.

![][0]

**Obrázek 1: Příklad nasazení scénáře ukázkami, napříč platformami pro zasílání zpráv služby Service Bus a protokolu AMQP 1.0**

V tuto chvíli jsou známé následujících klientských knihoven pro práci se Service Bus:

| Jazyk | Knihovna |
| --- | --- |
| Java |Klient Apache Qpid Java Message Service (JMS)<br/>Klientskou sadou SwiftMQ Java IIT softwaru |
| C |Apache Qpid kanálem C |
| PHP |Apache Qpid kanálem – PHP |
| Python |Apache Qpid kanálem – Python |
| C# |AMQP .net Lite |

**Obrázek 2: Tabulka knihoven klienta protokolu AMQP 1.0**

## <a name="summary"></a>Souhrn
* AMQP 1.0 je otevřít a spolehlivé zasílání zpráv protokol, který vám umožní vytvářet víc platforem, hybridní aplikace. AMQP 1.0 je OASIS standard.
* Podpora AMQP 1.0 je teď dostupná v Azure Service Bus, jakož i sběrnice služby pro Windows Server (Service Bus 1.1). Ceny jsou stejné jako u existující protokoly.

## <a name="next-steps"></a>Další postup
Jste připraveni na další informace? Naleznete pod těmito odkazy:

* [Pomocí protokolu AMQP Service Bus z .NET]
* [Pomocí protokolu AMQP Service Bus z Javy]
* [Instalace Apache Qpid kanálem C na virtuálním počítači Azure s Linuxem]
* [AMQP ve službě Service Bus pro systém Windows Server]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Pomocí protokolu AMQP Service Bus z .NET]: service-bus-amqp-dotnet.md
[Pomocí protokolu AMQP Service Bus z Javy]: service-bus-amqp-java.md
[Instalace Apache Qpid kanálem C na virtuálním počítači Azure s Linuxem]: service-bus-amqp-apache.md
[AMQP ve službě Service Bus pro systém Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
