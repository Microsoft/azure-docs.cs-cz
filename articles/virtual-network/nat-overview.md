---
title: Co je Azure Virtual Network NAT?
titlesuffix: Azure Virtual Network
description: Přehled Virtual Network funkcí NAT, prostředků, architektury a implementace. Přečtěte si, jak Virtual Network NAT funguje a jak používat prostředky brány NAT v cloudu.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to learn more about Virtual Network NAT, its NAT gateway resources, and what I can use them for.
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: allensu
ms.openlocfilehash: 75a2bb187b2ed7a234e99d8cd293cb30148bcb1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91667193"
---
# <a name="what-is-virtual-network-nat"></a>Co je Virtual Network NAT?

Virtual Network NAT (překlad síťových adres) zjednodušuje připojení k Internetu pouze pro virtuální sítě. Při konfiguraci v podsíti všechna odchozí připojení používá vaše zadané statické veřejné IP adresy.  Odchozí připojení je možné bez nástroje pro vyrovnávání zatížení nebo veřejných IP adres, které jsou přímo připojené k virtuálním počítačům. Překlad adres (NAT) je plně spravovaný a vysoce odolný.

<!-- 
<img src="./media/nat-overview/flow-map.svg" width="270" align="center">
![Virtual Network NAT](./media/nat-overview/flow-map.svg)
-->

<p align="center">
  <img src="./media/nat-overview/flow-map.svg" alt="Figure depicts a NAT receiving traffic from internal subnets and directing it to a public IP (PIP) and an IP prefix." width="256" title="Virtual Network NAT">
</p>



*Obrázek: Virtual Network NAT*

## <a name="static-ip-addresses-for-outbound-only"></a>Statické IP adresy pouze pro odchozí

Odchozí připojení je možné definovat pro každou podsíť pomocí překladu adres (NAT).  Několik podsítí v rámci jedné virtuální sítě může mít různé NAT. Podsíť je nakonfigurovaná tak, že určíte, který prostředek brány NAT se má použít. Všechny odchozí toky UDP a TCP z jakékoli instance virtuálního počítače budou používat překlad adres (NAT). 

Překlad adres (NAT) je kompatibilní s prostředky veřejné IP adresy standardního SKU nebo prostředky předpony veřejných IP adres nebo kombinací obou.  Pomocí předpony veřejných IP adres můžete přímo nebo distribuovat veřejné IP adresy předpony napříč více prostředky brány NAT. Překlad adres (NAT) odstraní veškerý provoz do rozsahu IP adres předpony.  Jakékoli filtrování IP vašich nasazení je teď snadné.

Veškerý odchozí provoz pro podsíť se zpracovává automaticky pomocí překladu adres (NAT) bez jakýchkoli konfigurací zákazníka.  Trasy definované uživatelem nejsou nutné. Překlad adres (NAT) má přednost před jinými odchozími scénáři a nahrazuje výchozí internetový cíl podsítě.

## <a name="on-demand-snat-with-multiple-ip-addresses-for-scale"></a>SNAT na vyžádání s více IP adresami pro škálování

NAT používá "překlad síťových adres portu" (PNAT nebo PAT) a doporučuje se pro většinu úloh. Dynamické nebo odchylované úlohy je možné snadno přizpůsobit pomocí odchozího toku na vyžádání. Vyhněte se rozsáhlému plánování, předběžnému přidělení a konečnému nadměrnému zajišťování odchozích prostředků. Prostředky portu SNAT jsou sdílené a dostupné ve všech podsítích pomocí konkrétního prostředku brány NAT a v případě potřeby jsou k dispozici.

Veřejná IP adresa připojená k překladu adres (NAT) poskytuje až 64 000 souběžných toků pro UDP a TCP. Můžete začít s jednou IP adresou a škálovat až 16 IP adres pomocí veřejných IP adres nebo předpon veřejných IP adres nebo obojího.  Prostředek brány NAT bude používat všechny IP adresy přidružené k prostředku pro odchozí připojení ze všech podsítí nakonfigurovaných se stejným prostředkem brány NAT.

Překlad adres (NAT) umožňuje vytvářet toky z virtuální sítě na Internet. Návratový provoz z Internetu je povolený jenom v reakci na aktivní tok.

Na rozdíl od odchozí SNAT nástroje pro vyrovnávání zatížení nemá překlad adres (NAT) žádná omezení, která privátní IP instance virtuálního počítače může vytvořit odchozí připojení.  Primární a sekundární konfigurace IP adres můžou vytvářet odchozí připojení k Internetu pomocí překladu adres (NAT).

## <a name="coexistence-of-inbound-and-outbound"></a>Koexistence příchozích a odchozích

Překlad adres (NAT) je kompatibilní s následujícími zdroji SKU Standard:

- Nástroj pro vyrovnávání zatížení
- Veřejná IP adresa
- Předpona veřejné IP adresy

Při použití spolu s překladem adres (NAT) tyto prostředky poskytují příchozí připojení k internetu vašim podsítím. Překlad adres (NAT) poskytuje veškeré odchozí připojení k Internetu z vašich podsítí.

