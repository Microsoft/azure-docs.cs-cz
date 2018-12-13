---
title: Odchozích připojení v Azure (klasické)
titlesuffix: Azure Load Balancer
description: Tento článek vysvětluje, jak Azure umožňuje cloudové služby pro komunikaci s veřejné internetové služby.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2018
ms.author: kumud
ms.openlocfilehash: 006d8e28413e0893cafe351577f8a018d13fd268
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53189995"
---
# <a name="outbound-connections-classic"></a>Odchozí připojení (Classic)

Azure poskytuje odchozí připojení pro nasazení zákazníků prostřednictvím několika různých mechanismů. Tento článek popisuje, co jsou scénáře, kdy se vztahují, jak fungují a jejich správě.

>[!NOTE]
>Tento článek se týká pouze nasazení Classic.  Kontrola [odchozí připojení](load-balancer-outbound-connections.md) pro všechny scénáře nasazení Resource Manageru v Azure.

Nasazení v Azure může komunikovat s koncovými body mimo Azure v veřejný adresní prostor IP adres. Když instance zahájí odchozího toku k cíli v veřejný adresní prostor IP adres, Azure dynamicky mapuje privátní IP adresu na veřejnou IP adresu. Po vytvoření tohoto mapování návratový přenos pro tento tok odchozí pocházející ze dosáhnout taky privátní IP adresa původu toku.

