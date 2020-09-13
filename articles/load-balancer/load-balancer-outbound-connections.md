---
title: Odchozí připojení v Azure
titleSuffix: Azure Load Balancer
description: Tento článek vysvětluje, jak Azure umožňuje virtuálním počítačům komunikovat s veřejnými službami sítě Internet.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/24/2020
ms.author: allensu
ms.openlocfilehash: 4368a025ecc158afa1ee78b8abd86bd6db42ba75
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89438661"
---
# <a name="outbound-connections-in-azure"></a>Odchozí připojení v Azure

Azure Load Balancer poskytuje odchozí připojení prostřednictvím různých mechanismů. Tento článek popisuje scénáře a jejich správu. Pokud dochází k potížím s odchozím připojením prostřednictvím Azure Load Balancer, přečtěte si článek [Průvodce odstraňováním potíží pro odchozí připojení](../load-balancer/troubleshoot-outbound-connection.md).

>[!NOTE]
>Tento článek popisuje nasazení Správce prostředků. Microsoft doporučuje Správce prostředků pro produkční úlohy.

## <a name="terminology"></a>Terminologie

| Označení | Příslušný protokol (y) | Podrobnosti|
| ---------|---------| -------|
| Překlad zdrojové síťové adresy (SNAT) | TCP, UDP | Nasazení v Azure může komunikovat s koncovými body mimo Azure ve veřejném adresním prostoru IP adres. Když instance inicializuje odchozí tok do cílového umístění ve veřejném adresním prostoru IP adres, Azure dynamicky mapuje privátní IP adresu na veřejnou IP adresu. Po vytvoření tohoto mapování se může vrátit přenos pro tento odchozí výstupní tok a získat tak privátní IP adresu, ve které tok vznikl. Azure používá k provedení této funkce **Překlad zdrojového síťového adres (SNAT)** .|
| Maskování portů SNAT (PAT)| TCP, UDP |  Při maskování více privátních IP adres za jednou veřejnou IP adresou využívá Azure k maskování/skrytí privátních IP adres **port (Pat) adresování portů** . Dočasné porty se používají pro PAT a jsou [vyčerpány](#preallocatedports) na základě velikosti fondu. Pokud je k instancím virtuálních počítačů, které nemají vyhrazené veřejné IP adresy, přidružen prostředek veřejné Load Balancer, je nutné přepsán každý zdroj odchozího připojení. Zdroj se přepíše z privátního adresního prostoru privátních IP adres virtuální sítě na front-endové veřejné IP adresy nástroje pro vyrovnávání zatížení. Ve veřejném adresním prostoru IP adres musí být pět záznamů toku (zdrojová IP adresa, zdrojový port, transportní protokol IP, cílová IP adresa, cílový port) jedinečný. Maskování portů SNAT se dá použít buď s protokolem IP TCP nebo UDP. Dočasné porty (porty SNAT) se používají k dosažení tohoto toho po přepsání privátní zdrojové IP adresy, protože více toků pochází z jedné veřejné IP adresy. Algoritmus SNAT maskování portů přiděluje porty SNAT odlišně pro UDP a TCP.|
| Porty SNAT| TCP | Porty SNAT jsou dočasné porty, které jsou k dispozici pro konkrétní zdrojovou adresu veřejné IP adresy. Jeden port SNAT se spotřebovává na jeden tok na jednu cílovou IP adresu, port. U více toků TCP se stejnou cílovou IP adresou, portem a protokolem každý tok TCP spotřebovává jeden port SNAT. Tím je zajištěno, že toky budou jedinečné, když pocházejí ze stejné veřejné IP adresy a jdou na stejnou cílovou IP adresu, port a protokol. Několik toků, z nichž každý má jinou cílovou IP adresu, port a protokol, sdílí jeden port SNAT. Cílová IP adresa, port a protokol vytvářejí toky jedinečné bez nutnosti dalších zdrojových portů, které by rozlišily toky ve veřejném adresním prostoru IP adres.|
|Porty SNAT | UDP | Porty UDP SNAT jsou spravovány jiným algoritmem než porty TCP SNAT.  Load Balancer používá algoritmus, který se označuje jako "kuželový překlad adres (NAT)" na portu UDP.  Pro každý tok se spotřebuje jeden port SNAT bez ohledu na cílovou IP adresu a port.|
| Vyčerpání | - | Po vyčerpání prostředků portů SNAT dojde k selhání odchozích toků, dokud nebudou stávající toky vydány porty SNAT. Load Balancer redeklaracích portů SNAT po ukončení toku a používá [prodlevu nečinnosti ve 4 minutách](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) pro uvolnění portů SNAT z nečinných toků. Porty UDP SNAT jsou obvykle vyčerpány mnohem rychleji než porty TCP SNAT z důvodu rozdílu používaného algoritmu. Tento rozdíl je nutné navrhovat a škálovat.|
| Chování vydání portu SNAT | TCP | Pokud některý ze serverů/klientů odesílá FINACK, port SNAT se uvolní po 240 sekundách. Pokud je zjištěna RST, port SNAT se uvolní po 15 sekundách. Pokud bylo dosaženo časového limitu nečinnosti, je port uvolněn.|
| Chování vydání portu SNAT | UDP |Pokud bylo dosaženo časového limitu nečinnosti, je port uvolněn.|
| Opakované použití portu SNAT | TCP, UDP | Po uvolnění portu je možné port v případě potřeby znovu použít.  Porty SNAT si můžete představit jako sekvence od nejnižší po nejvyšší dostupnou pro daný scénář a jako první dostupný port SNAT se použije pro nová připojení.|

### <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>Algoritmus přidělování portů

Azure používá algoritmus k určení počtu předem přidělených portů SNAT na základě velikosti back-endu při použití PAT. Pro každou veřejnou IP adresu přidruženou k nástroji pro vyrovnávání zatížení jsou porty 64 000 dostupné jako porty SNAT pro každý transportní protokol IP. Stejný počet portů SNAT se má předběžně přidělit pro UDP a TCP a spotřebovat nezávisle na IP přenosový protokol.  Použití portu SNAT se ale liší v závislosti na tom, jestli je tok UDP nebo TCP. Při vytváření odchozích toků se tyto porty spotřebují dynamicky (až do limitu před přidělením) a vydávají se v případě, že dojde k ukončení toku nebo [vypršení nečinnosti](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) . Porty se spotřebují jenom v případě, že je potřeba, aby toky byly jedinečné.

#### <a name="dynamic-snat-ports-allocated"></a><a name="snatporttable"></a> Přidělené dynamické porty SNAT

V následující tabulce jsou uvedena předalokace portů SNAT pro vrstvy velikostí back-endu:

| Velikost fondu (instance virtuálních počítačů) | Předběžně přidělené porty SNAT na konfiguraci IP adres |
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801 – 1000 | 32 |

Změna velikosti back-end fondu může ovlivnit některé z vašich navázaných toků:

- Pokud se velikost fondu back-endu zvyšuje a přejde do další úrovně, během přechodu na další větší úroveň fondu back-end se uvolní polovina předpřidělených portů SNAT. Toky, které jsou přidružené k uvolněnému portu SNAT, vyprší časový limit a je nutné je znovu vytvořit. Pokud se pokusíte o nový tok, tok se okamžitě dokončí, dokud nebudou dostupné porty k dispozici.
- Pokud se velikost fondu back-endu zmenší a přejde do nižší úrovně, zvýší se počet dostupných portů SNAT. V takovém případě nebudou ovlivněny existující porty SNAT a jejich příslušné toky.

## <a name="outbound-connections-scenario-overview"></a><a name="scenarios"></a>Přehled scénáře odchozích připojení

| Scénář | Metoda | Protokoly IP | Popis |
|  --- | --- | --- | --- |
|  1. virtuální počítač s veřejnou IP adresou (s Azure Load Balancer nebo bez něj | SNAT, maskování portů se nepoužívá. | TCP, UDP, ICMP, ESP | Azure používá veřejnou IP adresu přiřazenou ke konfiguraci protokolu IP síťové karty instance pro všechny odchozí toky. Instance má k dispozici všechny dočasné porty. Nezáleží na tom, jestli je virtuální počítač vyrovnaný k vyrovnávání zatížení. Tento scénář má přednost před ostatními. Veřejná IP adresa přiřazená k virtuálnímu počítači je vztah 1:1 (nikoli 1: mnoho) a implementovaný jako bezstavové 1:1 NAT. |
| 2. veřejné Load Balancer přidružené k virtuálnímu počítači (žádná veřejná IP adresa na virtuálním počítači/instanci) | SNAT s maskou portů (PAT) pomocí Load Balancer front-endu | TCP, UDP | V tomto scénáři musí být prostředek Load Balancer nakonfigurovaný s pravidlem nástroje pro vyrovnávání zatížení, aby bylo možné vytvořit propojení mezi veřejnou IP frontou a back-end fondem. Pokud konfiguraci pravidla nedokončíte, bude chování popsané ve scénáři 3. Pro úspěšné provedení testu stavu není nutné, aby pravidlo mělo funkční naslouchací proces ve fondu back-endu. Když virtuální počítač vytvoří odchozí tok, Azure převede privátní zdrojovou IP adresu odchozího toku na veřejnou IP adresu veřejného Load Balancer front-SNAT. Dočasné porty veřejné IP adresy front-endu nástroje pro vyrovnávání zatížení se používají k odlišení jednotlivých toků, které pocházejí z virtuálního počítače. SNAT dynamicky používá [předpřidělené dočasné porty](#preallocatedports) při vytváření odchozích toků. V tomto kontextu se dočasné porty používané pro SNAT nazývají porty SNAT. Porty SNAT jsou předem přiděleny, jak je popsáno v [tabulce výchozí přidělené porty SNAT](#snatporttable). |
| 3. VM (bez Load Balancer, žádná veřejná IP adresa) nebo virtuální počítač přidružený k základnímu internímu Load Balancer | SNAT s maskou portů (PAT) | TCP, UDP | Když virtuální počítač vytvoří odchozí tok, Azure převede IP adresu privátního zdroje odchozího toku do veřejné zdrojové IP adresy. Tato veřejná IP adresa se nedá **nakonfigurovat**, nedá se rezervovat a nepočítá se s limitem veřejných IP adres předplatného. Pokud znovu nasadíte virtuální počítač nebo skupinu dostupnosti nebo sadu škálování virtuálního počítače, tato veřejná IP adresa se uvolní a vyžádá se nová veřejná IP adresa. Nepoužívejte tento scénář pro seznam povolených IP adres. Místo toho použijte scénář 1 nebo 2, kde explicitně deklarujete odchozí chování. Porty SNAT jsou přednastavené tak, jak je popsáno v [tabulce výchozí přidělené porty SNAT](#snatporttable).

## <a name="outbound-rules"></a><a name="outboundrules"></a>Pravidla odchozích přenosů

 Odchozí pravidla usnadňují konfiguraci odchozího překladu síťových adres pro veřejné [Standard Load Balancer](load-balancer-standard-overview.md).  Máte plnou deklarativní kontrolu nad odchozím připojením, abyste mohli škálovat a ladit tuto možnost podle vašich konkrétních potřeb. V této části se rozbalí scénář 2 (B), který je popsaný výše.

![Load Balancer odchozí pravidla](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

U odchozích pravidel můžete použít Load Balancer k definování odchozího překladu adres (NAT) od začátku. Můžete také škálovat a ladit chování stávajícího odchozího překladu adres (NAT).

Odchozí pravidla umožňují řídit:

- které virtuální počítače by se měly přeložit na které veřejné IP adresy.
- Jak by měly být přiděleny Odchozí porty SNAT.
- které protokoly pro zajištění odchozího překladu pro.
- Jaká doba se má použít pro časový limit nečinnosti odchozího připojení (4-120 minut).
- Určuje, zda má být odesláno resetování protokolu TCP při nečinnosti.
- přenosové protokoly TCP i UDP s jedním pravidlem

### <a name="outbound-rule-definition"></a>Definice odchozího pravidla

Stejně jako u všech pravidel Load Balancer se odchozí pravidla řídí stejnou známou syntaxí jako vyrovnávání zatížení a příchozí pravidla **NAT:**  +  **parameters**  +  **back-end**Parameters Parameters. Odchozí pravidlo konfiguruje odchozí překlad adres (NAT) pro _všechny virtuální počítače identifikované fondem back-end_ pro překlad do _front-endu_.  _Parametry_ poskytují další jemně odstupňovanou kontrolu nad odchozím algoritmem NAT.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Škálování odchozího překladu adres (NAT) s několika IP adresami

Každá další IP adresa poskytovaná front-endu poskytuje další 64 000 dočasných portů, které Load Balancer použít jako porty SNAT. Pro plánování rozsáhlých scénářů můžete použít více IP adres a pomocí odchozích pravidel můžete zmírnit vzory náchylné k [vyčerpání SNAT](troubleshoot-outbound-connection.md#snatexhaust) .  

Můžete také použít [předponu veřejné IP adresy](https://aka.ms/lbpublicipprefix) přímo s odchozím pravidlem.  Použití předpony veřejných IP adres poskytuje snazší škálování a zjednodušený seznam toků pocházejících z nasazení Azure. Konfiguraci IP adresy front-endu můžete v rámci prostředku Load Balancer nakonfigurovat tak, aby odkazovala přímo na předponu veřejné IP adresy.  To umožňuje Load Balancer exkluzivní kontrolu nad předponou veřejné IP adresy a odchozí pravidlo automaticky použije všechny veřejné IP adresy obsažené v předponě veřejných IP adres pro odchozí připojení.  Každá IP adresa v rámci předpony veřejných IP adres poskytuje další 64 000 dočasných portů na IP adresu, které Load Balancer použít jako porty SNAT.

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Časový limit nečinnosti odchozího toku a resetování TCP

Odchozí pravidla poskytují konfigurační parametr pro řízení časového limitu nečinnosti odchozího toku a jejich párování s požadavky vaší aplikace. Výchozí časový limit nečinnosti ve výchozím nastavení na 4 minuty. Můžete se dozvědět, jak [nakonfigurovat časový limit nečinnosti](load-balancer-tcp-idle-timeout.md#tcp-idle-timeout). Výchozím chováním Load Balancer je vyřazení toku v tichém režimu v případě dosažení odchozího časového limitu nečinnosti.  Pomocí `enableTCPReset` parametru můžete povolit předvídatelné chování aplikace a určit, jestli se má v době nečinnosti odchozího nečinnosti odesílat obousměrné resetování protokolu TCP (TCP RST). Zkontrolujte [časový limit nečinnosti protokolu TCP](https://aka.ms/lbtcpreset) pro podrobnosti, včetně dostupnosti oblasti.

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Prevence odchozího připojení

Pravidla vyrovnávání zatížení poskytují automatické programování odchozího překladu adres (NAT). Některé scénáře ale mají výhodu nebo vyžadují, abyste v rámci pravidla vyrovnávání zatížení zakázali automatické programování odchozího překladu adres (NAT), které vám umožní ovládací prvek nebo Upřesnit jeho chování.  
Tento parametr můžete použít dvěma způsoby:

1. Volitelné potlačení používání příchozí IP adresy pro odchozí SNAT prostřednictvím zakázání odchozích SNAT pro pravidlo vyrovnávání zatížení
  
2. Vyladění odchozích parametrů SNAT pro IP adresu, která se používá pro příchozí a odchozí připojení současně.  Aby bylo možné převzít řízení odchozího pravidla, musí být toto automatické programování NAT zakázané.  Chcete-li například změnit přidělení portu SNAT adresy pro příchozí použití, `disableOutboundSnat` musí být parametr nastaven na hodnotu true.  Pokud se pokusíte použít odchozí pravidlo k předefinování parametrů IP adresy, která se používá také pro příchozí a která neuvolnila odchozí programování služby NAT pravidla vyrovnávání zatížení, operace Konfigurace odchozího pravidla se nezdaří.

>[!IMPORTANT]
> Pokud nastavíte tento parametr na hodnotu true a nemáte odchozí pravidlo pro definování odchozího připojení, váš virtuální počítač nebude mít odchozí připojení.  Některé operace virtuálního počítače nebo vaší aplikace můžou záviset na dostupnosti odchozího připojení. Ujistěte se, že rozumíte závislostem vašeho scénáře a že se tyto změny považují za důsledky.

V některých případech je možné, že virtuální počítač nebude moci vytvořit odchozí tok. Nebo může být potřeba, abyste mohli spravovat, které cíle je možné dosáhnout u odchozích toků, nebo které cíle můžou začít s příchozími toky. V takovém případě můžete pomocí [skupin zabezpečení sítě](../virtual-network/security-overview.md) spravovat cíle, ke kterým může virtuální počítač získat přístup. Pomocí skupin zabezpečení sítě můžete také spravovat, které veřejné cíle mohou iniciovat příchozí toky.

Když použijete NSG k virtuálnímu počítači s vyrovnáváním zatížení, věnujte pozornost [značkám služby](../virtual-network/security-overview.md#service-tags) a [výchozím pravidlům zabezpečení](../virtual-network/security-overview.md#default-security-rules). Musíte zajistit, aby virtuální počítač mohl přijímat požadavky na sondu stavu z Azure Load Balancer.

Pokud NSG blokuje požadavky na test stavu z AZURE_LOADBALANCER výchozí značky, test stavu virtuálního počítače se nepovede a virtuální počítač se označí jako neplatný. Load Balancer zastaví odesílání nových toků do tohoto virtuálního počítače.

## <a name="scenarios-with-outbound-rules"></a>Scénáře s odchozími pravidly

| # | Scénář| Podrobnosti|
|---|---|---|
| I | Odchozí připojení výmazu dat ke konkrétní sadě veřejných IP adres| Můžete použít odchozí pravidlo pro vymazání odchozích připojení, která se mají vyskytnout z konkrétní sady veřejných IP adres, aby se usnadnily scénáře přidávání na seznam povolených.  Tato veřejná IP adresa se může shodovat s použitím pravidla vyrovnávání zatížení nebo jiné sady veřejných IP adres, než je používá pravidlo vyrovnávání zatížení.  1. Vytvořte [předponu veřejné IP](https://aka.ms/lbpublicipprefix) adresy (nebo veřejné IP adresy z předpony veřejné IP adresy) 2. Vytvořte veřejnou Standard Load Balancer 3. Vytvořte front-endové odkazy na předponu veřejné IP adresy (nebo veřejné IP adresy), které chcete použít 4. Znovu použijte back-end fond nebo vytvořte fond back-end a umístěte virtuální počítače do back-endového fondu veřejné Load Balancer 5. Nakonfigurujte odchozí pravidlo na veřejném Load Balancer pro programování odchozího překladu adres (NAT) pro tyto virtuální počítače pomocí front-endu. Pokud nechcete, aby se pravidlo vyrovnávání zatížení používalo pro odchozí připojení, musíte zakázat odchozí SNAT v pravidle vyrovnávání zatížení.
| ODST | Úprava přidělení portu SNAT| Pomocí odchozích pravidel můžete vyladit [automatické přidělování portů SNAT na základě velikosti fondu back-endu](load-balancer-outbound-connections.md#preallocatedports). Pokud například máte dva virtuální počítače sdílející jednu veřejnou IP adresu pro odchozí NAT, možná budete chtít zvýšit počet portů SNAT přidělených z výchozích portů 1024, pokud máte vyčerpání SNAT. Každá veřejná IP adresa může přispívat až 64 000 dočasných portů.  Pokud nakonfigurujete odchozí pravidlo s jednou front-endu veřejné IP adresy, můžete do virtuálních počítačů ve fondu back-endu distribuovat celkem 64 000 portů SNAT.  Pro dva virtuální počítače se dá přidělit maximálně 32 000 portů SNAT pomocí odchozího pravidla (2x 32 000 = 64 000). Pomocí odchozích pravidel můžete vyladit porty SNAT přiřazené ve výchozím nastavení. Přidělíte více nebo méně, než je výchozí přidělení portu SNAT. Každá veřejná IP adresa ze všech front-endu odchozího pravidla přispívá až 64 000 dočasných portů, které se použijí jako porty SNAT.  Load Balancer přiděluje porty SNAT v násobcích 8. Pokud zadáte hodnotu, která není dělitelná 8, operace konfigurace se odmítne.  Pokud se pokusíte přidělit více portů SNAT, než je k dispozici na základě počtu veřejných IP adres, je operace konfigurace odmítnuta.  Pokud například přidělíte porty 10 000 na virtuální počítač a 7 virtuálních počítačů ve fondu back-end, bude se jednat o jednu veřejnou IP adresu, konfigurace se odmítne (7 x 10 000 SNAT portů > 64 000 portů SNAT).  Můžete přidat další veřejné IP adresy do front-endu odchozího pravidla a povolit tak scénář. Můžete se vrátit k [výchozímu přidělování portů SNAT na základě velikosti fondu back-endu](load-balancer-outbound-connections.md#preallocatedports) zadáním 0 pro počet portů. V takovém případě získají první 50 instancí virtuálních počítačů 1024 portů, 51-100 instancí virtuálních počítačů bude 512 a tak podle [tabulky](#snatporttable).|
| ODDÍLE| Povolit pouze odchozí | K poskytování odchozího překladu adres (NAT) pro skupinu virtuálních počítačů můžete použít veřejné Standard Load Balancer. V tomto scénáři můžete použít odchozí pravidlo samostatně, aniž byste museli mít žádná další pravidla.|
| IV | Odchozí překlad adres (NAT) pouze pro virtuální počítače (žádné příchozí) | Definujte veřejné Standard Load Balancer, umístěte virtuální počítače do back-endu fondu a nakonfigurujte odchozí pravidlo pro programování odchozího překladu adres (NAT) a výmaz dat z konkrétní veřejné IP adresy. Můžete také použít předponu veřejné IP adresy zjednodušit výpis zdroje odchozích připojení. 1. Vytvořte veřejnou Standard Load Balancer. 2. Vytvořte back-end fond a umístěte virtuální počítače do back-endového fondu veřejné Load Balancer. 3. Nakonfigurujte odchozí pravidlo na veřejném Load Balancer pro programování odchozího překladu adres (NAT) pro tyto virtuální počítače.
| V| Odchozí překlad adres (NAT) pro interní Standard Load Balancer scénáře| Při použití interního Standard Load Balancer není odchozí překlad adres (NAT) dostupný, dokud není explicitně deklarované odchozí připojení. Odchozí připojení můžete definovat pomocí odchozího pravidla pro vytvoření odchozího připojení pro virtuální počítače za interním Standard Load Balancer s těmito kroky: 1. Vytvořte veřejnou Standard Load Balancer. 2. Vytvořte fond back-end a umístěte virtuální počítače do back-endového fondu veřejných Load Balancer kromě interní Load Balancer. 3. Nakonfigurujte odchozí pravidlo na veřejném Load Balancer pro programování odchozího překladu adres (NAT) pro tyto virtuální počítače. Další podrobnosti o tomto scénáři najdete v [tomto příkladu](https://docs.microsoft.com/azure/load-balancer/egress-only). |
| VI | Povolit protokoly TCP & UDP pro odchozí překlad adres (NAT) s veřejným Standard Load Balancer | Při použití veřejné Standard Load Balancer se zadané automatické odchozí přenosy NAT shodují s transportním protokolem pravidla vyrovnávání zatížení. 1. Zakáže odchozí SNAT u pravidla vyrovnávání zatížení. 2. Nakonfigurujte odchozí pravidlo pro stejné Load Balancer. 3. Znovu použijte back-end fond, který už používá vaše virtuální počítače. 4. Jako součást odchozího pravidla zadejte "protokol": "vše". Když se používají jenom pravidla příchozího překladu adres (NAT), neposkytne se žádné odchozí NAT. 1. Umístěte virtuální počítače do back-endového fondu. 2. Definujte jednu nebo víc konfigurací IP front-endu s veřejnými IP adresami nebo předponou veřejné IP adresy 3. Nakonfigurujte odchozí pravidlo pro stejné Load Balancer. 4. Zadejte "protokol": "All" jako součást odchozího pravidla |


## <a name="limitations"></a>Omezení

- Maximální počet použitelných dočasných portů na IP adresu front-endu je 64 000.
- Rozsah konfigurovatelného odchozího časového limitu nečinnosti je 4 až 120 minut (240 až 7200 sekund).
- Load Balancer nepodporuje protokol ICMP pro odchozí překlad adres (NAT).
- Odchozí pravidla se dají použít jenom u primární konfigurace IP adresy síťového adaptéru.  Nemůžete vytvořit odchozí pravidlo pro sekundární IP adresu virtuálního počítače nebo síťové virtuální zařízení. Podporuje se víc síťových adaptérů.
- Role webového pracovního procesu bez virtuální sítě a dalších služeb platformy Microsoft můžou být dostupné, když se k vedlejšímu účinku používá jenom vnitřní Standard Load Balancer, která se dá použít jenom pro služby předplatného a jiné služby platformy. Nespoléhá se na tento vedlejší účinek jako na samotnou službu nebo na podkladovou platformu se může změnit bez předchozího upozornění. Vždy musíte předpokládat, že pokud budete chtít používat jenom interní Standard Load Balancer, musíte v případě potřeby explicitně vytvořit odchozí připojení. Scénář 3 popsaný v tomto článku není k dispozici.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Standard Load Balancer](load-balancer-standard-overview.md).
- Přečtěte si naše [Nejčastější dotazy týkající se Azure Load Balancer](load-balancer-faqs.md).
- Přečtěte si další informace o [odchozích pravidlech](load-balancer-outbound-rules-overview.md) pro standardní veřejné Load Balancer.
- Přečtěte si další informace o [Load Balancer](load-balancer-overview.md).
- Přečtěte si další informace o [skupinách zabezpečení sítě](../virtual-network/security-overview.md).
- Přečtěte si o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.
