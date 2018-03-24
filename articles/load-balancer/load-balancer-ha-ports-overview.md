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
ms.openlocfilehash: 09c51441d393de5d801e7a4c259b711a527349d8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="high-availability-ports-overview"></a>Přehled porty vysokou dostupnost

Azure standardní Vyrovnávání zatížení můžete načíst vyrovnávání na všechny porty TCP a UDP toky současně, pokud používáte interní Vyrovnávání zatížení. 

Pravidlo porty HA je varianta pravidlo, které jsou nakonfigurované na k interní standardní Vyrovnávání zatížení vyrovnávání zatížení. Použití nástroje pro vyrovnávání zatížení můžete zjednodušit tím, že poskytuje jednoho pravidla pro vyrovnávání zatížení všechny TCP a UDP toky přicházejících na všech portech k interní standardní Vyrovnávání zatížení. Rozhodnutí Vyrovnávání zatížení sítě se provádí na toku. To je založené na následujících pět řazené kolekce členů připojení: zdrojové IP adresy, zdrojového portu, cílové IP adresy, cílový port a protokolu.

Funkce porty HA vám pomůže se kritických scénářů, jako je vysoká dostupnost a dosah pro sítě virtuálních zařízení (hodnocení chyb zabezpečení) uvnitř virtuální sítě. Může také pomoct při velký počet portů musí být skupinu s vyrovnáváním zatížení. 

Funkce porty HA je nakonfigurovaná, když nastavíte porty front-end a back-end na **0**a protokol, který se **všechny**. Interní nástroj pro vyrovnávání zatížení prostředků pak vyrovnává všechny toky TCP a UDP, bez ohledu na číslo portu.

## <a name="why-use-ha-ports"></a>Proč používat porty HA?

### <a name="nva"></a>Virtuální síťová zařízení

NVAs můžete použít pro zabezpečení vaše úloha Azure z více typů ohrožení zabezpečení. Pokud v těchto scénářích používají NVAs, musí být spolehlivé a vysoce dostupné a musí škálovat pro vyžádání.

Jednoduše tak, že přidáním instance hodnocení chyb zabezpečení sítě k back-end fondu Azure interní služby Vyrovnávání zatížení a nakonfigurování pravidla pro vyrovnávání zatížení porty HA můžete dosáhnout těchto cílů.

HA porty poskytují některé výhody pro scénáře HA hodnocení chyb zabezpečení sítě:
- Rychlé převzetí služeb při selhání v pořádku instancí s sondy stavu jednotlivých instancí
- Vyšší výkon se Škálováním na více systémů pro *n*-aktivní instance
- *N*-aktivní a aktivní – pasivní scénáře
- Povinnost komplexní řešení, jako je Apache ZooKeeper uzly pro monitorování zařízení

Následující diagram představuje nasazení střed a paprsek virtuální sítě. Vynuceného tunelování koncových jejich provoz na virtuální síť rozbočovače a prostřednictvím hodnocení chyb zabezpečení, před opuštěním důvěryhodné místa. NVAs jsou připojeni k interní standardní pro vyrovnávání zatížení s konfigurací portů HA. Veškerý provoz můžete zpracovat a předávat odpovídajícím způsobem.

