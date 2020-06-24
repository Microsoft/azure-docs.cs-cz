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
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 0d16823f920695f84db74122c9a2ac07de0abdb2
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84907070"
---
# <a name="outbound-connections-in-azure"></a>Odchozí připojení v Azure

Azure Load Balancer poskytuje odchozí konektivitu pro zákaznická nasazení prostřednictvím několika různých mechanismů. Tento článek popisuje, co jsou scénáře, kdy se používají, jak fungují a jak je spravovat. Pokud dochází k potížím s odchozím připojením prostřednictvím Azure Load Balancer, přečtěte si článek [Průvodce odstraňováním potíží pro odchozí připojení](../load-balancer/troubleshoot-outbound-connection.md).

>[!NOTE] 
>Tento článek se zabývá jenom nasazeními Správce prostředků. Zkontrolujte [odchozí připojení (Classic)](load-balancer-outbound-connections-classic.md) pro všechny scénáře nasazení Classic v Azure.

Nasazení v Azure může komunikovat s koncovými body mimo Azure ve veřejném adresním prostoru IP adres. Když instance inicializuje odchozí tok do cílového umístění ve veřejném adresním prostoru IP adres, Azure dynamicky mapuje privátní IP adresu na veřejnou IP adresu. Po vytvoření tohoto mapování se může vrátit přenos pro tento odchozí výstupní tok a získat tak privátní IP adresu, ve které tok vznikl.