Azure používá k provedení této funkce překlad síťových adres zdroje (SNAT). Když více privátní IP adresy se vydávají za jednu veřejnou IP adresu, Azure využívá [port překladu adres (Jan)](#pat) k maskovat privátních IP adres. Dočasné porty se používají pro token PAT a jsou [předpřidělené](#preallocatedports) na základě velikosti fondu.

Existuje více [odchozí scénáře](#scenarios). Tyto scénáře můžete kombinovat podle potřeby. Seznamte se s nimi pečlivě pro pochopení možností, omezení a vzory, která je použita k modelu nasazení a scénář aplikace. Přečtěte si pokyny pro [Správa těchto scénářů](#snatexhaust).

## <a name="scenarios"></a>Přehled scénářů

Azure nabízí tři různých způsobů dosažení klasickými nasazeními odchozí připojení.  Ne všechna nasazení Classic mají všechny tři scénáře, která je jim dostupná:

| Scénář | Metoda | Protokoly IP | Popis | Webová Role pracovního procesu | IaaS | 
| --- | --- | --- | --- | --- | --- |
| [1. Virtuální počítač s veřejnou IP adresu Instance úroveň adresou](#ilpip) | SNAT, ho maskují portu se nepoužívá. | TCP, UDP, PROTOKOL ICMP, ESP | Azure používá veřejné IP adresy přiřazené virtuálního počítače. Instance má všechny dočasné porty, které jsou k dispozici. | Ne | Ano |
| [2. veřejný koncový bod s vyrovnáváním zatížení](#publiclbendpoint) | SNAT s ho maskují port (cesta) k veřejnému koncovému bodu | TCP, UDP | Azure sdílí s několika koncovými body privátní veřejné IP adresy veřejného koncového bodu. Azure používá pro token PAT dočasné porty veřejného koncového bodu. | Ano | Ano |
| [3. Samostatný virtuální počítač ](#defaultsnat) | SNAT pomocí portu ho maskují (cesta) | TCP, UDP | Azure automaticky označí veřejnou IP adresu pro SNAT, sdílí tuto veřejnou IP adresu s celého nasazení a používá dočasné porty koncový bod veřejné IP adresy pro token PAT. Toto je základní scénář pro předchozí scénáře. Pokud potřebujete viditelnosti a kontroly to nedoporučujeme. | Ano | Ano |

Toto je podmnožinou odchozí připojení funkce k dispozici pro nasazení Resource Manager v Azure.  

Jiné nasazení v modelu Classic mají různé funkce:

| Klasické nasazení | Funkce je dostupná | 
| --- | --- |
| Virtuální počítač | scénář [1](#ilpip), [2](#publiclbendpoint), nebo [3](#defaultsnat) |
| Webová Role pracovního procesu | pouze scénář [2](#publiclbendpoint), [3](#defaultsnat) | 

[Zmírnění dopadů strategie](#snatexhaust) mít také stejný rozdíly.

[Algoritmem použitým pro předběžné přidělování dočasné porty](#ephemeralports) pro token PAT pro nasazení classic je stejný jako u nasazení prostředků Azure Resource Manageru.

### <a name="ilpip"></a>Scénář 1: Virtuální počítač s veřejnou IP adresu Instance úroveň adresou

V tomto scénáři má virtuální počítač instanci úroveň veřejné IP (ILPIP) přiřazenou. Jde o odchozích připojení, nezáleží, jestli virtuální počítač má koncový bod s vyrovnáváním zatížení, nebo ne. Tento scénář má přednost před ostatní. Při použití ILPIP, virtuální počítač ILPIP používá pro všechny odchozí toky.  

Veřejné IP adresa přiřazená k virtuálnímu počítači je 1:1 vztahu (spíše než 1:many) a je implementovaný jako bezstavové NAT. 1:1  Port maskování (Jan) se nepoužívá a virtuální počítač má všechny dočasné porty, které jsou k dispozici pro použití.

Pokud aplikace zahájí mnoho odchozích toků a dochází k vyčerpání portů SNAT, vezměte v úvahu přiřazení [ILPIP ke zmírnění SNAT omezení](#assignilpip). Kontrola [Správa SNAT vyčerpání](#snatexhaust) v celém rozsahu.

### <a name="publiclbendpoint"></a>Scénář 2: Veřejný koncový bod s vyrovnáváním zatížení

V tomto scénáři je spojené s veřejnou IP adresu prostřednictvím koncových bodů s vyrovnáváním zatížení virtuálního počítače nebo webová Role pracovního procesu. Virtuální počítač nemá přiřazenou veřejnou IP adresu. 

Při vytváření virtuálního počítače s vyrovnáváním zatížení odchozího toku, přeloží Azure privátní zdrojové IP adresy odchozí tok veřejnou IP adresu veřejný koncový bod s vyrovnáváním zatížení. Azure používá k provedení této funkce SNAT. Azure také používá [token PAT](#pat) k maskovat více privátních IP adres za veřejné IP adresy. 

Dočasné porty nástroje pro vyrovnávání zatížení veřejnou IP adresu front-endovou slouží k rozlišení jednotlivých toků, vytvoří se virtuální počítač. Dynamicky používá SNAT [předpřidělené dočasné porty](#preallocatedports) při vytvoření odchozích toků. V tomto kontextu se označují jako dočasné porty používané pro SNAT SNAT porty.

SNAT porty jsou předpřidělené, jak je popsáno v [SNAT principy a token PAT](#snat) oddílu. Jsou to omezené prostředek, který může dojít k vyčerpání. Je důležité pochopit, jak jsou [spotřebované](#pat). Chcete-li pochopit, jak navrhnout za toto využití a zmírnit podle potřeby, zkontrolovat [Správa SNAT vyčerpání](#snatexhaust).

Při [několik veřejných koncových s vyrovnáváním zatížení bodů](load-balancer-multivip.md) neexistuje, některý z těchto veřejné IP adresy [Release candidate odchozích toků](#multivipsnat), a jedna náhodně vybraná.  

### <a name="defaultsnat"></a>Scénář 3: Přidružená žádná veřejná IP adresa

V tomto případě virtuální počítač nebo webová ROle pracovního procesu není součástí veřejný koncový bod s vyrovnáváním zatížení.  A v případě virtuálních počítačů, nemá ILPIP adresu přiřazenou. Když virtuální počítač vytvoří odchozí tok, přeloží Azure privátní zdrojové IP adresy odchozí tok veřejné Zdrojová IP adresa. Veřejnou IP adresu použít pro tento odchozí tok není Konfigurovatelný a nepočítá s limitem předplatného veřejný IP prostředek.  Platforma Azure automaticky přiřadí tuto adresu.

Azure používá SNAT s ho maskují port ([token PAT](#pat)) k provedení této funkce. Tento scénář je podobný [scénář 2](#lb), s výjimkou není žádnou kontrolu nad tím IP adresa používá. Toto je základní scénář pro při scénáře 1 a 2 neexistují. Pokud chcete řídit odchozí adresy nedoporučujeme tento scénář. Pokud odchozí připojení jsou důležitou součástí vaší aplikace, byste zvolili jiný scénář.

SNAT porty jsou předpřidělené, jak je popsáno v [SNAT principy a token PAT](#snat) oddílu.  Počet virtuálních počítačů nebo sdílení veřejné IP adresy webových rolí pracovního procesu určuje počet předběžně přidělené dočasné porty.   Je důležité pochopit, jak jsou [spotřebované](#pat). Chcete-li pochopit, jak navrhnout za toto využití a zmírnit podle potřeby, zkontrolovat [Správa SNAT vyčerpání](#snatexhaust).

## <a name="snat"></a>Principy SNAT a token PAT

### <a name="pat"></a>Port maskování SNAT PAT

Při nasazení vytvoří odchozí připojení, je přepsán každý zdroj odchozí připojení. Zdroj je přepsán z privátní adresní prostor IP adres na veřejnou IP adresu přidruženou k nasazení (založené na scénářích popsaných výše). V veřejný prostor IP adres musí být jedinečný 5-n-tice tok (Zdrojová IP adresa, zdrojový port, přenosový protokol IP, cílová IP adresa, cílový port).  

Dočasné porty (SNAT) umožňují dosáhnete po přepisování privátní zdrojovou IP adresu, protože více toků pocházejí z jedné veřejné IP adresy. 

Na jeden tok do jedné cílové IP adresy, portu a protokolu se spotřebovává jeden port SNAT. Každý tok pro více toků do stejné cílové IP adresy, portu a protokolu, využívá jeden port SNAT. Tím se zajistí, že toky jsou jedinečné, když pocházejí z stejnou veřejnou IP adresu a přejděte do stejné cílové IP adresy, portu a protokolu. 

Více toků, každý z nich různé cílové IP adresy, portu a protokolu, sdílet jeden port SNAT. Cílové IP adresy, portu a protokolu zkontrolujte toky jedinečný bez nutnosti dalších zdrojových portů k rozlišení toků v veřejný adresní prostor IP adres.

Když jsou k vyčerpání prostředků port SNAT, odchozí toky neúspěšné, dokud stávající toky release SNAT porty. Nástroje pro vyrovnávání zatížení uvolňuje SNAT porty, když tok se zavře a použije [časový limit nečinnosti 4 minuty](#idletimeout) pro opětovné získání SNAT porty z nečinnosti toky.

Vzory podmínky, které běžně vedou k vyčerpání portů SNAT zmírnit, najdete v tématu [Správa SNAT](#snatexhaust) oddílu.

### <a name="preallocatedports"></a>Předběžné přidělení dočasných portů pro port ho maskují SNAT PAT

Azure používá algoritmus k určení počtu předpřidělené SNAT portů jsou k dispozici na základě velikosti fondu back-endu při použití portu maskování SNAT ([token PAT](#pat)). SNAT porty jsou dočasné porty, které jsou k dispozici pro konkrétní veřejné zdrojové adresy IP.

Azure preallocates SNAT porty při nasazení podle počtu instancí virtuálního počítače nebo Role pracovního procesu webové sdílet dané veřejné IP adresy instance.  Při vytváření odchozích toků [token PAT](#pat) dynamicky využívá (až do limitu předběžně přidělené) a uvolní tyto porty po zavření toku nebo [vypršení časového limitu nečinnosti](#ideltimeout) stát.

Následující tabulka uvádí preallocations SNAT port pro úrovně velikosti fondů back-endu:

| Instance | Předběžně přidělené SNAT porty na instanci |
| --- | --- |
| 1 – 50 | 1,024 |
| 51 – 100 | 512 |
| 101 – 200 | 256 |
| 201-400 | 128 |

Mějte na paměti, že počet dostupných portů SNAT nepřekládá přímo na počet toků. Jeden port SNAT můžete znovu použít pro více míst jedinečný. Porty se spotřebuje, pouze pokud je nutné vytvářet toky jedinečný. Pokyny k návrhu a zmírnění distribuovaných útoků, přečtěte si část o [jak ke správě tohoto prostředku vyčerpatelným](#snatexhaust) a v části popisující [token PAT](#pat).

Změna velikosti vašeho nasazení může mít vliv na některé zavedené toků. Pokud velikost fondu back-endu zvyšuje a přejde na další úroveň, polovinu vaše předběžně přidělené SNAT portů je vyžádá zpět systém během přechodu na další úroveň větší fond back-endu. Toky, které jsou spojeny s portem uvolňovaného SNAT vyprší časový limit a musí být zrušena. Při pokusu o nový tok, tok proběhne úspěšně okamžitě, za předpokladu, předběžně přidělené porty jsou k dispozici.

Pokud se zmenší velikost pro nasazení a zvyšuje počet dostupných portů SNAT přechody do nižší úrovně. V tomto případě existující přidělených SNAT portů a jejich příslušné toky to nebude mít vliv.

Pokud cloudovou službu se znovu nasadit nebo změnit, infrastruktura může hlásit dočasně back-endového fondu bude až dvakrát jako velká jako skutečné a Azure se pak předběžné přidělení méně SNAT porty na instanci než se očekávalo.  Dočasně to můžete zvýšit pravděpodobnost vyčerpání portů SNAT. Nakonec velikost fondu přejde na skutečnou velikost a Azure bude automaticky zvětšovala v předběžně přidělené SNAT porty očekávaný počet podle výše uvedené tabulce.  Toto chování je záměrné a nelze ji konfigurovat.

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
[Dočasné porty](#preallocatedports) používá pro [token PAT](#pat) jsou vyčerpatelným prostředků, jak je popsáno v [žádné přidružena veřejná IP adresa](#defaultsnat) a [veřejný koncový bod s vyrovnáváním zatížení](#publiclbendpoint).

Pokud víte, že jste zahajování mnoho odchozí připojení TCP nebo UDP na stejnou cílovou IP adresu a port, a radí, podpora, že jste spotřebovává SNAT porty nebo sledovat selhání odchozí připojení (předpřidělené [dočasné porty](#preallocatedports) používané [token PAT](#pat)), máte několik možností, jak obecná omezení rizik. Prohlédněte si tyto možnosti a rozhodnout, co je k dispozici a pro váš scénář nejvhodnější. Je možné, že jeden nebo více může pomoct spravovat tento scénář.

Pokud máte potíže s principy odchozích připojení chování, můžete použít statistiky zásobníku IP (netstat). Nebo může být užitečné při sledování chování připojení pomocí zachytávání paketů.

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
Přiřazení ILPIP změní váš scénář [úroveň veřejná IP adresa Instance k virtuálnímu počítači](#ilpip). Všechny dočasné porty veřejné IP adresy, které se používají pro jednotlivé virtuální počítače jsou k dispozici k virtuálnímu počítači. (Na rozdíl od scénáře kde jsou všechny virtuální počítače přidružené k příslušné nasazení sdílené dočasné porty veřejnou IP adresu.) Existují kompromisy vzít v úvahu, jako je možný dopad na seznam povolených velký počet jednotlivých IP adres.

>[!NOTE] 
>Tato možnost není k dispozici v případě webových rolí pracovního procesu.

### <a name="idletimeout"></a>Keepalive použít k resetování odchozí časový limit nečinnosti

Odchozí připojení mají časový limit nečinnosti 4 minuty. Tento časový limit není měnitelné. Aktualizace nečinné toku a obnovit tento časový limit nečinnosti v případě potřeby však můžete použít přenosu (například TCP keepalive) nebo keepalive aplikační vrstvu.  Obraťte se na dodavatele jakémkoli zabaleném softwaru na to, zda je podporováno nebo jak se dá povolit.  Obecně pouze jedna strana musí generovat keepalive resetovat časový limit nečinnosti. 

## <a name="discoveroutbound"></a>Zjišťování veřejné IP adresy, která používá virtuální počítač
Existuje mnoho způsobů, jak určit IP adresu veřejné zdrojového odchozí připojení. OpenDNS poskytuje službu, která můžete zobrazit veřejnou IP adresu vašeho virtuálního počítače. 

Při použití příkazu nslookup, můžete odeslat dotaz DNS pro název myip.opendns.com OpenDNS překládání. Služba vrátí zdrojovou IP adresu, která jste použili k zaslání dotazu. Při spuštění následujícího dotazu z vašeho virtuálního počítače je odpověď na veřejnou IP adresu použít pro tento virtuální počítač:

    nslookup myip.opendns.com resolver1.opendns.com


## <a name="next-steps"></a>Další postup

- Další informace o [nástroje pro vyrovnávání zatížení](load-balancer-overview.md) používá v nasazení Resource Manager.
- Další informace o [odchozí připojení](load-balancer-outbound-connections.md) scénáře, které jsou k dispozici v nasazení Resource Manager.
