---
title: Přehled portů vysoké dostupnosti v Azure
titleSuffix: Azure Load Balancer
description: Přečtěte si o vyrovnávání zatížení portů vysoké dostupnosti na interním nástroji pro vyrovnávání zatížení.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2019
ms.author: allensu
ms.openlocfilehash: 6f089af71e4d32023e9cebd6613872f7db0eed7a
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94694955"
---
# <a name="high-availability-ports-overview"></a>Přehled portů vysoké dostupnosti

Azure Standard Load Balancer vám pomůže vyrovnávat zatížení **všech** toků protokolů na **všech** portech současně v případě, že používáte interní Load Balancer přes porty ha.

Porty vysoké dostupnosti (HA) je typ pravidla vyrovnávání zatížení, které poskytuje snadný způsob vyrovnávání zatížení **všech** toků, které přicházejí na **všechny** porty interního Standard Load Balancer. Rozhodnutí o vyrovnávání zatížení se provádí na jeden tok. Tato akce je založena na následujících pěti připojeních řazené kolekce členů: zdrojová IP adresa, zdrojový port, cílová IP adresa, cílový port a protokol.

Pravidla vyrovnávání zatížení portů HA vám pomůžou s kritickými scénáři, jako je vysoká dostupnost a škálování síťových virtuálních zařízení (síťová virtuální zařízení) uvnitř virtuálních sítí. Tato funkce může také pomáhat při vyrovnávání zatížení velkého počtu portů. 

Pravidla vyrovnávání zatížení portů HA jsou nakonfigurovaná při nastavení front-endu a back-endu portů na **hodnotu 0** a na protokol pro **všechny**. Prostředek interního nástroje pro vyrovnávání zatížení potom vyrovnává všechny toky TCP a UDP bez ohledu na číslo portu.

## <a name="why-use-ha-ports"></a>Proč používat porty HA?

### <a name="network-virtual-appliances"></a><a name="nva"></a>Síťová virtuální zařízení

Síťová virtuální zařízení můžete použít k zajištění zabezpečení úloh Azure před několika typy bezpečnostních hrozeb. Když v těchto scénářích použijete síťová virtuální zařízení, musí být spolehlivé a vysoce dostupné a musí se škálovat na vyžádání.

Tyto cíle můžete jednoduše dosáhnout přidáním instancí síťové virtuální zařízení do fondu back-end interního nástroje pro vyrovnávání zatížení a konfigurací pravidla pro vyrovnávání zatížení portů HA.

U scénářů s síťové virtuální zařízení HA nabízí porty HA následující výhody:
- Zajištění rychlého převzetí služeb při selhání u instancí s testy stavu jednotlivých instancí
- Zajištění vyššího výkonu při škálování na více instancí až na *n*-aktivních instancí
- Zajištění scénářů *n*-aktivních a aktivních – pasivní
- Eliminujte nutnost složitých řešení, jako jsou Apache ZooKeeper uzly pro monitorování zařízení.

Následující diagram představuje nasazení virtuální sítě typu centrum a paprsek. Paprsky Vynutí tunelové propojení jejich provozu do virtuální sítě rozbočovače i přes síťové virtuální zařízení, než vynechají důvěryhodné místo. Síťová virtuální zařízení jsou za interní Standard Load Balancer s konfigurací portů HA. Veškerý provoz se dá zpracovat a přeslat odpovídajícím způsobem. Když se nakonfiguruje jako zobrazit v následujícím diagramu, pravidlo vyrovnávání zatížení portů HA navíc poskytuje symetrii Flow pro příchozí a odchozí provoz.

