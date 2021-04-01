---
title: 'Řešení potíží s výkonem síťového propojení: Azure'
description: Tato stránka poskytuje standardizovanou metodu testování výkonu síťového propojení Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 01/07/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 35e080e0fe45c18ad6a6d5392e0c78b116853c3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98027464"
---
# <a name="troubleshooting-network-performance"></a>Řešení potíží s výkonem sítě
## <a name="overview"></a>Přehled
Azure nabízí stabilní a rychlý způsob připojení z vaší místní sítě do Azure. Malí i velcí zákazníci při provozu svých firem v Azure úspěšně využívají metody jako site-to-site VPN nebo ExpressRoute. Ale co se stane, když výkon nevyhovuje vašemu očekávání nebo předchozímu prostředí? Tento článek vám může přispět ke standardizaci způsobu testování a směrného plánu konkrétního prostředí.

Naučíte se, jak jednoduše a konzistentně testovat latenci sítě a šířku pásma mezi dvěma hostiteli. Také budete mít k dispozici Rady, jak si prohlédnout síť Azure, která vám pomůže izolovat problematické body. Popsané skripty a nástroje prostředí PowerShell vyžadují dva hostitele v síti (na obou koncích testovaných odkazů). Jeden hostitel musí být Windows Server nebo Desktop, druhý může být Windows nebo Linux. 

>[!NOTE]
>Přístup k řešení potíží, používaných nástrojů a metod jsou osobní preference. Tento dokument popisuje přístup a nástroje, které často přijímáte. Váš přístup se pravděpodobně bude lišit. nedošlo k žádným chybám s různými přístupy k řešení problémů. Pokud ale nemáte stanovený přístup, tento dokument vám může začít s cestou k vytváření vlastních metod, nástrojů a předvoleb pro řešení problémů se sítí.
>
>

## <a name="network-components"></a>Komponenty sítě
Než se prozkoumá do řešení potíží, probereme některé běžné výrazy a součásti. Tato diskuze zaručí, že v rámci koncového řetězce, který umožňuje připojení v Azure, uvažujete o každé komponentě.
![1][1]

Na nejvyšší úrovni je popis tří hlavních domén směrování sítě;

- síť Azure (modrý Cloud na pravé straně)
- Internet nebo WAN (zelený Cloud ve středu)
- Podniková síť (broskve v levém cloudu)

