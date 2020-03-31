---
title: Přehled AMQP 1.0 v Azure Service Bus
description: Zjistěte, jak služba Azure Service Bus podporuje protokol AMQP (Advanced Message Queuing Protocol), otevřený standardní protokol.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 0e8d19cc-de36-478e-84ae-e089bbc2d515
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 50d21cfe8136b9c794eae5104bbb34e28f7c1661
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759308"
---
# <a name="amqp-10-support-in-service-bus"></a>Podpora AMQP 1.0 v service bus
Cloudová služba Azure Service Bus i místní [sběrnice Service Bus pro Windows Server (Service Bus 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) podporují protokol 1.0 1.0 (Advanced Message Queueing Protocol. AMQP umožňuje vytvářet hybridní aplikace napříč platformami pomocí otevřeného standardního protokolu. Můžete vytvářet aplikace pomocí komponent, které jsou vytvořeny pomocí různých jazyků a rozhraní a které běží v různých operačních systémech. Všechny tyto komponenty se mohou připojit k service bus a bezproblémově vyměňovat strukturované obchodní zprávy efektivně a v plné věrnosti.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Úvod: Co je AMQP 1.0 a proč je to důležité?
Middleware produkty orientované na zprávy tradičně používají proprietární protokoly pro komunikaci mezi klientskými aplikacemi a zprostředkovateli. To znamená, že jakmile vyberete zprostředkovatele zasílání zpráv konkrétního dodavatele, musíte použít knihovny tohoto dodavatele k připojení klientských aplikací k tomuto zprostředkovateli. To má za následek určitou míru závislosti na tomto dodavateli, protože přenesení aplikace na jiný produkt vyžaduje změny kódu ve všech připojených aplikacích. 

Kromě toho je připojení zprostředkovatelů zasílání zpráv od různých dodavatelů složité. To obvykle vyžaduje přemostění na úrovni aplikace přesunout zprávy z jednoho systému do druhého a překládat mezi jejich proprietární formáty zpráv. Jedná se o běžný požadavek; například pokud je nutné poskytnout nové jednotné rozhraní pro starší různorodé systémy nebo integrovat it systémy po fúzi.

Softwarový průmysl je rychle se rozvíjející podnik; nové programovací jazyky a aplikační rámce jsou zaváděny někdy matoucím tempem. Podobně se požadavky na IT systémy v průběhu času vyvíjejí a vývojáři chtějí využít nejnovější funkce platformy. Někdy však vybraný dodavatel zasílání zpráv nepodporuje tyto platformy. Vzhledem k tomu, že protokoly zasílání zpráv jsou proprietární, není možné, aby ostatní poskytovali knihovny pro tyto nové platformy. Proto je nutné použít přístupy, jako je například vytváření bran nebo mostů, které umožňují pokračovat v používání produktu zasílání zpráv.

Vývoj protokolu AMQP (Advanced Message Queuing Protocol) 1.0 byl motivován těmito problémy. Vznikla u JP Morgan Chase, která, stejně jako většina firem poskytujících finanční služby, jsou těžkými uživateli middlewaru orientovaného na zprávy. Cíl byl jednoduchý: vytvořit otevřený standardní protokol zasílání zpráv, který umožnil vytvářet aplikace založené na zprávách pomocí komponent vytvořených pomocí různých jazyků, rámců a operačních systémů, které používají nejlepší komponenty z řady Dodavatelů.

## <a name="amqp-10-technical-features"></a>Technické funkce AMQP 1.0
AMQP 1.0 je efektivní, spolehlivý, drát-úrovni zasílání zpráv protokol, který můžete použít k vytváření robustní, multiplatformní, zasílání zpráv aplikací. Protokol má jednoduchý cíl: definovat mechaniku bezpečného, spolehlivého a efektivního přenosu zpráv mezi dvěma stranami. Samotné zprávy jsou kódovány pomocí přenosné reprezentace dat, která umožňuje heterogenním odesílatelům a příjemcům vyměňovat si strukturované obchodní zprávy s plnou věrností. Následuje přehled nejdůležitějších funkcí:

* **Efektivní**: AMQP 1.0 je protokol orientovaný na připojení, který používá binární kódování pro protokolové instrukce a obchodní zprávy přenášené přes něj. Zahrnuje sofistikovaná schémata řízení toku pro maximalizaci využití sítě a připojených komponent. Protokol byl však navržen tak, aby nastolit rovnováhu mezi účinností, flexibilitou a interoperabilitou.
* **Spolehlivý**: Protokol AMQP 1.0 umožňuje výměnu zpráv s řadou záruk spolehlivosti, od požáru a zapomenutí až po spolehlivé, přesně jednou potvrzené doručení.
* **Flexibilní**: AMQP 1.0 je flexibilní protokol, který lze použít pro podporu různých topologie. Stejný protokol lze použít pro komunikaci mezi klientem a klientem a zprostředkovatelem.
* **Broker-model nezávislé**: Specifikace AMQP 1.0 neprovádí žádné požadavky na model zasílání zpráv používaný zprostředkovatelem. To znamená, že je možné snadno přidat podporu AMQP 1.0 pro stávající zprostředkovatele zasílání zpráv.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 je standard (s velkým "S")
AMQP 1.0 je mezinárodní norma schválená ISO a IEC jako ISO/IEC 19464:2014.

AMQP 1.0 je od roku 2008 ve vývoji v základní skupině více než 20 společností, dodavatelů technologií i firem koncových uživatelů. Během této doby, uživatelské firmy přispěly jejich real-svět obchodní požadavky a dodavatelé technologií vyvinuli protokol, aby tyto požadavky. V průběhu celého procesu se dodavatelé účastnili workshopů, na kterých spolupracovali na ověření interoperability mezi jejich implementacemi.

V říjnu 2011 byla v říjnu 2012 vydána rozvojová práce na technický výbor v rámci Organizace pro rozvoj strukturovaných informačních standardů (OASIS) a standardu OASIS AMQP 1.0. Na technickém výboru se během vývoje normy podílely tyto firmy:

* **Dodavatelé technologií:** Axway Software, Huawei Technologies, IIT Software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, Progress Software, Red Hat, SITA, Software AG, Solace Systems, VMware, WSO2, Zenika.
* **Uživatelské firmy**: Bank of America, Credit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Některé z běžně citovaných výhod otevřených standardů zahrnují:

* Menší šance na uzamčení dodavatele
* Interoperabilita
* Široká dostupnost knihoven a nástrojů
* Ochrana proti zastarávání
* Dostupnost znalého personálu
* Nižší a zvládnutelné riziko

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 a servisní sběrnice
Podpora AMQP 1.0 ve službě Azure Service Bus znamená, že teď můžete využít služby Service Bus ve frontě a publikovat nebo odebírat zprostředkované funkce zasílání zpráv z řady platforem pomocí efektivního binárního protokolu. Kromě toho můžete vytvářet aplikace složené z součástí vytvořených pomocí kombinace jazyků, architektur a operačních systémů.

Následující obrázek znázorňuje příklad nasazení, ve kterém java klienti běží na Linuxu, napsaný pomocí standardní java message service (JMS) API a .NET klienti spuštěné v systému Windows, výměnu zpráv prostřednictvím service bus pomocí AMQP 1.0.

![][0]

**Obrázek 1: Příklad scénáře nasazení zobrazující zasílání zpráv mezi platformami pomocí služby Service Bus a AMQP 1.0**

V současné době je známo, že následující klientské knihovny pracují se službou Service Bus:

| Jazyk | Knihovna |
| --- | --- |
| Java |Klient Apache Qpid Java Message Service (JMS)<br/>IIT Software SwiftMQ Java klient |
| C |Apache Qpid Proton-C |
| PHP |Apache Qpid Proton-PHP |
| Python |Apache Qpid Proton-Python |
| C# |AMQP .NET Lite |

**Obrázek 2: Tabulka klientských knihoven AMQP 1.0**

## <a name="summary"></a>Souhrn
* AMQP 1.0 je otevřený a spolehlivý protokol zasílání zpráv, který můžete použít k vytváření hybridních aplikací pro různé platformy. AMQP 1.0 je standard OASIS.
* Podpora AMQP 1.0 je teď dostupná ve službě Azure Service Bus a Service Bus pro Windows Server (Service Bus 1.1). Ceny jsou stejné jako u existujících protokolů.

## <a name="next-steps"></a>Další kroky
Jste připraveni na další informace? Navštivte následující odkazy:

* [Použití služby Service Bus z rozhraní .NET s amqp]
* [Použití služby Service Bus z Javy s AMQP]
* [Instalace Apache Qpid Proton-C na virtuální počítač Azure Linux]
* [AMQP v service bus pro Windows Server]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Použití služby Service Bus z rozhraní .NET s amqp]: service-bus-amqp-dotnet.md
[Použití služby Service Bus z Javy s AMQP]: service-bus-amqp-java.md
[Instalace Apache Qpid Proton-C na virtuální počítač Azure Linux]: service-bus-amqp-apache.md
[AMQP v service bus pro Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