<a node="diagram"></a>
![Diagram virtuální sítě typu hub a paprsek s síťová virtuální zařízení nasazeným v režimu HA](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Pokud používáte síťová virtuální zařízení, potvrďte jejich poskytovatele, jak nejlépe využít porty HA a zjistit, jaké jsou podporované scénáře.

### <a name="load-balancing-large-numbers-of-ports"></a>Vyrovnávání zatížení velkých čísel portů

Porty HA můžete použít také pro aplikace, které vyžadují vyrovnávání zatížení velkého počtu portů. Tyto scénáře můžete zjednodušit pomocí interní [Standard Load Balancer](./load-balancer-overview.md) s porty ha. Jedno pravidlo vyrovnávání zatížení nahrazuje několik individuálních pravidel vyrovnávání zatížení, jednu pro každý port.

## <a name="region-availability"></a>Dostupnost v oblastech

Funkce porty vysoké dostupnosti je dostupná ve všech globálních oblastech Azure.

## <a name="supported-configurations"></a>Podporované konfigurace

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Jedna neodkazovaná konfigurace nepřímých IP adres (nepřímá návratová Server) na interní Standard Load Balancer

Tato konfigurace je základní konfigurace portů HA. Pomocí následujícího postupu můžete nakonfigurovat pravidlo vyrovnávání zatížení portů HA pro jednu front-end IP adresu:
1. Při konfiguraci Standard Load Balancer zaškrtněte políčko **porty ha** v konfiguraci Load Balancer pravidla.
2. V případě **plovoucí IP adresy** vyberte **zakázáno**.

Tato konfigurace nepovoluje žádnou jinou konfiguraci pravidla vyrovnávání zatížení v aktuálním prostředku nástroje pro vyrovnávání zatížení. Pro danou sadu back-endové instance taky neumožňuje žádnou jinou konfiguraci prostředků interního nástroje pro vyrovnávání zatížení.

Kromě tohoto pravidla pro porty HA ale můžete nakonfigurovat veřejné Standard Load Balancer pro back-endové instance.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Jedna a plovoucí IP adresa (přímá návratová Server) s vysokou dostupností na interní Standard Load Balancer

Podobně můžete nakonfigurovat nástroj pro vyrovnávání zatížení tak, aby používal pravidlo vyrovnávání zatížení s **portem ha** s jedním front-endu nastavením **plovoucí IP adresy** na **povoleno**. 

Pomocí této konfigurace můžete přidat další pravidla vyrovnávání zátěžového vyrovnávání zatížení sítě nebo veřejné nástroje pro vyrovnávání zatížení. V této konfiguraci ale nemůžete použít konfiguraci vyrovnávání zatížení HA s neplovoucími porty IP a vyšší úrovně.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Více konfigurací HA-ports na interním Standard Load Balancer

Pokud váš scénář vyžaduje, abyste pro stejný záložní fond nakonfigurovali více než jeden front-end port vysoké dostupnosti, můžete postupovat takto: 
- Nakonfigurujte více než jednu privátní IP adresu front-endu pro jeden interní prostředek Standard Load Balancer.
- Nakonfigurujte více pravidel vyrovnávání zatížení, kde každé pravidlo má jednu jedinečnou IP adresu front-end.
- Vyberte možnost **porty ha** a pak nastavte **plovoucí IP adresu** na **povoleno** pro všechna pravidla vyrovnávání zatížení.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Interní nástroj pro vyrovnávání zatížení s porty HA a veřejným nástrojem pro vyrovnávání zatížení ve stejné back-endové instanci

Můžete nakonfigurovat *jeden* prostředek veřejného Standard Load Balancer pro back-end prostředky spolu s jednou interní standard Load Balancer s porty ha.

## <a name="limitations"></a>Omezení

- Pravidla vyrovnávání zatížení portů HA jsou k dispozici pouze pro interní Standard Load Balancer.
- Kombinace pravidla vyrovnávání zatížení s porty HA a pravidlo vyrovnávání zatížení s porty bez vysoké dostupnosti, které odkazují na stejné IPConfiguration back-endu, **nejsou** v rámci jedné konfigurace IP adresy front-endu podporovány, pokud nemají povolenou plovoucí IP adresu.
- Existující fragmenty IP adresy předají pravidla pro vyrovnávání zatížení s porty HA do stejného cíle jako první paket.  Fragmentace IP adres protokolu UDP nebo TCP není podporována.
- Symetrie flow (primárně pro scénáře síťové virtuální zařízení) je podporována u back-endu instance a jediného síťového rozhraní (a jedné konfigurace protokolu IP) pouze v případě, že se používá, jak je znázorněno v diagramu výše a používá pravidla pro vyrovnávání zatížení portů Není k dispozici v žádném jiném scénáři. To znamená, že dva nebo více Load Balancerch prostředků a jejich příslušných pravidel nezávisle na rozhodnutích a nejsou nikdy koordinovány. Podívejte se na popis a diagram [síťových virtuálních zařízení](#nva). Pokud používáte více síťových adaptérů nebo chcete-li síťové virtuální zařízení mezi veřejným a interním Load Balancer, není k dispozici symetrie Flow.  Můžete to obejít tak, že zdroj NAT'ing příchozí přenos dat zařízení na IP adresu a umožníte doručení odpovědí na stejný síťové virtuální zařízení.  Důrazně však doporučujeme použít jeden síťový adaptér a použít referenční architekturu uvedenou v diagramu výše.


## <a name="next-steps"></a>Další kroky

- [Informace o Standard Load Balancer](load-balancer-overview.md)
