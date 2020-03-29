---
title: 'Poradce při potížích s výkonem síťového propojení: Azure'
description: Tato stránka obsahuje standardizovanou metodu testování výkonu síťového propojení Azure.
services: expressroute
author: tracsman
ms.service: expressroute
ms.topic: article
ms.date: 12/20/2017
ms.author: jonor
ms.custom: seodec18
ms.openlocfilehash: bb68919fba731caa32dcca3f4c991b8881afc6f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74869642"
---
# <a name="troubleshooting-network-performance"></a>Poradce při potížích s výkonem sítě
## <a name="overview"></a>Přehled
Azure nabízí stabilní a rychlé způsoby připojení z místní sítě k Azure. Malí i velcí zákazníci při provozu svých firem v Azure úspěšně využívají metody jako site-to-site VPN nebo ExpressRoute. Ale co se stane, když výkon nesplňuje vaše očekávání nebo předchozí zkušenosti? Tento dokument může pomoci standardizovat způsob testování a účaří konkrétního prostředí.

Tento dokument ukazuje, jak můžete snadno a konzistentně testovat latenci sítě a šířku pásma mezi dvěma hostiteli. Tento dokument také poskytuje některé rady o způsobech, jak se podívat na síť Azure a pomoci izolovat problémové body. Skript prostředí PowerShell a popisované nástroje vyžadují dva hostitele v síti (na obou koncích testovaného propojení). Jeden hostitel musí být Windows Server nebo Desktop, druhý může být windows nebo linux. 

>[!NOTE]
>Přístup k řešení potíží, nástroje a použité metody jsou osobní preference. Tento dokument popisuje přístup a nástroje, které často beru. Váš přístup se pravděpodobně bude lišit, není nic špatného na různých přístupech k řešení problémů. Pokud však nemáte zavedený přístup, tento dokument vám může pomoci začít s cestou k vytváření vlastních metod, nástrojů a předvoleb pro řešení problémů se sítí.
>
>

## <a name="network-components"></a>Komponenty sítě
Než začnete řešit problémy, probereme některé běžné termíny a součásti. Tato diskuse zajišťuje, že přemýšlíme o každé součásti v řetězci end-to-end, která umožňuje připojení v Azure.
![1][1]

Na nejvyšší úrovni popisuji tři hlavní síťové směrovací domény;

- síť Azure (modrý mrak vpravo)
- internet nebo WAN (zelený mrak uprostřed)
- firemní síť (broskvový mrak vlevo)

