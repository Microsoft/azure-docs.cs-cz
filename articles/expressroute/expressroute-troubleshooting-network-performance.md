---
title: Řešení potíží s výkonem virtuální síť Azure | Microsoft Docs
description: Tato stránka obsahuje standardizované metody testování výkonu odkaz síť Azure.
services: expressroute
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/20/2017
ms.author: jonor
ms.openlocfilehash: 56f011632a2aa3ef0632efd5ace472c0fc79a329
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2017
ms.locfileid: "27318980"
---
# <a name="troubleshooting-network-performance"></a>Řešení potíží s výkon sítě
## <a name="overview"></a>Přehled
Azure poskytuje stabilní a rychlými prvky pro připojení z vaší místní sítě do Azure. Metody jako VPN Site-to-Site a ExpressRoute jsou úspěšně používána zákazníky malé i velké ke spuštění své firmy v Azure. Ale co se stane, když výkonu nesplňuje očekávání nebo předchozí zkušenosti? Tento dokument může pomoct standardizovat způsob, jakým byste otestovat a standardních hodnot konkrétní prostředí.

Tento dokument ukazuje, jak můžete snadno a konzistentní otestovat latence sítě a šířku pásma mezi dvěma hostiteli. Tento dokument taky obsahuje některé Rady o způsobech podívejte se na síť Azure a pomůžou izolovat problém body. Skript prostředí PowerShell a nástroje, které jsou popsané vyžadují dva hostitele v síti (na libovolném konci odkazu testuje). Jeden hostitel musí být Windows Server nebo plocha, druhý může být buď Windows nebo Linux. 

>[!NOTE]
>Přístup k řešení potíží, nástroje a metody jsou osobní preference. Tento dokument popisuje přístup a nástroje, které často provést. Bude váš přístup pravděpodobně lišit, nic se různý přístup k řešení problémů. Ale pokud nemáte zavedených přístup, tento dokument můžete vám pomůžou začít na cestě k sestavení vlastní metody, nástroje a předvolby k řešení problémů se sítí.
>
>

## <a name="network-components"></a>Síťové součásti
Před tápat do řešení potíží s probereme některé běžné podmínky a součásti. Tato diskuse zajistí, že jsme udělali o jednotlivých součástech v řetězu začátku do konce, která umožňuje připojení v Azure.
[![1]][1]

Na nejvyšší úrovni I popisují tři domény směrování hlavní sítě;

- síť Azure (modré cloud na pravé straně)
- Internet nebo prostřednictvím sítě WAN (zelený cloud v centru)
- v podnikové síti (broskvoněmi cloud na levé straně)

