---
title: Nástroj pro vyrovnávání zatížení mezi oblastmi (Preview)
titleSuffix: Azure Load Balancer
description: Přehled vrstvy nástroje pro vyrovnávání zatížení mezi oblastmi pro Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: allensu
ms.custom: references_regions
ms.openlocfilehash: 89bf920a5a5dd833425f1b41bd206beaae9d30fd
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946258"
---
# <a name="cross-region-load-balancer-preview"></a>Nástroj pro vyrovnávání zatížení mezi oblastmi (Preview)

Azure Load Balancer distribuuje příchozí provoz, který dorazí na instance fondu back-end pro vyrovnávání zatížení.

Azure Standard Load Balancer podporuje vyrovnávání zatížení mezi oblastmi, což umožňuje použití geograficky redundantních scénářů HA, jako například:

* Příchozí provoz pocházející z více oblastí.
* [Okamžité globální převzetí služeb při selhání](#regional-redundancy) pro další optimální regionální nasazení.
* Načtěte distribuci mezi oblastmi do nejbližší oblasti Azure s [extrémně latencí](#ultra-low-latency).
* Možnost [horizontálního navýšení nebo snížení kapacity](#ability-to-scale-updown-behind-a-single-endpoint) za jeden koncový bod.
* [Statická IP adresa](#static-ip)
* [Zachování IP adresy klienta](#client-ip-preservation)
* [Sestavit v existujícím řešení vyrovnávání zatížení](#build-cross-region-solution-on-existing-azure-load-balancer) bez výukové křivky

> [!IMPORTANT]
> Nástroj pro vyrovnávání zatížení mezi oblastmi je momentálně ve verzi Preview a je možné ho nasadit na portálu. Přihlaste se, abyste **https://preview.portal.azure.com** mohli tuto funkci Zobrazit a nasadit. </br> </br>
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vyrovnávání zatížení mezi oblastmi nabízí stejné výhody vysokého výkonu a nízké latence jako místní Nástroj pro vyrovnávání zatížení (Standard). 

Konfigurace IP adresy front-endu pro nástroj pro vyrovnávání zatížení napříč oblastmi je statická a inzerovaná napříč [většinou oblastí Azure](#participating-regions).

:::image type="content" source="./media/cross-region-overview/cross-region-load-balancer.png" alt-text="Diagram nástroje pro vyrovnávání zatížení mezi oblastmi" border="true":::

> [!NOTE]
> Port back-endu vašeho pravidla vyrovnávání zatížení v nástroji pro vyrovnávání zatížení mezi oblastmi se musí shodovat s portem front-endu pravidla vyrovnávání zatížení nebo příchozího překladu adres (NAT) na regionálním standardním nástroji 

### <a name="regional-redundancy"></a>Regionální redundance

Nakonfigurujte regionální redundanci přidáním globální veřejné IP adresy front-endu ke stávajícím nástrojům pro vyrovnávání zatížení. 

Pokud dojde k výpadku jedné oblasti, provoz se směruje na další nejbližší zdravý Nástroj pro vyrovnávání zatížení.  

Sonda stavu nástroje pro vyrovnávání zatížení mezi oblastmi shromažďuje informace o dostupnosti každých 20 sekund. Pokud jeden místní Nástroj pro vyrovnávání zatížení pozastaví svou dostupnost na 0, nástroj pro vyrovnávání zatížení v různých oblastech tuto chybu detekuje. Místní Nástroj pro vyrovnávání zatížení se pak vybere mimo rotaci. 

:::image type="content" source="./media/cross-region-overview/global-region-view.png" alt-text="Diagram zobrazení provozu v globální oblasti" border="true":::

### <a name="ultra-low-latency"></a>Extrémně nízká latence

Algoritmus vyrovnávání zatížení geografické blízkosti je založený na geografickém umístění uživatelů a vašich regionálních nasazení. 

Provoz spuštěný z klienta se dostane do nejbližší oblasti v oblasti a prochází přes páteřní síť Microsoft s globální sítí, aby dosáhl nejbližšího regionálního nasazení. 

V oblastech Azure máte například nástroj pro vyrovnávání zatížení v různých oblastech se standardními nástroji pro vyrovnávání zatížení:

* USA – západ
* Severní Evropa

Pokud se tok spustí z Seattlu, provoz vstoupí do Západní USA. Tato oblast je nejbližší oblastí účasti z Seattlu. Provoz se směruje do nejbližší oblasti nástroje pro vyrovnávání zatížení, která je Západní USA.

Nástroj pro vyrovnávání zatížení mezi oblastmi Azure používá algoritmus vyrovnávání zatížení geografické blízkosti pro rozhodování o směrování. 

Nakonfigurovaný režim distribuce zatížení regionálních nástrojů pro vyrovnávání zatížení se používá pro rozhodování o konečném rozhodnutí o směrování, když se pro geografickou blízkosti používá několik regionálních nástrojů pro vyrovnávání zatížení.

Další informace najdete v tématu [Konfigurace distribučního režimu pro Azure Load Balancer](./load-balancer-distribution-mode.md).


### <a name="ability-to-scale-updown-behind-a-single-endpoint"></a>Možnost horizontálního navýšení nebo snížení kapacity za jeden koncový bod

Když zveřejňujete globální koncový bod nástroje pro vyrovnávání zatížení mezi oblastmi zákazníkům, můžete přidat nebo odebrat regionální nasazení za globálním koncovým bodem bez dopadu na zákazníky. 

<!---To learn about how to add or remove a regional deployment from the backend, read more [here](TODO: Insert CLI doc here).--->

### <a name="static-ip"></a>Statická IP adresa
Nástroj pro vyrovnávání zatížení mezi oblastmi se dodává se statickou veřejnou IP adresou, která zajišťuje, že IP adresa zůstane stejná. Další informace o statické IP adrese najdete [tady](../virtual-network/public-ip-addresses.md#allocation-method) .

### <a name="client-ip-preservation"></a>Zachování IP adresy klienta
Nástroj pro vyrovnávání zatížení mezi oblastmi je průchozí síťový nástroj pro vyrovnávání zatížení vrstvy 4. Tento průchozí postup zachovává původní IP adresu paketu.  Původní IP adresa je k dispozici pro kód, který běží na virtuálním počítači. Toto uchování vám umožní použít logiku, která je specifická pro IP adresu.

## <a name="build-cross-region-solution-on-existing-azure-load-balancer"></a>Sestavení řešení pro různé oblasti v existující Azure Load Balancer
Back-end fond nástroje pro vyrovnávání zatížení mezi oblastmi obsahuje jednu nebo více regionálních nástrojů pro vyrovnávání zatížení. 

Přidejte stávající nasazení nástroje pro vyrovnávání zatížení do nástroje pro vyrovnávání zatížení mezi oblastmi pro vysoce dostupné nasazení v různých oblastech.

**Domovská oblast** je místo, kde je nasazený nástroj pro vyrovnávání zatížení mezi oblastmi. Tato oblast nemá vliv na to, jak se bude směrovat provoz. Pokud dojde k výpadku domovské oblasti, nebude to mít vliv na tok provozu.

### <a name="home-regions"></a>Domovské oblasti
* USA – východ 2
* USA – západ
* West Europe
* Southeast Asia
* USA – střed
* Severní Evropa
* Východní Asie

> [!NOTE]
> Službu pro vyrovnávání zatížení mezi oblastmi můžete nasadit jenom v jedné z osmi výše uvedených oblastí.

**Zúčastněnou oblastí** je místo, kde je k dispozici globální veřejná IP adresa nástroje pro vyrovnávání zatížení. 

Provoz, který spustí uživatel, bude v rámci sítě Microsoft Core Network cestovat do nejbližší oblasti účastnící se. 

Nástroj pro vyrovnávání zatížení mezi oblastmi směruje provoz na příslušný místní Nástroj pro vyrovnávání zatížení.

### <a name="participating-regions"></a>Zúčastněné oblasti
* East US 
* West Europe 
* Střední USA 
* USA – východ 2 
* USA – západ 
* Severní Evropa 
* Středojižní USA 
* Západní USA 2 
* Spojené království – jih 
* Southeast Asia 
* USA – středosever 
* Japonsko – východ 
* Východní Asie 
* USA – středozápad 
* Austrálie – jihovýchod 
* Austrálie – východ 
* Indie – střed 

## <a name="limitations"></a>Omezení

* Konfigurace protokolu IP front-endu jsou pouze veřejné. Interní front-end není v současné době podporován.

* Soukromý nebo interní nástroj pro vyrovnávání zatížení se nedá přidat do back-endového fondu pro meziregionální Nástroj pro vyrovnávání zatížení. 

* Konfigurace IP adresy front-endu protokolu IPv6 mezi oblastmi není podporována. 

* V tuto chvíli nejde nakonfigurovat sondu stavu. Výchozí sonda stavu automaticky shromažďuje informace o dostupnosti pro místní Nástroj pro vyrovnávání zatížení každých 20 sekund. 

* Službu Azure Kubernetes Service (AKS) aktuálně nelze integrovat s Load Balancer mezi oblastmi. Při nastavování Load Balancer pro různé oblasti před veřejným Load Balancer nasazeným pomocí AKS by se měla očekávat ztráta připojení.

## <a name="pricing-and-sla"></a>Ceny a smlouvy SLA
Nástroj pro vyrovnávání zatížení mezi oblastmi sdílí [smlouvu SLA](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/ ) standardního nástroje pro vyrovnávání zatížení.

 
## <a name="next-steps"></a>Další kroky

- Viz [kurz: Vytvoření nástroje pro vyrovnávání zatížení mezi oblastmi pomocí Azure Portal](tutorial-cross-region-portal.md) k vytvoření nástroje pro vyrovnávání zatížení mezi oblastmi.
- Pokud chcete vytvořit standardní oblastní Nástroj pro vyrovnávání zatížení, přečtěte si téma [vytvoření veřejného nástroje pro vyrovnávání zatížení](quickstart-load-balancer-standard-public-portal.md) .
- Přečtěte si další informace o [Azure Load Balancer](load-balancer-overview.md).
