---
title: Odchozí připojení v Azure
titleSuffix: Azure Load Balancer
description: Tento článek vysvětluje, jak Azure umožňuje virtuálním mům komunikovat s veřejnými internetovými službami.
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
ms.openlocfilehash: f8f21405a79a6fcf70adef9815ba06a229d6954d
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886972"
---
# <a name="outbound-connections-in-azure"></a>Odchozí připojení v Azure

Azure poskytuje odchozí připojení pro nasazení zákazníků prostřednictvím několika různých mechanismů. Tento článek popisuje, jaké jsou scénáře, kdy se použijí, jak fungují a jak je spravovat.

>[!NOTE] 
>Tento článek se týká pouze nasazení Správce prostředků. Zkontrolujte [odchozí připojení (classic)](load-balancer-outbound-connections-classic.md) pro všechny klasické scénáře nasazení v Azure.

Nasazení v Azure může komunikovat s koncovými body mimo Azure ve veřejném IP adresním prostoru. Když instance iniciuje odchozí tok do cíle ve veřejném adresním prostoru IP, Azure dynamicky mapuje privátní IP adresu na veřejnou IP adresu. Po vytvoření tohoto mapování může vrátit provoz pro tento odchozí tok, který pochází, také na privátní IP adresu, ze které pochází.

