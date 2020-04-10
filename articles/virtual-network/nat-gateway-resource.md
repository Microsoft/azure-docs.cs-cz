---
title: Navrhování virtuálních sítí pomocí prostředků brány NAT
titleSuffix: Azure Virtual Network NAT
description: Přečtěte si, jak navrhovat virtuální sítě pomocí prostředků brány NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to learn more about how to design virtual networks with NAT gateway resources.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2020
ms.author: allensu
ms.openlocfilehash: 4095b0b48e86b0aafcc86d74ca1fa25bacddf0ec
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011714"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources"></a>Navrhování virtuálních sítí pomocí prostředků brány NAT

Prostředky brány NAT jsou součástí [překladu síťových překladů a](nat-overview.md) poskytují odchozí připojení k Internetu pro jednu nebo více podsítí virtuální sítě. Podsíť virtuální sítě uvádí, které nat brána bude použita. Nat poskytuje překlad zdrojové síťové adresy (SNAT) pro podsíť.  Prostředky brány NAT určují, které statické IP adresy virtuální počítače používají při vytváření odchozích toků. Statické adresy IP pocházejí z veřejných prostředků IP adres, veřejných prostředků předpony IP nebo obojího. Prostředek brány NAT může použít až 16 statických IP adres z obou.


<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtuální síť NAT pro odchozí do Internetu">
</p>

*Obrázek: Virtuální síť NAT pro odchozí do Internetu*

## <a name="how-to-deploy-nat"></a>Jak nasadit NAT

Konfigurace a používání brány NAT je záměrně jednoduché:  