Vyhledávání v diagramu zprava doleva, probereme stručně jednotlivé komponenty:
 - **Virtuální počítač** – server může mít několik síťových adaptérů, zkontrolujte všechny statické trasy, výchozí trasy a nastavení operačního systému jsou odesílání a příjem přenosů způsob, jak si myslíte, že je. Každý virtuální počítač SKU má také, omezení šířky pásma. Pokud používáte menší SKU virtuálních počítačů, provozu je omezena šířka pásma, která je k dispozici na síťový adaptér. Obvykle je možné použít DS5v2 pro testování (a pak odstraňte po dokončení testování jak ušetřit peníze) zajistit odpovídající šířku pásma na virtuálním počítači.
 - **Síťový adaptér** -zajistěte znáte privátní IP adresa, která je přiřazena k na síťový adaptér.
 - **Síťový adaptér NSG** – existuje může být konkrétní skupiny Nsg na úrovni síťový adaptér, ujistěte se, je vhodné pro provoz se snažíte předat sada pravidel NSG. Například zajistěte porty 5201 iPerf, 3389 pro protokol RDP, nebo jsou otevřené povolit průchod přenosů testovací 22 pro SSH.
 - **VNet Subnet** -The síťový adaptér je přiřazen do konkrétní podsítě, ujistěte se, víte, které pravidla a jedna přidružené této podsíti.
 - **Podsíť NSG** – stejně jako síťovou kartu, skupiny Nsg lze použít na podsíť i. Ujistěte se, je vhodné pro provoz, který se snažíte předat sada pravidel NSG. (pro provoz příchozí síťový adaptér NSG se použije první podsíť a pak NSG síťový adaptér, a naopak pro provoz odchozí z virtuálního počítače síťový adaptér NSG platí nejprve pak NSG podsíť stává play).
 - **Podsíť UDR** -trasy definované uživatelem může směrovat přenosy na střední směrování (jako jsou brány firewall nebo služby Vyrovnávání zatížení). Ujistěte se, že znáte, pokud je UDR v místě pro provozu a pokud, kam putují a k čemu tento další segment použít k provozu. (například Brána firewall může předat některé provozy a odepření ostatní přenosy mezi stejné dvěma hostiteli).
 - **Podsíť brány / NSG / UDR** – stejně jako podsíť virtuálních počítačů, může mít podsíť brány skupiny Nsg a udr. Ujistěte se, že víte, pokud máte nějaké a jaký vliv mají provozu.
 - **Brána virtuální sítě (ExpressRoute)** -po partnerského vztahu (ExpressRoute) nebo VPN je povoleno, nejsou k dispozici mnoho nastavení, které můžou ovlivnit jak nebo, pokud provoz trasy. Pokud máte víc okruhů ExpressRoute nebo VPN tunely připojené ke stejné bráně virtuální sítě, měli vzít v potaz nastavení váhy připojení jako toto nastavení ovlivňuje připojení předvoleb a ovlivňuje cestu, kterou trvá provozu.
 - **Směrovat filtru** (není vidět) - filtr trasy jenom platí pro Microsoft Peering v ExpressRoute, ale je velmi důležité zkontrolovat, pokud nevidíte trasy očekáváte, že na Peering společnosti Microsoft. 

V tomto okamžiku jste v části propojení WAN. Tuto doménu směrování může být poskytovatele služeb, vaší podnikové sítě WAN nebo Internetu. Mnoho segmentů směrování, technologie a společností zabývajících se tyto odkazy může ztížit poněkud řešení. Často pracujete pravidla Azure a podnikových sítích nejprve před přechod do této kolekce společností a směrování.

Na předchozím obrázku je zcela vlevo vaší podnikové síti. V závislosti na velikosti vaší společnosti lze tuto doménu směrování mezi jste a sítě WAN nebo více vrstev zařízení v síti kanceláře/enterprise několik síťových zařízení.

Vzhledem ke složitosti těchto tří různých vysoké úrovně síťové prostředí, je často optimální spustit na okrajích a pokuste se zobrazí, kde je dobrý výkon a kde snižuje. Tento přístup může pomoct identifikovat problém doménu směrování ze tří a potom se zaměřit vaše řešení potíží s na konkrétní prostředí.

## <a name="tools"></a>Nástroje
Většiny síťových problémů lze analyzovat a izolované pomocí základní nástroje, jako je ping a obejít trasu. Navíc není obvyklé, že budete muset přejít hloubky analýza paketů jako Wireshark. Pomoci při řešení, byla vyvinuta Toolkit připojení k Azure (AzureCT) se umístí některé z těchto nástrojů snadno balíčku. Pro testování výkonu, I chtěli použít iPerf a Pspingu. iPerf je nástroj pro běžně používané a spouští na většině operačních systémů. iPerf je vhodný pro testování základní přínos a je poměrně snadno použitelný. Pspingu je nástroj ping vyvinuté SysInternals. Pspingu je snadný způsob, jak provést příkazy ping ICMP a TCP do jednoho příkazu také snadno použitelný. Oba tyto nástroje jsou jednoduché a jsou "nainstalované" jednoduše tak, že kopírování souborů do adresáře na hostiteli.

