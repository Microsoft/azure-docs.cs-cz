---
title: Co je služba Azure NAT?
titlesuffix: Azure NAT
description: Přehled funkcí, architektury a implementace služby Azure NAT Přečtěte si, jak služba NAT funguje a jak ji používat v cloudu.
services: nat
documentationcenter: na
author: asudbring
ms.service: nat
Customer intent: As an IT administrator, I want to learn more about the Azure NAT service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/21/2019
ms.author: allensu
ms.openlocfilehash: 04464716384c1700571096443963499b0ca1b261
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376501"
---
# <a name="what-is-azure-nat-service-public-preview"></a>Co je služba Azure NAT (Public Preview)

Službu Azure NAT můžete použít k poskytování odchozích připojení pro cíle veřejných IP adres pro všechny virtuální počítače ve vaší virtuální síti. Služba NAT je schopná pojmout různé druhy úloh tím, že poskytuje překlady na vyžádání bez nutnosti předplánování požadavků na instance.

Můžete definovat, které podsítě virtuální sítě by měly používat službu překladu adres (NAT). Odchozí připojení můžete škálovat s jednou nebo více IP adresami. A můžete vyladit časový limit nečinnosti od 4 do 120 minut. Služba NAT taky vždy pošle resetování TCP, když se znovu použijí neaktivní připojení, jako by to byl případ, kdy byl překročen časový limit nečinnosti. 

[Zóny dostupnosti](../availability-zones/az-overview.md) scénáře můžete vytvořit umístěním služby NAT do konkrétní zóny dostupnosti.

Služba NAT je spravovaná, odolná služba pro odchozí připojení. Službu NAT můžete kombinovat se standardními prostředky veřejné IP adresy a standardním nástrojem pro vyrovnávání zatížení a vytvořit další příchozí scénáře.

