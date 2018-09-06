---
title: Odchozích připojení v Azure | Dokumentace Microsoftu
description: Tento článek vysvětluje, jak Azure umožňuje virtuálním počítačům komunikovat s veřejné internetové služby.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/27/2018
ms.author: kumud
ms.openlocfilehash: ea8e8ae9b0f487481ac2f25d4e2b9c5733e15431
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "43842251"
---
# <a name="outbound-connections-in-azure"></a>Odchozích připojení v Azure

Azure poskytuje odchozí připojení pro nasazení zákazníků prostřednictvím několika různých mechanismů. Tento článek popisuje, co jsou scénáře, kdy se vztahují, jak fungují a jejich správě.

>[!NOTE] 
>Tento článek se týká pouze nasazení Resource Manager. Kontrola [odchozí připojení (Classic)](load-balancer-outbound-connections-classic.md) pro všechny scénáře nasazení Classic do Azure.

Nasazení v Azure může komunikovat s koncovými body mimo Azure v veřejný adresní prostor IP adres. Když instance zahájí odchozího toku k cíli v veřejný adresní prostor IP adres, Azure dynamicky mapuje privátní IP adresu na veřejnou IP adresu. Po vytvoření tohoto mapování návratový přenos pro tento tok odchozí pocházející ze dosáhnout taky privátní IP adresa původu toku.

