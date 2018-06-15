---
title: Přehled porty vysoké dostupnosti v Azure | Microsoft Docs
description: Další informace o na interní nástroj pro vyrovnávání zatížení porty vysokou dostupnost.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: kumud
ms.openlocfilehash: 53e09498324f802ce1839d262999657d37ee973b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32169114"
---
# <a name="high-availability-ports-overview"></a>Přehled porty vysokou dostupnost

Azure standardní Vyrovnávání zatížení vám pomůže Vyrovnávání zatížení na všechny porty TCP a UDP toky současně při použití vyrovnávání zatížení interní. 

Pravidla porty vysokou dostupnost (HA) je-li hodnotu typu variant pravidla Vyrovnávání zatížení, nakonfigurované na k interní standardní Vyrovnávání zatížení. Použití nástroje pro vyrovnávání zatížení můžete zjednodušit tím, že poskytuje jediné pravidlo Vyrovnávání zatížení všechny toky TCP a UDP, které přicházejí na všech portech k interní standardní Vyrovnávání zatížení. Rozhodnutí o vyrovnávání zatížení se provádí na toku. Tato akce je založena na následujících pět řazené kolekce členů připojení: zdrojové IP adresy, zdrojového portu, cílové IP adresy, cílový port a protokolu.

Funkce porty HA vám pomůže se kritických scénářů, jako je vysoká dostupnost a dosah pro sítě virtuálních zařízení (NVAs) uvnitř virtuální sítě. Tato funkce může také pomoci při velký počet portů musí být vyrovnávání zatížení sítě. 

Funkce porty HA je nakonfigurovaná, když nastavíte porty front-end a back-end na **0** a protokol, který se **všechny**. Prostředek pro vyrovnávání zatížení pro vnitřní pak vyrovnává všechny toky TCP a UDP, bez ohledu na číslo portu.

## <a name="why-use-ha-ports"></a>Proč používat porty HA?

### <a name="nva"></a>Virtuální síťová zařízení

NVAs můžete použít k zabezpečení vaše úloha Azure z více typů ohrožení zabezpečení. Použijete-li v těchto scénářích NVAs, musí být spolehlivé a vysoce dostupné a musí škálovat pro vyžádání.

Můžete jednoduše tak, že přidáním instance hodnocení chyb zabezpečení sítě k back-end fondu nástroj pro vyrovnávání zatížení pro vnitřní dosáhnout těchto cílů a konfigurace HA porty pravidlo Vyrovnávání zatížení.

Pro scénáře HA hodnocení chyb zabezpečení HA porty nabízí následující výhody:
- Rychlé převzetí služeb při selhání na v pořádku instance poskytnout sondy stavu jednotlivých instancí
- Zajistit vyšší výkon se Škálováním na více systémů pro *n*-aktivní instance
- Zadejte *n*-aktivní a aktivní – pasivní scénáře
- Eliminují nutnost použití komplexní řešení, jako je například Apache ZooKeeper uzly pro monitorování zařízení

Následující diagram představuje nasazení střed a paprsek virtuální sítě. Vynuceného tunelování koncových jejich provoz na virtuální síť rozbočovače a prostřednictvím hodnocení chyb zabezpečení, před opuštěním důvěryhodné místa. NVAs jsou připojeni k interní standardní pro vyrovnávání zatížení s konfigurací portů HA. Veškerý provoz můžete zpracovat a předávat odpovídajícím způsobem.

