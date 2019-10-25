---
title: Odchozí pravidla v Azure Load Balancer
titlesuffix: Azure Load Balancer
description: Použití odchozích pravidel k definování odchozích překladů síťových adres
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 7/17/2019
ms.author: allensu
ms.openlocfilehash: 9fc9eb347e97fe6ab57b3e30651e4ea77a4ce9c8
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790237"
---
# <a name="load-balancer-outbound-rules"></a>Load Balancer odchozí pravidla

Azure Load Balancer poskytuje kromě příchozího i odchozí připojení z virtuální sítě.  Odchozí pravidla usnadňují konfiguraci odchozího překladu síťových adres pro veřejné [Standard Load Balancer](load-balancer-standard-overview.md).  Máte plnou deklarativní kontrolu nad odchozím připojením, abyste mohli škálovat a ladit tuto možnost podle vašich konkrétních potřeb.

![Load Balancer odchozí pravidla](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Pomocí odchozích pravidel můžete použít Load Balancer k těmto akcím: 
- definice odchozího překladu adres (NAT) od začátku
- Škálujte a optimalizujte chování stávajícího odchozího překladu adres (NAT). 

Odchozí pravidla umožňují řídit:
- které virtuální počítače by se měly přeložit na které veřejné IP adresy. 
- Jak by měly být přiděleny [Odchozí porty SNAT](load-balancer-outbound-connections.md#snat) .
- které protokoly pro zajištění odchozího překladu pro.
- Jaká doba se má použít pro časový limit nečinnosti odchozího připojení (4-120 minut).
- Určuje, zda má být pro časový limit nečinnosti (v Public Preview) odesláno resetování protokolu TCP. 

Odchozí pravidla rozbalí [scénář 2](load-balancer-outbound-connections.md#lb) , který je popsaný v článku o [odchozích připojeních](load-balancer-outbound-connections.md) , a Priorita scénáře zůstane tak, jak je.

## <a name="outbound-rule"></a>Odchozí pravidlo

Stejně jako všechna pravidla Load Balancer se odchozí pravidla řídí stejnou známou syntaxí jako vyrovnávání zatížení a příchozí pravidla překladu adres (NAT):

**parametry** + **front-endu** + **back-end fondu**

Odchozí pravidlo konfiguruje odchozí překlad adres (NAT) pro _všechny virtuální počítače identifikované fondem back-end_ pro překlad do _front-endu_.  _Parametry_ a poskytují další jemně odstupňovanou kontrolu nad odchozím algoritmem NAT.

Verze rozhraní API "2018-07-01" umožňuje strukturované definování odchozích pravidel takto:

```json
      "outboundRules": [
        {
          "frontendIPConfigurations": [ list_of_frontend_ip_configuations ],
          "allocatedOutboundPorts": number_of_SNAT_ports,
          "idleTimeoutInMinutes": 4 through 66,
          "enableTcpReset": true | false,
          "protocol": "Tcp" | "Udp" | "All",
          "backendAddressPool": backend_pool_reference,
        }
      ]
```

>[!NOTE]
>Platná konfigurace odchozího překladu adres (NAT) je složený ze všech odchozích pravidel a pravidel vyrovnávání zatížení. Odchozí pravidla jsou přírůstková na pravidla vyrovnávání zatížení. Přečtěte si téma [zakázání odchozího překladu adres (NAT) pro pravidlo vyrovnávání zatížení](#disablesnat) pro správu efektivního překladu NAT, když se na virtuální počítač vztahuje víc pravidel Při definování odchozího pravidla, které používá stejnou veřejnou IP adresu jako pravidlo vyrovnávání zatížení, je nutné [Zakázat odchozí SNAT](#disablesnat) .

### <a name="scale"></a>Škálování odchozího překladu adres (NAT) s několika IP adresami

I když se odchozí pravidlo dá použít jenom s jednou veřejnou IP adresou, odchozí pravidla zjednodušují zátěž v konfiguraci pro škálování odchozího překladu adres (NAT). Pro plánování rozsáhlých scénářů můžete použít více IP adres a pomocí odchozích pravidel můžete zmírnit vzory náchylné k [vyčerpání SNAT](load-balancer-outbound-connections.md#snatexhaust) .  

Každá další IP adresa poskytovaná front-endu poskytuje 64 000 dočasných portů, které Load Balancer použít jako porty SNAT. I když mají pravidla vyrovnávání zatížení nebo příchozího překladu adres (NAT) jeden front-end, toto pravidlo rozšíří rozhraní front-end a umožní více front-endu na jedno pravidlo.  U několika front-endu na pravidlo je množství dostupných portů SNAT vynásobeno každou veřejnou IP adresou a může být podporováno velké scénáře.

Kromě toho můžete použít [předponu veřejné IP adresy](https://aka.ms/lbpublicipprefix) přímo s odchozím pravidlem.  Použití předpony veřejných IP adres poskytuje snazší škálování a zjednodušený seznam toků pocházejících z nasazení Azure. Konfiguraci IP adresy front-endu můžete v rámci prostředku Load Balancer nakonfigurovat tak, aby odkazovala přímo na předponu veřejné IP adresy.  To umožňuje Load Balancer exkluzivní kontrolu nad předponou veřejné IP adresy a odchozí pravidlo automaticky použije všechny veřejné IP adresy obsažené v předponě veřejných IP adres pro odchozí připojení.  Jednotlivé IP adresy v rozsahu předpony veřejných IP adres poskytují 64 000 dočasných portů na IP adresu Load Balancer použít jako porty SNAT.   

Při použití této možnosti nemůžete mít jednotlivé prostředky veřejné IP adresy vytvořené z předpony veřejných IP adres, protože odchozí pravidlo musí mít úplnou kontrolu nad předponou veřejné IP adresy.  Pokud potřebujete přesnější kontrolu, můžete vytvořit samostatný prostředek veřejné IP adresy z předpony veřejné IP adresy a jednotlivě přiřadit více veřejných IP adres do front-endu odchozího pravidla.

### <a name="snatports"></a>Vyladit alokaci portů SNAT

Pomocí odchozích pravidel můžete vyladit [automatické přidělování portů SNAT na základě velikosti fondu back-endu](load-balancer-outbound-connections.md#preallocatedports) a přidělit více nebo méně, než jaké automatické přidělování portů SNAT nabízí.

K přidělení portů 10 000 SNAT na virtuální počítač (konfigurace IP adresy NIC) použijte následující parametr.
 

          "allocatedOutboundPorts": 10000

Každá veřejná IP adresa ze všech front-endu odchozího pravidla přispívá až 64 000 dočasných portů, které se použijí jako porty SNAT.  Load Balancer přiděluje porty SNAT v násobcích 8. Pokud zadáte hodnotu, která není dělitelná 8, operace konfigurace se odmítne.  Pokud se pokusíte přidělit více portů SNAT, než je k dispozici na základě počtu veřejných IP adres, je operace konfigurace odmítnuta.  Pokud například přidělíte porty 10 000 na virtuální počítač a 7 virtuálních počítačů ve fondu back-end, bude se jednat o jednu veřejnou IP adresu, konfigurace se odmítne (7 x 10 000 SNAT portů > 64 000 portů SNAT).  Můžete přidat další veřejné IP adresy do front-endu odchozího pravidla a povolit tak scénář.

Můžete vrátit zpět na [automatické přidělování portů SNAT na základě velikosti fondu back-endu](load-balancer-outbound-connections.md#preallocatedports) zadáním 0 pro počet portů.

### <a name="idletimeout"></a>Řízení časového limitu nečinnosti odchozího toku

Odchozí pravidla poskytují konfigurační parametr pro řízení časového limitu nečinnosti odchozího toku a jejich párování s požadavky vaší aplikace.  Výchozí časový limit nečinnosti ve výchozím nastavení na 4 minuty.  Parametr přijímá hodnotu od 4 do 120 pro určitý počet minut pro časový limit nečinnosti pro toky, které odpovídají tomuto konkrétnímu pravidlu.

Pro nastavení odchozího časového limitu nečinnosti na 1 hodinu použijte následující parametr:

          "idleTimeoutInMinutes": 60

### <a name="tcprst"></a><a name="tcpreset"></a> Povolit resetování protokolu TCP při nečinnosti časového limitu (Preview)

Výchozím chováním Load Balancer je vyřazení toku v tichém režimu v případě dosažení odchozího časového limitu nečinnosti.  Pomocí parametru enableTCPReset můžete povolit předvídatelné chování aplikace a určit, jestli se má v době nečinnosti odchozího nečinnosti odesílat obousměrné resetování protokolu TCP (TCP RST). 

Pro povolení resetování protokolu TCP u odchozího pravidla použijte následující parametr:

           "enableTcpReset": true

Podrobnosti o dostupnosti v oblasti najdete v podrobnostech o [vypršení platnosti protokolu TCP při nečinnosti (Preview)](https://aka.ms/lbtcpreset) .

### <a name="proto"></a>Podpora přenosových protokolů TCP i UDP s jedním pravidlem

Pro transportní protokol odchozího pravidla pravděpodobně budete chtít použít "vše", ale můžete také použít odchozí pravidlo na konkrétní transportní protokol a také v případě, že to je potřeba udělat.

Pomocí následujícího parametru nastavte protokol na TCP a UDP:

          "protocol": "All"

### <a name="disablesnat"></a>Zakázat odchozí překlad adres (NAT) pro pravidlo vyrovnávání zatížení

Jak bylo uvedeno dříve, pravidla vyrovnávání zatížení poskytují automatické programování odchozího překladu adres (NAT). Některé scénáře ale mají výhodu nebo vyžadují, abyste v rámci pravidla vyrovnávání zatížení zakázali automatické programování odchozího překladu adres (NAT), které vám umožní ovládací prvek nebo Upřesnit jeho chování.  Odchozí pravidla mají scénáře, kde je důležité zastavit automatické odchozí připojení NAT.

Tento parametr můžete použít dvěma způsoby:
- Volitelné potlačení používání příchozí IP adresy pro odchozí překlad adres (NAT).  Odchozí pravidla jsou přírůstková na pravidla vyrovnávání zatížení a s touto sadou parametrů je odchozí pravidlo řízení.
  
- Vyladění odchozích parametrů NAT pro IP adresu, která se používá pro příchozí a odchozí provoz.  Aby bylo možné převzít řízení odchozího pravidla, musí být toto automatické programování NAT zakázané.  Chcete-li například změnit přidělení portu SNAT adresy, která se používá také pro příchozí, musí být tento parametr nastaven na hodnotu true.  Pokud se pokusíte použít odchozí pravidlo k předefinování parametrů IP adresy, která se používá také pro příchozí a která neuvolnila odchozí programování služby NAT pravidla vyrovnávání zatížení, operace Konfigurace odchozího pravidla se nezdaří.

>[!IMPORTANT]
> Pokud nastavíte tento parametr na hodnotu true a nemáte odchozí pravidlo (nebo [scénář veřejné IP adresy na úrovni instance](load-balancer-outbound-connections.md#ilpip) pro definování odchozího připojení), váš virtuální počítač nebude mít odchozí připojení.  Některé operace virtuálního počítače nebo vaší aplikace můžou záviset na dostupnosti odchozího připojení. Ujistěte se, že rozumíte závislostem vašeho scénáře a že se tyto změny považují za důsledky.

Odchozí SNAT pro pravidlo vyrovnávání zatížení můžete zakázat s tímto parametrem konfigurace:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

Parametr disableOutboundSNAT má výchozí hodnotu false, což znamená, že pravidlo vyrovnávání **zatížení poskytuje automatické** odchozí překlad adres (NAT) jako zrcadlový obraz konfigurace pravidla vyrovnávání zatížení.  

Pokud nastavíte disableOutboundSnat na hodnotu true pro pravidlo vyrovnávání zatížení, pravidlo vyrovnávání zatížení vydává kontrolu nad jinak automatické programování odchozího překladu adres (NAT).  Odchozí SNAT v důsledku pravidla vyrovnávání zatížení je zakázané.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Znovu použít existující nebo definovat nové back-endové fondy

Odchozí pravidla nezavádí nový koncept pro definování skupiny virtuálních počítačů, na které by se mělo pravidlo vztahovat.  Místo toho znovu použijí koncept fondu back-end, který se používá také pro pravidla vyrovnávání zatížení. Tuto možnost můžete použít ke zjednodušení konfigurace buď opětovným použitím existující definice back-end fondu, nebo vytvořením speciálně pro odchozí pravidlo.

## <a name="scenarios"></a>Scénáře

### <a name="groom"></a>Odchozí připojení výmazu dat ke konkrétní sadě veřejných IP adres

Můžete použít odchozí pravidlo pro vymazání odchozích připojení, která se mají vyskytnout z konkrétní sady veřejných IP adres, aby se usnadnily scénáře přidávání na seznam povolených.  Tato veřejná IP adresa se může shodovat s použitím pravidla vyrovnávání zatížení nebo jiné sady veřejných IP adres, než je používá pravidlo vyrovnávání zatížení.  

1. Vytvořit [předponu veřejné IP](https://aka.ms/lbpublicipprefix) adresy (nebo veřejné IP adresy z předpony veřejných IP adres)
2. Vytvoření veřejného Load Balanceru úrovně Standard
3. Vytvoření front-endu odkazujících na předponu veřejné IP adresy (nebo veřejné IP adresy), které chcete použít
4. Znovu použijte back-end fond nebo vytvořte fond back-end a umístěte virtuální počítače do back-endového fondu veřejné Load Balancer
5. Konfigurace odchozího pravidla na veřejném Load Balancer pro programování odchozího překladu adres (NAT) pro tyto virtuální počítače pomocí front-endu
   
Pokud nechcete, aby se pravidlo vyrovnávání zatížení používalo pro odchozí připojení, musíte [Zakázat odchozí SNAT](#disablesnat) v pravidle vyrovnávání zatížení.

### <a name="modifysnat"></a>Úprava přidělení portu SNAT

Pomocí odchozích pravidel můžete vyladit [automatické přidělování portů SNAT na základě velikosti fondu back-endu](load-balancer-outbound-connections.md#preallocatedports).

Pokud například máte dva virtuální počítače sdílející jednu veřejnou IP adresu pro odchozí NAT, možná budete chtít zvýšit počet portů SNAT přidělených z výchozích portů 1024, pokud máte vyčerpání SNAT. Každá veřejná IP adresa může přispívat až 64 000 dočasných portů.  Pokud nakonfigurujete odchozí pravidlo s jednou front-endu veřejné IP adresy, můžete do virtuálních počítačů ve fondu back-endu distribuovat celkem 64 000 portů SNAT.  Pro dva virtuální počítače se dá přidělit maximálně 32 000 portů SNAT pomocí odchozího pravidla (2x 32 000 = 64 000).

Zkontrolujte [odchozí připojení](load-balancer-outbound-connections.md) a podrobnosti o přidělování a používání portů [SNAT](load-balancer-outbound-connections.md#snat) .

### <a name="outboundonly"></a>Povolit pouze odchozí

K poskytování odchozího překladu adres (NAT) pro skupinu virtuálních počítačů můžete použít veřejné Standard Load Balancer. V tomto scénáři můžete použít odchozí pravidlo samostatně, aniž byste museli mít žádná další pravidla.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>Odchozí překlad adres (NAT) pouze pro virtuální počítače (žádné příchozí)

Definujte veřejné Standard Load Balancer, umístěte virtuální počítače do back-endu fondu a nakonfigurujte odchozí pravidlo pro programování odchozího překladu adres (NAT) a výmaz dat z konkrétní veřejné IP adresy. Můžete také použít předponu veřejné IP adresy zjednodušit výpis zdroje odchozích připojení.

1. Vytvořte veřejnou Standard Load Balancer.
2. Vytvořte back-end fond a umístěte virtuální počítače do back-endového fondu veřejné Load Balancer.
3. Nakonfigurujte odchozí pravidlo na veřejném Load Balancer pro programování odchozího překladu adres (NAT) pro tyto virtuální počítače.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>Odchozí překlad adres (NAT) pro interní Standard Load Balancer scénáře

Při použití interního Standard Load Balancer není odchozí překlad adres (NAT) dostupný, dokud není explicitně deklarované odchozí připojení. Odchozí připojení můžete definovat pomocí odchozího pravidla pro vytvoření odchozího připojení pro virtuální počítače za interním Standard Load Balancer s těmito kroky:

1. Vytvořte veřejnou Standard Load Balancer.
2. Vytvořte fond back-end a umístěte virtuální počítače do back-endového fondu veřejných Load Balancer kromě interní Load Balancer.
3. Nakonfigurujte odchozí pravidlo na veřejném Load Balancer pro programování odchozího překladu adres (NAT) pro tyto virtuální počítače.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Povolit protokoly TCP & UDP pro odchozí překlad adres (NAT) s veřejným Standard Load Balancer

- Při použití veřejné Standard Load Balancer se zadané automatické odchozí přenosy NAT shodují s transportním protokolem pravidla vyrovnávání zatížení.  

   1. Zakáže odchozí SNAT u pravidla vyrovnávání zatížení.
   2. Nakonfigurujte odchozí pravidlo pro stejné Load Balancer.
   3. Znovu použijte back-end fond, který už používá vaše virtuální počítače.
   4. Jako součást odchozího pravidla zadejte "protokol": "vše".

- Když se používají jenom pravidla příchozího překladu adres (NAT), neposkytne se žádné odchozí NAT.

   1. Umístěte virtuální počítače do back-endového fondu.
   2. Definujte jednu nebo víc konfigurací IP front-endu s veřejnými IP adresami nebo předponou veřejné IP adresy.
   3. Nakonfigurujte odchozí pravidlo pro stejné Load Balancer.
   4. Zadejte "protokol": "All" jako součást odchozího pravidla

## <a name="limitations"></a>Omezení

- Maximální počet použitelných dočasných portů na IP adresu front-endu je 64 000.
- Rozsah konfigurovatelného odchozího časového limitu nečinnosti je 4 až 120 minut (240 až 7200 sekund).
- Load Balancer nepodporuje protokol ICMP pro odchozí překlad adres (NAT).
- Portál nelze použít ke konfiguraci nebo zobrazení odchozích pravidel.  Místo toho použijte šablony REST API AZ CLI 2,0 nebo PowerShell.
- Odchozí pravidla se dají použít jenom u primární konfigurace IP adresy síťového adaptéru.  Podporuje se víc síťových adaptérů.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o použití [Load Balancer pro odchozí připojení](load-balancer-outbound-connections.md).
- Přečtěte si o [Standard Load Balancer](load-balancer-standard-overview.md).
- Přečtěte si o [Nastavení obousměrného protokolu TCP při nečinnosti](load-balancer-tcp-reset.md).
- [Nakonfigurujte odchozí pravidla pomocí Azure CLI 2,0](configure-load-balancer-outbound-cli.md).