Azure používá k provedení této funkce překlad síťových adres zdroje (SNAT). Když více privátní IP adresy se vydávají za jednu veřejnou IP adresu, Azure využívá [port překladu adres (Jan)](#pat) k maskovat privátních IP adres. Dočasné porty se používají pro token PAT a jsou [předpřidělené](#preallocatedports) na základě velikosti fondu.

Existuje více [odchozí scénáře](#scenarios). Tyto scénáře můžete kombinovat podle potřeby. Seznamte se s nimi pečlivě pro pochopení možností, omezení a vzory, která je použita k modelu nasazení a scénář aplikace. Přečtěte si pokyny pro [Správa těchto scénářů](#snatexhaust).

>[!IMPORTANT] 
>Load balancer úrovně Standard zavádí nové možnosti a různé chování odchozí připojení.   Například [scénář 3](#defaultsnat) neexistuje interní Load balanceru úrovně Standard je k dispozici a různé kroky je třeba provést.   Pečlivě si prostudujte vám pomohou pochopit celkový koncepty a rozdíly mezi skladovými položkami této celý dokument.

## <a name="scenarios"></a>Přehled scénářů

Nástroj Azure Load Balancer a související prostředky nejsou explicitně definovány při použití [Azure Resource Manageru](#arm).  Azure poskytuje aktuálně tří různých způsobů dosažení odchozí připojení pro prostředky Azure Resource Manageru. 

| Scénář | Metoda | Protokoly IP | Popis |
| --- | --- | --- | --- |
| [1. Virtuální počítač s veřejnou IP adresu Instance úroveň adresou (s nebo bez nástroje pro vyrovnávání zatížení)](#ilpip) | SNAT, ho maskují portu se nepoužívá. | TCP, UDP, PROTOKOL ICMP, ESP | Azure používá veřejné IP adresy přiřazené ke konfiguraci IP adresy z instance NIC. Instance má všechny dočasné porty, které jsou k dispozici. |
| [2. Veřejný nástroj pro vyrovnávání zatížení, které jsou přidružené k virtuálnímu počítači (žádné Instance úroveň veřejnou IP adresu na instanci)](#lb) | SNAT pomocí portu ho maskují (cesta) pomocí nástroje pro vyrovnávání zatížení front-endů | TCP, UDP |Veřejnou IP adresu veřejnou front-endů nástroje pro vyrovnávání zatížení Azure sdílí s více privátními IP adresami. Azure používá dočasných portů front-endů token pat. |
| [3. Samostatný virtuální počítač (žádná služba Vyrovnávání zatížení, žádné Instance úroveň veřejnou IP adresu)](#defaultsnat) | SNAT pomocí portu ho maskují (cesta) | TCP, UDP | Azure automaticky označí veřejnou IP adresu pro SNAT, sdílí s více privátními IP adresami sady dostupnosti. Tato veřejná IP adresa a používá dočasné porty tuto veřejnou IP adresu. Toto je základní scénář pro předchozí scénáře. Pokud potřebujete viditelnosti a kontroly to nedoporučujeme. |

Pokud nechcete, aby virtuální počítač komunikovat s koncovými body mimo Azure v veřejný prostor IP adres, můžete použít skupiny zabezpečení sítě (Nsg) k blokování přístupu podle potřeby. V části [brání odchozí připojení](#preventoutbound) podrobněji popisuje skupiny zabezpečení sítě. Pokyny k návrhu, implementaci a správu virtuální síť, bez jakékoli odchozí přístup je mimo rámec tohoto článku.

### <a name="ilpip"></a>Scénář 1: Virtuální počítač s veřejnou IP adresu Instance úroveň adresou

V tomto scénáři má virtuální počítač instanci úroveň veřejné IP (ILPIP) přiřazenou. Jde o odchozích připojení, nezáleží, jestli virtuální počítač je Vyrovnávané nebo ne. Tento scénář má přednost před ostatní. Při použití ILPIP, virtuální počítač ILPIP používá pro všechny odchozí toky.  

Veřejné IP adresa přiřazená k virtuálnímu počítači je 1:1 vztahu (spíše než 1:many) a je implementovaný jako bezstavové NAT. 1:1  Port maskování (Jan) se nepoužívá a virtuální počítač má všechny dočasné porty, které jsou k dispozici pro použití.

Pokud aplikace zahájí mnoho odchozích toků a dochází k vyčerpání portů SNAT, vezměte v úvahu přiřazení [ILPIP ke zmírnění SNAT omezení](#assignilpip). Kontrola [Správa SNAT vyčerpání](#snatexhaust) v celém rozsahu.

### <a name="lb"></a>Scénář 2: S vyrovnáváním zatížení VM bez Instance úroveň veřejné IP adresy

V tomto scénáři je virtuální počítač součástí veřejného back-endového fondu nástroje pro vyrovnávání zatížení. Virtuální počítač nemá přiřazenou veřejnou IP adresu. Prostředku nástroje pro vyrovnávání zatížení musí mít nakonfigurovanou pravidlo služby load balancer můžete vytvořit propojení mezi veřejnou IP front-endu s back-endový fond.

Pokud neprovedete konfiguraci tohoto pravidla, chování je, jak je popsáno ve scénáři [samostatný virtuální počítač s žádná úroveň veřejná IP adresa Instance](#defaultsnat). Není nutné u pravidla splnit mít funkční naslouchací proces ve fondu back-endu pro sondu stavu úspěšné.

Při vytváření virtuálního počítače s vyrovnáváním zatížení odchozího toku, přeloží Azure privátní zdrojové IP adresy odchozí tok veřejnou IP adresu veřejnou front-endu nástroje pro vyrovnávání zatížení. Azure používá k provedení této funkce SNAT. Azure také používá [token PAT](#pat) k maskovat více privátních IP adres za veřejné IP adresy. 

Dočasné porty nástroje pro vyrovnávání zatížení veřejnou IP adresu front-endovou slouží k rozlišení jednotlivých toků, vytvoří se virtuální počítač. Dynamicky používá SNAT [předpřidělené dočasné porty](#preallocatedports) při vytvoření odchozích toků. V tomto kontextu se označují jako dočasné porty používané pro SNAT SNAT porty.

SNAT porty jsou předpřidělené, jak je popsáno v [SNAT principy a token PAT](#snat) oddílu. Jsou to omezené prostředek, který může dojít k vyčerpání. Je důležité pochopit, jak jsou [spotřebované](#pat). Chcete-li pochopit, jak navrhnout za toto využití a zmírnit podle potřeby, zkontrolovat [Správa SNAT vyčerpání](#snatexhaust).

Když [několika (veřejných) IP adresy jsou spojeny s základní nástroje pro vyrovnávání zatížení](load-balancer-multivip-overview.md), všechny tyto veřejné IP adresy jsou [Release candidate pro odchozí toky](#multivipsnat), a jedna je vybrána.  

Pokud chcete monitorovat stav odchozí připojení s základní nástroje pro vyrovnávání zatížení, můžete použít [Log Analytics pro nástroj pro vyrovnávání zatížení](load-balancer-monitor-log.md) a [upozornění protokolů událostí](load-balancer-monitor-log.md#alert-event-log) pro monitorování zpráv vyčerpání portů SNAT.

### <a name="defaultsnat"></a>Scénář 3: Samostatný virtuální počítač bez Instance úroveň veřejné IP adresy

V tomto případě virtuální počítač není součástí fondu veřejný nástroj pro vyrovnávání zatížení (a není součástí fondu interního Load balanceru úrovně Standard) a nemá ILPIP adresu přiřazenou. Když virtuální počítač vytvoří odchozí tok, přeloží Azure privátní zdrojové IP adresy odchozí tok veřejné Zdrojová IP adresa. Veřejnou IP adresu použít pro tento odchozí tok není Konfigurovatelný a nepočítá s limitem předplatného veřejný IP prostředek.

>[!IMPORTANT] 
>Tento scénář platí i při __pouze__ je připojen interní Load balancer úrovně Basic. Scénář 3 je __není k dispozici__ když interní Load balanceru úrovně Standard je připojen k virtuálnímu počítači.  Musíte explicitně vytvořit [scénář 1](#ilpip) nebo [scénář 2](#lb) kromě používání interní Load balanceru úrovně Standard.

Azure používá SNAT s ho maskují port ([token PAT](#pat)) k provedení této funkce. Tento scénář je podobný [scénář 2](#lb), s výjimkou není žádnou kontrolu nad tím IP adresa používá. Toto je základní scénář pro při scénáře 1 a 2 neexistují. Pokud chcete řídit odchozí adresy nedoporučujeme tento scénář. Pokud je odchozí připojení jsou důležitou součástí vaší aplikace, měli byste zvolit jiný scénář.

SNAT porty jsou předpřidělené, jak je popsáno v [SNAT principy a token PAT](#snat) oddílu.  Počet sdílení dostupnosti virtuálních počítačů určuje, jaké úroveň předběžné přidělování platí.  Samostatný virtuální počítač bez dostupnosti je v podstatě fond 1 pro účely stanovení předběžné přidělení (porty 1024 SNAT). SNAT porty jsou omezené prostředek, který může dojít k vyčerpání. Je důležité pochopit, jak jsou [spotřebované](#pat). Chcete-li pochopit, jak navrhnout za toto využití a zmírnit podle potřeby, zkontrolovat [Správa SNAT vyčerpání](#snatexhaust).

### <a name="combinations"></a>Více, kombinované scénáře

Můžete kombinovat scénáře popsané v předchozích částech k dosažení konkrétního výsledku. Při scénářích s více jsou k dispozici, se vztahuje pořadí podle priority: [scénář 1](#ilpip) má přednost před [scénář 2](#lb) a [3](#defaultsnat). [Scénář 2](#lb) přepíše [scénář 3](#defaultsnat).

Příkladem je nasazení služby Azure Resource Manageru, kde aplikace spoléhá na odchozí připojení pro omezený počet míst, ale také přijímá příchozí toky přes front-endu nástroje pro vyrovnávání zatížení. V takovém případě můžete kombinovat scénáře 1 a 2 pro humanitární. Další způsoby, najdete v tématu [Správa SNAT vyčerpání](#snatexhaust).

### <a name="multife"></a> Několik front-endů pro odchozí toky

#### <a name="load-balancer-standard"></a>Load Balancer úrovně Standard

Load balancer úrovně Standard používá všechny kandidáty pro odchozích toků na stejný čas, kdy [několika (veřejných) IP front-endů](load-balancer-multivip-overview.md) je k dispozici. Každá front-endu vynásobí počet dostupných portů předběžně přidělené SNAT Pokud pravidlo Vyrovnávání zatížení je povolené pro odchozí připojení.

Můžete potlačit použitím front-endové IP adresy z používán pro odchozí připojení s novou možností pravidlo Vyrovnávání zatížení:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Za normálních okolností se tato možnost výchozí _false_ a oznamuje, že toto pravidlo programy odchozí SNAT pro přidružené virtuální počítače v back-endový fond pravidlo Vyrovnávání zatížení.  To můžete změnit na _true_ zabránit Load Balancer pomocí IP adresy přidružené front-endu pro odchozí připojení pro virtuální počítač je ve fondu back-end toto pravidlo Vyrovnávání zatížení.  A můžete stále taky určit konkrétní IP adresu pro odchozí toky, jak je popsáno v [více, kombinované scénáře](#combinations) také.

#### <a name="load-balancer-basic"></a>Základní nástroje pro vyrovnávání zatížení

Základní nástroje pro vyrovnávání zatížení vybere jeden front-endu má být použit pro odchozí toky při [několika (veřejných) IP front-endů](load-balancer-multivip-overview.md) jsou kandidáty pro odchozí toky. Tato volba není Konfigurovatelný a je třeba zvážit algoritmus výběru bude náhodné. Konkrétní IP adresu pro odchozí toky můžete určit, jak je popsáno v [více, kombinované scénáře](#combinations).

### <a name="az"></a> Zóny dostupnosti

Při použití [Load Balanceru úrovně Standard se zónami dostupnosti](load-balancer-standard-availability-zones.md)zónově redundantních front-endů může poskytnout zónově redundantní odchozí připojení SNAT a programování SNAT odolává selhání zóny.  Když oblastmi front-endů se používají, odchozí připojení SNAT sdílet rozpadu zónu, do kterých patří.

## <a name="snat"></a>Principy SNAT a token PAT

### <a name="pat"></a>Port maskování SNAT PAT

Pokud prostředek veřejné nástroje pro vyrovnávání zatížení je spojen s instancí virtuálních počítačů, každý zdroj odchozí připojení je přepsán. Zdroj je přepsána z virtuální sítě privátní adresní prostor IP adres pro front-endu nástroje pro vyrovnávání zatížení veřejnou IP adresu. V veřejný prostor IP adres musí být jedinečný 5-n-tice tok (Zdrojová IP adresa, zdrojový port, přenosový protokol IP, cílová IP adresa, cílový port).  Maskování SNAT portu je možné s protokoly TCP nebo UDP IP.

Dočasné porty (SNAT) umožňují dosáhnete po přepisování privátní zdrojovou IP adresu, protože více toků pocházejí z jedné veřejné IP adresy. Algoritmu port maskování SNAT jinak přiděluje SNAT porty UDP a TCP.

#### <a name="tcp"></a>SNAT porty TCP

Na jeden tok do jedné cílové IP adresy, portu se spotřebovává jeden port SNAT. Každý tok TCP více Velkoobjemové toky na stejnou cílovou IP adresu, port a protokol, vyžaduje jeden port SNAT. Tím se zajistí, že toky jsou jedinečné, když pocházejí z stejnou veřejnou IP adresu a přejděte do stejné cílové IP adresy, portu a protokolu. 

Více toků, každý z nich různé cílové IP adresy, portu a protokolu, sdílet jeden port SNAT. Cílové IP adresy, portu a protokolu zkontrolujte toky jedinečný bez nutnosti dalších zdrojových portů k rozlišení toků v veřejný adresní prostor IP adres.

#### <a name="udp"></a> SNAT porty UDP

Porty UDP SNAT spravuje jiný algoritmus než porty TCP SNAT.  Nástroj pro vyrovnávání zatížení používá algoritmus označuje jako "s omezením port kužel NAT" pro protokol UDP.  Pro každý tok, bez ohledu na cílovou IP adresu, port se spotřebovává jeden port SNAT.

#### <a name="exhaustion"></a>Vyčerpání

Když jsou k vyčerpání prostředků port SNAT, odchozí toky neúspěšné, dokud stávající toky release SNAT porty. Nástroje pro vyrovnávání zatížení uvolňuje SNAT porty, když tok se zavře a použije [časový limit nečinnosti 4 minuty](#idletimeout) pro opětovné získání SNAT porty z nečinnosti toky.

Porty UDP SNAT vyčerpání obvykle mnohem rychleji než porty TCP SNAT kvůli rozdílu ve použitý algoritmus. Je nutné návrh a škálování testování pomocí tohoto rozdílu v úvahu.

Vzory podmínky, které běžně vedou k vyčerpání portů SNAT zmírnit, najdete v tématu [Správa SNAT](#snatexhaust) oddílu.

### <a name="preallocatedports"></a>Předběžné přidělení dočasných portů pro port ho maskují SNAT PAT

Azure používá algoritmus k určení počtu předpřidělené SNAT portů jsou k dispozici na základě velikosti fondu back-endu při použití portu maskování SNAT ([token PAT](#pat)). SNAT porty jsou dočasné porty, které jsou k dispozici pro konkrétní veřejné zdrojové adresy IP.

Stejný počet SNAT porty jsou předpřidělené UDP a TCP v uvedeném pořadí a využívat nezávisle na transportní protokol IP.  Použití portů SNAT se však liší v závislosti na tom, jestli je tok UDP nebo TCP.

>[!IMPORTANT]
>Standardní SKU SNAT programování je za IP přenosový protokol a odvozené z pravidla Vyrovnávání zatížení.  Pokud existuje pravidlo Vyrovnávání zatížení TCP jenom SNAT je dostupná jenom pro protokol TCP. Pokud máte jenom TCP pravidlo Vyrovnávání zatížení a potřebujete odchozí SNAT UDP, vytvořte pravidla ze stejné front-endu do stejného back-endový fond Vyrovnávání zatížení UDP.  Tím se aktivuje SNAT programování pro protokol UDP.  Pracovní pravidlo nebo stavu testu se nevyžaduje.  Základní skladová položka SNAT vždy programy SNAT pro obě přenosový protokol IP, bez ohledu na to přenosový protokol podle pravidla Vyrovnávání zatížení.

Azure preallocates SNAT porty ke konfiguraci IP adresy nic každý virtuální počítač. Když se do fondu Přidá konfiguraci IP, jsou pro tuto konfiguraci IP adresy na základě velikosti fondu back-endu předpřidělené porty SNAT. Při vytváření odchozích toků [token PAT](#pat) dynamicky využívá (až do limitu předběžně přidělené) a uvolní tyto porty po zavření toku nebo [vypršení časového limitu nečinnosti](#idletimeout) stát.

Následující tabulka uvádí preallocations SNAT port pro úrovně velikosti fondů back-endu:

| Velikost fondu (instance virtuálních počítačů) | Předběžně přidělené SNAT porty na konfiguraci IP adresy|
| --- | --- |
| 1 – 50 | 1,024 |
| 51 – 100 | 512 |
| 101 – 200 | 256 |
| 201-400 | 128 |
| 401 800 | 64 |
| 801 – 1 000 | 32 |

>[!NOTE]
> Při použití Load Balanceru úrovně Standard s [několik front-endů](load-balancer-multivip-overview.md), [každou IP adresu front-endu vynásobí počet dostupných portů SNAT](#multivipsnat) v předchozí tabulce. Back-endový fond 50 virtuálních počítačů s 2 pravidla Vyrovnávání zatížení, každý s samostatné front-endovou IP adresou, například bude používat porty SNAT 2048 (2 x 1 024) na konfiguraci IP adresy. Získáte v podrobnostech o [několik front-endů](#multife).

Mějte na paměti, že počet dostupných portů SNAT nepřekládá přímo na počet toků. Jeden port SNAT můžete znovu použít pro více míst jedinečný. Porty se spotřebuje, pouze pokud je nutné vytvářet toky jedinečný. Pokyny k návrhu a zmírnění distribuovaných útoků, přečtěte si část o [jak ke správě tohoto prostředku vyčerpatelným](#snatexhaust) a v části popisující [token PAT](#pat).

Změna velikosti back-endový fond může mít vliv na některé zavedené toků. Pokud velikost fondu back-endu zvyšuje a přejde na další úroveň, polovinu vaše předběžně přidělené SNAT portů je vyžádá zpět systém během přechodu na další úroveň větší fond back-endu. Toky, které jsou spojeny s portem uvolňovaného SNAT vyprší časový limit a musí být zrušena. Při pokusu o nový tok, tok proběhne úspěšně okamžitě, za předpokladu, předběžně přidělené porty jsou k dispozici.

Pokud se zmenší velikost fondu back-endu a zvyšuje počet dostupných portů SNAT přechody do nižší úrovně. V tomto případě existující přidělených SNAT portů a jejich příslušné toky to nebude mít vliv.

Přidělení SNAT porty jsou konkrétní IP přenosový protokol (TCP a UDP se zachovají samostatně) a se vydávají za následujících podmínek:

### <a name="tcp-snat-port-release"></a>Port TCP SNAT vydání verze

- Pokud obě server/klient odešle FIN/potvrzení, bude vydána SNAT port po 240 sekund.
- Pokud je zobrazena RVNÍ, bude vydána SNAT port po 15 sekundách.
- bylo dosaženo časového limitu nečinnosti

### <a name="udp-snat-port-release"></a>Port UDP SNAT vydání verze

- bylo dosaženo časového limitu nečinnosti

## <a name="problemsolving"></a> Řešení problémů 

Tato část je určena pro zmírnění SNAT vyčerpání a další scénáře, které mohou nastat u odchozích připojení v Azure.

### <a name="snatexhaust"></a> Správa vyčerpání portů SNAT PAT
[Dočasné porty](#preallocatedports) používá pro [token PAT](#pat) jsou vyčerpatelným prostředků, jak je popsáno v [samostatný virtuální počítač bez Instance úroveň veřejnou IP adresu](#defaultsnat) a [s vyrovnáváním zatížení virtuálních počítačů bez Úroveň veřejnou IP adresu instance](#lb).

Pokud víte, že jste zahajování mnoho odchozí připojení TCP nebo UDP na stejnou cílovou IP adresu a port, a radí, podpora, že jste spotřebovává SNAT porty nebo sledovat selhání odchozí připojení (předpřidělené [dočasné porty](#preallocatedports) používané [token PAT](#pat)), máte několik možností, jak obecná omezení rizik. Prohlédněte si tyto možnosti a rozhodnout, co je k dispozici a pro váš scénář nejvhodnější. Je možné, že jeden nebo více může pomoct spravovat tento scénář.

Pokud máte potíže s principy odchozích připojení chování, můžete použít statistiky zásobníku IP (netstat). Nebo může být užitečné při sledování chování připojení pomocí zachytávání paketů. Můžete provádět tyto zachytávání paketů v hostovaném operačním systému vaší instance nebo použít [Network Watcher pro zachytávání paketů](../network-watcher/network-watcher-packet-capture-manage-portal.md).

#### <a name="connectionreuse"></a>Úprava aplikace opakovaně používat připojení 
Můžete snížit poptávka po dočasné porty, které se používají pro SNAT opakovaným připojení ve vaší aplikaci. To platí zejména pro protokoly HTTP/1.1, kde opakované použití připojení je výchozí nastavení. A zase využívat jiné protokoly, které používají protokol HTTP jako jejich přenos (například REST). 

Vždy je lepší než osobou, atomické připojení TCP pro každý požadavek znovu. Znovu použít výsledky v výkonnější, velmi efektivní TCP transakce.

#### <a name="connection pooling"></a>Upravit aplikaci, aby používala sdružování připojení
Můžete použít připojení sdružování schéma ve vaší aplikaci, kde jsou požadavky interně distribuované napříč fixní sadu připojení (každý opětovné použití tam, kde je to možné). Toto schéma omezí počet dočasných portů používá a vytvoří prostředí s lépe předvídatelným. Toto schéma může také zvýšit propustnost požadavků tím, že více souběžných operací, když blokuje jedno připojení pro odpověď operace.  

Sdružování připojení možná již existuje v rámci, které používáte k vývoji vaší aplikace a nastavení konfigurace pro vaši aplikaci. Můžete kombinovat s opakované použití připojení sdružování připojení. Více požadavků je pak využívat pevná a předvídatelná počet portů, které mají stejnou cílovou IP adresu a port. Požadavky také výhody efektivní využití TCP transakcí snížení latence a využití prostředků. UDP transakce mohou také těžit, protože správa UDP toků můžete zase vyhnout výfukového podmínky a spravovat využití portu SNAT.

#### <a name="retry logic"></a>Upravit aplikaci, aby používala méně agresivní logika opakovaných pokusů
Když [předpřidělené dočasné porty](#preallocatedports) používá pro [token PAT](#pat) jsou vyčerpání nebo aplikace dojde k selhání, agresivní nebo útokům hrubou silou opakuje bez decay a omezení rychlosti logiku způsobit vyčerpání dojít, nebo zachovat. Poptávka po dočasné porty můžete omezit použitím méně agresivní logika opakovaných pokusů. 

Dočasné porty mít 4 minutách nečinnosti časový limit (není možné nastavit). Pokud opakované pokusy jsou příliš agresivní, vyčerpání nemá žádnou možnost Vymazat sama o sobě. Vzhledem k tomu, jak – a jak často – aplikace opakování transakce je proto zásadní součástí tohoto návrhu.

#### <a name="assignilpip"></a>Přiřadit úrovni veřejná IP adresa Instance pro každý virtuální počítač
Přiřazení ILPIP změní váš scénář [úroveň veřejná IP adresa Instance k virtuálnímu počítači](#ilpip). Všechny dočasné porty veřejné IP adresy, které se používají pro jednotlivé virtuální počítače jsou k dispozici k virtuálnímu počítači. (Na rozdíl od scénáře, ve kterém jsou dočasné porty veřejnou IP adresu sdílené všechny virtuální počítače přidružené k příslušné back-endový fond.) Existují kompromisy vzít v úvahu, jako je například další náklady na veřejné IP adresy a možný dopad na seznam povolených velký počet jednotlivých IP adres.

>[!NOTE] 
>Tato možnost není k dispozici v případě webových rolí pracovního procesu.

#### <a name="multifesnat"></a>Použít několik front-endů

Při použití veřejného Load Balanceru úrovně Standard, přiřadíte [několik front-endová IP adres pro odchozí připojení](#multife) a [vynásobte počet dostupných portů SNAT](#preallocatedports).  Je potřeba vytvořit front-endová konfigurace protokolu IP, pravidlo a back-endový fond pro aktivaci programování SNAT na veřejnou IP adresu front-endu.  Pravidlo nemusí fungovat a není potřeba žádná sonda stavu úspěšné.  Pokud používáte několik front-endů pro příchozí i (nejenom pro odchozí), měli byste použít sondy stavu vlastní také zajistit spolehlivost.

>[!NOTE]
>Ve většině případů je vyčerpání portů SNAT znaménko chybný návrhu.  Ujistěte se, že víte, proč jsou vyčerpání portů před použitím více front-endů přidání portů SNAT.  Může být maskování problém, což může vést k selhání později.

#### <a name="scaleout"></a>Horizontální navýšení kapacity

[Předpřidělené porty](#preallocatedports) jsou přiřazeny v závislosti na velikosti fondu back-endu a seskupené do vrstev, pokud chcete přerušení minimalizovat, když některé porty muset znovu přidělit, aby další vrstvou velikost větší fond back-endu.  Můžete mít možnost intenzitu zrcadlových SNAT port využití pro daný front-endu se tak zvýší o škálování fondu back-endu na maximální velikost v dané vrstvě.  To vyžaduje pro aplikaci pro horizontální navýšení kapacity efektivně.

2 virtuální počítače v back-endový fond by mít například 1024 SNAT porty každou konfiguraci IP, která umožňuje celkem 2 048 SNAT porty pro nasazení k dispozici.  Pokud nasazení zvýší na 50 virtuální počítače, i když počet portů SNAT předpřidělené konstanta zůstane porty na virtuální počítač, a cena celkem 51,200 (50 x 1 024) je možné toto nasazení určeno.  Pokud chcete horizontálně navýšit kapacitu vašeho nasazení, zkontrolujte počet [předpřidělené porty](#preallocatedports) za vrstvu, a ujistěte se, že obrazce vaše horizontální navýšení kapacity až na příslušné úrovni.  V předchozím příkladu Pokud jste vybrali pro horizontální navýšení kapacity až 51 místo 50 instancí by o průběhu na další úroveň a end si s menším počtem SNAT porty na virtuální počítač také jako celkový počet.

Pokud horizontální navýšení kapacity na další úroveň velikost větší fond back-end je potenciální pro některé z vašich odchozích připojení, vypršení časového limitu přidělené porty museli znovu přidělit.  Pokud využíváte jen některé z vašich SNAT porty, horizontální navýšení kapacity v nejbližší větší velikost fondu back-end není důležitá.  Poloviční existující porty budou nevyčerpané pokaždé, když přejdete na další úroveň fondu back-endu.  Pokud nechcete, aby to provedou, budete muset tvarovat nasazení tak, aby velikost vrstvy.  Nebo Ujistěte se, že vaše aplikace můžete zjišťovat a opakujte podle potřeby.  Keepalive TCP vám můžou pomoct v rozpoznat, kdy SNAT porty už funkce z důvodu přerozděleni.

### <a name="idletimeout"></a>Keepalive použít k resetování odchozí časový limit nečinnosti

Odchozí připojení mají časový limit nečinnosti 4 minuty. Tento časový limit není měnitelné. Aktualizace nečinné toku a obnovit tento časový limit nečinnosti v případě potřeby však můžete použít přenosu (například TCP keepalive) nebo keepalive aplikační vrstvu.  

Při použití keepalive TCP, je dostatečná povolení na jedné straně připojení. Například je dostatečná povolení na pouze k resetování nečinnosti tok na straně serveru a není nutné pro obě strany iniciované keepalive TCP.  Podobně jako koncepty pro aplikační vrstvu, včetně konfigurace klienta a serveru databáze neexistují.  Zkontrolujte na straně serveru, pro jaké možnosti jsou určeny pro keepalive konkrétní aplikace.

## <a name="discoveroutbound"></a>Zjišťování veřejné IP adresy, která používá virtuální počítač
Existuje mnoho způsobů, jak určit IP adresu veřejné zdrojového odchozí připojení. OpenDNS poskytuje službu, která můžete zobrazit veřejnou IP adresu vašeho virtuálního počítače. 

Při použití příkazu nslookup, můžete odeslat dotaz DNS pro název myip.opendns.com OpenDNS překládání. Služba vrátí zdrojovou IP adresu, která jste použili k zaslání dotazu. Při spuštění následujícího dotazu z vašeho virtuálního počítače je odpověď na veřejnou IP adresu použít pro tento virtuální počítač:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Brání odchozí připojení
V některých případech není žádoucí, pro virtuální počítač, který bude moct vytvořit odchozí tok. Nebo může být požadavek na správu, které cíle je dostupný odchozí toky nebo které cíle zahájit toky v příchozím provozu. V takovém případě můžete použít [skupiny zabezpečení sítě](../virtual-network/security-overview.md) ke správě cílů, které můžete oslovit virtuálního počítače. Můžete také pomocí skupin zabezpečení sítě pro správu, který veřejný cíl můžete zahájit toky v příchozím provozu.

Když použijete skupinu NSG k virtuálnímu počítači s vyrovnáváním zatížení, věnujte pozornost [značky služeb](../virtual-network/security-overview.md#service-tags) a [výchozích pravidlech zabezpečení](../virtual-network/security-overview.md#default-security-rules). Ujistěte se, že virtuální počítač může přijímat požadavky sondy stavu ze služby Azure Load Balancer. 

Pokud skupinu NSG blokuje požadavky sondy stavu z výchozí značky AZURE_LOADBALANCER, sondy stavu vašeho virtuálního počítače se nezdaří a virtuálního počítače je označena. Nástroj pro vyrovnávání zatížení zastaví odesílání nových toků na tomto virtuálním počítači.

## <a name="limitations"></a>Omezení
- DisableOutboundSnat není k dispozici možnost při konfiguraci pravidla na portálu pro vyrovnávání zatížení.  Místo toho použijte REST, šablonu nebo klienta nástroje.
- Webových rolí pracovního procesu bez virtuální sítě a dalších služeb platformy Microsoft může být přístupný, když kvůli vedlejším účinkem z jak pre-VNet služeb a další platformy služby funkce se používá jenom interní Load balanceru úrovně Standard. Tomuto vedlejšímu efektu nesmí využívají, jako příslušné služby sebe samu ani na základní platformě mohou změnit bez předchozího upozornění. Musíte vždy předpokládat, že je potřeba vytvořit odchozí připojení explicitně v případě potřeby při použití interní Load balanceru úrovně Standard pouze. [Výchozí SNAT](#defaultsnat) scénář 3 popsaných v tomto článku není k dispozici.

## <a name="next-steps"></a>Další postup

- Další informace o [nástroje pro vyrovnávání zatížení](load-balancer-overview.md).
- Další informace o [Load Balanceru úrovně Standard](load-balancer-standard-overview.md).
- Další informace o [skupiny zabezpečení sítě](../virtual-network/security-overview.md).
- Přečtěte si o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.
