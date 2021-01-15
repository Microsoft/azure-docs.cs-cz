---
title: Navrhování virtuálních sítí pomocí prostředků brány NAT
titleSuffix: Azure Virtual Network NAT
description: Naučte se navrhovat virtuální sítě s prostředky brány NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to learn more about how to design virtual networks with NAT gateway resources.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: allensu
ms.openlocfilehash: d4ef8e6207d53a192b19f8343a60093e82368fa6
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223376"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources"></a>Navrhování virtuálních sítí pomocí prostředků brány NAT

Prostředky brány NAT jsou součástí [Virtual Network NAT](nat-overview.md) a poskytují odchozí připojení k Internetu pro jednu nebo více podsítí virtuální sítě. Podsíť stavu virtuální sítě, která se použije pro bránu NAT. Překlad adres (NAT) pro podsíť poskytuje překlad adres (SNAT).  Prostředky brány NAT určují, které statické IP adresy virtuální počítače používají při vytváření odchozích toků. Statické IP adresy pocházejí z prostředků veřejné IP adresy (PIP), prostředků předpony veřejných IP adres nebo obojího. Pokud se používá prostředek předpony veřejných IP adres, všechny IP adresy celého prostředku předpony veřejné IP adresy se spotřebují prostředkem brány NAT. Prostředek brány NAT může využít celkem až 16 statických IP adres z obou.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="Virtual Network překlad adres (NAT) pro odchozí připojení do Internetu">
</p>

*Obrázek: Virtual Network NAT pro odchozí připojení do Internetu*

## <a name="how-to-deploy-nat"></a>Postup nasazení překladu adres (NAT)

Konfigurace a používání brány NAT je záměrně jednoduché:  

