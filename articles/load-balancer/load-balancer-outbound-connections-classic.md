---
title: Odchozí připojení v Azure (klasický) | Microsoft Docs
description: Tento článek vysvětluje, jak Azure umožňuje cloudové služby pro komunikaci s služby veřejného Internetu.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 7679fd253370d8ca9ca9ac57dc080806050f5c3c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="outbound-connections-classic"></a>Odchozí připojení (klasické)

Azure poskytuje odchozí připojení pro zákaznických nasazení pomocí několika různých mechanismů. Tento článek popisuje, co jsou scénáře, kdy se vztahují, jak pracují a jak spravovat.

>[!NOTE]
>Tento článek se týká jenom nasazení Classic.  Zkontrolujte [odchozí připojení](load-balancer-outbound-connections.md) pro všechny scénáře nasazení Resource Manager v Azure.

Nasazení v Azure může komunikovat s koncovými body mimo Azure v rámci veřejný adresní prostor IP adres. Pokud instance zahájí odchozího toku do cílového umístění v veřejný adresní prostor IP adres, Azure dynamicky mapuje privátní IP adresu na veřejnou IP adresu. Po vytvoření tohoto mapování, návratový provoz pro tuto odchozí původu toku dosáhnout taky privátní IP adresu kde toku vytvořena.