Při pohledu na diagram zprava doleva, pojďme stručně diskutovat o každé součásti:
 - **Virtuální počítač** – server může mít více nic, zajistit všechny statické trasy, výchozí trasy a nastavení operačního systému jsou odesílání a přijímání provozu tak, jak si myslíte, že je. Každá skladová položka virtuálního virtuálního souboru má také omezení šířky pásma. Pokud používáte menší skladovou položku virtuálního počítače, je váš provoz omezen šířkou pásma dostupnou pro nic. Obvykle používám DS5v2 pro testování (a poté odstraním jednou provedenou testováním, abych ušetřil peníze), abych zajistil dostatečnou šířku pásma na virtuálním domě.
 - **NIC** – Ujistěte se, že znáte privátní IP adresu, která je přiřazena k dané nic.
 - **Nic NSG** - může být konkrétní nsg použity na úrovni nic, ujistěte se, že sada pravidel nsg je vhodná pro provoz, který se pokoušíte předat. Například zajistěte, aby porty 5201 pro iPerf, 3389 pro RDP nebo 22 pro SSH byly otevřené, aby umožnily průchod testovacího provozu.
 - **Podsíť virtuální sítě** – síťová síť je přiřazena k určité podsíti, ujistěte se, že víte, která z nich a pravidla přidružená k této podsíti.
 - **NSG podsítě** – stejně jako nic, skupiny nsg lze použít také v podsíti. Ujistěte se, že sada pravidel nsg je vhodná pro přenos, který se pokoušíte předat. (Pro přenosy příchozí do síťové sítě podsítě NSG platí nejprve, pak nic NSG, naopak pro přenosy odchozí z virtuálního zařízení nic nsg platí nejprve pak podsítě NSG vstoupí do hry).
 - **Podsíť UDR** – Uživatelem definované trasy mohou směrovat provoz na zprostředkující směrování (například bránu firewall nebo nástroj pro vyrovnávání zatížení). Ujistěte se, že víte, jestli je UDR na místě pro váš provoz, a pokud ano, kde to jde a co to další hop udělá pro váš provoz. (například brána firewall může projít některým provozem a odepřít další provoz mezi stejnými dvěma hostiteli).
 - **Podsíť brány / NSG / UDR** – stejně jako podsíť virtuálních můe, podsíť brány může mít skupiny nsg a UDR. Ujistěte se, že víte, jestli tam jsou a jaké účinky mají na váš provoz.
 - **Brána virtuální sítě (ExpressRoute)** – po povolení partnerského vztahu (ExpressRoute) nebo VPN není mnoho nastavení, která mohou ovlivnit jak nebo jestli dopravní trasy. Pokud máte více okruhů ExpressRoute nebo tunelů VPN připojených ke stejné bráně virtuální sítě, měli byste si být vědomi nastavení hmotnosti připojení, protože toto nastavení ovlivňuje předvolbu připojení a ovlivňuje cestu, kterou se řídí váš provoz.
 - **Filtr trasy** (není zobrazen) – Filtr trasy se vztahuje pouze na partnerský vztah Microsoftu na ExpressRoute, ale je důležité zkontrolovat, zda nevidíte trasy, které očekáváte v partnerské společnosti Microsoft. 

V tomto okamžiku jste na WAN část odkazu. Tato směrovací doména může být vaším poskytovatelem služeb, podnikovou sítěm WAN nebo internetem. Mnoho chmel, technologie a společnosti zapojené s těmito odkazy může být poněkud obtížné řešit. Často pracujete vyloučit Azure a vaše podnikové sítě nejprve před přechodem do této kolekce společností a směrování.

V předchozím diagramu je zcela vlevo vaše podniková síť. V závislosti na velikosti vaší společnosti může být tato směrovací doména několik síťových zařízení mezi vámi a sítí WAN nebo více vrstvami zařízení v síti campus/enterprise.

Vzhledem ke složitosti těchto tří různých síťových prostředí na vysoké úrovni je často optimální začít na okrajích a pokusit se ukázat, kde je výkon dobrý a kde se snižuje. Tento přístup může pomoci identifikovat problém směrování domény tři a potom zaměřit řešení potíží na konkrétní prostředí.

## <a name="tools"></a>Nástroje
Většinu problémů se sítí lze analyzovat a izolovat pomocí základních nástrojů, jako je ping a traceroute. Je to vzácné, že musíte jít tak hluboko jako analýza paketů jako Wireshark. Chcete-li pomoci s řešením potíží, Azure Connectivity Toolkit (AzureCT) byl vyvinut tak, aby některé z těchto nástrojů v jednoduchém balíčku. Pro testování výkonu, rád používám iPerf a PSPing. iPerf je běžně používaný nástroj a běží na většině operačních systémů. iPerf je dobré pro základní testy výkonů a je poměrně snadné použití. PSPing je ping nástroj vyvinutý SysInternals. PSPing je snadný způsob, jak provádět ICMP a TCP ping v jednom také snadno použitelný příkaz. Oba tyto nástroje jsou lehké a jsou "nainstalovány" jednoduše tím, že zvládá soubory do adresáře na hostiteli.

