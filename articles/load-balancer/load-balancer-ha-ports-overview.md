---
title: Přehledu portů s vysokou dostupností v Azure
titlesuffix: Azure Load Balancer
description: Další informace o na interní nástroj pro vyrovnávání zatížení porty vysokou dostupnost.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2018
ms.author: kumud
ms.openlocfilehash: e37b127b112768cd09989e1a4b5edf99ca101983
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141862"
---
# <a name="high-availability-ports-overview"></a>Přehledu portů s vysokou dostupností

Azure Load balanceru úrovně Standard vám pomůže – nástroj pro vyrovnávání zatížení na všech portech TCP a UDP toky současně při použití interního nástroje load balancer. 

Pravidla portů vysoké dostupnosti (HA) je varianta pravidlo Vyrovnávání zatížení, nakonfigurovali na interní Load balanceru úrovně Standard. Použití nástroje pro vyrovnávání zatížení můžete zjednodušit tím, že jedno pravidlo Vyrovnávání zatížení poskytuje všechny protokoly TCP a UDP toky, které přicházejí na všech portech interní Load balanceru úrovně Standard. Vyrovnávání zatížení rozhodnutí je na tok. Tato akce je založena na následujících pět řazené kolekce členů připojení: zdrojové IP adresa, zdrojový port, cílová IP adresa, cílový port a protokol.

Funkce porty HA vám pomůžou s důležité scénáře, jako je vysoká dostupnost a škálování pro síťová virtuální zařízení (Nva) uvnitř virtuální sítě. Tato funkce může také pomoct při velký počet portů musí být s vyrovnáváním zatížení. 

Funkce porty HA se nakonfiguruje při nastavení portů front-endu a back endu **0** a protokol, který se **všechny**. Prostředek nástroje pro vyrovnávání zatížení interní pak vyrovnává všechny toky TCP a UDP, bez ohledu na číslo portu.

## <a name="why-use-ha-ports"></a>Proč používat porty s vysokou DOSTUPNOSTÍ?

### <a name="nva"></a>Síťová virtuální zařízení

Síťová virtuální zařízení můžete použít pomáhají zabezpečit vaše úloha Azure před různými typy bezpečnostních hrozeb. Při použití síťových virtuálních zařízení v těchto scénářích platí, musí být spolehlivé a vysoce dostupné a musí horizontální navýšení kapacity pro požadavek.

Těchto cílů můžete dosáhnout pouhým přidáním instance síťového virtuálního zařízení k back endový fond vašeho nástroje pro vyrovnávání zatížení interní a konfiguraci vysokou DOSTUPNOSTÍ porty pravidlo nástroje pro vyrovnávání zatížení.

Porty s vysokou DOSTUPNOSTÍ pro scénáře HA síťové virtuální zařízení, nabízí následující výhody:
- Poskytnout rychlé převzetí služeb při selhání v dobrém stavu instance, sondy stavu jednotlivé instance
- Zajištění vyššího výkonu se horizontální navýšení kapacity na *n*– aktivní instance
- Zadejte *n*– aktivní a aktivní pasivní scénáře
- Eliminuje nutnost komplexní řešení, jako je Apache ZooKeeper uzly pro monitorování zařízení

V níže uvedeném diagramu nasazení střed a paprsek virtuální sítě. Paprsky vynuceného tunelování provoz do virtuální sítě centra a přes síťové virtuální zařízení, před opuštěním důvěryhodné místa. Síťová virtuální zařízení jsou připojeni interní Load balanceru úrovně Standard s konfigurací porty HA. Veškerý provoz můžete zpracovat a předávaných odpovídajícím způsobem.