Při prohlížení diagramu zprava doleva se podíváme na krátkou součást:
 - **Virtuální počítač** – Server může mít několik síťových karet. Zajistěte, aby všechny statické trasy, výchozí trasy a nastavení operačního systému odesílaly a přijímaly provoz způsobem, který jste si myslíte. Každé SKU virtuálního počítače má také omezení šířky pásma. Pokud používáte menší SKU virtuálních počítačů, je váš provoz omezený šířkou pásma dostupnou pro síťové rozhraní. Obvykle používám DS5v2 k testování (a pak se po dokončení testování za účelem úspory peněz odstraní), aby se zajistila odpovídající šířka pásma na virtuálním počítači.
 - **Síťová karta** – Ujistěte se, že znáte privátní IP adresu, která je přiřazená k DANÉmu síťovému rozhraní.
 - **Síťové rozhraní NSG** – pro přenos, který se pokoušíte předat, se můžou použít konkrétní skupin zabezpečení sítě. Ujistěte se například, že porty 5201 pro iPerf, 3389 pro RDP nebo 22 pro SSH jsou otevřené, aby bylo možné předat testovací přenos.
 - **Podsíť virtuální sítě** – síťová karta je přiřazena konkrétní podsíti, ujistěte se, že máte jistotu, která z nich je a pravidla přidružená k této podsíti.
 - **Podsíť NSG** – stejně jako síťové rozhraní, skupin zabezpečení sítě se dá použít i v podsíti. Zajistěte, aby byla sada pravidel NSG vhodná pro přenos, který se pokoušíte předat. (Pro příchozí přenosy na síťový adaptér se jako první použije podsíť NSG a pak NSG síťové karty. Při odchozím provozu z virtuálního počítače se jako první použije NSG síťové rozhraní a použije se podsíť NSG).
 - **Podsíť udr** – trasy User-Defined můžou směrovat provoz na zprostředkující směrování (jako je brána firewall nebo nástroj pro vyrovnávání zatížení). Ujistěte se, že víte, jestli je pro váš provoz UDR. Pokud ano, pochopíte, kde to bude a co další směrování provede s vaším provozem. Například brána firewall může předat určitý provoz a odepřít jiný provoz mezi stejnými dvěma hostiteli.
 - **Podsíť brány/NSG/udr** – stejně jako podsíť virtuálních počítačů, může mít podsíť brány skupin zabezpečení sítě a udr. Ujistěte se, že víte, jestli tam jsou a jaké mají vliv na provoz.
 - **Brána virtuální sítě (ExpressRoute)** – po povolení partnerského vztahu (ExpressRoute) nebo VPN není k dispozici mnoho nastavení, které může ovlivnit, jak nebo jestli jsou trasy provozu. Pokud máte bránu virtuální sítě připojenou k několika okruhům ExpressRoute nebo tunelům VPN, měli byste si uvědomit nastavení váhy připojení. Váha připojení má vliv na Předvolby připojení a určuje cestu, kterou přenos trvá.
 - **Filtr tras** (nezobrazený) – při použití partnerského vztahu Microsoftu prostřednictvím ExpressRoute je nutný filtr tras. Pokud nepřijímáte žádné trasy, ověřte, zda je filtr tras konfigurován a správně použit pro okruh.

V tomto okamžiku se nacházíte v části WAN tohoto odkazu. Tato doména směrování může být vaším poskytovatelem služeb, firemní sítí WAN nebo internetem. Tyto odkazy se týkají mnoha segmentů směrování, zařízení a společností, což by mohlo ztížit problémy. Než budete moci prozkoumat směrování mezi, musíte nejdřív vyhodnotit Azure i firemní sítě.

V předchozím diagramu je úplně vlevo vaše podniková síť. V závislosti na velikosti vaší společnosti může být tato doména směrování několik síťových zařízení mezi vámi a sítí WAN nebo více vrstvami zařízení v síti areál/Enterprise.

Vzhledem ke složitosti těchto tří různých síťových prostředí vysoké úrovně. Je často optimální začít na okrajích a zkusit Ukázat, kde je výkon dobrý, a kde se snižuje. Tento přístup může přispět k identifikaci problému s doménou směrování těchto tří. Pak můžete své řešení potíží zaměřit na konkrétní prostředí.

## <a name="tools"></a>nástroje
Většinu problémů se sítí se dá analyzovat a izolovat pomocí základních nástrojů, jako je třeba příkazy traceroute. Je zřídka nutné jako analýzu paketů použít nástroje jako Wireshark. 

Pro pomoc s řešením potíží se vyvinula sada Azure Connectivity Toolkit (AzureCT), která do snadného balíčku tyto nástroje umísťuje. Pro testování výkonu můžou nástroje, jako je iPerf a PSPing, poskytnout informace o vaší síti. iPerf je běžně používaný nástroj pro základní testy funkční výkonnosti a je poměrně snadno použitelný. PSPing je nástroj pro použití testu, který vyvinula společnost Sysinternals. PSPing může k přístupu ke vzdálenému hostiteli použít příkazy protokolu ICMP i TCP. Oba tyto nástroje jsou odlehčené a jsou "nainstalují" jednoduše kopírování soubory do adresáře na hostiteli.

Tyto nástroje a metody jsou zabaleny do modulu PowerShellu (AzureCT), který můžete nainstalovat a použít.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT – sada nástrojů pro připojení Azure
Modul AzureCT PowerShell má dvě součásti [testování dostupnosti][Availability Doc] a [testování výkonu][Performance Doc]. Tento dokument se týká pouze testování výkonu, takže se můžete soustředit na dva příkazy výkonu propojení v tomto modulu PowerShellu.