Do modulu PowerShell (AzureCT), můžete nainstalovat a používat jste zabalené všech těchto nástrojů a metody

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT - nástrojů Azure připojení
Modul prostředí AzureCT PowerShell má dvě součásti [testování dostupnosti] [ Availability Doc] a [testování výkonu][Performance Doc]. Tento dokument je pouze nevadí testování výkonu, takže umožňuje zaměřit se na dva příkazy odkaz výkonu v tento modul prostředí PowerShell.

Existují tři základní kroky, chcete-li použít tato sada nástrojů pro testování výkonu. 1) nainstalujte modul prostředí PowerShell, 2) nainstalujte nástroje iPerf podpůrné aplikace a Pspingu 3) spusťte test výkonu.

1. Instalace modulu prostředí PowerShell

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Tento příkaz stáhne modul prostředí PowerShell a nainstaluje místně.

2. Instalace podpůrných aplikací
    ```powershell
    Install-LinkPerformance
    ```
    Tento příkaz AzureCT instaluje iPerf a Pspingu v nový adresář "C:\ACTTools", se rovněž otevře porty brány Windows Firewall povolí protokol ICMP a portem 5201 provoz (iPerf).

3. Spuštění testu výkonnosti

    U vzdáleného hostitele musíte nejprve nainstalovat a spustit iPerf v režimu serveru. Ujistěte se také ke vzdálenému hostiteli naslouchá na buď 3389 (RDP pro Windows) nebo 22 (SSH pro Linux) a umožňuje provoz na portu 5201 pro iPerf. Pokud je vzdálený hostitel windows, nainstalujte AzureCT a spustit příkaz Install-LinkPerformance nastavit iPerf a pravidla brány firewall potřebné ke spuštění nástroje iPerf v režimu serveru úspěšně. 
    
    Jakmile vzdálený počítač není připraven, otevřete prostředí PowerShell v místním počítači a spuštění testu:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Tento příkaz spustí řadu souběžných zatížení a testy latence a získat tak představu kapacitu šířky pásma a latence odkaz na vaši síť.

4. Zkontrolujte výstup testů

    Formát výstupu prostředí PowerShell vypadá podobně jako:

    [![4]][4]

    Podrobné výsledky všech iPerf a Pspingu testů jsou v jednotlivých textových souborů v adresáři nástroje AzureCT v "C:\ACTTools."

## <a name="troubleshooting"></a>Řešení potíží
Pokud není poskytnutí testu výkonnosti očekávané výsledky, nad tím, proč mělo progresivní Podrobný proces. Získá počet součásti v cestě, systematicky obecně poskytuje rychlejší cesta k řešení než přechod kolem a potenciálně zbytečnému provádění stejné testování vícekrát.

>[!NOTE]
>Tento scénář v tomto poli je problém výkonu, nejsou potíže s připojením. Postup by být jiné, pokud nebyla vůbec předávání provoz.
>
>