![Diagram střed a paprsek virtuální síť s NVAs nasazené v režimu HA](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Pokud používáte NVAs, zkontrolujte u příslušného poskytovatele, jak nejlépe používat porty HA a jaké postupy se podporují.

### <a name="load-balancing-large-numbers-of-ports"></a>Velký počet portů Vyrovnávání zatížení

Můžete také použít HA porty pro aplikace, které vyžadují Vyrovnávání zatížení velkého počtu portů. Tyto scénáře můžete zjednodušit pomocí interní [nástroj pro vyrovnávání zatížení](load-balancer-standard-overview.md) s vysokou DOSTUPNOSTÍ porty. Pravidlo Vyrovnávání zatížení jednu nahrazuje více jednotlivých pravidel, jeden pro každou port Vyrovnávání zatížení.

## <a name="region-availability"></a>Dostupnost v oblastech

Funkce porty HA je dostupná v všech oblastech globální Azure.

## <a name="supported-configurations"></a>Podporované konfigurace

### <a name="one-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-the-internal-standard-load-balancer"></a>Jeden jedna s plovoucí IP (bez - přímou odpověď ze serveru) HA porty konfigurační na interní standardní Vyrovnávání zatížení

Toto je základní konfigurace HA porty. Následující konfigurace umožňuje konfigurovat porty HA rozložení zátěže na jednom front-endovou IP adresu –
- Při konfiguraci standardní nástroj pro vyrovnávání zatížení, vyberte **HA porty** políčko v konfiguraci pravidla pro vyrovnávání zatížení 
- Zajistit **plovoucí IP adresa** je nastaven na **zakázané**.

Tato konfigurace neumožňuje ostatní Vyrovnávání zatížení pravidlo konfigurace na aktuální prostředku Vyrovnávání zatížení, a také žádné jiné vnitřní konfigurace služby Vyrovnávání zatížení prostředků pro danou sadu instance back-end.

Ale můžete nakonfigurovat veřejný Standard Vyrovnávání zatížení pro instance back-end kromě tomuto pravidlu HA portu.

## <a name="one-single-floating-ip-direct-server-return-ha-ports-configuration-on-the-internal-standard-load-balancer"></a>Jeden jeden plovoucí IP (přímé odpovědi ze serveru) HA konfigurace portů na interní standardní Vyrovnávání zatížení

Podobně můžete nakonfigurovat nástroj pro vyrovnávání zatížení použít pravidlo s Vyrovnávání zatížení **HA Port** s jednom front-endu a **plovoucí IP adresa** nastavena na **povoleno**. 

Tato konfigurace umožňuje přidat více plovoucí IP rozložení zátěže pravidla, nebo veřejné Vyrovnávání zatížení. Vyrovnávání konfiguraci bez plovoucí IP HA portu boad nad tato konfigurace však nelze použít.

## <a name="multiple-ha-ports-configurations-on-the-internal-standard-load-balancer"></a>Konfigurací s více porty HA na interní standardní Vyrovnávání zatížení

Pokud váš scénář vyžaduje, že nakonfigurujete více než jeden port frontends HA pro stejný fond back-end, lze dosáhnout pomocí: 
- Konfigurace více než jeden front-endu privátní IP adresy pro jediný zdroj interní nástroj pro vyrovnávání zatížení.
- Konfigurace více pravidel, kde každé pravidlo obsahuje jeden Vyrovnávání zatížení jedinečný front-endovou IP adresa je vybrána.
- Vyberte **HA porty** možnost a nastavte **plovoucí IP adresa** k **povoleno** pro všechna pravidla Vyrovnávání zatížení.

## <a name="internal-load-balancer-with-ha-ports--public-load-balancer-on-the-same-backend-instances"></a>Interní Vyrovnávání zatížení s porty HA & veřejné Vyrovnávání zatížení v rámci stejné instancí back-end

Můžete nakonfigurovat **jeden** prostředek veřejné nástroj pro vyrovnávání zatížení pro back-endové prostředky společně s jeden interní standardní Vyrovnávání zatížení s vysokou DOSTUPNOSTÍ porty.

>[!NOTE]
>Tato funkce je dostupná prostřednictvím šablon Azure Resource Manageru dnes, ale nikoli prostřednictvím portálu Azure.

## <a name="limitations"></a>Omezení

- HA porty konfigurace je k dispozici pouze pro interní nástroj pro vyrovnávání zatížení, není k dispozici pro veřejné služby Vyrovnávání zatížení.

- Kombinace HA porty pravidla služby Vyrovnávání zatížení a jiných - HA porty pravidla služby Vyrovnávání zatížení nepodporuje.

- Funkci HA porty není k dispozici pro protokol IPv6.

- Tok symetrie pro scénáře hodnocení chyb zabezpečení sítě je podporováno pouze jednu síťovou kartu. Zobrazit popis a diagram pro [síťových virtuálních zařízení](#nva). Ale pokud cílový NAT může fungovat pro váš scénář, může používáte, a ujistěte se, že interní nástroj pro vyrovnávání zatížení, odešle návratový provoz stejné hodnocení chyb zabezpečení.


## <a name="next-steps"></a>Další postup

- [Konfigurace portů HA na interní standardní Vyrovnávání zatížení](load-balancer-configure-ha-ports.md)
- [Další informace o vyrovnávání zatížení standardní](load-balancer-standard-overview.md)