Existují tři základní kroky pro použití této sady nástrojů pro testování výkonu. 1) nainstalujte modul PowerShellu, 2) nainstalujte podpůrné aplikace iPerf a PSPing 3) spusťte test výkonnosti.

1. Instalace modulu PowerShellu

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Tento příkaz stáhne modul prostředí PowerShell a nainstaluje ho místně.

2. Instalace podpůrných aplikací
    ```powershell
    Install-LinkPerformance
    ```
    Tento příkaz AzureCT nainstaluje iPerf a PSPing do nového adresáře "C:\ACTTools", otevře také porty brány Windows Firewall, aby bylo možné provozovat přenosy ICMP a port 5201 (iPerf).

3. Spustit test výkonnosti

    Nejdřív musíte na vzdáleném hostiteli nainstalovat a spustit iPerf v režimu serveru. Ujistěte se taky, že vzdálený hostitel naslouchá buď 3389 (RDP pro Windows), nebo 22 (SSH pro Linux), a povoluje provoz na portu 5201 pro iPerf. Pokud je vzdálený hostitel Windows, nainstalujte AzureCT a spusťte příkaz Install-LinkPerformance. Příkaz nastaví iPerf a pravidla brány firewall nutná k úspěšnému spuštění iPerf v režimu serveru. 
    
    Jakmile je vzdálený počítač připravený, otevřete v místním počítači prostředí PowerShell a spusťte test:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Tento příkaz spustí řadu souběžných testů zatížení a latencí, které vám pomůžou odhadnout kapacitu šířky pásma a latenci síťového spojení.

4. Kontrola výstupu testů

    Výstupní formát PowerShellu vypadá nějak takto:

    ![4][4]

    Podrobné výsledky testů iPerf a PSPing jsou v jednotlivých textových souborech v adresáři nástrojů AzureCT na adrese "C:\ACTTools.".

## <a name="troubleshooting"></a>Řešení potíží
Pokud test výkonnosti neposkytuje očekávané výsledky, zjistíte, proč by měl být progresivní postup s podrobnými kroky. S ohledem na počet komponent v cestě bude systematický přístup poskytovat rychlejší cestu k řešení, než je možné proejít. Pomocí systematického řešení můžete zabránit v provádění zbytečných testů vícekrát.

>[!NOTE]
>Scénář představuje problém s výkonem, nikoli problém s připojením. Postup se liší v případě, že přenos neprojde vůbec.
>
>