Azure používá zdroj překládání adres (překládat pomocí SNAT) a provést tuto funkci. Pokud více privátních IP adres se vydávají za jednu veřejnou IP adresu, použije Azure [portu překlad adres (Jan)](#pat) k maskovat privátních IP adres. Dočasné porty se používají pro Jan a jsou [souhrnů ještě neumístěných](#preallocatedports) na základě velikosti fondu.

Existuje více [odchozí scénáře](#scenarios). Tyto scénáře můžete kombinovat podle potřeby. Přečtěte si je pečlivě a pochopit schopnosti, omezení a vzory, protože se vztahují na váš model nasazení a aplikační scénář. Přečtěte si pokyny pro [Správa těchto scénářů](#snatexhaust).

## <a name="scenarios"></a>Přehled scénáře

Azure poskytuje tři různých způsobů dosažení nasazení Classic odchozí připojení.  Všechna nasazení Classic mají všechny tři scénáře, která je jim dostupná:

| Scénář | Metoda | Popis | Webové Role pracovního procesu | IaaS | 
| --- | --- | --- | --- | --- |
| [1. Virtuální počítač s Instance úroveň veřejnou IP adresu](#ilpip) | Překládat pomocí SNAT, port vydávají nepoužívá |Azure používá veřejné IP adresy přiřazené virtuálního počítače. Instance má všechny dočasné porty, které jsou k dispozici. | Ne | Ano |
| [2. veřejný koncový bod Vyrovnávání zatížení sítě](#publiclbendpoint) | Překládat pomocí SNAT s port vydávají (PAT) pro veřejný koncový bod |Azure sdílí veřejných IP adres veřejný koncový bod s více privátní koncových bodů. Azure používá dočasné porty veřejný koncový bod pro Jan. | Ano | Ano |
| [3. Samostatný virtuální počítač ](#defaultsnat) | Překládat pomocí SNAT s port vydávají (PAT) | Azure automaticky označí veřejnou IP adresu pro překládat pomocí SNAT, sdílí tato veřejná IP adresa s celé nasazení a používá dočasné porty koncový bod veřejné IP adresy pro Jan. Toto je záložní scénář pro uvedených scénářů. Pokud potřebujete viditelnost a kontrolu se nedoporučuje. | Ano | Ano|

Toto je podmnožinu funkcí odchozí připojení k dispozici pro nasazení Resource Manager v Azure.  

Jiné nasazení v klasickém mají různé funkce:

| Nasazení Classic | Funkce je k dispozici | 
| --- | --- |
| Virtuální počítač | scénář [1](#ilpip), [2](#publiclbendpoint), nebo [3](#defaultsnat) |
| Webové Role pracovního procesu | jenom scénář s [2](#publiclbendpoint), [3](#defaultsnat) | 

[Zmírnění dopadů strategie](#snatexhaust) mít také stejnou rozdíly.

[Algoritmem použitým pro předběžné přidělování dočasné porty](#ephemeralports) Jan pro nasazení classic je stejné jako pro nasazení prostředků Azure Resource Manager.

### <a name="ilpip"></a>Scénář 1: Počítač s adresou veřejná IP adresa na úrovni Instance

V tomto scénáři má virtuální počítač instanci úroveň veřejné IP splnění přiřazen. Pokud jde o odchozí připojení, nezáleží, jestli virtuální počítač má koncový bod Vyrovnávání zatížení, nebo ne. Tento scénář má přednost před ostatní. Pokud se používá splnění, virtuální počítač používá splnění pro všechny odchozí toky.  

Veřejné IP adresy, které jsou přiřazené k virtuálnímu počítači je 1:1 relace (místo 1:many) a implementovaný jako bezstavové NAT. 1:1  Port podvržený (Jan) se nepoužívá a virtuální počítač má všechny dočasné porty, které jsou k dispozici pro použití.

Pokud vaše aplikace iniciuje mnoho odchozí toky a dochází k vyčerpání port překládat pomocí SNAT, vezměte v úvahu přiřazení [splnění zmírnit překládat pomocí SNAT omezení](#assignilpip). Zkontrolujte [Správa překládat pomocí SNAT vyčerpání](#snatexhaust) v celé jeho šíři.

### <a name="publiclbendpoint"></a>Scénář 2: Veřejného Vyrovnávání zatížení koncového bodu

V tomto scénáři je přidružen veřejnou IP adresu prostřednictvím koncového bodu Vyrovnávání zatížení sítě virtuálních počítačů nebo webové Role pracovního procesu. Virtuální počítač nemá veřejnou IP adresu přiřazen. 

Při vyrovnávání zatížení sítě virtuálního počítače vytvoří odchozí tok, znamená, že Azure privátní zdrojovou IP adresu odchozího toku veřejnou IP adresu veřejný koncový bod Vyrovnávání zatížení sítě. Azure pomocí této funkce překládat pomocí SNAT. Azure také používá [PAT](#pat) k maskovat více privátních IP adres za veřejnou IP adresu. 

Dočasné porty nástroje pro vyrovnávání zatížení veřejnou IP adresu front-endovou slouží k rozlišení jednotlivých toky pochází ve virtuálním počítači. Dynamicky používá překládat pomocí SNAT [souhrnů ještě neumístěných dočasné porty](#preallocatedports) vytvoření odchozí toky. V tomto kontextu se nazývají dočasné porty používané pro překládat pomocí SNAT porty překládat pomocí SNAT.

Překládat pomocí SNAT porty jsou souhrnů ještě neumístěných jak je popsáno v [překládat pomocí SNAT principy a Jana](#snat) části. Konečný prostředek, který může dojít k vyčerpání jsou. Je důležité pochopit, jak jsou [spotřebované](#pat). Zjistit informace o návrhu pro tento spotřebu a zmírnit podle potřeby zkontrolujte [Správa překládat pomocí SNAT vyčerpání](#snatexhaust).

Při [více veřejné koncové Vyrovnávání zatížení sítě body](load-balancer-multivip.md) neexistuje, některý z těchto veřejné IP adresy [kandidátem na odchozí toky](#multivipsnat), a jeden je náhodně vybrané.  

### <a name="defaultsnat"></a>Scénář 3: Žádné veřejné IP adresy přidružené

V tomto scénáři webové ROle pracovního procesu na virtuální počítač není součástí veřejný koncový bod Vyrovnávání zatížení sítě.  A v případě virtuálních počítačů, nemá adresu splnění přiřazen. Když virtuální počítač se vytváří odchozího toku, překládá Azure privátní zdrojovou IP adresu odchozího toku veřejné Zdrojová IP adresa. Veřejnou IP adresu použitou pro tuto odchozího toku není Konfigurovatelný a nepočítá proti odběru limitu prostředků veřejné IP.  Azure automaticky přiděluje tuto adresu.

Azure používá překládat pomocí SNAT s vydávají port ([PAT](#pat)) a provést tuto funkci. Tento scénář je podobná [scénář 2](#lb), s výjimkou je žádnou kontrolu nad adresa IP použitá. Toto je záložní scénář pro při scénáře 1 a 2 neexistují. Pokud chcete kontrolu nad adresu odchozí nedoporučujeme tento scénář. Pokud odchozí připojení jsou důležitou součástí aplikace, měli jste zvolili jiný scénář.

Překládat pomocí SNAT porty jsou souhrnů ještě neumístěných jak je popsáno v [překládat pomocí SNAT principy a Jana](#snat) části.  Počet virtuálních počítačů nebo rolí pracovního procesu webové sdílení veřejnou IP adresu určuje počet předběžně přidělené dočasné porty.   Je důležité pochopit, jak jsou [spotřebované](#pat). Zjistit informace o návrhu pro tento spotřebu a zmírnit podle potřeby zkontrolujte [Správa překládat pomocí SNAT vyčerpání](#snatexhaust).

## <a name="snat"></a>Princip překládat pomocí SNAT a Jan

### <a name="pat"></a>Port podvržený překládat pomocí SNAT (Jan)

Při nasazení vytvoří odchozí připojení, je přepsaná každý zdroj odchozí připojení. Zdroj je přepsána z prostor privátní IP adresy pro veřejnou IP adresu přidruženou k nasazení (podle scénáře popsané výše). V veřejný adresní prostor IP adres 5-n-tice toku (zdrojové IP adresy, zdrojového portu, přenosového protokolu IP, cílové IP adresy, cílový port) musí být jedinečný.  

Dočasné porty (porty překládat pomocí SNAT) se používají k dosáhnout po přepisování privátní zdrojovou IP adresu, protože více toků pocházejí z jedné veřejné IP adresy. 

Jeden port překládat pomocí SNAT obsazením za toku do jedné cílové IP adresy, portu a protokolu. Pro více toků na stejnou cílovou IP adresu, port a protokol využívá každý tok jediný port překládat pomocí SNAT. Zajistíte, že tyto toky jsou jedinečné, když budou pocházet ze stejné veřejnou IP adresu, přejděte na stejnou cílovou IP adresu, port a protokol. 

Více toků, každý na jinou cílovou IP adresu, port a protokol, sdílet jeden port překládat pomocí SNAT. Cílovou IP adresu, port a protokol zkontrolujte toky jedinečný bez nutnosti dalších zdrojových portů k rozlišení toků v veřejný adresní prostor IP adres.

Když vyčerpání prostředků překládat pomocí SNAT port, odchozí toky nezdaří, dokud existující toky verze porty překládat pomocí SNAT. Nástroj pro vyrovnávání zatížení získá překládat pomocí SNAT porty, když toku zavře a používá [časový limit nečinnosti 4minutové](#idletimeout) pro opětovného získání překládat pomocí SNAT porty z nečinnosti toků.

Vzory pro zmírnění podmínky, které běžně vést k vyčerpání port překládat pomocí SNAT, přečtěte si [Správa překládat pomocí SNAT](#snatexhaust) části.

### <a name="preallocatedports"></a>Předběžné přidělení dočasných portů pro port vydávají překládat pomocí SNAT (Jan)

Azure používá algoritmus určit počet souhrnů ještě neumístěných překládat pomocí SNAT porty, které jsou k dispozici na základě velikosti fondu back-end při použití portu podvržený překládat pomocí SNAT ([PAT](#pat)). Překládat pomocí SNAT porty jsou dočasné porty, které jsou k dispozici pro konkrétní veřejné zdrojové adresy IP.

Azure preallocates překládat pomocí SNAT porty při nasazení založené na tom, jak velký počet instancí virtuálního počítače nebo webové Role pracovního procesu sdílet danou veřejnou IP adresu instance.  Když se vytváří odchozí toky, [PAT](#pat) dynamicky využívá (až předběžně přidělené limit) a uvolní tyto porty toku zavře nebo [časové limity nečinnosti](#ideltimeout) dojít.

V následující tabulce jsou uvedeny preallocations port překládat pomocí SNAT pro úrovně velikosti fondu back-end:

| Instance | Předběžně přidělené překládat pomocí SNAT porty na instanci |
| --- | --- |
| 1 – 50 | 1,024 |
| 51 až 100 | 512 |
| 101-200 | 256 |
| 201 – 400 | 128 |

Mějte na paměti, že počet dostupných portů překládat pomocí SNAT nepřekládá přímo na toků. Jediný port překládat pomocí SNAT lze znovu použít pro více cílů jedinečný. Porty jsou využívat pouze v případě, že je nutné, aby toky jedinečný. Pokyny k návrhu a zmírnění dopadů, naleznete v části o [postup správy tento prostředek vyčerpatelným](#snatexhaust) a oddíl, který popisuje [PAT](#pat).

Změna velikosti vašeho nasazení může mít vliv na některé vaše zavedených toků. Pokud velikost fondu back-end zvyšuje a přejde do s další vrstvou, polovinu vaší předběžně přidělené překládat pomocí SNAT porty jsou uvolnit při přechodu do další vrstvou větší fond back-end. Toky, které jsou spojeny s regenerovaný portem překládat pomocí SNAT vypršení časového limitu, který se musí znovu navázat. Pokud dojde k pokusu o nové toku, bude toku okamžitě úspěšné, dokud předběžně přidělené porty jsou k dispozici.

Pokud velikost nasazení snižuje a přejde do nižší úrovně, zvyšuje počet dostupných portů překládat pomocí SNAT. V takovém případě existující přidělené překládat pomocí SNAT porty a jejich odpovídajících toky neovlivní.

Přidělení překládat pomocí SNAT porty jsou konkrétní IP přenosového protokolu (TCP a UDP udržované samostatně) a jsou uvolněny za následujících podmínek:

### <a name="tcp-snat-port-release"></a>Verze port TCP překládat pomocí SNAT

- Pokud obě server nebo klient odešle najít nebo potvrzení, budou překládat pomocí SNAT port vydané po 240 sekund.
- Pokud je vidět RVNÍ, budou překládat pomocí SNAT port vydané po 15 sekund.
- byl dosažen limit nečinnosti

### <a name="udp-snat-port-release"></a>Verze port UDP překládat pomocí SNAT

- byl dosažen limit nečinnosti

## <a name="problemsolving"></a> Řešení problémů 

Tato část je určena pro zmírnění překládat pomocí SNAT vyčerpání a v dalších scénářích, které mohou nastat u odchozí připojení v Azure.

### <a name="snatexhaust"></a> Správa vyčerpání port překládat pomocí SNAT (Jan)
[Dočasné porty](#preallocatedports) používá pro [PAT](#pat) jsou vyčerpatelným prostředků, jak je popsáno v [žádné veřejné IP adresy přidružené](#defaultsnat) a [veřejný koncový bod s vyrovnáváním zatížení](#publiclbendpoint).

Pokud víte, že jste zahajování mnoho odchozí připojení TCP nebo UDP na stejnou cílovou IP adresu a port, a doporučujeme pomocí podpory, že jste vyčerpává porty překládat pomocí SNAT nebo sledovat selhání odchozí připojení (souhrnů ještě neumístěných [dočasných porty](#preallocatedports) používá [PAT](#pat)), máte několik možností obecné zmírnění dopadů. Přečtěte si tyto možnosti a rozhodnout, jaký je k dispozici a nejvhodnější pro váš scénář. Je možné, že jeden nebo více může pomoct spravovat tento scénář.

Pokud máte potíže seznámení s chováním odchozí připojení, můžete použít IP zásobník statistiky (netstat). Nebo může být užitečné, abyste mohli pozorovat chování připojení pomocí paketu zachycení.

#### <a name="connectionreuse"></a>Upravit aplikaci znovu použít připojení 
Můžete snížit vyžádání pro dočasné porty, které se používají pro překládat pomocí SNAT opětovným použitím připojení ve vaší aplikaci. To platí hlavně pro protokoly jako HTTP/1.1, kde je výchozím nastavením, opakované použití připojení. A pak využívat jiné protokoly, které používají protokol HTTP jako způsob přepravy (například REST). 

Opakované použití je vždy lepší, než jednotlivé, atomic připojení TCP pro každou žádost. Znovu použít za následek další původce, velmi efektivní TCP transakce.

#### <a name="connection pooling"></a>Upravit aplikaci, aby používala sdružování připojení
Můžete použít připojení sdružování schéma ve vaší aplikaci, kde jsou požadavky interně distribuovány na pevnou sadu připojení (každý opakovaného použití kde je to možné). Toto schéma omezí počet dočasné porty používá a vytvoří předvídatelnější prostředí. Toto schéma, můžete taky zvýšit propustnost žádostí tím, že více souběžných operací, když blokuje jednoho připojení na odpověď operace.  

Sdružování připojení možná již existuje v rámci, který používáte k vývoji vaší aplikace nebo nastavení konfigurace pro vaši aplikaci. Můžete kombinovat s opakované použití připojení sdružování připojení. Více požadavků pak využívat pevné, předvídatelný počet portů na stejnou cílovou IP adresu a port. Požadavky také těžit z efektivní využití TCP transakce snižuje latence a využití prostředků. Transakce UDP mohou také těžit, protože správa UDP toků můžete zase vyhnout výfukového podmínky a spravovat využití portu překládat pomocí SNAT.

#### <a name="retry logic"></a>Upravit aplikaci, aby používala méně agresivní logika opakovaných pokusů
Když [souhrnů ještě neumístěných dočasné porty](#preallocatedports) používá pro [PAT](#pat) jsou vyčerpání nebo aplikace dojde k selhání, agresivní nebo hrubou silou opakuje bez decay a omezení rychlosti logiku způsobit vyčerpání dojít, nebo zachovat. Vyžádání pro dočasné porty můžete omezit použitím méně agresivní logika opakovaných pokusů. 

Dočasné porty mít 4 minut nečinnosti, po vypršení časového limitu (není možné nastavit). Pokud jsou moc agresivní opakované pokusy, má k vyčerpání nebyla příležitost vymazány svoje vlastní. Vzhledem k tomu, jak – a jak často – aplikace opakování transakce je proto důležitou součástí návrhu.

#### <a name="assignilpip"></a>Přiřazení IP adresy Instance úrovně veřejné pro každý virtuální počítač
Přiřazení splnění změní váš scénář [Instance úroveň veřejnou IP adresu pro virtuální počítač](#ilpip). Všechny dočasné porty veřejné IP adresy, které se používají pro jednotlivé virtuální počítače jsou k dispozici pro virtuální počítač. (Na rozdíl od scénáře kde sdílené dočasné porty veřejnou IP adresu s všechny virtuální počítače spojené s odpovídající nasazení.) Existují kompromisy vzít v úvahu, například potenciální dopad vytvoření seznamu povolených velký počet jednotlivé IP adresy.

>[!NOTE] 
>Tato možnost není k dispozici pro webové role pracovního procesu.

### <a name="idletimeout"></a>Použít udržování otevřených připojení k resetování odchozí časový limit nečinnosti

Odchozí připojení mít časový limit nečinnosti 4 minuty. Tento časový limit není upravit. Však můžete přenos (například TCP udržování otevřených připojení) nebo udržování otevřených připojení aplikační vrstvě aktualizujte tok nečinnosti a obnovit tento časový limit nečinnosti v případě potřeby.  Obraťte se na dodavatele jakékoli zabalené software na to, zda je podporováno, nebo jak jej povolit.  Obecně pouze jedné strany, které je potřeba generovat udržování otevřených připojení k resetování časový limit nečinnosti. 

## <a name="discoveroutbound"></a>Zjišťování veřejnou IP adresu, která používá virtuální počítač
Existuje mnoho způsobů, jak určit IP adresu zdrojového veřejné odchozí připojení. OpenDNS poskytuje službu, která můžete zobrazit veřejnou IP adresu vašeho virtuálního počítače. 

Pomocí příkazu nslookup můžete odeslat dotaz DNS pro název myip.opendns.com do překladače OpenDNS. Služba vrátí zdrojové IP adresy, který se používá k odeslání dotazu. Když spustíte následující dotaz z virtuálního počítače, je odpověď na veřejné IP adresy použít pro tento virtuální počítač:

    nslookup myip.opendns.com resolver1.opendns.com


## <a name="next-steps"></a>Další postup

- Další informace o [nástroj pro vyrovnávání zatížení](load-balancer-overview.md) používá v nasazení Resource Manager.
- Informace o režimu [odchozí připojení](load-balancer-outbound-connections.md) scénáře, které jsou k dispozici v nasazení Resource Manager.