![Diagram střed a paprsek virtuální síť s NVAs nasazené v režimu HA](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Pokud používáte NVAs, ověřte jejich zprostředkovatelům jak nejlépe používat porty HA a zjistěte, jaké postupy se podporují.

### <a name="load-balancing-large-numbers-of-ports"></a>Vyrovnávání zatížení velkého počtu portů

Můžete také použít HA porty pro aplikace, které vyžadují Vyrovnávání zatížení velkého počtu portů. Tyto scénáře můžete zjednodušit pomocí interní [nástroj pro vyrovnávání zatížení](load-balancer-standard-overview.md) s vysokou DOSTUPNOSTÍ porty. Jedno pravidlo Vyrovnávání zatížení nahrazuje více jednotlivých Vyrovnávání zatížení pravidel, jednu pro každý z portů.

## <a name="region-availability"></a>Dostupnost v oblastech

Funkce porty HA je dostupná v všech oblastech globální Azure.

## <a name="supported-configurations"></a>Podporované konfigurace

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Jeden s plovoucí IP (bez - přímou odpověď ze serveru) HA-porty konfiguraci na interní standardní Vyrovnávání zatížení

Tato konfigurace je základní konfiguraci portů HA. Můžete nakonfigurovat HA porty Vyrovnávání zatížení pravidla u jediné front-end IP adresy následujícím způsobem:
1. Při konfiguraci nástroj pro vyrovnávání zatížení, vyberte **HA porty** políčko v konfiguraci pravidla pro vyrovnávání zatížení.
2. Pro **plovoucí IP adresa**, vyberte **zakázané**.

Tato konfigurace neumožňuje ostatní Vyrovnávání zatížení pravidlo konfigurace na aktuální prostředek pro vyrovnávání zatížení. Umožňuje také žádné jiné vnitřní konfigurace služby Vyrovnávání zatížení prostředků pro danou sadu instance back-end.

Ale můžete nakonfigurovat veřejný Standard Vyrovnávání zatížení pro instance back-end kromě toto pravidlo porty HA.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Jeden plovoucí IP (přímé odpovědi ze serveru) HA-porty konfiguraci na interní standardní Vyrovnávání zatížení

Podobně můžete nakonfigurovat nástroj pro vyrovnávání zatížení použít pravidlo Vyrovnávání zatížení s **HA Port** s jednom front-endu nastavením **plovoucí IP adresa** k **povoleno**. 

Pomocí této konfigurace můžete přidat další plovoucí IP Vyrovnávání zatížení pravidel a vyrovnávání zatížení veřejné. Však nelze používat s plovoucí IP, HA porty konfiguraci Vyrovnávání zatížení na základě této konfigurace.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Konfigurací s více porty HA na interní standardní Vyrovnávání zatížení

Pokud váš scénář vyžaduje, že nakonfigurujete více než jeden HA portů front-endu pro stejný fond back-end, můžete provést následující: 
- Konfigurace více než jeden front-end privátní IP adresy pro jediný zdroj interní nástroj pro vyrovnávání zatížení.
- Nakonfigurujte víc pravidel Vyrovnávání zatížení, kde každé pravidlo má jeden jedinečný front-end IP adresu vybrané.
- Vyberte **HA porty** možnost a potom nastavte **plovoucí IP adresa** k **povoleno** pro všechna pravidla Vyrovnávání zatížení.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Interní nástroj s vysokou DOSTUPNOSTÍ porty a nástroj pro vyrovnávání zatížení veřejnou ve stejné instanci back-end

Můžete nakonfigurovat *jeden* prostředek veřejné nástroj pro vyrovnávání zatížení pro back endové prostředky, společně s jeden interní standardní Vyrovnávání zatížení s vysokou DOSTUPNOSTÍ porty.

>[!NOTE]
>Tato funkce je aktuálně k dispozici prostřednictvím šablon Azure Resource Manageru, ale není k dispozici prostřednictvím portálu Azure.

## <a name="limitations"></a>Omezení

- HA porty konfigurace je k dispozici pouze pro interní služby load balancer. Není k dispozici pro nástroje pro vyrovnávání zatížení veřejné.

- Kombinování HA porty pravidlo Vyrovnávání zatížení a bez HA portů, které se pravidlo Vyrovnávání zatížení nepodporuje.

- Funkci porty HA není k dispozici pro protokol IPv6.

- Tok symetrie pro scénáře hodnocení chyb zabezpečení sítě je podporováno pouze jednu síťovou kartu. Zobrazit popis a diagram pro [síťových virtuálních zařízení](#nva). Ale pokud cíl NAT může fungovat pro váš scénář, můžete ji použít a ujistěte se, že nástroj pro vyrovnávání zatížení pro vnitřní odešle návratový provoz do stejné hodnocení chyb zabezpečení.


## <a name="next-steps"></a>Další postup

- [Konfigurace portů HA na interní standardní Vyrovnávání zatížení](load-balancer-configure-ha-ports.md)
- [Další informace o vyrovnávání zatížení standardní](load-balancer-standard-overview.md)