Nejdřív vyžádejte své předpoklady. Je vaše očekávání přijatelné? Pokud máte například okruh ExpressRoute s 1 GB/s a 100 MS latence. Nemusíte očekávat plný přenos dat s vysokou rychlostí 1 GB/s, a to s ohledem na výkonové charakteristiky odkazů TCP přes vysoké latence. Další informace o odhadech výkonu najdete v [části s odkazy](#references) .

Dále doporučujeme začít na okrajích mezi doménami směrování a zkusit izolovat problém do jedné hlavní domény směrování. Můžete začít s podnikovou sítí, sítí WAN nebo sítí Azure. Lidé v cestě často viny "černé pole". I když je černý rámeček snadno blamingý, může to významně trvat. Obzvláště v případě, že se problém nachází v oblasti, kde můžete provádět změny a problém vyřešit. Před předáním poskytovateli služeb nebo poskytovatele internetových služeb se ujistěte, že jste provedli své náležité opatrnosti.

Jakmile identifikujete hlavní doménu směrování, která zdánlivě obsahuje daný problém, měli byste vytvořit diagram příslušné oblasti. Vykreslením diagramu na tabuli, poznámkovém bloku nebo v aplikaci Visio můžete vyřešit a izolovat problém. Můžete plánovat testovací body a aktualizovat mapu při vymazání oblastí nebo dig hlouběji, jak testování probíhá.

Teď, když máte diagram, začněte rozdělit síť k segmentům a zužte problém. Zjistěte, kde funguje a kde ne. Pokračujte v přesouvání bodů testování a izolujte tak problematickou součást.

Nezapomeňte se také podívat na jiné vrstvy modelu OSI. Je snadné se zaměřit na síť a vrstvy 1-3 (fyzické, datové a síťové vrstvy), ale problémy mohou být také ve vrstvě 7 v aplikační vrstvě. Mějte na paměti, že si myslíte a ověříte předpoklady.

## <a name="advanced-expressroute-troubleshooting"></a>Pokročilé řešení potíží s ExpressRoute
Pokud si nejste jistí, kde se skutečně nachází okraj cloudu, může být izolace součástí Azure výzvou. Při použití ExpressRoute je hraniční součástí síťová komponenta s názvem Microsoft Enterprise Edge (MSEE). **Při použití ExpressRoute** je MSEE první kontaktní bod do sítě Microsoftu a poslední směrování při opustit síť Microsoftu. Když vytváříte objekt připojení mezi vaší bránou virtuální sítě a okruhem ExpressRoute, skutečně vytváříte připojení k MSEE. Rozpoznání MSEE jako prvního nebo posledního směrování v závislosti na tom, jaký směr je důležitý pro izolaci potíží se sítí Azure. Dozvíte se, který směr se ukáže, jestli se problém nachází v Azure nebo v podnikové síti v síti WAN. 

![2][2]

>[!NOTE]
> Všimněte si, že MSEE není v cloudu Azure. ExpressRoute je ve skutečnosti na hranici sítě Microsoft, která není ve skutečnosti v Azure. Až budete s ExpressRoute připojeni k MSEE, jste připojeni k síti společnosti Microsoft. potom můžete přejít k libovolné cloudové službě, jako je Microsoft 365 (se partnerským vztahem Microsoftu) nebo s Azure (s privátním a/nebo partnerským vztahem Microsoftu).
>
>

Pokud jsou dva virtuální sítě připojeny ke **stejnému** okruhu ExpressRoute, můžete provést řadu testů k izolaci problému v Azure.
 
### <a name="test-plan"></a>Testovací plán
1. Spusťte test Get-LinkPerformance mezi VM1 a VM2. Tento test vám poskytne přehled o tom, jestli je problém místní nebo ne. Pokud tento test vytváří přijatelnou latenci a výsledky šířky pásma, můžete označit místní síť VNet jako správnou.
2. Za předpokladu, že provoz místní virtuální sítě je dobrý, spusťte test Get-LinkPerformance mezi VM1 a VM3. Tento test provede připojení přes síť Microsoft k MSEE a zpátky do Azure. Pokud tento test vytváří přijatelnou latenci a výsledky šířky pásma, můžete označit síť Azure jako správnou.
3. Pokud se pravidlo Azure vyřadí, můžete v podnikové síti vyzkoušet podobnou posloupnost testů. Pokud se to také testuje dobře, je čas pracovat s poskytovatelem služeb nebo poskytovatelem internetových služeb k diagnostice připojení WAN. Příklad: Spusťte tento test mezi dvěma pobočkami nebo mezi vaší deskou a serverem datového centra. V závislosti na tom, co testujete, vyhledejte koncové body, jako jsou servery a klientské počítače, které mohou tuto cestu vykonat.

>[!IMPORTANT]
> Je velmi důležité, aby každý test vyzkoušel čas spuštění testu a záznam výsledků v běžném umístění (líbí se mi aplikace OneNote nebo Excel). Každý testovací běh by měl mít stejný výstup, aby bylo možné porovnat výsledná data napříč testovacími běhy a nesmí mít v datech "díry". Konzistence napříč několika testy je primárním důvodem, proč používám AzureCT k řešení potíží. Hodnota Magic není ve scénářích, které spouštíte, ale v takovém případě je to skutečnost, *že se z* každého a každého testu získá *konzistentní výstup testu a dat* . Záznam času a existence konzistentních dat je zvlášť užitečný, pokud později zjistíte, že se jedná o problém občas. Napečlivé vylaďování se na vaše shromažďování dat předem a vyhnete se tak hodinám při přezkoušení stejných scénářů (Tento postup jsem se naučil před několika lety).
>
>

## <a name="the-problem-is-isolated-now-what"></a>Problém je izolovaný a teď?
Čím víc se problém izoluje, tím rychleji řešení zjistíte. Při řešení potíží se dostanete k bodu, ve kterém nemůžete dál docházet. Například uvidíte propojení mezi vaším poskytovatelem služeb, které přijímá směrování přes Evropu, ale očekáváte, že cesta zůstane v Asii. V tuto chvíli byste měli někoho požádat o pomoc. Uživatel, který požadujete, závisí na doméně směrování, ke které jste tento problém izolují. Pokud je můžete zúžit na konkrétní komponentu, která by byla ještě lepší.

V případě problémů s podnikovou sítí může vaše interní oddělení IT nebo poskytovatel služeb pomáhat s konfigurací zařízení nebo s opravou hardwaru.

V případě sítě WAN vám budou pokaždé, když sdílíte výsledky testování s vaším poskytovatelem služeb nebo poskytovatelem internetových služeb, pomůže začít. Tím se také vyhnete duplikování stejné práce, kterou jste už provedli. , Pokud chtějí ověřit vaše výsledky sami, nemusíte být problematické. "Trust, ale ověřte" je dobrý mottem při řešení potíží na základě nahlášených výsledků jiných lidí.

Pokud se v Azure rozhodnete, že problém izolujete v co nejpodrobněji, je čas si projít [dokumentaci k síti Azure][Network Docs] a pak Pokud pořád potřebujeme [otevřít lístek podpory][Ticket Link].

## <a name="references"></a>Reference
### <a name="latencybandwidth-expectations"></a>Očekávání při latenci a šířce pásma
>[!TIP]
> Zeměpisná latence (v mílích nebo kilometrech) mezi koncovými body, které testujete, je nejmnohem největší součástí latence. I když se jedná o latenci zařízení (fyzické a virtuální komponenty, počet směrování atd.), je zeměpisně prověřená největší součást celkové latence při obchodování s připojeními WAN. Je také důležité si uvědomit, že vzdálenost v rámci optické dráhy není rovna vzdálenosti lineární nebo silniční mapy. Tato vzdálenost je neuvěřitelně těžko se dostat s přesností. V důsledku toho používáme pro Internet kalkulačku na dálku a víte, že tato metoda je všeobecně nepřesná míra, ale je dostačující pro nastavení Obecné očekávaného očekávání.
>
>

Mám ExpressRouteou instalaci v Seattlu, Washington v USA. Následující tabulka ukazuje latenci a šířku pásma, které jsem prokáže při testování na různá umístění Azure. Odhadl (a) jsem geografickou vzdálenost mezi každým koncem testu.

Nastavení testu:
 - Fyzický server se systémem Windows Server 2016 s síťovou kartou s rychlostí 10 GB/s, připojený k okruhu ExpressRoute.
 - Okruh peering – Premium ExpressRoute v umístění identifikovaném s povoleným privátním partnerským vztahem.
 - Virtuální síť Azure s bránou UltraPerformance v zadané oblasti.
 - Virtuální počítač s DS5v2, na kterém běží Windows Server 2016 ve virtuální síti. Virtuální počítač nebyl připojený k doméně, který je vytvořený z výchozí image Azure (bez optimalizace ani přizpůsobení) s nainstalovaným AzureCT.
 - Všechny testy používají příkaz AzureCT Get-LinkPerformance s zátěžovým testem na 5 minut pro každé šest testovacích běhů. Například:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - Tok dat pro každý test měl zátěžový tok z místního fyzického serveru (iPerf Client v Seattlu) až do virtuálního počítače Azure (iPerf Server v uvedené oblasti Azure).
 - Data sloupce "latence" nepochází z zátěžového testu (test latence TCP bez iPerf).
 - Sloupce s maximální šířkou šířky pásma jsou ze zátěžového testu toku 16 TCP s velikostí okna o velikosti 1 MB.

![3][3]

### <a name="latencybandwidth-results"></a>Výsledky latence a šířky pásma
>[!IMPORTANT]
> Tato čísla platí pouze pro obecné reference. Mnoho faktorů ovlivňuje latenci a i když jsou tyto hodnoty všeobecně konzistentní v čase, podmínky v rámci Azure nebo v síti poskytovatelů služeb mohou kdykoli odesílat přenosy přes různé cesty, takže může být ovlivněna latence a šířka pásma. Obecně platí, že vliv těchto změn nevede k významným rozdílům.
>
>

| ExpressRoute<br/>Umístění|Azure<br/>Oblast | –<br/>Vzdálenost (km) | Latence|1 relace<br/>Šířka pásma | Maximum<br/>Šířka pásma |
| ------------------------------------------ | --------------------------- |  - | - | - | - |
| Seattle | Západní USA 2        |    191 km |   5 MS | 262,0 Mbit/s |  3,74 Gbits/s |
| Seattle | USA – západ          |  1 094 km |  18 MS |  82,3 Mbit/s |  3,70 Gbits/s |
| Seattle | USA – střed       |  2 357 km |  40 MS |  38,8 Mbit/s |  2,55 Gbits/s |
| Seattle | Středojižní USA |  2 877 km |  51 MS |  30,6 Mbit/s |  2,49 Gbits/s |
| Seattle | USA – středosever |  2 792 km |  55 MS |  27,7 Mbit/s |  2,19 Gbits/s |
| Seattle | USA – východ 2        |  3 769 km |  73 MS |  21,3 Mbit/s |  1,79 Gbits/s |
| Seattle | East US          |  3 699 km |  74 MS |  21,1 Mbit/s |  1,78 Gbits/s |
| Seattle | Japonsko – východ       |  7 705 km | 106 MS |  14,6 Mbit/s |  1,22 Gbits/s |
| Seattle | Spojené království – jih         |  7 708 km | 146 MS |  10,6 Mbit/s |   896 Mbit/s |
| Seattle | West Europe      |  7 834 km | 153 MS |  10,2 Mbit/s |   761 Mbit/s |
| Seattle | Austrálie – východ   | 12 484 km | 165 MS |   9,4 Mbit/s |   794 Mbit/s |
| Seattle | Southeast Asia   | 12 989 km | 170 MS |   9,2 Mbit/s |   756 Mbit/s |
| Seattle | Brazílie – jih *   | 10 930 km | 189 MS |   8,2 Mbit/s |   699 Mbit/s |
| Seattle | Indie – jih      | 12 918 km | 202 MS |   7,7 Mbit/s |   634 Mbit/s |

\* Latence pro Brazílie je dobrým příkladem, kdy se vzdálenost lineárního øádku významně liší od vzdálenosti optického běhu. Očekávaná latence by byla na čtvrti od 160 MS, ale ve skutečnosti je 189 MS. Rozdíl v latenci by mohl označovat problém v síti někde jinde. Ale realita je, že optická čára nepřechází na Brazílii v přímé spojnici. Proto byste měli očekávat dalších 1 000 km nebo cestování, abyste se dostali do Brazílie z Seattlu.

## <a name="next-steps"></a>Další kroky
1. Stáhněte si sadu nástrojů pro připojení k Azure z GitHubu na adrese [https://aka.ms/AzCT][ACT]
2. Postupujte podle pokynů pro [propojení testu výkonu][Performance Doc] .

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "Síťové součásti Azure"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "Řešení potíží s ExpressRoute"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "Testovací prostředí výkonu"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "Výstup PowerShellu"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: ../index.yml
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT