---
title: Odchozí pravidla – Azure Load Balancer
description: Pomocí této cesty výukového programu můžete začít používat odchozí pravidla k definování odchozích překladů síťových adres.
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
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304187"
---
# <a name="load-balancer-outbound-rules"></a>Odchozí pravidla nástroje pro vyrovnávání zatížení

Azure Load Balancer poskytuje odchozí připojení z virtuální sítě navíc příchozí.  Odchozí pravidla usnadňují konfiguraci odchozího překladu síťových adres pro veřejné [Standard Load Balancer](load-balancer-standard-overview.md).  Budete mít úplný deklarativní kontrolu nad odchozí připojení ke škálování a optimalizaci tato možnost vašim konkrétním potřebám.

![Odchozí pravidla nástroje pro vyrovnávání zatížení](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

U odchozích pravidel můžete použít nástroje pro vyrovnávání zatížení: 
- Definujte odchozí NAT úplně od začátku.
- škálování a optimalizaci chování existující odchozí NAT. 

Odchozí pravidla umožňují řídit:
- které virtuální počítače by měl přeložit na které veřejné IP adresy. 
- Jak by měly být přiděleny [Odchozí porty SNAT](load-balancer-outbound-connections.md#snat) .
- protokoly, které k poskytování odchozí překlad.
- Jaká doba se má použít pro časový limit nečinnosti odchozího připojení (4-120 minut).
- Určuje, zda má být odesláno resetování protokolu TCP při nečinnosti.

Odchozí pravidla rozbalí [scénář 2](load-balancer-outbound-connections.md#lb) , který je popsaný v článku o [odchozích připojeních](load-balancer-outbound-connections.md) , a Priorita scénáře zůstane tak, jak je.

## <a name="outbound-rule"></a>Odchozí pravidlo

Stejně jako všechna pravidla služby Load balancer úrovně odchozí pravidla postupujte podle stejné syntaxe známé jako Vyrovnávání zatížení a pravidla příchozího překladu adres:

**parametry** + **front-endu** + **back-end fondu**

Odchozí pravidlo konfiguruje odchozí překlad adres (NAT) pro _všechny virtuální počítače identifikované fondem back-end_ pro překlad do _front-endu_.  _Parametry_ a poskytují další jemně odstupňovanou kontrolu nad odchozím algoritmem NAT.

Rozhraní API ve verzi "2018-07-01" povoluje definici odchozí pravidlo strukturované následujícím způsobem:

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
>Efektivní odchozí NAT. je složený všechny odchozí pravidla a pravidla Vyrovnávání zatížení. Odchozí pravidla se přičítají k pravidla Vyrovnávání zatížení. Přečtěte si téma [zakázání odchozího překladu adres (NAT) pro pravidlo vyrovnávání zatížení](#disablesnat) pro správu efektivního překladu NAT, když se na virtuální počítač vztahuje víc pravidel Při definování odchozího pravidla, které používá stejnou veřejnou IP adresu jako pravidlo vyrovnávání zatížení, je nutné [Zakázat odchozí SNAT](#disablesnat) .

### <a name="scale"></a>Škálování odchozího překladu adres (NAT) s několika IP adresami

Zatímco odchozí pravidlo lze použít s právě jednu veřejnou IP adresu, odchozí pravidla usnadnění konfigurace zatížení pro škálování odchozí NAT. Pro plánování rozsáhlých scénářů můžete použít více IP adres a pomocí odchozích pravidel můžete zmírnit vzory náchylné k [vyčerpání SNAT](load-balancer-outbound-connections.md#snatexhaust) .  

Každá další IP adresa poskytovaná front-endu poskytuje 64 000 dočasných portů, které Load Balancer použít jako porty SNAT. Vyrovnávání zatížení nebo pravidla příchozího překladu adres mají jeden front-endu, odchozí pravidlo rozšiřuje pojem front-endu a umožňuje několik front-endů v jednotlivém pravidle.  Několik front-endů pro každé pravidlo počet dostupných portů SNAT se násobí s každou veřejnou IP adresu a velké scénářích může být podporovaný.

Kromě toho můžete použít [předponu veřejné IP adresy](https://aka.ms/lbpublicipprefix) přímo s odchozím pravidlem.  Pomocí veřejné IP adresy umožňuje snazší škálování a zjednodušené seznamu povolených toků pocházející z nasazení vašeho řešení Azure předponu. Můžete nakonfigurovat IP konfigurace front-endu v rámci prostředek nástroje pro vyrovnávání zatížení, který chcete odkazovat přímo předponu veřejné IP adresy.  Díky tomu nástroje pro vyrovnávání zatížení výhradní kontrolu nad veřejných předpon adres IP a odchozí pravidla budou automaticky používat všechny veřejné IP adresy obsažené v rozsahu předpony veřejných IP pro odchozí připojení.  Jednotlivé IP adresy v rozsahu předpony veřejných IP adres poskytují 64 000 dočasných portů na IP adresu Load Balancer použít jako porty SNAT.   

Nemůžete mít jednotlivé prostředky pro adres veřejné IP adresy vytvořené z veřejných předpon adres IP při použití této možnosti jako odchozí pravidlo musí mít úplnou kontrolu nad veřejných předpon adres IP.  Pokud potřebujete více jemné kontrolu, můžete vytvořit jednotlivé prostředek veřejné IP adresy z veřejných předpon adres IP a jednotlivě přiřadit několik veřejných IP adres frontendu odchozí pravidlo.

### <a name="snatports"></a>Vyladit alokaci portů SNAT

Pomocí odchozích pravidel můžete vyladit [automatické přidělování portů SNAT na základě velikosti fondu back-endu](load-balancer-outbound-connections.md#preallocatedports) a přidělit více nebo méně, než jaké automatické přidělování portů SNAT nabízí.

Použijte tento parametr k přidělení 10 000 SNAT porty na virtuálním počítači (Konfigurace IP adresy NIC).
 

          "allocatedOutboundPorts": 10000

Každá veřejná IP adresa ze všech front-endu odchozího pravidla přispívá až 64 000 dočasných portů, které se použijí jako porty SNAT.  Nástroj pro vyrovnávání zatížení přiděluje SNAT porty v násobcích po 8. Pokud zadáte hodnotu není dělitelná 8, operace konfigurace byl odmítnut.  Při pokusu o přidělení více SNAT porty, než je k dispozici na základě počtu veřejných IP adres, operace konfigurace byl odmítnut.  Pokud například přidělíte porty 10 000 na virtuální počítač a 7 virtuálních počítačů ve fondu back-end, bude se jednat o jednu veřejnou IP adresu, konfigurace se odmítne (7 x 10 000 SNAT portů > 64 000 portů SNAT).  Můžete přidat více veřejné IP adresy pro front-endu odchozí pravidla povolit tento scénář.

Můžete vrátit zpět na [automatické přidělování portů SNAT na základě velikosti fondu back-endu](load-balancer-outbound-connections.md#preallocatedports) zadáním 0 pro počet portů. V takovém případě získají první 50 instancí virtuálních počítačů 1024 portů, 51-100 instancí virtuálních počítačů bude 512 a tak podle tabulky.

### <a name="idletimeout"></a>Řízení časového limitu nečinnosti odchozího toku

Odchozí pravidla poskytují parametr konfigurace k řízení odchozího toku časový limit nečinnosti a přizpůsobit potřebám vaší aplikace.  Výchozí odchozí časový limit nečinnosti 4 minuty.  Parametr přijímá hodnotu od 4 do 120 pro určitý počet minut pro časový limit nečinnosti pro toky, které odpovídají tomuto konkrétnímu pravidlu.

K nastavení odchozí časový limit nečinnosti na 1 hodinu, použijte následující parametr:

          "idleTimeoutInMinutes": 60

### <a name="tcprst"></a><a name="tcpreset"></a> Povolit resetování protokolu TCP pro časový limit nečinnosti

Výchozí chování nástroje pro vyrovnávání zatížení je tiše síťový tok, když se dosáhlo odchozí časový limit nečinnosti.  S parametrem enableTCPReset můžete povolit předvídatelnějšího chování aplikace a řídit, jestli se má odeslat obousměrné TCP Reset (TCP RVNÍ) na časový limit odchozí časový limit nečinnosti. 

Povolte resetování TCP na odchozí pravidlo pomocí následující parametr:

           "enableTcpReset": true

Zkontrolujte [časový limit nečinnosti protokolu TCP](https://aka.ms/lbtcpreset) pro podrobnosti, včetně dostupnosti oblasti.

### <a name="proto"></a>Podpora přenosových protokolů TCP i UDP s jedním pravidlem

Budete pravděpodobně chtít použít "All" pro přenosový protokol odchozí pravidla, ale můžete také použít odchozí pravidlo na konkrétní přenosový protokol a pokud je potřeba udělat.

Nastavení protokolu TCP a UDP, použijte následující parametr:

          "protocol": "All"

### <a name="disablesnat"></a>Zakázat odchozí překlad adres (NAT) pro pravidlo vyrovnávání zatížení

Jak bylo uvedeno dříve, pravidla Vyrovnávání zatížení poskytnout automatické programování odchozí NAT. Některé scénáře však výhody nebo vyžadovat, abyste zakázat automatické programování odchozí NAT, že pravidlo, které umožňují řídit nebo Upřesnit chování Vyrovnávání zatížení.  Odchozí pravidla mají scénáře, kdy je potřeba zastavit automatické odchozí NAT programování.

Tento parametr lze použít dvěma způsoby:
- Volitelné potlačení použití příchozí IP adresu pro odchozí NAT.  Odchozí pravidla se přičítají k pravidla Vyrovnávání zatížení a tento parametr nastaven, odchozí pravidlo je v ovládacím prvku.
  
- Vyladění odchozí NAT parametry současně použít pro příchozí a odchozí IP adresy.  Povolit odchozí pravidlo převzít kontrolu, musí se zakázat automatické odchozí NAT programování.  Například, chcete-li změnit port přidělení SNAT adresy také použít pro příchozí, tento parametr musí být nastaven na hodnotu true.  Pokud se pokusíte použít odchozí pravidlo k předefinování parametry IP adresy používá také pro příchozí a nebyly vydané odchozí NAT programování pravidlo Vyrovnávání zatížení, se nezdaří operace konfigurace odchozí pravidla.

>[!IMPORTANT]
> Pokud nastavíte tento parametr na hodnotu true a nemáte odchozí pravidlo (nebo [scénář veřejné IP adresy na úrovni instance](load-balancer-outbound-connections.md#ilpip) pro definování odchozího připojení), váš virtuální počítač nebude mít odchozí připojení.  Některé operace virtuálního počítače nebo vaše aplikace může záviset na s odchozí připojení k dispozici. Ujistěte se, že přehled o závislostech váš scénář a mít považovat za dopad provedení této změny.

Můžete zakázat odchozí SNAT na pravidlo s tímto parametrem konfiguraci Vyrovnávání zatížení:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

Parametr disableOutboundSNAT má výchozí hodnotu false, což znamená, že pravidlo vyrovnávání **zatížení poskytuje automatické** odchozí překlad adres (NAT) jako zrcadlový obraz konfigurace pravidla vyrovnávání zatížení.  

Pokud nastavíte disableOutboundSnat na hodnotu true pro pravidlo Vyrovnávání zatížení, pravidla Vyrovnávání zatížení uvolní řízení jinak automatické odchozí NAT programování.  Odchozí SNAT v důsledku pravidla Vyrovnávání zatížení je zakázaná.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Znovu použít existující, nebo definujte nové back-endové fondy

Odchozí pravidla nezavádí nové konceptu k definování skupiny virtuálních počítačů, pro které má pravidlo platit.  Místo toho použít koncept back-endový fond, který se používá také pro pravidla Vyrovnávání zatížení. Můžete to zjednodušit konfiguraci tím, že opětovné použití existující definice fondu back-end nebo vytvoření nového speciálně pro odchozí pravidlo.

## <a name="scenarios"></a>Scénáře

### <a name="groom"></a>Odchozí připojení výmazu dat ke konkrétní sadě veřejných IP adres

Odchozí pravidlo slouží k pravidelnému mazání odchozí připojení se zobrazí jenom na úzkou konkrétní sadu veřejné IP adresy k usnadnění scénářů na seznam povolených.  Tuto veřejnou IP adresu zdroje může být stejný jako pravidla Vyrovnávání zatížení nebo jinou sadu veřejných IP adres než používá pravidla Vyrovnávání zatížení.  

1. Vytvořit [předponu veřejné IP](https://aka.ms/lbpublicipprefix) adresy (nebo veřejné IP adresy z předpony veřejných IP adres)
2. Vytvoření veřejného Load Balanceru úrovně Standard
3. Vytvoření front-endů odkazující na veřejnosti předpon adres IP (nebo veřejné IP adresy), které chcete použít
4. Opakovaně používat back-endový fond nebo vytvořte back-endového fondu a umístěte virtuální počítače do back-endový fond veřejného nástroje pro vyrovnávání zatížení
5. Odchozí pravidlo nakonfigurovat na veřejný Load balancer úrovně programovat odchozí NAT pro tyto virtuální počítače pomocí front-endů
   
Pokud nechcete, aby se pravidlo vyrovnávání zatížení používalo pro odchozí připojení, musíte [Zakázat odchozí SNAT](#disablesnat) v pravidle vyrovnávání zatížení.

### <a name="modifysnat"></a>Úprava přidělení portu SNAT

Pomocí odchozích pravidel můžete vyladit [automatické přidělování portů SNAT na základě velikosti fondu back-endu](load-balancer-outbound-connections.md#preallocatedports).

Pokud budete mít dva virtuální počítače sdílení jednu veřejnou IP adresu pro odchozí NAT, můžete chtít zvýšit počet portů SNAT přiděluje z výchozí porty 1024, pokud dochází k vyčerpání SNAT. Každá veřejná IP adresa může přispívat až 64 000 dočasných portů.  Pokud nakonfigurujete odchozí pravidlo s jednou front-endu veřejné IP adresy, můžete do virtuálních počítačů ve fondu back-endu distribuovat celkem 64 000 portů SNAT.  Pro dva virtuální počítače se dá přidělit maximálně 32 000 portů SNAT pomocí odchozího pravidla (2x 32 000 = 64 000).

Zkontrolujte [odchozí připojení](load-balancer-outbound-connections.md) a podrobnosti o přidělování a používání portů [SNAT](load-balancer-outbound-connections.md#snat) .

### <a name="outboundonly"></a>Povolit pouze odchozí

Veřejného Load balanceru úrovně Standard můžete použít k poskytnutí odchozí NAT pro skupinu virtuálních počítačů. V tomto scénáři můžete použít odchozí pravidlo samostatně, bez nutnosti jakékoli další pravidla.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>Odchozí NAT pro virtuální počítače pouze (ne příchozí)

Definovat veřejný Load Balancer Standard, umístěte virtuální počítače do back-endového fondu a nakonfigurovat odchozí pravidlo programu odchozí NAT a mazání odchozí připojení k pocházejí z konkrétní veřejné IP adresy. Můžete také použít veřejných předpon adres IP pro zjednodušení seznamu povolených zdroj odchozí připojení.

1. Vytvoření veřejného Load Balanceru úrovně Standard.
2. Vytvořte back-endový fond a umístěte virtuální počítače do back-endový fond veřejného nástroje pro vyrovnávání zatížení.
3. Odchozí pravidlo nakonfigurujte na veřejný Load balancer úrovně programovat odchozí NAT pro tyto virtuální počítače.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>Odchozí NAT pro interní nástroj pro vyrovnávání zatížení scénáře

Při použití interní Load balanceru úrovně Standard, odchozí NAT není k dispozici, dokud explicitně deklarovány odchozí připojení. Můžete definovat odchozí připojení pomocí odchozí pravidla vytvořit odchozí připojení pro virtuální počítače za bránou interní Load balanceru úrovně Standard pomocí těchto kroků:

1. Vytvoření veřejného Load Balanceru úrovně Standard.
2. Vytvořte back-endového fondu a umístěte virtuální počítače do back-endový fond veřejného Load balanceru úrovně kromě interního nástroje pro vyrovnávání zatížení.
3. Odchozí pravidlo nakonfigurujte na veřejný Load balancer úrovně programovat odchozí NAT pro tyto virtuální počítače.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Povolit protokoly TCP a UDP pro odchozí NAT s veřejného Load balanceru úrovně Standard

- Při použití veřejného Load balanceru úrovně Standard, automatické odchozí NAT programování poskytuje odpovídá přenosový protokol pravidlo Vyrovnávání zatížení.  

   1. Zakážete odchozí SNAT na pravidla Vyrovnávání zatížení.
   2. Odchozí pravidlo nakonfigurujte na stejné nástroje pro vyrovnávání zatížení.
   3. Opakovaně používat back-endový fond už používá vaše virtuální počítače.
   4. Zadejte "protokol": "Vše" jako součást odchozí pravidla.

- Pokud se používá jenom příchozích pravidel NAT, je k dispozici žádná odchozí NAT.

   1. Umístěte virtuální počítače v back-endový fond.
   2. Definujte jeden nebo více konfigurací protokolu IP front-endu pomocí veřejné IP adresy nebo předpony veřejných IP.
   3. Odchozí pravidlo nakonfigurujte na stejné nástroje pro vyrovnávání zatížení.
   4. Zadejte "protokol": "Vše" jako součást odchozí pravidla

## <a name="limitations"></a>Omezení

- Maximální počet použitelných dočasných portů na IP adresu front-endu je 64 000.
- Rozsah konfigurovatelného odchozího časového limitu nečinnosti je 4 až 120 minut (240 až 7200 sekund).
- Nástroj pro vyrovnávání zatížení nepodporuje protokol ICMP pro odchozí NAT.
- Odchozí pravidla se dají použít jenom u primární konfigurace IP adresy síťového adaptéru.  Podporuje se víc síťových adaptérů.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o použití [Load Balancer pro odchozí připojení](load-balancer-outbound-connections.md).
- Přečtěte si o [Standard Load Balancer](load-balancer-standard-overview.md).
- Přečtěte si o [Nastavení obousměrného protokolu TCP při nečinnosti](load-balancer-tcp-reset.md).
- [Nakonfigurujte odchozí pravidla pomocí Azure CLI 2,0](configure-load-balancer-outbound-cli.md).
