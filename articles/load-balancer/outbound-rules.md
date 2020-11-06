---
title: Odchozí pravidla Azure Load Balancer
description: Tento článek vysvětluje, jak nakonfigurovat odchozí pravidla, která řídí provoz internetového provozu pomocí Azure Load Balancer.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 947ecaa2efbfb013f1f3e8203d1c4296b9ca329f
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422157"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Odchozí pravidla Azure Load Balancer

Odchozí pravidla vám umožňují explicitně definovat SNAT (Zdrojová síťová adresa) pro veřejný Nástroj pro vyrovnávání zatížení úrovně Standard. Tato konfigurace umožňuje používat veřejné IP adresy vašeho nástroje pro vyrovnávání zatížení k poskytování odchozího připojení k Internetu pro instance back-endu.

Tato konfigurace umožňuje:

* Maskování IP adres
* Zjednodušení seznamů povolených.
* Snižuje počet prostředků veřejné IP adresy pro nasazení.

U odchozích pravidel máte úplnou deklarativní kontrolu nad odchozím připojením k Internetu. Odchozí pravidla umožňují škálovat a ladit tuto schopnost vašim konkrétním potřebám. 

Odchozí pravidla se budou dodržovat jenom v případě, že back-end virtuální počítač nemá veřejnou IP adresu na úrovni instance (ILPIP).

