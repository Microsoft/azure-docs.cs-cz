---
title: Co je překlad síťových překladů do virtuální sítě Azure?
titlesuffix: Azure Virtual Network
description: Přehled funkcí virtuální sítě na překladu sítí, prostředků, architektury a implementace. Zjistěte, jak virtuální síť NAT funguje a jak používat prostředky brány NAT v cloudu.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to learn more about Virtual Network NAT, its NAT gateway resources, and what I can use them for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2020
ms.author: allensu
ms.openlocfilehash: 4b34d4208d8686cdac3f8164d2cf7efb2d881346
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79409894"
---
# <a name="what-is-virtual-network-nat"></a>Co je překlad virtuální sítě k překladu?

Virtuální síť NAT (překlad síťových adres) zjednodušuje pouze odchozí připojení k Internetu pro virtuální sítě. Při konfiguraci v podsíti používá všechna odchozí připojení zadané statické veřejné IP adresy.  Odchozí připojení je možné bez nástroje pro vyrovnávání zatížení nebo veřejných IP adres přímo připojených k virtuálním počítačům. NAT je plně řízena a vysoce odolná.

<!-- 
<img src="./media/nat-overview/flow-map.svg" width="270" align="center">
![Virtual Network NAT](./media/nat-overview/flow-map.svg)
-->

<p align="center">
  <img src="./media/nat-overview/flow-map.svg" width="256" title="Virtuální síť NAT">
</p>



*Obrázek: Virtuální síť NAT*

## <a name="static-ip-addresses-for-outbound-only"></a>Statické ADRESY IP pro pouze odchozí

Odchozí připojení lze definovat pro každou podsíť s NAT.  Více podsítí v rámci stejné virtuální sítě může mít různé NAT. Podsíť je konfigurována zadáním [prostředku brány NAT,](./nat-gateway-resource.md) který má být používán. Všechny odchozí toky UDP a TCP z libovolné instance virtuálního počítače budou používat NAT. 