Prostředek brány NAT:
- Vytvořte prostředek brány NAT pro oblast nebo oblast (izolovaný z zóny),
- Přiřaďte IP adresy,
- V případě potřeby upravte časový limit nečinnosti protokolu TCP (volitelné).  Zkontrolujte [časovače](#timers) <ins>předtím, než</ins> změníte výchozí nastavení.

Virtuální síť:
- Nakonfigurujte podsíť virtuální sítě tak, aby používala bránu NAT.

Trasy definované uživatelem nejsou nutné.

## <a name="resource"></a>Prostředek

Prostředek je navržený tak, aby byl jednoduchý, jak můžete vidět v následujícím příkladu Azure Resource Manager ve formátu podobném šabloně.  Tento formát podobný tomuto: slouží k znázornění konceptů a struktury.  Upravte příklad podle svých potřeb.  Tento dokument není určený jako kurz.

Následující diagram znázorňuje zapisovatelné odkazy mezi různými Azure Resource Manager prostředky.  Šipka označuje směr odkazu, který pochází z místa, odkud je možné zapisovat. Opakování 

<p align="center">
  <img src="media/nat-overview/flow-map.svg" alt="Figure depicts a NAT receiving traffic from internal subnets and directing it to a public IP and an IP prefix." width="256" title="Virtual Network objektového modelu NAT">
</p>

*Obrázek: Virtual Network objektového modelu NAT*

Překlad adres (NAT) se doporučuje pro většinu úloh, pokud nemáte konkrétní závislost na [Load Balancer odchozí připojení na základě fondu](../load-balancer/load-balancer-outbound-connections.md).  

Můžete migrovat ze standardních scénářů nástroje pro vyrovnávání zatížení, včetně [odchozích pravidel](../load-balancer/load-balancer-outbound-connections.md#outboundrules), do brány NAT. Pokud chcete migrovat, přesuňte prostředky předpony veřejné IP adresy a veřejné IP adresy ze služby Load Balancer do brány NAT. Nové IP adresy brány NAT se nevyžadují. Prostředky se standardními veřejnými IP adresami a prostředky předpony veřejných IP adres se dají znovu použít, dokud celková hodnota nepřekročí 16 IP adres. Plánování migrace s přerušením služeb při přechodu.  K minimalizaci přerušení slouží automatizace procesu. Nejprve otestujte migraci v přípravném prostředí.  V průběhu přechodu nejsou ovlivněny příchozí toky.


Následující příklad je fragment kódu z Azure Resource Manager šablony.  Tato šablona nasadí několik prostředků, včetně brány NAT.  V tomto příkladu má šablona následující parametry:

- **natgatewayname** – název brány NAT.
- **umístění** – oblast Azure, kde se nachází prostředek.
- **publicipname** – název odchozí veřejné IP adresy přidružené k BRÁNě NAT
- **vnetname** – název virtuální sítě.
- název **podsítě** – název podsítě přidružené k bráně NAT.

Celkový počet IP adres poskytnutých všemi IP adresami a prostředky předpony nesmí být delší než 16 IP adres. Je povolen libovolný počet IP adres mezi 1 a 16.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="81-96":::

Po vytvoření prostředku brány NAT se dá použít v jedné nebo několika podsítích virtuální sítě. Určete podsítě, které tento prostředek brány NAT používají. Brána NAT nemůže rozbírat více než jednu virtuální síť. Není nutné přiřadit stejnou bránu NAT ke všem podsítím virtuální sítě. Jednotlivé podsítě je možné nakonfigurovat pomocí různých prostředků brány NAT.

Scénáře, které nepoužívají zóny dostupnosti, budou oblastní (bez zadání zóny). Pokud používáte zóny dostupnosti, můžete zadat zónu pro izolaci překladu adres (NAT) pro konkrétní zónu. Zóna – redundance není podporována. Zkontrolujte [zóny dostupnosti](#availability-zones)NAT.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="1-146" highlight="81-96":::

Brány NAT jsou definované s vlastností v podsíti v rámci virtuální sítě. Toky vytvořené virtuálními počítači v **podsíti** podsítě virtuální sítě **vnetname** budou používat bránu NAT. Všechna odchozí připojení budou používat IP adresy přidružené k **natgatewayname** jako zdrojová IP adresa.

Další informace o šabloně Azure Resource Manager použité v tomto příkladu naleznete v tématu:

- [Rychlý Start: Vytvoření brány NAT – šablona Správce prostředků](quickstart-create-nat-gateway-template.md)
- [Virtual Network NAT](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm/)

## <a name="design-guidance"></a>Doprovodné materiály k návrhu

V této části se seznámíte s důležitými informacemi pro navrhování virtuálních sítí pomocí překladu adres (NAT).  

1. [Optimalizace nákladů](#cost-optimization)
1. [Koexistence příchozích a odchozích](#coexistence-of-inbound-and-outbound)
2. [Správa základních prostředků](#managing-basic-resources)
3. [Zóny dostupnosti](#availability-zones)

### <a name="cost-optimization"></a>Optimalizace nákladů

[Koncové body služby](virtual-network-service-endpoints-overview.md) a [soukromé odkazy](../private-link/private-link-overview.md) jsou možnosti, které je vhodné zvážit pro optimalizaci nákladů. NAT není pro tyto služby potřeba. Přenosy směrované do koncových bodů služby nebo privátního propojení nejsou zpracovávány překladem adres (NAT) virtuální sítě.  

Koncové body služby propojují prostředky služeb Azure s vaší virtuální sítí a ovládají přístup k prostředkům služby Azure. Když například přistupujete k Azure Storage, použijte koncový bod služby pro úložiště, abyste se vyhnuli poplatkům za NAT zpracovaných dat. Koncové body služby jsou bezplatné.

Privátní odkaz zveřejňuje službu Azure PaaS (nebo jiné služby hostované pomocí privátního propojení) jako soukromý koncový bod ve virtuální síti.  Privátní odkaz se účtuje na základě doby trvání a zpracovaných dat.

Vyhodnoťte, jestli je jeden nebo oba z těchto přístupů pro váš scénář dobrým způsobem, a použijte podle potřeby.

### <a name="coexistence-of-inbound-and-outbound"></a>Koexistence příchozích a odchozích

Brána NAT je kompatibilní s:

 - Load Balancer úrovně Standard
 - Standardní veřejná IP adresa
 - Standardní předpona veřejných IP adres

Při vývoji nového nasazení začněte se standardními SKU Standard.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network." width="256" title="Virtual Network překlad adres (NAT) pro odchozí připojení do Internetu">
</p>

*Obrázek: Virtual Network NAT pro odchozí připojení do Internetu*

Scénář jenom pro odchozí připojení k Internetu poskytovaný bránou NAT se dá rozšířit o funkce z Internetu. Každý prostředek ví o směru, ve kterém se tok nacházel. V podsíti s bránou NAT je všechny odchozí a internetové scénáře nahrazené bránou NAT. Příchozí z internetových scénářů poskytuje příslušný prostředek.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>NAT a virtuální počítač s veřejnou IP adresou na úrovni instance

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP." width="300" title="Virtual Network NAT a virtuální počítač s veřejnou IP adresou na úrovni instance">
</p>

*Obrázek: Virtual Network NAT a virtuální počítač s veřejnou IP adresou na úrovni instance*

| Směr | Prostředek |
|:---:|:---:|
| Příchozí | Virtuální počítač s veřejnou IP adresou na úrovni instance |
| Odchozí | NAT Gateway |

Virtuální počítač bude používat bránu NAT pro odchozí připojení.  Příchozí původ není ovlivněn.

#### <a name="nat-and-vm-with-public-load-balancer"></a>NAT a virtuální počítač s veřejným Load Balancer

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with a public load balancer." width="350" title="Virtual Network NAT a virtuální počítač s veřejným Load Balancer">
</p>

*Obrázek: Virtual Network NAT a virtuální počítač s veřejným Load Balancer*

| Směr | Prostředek |
|:---:|:---:|
| Příchozí | veřejné Load Balancer |
| Odchozí | NAT Gateway |

Jakákoli odchozí konfigurace z pravidla vyrovnávání zatížení nebo odchozích pravidel je nahrazena bránou NAT.  Příchozí původ není ovlivněn.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-public-load-balancer"></a>NAT a virtuální počítač s veřejnou IP adresou na úrovni instance a veřejnými Load Balancer

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP and a public load balancer." width="425" title="Virtual Network NAT a virtuální počítač s veřejnou IP adresou na úrovni instance a veřejnými Load Balancer">
</p>

*Obrázek: Virtual Network NAT a virtuální počítač s veřejnou IP adresou na úrovni instance a veřejným Load Balancer*

| Směr | Prostředek |
|:---:|:---:|
| Příchozí | Virtuální počítač s veřejnou IP adresou na úrovni instance a veřejnými Load Balancer |
| Odchozí | NAT Gateway |

Jakákoli odchozí konfigurace z pravidla vyrovnávání zatížení nebo odchozích pravidel je nahrazena bránou NAT.  Virtuální počítač bude taky používat bránu NAT pro odchozí připojení.  Příchozí původ není ovlivněn.

### <a name="managing-basic-resources"></a>Správa základních prostředků

Služba Load Balancer úrovně Standard, veřejná IP adresa a předpona veřejné IP adresy jsou kompatibilní s bránou NAT. Brány NAT pracují v oboru podsítě. Základní skladová položka těchto služeb musí být nasazená v podsíti bez brány NAT. Toto rozdělení umožňuje, aby obě varianty SKU existovaly ve stejné virtuální síti.

Brány NAT mají přednost před odchozími scénáři podsítě. Základní nástroj pro vyrovnávání zatížení nebo veřejnou IP adresu (a všechny spravované služby, které jsou s nimi vytvořeny), nelze upravit pomocí správných překladů. Brána NAT v podsíti přebírá kontrolu odchozího provozu na Internet. Příchozí provoz do služby Load Balancer úrovně Basic a veřejná IP adresa nejsou k dispozici. Příchozí provoz na základní nástroj pro vyrovnávání zatížení, nebo veřejná IP adresa nakonfigurovaná na virtuálním počítači, nebude k dispozici.

### <a name="availability-zones"></a>Zóny dostupnosti

#### <a name="zone-isolation-with-zonal-stacks"></a>Izolace zóny pomocí plošných zásobníků

<p align="center">
  <img src="media/nat-overview/az-directions.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet." width="425" title="Virtual Network překlad adres (NAT) s izolací zóny, vytváření více "zonal stacks"">
</p>

*Obrázek: Virtual Network překlad adres (NAT) s izolací zóny, vytvoření několika "plošných zásobníků"*

I bez zón dostupnosti je NAT odolné a může zamezit několik selhání součástí infrastruktury.  Zóny dostupnosti sestavují tuto odolnost pomocí scénářů izolace zón pro překlad adres (NAT).

Virtuální sítě a jejich podsítě jsou regionální konstrukce.  Podsítě nejsou omezeny na zónu.

Příslib oblasti pro izolaci zóny existuje, pokud instance virtuálního počítače pomocí prostředku brány NAT je ve stejné zóně jako prostředek brány NAT a jeho veřejné IP adresy. Vzor, který chcete použít pro izolaci zóny, vytváří pro každou zónu dostupnosti "plošný zásobník".  Tato "sada oblastí" sestává z instancí virtuálních počítačů, prostředků brány NAT, veřejné IP adresy nebo prostředků předpony v podsíti, u které se předpokládá, že obsluhuje pouze stejnou zónu.   Operace roviny ovládacího prvku a rovina dat jsou pak zarovnány k zadané zóně a omezeny na ni. 

Selhání v jiné oblasti, než kde váš scénář existuje, by mělo být bez dopadu na překlad adres (NAT). Odchozí přenosy z virtuálních počítačů ve stejné zóně selžou z důvodu izolace zóny.  

#### <a name="integrating-inbound-endpoints"></a>Integrace příchozích koncových bodů

Pokud váš scénář vyžaduje vstupní koncové body, máte dvě možnosti:

| Možnost | Vzor | Příklad | Verze Pro | Př |
|---|---|---|---|---|
| první | **Zarovnejte** příchozí koncové body s příslušnými **zásobníky pro oblast** , které vytváříte pro odchozí. | Vytvořte službu Load Balancer úrovně Standard s oblastmi front-endu. | Stejný model stavu a režim selhání pro příchozí a odchozí. Jednodušší provoz. | Jednotlivé IP adresy na zónu může být potřeba maskovat běžným názvem DNS. |
| (2) | Skládání plošných zásobníků **přes příchozí** koncový bod **mezi zónami** | Vytvořte službu Load Balancer úrovně Standard s front-redundantním front-endu. | Jedna IP adresa pro příchozí koncový bod | Různé modely stavu a režimy selhání pro příchozí a odchozí.  Složitější pro práci. |

>[!NOTE]
> Brána NAT izolovaná na zóně vyžaduje, aby IP adresy odpovídaly zóně brány NAT. Prostředky brány NAT s IP adresami z jiné zóny nebo bez zóny nejsou povolené.

#### <a name="cross-zone-outbound-scenarios-not-supported"></a>Odchozí scénáře mezi zónami nejsou podporovány.

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet, with the connections between to of the gateways and their subnets broken." width="425" title="Virtual Network NAT není kompatibilní s podsítí pokrývání zón.">
</p>

*Obrázek: Virtual Network NAT není kompatibilní s podsítí pokrývání zóny*

Pokud jsou instance virtuálních počítačů nasazené ve více zónách ve stejné podsíti, nemůžete dosáhnout příslibu Zona s prostředky brány NAT.   A dokonce i v případě, že jsou k podsíti připojená několik bran NAT, instance virtuálního počítače by nevěděla, který prostředek brány NAT má vybrat.

Does't příslib v oblasti a v případě, že se nerovná zóna instance virtuálního počítače a zóna brány NAT v oblasti oblast, se pro instance virtuálních počítačů používá místní prostředek brány NAT.

I když se zdá, že se tento scénář bude pracovat, jeho model stavu a režim selhání není definovaný v bodu zóny dostupnosti zobrazení. Zvažte místo toho použití zásobníků oblastí nebo všech oblastí.

>[!NOTE]
>Vlastnost Zones prostředku brány NAT není proměnlivá.  Znovu nasaďte prostředek brány NAT pomocí zamýšleného regionu nebo Předvolby zóny.

>[!NOTE] 
>IP adresy samy sebe nejsou v zóně – redundantní, pokud není zadaná žádná zóna.  Front [-end Standard Load Balancer je redundantní v zóně](../load-balancer/load-balancer-standard-availability-zones.md#frontend) , pokud IP adresa není vytvořená v konkrétní zóně.  To se nevztahuje na překlad adres (NAT).  Podporovaná je jenom regionální nebo izolovaný izolaci zón.

## <a name="performance"></a>Výkon

Každý prostředek brány NAT může poskytovat propustnost až 50 GB/s. Nasazení můžete rozdělit do několika podsítí a přiřadit každou podsíť nebo skupiny podsítí a bránu NAT pro horizontální navýšení kapacity.

Každá brána NAT podporuje 64 000 toků pro TCP a UDP na přiřazenou odchozí IP adresu.  Podrobné informace a pokyny k [řešení problémů najdete](./troubleshoot-nat.md) v následující části o překladu zdrojového síťového adres (SNAT).

## <a name="source-network-address-translation"></a>Překlad zdrojové síťové adresy

Zdrojový překlad adres (SNAT) přepíše zdroj toku, který má být vytvořen z jiné IP adresy.  Prostředky brány NAT používají variantu SNAT, která se běžně označuje jako na překladu adres portů (PAT). PAT přepíše zdrojovou a zdrojový port. V SNAT není žádný pevný vztah mezi počtem privátních adres a jejich přeloženými veřejnými adresami.  

### <a name="fundamentals"></a>Základy

Pojďme se podívat na příklad čtyř toků a vysvětlit základní koncept.  Brána NAT používá prostředek 65.52.1.1 veřejné IP adresy a virtuální počítač vytváří připojení k 65.52.0.1.

| Tok | Zdrojová řazená kolekce členů | Cílová řazená kolekce členů |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |

Tyto toky můžou vypadat takto po přijetí PAT:

| Tok | Zdrojová řazená kolekce členů | SNAT'ed zdrojová řazená kolekce členů | Cílová řazená kolekce členů | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | **65.52.1.1:1234** | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | **65.52.1.1:1235** | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | **65.52.1.1:1236** | 65.52.0.1:80 |

Cíl se zobrazí jako 65.52.0.1 (zdrojová řazená kolekce členů na SNAT) se zobrazeným přiřazeným portem.  PAT, jak je znázorněno v předchozí tabulce, se označuje také jako maskování portů SNAT.  Několik privátních zdrojů je maskovaných za IP adresou a portem.  

#### <a name="source-snat-port-reuse"></a>zdroj (SNAT) opakované použití portu

Brány NAT oportunisticky znovu používat zdrojový port (SNAT).  Následující příklad znázorňuje tento koncept jako dodatečný tok pro předchází sadu toků.  Virtuální počítač v příkladu je tok, který se 65.52.0.2.

| Tok | Zdrojová řazená kolekce členů | Cílová řazená kolekce členů |
|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

Brána NAT bude pravděpodobně překládat tok 4 na port, který může být použit i pro jiné cíle.  Další diskuzi o správném určení velikosti zřizování IP adres najdete v tématu [škálování](#scaling) .

| Tok | Zdrojová řazená kolekce členů | SNAT'ed zdrojová řazená kolekce členů | Cílová řazená kolekce členů | 
|:---:|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.1.1:**1234** | 65.52.0.2:80 |

Neprovádějte závislost na konkrétním způsobu, jakým zdrojové porty jsou přiřazeny v předchozím příkladu.  Předchozí je příkladem základního konceptu.

SNAT, kterou poskytuje překlad adres (NAT), se liší od [Load Balancer](../load-balancer/load-balancer-outbound-connections.md) v několika aspektech.

### <a name="on-demand"></a>Na vyžádání

NAT poskytuje porty SNAT na vyžádání pro nové toky odchozího provozu. Všechny dostupné porty SNAT v inventáři používá libovolný virtuální počítač v podsítích konfigurovaných pomocí překladu adres (NAT). 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T." width="550" title="Odchozí SNAT na vyžádání Virtual Network překladu adres (NAT)">
</p>

*Obrázek: Virtual Network NAT na vyžádání odchozí SNAT*

Jakákoli konfigurace IP virtuálního počítače může v případě potřeby vytvářet odchozí toky na vyžádání.  Plánování předběžného přidělování za instance, včetně nadměrného zřízení případných případů, se nevyžaduje.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T with exhaustion threshold." width="550" title="Rozdíly ve scénářích vyčerpání">
</p>

*Obrázek: rozdíly ve scénářích vyčerpání*

Po vydání portu SNAT je možné ho použít pro libovolný virtuální počítač v podsítích nakonfigurovaných pomocí překladu adres (NAT).  Přidělení na vyžádání umožňuje dynamickým a odlišným úlohám v podsítích používat porty SNAT podle potřeby.  Pokud je k dispozici inventář portů SNAT, budou toky SNAT úspěšné. Z většího inventáře místo toho zvýhodněné body SNAT z provozu. Porty SNAT nejsou nepoužitelné pro virtuální počítače, které je aktivně nepotřebují.

### <a name="scaling"></a>Škálování

Škálování NAT je primárně funkcí správy sdíleného, dostupného inventáře portů SNAT. Překlad adres (NAT) potřebuje dostatek inventáře portů SNAT pro očekávané výstupní toky ve špičce pro všechny podsítě připojené k prostředku brány NAT.  K vytvoření inventáře portů SNAT můžete použít prostředky veřejné IP adresy, prostředky předpony veřejných IP adres nebo obojí.  

>[!NOTE]
>Pokud přiřazujete prostředek předpony veřejných IP adres, použije se celá předpona veřejné IP adresy.  Nemůžete přiřadit prostředek předpony veřejných IP adres a pak rozdělit jednotlivé IP adresy, které se přiřadí jiným prostředkům.  Chcete-li přiřadit jednotlivé IP adresy z předpony veřejné IP adresy k více prostředkům, je třeba vytvořit jednotlivé veřejné IP adresy z prostředku předpony veřejné IP adresy a přiřadit je podle potřeby namísto samotného prostředku předpony veřejné IP adresy.

SNAT namapuje privátní adresy na jednu nebo více veřejných IP adres a přepíše zdrojové adresy a zdrojový port v procesech. Prostředek brány NAT bude pro tento překlad používat porty 64 000 (porty SNAT) podle nakonfigurované veřejné IP adresy. Prostředky brány NAT můžou škálovat až na 16 IP adres a 1 milion portů SNAT. Pokud je zadaný prostředek předpony veřejných IP adres, každá IP adresa v rámci předpony poskytuje inventář portů SNAT. A přidáním dalších veřejných IP adres zvýšíte dostupné porty SNAT inventáře. TCP a UDP jsou samostatné inventáře portů SNAT a nesouvisející.

Prostředky brány NAT oportunisticky opakované použití zdrojového kódu (SNAT). Jako pokyny k návrhu pro účely škálování byste měli předpokládat, že každý tok vyžaduje nový port SNAT a škálovat celkový počet dostupných IP adres pro odchozí provoz.  Měli byste pečlivě zvážit škálování, které navrhujete, a zajistit, aby se odpovídajícím způsobem zřídily IP adresy.

Porty SNAT do různých cílů se pravděpodobně znovu použijí, pokud je to možné. A jako přístupy k vyčerpání portů SNAT nemusí být toky úspěšné.  

Podívejte se například na [základy SNAT](#source-network-address-translation) .


### <a name="protocols"></a>Protokoly

Prostředky brány NAT komunikují s hlavičkami IP a IP přenosů protokolů UDP a TCP a nezávislá se do datových částí aplikační vrstvy.  Jiné protokoly IP nejsou podporovány.

### <a name="timers"></a>Časovače

>[!IMPORTANT]
>Dlouhý časovač nečinnosti může nutně zvýšit pravděpodobnost vyčerpání SNAT. Čím dál zadáte časovač, tím déle bude zařízení NAT na portech SNAT, dokud nezruší časový limit nečinnosti. Pokud vaše toky nemají časový limit nečinnosti, budou se nakonec zdařit a zbytečně spotřebovávat inventář portů SNAT.  Toky, které selžou 2 hodiny, se nezdařily i ve výchozím nastavení 4 minuty. Zvýšení časového limitu nečinnosti je poslední možnost použití, která by se měla používat zřídka. Pokud tok nikdy nepracuje, nebude to mít vliv na nečinný časovač.

Časový limit nečinnosti TCP se dá upravit ze 4 minut (výchozí) na 120 minut (2 hodiny) pro všechny toky.  Kromě toho můžete časovač nečinnosti resetovat pomocí provozu toku.  Doporučený vzor pro obnovení dlouhých nečinných připojení a zjišťování živých koncových bodů je udržení naživu v TCP.  Kontroly zatížení sítě TCP se zobrazují jako duplicitní potvrzení koncových bodů, jsou nízká režie a neviditelná pro vrstvu aplikace.

Pro vydání portu SNAT se používají následující časovače:

| Časovač | Hodnota |
|---|---|
| TCP FIN | 60 sekund |
| TCP RST | 10 sekund |
| Poloviční otevření TCP | 30 sekund |

Port SNAT je k dispozici pro opakované použití na stejnou cílovou IP adresu a cílový port po 5 sekundách.

>[!NOTE] 
>Tato nastavení časovače se mohou změnit. Tyto hodnoty jsou k dispozici pro pomoc při řešení potíží a v současné době byste neměli mít závislost na konkrétních časovačích.

## <a name="limitations"></a>Omezení

- Překlad adres (NAT) je kompatibilní s veřejnou IP adresou SKU, předponou veřejné IP adresy a prostředky nástroje pro vyrovnávání zatížení.   Základní prostředky (například základní nástroj pro vyrovnávání zatížení) a jakékoli produkty, které jsou z nich odvozené, nejsou kompatibilní se službou NAT.  Základní prostředky musí být umístěné v podsíti, která není nakonfigurovaná s překladem adres (NAT).
- Rodina adres IPv4 je podporovaná.  Překlad adres (NAT) nekomunikuje s řadou IPv6 adres.  Překlad adres (NAT) nejde nasadit v podsíti s předponou IPv6.
- Překlad adres (NAT) nemůže zahrnovat víc virtuálních sítí.

## <a name="suggestions"></a>Návrhy

Chceme zjistit, jak můžeme službu vylepšit. Chybí funkce? Udělejte si případ, co by se mělo na webu [UserVoice pro překlad adres (NAT)](https://aka.ms/natuservoice)sestavit dál.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o službě [NAT pro virtuální sítě](nat-overview.md).
* Seznamte [se s metrikami a upozorněními pro prostředky brány NAT](nat-metrics.md).
* Přečtěte si informace o [řešení potíží s prostředky brány NAT](troubleshoot-nat.md).
* Kurz pro ověření brány NAT
  - [Azure CLI](tutorial-create-validate-nat-gateway-cli.md)
  - [PowerShell](tutorial-create-validate-nat-gateway-powershell.md)
  - [Azure Portal](tutorial-create-validate-nat-gateway-portal.md)
* Rychlý Start pro nasazení prostředku brány NAT
  - [Azure CLI](./quickstart-create-nat-gateway-cli.md)
  - [PowerShell](./quickstart-create-nat-gateway-powershell.md)
  - [Azure Portal](./quickstart-create-nat-gateway-portal.md)
  - [Šablona](./quickstart-create-nat-gateway-template.md)
* Další informace o rozhraní API prostředků brány NAT
  - [REST API](/rest/api/virtualnetwork/natgateways)
  - [Azure CLI](/cli/azure/network/nat/gateway)
  - [PowerShell](/powershell/module/az.network/new-aznatgateway)
* Přečtěte si o [zónách dostupnosti](../availability-zones/az-overview.md).
* Přečtěte si o [službě Load Balancer úrovně Standard](../load-balancer/load-balancer-overview.md).
* Seznamte [se se zónami dostupnosti a standardním nástrojem pro vyrovnávání zatížení](../load-balancer/load-balancer-standard-availability-zones.md).
* [Řekněte nám, co se má sestavit příště pro Virtual Network překlad adres (NAT) ve službě UserVoice](https://aka.ms/natuservoice).