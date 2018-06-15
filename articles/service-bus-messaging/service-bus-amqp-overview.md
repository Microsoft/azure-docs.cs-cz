---
title: Přehled protokolu AMQP 1.0 v Azure Service Bus | Microsoft Docs
description: Další informace o použití Advanced zpráv služby Řízení front protokol (AMQP) 1.0 v Azure.
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 0e8d19cc-de36-478e-84ae-e089bbc2d515
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: 6d2dffd22ecfc0aaf6e338567d5cf107a2c07383
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2018
ms.locfileid: "28926593"
---
# <a name="amqp-10-support-in-service-bus"></a>Podpora protokolu AMQP 1.0 v Service Bus
Cloudové služby Azure Service Bus i místní [sběrnice služby pro Windows Server (Service Bus 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) podporu Advanced zpráv služby Řízení front Protocol (AMQP) 1.0. AMQP umožňuje vytvořit a platformy, hybridní aplikace pomocí otevřený standardní protokol. Můžete vytvořit aplikací s použitím komponenty, které jsou integrovány v různých jazycích a architektury a které běží na různých operačních systémech. Všechny tyto součásti mohou připojit k Service Bus a bezproblémově výměna strukturovaných obchodní zpráv, efektivní a na úplné přesnost.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Úvod: Co je protokolu AMQP 1.0 a proč je důležité?
Produkty zaměřené na konkrétní zprávu middleware tradičně používaly speciální protokoly pro komunikaci mezi klientskými aplikacemi a zprostředkovatelé. To znamená, že po dokončení výběru zasílání zpráv zprostředkovatele od dodavatele, musíte použít od dodavatele knihovny pro připojení vaší klientské aplikace pro tohoto zprostředkovatele. Portování aplikaci jiným produktem vyžaduje změn kódu v propojených aplikacích výsledkem stupeň závislost na příslušného dodavatele. 

Kromě toho se připojit zprostředkovatelé zasílání zpráv od různých dodavatelů složité. To obvykle vyžaduje úrovni aplikace přemostění přesunout zpráv v jednom systému a pro převod mezi jejich formáty proprietární zpráv. Toto je běžné požadavek; například při musí zadat nové jednotné rozhraní pro starší různorodých systémů nebo integrovat následující fúzi systémy IT.

Softwarového oboru se o přesouvání fast firmu; Někdy nepřeberné tempem byly zavedeny nové programovací jazyky a architektur aplikací. Podobně požadavky na systémy IT v průběhu času vyvíjejí a vývojáři chtějí využívat výhody nejnovějších funkcí platformy. Ale někdy vybraného zasílání zpráv dodavatele nepodporuje tyto platformy. Protože zasílání zpráv protokoly jsou vlastní, není možné ostatním uživatelům poskytnout knihovny pro tyto nové platformy. Proto je nutné použít přístupy, jako je například vytváření mostů, které vám umožní pokračovat v používání zasílání zpráv produktu nebo brány.

Vývoj systému Advanced zpráv služby Řízení front protokol (AMQP) 1.0 byl motivované tyto problémy. Bylo provedeno v Chase Nováková JP, kdo jako firmami nejvíce finančních služeb, jsou náročné uživatele middleware orientovaný na zprávy. Cílem je jednoduchý: vytvoření protokol otevřené standardní zasílání zpráv, který umožňuje vytvářet aplikace na základě zpráv komponent vytvořených pomocí různých jazyků, architektury a operační systémy, všechny pomocí součásti nejlépe svého druhu z rozsahu Dodavatelé.

## <a name="amqp-10-technical-features"></a>Technické funkce protokolu AMQP 1.0
AMQP 1.0 je efektivní, spolehlivou a úroveň zasílání zpráv protokol, který můžete použít k vytvoření robustní, a platformy, zasílání zpráv na úrovni aplikace. Protokol má jednoduchého cíle: Chcete-li definovat mechanismů zabezpečeným, spolehlivým a efektivní přenos zpráv mezi dvěma účastníky. Samotné zprávy jsou zakódovány pomocí přenosné datové znázornění umožňující heterogenní odesílateli a příjemci k výměně zpráv strukturovaných obchodní na úplné přesnost. Následuje souhrn nejdůležitější funkce:

* **Efektivní**: AMQP 1.0 je orientované připojení protokolu této používá binární kódování pro protokol pokyny a obchodní zprávy přenosu. Její součástí jsou schémata sofistikované řízení toku chcete maximalizovat využití sítě a připojené součástí. Ale nutné dodat, protokol byl navržen k vytvořit rovnováhu mezi efektivitu, flexibilitu a vzájemná funkční spolupráce.
* **Spolehlivé**: umožňuje výměnu s rozsahem spolehlivost záruky, z ještě efektivněji a nezapomněli spolehlivé, přesně zpráv protokolu protokolu AMQP 1.0 – jednou potvrdí doručení.
* **Flexibilní**: protokolu AMQP 1.0 je flexibilní protokol, který lze použít pro podporu různých topologiích. Stejný protokol slouží pro komunikaci klienta klienta, klient zprostředkovatele a zprostředkovatele zprostředkovatele.
* **Zprostředkovatel modelu nezávisle**: protokolu AMQP 1.0 – specifikace zajistěte, aby nebyly žádné požadavky pro zasílání zpráv model používá zprostředkovatel. To znamená, že je možné snadno přidat podporu protokolu AMQP 1.0 existující zprostředkovatelé zasílání zpráv.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 je Standard (s na velké.)
AMQP 1.0 je standard mezinárodní schváleny ISO a IEC jako ISO/IEC 19464:2014.

AMQP 1.0 byl vyvíjen od 2008, Skupina základních víc než 20 společností technologie dodavatelé a podniky koncového uživatele. Během této doby uživatel podniky přispěla jejich skutečné obchodní požadavky a dodavatelé technologie vyvinuly protokol pro naplnění těchto požadavků. V celém procesu dodavatelé účastnili pracovní konference, ve kterých se spolupracovali ověření vzájemná funkční spolupráce mezi jejich implementace.

V říjnu 2011 vývoji přešla do technický výbor v rámci organizace postupu o strukturovaných informace standardy (OASIS) a standardní OASIS AMQP 1.0 byla vydána v říjen 2012. Následující podniky účastnili technický výbor během vývoje standardní:

* **Dodavatelé technologie**: Axway softwaru, Huawei technologie, IIT softwaru, INETCO systémy, Kaazing, Microsoft, Mitre Corporation, Primeton technologie, průběh softwaru, Red Hat, SITA, softwaru AG, Solace systémy, VMware, WSO2, Zenika.
* **Uživatel podniky**: Bank Amerika, platební Suisse, Boerse německých, Goldman Sachs, JPMorgan Chase.

Mezi výhody běžně citovaný otevřených standardů, patří:

* Menší riziko dodavatele zámku v
* Vzájemná funkční spolupráce
* Obecné dostupnosti knihovny a nástroje
* Ochrana proti životnosti
* Dostupnost zkušení pracovníci
* Menší a spravovat rizika

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 a Service Bus
Podpora protokolu AMQP 1.0 v Azure Service Bus znamená, že můžete nyní využít služby Řízení front Service Bus a publikování a přihlášení k odběru zprostředkované zasílání zpráv funkcí z rozsahu platforem a efektivní binární protokol. Kromě toho můžete vytvořit aplikace skládá z komponenty sestaven pomocí kombinace jazyky, architektury a operační systémy.

Následující obrázek ukazuje příklad nasazení, ve kterém Java klientů se systémem Linux, vytvořené pomocí standardní Java zprávy služby (JMS) rozhraní API a rozhraní .NET klientů se systémem Windows, výměna zpráv přes Service Bus pomocí protokolu AMQP 1.0.

![][0]

**Obrázek 1: Příklad nasazení zobrazující napříč platformami zasílání zpráv pomocí sběrnice a protokolu AMQP 1.0**

V tuto chvíli následujících klientských knihoven se ví, pracovat s sběrnice:

| Jazyk | Knihovna |
| --- | --- |
| Java |Klient Apache Qpid Java zprávy služby (JMS)<br/>SwiftMQ Java IIT softwaru klienta |
| C |Apache Qpid kanálem C |
| PHP |Apache Qpid kanálem PHP |
| Python |Apache Qpid kanálem Python |
| C# |AMQP .Net Lite |

**Obrázek 2: Tabulka knihoven klienta protokolu AMQP 1.0**

## <a name="summary"></a>Souhrn
* AMQP 1.0 je otevřený, spolehlivého zasílání zpráv protokol, který můžete použít k vytvoření a platformy, hybridní aplikace. AMQP 1.0 je OASIS standard.
* Podpora protokolu AMQP 1.0 je teď dostupná v Azure Service Bus, jakož i sběrnice služby pro Windows Server (Service Bus 1.1). Ceny je stejné jako pro existující protokoly.

## <a name="next-steps"></a>Další postup
Připraveni na další informace? Získáte pomocí následujících odkazů:

* [Pomocí protokolu AMQP služby Service Bus pomocí technologie .NET]
* [Pomocí protokolu AMQP sběrnice z Javy]
* [Instalace Apache Qpid kanálem C ve virtuálním počítači Azure Linux]
* [AMQP v Service Bus pro systém Windows Server]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Pomocí protokolu AMQP služby Service Bus pomocí technologie .NET]: service-bus-amqp-dotnet.md
[Pomocí protokolu AMQP sběrnice z Javy]: service-bus-amqp-java.md
[Instalace Apache Qpid kanálem C ve virtuálním počítači Azure Linux]: service-bus-amqp-apache.md
[AMQP v Service Bus pro systém Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