Funkce NAT a kompatibilní standardní SKU mají na paměti, kdy byl tok spuštěn. Scénáře příchozího a odchozího přenosu můžou existovat současně. Tyto scénáře dostanou správné překlady síťových adres, protože tyto funkce mají informace o směru toku. 

<!-- 
<img src="./media/nat-overview/flow-direction4.svg" width="500" align="center">
![Virtual Network NAT flow direction](./media/nat-overview/flow-direction4.svg)
-->
<p align="center">
  <img src="./media/nat-overview/flow-direction4.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP and a public load balancer." width="512" title="Virtual Network směr toku NAT">
</p>

*Obrázek: Virtual Network směr toku NAT*

## <a name="fully-managed-highly-resilient"></a>Plně spravovaná, vysoce odolná

Překlad adres (NAT) je plně škálovatelný od začátku. Není nutná žádná operace se škálováním na více instancí ani na více instancí.  Azure za vás spravuje operace překladu adres (NAT).  NAT má vždycky více domén selhání a může tolerovat více selhání bez výpadku služby.

## <a name="tcp-reset-for-unrecognized-flows"></a>Resetování protokolu TCP pro nerozpoznané toky

Soukromá strana služby NAT odesílá pakety pro resetování TCP pro pokusy o komunikaci s připojením TCP, které neexistuje. Jedním z příkladů je připojení, u kterých se dosáhlo časového limitu nečinnosti. Další přijaté pakety vrátí nastavení protokolu TCP na privátní IP adresu k signalizaci a vynucení ukončení připojení.

Veřejná strana překladu síťových adres (NAT) negeneruje pakety pro resetování TCP ani žádný jiný provoz.  Vygeneruje se jenom provoz vzniklý virtuální sítí zákazníka.

## <a name="configurable-tcp-idle-timeout"></a>Konfigurovatelný časový limit nečinnosti protokolu TCP

Použije se výchozí časový limit nečinnosti TCP 4 minuty a dá se zvýšit na až 120 minut. Časová prodleva v toku může také resetovat časovač nečinnosti, včetně udržení protokolu TCP.

## <a name="regional-or-zone-isolation-with-availability-zones"></a>Regionální nebo izolovaná izolace se zónami dostupnosti

Překlad adres (NAT) je ve výchozím nastavení regionální. Při vytváření scénářů [zón dostupnosti](../availability-zones/az-overview.md) se NAT může izolovat v konkrétní zóně (nasazení oblastí).

<!-- 
<img src="./media/nat-overview/az-directions.svg" width="500" align="center">
![Virtual Network NAT with availability zones](./media/nat-overview/az-directions.svg)
-->

<p align="center">
  <img src="./media/nat-overview/az-directions.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet." width="512" title="Virtual Network překlad adres (NAT) se zónami dostupnosti">
</p>

*Obrázek: Virtual Network překlad adres (NAT) se zónami dostupnosti*

## <a name="multi-dimensional-metrics-for-observability"></a>Multidimenzionální metriky pro pozorování

Provoz vašeho překladu adres (NAT) můžete monitorovat prostřednictvím multidimenzionální metriky, která je dostupná v Azure Monitor. Tyto metriky je možné využít ke sledování využití a k řešení problémů.  Prostředky brány NAT zpřístupňují následující metriky:
- Bajty
- Rámců
- Vyřazené pakety
- Celkový počet připojení SNAT
- Přechody stavu připojení SNAT podle intervalu

<!-- "ADD when PM is done" Learn more about [NAT gateway metrics](./nat-gateway-metrics.md) -->

## <a name="sla"></a>SLA

V obecné dostupnosti je k dispozici alespoň 99,9% cesta k datům NAT.

## <a name="pricing"></a>Ceny

Podrobnosti o cenách najdete v tématu [Virtual Network ceny](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="availability"></a>Dostupnost

Virtual Network překlad adres (NAT) a prostředek brány NAT jsou k dispozici ve všech oblastech všech [oblastí](https://azure.microsoft.com/global-infrastructure/regions/)cloudu Azure.

## <a name="suggestions"></a>Návrhy

Chceme zjistit, jak můžeme službu vylepšit. Navrhněte a hlasujte, co by se mělo na webu [UserVoice pro překlad adres (NAT)](https://aka.ms/natuservoice)sestavit dál.

## <a name="limitations"></a>Omezení

* Překlad adres (NAT) je kompatibilní s veřejnou IP adresou SKU, předponou veřejné IP adresy a prostředky nástroje pro vyrovnávání zatížení. Základní prostředky, jako je základní nástroj pro vyrovnávání zatížení, a všechny produkty, které jsou z nich odvozené, nejsou kompatibilní se službou NAT.  Základní prostředky musí být umístěné v podsíti, která není nakonfigurovaná s překladem adres (NAT).
* Rodina adres IPv4 je podporovaná.  Překlad adres (NAT) nekomunikuje s řadou IPv6 adres.  Překlad adres (NAT) nejde nasadit v podsíti s předponou IPv6.
* Překlad adres (NAT) nemůže zahrnovat víc virtuálních sítí.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [prostředku brány NAT](./nat-gateway-resource.md).
* [Řekněte nám, co se má sestavit příště pro Virtual Network překlad adres (NAT) ve službě UserVoice](https://aka.ms/natuservoice).
