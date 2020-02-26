---
title: Navrhování virtuálních sítí pomocí prostředků brány NAT
titleSuffix: Azure Virtual Network NAT
description: Naučte se navrhovat virtuální sítě s prostředky brány NAT.
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
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 9d9033689472fbc462eefd3d31c2b36a19add81a
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589466"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources-public-preview"></a>Navrhování virtuálních sítí pomocí prostředků brány NAT (Public Preview)

Prostředky brány NAT jsou součástí [Virtual Network NAT](nat-overview.md) a poskytují odchozí připojení k Internetu pro jednu nebo více podsítí virtuální sítě. Podsíť stavu virtuální sítě, která se použije pro bránu NAT. Překlad adres (NAT) pro podsíť poskytuje překlad adres (SNAT).  Prostředky brány NAT určují, které statické IP adresy virtuální počítače používají při vytváření odchozích toků. Statické IP adresy pocházejí z prostředků veřejné IP adresy, prostředků předpony veřejných IP adres nebo obojího. Prostředek brány NAT může používat až 16 statických IP adres z obou.


<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtual Network překlad adres (NAT) pro odchozí připojení do Internetu">
</p>

*Obrázek: Virtual Network NAT pro odchozí připojení do Internetu*


>[!NOTE] 
>Virtual Network překlad adres (NAT) je v tuto chvíli k dispozici jako Public Preview. V současné době je dostupná jenom v omezené sadě [oblastí](nat-overview.md#region-availability). Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.comsupport/legal/preview-supplemental-terms).

## <a name="how-to-deploy-nat"></a>Postup nasazení překladu adres (NAT)

Konfigurace a používání brány NAT je záměrně jednoduché:  

Prostředek brány NAT:
- Vytvořte prostředek brány NAT pro oblast nebo oblast (izolovaný z zóny),
- Přiřaďte IP adresy,
- Změnit časový limit nečinnosti (volitelné).

Virtuální síť:
- Nakonfigurujte podsíť virtuální sítě tak, aby používala bránu NAT.

Trasy definované uživatelem nejsou nutné.

## <a name="resource"></a>Prostředek

Prostředek je navržený tak, aby byl jednoduchý, jak můžete vidět v následujícím příkladu Azure Resource Manager ve formátu podobném šabloně.  Tento formát podobný tomuto: slouží k znázornění konceptů a struktury.  Upravte příklad podle svých potřeb.  Tento dokument není určený jako kurz.

Následující diagram znázorňuje zapisovatelné odkazy mezi různými Azure Resource Manager prostředky.  Šipka označuje směr odkazu, který pochází z místa, odkud je možné zapisovat. Revize 

<p align="center">
  <img src="media/nat-overview/flow-map.svg" width="256" title="Virtual Network objektového modelu NAT">
</p>

*Obrázek: Virtual Network objektového modelu NAT*

Překlad adres (NAT) se doporučuje pro většinu úloh, pokud nemáte konkrétní závislost na [Load Balancer odchozí připojení na základě fondu](../load-balancer/load-balancer-outbound-connections.md).  

Můžete migrovat ze standardních scénářů nástroje pro vyrovnávání zatížení, včetně [odchozích pravidel](../load-balancer/load-balancer-outbound-rules-overview.md), do brány NAT. Pokud chcete migrovat, přesuňte prostředky předpony veřejné IP adresy a veřejné IP adresy ze služby Load Balancer do brány NAT. Nové IP adresy brány NAT se nevyžadují. Veřejné IP adresy a předpony Standard se dají znovu použít, dokud celková hodnota nepřekročí 16 IP adres. Plánování migrace s přerušením služeb při přechodu.  K minimalizaci přerušení slouží automatizace procesu. Nejprve otestujte migraci v přípravném prostředí.  V průběhu přechodu nejsou ovlivněny příchozí toky.

Následující příklad vytvoří prostředek brány NAT s názvem _myNATGateway_ , který se vytvoří v oblasti _východní USA 2, AZ 1_ s časovým limitem nečinnosti _4 minut_ . K dispozici jsou tyto odchozí IP adresy:
- Sada prostředků veřejných IP adres _myIP1_ a _myIP2_ a 
- Sada prostředků předpony veřejné IP adresy _myPrefix1_ a _myPrefix2_. 

Celkový počet IP adres poskytnutý všemi čtyřmi prostředky IP adres nemůže být celkový, aby bylo celkem 16 adres IP. Je povolen libovolný počet IP adres mezi 1 a 16.

```json
{
"name": "myNATGateway",
   "type": "Microsoft.Network/natGateways",
   "apiVersion": "2018-11-01",
   "location": "East US 2",
   "sku": { "name": "Standard" },
   "zones": [ "1" ],
   "properties": {
      "idleTimeoutInMinutes": 4, 
      "publicIPPrefixes": [
         {
            "id": "ref to myPrefix1"
         },
         {
            "id": "ref to myPrefix2"
         }
      ],
      "publicIPAddresses": [
         {
            "id": "ref to myIP1"
         },
         {
            "id": "ref to myIP2"
         }
      ]
   }
}
```

Po vytvoření prostředku brány NAT se dá použít v jedné nebo několika podsítích virtuální sítě. Určete podsítě, které tento prostředek brány NAT používají. Brána NAT nemůže rozbírat více než jednu virtuální síť. Není nutné přiřadit stejnou bránu NAT ke všem podsítím virtuální sítě. Jednotlivé podsítě je možné nakonfigurovat pomocí různých prostředků brány NAT.

Scénáře, které nepoužívají zóny dostupnosti, budou oblastní (bez zadání zóny). Pokud používáte zóny dostupnosti, můžete zadat zónu pro izolaci překladu adres (NAT) pro konkrétní zónu. Zóna – redundance není podporována. Zkontrolujte [zóny dostupnosti](#availability-zones)NAT.


```json
{
   "name": "myVNet",
   "apiVersion": "2018-11-01",
   "type": "Microsoft.Network/virtualNetworks",
   "location": "myRegion", 
   "properties": {
      "addressSpace": {
          "addressPrefixes": [
           "192.168.0.0/16"
          ]
      },
      "subnets": [
         {
            "name": "mySubnet1",
            "properties": {
               "addressPrefix": "192.168.0.0/24",
               "natGateway": {
                  "id": "ref to myNATGateway"
               }
            }
         } 
      ]
   }
}
```
Brány NAT jsou definované s vlastností v podsíti v rámci virtuální sítě. Toky vytvořené virtuálními počítači v podsíti _mySubnet1_ služby Virtual Network _myVNet_ budou používat bránu NAT. Všechna odchozí připojení budou používat IP adresy přidružené k _myNatGateway_ jako zdrojová IP adresa.


## <a name="design-guidance"></a>Doprovodné materiály k návrhu

V této části se seznámíte s důležitými informacemi pro navrhování virtuálních sítí pomocí překladu adres (NAT).  

1. [Optimalizace nákladů](#cost-optimization)
1. [Koexistence příchozích a odchozích](#coexistence-of-inbound-and-outbound)
2. [Správa základních prostředků](#managing-basic-resources)
3. [Zóny dostupnosti](#availability-zones)

### <a name="cost-optimization"></a>Optimalizace nákladů

[Koncové body služby](virtual-network-service-endpoints-overview.md) a [soukromé odkazy](../private-link/private-link-overview.md) jsou dvě možnosti, které je potřeba vzít v úvahu pro optimalizaci nákladů, u kterých není potřeba NAT.  Přenos dat směrovaný do koncových bodů služby nebo privátního propojení se nezpracovává pomocí překladu adres (NAT) virtuální sítě.  

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
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtual Network překlad adres (NAT) pro odchozí připojení do Internetu">
</p>

*Obrázek: Virtual Network NAT pro odchozí připojení do Internetu*

Scénář jenom pro odchozí připojení k Internetu poskytovaný bránou NAT se dá rozšířit o funkce z Internetu. Každý prostředek ví o směru, ve kterém se tok nacházel. V podsíti s bránou NAT je všechny odchozí a internetové scénáře nahrazené bránou NAT. Příchozí z internetových scénářů poskytuje příslušný prostředek.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>NAT a virtuální počítač s veřejnou IP adresou na úrovni instance

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" width="300" title="Virtual Network NAT a virtuální počítač s veřejnou IP adresou na úrovni instance">
</p>

*Obrázek: Virtual Network NAT a virtuální počítač s veřejnou IP adresou na úrovni instance*

| Směr | Prostředek |
|:---:|:---:|
| Příchozí | Virtuální počítač s veřejnou IP adresou na úrovni instance |
| Odchozí | NAT Gateway |

Virtuální počítač bude používat bránu NAT pro odchozí připojení.  Příchozí původ není ovlivněn.

#### <a name="nat-and-vm-with-public-load-balancer"></a>NAT a virtuální počítač s veřejným Load Balancer

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" width="350" title="Virtual Network NAT a virtuální počítač s veřejným Load Balancer">
</p>

*Obrázek: Virtual Network NAT a virtuální počítač s veřejným Load Balancer*

| Směr | Prostředek |
|:---:|:---:|
| Příchozí | veřejné Load Balancer |
| Odchozí | NAT Gateway |

Jakákoli odchozí konfigurace z pravidla vyrovnávání zatížení nebo odchozích pravidel je nahrazena bránou NAT.  Příchozí původ není ovlivněn.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-public-load-balancer"></a>NAT a virtuální počítač s veřejnou IP adresou na úrovni instance a veřejnými Load Balancer

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" width="425" title="Virtual Network NAT a virtuální počítač s veřejnou IP adresou na úrovni instance a veřejnými Load Balancer">
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

I bez zón dostupnosti je NAT odolné a může zamezit několik selhání součástí infrastruktury. Pokud jsou zóny dostupnosti součástí vašeho scénáře, měli byste nakonfigurovat překlad adres (NAT) pro konkrétní zónu.  Operace roviny ovládacího prvku a rovina dat jsou omezeny na zadanou zónu. Selhání v jiné oblasti, než kde váš scénář existuje, by mělo být bez dopadu na překlad adres (NAT). Odchozí přenosy z virtuálních počítačů ve stejné zóně selžou z důvodu izolace zóny.

<p align="center">
  <img src="media/nat-overview/az-directions.svg" width="425" title="Virtual Network překlad adres (NAT) se zónami dostupnosti">
</p>

*Obrázek: Virtual Network překlad adres (NAT) se zónami dostupnosti*

Brána NAT izolovaná na zóně vyžaduje, aby IP adresy odpovídaly zóně brány NAT. Prostředky brány NAT s IP adresami z jiné zóny nebo bez zóny nejsou podporované.

Virtuální sítě a podsítě jsou regionální a nikoli zarovnané na oblast. Virtuální počítač musí být ve stejné zóně jako brána NAT pro příslib odchozích připojení. Izolace zóny se vytvoří vytvořením oblasti "zásobníku" na jednu zónu dostupnosti. Příslib oblasti oblastí neexistují při překřížených zónách brány NAT nebo pomocí místní brány NAT s virtuálními počítači.

Když nasadíte služby Virtual Machine Scale Sets pro použití s překladem adres (NAT), nasadíte na vlastní podsíť sadu škálování s možností škálování a přiřadíte ji k této podsíti odpovídající bráně NAT. Pokud používáte škálované hraniční sady (sadu škálování ve dvou nebo více zónách), překlad adres (NAT) nebude poskytovat příslib oblasti.  Překlad adres (NAT) nepodporuje redundanci zón.  Podporovaná je jenom regionální nebo izolovaný izolaci zón.

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" width="425" title="Virtual Network NAT pokrývání zón">
</p>

*Obrázek: Virtual Network NAT zahrnující zóny*

Vlastnost Zones není proměnlivá.  Znovu nasaďte prostředek brány NAT pomocí zamýšleného regionu nebo Předvolby zóny.

>[!NOTE] 
>IP adresy samy sebe nejsou v zóně – redundantní, pokud není zadaná žádná zóna.  Front [-end Standard Load Balancer je redundantní v zóně](../load-balancer/load-balancer-standard-availability-zones.md#frontend) , pokud IP adresa není vytvořená v konkrétní zóně.  To se nevztahuje na překlad adres (NAT).  Podporovaná je jenom regionální nebo izolovaný izolaci zón.

## <a name="source-network-address-translation"></a>Překlad zdrojové síťové adresy

Zdrojový překlad adres (SNAT) přepíše zdroj toku, který má být vytvořen z jiné IP adresy.  Prostředky brány NAT používají variantu SNAT, která se běžně označuje jako na překladu adres portů (PAT). PAT přepíše zdrojovou a zdrojový port. V SNAT není žádný pevný vztah mezi počtem privátních adres a jejich přeloženými veřejnými adresami.  

### <a name="fundamentals"></a>Základy

Pojďme se podívat na příklad čtyř toků a vysvětlit základní koncept.  Brána NAT používá 65.52.0.2 prostředku s veřejnou IP adresou.

| Tok | Zdrojová řazená kolekce členů | Cílová řazená kolekce členů |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

Tyto toky můžou vypadat takto po přijetí PAT:

| Tok | Zdrojová řazená kolekce členů | SNAT'ed zdrojová řazená kolekce členů | Cílová řazená kolekce členů | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.2:234 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.2:235 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.2:236 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:237 | 65.52.0.2:80 |

Cíl se zobrazí jako 65.52.0.2 (zdrojová řazená kolekce členů na SNAT) se zobrazeným přiřazeným portem.  PAT, jak je znázorněno v předchozí tabulce, se označuje také jako maskování portů SNAT.  Několik privátních zdrojů je maskovaných za IP adresou a portem.

Neprovádějte závislost na konkrétním způsobu, jakým jsou přiřazeny zdrojové porty.  Předchozí je příkladem základního konceptu.

SNAT, kterou poskytuje překlad adres (NAT), se liší od [Load Balancer](../load-balancer/load-balancer-outbound-connections.md) v několika aspektech.

### <a name="on-demand"></a>Na vyžádání

NAT poskytuje porty SNAT na vyžádání pro nové toky odchozího provozu. Všechny dostupné porty SNAT v inventáři používá libovolný virtuální počítač v podsítích konfigurovaných pomocí překladu adres (NAT). 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" width="550" title="Odchozí SNAT na vyžádání Virtual Network překladu adres (NAT)">
</p>

*Obrázek: Virtual Network NAT na vyžádání odchozí SNAT*

Jakákoli konfigurace IP virtuálního počítače může v případě potřeby vytvářet odchozí toky na vyžádání.  Plánování předběžného přidělování za instance, včetně nadměrného zřízení případných případů, se nevyžaduje.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" width="550" title="Rozdíly ve scénářích vyčerpání">
</p>

*Obrázek: rozdíly ve scénářích vyčerpání*

Po vydání portu SNAT je možné ho použít pro libovolný virtuální počítač v podsítích nakonfigurovaných pomocí překladu adres (NAT).  Přidělení na vyžádání umožňuje dynamickým a odlišným úlohám v podsítích používat porty SNAT podle potřeby.  Pokud je k dispozici inventář portů SNAT, budou toky SNAT úspěšné. Z většího inventáře místo toho zvýhodněné body SNAT z provozu. Porty SNAT nejsou nepoužitelné pro virtuální počítače, které je aktivně nepotřebují.

### <a name="scaling"></a>Škálování

Překlad adres (NAT) potřebuje pro kompletní odchozí scénář dostatek inventáře portů SNAT. Škálování NAT je primárně funkcí správy sdíleného, dostupného inventáře portů SNAT. Aby bylo možné adresovat výstupní odchozí tok pro všechny podsítě připojené k prostředku brány NAT, musí existovat dostatečný inventář.

SNAT namapuje několik privátních adres na jednu veřejnou adresu a k škálování používá víc veřejných IP adres.

Prostředek brány NAT bude používat porty 64 000 (porty SNAT) veřejné IP adresy.  Tyto porty SNAT se stanou dostupnými inventáři pro mapování privátního toku na veřejné. A přidáním dalších veřejných IP adres zvýšíte dostupné porty SNAT inventáře. Prostředky brány NAT můžou škálovat až na 16 IP adres a 1 milion portů SNAT.  TCP a UDP jsou samostatné inventáře portů SNAT a nesouvisející.

Prostředky brány NAT oportunisticky znovu použít zdrojové porty. Pro účely škálování byste měli předpokládat, že každý tok vyžaduje nový port SNAT a škálovat celkový počet dostupných IP adres pro odchozí provoz.

### <a name="protocols"></a>Protokoly

Prostředky brány NAT komunikují s hlavičkami IP a IP přenosů protokolů UDP a TCP a nezávislá se do datových částí aplikační vrstvy.  Jiné protokoly IP nejsou podporovány.

### <a name="timers"></a>Časovače

Časový limit nečinnosti lze upravit ze 4 minut (výchozí) na 120 minut (2 hodiny) pro všechny toky.  Kromě toho můžete časovač nečinnosti resetovat pomocí provozu toku.  Doporučený vzor pro obnovení dlouhých nečinných připojení a zjišťování živých koncových bodů je udržení naživu v TCP.  Kontroly zatížení sítě TCP se zobrazují jako duplicitní potvrzení koncových bodů, jsou nízká režie a neviditelná pro vrstvu aplikace.

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
- Rodina adres IPv4 je podporovaná.  Překlad adres (NAT) nekomunikuje s řadou IPv6 adres.
- Protokolování toku NSG se při použití překladu adres (NAT) nepodporuje.
- Překlad adres (NAT) nemůže zahrnovat víc virtuálních sítí.

## <a name="preview-participation"></a>Účast ve verzi Preview

Podle [pokynů povolte své předplatné](nat-overview.md#public-preview-participation).

## <a name="feedback"></a>Váš názor

Chceme zjistit, jak můžeme službu vylepšit. Sdílejte svůj [názor na Public Preview](https://aka.ms/natfeedback) s námi.  A můžete navrhnout a hlasovat o tom, co by se mělo na webu [UserVoice pro překlad adres (NAT)](https://aka.ms/natuservoice)sestavit dál.

## <a name="next-steps"></a>Další kroky

- Další informace o [překladu adres (NAT) pro virtuální sítě](nat-overview.md)
- Kurz pro ověření brány NAT
  * Rozhraní příkazového [řádku Azure](tutorial-create-validate-nat-gateway-cli.md)
  * [PowerShell](tutorial-create-validate-nat-gateway-cli.md),
  * [Azure Portal](tutorial-create-validate-nat-gateway-cli.md)
- Rychlý Start pro nasazení prostředku brány NAT
  * Rozhraní příkazového [řádku Azure](./quickstart-create-nat-gateway-cli.md)
  * [PowerShell](./quickstart-create-nat-gateway-powershell.md),
  * [Portál](./quickstart-create-nat-gateway-portal.md).
- Přečtěte si další informace o [zónách dostupnosti](../availability-zones/az-overview.md).
- Přečtěte si další informace o [službě Load Balancer úrovně Standard](../load-balancer/load-balancer-standard-overview.md).
- Přečtěte si další informace o [zónách dostupnosti a standardním nástroji pro vyrovnávání zatížení](../load-balancer/load-balancer-standard-availability-zones.md).
- Další informace o rozhraní API prostředků brány NAT
  * [REST API](https://docs.microsoft.com/rest/api/virtualnetwork/natgateways),
  * Rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/network/nat/gateway?view=azure-cli-latest)
  * [PowerShell](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway).
- [Řekněte nám, co se má sestavit dál ve službě UserVoice](https://aka/natuservoice).
- [Poskytněte zpětnou vazbu k Public Preview](https://aka.ms/natfeedback).
