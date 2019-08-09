---
title: Odchozí připojení v Azure
titlesuffix: Azure Load Balancer
description: Tento článek vysvětluje, jak Azure umožňuje virtuálním počítačům komunikovat s veřejnými službami sítě Internet.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 9dcc5fa201c08ca4b1e65b8aae88118731eba427
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881069"
---
# <a name="outbound-connections-in-azure"></a>Odchozí připojení v Azure

Azure poskytuje odchozí konektivitu pro zákaznická nasazení prostřednictvím několika různých mechanismů. Tento článek popisuje, co jsou scénáře, kdy se používají, jak fungují a jak je spravovat.

>[!NOTE] 
>Tento článek se zabývá jenom nasazeními Správce prostředků. Zkontrolujte [odchozí připojení (Classic)](load-balancer-outbound-connections-classic.md) pro všechny scénáře nasazení Classic v Azure.

Nasazení v Azure může komunikovat s koncovými body mimo Azure ve veřejném adresním prostoru IP adres. Když instance inicializuje odchozí tok do cílového umístění ve veřejném adresním prostoru IP adres, Azure dynamicky mapuje privátní IP adresu na veřejnou IP adresu. Po vytvoření tohoto mapování se může vrátit přenos pro tento odchozí výstupní tok a získat tak privátní IP adresu, ve které tok vznikl.