![Diagram střed a paprsek virtuální sítě se síťovými virtuálními zařízeními nasazené v režimu s vysokou DOSTUPNOSTÍ](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Pokud používáte síťových virtuálních zařízení, ověřte u poskytovatelů jak nejlépe používat porty s vysokou DOSTUPNOSTÍ a zjistěte, jaké postupy se podporují.

### <a name="load-balancing-large-numbers-of-ports"></a>Vyrovnávání zatížení velký počet portů

Porty s vysokou DOSTUPNOSTÍ můžete použít také pro aplikace, které vyžadují Vyrovnávání zatížení velkého počtu portů. Tyto scénáře můžete zjednodušit pomocí vnitřního [Load balanceru úrovně Standard](load-balancer-standard-overview.md) s porty s vysokou DOSTUPNOSTÍ. Jedno pravidlo Vyrovnávání zatížení nahrazuje více jednotlivá pravidla Vyrovnávání zatížení, jeden pro každý z portů.

## <a name="region-availability"></a>Dostupnost v oblastech

Funkce porty HA je k dispozici ve všech globálních oblastech Azure.

## <a name="supported-configurations"></a>Podporované konfigurace

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Jeden, s plovoucí konfiguraci protokolu IP (jiné - Server přímou odpověď ze)-porty s vysokou DOSTUPNOSTÍ na interní Load balanceru úrovně Standard

Tato konfigurace je základní konfigurace porty HA. Vysokou DOSTUPNOSTÍ můžete nakonfigurovat porty služby Vyrovnávání zatížení pravidla u jedné front-endové IP adresy následujícím způsobem:
1. Při konfiguraci nástroje pro vyrovnávání zatížení, vyberte **porty s vysokou DOSTUPNOSTÍ** zaškrtávací políčko v konfiguraci pravidla nástroje pro vyrovnávání zatížení.
2. Pro **plovoucí IP adresy**vyberte **zakázané**.

Tato konfigurace nepovoluje žádné jiné služby Vyrovnávání zatížení konfiguraci pravidla na aktuální prostředek nástroje pro vyrovnávání zatížení. Umožňuje také žádné jiné vnitřní konfigurace služby Vyrovnávání zatížení prostředků pro danou sadu back endových instancí.

Ale můžete nakonfigurovat veřejný Load balancer úrovně Standard pro back endových instancí kromě toto pravidlo porty HA.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Jediné, s plovoucí desetinnou čárkou konfigurace IP (Server přímou odpověď ze)-porty s vysokou DOSTUPNOSTÍ na interní Load balanceru úrovně Standard

Podobně můžete nakonfigurovat nástroj pro vyrovnávání zatížení použít pravidlo Vyrovnávání zatížení s **HA Port** pomocí jediného front-endu nastavením **plovoucí IP adresy** k **povoleno**. 

Pomocí této konfigurace můžete přidat další plovoucí IP pravidla Vyrovnávání zatížení a/nebo nástroj pro vyrovnávání zatížení veřejnou. Však nelze používat se s plovoucí IP Adresou, porty HA nad tuto konfiguraci s vyrovnáváním zatížení.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Konfigurací s více porty s vysokou DOSTUPNOSTÍ na interní Load balanceru úrovně Standard

Pokud vaše situace vyžaduje, abyste nakonfigurovali více než jeden HA port front-endu pro stejný fond back-end, máte následující: 
- Konfigurace více front-endu privátní IP adresu pro jeden prostředek interní nástroj pro vyrovnávání zatížení.
- Nakonfigurujte víc pravidel Vyrovnávání zatížení, kde každé pravidlo má jednu jedinečnou front-endovou IP adresu vybrané.
- Vyberte **porty s vysokou DOSTUPNOSTÍ** možnost a potom nastavte **plovoucí IP adresy** k **povoleno** pro všechna pravidla Vyrovnávání zatížení.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Interního nástroje load balancer pomocí porty s vysokou DOSTUPNOSTÍ a veřejný load balancer ve stejné instanci back-end

Můžete nakonfigurovat *jeden* prostředků veřejného Load balanceru úrovně Standard pro back endové prostředky, spolu jednoho interního Load balanceru úrovně Standard s porty s vysokou DOSTUPNOSTÍ.

>[!NOTE]
>Tato funkce je aktuálně k dispozici prostřednictvím šablon Azure Resource Manageru, ale není k dispozici prostřednictvím portálu Azure portal.

## <a name="limitations"></a>Omezení

- HA konfigurace portů je dostupné jenom pro interní služby load balancer. Není k dispozici pro nástroje pro vyrovnávání zatížení veřejnou.

- Kombinování vysokou DOSTUPNOSTÍ porty pravidlo Vyrovnávání zatížení a porty vysokou dostupnost, pravidlo Vyrovnávání zatížení se nepodporuje.

- Funkce porty HA není k dispozici pro protokol IPv6.

- Tok symetrie pro scénáře síťové virtuální zařízení podporuje pouze jednu síťovou kartu. Zobrazit popis a diagram pro [síťových virtuálních zařízení](#nva). Nicméně pokud cíl NAT můžete pro váš scénář fungovat, můžete ji třeba použít zajistit, aby že interní služby load balancer odešle zpětný provoz do stejné síťové virtuální zařízení.


## <a name="next-steps"></a>Další postup

- [Konfigurovat porty s vysokou DOSTUPNOSTÍ na interní Load balanceru úrovně Standard](load-balancer-configure-ha-ports.md)
- [Další informace o Load balanceru úrovně Standard](load-balancer-standard-overview.md)
