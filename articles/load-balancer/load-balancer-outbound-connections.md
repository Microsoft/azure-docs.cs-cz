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
ms.openlocfilehash: 988434a72359ebe6ef84c4fd94041b2d4b90d5d9
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2020
ms.locfileid: "84221090"
---
# <a name="outbound-connections-in-azure"></a>Odchozí připojení v Azure

Azure Load Balancer poskytuje odchozí konektivitu pro zákaznická nasazení prostřednictvím několika různých mechanismů. Tento článek popisuje, co jsou scénáře, kdy se používají, jak fungují a jak je spravovat. Pokud dochází k potížím s odchozím připojením prostřednictvím Azure Load Balancer, přečtěte si [Průvodce odstraňováním potíží pro odchozí připojení] (.. /load-balancer/troubleshoot-outbound-connection.md).

>[!NOTE] 
>Tento článek se zabývá jenom nasazeními Správce prostředků. Zkontrolujte [odchozí připojení (Classic)](load-balancer-outbound-connections-classic.md) pro všechny scénáře nasazení Classic v Azure.

Nasazení v Azure může komunikovat s koncovými body mimo Azure ve veřejném adresním prostoru IP adres. Když instance inicializuje odchozí tok do cílového umístění ve veřejném adresním prostoru IP adres, Azure dynamicky mapuje privátní IP adresu na veřejnou IP adresu. Po vytvoření tohoto mapování se může vrátit přenos pro tento odchozí výstupní tok a získat tak privátní IP adresu, ve které tok vznikl.

Azure používá k provedení této funkce překlad zdrojového síťového adres (SNAT). Při maskování více privátních IP adres za jednou veřejnou IP adresou využívá Azure k maskování privátních IP adres [port (Pat) adresování](#pat) . Dočasné porty se používají pro PAT a jsou [vyčerpány](#preallocatedports) na základě velikosti fondu.

Existuje několik [odchozích scénářů](#scenarios). Tyto scénáře můžete v případě potřeby kombinovat. Pečlivě si přečtěte informace o možnostech, omezeních a vzorech, které se vztahují na váš model nasazení a scénář aplikace. Přečtěte si pokyny pro [správu těchto scénářů](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust).

>[!IMPORTANT] 
>Standard Load Balancer a standardní veřejná IP adresa přináší nové možnosti a různá chování pro odchozí připojení.  Nejsou stejné jako základní SKU.  Pokud chcete odchozí připojení při práci se standardními SKU, musíte ho explicitně definovat buď se standardními veřejnými IP adresami, nebo se standardními veřejnými Load Balancer.  To zahrnuje vytvoření odchozího připojení při použití interního Standard Load Balancer.  Doporučujeme vždy používat odchozí pravidla pro standardní veřejné Load Balancer.  [Scénář 3](#defaultsnat) není k dispozici u SKU Standard.  To znamená, že když se používá interní Standard Load Balancer, musíte provést kroky pro vytvoření odchozího připojení pro virtuální počítače ve fondu back-end, pokud je potřeba odchozí připojení.  V kontextu odchozího připojení, jednoho samostatného virtuálního počítače, který je ve skupině dostupnosti, se všechny instance v VMSS chovají jako skupina. To znamená, že pokud je jeden virtuální počítač ve skupině dostupnosti přidružený ke standardní SKU, všechny instance virtuálních počítačů v této skupině dostupnosti se teď budou chovat stejnými pravidly, jako kdyby byly přidružené ke standardní SKU, a to i v případě, že se k ní nepřímo přidružit samostatná instance. V případě samostatného virtuálního počítače s několika síťovými kartami připojenými k nástroji pro vyrovnávání zatížení je toto chování také pozorováno. Pokud se jedna síťová karta přidá jako samostatná, bude to mít stejné chování. Pečlivě si Projděte celý dokument, abyste porozuměli celkovým koncepcím, Projděte si [Standard Load Balancer](load-balancer-standard-overview.md) rozdíly mezi SKU a zkontrolujte [odchozí pravidla](load-balancer-outbound-rules-overview.md).  Použití odchozích pravidel umožňuje detailní kontrolu nad všemi aspekty odchozího připojení.

## <a name="scenario-overview"></a><a name="scenarios"></a>Přehled scénáře

Azure Load Balancer a související prostředky jsou explicitně definovány při použití [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Azure v současné době poskytuje tři různé metody pro zajištění odchozího připojení pro Azure Resource Manager prostředky. 

| Skladové položky | Scénář | Metoda | Protokoly IP | Popis |
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

## <a name="limitations"></a>Omezení
- Role webového pracovního procesu bez virtuální sítě a dalších služeb platformy Microsoft můžou být dostupné, když se k vedlejšímu účinku používá jenom vnitřní Standard Load Balancer, která se dá použít jenom pro služby předplatného a jiné služby platformy. Nespoléhá se na tento vedlejší účinek jako na samotnou službu nebo na podkladovou platformu se může změnit bez předchozího upozornění. Vždy musíte předpokládat, že pokud budete chtít používat jenom interní Standard Load Balancer, musíte v případě potřeby explicitně vytvořit odchozí připojení. [Výchozí scénář SNAT](#defaultsnat) 3, který je popsaný v tomto článku, není k dispozici.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Standard Load Balancer](load-balancer-standard-overview.md).
- Přečtěte si další informace o [odchozích pravidlech](load-balancer-outbound-rules-overview.md) pro standardní veřejné Load Balancer.
- Přečtěte si další informace o [Load Balancer](load-balancer-overview.md).
- Přečtěte si další informace o [skupinách zabezpečení sítě](../virtual-network/security-overview.md).
- Přečtěte si o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.