Azure k provedení této funkce používá překlad zdrojové síťové adresy (SNAT). Pokud se za jednu veřejnou IP adresu maskuje více privátních IP adres, Azure použije [překlad adresy portu (PAT)](#pat) k maskování privátních IP adres. Dočasné porty se používají pro PAT a jsou [předčasovanéumístěny](#preallocatedports) na základě velikosti fondu.

Existuje více [odchozích scénářů](#scenarios). Tyto scénáře můžete kombinovat podle potřeby. Pečlivě si je prohlédněte, abyste pochopili možnosti, omezení a vzory, které se vztahují k modelu nasazení a scénáři aplikace. Projděte si pokyny pro [správu těchto scénářů](#snatexhaust).

>[!IMPORTANT] 
>Standardní vyrovnávání zatížení a standardní veřejné IP zavést nové schopnosti a různé chování odchozí připojení.  Nejsou stejné jako základní sku.  Pokud chcete odchozí připojení při práci se standardními sku, musíte explicitně definovat buď se standardními veřejnými IP adresami nebo standardním veřejným vyvyčažantem zatížení.  To zahrnuje vytváření odchozí připojení při použití interní standardní vyrovnávání zatížení.  Doporučujeme vždy používat odchozí pravidla na standardní veřejné vyrovnávání zatížení.  [Scénář 3](#defaultsnat) není k dispozici se standardní skladovou položkou.  To znamená, že při použití interního standardního vyvažovače zatížení je třeba podniknout kroky k vytvoření odchozího připojení pro virtuální chod v back-endovém fondu, pokud je odchozí připojení žádoucí.  V kontextu odchozí připojení, jeden samostatný virtuální virtuální ms, všechny virtuálních zařízení v dostupnosti MNoži, všechny instance v VMSS chovat jako skupina. To znamená, že pokud je jeden virtuální virtuální podnik v sadě dostupnosti přidružený ke standardní skladové jednotce, všechny instance virtuálních stránek v rámci této sady dostupnosti se nyní chovají podle stejných pravidel, jako kdyby byly přidruženy ke standardní skladové jednotce, i když s ní není přímo přidružena jednotlivá instance. Toto chování je také pozorováno v případě samostatného virtuálního počítače s více kartami síťového rozhraní připojenými k systému vyrovnávání zatížení. Pokud jedna nic je přidán jako samostatný, bude mít stejné chování. Pečlivě zkontrolujte celý tento dokument, abyste porozuměli celkovým konceptům, prostudovali [standardní vyvažovače zatížení,](load-balancer-standard-overview.md) kde najdou rozdíly mezi sku, a zkontrolujte [odchozí pravidla](load-balancer-outbound-rules-overview.md).  Použití odchozích pravidel umožňuje jemně odstupňovanou kontrolu nad všemi aspekty odchozího připojení.

## <a name="scenario-overview"></a><a name="scenarios"></a>Přehled scénáře

Azure Load Balancer a související prostředky jsou explicitně definované, když používáte [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Azure aktuálně poskytuje tři různé metody k dosažení odchozí připojení pro prostředky Azure Resource Manager. 

| SKU | Scénář | Metoda | Protokoly IP | Popis |
| --- | --- | --- | --- | --- |
| Standardní, Základní | [1. Virtuální virtuální ms s veřejnou IP adresou na úrovni instance (s vyrovnáváním zatížení nebo bez něj)](#ilpip) | SNAT, port maskování nepoužívá | TCP, UDP, ICMP, ESP | Azure používá veřejnou IP adresu přiřazenou konfiguraci IP adresy nic instance. Instance má všechny dočasné porty k dispozici. Při použití standardního nástroje pro vyrovnávání zatížení nejsou [odchozí pravidla](load-balancer-outbound-rules-overview.md) podporována, pokud je virtuálnímu počítači přiřazena veřejná IP adresa. |
| Standardní, Základní | [2. Veřejný vyrovnávání zatížení přidružený k virtuálnímu počítači (žádná veřejná IP adresa v instanci)](#lb) | SNAT s maskováním portů (PAT) pomocí frontendů vykladače vyrovnávání zatížení | TCP, UDP |Azure sdílí veřejnou IP adresu veřejných frontendů vykladače vyrovnávání zatížení s více privátními IP adresami. Azure používá dočasné porty front-endů na PAT. [Odchozí pravidla](load-balancer-outbound-rules-overview.md) byste měli použít k explicitnímu definování odchozího připojení. |
| žádné nebo Základní | [3. Samostatný virtuální virtuální ms (bez vytápětku zatížení, bez veřejné IP adresy)](#defaultsnat) | SNAT s maskováním portů (PAT) | TCP, UDP | Azure automaticky označí veřejnou IP adresu pro SNAT, sdílí tuto veřejnou IP adresu s více privátními IP adresami skupiny dostupnosti a používá dočasné porty této veřejné IP adresy. Tento scénář je záložní pro předchozí scénáře. Nedoporučujeme to, pokud potřebujete viditelnost a kontrolu. |

Pokud nechcete, aby virtuální počítač komunikoval s koncovými body mimo Azure ve veřejném IP adresním prostoru, můžete použít skupiny zabezpečení sítě (NSG) k zablokování přístupu podle potřeby. Část [Zabránění odchozímu připojení](#preventoutbound) podrobněji popisuje soubory nsg. Pokyny pro navrhování, implementaci a správu virtuální sítě bez odchozího přístupu je mimo rozsah tohoto článku.

### <a name="scenario-1-vm-with-public-ip-address"></a><a name="ilpip"></a>Scénář 1: Virtuální počítače s veřejnou IP adresou

V tomto scénáři má virtuální ho virtuálního počítače je přiřazena veřejná IP adresa. Pokud jde o odchozí připojení, nezáleží na tom, jestli je virtuální modul vyrovnáváný nebo ne. Tento scénář má přednost před ostatními. Při použití veřejné IP adresy virtuální ho virtuálního počítačů používá veřejnou IP adresu pro všechny odchozí toky.  

Veřejná IP adresa přiřazená virtuálnímu počítačůmu je vztah 1:1 (místo 1: mnoho) a implementovaná jako BEZstavový NAT 1:1.  Maskování portů (PAT) se nepoužívá a virtuální modul má všechny dočasné porty, které jsou k dispozici pro použití.

Pokud vaše aplikace iniciuje mnoho odchozích toků a dochází k vyčerpání portu SNAT, zvažte přiřazení [veřejné IP adresy ke zmírnění omezení SNAT](#assignilpip). Recenze [Správa Vyčerpání SNAT](#snatexhaust) v plném rozsahu.

### <a name="scenario-2-load-balanced-vm-without-a-public-ip-address"></a><a name="lb"></a>Scénář 2: Virtuální počítače s vyrovnáváním zatížení bez veřejné IP adresy

V tomto scénáři je virtuální počítače součástí veřejného back-endového fondu vyrovnávání zatížení. Virtuální ho virtuálního počítačům není přiřazena veřejná IP adresa. Prostředek vyrovnávání zatížení musí být nakonfigurován s pravidlem vyrovnávání zatížení, aby bylo možné vytvořit propojení mezi veřejným front-endem IP s back-endovým fondem.

Pokud tuto konfiguraci pravidla nedokončíte, bude chování popsané ve scénáři samostatného [virtuálního počítače bez veřejné IP adresy](#defaultsnat). Není nutné pro pravidlo mít pracovní naslouchací proces v back-endovém fondu pro sondu stavu úspěšné.

Když virtuální počítač s vyrovnáváním zatížení vytvoří odchozí tok, Azure převede privátní zdrojovou IP adresu odchozího toku na veřejnou IP adresu veřejného frontendu vykladače vyrovnávání zatížení. Azure k provedení této funkce používá SNAT. Azure také používá [PAT](#pat) k maskovat více privátních IP adres za veřejnou IP adresu. 

Dočasné porty front-endu veřejné IP adresy vykladače vyrovnávání zatížení se používají k rozlišení jednotlivých toků pocházejících z virtuálního počítačů. SNAT dynamicky používá [předčasově přidělené dočasné porty](#preallocatedports) při vytváření odchozích toků. V tomto kontextu dočasné porty používané pro SNAT se nazývají porty SNAT.

Porty SNAT jsou předem přiděleny, jak je popsáno v části [Porozumění SNAT a PAT.](#snat) Je to konečný zdroj, který může být vyčerpán. Je důležité pochopit, jak jsou [konzumovány](#pat). Chcete-li pochopit, jak navrhnout pro tuto spotřebu a zmírnit podle potřeby, [přečtěte si řízení vyčerpání SNAT](#snatexhaust).

Pokud [je k vykladaču zatížení Basic přidruženo více veřejných IP adres](load-balancer-multivip-overview.md), je kterákoli z těchto veřejných IP adres kandidátem pro odchozí toky a jedna je vybrána náhodně.  

Chcete-li sledovat stav odchozích připojení pomocí služby Load Balancer Basic, můžete použít [protokoly Azure Monitor pro vyrovnávání zatížení](load-balancer-monitor-log.md) a [protokoly událostí výstrah](load-balancer-monitor-log.md#alert-event-log) ke sledování zpráv vyčerpání portů SNAT.

### <a name="scenario-3-standalone-vm-without-a-public-ip-address"></a><a name="defaultsnat"></a>Scénář 3: Samostatný virtuální počítače bez veřejné IP adresy

V tomto scénáři není virtuální počítače součástí veřejného fondu vyrovnávání zatížení (a není součástí interního fondu standardního vyrovnávání zatížení) a nemá přiřazenou veřejnou IP adresu. Když virtuální počítač vytvoří odchozí tok, Azure převede privátní zdrojovou IP adresu odchozího toku na ip adresu veřejného zdroje. Veřejná IP adresa použitá pro tento odchozí tok není konfigurovatelná a nezapočítává se do limitu veřejných prostředků IP předplatného. Tato veřejná IP adresa vám nepatří a nelze ji rezervovat. Pokud znovu nasadíte virtuální počítač nebo sadu dostupnosti nebo škálovací sadu virtuálních počítačů, bude tato veřejná IP adresa uvolněna a bude požadována nová veřejná IP adresa. Nepoužívejte tento scénář pro seznam adres IP seznamu povolených. Místo toho použijte jeden z dalších dvou scénářů, kde explicitně deklarujete odchozí scénář a veřejnou IP adresu, která se má použít pro odchozí připojení.

>[!IMPORTANT] 
>Tento scénář platí také v případě, že je připojen __pouze__ interní základní vyrovnávání zatížení. Scénář 3 __není k dispozici,__ pokud je k virtuálnímu počítače připojen interní standardní vyrovnávání zatížení.  Kromě použití interního standardního vytápětku zatížení je nutné explicitně vytvořit [scénář 1](#ilpip) nebo [scénář 2.](#lb)

Azure k provedení této funkce používá SNAT s maskováním portů[(PAT).](#pat) Tento scénář je podobný [scénář2](#lb), s tím rozdílem, že neexistuje žádná kontrola nad IP adresu použitou. Toto je záložní scénář, kdy scénáře 1 a 2 neexistují. Tento scénář nedoporučujeme, pokud chcete mít kontrolu nad odchozí adresou. Pokud odchozí připojení jsou důležitou součástí aplikace, měli byste zvolit jiný scénář.

Porty SNAT jsou předevětřeny, jak je popsáno v části [Porozumění SNAT a PAT.](#snat)  Počet virtuálních disponibilních virtuálních aplikací sdílejících sadu dostupnosti určuje, která úroveň předběžného přidělení se použije.  Samostatný virtuální modul bez sady dostupnosti je efektivně fond 1 pro účely určení předběžného přidělení (1024 portů SNAT). Porty SNAT jsou omezeným zdrojem, který lze vyčerpat. Je důležité pochopit, jak jsou [konzumovány](#pat). Chcete-li pochopit, jak navrhnout pro tuto spotřebu a zmírnit podle potřeby, [přečtěte si řízení vyčerpání SNAT](#snatexhaust).

### <a name="multiple-combined-scenarios"></a><a name="combinations"></a>Více kombinovaných scénářů

Můžete kombinovat scénáře popsané v předchozích částech k dosažení konkrétního výsledku. Pokud je k dispozici více scénářů, použije se pořadí priorit: [scénář 1](#ilpip) má přednost před [scénářem 2](#lb) a [3](#defaultsnat). [Scénář 2](#lb) přepíše [scénář 3](#defaultsnat).

Příkladem je nasazení Azure Resource Manager, kde aplikace do značné míry spoléhá na odchozí připojení k omezenému počtu cílů, ale také přijímá příchozí toky přes front-end nástroje pro vyrovnávání zatížení. V tomto případě můžete kombinovat scénáře 1 a 2 pro úlevu. Další vzorce nazuje [správa vyčerpání SNAT](#snatexhaust).

### <a name="multiple-frontends-for-outbound-flows"></a><a name="multife"></a>Více front-endů pro odchozí toky

#### <a name="standard-load-balancer"></a>Standardní balancer zatížení

Standardní vyrovnávání zatížení používá všechny kandidáty pro odchozí toky ve stejnou dobu, kdy je k dispozici [více (veřejných) front-endů IP.](load-balancer-multivip-overview.md) Každý front-end vynásobí počet dostupných předpravých portů SNAT, pokud je pro odchozí připojení povoleno pravidlo vyrovnávání zatížení.

Můžete se rozhodnout potlačit použití front-endové ip adresy pro odchozí připojení s novou možností pravidla vyrovnávání zatížení:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Za normálních `disableOutboundSnat` okolností možnost výchozí _false_ a znamená, že toto pravidlo programy odchozí SNAT pro přidružené virtuální chodů v back-endfondu pravidla vyrovnávání zatížení. To `disableOutboundSnat` lze změnit na _true_ zabránit vyrovnávání zatížení z použití přidružené front-endip adresu pro odchozí připojení pro virtuální chody v back-endfondu tohoto pravidla vyrovnávání zatížení.  A můžete také stále určit konkrétní IP adresu pro odchozí toky, jak je popsáno v [více, kombinované scénáře](#combinations) také.

#### <a name="load-balancer-basic"></a>Základní systém vyrovnávání zatížení

Vyrovnávání zatížení Basic zvolí jeden front-end, který se použije pro odchozí toky, když [více (veřejné) IP front-endy](load-balancer-multivip-overview.md) jsou kandidáty pro odchozí toky. Tento výběr nelze konfigurovat a algoritmus výběru byste měli považovat za náhodný. Můžete určit konkrétní IP adresu pro odchozí toky, jak je popsáno v [více, kombinované scénáře](#combinations).

### <a name="availability-zones"></a><a name="az"></a>Zóny dostupnosti

Při použití [standardního vyvažovače zatížení se zónami dostupnosti](load-balancer-standard-availability-zones.md)může front-endy redundantní zóny poskytovat zónově redundantní odchozí připojení SNAT a programování SNAT přežije selhání zóny.  Při použití zónových frontendů sdílejí odchozí připojení SNAT osud se zónou, do které patří.

## <a name="understanding-snat-and-pat"></a><a name="snat"></a>Pochopení SNAT a PAT

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>Port maskování SNAT (PAT)

Když je veřejný prostředek vyrovnávání zatížení přidružený k instancí virtuálních připojení, každý zdroj odchozího připojení se přepíše. Zdroj je přepsán z privátního ip adresního prostoru virtuální sítě na front-endovou veřejnou IP adresu nástroje pro vyrovnávání zatížení. V adresním prostoru veřejné IP adresy musí být 5 n-tice toku (zdrojová IP adresa, zdrojový port, přenosový protokol IP, cílová IP adresa, cílový port) jedinečný.  Port mašlování SNAT lze použít s protokoly IP TCP nebo UDP.

Dočasné porty (porty SNAT) se používají k dosažení tohoto cíle po přepsání privátní zdrojové IP adresy, protože více toků pochází z jedné veřejné IP adresy. Port maskování SNAT algoritmus přiděluje porty SNAT odlišně pro UDP versus TCP.

#### <a name="tcp-snat-ports"></a><a name="tcp"></a>Porty SNAT protokolu TCP

Jeden port SNAT je spotřebován na tok na jednu cílovou IP adresu, port. Pro více toků TCP na stejnou cílovou adresu IP, port a protokol spotřebovává každý tok Protokolu TCP jeden port SNAT. Tím je zajištěno, že toky jsou jedinečné, pokud pocházejí ze stejné veřejné IP adresy a přejdou na stejnou cílovou IP adresu, port a protokol. 

Více toků, každý do jiné cílové IP adresy, portu a protokolu, sdílí jeden port SNAT. Cílová adresa IP, port a protokol činí toky jedinečnými bez nutnosti dalších zdrojových portů k rozlišení toků ve veřejném adresním prostoru IP.

#### <a name="udp-snat-ports"></a><a name="udp"></a>Porty SNAT UDP

Porty SNAT Protokolu UDP jsou spravovány jiným algoritmem než porty TCP SNAT.  Vykladač zatížení používá algoritmus známý jako "port-omezený kužel NAT" pro UDP.  Jeden port SNAT je spotřebován pro každý tok, bez ohledu na cílovou IP adresu, port.

#### <a name="snat-port-reuse"></a>Opětovné použití portu SNAT

Po uvolnění portu je port k dispozici pro opakované použití podle potřeby.  Porty SNAT si můžete usuzovat jako sekvenci od nejnižší po nejvyšší dostupnou pro daný scénář a první dostupný port SNAT se používá pro nová připojení. 
 
#### <a name="exhaustion"></a>Vyčerpání

Když jsou vyčerpány prostředky portu SNAT, odchozí toky se nezdaří, dokud existující toky uvolnit porty SNAT. Balancer zatížení získá porty SNAT, když se tok zavře a použije [4minutový časový limit nečinnosti](#idletimeout) pro rekultivaci portů SNAT z nečinných toků.

Porty SNAT UDP se obvykle odvápou mnohem rychleji než porty TCP SNAT kvůli rozdílu v použitém algoritmu. Je nutné navrhnout a měřítko test s tímto rozdílem v mysli.

Pro vzory ke zmírnění podmínek, které obvykle vedou k vyčerpání portu SNAT, zkontrolujte [řídící oddíl SNAT.](#snatexhaust)

### <a name="ephemeral-port-preallocation-for-port-masquerading-snat-pat"></a><a name="preallocatedports"></a>Předběžné přidělení dočasných portů pro maskování portů SNAT (PAT)

Azure používá algoritmus k určení počtu předpravých portů SNAT, které jsou k dispozici na základě velikosti back-endového fondu při použití portu maskování SNAT ([PAT](#pat)). Porty SNAT jsou dočasné porty, které jsou k dispozici pro konkrétní veřejnou zdrojovou adresu IP.

Stejný počet portů SNAT jsou předběžně přiděleny pro UDP a TCP v uvedeném pořadí a spotřebovány nezávisle na přenosový protokol IP.  Využití portu SNAT se však liší v závislosti na tom, zda je tok UDP nebo TCP.

>[!IMPORTANT]
>Standardní programování SKU SNAT je na přenosový protokol IP a je odvozeno z pravidla vyrovnávání zatížení.  Pokud existuje pouze pravidlo vyrovnávání zatížení Protokolu TCP, je snat k dispozici pouze pro protokol TCP. Pokud máte pouze pravidlo vyrovnávání zatížení Protokolu TCP a potřebujete odchozí snat pro UDP, vytvořte pravidlo vyrovnávání zatížení UDP ze stejného front-endu do stejného back-endového fondu.  Tím se spustí programování SNAT pro UDP.  Pracovní pravidlo nebo zdravotní sonda není vyžadována.  Základní sku snat vždy programy SNAT pro oba ip transportní protokol, bez ohledu na přenosový protokol zadaný v pravidle vyrovnávání zatížení.

Azure předem přiděluje porty SNAT do konfigurace IP nenokové mikiny každého virtuálního počítače. Při přidání konfigurace IP do fondu jsou porty SNAT předskutečně přiděleny pro tuto konfiguraci IP na základě velikosti fondu back-end. Při vytvoření odchozích toků [pat](#pat) dynamicky spotřebovává (až do přednastavit limit) a uvolní tyto porty při zavření toku nebo [nečinnosti časové limity](#idletimeout) dojít.

V následující tabulce jsou uvedeny předběžné přidělení portů SNAT pro úrovně velikosti back-endového fondu:

| Velikost fondu (instance virtuálních her) | Předpočitatou porty SNAT na konfiguraci PROTOKOLU IP|
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

>[!NOTE]
> Při použití standardního vytálení zatížení s [více front-endy](load-balancer-multivip-overview.md)každá front-endová IP adresa vynásobí počet dostupných portů SNAT v předchozí tabulce. Například back-endový fond 50 virtuálních počítačů se 2 pravidly vyrovnávání zatížení, každý se samostatnou ip adresou front-endu, použije 2048 (2x 1024) Porty SNAT na konfiguraci IP. Viz podrobnosti pro [více front-endů](#multife).

Nezapomeňte, že počet portů SNAT k dispozici se nepřekládá přímo na počet toků. Jeden port SNAT lze znovu použít pro více jedinečných cílů. Porty jsou spotřebovány pouze v případě, že je nutné, aby toky jedinečné. Pokyny k návrhu a zmírnění naleznete v části o [tom, jak spravovat tento vyčerpatelný prostředek,](#snatexhaust) a v části, která popisuje [pat](#pat).

Změna velikosti back-endového fondu může ovlivnit některé zavedené toky. Pokud se velikost back-endového fondu zvětší a přejde do další úrovně, polovina předpravých portů SNAT se získá během přechodu na další větší úroveň fondu back-endu. Toky, které jsou přidruženy k regenerovanému portu SNAT, budou časový majek a musí být obnoveny. Pokud dojde k pokusu o nový tok, tok bude okamžitě úspěšné, dokud jsou k dispozici předpravý chod porty.

Pokud velikost back-endového fondu zmenší a přejde do nižší vrstvy, zvýší se počet dostupných portů SNAT. V tomto případě nejsou ovlivněny stávající přidělené porty SNAT a jejich příslušné toky.

Přidělení portů SNAT jsou specifická pro přenosový protokol PROTOKOLU IP (TCP a UDP jsou udržovány samostatně) a jsou uvolněny za následujících podmínek:

### <a name="tcp-snat-port-release"></a>Uvolnění portu TCP SNAT

- Pokud některý server/klient odešle FINACK, port SNAT bude uvolněn po 240 sekundách.
- Pokud rst je vidět, SNAT port bude uvolněna po 15 sekundách.
- Pokud byl dosažen časový limit nečinnosti, port je uvolněn.

### <a name="udp-snat-port-release"></a>Uvolnění portu SNAT UDP

- Pokud byl dosažen časový limit nečinnosti, port je uvolněn.

## <a name="problem-solving"></a><a name="problemsolving"></a>Řešení problémů 

Tato část je určena ke zmírnění vyčerpání SNAT a které mohou nastat s odchozí připojení v Azure.

### <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a>Správa vyčerpání portů SNAT (PAT)
[Dočasné porty](#preallocatedports) používané pro [PAT](#pat) jsou vyčerpatelný prostředek, jak je popsáno v [samostatném virtuálním virtuálním ms bez veřejné IP adresy](#defaultsnat) a [virtuálního virtuálního počítačů s vyrovnáváním zatížení bez veřejné IP adresy](#lb). Můžete sledovat vaše využití dočasných portů a porovnat s aktuální přidělení k určení rizika nebo k potvrzení snat exhuastion pomocí [této](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation) příručky.

Pokud víte, že iniciujete mnoho odchozích připojení TCP nebo UDP ke stejné cílové adrese IP a portu a zjistíte selhání odchozích připojení nebo jste upozorněni podporou, že vyčerpáváte porty SNAT (předčasované dočasné [porty](#preallocatedports) používané [patem](#pat)), máte několik obecných možností zmírnění rizik. Projděte si tyto možnosti a rozhodněte se, co je k dispozici a co je nejlepší pro váš scénář. Je možné, že jeden nebo více může pomoci spravovat tento scénář.

Pokud máte potíže s pochopením chování odchozího připojení, můžete použít statistiku zásobníku IP (netstat). Nebo může být užitečné sledovat chování připojení pomocí zachycení paketů. Tato zachycení paketů můžete provádět v hostovaném osu vaší instance nebo použít [sledovací proces sítě pro sběr paketů](../network-watcher/network-watcher-packet-capture-manage-portal.md). 

#### <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>Úprava aplikace pro opakované použití připojení 
Můžete snížit poptávku po dočasných portech, které se používají pro SNAT pomocí opětovného použití připojení ve vaší aplikaci. To platí zejména pro protokoly, jako je HTTP/1.1, kde opětovné použití připojení je výchozí. A další protokoly, které používají http jako jejich přenos (například REST) může mít prospěch zase. 

Opakované použití je vždy lepší než jednotlivá atomická připojení TCP pro každý požadavek. Opakované použití má za následek výkonnější a efektivnější transakce TCP.

#### <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>Úprava aplikace tak, aby používala sdružování připojení
Můžete použít schéma sdružování připojení ve vaší aplikaci, kde jsou požadavky interně distribuovány přes pevnou sadu připojení (každé opětovné použití, kde je to možné). Toto schéma omezuje počet dočasných portů, které se používají, a vytváří předvídatelnější prostředí. Toto schéma může také zvýšit propustnost požadavků povolením více souběžných operací, když jedno připojení blokuje odpověď na operaci.  

Sdružování připojení již může existovat v rámci, který používáte k vývoji aplikace nebo nastavení konfigurace pro vaši aplikaci. Sdružování připojení můžete kombinovat s opětovným použitím připojení. Vaše více požadavků pak spotřebují pevný, předvídatelný počet portů na stejnou cílovou IP adresu a port. Požadavky také těžit z efektivní využití transakcí TCP snížení latence a využití prostředků. Transakce UDP mohou být také přínosné, protože správa počtu toků UDP může zase zabránit podmínkám výfukových plynů a spravovat využití portu SNAT.

#### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>Upravte aplikaci tak, aby používala méně agresivní logiku opakování
Při [předčasově přidělené dočasné porty](#preallocatedports) používané pro [PAT](#pat) jsou vyčerpány nebo dojde k selhání aplikace, agresivní nebo hrubou silou opakování bez rozpadu a backoff logiky způsobit vyčerpání dojít nebo přetrvávají. Můžete snížit poptávku po dočasných portech pomocí méně agresivní logiky opakování. 

Dočasné porty mají 4minutový časový limit nečinnosti (není nastavitelný). Pokud jsou opakování příliš agresivní, vyčerpání nemá možnost uklidit sám. Proto vzhledem k tomu, jak a jak často - aplikace opakování transakcí je důležitou součástí návrhu.

#### <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Přiřazení veřejné IP adresy ke každému virtuálnímu virtuálnímu počítačům
Přiřazení veřejné IP adresy změní váš scénář na [veřejnou IP adresu na virtuální počítač](#ilpip). Všechny dočasné porty veřejné IP adresy, které se používají pro každý virtuální modul, jsou k dispozici pro virtuální hod. (Na rozdíl od scénářů, kde jsou dočasné porty veřejné IP adresy sdíleny se všemi virtuálními počítačůmi přidruženými k příslušnému back-endovému fondu.) Existují kompromisy, které je třeba zvážit, jako jsou dodatečné náklady na veřejné IP adresy a potenciální dopad zařazení velkého počtu jednotlivých IP adres na seznam.

>[!NOTE] 
>Tato možnost není k dispozici pro role webových pracovních dělníků.

#### <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Použití více front-endů

Při použití veřejného standardního vyvažovače zatížení přiřadíte [více front-endových IP adres pro odchozí připojení](#multife) a [vynásobíte počet dostupných portů SNAT](#preallocatedports).  Vytvořte front-endovou konfiguraci IP adresy, pravidlo a back-endový fond, který aktivuje programování SNAT na veřejnou IP adresu front-endu.  Pravidlo nemusí fungovat a sonda stavu nemusí být úspěšná.  Pokud používáte více front-endů pro příchozí také (nikoli pouze pro odchozí), měli byste použít vlastní sondy stavu dobře zajistit spolehlivost.

>[!NOTE]
>Ve většině případů je vyčerpání portů SNAT známkou špatného designu.  Ujistěte se, že jste pochopili, proč jste vyčerpávající porty před použitím více front-endů přidat Porty SNAT.  Možná maskujete problém, který může později vést k selhání.

#### <a name="scale-out"></a><a name="scaleout"></a>Horizontální navýšení kapacity

[Předreallocated porty](#preallocatedports) jsou přiřazeny na základě velikosti fondu back-endu a seskupeny do vrstev, aby se minimalizovalo narušení, když některé porty musí být přerozděleny tak, aby vyhovovaly další větší velikosti fondu back-endu.  Můžete mít možnost zvýšit intenzitu využití portu SNAT pro daný front-end škálováním back-endového fondu na maximální velikost pro danou úroveň.  To vyžaduje, aby aplikace efektivně škálovat.

Například dva virtuální počítače v back-endovém fondu by měly 1024 portů SNAT dostupných pro konfiguraci IP, což umožňuje celkem 2048 portů SNAT pro nasazení.  Pokud by se nasazení zvýšilo na 50 virtuálních počítačů, i když počet předpravých portů zůstává konstantní na virtuální počítač, lze nasazení použít celkem 51 200 (50 x 1024) portů SNAT.  Pokud chcete horizontální navýšení kapacity vašeho nasazení, zkontrolujte počet [předpravých portů](#preallocatedports) na úroveň a ujistěte se, že tvar škálování na maximum pro příslušnou úroveň.  V předchozím příkladu, pokud jste zvolili horizontální navýšení kapacity na 51 namísto 50 instancí, by přecházení do další vrstvy a skončit s menším počtem portů SNAT na virtuální hod, stejně jako celkem.

Pokud horizontální navýšení kapacity na další větší úroveň velikosti fondu back-endu, je potenciál pro některé odchozí připojení k časový limit, pokud přidělené porty mají být přerozděleny.  Pokud používáte pouze některé porty SNAT, horizontální navýšení kapacity přes další větší velikost back-endového fondu je bezvýznamné.  Polovina existujících portů bude přerozdělena při každém přesunu na další úroveň fondu back-endu.  Pokud nechcete, aby k tomu došlo, musíte utvářet nasazení na velikost vrstvy.  Nebo se ujistěte, že vaše aplikace můžete zjistit a opakovat podle potřeby.  Tcp keepalives může pomoci při zjišťování, kdy porty SNAT již nefungují z důvodu přerozdělení.

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>Obnovení časového limitu odchozího nečinnosti pomocí udržovatých životů

Odchozí připojení mají časový limit nečinnosti 4 minuty. Tento časový čas je nastavitelný pomocí [odchozích pravidel](../load-balancer/load-balancer-outbound-rules-overview.md#idletimeout). Můžete také použít přenos (například TCP keepalives) nebo udržovat y vrstvy aplikace k aktualizaci toku nečinnosti a v případě potřeby obnovit tento časový limit nečinnosti.  

Při použití TCP keepalives, stačí povolit na jedné straně připojení. Například stačí povolit jim na straně serveru pouze obnovit časovač nečinnosti toku a není nutné pro obě strany zahájit tcp keepalives.  Podobné koncepty existují pro aplikační vrstvu, včetně konfigurace klienta databáze.  Zkontrolujte na straně serveru, jaké možnosti existují pro údržbu specifických pro aplikaci.

## <a name="discovering-the-public-ip-that-a-vm-uses"></a><a name="discoveroutbound"></a>Zjišťování veřejné IP adresy, kterou virtuální hotel používá
Existuje mnoho způsobů, jak určit veřejnou zdrojovou IP adresu odchozího připojení. OpenDNS poskytuje službu, která vám může zobrazit veřejnou IP adresu vašeho virtuálního počítače. 

Pomocí příkazu nslookup můžete odeslat dotaz DNS na název myip.opendns.com překládání OpenDNS. Služba vrátí zdrojovou ADRESU IP, která byla použita k odeslání dotazu. Při spuštění následujícího dotazu z virtuálního počítače je odpovědí veřejná IP adresa používaná pro tento virtuální počítač:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Zabránění odchozímu připojení
Někdy je nežádoucí pro virtuální hod u chtít vytvořit odchozí tok. Nebo může být požadavek na správu cílů, které lze dosáhnout s odchozími toky nebo které cíle mohou zahájit příchozí toky. V takovém případě můžete pomocí [skupin zabezpečení sítě](../virtual-network/security-overview.md) spravovat cíle, které virtuální hod může dosáhnout. K řízení veřejných cílů určení můžete také použít soubory nsg, které mohou iniciovat příchozí toky.

Při použití skupiny zabezpečení zabezpečení na virtuální ms s vyrovnáváním zatížení, dávat pozor na [značky služby](../virtual-network/security-overview.md#service-tags) a [výchozí pravidla zabezpečení](../virtual-network/security-overview.md#default-security-rules). Musíte zajistit, že virtuální počítač může přijímat požadavky na sondu stavu z Azure Load Balancer. 

Pokud skupina zabezpečení blokuje požadavky na sondu stavu z výchozí značky AZURE_LOADBALANCER, sonda stavu virtuálního počítače se nezdaří a virtuální počítač je označen. Balancer zatížení zastaví odesílání nových toků do tohoto virtuálního účtu.

## <a name="limitations"></a>Omezení
- Role webových pracovních dělníků bez virtuální sítě a dalších služeb platformy Microsoftu můžou být přístupné, když se z důvodu vedlejšího účinku funkce služeb před virtuální sítí a dalších služeb platformy před virtuální sítí používá jenom interní standardní vyrovnávání zatížení. Nespoléhejte se na tento vedlejší účinek, protože příslušná služba sama nebo podkladová platforma se může změnit bez předchozího upozornění. Vždy musíte předpokládat, že je třeba vytvořit odchozí připojení explicitně v případě potřeby při použití pouze interní standardní vyrovnávání zatížení. Výchozí scénář [SNAT](#defaultsnat) 3 popsaný v tomto článku není k dispozici.

## <a name="next-steps"></a>Další kroky

- Další informace o [standardním vyvykladaču zatížení](load-balancer-standard-overview.md).
- Přečtěte si další informace o [odchozích pravidlech](load-balancer-outbound-rules-overview.md) pro standardní veřejný vyvažovač zatížení.
- Další informace o [vykladaču zatížení](load-balancer-overview.md).
- Další informace o [skupinách zabezpečení sítě](../virtual-network/security-overview.md).
- Přečtěte si o některých dalších klíčových [síťových funkcích](../networking/networking-overview.md) v Azure.
