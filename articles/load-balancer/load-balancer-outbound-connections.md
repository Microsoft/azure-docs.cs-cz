---
title: Odchozí připojení
titleSuffix: Azure Load Balancer
description: Tento článek vysvětluje, jak Azure umožňuje virtuálním počítačům komunikovat s veřejnými službami sítě Internet.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: contperfq1
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/30/2020
ms.author: allensu
ms.openlocfilehash: 6b9f454c75a10644e86931dc86ebd9514e5431d3
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91649792"
---
# <a name="outbound-connections"></a>Odchozí připojení

Azure Load Balancer poskytuje odchozí připojení prostřednictvím různých mechanismů. Tento článek popisuje scénáře a jejich správu. 


## <a name="scenarios"></a>Scénáře

* Virtuální počítač s veřejnou IP adresou
* Virtuální počítač bez veřejné IP adresy
* Virtuální počítač bez veřejné IP adresy a bez standardního nástroje pro vyrovnávání zatížení.

### <a name="virtual-machine-with-public-ip"></a><a name="scenario1"></a>Virtuální počítač s veřejnou IP adresou

| Přidružení | Metoda | Protokoly IP |
| ---------- | ------ | ------------ |
| Veřejný Nástroj pro vyrovnávání zatížení nebo samostatný | [SNAT (zdrojový překlad adresy zdrojové sítě)](#snat) </br> [Pat (maskování portů)](#pat) se nepoužívá. | TCP (protokol řízení přenosů) </br> UDP (protokol datadatagram uživatele) </br> ICMP (Internet Control Message Protocol) </br> ESP (zapouzdření datové části zabezpečení) |

#### <a name="description"></a>Description

Azure používá veřejnou IP adresu přiřazenou ke konfiguraci protokolu IP síťové karty instance pro všechny odchozí toky. Instance má k dispozici všechny dočasné porty. Nezáleží na tom, jestli je virtuální počítač vyrovnaný k vyrovnávání zatížení. Tento scénář má přednost před ostatními. 

Veřejná IP adresa přiřazená k virtuálnímu počítači je vztah 1:1 (nikoli 1: mnoho) a implementovaný jako bezstavové 1:1 NAT.

### <a name="virtual-machine-without-public-ip"></a><a name="scenario2"></a>Virtuální počítač bez veřejné IP adresy

| Přidružení | Metoda | Protokoly IP |
| ------------ | ------ | ------------ |
| Veřejný nástroj pro vyrovnávání zatížení | Použití front-endu služby Vyrovnávání zatížení pro [SNAT](#snat) s [Pat (maskování portů)](#pat).| TCP </br> UDP |

#### <a name="description"></a>Description

Prostředek nástroje pro vyrovnávání zatížení je nakonfigurovaný s pravidlem nástroje pro vyrovnávání zatížení. Toto pravidlo slouží k vytvoření propojení mezi veřejnou IP frontou front-endu a back-end fondem. 

Pokud konfiguraci pravidla nedokončíte, bude chování popsané ve scénáři 3. 

Pro úspěšné provedení testu stavu není vyžadováno pravidlo s naslouchacím rozhraním.

Když virtuální počítač vytvoří odchozí tok, Azure převede zdrojovou IP adresu na veřejnou IP adresu front-endu veřejného nástroje pro vyrovnávání zatížení. Tento překlad se provádí přes [SNAT](#snat). 

Dočasné porty pro veřejnou IP adresu front-endu pro vyrovnávání zatížení se používají k odlišení jednotlivých toků pocházejících z virtuálního počítače. SNAT dynamicky používá [předpřidělené dočasné porty](#preallocatedports) při vytváření odchozích toků. 

V tomto kontextu se dočasné porty používané pro SNAT nazývají porty SNAT. Porty SNAT jsou předem přiděleny, jak je popsáno ve [výchozí tabulce alokace portů SNAT](#snatporttable).

### <a name="virtual-machine-without-public-ip-and-without-standard-load-balancer"></a><a name="scenario3"></a>Virtuální počítač bez veřejné IP adresy a bez standardního nástroje pro vyrovnávání zatížení

| Přidružení | Metoda | Protokoly IP |
| ------------ | ------ | ------------ |
|Žádné </br> Základní nástroj pro vyrovnávání zatížení | [SNAT](#snat) s [maskou portů (Pat)](#pat)| TCP </br> UDP | 

#### <a name="description"></a>Description

Když virtuální počítač vytvoří odchozí tok, Azure převede zdrojovou IP adresu na veřejnou zdrojovou IP adresu. Tato veřejná IP adresa **není konfigurovatelná** a nedá se rezervovat. Tato adresa se nepočítá s omezením prostředků veřejné IP adresy předplatného. 

Veřejná IP adresa se uvolní a nová veřejná IP adresa se požaduje, pokud znovu nasadíte: 

* Virtuální počítač
* Skupina dostupnosti
* Škálovací sada virtuálních počítačů  

Nepoužívejte tento scénář pro přidání IP adres do seznamu povolených serverů. Použijte scénář 1 nebo 2, kde explicitně deklarujete odchozí chování. Porty [SNAT](#snat) jsou předpřidělené, jak je popsáno ve [výchozí tabulce alokace portů SNAT](#snatporttable).



## <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>Algoritmus přidělování portů

Azure používá algoritmus k určení počtu dostupných portů [SNAT](#snat) . Algoritmus vychází z počtu portů na velikosti back-end fondu. 

Pro každou veřejnou IP adresu přidruženou k nástroji pro vyrovnávání zatížení jsou porty 64 000 k dispozici jako porty [SNAT](#snat) . Stejný počet portů [SNAT](#snat) je předpřidělený pro protokoly UDP a TCP. Porty se spotřebovávají nezávisle na protokol IP. 

Použití portu [SNAT](#snat) se liší v závislosti na tom, jestli je tok UDP nebo TCP. 

Porty se spotřebovávají dynamicky až do limitu předvyhrazeného.  Porty jsou uvolněny, když se tok ukončí nebo dojde k vypršení časového limitu nečinnosti.

Další informace o časových limitech nečinnosti najdete [v tématu řešení potíží s odchozími připojeními v Azure Load Balancer](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) 

Porty se spotřebují jenom v případě, že je potřeba, aby toky byly jedinečné.

### <a name="dynamic-snat-ports-preallocation"></a><a name="snatporttable"></a> Dynamické přidělování portů SNAT

V následující tabulce jsou uvedena předalokace portů [SNAT](#snat) pro vrstvy velikostí back-endu:

| Velikost fondu (instance virtuálních počítačů) | Předběžně přidělené porty SNAT na konfiguraci IP adres |
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801 – 1000 | 32 |

Změna velikosti back-end fondu může ovlivnit některé z vašich navázaných toků:

- Velikost fondu back-endu zvyšuje přechody triggerů do další úrovně. Polovina předpřidělených portů [SNAT](#snat) se při přechodu na další úroveň uvolní. 

- Toky spojené s neplatným časovým limitem portu [SNAT](#snat) a zavřením Tyto toky je nutné znovu vytvořit. Pokud se pokusíte o nový tok, tok se okamžitě dokončí, dokud nebudou dostupné porty k dispozici.

- Pokud se velikost fondu back-endu sníží a přejde do nižší úrovně, zvýší se počet dostupných portů [SNAT](#snat) . Existující porty [SNAT](#snat) a jejich příslušné toky nejsou ovlivněny.

## <a name="outbound-rules"></a><a name="outboundrules"></a>Pravidla odchozích přenosů

 Odchozí pravidla umožňují konfiguraci odchozího překladu síťových adres ve veřejném [standardním nástroji pro vyrovnávání zatížení](load-balancer-standard-overview.md).  

> [!NOTE]
> **Azure Virtual Network NAT** může poskytovat odchozí připojení pro virtuální počítače ve virtuální síti.  Další informace najdete v tématu [co je Azure Virtual Network NAT](../virtual-network/nat-overview.md) .

Máte plnou deklarativní kontrolu nad odchozím připojením, abyste mohli škálovat a ladit tuto možnost podle vašich potřeb.

![Odchozí pravidla nástroje pro vyrovnávání zatížení](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

U odchozích pravidel můžete k definování odchozího překladu adres (NAT) od začátku použít nástroj pro vyrovnávání zatížení. Můžete také škálovat a ladit chování stávajícího odchozího překladu adres (NAT).

Odchozí pravidla umožňují řídit:

- Které virtuální počítače by se měly přeložit na které veřejné IP adresy.
- Jak by měly být zadány Odchozí porty [SNAT](#snat) .
- Které protokoly pro zajištění odchozího překladu pro.
- Jaká doba se má použít pro časový limit nečinnosti odchozího připojení (4-120 minut).
- Určuje, zda má být odesláno resetování protokolu TCP při nečinnosti.
- Přenosové protokoly TCP i UDP s jedním pravidlem

### <a name="outbound-rule-definition"></a>Definice odchozího pravidla

Odchozí pravidla dodržují stejnou známou syntaxi jako vyrovnávání zatížení a příchozí pravidla **frontend**NAT:  +  **parameters**  +  **back-** Endu parametrů front-endu. Odchozí pravidlo konfiguruje odchozí překlad adres (NAT) pro _všechny virtuální počítače identifikované fondem back-end_ pro překlad do _front-endu_.  _Parametry_ poskytují další jemně odstupňovanou kontrolu nad odchozím algoritmem NAT.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Škálování odchozího překladu adres (NAT) s několika IP adresami

Každá další IP adresa poskytovaná front-endu poskytuje další 64 000 dočasné porty pro nástroj pro vyrovnávání zatížení, které slouží jako porty SNAT. 

Pro plánování rozsáhlých scénářů použijte více IP adres. Pomocí odchozích pravidel můžete zmírnit [vyčerpání SNAT](troubleshoot-outbound-connection.md#snatexhaust). 

Můžete také použít [předponu veřejné IP adresy](https://aka.ms/lbpublicipprefix) přímo s odchozím pravidlem. 

Předpona veřejné IP adresy zvyšuje velikost vašeho nasazení. Předponu můžete přidat do seznamu povolených toků z vašich prostředků Azure. V nástroji pro vyrovnávání zatížení můžete nakonfigurovat konfiguraci protokolu IP front-endu, aby odkazovala na předponu veřejné IP adresy.  

Nástroj pro vyrovnávání zatížení má kontrolu nad předponou veřejné IP adresy. Odchozí pravidlo automaticky použije všechny veřejné IP adresy obsažené v předponě veřejných IP adres pro odchozí připojení. 

Každá IP adresa v rámci předpony veřejných IP adres poskytuje další 64 000 dočasných portů na IP adresu, které nástroj pro vyrovnávání zatížení použije jako porty SNAT.

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Časový limit nečinnosti odchozího toku a resetování TCP

Odchozí pravidla poskytují konfigurační parametr pro řízení časového limitu nečinnosti odchozího toku a jejich párování s požadavky vaší aplikace. Výchozí časový limit nečinnosti ve výchozím nastavení na 4 minuty. Další informace najdete v tématu [Konfigurace časových limitů nečinnosti](load-balancer-tcp-idle-timeout.md). 

Výchozím chováním nástroje pro vyrovnávání zatížení je vyřazení toku v tichém režimu, pokud bylo dosaženo odchozího časového limitu nečinnosti. `enableTCPReset`Parametr umožňuje předvídatelné chování aplikace a řízení. Parametr určuje, zda má být odesláno obousměrné resetování TCP (TCP RST) v časovém limitu odchozího nečinnosti. 

Zkontrolujte [časový limit nečinnosti protokolu TCP](https://aka.ms/lbtcpreset) pro podrobnosti, včetně dostupnosti oblasti.

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Prevence odchozího připojení

Pravidla vyrovnávání zatížení poskytují automatické programování odchozího překladu adres (NAT). Některé scénáře mají výhodu nebo vyžadují, abyste na základě pravidla vyrovnávání zatížení zakázali automatické programování odchozího překladu adres (NAT). Vypnutí prostřednictvím pravidla vám umožní řídit nebo zdokonalovat chování.  

Tento parametr můžete použít dvěma způsoby:

1. Prevence příchozí IP adresy pro odchozí SNAT. Zakáže odchozí SNAT v pravidle vyrovnávání zatížení.
  
2. Vyladění odchozích parametrů [SNAT](#snat) pro IP adresu, která se používá pro příchozí a odchozí připojení současně. Aby bylo možné převzít řízení odchozího pravidla, musí být zakázané automatické odchozí NAT. Chcete-li změnit přidělení portu SNAT adresy, která se používá také pro příchozí, `disableOutboundSnat` parametr musí být nastaven na hodnotu true. 

Operace Konfigurace odchozího pravidla se nezdaří, pokud se pokusíte změnit definici IP adresy, která se používá pro příchozí.  Nejdřív zakažte odchozí NAT pravidla vyrovnávání zatížení.

>[!IMPORTANT]
> Pokud nastavíte tento parametr na hodnotu true a nemáte odchozí pravidlo pro definování odchozího připojení, váš virtuální počítač nebude mít odchozí připojení.  Některé operace virtuálního počítače nebo vaší aplikace můžou záviset na dostupnosti odchozího připojení. Ujistěte se, že rozumíte závislostem vašeho scénáře a že se tyto změny považují za důsledky.

V některých případech je pro vytvoření odchozího toku na virtuálním počítači nežádoucí. Může se stát, že budete muset spravovat, které cíle obdrží odchozí toky, nebo které cíle začínají vstupními toky. Pomocí [skupin zabezpečení sítě](../virtual-network/security-overview.md) můžete spravovat cíle, které virtuální počítač dosáhne. Pomocí skupin zabezpečení sítě můžete spravovat, které veřejné cíle začínají příchozí toky.

Když použijete NSG k virtuálnímu počítači s vyrovnáváním zatížení, věnujte pozornost [značkám služby](../virtual-network/security-overview.md#service-tags) a [výchozím pravidlům zabezpečení](../virtual-network/security-overview.md#default-security-rules). Ujistěte se, že virtuální počítač může přijímat požadavky na sondu stavu z Azure Load Balancer.

Pokud aplikace NSG blokuje požadavky na test stavu z AZURE_LOADBALANCER výchozí značky, test stavu virtuálního počítače se nepovede a virtuální počítač je označený jako nedostupný. Load Balancer zastaví odesílání nových toků do tohoto virtuálního počítače.

## <a name="scenarios-with-outbound-rules"></a>Scénáře s odchozími pravidly

### <a name="outbound-rules-scenarios"></a>Scénáře odchozích pravidel

* Nakonfigurujte odchozí připojení ke konkrétní sadě veřejných IP adres nebo předpony.
* Upravte přidělování portů [SNAT](#snat) .
* Povolit pouze odchozí.
* Odchozí překlad adres (NAT) jenom pro virtuální počítače (bez příchozího připojení)
* Odchozí překlad adres (NAT) pro interní nástroj pro vyrovnávání zatížení
* Povolte protokoly TCP & UDP pro odchozí překlad adres (NAT) s veřejným nástrojem pro vyrovnávání zatížení.

### <a name="configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>Konfigurace odchozích připojení ke konkrétní sadě veřejných IP adres nebo předpony

#### <a name="details"></a>Podrobnosti

Tento scénář použijte k přizpůsobení odchozích připojení k původnímu ze sady veřejných IP adres. Přidejte veřejné IP adresy nebo předpony do seznamu povolených nebo odepřených adres na základě původu.

Tato veřejná IP adresa nebo předpona může být stejná jako používaná pravidlem vyrovnávání zatížení. 

Použití jiné veřejné IP adresy nebo předpony, než kterou používá pravidlo vyrovnávání zatížení:  

1. Vytvořte předponu veřejné IP adresy nebo veřejnou IP adresu.
2. Vytvoření veřejného nástroje pro vyrovnávání zatížení Standard 
3. Vytvořte front-end odkazující na předponu veřejné IP adresy nebo veřejnou IP adresu, kterou chcete použít. 
4. Znovu použijte back-end fond nebo vytvořte fond back-end a umístěte virtuální počítače do back-endového fondu veřejného nástroje pro vyrovnávání zatížení.
5. Nakonfigurujte odchozí pravidlo na veřejném nástroji pro vyrovnávání zatížení, abyste povolili odchozí překlad adres (NAT) pro virtuální počítače pomocí front-endu. Pokud nechcete, aby se pravidlo vyrovnávání zatížení používalo pro odchozí, zakažte odchozí SNAT na základě pravidla vyrovnávání zatížení.

### <a name="modify-snat-port-allocation"></a><a name="scenario2out"></a>Úprava přidělení portu [SNAT](#snat)

#### <a name="details"></a>Podrobnosti

Pomocí odchozích pravidel můžete vyladit [automatické přidělování portů SNAT na základě velikosti fondu back-endu](load-balancer-outbound-connections.md#preallocatedports). 

Pokud se setkáte s vyčerpáním SNAT, zvyšte počet portů [SNAT](#snat) předaných z výchozí hodnoty 1024. 

Každá veřejná IP adresa přispívá až do 64 000 dočasných portů. Počet virtuálních počítačů v back-end fondu určuje počet portů distribuovaných do každého virtuálního počítače. Jeden virtuální počítač ve fondu back-end má přístup k maximálnímu počtu portů 64 000. U dvou virtuálních počítačů se dá určit maximálně 32 000 portů [SNAT](#snat) s odchozím pravidlem (2x 32 000 = 64 000). 

Pomocí odchozích pravidel můžete vyladit porty SNAT zadané ve výchozím nastavení. Poskytnete více nebo méně, než je výchozí přidělení portu [SNAT](#snat) . Každá veřejná IP adresa z front-endu odchozího pravidla přispívá až 64 000 dočasných portů, které se použijí jako porty [SNAT](#snat) .  

Nástroj pro vyrovnávání zatížení poskytuje porty [SNAT](#snat) v násobcích 8. Pokud zadáte hodnotu, která není dělitelná 8, operace konfigurace se odmítne. 

Pokud se pokusíte zadat více portů [SNAT](#snat) , než je k dispozici na základě počtu veřejných IP adres, je operace konfigurace odmítnuta.

Pokud udělíte porty 10 000 pro každý virtuální počítač a sedm virtuálních počítačů v back-endu fondu, který sdílí jednu veřejnou IP adresu, konfigurace se odmítne. 7 vynásobené 10 000 překračuje limit portu 64 000. Přidejte další veřejné IP adresy do front-endu odchozího pravidla, aby se tento scénář povolil. 

Vraťte se k [výchozímu přidělení portu](load-balancer-outbound-connections.md#preallocatedports) zadáním 0 pro počet portů. První instance virtuálních počítačů 50 získají 1024 portů, 51-100 instancí virtuálních počítačů bude až do maximálního počtu instancí 512.  Další informace o výchozím přidělování portů SNAT najdete v tématu [tabulka přidělení portů SNAT](#snatporttable).

### <a name="enable-outbound-only"></a><a name="scenario3out"></a>Povolit pouze odchozí

#### <a name="details"></a>Podrobnosti

Využijte veřejný Nástroj pro vyrovnávání zatížení k poskytování odchozího překladu adres (NAT) pro skupinu virtuálních počítačů. V tomto scénáři použijte samotné pravidlo odchozího připojení bez nutnosti dalších pravidel.

> [!NOTE]
> Služba **Azure Virtual Network NAT** může pro virtuální počítače poskytovat odchozí připojení bez nutnosti nástroje pro vyrovnávání zatížení.  Další informace najdete v tématu [co je Azure Virtual Network NAT](../virtual-network/nat-overview.md) .

### <a name="outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>Odchozí překlad adres (NAT) pouze pro virtuální počítače (žádné příchozí)

> [!NOTE]
> Služba **Azure Virtual Network NAT** může pro virtuální počítače poskytovat odchozí připojení bez nutnosti nástroje pro vyrovnávání zatížení.  Další informace najdete v tématu [co je Azure Virtual Network NAT](../virtual-network/nat-overview.md) .

#### <a name="details"></a>Podrobnosti

Pro tento scénář:

1. Vytvořte veřejnou IP adresu nebo předponu.
2. Vytvořte veřejný Nástroj pro vyrovnávání zatížení. 
3. Vytvořte front-end přidružený k veřejné IP adrese nebo k vyhradení předpony pro odchozí připojení.
4. Vytvořte back-end fond pro virtuální počítače.
5. Virtuální počítače umístěte do back-endu fondu.
6. Nakonfigurujte odchozí pravidlo pro povolení odchozího překladu adres (NAT).

Pro škálování portů [SNAT](#snat) použijte předponu nebo veřejnou IP adresu. Přidejte zdroj odchozích připojení do seznamu povolených nebo odepřených připojení.

### <a name="outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>Odchozí překlad adres (NAT) pro interní nástroj pro vyrovnávání zatížení

> [!NOTE]
> **Azure Virtual Network NAT** může poskytovat odchozí připojení pro virtuální počítače, které využívají interní nástroj pro vyrovnávání zatížení.  Další informace najdete v tématu [co je Azure Virtual Network NAT](../virtual-network/nat-overview.md) .

#### <a name="details"></a>Podrobnosti

Odchozí připojení není k dispozici pro interní nástroj pro vyrovnávání zatížení, dokud není explicitně deklarované. 

Další informace najdete v tématu [Konfigurace nástroje pro vyrovnávání zatížení jen pro odchozí](https://docs.microsoft.com/azure/load-balancer/egress-only)připojení.


### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>Povolte protokoly TCP & UDP pro odchozí překlad adres (NAT) s veřejným nástrojem pro vyrovnávání zatížení.

#### <a name="details"></a>Podrobnosti

Při použití veřejného nástroje pro vyrovnávání zatížení se zadané automatické odchozí NAT shoduje s transportním protokolem pravidla vyrovnávání zatížení. 

1. Zakažte odchozí [SNAT](#snat) na pravidlo vyrovnávání zatížení. 
2. Nakonfigurujte odchozí pravidlo pro stejný nástroj pro vyrovnávání zatížení.
3. Znovu použijte back-end fond, který už používá vaše virtuální počítače. 
4. Jako součást odchozího pravidla zadejte "protokol": "vše". 

Když se používají jenom pravidla příchozího překladu adres (NAT), neposkytne se žádné odchozí NAT. 

1. Umístěte virtuální počítače do back-endového fondu.
2. Definujte jednu nebo víc konfigurací IP front-endu s veřejnými IP adresami nebo předponou veřejné IP adresy. 
3. Nakonfigurujte odchozí pravidlo pro stejný nástroj pro vyrovnávání zatížení. 
4. Zadejte "protokol": "All" jako součást odchozího pravidla

## <a name="terminology"></a><a name="terms"></a> Vede

### <a name="source-network-address-translation"></a><a name="snat"></a>Překlad zdrojové síťové adresy

| Příslušný protokol (y) |
|------------------------|
| TCP </br> UDP          |

#### <a name="details"></a>Podrobnosti

Nasazení v Azure může komunikovat s koncovými body mimo Azure ve veřejném adresním prostoru IP adres.

Když instance spustí odchozí provoz do cílového umístění s veřejnou IP adresou, Azure dynamicky namapuje soukromou IP adresu prostředku na veřejnou IP adresu. 

Po vytvoření tohoto mapování se vrátí přenos pro tento odchozí výstupní tok a dosáhne privátní IP adresy, kde tok vznikl. 

Pro tuto funkci Azure používá **Překlad zdrojového síťového adres (SNAT)** .

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>Maskování portů SNAT (PAT)

| Příslušný protokol (y) |
|------------------------|
| TCP </br> UDP          |

#### <a name="details"></a>Podrobnosti

Pokud jsou privátní IP adresy za jednou veřejnou IP adresou, Azure k skrytí privátních IP adres používá službu pro **Překlad adres portů (Pat)** . 

Dočasné porty se používají pro PAT a jsou [vyčerpány](#preallocatedports) na základě velikosti fondu. 

Když je veřejný Nástroj pro vyrovnávání zatížení přidružený k virtuálním počítačům bez veřejných IP adres, přepíše se každý zdroj odchozího připojení. 

Zdroj je přepsaný z privátní IP adresy virtuální sítě na veřejnou IP adresu front-endu pro vyrovnávání zatížení. 

Ve veřejném adresním prostoru IP adres musí být pět záznamů toku jedinečné:

* Zdrojová IP adresa
* Zdrojový port
* Protokol IP Transport
* Cílová IP adresa
* Cílový port 

Maskování portů SNAT se dá použít buď s protokoly TCP, nebo UDP. Porty SNAT se používají po přepsání privátní zdrojové IP adresy, protože více toků pochází z jedné veřejné IP adresy. Algoritmus SNAT pro maskování portů pro protokol UDP vs TCP poskytuje jiné porty SNAT.

### <a name="snat-ports-tcp"></a>Porty SNAT (TCP)

| Příslušný protokol (y) |
|------------------------|
| TCP |

#### <a name="details"></a>Podrobnosti

Porty SNAT jsou dočasné porty, které jsou k dispozici pro veřejnou IP adresu. Jeden port SNAT se spotřebovává na jeden tok do jedné cílové IP adresy a portu. 

U více toků TCP se stejnou cílovou IP adresou, portem a protokolem každý tok TCP spotřebovává jeden port SNAT. 

Tato spotřeba zajišťuje, že toky jsou jedinečné, když pocházejí ze stejné veřejné IP adresy a cestují k:

* Stejná cílová IP adresa
* Port
* Protokol 

Více toků sdílí jeden port SNAT. 

Cílová IP adresa, port a protokol vytvářejí toky jedinečné bez nutnosti dalších zdrojových portů, které by rozlišily toky ve veřejném adresním prostoru IP adres.


### <a name="snat-ports-udp"></a>Porty SNAT (UDP)

| Příslušný protokol (y) |
|------------------------|
| UDP |

#### <a name="details"></a>Podrobnosti

Porty UDP SNAT jsou spravovány jiným algoritmem než porty TCP SNAT.  Nástroj pro vyrovnávání zatížení používá algoritmus s názvem kuželový překlad adres (NAT) pro protokol UDP.

Pro každou cílovou IP adresu a port každého toku se spotřebuje jeden port SNAT.


### <a name="exhaustion"></a><a name="exhaustion"></a>Vyčerpání

| Příslušný protokol (y) |
|------------------------|
| – |

#### <a name="details"></a>Podrobnosti

Po vyčerpání prostředků portů SNAT dojde k selhání odchozích toků, dokud nebudou stávající toky vydány porty SNAT. Nástroj pro vyrovnávání zatížení po ukončení toku obnoví porty SNAT.

Nástroj pro vyrovnávání zatížení využije [časový limit nečinnosti](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) čtyři minuty k uvolnění portů SNAT.

Porty UDP SNAT jsou obvykle vyčerpány mnohem rychleji než porty TCP SNAT z důvodu rozdílu používaného algoritmu. Vyzkoušejte návrh a proveďte změnu měřítka z důvodu tohoto rozdílu.

### <a name="snat-port-release-behavior-tcp"></a>Chování vydání portu SNAT (TCP)

| Příslušný protokol (y) |
|------------------------|
| TCP |

#### <a name="details"></a>Podrobnosti

Když server nebo klient pošle FINACK, port SNAT se uvolní po 240 sekundách. V případě, že se zobrazuje RST, port SNAT se uvolní po 15 sekundách. Pokud byl dosažen časový limit nečinnosti, je port uvolněn.

### <a name="snat-port-release-behavior-udp"></a>Chování vydání portu SNAT (UDP)

| Příslušný protokol (y) |
|------------------------|
| TCP |

#### <a name="details"></a>Podrobnosti

Při dosažení časového limitu nečinnosti se port uvolní.

### <a name="snat-port-reuse"></a>Opakované použití portu SNAT

| Příslušný protokol (y) |
|------------------------|
| TCP </br> UDP |

#### <a name="details"></a>Podrobnosti

Po uvolnění portu je možné ho znovu použít. Porty SNAT jsou sekvence od nejnižší k dispozici pro daný scénář a první dostupný port SNAT se používá pro nová připojení.

## <a name="limitations"></a>Omezení

- Maximální počet použitelných dočasných portů na IP adresu front-endu je 64 000.
- Rozsah konfigurovatelného odchozího časového limitu nečinnosti je 4 až 120 minut (240 až 7200 sekund).
- Load Balancer nepodporuje protokol ICMP pro odchozí překlad adres (NAT).
- Odchozí pravidla se dají použít jenom u primární konfigurace IP adresy síťového adaptéru.  Nemůžete vytvořit odchozí pravidlo pro sekundární IP adresu virtuálního počítače nebo síťové virtuální zařízení. Podporuje se víc síťových adaptérů.
- Role webového pracovního procesu bez virtuální sítě a dalších služeb platformy Microsoft mohou být přístupné při použití interního nástroje pro vyrovnávání zatížení. Tato přístupnost je z důvodu vedlejšího efektu provozu služeb a dalších služeb platformy. Nespoléhá se na tento vedlejší účinek jako na samotnou službu ani na podkladovou platformu se může změnit bez předchozího upozornění. Při použití interního standardního nástroje pro vyrovnávání zatížení vždy předpokládat, že je potřeba vytvořit odchozí připojení explicitně. Scénář 3 popsaný v tomto článku není k dispozici.

## <a name="next-steps"></a>Další kroky

Pokud máte problémy s odchozím připojením prostřednictvím Azure Load Balancer, přečtěte si článek [Průvodce odstraňováním potíží pro odchozí připojení](../load-balancer/troubleshoot-outbound-connection.md).

- Přečtěte si další informace o [službě Load Balancer úrovně Standard](load-balancer-standard-overview.md).
- Přečtěte si naše [Nejčastější dotazy týkající se Azure Load Balancer](load-balancer-faqs.md).
- Přečtěte si další informace o [odchozích pravidlech](load-balancer-outbound-rules-overview.md) pro standardní veřejný Nástroj pro vyrovnávání zatížení.