Všechny tyto nástroje a metody jsem zabalil do modulu PowerShell (AzureCT), který můžete nainstalovat a použít.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT – sada nástrojů pro připojení Azure
Modul AzureCT PowerShell má dvě součásti [testování dostupnosti][Availability Doc] a [testování výkonu][Performance Doc]. Tento dokument se týká pouze testování výkonu, takže umožňuje zaměřit se na dva příkazy výkonu propojení v tomto modulu Prostředí PowerShell.

Existují tři základní kroky pro použití této sady nástrojů pro testování výkonu. 1) Nainstalujte modul PowerShell, 2) Nainstalujte podpůrné aplikace iPerf a PSPing 3) Spusťte test výkonu.

1. Instalace modulu PowerShellu

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Tento příkaz stáhne modul PowerShell a nainstaluje jej místně.

2. Instalace podpůrných aplikací
    ```powershell
    Install-LinkPerformance
    ```
    Tento příkaz AzureCT nainstaluje iPerf a PSPing v novém adresáři "C:\ACTTools", otevře také porty brány Windows Firewall pro povolení přenosu ICMP a portu 5201 (iPerf).

3. Spuštění testu výkonu

    Za prvé, na vzdáleném hostiteli je nutné nainstalovat a spustit iPerf v režimu serveru. Také ujistěte se, že vzdálený hostitel naslouchá buď na 3389 (RDP pro Windows) nebo 22 (SSH pro Linux) a umožňuje provoz na portu 5201 pro iPerf. Pokud vzdálený hostitel je windows, nainstalujte AzureCT a spusťte příkaz Install-LinkPerformance nastavit iPerf a pravidla brány firewall potřebné ke spuštění iPerf v režimu serveru úspěšně. 
    
    Jakmile je vzdálený počítač připraven, otevřete powershell v místním počítači a spusťte test:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Tento příkaz spustí řadu souběžných testů zatížení a latence, které pomáhají odhadnout kapacitu šířky pásma a latenci síťového propojení.

4. Zkontrolujte výstup testů

    Výstupní formát Prostředí PowerShell vypadá podobně jako:

    ![4][4]

    Podrobné výsledky všech testů iPerf a PSPing jsou v jednotlivých textových souborech v adresáři nástrojů AzureCT na adrese "C:\ACTTools".

## <a name="troubleshooting"></a>Řešení potíží
Pokud test výkonu neposkytuje očekávané výsledky, zjišťuje, proč by měl být progresivní krok za krokem proces. Vzhledem k počtu součástí v cestě, systematický přístup obecně poskytuje rychlejší cestu k řešení než skákání kolem a potenciálně zbytečně dělá stejné testování vícekrát.

>[!NOTE]
>Scénář je zde problém s výkonem, nikoli problém s připojením. Kroky by byly jiné, kdyby provoz vůbec neprocházel.
>
>