Nat je kompatibilní se standardními [prostředky veřejné IP adresy](./virtual-network-ip-addresses-overview-arm.md#standard) sku nebo [veřejnými prostředky předpony IP](./public-ip-address-prefix.md) nebo kombinací obou.  Veřejnou předponu IP můžete použít přímo nebo distribuovat veřejné IP adresy předpony mezi více prostředků brány NAT. NAT bude groom veškerý provoz na rozsah IP adres předpony.  Jakýkoli seznam ip povolených vašich nasazení je nyní snadný.

Veškerý odchozí provoz pro podsíť je zpracován nat automaticky bez konfigurace zákazníka.  Uživatelem definované trasy nejsou nutné. Nat má přednost před [ostatními odchozími scénáři](../load-balancer/load-balancer-outbound-connections.md) a nahrazuje výchozí cíl Sítě pro Internet.

## <a name="on-demand-snat-with-multiple-ip-addresses-for-scale"></a>SNAT na vyžádání s více IP adresami pro škálování

Nat používá "překlad adres sítě portů" (PNAT nebo PAT) a je doporučeno pro většinu úloh. Dynamické nebo rozdílné úlohy lze snadno přizpůsobit s přidělením odchozího toku na vyžádání. Rozsáhlé předběžné plánování, předběžné přidělení a nakonec nadměrné zřizování odchozích prostředků je zabráněno. Prostředky portu SNAT jsou sdílené a dostupné ve všech podsítích pomocí konkrétního prostředku brány NAT a jsou k dispozici v případě potřeby.

Veřejná IP adresa připojená k nat poskytuje až 64 000 souběžných toků pro UDP a TCP. Můžete začít s jednou IP adresou a škálovat až na 16 veřejných IP adres.

Nat umožňuje toky, které mají být vytvořeny z virtuální sítě do Internetu. Návratový provoz z Internetu je povolen pouze v reakci na aktivní tok.

Na rozdíl od nástroje pro vyrovnávání zatížení odchozí SNAT, NAT nemá žádná omezení, na které privátní IP instance virtuálního počítače může provádět odchozí připojení.  Sekundární konfigurace IP adres mohou vytvářet odchozí připojení k Internetu pomocí nástroje NAT.

## <a name="coexistence-of-inbound-and-outbound"></a>Koexistence příchozích a odchozích

Nat je kompatibilní s následujícími standardními prostředky skladové položky:

- [Vyrovnávání zatížení](../load-balancer/load-balancer-overview.md)
- [Veřejná IP adresa](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- [Předpona veřejné IP adresy](../virtual-network/public-ip-address-prefix.md)

Při použití společně s nat, tyto prostředky poskytují příchozí připojení k Internetu do podsítě. Nat poskytuje veškeré odchozí připojení k Internetu z podsítí.

Funkce NAT a kompatibilní standardní skladové položky jsou si vědomy směru, kterým byl tok spuštěn. Příchozí a odchozí scénáře mohou existovat společně. Tyto scénáře obdrží správné překlady síťových adres, protože tyto funkce jsou si vědomy směru toku. 

<!-- 
<img src="./media/nat-overview/flow-direction4.svg" width="500" align="center">
![Virtual Network NAT flow direction](./media/nat-overview/flow-direction4.svg)
-->
<p align="center">
  <img src="./media/nat-overview/flow-direction4.svg" width="512" title="Směr toku virtuální sítě na navádění">
</p>

*Obrázek: Směr toku virtuální sítě na navádění*

## <a name="fully-managed-highly-resilient"></a>Plně řízená, vysoce odolná

NAT je plně škálován od začátku. Není nutná žádná operace navádění nebo škálování.  Azure spravuje provoz NAT za vás.  Nat má vždy více domén selhání a může vydržet více selhání bez výpadku služby.

## <a name="tcp-reset-for-unrecognized-flows"></a>TCP Reset pro nerozpoznané toky

Soukromá strana nat odesílá pakety TCP Reset pro pokusy o komunikaci na připojení TCP, který neexistuje. Jedním z příkladů je připojení, které dosáhly časového limitu nečinnosti. Další přijatý paket vrátí protokol TCP Reset na privátní adresu IP, která signalizuje a vynucuje ukončení připojení.

Veřejná strana nat negeneruje pakety TCP Reset ani žádný jiný přenos.  Je emitován pouze provoz vytvořený virtuální sítí zákazníka.

## <a name="configurable-tcp-idle-timeout"></a>Konfigurovatelný časový limit nečinnosti protokolu TCP

Použije se výchozí časový limit nečinnosti protokolu TCP na 4 minuty, který lze zvýšit až na 120 minut. Jakákoli aktivita na toku můžete také obnovit časovač nečinnosti, včetně tcp keepalives.

## <a name="regional-or-zone-isolation-with-availability-zones"></a>Regionální nebo zónová izolace se zónami dostupnosti

Nat je ve výchozím nastavení regionální. Při vytváření [scénářů zón dostupnosti](../availability-zones/az-overview.md) lze nat být izolován v určité zóně (zónové nasazení).

<!-- 
<img src="./media/nat-overview/az-directions.svg" width="500" align="center">
![Virtual Network NAT with availability zones](./media/nat-overview/az-directions.svg)
-->

<p align="center">
  <img src="./media/nat-overview/az-directions.svg" width="512" title="Virtuální síť NAT s zónami dostupnosti">
</p>

*Obrázek: Překlad síťových sítí s zónami dostupnosti*

## <a name="multi-dimensional-metrics-for-observability"></a>Vícerozměrné metriky pro pozorovatelnost

Můžete sledovat provoz vašeho NAT prostřednictvím vícerozměrné metriky vystavené v Azure Monitoru. Tyto metriky lze použít ke sledování využití a řešení potíží.  Prostředky brány NAT zveřejňují následující metriky:
- Bajty
- Pakety
- Vynecháné pakety
- Celkový počet připojení SNAT
- Přechody stavu připojení SNAT za interval.

<!-- "ADD when PM is done" Learn more about [NAT gateway metrics](./nat-gateway-metrics.md) -->

## <a name="sla"></a>SLA

Při obecné dostupnosti je k dispozici cesta k datům NAT alespoň z 99,9 %.


## <a name="pricing"></a>Ceny

Brána NAT se účtuje dvěma samostatnými metry:

| Měřič | Sazba |
| --- | --- |
| Hodiny zdroje | $0.045/hod. |
| Zpracovávaná data | $0.045/CZ |

Účty hodin zdrojů pro dobu, po kterou prostředek brány NAT existuje.
Data zpracované účty pro veškerý provoz zpracovaný zdrojem brány NAT.

## <a name="availability"></a>Dostupnost

Virtuální síť NAT a prostředek brány NAT jsou dostupné ve všech [oblastech](https://azure.microsoft.com/global-infrastructure/regions/)veřejného cloudu Azure .

## <a name="support"></a>Podpora

NAT je podporován prostřednictvím běžných kanálů podpory.

## <a name="feedback"></a>Váš názor

Chceme vědět, jak můžeme zlepšit službu. Navrhnout a hlasovat o tom, co bychom měli stavět dál na [UserVoice pro NAT](https://aka.ms/natuservoice).


## <a name="limitations"></a>Omezení

* Nat je kompatibilní se standardními veřejnými IP adresami Skladových disponií, veřejnou předponou IP a prostředky pro vyrovnávání zatížení. Základní prostředky, jako je například základní vyvyčažitetetele zatížení a všechny produkty z nich odvozené nejsou kompatibilní s NAT.  Základní prostředky musí být umístěny v podsíti, která není nakonfigurována pomocí nasíťového nat.
* Rodina adres IPv4 je podporována.  NAT nespolupracuje s rodinou adres IPv6.  Nat nelze nasadit v podsíti s předponou IPv6.
* Protokolování toku nsg není podporováno při použití NAT.
* Nat nemůže protápit více virtuálních sítí.

## <a name="next-steps"></a>Další kroky

* Informace o [prostředku brány NAT](./nat-gateway-resource.md).
* [Řekněte nám, co se má stavět pro virtuální síť NAT v UserVoice](https://aka.ms/natuservoice).

