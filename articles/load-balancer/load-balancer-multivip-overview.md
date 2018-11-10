---
title: Několik front-Endů pro Azure Load Balancer | Dokumentace Microsoftu
description: Přehled několik front-Endů v Azure Load Balancer
services: load-balancer
documentationcenter: na
author: chkuhtz
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: chkuhtz
ms.openlocfilehash: d435d2c491cf17356e96f7bbb05b1e22c8e04aca
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219354"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Několik front-Endů pro Azure Load Balancer

Nástroj Azure Load Balancer umožňuje více portů a několik IP adres služby pro vyrovnávání zatížení. Vám pomůže definice nástroje pro vyrovnávání zatížení veřejné a vnitřní tocích Vyrovnávání zatížení mezi sadu virtuálních počítačů.

Tento článek popisuje základní informace o tuto možnost, důležité koncepty a omezení. Pokud chcete vystavit služby na jednu IP adresu, najdete pokyny, zjednodušené [veřejné](load-balancer-get-started-internet-portal.md) nebo [interní](load-balancer-get-started-ilb-arm-portal.md) konfigurace nástroje pro vyrovnávání zatížení. Přidává několik front-endů se přičítají k jedné front-endová konfigurace. Pomocí konceptů v tomto článku, můžete rozbalit zjednodušenou konfiguraci v každém okamžiku.

Při definování služby Azure Load Balancer front-endových a back-endová konfigurace fondu jsou propojeny se pravidla. Sonda stavu, který odkazuje pravidlo se používá k určení jak nové toky se odesílají do uzlu ve fondu back-endu. Front-endu (označuje se také jako virtuální IP adresy) je definována 3 řazené kolekce členů sestává z adresy IP (veřejné nebo interní), přenosový protokol (UDP nebo TCP) a číslo portu od pravidlo Vyrovnávání zatížení. Back-endový fond je kolekce konfigurací protokolu IP virtuálního počítače (součást síťové karty prostředků), které odkazují na back-endového fondu nástroje pro vyrovnávání zatížení.

Následující tabulka obsahuje některé ukázkové konfigurace front-endu:

| Front-end | IP adresa | Protokol | port |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

V tabulce jsou uvedeny čtyři různé front-endů. Front-Endů č. 1, #2 a #3 jsou jediné front-endu s více pravidel. Použít stejnou IP adresu, ale portu nebo protokol se liší pro každý front-end. Front-Endů č. 1 a #4 jsou příkladem několik front-endů, kde stejný front-endu protokol a port se využívají opakovaně v několika front-endů.

Nástroj Azure Load Balancer poskytuje flexibilitu při definování pravidla Vyrovnávání zatížení. Pravidlo deklaruje jak adresu a port na front-endu je namapována na cílovou adresu a port v back-endu. Určuje, jestli se využívají opakovaně back-endu v pravidlech, závisí na typu pravidla. Každý typ pravidla má specifické požadavky, které můžou ovlivnit návrh konfigurace a test paměti hostitele. Existují dva typy pravidel:

1. Výchozí pravidlo s opakované použití žádné back-endový port
2. Pravidlo plovoucí IP adresy, kde údaje znovu použijí back-endu

Nástroj Azure Load Balancer umožňuje kombinovat oba typy pravidel na stejnou konfiguraci nástroje pro vyrovnávání zatížení. Nástroje pro vyrovnávání zatížení můžete používat současně pro daného virtuálního počítače nebo libovolné kombinaci tak dlouho, dokud dodržovat omezení pravidla. Jaký typ pravidla zvolíte závisí na požadavky aplikace a složitosti podpora této konfigurace. By se měl vyhodnotit, jaké typy pravidel jsou nejvhodnější pro váš scénář.

Začněte s výchozí chování podíváme na další tyto scénáře.

## <a name="rule-type-1-no-backend-port-reuse"></a>Pravidlo typu #1: opakované použití žádné back-endový port

