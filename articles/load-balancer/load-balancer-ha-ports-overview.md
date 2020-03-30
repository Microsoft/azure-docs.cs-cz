---
title: Přehled portů s vysokou dostupností v Azure
titleSuffix: Azure Load Balancer
description: Seznamte se s vysokou dostupností portů vyrovnávání zatížení na interním vyrovnávání zatížení.
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
ms.openlocfilehash: 5ada709350802344bfa65cce269735baa416edf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234453"
---
# <a name="high-availability-ports-overview"></a>Přehled portů s vysokou dostupností

Azure Standard Balancer pomáhá vyrovnávání zatížení TCP a UDP toky na všech portech současně, když používáte interní vyrovnávání zatížení. 

Pravidlo vyrovnávání zatížení portů s vysokou dostupností (HA) je variantou pravidla vyrovnávání zatížení, které je nakonfigurováno v interním standardním vyvažovači zatížení. Můžete zjednodušit použití vykladače zatížení tím, že poskytuje jediné pravidlo pro vyrovnávání zatížení všech toků TCP a UDP, které přicházejí na všechny porty interního standardního vyrovnávání zatížení. Rozhodnutí o vyrovnávání zatížení se provádí na tok. Tato akce je založena na následujícím připojení s pěti řazáky: zdrojová ADRESA IP, zdrojový port, cílová adresa IP, cílový port a protokol

Pravidla vyrovnávání zatížení portů HA vám pomohou s kritickými scénáři, jako je vysoká dostupnost a škálování síťových virtuálních zařízení (NVA) ve virtuálních sítích. Funkce může také pomoci, když velký počet portů musí být vyrovnávání zatížení. 

Pravidla vyrovnávání zatížení portů HA jsou konfigurována, když nastavíte porty front-end a back-end na **0** a protokol na **Všechny**. Interní prostředek pro vyrovnávání zatížení pak vyvažuje všechny toky TCP a UDP bez ohledu na číslo portu.

## <a name="why-use-ha-ports"></a>Proč používat porty HA?

### <a name="network-virtual-appliances"></a><a name="nva"></a>Síťová virtuální zařízení

Pomocí virtuálních zařízení můžete zabezpečit úlohy Azure před různými typy bezpečnostních hrozeb. Při použití sítě pro připojení k síti v těchto scénářích musí být spolehlivé a vysoce dostupné a musí škálovat pro poptávku.

Těchto cílů můžete dosáhnout jednoduše přidáním instancí NVA do back-endového fondu interního systému vyrovnávání zatížení a konfigurací pravidla vyrovnávání zatížení portů HA.

Pro scénáře NVA HA nabízejí porty HA následující výhody:
- Poskytněte rychlé převzetí služeb při selhání zdravým instancím s prominci stavu pro instancí
- Zajistěte vyšší výkon s horizontálním navýšením kapacity na *n*-active instancí
- Poskytují *n*-aktivní a aktivní pasivní scénáře
- Eliminujte potřebu složitých řešení, jako jsou uzly Apache ZooKeeper pro monitorování zařízení

Následující diagram představuje nasazení virtuální sítě hub-and-spoke. Paprsky síla tunelovat jejich provoz do centrální virtuální sítě a přes NVA, před opuštěním důvěryhodného prostoru. Virtuální virtuální počítače jsou za interním standardním nástrojem pro vyrovnávání zatížení s konfigurací portů HA. Veškerý provoz lze odpovídajícím způsobem zpracovat a předat dál. Při konfiguraci jako zobrazit v následujícím diagramu, ha porty vyrovnávání zatížení pravidlo navíc poskytuje symetrii toku pro příchozí a odchozí přenosy.

