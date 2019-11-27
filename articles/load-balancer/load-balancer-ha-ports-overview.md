---
title: Přehledu portů s vysokou dostupností v Azure
titleSuffix: Azure Load Balancer
description: Další informace o na interní nástroj pro vyrovnávání zatížení porty vysokou dostupnost.
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
ms.openlocfilehash: c6529e2585a7fca2d160d093d303afa02e6f9379
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215077"
---
# <a name="high-availability-ports-overview"></a>Přehledu portů s vysokou dostupností

Azure Load balanceru úrovně Standard vám pomůže – nástroj pro vyrovnávání zatížení na všech portech TCP a UDP toky současně při použití interního nástroje load balancer. 

Pravidlo vyrovnávání zatížení portů vysoké dostupnosti (HA) je variantou pravidla vyrovnávání zatížení, které je nakonfigurováno na interní Standard Load Balancer. Použití nástroje pro vyrovnávání zatížení můžete zjednodušit tím, že jedno pravidlo Vyrovnávání zatížení poskytuje všechny protokoly TCP a UDP toky, které přicházejí na všech portech interní Load balanceru úrovně Standard. Vyrovnávání zatížení rozhodnutí je na tok. Tato akce je založena na následujících pěti připojeních řazené kolekce členů: zdrojová IP adresa, zdrojový port, cílová IP adresa, cílový port a protokol.

Pravidla vyrovnávání zatížení portů HA vám pomůžou s kritickými scénáři, jako je vysoká dostupnost a škálování síťových virtuálních zařízení (síťová virtuální zařízení) uvnitř virtuálních sítí. Tato funkce může také pomoct při velký počet portů musí být s vyrovnáváním zatížení. 

Pravidla vyrovnávání zatížení portů HA jsou nakonfigurovaná při nastavení front-endu a back-endu portů na **hodnotu 0** a na protokol pro **všechny**. Prostředek interního nástroje pro vyrovnávání zatížení potom vyrovnává všechny toky TCP a UDP bez ohledu na číslo portu.

## <a name="why-use-ha-ports"></a>Proč používat porty s vysokou DOSTUPNOSTÍ?

### <a name="nva"></a>Síťová virtuální zařízení

Síťová virtuální zařízení můžete použít pomáhají zabezpečit vaše úloha Azure před různými typy bezpečnostních hrozeb. Při použití síťových virtuálních zařízení v těchto scénářích platí, musí být spolehlivé a vysoce dostupné a musí horizontální navýšení kapacity pro požadavek.

Těchto cílů můžete dosáhnout pouhým přidáním instance síťového virtuálního zařízení k back endový fond vašeho nástroje pro vyrovnávání zatížení interní a konfiguraci vysokou DOSTUPNOSTÍ porty pravidlo nástroje pro vyrovnávání zatížení.

Porty s vysokou DOSTUPNOSTÍ pro scénáře HA síťové virtuální zařízení, nabízí následující výhody:
- Poskytnout rychlé převzetí služeb při selhání v dobrém stavu instance, sondy stavu jednotlivé instance
- Zajištění vyššího výkonu při škálování na více instancí až na *n*-aktivních instancí
- Zajištění scénářů *n*-aktivních a aktivních – pasivní
- Eliminuje nutnost komplexní řešení, jako je Apache ZooKeeper uzly pro monitorování zařízení

V níže uvedeném diagramu nasazení střed a paprsek virtuální sítě. Paprsky vynuceného tunelování provoz do virtuální sítě centra a přes síťové virtuální zařízení, před opuštěním důvěryhodné místa. Síťová virtuální zařízení jsou připojeni interní Load balanceru úrovně Standard s konfigurací porty HA. Veškerý provoz můžete zpracovat a předávaných odpovídajícím způsobem. Když se nakonfiguruje jako zobrazit v následujícím diagramu, pravidlo vyrovnávání zatížení portů HA navíc poskytuje symetrii Flow pro příchozí a odchozí provoz.