Prostředek brány NAT:
- Vytvořit regionální nebo zónový (zónově izolovaný) prostředek brány NAT,
- Přiřadit IP adresy,
- V případě potřeby upravte časový limit nečinnosti protokolu TCP (volitelné).  <ins>Před</ins> změnou výchozího nastavení zkontrolujte [časovače.](#timers)

Virtuální síť:
- Nakonfigurujte podsíť virtuální sítě tak, aby používala bránu NAT.

Uživatelem definované trasy nejsou nutné.

## <a name="resource"></a>Prostředek

Prostředek je navržen tak, aby byl jednoduchý, jak můžete vidět z následujícího příkladu Správce prostředků Azure ve formátu podobném šabloně.  Tento formát šablony je zde zobrazen pro ilustraci pojmů a struktury.  Upravte příklad podle svých potřeb.  Tento dokument není určen jako kurz.

Následující diagram znázorňuje zapisovatelné odkazy mezi různými prostředky Azure Resource Manager.  Šipka označuje směr odkazu, který pochází z místa, odkud je zapisovatelný. Revize 

<p align="center">
  <img src="media/nat-overview/flow-map.svg" width="256" title="Objektový model NAT virtuální sítě">
</p>

*Obrázek: Objektový model virtuální sítě NAT*

Nat se doporučuje pro většinu úloh, pokud nemáte konkrétní závislost na odchozí připojení nástroje [pro vyrovnávání zatížení založené](../load-balancer/load-balancer-outbound-connections.md)na fondu .  

Můžete migrovat ze standardních scénářů nástrojů pro vyrovnávání zatížení, včetně [odchozích pravidel](../load-balancer/load-balancer-outbound-rules-overview.md), do brány NAT. Chcete-li migrovat, přesuňte veřejné ip a veřejné ip prefix ové prostředky z frontendů vykladače vyrovnávání zatížení do brány NAT. Nové IP adresy pro bránu NAT nejsou povinné. Standardní veřejnou IP adresu a předponu lze znovu použít, pokud celkový počet nepřesáhne 16 IP adres. Plánujte migraci s ohledem na přerušení služby během přechodu.  Můžete minimalizovat přerušení automatizací procesu. Nejprve otestujte migraci v přípravném prostředí.  Během přechodu nejsou ovlivněny příchozí původní toky.

Následující příklad je úryvek ze šablony Azure Resource Manager.  Tato šablona nasazuje několik prostředků, včetně brány NAT.  Šablona má v tomto příkladu následující parametry:

- **natgatewayname** - Název brány NAT.
- **umístění** – oblast Azure, kde se nachází prostředek.
- **publicipname** - Název odchozí veřejné IP adresy přidružené k bráně NAT.
- **vnetname** - Název virtuální sítě.
- **podprogram -** Název podsítě přidružené k bráně NAT.

Celkový počet adres IP poskytnutých všemi prostředky IP adresy a předpony nesmí překročit celkem 16 IP adres. Libovolný počet IP adres mezi 1 a 16 je povolen.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="81-96":::

Po vytvoření prostředku brány NAT jej lze použít v jedné nebo více podsítích virtuální sítě. Určete, které podsítě používají tento prostředek brány NAT. Brána NAT není schopna překlíkem více než jednu virtuální síť. Není nutné přiřadit stejnou bránu NAT všem podsítím virtuální sítě. Jednotlivé podsítě lze konfigurovat s různými prostředky brány NAT.

Scénáře, které nepoužívají zóny dostupnosti, budou místní (není zadána žádná zóna). Pokud používáte zóny dostupnosti, můžete určit zónu pro izolujeTE NAT na určitou zónu. Redundance zóny není podporována. Projděte si [zóny dostupnosti NAT](#availability-zones).

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="1-146" highlight="81-96":::

Brány NAT jsou definovány s vlastností v podsíti v rámci virtuální sítě. Toky vytvořené virtuálními počítači v **podsíťovém názvu** virtuální **sítě** virtuální sítě budou používat bránu NAT. Všechna odchozí připojení budou používat IP adresy přidružené k **natgatewayname** jako zdrojovou IP adresu.

Další informace o šabloně Azure Resource Manager použité v tomto příkladu najdete v tématu:

- [Úvodní příručka: Vytvoření brány NAT – šablona Správce prostředků](quickstart-create-nat-gateway-template.md)
- [Virtuální síť NAT](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm/)

## <a name="design-guidance"></a>Pokyny k návrhu

V této části se seznamte s aspekty návrhu virtuálních sítí pomocí nat.  

1. [Optimalizace nákladů](#cost-optimization)
1. [Koexistence příchozích a odchozích](#coexistence-of-inbound-and-outbound)
2. [Správa základních zdrojů](#managing-basic-resources)
3. [Zóny dostupnosti](#availability-zones)

### <a name="cost-optimization"></a>Optimalizace nákladů

[Koncové body služby](virtual-network-service-endpoints-overview.md) a [soukromé propojení](../private-link/private-link-overview.md) jsou možnosti, které je třeba zvážit pro optimalizaci nákladů. Nat není potřeba pro tyto služby. Přenosy směrované do koncových bodů služby nebo soukromého propojení není zpracována virtuální sítě NAT.  

Koncové body služby propojují prostředky služeb Azure s vaší virtuální sítí a řídí přístup k prostředkům služeb Azure. Například při přístupu k úložišti Azure použijte koncový bod služby pro úložiště, abyste se vyhnuli poplatkům za zpracování dat NAT. Koncové body služby jsou zdarma.

Privátní odkaz zveřejňuje služby Azure PaaS (nebo jiné služby hostované s privátním odkazem) jako soukromý koncový bod uvnitř virtuální sítě.  Soukromé propojení se účtuje na základě doby trvání a zpracovávaných dat.

Vyhodnoťte, pokud jeden nebo oba tyto přístupy jsou vhodné pro váš scénář a použít podle potřeby.

### <a name="coexistence-of-inbound-and-outbound"></a>Koexistence příchozích a odchozích

Brána NAT je kompatibilní s:

 - Standardní odvykač zatížení
 - Standardní veřejná IP adresa
 - Standardní veřejná předpona IP

Při vývoji nového nasazení začněte se standardními skum.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtuální síť NAT pro odchozí do Internetu">
</p>

*Obrázek: Virtuální síť NAT pro odchozí do Internetu*

Scénář odchozího internetu poskytovaný bránou NAT lze rozšířit pomocí příchozích funkcí Internetu. Každý prostředek si je vědomsměru, ve kterém pochází tok. V podsíti s bránou NAT jsou všechny scénáře odchozího do Internetu nahrazeny bránou NAT. Příchozí z internetových scénářů jsou poskytovány příslušného prostředku.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>NAT a virtuální hod s veřejnou IP adresou na úrovni instance

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" width="300" title="Virtuální síť NAT a virtuální počítač s veřejnou IP adresou na úrovni instance">
</p>

*Obrázek: Virtuální síť NAT a virtuální počítač s veřejnou IP adresou na úrovni instance*

| Směr | Prostředek |
|:---:|:---:|
| Příchozí | Virtuální virtuální ms s veřejnou IP adresou na úrovni instance |
| Odchozí | NAT Gateway |

Virtuální počítač bude používat bránu NAT pro odchozí.  Příchozí pochází není ovlivněna.

#### <a name="nat-and-vm-with-public-load-balancer"></a>NAT a virtuální hod s veřejným balancerem zatížení

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" width="350" title="Virtuální síť NAT a virtuální počítač s veřejným nástrojem pro vyrovnávání zatížení">
</p>

*Obrázek: Virtuální síť NAT a virtuální počítač s veřejným nástrojem pro vyrovnávání zatížení*

| Směr | Prostředek |
|:---:|:---:|
| Příchozí | veřejný balancer |
| Odchozí | NAT Gateway |

Jakákoli odchozí konfigurace z pravidla vyrovnávání zatížení nebo odchozích pravidel je nahrazena bránou NAT.  Příchozí pochází není ovlivněna.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-public-load-balancer"></a>NAT a virtuální hod s veřejnou IP adresou na úrovni instance a veřejným vyvyčovávatelem zatížení

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" width="425" title="Virtuální síť NAT a virtuální počítač s veřejnou IP adresou na úrovni instance a veřejným nástrojem pro vyrovnávání zatížení">
</p>

*Obrázek: Virtuální síť NAT a virtuální počítač s veřejnou IP adresou na úrovni instance a veřejným nástrojem pro vyrovnávání zatížení*

| Směr | Prostředek |
|:---:|:---:|
| Příchozí | Virtuální ms s veřejnou IP adresou na úrovni instance a veřejným vyvažovačem zatížení |
| Odchozí | NAT Gateway |

Jakákoli odchozí konfigurace z pravidla vyrovnávání zatížení nebo odchozích pravidel je nahrazena bránou NAT.  Virtuální počítač bude také používat bránu NAT pro odchozí.  Příchozí pochází není ovlivněna.

### <a name="managing-basic-resources"></a>Správa základních zdrojů

Standardní vyrovnávání zatížení, veřejná IP adresa a veřejná předpona IP jsou kompatibilní s bránou NAT. Brány NAT pracují v rozsahu podsítě. Základní skladová položka těchto služeb musí být nasazena v podsíti bez brány NAT. Toto oddělení umožňuje obě varianty skladové položky koexistovat ve stejné virtuální síti.

Brány NAT mají přednost před odchozími scénáři podsítě. Základní vyrovnávání zatížení nebo veřejnou IP adresu (a všechny spravované služby vytvořené s nimi) nelze upravit pomocí správných překladů. Brána NAT přebírá kontrolu nad odchozím do internetového provozu v podsíti. Příchozí provoz na základní vyrovnávání zatížení a veřejnou IP adresu není k dispozici. Příchozí provoz na základní vyrovnávání zatížení a nebo veřejná IP nakonfigurovaná na virtuálním počítači nebude k dispozici.

### <a name="availability-zones"></a>Zóny dostupnosti

#### <a name="zone-isolation-with-zonal-stacks"></a>Zónová izolace se zónovými zásobníky

<p align="center">
  <img src="media/nat-overview/az-directions.svg" width="425" title="Virtuální síť NAT s izolací zóny, vytvoření více "zonal stacks"">
</p>

*Obrázek: Virtuální síť NAT s izolací zóny, vytvoření více "zónonální zásobníky"*

I bez zón dostupnosti je nat odolný a může přežít selhání více součástí infrastruktury.  Zóny dostupnosti se na této odolnosti vytvářejí pomocí scénářů izolace zóny pro nat.

Virtuální sítě a jejich podsítě jsou místní konstrukce.  Podsítě nejsou omezeny na zónu.

Zónové příslib pro izolaci zóny existuje, když je instance virtuálního počítače používající prostředek brány NAT ve stejné zóně jako prostředek brány NAT a jeho veřejné IP adresy. Vzor, který chcete použít pro izolaci zóny, vytváří "zónový zásobník" pro zónu dostupnosti.  Tento zásobník zón se skládá z instancí virtuálních počítačů, prostředků brány NAT, veřejných IP adres nebo prostředků předpony v podsíti, o které se předpokládá, že obsluhuje pouze stejnou zónu.   Operace roviny řízení a rovina dat jsou pak zarovnány a omezeny na zadanou zónu. 

Očekává se, že selhání v jiné zóně, než kde existuje váš scénář, nebude mít dopad na nat. Odchozí provoz z virtuálních počítačů ve stejné zóně se nezdaří z důvodu izolace zóny.  

#### <a name="integrating-inbound-endpoints"></a>Integrace příchozích koncových bodů

Pokud váš scénář vyžaduje příchozí koncové body, máte dvě možnosti:

| Možnost | Vzor | Příklad | Verze Pro | Con |
|---|---|---|---|---|
| (1) | **Zarovnejte** příchozí koncové body s příslušnými **zásobníky zonální, které** vytváříte pro odchozí. | Vytvořte standardní vyvažovač zatížení se zonálním frontendem. | Stejný model stavu a režim selhání pro příchozí a odchozí. Jednodušší ovládání. | Jednotlivé IP adresy pro jednotlivé zóny může být nutné maskovat běžným názvem DNS. |
| (2) | **Překryvte** zásobníky zón s příchozím koncovým bodem **mezi zónami.** | Vytvořte standardní vyvažovač zatížení s front-endem bez zón. | Jedna ADRESA IP pro příchozí koncový bod. | Různé modely stavu a režimy selhání pro příchozí a odchozí.  Složitější provoz. |

>[!NOTE]
> Zónově izolovaná brána NAT vyžaduje IP adresy, které odpovídají zóně brány NAT. Prostředky brány NAT s IP adresami z jiné zóny nebo bez zóny nejsou povoleny.

#### <a name="cross-zone-outbound-scenarios-not-supported"></a>Odchozí scénáře mezi zónami nejsou podporovány

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" width="425" title="Virtuální síť NAT není kompatibilní s podsítí pro zónování">
</p>

*Obrázek: Překlad síťových sítí není kompatibilní s podsítí zahrnující zónu*

Pokud se instance virtuálních strojů nasazují ve více zónách ve stejné podsíti, nelze dosáhnout zonálního příslibu s prostředky brány NAT.   A i kdyby k podsíti bylo připojeno více zonálních bran NAT, instance virtuálního počítače by nevěděla, který prostředek brány NAT má být vyvolený.

Zonální příslib neexistuje, když a) zóna instance virtuálního počítače a zóny zóny brány nananek zóny nejsou zarovnány nebo b) prostředek brány regionální NAT se používá s instancemi místního virtuálního počítače.

Zatímco scénář se zobrazí pracovat, jeho model stavu a režim selhání není definovánz hlediska zóny dostupnosti. Zvažte jít s zonální zásobníky nebo všechny regionální místo.

>[!NOTE]
>Vlastnost zóny prostředku brány NAT není proměnlivá.  Znovu nasaďte prostředek brány NAT s zamýšlenou místní nebo zónovou předvolbou.

>[!NOTE] 
>IP adresy samy o sobě nejsou zónově redundantní, pokud není zadána žádná zóna.  Front-end [standardního vykladače zatížení je zóna redundantní,](../load-balancer/load-balancer-standard-availability-zones.md#frontend) pokud ip adresa není vytvořena v určité zóně.  To se nevztahuje na NAT.  Podporována je pouze regionální nebo zónová izolace.

## <a name="source-network-address-translation"></a>Překlad zdrojové síťové adresy

Zdrojový překlad síťových adres (SNAT) přepíše zdroj toku, který má pocházet z jiné adresy IP.  Prostředky brány NAT používají variantu SNAT běžně označované jako překlad adresy portu (PAT). PAT přepíše zdrojovou adresu a zdrojový port. S SNAT neexistuje žádný pevný vztah mezi počtem soukromých adres a jejich přeloženými veřejnými adresami.  

### <a name="fundamentals"></a>Základy

Podívejme se na příklad čtyř toků, které vysvětlují základní koncept.  Brána NAT používá prostředek veřejné IP adresy 65.52.0.2.

| Tok | Zdrojová řazená kolekce členů | Cílová řazená kolekce členů |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

Tyto toky mohou vypadat takto po PAT došlo:

| Tok | Zdrojová řazená kolekce členů | SNAT'ed zdroj n-tice | Cílová řazená kolekce členů | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.2:234 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.2:235 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.2:236 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:237 | 65.52.0.2:80 |

Cíl uvidí zdroj toku jako 65.52.0.2 (zdrojová řazená kolekce členů SNAT) se zobrazeným přiřazeným portem.  PAT, jak je znázorněno v předchozí tabulce se také nazývá port maskování SNAT.  Za ip adresou a portem je maskováno více soukromých zdrojů.

Neber závislost na konkrétním způsobu přiřazení zdrojových portů.  Předek je pouze ilustrací základního pojmu.

SNAT poskytované NAT se liší od [load balancer](../load-balancer/load-balancer-outbound-connections.md) v několika aspektech.

### <a name="on-demand"></a>Na vyžádání

NAT poskytuje porty SNAT na vyžádání pro nové odchozí přenosy. Všechny dostupné porty SNAT ve skladu používají všechny virtuální počítače v podsítích nakonfigurovaných pomocí NAT. 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" width="550" title="Virtuální síť NAT odchozí SNAT na vyžádání">
</p>

*Obrázek: Virtuální síť NAT na vyžádání odchozí SNAT*

Libovolná konfigurace IP virtuálního počítače může podle potřeby vytvářet odchozí toky na vyžádání.  Předběžné přidělení podle plánování instance včetně překročení prvního případu na instanci není povinné.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" width="550" title="Rozdíly ve scénářích vyčerpání">
</p>

*Obrázek: Rozdíly ve scénářích vyčerpání*

Jakmile se port SNAT uvolní, je k dispozici pro použití libovolným virtuálním počítačem v podsítích nakonfigurovaných pomocí NAT.  Přidělení na vyžádání umožňuje dynamické a rozdílné úlohy v podsítích používat porty SNAT podle potřeby.  Dokud bude k dispozici inventář portů SNAT, toky SNAT budou úspěšné. SNAT port hot spots těžit z větší zásoby místo. Porty SNAT nejsou ponechány nepoužívané pro virtuální počítače, které je aktivně nepotřebují.

### <a name="scaling"></a>Škálování

Škálování NAT je především funkce správy sdílené, dostupné snat port zásob. Nat potřebuje dostatek zásob portů SNAT pro očekávané maximální odchozí toky pro všechny podsítě připojené k prostředku brány NAT.  K vytvoření inventáře portů SNAT můžete použít prostředky veřejných IP adres, veřejné prostředky předpony IP nebo obojí.

SNAT mapuje soukromé adresy na jednu nebo více veřejných IP adres, přepisuje zdrojovou adresu a zdrojový port v procesech. Prostředek brány NAT použije pro tento překlad 64 000 portů (portů SNAT) na konfigurovanou veřejnou IP adresu. Prostředky brány NAT mohou škálovat až na 16 IP adres a 1M Porty SNAT. Pokud je k dispozici prostředek veřejné předpony IP, každá adresa IP v rámci předpony poskytuje inventář portů SNAT. A přidání dalších veřejných IP adres zvyšuje dostupné porty SNAT. TCP a UDP jsou samostatné zásoby portů SNAT a nesouvisející.

Prostředky brány NAT oportunisticky znovu používají zdrojové porty. Pro účely škálování byste měli předpokládat, že každý tok vyžaduje nový port SNAT a škálovat celkový počet dostupných IP adres pro odchozí provoz.

### <a name="protocols"></a>Protokoly

Prostředky brány NAT interagují s hlavičkami přenosu IP a IP toků UDP a TCP a jsou agnostik k datovým částem aplikační vrstvy.  Jiné protokoly IP nejsou podporovány.

### <a name="timers"></a>Časovače

>[!IMPORTANT]
>Dlouhý časovač nečinnosti může zbytečně zvýšit pravděpodobnost vyčerpání SNAT. Čím déle časovač zadáte, delší NAT bude držet na SNAT porty, dokud se nakonec časový limit nečinnosti. Pokud jsou časový limit nečinnosti, nakonec se nezdaří a zbytečně spotřebovávají zásoby portů SNAT.  Toky, které selhávají po 2 hodinách, by selhaly také ve výchozím nastavení 4 minuty. Zvýšení časového limitu nečinnosti je poslední možnost, která by měla být použita střídmě. Pokud tok nikdy nezůstane nečinný, nebude ovlivněn časovačem nečinnosti.

Časový limit nečinnosti protokolu TCP lze upravit ze 4 minut (výchozí) na 120 minut (2 hodiny) pro všechny toky.  Kromě toho můžete obnovit časovač nečinnosti s provozem na toku.  Doporučený vzor pro aktualizaci dlouhých nečinných připojení a detekce živosti koncového bodu je tcp keepalives.  Tcp keepalives se zobrazí jako duplicitní ACK ke koncovým bodům, jsou nízké režii a neviditelné pro aplikační vrstvu.

Pro vydání portu SNAT se používají následující časovače:

| Časovač | Hodnota |
|---|---|
| TCP FIN | 60 sekund |
| TCP RST | 10 sekund |
| TCP napůl otevřené | 30 sekund |

Port SNAT je k dispozici pro opakované použití na stejnou cílovou adresu IP a cílový port po 5 sekundách.

>[!NOTE] 
>Tato nastavení časovače se mohou změnit. Hodnoty jsou k dispozici pro řešení potíží a neměli byste mít závislost na konkrétní časovače v tomto okamžiku.

## <a name="limitations"></a>Omezení

- Nat je kompatibilní se standardními veřejnými IP adresami Skladových disponií, veřejnou předponou IP a prostředky pro vyrovnávání zatížení.   Základní zdroje (například základní vyvyčovávač zatížení) a všechny produkty z nich odvozené nejsou kompatibilní s nat.  Základní prostředky musí být umístěny v podsíti, která není nakonfigurována pomocí nasíťového nat.
- Rodina adres IPv4 je podporována.  NAT nespolupracuje s rodinou adres IPv6.  Nat nelze nasadit v podsíti s předponou IPv6.
- Protokolování toku nsg není podporováno při použití NAT.
- Nat nemůže protápit více virtuálních sítí.


## <a name="feedback"></a>Váš názor

Chceme vědět, jak můžeme zlepšit službu. Chybí vám schopnost? Udělej si svůj případ pro to, co bychom měli stavět další na [UserVoice pro NAT](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Další kroky

* Další informace o [virtuální síti NAT](nat-overview.md).
* Přečtěte si [o metrikách a výstrahách pro prostředky brány NAT](nat-metrics.md).
* Informace o [řešení potíží s prostředky brány NAT](troubleshoot-nat.md).
* Kurz pro ověření brány NAT
  - [Azure CLI](tutorial-create-validate-nat-gateway-cli.md)
  - [PowerShell](tutorial-create-validate-nat-gateway-powershell.md)
  - [Portál](tutorial-create-validate-nat-gateway-portal.md)
* Úvodní příručka pro nasazení prostředku brány NAT
  - [Azure CLI](./quickstart-create-nat-gateway-cli.md)
  - [PowerShell](./quickstart-create-nat-gateway-powershell.md)
  - [Portál](./quickstart-create-nat-gateway-portal.md)
  - [Šablona](./quickstart-create-nat-gateway-template.md)
* Informace o rozhraní API pro prostředky brány NAT
  - [REST API](https://docs.microsoft.com/rest/api/virtualnetwork/natgateways)
  - [Azure CLI](https://docs.microsoft.com/cli/azure/network/nat/gateway?view=azure-cli-latest)
  - [PowerShell](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway)
* Další informace o [zónách dostupnosti](../availability-zones/az-overview.md).
* Informace o [standardním vyvažovači zatížení](../load-balancer/load-balancer-standard-overview.md).
* Informace o [zónách dostupnosti a standardním vyvažovači zatížení](../load-balancer/load-balancer-standard-availability-zones.md).
* [Řekněte nám, co se má stavět pro virtuální síť NAT v UserVoice](https://aka.ms/natuservoice).