<a node="diagram"></a>
![Diagram virtuální sítě hub-and-spoke s síťovými virtuálními zařízeními nasazenými v režimu HA](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Pokud používáte virtuální virtuální virtuální tok, potvrďte u svých poskytovatelů, jak nejlépe používat porty HA a zjistěte, které scénáře jsou podporovány.

### <a name="load-balancing-large-numbers-of-ports"></a>Vyrovnávání zatížení velký počet portů

Porty HA můžete také použít pro aplikace, které vyžadují vyrovnávání zatížení velkého počtu portů. Tyto scénáře můžete zjednodušit pomocí interního [standardního vytápěče zatížení](load-balancer-standard-overview.md) s porty HA. Jediné pravidlo vyrovnávání zatížení nahrazuje více jednotlivých pravidel vyrovnávání zatížení, jedno pro každý port.

## <a name="region-availability"></a>Dostupnost v oblastech

Funkce portů HA je dostupná ve všech globálních oblastech Azure.

## <a name="supported-configurations"></a>Podporované konfigurace

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Konfigurace portů HA s jedním neplovoucím protokolem IP (non-Direct Server Return) na interním standardním nástroji pro vyrovnávání zatížení

Tato konfigurace je základní konfigurace portů HA. Pravidlo vyrovnávání zatížení portů HA můžete nakonfigurovat na jedné front-endové IP adrese následujícím způsobem:
1. Při konfiguraci standardního nástroje pro vyrovnávání zatížení zaškrtněte políčko **Porty HA** v konfiguraci pravidla nástroje pro vyrovnávání zatížení.
2. V **případě plovoucí IP**položky vyberte **zakázáno**.

Tato konfigurace neumožňuje žádnou jinou konfiguraci pravidla vyrovnávání zatížení na aktuální prostředek nástroje pro vyrovnávání zatížení. Také neumožňuje žádnou jinou konfiguraci prostředků nástroje pro interní nástroj pro vyrovnávání zatížení pro danou sadu back-endových instancí.

Kromě tohoto pravidla portů HA však můžete nakonfigurovat veřejný standardní systém vyrovnávání zatížení pro back-endové instance.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Konfigurace portů HA s jedním plovoucím protokolem IP (Direct Server Return) na interním standardním nástroji pro vyrovnávání zatížení

Podobně můžete nakonfigurovat zařízení pro vyrovnávání zatížení tak, aby používalo pravidlo vyrovnávání zatížení s **portem HA** s jedním front-endem nastavením **plovoucí IP** adresy na **povoleno**. 

Pomocí této konfigurace můžete přidat další plovoucí pravidla vyrovnávání zatížení IP nebo veřejný nástroj pro vyrovnávání zatížení. Však nelze použít neplovoucí IP, HA porty vyrovnávání zatížení konfigurace nad tuto konfiguraci.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Více konfigurací portů HA na interním standardním nástroji pro vyrovnávání zatížení

Pokud váš scénář vyžaduje, abyste nakonfigurovali více než jeden front-end portu HA pro stejný back-endový fond, můžete provést následující kroky: 
- Nakonfigurujte více než jednu privátní IP adresu front-endu pro jeden interní prostředek standardního vyrovnávání zatížení.
- Nakonfigurujte více pravidel vyrovnávání zatížení, kde každé pravidlo má jednu jedinečnou front-endovou IP adresu.
- Vyberte možnost **porty HA** a nastavte **plovoucí IP** na **Povoleno** pro všechna pravidla vyrovnávání zatížení.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Interní správce zatížení s porty HA a veřejným vyvyčovávačem zatížení ve stejné back-endové instanci

Můžete nakonfigurovat *jeden* veřejný prostředek standardního vyrovnávání zatížení pro back-endové prostředky spolu s jedním interním standardním standardním vyvažovačem zatížení s porty HA.

>[!NOTE]
>Tato funkce je momentálně dostupná prostřednictvím šablon Azure Resource Manager, ale není dostupná přes portál Azure.

## <a name="limitations"></a>Omezení

- Pravidla vyrovnávání zatížení portů HA jsou k dispozici pouze pro interní standardní vytápěč zatížení.
- Kombinace pravidla vyrovnávání zatížení portů HA a pravidla vyrovnávání zatížení portů NEŽ HA není podporována.
- Existující fragmenty PROTOKOLU IP budou předány pravidly vyrovnávání zatížení portů HA do stejného cíle jako první paket.  Fragmentace protokolu IP paketu UDP nebo TCP není podporována.
- Symetrie toku (především pro scénáře nva) je podporována s back-endovou instancí a jednu nic (a jednu konfiguraci IP) pouze při použití, jak je znázorněno na obrázku výše a pomocí pravidel vyrovnávání zatížení portů HA. Není k dispozici v žádném jiném scénáři. To znamená, že dva nebo více prostředků vyrovnávání zatížení a jejich příslušná pravidla činí nezávislá rozhodnutí a nikdy nejsou koordinována. Viz popis a diagram [síťových virtuálních zařízení](#nva). Pokud používáte více nic nebo sendvičování síťového virtuálního počítače mezi veřejné a interní vyrovnávání zatížení, symetrie toku není k dispozici.  Je možné, že tento problém obejdete podle zdroje NAT'ing toku příchozího přenosu dat do IP adresy zařízení, aby odpovědi dorazí na stejné síťové virtuální zařízení.  Důrazně však doporučujeme použít jednu nic a pomocí referenční architektury uvedené v diagramu výše.


## <a name="next-steps"></a>Další kroky

- [Konfigurace portů HA v interním standardním systému vyrovnávání zatížení](load-balancer-configure-ha-ports.md)
- [Další informace o standardním balanceru](load-balancer-standard-overview.md)