>[!NOTE] 
>Služba Azure NAT je v tuto chvíli dostupná jako verze Public Preview. V současné době je dostupná jenom v omezené sadě [oblastí](#regions). Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.comsupport/legal/preview-supplemental-terms).

## <a name="nat-gateways"></a>Brány NAT

Služba Azure NAT poskytuje prostředek brány NAT. Tento prostředek se dá nakonfigurovat s jednou nebo více veřejnými IP adresami, které používají [standardní veřejné IP adresy](../virtual-network/virtual-network-ip-addresses-overview-arm.md#standard) nebo [standardní prostředky PŘEDPONy veřejných IP](../virtual-network/public-ip-address-prefix.md) adres nebo obojí. Službu NAT použijete tak, že nakonfigurujete podsítě prostředku virtuální sítě tak, aby používaly konkrétní bránu NAT. Bránu NAT je možné použít na více podsítích stejné virtuální sítě. Různé virtuální sítě potřebují samostatné brány NAT.

Podsíť začne používat bránu NAT pro odchozí připojení, když je nakonfigurovaná pro podsíť virtuální sítě. Brána NAT nahrazuje výchozí internetový cíl podsítě a konfigurace UDR není potřeba. Odchozí překlady jsou k dispozici na vyžádání, protože je vytvoří virtuální počítač.

>[!IMPORTANT]
>Odchozí toky nebudou úspěšné, dokud pro bránu NAT nepřiřadíte také alespoň jednu veřejnou IP adresu.

>[!NOTE]
> Vyžadujete-li zaručené přidělení na virtuálním počítači nebo potřebujete členitost pro definování odchozího připojení na úrovni instance nebo fondů instancí nebo záruky portů pro překlad zdrojových síťových adres (SNAT) na určitou instanci, použijte možnost [Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) s [odchozími pravidly](../load-balancer/load-balancer-outbound-rules-overview.md) pro definování Load Balancer odchozího odchozího překladu adres na základě fondu (SNAT).   Tato flexibilita přináší výrazné zvýšení složitosti a dodatečné plánování, když jsou kombinovány různé úlohy, nebo odchozí připojení úloh nemůže být přesně roztříděno na jednotlivé instance.

## <a name="combination-of-inbound-and-outbound-scenarios-and-skus"></a>Kombinace scénářů příchozích a odchozích přenosů a SKU

Scénáře příchozího a odchozího připojení můžete vytvořit ve stejných podsítích s bránou NAT. kombinací brány NAT s standardní veřejnou IP adresou budou mít přístup na úrovni instance k virtuálnímu a standardnímu veřejnému koncovému bodu nástroje pro vyrovnávání zatížení, včetně všech služeb založených na nich. Brána NAT a standardní veřejná IP adresa a standardní veřejný Nástroj pro vyrovnávání zatížení mají na vědomí směr toku. U odchozích toků nahrazuje brána NAT všechny ostatní odchozí scénáře a má přednost před [Load Balancer odchozí připojení](../load-balancer/load-balancer-outbound-connections.md) a [veřejné IP adresy na úrovni instance na virtuálních počítačích](../load-balancer/load-balancer-outbound-connections.md) . U příchozích vycházejících toků se dá veřejné IP adresy na úrovni instance nebo veřejné koncové body pro nástroj pro vyrovnávání zatížení používat současně s bránou NAT.  Po dokončení tohoto scénáře budou příchozí nebo odchozí toky úspěšné ve stejných podsítích.  Výsledný scénář je "zabezpečený standardně" pro příchozí prvotní scénáře, protože je vyžadován NSG a musí explicitně povolit příchozí původní provoz.

Máte možnost rozdělit nasazení ve virtuální síti do podsítí pro ovlivnění odchozího chování. Veřejné IP adresy můžete také použít jako veřejné IP adresy na úrovni instance na virtuálních počítačích a v nástroji pro vyrovnávání zatížení, který poskytuje odchozí připojení zdrojové sítě (SNAT) pro odchozí připojení v jedné podsíti a bráně NAT v jiné podsíti. stejná virtuální síť. Brány NAT nemůžete kombinovat s prostředky pomocí základních prostředků veřejných IP adres nebo prostředků základního nástroje pro vyrovnávání zatížení ve stejné podsíti. Základní prostředky SKU můžete nasadit do jiné podsítě stejné virtuální sítě.

## <a name="network-address-translation"></a>Překlad síťových adres

Po dokončení konfigurace brána NAT poskytuje maskování adres zdrojového síťového překladu (SNAT) pro všechny aplikace TCP a UDP přeložením privátní IP adresy instance virtuálního počítače na veřejné IP adresy.

Toky z privátních IP adres se překládají na jednu nebo víc veřejných IP adres na základě vaší konfigurace. K překladu dojde, protože zdrojová adresa privátní IP adresy zdroje překladu adres (SNAT) se změní na veřejnou IP adresu. Maskování portů znamená, že zdrojový port je přepsán pro odlišení toků po překladu a zjednodušuje mapování mnoha na jeden/mnoho. Zdrojové číslo portu pro výsledný tok ve veřejném adresním prostoru IP adres se přiřadí na vyžádání, protože zdrojové virtuální počítače spustí toky. Více připojení ke stejné cílové veřejné IP adrese, protokolu IP a cílové kombinaci portů využívají další port na jeden tok.

Když virtuální počítače provedou odchozí připojení k těmto podsítím, zdrojové porty se předávají na vyžádání a vydávají se po dokončení toku nebo překročení časového limitu nečinnosti. Každá veřejná IP adresa poskytuje porty 55 000 SNAT, které se mají použít, a umožňuje horizontální navýšení kapacity pomocí několika veřejných IP adres. Brána NAT se pokusí znovu použít porty pro toky do různých umístění, aby se zvýšilo škálování odchozího připojení. 

Můžete snadno adresovat více úloh ve stejné podsíti.  Na rozdíl od odchozího připojení Azure Load Balancer nemusíte předem přidělit ani předpočítat pro nejhorší případ použití virtuálních počítačů na virtuálním počítači. Místo toho plánujete celkový objem odchozích připojení pro všechny virtuální počítače v nakonfigurovaných podsítích. Každý dostupný port je v případě potřeby sdílen pro všechny instance.

## <a name="timeout-and-tcp-resets"></a>Časový limit a resetování TCP

Brána NAT implementuje časový limit nečinnosti a pošle obnovení TCP (RST) pro toky, které neexistují.  Vypršení časových limitů se dá nakonfigurovat ze 4 minut (výchozí) na 120 minut. Do zdroje se vrátí paket TCP RST při doručení do brány NAT a neexistuje žádné vyhovující připojení. Když tok dosáhne časového limitu nečinnosti, bude tok odebraný z brány NAT a port bude k dispozici pro další vytvořený tok. Pokud například připojení TCP dosáhne časového limitu nečinnosti a další pakety se zobrazí pro již vypršení platnosti připojení, pošle se na zdroj TCP RST.  Vaše aplikace může k signalizaci a v případě potřeby používat udržení stavu koncového bodu pomocí protokolu TCP.

## <a name="availability-zones"></a>Zóny dostupnosti

Brány NAT můžete v případě potřeby umístit do konkrétní zóny dostupnosti.  

Veřejná IP adresa v oblasti Zona musí odpovídat stejné zóně dostupnosti jako brána NAT. Předpona veřejné IP adresy nepodporuje zóny dostupnosti a nedá se použít.

Když je nakonfigurovaná brána NAT NAT, rovina dat brány NAT se zarovná se a izoluje se požadovanou zónou dostupnosti.

Rovinu dat brány NAT můžete zarovnat s virtuálním počítačem v konkrétní zóně.  Toto zarovnání můžete provést tak, že vytvoříte místní podsíť, která bude obsahovat jenom virtuální počítače ve stejné zóně, jako je brána NAT konfigurovaná pro podsíť.

Umístění zóny dostupnosti brány NAT nejde změnit a bránu NAT nemůžete převést z regionu na oblast nebo oblast na oblast.

## <a name="outbound-connection-service-comparison"></a>Porovnání služby odchozího připojení

Brána NAT a Load Balancer odchozí připojení jsou určené pro různé scénáře. Klíčové kompromisy, které je potřeba vzít v úvahu, jsou jednoduchost, přidělení na vyžádání a škálování na úrovni virtuální sítě oproti škálám orientovaným na fondy a složitější scénáře.  K dispozici jsou také různé rozdíly v chování a požadavky na plánování.

Služba NAT je jednodušší způsob konfigurace odchozího připojení pro virtuální sítě namísto konfigurace založené na fondu pro [Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) pomocí [odchozích pravidel](../load-balancer/load-balancer-outbound-rules-overview.md).  Můžete snadno nakonfigurovat a škálovat odchozí připojení pro podsíť a dosáhnout scénářů, které nejsou dřív možné.

Služba NAT bude mít přednost před odchozí SNAT nástroje pro vyrovnávání zatížení, ale obě můžou být kombinované pro příchozí a odchozí scénář. Pokud kombinujete příchozí prvotní scénáře s veřejnými IP adresami a koncovými body nástroje pro vyrovnávání zatížení, můžete pro standardní veřejnou IP adresu a standardní nástroje pro vyrovnávání zatížení využít také možnost zabezpečení ve výchozím nastavení.  Až NSG existuje a povolí explicitní provoz, bude příchozí provoz povolený.

Služba NAT může vytvořit předvídatelné odchozí připojení pro vaši virtuální síť. Pokud potřebujete jenom odchozí připojení, nemusíte konfigurovat nástroj pro vyrovnávání zatížení s odchozími pravidly, připojte počítače ke fondu, vypočítejte a definujte alokaci portů SNAT nebo přiřaďte k jednotlivým počítačům veřejné IP adresy. Můžete delegovat zodpovědnost za odchozí připojení ke Správci prostředku brány NAT, nikoli správce nástroje pro vyrovnávání zatížení.

Služba NAT umožňuje naplánování odchozího připojení pro škálování úloh ve špičce virtuální sítě nebo podsítí v rámci, spíše než špičkové škálování instancí virtuálních počítačů. Další požadavky můžete snadněji přizpůsobit tím, že zvýšíte počet veřejných IP adres dynamicky na všechny podsítě celé virtuální sítě. Místo výpočtu a přeplánování přidělování portů SNAT služby pro vyrovnávání zatížení služba NAT sdílí porty SNAT, které jsou dostupné pro odchozí připojení, a poskytuje je na vyžádání, aby pokryly shluky nebo nesnadno předvídatelné úlohy.  Nástroj pro vyrovnávání zatížení předem přidělí určitý počet portů SNAT pro danou instanci virtuálního počítače.  

Služba NAT vždy vrátí pakety protokolu TCP reset (RST) odesílateli pro neexistující toky (například z důvodu vypršení časového limitu nečinnosti). Službu Load Balancer úrovně Standard je možné nakonfigurovat tak, aby povolovala [resetování protokolu TCP při nečinnosti](../load-balancer/load-balancer-tcp-reset.md), což vytvoří pakety TCP RST do obou koncových bodů připojení v době časového limitu nečinnosti.

## <a name="regions"></a>Oblasti

Služba NAT je dostupná v těchto oblastech.
- USA – východ 2
- Středozápadní USA

## <a name="limitations"></a>Omezení

- Služba NAT není kompatibilní se základními veřejnými IP adresami nebo základními nástroji pro vyrovnávání zatížení ve stejné podsíti.  Musí existovat v podsíti, která není obsluhována bránou NAT.
- Jsou podporovány protokoly aplikace založené na TCP a UDP.
- Brány NAT je možné nakonfigurovat v jedné nebo několika podsítích virtuální sítě.
- Protokol IPv6 se nepodporuje.
- Předpona veřejné IP adresy nepodporuje umístění na více oblastech a nedá se použít s bránou NAT pro oblast.

## <a name="next-steps"></a>Další kroky

- [Sdělte nám svůj názor na plán služby NAT](https://aka.ms/natuservoice) .