![Load Balancer odchozí pravidla](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

U odchozích pravidel můžete explicitně definovat odchozí chování **SNAT** .

Odchozí pravidla umožňují řídit:

* **Které virtuální počítače jsou přeložené na které veřejné IP adresy.**
     * Dvě pravidla byla back-end fondu A používá IP adresu a a B, back-end fond B používá IP adresu C a D.
* **Jak jsou přidělovány Odchozí porty SNAT.**
     * Back-end fond B je jediným fondem, který umožňuje odchozí připojení, přidělte všem portům SNAT back-end fondu B a žádnému do fondu back-endu a.
* **Které protokoly pro zajištění odchozího překladu pro.**
     * Back-end fond B potřebuje porty UDP pro odchozí připojení. Fond back-endu potřebuje protokol TCP. Udělte porty TCP portům a a UDP do B.
* **Jaká doba se má použít pro časový limit nečinnosti odchozího připojení (4-120 minut).**
     * Pokud máte dlouho běžící připojení s udržením naživu, vyhradte nečinné porty pro dlouhotrvající připojení po dobu až 120 minut. Pro nová připojení se předpokládá, že se zastaralá připojení přebírají a vydávají porty za 4 minuty. 
* **Určuje, zda má být pro časový limit nečinnosti odesláno resetování protokolu TCP.**
     * Když načasování nečinných připojení, pošleme klientovi a serveru TCP RST, aby věděli, že tok je opuštěný?

## <a name="outbound-rule-definition"></a>Definice odchozího pravidla

Odchozí pravidla dodržují stejnou známou syntaxi jako vyrovnávání zatížení a příchozí pravidla **frontend** NAT:  +  **parameters**  +  **back-** Endu parametrů front-endu. 

Odchozí pravidlo konfiguruje odchozí překlad adres (NAT) pro _všechny virtuální počítače identifikované fondem back-end_ pro překlad do _front-endu_.  

_Parametry_ poskytují další jemně odstupňovanou kontrolu nad odchozím algoritmem NAT.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Škálování odchozího překladu adres (NAT) s několika IP adresami

Každá další IP adresa poskytovaná front-endu poskytuje další 64 000 dočasné porty pro nástroj pro vyrovnávání zatížení, které slouží jako porty SNAT. 

Pro plánování rozsáhlých scénářů použijte více IP adres. Pomocí odchozích pravidel můžete zmírnit [vyčerpání SNAT](troubleshoot-outbound-connection.md#snatexhaust). 

Můžete také použít [předponu veřejné IP adresy](https://aka.ms/lbpublicipprefix) přímo s odchozím pravidlem. 

Předpona veřejné IP adresy zvyšuje velikost vašeho nasazení. Předponu můžete přidat do seznamu povolených toků z vašich prostředků Azure. V nástroji pro vyrovnávání zatížení můžete nakonfigurovat konfiguraci protokolu IP front-endu, aby odkazovala na předponu veřejné IP adresy.  

Nástroj pro vyrovnávání zatížení má kontrolu nad předponou veřejné IP adresy. Odchozí pravidlo automaticky použije všechny veřejné IP adresy obsažené v předponě veřejných IP adres pro odchozí připojení. 

Každá IP adresa v rámci předpony veřejných IP adres poskytuje další 64 000 dočasných portů na IP adresu, které nástroj pro vyrovnávání zatížení použije jako porty SNAT.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Časový limit nečinnosti odchozího toku a resetování TCP

Odchozí pravidla poskytují konfigurační parametr pro řízení časového limitu nečinnosti odchozího toku a jejich párování s požadavky vaší aplikace. Výchozí časový limit nečinnosti ve výchozím nastavení na 4 minuty. Další informace najdete v tématu [Konfigurace časových limitů nečinnosti](load-balancer-tcp-idle-timeout.md). 

Výchozím chováním nástroje pro vyrovnávání zatížení je vyřazení toku v tichém režimu, pokud bylo dosaženo odchozího časového limitu nečinnosti. `enableTCPReset`Parametr umožňuje předvídatelné chování aplikace a řízení. Parametr určuje, zda má být odesláno obousměrné resetování TCP (TCP RST) v časovém limitu odchozího nečinnosti. 

Zkontrolujte [časový limit nečinnosti protokolu TCP](https://aka.ms/lbtcpreset) pro podrobnosti, včetně dostupnosti oblasti.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>Explicitní zabezpečení a řízení odchozího připojení

Pravidla vyrovnávání zatížení poskytují automatické programování odchozího překladu adres (NAT). Některé scénáře mají výhodu nebo vyžadují, abyste na základě pravidla vyrovnávání zatížení zakázali automatické programování odchozího překladu adres (NAT). Vypnutí prostřednictvím pravidla vám umožní řídit nebo zdokonalovat chování.  

Tento parametr můžete použít dvěma způsoby:

1. Prevence příchozí IP adresy pro odchozí SNAT. Zakáže odchozí SNAT v pravidle vyrovnávání zatížení.
  
2. Vyladění odchozích parametrů **SNAT** pro IP adresu, která se používá pro příchozí a odchozí připojení současně. Aby bylo možné převzít řízení odchozího pravidla, musí být zakázané automatické odchozí NAT. Chcete-li změnit přidělení portu SNAT adresy, která se používá také pro příchozí, `disableOutboundSnat` parametr musí být nastaven na hodnotu true. 

Operace Konfigurace odchozího pravidla se nezdaří, pokud se pokusíte změnit definici IP adresy, která se používá pro příchozí.  Nejdřív zakažte odchozí NAT pravidla vyrovnávání zatížení.

>[!IMPORTANT]
> Pokud nastavíte tento parametr na hodnotu true a nemáte odchozí pravidlo pro definování odchozího připojení, váš virtuální počítač nebude mít odchozí připojení.  Některé operace virtuálního počítače nebo vaší aplikace můžou záviset na dostupnosti odchozího připojení. Ujistěte se, že rozumíte závislostem vašeho scénáře a že se tyto změny považují za důsledky.

V některých případech je pro vytvoření odchozího toku na virtuálním počítači nežádoucí. Může se stát, že budete muset spravovat, které cíle obdrží odchozí toky, nebo které cíle začínají vstupními toky. Pomocí [skupin zabezpečení sítě](../virtual-network/security-overview.md) můžete spravovat cíle, které virtuální počítač dosáhne. Pomocí skupin zabezpečení sítě můžete spravovat, které veřejné cíle začínají příchozí toky.

Když použijete NSG k virtuálnímu počítači s vyrovnáváním zatížení, věnujte pozornost [značkám služby](../virtual-network/security-overview.md#service-tags) a [výchozím pravidlům zabezpečení](../virtual-network/security-overview.md#default-security-rules). 

Ujistěte se, že virtuální počítač může přijímat požadavky na sondu stavu z Azure Load Balancer.

Pokud aplikace NSG blokuje požadavky na test stavu z AZURE_LOADBALANCER výchozí značky, test stavu virtuálního počítače se nepovede a virtuální počítač je označený jako nedostupný. Nástroj pro vyrovnávání zatížení zastaví odesílání nových toků do tohoto virtuálního počítače.

## <a name="scenarios-with-outbound-rules"></a>Scénáře s odchozími pravidly
        

### <a name="outbound-rules-scenarios"></a>Scénáře odchozích pravidel


* Nakonfigurujte odchozí připojení ke konkrétní sadě veřejných IP adres nebo předpony.
* Upravte přidělování portů [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) .
* Povolit pouze odchozí.
* Odchozí překlad adres (NAT) jenom pro virtuální počítače (bez příchozího připojení)
* Odchozí překlad adres (NAT) pro interní nástroj pro vyrovnávání zatížení
* Povolte protokoly TCP & UDP pro odchozí překlad adres (NAT) s veřejným nástrojem pro vyrovnávání zatížení.


### <a name="scenario-1-configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>Scénář 1: Konfigurace odchozích připojení na určitou sadu veřejných IP adres nebo předpony


#### <a name="details"></a>Podrobnosti


Tento scénář použijte k přizpůsobení odchozích připojení k původnímu ze sady veřejných IP adres. Přidejte veřejné IP adresy nebo předpony do seznamu povolených nebo odepřených adres na základě původu.


Tato veřejná IP adresa nebo předpona může být stejná jako používaná pravidlem vyrovnávání zatížení. 


Použití jiné veřejné IP adresy nebo předpony, než kterou používá pravidlo vyrovnávání zatížení: 


1. Vytvořte předponu veřejné IP adresy nebo veřejnou IP adresu.
2. Vytvoření veřejného nástroje pro vyrovnávání zatížení Standard 
3. Vytvořte front-end odkazující na předponu veřejné IP adresy nebo veřejnou IP adresu, kterou chcete použít. 
4. Znovu použijte back-end fond nebo vytvořte fond back-end a umístěte virtuální počítače do back-endového fondu veřejného nástroje pro vyrovnávání zatížení.
5. Nakonfigurujte odchozí pravidlo na veřejném nástroji pro vyrovnávání zatížení, abyste povolili odchozí překlad adres (NAT) pro virtuální počítače pomocí front-endu. Nedoporučujeme používat pro odchozí připojení pravidlo vyrovnávání zatížení, zakázat odchozí SNAT na základě pravidla vyrovnávání zatížení.


### <a name="scenario-2-modify-snat-port-allocation"></a><a name="scenario2out"></a>Scénář 2: Úprava přidělení portu [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources)


#### <a name="details"></a>Podrobnosti


Pomocí odchozích pravidel můžete vyladit [automatické přidělování portů SNAT na základě velikosti fondu back-endu](load-balancer-outbound-connections.md#preallocatedports). 


Pokud se setkáte s vyčerpáním SNAT, zvyšte počet portů [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) předaných z výchozí hodnoty 1024. 


Každá veřejná IP adresa přispívá až do 64 000 dočasných portů. Počet virtuálních počítačů v back-end fondu určuje počet portů distribuovaných do každého virtuálního počítače. Jeden virtuální počítač ve fondu back-end má přístup k maximálnímu počtu portů 64 000. U dvou virtuálních počítačů se dá určit maximálně 32 000 portů [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) s odchozím pravidlem (2x 32 000 = 64 000). 


Pomocí odchozích pravidel můžete vyladit porty SNAT zadané ve výchozím nastavení. Poskytnete více nebo méně, než je výchozí přidělení portu [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) . Každá veřejná IP adresa z front-endu odchozího pravidla přispívá až 64 000 dočasných portů, které se použijí jako porty [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) . 


Nástroj pro vyrovnávání zatížení poskytuje porty [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) v násobcích 8. Pokud zadáte hodnotu, která není dělitelná 8, operace konfigurace se odmítne. Každé pravidlo vyrovnávání zatížení a pravidlo příchozího překladu adres (NAT) budou využívat rozsah 8 portů. Pokud pravidlo vyrovnávání zatížení nebo příchozího překladu adres (NAT) sdílí stejný rozsah 8 jako jiný, nepoužijí se žádné další porty.


Pokud se pokusíte zadat více portů [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) , než je k dispozici na základě počtu veřejných IP adres, je operace konfigurace odmítnuta. Pokud například udělíte porty 10 000 pro každý virtuální počítač a sedm virtuálních počítačů v back-endovém fondu, sdílí jednu veřejnou IP adresu, konfigurace se odmítne. 7 vynásobené 10 000 překračuje limit portu 64 000. Přidejte další veřejné IP adresy do front-endu odchozího pravidla, aby se tento scénář povolil. 


Vraťte se k [výchozímu přidělení portu](load-balancer-outbound-connections.md#preallocatedports) zadáním 0 pro počet portů. První instance virtuálních počítačů 50 získají 1024 portů, 51-100 instancí virtuálních počítačů bude až do maximálního počtu instancí 512. Další informace o výchozím přidělování portů SNAT najdete v tématu [tabulka přidělení portů SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).


### <a name="scenario-3-enable-outbound-only"></a><a name="scenario3out"></a>Scénář 3: povolení pouze odchozího


#### <a name="details"></a>Podrobnosti


Využijte veřejný Nástroj pro vyrovnávání zatížení k poskytování odchozího překladu adres (NAT) pro skupinu virtuálních počítačů. V tomto scénáři použijte samotné pravidlo odchozího připojení bez dalších nakonfigurovaných pravidel.


> [!NOTE]
> Služba **Azure Virtual Network NAT** může pro virtuální počítače poskytovat odchozí připojení bez nutnosti nástroje pro vyrovnávání zatížení. Další informace najdete v tématu [co je Azure Virtual Network NAT](../virtual-network/nat-overview.md) .

### <a name="scenario-4-outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>Scénář 4: odchozí překlad adres (NAT) pouze pro virtuální počítače (bez příchozích)


> [!NOTE]
> Služba **Azure Virtual Network NAT** může pro virtuální počítače poskytovat odchozí připojení bez nutnosti nástroje pro vyrovnávání zatížení. Další informace najdete v tématu [co je Azure Virtual Network NAT](../virtual-network/nat-overview.md) .

#### <a name="details"></a>Podrobnosti


Pro tento scénář: Azure Load Balancer odchozí pravidla a Virtual Network překlad adres (NAT) jsou dostupné možnosti pro odchozí přenos dat z virtuální sítě.


1. Vytvořte veřejnou IP adresu nebo předponu.
2. Vytvořte veřejný Nástroj pro vyrovnávání zatížení. 
3. Vytvořte front-end přidružený k veřejné IP adrese nebo k vyhradení předpony pro odchozí připojení.
4. Vytvořte back-end fond pro virtuální počítače.
5. Virtuální počítače umístěte do back-endu fondu.
6. Nakonfigurujte odchozí pravidlo pro povolení odchozího překladu adres (NAT).



Pro škálování portů [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) použijte předponu nebo veřejnou IP adresu. Přidejte zdroj odchozích připojení do seznamu povolených nebo odepřených připojení.



### <a name="scenario-5-outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>Scénář 5: odchozí překlad adres (NAT) pro interní službu Load Balancer úrovně Standard


> [!NOTE]
> **Azure Virtual Network NAT** může poskytovat odchozí připojení pro virtuální počítače, které využívají interní nástroj pro vyrovnávání zatížení. Další informace najdete v tématu [co je Azure Virtual Network NAT](../virtual-network/nat-overview.md) .

#### <a name="details"></a>Podrobnosti


Odchozí připojení není k dispozici pro interní nástroj pro vyrovnávání zatížení, dokud není explicitně deklarované prostřednictvím veřejných IP adres na úrovni instance nebo Virtual Network překladem adres (NAT), nebo přidružením členů fondu back-end k konfiguraci nástroje pro vyrovnávání zatížení jen pro odchozí připojení. 


Další informace najdete v tématu [Konfigurace nástroje pro vyrovnávání zatížení jen pro odchozí](https://docs.microsoft.com/azure/load-balancer/egress-only)připojení.




### <a name="scenario-6-enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>Scénář 6: povolení protokolů TCP & UDP pro odchozí překlad adres (NAT) s veřejným nástrojem pro vyrovnávání zatížení


#### <a name="details"></a>Podrobnosti


Při použití veřejného nástroje pro vyrovnávání zatížení se zadané automatické odchozí NAT shoduje s transportním protokolem pravidla vyrovnávání zatížení. 


1. Zakažte odchozí [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) na pravidlo vyrovnávání zatížení. 
2. Nakonfigurujte odchozí pravidlo pro stejný nástroj pro vyrovnávání zatížení.
3. Znovu použijte back-end fond, který už používá vaše virtuální počítače. 
4. Jako součást odchozího pravidla zadejte "protokol": "vše". 


Když se používají jenom pravidla příchozího překladu adres (NAT), neposkytne se žádné odchozí NAT. 


1. Umístěte virtuální počítače do back-endového fondu.
2. Definujte jednu nebo víc konfigurací IP front-endu s veřejnými IP adresami nebo předponou veřejné IP adresy. 
3. Nakonfigurujte odchozí pravidlo pro stejný nástroj pro vyrovnávání zatížení. 
4. Zadejte "protokol": "All" jako součást odchozího pravidla


## <a name="limitations"></a>Omezení

- Maximální počet použitelných dočasných portů na IP adresu front-endu je 64 000.
- Rozsah konfigurovatelného odchozího časového limitu nečinnosti je 4 až 120 minut (240 až 7200 sekund).
- Load Balancer nepodporuje protokol ICMP pro odchozí překlad adres (NAT).
- Odchozí pravidla se dají použít jenom u primární konfigurace IP adresy síťového adaptéru.  Nemůžete vytvořit odchozí pravidlo pro sekundární IP adresu virtuálního počítače nebo síťové virtuální zařízení. Podporuje se víc síťových adaptérů.
- Všechny virtuální počítače v rámci **skupiny dostupnosti** se musí přidat do back-endu fondu pro odchozí připojení. 
- Všechny virtuální počítače v rámci **sady škálování virtuálních počítačů** musí být pro odchozí připojení přidané do fondu back-endu.

## <a name="next-steps"></a>Další kroky

- Další informace o [Azure Standard Load Balancer](load-balancer-overview.md)
- Přečtěte si naše [Nejčastější dotazy týkající se Azure Load Balancer](load-balancer-faqs.md)

