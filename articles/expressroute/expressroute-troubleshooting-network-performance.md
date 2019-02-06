---
title: 'Řešení potíží s výkonem virtuální sítě: Azure | Dokumentace Microsoftu'
description: Tato stránka poskytuje standardizované způsob testování výkonu propojení sítí Azure.
services: expressroute
author: tracsman
ms.service: expressroute
ms.topic: article
ms.date: 12/20/2017
ms.author: jonor
ms.custom: seodec18
ms.openlocfilehash: dfd55ac12587cf99cc3fc3ff8eac4f4572229396
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753495"
---
# <a name="troubleshooting-network-performance"></a>Řešení potíží s výkonem sítě
## <a name="overview"></a>Přehled
Azure poskytuje stabilní a rychlými prvky pro připojení z vaší místní sítě k Azure. Metody, jako jsou VPN typu Site-to-Site a ExpressRoute se úspěšně používají zákazníci malé i velké při provozu svých firem v Azure. Ale co se stane, když výkon nesplňuje vaše očekávání nebo předchozí zkušenosti? Tento dokument může pomoct standardizovat standardní hodnoty a způsob, jakým testujete konkrétní prostředí.

Tento dokument ukazuje, jak můžete snadno a konzistentně otestovat latence sítě a šířku pásma mezi dvěma hostiteli. Tento dokument obsahuje také některé Rady o způsobech síť Azure a pomáhají izolovat problém body. Skript prostředí PowerShell a nástroje popsané vyžadují dva hostitele v síti (na jednom konci odkazu testování). Jeden z hostitelů musí být Windows Server nebo Desktopu, druhý může být Windows nebo Linux. 

>[!NOTE]
>Přístup k řešení potíží, nástroje a metody jsou osobní preference. Tento dokument popisuje přístup a nástroje, které obvykle trvat. Váš přístup se budou pravděpodobně lišit, není nic špatného různé přístupy k řešení problémů. Ale pokud nemáte zavedený přístup, tento dokument můžete začít pracovat na cestu k vytváření vlastních metod, nástrojů a předvolby pro řešení potíží s problémy se sítí.
>
>

## <a name="network-components"></a>Síťové součásti
Než si podrobně řešení potíží s probereme, probereme některé běžné pojmy a komponent. Tato diskuse se zajistí, že jsme udělali o jednotlivých součástech začátku do konce řetězce, který umožňuje připojení v Azure.
[![1]][1]

Na nejvyšší úrovni můžu popisují tři hlavní síťové směrování domény;

- sítě Azure (modré cloud na pravé straně)
- Internet nebo síť WAN (zelená cloud v centru)
- v podnikové síti (broskvoněmi cloud na levé straně)