Azure používá k provedení této funkce překlad zdrojového síťového adres (SNAT). Při maskování více privátních IP adres za jednou veřejnou IP adresou využívá Azure k maskování privátních IP adres [port (Pat) adresování](#pat) . Dočasné porty se používají pro PAT a jsou [vyčerpány](#preallocatedports) na základě velikosti fondu.

Existuje několik [odchozích scénářů](#scenarios). Tyto scénáře můžete v případě potřeby kombinovat. Pečlivě si přečtěte informace o možnostech, omezeních a vzorech, které se vztahují na váš model nasazení a scénář aplikace. Přečtěte si pokyny pro [správu těchto scénářů](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust).

>[!IMPORTANT] 
>Standard Load Balancer a standardní veřejná IP adresa přináší nové možnosti a různá chování pro odchozí připojení.  Nejsou stejné jako základní SKU.  Pokud chcete odchozí připojení při práci se standardními SKU, musíte ho explicitně definovat buď se standardními veřejnými IP adresami, nebo se standardními veřejnými Load Balancer.  To zahrnuje vytvoření odchozího připojení při použití interního Standard Load Balancer.  Doporučujeme vždy používat odchozí pravidla pro standardní veřejné Load Balancer.  [Scénář 3](#defaultsnat) není k dispozici u SKU Standard.  To znamená, že když se používá interní Standard Load Balancer, musíte provést kroky pro vytvoření odchozího připojení pro virtuální počítače ve fondu back-end, pokud je potřeba odchozí připojení.  V kontextu odchozího připojení, jednoho samostatného virtuálního počítače, který je ve skupině dostupnosti, se všechny instance v VMSS chovají jako skupina. To znamená, že pokud je jeden virtuální počítač ve skupině dostupnosti přidružený ke standardní SKU, všechny instance virtuálních počítačů v této skupině dostupnosti se teď budou chovat stejnými pravidly, jako kdyby byly přidružené ke standardní SKU, a to i v případě, že se k ní nepřímo přidružit samostatná instance. V případě samostatného virtuálního počítače s několika síťovými kartami připojenými k nástroji pro vyrovnávání zatížení je toto chování také pozorováno. Pokud se jedna síťová karta přidá jako samostatná, bude to mít stejné chování. Pečlivě si Projděte celý dokument, abyste porozuměli celkovým koncepcím, Projděte si [Standard Load Balancer](load-balancer-standard-overview.md) rozdíly mezi SKU a zkontrolujte [odchozí pravidla](load-balancer-outbound-rules-overview.md).  Použití odchozích pravidel umožňuje detailní kontrolu nad všemi aspekty odchozího připojení.

## <a name="scenario-overview"></a><a name="scenarios"></a>Přehled scénáře

Azure Load Balancer a související prostředky jsou explicitně definovány při použití [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Azure v současné době poskytuje tři různé metody pro zajištění odchozího připojení pro Azure Resource Manager prostředky. 

| Skladové položky | Scénář | Metoda | Protokoly IP | Description |
| --- | --- | --- | --- | --- |
| Standardní, základní | [1. virtuální počítač s veřejnou IP adresou na úrovni instance (s Load Balancer nebo bez něj)](#ilpip) | SNAT, maskování portů se nepoužívá. | TCP, UDP, ICMP, ESP | Azure používá veřejnou IP adresu přiřazenou ke konfiguraci protokolu IP síťové karty instance. Instance má k dispozici všechny dočasné porty. Při použití Standard Load Balancer se [odchozí pravidla](load-balancer-outbound-rules-overview.md) nepodporují, pokud je k virtuálnímu počítači přiřazená veřejná IP adresa. |
| Standardní, základní | [2. veřejné Load Balancer přidružené k virtuálnímu počítači (bez veřejné IP adresy v instanci)](#lb) | SNAT s maskou portů (PAT) pomocí Load Balancer front-endu | TCP, UDP |Azure sdílí veřejnou IP adresu veřejných Load Balancer front-endu s více privátními IP adresami. Azure používá dočasné porty front-endu na PAT. Při použití Standard Load Balancer byste měli použít [odchozí pravidla](load-balancer-outbound-rules-overview.md) k explicitnímu definování odchozího připojení. |
| žádné nebo základní | [3. samostatný virtuální počítač (bez Load Balancer, žádná veřejná IP adresa)](#defaultsnat) | SNAT s maskou portů (PAT) | TCP, UDP | Azure automaticky určí veřejnou IP adresu pro SNAT, sdílí tuto veřejnou IP adresu s více privátními IP adresami skupiny dostupnosti a používá dočasné porty této veřejné IP adresy. Tento scénář je záložní pro předchozí scénáře. Nedoporučujeme ho, pokud potřebujete viditelnost a kontrolu. |

Pokud nechcete, aby virtuální počítač komunikoval s koncovými body mimo Azure ve veřejném adresním prostoru IP adres, můžete podle potřeby zablokovat přístup pomocí skupin zabezpečení sítě (skupin zabezpečení sítě). Oddíl [zabraňující odchozímu připojení](#preventoutbound) popisuje skupin zabezpečení sítě podrobněji. Pokyny k navrhování, implementaci a správě virtuální sítě bez jakéhokoli odchozího přístupu jsou mimo rámec tohoto článku.

### <a name="scenario-1-vm-with-public-ip-address"></a><a name="ilpip"></a>Scénář 1: virtuální počítač s veřejnou IP adresou

V tomto scénáři má virtuální počítač přiřazenou veřejnou IP adresu. Vzhledem k tomu, že se jedná o odchozí připojení, nezáleží na tom, jestli je virtuální počítač vyrovnaný k vyrovnávání zatížení. Tento scénář má přednost před ostatními. Když použijete veřejnou IP adresu, virtuální počítač použije veřejnou IP adresu pro všechny odchozí toky.  

Veřejná IP adresa přiřazená k virtuálnímu počítači je vztah 1:1 (nikoli 1: mnoho) a implementovaný jako bezstavové 1:1 NAT.  Maskování portů (PAT) se nepoužívá a virtuální počítač má k dispozici všechny dočasné porty, které je možné použít.

Pokud vaše aplikace zahájí mnoho odchozích toků a dojde k vyčerpání portů SNAT, zvažte přiřazení [veřejné IP adresy, která bude zmírnit omezení SNAT](../load-balancer/troubleshoot-outbound-connection.md#assignilpip). Přečtěte si celou [správu vyčerpání SNAT](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust) .

### <a name="scenario-2-load-balanced-vm-without-a-public-ip-address"></a><a name="lb"></a>Scénář 2: virtuální počítač s vyrovnáváním zatížení bez veřejné IP adresy

V tomto scénáři je virtuální počítač součástí veřejného Load Balancer fondu back-endu. K virtuálnímu počítači není přiřazená žádná veřejná IP adresa. Aby bylo možné vytvořit propojení mezi veřejnou IP frontou a fondem back-end, musí být prostředek Load Balancer nakonfigurovaný s pravidlem nástroje pro vyrovnávání zatížení.

Pokud konfiguraci pravidla nedokončíte, bude chování popsané ve scénáři [samostatného virtuálního počítače bez veřejné IP adresy](#defaultsnat). Pro úspěšné provedení testu stavu není nutné, aby pravidlo mělo funkční naslouchací proces ve fondu back-endu.

Když virtuální počítač s vyrovnáváním zatížení vytvoří odchozí tok, Azure převede privátní zdrojovou IP adresu odchozího toku na veřejnou IP adresu veřejné Load Balancer front-endu. Azure používá k provedení této funkce SNAT. Azure také používá [Pat](#pat) k maskování více privátních IP adres za veřejnou IP adresou. 

Dočasné porty front-endu veřejné IP adresy nástroje pro vyrovnávání zatížení se používají k odlišení jednotlivých toků, které pocházejí z virtuálního počítače. SNAT dynamicky používá [předpřidělené dočasné porty](#preallocatedports) při vytváření odchozích toků. V tomto kontextu se dočasné porty používané pro SNAT nazývají porty SNAT.

Porty SNAT jsou předem přiděleny, jak je popsáno v části [Princip SNAT a Pat](#snat) . Jsou to konečný prostředek, který je možné vyčerpat. Je důležité pochopit, jak se [spotřebovávají](#pat). Informace o tom, jak navrhnout tuto spotřebu a zmírnit podle potřeby, najdete v tématu [Správa vyčerpání SNAT](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust).

Když je [k Load Balancer Basic přidružená víc veřejných IP adres](load-balancer-multivip-overview.md), každá z těchto veřejných IP adres je kandidátem na odchozí toky a jedna z nich se vybere náhodně.  

Pokud chcete monitorovat stav odchozích připojení pomocí Load Balancer Basic, můžete k monitorování zpráv pro vyčerpání portů SNAT použít [protokoly Azure monitor pro Load Balancer](load-balancer-monitor-log.md) a [protokoly událostí výstrah](load-balancer-monitor-log.md#alert-event-log) .

### <a name="scenario-3-standalone-vm-without-a-public-ip-address"></a><a name="defaultsnat"></a>Scénář 3: samostatný virtuální počítač bez veřejné IP adresy

V tomto scénáři není virtuální počítač součástí fondu veřejných Load Balancer (a není součástí interního fondu Standard Load Balancer) a nemá přiřazenou veřejnou IP adresu. Když virtuální počítač vytvoří odchozí tok, Azure převede IP adresu privátního zdroje odchozího toku do veřejné zdrojové IP adresy. Veřejnou IP adresu, která se používá pro tento výstupní tok, nejde konfigurovat a nepočítá se s limitem veřejných IP adres předplatného. Tato veřejná IP adresa nepatří vám a nedá se rezervovat. Pokud znovu nasadíte virtuální počítač nebo skupinu dostupnosti nebo sadu škálování virtuálního počítače, tato veřejná IP adresa se uvolní a vyžádá se nová veřejná IP adresa. Nepoužívejte tento scénář pro seznam povolených IP adres. Místo toho použijte jeden z dalších dvou scénářů, kde explicitně deklarujete odchozí scénář a veřejnou IP adresu, která se má použít pro odchozí připojení.

>[!IMPORTANT] 
>Tento scénář platí také v případě, že je připojena __pouze__ interní základní Load Balancer. Pokud je k virtuálnímu počítači připojený interní Standard Load Balancer, není scénář 3 __dostupný__ .  Kromě používání interního Standard Load Balancer musíte explicitně vytvořit [scénář 1](#ilpip) nebo [scénář 2](#lb) .

Azure používá k provedení této funkce SNAT s maskou portů ([Pat](#pat)). Tento scénář je podobný [scénáři 2](#lb), s výjimkou toho, že se používá žádná kontrola nad použitou IP adresou. Toto je nouzový scénář pro případy, kdy scénáře 1 a 2 neexistují. Tento scénář nedoporučujeme, pokud chcete mít kontrolu nad odchozí adresou. Pokud jsou odchozí připojení důležitou součástí vaší aplikace, měli byste zvolit jiný scénář.

Porty SNAT jsou předpřidělené, jak je popsáno v části [Princip SNAT a Pat](#snat) .  Počet virtuálních počítačů, které sdílejí skupinu dostupnosti, zjistí, která úroveň předplatného se vztahuje.  Samostatný virtuální počítač bez skupiny dostupnosti je pro účely určení předřazení (1024 SNAT) efektivně fondem 1. Porty SNAT jsou konečným prostředkem, který je možné vyčerpat. Je důležité pochopit, jak se [spotřebovávají](#pat). Informace o tom, jak navrhnout tuto spotřebu a zmírnit podle potřeby, najdete v tématu [Správa vyčerpání SNAT](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust).

### <a name="multiple-combined-scenarios"></a><a name="combinations"></a>Více kombinovaných scénářů

Scénáře popsané v předchozích částech můžete kombinovat k dosažení konkrétního výsledku. Pokud je přítomno více scénářů, použije se pořadí priorit: [scénář 1](#ilpip) má přednost před [scénářem 2](#lb) a [3](#defaultsnat). [Scénář 2](#lb) Přepisuje [scénář 3](#defaultsnat).

Příkladem Azure Resource Manager nasazení, kde aplikace spoléhá na odchozí připojení k omezenému počtu míst, ale také přijímá příchozí toky přes Load Balancer front-endu. V takovém případě můžete kombinovat scénáře 1 a 2 pro úlevu. Další vzory najdete v přehledu [správy vyčerpání SNAT](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust).

### <a name="multiple-frontends-for-outbound-flows"></a><a name="multife"></a>Několik front-endové pro odchozí toky

#### <a name="standard-load-balancer"></a>Standard Load Balancer

Standard Load Balancer používá všechny kandidáty u odchozích toků současně v případě, že je k dispozici [více (veřejných) front-endové IP adresy](load-balancer-multivip-overview.md) . Každý front-end vynásobí počet dostupných předem přidělených portů SNAT, pokud je pro odchozí připojení povolené pravidlo vyrovnávání zatížení.

Pro odchozí připojení s novou možností pravidla vyrovnávání zatížení se můžete rozhodnout potlačit IP adresu front-endu, která se používá.

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Standardně je `disableOutboundSnat` Tato možnost standardně _nepravdivá_ a oznamuje, že toto pravidlo pro přidružené virtuální počítače ve fondu back-endu pravidla vyrovnávání zatížení odesílá odchozí SNAT. `disableOutboundSnat`Vlastnost lze změnit na _hodnotu true_ , pokud chcete, aby Load Balancer nepoužívala přidruženou IP adresu front-endu pro odchozí připojení virtuálních počítačů ve fondu back-end tohoto pravidla vyrovnávání zatížení.  A také můžete určit konkrétní IP adresu pro odchozí toky, jak je popsáno v [několika kombinovaných scénářích](#combinations) .

#### <a name="load-balancer-basic"></a>Load Balancer Basic

Load Balancer Basic zvolí jeden front-end, který se má použít pro odchozí toky, pokud jsou kandidáti na odchozí toky [více (veřejných) front-endu IP](load-balancer-multivip-overview.md) . Tento výběr není možné konfigurovat a měli byste zvážit, že je algoritmus výběru náhodný. Můžete určit konkrétní IP adresu pro odchozí toky, jak je popsáno v [několika kombinovaných scénářích](#combinations).

### <a name="availability-zones"></a><a name="az"></a>Zóny dostupnosti

Při použití [Standard Load Balancer s zóny dostupnosti](load-balancer-standard-availability-zones.md)může koncová zóna redundantní v zóně poskytovat odchozí připojení SNAT v zóně redundantní a programování SNAT zachová selhání zóny.  Když se používají front-endy, odchozí připojení SNAT sdílí rozpad s zónou, ke které patří.

## <a name="understanding-snat-and-pat"></a><a name="snat"></a>Porozumění SNAT a PAT

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>Maskování portů SNAT (PAT)

Pokud je k instancím virtuálních počítačů, které nemají vyhrazené veřejné IP adresy, přidružen prostředek veřejné Load Balancer, je nutné přepsán každý zdroj odchozího připojení. Zdroj se přepíše z privátního adresního prostoru privátních IP adres virtuální sítě na front-endové veřejné IP adresy nástroje pro vyrovnávání zatížení. Ve veřejném adresním prostoru IP adres musí být pět záznamů toku (zdrojová IP adresa, zdrojový port, transportní protokol IP, cílová IP adresa, cílový port) jedinečný. Maskování portů SNAT se dá použít buď s protokolem IP TCP nebo UDP.

Dočasné porty (porty SNAT) se používají k dosažení tohoto toho po přepsání privátní zdrojové IP adresy, protože více toků pochází z jedné veřejné IP adresy. Algoritmus SNAT maskování portů přiděluje porty SNAT odlišně pro UDP a TCP.

#### <a name="tcp-snat-ports"></a><a name="tcp"></a>Porty TCP SNAT

Jeden port SNAT se spotřebovává na jeden tok na jednu cílovou IP adresu, port. U více toků TCP se stejnou cílovou IP adresou, portem a protokolem každý tok TCP spotřebovává jeden port SNAT. Tím je zajištěno, že toky budou jedinečné, když pocházejí ze stejné veřejné IP adresy a jdou na stejnou cílovou IP adresu, port a protokol. 

Několik toků, z nichž každý má jinou cílovou IP adresu, port a protokol, sdílí jeden port SNAT. Cílová IP adresa, port a protokol vytvářejí toky jedinečné bez nutnosti dalších zdrojových portů, které by rozlišily toky ve veřejném adresním prostoru IP adres.

#### <a name="udp-snat-ports"></a><a name="udp"></a>Porty UDP SNAT

Porty UDP SNAT jsou spravovány jiným algoritmem než porty TCP SNAT.  Load Balancer používá algoritmus, který se označuje jako "kuželový překlad adres (NAT)" na portu UDP.  Pro každý tok se spotřebuje jeden port SNAT bez ohledu na cílovou IP adresu a port.

#### <a name="snat-port-reuse"></a>Opakované použití portu SNAT

Po uvolnění portu je možné port v případě potřeby znovu použít.  Porty SNAT si můžete představit jako sekvence od nejnižší po nejvyšší dostupnou pro daný scénář a jako první dostupný port SNAT se použije pro nová připojení. 
 
#### <a name="exhaustion"></a>Vyčerpání

Po vyčerpání prostředků portů SNAT dojde k selhání odchozích toků, dokud nebudou stávající toky vydány porty SNAT. Load Balancer redeklaracích portů SNAT po ukončení toku a používá [prodlevu nečinnosti ve 4 minutách](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) pro uvolnění portů SNAT z nečinných toků.

Porty UDP SNAT jsou obvykle vyčerpány mnohem rychleji než porty TCP SNAT z důvodu rozdílu používaného algoritmu. Tento rozdíl je nutné navrhovat a škálovat.

V případě vzorů pro zmírnění podmínek, které běžně vedou k vyčerpání portů SNAT, si přečtěte část [Správa SNAT](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust) .

### <a name="ephemeral-port-preallocation-for-port-masquerading-snat-pat"></a><a name="preallocatedports"></a>Předběžné přidělení portu SNAT pro maskování portů SNAT (PAT)

Azure používá algoritmus k určení počtu předem přidělených portů SNAT na základě velikosti back-endu při použití maskování portů SNAT ([Pat](#pat)). Porty SNAT jsou dočasné porty, které jsou k dispozici pro konkrétní zdrojovou adresu veřejné IP adresy. Pro každou veřejnou IP adresu přidruženou k nástroji pro vyrovnávání zatížení jsou porty 64 000 dostupné jako porty SNAT pro každý transportní protokol IP.

Stejný počet portů SNAT se má předběžně přidělit pro UDP a TCP a spotřebovat nezávisle na IP přenosový protokol.  Použití portu SNAT se ale liší v závislosti na tom, jestli je tok UDP nebo TCP.

>[!IMPORTANT]
>Pořadová jednotka Standard SKU SNAT je podle přenosového protokolu IP a je odvozená od pravidla vyrovnávání zatížení.  Pokud existuje pouze pravidlo vyrovnávání zatížení TCP, je SNAT dostupné pouze pro protokol TCP. Pokud máte pouze pravidlo vyrovnávání zatížení TCP a potřebujete odchozí SNAT pro protokol UDP, vytvořte pravidlo vyrovnávání zatížení UDP ze stejného front-endu do stejného back-end fondu.  Tím se aktivuje programování SNAT pro UDP.  Pracovní pravidlo nebo sondu stavu se nevyžaduje.  Základní SKU SNAT SNAT vždycky pro protokol IP transportu (bez ohledu na transportní protokol zadaný v pravidle vyrovnávání zatížení).

Azure předřadí porty SNAT do konfigurace protokolu IP síťové karty každého virtuálního počítače. Když se do fondu přidá konfigurace IP adresy, porty SNAT se pro tuto konfiguraci IP adres přidělí na základě velikosti fondu back-endu. Při vytváření odchozích toků se [Jana dynamicky spotřebovává](#pat) (až do limitu před přidělením) a uvolní tyto porty, když dojde k ukončení toku nebo [vypršení nečinnosti](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) .

V následující tabulce jsou uvedena předalokace portů SNAT pro vrstvy velikostí back-endu:

| Velikost fondu (instance virtuálních počítačů) | Předběžně přidělené porty SNAT na konfiguraci IP adres|
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801 – 1000 | 32 |

>[!NOTE]
> Když použijete Standard Load Balancer s [více front-endu](load-balancer-multivip-overview.md), každá IP adresa front-endu vynásobí počet dostupných portů SNAT v předchozí tabulce. Například fond back-endu 50 virtuálního počítače se dvěma pravidly vyrovnávání zatížení, z nichž každá má samostatnou IP adresu front-endu, použije 2048 (2x 1024) portů SNAT na pravidlo. Podívejte se na podrobnosti pro [více front-endu](#multife).

Pamatujte, že počet dostupných portů SNAT se nepřevádí přímo na počet toků. Jeden port SNAT lze znovu použít pro více jedinečných umístění. Porty se spotřebují jenom v případě, že je potřeba, aby toky byly jedinečné. Pokyny pro návrh a zmírnění najdete v části o [tom, jak spravovat tento prostředek exhaustible](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust) a část popisující [Pat](#pat).

Změna velikosti back-end fondu může ovlivnit některé z vašich navázaných toků. Pokud se velikost fondu back-endu zvyšuje a přejde do další úrovně, během přechodu na další větší úroveň fondu back-end se uvolní polovina předpřidělených portů SNAT. Toky, které jsou přidružené k uvolněnému portu SNAT, vyprší časový limit a je nutné je znovu vytvořit. Pokud se pokusíte o nový tok, tok se okamžitě dokončí, dokud nebudou dostupné porty k dispozici.

Pokud se velikost fondu back-endu zmenší a přejde do nižší úrovně, zvýší se počet dostupných portů SNAT. V takovém případě nebudou ovlivněny existující porty SNAT a jejich příslušné toky.

Přidělení portů SNAT jsou specifická pro protokol IP (TCP a UDP se uchovávají samostatně) a vydávají se za následujících podmínek:

### <a name="tcp-snat-port-release"></a>Verze portu TCP SNAT

- Pokud některý ze serverů/klientů odesílá FINACK, port SNAT se uvolní po 240 sekundách.
- Pokud je zjištěna RST, port SNAT se uvolní po 15 sekundách.
- Pokud bylo dosaženo časového limitu nečinnosti, je port uvolněn.

### <a name="udp-snat-port-release"></a>Vydání portu UDP SNAT

- Pokud bylo dosaženo časového limitu nečinnosti, je port uvolněn.

## <a name="discovering-the-public-ip-that-a-vm-uses"></a><a name="discoveroutbound"></a>Zjištění veřejné IP adresy, kterou virtuální počítač používá
Existuje mnoho způsobů, jak určit veřejnou zdrojovou IP adresu odchozího připojení. OpenDNS poskytuje službu, která vám umožní zobrazit veřejnou IP adresu vašeho virtuálního počítače. 

Pomocí příkazu nslookup můžete odeslat dotaz DNS pro název myip.opendns.com do překladače OpenDNS. Služba vrátí zdrojovou IP adresu, která se použila k odeslání dotazu. Když z virtuálního počítače spustíte následující dotaz, odpověď je veřejná IP adresa používaná pro tento virtuální počítač:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Prevence odchozího připojení
V některých případech je možné, že virtuální počítač nebude moci vytvořit odchozí tok. Nebo může být potřeba, abyste mohli spravovat, které cíle je možné dosáhnout u odchozích toků, nebo které cíle můžou začít s příchozími toky. V takovém případě můžete pomocí [skupin zabezpečení sítě](../virtual-network/security-overview.md) spravovat cíle, ke kterým může virtuální počítač získat přístup. Pomocí skupin zabezpečení sítě můžete také spravovat, které veřejné cíle mohou iniciovat příchozí toky.

Když použijete NSG k virtuálnímu počítači s vyrovnáváním zatížení, věnujte pozornost [značkám služby](../virtual-network/security-overview.md#service-tags) a [výchozím pravidlům zabezpečení](../virtual-network/security-overview.md#default-security-rules). Musíte zajistit, aby virtuální počítač mohl přijímat požadavky na sondu stavu z Azure Load Balancer. 

Pokud NSG blokuje požadavky na test stavu z AZURE_LOADBALANCER výchozí značky, test stavu virtuálního počítače se nepovede a virtuální počítač se označí jako neplatný. Load Balancer zastaví odesílání nových toků do tohoto virtuálního počítače.

## <a name="connections-to-azure-storage-in-the-same-region"></a>Připojení k Azure Storage ve stejné oblasti

Pro připojení k úložišti ve stejné oblasti jako virtuální počítač není nutné mít odchozí připojení prostřednictvím výše uvedených scénářů. Pokud to nechcete, použijte skupiny zabezpečení sítě (skupin zabezpečení sítě), jak je vysvětleno výše. Připojení k úložišti v jiných oblastech vyžaduje odchozí připojení. Pamatujte na to, že při připojování k úložišti z virtuálního počítače ve stejné oblasti bude zdrojová IP adresa v diagnostických protokolech úložiště interní adresa poskytovatele, nikoli veřejná IP adresa vašeho virtuálního počítače. Pokud chcete omezit přístup k účtu úložiště na virtuální počítače v jedné nebo více Virtual Networkch podsítích ve stejné oblasti, při konfiguraci brány firewall účtu úložiště použijte [koncové body služby Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md) a nemusíte mít veřejnou IP adresu. Po nakonfigurování koncových bodů služby se v diagnostických protokolech úložiště zobrazí vaše Virtual Network privátní IP adresa, nikoli adresa interního poskytovatele.

## <a name="azure-load-balancer-outbound-rules"></a><a name="outboundrules"></a>Azure Load Balancer odchozí pravidla

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
- Určuje, zda má být odesláno resetování protokolu TCP při nečinnosti.

Odchozí pravidla rozbalí [scénář 2](load-balancer-outbound-connections.md#lb) , který je popsaný v článku o [odchozích připojeních](load-balancer-outbound-connections.md) , a Priorita scénáře zůstane tak, jak je.

### <a name="outbound-rule"></a>Odchozí pravidlo

Stejně jako všechna pravidla Load Balancer se odchozí pravidla řídí stejnou známou syntaxí jako vyrovnávání zatížení a příchozí pravidla překladu adres (NAT):

**front-end**  +  **parametry**  +  **back-end fond**

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

#### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a>Škálování odchozího překladu adres (NAT) s několika IP adresami

I když se odchozí pravidlo dá použít jenom s jednou veřejnou IP adresou, odchozí pravidla zjednodušují zátěž v konfiguraci pro škálování odchozího překladu adres (NAT). Pro plánování rozsáhlých scénářů můžete použít více IP adres a pomocí odchozích pravidel můžete zmírnit vzory náchylné k [vyčerpání SNAT](troubleshoot-outbound-connection.md#snatexhaust) .  

Každá další IP adresa poskytovaná front-endu poskytuje 64 000 dočasných portů, které Load Balancer použít jako porty SNAT. I když mají pravidla vyrovnávání zatížení nebo příchozího překladu adres (NAT) jeden front-end, toto pravidlo rozšíří rozhraní front-end a umožní více front-endu na jedno pravidlo.  U několika front-endu na pravidlo je množství dostupných portů SNAT vynásobeno každou veřejnou IP adresou a může být podporováno velké scénáře.

Kromě toho můžete použít [předponu veřejné IP adresy](https://aka.ms/lbpublicipprefix) přímo s odchozím pravidlem.  Použití předpony veřejných IP adres poskytuje snazší škálování a zjednodušený seznam toků pocházejících z nasazení Azure. Konfiguraci IP adresy front-endu můžete v rámci prostředku Load Balancer nakonfigurovat tak, aby odkazovala přímo na předponu veřejné IP adresy.  To umožňuje Load Balancer exkluzivní kontrolu nad předponou veřejné IP adresy a odchozí pravidlo automaticky použije všechny veřejné IP adresy obsažené v předponě veřejných IP adres pro odchozí připojení.  Jednotlivé IP adresy v rozsahu předpony veřejných IP adres poskytují 64 000 dočasných portů na IP adresu Load Balancer použít jako porty SNAT.   

Při použití této možnosti nemůžete mít jednotlivé prostředky veřejné IP adresy vytvořené z předpony veřejných IP adres, protože odchozí pravidlo musí mít úplnou kontrolu nad předponou veřejné IP adresy.  Pokud potřebujete přesnější kontrolu, můžete vytvořit samostatný prostředek veřejné IP adresy z předpony veřejné IP adresy a jednotlivě přiřadit více veřejných IP adres do front-endu odchozího pravidla.

#### <a name="tune-snat-port-allocation"></a><a name="snatports"></a>Vyladit alokaci portů SNAT

Pomocí odchozích pravidel můžete vyladit [automatické přidělování portů SNAT na základě velikosti fondu back-endu](load-balancer-outbound-connections.md#preallocatedports) a přidělit více nebo méně, než jaké automatické přidělování portů SNAT nabízí.

K přidělení portů 10 000 SNAT na virtuální počítač (konfigurace IP adresy NIC) použijte následující parametr.
 

          "allocatedOutboundPorts": 10000

Každá veřejná IP adresa ze všech front-endu odchozího pravidla přispívá až 64 000 dočasných portů, které se použijí jako porty SNAT.  Load Balancer přiděluje porty SNAT v násobcích 8. Pokud zadáte hodnotu, která není dělitelná 8, operace konfigurace se odmítne.  Pokud se pokusíte přidělit více portů SNAT, než je k dispozici na základě počtu veřejných IP adres, je operace konfigurace odmítnuta.  Pokud například přidělíte porty 10 000 na virtuální počítač a 7 virtuálních počítačů ve fondu back-end, bude se jednat o jednu veřejnou IP adresu, konfigurace se odmítne (7 x 10 000 SNAT portů > 64 000 portů SNAT).  Můžete přidat další veřejné IP adresy do front-endu odchozího pravidla a povolit tak scénář.

Můžete vrátit zpět na [automatické přidělování portů SNAT na základě velikosti fondu back-endu](load-balancer-outbound-connections.md#preallocatedports) zadáním 0 pro počet portů. V takovém případě získají první 50 instancí virtuálních počítačů 1024 portů, 51-100 instancí virtuálních počítačů bude 512 a tak podle tabulky.

#### <a name="control-outbound-flow-idle-timeout"></a><a name="idletimeout"></a>Řízení časového limitu nečinnosti odchozího toku

Odchozí pravidla poskytují konfigurační parametr pro řízení časového limitu nečinnosti odchozího toku a jejich párování s požadavky vaší aplikace.  Výchozí časový limit nečinnosti ve výchozím nastavení na 4 minuty.  Parametr přijímá hodnotu od 4 do 120 pro určitý počet minut pro časový limit nečinnosti pro toky, které odpovídají tomuto konkrétnímu pravidlu.

Pro nastavení odchozího časového limitu nečinnosti na 1 hodinu použijte následující parametr:

          "idleTimeoutInMinutes": 60

#### <a name="enable-tcp-reset-on-idle-timeout"></a><a name="tcprst"></a><a name="tcpreset"></a>Povolit resetování protokolu TCP pro časový limit nečinnosti

Výchozím chováním Load Balancer je vyřazení toku v tichém režimu v případě dosažení odchozího časového limitu nečinnosti.  Pomocí parametru enableTCPReset můžete povolit předvídatelné chování aplikace a určit, jestli se má v době nečinnosti odchozího nečinnosti odesílat obousměrné resetování protokolu TCP (TCP RST). 

Pro povolení resetování protokolu TCP u odchozího pravidla použijte následující parametr:

           "enableTcpReset": true

Zkontrolujte [časový limit nečinnosti protokolu TCP](https://aka.ms/lbtcpreset) pro podrobnosti, včetně dostupnosti oblasti.

#### <a name="support-both-tcp-and-udp-transport-protocols-with-a-single-rule"></a><a name="proto"></a>Podpora přenosových protokolů TCP i UDP s jedním pravidlem

Pro transportní protokol odchozího pravidla pravděpodobně budete chtít použít "vše", ale můžete také použít odchozí pravidlo na konkrétní transportní protokol a také v případě, že to je potřeba udělat.

Pomocí následujícího parametru nastavte protokol na TCP a UDP:

          "protocol": "All"

#### <a name="disable-outbound-nat-for-a-load-balancing-rule"></a><a name="disablesnat"></a>Zakázat odchozí překlad adres (NAT) pro pravidlo vyrovnávání zatížení

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

#### <a name="reuse-existing-or-define-new-backend-pools"></a>Znovu použít existující nebo definovat nové back-endové fondy

Odchozí pravidla nezavádí nový koncept pro definování skupiny virtuálních počítačů, na které by se mělo pravidlo vztahovat.  Místo toho znovu použijí koncept fondu back-end, který se používá také pro pravidla vyrovnávání zatížení. Tuto možnost můžete použít ke zjednodušení konfigurace buď opětovným použitím existující definice back-end fondu, nebo vytvořením speciálně pro odchozí pravidlo.

### <a name="scenarios"></a>Scénáře

#### <a name="groom-outbound-connections-to-a-specific-set-of-public-ip-addresses"></a><a name="groom"></a>Odchozí připojení výmazu dat ke konkrétní sadě veřejných IP adres

Můžete použít odchozí pravidlo pro vymazání odchozích připojení, která se mají vyskytnout z konkrétní sady veřejných IP adres, aby se usnadnily scénáře přidávání na seznam povolených.  Tato veřejná IP adresa se může shodovat s použitím pravidla vyrovnávání zatížení nebo jiné sady veřejných IP adres, než je používá pravidlo vyrovnávání zatížení.  

1. Vytvořit [předponu veřejné IP](https://aka.ms/lbpublicipprefix) adresy (nebo veřejné IP adresy z předpony veřejných IP adres)
2. Vytvoření veřejného Load Balanceru úrovně Standard
3. Vytvoření front-endu odkazujících na předponu veřejné IP adresy (nebo veřejné IP adresy), které chcete použít
4. Znovu použijte back-end fond nebo vytvořte fond back-end a umístěte virtuální počítače do back-endového fondu veřejné Load Balancer
5. Konfigurace odchozího pravidla na veřejném Load Balancer pro programování odchozího překladu adres (NAT) pro tyto virtuální počítače pomocí front-endu
   
Pokud nechcete, aby se pravidlo vyrovnávání zatížení používalo pro odchozí připojení, musíte [Zakázat odchozí SNAT](#disablesnat) v pravidle vyrovnávání zatížení.

#### <a name="modify-snat-port-allocation"></a><a name="modifysnat"></a>Úprava přidělení portu SNAT

Pomocí odchozích pravidel můžete vyladit [automatické přidělování portů SNAT na základě velikosti fondu back-endu](load-balancer-outbound-connections.md#preallocatedports).

Pokud například máte dva virtuální počítače sdílející jednu veřejnou IP adresu pro odchozí NAT, možná budete chtít zvýšit počet portů SNAT přidělených z výchozích portů 1024, pokud máte vyčerpání SNAT. Každá veřejná IP adresa může přispívat až 64 000 dočasných portů.  Pokud nakonfigurujete odchozí pravidlo s jednou front-endu veřejné IP adresy, můžete do virtuálních počítačů ve fondu back-endu distribuovat celkem 64 000 portů SNAT.  Pro dva virtuální počítače se dá přidělit maximálně 32 000 portů SNAT pomocí odchozího pravidla (2x 32 000 = 64 000).

Zkontrolujte [odchozí připojení](load-balancer-outbound-connections.md) a podrobnosti o přidělování a používání portů [SNAT](load-balancer-outbound-connections.md#snat) .

#### <a name="enable-outbound-only"></a><a name="outboundonly"></a>Povolit pouze odchozí

K poskytování odchozího překladu adres (NAT) pro skupinu virtuálních počítačů můžete použít veřejné Standard Load Balancer. V tomto scénáři můžete použít odchozí pravidlo samostatně, aniž byste museli mít žádná další pravidla.

##### <a name="outbound-nat-for-vms-only-no-inbound"></a>Odchozí překlad adres (NAT) pouze pro virtuální počítače (žádné příchozí)

Definujte veřejné Standard Load Balancer, umístěte virtuální počítače do back-endu fondu a nakonfigurujte odchozí pravidlo pro programování odchozího překladu adres (NAT) a výmaz dat z konkrétní veřejné IP adresy. Můžete také použít předponu veřejné IP adresy zjednodušit výpis zdroje odchozích připojení.

1. Vytvořte veřejnou Standard Load Balancer.
2. Vytvořte back-end fond a umístěte virtuální počítače do back-endového fondu veřejné Load Balancer.
3. Nakonfigurujte odchozí pravidlo na veřejném Load Balancer pro programování odchozího překladu adres (NAT) pro tyto virtuální počítače.

##### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>Odchozí překlad adres (NAT) pro interní Standard Load Balancer scénáře

Při použití interního Standard Load Balancer není odchozí překlad adres (NAT) dostupný, dokud není explicitně deklarované odchozí připojení. Odchozí připojení můžete definovat pomocí odchozího pravidla pro vytvoření odchozího připojení pro virtuální počítače za interním Standard Load Balancer s těmito kroky:

1. Vytvořte veřejnou Standard Load Balancer.
2. Vytvořte fond back-end a umístěte virtuální počítače do back-endového fondu veřejných Load Balancer kromě interní Load Balancer.
3. Nakonfigurujte odchozí pravidlo na veřejném Load Balancer pro programování odchozího překladu adres (NAT) pro tyto virtuální počítače.

##### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Povolit protokoly TCP & UDP pro odchozí překlad adres (NAT) s veřejným Standard Load Balancer

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
- Odchozí pravidla se dají použít jenom u primární konfigurace IP adresy síťového adaptéru.  Podporuje se víc síťových adaptérů.
- Role webového pracovního procesu bez virtuální sítě a dalších služeb platformy Microsoft můžou být dostupné, když se k vedlejšímu účinku používá jenom vnitřní Standard Load Balancer, která se dá použít jenom pro služby předplatného a jiné služby platformy. Nespoléhá se na tento vedlejší účinek jako na samotnou službu nebo na podkladovou platformu se může změnit bez předchozího upozornění. Vždy musíte předpokládat, že pokud budete chtít používat jenom interní Standard Load Balancer, musíte v případě potřeby explicitně vytvořit odchozí připojení. [Výchozí scénář SNAT](#defaultsnat) 3, který je popsaný v tomto článku, není k dispozici.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Standard Load Balancer](load-balancer-standard-overview.md).
- Přečtěte si další informace o [odchozích pravidlech](load-balancer-outbound-rules-overview.md) pro standardní veřejné Load Balancer.
- Přečtěte si další informace o [Load Balancer](load-balancer-overview.md).
- Přečtěte si další informace o [skupinách zabezpečení sítě](../virtual-network/security-overview.md).
- Přečtěte si o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.
