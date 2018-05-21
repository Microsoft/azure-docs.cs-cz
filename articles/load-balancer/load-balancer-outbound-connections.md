---
title: Odchozí připojení v Azure | Microsoft Docs
description: Tento článek vysvětluje, jak Azure umožňuje virtuálním počítačům komunikovat s služby veřejného Internetu.
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
ms.date: 05/08/2018
ms.author: kumud
ms.openlocfilehash: 14dc28bdca9b1c3cfa78c8120a68f7e2a16fbea1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="outbound-connections-in-azure"></a>Odchozí připojení v Azure

Azure poskytuje odchozí připojení pro zákaznických nasazení pomocí několika různých mechanismů. Tento článek popisuje, co jsou scénáře, kdy se vztahují, jak pracují a jak spravovat.

>[!NOTE] 
>Tento článek se týká pouze nasazení Resource Manager. Zkontrolujte [odchozí připojení (klasické)](load-balancer-outbound-connections-classic.md) pro všechny scénáře nasazení Classic v Azure.

Nasazení v Azure může komunikovat s koncovými body mimo Azure v rámci veřejný adresní prostor IP adres. Pokud instance zahájí odchozího toku do cílového umístění v veřejný adresní prostor IP adres, Azure dynamicky mapuje privátní IP adresu na veřejnou IP adresu. Po vytvoření tohoto mapování, návratový provoz pro tuto odchozí původu toku dosáhnout taky privátní IP adresu kde toku vytvořena.