Prohlížení diagramu zprava doleva, Pojďme se stručně jednotlivé komponenty:
 - **Virtuální počítač** – server může mít více síťových adaptérů, zkontrolujte všechny statické trasy a výchozí trasy a nastavení operačního systému se odesílají a přijímají data tak, jak si myslíte, že je. Každý skladovou Položku virtuálního počítače má také omezení šířky pásma. Pokud používáte menší skladovou Položku virtuálního počítače, provoz je omezena šířka pásma dostupná na síťový adaptér. Používám obvykle DS5v2 testování (a následně odstranit až dokončíte testování ušetřit) zajistit odpovídající šířku pásma na virtuálním počítači.
 - **Síťová karta** – zajistěte vědět, který je přiřazen k síťové KARTĚ dotyčný privátní IP.
 - **Skupina zabezpečení sítě se síťovou kartu** – existuje může být konkrétní skupiny Nsg na úrovni síťové karty, ujistěte se, je vhodné pro provoz, který zkoušíte předat sadu pravidel skupiny zabezpečení sítě. Například zkontrolujte porty 5201 pro iPerf, port 3389 pro RDP nebo jsou otevřené pro povolení provozu testu předat 22 pro SSH.
 - **Virtuální síť podsíť** – The síťová karta je přiřazená v konkrétní podsíti, ujistěte se, víte, které jedna a pravidla přidružená k této podsíti.
 - **Skupina NSG podsítě** – stejně jako síťové karty, skupiny zabezpečení sítě je možné použít na podsíť i. Zkontrolujte sadu pravidel skupiny zabezpečení sítě je vhodné pro provoz, který se snažíte předat. (pro přenosy příchozích na síťový adaptér podsíť, ve které skupina NSG použije první pak síťové karty, skupiny zabezpečení sítě, a naopak provozu odchozího z virtuálního počítače platí nejprve síťové karty, skupiny zabezpečení sítě a skupina NSG podsítě vstupu do play).
 - **Podsíť směrování definovaného uživatelem** -trasy definované uživatelem můžete směrovat provoz do střední směrování (jako jsou brány firewall nebo nástroj pro vyrovnávání zatížení). Ujistěte se, že budete vědět, jestli je trasu UDR v místě pro provoz a pokud tak kam putují a co udělá tuto další segment směrování do provozu. (například Brána firewall může předat část provozu a odepřít ostatní přenosy mezi dvěma hostitelskými počítači stejné).
 - **Podsíť brány / NSG / UDR** – stejně jako podsíť virtuálních počítačů, podsíť brány můžou mít skupin zabezpečení sítě a trasy definované uživatelem. Ujistěte se, že budete vědět, pokud jsou nějaké a efekty, které mají v provozu.
 - **Brány virtuální sítě (ExpressRoute)** – po povolení VPN nebo partnerského vztahu (ExpressRoute), nejsou k dispozici řadu nastavení, které můžou ovlivnit jak nebo, pokud přenos dat trasy. Pokud máte víc okruhů ExpressRoute nebo VPN tunely připojené ke stejné bráně virtuální sítě, měli vzít v potaz nastavení váhy připojení jako tato předvolba připojení ovlivňuje nastavení a má vliv cestu, která přijímá provoz.
 - **Směrovat filtr** (není vidět) - filtr tras pouze platí pro Microsoft Peering v ExpressRoute, ale je velmi důležité zkontrolovat, pokud se nezobrazují trasy očekáváte, že na Microsoft Peering. 

V tomto okamžiku jste na síť WAN část odkazu. Tuto doménu směrování může být váš poskytovatel služeb, vaší podnikové sítě WAN nebo z Internetu. Mnoho segmentů směrování, technologie a společnostem, které jsou součástí těchto odkazů může ztížit nich do jisté míry k řešení potíží. Často při práci na pravidla Azure a vaší podnikové sítě nejprve než si řekneme, do této kolekce společností a směrování.

Úplně nalevo na předchozím obrázku, je vaší podnikové síti. V závislosti na velikosti vaší společnosti lze tuto doménu směrování mezi vám a sítě WAN nebo více vrstev zařízením v síti campus/enterprise několik síťových zařízení.

Vzhledem ke složitosti tyto tři různé základní síťové prostředí, je často optimální začínají na okraji a pokusí zobrazit, kde je dobrý výkon a kde snižuje. Tento přístup může pomoct identifikovat problém směrování domény ze tří a pak zaměřit se řešení potíží v daném konkrétním prostředí.

## <a name="tools"></a>Nástroje
Většinu problémů se sítí mohou být analyzovány a izolované pomocí základní nástroje, jako je ping a traceroute. Je vzácné, že budete muset přejít hlouběji analýza paketů jako Wireshark. Kvůli odstraňování potíží, byla vyvinuta vložit některé z těchto nástrojů, snadno balíčku Azure připojení Toolkit (AzureCT). Pro testování výkonnosti, chci používat iPerf a PSPing. iPerf je nástroj často používaný a běží na většině operačních systémů. iPerf je vhodný pro základní funkční testy a poměrně snadno používá. PSPing je nástroj ping vypracovanou organizací cccppf SysInternals. PSPing je snadný způsob, jak provést příkazy ping protokolu ICMP a TCP v jednom příkazu také snadno používá. Oba tyto nástroje jsou jednoduché a jsou "nainstalováno" jednoduše tak, že kopírování souborů do adresáře na hostiteli.