<a node="diagram"></a>
![diagramem virtuální sítě s rozbočovačem a s síťová virtuální zařízení nasazenými v režimu HA](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Pokud používáte síťových virtuálních zařízení, ověřte u poskytovatelů jak nejlépe používat porty s vysokou DOSTUPNOSTÍ a zjistěte, jaké postupy se podporují.

### <a name="load-balancing-large-numbers-of-ports"></a>Vyrovnávání zatížení velký počet portů

Porty s vysokou DOSTUPNOSTÍ můžete použít také pro aplikace, které vyžadují Vyrovnávání zatížení velkého počtu portů. Tyto scénáře můžete zjednodušit pomocí interní [Standard Load Balancer](load-balancer-standard-overview.md) s porty ha. Jedno pravidlo Vyrovnávání zatížení nahrazuje více jednotlivá pravidla Vyrovnávání zatížení, jeden pro každý z portů.

## <a name="region-availability"></a>Dostupnost v oblastech

Funkce porty HA je k dispozici ve všech globálních oblastech Azure.

## <a name="supported-configurations"></a>Podporované konfigurace

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Jeden, s plovoucí konfiguraci protokolu IP (jiné - Server přímou odpověď ze)-porty s vysokou DOSTUPNOSTÍ na interní Load balanceru úrovně Standard

Tato konfigurace je základní konfigurace porty HA. Vysokou DOSTUPNOSTÍ můžete nakonfigurovat porty služby Vyrovnávání zatížení pravidla u jedné front-endové IP adresy následujícím způsobem:
1. Při konfiguraci Standard Load Balancer zaškrtněte políčko **porty ha** v konfiguraci Load Balancer pravidla.
2. V případě **plovoucí IP adresy**vyberte **zakázáno**.

Tato konfigurace nepovoluje žádné jiné služby Vyrovnávání zatížení konfiguraci pravidla na aktuální prostředek nástroje pro vyrovnávání zatížení. Umožňuje také žádné jiné vnitřní konfigurace služby Vyrovnávání zatížení prostředků pro danou sadu back endových instancí.

Ale můžete nakonfigurovat veřejný Load balancer úrovně Standard pro back endových instancí kromě toto pravidlo porty HA.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Jediné, s plovoucí desetinnou čárkou konfigurace IP (Server přímou odpověď ze)-porty s vysokou DOSTUPNOSTÍ na interní Load balanceru úrovně Standard

Podobně můžete nakonfigurovat nástroj pro vyrovnávání zatížení tak, aby používal pravidlo vyrovnávání zatížení s **portem ha** s jedním front-endu nastavením **plovoucí IP adresy** na **povoleno**. 

Pomocí této konfigurace můžete přidat další plovoucí IP pravidla Vyrovnávání zatížení a/nebo nástroj pro vyrovnávání zatížení veřejnou. Však nelze používat se s plovoucí IP Adresou, porty HA nad tuto konfiguraci s vyrovnáváním zatížení.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Konfigurací s více porty s vysokou DOSTUPNOSTÍ na interní Load balanceru úrovně Standard

Pokud vaše situace vyžaduje, abyste nakonfigurovali více než jeden HA port front-endu pro stejný fond back-end, máte následující: 
- Konfigurace více front-endu privátní IP adresu pro jeden prostředek interní nástroj pro vyrovnávání zatížení.
- Nakonfigurujte víc pravidel Vyrovnávání zatížení, kde každé pravidlo má jednu jedinečnou front-endovou IP adresu vybrané.
- Vyberte možnost **porty ha** a pak nastavte **plovoucí IP adresu** na **povoleno** pro všechna pravidla vyrovnávání zatížení.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Interního nástroje load balancer pomocí porty s vysokou DOSTUPNOSTÍ a veřejný load balancer ve stejné instanci back-end

Můžete nakonfigurovat *jeden* veřejný prostředek Standard Load Balancer pro prostředky back-endu spolu s jednou interní standard Load Balancer s porty ha.

>[!NOTE]
>Tato funkce je aktuálně k dispozici prostřednictvím šablon Azure Resource Manageru, ale není k dispozici prostřednictvím portálu Azure portal.

## <a name="limitations"></a>Omezení

- Pravidla vyrovnávání zatížení portů HA jsou k dispozici pouze pro interní Standard Load Balancer.
- Kombinování vysokou DOSTUPNOSTÍ porty pravidlo Vyrovnávání zatížení a porty vysokou dostupnost, pravidlo Vyrovnávání zatížení se nepodporuje.
- Existující fragmenty IP adresy předají pravidla pro vyrovnávání zatížení s porty HA do stejného cíle jako první paket.  Fragmentace IP adres protokolu UDP nebo TCP není podporována.
- Pravidla vyrovnávání zatížení portů HA nejsou k dispozici pro protokol IPv6.
- Symetrie flow (primárně pro scénáře síťové virtuální zařízení) je podporována u back-endu instance a jediného síťového rozhraní (a jedné konfigurace protokolu IP) pouze v případě, že se používá, jak je znázorněno v diagramu výše a používá pravidla pro vyrovnávání zatížení portů Není k dispozici v žádném jiném scénáři. To znamená, že dva nebo více Load Balancerch prostředků a jejich příslušných pravidel nezávisle na rozhodnutích a nejsou nikdy koordinovány. Podívejte se na popis a diagram [síťových virtuálních zařízení](#nva). Pokud používáte více síťových adaptérů nebo chcete-li síťové virtuální zařízení mezi veřejným a interním Load Balancer, není k dispozici symetrie Flow.  Můžete to obejít tak, že zdroj NAT'ing příchozí přenos dat zařízení na IP adresu a umožníte doručení odpovědí na stejný síťové virtuální zařízení.  Důrazně však doporučujeme použít jeden síťový adaptér a použít referenční architekturu uvedenou v diagramu výše.


## <a name="next-steps"></a>Další kroky

- [Konfigurace portů HA na interní Standard Load Balancer](load-balancer-configure-ha-ports.md)
- [Informace o Standard Load Balancer](load-balancer-standard-overview.md)