Nejprve challenge předpokladů. Je možné logicky vaše očekávání? Například pokud máte na okruh ExpressRoute 1 GB/s a 100 ms latence nepřiměřený očekávat úplné 1 GB/s provozu vzhledem k vlastnostem výkonu TCP přes odkazy vysokou latencí. Najdete v článku [odkazuje na části](#references) pro další předpoklady na výkon.

V dalším kroku doporučujeme od okraje mezi domény směrování a pokuste se izolovat daný problém na jedné hlavní domény směrování; v podnikové síti, využití sítě WAN nebo síť Azure. Lidé často viny "černé pole" v cestě, při blaming černé políčko je snadné provést, může výrazně zdržet řešení hlavně v případě, že problém je skutečně v oblasti, máte možnost provést změny. Zajistěte, aby že se vaše kvůli opatrností před předáním na poskytovateli služeb nebo poskytovatele internetových služeb.

Jakmile jste zformulovali hlavní doménu směrování, který se zobrazí tak, aby obsahovala problém, měli byste vytvořit diagram oblasti nejistá. Buď na Tabule, Poznámkový blok nebo Visio jako diagram poskytuje konkrétní "boj map" Povolit metodický přístup k další isolate problém. Můžete naplánovat body testování a aktualizujte mapy, jak vymazat oblasti nebo dig hlubší v průběhu testování.

Teď, když máte diagram, spusťte k rozdělení na segmenty sítě a problém zúžit. Zjistíte, kde funguje a kde nepodporuje. Zachovat přesunutí bodům testování izolovat dolů komponentu problematické.

Také nezapomeňte se podívat na ostatní vrstvy OSI modelu. Je snadné se zaměřují na síť a vrstvy 1 – 3 (fyzické, datech a síťové vrstvy), ale problémů může být také nahoru v vrstvy 7 v aplikační vrstvě. Ponechte otevřete paměti a zkontrolujte předpoklady.

## <a name="advanced-expressroute-troubleshooting"></a>Pokročilé řešení problémů s ExpressRoute
Pokud si nejste jistí, kde ve skutečnosti je hrany cloudu, izolace komponenty Azure může být složité. Pokud se používá služba ExpressRoute, je hranici sítě komponenty s názvem Microsoft Enterprise Edge (MSEE). **Při použití ExpressRoute**, MSEE je první bod kontaktu do sítě společnosti Microsoft a posledního směrování a sítě Microsoftu. Když vytvoříte objekt připojení mezi bránou virtuální sítě a okruh ExpressRoute, ve skutečnosti děláte připojení MSEE. Rozpozná MSEE, jako je první nebo poslední směrování (v závislosti na směru budete) je nezbytné k izolování problémy se sítí Azure buď prokázat, že problém je v Azure nebo další po proudu v síti WAN nebo podnikové síti. 

[![2]][2]

>[!NOTE]
> Všimněte si, že MSEE není v cloudu Azure. ExpressRoute je ve skutečnosti na hranici sítě Microsoft ve skutečnosti není v Azure. Po připojení s ExpressRoute pro MSEE jste připojení k síti společnosti Microsoft, zde můžete přejít k některému z cloudové služby, jako je Office 365 (se Microsoft Peering) nebo Azure (s privátní nebo Microsoft Peering).
>
>

Pokud dvě virtuální sítě (virtuální sítě A a b. v diagramu) jsou připojeny k **stejné** okruh ExpressRoute, můžete provést řadu testů izolovat daný problém v Azure (nebo prokázat, není v Azure)
 
### <a name="test-plan"></a>Testovací plán
1. Spusťte test Get-LinkPerformance mezi VM1 a virtuálního počítače 2. Tento test poskytuje náhled do, pokud tento problém není místní, nebo není. Pokud tento test vytváří přijatelná latence a výsledky šířky pásma, můžete označit jako dobré místní sítě VNet.
2. Za předpokladu, že místní virtuální síť provoz je vhodný, spusťte test Get-LinkPerformance mezi VM1 a VM3. Tento test vykonává připojení prostřednictvím sítě Microsoft dolů MSEE a zpět do Azure. Pokud tento test vytváří přijatelná latence a výsledky šířky pásma, můžete označit jako funkční síť Azure.
3. Pokud je vyloučena Azure, můžete provést podobné sekvence testů ve vaší podnikové síti. Pokud, také testy dobře, je čas pracovat s poskytovateli služeb nebo poskytovatele internetových služeb k diagnostikování připojení WAN. Příklad: Spusťte tento test mezi dvě pobočky nebo mezi svého stolu a server datového centra. V závislosti na tom, co testujete, najít koncových bodů (servery, počítače, atd.), mohou vykonávat této cestě.

>[!IMPORTANT]
> Je důležité, že pro každý test můžete označit čas spuštění testu a zaznamenejte výsledky ve společném umístění (I jako OneNote nebo v aplikaci Excel). Každého testu by měl mít identické výstupu, abyste mohli porovnat Výsledná data napříč testovací spouští a není nutné "mezery" v datech. Konzistence napříč více testů je hlavním důvodem, že je možné použít AzureCT pro řešení potíží. Magic není ve scénářích přesný zatížení spuštění, ale místo toho *magic* je skutečnost, že se zobrazila *konzistentní výstup testu a data* z každého testu. Záznam času a nutnosti pokaždé, když jeden konzistentní dat je obzvláště užitečné, pokud později zjistíte, že je výskyt občasný problém. Být péče se shromažďováním dat. předem a budete vyhnout hodin opětovném testování stejné scénáře (I naučili takto pevný mnoha lety).
>
>

## <a name="the-problem-is-isolated-now-what"></a>Problém je izolovaný, co teď?
Čím můžete izolovat daný problém tím snazší je chcete opravit, ale často dostanete bodu, kde nelze přejít hlubší nebo další s vaše řešení potíží. Příklad: klikněte na odkaz mezi poskytovatele služeb díky směrování prostřednictvím Evropa, ale očekávaném umístění je všechny v Asii. Je tento bod by měl oslovení nápovědu. Kdo se požádat je závislá na doménu směrování, které izolované problém nebo ještě lepší, pokud budete moci zúžit dolů pro konkrétní součást.

U problémů podnikové síti interní IT oddělení nebo poskytovatel služeb podpora síti (která může být výrobce hardwaru) můžou pomoci při konfiguraci zařízení nebo oprava hardwaru.

Využití sítě WAN sdílení testování výsledky s poskytovateli služeb nebo poskytovatele internetových služeb může pomoct získat jejich spuštění a vyhnout, které se vztahuje některá stejné základů, které jste již testována. Však není možné offended Pokud chtějí ověřte výsledky sami. "Vztahu důvěryhodnosti, ale ověření" je dobré mottem při řešení potíží s na základě jiní lidé hlášené výsledků.

S Azure, až izolovat problém v množství podrobností budete mít, je čas ke kontrole [dokumentaci k síti Azure] [ Network Docs] a pak v případě potřeby stále [otevřete lístek podpory][Ticket Link].

## <a name="references"></a>Odkazy
### <a name="latencybandwidth-expectations"></a>Očekávání latenci nebo šířky pásma
>[!TIP]
> Zeměpisná latence (miles nebo kilometrech) mezi koncovými body, které testujete je zdaleka největší součást latence. Zatímco související se situací je latence zařízení (fyzické a virtuální součásti, počet segmentů směrování, atd.), geography ukázal jako největší součást celkového latence při plánování práce s připojení WAN. Je také důležité si uvědomit, že je vzdálenost vzdálenost fiber spustit není lineární nebo distance silniční mapu. Tato vzdálenost je velmi obtížné získat s žádné přesnost. V důsledku toho I obecně použití kalkulačky vzdálenost města na Internetu a vědět, že tato metoda je grossly nesprávné míry, ale stačí nastavit obecné očekávání.
>
>

Mám instalaci ExpressRoute v Praze, Washington v USA. V následující tabulce jsou uvedeny latence a šířky pásma zobrazilo testování do různých umístění Azure. I jste odhadované geografické vzdálenost mezi každou konci testu.

Nastavení testu:
 - Fyzický server se spuštěným systémem Windows Server 2016 s na 10 GB/s síťového adaptéru, připojený k okruhu ExpressRoute.
 - Okruh Premium ExpressRoute – 10 GB / v umístění identifikovaný pomocí soukromého partnerského vztahu povolena.
 - Azure VNet s bránu UltraPerformance v zadané oblasti.
 - Virtuální počítač DS5v2 systémem Windows Server 2016 virtuální sítě. Virtuální počítač byl nepřipojená k doméně připojen, vytvořené z výchozí obrázek Azure (žádné optimalizace nebo přizpůsobení) s AzureCT nainstalována.
 - Všechny testy se pomocí příkazu AzureCT Get-LinkPerformance 5 minut zátěžového testu pro každou z šesti testovací spouští. Příklad:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - Tok dat pro každý test měl zátěž vyplývající z fyzického na místním serveru (iPerf klient v Praze) až do virtuálního počítače Azure (iPerf server v oblasti uvedené Azure).
 - Data ve sloupci "Latence" je z ne zátěžového testu (testu latence TCP bez iPerf spuštěná).
 - Data ve sloupci "Maximální šířky pásma" je z 16 testu zatížení toku TCP při velikosti okna 1 Mb.

[![3]][3]

### <a name="latencybandwidth-results"></a>Výsledky latenci nebo šířky pásma
>[!IMPORTANT]
> Tato čísla jsou obecné pouze pro referenci. Vliv na mnoha faktorech latence a tyto hodnoty jsou obecně konzistentní v čase, podmínky v rámci Azure nebo poskytovatelé služeb sítě mohou odesílat provoz přes různé cesty kdykoli, proto může mít vliv latencí a šířkou pásma. Obecně platí důsledky tyto změny nebudete mít za následek významné rozdíly.
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>Umístění|Azure<br/>Oblast|Odhad<br/>Vzdálenost (km)|Latence|1 relace<br/>Šířka pásma|Maximum<br/>Šířka pásma|
| Seattle | Západní USA 2        |    191 km |   5 ms | 262.0 Mbit/s |  3.74 Gbits za sekundu | 21
| Seattle | Západní USA          |  1,094 km |  18 ms |  82.3 Mbit/s |  3.70 Gbits za sekundu | 20
| Seattle | Střed USA       |  2,357 km |  40 ms |  38.8 Mbit/s |  2.55 Gbits za sekundu | 17
| Seattle | Střed USA – jih |  2,877 km |  51 ms |  30.6 Mbit/s |  2.49 Gbits za sekundu | 19
| Seattle | Střed USA – sever |  2,792 km |  55 ms |  27.7 Mbit/s |  2.19 Gbits za sekundu | 18
| Seattle | Východní USA 2        |  3,769 km |  73 ms |  21.3 Mbit/s |  1.79 Gbits za sekundu | 16
| Seattle | Východ USA          |  3,699 km |  74 ms |  21.1 Mbit/s |  1.78 Gbits za sekundu | 15
| Seattle | Japonsko – východ       |  7,705 km | 106 ms |  14.6 Mbit/s |  1.22 Gbits za sekundu | 28
| Seattle | Spojené království – jih         |  7,708 km | 146 ms |  10.6 Mbit/s |   896 Mbit/s | 24
| Seattle | Západní Evropa      |  7,834 km | 153 ms |  10.2 Mbit/s |   761 Mbit/s | 23
| Seattle | Austrálie – východ   | 12,484 km | 165 ms |   9.4 Mbit/s |   794 Mbit/s | 26
| Seattle | Jihovýchodní Asie   | 12,989 km | 170 ms |   9.2 Mbit/s |   756 Mbit/s | 25
| Seattle | Brazílie – jih *   | 10,930 km | 189 ms |   8.2 Mbit/s |   699 Mbit/s | 22
| Seattle | Indie – jih      | 12,918 km | 202 ms |   7.7 Mbit/s |   634 Mbit/s | 27

\*Latence Brazílie je dobrým příkladem, pokud lineární vzdálenost výrazně liší od fiber spustit vzdálenost. I očekáváte, že by v okolí z 160 ms latenci, ale je ve skutečnosti 189 ms. Tento rozdíl proti Moje očekávání může znamenat někde potíže se sítí, ale s největší pravděpodobností, spusťte fiber nepřekračuje Brazílie v přímce a má navíc 1000 km nebo tak cesta k získání Brazílie z Praha.

## <a name="next-steps"></a>Další postup
1. Sadu nástrojů Azure připojení stáhnout z webu GitHub na [http://aka.ms/AzCT][ACT]
2. Postupujte podle pokynů pro [odkaz testování výkonu][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "azure síťových součástí"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "řešení potíží s ExpressRoute"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "výkonu testovacího prostředí"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "výstup prostředí PowerShell"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index#pivot=services&panel=network
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: http://aka.ms/AzCT