Do modulu prostředí PowerShell (AzureCT), který můžete nainstalovat a používat jste zabalené všechny tyto nástroje a metody

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT - Azure připojení Toolkit
Modul AzureCT PowerShell má dvě součásti [testování dostupnosti] [ Availability Doc] a [testování výkonu][Performance Doc]. Tento dokument je jenom obeznámeni s testováním výkonnosti, takže umožňuje zaměřit se na dva příkazy výkonu odkaz v tomto modulu prostředí PowerShell.

Existují tři základní kroky, chcete-li použít tato sada nástrojů pro testování výkonu. (1) nainstalujte modul prostředí PowerShell, 2) nainstalovat podpůrné iPerf aplikací a PSPing 3) spusťte test výkonnosti.

1. Instalace modulu prostředí PowerShell

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Tento příkaz stáhne modulu prostředí PowerShell a nainstaluje ho místně.

2. Instalace podporovaných aplikací
    ```powershell
    Install-LinkPerformance
    ```
    Tento příkaz AzureCT nainstaluje iPerf a PSPing v novém adresáři "C:\ACTTools", také otevře porty brány Windows Firewall povolit protokol ICMP a portem 5201 provoz (iPerf).

3. Spustit test výkonnosti

    Nejprve na vzdáleném hostiteli musíte nainstalovat a spustit iPerf v režimu serveru. Ujistěte se také na vzdáleného hostitele naslouchá buď 3389 (RDP pro Windows) nebo 22 (SSH pro Linux) a povolení provozu na portu 5201 pro iPerf. Pokud na vzdáleného hostitele je systém windows, nainstalujte AzureCT a spuštěním příkazu Install-LinkPerformance nastavení iPerf a pravidla brány firewall, které jsou potřebné ke spuštění iPerf v režimu serveru úspěšně. 
    
    Jakmile vzdálený počítač připravený, v místním počítači, otevřete PowerShell a spusťte test:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Tento příkaz spustí řadu souběžných zatížení a testů latence pomoct s odhadem kapacitu šířky pásma a latencí síťové propojení.

4. Prohlédněte si výstup testů

    Vypadá podobně jako formát výstupu prostředí PowerShell:

    [![4]][4]

    Podrobné výsledky všech iPerf a PSPing testy jsou v jednotlivých textových souborů v adresáři AzureCT nástroje na "C:\ACTTools."

## <a name="troubleshooting"></a>Řešení potíží
Pokud test výkonnosti neposkytuje je očekávané výsledky, tím, proč musí být progresivní Podrobný proces. Zadaný počet součástí v cestě, nabízí systematický přístup obecně rychlejší cestu k řešení než přechod kolem a potenciálně zbytečně to stejné testování více než jednou.

>[!NOTE]
>Scénář v tomto poli je problému s výkonem, není problém s připojením. Kroky by lišit, pokud nebyla vůbec předáváte provoz.
>
>