Azure používá zdroj překládání adres (překládat pomocí SNAT) a provést tuto funkci. Pokud více privátních IP adres se vydávají za jednu veřejnou IP adresu, použije Azure [portu překlad adres (Jan)](#pat) k maskovat privátních IP adres. Dočasné porty se používají pro Jan a jsou [souhrnů ještě neumístěných](#preallocatedports) na základě velikosti fondu.

Existuje více [odchozí scénáře](#scenarios). Tyto scénáře můžete kombinovat podle potřeby. Přečtěte si je pečlivě a pochopit schopnosti, omezení a vzory, protože se vztahují na váš model nasazení a aplikační scénář. Přečtěte si pokyny pro [Správa těchto scénářů](#snatexhaust).

>[!IMPORTANT] 
>Nástroj pro vyrovnávání zatížení zavádí nové schopnosti a různé chování odchozí připojení.   Například [scénář 3](#defaultsnat) neexistuje, pokud je interní standardní Vyrovnávání zatížení a různé kroky je třeba přijmout.   Pečlivě zkontrolujte tento celý dokument pochopit celkové koncepty a rozdíly mezi SKU.

## <a name="scenarios"></a>Přehled scénáře

Azure nástroj pro vyrovnávání zatížení a související prostředky jsou explicitně definován při použití [Azure Resource Manager](#arm).  Azure aktuálně poskytuje tři různých způsobů dosažení odchozí připojení pro prostředky Azure Resource Manager. 

| Scénář | Metoda | Protokoly IP | Popis |
| --- | --- | --- | --- |
| [1. Virtuální počítač s adresu veřejná IP adresa na úrovni Instance (s nebo bez nástroj pro vyrovnávání zatížení)](#ilpip) | Překládat pomocí SNAT, port vydávají nepoužívá | PROTOKOL TCP, UDP, ICMP, ESP | Azure používá veřejnou IP adresu, které jsou přiřazené ke konfiguraci IP adresy z instance síťový adaptér. Instance má všechny dočasné porty, které jsou k dispozici. |
| [2. Veřejné nástroj pro vyrovnávání zatížení, které jsou přidružené k virtuálnímu počítači (žádná Instance úroveň veřejná IP adresa v instanci)](#lb) | Překládat pomocí SNAT s port vydávají (PAT) pomocí frontends nástroj pro vyrovnávání zatížení | TCP, UDP |Azure sdílí veřejnou IP adresu veřejné frontends nástroj pro vyrovnávání zatížení s více privátních IP adres. Azure používá dočasné porty frontends k Jan. |
| [3. Samostatný virtuální počítač (žádná služba Vyrovnávání zatížení, žádná Instance úroveň veřejná IP adresa)](#defaultsnat) | Překládat pomocí SNAT s port vydávají (PAT) | TCP, UDP | Azure automaticky označí veřejnou IP adresu pro překládat pomocí SNAT, sdílí tato veřejná IP adresa s více privátních IP adres skupiny dostupnosti a používá dočasné porty tato veřejná IP adresa. Toto je záložní scénář pro uvedených scénářů. Pokud potřebujete viditelnost a kontrolu se nedoporučuje. |

Pokud nechcete, aby virtuální počítač ke komunikaci s koncovými body mimo Azure v rámci prostor veřejných IP adres, můžete použít skupiny zabezpečení sítě (Nsg) k blokování přístupu podle potřeby. V části [nelze navázat odchozí připojení](#preventoutbound) podrobněji popisuje skupiny Nsg. Pokyny k návrhu, implementaci a správu virtuálních sítí bez jakékoli odchozí přístup je mimo rámec tohoto článku.

### <a name="ilpip"></a>Scénář 1: Počítač s adresou veřejná IP adresa na úrovni Instance

V tomto scénáři má virtuální počítač instanci úroveň veřejné IP splnění přiřazen. Co se týče odchozí připojení, nezáleží, jestli virtuální počítač je Vyrovnávané nebo ne. Tento scénář má přednost před ostatní. Pokud se používá splnění, virtuální počítač používá splnění pro všechny odchozí toky.  

Veřejné IP adresy, které jsou přiřazené k virtuálnímu počítači je 1:1 relace (místo 1:many) a implementovaný jako bezstavové NAT. 1:1  Port podvržený (Jan) se nepoužívá a virtuální počítač má všechny dočasné porty, které jsou k dispozici pro použití.

Pokud vaše aplikace iniciuje mnoho odchozí toky a dochází k vyčerpání port překládat pomocí SNAT, vezměte v úvahu přiřazení [splnění zmírnit překládat pomocí SNAT omezení](#assignilpip). Zkontrolujte [Správa překládat pomocí SNAT vyčerpání](#snatexhaust) v celé jeho šíři.

### <a name="lb"></a>Scénář 2: Vyrovnávání zatížení sítě VM bez Instance úroveň veřejné IP adresy

V tomto scénáři je virtuální počítač součástí veřejné back-endový fond Vyrovnávání zatížení. Virtuální počítač nemá veřejnou IP adresu přiřazen. Vyrovnávání zatížení prostředků musí být nakonfigurované pravidlo Vyrovnávání zatížení pro vytvoření odkazu mezi veřejnou IP front-endovou s fondem back-end.

Pokud neprovedete tohoto pravidla konfigurace, toto chování je, jak je popsáno v tento scénář pro [samostatný virtuální počítač s žádné Instance úroveň veřejnou IP adresu](#defaultsnat). Není nutné pro pravidlo tak, aby měl pracovní naslouchací proces ve fondu back-end pro test stavu úspěšné.

Při vyrovnávání zatížení sítě virtuálního počítače vytvoří odchozí tok, znamená, že Azure privátní zdrojovou IP adresu odchozího toku veřejnou IP adresu veřejnou front-endovou Vyrovnávání zatížení. Azure pomocí této funkce překládat pomocí SNAT. Azure také používá [PAT](#pat) k maskovat více privátních IP adres za veřejnou IP adresu. 

Dočasné porty nástroje pro vyrovnávání zatížení veřejnou IP adresu front-endovou slouží k rozlišení jednotlivých toky pochází ve virtuálním počítači. Dynamicky používá překládat pomocí SNAT [souhrnů ještě neumístěných dočasné porty](#preallocatedports) vytvoření odchozí toky. V tomto kontextu se nazývají dočasné porty používané pro překládat pomocí SNAT porty překládat pomocí SNAT.

Překládat pomocí SNAT porty jsou souhrnů ještě neumístěných jak je popsáno v [překládat pomocí SNAT principy a Jana](#snat) části. Konečný prostředek, který může dojít k vyčerpání jsou. Je důležité pochopit, jak jsou [spotřebované](#pat). Zjistit informace o návrhu pro tento spotřebu a zmírnit podle potřeby zkontrolujte [Správa překládat pomocí SNAT vyčerpání](#snatexhaust).

Když [více (veřejné) IP adresy, které jsou přidružené základní nástroje pro vyrovnávání zatížení](load-balancer-multivip-overview.md), jakékoliv tyto veřejné IP adresy jsou [kandidátem na odchozí toky](#multivipsnat), a jedna je vybrána.  

Chcete-li sledovat stav odchozí připojení s základní nástroje pro vyrovnávání zatížení, můžete použít [analýzy protokolů pro vyrovnávání zatížení](load-balancer-monitor-log.md) a [výstrahy protokoly událostí](load-balancer-monitor-log.md#alert-event-log) pro monitorování překládat pomocí SNAT port vyčerpání zprávy.

### <a name="defaultsnat"></a>Scénář 3: Samostatný virtuální počítač bez Instance úroveň veřejnou IP adresu

V tomto scénáři virtuální počítač není součástí veřejné fondu vyrovnávání zatížení (a není součástí fondu interní nástroj pro vyrovnávání zatížení) a nemá adresu splnění přiřazen. Když virtuální počítač se vytváří odchozího toku, překládá Azure privátní zdrojovou IP adresu odchozího toku veřejné Zdrojová IP adresa. Veřejnou IP adresu použitou pro tuto odchozího toku není Konfigurovatelný a nepočítá proti odběru limitu prostředků veřejné IP.

>[!IMPORTANT] 
>Tento scénář platí také při __pouze__ je připojený k interní základní pro vyrovnávání zatížení. Scénář 3 je __není k dispozici__ když je interní standardní Vyrovnávání zatížení připojen k virtuálnímu počítači.  Je třeba explicitně vytvořit [scénář 1](#ilpip) nebo [scénář 2](#lb) kromě používání interní standardní Vyrovnávání zatížení.

Azure používá překládat pomocí SNAT s vydávají port ([PAT](#pat)) a provést tuto funkci. Tento scénář je podobná [scénář 2](#lb), s výjimkou je žádnou kontrolu nad adresa IP použitá. Toto je záložní scénář pro při scénáře 1 a 2 neexistují. Pokud chcete kontrolu nad adresu odchozí nedoporučujeme tento scénář. Pokud odchozí připojení jsou důležitou součástí aplikace, měli jste zvolili jiný scénář.

Překládat pomocí SNAT porty jsou souhrnů ještě neumístěných jak je popsáno v [překládat pomocí SNAT principy a Jana](#snat) části.  Počet virtuálních počítačů sdílení skupiny dostupnosti Určuje, které předběžné přidělování vrstvě platí.  Samostatný virtuální počítač bez skupiny dostupnosti je efektivně fond 1 pro účely určení předběžné přidělení (porty 1024 překládat pomocí SNAT). Konečný prostředek, který může dojít k vyčerpání jsou porty překládat pomocí SNAT. Je důležité pochopit, jak jsou [spotřebované](#pat). Zjistit informace o návrhu pro tento spotřebu a zmírnit podle potřeby zkontrolujte [Správa překládat pomocí SNAT vyčerpání](#snatexhaust).

### <a name="combinations"></a>Více, kombinované scénářů

Popsané v předchozích částech k dosažení konkrétní výsledek scénáře můžete kombinovat. Pokud existuje více scénářů, se vztahují pořadí podle priority: [scénář 1](#ilpip) má přednost před [scénář 2](#lb) a [3](#defaultsnat). [Scénář 2](#lb) přepsání [scénář 3](#defaultsnat).

Příkladem je nasazení služby Správce prostředků Azure, kde založena na odchozí připojení k omezenému počtu cílů aplikace, ale také přijme příchozí toky přes front-end pro vyrovnávání zatížení. V takovém případě můžete kombinovat scénáře 1 a 2 pro pomoc. Pro další vzory, zkontrolujte [Správa překládat pomocí SNAT vyčerpání](#snatexhaust).

### <a name="multife"></a> Více frontends pro odchozí toky

#### <a name="load-balancer-standard"></a>Load Balancer úrovně Standard

Standardní nástroje pro vyrovnávání zatížení používá všechny kandidáty pro odchozí toky na stejný čas, kdy [více (veřejné) frontends IP](load-balancer-multivip-overview.md) je k dispozici. Každý front-endu vynásobí počet dostupných portů předběžně přidělené překládat pomocí SNAT, pokud se pravidlo Vyrovnávání zatížení je povolený pro odchozí připojení.

Můžete potlačit na front-endovou IP adresu z používá pro odchozí připojení s novou možností pravidlo Vyrovnávání zatížení:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Za normálních okolností se tato možnost výchozí _false_ a označuje, že toto pravidlo programy odchozí překládat pomocí SNAT pro přidružené virtuální počítače ve fondu back-end pravidlo Vyrovnávání zatížení.  Toto můžete změnit tak, aby _true_ zabránit Vyrovnávání zatížení pomocí IP adresy přidružené front-endu pro odchozí připojení pro virtuální počítač je ve fondu back-end toto pravidlo Vyrovnávání zatížení.  A konkrétní IP adresu pro odchozí toky můžete stále také určit, jak je popsáno v [několika kombinaci scénáře](#combinations) také.

#### <a name="load-balancer-basic"></a>Basic nástroje pro vyrovnávání zatížení

Základní nástroje pro vyrovnávání zatížení vybere jeden front-endu má být použit pro odchozí toky při [více (veřejné) frontends IP](load-balancer-multivip-overview.md) jsou kandidáty pro odchozí toky. Tento výběr není konfigurovatelné a měli byste zvážit algoritmus výběr být náhodné. Konkrétní IP adresu pro odchozí toky můžete určit, jak je popsáno v [několika kombinaci scénáře](#combinations).

### <a name="az"></a> Dostupnost zóny

Při použití [standardní Vyrovnávání zatížení s dostupnost zóny](load-balancer-standard-availability-zones.md), zónově redundantní frontends může poskytnout zónově redundantní odchozí připojení překládat pomocí SNAT a překládat pomocí SNAT programování odolává selhání zóny.  Když se použije oblastmi frontends, odchozí připojení překládat pomocí SNAT rozklad sdílet s zóny, ke které patří.

## <a name="snat"></a>Princip překládat pomocí SNAT a Jan

### <a name="pat"></a>Port podvržený překládat pomocí SNAT (Jan)

Pokud prostředek veřejné nástroje pro vyrovnávání zatížení je spojen s instancí virtuálních počítačů, je přepsaná každý zdroj odchozí připojení. Zdroj je přepisuje z virtuální sítě privátní adresní prostor IP adres, aby front-endu veřejná IP adresa služby Vyrovnávání zatížení. V veřejný adresní prostor IP adres 5-n-tice toku (zdrojové IP adresy, zdrojového portu, přenosového protokolu IP, cílové IP adresy, cílový port) musí být jedinečný.  Podvržený překládat pomocí SNAT portu můžete použít s protokoly TCP nebo UDP IP.

Dočasné porty (porty překládat pomocí SNAT) se používají k dosáhnout po přepisování privátní zdrojovou IP adresu, protože více toků pocházejí z jedné veřejné IP adresy. 

Jeden port překládat pomocí SNAT obsazením za toku do jedné cílové IP adresy, portu a protokolu. Pro více toků na stejnou cílovou IP adresu, port a protokol využívá každý tok jediný port překládat pomocí SNAT. Zajistíte, že tyto toky jsou jedinečné, když budou pocházet ze stejné veřejnou IP adresu, přejděte na stejnou cílovou IP adresu, port a protokol. 

Více toků, každý na jinou cílovou IP adresu, port a protokol, sdílet jeden port překládat pomocí SNAT. Cílovou IP adresu, port a protokol zkontrolujte toky jedinečný bez nutnosti dalších zdrojových portů k rozlišení toků v veřejný adresní prostor IP adres.

Když vyčerpání prostředků překládat pomocí SNAT port, odchozí toky nezdaří, dokud existující toky verze porty překládat pomocí SNAT. Nástroj pro vyrovnávání zatížení získá překládat pomocí SNAT porty, když toku zavře a používá [časový limit nečinnosti 4minutové](#idletimeout) pro opětovného získání překládat pomocí SNAT porty z nečinnosti toků.

Vzory pro zmírnění podmínky, které běžně vést k vyčerpání port překládat pomocí SNAT, přečtěte si [Správa překládat pomocí SNAT](#snatexhaust) části.

### <a name="preallocatedports"></a>Předběžné přidělení dočasných portů pro port vydávají překládat pomocí SNAT (Jan)

Azure používá algoritmus určit počet souhrnů ještě neumístěných překládat pomocí SNAT porty, které jsou k dispozici na základě velikosti fondu back-end při použití portu podvržený překládat pomocí SNAT ([PAT](#pat)). Překládat pomocí SNAT porty jsou dočasné porty, které jsou k dispozici pro konkrétní veřejné zdrojové adresy IP.

Stejný počet překládat pomocí SNAT porty jsou souhrnů ještě neumístěných UDP a TCP v uvedeném pořadí a využívat nezávisle na přenosového protokolu IP. 

>[!IMPORTANT]
>Standardní SKU SNAT programování je za přenosový protokol IP a jsou odvozené z pravidlo Vyrovnávání zatížení.  Pokud pravidlo Vyrovnávání zatížení TCP existuje pouze, je k dispozici pro TCP pouze překládat pomocí SNAT. Pokud máte pouze TCP pravidlo Vyrovnávání zatížení a nutnost UDP odchozí překládat pomocí SNAT, vytvořte pravidla ze stejné front-endu do stejného fondu back-end pro vyrovnávání zatížení UDP.  Tím se aktivuje programování pro UDP překládat pomocí SNAT.  Pracovní pravidlo nebo stav testu se nevyžaduje.  Základní SKU překládat pomocí SNAT vždy programů překládat pomocí SNAT pro obě transportního protokolu IP, bez ohledu na přenosový protokol zadaný v pravidlo Vyrovnávání zatížení.

Azure preallocates překládat pomocí SNAT porty ke konfiguraci IP adresy síťového adaptéru každý virtuální počítač. Při konfiguraci IP adres je přidán do fondu, jsou porty překládat pomocí SNAT souhrnů ještě neumístěných pro tuto konfiguraci IP adresy na základě velikosti fondu back-end. Když se vytváří odchozí toky, [PAT](#pat) dynamicky využívá (až předběžně přidělené limit) a uvolní tyto porty toku zavře nebo [časové limity nečinnosti](#ideltimeout) dojít.

V následující tabulce jsou uvedeny preallocations port překládat pomocí SNAT pro úrovně velikosti fondu back-end:

| Velikost fondu (instance virtuálních počítačů) | Předběžně přidělené překládat pomocí SNAT porty na konfiguraci protokolu IP|
| --- | --- |
| 1 – 50 | 1,024 |
| 51 až 100 | 512 |
| 101-200 | 256 |
| 201 – 400 | 128 |
| 401 – 800 | 64 |
| 1000 801 | 32 |

>[!NOTE]
> Při použití standardní Vyrovnávání zatížení s [více frontends](load-balancer-multivip-overview.md), [každou IP adresu front-endu vynásobí počet dostupných portů překládat pomocí SNAT](#multivipsnat) v předchozí tabulce. Například fond back-end 50 Virtuálního počítače s 2 pravidla vyrovnávání zátěže, každý s samostatný front-end IP adresy, bude používat porty překládat pomocí SNAT 2048 (2 x 1 024) na konfiguraci protokolu IP. Získáte v podrobnostech o [více frontends](#multife).

Mějte na paměti, že počet dostupných portů překládat pomocí SNAT nepřekládá přímo na toků. Jediný port překládat pomocí SNAT lze znovu použít pro více cílů jedinečný. Porty jsou využívat pouze v případě, že je nutné, aby toky jedinečný. Pokyny k návrhu a zmírnění dopadů, naleznete v části o [postup správy tento prostředek vyčerpatelným](#snatexhaust) a oddíl, který popisuje [PAT](#pat).

Změna velikosti fondu back-end může mít vliv na některé vaše zavedených toků. Pokud velikost fondu back-end zvyšuje a přejde do s další vrstvou, polovinu vaší předběžně přidělené překládat pomocí SNAT porty jsou uvolnit při přechodu do další vrstvou větší fond back-end. Toky, které jsou spojeny s regenerovaný portem překládat pomocí SNAT vypršení časového limitu, který se musí znovu navázat. Pokud dojde k pokusu o nové toku, bude toku okamžitě úspěšné, dokud předběžně přidělené porty jsou k dispozici.

Pokud velikost fondu back-end snižuje a přejde do nižší úrovně, zvyšuje počet dostupných portů překládat pomocí SNAT. V takovém případě existující přidělené překládat pomocí SNAT porty a jejich odpovídajících toky neovlivní.

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
[Dočasné porty](#preallocatedports) používá pro [PAT](#pat) jsou vyčerpatelným prostředků, jak je popsáno v [samostatný virtuální počítač bez Instance úroveň veřejnou IP adresu](#defaultsnat) a [Vyrovnávání zatížení sítě virtuálních počítačů bez Instance úroveň veřejnou IP adresu](#lb).

Pokud víte, že jste zahajování mnoho odchozí připojení TCP nebo UDP na stejnou cílovou IP adresu a port, a doporučujeme pomocí podpory, že jste vyčerpává porty překládat pomocí SNAT nebo sledovat selhání odchozí připojení (souhrnů ještě neumístěných [dočasných porty](#preallocatedports) používá [PAT](#pat)), máte několik možností obecné zmírnění dopadů. Přečtěte si tyto možnosti a rozhodnout, jaký je k dispozici a nejvhodnější pro váš scénář. Je možné, že jeden nebo více může pomoct spravovat tento scénář.

Pokud máte potíže seznámení s chováním odchozí připojení, můžete použít IP zásobník statistiky (netstat). Nebo může být užitečné, abyste mohli pozorovat chování připojení pomocí paketu zachycení. Můžete provádět tyto záznamy o paketů v hostovaný operační systém instanci služby nebo použít [sledovací proces sítě pro zachytávání paketů](../network-watcher/network-watcher-packet-capture-manage-portal.md).

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
Přiřazení splnění změní váš scénář [Instance úroveň veřejnou IP adresu pro virtuální počítač](#ilpip). Všechny dočasné porty veřejné IP adresy, které se používají pro jednotlivé virtuální počítače jsou k dispozici pro virtuální počítač. (Na rozdíl od scénáře kde sdílené dočasné porty veřejnou IP adresu s všechny virtuální počítače přidružené k příslušné back-end fondu.) Existují kompromisy vzít v úvahu, například další náklady na veřejné IP adresy a potenciální dopad vytvoření seznamu povolených velký počet jednotlivé IP adresy.

>[!NOTE] 
>Tato možnost není k dispozici pro webové role pracovního procesu.

#### <a name="multifesnat"></a>Použití více frontends

Pokud používáte veřejný Standard pro vyrovnávání zatížení, přiřadíte [více front-endovou IP adres pro odchozí připojení](#multife) a [vynásobte počet dostupných portů překládat pomocí SNAT](#preallocatedports).  Potřebujete vytvořit front-endové konfigurace protokolu IP, pravidla a fond back-end pro aktivaci programování překládat pomocí SNAT na veřejné IP front-endu.  Pravidlo nemusí fungovat a test stavu nemusí být úspěšné.  Pokud používáte více frontends pro příchozí i (a nikoli jen pro odchozí), měli byste použít vlastní stavu sondy a zajistit spolehlivost.

>[!NOTE]
>Ve většině případů je vyčerpání překládat pomocí SNAT portů znaménkem chybný návrhu.  Ujistěte se, že rozumíte, proč se vyčerpává porty před použitím více frontends přidání portů překládat pomocí SNAT.  Může být maskování problém, což může vést k selhání později.

#### <a name="scaleout"></a>Horizontální navýšení kapacity

[Souhrnů ještě neumístěných porty](#preallocatedports) jsou přiřazené podle velikosti fondu back-end a seskupené do vrstev, abyste minimalizovali dopad při některé porty muset znovu přidělit, aby dokázala pojmout další vrstvou velikost větší fond back-end.  Můžete mít možnost zvýšit intenzitou využití portu překládat pomocí SNAT pro danou front-endu škálování fondu back-end pro maximální velikost v dané vrstvě.  To vyžaduje pro aplikace pro efektivní škálování.

Například 2 virtuální počítače ve fondu back-end by měla mít 1024 překládat pomocí SNAT porty pro konfiguraci protokolu IP, která umožňuje celkem překládat pomocí SNAT 2048 porty pro nasazení k dispozici.  Pokud nasazení bylo možné zvýšit na 50 virtuální počítače, i když počet souhrnů ještě neumístěných konstanta zůstanou porty na virtuální počítač, celkem 51,200 (50 x 1 024) překládat pomocí SNAT porty lze toto nasazení.  Pokud chcete horizontální škálování vašeho nasazení, zkontrolujte počet [souhrnů ještě neumístěných porty](#preallocatedports) za vrstvy, abyste měli jistotu, utvářejí vaše škálování na maximum pro odpovídající úroveň.  V příkladu předcházející, pokud jste nastavili škálovat na 51 místo 50 instancí vaší by průběhu další vrstvou a end až s menší překládat pomocí SNAT porty na virtuální počítač také jako součet.

Naopak škálování na další větší back-end fondu velikost vrstvy potenciálně odchozí připojení Pokud přidělené porty muset znovu přidělit.  Pokud nechcete, aby to proběhla, budete muset utvářejí nasazení tak, aby velikost vrstvy.  Případně zajistěte, aby vaše aplikace zjistit a opakujte podle potřeby.  Může být užitečné udržování otevřených připojení protokolu TCP v rozpoznat, kdy překládat pomocí SNAT porty už funkce z důvodu se opětovnému přidělení.

### <a name="idletimeout"></a>Použít udržování otevřených připojení k resetování odchozí časový limit nečinnosti

Odchozí připojení mít časový limit nečinnosti 4 minuty. Tento časový limit není upravit. Však můžete přenos (například TCP udržování otevřených připojení) nebo udržování otevřených připojení aplikační vrstvě aktualizujte tok nečinnosti a obnovit tento časový limit nečinnosti v případě potřeby.  

Při použití udržování otevřených připojení TCP, je dostatečná a umožňuje jim na jedné straně připojení. Například je dostačující je povolit na straně serveru pouze na resetovat časovač nečinnosti toku a není nutné pro obě strany initiated udržování otevřených připojení TCP.  Podobně jako koncepty pro aplikační vrstvu, včetně konfigurace klienta se serverem databáze neexistují.  Zkontrolujte na straně serveru, pro jaké možnosti pro konkrétní udržování otevřených připojení aplikace neexistují.

## <a name="discoveroutbound"></a>Zjišťování veřejnou IP adresu, která používá virtuální počítač
Existuje mnoho způsobů, jak určit IP adresu zdrojového veřejné odchozí připojení. OpenDNS poskytuje službu, která můžete zobrazit veřejnou IP adresu vašeho virtuálního počítače. 

Pomocí příkazu nslookup můžete odeslat dotaz DNS pro název myip.opendns.com do překladače OpenDNS. Služba vrátí zdrojové IP adresy, který se používá k odeslání dotazu. Když spustíte následující dotaz z virtuálního počítače, je odpověď na veřejné IP adresy použít pro tento virtuální počítač:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Nelze navázat odchozí připojení
Někdy je žádoucí pro virtuální počítač povolit vytváření odchozího toku. Nebo může být požadavek na správu, které cíle je dostupný odchozí toky nebo které cíle zahájit příchozí toky. V takovém případě můžete použít [skupin zabezpečení sítě](../virtual-network/security-overview.md) ke správě cílů, které mohou být využity virtuálního počítače. Skupiny Nsg můžete použít také ke správě, který veřejné cílové můžete zahájit příchozí toky.

Když použijete skupinu NSG k virtuálnímu počítači s vyrovnáváním zatížení, věnovat pozornost [služby značky](../virtual-network/security-overview.md#service-tags) a [výchozí pravidla zabezpečení](../virtual-network/security-overview.md#default-security-rules). Je nutné zajistit, že virtuální počítač může přijímat žádosti o kontrolu stavu z nástroje pro vyrovnávání zatížení Azure. 

Pokud skupina NSG blokuje žádostí o stav testu z výchozí značka AZURE_LOADBALANCER, selže test stavu vašeho virtuálního počítače a virtuálního počítače je označena. Nástroj pro vyrovnávání zatížení zastaví odesílání nové toky do tohoto virtuálního počítače.

## <a name="limitations"></a>Omezení
- DisableOutboundSnat není k dispozici možnost, při konfiguraci portálu pravidlo Vyrovnávání zatížení.  Místo toho použijte nástroje REST, šablonu nebo klienta.
- Webové role pracovního procesu bez virtuální sítě a další služby platformy Microsoft může být přístupné, pokud se používá pouze k interním standardní pro vyrovnávání zatížení z důvodu vedlejším účinkem z jak pre-VNet služby a jiné platformy služeb funkce. Nesmí spoléhají na tomuto vedlejšímu efektu jako příslušné služby sám sebe nebo základní platformu mohou změnit bez předchozího upozornění. Musí vždy předpokládat, že je nutné v případě potřeby při použití k interní standardní pro vyrovnávání zatížení pouze explicitně vytvořit odchozí připojení. [Výchozí překládat pomocí SNAT](#defaultsnat) scénář 3 popsané v tomto článku není k dispozici.

## <a name="next-steps"></a>Další postup

- Další informace o [nástroj pro vyrovnávání zatížení](load-balancer-overview.md).
- Další informace o [Load Balanceru úrovně Standard](load-balancer-standard-overview.md).
- Další informace o [skupin zabezpečení sítě](../virtual-network/security-overview.md).
- Přečtěte si o některých dalších klíče [sítě možnosti](../networking/networking-overview.md) v Azure.