Za prvé, zpochybnit své předpoklady. Je vaše očekávání rozumné? Například pokud máte okruh ExpressRoute 1 Gb/s a latenci 100 ms, je nerozumné očekávat plný chod 1 Gb/s vzhledem k výkonnostním charakteristikám protokolu TCP přes odkazy s vysokou latencí. Další [informace o předpokladech](#references) výkonu naleznete v části Reference.

Dále doporučuji začít na okrajích mezi směrovacími doménami a pokusit se izolovat problém na jednu hlavní doménu směrování; podnikové sítě, sítě WAN nebo sítě Azure. Lidé často obviňují "černou skříňku" v cestě, zatímco obviňování černé skříňky je snadné, může to výrazně zpozdit řešení, zejména pokud je problém ve skutečnosti v oblasti, kterou máte možnost provádět změny. Ujistěte se, že si due diligence před předáním svého poskytovatele služeb nebo ISP.

Jakmile určíte hlavní doménu směrování, která zřejmě obsahuje problém, měli byste vytvořit diagram dané oblasti. Buď na tabuli, poznámkovém bloku nebo v ina visiu jako diagram poskytuje konkrétní "bitevní mapu", která umožňuje metodický přístup k dalšímu izolátu problému. Můžete plánovat testovací body a aktualizovat mapu, jak si jasné oblasti nebo kopat hlouběji, jak testování postupuje.

Nyní, když máte diagram, začněte rozdělit síť na segmenty a zúžit problém. Zjistěte, kde to funguje a kde ne. Pokračujte v přesouvání testovacích bodů, abyste se izolovali na problematickou složku.

Nezapomeňte se také podívat na další vrstvy modelu OSI. Je snadné se zaměřit na síť a vrstvy 1 - 3 (fyzické, datové a síťové vrstvy), ale problémy mohou být také ve vrstvě 7 v aplikační vrstvě. Mějte otevřenou mysl a ověřte předpoklady.

## <a name="advanced-expressroute-troubleshooting"></a>Řešení potíží s Advanced ExpressRoute
Pokud si nejste jisti, kde ve skutečnosti je okraj cloudu, izolace součástí Azure může být problém. Při použití ExpressRoute je okraj síťovou součástí nazvanou Microsoft Enterprise Edge (MSEE). **Při použití ExpressRoute**, MSEE je první kontaktní místo do sítě společnosti Microsoft a poslední směrování opuštění sítě společnosti Microsoft. Když vytvoříte objekt připojení mezi bránou virtuální sítě a okruhem ExpressRoute, ve skutečnosti vytváříte připojení k MSEE. Rozpoznání MSEE jako první nebo poslední směrování (v závislosti na směru, kterým se ubíráte) je důležité pro izolaci problémů azure network buď dokázat problém je v Azure nebo dále po proudu v SÍTI WAN nebo podnikové sítě. 

![2][2]

>[!NOTE]
> Všimněte si, že MSEE není v cloudu Azure. ExpressRoute je ve skutečnosti na okraji sítě Microsoft není ve skutečnosti v Azure. Jakmile jste připojeni k ExpressRoute k MSEE, jste připojeni k síti Microsoftu, odtud můžete přejít na některou z cloudových služeb, jako je Office 365 (s Microsoft Peering) nebo Azure (s Privátní a/nebo Partnerský vztah Microsoftu).
>
>

Pokud jsou dvě virtuální sítě (virtuální sítě A a B v diagramu) připojené ke **stejnému** okruhu ExpressRoute, můžete provést řadu testů k izolování problému v Azure (nebo dokázat, že není v Azure)
 
### <a name="test-plan"></a>Plán zkoušek
1. Spusťte test Get-LinkPerformance mezi VM1 a VM2. Tento test poskytuje přehled o tom, zda je problém místní nebo ne. Pokud tento test přináší přijatelnou latenci a výsledky šířky pásma, můžete označit místní síť virtuální sítě jako dobrou.
2. Za předpokladu, že místní provoz virtuální sítě je dobrý, spusťte test Get-LinkPerformance mezi VM1 a VM3. Tento test procvičuje připojení prostřednictvím sítě Microsoftu až do MSEE a zpět do Azure. Pokud tento test přináší přijatelnou latenci a výsledky šířky pásma, můžete síť Azure označit jako dobrou.
3. Pokud azure je vyloučeno, můžete provést podobnou posloupnost testů v podnikové síti. Pokud to také dobře testuje, je čas spolupracovat s poskytovatelem služeb nebo poskytovatelem služeb Internetu na diagnostice připojení WAN. Příklad: Spusťte tento test mezi dvěma pobočkami nebo mezi stolem a serverem datového centra. V závislosti na tom, co testujete, najít koncové body (servery, počítače, atd.), které můžete vykonávat tuto cestu.

>[!IMPORTANT]
> Je důležité, abyste si u každého testu označili denní dobu, kdy test spustíte, a zaznamenáte výsledky do společného umístění (líbí se mi OneNote nebo Excel). Každý testovací běh by měl mít stejný výstup, takže můžete porovnat výsledná data napříč testovacími běhy a nemají "díry" v datech. Konzistence mezi více testy je primární důvod, proč používám AzureCT pro řešení potíží. Kouzlo není v přesné zatížení scénáře, které jsem spustit, ale místo toho *kouzlo* je skutečnost, že jsem si *konzistentní test a výstup dat* z každého testu. Záznam času a konzistentní data pokaždé, když je zvláště užitečné, pokud později zjistíte, že problém je sporadický. Buďte pečliví se svým sběrem dat předem a vyhnete se hodinám opakovaného testování stejných scénářů (naučil jsem se to tvrdě před mnoha lety).
>
>

## <a name="the-problem-is-isolated-now-what"></a>Problém je izolovaný, co teď?
Čím více můžete izolovat problém, tím snazší je opravit, ale často se dostanete do bodu, kdy nemůžete jít hlouběji nebo dále s řešením problémů. Příklad: zobrazí se odkaz napříč poskytovatelem služeb, který provádí směrování po Evropě, ale vaše očekávaná cesta je v Asii. Tento bod je, když byste měli oslovit o pomoc. Kdo se zeptáte, je závislá na doméně směrování, kterou jste izolovali problém, nebo ještě lépe, pokud jste schopni zúžit na konkrétní součást.

V případě problémů s podnikovou sítí může s konfigurací nebo opravou hardwaru pomoci interní oddělení IT nebo poskytovatel služeb podporující vaši síť (který může být výrobcem hardwaru).

V případě sítě WAN může sdílení výsledků testování s poskytovatelem služeb nebo poskytovatelem služeb Internetu pomoci začít a vyhnout se pokrytí některých stejných důvodů, které jste již testovali. Nenechte se však urazit, pokud chtějí ověřit vaše výsledky sami. "Důvěřovat, ale ověřit" je dobré motto při řešení problémů na základě hlášených výsledků jiných lidí.

S Azure, jakmile izolovat problém v co nejpodrobněji, jak jste schopni, je čas zkontrolovat [dokumentaci k síti Azure][Network Docs] a pak v případě potřeby [otevřít lístek podpory][Ticket Link].

## <a name="references"></a>Odkazy
### <a name="latencybandwidth-expectations"></a>Očekávání latence/šířky pásma
>[!TIP]
> Geografická latence (míle nebo kilometry) mezi koncovými body, které testujete, je zdaleka největší součástí latence. Zatímco je zařízení latence (fyzické a virtuální komponenty, počet směrování, atd.), geografie se ukázala být největší součástí celkové latence při práci s připojení wan. Je také důležité si uvědomit, že vzdálenost je vzdálenost vlákno spustit není přímka nebo cestovní mapa vzdálenost. Tato vzdálenost je neuvěřitelně těžké se dostat s přesností. V důsledku toho obecně používám kalkulátor vzdálenosti města na internetu a vím, že tato metoda je hrubě nepřesné opatření, ale stačí nastavit obecné očekávání.
>
>

Mám expressroute nastavení v Seattlu, Washington v USA. V následující tabulce je uvedena latence a šířka pásma, které se mi podařilo protestovat do různých umístění Azure. Odhadl jsem geografickou vzdálenost mezi jednotlivými konci testu.

Nastavení testu:
 - Fyzický server se systémem Windows Server 2016 s 10 Gb/s NIC připojenýkem k okruhu ExpressRoute.
 - Okruh Premium ExpressRoute 10 Gb/s v umístění označeném privátním partnerským vztahem je povolen.
 - Virtuální síť Azure s bránou UltraPerformance v zadané oblasti.
 - Virtuální počítač DS5v2 se systémem Windows Server 2016 na virtuální síti. Virtuální počítač byl připojen bez domény, postavený z výchozí image Azure (bez optimalizace nebo přizpůsobení) s nainstalovanou AzureCT.
 - Všechny testování bylo pomocí příkazu AzureCT Get-LinkPerformance s 5 minut zátěžový test pro každý ze šesti spuštění testu. Například:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - Tok dat pro každý test měl zatížení toku z místního fyzického serveru (iPerf klient v Seattlu) až do virtuálního počítače Azure (iPerf server v uvedené oblasti Azure).
 - Data sloupce "Latence" jsou z testu bez zatížení (test latence Protokolu TCP bez spuštění iPerf).
 - Data sloupce "Maximální šířka pásma" pocházejí z testu zatížení toku 16 TCP s velikostí okna 1 MB.

![3][3]

### <a name="latencybandwidth-results"></a>Výsledky latence/šířky pásma
>[!IMPORTANT]
> Tato čísla jsou pouze orientační. Latenci ovlivňuje mnoho faktorů, a zatímco tyto hodnoty jsou obecně konzistentní v průběhu času, podmínky v rámci Azure nebo sítě poskytovatelů služeb mohou kdykoli odesílat provoz různými cestami, takže latence a šířka pásma mohou být ovlivněny. Obecně účinky těchto změn nemají za následek významné rozdíly.
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>Umístění|Azure<br/>Region (Oblast)|Odhadované<br/>Vzdálenost (km)|Latence|1 Relace<br/>Šířka pásma|Maximum<br/>Šířka pásma|
| Seattle | USA – západ 2        |    191 km |   5 ms | 262,0 Mbits/s |  3,74 Gbitů/s |
| Seattle | USA – západ          |  1 094 km |  18 ms |  82,3 Mbits/s |  3,70 Gbitů/s |
| Seattle | USA – střed       |  2 357 km |  40 ms |  38,8 Mb/s |  2,55 Gbitů/s |
| Seattle | USA – středojih |  2 877 km |  51 ms |  30,6 Mb/s |  2,49 Gbitů/s |
| Seattle | USA – středosever |  2 792 km |  55 ms |  27,7 Mbits/s |  2.19 Gbity/s |
| Seattle | USA – východ 2        |  3 769 km |  73 ms |  21,3 Mbits/s |  1,79 Gbitů/s |
| Seattle | USA – východ          |  3 699 km |  74 ms |  21,1 Mbits/s |  1,78 Gbitů/s |
| Seattle | Japonsko – východ       |  7 705 km | 106 ms |  14,6 Mbits/s |  1,22 Gbitů/s |
| Seattle | Spojené království – jih         |  7 708 km | 146 ms |  10,6 Mb/s |   896 Mbits/s |
| Seattle | Západní Evropa      |  7 834 km | 153 ms |  10,2 Mbits/s |   761 Mbits/s |
| Seattle | Austrálie – východ   | 12 484 km | 165 ms |   9,4 Mbits/s |   794 Mbits/s |
| Seattle | Jihovýchodní Asie   | 12 989 km | 170 ms |   9,2 Mbits/s |   756 Mbits/s |
| Seattle | Brazílie – jih *   | 10 930 km | 189 ms |   8,2 Mbits/s |   699 Mb/s |
| Seattle | Indie – jih      | 12 918 km | 202 ms |   7,7 Mbits/s |   634 Mbits/s |

\*Latence do Brazílie je dobrým příkladem, kde se vzdálenost přímky výrazně liší od vzdálenosti běhu vlákna. Očekával bych, že latence bude v okolí 160 ms, ale ve skutečnosti je 189 ms. Tento rozdíl oproti mému očekávání by mohlo znamenat problém sítě někde, ale s největší pravděpodobností, že vlákno běh nejde do Brazílie v přímce a má navíc 1000 km nebo tak cesty se dostat do Brazílie ze Seattlu.

## <a name="next-steps"></a>Další kroky
1. Stáhněte si sadu nástrojů pro připojení Azure z GitHubu na adrese[https://aka.ms/AzCT][ACT]
2. Postupujte podle pokynů pro [testování výkonu odkazu][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "Síťové komponenty Azure"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "Řešení potíží s expressroute"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "Testovací prostředí Perf"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "Výstup prostředí PowerShell"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT
