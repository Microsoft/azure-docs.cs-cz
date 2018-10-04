---
title: Odchozí pravidla ve službě Azure Load Balancer | Dokumentace Microsoftu
description: Můžete definovat odchozí síťové adresy překlady odchozí pravidla
services: load-balancer
documentationcenter: na
author: KumudD
manager: jpconnock
tags: azure-resource-manager
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/3/2018
ms.author: kumud
ms.openlocfilehash: 50b567b298406b936a11ad5a8737ce4b1e21c0c7
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248720"
---
# <a name="load-balancer-outbound-rules"></a>Odchozí pravidla nástroje pro vyrovnávání zatížení

Azure Load Balancer poskytuje odchozí připojení z virtuální sítě navíc příchozí.  Odchozí pravidla se dají jednoduše nakonfigurovat veřejný [Load balanceru úrovně Standard](load-balancer-standard-overview.md)na odchozí překladu adres.  Budete mít úplný deklarativní kontrolu nad odchozí připojení ke škálování a optimalizaci tato možnost vašim konkrétním potřebám.

![Odchozí pravidla nástroje pro vyrovnávání zatížení](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

U odchozích pravidel můžete použít nástroje pro vyrovnávání zatížení: 
- Definujte odchozí NAT úplně od začátku.
- škálování a optimalizaci chování existující odchozí NAT. 

Odchozí pravidla umožňují řídit:
- které virtuální počítače by měl přeložit na které veřejné IP adresy. 
- Jak [odchozích portech SNAT](load-balancer-outbound-connections.md#snat) by měly být přiděleny.
- protokoly, které k poskytování odchozí překlad.
- jaké doby trvání pro odchozí připojení. časový limit nečinnosti.
- Určuje, zda odesílat TCP Reset na časový limit nečinnosti (ve verzi Public Preview). 

Odchozí pravidla rozšiřují [scénář 2](load-balancer-outbound-connections.md#lb) v podle [odchozí připojení](load-balancer-outbound-connections.md) článku a určování priorit scénář bude nadála – je.

## <a name="outbound-rule"></a>Odchozí pravidlo

Stejně jako všechna pravidla služby Load balancer úrovně odchozí pravidla postupujte podle stejné syntaxe známé jako Vyrovnávání zatížení a pravidla příchozího překladu adres:

**front-endu** + **parametry** + **back-endový fond**

Nakonfiguruje odchozí NAT pro odchozí pravidlo _všechny virtuální počítače identifikované back-endový fond_ mají být převedeny na _front-endu_.  A _parametry_ poskytují další Pokud potřebujete jemněji odstupňované řízení odchozí NAT algoritmus.

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
>Efektivní odchozí NAT. je složený všechny odchozí pravidla a pravidla Vyrovnávání zatížení. Odchozí pravidla se přičítají k pravidla Vyrovnávání zatížení. Kontrola [zakázání odchozí NAT pro pravidlo Vyrovnávání zatížení](#disablesnat) ke správě efektivní odchozí překlad síťových adres při více pravidla se vztahují k virtuálnímu počítači. Je nutné [zakázat odchozí SNAT](#disablesnat) při definování odchozí pravidlo, které používá stejnou veřejnou IP adresu jako pravidlo Vyrovnávání zatížení.

### <a name="scale"></a> Škálování odchozí NAT s několika IP adresami

Zatímco odchozí pravidlo lze použít s právě jednu veřejnou IP adresu, odchozí pravidla usnadnění konfigurace zatížení pro škálování odchozí NAT. Několik IP adres můžete použít k plánování pro scénáře velkého rozsahu a odchozích pravidel můžete použít ke zmírnění [SNAT vyčerpání](load-balancer-outbound-connections.md#snatexhaust) vzory náchylné k chybám.  

Každý další IP adresu poskytované front-end poskytuje dosahovat 64 000 dočasné porty pro nástroj pro vyrovnávání zatížení pro použití jako porty SNAT. Vyrovnávání zatížení nebo pravidla příchozího překladu adres mají jeden front-endu, odchozí pravidlo rozšiřuje pojem front-endu a umožňuje několik front-endů v jednotlivém pravidle.  Několik front-endů pro každé pravidlo počet dostupných portů SNAT se násobí s každou veřejnou IP adresu a velmi velké scénářích může být podporovaný.

Kromě toho můžete použít [předponu veřejné IP](https://aka.ms/lbpublicipprefix) přímo s pravidlem odchozí.  To umožňuje snazší škálování a zjednodušené vytváření seznamu povolených toky pocházející z nasazení služby Azure. Můžete nakonfigurovat IP konfigurace front-endu v rámci prostředek nástroje pro vyrovnávání zatížení, který chcete odkazovat přímo předponu veřejné IP adresy.  Díky tomu nástroje pro vyrovnávání zatížení výhradní kontrolu nad veřejných předpon adres IP a odchozí pravidla budou automaticky používat všechny veřejné IP adresy obsažené v rozsahu předpony veřejných IP pro odchozí připojení.  Každá z IP adresy v rozsahu veřejných předpon adres IP poskytují dosahovat 64 000 dočasné porty na IP adresu nástroje pro vyrovnávání zatížení pro použití jako porty SNAT.   

Nemůžete mít jednotlivé prostředky pro adres veřejné IP adresy vytvořené z veřejných předpon adres IP při použití této možnosti jako odchozí pravidlo musí mít úplnou kontrolu nad veřejných předpon adres IP.  Pokud potřebujete více jemné kontrolu, můžete vytvořit jednotlivé prostředek veřejné IP adresy z veřejných předpon adres IP a jednotlivě přiřadit několik veřejných IP adres frontendu odchozí pravidlo.

### <a name="snatports"></a> Ladit přidělování SNAT portů

Odchozí pravidla můžete použít k ladění [automatické přidělování port SNAT podle velikosti fondu back-endu](load-balancer-outbound-connections.md#preallocatedports) a přidělit víc nebo míň, než poskytuje funkce pro automatické přidělování port SNAT.

Použijte tento parametr k přidělení 10 000 SNAT porty na virtuálním počítači (Konfigurace IP adresy NIC).
 

          "allocatedOutboundPorts": 10000

Každá veřejná IP adresa ze všech front-endů odchozí pravidla přispívá až 64 000 dočasné porty pro nás jako porty SNAT.  Nástroj pro vyrovnávání zatížení přiděluje SNAT porty v násobcích po 8. Pokud zadáte hodnotu není dělitelná 8, operace konfigurace byl odmítnut.  Při pokusu o přidělení více SNAT porty, než je k dispozici na základě počtu veřejných IP adres, operace konfigurace byl odmítnut.  Pokud přidělíte 10 000 porty na virtuálním počítači a 7 virtuálních počítačů v back-end fondu by sdílet jednu veřejnou IP adresu, je konfigurace odmítnuté (7 x 10,0000 SNAT porty > 64 000 SNAT porty).  Můžete přidat více veřejné IP adresy pro front-endu odchozí pravidla povolit tento scénář.

Můžete se vrátit zpět k [automatické přidělování port SNAT podle velikosti fondu back-endu](load-balancer-outbound-connections.md#preallocatedports) zadáním 0 pro čísla portů.

### <a name="idletimeout"></a> Časový limit nečinnosti odchozího toku řízení

Odchozí pravidla poskytují parametr konfigurace k řízení odchozího toku časový limit nečinnosti a přizpůsobit potřebám vaší aplikace.  Výchozí odchozí časový limit nečinnosti 4 minuty.  Parametr přijímá hodnotu od 4 do 66 konkrétní dobu v minutách pro časový limit nečinnosti pro tento konkrétní pravidlo pro porovnávání toky.

K nastavení odchozí časový limit nečinnosti na 1 hodinu, použijte následující parametr:

          "idleTimeoutInMinutes": 60

### <a name="tcprst"></a> <a name="tcpreset"></a> Povolte resetování TCP na časový limit nečinnosti (Preview)

Výchozí chování nástroje pro vyrovnávání zatížení je tiše síťový tok, když se dosáhlo odchozí časový limit nečinnosti.  S parametrem enableTCPReset můžete povolit předvídatelnějšího chování aplikace a řídit, jestli se má odeslat obousměrné TCP Reset (TCP RVNÍ) na časový limit odchozí časový limit nečinnosti. 

Povolte resetování TCP na odchozí pravidlo pomocí následující parametr:

           "enableTcpReset": true

Kontrola [TCP vynulují časový limit nečinnosti (Preview)](https://aka.ms/lbtcpreset) podrobnosti, včetně dostupnosti oblast.

### <a name="proto"></a> Podpora přenosové protokoly TCP a UDP s jedním pravidlem

Budete pravděpodobně chtít použít "All" pro přenosový protokol odchozí pravidla, ale můžete také použít odchozí pravidlo na konkrétní přenosový protokol a pokud je potřeba udělat.

Nastavení protokolu TCP a UDP, použijte následující parametr:

          "protocol": "All"

### <a name="disablesnat"></a> Zakázat odchozí NAT pro pravidlo Vyrovnávání zatížení

Jak bylo uvedeno dříve, pravidla Vyrovnávání zatížení poskytnout automatické programování odchozí NAT. Některé scénáře však výhody nebo vyžadovat, abyste zakázat automatické programování odchozí NAT, že pravidlo, které umožňují řídit nebo Upřesnit chování Vyrovnávání zatížení.  Odchozí pravidla mají scénáře, kdy je potřeba zastavit automatické odchozí NAT programování.

Tento parametr lze použít dvěma způsoby:
- Volitelné potlačení použití příchozí IP adresu pro odchozí NAT.  Odchozí pravidla se přičítají k pravidla Vyrovnávání zatížení a tento parametr nastaven, odchozí pravidlo je v ovládacím prvku.
  
- Vyladění odchozí NAT parametry současně použít pro příchozí a odchozí IP adresy.  Povolit odchozí pravidlo převzít kontrolu, musí se zakázat automatické odchozí NAT programování.  Například, chcete-li změnit port přidělení SNAT adresy také použít pro příchozí, tento parametr musí být nastaven na hodnotu true.  Pokud se pokusíte použít odchozí pravidlo k předefinování parametry IP adresy používá také pro příchozí a nebyly vydané odchozí NAT programování pravidlo Vyrovnávání zatížení, se nezdaří operace konfigurace odchozí pravidla.

>[!IMPORTANT]
> Váš virtuální počítač nebude mít odchozí připojení, pokud tento parametr nastaven na hodnotu true a není nutné odchozí pravidlo (nebo [veřejného scénáře IP na úrovni instance](load-balancer-outbound-connections.md#ilpip) definovat odchozí připojení.  Některé operace virtuálního počítače nebo vaše aplikace může záviset na s odchozí připojení k dispozici. Ujistěte se, že přehled o závislostech váš scénář a mít považovat za dopad provedení této změny.

Můžete zakázat odchozí SNAT na pravidlo s tímto parametrem konfiguraci Vyrovnávání zatížení:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

DisableOutboundSNAT parametr výchozí hodnotu false, což znamená, že pravidla Vyrovnávání zatížení **nemá** poskytují automatickou odchozí NAT jako zrcadlový obraz konfiguraci pravidla Vyrovnávání zatížení.  

Pokud nastavíte disableOutboundSnat na hodnotu true pro pravidlo Vyrovnávání zatížení, pravidla Vyrovnávání zatížení uvolní řízení jinak automatické odchozí NAT programování.  Odchozí SNAT v důsledku pravidla Vyrovnávání zatížení je zakázaná.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Znovu použít existující, nebo definujte nové back-endové fondy

Odchozí pravidla nezavádí nové konceptu k definování skupiny virtuálních počítačů, pro které má pravidlo platit.  Místo toho použít koncept back-endový fond, který se používá také pro pravidla Vyrovnávání zatížení. Můžete to zjednodušit konfiguraci tím, že opětovné použití existující definice fondu back-end nebo vytvoření nového speciálně pro odchozí pravidlo.

## <a name="scenarios"></a>Scénáře

### <a name="groom"></a> Odchozí připojení výmazu dat pro konkrétní sadu veřejné IP adresy

Odchozí pravidlo slouží k pravidelnému mazání odchozí připojení se zobrazí jenom na úzkou konkrétní sadu veřejné IP adresy k usnadnění scénářů na seznam povolených.  Tuto veřejnou IP adresu zdroje může být stejný jako pravidla Vyrovnávání zatížení nebo jinou sadu veřejných IP adres než používá pravidla Vyrovnávání zatížení.  

1. Vytvoření [předponu veřejné IP](https://aka.ms/lbpublicipprefix) (nebo veřejné IP adresy z veřejných předpon adres IP)
2. Vytvoření veřejného Load Balanceru úrovně Standard
3. Vytvoření front-endů odkazující na veřejnosti předpon adres IP (nebo veřejné IP adresy), které chcete použít
4. Opakovaně používat back-endový fond nebo vytvořte back-endového fondu a umístěte virtuální počítače do back-endový fond veřejného nástroje pro vyrovnávání zatížení
5. Odchozí pravidlo nakonfigurovat na veřejný Load balancer úrovně programovat odchozí NAT pro tyto virtuální počítače pomocí front-endů
   
Pokud nechcete, aby pro pravidlo Vyrovnávání zatížení má být použit pro odchozí, budete muset [zakázat odchozí SNAT](#disablesnat) na pravidla Vyrovnávání zatížení.

### <a name="modifysnat"></a> Upravit přidělení SNAT portu

Odchozí pravidla můžete použít k ladění [automatické přidělování port SNAT podle velikosti fondu back-endu](load-balancer-outbound-connections.md#preallocatedports).

Pokud budete mít dva virtuální počítače sdílení jednu veřejnou IP adresu pro odchozí NAT, můžete chtít zvýšit počet portů SNAT přiděluje z výchozí porty 1024, pokud dochází k vyčerpání SNAT. Každá veřejná IP adresa může přispívat do 64 000 dočasné porty.  Pokud nakonfigurujete odchozí pravidlo s jedné veřejné IP adresy front-endu, můžete distribuovat celkem dosahovat 64 000 portů SNAT na virtuální počítače v back-endový fond.  Pro dva virtuální počítače je možné přidělit maximálně 32 000 portů SNAT s pravidlem odchozí (2 × 32 000 = 64 000).

Kontrola [odchozí připojení](load-balancer-outbound-connections.md) a podrobnosti o [SNAT](load-balancer-outbound-connections.md#snat) porty jsou přidělovány a využívány.

### <a name="outboundonly"></a> Povolit pouze odchozí

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

- Maximální počet použitelné dočasné porty na front-endové IP adresy je 64 000.
- Rozsah konfigurovatelné odchozí časový limit nečinnosti je 66 do 4 minut (240 až 4000 sekund).
- Nástroj pro vyrovnávání zatížení nepodporuje protokol ICMP pro odchozí NAT.
- Portál nelze použít ke konfiguraci nebo zobrazení odchozí pravidla.  Použijte šablony, rozhraní REST API, Az CLI 2.0 nebo prostředí PowerShell.

## <a name="next-steps"></a>Další postup

- Další informace o použití [nástroje pro vyrovnávání zatížení pro odchozí připojení](load-balancer-outbound-connections.md).
- Další informace o [Load balanceru úrovně Standard](load-balancer-standard-overview.md).
- Další informace o [obousměrné TCP Reset na časový limit nečinnosti](load-balancer-tcp-reset.md).
- [Nakonfigurujte odchozí pravidla pomocí Azure CLI 2.0](configure-load-balancer-outbound-cli.md).