![Několik front-endu obrázku s zelenou a fialové front-endu](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

V tomto scénáři jsou nakonfigurované front-endů následujícím způsobem:

| Front-end | IP adresa | Protokol | port |
| --- | --- | --- | --- |
| ![zelená front-endu](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![fialový front-endu](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

DIP je cílem příchozího toku. Každý virtuální počítač v back-endový fond poskytuje požadovanou službu jedinečný port vyhrazené IP adresy. Tato služba je spojené s front-endu pomocí definice pravidla.

Definujeme dvě pravidla:

| Pravidlo | Mapování front-endu | Na back-endový fond |
| --- | --- | --- |
| 1 |![zelená front-endu](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![Back-endu](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![Back-endu](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![Back-endu](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![Back-endu](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

Kompletní mapování ve službě Azure Load Balancer je teď následujícím způsobem:

| Pravidlo | Front-endové IP adresy | Protokol | port | Cíl | port |
| --- | --- | --- | --- | --- | --- |
| ![zelená pravidlo](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |Vyhrazené IP adresy IP adresa |80 |
| ![fialový pravidlo](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |Vyhrazené IP adresy IP adresa |81 |

Každé pravidlo pro dotazovaní musí předložit tok s jedinečnou kombinací cílová IP adresa a cílový port. Změnou cílový port toku víc pravidel doručovat toků do stejné vyhrazené IP adresy na jiném portu.

Sondy stavu jsou vždy směrovala na vyhrazené IP adresy virtuálního počítače. Musíte zajistit, je, že váš test odráží stav virtuálního počítače.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Pravidlo typu #2: opakované použití port back-end pomocí plovoucí IP adresy

Azure Load Balancer poskytuje flexibilitu pro opětovné použití front-endový port napříč více front-endy bez ohledu na typ pravidla použít. Kromě toho některé scénáře aplikací raději nebo vyžadují stejný port, které bude využívat několik instancí aplikace na jeden virtuální počítač v back-endový fond. Mezi běžné příklady opakované použití portu patří clusteringu pro vysokou dostupnost, síťová virtuální zařízení a jestli vystavuje několik koncových bodů protokolu TLS bez znova šifrovat.

Pokud chcete znovu použít back-endový port napříč více pravidel, je nutné povolit plovoucí IP adresy v definici pravidla.

Je "Plovoucí IP" Azure terminologie pro část, která se označuje jako přímé Server vrátit (DSR). DSR se skládá ze dvou částí: topologie toku a IP adres schéma mapování. Na úrovni platformy Azure Load Balancer vždy funguje v topologii DSR toku bez ohledu na to, zda je nebo není povolená plovoucí IP adresy. To znamená, že odchozí součást toku je vždy správně přepsány, aby tok přímo zpět k původu.

S typem pravidla výchozí Azure zpřístupňuje tradiční schéma mapování IP adres pro snadnější použití vyrovnávání zatížení. Povolení plovoucí IP adresy se změní schéma mapování IP adres umožňuje pro vyšší flexibilitu, jak je popsáno níže.

Následující diagram znázorňuje tuto konfiguraci:

![Několik front-endu obrázku s zelenou a fialové front-endu s DSR](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

V tomto scénáři má každý virtuální počítač v back-endový fond tři síťová rozhraní:

* Vyhrazené IP adresy: virtuálních síťových Adaptérů přidružený k virtuálnímu počítači (Konfigurace IP prostředku Azure pro síťové rozhraní)
* Front-endu 1: rozhraní zpětné smyčky v rámci hostovaného operačního systému, který má nakonfigurovanou IP adresu front-endu 1
* Front-endu 2: rozhraní zpětné smyčky v rámci hostovaného operačního systému, který má nakonfigurovanou IP adresu front-endu 2

> [!IMPORTANT]
> Konfigurace zpětné smyčky rozhraní se provádí v rámci hostovaného operačního systému. Tato konfigurace není provedena nebo spravuje Azure. Bez této konfiguraci pravidel, nebudou fungovat. Definice sondy stavu použijte vyhrazené IP adresy virtuálního počítače, nikoli rozhraní zpětné smyčky představující DSR front-endu. Vaše služba proto musíte zadat odpovědím sondy portu vyhrazené IP adresy, které odráží stav služby nabízené na rozhraní zpětné smyčky představující DSR front-endu.

Předpokládejme, že stejnou konfiguraci front-endu jako v předchozím scénáři:

| Front-end | IP adresa | Protokol | port |
| --- | --- | --- | --- |
| ![zelená front-endu](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![fialový front-endu](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Definujeme dvě pravidla:

| Pravidlo | Front-end | Mapování na back-endový fond |
| --- | --- | --- |
| 1 |![pravidlo](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![Back-endu](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 (v VM1 a VM2) |
| 2 |![pravidlo](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![Back-endu](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 (v VM1 a VM2) |

Následující tabulka ukazuje kompletní mapování v nástroji pro vyrovnávání zatížení:

| Pravidlo | Front-endové IP adresy | Protokol | port | Cíl | port |
| --- | --- | --- | --- | --- | --- |
| ![zelená pravidlo](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |stejné jako front-endu (65.52.0.1) |stejné jako front-endu (80) |
| ![fialový pravidlo](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |stejné jako front-endu (65.52.0.2) |stejné jako front-endu (80) |

Cíl příchozího toku je IP adresa front-endu na adresu zpětné smyčky rozhraní ve virtuálním počítači. Každé pravidlo pro dotazovaní musí předložit tok s jedinečnou kombinací cílová IP adresa a cílový port. Pomocí různých cílovou adresu IP toku, opakované použití portu je možné ve stejném virtuálním počítači. Vaše služba je přístupný nástroj pro vyrovnávání zatížení pomocí vazby na IP adresu a port rozhraní odpovídajících zpětné smyčky front-endu.

Všimněte si, že v tomto příkladu není změnit cílový port. I když je to scénář plovoucí IP adresy, podporuje nástroj pro vyrovnávání zatížení Azure také definice pravidla pro přepsání cílový port back-endu a aby liší od cílový port front-endu.

Typ pravidla plovoucí IP adresy je základem některé postupy konfigurace nástroje pro vyrovnávání zatížení. Je jedním z příkladů, které jsou aktuálně dostupné [SQL AlwaysOn s víc naslouchacích procesů](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) konfigurace. V průběhu času bude dokumentujeme další z těchto scénářů.

## <a name="limitations"></a>Omezení

* Několik front-endová konfigurace jsou podporovány pouze s virtuálními počítači IaaS.
* S pravidlem plovoucí IP adresy musí vaše aplikace použít primární konfiguraci protokolu IP pro odchozí toky. Pokud vaše aplikace vytvoří vazbu na IP adresu front-endu nakonfigurované na zpětnou smyčku rozhraní v guest OS, Azure SNAT není k dispozici pro přepsání odchozí tok a tok selže.
* Veřejné IP adresy mají vliv na fakturaci. Další informace najdete v tématu [ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Předplatné omezení platí. Další informace najdete v tématu [omezení služby](../azure-subscription-service-limits.md#networking-limits) podrobnosti.

## <a name="next-steps"></a>Další postup

- Kontrola [odchozí připojení](load-balancer-outbound-connections.md) na vědomí následky několik front-endů na chování odchozí připojení.
