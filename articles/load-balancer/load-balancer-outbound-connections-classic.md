---
title: Odchozí připojení v Azure (Classic)
titlesuffix: Azure Load Balancer
description: Tento článek vysvětluje, jak Azure umožňuje cloudovým službám komunikovat s veřejnými službami sítě Internet.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2018
ms.author: allensu
ms.openlocfilehash: 10af3b4838aae1565bac1d996997c117a74cedbc
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274672"
---
# <a name="outbound-connections-classic"></a>Odchozí připojení (klasická)

Azure poskytuje odchozí konektivitu pro zákaznická nasazení prostřednictvím několika různých mechanismů. Tento článek popisuje, co jsou scénáře, kdy se používají, jak fungují a jak je spravovat.

>[!NOTE]
>Tento článek se zabývá jenom klasickými nasazeními.  Projděte si [odchozí připojení](load-balancer-outbound-connections.md) pro všechny scénáře nasazení Správce prostředků v Azure.

Nasazení v Azure může komunikovat s koncovými body mimo Azure ve veřejném adresním prostoru IP adres. Když instance inicializuje odchozí tok do cílového umístění ve veřejném adresním prostoru IP adres, Azure dynamicky mapuje privátní IP adresu na veřejnou IP adresu. Po vytvoření tohoto mapování se může vrátit přenos pro tento odchozí výstupní tok a získat tak privátní IP adresu, ve které tok vznikl.