Nejprve vybízí předpokladů. Je přijatelné vaše očekávání? Například pokud už máte okruh ExpressRoute 1 GB/s a 100 ms, latence je odpor očekávat úplné 1 GB/s provozu zadané charakteristiky výkonu protokolu TCP přes odkazy s vysokou latencí. Zobrazit [odkazuje na část](#references) pro další předpoklady na výkon.

Dále doporučujeme počínaje okraje mezi domény směrování a zkuste a izolovat daný problém na jeden hlavní domény směrování. v podnikové síti, sítě WAN nebo síť Azure. Lidé často viny "černé skříňky" v cestě, při blaming černé skříňky je jednoduché, může výrazně zdržet rozlišení zejména v případě, že problém je ve skutečnosti v oblasti, budete mít možnost provádět změny. Zajistěte, aby že se vypršení platnosti nejopatrněji před předáním váš poskytovatel služeb nebo poskytovatele internetových služeb.

Jakmile identifikujete hlavní doménu směrování, která se zobrazí tak, aby obsahovala problém, byste měli vytvořit diagram oblasti nejistá. Buď na tabuli, Poznámkový blok nebo jako diagram Visia obsahuje konkrétní "zocelenou mapy" Povolit metodický přístup k dalším izolovat problém. Můžete naplánovat testovací body a aktualizovat mapu, jak vymazat oblasti nebo podrobnější průběhu testování.

Teď, když máte diagramu rozdělit do segmentů sítě, a zúžit problém. Zjistěte, ve kterém funguje a kde nepodporuje. Přesouvat testovacích bodů izolovat dolů problematický komponenty.

Také nezapomeňte se podívat na jiných úrovních modelu OSI. Je snadné zaměřit se na síť a vrstvy 1-3 (fyzické, datech a síťové vrstvy) ale problémů může být také nahoru na vrstvě 7 v aplikační vrstvě. Ponechat otevřené paměti a ověření předpokladů.

## <a name="advanced-expressroute-troubleshooting"></a>Pokročilé řešení potíží s ExpressRoute
Pokud si nejste jisti, kde ve skutečnosti je edge cloudu, izolace komponenty Azure může být složité. Když se používá služba ExpressRoute, je na hraničních zařízeních sítě komponenta s názvem Microsoft Enterprise Edge (MSEE). **Při použití ExpressRoute**, směrovači MSEE je první bod kontaktu do sítě společnosti Microsoft a poslední segment směrování opouští síť společnosti Microsoft. Když vytvoříte objekt připojení mezi bránou virtuální sítě a okruh ExpressRoute, ve skutečnosti děláte připojení směrovači MSEE. Uvědomili si směrovači MSEE jako první nebo poslední segment směrování (v závislosti na směru budete) je zásadní význam pro izolaci sítí Azure se nezdařilo buď prokázat, že problém je v Azure nebo další směru server-klient v síti WAN nebo podnikové síti. 

[![2]][2]

>[!NOTE]
> Všimněte si, že směrovači MSEE není v cloudu Azure. ExpressRoute je ve skutečnosti na hranici sítě Microsoftu, které nejsou ve skutečnosti v Azure. Jakmile budete připojeni pomocí připojení ExpressRoute, aby směrovači MSEE jste připojeni k síti společnosti Microsoft, odtud můžete přejít na libovolnou z cloudových služeb, jako je Office 365 (s Microsoft Peering) nebo Azure (s privátní a/nebo Microsoft Peering).
>
>

Pokud dvě virtuální sítě (virtuální sítě A a B v diagramu) jsou připojené k **stejné** okruh ExpressRoute, můžete provádět řadu testů, které budou izolovat daný problém v Azure (nebo prokázat, není v Azure)
 
### <a name="test-plan"></a>Testovací plán
1. Spusťte test Get-LinkPerformance až VM1, VM2. Tento test nabízí pohled na, pokud se jedná o místní nebo ne. Pokud tento test vytvoří přijatelnou latencí a šířkou pásma výsledky, můžete označit místní sítě k virtuální síti jako v pořádku.
2. Za předpokladu, že místní virtuální sítě provoz je dobré, spuštění testu Get-LinkPerformance mezi VM1 a VM3. Tento test vykonává připojení prostřednictvím sítě Microsoft dolů směrovači MSEE a zpět do Azure. Pokud tento test vytvoří přijatelnou latencí a šířkou pásma výsledky, je síť Azure označit jako v pořádku.
3. Pokud je vyloučena Azure, můžete provádět podobné pořadí testů v podnikové síti. Pokud také testy dobře, je čas pracovat s poskytovatele služeb nebo poskytovatele internetových služeb k diagnostice připojení WAN. Příklad: Spuštění tohoto testu mezi dvě pobočky nebo mezi vašeho helpdesku a server datového centra. V závislosti na tom, co testujete, najít koncových bodů (servery, počítače, atd.), který můžete také vyzkoušet tuto cestu.

>[!IMPORTANT]
> Je důležité pro každý test označit čas spuštění testu a poznamenejte si výsledky ve společném umístění (chci Onenotu nebo v Excelu). Každého testovacího běhu by měl mít identické výstup vám umožní porovnat Výsledná data napříč testovací běhy a není nutné "děr" v datech. Konzistence napříč více testů je primární důvod, proč že můžu používat AzureCT pro řešení potíží. Kouzlo není ve scénářích přesné zátěžových mohu spustit, ale místo toho *magic* je skutečnost, že můžu získat *konzistentní výstup testu a data* z každého testu. Zaznamenání času a s konzistentní data pokaždé, když jeden je zvláště užitečné, pokud později zjistíte, že problém je sporadické. Být pečlivé se shromažďováním dat ještě před zahájením a budete vyhnout hodin opakované stejné scénáře (jsem zjistila tento přiznejme po mnoho let před).
>
>

## <a name="the-problem-is-isolated-now-what"></a>Problém je izolovaný, co teď?
Čím může izolovat daný problém tím snazší se opravit, ale často kontaktovat bod, ve kterém nemůže přejít hlouběji nebo další při řešení vašich potíží. Příklad: odkaz napříč váš poskytovatel služeb trvá segmenty směrování přes Evropa, ale očekávaném umístění je všechny v Asii. Tento bod je při obraťte se nápovědu. Kdo požádáte je závislá na doménu směrování, který jste izolovali problém nebo ještě lepší, pokud je to možné, chcete-li zúžit na konkrétní součást.

Pro problémy v podnikové síti může být pomoct s zařízení konfigurace nebo hardware oprava interní IT oddělení nebo poskytovatel služeb podpoře vaší sítě (který může být výrobce hardwaru).

Síť WAN sdílení výsledků testování se váš poskytovatel služeb nebo poskytovatele internetových služeb může pomoct je spuštěna a vyhnout se některé stejného základu, které jste otestovali již pokrývá. Ale není možné offended Pokud chtějí ověřit výsledky sami. "Vztahu důvěryhodnosti, ale ověřte" je dobré motto při řešení potíží v závislosti na výsledcích ohlášené jiných uživatelů.

S Azure, jakmile izolovat problém v co nejvíce podrobností, budete mít, je čas ke kontrole [dokumentace ke službě Azure Network] [ Network Docs] a pak v případě potřeby pořád [otevřete lístek podpory][Ticket Link].

## <a name="references"></a>Odkazy
### <a name="latencybandwidth-expectations"></a>Očekávání latence nebo šířka pásma
>[!TIP]
> Geografické latence (mil nebo kilometrů) mezi koncovými body, které testujete je zdaleka nejvyšší součástí latence. Je zahrnutých zařízení latence (fyzické a virtuální komponenty, počet segmentů směrování, atd.), Geografie ukázala jako největší součástí celkové latence při zpracování komplexnějších připojení WAN. Je také důležité si uvědomit, že vzdálenost vzdálenost fiber spuštění není lineární nebo podrobný popis vzdálenosti. Tato vzdálenost je mimořádně obtížné získat pomocí jakékoli přesnost. V důsledku toho jsem obecně použití city kalkulačky vzdálenost na Internetu a vědět, že tato metoda je grossly nepřesné míry, ale stačí nastavit obecné očekávání.
>
>

Mám instalační program ExpressRoute v Seattlu ve Washingtonu v USA. V následující tabulce jsou uvedeny latenci a šířky pásma viděl jsem testování do různých umístění Azure. Můžu jste odhadnout geografické vzdálenosti mezi každý konec prvku testu.

Nastavení testu:
 - Fyzický server s Windows serverem 2016 s a 10 GB/s síťové rozhraní, připojené k okruhu ExpressRoute.
 - Okruh Premium ExpressRoute 10 v umístění označena privátní partnerské vztahy povolena.
 - Virtuální síť Azure s bránou UltraPerformance v zadané oblasti.
 - DS5v2 virtuálního počítače s Windows serverem 2016 ve virtuální síti. Virtuální počítač byl nedoménové připojený, vytvořené z výchozí imagí Azure (bez optimalizace nebo přizpůsobení) pomocí AzureCT nainstalované.
 - Všechny testy se pomocí příkazu AzureCT Get-LinkPerformance 5 minut zátěžového testu pro každý z šesti testovacích běhů. Příklad:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - Tok dat pro každý test měl zátěž vyplývající z fyzického na místním serveru (iPerf klienta v Praze) až do virtuálního počítače Azure (iPerf server v uvedených oblast Azure).
 - Sloupec data "Latence" je z ne zátěžový test (TCP test latenci bez iPerf spuštění).
 - Sloupec data "Maximální šířka pásma" je z 16 testu zatížení tok TCP se velikost okna 1 Mb.

[![3]][3]

### <a name="latencybandwidth-results"></a>Výsledky latence nebo šířka pásma
>[!IMPORTANT]
> Tato čísla jsou obecné pouze pro referenci. Latence, ovlivňuje mnoho faktorů a tyto hodnoty jsou obecně konzistentní vzhledem k aplikacím v čase, podmínek v rámci Azure nebo v síti poskytovatele služeb mohou odesílat provoz prostřednictvím různých cest kdykoli, proto mohou být ovlivněny latencí a šířkou pásma. Efekty těchto změn není obecně platí, způsobit významné rozdíly.
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>Umístění|Azure<br/>Oblast|Odhad<br/>Distance (km)|Latence|1 relace<br/>Šířka pásma|Maximum<br/>Šířka pásma|
| Seattle | Západní USA 2        |    191 km |   5 ms | 262.0 Mbit/s |  3.74 Gbits za sekundu | 21
| Seattle | Západní USA          |  1,094 km |  18 ms |  82.3 Mbit/s |  3.70 Gbits za sekundu | 20
| Seattle | USA – střed       |  2,357 km |  40 ms |  38.8 Mbit/s |  2.55 Gbits za sekundu | 17
| Seattle | Středojižní USA |  2,877 km |  51 ms |  30.6 Mbit/s |  2.49 Gbits za sekundu | 19
| Seattle | Středoseverní USA |  2,792 km |  55 ms |  27.7 Mbit/s |  2.19 Gbits za sekundu | 18
| Seattle | Východní USA 2        |  3,769 km |  73 ms |  21.3 Mbit/s |  1.79 Gbits za sekundu | 16
| Seattle | USA – východ          |  3,699 km |  74 ms |  21.1 Mbit/s |  1.78 Gbits za sekundu | 15
| Seattle | Japonsko – východ       |  7,705 km | 106 ms |  14.6 Mbit/s |  1.22 Gbits za sekundu | 28
| Seattle | Velká Británie – jih         |  7,708 km | 146 ms |  10.6 Mbit/s |   896 Mbit/s | 24
| Seattle | Západní Evropa      |  7,834 km | 153 ms |  10.2 Mbit/s |   761 Mbit/s | 23
| Seattle | Austrálie – východ   | 12,484 km | 165 ms |   9.4 Mbit/s |   794 Mbit/s | 26
| Seattle | Jihovýchodní Asie   | 12,989 km | 170 ms |   9.2 Mbit/s |   756 Mbit/s | 25
| Seattle | Brazílie – jih *   | 10,930 km | 189 ms |   8.2 Mbit/s |   699 Mbit/s | 22
| Seattle | Indie – jih      | 12,918 km | 202 ms |   7.7 Mbit/s |   634 Mbit/s | 27

\* Latence Brazílie je typický příklad, kde lineární vzdálenost výrazně liší od fiber spustit vzdálenost. Můžu očekáváte, že by měly být v okolí 160 ms latenci, ale je ve skutečnosti 189 ms. Tento rozdíl oproti Moje očekávání může znamenat někde potíže se sítí, ale pravděpodobně, aby běžela fiber nepřekračuje Brazílie v rovné čáry a dalších 1 000 km nebo, cesty k získání Brazílie ze Seattlu.

## <a name="next-steps"></a>Další postup
1. Stáhnout z Githubu v Azure připojení Toolkit [http://aka.ms/AzCT][ACT]
2. Postupujte podle pokynů pro [propojit testování výkonu][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "azure síťových součástí"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "řešení potíží s ExpressRoute"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "prostředí pro testování výkonu"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "výstup prostředí PowerShell"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: http://aka.ms/AzCT











