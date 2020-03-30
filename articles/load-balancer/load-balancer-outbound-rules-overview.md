---
title: Odchozí pravidla – Azure Load Balancer
description: Pomocí tohoto studijního programu začněte používat odchozí pravidla k definování překladů odchozích síťových adres.
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
ms.openlocfilehash: d419c213b3bcfef3631d68eb9d4cb485291bed31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304187"
---
# <a name="load-balancer-outbound-rules"></a>Odchozí pravidla vykladače zatížení

Azure Load Balancer poskytuje odchozí připojení z virtuální sítě kromě příchozí.  Odchozí pravidla usnadňují konfiguraci překladu odchozích síťových adres [služby Public Standard Load Balancer.](load-balancer-standard-overview.md)  Máte plnou deklarativní kontrolu nad odchozí připojení škálovat a optimalizovat tuto schopnost na vaše konkrétní potřeby.

![Odchozí pravidla vykladače zatížení](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Pomocí odchozích pravidel můžete pomocí vykladače zatížení: 
- definujte odchozí NAT od nuly.
- škálujte a vyladěte chování existujícího odchozího nat. 

Odchozí pravidla umožňují řídit:
- které virtuální počítače by měly být přeloženy na které veřejné IP adresy. 
- jak by měly být [přiděleny odchozí porty SNAT.](load-balancer-outbound-connections.md#snat)
- protokoly, pro které mají být poskytnuty odchozí překlady.
- jakou dobu trvání použít pro časový limit nečinnosti odchozího připojení (4-120 minut).
- zda má být při nečinnosti odeslán onulovací protokol TCP Reset

Odchozí pravidla rozbalí [scénář 2](load-balancer-outbound-connections.md#lb) popsaný v článku [odchozí připojení](load-balancer-outbound-connections.md) a priorita scénáře zůstane tak, jak je.

## <a name="outbound-rule"></a>Odchozí pravidlo

Stejně jako všechna pravidla pro vyrovnávání zatížení se odchozí pravidla řídí stejnou známou syntaxí jako pravidla vyrovnávání zatížení a příchozího nat:

**front-endové** + **parametry** + **back-end fondu**

Odchozí pravidlo konfiguruje odchozí PŘEKLAD DAT pro _všechny virtuální počítače identifikované back-endovým fondem,_ aby byly přeloženy do _front-endu_.  A _parametry_ poskytují další jemně odstupňované řízení nad algoritmus odchozí NAT.

Verze rozhraní API "2018-07-01" umožňuje definici odchozího pravidla strukturovanou takto:

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
>Efektivní konfigurace odchozího nat je složena ze všech odchozích pravidel a pravidel vyrovnávání zatížení. Odchozí pravidla jsou přírůstková pro pravidla vyrovnávání zatížení. Zkontrolujte [zakázání odchozího překladu ADRES pro pravidlo vyrovnávání zatížení,](#disablesnat) abyste mohli spravovat efektivní překlad odchozího překladu NAT, když se na virtuální počítač vztahuje více pravidel. Při definování odchozího pravidla, které používá stejnou veřejnou IP adresu jako pravidlo vyrovnávání zatížení, je nutné [zakázat odchozí snat.](#disablesnat)

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a>Škálování odchozího NAT s více IP adresami

Zatímco odchozí pravidlo lze použít pouze s jednou veřejnou IP adresou, odchozí pravidla usnadňují konfigurační zátěž pro škálování odchozího NAT. Můžete použít více IP adres k plánování pro rozsáhlé scénáře a můžete použít odchozí pravidla ke zmírnění vzorců náchylných k [vyčerpání SNAT.](load-balancer-outbound-connections.md#snatexhaust)  

Každá další IP adresa poskytovaná front-endem poskytuje 64 000 dočasných portů pro vykladač zatížení, které lze použít jako porty SNAT. Zatímco vyrovnávání zatížení nebo příchozí pravidla NAT mají jeden front-end, odchozí pravidlo rozbalí pojem front-endu a povolí více front-endů na pravidlo.  S více front-endů na pravidlo se množství dostupných portů SNAT násobí každou veřejnou IP adresou a mohou být podporovány velké scénáře.

Kromě toho můžete použít [veřejnou předponu IP](https://aka.ms/lbpublicipprefix) přímo s odchozím pravidlem.  Použití veřejné IP předpony zajišťuje snadnější škálování a zjednodušené bílé výpistotototovy pocházející z vašeho nasazení Azure. Konfiguraci ip adresy front-end u prostředku nástroje pro vyrovnávání zatížení můžete nakonfigurovat tak, aby přímo odkazovala na předponu veřejné IP adresy.  To umožňuje výhradní kontrolu vykladače zatížení nad veřejnou předponou IP a odchozí pravidlo automaticky použije všechny veřejné IP adresy obsažené ve veřejné předponě IP pro odchozí připojení.  Každá adresa IP v rozsahu veřejné předpony IP poskytuje 64 000 dočasných portů na ip adresu pro vyvažovač elánu s vyrovnáváním zatížení, které se mají použít jako porty SNAT.   

Při použití této možnosti nelze mít vytvořené jednotlivé prostředky veřejné IP adresy, protože odchozí pravidlo musí mít úplnou kontrolu nad veřejnou předponou IP.  Pokud potřebujete více jemně odstupňovaného ovládacího prvku, můžete vytvořit jednotlivé prostředky veřejné IP adresy z veřejné předpony IP a přiřadit více veřejných IP adres jednotlivě front-endu odchozího pravidla.

### <a name="tune-snat-port-allocation"></a><a name="snatports"></a>Naladění přidělení portu SNAT

Odchozí pravidla můžete použít k vyladění [automatického přidělení portu SNAT na základě velikosti back-endového fondu](load-balancer-outbound-connections.md#preallocatedports) a přidělit více nebo méně, než poskytuje automatické přidělení portu SNAT.

Následující parametr slouží k přidělení 10 000 portů SNAT na virtuální virtuální modul (konfigurace IP rozhraní).
 

          "allocatedOutboundPorts": 10000

Každá veřejná IP adresa ze všech frontendů odchozího pravidla přispívá až k 64 000 dočasným portům pro použití jako porty SNAT.  Balancer zatížení přiděluje porty SNAT v násobcích 8. Pokud zadáte hodnotu, která není dělitelná 8, operace konfigurace je odmítnuta.  Pokud se pokusíte přidělit více portů SNAT, než je k dispozici na základě počtu veřejných IP adres, operace konfigurace bude odmítnuta.  Pokud například přidělíte 10 000 portů na virtuální počítače a 7 virtuálních počítačů v back-endovém fondu bude sdílet jednu veřejnou IP adresu, konfigurace se odmítne (7 x 10 000 portů SNAT > 64 000 portů SNAT).  Můžete přidat další veřejné IP adresy do frontendu odchozí pravidlo povolit scénář.

Můžete se vrátit zpět k [automatickému přidělení portu SNAT na základě velikosti back-endového fondu](load-balancer-outbound-connections.md#preallocatedports) zadáním 0 pro počet portů. V takovém případě prvních 50 instancí virtuálních počítačů získá 1024 portů, 51-100 instancí virtuálních počítačů získá 512 a tak dále podle tabulky.

### <a name="control-outbound-flow-idle-timeout"></a><a name="idletimeout"></a>Časový limit nečinnosti odchozího toku

Odchozí pravidla poskytují parametr konfigurace pro řízení časového limitu nečinnosti odchozího toku a jeho přizpůsobení potřebám vaší aplikace.  Odchozí časové limity nečinnosti jsou výchozí na 4 minuty.  Parametr přijímá hodnotu od 4 do 120 k určité počet minut pro časový limit nečinnosti pro toky odpovídající tok ům odpovídající toto konkrétní pravidlo.

Následující parametr slouží k nastavení časového limitu odchozí horečné činnosti na 1 hodinu:

          "idleTimeoutInMinutes": 60

### <a name="enable-tcp-reset-on-idle-timeout"></a><a name="tcprst"></a><a name="tcpreset"></a> Povolit reset protokolu TCP při časovém limitu nečinnosti

Výchozí chování Balancer je přetažení toku tiše po dosažení časového limitu odchozí nečinnosti.  S parametrem enableTCPReset můžete povolit předvídatelnější chování aplikace a řídit, zda chcete odeslat obousměrný protokol TCP Reset (TCP RST) v době mimo časový limit odchozí horečné nečinnosti. 

Pomocí následujícího parametru povolte protokol TCP Reset u odchozího pravidla:

           "enableTcpReset": true

Zkontrolujte [TCP Reset na časový limit nečinnosti](https://aka.ms/lbtcpreset) pro podrobnosti, včetně dostupnosti oblasti.

### <a name="support-both-tcp-and-udp-transport-protocols-with-a-single-rule"></a><a name="proto"></a>Podpora přenosových protokolů TCP i UDP pomocí jediného pravidla

Pravděpodobně budete chtít použít "Vše" pro protokol přenosu odchozí pravidlo, ale můžete také použít odchozí pravidlo pro konkrétní transportní protokol také v případě, že je třeba tak učinit.

K nastavení protokolu na protokol TCP a UDP použijte následující parametr:

          "protocol": "All"

### <a name="disable-outbound-nat-for-a-load-balancing-rule"></a><a name="disablesnat"></a>Zakázání odchozího nat pro pravidlo vyrovnávání zatížení

Jak již bylo uvedeno dříve, pravidla vyrovnávání zatížení poskytují automatické programování odchozího NAT. Některé scénáře však mají prospěch nebo vyžadují zakázání automatického programování odchozího nat podle pravidla vyrovnávání zatížení, které vám umožní řídit nebo upřesnit chování.  Odchozí pravidla mají scénáře, kde je důležité zastavit automatické odchozí programování NAT.

Tento parametr můžete použít dvěma způsoby:
- Volitelné potlačení použití příchozí IP adresy pro odchozí NAT.  Odchozí pravidla jsou přírůstková pro pravidla vyrovnávání zatížení a s touto sadou parametrů je výstupní pravidlo pod kontrolou.
  
- Vylaďte parametry odchozího nat adresy IP používané pro příchozí a odchozí současně.  Automatické odchozí programování NAT musí být zakázáno, aby odchozí pravidlo převzít kontrolu.  Například chcete-li změnit přidělení portu SNAT adresy, která se používá také pro příchozí, musí být tento parametr nastaven na hodnotu true.  Pokud se pokusíte použít odchozí pravidlo k předefinování parametrů adresy IP, které se také používají pro příchozí a nevydali jste odchozí programování NAT pravidla vyrovnávání zatížení, operace konfigurace odchozího pravidla se nezdaří.

>[!IMPORTANT]
> Virtuální počítač nebude mít odchozí připojení, pokud nastavíte tento parametr na hodnotu true a nemáte odchozí pravidlo (nebo [scénář veřejné IP adresy na úrovni instance,](load-balancer-outbound-connections.md#ilpip) který by definoval odchozí připojení.  Některé operace virtuálního počítače nebo aplikace může záviset na tom, že odchozí připojení k dispozici. Ujistěte se, že rozumíte závislostem vašeho scénáře a zvážili dopad provedení této změny.

Pomocí tohoto parametru konfigurace můžete zakázat odchozí snat na pravidle vyrovnávání zatížení:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

Parametr disableOutboundSNAT je výchozí na false, což znamená, že pravidlo vyrovnávání zatížení **poskytuje** automatický odchozí NAT jako zrcadlový obraz konfigurace pravidla vyrovnávání zatížení.  

Pokud nastavíte disableOutBoundSnat na true na pravidlo vyrovnávání zatížení, pravidlo vyrovnávání zatížení uvolní řízení jinak automatického odchozího programování NAT.  Odchozí SNAT v důsledku pravidla vyrovnávání zatížení je zakázáno.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Opětovné použití existujících nebo definování nových back-endových fondů

Odchozí pravidla nezavádějí nový koncept pro definování skupiny virtuálních vod, na které by se pravidlo mělo vztahovat.  Místo toho znovu použít koncept back-endového fondu, který se také používá pro pravidla vyrovnávání zatížení. Můžete použít ke zjednodušení konfigurace buď opětovné použití existující definice back-endového fondu nebo vytvořením konkrétně pro odchozí pravidlo.

## <a name="scenarios"></a>Scénáře

### <a name="groom-outbound-connections-to-a-specific-set-of-public-ip-addresses"></a><a name="groom"></a>Ženich odchozí připojení k určité sadě veřejných IP adres

Odchozí pravidlo můžete použít k úpravě odchozích připojení, která mají vypadat jako pocházející z určité sady veřejných IP adres, abyste usnadnili scénáře whitelistingu.  Tato zdrojová veřejná IP adresa může být stejná jako u pravidla vyrovnávání zatížení nebo jiné sady veřejných IP adres, než používá pravidlo vyrovnávání zatížení.  

1. Vytvořit [veřejnou předponu IP](https://aka.ms/lbpublicipprefix) (nebo veřejné IP adresy z veřejné předpony IP)
2. Vytvoření veřejného Load Balanceru úrovně Standard
3. Vytvoření front-endů odkazujících na veřejnou předponu IP (nebo veřejné IP adresy), kterou chcete použít
4. Znovu použít back-endový fond nebo vytvořit back-endový fond a umístit virtuální chod do back-endového fondu veřejného vykladače zatížení
5. Konfigurace odchozího pravidla pro veřejný vyrovnávání zatížení pro programování odchozího nat pro tyto virtuální počítače pomocí front-endů
   
Pokud si nepřejete, aby pravidlo vyrovnávání zatížení bylo použito pro odchozí, je třeba [zakázat odchozí SNAT](#disablesnat) na pravidle vyrovnávání zatížení.

### <a name="modify-snat-port-allocation"></a><a name="modifysnat"></a>Změna přidělení portu SNAT

Odchozí pravidla můžete použít k vyladění [automatického přidělení portu SNAT na základě velikosti back-endového fondu](load-balancer-outbound-connections.md#preallocatedports).

Například pokud máte dva virtuální počítače sdílející jednu veřejnou IP adresu pro odchozí NAT, můžete chtít zvýšit počet portů SNAT přidělených z výchozích portů 1024, pokud dochází k vyčerpání SNAT. Každá veřejná IP adresa může přispět až 64 000 dočasnými porty.  Pokud nakonfigurujete odchozí pravidlo s jedním front-endem veřejné IP adresy, můžete distribuovat celkem 64 000 portů SNAT do virtuálních počítačů v back-endovém fondu.  Pro dva virtuální servery lze přidělit maximálně 32 000 portů SNAT s pravidlem odchozí (2x 32 000 = 64 000).

Zkontrolujte [odchozí připojení](load-balancer-outbound-connections.md) a podrobnosti o tom, jak jsou porty [SNAT](load-balancer-outbound-connections.md#snat) přidělovány a používány.

### <a name="enable-outbound-only"></a><a name="outboundonly"></a>Povolit pouze odchozí

Veřejný standardní vyrovnávání zatížení můžete použít k poskytnutí odchozího NAT pro skupinu virtuálních< chodů. V tomto scénáři můžete použít odchozí pravidlo samo o sobě, bez nutnosti další pravidla.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>Odchozí NAT pro pouze virtuální virtuální já (bez příchozích)

Definujte veřejný standardní vyrovnávání zatížení, umístěte virtuální počítače do back-endového fondu a nakonfigurujte odchozí pravidlo pro naprogramování odchozího nat a groom odchozí připojení, které pocházejí z určité veřejné IP adresy. Můžete také použít veřejnou předponu IP zjednodušit white-listing zdroj odchozích připojení.

1. Vytvořte veřejný standardní vyrovnávání zatížení.
2. Vytvořte back-endový fond a umístěte virtuální chod do back-endového fondu veřejného vyvykladače zatížení.
3. Nakonfigurujte odchozí pravidlo pro veřejný vyrovnávání zatížení pro programování odchozího NAT pro tyto virtuální počítače.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>Odchozí nat pro interní scénáře standardního vyrovnávání zatížení

Při použití interního standardního systému vyrovnávání zatížení není odchozí nat k dispozici, dokud není explicitně deklarováno odchozí připojení. Odchozí připojení můžete definovat pomocí odchozího pravidla k vytvoření odchozího připojení pro virtuální virtuální zařízení za interním standardním vyvažovačem zatížení pomocí těchto kroků:

1. Vytvořte veřejný standardní vyrovnávání zatížení.
2. Vytvořte back-endový fond a umístěte virtuální chod do back-endového fondu veřejného vykladače zatížení kromě interního vytápětku zatížení.
3. Nakonfigurujte odchozí pravidlo pro veřejný vyrovnávání zatížení pro programování odchozího NAT pro tyto virtuální počítače.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Povolení protokolů TCP & UDP pro odchozí NAT s veřejným standardním likvérem zatížení

- Při použití veřejného standardního nástroje pro vyrovnávání zatížení odpovídá automatické odchozí programování NAT, které je k dispozici, transportnímu protokolu pravidla vyrovnávání zatížení.  

   1. Zakažte odchozí snat na pravidlo vyrovnávání zatížení.
   2. Nakonfigurujte odchozí pravidlo ve stejném vyvažovači zatížení.
   3. Znovu použít back-endový fond, který už vaše virtuální počítače používají.
   4. Zadejte "protokol": "Vše" jako součást odchozího pravidla.

- Při použití pouze příchozí pravidla NAT, není k dispozici žádný odchozí NAT.

   1. Umístěte virtuální chod do back-endového fondu.
   2. Definujte jednu nebo více front-endových konfigurací IP s veřejnými IP adresami nebo veřejnou předponou IP.
   3. Nakonfigurujte odchozí pravidlo ve stejném vyvažovači zatížení.
   4. Zadejte "protokol": "Vše" jako součást odchozího pravidla.

## <a name="limitations"></a>Omezení

- Maximální počet použitelných dočasných portů na front-endovou IP adresu je 64 000.
- Rozsah konfigurovatelného časového limitu odchozí hodu je 4 až 120 minut (240 až 7200 sekund).
- Vykladač zatížení nepodporuje ICMP pro odchozí NAT.
- Odchozí pravidla lze použít pouze pro primární konfiguraci IP nenokovací aplikace.  Podporováno je více nic.

## <a name="next-steps"></a>Další kroky

- Informace o použití [služby Balancer pro odchozí připojení](load-balancer-outbound-connections.md).
- Další informace o [standardním vyvykladaču zatížení](load-balancer-standard-overview.md).
- Informace o [obousměrném obnovení protokolu TCP při časovém limitu nečinnosti](load-balancer-tcp-reset.md).
- [Konfigurace odchozích pravidel pomocí rozhraní Azure CLI 2.0](configure-load-balancer-outbound-cli.md).