Azure používá k provedení této funkce překlad zdrojového síťového adres (SNAT). Při maskování více privátních IP adres za jednou veřejnou IP adresou využívá Azure k maskování privátních IP adres [port (Pat) adresování](#pat) . Dočasné porty se používají pro PAT a jsou vyčerpány na základě velikosti fondu. [](#preallocatedports)

Existuje několik [odchozích scénářů](#scenarios). Tyto scénáře můžete v případě potřeby kombinovat. Pečlivě si přečtěte informace o možnostech, omezeních a vzorech, které se vztahují na váš model nasazení a scénář aplikace. Přečtěte si pokyny pro [správu těchto scénářů](#snatexhaust).

## <a name="scenarios"></a>Přehled scénáře

Azure poskytuje tři různé metody pro zajištění klasických nasazení pro odchozí připojení.  Ne všechna klasická nasazení mají k dispozici všechny tři scénáře:

| Scénář | Metoda | Protokoly IP | Popis | Role webového pracovního procesu | IaaS | 
| --- | --- | --- | --- | --- | --- |
| [1. Virtuální počítač s veřejnou IP adresou na úrovni instance](#ilpip) | SNAT, maskování portů se nepoužívá. | TCP, UDP, ICMP, ESP | Azure používá virtuální počítač přiřazený k veřejné IP adrese. Instance má k dispozici všechny dočasné porty. | Ne | Ano |
| [2. veřejný koncový bod s vyrovnáváním zatížení](#publiclbendpoint) | SNAT s maskou portů (PAT) k veřejnému koncovému bodu | TCP, UDP | Azure sdílí veřejný koncový bod veřejné IP adresy s více privátními koncovými body. Azure používá dočasné porty veřejného koncového bodu pro PAT. | Ano | Ano |
| [3. Samostatný virtuální počítač](#defaultsnat) | SNAT s maskou portů (PAT) | TCP, UDP | Azure automaticky určí veřejnou IP adresu pro SNAT, sdílí tuto veřejnou IP adresu s celým nasazením a používá dočasné porty IP adresy veřejného koncového bodu pro PAT. Toto je nouzový scénář pro předchozí scénáře. Nedoporučujeme ho, pokud potřebujete viditelnost a kontrolu. | Ano | Ano |

Toto je podmnožina funkcí odchozího připojení, která je dostupná pro Správce prostředků nasazení v Azure.  

Různá nasazení v klasickém prostředí mají různé funkce:

| Klasické nasazení | Dostupné funkce | 
| --- | --- |
| Virtuální počítač | scénář [1](#ilpip), [2](#publiclbendpoint)nebo [3](#defaultsnat) |
| Role webového pracovního procesu | pouze scénář [2](#publiclbendpoint), [3](#defaultsnat) | 

[Strategie zmírnění rizik](#snatexhaust) mají také stejné rozdíly.

Algoritmus používaný k předrozdělování dočasných portů pro PAT pro nasazení Classic je stejný jako u Azure Resource Managerch nasazení prostředků.

### <a name="ilpip"></a>Scénář 1: Virtuální počítač s veřejnou IP adresou na úrovni instance

V tomto scénáři má virtuální počítač přiřazenou veřejnou IP adresu na úrovni instance (ILPIP). Vzhledem k tomu, že se jedná o odchozí připojení, nezáleží na tom, jestli má virtuální počítač koncový bod s vyrovnáváním zatížení. Tento scénář má přednost před ostatními. Když se použije ILPIP, virtuální počítač použije ILPIP pro všechny odchozí toky.  

Veřejná IP adresa přiřazená k virtuálnímu počítači je vztah 1:1 (nikoli 1: mnoho) a implementovaný jako bezstavové 1:1 NAT.  Maskování portů (PAT) se nepoužívá a virtuální počítač má k dispozici všechny dočasné porty, které je možné použít.

Pokud vaše aplikace zahájí mnoho odchozích toků a dojde k vyčerpání portů SNAT, zvažte přiřazení [ILPIP pro zmírnění omezení SNAT](#assignilpip). Přečtěte si celou [správu vyčerpání SNAT](#snatexhaust) .

### <a name="publiclbendpoint"></a>Scénář 2: Veřejný koncový bod s vyrovnáváním zatížení

V tomto scénáři je virtuální počítač nebo role webového pracovního procesu přidružena k veřejné IP adrese prostřednictvím koncového bodu s vyrovnáváním zatížení. K virtuálnímu počítači není přiřazená žádná veřejná IP adresa. 

Když virtuální počítač s vyrovnáváním zatížení vytvoří odchozí tok, Azure převede privátní zdrojovou IP adresu odchozího toku na veřejnou IP adresu veřejného koncového bodu s vyrovnáváním zatížení. Azure používá k provedení této funkce SNAT. Azure také používá [Pat](#pat) k maskování více privátních IP adres za veřejnou IP adresou. 

Dočasné porty front-endu veřejné IP adresy nástroje pro vyrovnávání zatížení se používají k odlišení jednotlivých toků, které pocházejí z virtuálního počítače. SNAT dynamicky používá [předpřidělené dočasné porty](#preallocatedports) při vytváření odchozích toků. V tomto kontextu se dočasné porty používané pro SNAT nazývají porty SNAT.

Porty SNAT jsou předpřidělené, jak je popsáno v části [Princip SNAT a Pat](#snat) . Jsou to konečný prostředek, který je možné vyčerpat. Je důležité pochopit, jak se [spotřebovávají](#pat). Informace o tom, jak navrhnout tuto spotřebu a zmírnit podle potřeby, najdete v tématu [Správa vyčerpání SNAT](#snatexhaust).

Pokud existuje [více veřejných koncových bodů](load-balancer-multivip.md) s vyrovnáváním zatížení, každá z těchto veřejných IP adres je kandidátem pro odchozí toky a jedna je vybrána náhodně.  

### <a name="defaultsnat"></a>Scénář 3: Není přidružená žádná veřejná IP adresa.

V tomto scénáři není virtuální počítač nebo ROle webového pracovního procesu součástí veřejného koncového bodu s vyrovnáváním zatížení.  A v případě virtuálního počítače k němu není přiřazená ILPIP adresa. Když virtuální počítač vytvoří odchozí tok, Azure převede IP adresu privátního zdroje odchozího toku do veřejné zdrojové IP adresy. Veřejnou IP adresu, která se používá pro tento výstupní tok, nejde konfigurovat a nepočítá se s limitem veřejných IP adres předplatného.  Azure tuto adresu automaticky přidělí.

Azure používá k provedení této funkce SNAT s maskou portů ([Pat](#pat)). Tento scénář je podobný scénáři 2, s výjimkou toho, že se používá žádná kontrola nad použitou IP adresou. Toto je nouzový scénář pro případy, kdy scénáře 1 a 2 neexistují. Tento scénář nedoporučujeme, pokud chcete mít kontrolu nad odchozí adresou. Pokud jsou odchozí připojení důležitou součástí vaší aplikace, měli byste zvolit jiný scénář.

Porty SNAT jsou předpřidělené, jak je popsáno v části [Princip SNAT a Pat](#snat) .  Počet virtuálních počítačů nebo rolí webového pracovního procesu, které sdílí veřejnou IP adresu, určuje počet předpřidělených dočasných portů.   Je důležité pochopit, jak se [spotřebovávají](#pat). Informace o tom, jak navrhnout tuto spotřebu a zmírnit podle potřeby, najdete v tématu [Správa vyčerpání SNAT](#snatexhaust).

## <a name="snat"></a>Porozumění SNAT a PAT

### <a name="pat"></a>Maskování portů SNAT (PAT)

Když nasazení provede odchozí připojení, přepíše se každý zdroj odchozího připojení. Zdroj se přepíše z privátního adresního prostoru IP adres na veřejnou IP adresu přidruženou k nasazení (na základě výše popsaných scénářů). Ve veřejném adresním prostoru IP adres musí být pět záznamů toku (zdrojová IP adresa, zdrojový port, transportní protokol IP, cílová IP adresa, cílový port) jedinečný.  

Dočasné porty (porty SNAT) se používají k dosažení tohoto toho po přepsání privátní zdrojové IP adresy, protože více toků pochází z jedné veřejné IP adresy. 

Jeden port SNAT se spotřebovává na jeden tok na jednu cílovou IP adresu, port a protokol. U více toků na stejnou cílovou IP adresu, port a protokol využívá každý tok jeden port SNAT. Tím je zajištěno, že toky budou jedinečné, když pocházejí ze stejné veřejné IP adresy a jdou na stejnou cílovou IP adresu, port a protokol. 

Několik toků, z nichž každý má jinou cílovou IP adresu, port a protokol, sdílí jeden port SNAT. Cílová IP adresa, port a protokol vytvářejí toky jedinečné bez nutnosti dalších zdrojových portů, které by rozlišily toky ve veřejném adresním prostoru IP adres.

Po vyčerpání prostředků portů SNAT dojde k selhání odchozích toků, dokud nebudou stávající toky vydány porty SNAT. Load Balancer redeklaracích portů SNAT po ukončení toku a používá prodlevu nečinnosti ve [4 minutách](#idletimeout) pro uvolnění portů SNAT z nečinných toků.

V případě vzorů pro zmírnění podmínek, které běžně vedou k vyčerpání portů SNAT, si přečtěte část [Správa SNAT](#snatexhaust) .

### <a name="preallocatedports"></a>Předběžné přidělení portu SNAT pro maskování portů SNAT (PAT)

Azure používá algoritmus k určení počtu předem přidělených portů SNAT na základě velikosti back-endu při použití maskování portů SNAT ([Pat](#pat)). Porty SNAT jsou dočasné porty, které jsou k dispozici pro konkrétní zdrojovou adresu veřejné IP adresy.

Azure při nasazení instance předem přidělí porty SNAT v závislosti na tom, kolik instancí virtuálních počítačů nebo instancí role webového pracovního procesu sdílí danou veřejnou IP adresu.  Při vytváření odchozích toků se [](#pat) Jana dynamicky spotřebovává (až do limitu před přidělením) a uvolní tyto porty, když dojde k ukončení toku nebo vypršení nečinnosti.

V následující tabulce jsou uvedena předalokace portů SNAT pro vrstvy velikostí back-endu:

| Instance | Předběžně přidělené porty SNAT na instanci |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |

Pamatujte, že počet dostupných portů SNAT se nepřevádí přímo na počet toků. Jeden port SNAT lze znovu použít pro více jedinečných umístění. Porty se spotřebují jenom v případě, že je potřeba, aby toky byly jedinečné. Pokyny pro návrh a zmírnění najdete v části o [tom, jak spravovat tento prostředek exhaustible](#snatexhaust) a část popisující [Pat](#pat).

Změna velikosti nasazení může mít vliv na některé z vašich navázaných toků. Pokud se velikost fondu back-endu zvyšuje a přejde do další úrovně, během přechodu na další větší úroveň fondu back-end se uvolní polovina předpřidělených portů SNAT. Toky, které jsou přidružené k uvolněnému portu SNAT, vyprší časový limit a je nutné je znovu vytvořit. Pokud se pokusíte o nový tok, tok se okamžitě dokončí, dokud nebudou dostupné porty k dispozici.

Pokud se velikost nasazení zmenší a přejde do nižší úrovně, zvýší se počet dostupných portů SNAT. V takovém případě nebudou ovlivněny existující porty SNAT a jejich příslušné toky.

Pokud dojde k opětovnému nasazení nebo změně cloudové služby, může infrastruktura dočasně hlásit back-end fond až dvakrát tak velké, než se čekalo, a Azure pak předem přidělit porty SNAT na jednu instanci.  To může dočasně zvýšit pravděpodobnost vyčerpání portů SNAT. Nakonec se velikost fondu převede na skutečnou velikost a Azure automaticky zvýší počet předběžně přidělených portů SNAT na očekávané číslo podle výše uvedené tabulky.  Toto chování je záměrné a nedá se konfigurovat.

Přidělení portů SNAT jsou specifická pro protokol IP (TCP a UDP se uchovávají samostatně) a vydávají se za následujících podmínek:

### <a name="tcp-snat-port-release"></a>Verze portu TCP SNAT

- Pokud server/klient posílá FIN/ACK, port SNAT se uvolní po 240 sekundách.
- Pokud je zjištěna RST, port SNAT se uvolní po 15 sekundách.
- byl dosažen časový limit nečinnosti.

### <a name="udp-snat-port-release"></a>Vydání portu UDP SNAT

- byl dosažen časový limit nečinnosti.

## <a name="problemsolving"></a>Řešení problémů 

Tato část je určená k omezení vyčerpání SNAT a dalších scénářů, ke kterým může dojít u odchozích připojení v Azure.

### <a name="snatexhaust"></a>Správa vyčerpání portů SNAT (PAT)
[Dočasné porty](#preallocatedports) používané pro [Pat](#pat) jsou prostředek exhaustible, jak je popsáno v tématu [bez přidružené veřejné IP adresy](#defaultsnat) a [veřejného koncového bodu s vyrovnáváním zatížení](#publiclbendpoint).

Pokud víte, že zahajujete mnoho odchozích připojení TCP nebo UDP ke stejné cílové IP adrese a portu, a sledujete neúspěšné odchozí připojení nebo se vám doporučuje podpora, že vyčerpáte porty SNAT (předběžně přidělené [dočasné porty](#preallocatedports) ). pomocí [Pat](#pat)) máte několik obecných možností pro zmírnění rizik. Projděte si tyto možnosti a rozhodněte, co je k dispozici a co nejlépe vyhovuje vašemu scénáři. Je možné, že jeden nebo více může pomáhat při správě tohoto scénáře.

Pokud se vám nedaří pochopit chování odchozího připojení, můžete použít statistiku protokolu IP stack (netstat). Nebo může být užitečné sledovat chování připojení pomocí zachycení paketů.

#### <a name="connectionreuse"></a>Úprava aplikace pro opětovné použití připojení 
Můžete snížit poptávku za dočasné porty, které se používají pro SNAT, a to tak, že znovu použijete připojení ve vaší aplikaci. To platí hlavně pro protokoly jako HTTP/1.1, kde je výchozí nastavení opětovného použití připojení. A další protokoly, které používají protokol HTTP jako jejich přenos (například REST), můžou mít výhodu. 

Opakované použití je vždy lepší než individuální, atomické připojení TCP pro každý požadavek. Opakované použití má za následek více výkonné, velmi efektivní transakce TCP.

#### <a name="connection pooling"></a>Úprava aplikace pro použití sdružování připojení
Ve své aplikaci můžete použít schéma sdružování připojení, ve kterém jsou požadavky interně distribuovány přes pevně danou sadu připojení (podle toho, co je to možné). Toto schéma omezuje počet dočasných používaných portů a vytvoří předvídatelné prostředí. Toto schéma může také zvýšit propustnost žádostí tím, že umožňuje více souběžných operací, když je jedno připojení blokováno na reakci operace.  

Sdružování připojení může již existovat v rámci rozhraní, které používáte k vývoji aplikace nebo nastavení konfigurace aplikace. Sdružování připojení můžete kombinovat s opětovným použitím připojení. Vaše vícenásobné požadavky pak spotřebovávají pevný a předvídatelný počet portů na stejnou cílovou IP adresu a port. Požadavky také využívají efektivní využití transakcí TCP, které snižují latenci a využití prostředků. Transakce UDP můžou také těžit z toho důvodu, že správa počtu toků UDP se může vyhnout podmínkám výfuku a spravovat využití portů SNAT.

#### <a name="retry logic"></a>Upravit aplikaci tak, aby používala méně agresivní logiku opakování
Pokud jsou [předběžně přidělené dočasné porty](#preallocatedports) používané pro [Pat](#pat) vyčerpány nebo dojde k selhání aplikace, agresivní nebo nepřímý pokus o opakování při nedecay a omezení rychlostií služby Logic způsobuje vyčerpání nebo uchování. Můžete snížit poptávku za dočasné porty pomocí méně agresivní logiky opakování. 

Dočasné porty mají časový limit nečinnosti 4 minut (není upravitelný). Pokud jsou opakované pokusy příliš agresivní, vyčerpání není nijak jasné. Proto zvažte, jak--a jak často--vaše aplikace pokusy o opakování jsou důležitou součástí návrhu.

#### <a name="assignilpip"></a>Přiřazení veřejné IP adresy na úrovni instance ke každému virtuálnímu počítači
Přiřazení ILPIP mění scénář pro [veřejnou IP adresu na úrovni instance virtuálního počítače](#ilpip). Pro virtuální počítač jsou k dispozici všechny dočasné porty veřejné IP adresy, které se používají pro každý virtuální počítač. (Na rozdíl od scénářů, kdy jsou dočasné porty veřejné IP adresy sdílené se všemi virtuálními počítači přidruženými k příslušnému nasazení.) Existují kompromisy, které je potřeba vzít v úvahu, například potenciální dopad na povolený velký počet individuálních IP adres.

>[!NOTE] 
>Tato možnost není k dispozici pro role webového pracovního procesu.

### <a name="idletimeout"></a>Obnovení odchozího nečinného časového limitu pomocí kontroly stavu

Odchozí připojení mají časový limit nečinnosti 4 minut. Tento časový limit není upravitelný. Můžete ale v případě potřeby použít přenos (například kontroly stavu protokolu TCP) nebo udržení naživu v aplikační vrstvě a obnovit tento časový limit nečinnosti.  Obraťte se na dodavatele jakéhokoli zabaleného softwaru, ať už je to podporováno, nebo jak ho povolit.  Obecně pouze jedna strana potřebuje k vygenerování kontroly stavu obnovení, aby se obnovil časový limit nečinnosti. 

## <a name="discoveroutbound"></a>Zjištění veřejné IP adresy, kterou virtuální počítač používá
Existuje mnoho způsobů, jak určit veřejnou zdrojovou IP adresu odchozího připojení. OpenDNS poskytuje službu, která vám umožní zobrazit veřejnou IP adresu vašeho virtuálního počítače. 

Pomocí příkazu nslookup můžete odeslat dotaz DNS pro název myip.opendns.com do překladače OpenDNS. Služba vrátí zdrojovou IP adresu, která se použila k odeslání dotazu. Když z virtuálního počítače spustíte následující dotaz, odpověď je veřejná IP adresa používaná pro tento virtuální počítač:

    nslookup myip.opendns.com resolver1.opendns.com


## <a name="next-steps"></a>Další postup

- Přečtěte si další informace o [Load Balancer](load-balancer-overview.md) používaném v nasazeních Správce prostředků.
- Přečtěte si informace o scénářích [odchozího připojení](load-balancer-outbound-connections.md) , které jsou dostupné v Správce prostředků nasazeních.