Azure používá k provedení této funkce překlad zdrojového síťového adres (SNAT). Při maskování více privátních IP adres za jednou veřejnou IP adresou využívá Azure k maskování privátních IP adres [port (Pat) adresování](#pat) . Dočasné porty se používají pro PAT a jsou vyčerpány na základě velikosti fondu. [](#preallocatedports)

Existuje několik [odchozích scénářů](#scenarios). Tyto scénáře můžete v případě potřeby kombinovat. Pečlivě si přečtěte informace o možnostech, omezeních a vzorech, které se vztahují na váš model nasazení a scénář aplikace. Přečtěte si pokyny pro [správu těchto scénářů](#snatexhaust).

>[!IMPORTANT] 
>Standard Load Balancer a standardní veřejná IP adresa přináší nové možnosti a různá chování pro odchozí připojení.  Nejsou stejné jako základní SKU.  Pokud chcete odchozí připojení při práci se standardními SKU, musíte ho explicitně definovat buď se standardními veřejnými IP adresami, nebo se standardními veřejnými Load Balancer.  To zahrnuje vytvoření odchozího připojení při použití interního Standard Load Balancer.  Doporučujeme vždy používat odchozí pravidla pro standardní veřejné Load Balancer.  [Scénář 3](#defaultsnat) není k dispozici u SKU Standard.  To znamená, že když se používá interní Standard Load Balancer, musíte provést kroky pro vytvoření odchozího připojení pro virtuální počítače ve fondu back-end, pokud je potřeba odchozí připojení.  V kontextu odchozího připojení, jednoho samostatného virtuálního počítače, který je ve skupině dostupnosti, se všechny instance v VMSS chovají jako skupina. To znamená, že pokud je jeden virtuální počítač ve skupině dostupnosti přidružený ke standardní SKU, všechny instance virtuálních počítačů v této skupině dostupnosti se teď budou chovat stejnými pravidly, jako kdyby byly přidružené ke standardní SKU, a to i v případě, že se k ní nepřímo přidružit samostatná instance.  Pečlivě si Projděte celý dokument, abyste porozuměli celkovým koncepcím, Projděte si [Standard Load Balancer](load-balancer-standard-overview.md) rozdíly mezi SKU a zkontrolujte [odchozí pravidla](load-balancer-outbound-rules-overview.md).  Použití odchozích pravidel umožňuje detailní kontrolu nad všemi aspekty odchozího připojení.

## <a name="scenarios"></a>Přehled scénáře

Azure Load Balancer a související prostředky jsou explicitně definovány při použití [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Azure v současné době poskytuje tři různé metody pro zajištění odchozího připojení pro Azure Resource Manager prostředky. 

| Skladové položky | Scénář | Metoda | Protokoly IP | Popis |
| --- | --- | --- | --- | --- |
| Standardní, základní | [1. Virtuální počítač s veřejnou IP adresou (s Load Balancer nebo bez něj)](#ilpip) | SNAT, maskování portů se nepoužívá. | TCP, UDP, ICMP, ESP | Azure používá veřejnou IP adresu přiřazenou ke konfiguraci protokolu IP síťové karty instance. Instance má k dispozici všechny dočasné porty. Při použití Standard Load Balancer byste měli použít [odchozí pravidla](load-balancer-outbound-rules-overview.md) k explicitnímu definování odchozího připojení. |
| Standardní, základní | [2. Veřejné Load Balancer přidružené k virtuálnímu počítači (bez veřejné IP adresy v instanci)](#lb) | SNAT s maskou portů (PAT) pomocí Load Balancer front-endu | TCP, UDP |Azure sdílí veřejnou IP adresu veřejných Load Balancer front-endu s více privátními IP adresami. Azure používá dočasné porty front-endu na PAT. |
| žádné nebo základní | [3. Samostatný virtuální počítač (bez Load Balancer, žádná veřejná IP adresa)](#defaultsnat) | SNAT s maskou portů (PAT) | TCP, UDP | Azure automaticky určí veřejnou IP adresu pro SNAT, sdílí tuto veřejnou IP adresu s více privátními IP adresami skupiny dostupnosti a používá dočasné porty této veřejné IP adresy. Tento scénář je záložní pro předchozí scénáře. Nedoporučujeme ho, pokud potřebujete viditelnost a kontrolu. |

Pokud nechcete, aby virtuální počítač komunikoval s koncovými body mimo Azure ve veřejném adresním prostoru IP adres, můžete podle potřeby zablokovat přístup pomocí skupin zabezpečení sítě (skupin zabezpečení sítě). Oddíl [zabraňující odchozímu připojení](#preventoutbound) popisuje skupin zabezpečení sítě podrobněji. Pokyny k navrhování, implementaci a správě virtuální sítě bez jakéhokoli odchozího přístupu jsou mimo rámec tohoto článku.

### <a name="ilpip"></a>Scénář 1: Virtuální počítač s veřejnou IP adresou

V tomto scénáři má virtuální počítač přiřazenou veřejnou IP adresu. Vzhledem k tomu, že se jedná o odchozí připojení, nezáleží na tom, jestli je virtuální počítač vyrovnaný k vyrovnávání zatížení. Tento scénář má přednost před ostatními. Když použijete veřejnou IP adresu, virtuální počítač použije veřejnou IP adresu pro všechny odchozí toky.  

Veřejná IP adresa přiřazená k virtuálnímu počítači je vztah 1:1 (nikoli 1: mnoho) a implementovaný jako bezstavové 1:1 NAT.  Maskování portů (PAT) se nepoužívá a virtuální počítač má k dispozici všechny dočasné porty, které je možné použít.

Pokud vaše aplikace zahájí mnoho odchozích toků a dojde k vyčerpání portů SNAT, zvažte přiřazení [veřejné IP adresy, která bude zmírnit omezení SNAT](#assignilpip). Přečtěte si celou [správu vyčerpání SNAT](#snatexhaust) .

### <a name="lb"></a>Scénář 2: Virtuální počítač s vyrovnáváním zatížení bez veřejné IP adresy

V tomto scénáři je virtuální počítač součástí veřejného Load Balancer fondu back-endu. K virtuálnímu počítači není přiřazená žádná veřejná IP adresa. Aby bylo možné vytvořit propojení mezi veřejnou IP frontou a fondem back-end, musí být prostředek Load Balancer nakonfigurovaný s pravidlem nástroje pro vyrovnávání zatížení.

Pokud konfiguraci pravidla nedokončíte, bude chování popsané ve scénáři [samostatného virtuálního počítače bez veřejné IP adresy](#defaultsnat). Pro úspěšné provedení testu stavu není nutné, aby pravidlo mělo funkční naslouchací proces ve fondu back-endu.

Když virtuální počítač s vyrovnáváním zatížení vytvoří odchozí tok, Azure převede privátní zdrojovou IP adresu odchozího toku na veřejnou IP adresu veřejné Load Balancer front-endu. Azure používá k provedení této funkce SNAT. Azure také používá [Pat](#pat) k maskování více privátních IP adres za veřejnou IP adresou. 

Dočasné porty front-endu veřejné IP adresy nástroje pro vyrovnávání zatížení se používají k odlišení jednotlivých toků, které pocházejí z virtuálního počítače. SNAT dynamicky používá [předpřidělené dočasné porty](#preallocatedports) při vytváření odchozích toků. V tomto kontextu se dočasné porty používané pro SNAT nazývají porty SNAT.

Porty SNAT jsou předem přiděleny, jak je popsáno v části [Princip SNAT a Pat](#snat) . Jsou to konečný prostředek, který je možné vyčerpat. Je důležité pochopit, jak se [spotřebovávají](#pat). Informace o tom, jak navrhnout tuto spotřebu a zmírnit podle potřeby, najdete v tématu [Správa vyčerpání SNAT](#snatexhaust).

Když je [k Load Balancer Basic přidružená víc veřejných IP adres](load-balancer-multivip-overview.md), každá z těchto veřejných IP adres je kandidátem na odchozí toky a jedna z nich se vybere náhodně.  

Pokud chcete monitorovat stav odchozích připojení pomocí Load Balancer Basic, můžete k monitorování zpráv pro vyčerpání portů SNAT použít [protokoly Azure monitor pro Load Balancer](load-balancer-monitor-log.md) a [protokoly událostí výstrah](load-balancer-monitor-log.md#alert-event-log) .

### <a name="defaultsnat"></a>Scénář 3: Samostatný virtuální počítač bez veřejné IP adresy

V tomto scénáři není virtuální počítač součástí fondu veřejných Load Balancer (a není součástí interního fondu Standard Load Balancer) a nemá přiřazenou veřejnou IP adresu. Když virtuální počítač vytvoří odchozí tok, Azure převede IP adresu privátního zdroje odchozího toku do veřejné zdrojové IP adresy. Veřejnou IP adresu, která se používá pro tento výstupní tok, nejde konfigurovat a nepočítá se s limitem veřejných IP adres předplatného. Tato veřejná IP adresa nepatří vám a nedá se rezervovat. Pokud znovu nasadíte virtuální počítač nebo skupinu dostupnosti nebo sadu škálování virtuálního počítače, tato veřejná IP adresa se uvolní a vyžádá se nová veřejná IP adresa. Nepoužívejte tento scénář pro seznam povolených IP adres. Místo toho použijte jeden z dalších dvou scénářů, kde explicitně deklarujete odchozí scénář a veřejnou IP adresu, která se má použít pro odchozí připojení.

>[!IMPORTANT] 
>Tento scénář platí také v případě, že je připojena __pouze__ interní základní Load Balancer. Pokud je k virtuálnímu počítači připojený interní Standard Load Balancer, není scénář 3 __dostupný__ .  Kromě používání interního Standard Load Balancer musíte explicitně vytvořit [scénář 1](#ilpip) nebo [scénář 2](#lb) .

Azure používá k provedení této funkce SNAT s maskou portů ([Pat](#pat)). Tento scénář je podobný [scénáři 2](#lb), s výjimkou toho, že se používá žádná kontrola nad použitou IP adresou. Toto je nouzový scénář pro případy, kdy scénáře 1 a 2 neexistují. Tento scénář nedoporučujeme, pokud chcete mít kontrolu nad odchozí adresou. Pokud jsou odchozí připojení důležitou součástí vaší aplikace, měli byste zvolit jiný scénář.

Porty SNAT jsou předpřidělené, jak je popsáno v části [Princip SNAT a Pat](#snat) .  Počet virtuálních počítačů, které sdílejí skupinu dostupnosti, zjistí, která úroveň předplatného se vztahuje.  Samostatný virtuální počítač bez skupiny dostupnosti je pro účely určení předřazení (1024 SNAT) efektivně fondem 1. Porty SNAT jsou konečným prostředkem, který je možné vyčerpat. Je důležité pochopit, jak se [spotřebovávají](#pat). Informace o tom, jak navrhnout tuto spotřebu a zmírnit podle potřeby, najdete v tématu [Správa vyčerpání SNAT](#snatexhaust).

### <a name="combinations"></a>Více kombinovaných scénářů

Scénáře popsané v předchozích částech můžete kombinovat k dosažení konkrétního výsledku. Pokud je přítomno více scénářů, použije se pořadí priorit: [scénář 1](#ilpip) má přednost před [scénářem 2](#lb) a [3](#defaultsnat). [Scénář 2](#lb) Přepisuje [scénář 3](#defaultsnat).

Příkladem Azure Resource Manager nasazení, kde aplikace spoléhá na odchozí připojení k omezenému počtu míst, ale také přijímá příchozí toky přes Load Balancer front-endu. V takovém případě můžete kombinovat scénáře 1 a 2 pro úlevu. Další vzory najdete v přehledu [správy vyčerpání SNAT](#snatexhaust).

### <a name="multife"></a>Několik front-endové pro odchozí toky

#### <a name="standard-load-balancer"></a>Load Balancer úrovně Standard

Standard Load Balancer používá všechny kandidáty u odchozích toků současně v případě, že je k dispozici [více (veřejných) front-endové IP adresy](load-balancer-multivip-overview.md) . Každý front-end vynásobí počet dostupných předem přidělených portů SNAT, pokud je pro odchozí připojení povolené pravidlo vyrovnávání zatížení.

Pro odchozí připojení s novou možností pravidla vyrovnávání zatížení se můžete rozhodnout potlačit IP adresu front-endu, která se používá.

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Standardně je `disableOutboundSnat` Tato možnost standardně _nepravdivá_ a oznamuje, že toto pravidlo pro přidružené virtuální počítače ve fondu back-endu pravidla vyrovnávání zatížení odesílá odchozí SNAT. Vlastnost lze změnit na _hodnotu true_ , pokud chcete, aby Load Balancer nepoužívala přidruženou IP adresu front-endu pro odchozí připojení virtuálních počítačů ve fondu back-end tohoto pravidla vyrovnávání zatížení. `disableOutboundSnat`  A také můžete určit konkrétní IP adresu pro odchozí toky, jak je popsáno v [několika kombinovaných scénářích](#combinations) .

#### <a name="load-balancer-basic"></a>Load Balancer Basic

Load Balancer Basic zvolí jeden front-end, který se má použít pro odchozí toky, pokud jsou kandidáti na odchozí toky [více (veřejných) front-endu IP](load-balancer-multivip-overview.md) . Tento výběr není možné konfigurovat a měli byste zvážit, že je algoritmus výběru náhodný. Můžete určit konkrétní IP adresu pro odchozí toky, jak je popsáno v [několika kombinovaných scénářích](#combinations).

### <a name="az"></a>Zóny dostupnosti

Při použití [Standard Load Balancer s zóny dostupnosti](load-balancer-standard-availability-zones.md)může koncová zóna redundantní v zóně poskytovat odchozí připojení SNAT v zóně redundantní a programování SNAT zachová selhání zóny.  Když se používají front-endy, odchozí připojení SNAT sdílí rozpad s zónou, ke které patří.

## <a name="snat"></a>Porozumění SNAT a PAT

### <a name="pat"></a>Maskování portů SNAT (PAT)

Pokud je k instancím virtuálních počítačů přidružen prostředek veřejné Load Balancer, bude se každý zdroj odchozího připojení přepsán. Zdroj se přepíše z privátního adresního prostoru privátních IP adres virtuální sítě na front-endové veřejné IP adresy nástroje pro vyrovnávání zatížení. Ve veřejném adresním prostoru IP adres musí být pět záznamů toku (zdrojová IP adresa, zdrojový port, transportní protokol IP, cílová IP adresa, cílový port) jedinečný.  Maskování portů SNAT se dá použít buď s protokolem IP TCP nebo UDP.

Dočasné porty (porty SNAT) se používají k dosažení tohoto toho po přepsání privátní zdrojové IP adresy, protože více toků pochází z jedné veřejné IP adresy. Algoritmus SNAT maskování portů přiděluje porty SNAT odlišně pro UDP a TCP.

#### <a name="tcp"></a>Porty TCP SNAT

Jeden port SNAT se spotřebovává na jeden tok na jednu cílovou IP adresu, port. U více toků TCP se stejnou cílovou IP adresou, portem a protokolem každý tok TCP spotřebovává jeden port SNAT. Tím je zajištěno, že toky budou jedinečné, když pocházejí ze stejné veřejné IP adresy a jdou na stejnou cílovou IP adresu, port a protokol. 

Několik toků, z nichž každý má jinou cílovou IP adresu, port a protokol, sdílí jeden port SNAT. Cílová IP adresa, port a protokol vytvářejí toky jedinečné bez nutnosti dalších zdrojových portů, které by rozlišily toky ve veřejném adresním prostoru IP adres.

#### <a name="udp"></a>Porty UDP SNAT

Porty UDP SNAT jsou spravovány jiným algoritmem než porty TCP SNAT.  Load Balancer používá algoritmus, který se označuje jako "kuželový překlad adres (NAT)" na portu UDP.  Pro každý tok se spotřebuje jeden port SNAT bez ohledu na cílovou IP adresu a port.

#### <a name="snat-port-reuse"></a>Opakované použití portu SNAT

Po uvolnění portu je možné port v případě potřeby znovu použít.  Porty SNAT si můžete představit jako sekvence od nejnižší po nejvyšší dostupnou pro daný scénář a jako první dostupný port SNAT se použije pro nová připojení. 
 
#### <a name="exhaustion"></a>Vyčerpání

Po vyčerpání prostředků portů SNAT dojde k selhání odchozích toků, dokud nebudou stávající toky vydány porty SNAT. Load Balancer redeklaracích portů SNAT po ukončení toku a používá prodlevu nečinnosti ve [4 minutách](#idletimeout) pro uvolnění portů SNAT z nečinných toků.

Porty UDP SNAT jsou obvykle vyčerpány mnohem rychleji než porty TCP SNAT z důvodu rozdílu používaného algoritmu. Tento rozdíl je nutné navrhovat a škálovat.

V případě vzorů pro zmírnění podmínek, které běžně vedou k vyčerpání portů SNAT, si přečtěte část [Správa SNAT](#snatexhaust) .

### <a name="preallocatedports"></a>Předběžné přidělení portu SNAT pro maskování portů SNAT (PAT)

Azure používá algoritmus k určení počtu předem přidělených portů SNAT na základě velikosti back-endu při použití maskování portů SNAT ([Pat](#pat)). Porty SNAT jsou dočasné porty, které jsou k dispozici pro konkrétní zdrojovou adresu veřejné IP adresy.

Stejný počet portů SNAT se má předběžně přidělit pro UDP a TCP a spotřebovat nezávisle na IP přenosový protokol.  Použití portu SNAT se ale liší v závislosti na tom, jestli je tok UDP nebo TCP.

>[!IMPORTANT]
>Pořadová jednotka Standard SKU SNAT je podle přenosového protokolu IP a je odvozená od pravidla vyrovnávání zatížení.  Pokud existuje pouze pravidlo vyrovnávání zatížení TCP, je SNAT dostupné pouze pro protokol TCP. Pokud máte pouze pravidlo vyrovnávání zatížení TCP a potřebujete odchozí SNAT pro protokol UDP, vytvořte pravidlo vyrovnávání zatížení UDP ze stejného front-endu do stejného back-end fondu.  Tím se aktivuje programování SNAT pro UDP.  Pracovní pravidlo nebo sondu stavu se nevyžaduje.  Základní SKU SNAT SNAT vždycky pro protokol IP transportu (bez ohledu na transportní protokol zadaný v pravidle vyrovnávání zatížení).

Azure předřadí porty SNAT do konfigurace protokolu IP síťové karty každého virtuálního počítače. Když se do fondu přidá konfigurace IP adresy, porty SNAT se pro tuto konfiguraci IP adres přidělí na základě velikosti fondu back-endu. Při vytváření odchozích toků se [](#pat) Jana dynamicky spotřebovává (až do limitu před přidělením) a uvolní tyto porty, když dojde k ukončení toku nebo [vypršení nečinnosti](#idletimeout) .

V následující tabulce jsou uvedena předalokace portů SNAT pro vrstvy velikostí back-endu:

| Velikost fondu (instance virtuálních počítačů) | Předběžně přidělené porty SNAT na konfiguraci IP adres|
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801 – 1000 | 32 |

>[!NOTE]
> Když použijete Standard Load Balancer s [více front-endu](load-balancer-multivip-overview.md), každá IP adresa front-endu vynásobí počet dostupných portů SNAT v předchozí tabulce. Například fond back-endu 50 virtuálního počítače se dvěma pravidly vyrovnávání zatížení, z nichž každá má samostatnou IP adresu front-endu, bude na konfiguraci protokolu IP používat 2048 (2x 1024) portů SNAT. Podívejte se na podrobnosti pro [více front-endu](#multife).

Pamatujte, že počet dostupných portů SNAT se nepřevádí přímo na počet toků. Jeden port SNAT lze znovu použít pro více jedinečných umístění. Porty se spotřebují jenom v případě, že je potřeba, aby toky byly jedinečné. Pokyny pro návrh a zmírnění najdete v části o [tom, jak spravovat tento prostředek exhaustible](#snatexhaust) a část popisující [Pat](#pat).

Změna velikosti back-end fondu může ovlivnit některé z vašich navázaných toků. Pokud se velikost fondu back-endu zvyšuje a přejde do další úrovně, během přechodu na další větší úroveň fondu back-end se uvolní polovina předpřidělených portů SNAT. Toky, které jsou přidružené k uvolněnému portu SNAT, vyprší časový limit a je nutné je znovu vytvořit. Pokud se pokusíte o nový tok, tok se okamžitě dokončí, dokud nebudou dostupné porty k dispozici.

Pokud se velikost fondu back-endu zmenší a přejde do nižší úrovně, zvýší se počet dostupných portů SNAT. V takovém případě nebudou ovlivněny existující porty SNAT a jejich příslušné toky.

Přidělení portů SNAT jsou specifická pro protokol IP (TCP a UDP se uchovávají samostatně) a vydávají se za následujících podmínek:

### <a name="tcp-snat-port-release"></a>Verze portu TCP SNAT

- Pokud některý ze serverů/klientů odesílá FINACK, port SNAT se uvolní po 240 sekundách.
- Pokud je zjištěna RST, port SNAT se uvolní po 15 sekundách.
- Pokud bylo dosaženo časového limitu nečinnosti, je port uvolněn.

### <a name="udp-snat-port-release"></a>Vydání portu UDP SNAT

- Pokud bylo dosaženo časového limitu nečinnosti, je port uvolněn.

## <a name="problemsolving"></a>Řešení problémů 

Tato část je určená k omezení vyčerpání SNAT a k tomu může dojít při odchozích připojeních v Azure.

### <a name="snatexhaust"></a>Správa vyčerpání portů SNAT (PAT)
[Dočasné porty](#preallocatedports) používané pro [Pat](#pat) jsou prostředek exhaustible, jak je popsáno na [samostatném virtuálním počítači bez veřejné IP adresy](#defaultsnat) a [virtuálního počítače s vyrovnáváním zatížení bez veřejné IP adresy](#lb).

Pokud víte, že zahajujete mnoho odchozích připojení TCP nebo UDP ke stejné cílové IP adrese a portu, a sledujete neúspěšné odchozí připojení nebo se vám doporučuje podpora, že vyčerpáte porty SNAT (předběžně přidělené [dočasné porty](#preallocatedports) ). pomocí [Pat](#pat)) máte několik obecných možností pro zmírnění rizik. Projděte si tyto možnosti a rozhodněte, co je k dispozici a co nejlépe vyhovuje vašemu scénáři. Je možné, že jeden nebo více může pomáhat při správě tohoto scénáře.

Pokud se vám nedaří pochopit chování odchozího připojení, můžete použít statistiku protokolu IP stack (netstat). Nebo může být užitečné sledovat chování připojení pomocí zachycení paketů. Tato zachytávání paketů můžete provést v hostovaném operačním systému vaší instance nebo použít [Network Watcher pro zachytávání paketů](../network-watcher/network-watcher-packet-capture-manage-portal.md).

#### <a name="connectionreuse"></a>Úprava aplikace pro opětovné použití připojení 
Můžete snížit poptávku za dočasné porty, které se používají pro SNAT, a to tak, že znovu použijete připojení ve vaší aplikaci. To platí hlavně pro protokoly jako HTTP/1.1, kde je výchozí nastavení opětovného použití připojení. A další protokoly, které používají protokol HTTP jako jejich přenos (například REST), můžou mít výhodu. 

Opakované použití je vždy lepší než individuální, atomické připojení TCP pro každý požadavek. Opakované použití má za následek více výkonné, velmi efektivní transakce TCP.

#### <a name="connection pooling"></a>Úprava aplikace pro použití sdružování připojení
Ve své aplikaci můžete použít schéma sdružování připojení, ve kterém jsou požadavky interně distribuovány přes pevně danou sadu připojení (podle toho, co je to možné). Toto schéma omezuje počet dočasných používaných portů a vytvoří předvídatelné prostředí. Toto schéma může také zvýšit propustnost žádostí tím, že umožňuje více souběžných operací, když je jedno připojení blokováno na reakci operace.  

Sdružování připojení může již existovat v rámci rozhraní, které používáte k vývoji aplikace nebo nastavení konfigurace aplikace. Sdružování připojení můžete kombinovat s opětovným použitím připojení. Vaše vícenásobné požadavky pak spotřebovávají pevný a předvídatelný počet portů na stejnou cílovou IP adresu a port. Požadavky také využívají efektivní využití transakcí TCP, které snižují latenci a využití prostředků. Transakce UDP můžou také těžit z toho důvodu, že správa počtu toků UDP se může vyhnout podmínkám výfuku a spravovat využití portů SNAT.

#### <a name="retry logic"></a>Upravit aplikaci tak, aby používala méně agresivní logiku opakování
Pokud jsou [předběžně přidělené dočasné porty](#preallocatedports) používané pro [Pat](#pat) vyčerpány nebo dojde k selhání aplikace, agresivní nebo nepřímý pokus o opakování při nedecay a omezení rychlostií služby Logic způsobuje vyčerpání nebo uchování. Můžete snížit poptávku za dočasné porty pomocí méně agresivní logiky opakování. 

Dočasné porty mají časový limit nečinnosti 4 minut (není upravitelný). Pokud jsou opakované pokusy příliš agresivní, vyčerpání není nijak jasné. Proto zvažte, jak--a jak často--vaše aplikace pokusy o opakování jsou důležitou součástí návrhu.

#### <a name="assignilpip"></a>Přiřazení veřejné IP adresy každému virtuálnímu počítači
Přiřazení veřejné IP adresy změní váš scénář na [veřejnou IP adresu virtuálního počítače](#ilpip). Pro virtuální počítač jsou k dispozici všechny dočasné porty veřejné IP adresy, které se používají pro každý virtuální počítač. (Na rozdíl od scénářů, kdy jsou dočasné porty veřejné IP adresy sdílené se všemi virtuálními počítači přidruženými k příslušnému back-end fondu.) Existují kompromisy, které je potřeba vzít v úvahu, například dodatečné náklady na veřejné IP adresy a potenciální dopad na povolený velký počet individuálních IP adres.

>[!NOTE] 
>Tato možnost není k dispozici pro role webového pracovního procesu.

#### <a name="multifesnat"></a>Použít více front-endu

Při použití veřejných Standard Load Balancer přiřadíte [více front-ENDOVÉ IP adresy pro odchozí připojení](#multife) a vynásobte [počet dostupných portů SNAT](#preallocatedports).  Vytvořte konfiguraci IP adresy front-endu, pravidlo a back-end fond pro aktivaci programování SNAT na veřejnou IP adresu front-endu.  Pravidlo nemusí fungovat a sonda stavu nemusí být úspěšná.  Pokud používáte více front-endu pro příchozí i (nikoli jenom pro odchozí), měli byste použít vlastní sondy stavu, abyste zajistili spolehlivost.

>[!NOTE]
>Ve většině případů je vyčerpání portů SNAT označením špatného návrhu.  Ujistěte se, že rozumíte tomu, proč vyčerpáte porty před použitím více front-endu pro přidání portů SNAT.  Můžete maskovat problém, který může vést k selhání později.

#### <a name="scaleout"></a>Horizontální navýšení kapacity

Předem [přidělené porty](#preallocatedports) se přiřazují na základě velikosti fondu back-endu a seskupené do vrstev, aby se minimalizovalo přerušení, když se některé porty musí přidělit, aby se vešly do další větší úrovně velikosti fondu back-endu.  Můžete mít možnost zvýšit intenzitu využití portu SNAT pro daný front-end tím, že back-end fond nastavíte na maximální velikost pro danou vrstvu.  K tomu je potřeba, aby se aplikace mohla škálovat efektivně.

Například dva virtuální počítače ve fondu back-end budou mít 1024 až až na konfiguraci IP adres, což umožňuje celkem 2048 portů SNAT pro nasazení.  Pokud se nasazení zvýšilo na 50 virtuálních počítačů, a to i v případě, že počet předvázaných portů zůstává na virtuálním počítači konstantní, může nasazení použít celkem 51 200 portů SNAT (50 x 1024).  Pokud chcete horizontální navýšení kapacity nasazení, zkontrolujte počet předvázaných [portů](#preallocatedports) na vrstvu, abyste se ujistili, že budete škálovat na maximum pro příslušnou vrstvu.  Pokud jste v předchozím příkladu zvolili horizontální navýšení kapacity na 51 a nikoli 50 instancí, budete postupovat až na další vrstvu a skončíte s méně porty SNAT na jeden virtuální počítač i v celkovém počtu.

Pokud nakonfigurujete horizontální navýšení úrovně velikosti fondu back-end, je možné, že některá z vašich odchozích připojení vyprší časový limit, pokud je potřeba znovu přidělit přidělené porty.  Pokud používáte jenom některé z vašich portů SNAT, horizontální navýšení kapacity v nejbližší větší velikosti fondu back-endu je bezvýznamnými.  Po každém přesunu na další úroveň fondu back-end se existující porty znovu přidělí.  Pokud nechcete, aby to bylo provedeno, je nutné nasazovat nasazení na velikost vrstvy.  Nebo se ujistěte, že se vaše aplikace může v případě potřeby detekovat a opakovat.  Možnost udržení protokolu TCP může pomoci při detekci, kdy porty SNAT již nefungují, protože se znovu přidělují.

### <a name="idletimeout"></a>Obnovení odchozího nečinného časového limitu pomocí kontroly stavu

Odchozí připojení mají časový limit nečinnosti 4 minut. Tento časový limit není upravitelný. Můžete ale v případě potřeby použít přenos (například kontroly stavu protokolu TCP) nebo udržení naživu v aplikační vrstvě a obnovit tento časový limit nečinnosti.  

Při použití kontroly udržení protokolu TCP je stačí povolit je na jedné straně připojení. Například stačí, abyste je povolili na straně serveru pouze k resetování časovače nečinnosti toku a není nutné, aby obě strany iniciovaly kontroly stavu protokolu TCP.  Pro aplikační vrstvu, včetně konfigurací klient-server databáze, existují podobné koncepty.  Na straně serveru můžete zjistit, jaké možnosti jsou pro kontroly a nastavení pro konkrétní aplikaci k dispozici.

## <a name="discoveroutbound"></a>Zjištění veřejné IP adresy, kterou virtuální počítač používá
Existuje mnoho způsobů, jak určit veřejnou zdrojovou IP adresu odchozího připojení. OpenDNS poskytuje službu, která vám umožní zobrazit veřejnou IP adresu vašeho virtuálního počítače. 

Pomocí příkazu nslookup můžete odeslat dotaz DNS pro název myip.opendns.com do překladače OpenDNS. Služba vrátí zdrojovou IP adresu, která se použila k odeslání dotazu. Když z virtuálního počítače spustíte následující dotaz, odpověď je veřejná IP adresa používaná pro tento virtuální počítač:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Prevence odchozího připojení
V některých případech je možné, že virtuální počítač nebude moci vytvořit odchozí tok. Nebo může být potřeba, abyste mohli spravovat, které cíle je možné dosáhnout u odchozích toků, nebo které cíle můžou začít s příchozími toky. V takovém případě můžete pomocí [skupin zabezpečení sítě](../virtual-network/security-overview.md) spravovat cíle, ke kterým může virtuální počítač získat přístup. Pomocí skupin zabezpečení sítě můžete také spravovat, které veřejné cíle mohou iniciovat příchozí toky.

Když použijete NSG k virtuálnímu počítači s vyrovnáváním zatížení, věnujte pozornost [značkám služby](../virtual-network/security-overview.md#service-tags) a [výchozím pravidlům zabezpečení](../virtual-network/security-overview.md#default-security-rules). Musíte zajistit, aby virtuální počítač mohl přijímat požadavky na sondu stavu z Azure Load Balancer. 

Pokud NSG blokuje požadavky na test stavu z výchozí značky AZURE_LOADBALANCER, test stavu virtuálního počítače se nepovede a virtuální počítač se označí jako neplatný. Load Balancer zastaví odesílání nových toků do tohoto virtuálního počítače.

## <a name="limitations"></a>Omezení
- DisableOutboundSnat není k dispozici jako možnost při konfiguraci pravidla vyrovnávání zatížení na portálu.  Místo toho použijte nástroj REST, šablonu nebo nástroje klienta.
- Role webového pracovního procesu bez virtuální sítě a dalších služeb platformy Microsoft můžou být dostupné, když se k vedlejšímu účinku používá jenom vnitřní Standard Load Balancer, která se dá použít jenom pro služby předplatného a jiné služby platformy. Nespoléhá se na tento vedlejší účinek jako na samotnou službu nebo na podkladovou platformu se může změnit bez předchozího upozornění. Vždy musíte předpokládat, že pokud budete chtít používat jenom interní Standard Load Balancer, musíte v případě potřeby explicitně vytvořit odchozí připojení. [Výchozí scénář SNAT](#defaultsnat) 3, který je popsaný v tomto článku, není k dispozici.

## <a name="next-steps"></a>Další kroky

- Další informace o [Load Balanceru úrovně Standard](load-balancer-standard-overview.md).
- Přečtěte si další informace o [odchozích pravidlech](load-balancer-outbound-rules-overview.md) pro standardní veřejné Load Balancer.
- Přečtěte si další informace o [Load Balancer](load-balancer-overview.md).
- Přečtěte si další informace o [skupinách zabezpečení sítě](../virtual-network/security-overview.md).
- Přečtěte si o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.
