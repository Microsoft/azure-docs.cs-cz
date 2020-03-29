---
title: Optimalizace výkonu protokolu TCP/IP pro virtuální počítače Azure | Dokumenty společnosti Microsoft
description: Naučte se různé běžné techniky ladění výkonu Protokolu TCP/IP a jejich vztah k virtuálním počítačům Azure.
services: virtual-network
documentationcenter: na
author: rimayber
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: rimayber
ms.reviewer: dgoddard, stegag, steveesp, minale, btalb, prachank
ms.openlocfilehash: bb23484903ac3ce129c6e7a7a27e0765c227fb1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68297788"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>Ladění výkonu protokolu TCP/IP pro virtuální počítače Azure

Tento článek popisuje běžné techniky ladění výkonu Protokolu TCP/IP a některé věci, které je třeba zvážit při jejich použití pro virtuální počítače spuštěné v Azure. Poskytne základní přehled technik a prozkoumá, jak je lze vyladit.

## <a name="common-tcpip-tuning-techniques"></a>Běžné techniky ladění protokolu TCP/IP

### <a name="mtu-fragmentation-and-large-send-offload"></a>MTU, fragmentace a velké odesílání nezátěže

#### <a name="mtu"></a>Mtu

Maximální přenosová jednotka (MTU) je největší velikost rámce (paket), určené v bajtů, které mohou být odeslány přes síťové rozhraní. MTU je konfigurovatelné nastavení. Výchozí mtu používaný na virtuálních počítačích Azure a výchozí nastavení na většině síťových zařízení po celém světě, je 1 500 bajtů.

#### <a name="fragmentation"></a>Fragmentace

K fragmentaci dochází při odeslání paketu, který přesahuje mtu síťového rozhraní. Zásobník Protokolu TCP/IP rozdělí paket na menší části (fragmenty), které odpovídají mtu rozhraní. Fragmentace probíhá ve vrstvě IP a je nezávislá na základním protokolu (například TCP). Při odeslání paketu o 2 000 bajtů přes síťové rozhraní s mtu 1 500 bude paket rozdělen na jeden paket o 1 500 bajtů a jeden paket o průměru 500 bajtů.

Síťová zařízení v cestě mezi zdrojem a cílem mohou buď vyřadit pakety, které překračují mtu nebo fragmentovat paket na menší části.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>Bit Nefragmentovat v paketu IP

Bit Nefragmentovat (DF) je příznak v záhlaví protokolu IP. Bit DF označuje, že síťová zařízení na cestě mezi odesílatelem a příjemcem nesmí paket fragmentovat. Tento bit může být nastaven z mnoha důvodů. (Viz část "Path MTU Discovery" tohoto článku pro jeden příklad.) Pokud síťové zařízení přijme paket s nastaveným bitem Nefragmentovat a tento paket překročí rozhraní MTU rozhraní zařízení, je standardním chováním, aby zařízení paket přeřadila. Zařízení odešle zprávu ICMP Fragmentace potřebné zpět k původnímu zdroji paketu.

#### <a name="performance-implications-of-fragmentation"></a>Důsledky fragmentace výkonu

Fragmentace může mít negativní vliv na výkon. Jedním z hlavních důvodů vlivu na výkon je dopad fragmentace a opětovné sestavení paketů na procesor/paměť. Pokud síťové zařízení potřebuje fragmentovat paket, bude muset přidělit prostředky procesoru nebo paměti k provedení fragmentace.

Totéž se stane, když je paket znovu sestaven. Síťové zařízení musí ukládat všechny fragmenty, dokud nejsou přijaty, aby je mohlo znovu sestavit do původního paketu. Tento proces fragmentace a opětovné sestavení může také způsobit latenci.

Další možné negativní důsledky fragmentace výkonu je, že fragmentované pakety mohou být doručeny mimo pořadí. Pokud jsou pakety přijaty mimo pořadí, některé typy síťových zařízení je mohou vypustit. Pokud k tomu dojde, celý paket musí být znovu odeslán.

Fragmenty jsou obvykle vynechány zabezpečovacími zařízeními, jako jsou síťové brány firewall nebo při vyčerpání vyrovnávacích pamětí pro příjem síťového zařízení. Při vyčerpání vyrovnávacích pamětí pro příjem síťového zařízení se síťové zařízení pokouší znovu sestavit fragmentovaný paket, ale nemá prostředky k uložení a opětovnému převzetí paketu.

Fragmentace může být vnímána jako negativní operace, ale podpora fragmentace je nezbytná, když připojujete různé sítě přes internet.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Výhody a důsledky úpravy MTU

Obecně řečeno, můžete vytvořit efektivnější síť zvýšením MTU. Každý přenášený paket obsahuje informace záhlaví, které jsou přidány do původního paketu. Když fragmentace vytvoří více paketů, je více režie záhlaví a to dělá síť méně efektivní.

Tady je příklad. Velikost záhlaví Ethernet je 14 bajtů plus 4bajtová sekvence kontroly rámce pro zajištění konzistence rámce. Pokud je odeslán jeden paket o 2 000 bajtů, je v síti přidáno 18 bajtů režie sítě Ethernet. Pokud je paket fragmentován na paket o objemu 1 500 bajtů a paket o objemu 500 bajtů, bude mít každý paket 18 bajtů záhlaví sítě Ethernet, celkem 36 bajtů.

Mějte na paměti, že zvýšení MTU nemusí nutně vytvořit efektivnější síť. Pokud aplikace odešle pouze 500bajtové pakety, stejná režie záhlaví bude existovat bez ohledu na to, zda je mtu 1 500 bajtů nebo 9 000 bajtů. Síť bude efektivnější pouze v případě, že používá větší velikosti paketů, které jsou ovlivněny MTU.

#### <a name="azure-and-vm-mtu"></a>Azure a MTU virtuálního počítače

Výchozí mtu pro virtuální počítače Azure je 1 500 bajtů. Zásobník virtuální sítě Azure se pokusí fragmentovat paket na 1 400 bajtů.

Všimněte si, že zásobník virtuální sítě není ze své podstaty neefektivní, protože fragmentuje pakety na 1 400 bajtů, i když virtuální počítače mají MTU 1 500. Velké procento síťových paketů je mnohem menší než 1 400 nebo 1 500 bajtů.

#### <a name="azure-and-fragmentation"></a>Azure a fragmentace

Zásobník virtuální sítě je nastaven tak, aby vyřazoval fragmenty mimo pořadí, tedy fragmentované pakety, které nedorazí v původním fragmentovaném pořadí. Tyto pakety jsou vynechány hlavně kvůli chybě zabezpečení sítě oznámené v listopadu 2018 s názvem FragmentSmack.

FragmentSmack je vada ve způsobu, jakým linuxové jádro zpracovávalo opětovné sestavení fragmentovaných paketů IPv4 a IPv6. Vzdálený útočník by mohl tuto chybu použít ke spuštění nákladných operací opětovného sestavení fragmentů, což by mohlo vést ke zvýšení procesoru a odmítnutí služby v cílovém systému.

#### <a name="tune-the-mtu"></a>Naladit MTU

Můžete nakonfigurovat Azure VM MTU, stejně jako v jakémkoli jiném operačním systému. Ale měli byste zvážit fragmentaci, ke které dochází v Azure, popsané výše, když konfigurujete MTU.

Nechceme podporovat zákazníky ke zvýšení MTU Virtuálního Měna. Tato diskuse je určena k vysvětlení podrobností o tom, jak Azure implementuje MTU a provádí fragmentaci.

> [!IMPORTANT]
>Zvýšení MTU není známo, že zlepšit výkon a může mít negativní vliv na výkon aplikace.
>
>

#### <a name="large-send-offload"></a>Velké odesílání nezatížení

Velké odesílání snižování zátěže (LSO) může zlepšit výkon sítě převedením segmentace paketů na adaptér Ethernet. Je-li povoleno připojení LSO, vytvoří zásobník protokolu TCP/IP velký paket TCP a odešle jej adaptéru Ethernet k segmentaci před jeho předáním. Výhodou LSO je, že může uvolnit procesor z segmentace paketů do velikostí, které odpovídají MTU a převést toto zpracování na ethernetové rozhraní, kde se provádí v hardwaru. Další informace o výhodách služby LSO naleznete v [tématu Podpora velkého odesílání nezatížení](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

Když je povoleno LSO, zákazníci Azure může zobrazit velké velikosti rámců při provádění zachycení paketů. Tyto velké velikosti snímků může vést některé zákazníky, aby si mysleli, fragmentace dochází, nebo že velké MTU se používá, když to není. Pomocí protokolu LSO může adaptér Ethernet inzerovat větší maximální velikost segmentu (MSS) do zásobníku protokolu TCP/IP a vytvořit tak větší paket TCP. Celý tento nesegmentovaný rámec je pak předán adaptéru Ethernet a bude viditelný v zachycení paketu prováděném na virtuálním počítači. Ale paket bude rozdělen do mnoha menších rámů ethernetovým adaptérem, podle Adaptér u adaptéru Ethernet MTU.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>TCP MSS velikost okna a PMTUD

#### <a name="tcp-maximum-segment-size"></a>Maximální velikost segmentu protokolu TCP

Maximální velikost segmentu TCP (MSS) je nastavení, které omezuje velikost segmentů TCP, což zabraňuje fragmentaci paketů TCP. Operační systémy obvykle používají tento vzorec k nastavení mss:

`MSS = MTU - (IP header size + TCP header size)`

Hlavička PROTOKOLU IP a hlavička Protokolu TCP jsou celkem 20 bajtů nebo celkem 40 bajtů. Takže rozhraní s MTU 1,500 bude mít MSS 1,460. Ale MSS je konfigurovatelný.

Toto nastavení je dohodnuto v tcp třícestný handshake při tcp relace je nastavena mezi zdrojem a cílem. Obě strany odesílají hodnotu MSS a nižší z těchto dvou se používá pro připojení TCP.

Mějte na paměti, že MTU zdroje a určení nejsou pouze faktory, které určují hodnotu MSS. Zprostředkující síťová zařízení, jako jsou brány VPN, včetně azure vpn gateway, můžete upravit MTU nezávisle na zdroji a cíli, aby byl zajištěn optimální výkon sítě.

#### <a name="path-mtu-discovery"></a>Zjišťování mtu cesty

MSS je vyjednána, ale nemusí označovat skutečné MSS, které lze použít. Důvodem je, že ostatní síťová zařízení v cestě mezi zdrojem a cílem může mít nižší hodnotu MTU než zdroj a cíl. V takovém případě zařízení, jehož MTU je menší než paket vynese paket. Zařízení odešle zpět zprávu ICMP Fragmentace (typ 3, kód 4), která obsahuje jeho MTU. Tato zpráva ICMP umožňuje zdrojovému hostiteli odpovídajícím způsobem snížit jeho mtu cesty. Proces se nazývá Path MTU Discovery (PMTUD).

Proces PMTUD je neefektivní a ovlivňuje výkon sítě. Při odeslání paketů, které přesahují mtu síťové cesty, je třeba je znovu odeslat pomocí nižší hospo- služby MSS. Pokud odesílatel neobdrží zprávu ICMP Fragmentace potřebné, možná proto, že síťová brána firewall v cestě (běžně označované jako *blackhole PMTUD*), odesílatel neví, že je třeba snížit MSS a bude průběžně přenášet paket. To je důvod, proč nedoporučujeme zvýšit MTU virtuálního počítače Azure.

#### <a name="vpn-and-mtu"></a>VPN a MTU

Pokud používáte virtuální počítače, které provádějí zapouzdření (jako jsou virtuální ny IPsec), existují některé další důležité informace týkající se velikosti paketů a MTU. Virtuální ny přidávají do paketů další hlavičky, což zvětšuje velikost paketu a vyžaduje menší službu MSS.

Pro Azure doporučujeme nastavit tcp MSS upnutí na 1 350 bajtů a rozhraní tunelového propojení MTU na 1 400. Další informace naleznete na [stránce zařízení VPN a parametrů protokolu IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Latence, doba odezvy a změna měřítka okna TCP

#### <a name="latency-and-round-trip-time"></a>Latence a doba odezvy

Latence sítě se řídí rychlostí světla v síti s optickými vlákny. Síťová propustnost protokolu TCP se také efektivně řídí dobou odezvy (RTT) mezi dvěma síťovými zařízeními.

| | | | |
|-|-|-|-|
|**Postupu**|**Vzdálenost**|**Jednosměrný čas**|**Rtt**|
|Z New Yorku do San Franciska|4 148 km|21 ms|42 ms|
|Z New Yorku do Londýna|5 585 km|28 ms|56 ms|
|Z New Yorku do Sydney|15 993 km|80 ms|160 ms|

V této tabulce je uvedena přímka mezi dvěma umístěními. V sítích je vzdálenost obvykle delší než přímka. Zde je jednoduchý vzorec pro výpočet minimální RTT, která se řídí rychlostí světla:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

Můžete použít 200 pro rychlost šíření. To je vzdálenost, v metrech, že světlo cestuje v 1 milisekundu.

Vezměme si New York do San Francisca jako příklad. Přímka je 4 148 km. Připojením této hodnoty do rovnice získáme následující:

`Minimum RTT = 2 * (4,148 / 200)`

Výstup rovnice je v milisekundách.

Chcete-li dosáhnout nejlepšího výkonu sítě, je logickou možností vybrat cíle s nejkratší vzdáleností mezi nimi. Měli byste také navrhnout virtuální síť pro optimalizaci cesty provozu a snížení latence. Další informace naleznete v části "Důležité informace o návrhu sítě" tohoto článku.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Latence a odezva na tcp

Doba odezvy má přímý vliv na maximální propustnost protokolu TCP. V protokolu TCP je *velikost okna* maximální objem přenosů, které lze odeslat prostředpou připojení TCP předtím, než odesílatel potřebuje přijmout potvrzení od příjemce. Pokud je protokol TCP MSS nastaven na 1 460 a velikost okna TCP je nastavena na 65 535, může odesílatel odeslat 45 paketů dříve, než bude muset od příjemce obdržet potvrzení. Pokud odesílatel neobdrží potvrzení, bude znovu přenášet data. Tento vzorec vypadá takto:

`TCP window size / TCP MSS = packets sent`

V tomto příkladu 65,535 / 1,460 je zaokrouhleno nahoru na 45.

Tento stav "čekání na potvrzení", mechanismus pro zajištění spolehlivého doručování dat, je to, co způsobí, že RTT ovlivnit propustnost TCP. Čím déle odesílatel čeká na potvrzení, tím déle musí čekat před odesláním dalších dat.

Tady je vzorec pro výpočet maximální propustnosti jednoho připojení TCP:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

Tato tabulka zobrazuje maximální propustnost megabajtů za sekundu jednoho připojení TCP. (Pro čitelnost se pro měrnou jednotku používají megabajty.)

| | | | |
|-|-|-|-|
|**Velikost okna TCP (bajty)**|**Latence RTT (ms)**|**Maximální propustnost megabajt/sekunda**|**Maximální propustnost megabitu/sekundy**|
|65,535|1|65.54|524.29|
|65,535|30|2.18|17.48|
|65,535|60|1.09|8.74|
|65,535|90|.73|5.83|
|65,535|120|.55|4.37|

Pokud dojde ke ztrátě paketů, sníží se maximální propustnost připojení TCP, zatímco odesílatel znovu odešle data, která již odeslal.

#### <a name="tcp-window-scaling"></a>Změna velikosti okna TCP

Změna měřítka okna TCP je technika, která dynamicky zvyšuje velikost okna TCP, aby bylo možné odeslat více dat před vyžadováním potvrzení. V předchozím příkladu by bylo odesláno 45 paketů před vyžadováním potvrzení. Pokud zvýšíte počet paketů, které mohou být odeslány před potvrzení je potřeba, snižujete počet odehraných čeká na potvrzení, což zvyšuje maximální propustnost TCP.

Tato tabulka ilustruje tyto relace:

| | | | |
|-|-|-|-|
|**Velikost okna TCP (bajty)**|**Latence RTT (ms)**|**Maximální propustnost megabajt/sekunda**|**Maximální propustnost megabitu/sekundy**|
|65,535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

Ale hodnota hlavičky TCP pro velikost okna TCP je pouze 2 bajty dlouhé, což znamená, že maximální hodnota pro okno příjmu je 65 535. Chcete-li zvýšit maximální velikost okna, byl zaveden faktor měřítka okna TCP.

Faktor měřítka je také nastavení, které můžete nakonfigurovat v operačním systému. Tady je vzorec pro výpočet velikosti okna TCP pomocí faktorů měřítka:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Zde je výpočet faktoru měřítka okna 3 a velikosti okna 65 535:

`65,535 \* (2^3) = 262,140 bytes`

Faktor měřítka 14 má za následek velikost okna TCP 14 (maximální povolená hodnota posunu). Velikost okna TCP bude 1 073 725 440 bajtů (8,5 gigabitů).

#### <a name="support-for-tcp-window-scaling"></a>Podpora změny velikosti okna TCP

Systém Windows může nastavit různé faktory měřítka pro různé typy připojení. (Třídy připojení zahrnují datové centrum, internet a tak dále.) Příkaz `Get-NetTCPConnection` PowerShell slouží k zobrazení typu připojení pro změnu velikosti okna:

```powershell
Get-NetTCPConnection
```

Pomocí příkazu `Get-NetTCPSetting` PowerShell můžete zobrazit hodnoty jednotlivých tříd:

```powershell
Get-NetTCPSetting
```

Počáteční velikost okna TCP a faktor měřítka tcp v `Set-NetTCPSetting` systému Windows můžete nastavit pomocí příkazu PowerShell. Další informace naleznete [v tématu Set-NetTCPSetting](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps).

```powershell
Set-NetTCPSetting
```

Toto jsou efektivní nastavení `AutoTuningLevel`Protokolu TCP pro:

| | | | |
|-|-|-|-|
|**Autotuninglevel**|**Faktor měřítka**|**Multiplikátor změny měřítka**|**Vzorec<br/>pro výpočet maximální velikosti okna**|
|Zakázáno|Žádný|Žádný|Velikost okna|
|S omezeným přístupem|4|2^4|Velikost okna * (2^4)|
|Vysoce omezené|2|2^2|Velikost okna * (2^2)|
|Normální|8|2^8|Velikost okna * (2^8)|
|Experimentální|14|2^14|Velikost okna * (2^14)|

Tato nastavení s největší pravděpodobností ovlivní výkon protokolu TCP, ale mějte na paměti, že výkon protokolu TCP může ovlivnit i mnoho dalších faktorů na internetu mimo kontrolu Azure.

#### <a name="increase-mtu-size"></a>Zvětšit velikost MTU

Vzhledem k tomu, že větší MTU znamená větší MSS, můžete se ptát, zda zvýšení MTU může zvýšit výkon TCP. Pravděpodobně ne. Existují klady a zápory velikosti paketů nad rámec provozu TCP. Jak již bylo popsáno dříve, nejdůležitější faktory ovlivňující výkon propustnost TCP jsou velikost okna TCP, ztráta paketů a RTT.

> [!IMPORTANT]
> Nedoporučujeme, aby zákazníci Azure změnili výchozí hodnotu MTU na virtuálních počítačích.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Zrychlené vytváření sítí a příjem boční škálování

#### <a name="accelerated-networking"></a>Urychlení sítě

Funkce sítě virtuálních strojů byly historicky náročné na procesor u hostovaného virtuálního počítače i hypervisoru/hostitele. Každý paket, který prochází hostitelem, je zpracován v softwaru hostitelským procesorem, včetně všech zapouzdření a dekapsulace virtuální sítě. Takže čím větší provoz prochází hostitelem, tím vyšší je zatížení CPU. A pokud je hostitelský procesor zaneprázdněn jinými operacemi, bude to mít vliv také na propustnost sítě a latenci. Azure řeší tento problém s akcelerované sítě.

Zrychlené sítě poskytují konzistentní ultranízkou latenci sítě prostřednictvím interního programovatelného hardwaru Azure a technologií, jako je SR-IOV. Zrychlené sítě přesune většinu softwarově definovaného síťového zásobníku Azure mimo procesory do inteligentních sítí založených na FPGA. Tato změna umožňuje aplikacím koncových uživatelů znovu získat výpočetní cykly, což klade menší zatížení na virtuální počítač, snižuje kolísání a nekonzistence latence. Jinými slovy, výkon může být více deterministický.

Zrychlené sítě zlepšuje výkon tím, že umožňuje hostu virtuálního virtuálního zařízení obejít hostitele a vytvořit cestu k datům přímo s inteligentním čipem hostitele. Zde jsou některé výhody zrychlených sítí:

- **Nižší latence / vyšší pakety za sekundu (pps):** Odebrání virtuálního přepínače z datové cesty eliminuje čas pakety stráví v hostiteli pro zpracování zásad a zvyšuje počet paketů, které mohou být zpracovány ve virtuálním počítači.

- **Snížená nervozita**: Zpracování virtuálního přepínače závisí na množství zásad, které je třeba použít, a na zatížení procesoru, který zpracovává. Překládka vynucení zásad na hardware odstraní tuto variabilitu doručením paketů přímo do virtuálního počítače, odstraněním komunikace mezi hostiteli a virtuálním počítačem a všemi přerušeními softwaru a přepnutími kontextu.

- **Snížení využití procesoru**: Vynechání virtuálního přepínače v hostiteli vede k menšímu využití procesoru pro zpracování síťového provozu.

Chcete-li používat zrychlené sítě, musíte explicitně povolit na každém příslušném virtuálním počítači. Pokyny najdete [v tématu Vytvoření linuxového virtuálního počítače se zrychlenou sítí.](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)

#### <a name="receive-side-scaling"></a>Přijímat změnu velikosti strany

Škálování na straně příjmu (RSS) je technologie síťového ovladače, která distribuuje příjem síťového provozu efektivněji distribucí zpracování příjmu mezi více procesorů ve víceprocesorovém systému. Jednoduše řečeno, RSS umožňuje systému zpracovat více přijatých přenosů, protože používá všechny dostupné procesory namísto jednoho. Pro více technické diskuse rss, viz [Úvod pro příjem boční škálování](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

Chcete-li získat nejlepší výkon při zrychlené sítě je povolena na virtuálním počítači, musíte povolit RSS. RSS může také poskytovat výhody pro virtuální počítače, které nepoužívají zrychlené sítě. Přehled, jak zjistit, jestli je povoleno RSS a jak ho povolit, najdete [v tématu Optimalizace propustnosti sítě pro virtuální počítače Azure](https://aka.ms/FastVM).

### <a name="tcp-time_wait-and-time_wait-assassination"></a>TCP TIME_WAIT a TIME_WAIT atentát

TCP TIME_WAIT je další běžné nastavení, které ovlivňuje výkon sítě a aplikace. Na zaneprázdněných virtuálních počítačích, které otevírají a zavírají mnoho soketů, buď jako klienti, nebo jako servery (zdrojový port IP:Zdrojový port + cílový port:Cílový port) během normálního provozu protokolu TCP může daný soket skončit ve stavu TIME_WAIT po dlouhou dobu. TIME_WAIT stav je určen k povolení další data, která mají být dodány na soketu před zavřením. Takže zásobníky TCP/IP obecně zabránit opakované použití soketu tichým uvolněním paketu TCP SYN klienta.

Doba, po kterou je soket v TIME_WAIT je konfigurovatelná. Může se pohybovat od 30 sekund do 240 sekund. Sokety jsou omezeným zdrojem a počet soketů, které lze použít v daném okamžiku, je konfigurovatelný. (Počet dostupných soketů je obvykle přibližně 30 000.) Pokud jsou spotřebovány dostupné sokety nebo pokud klienti a servery mají neshodné nastavení TIME_WAIT a virtuální ho virtuálního počítače se pokusí znovu použít soket ve TIME_WAIT stavu, nová připojení se nezdaří jako TCP SYN pakety jsou tiše vynechány.

Hodnota rozsahu portů pro odchozí sokety je obvykle konfigurovatelná v zásobníku protokolu TCP/IP operačního systému. Totéž platí pro nastavení TIME_WAIT TCP a opětovné použití soketu. Změna těchto čísel může potenciálně zlepšit škálovatelnost. V závislosti na situaci by však tyto změny mohly způsobit problémy s interoperabilitou. Pokud tyto hodnoty změníte, měli byste být opatrní.

K řešení tohoto omezení škálování můžete použít TIME_WAIT atentát. TIME_WAIT vražda umožňuje soket znovu použít v určitých situacích, například když pořadové číslo v paketu IP nového připojení překročí pořadové číslo posledního paketu z předchozího připojení. V takovém případě operační systém umožní navázat nové připojení (přijme nové SYN/ACK) a vynutí uzavření předchozího připojení, které bylo ve TIME_WAIT stavu. Tato funkce je podporovaná na virtuálních počítačích s Windows v Azure. Další informace o podpoře v jiných virtuálních virtuálních her, obraťte se na dodavatele operačního softwaru.

Další informace o konfiguraci nastavení protokolu TCP TIME_WAIT a rozsahu zdrojového portu naleznete v [tématu Nastavení, která lze upravit za účelem zvýšení výkonu sítě](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Faktory virtuální sítě, které mohou ovlivnit výkon

### <a name="vm-maximum-outbound-throughput"></a>Maximální odchozí propustnost virtuálního měn

Azure poskytuje různé velikosti a typy virtuálních počítačů, každý s jinou kombinací možností výkonu. Jednou z těchto funkcí je propustnost sítě (nebo šířka pásma), která se měří v megabitech za sekundu (Mb/s). Vzhledem k tomu, že virtuální počítače jsou hostovány na sdíleném hardwaru, musí být kapacita sítě spravedlivě sdílena mezi virtuálními počítači používajícími stejný hardware. Větší virtuální počítače jsou přiděleny větší šířku pásma než menší virtuální počítače.

Šířka pásma sítě přidělená každému virtuálnímu počítači se měří při odchozím (odchozím) provozu z virtuálního počítače. Veškerý síťový provoz opouštějící virtuální počítač se započítává do přiděleného limitu bez ohledu na cíl. Například pokud virtuální počítač má limit 1 000 Mb/s, toto omezení platí bez ohledu na to, zda je odchozí provoz určený pro jiný virtuální počítač ve stejné virtuální síti nebo pro jiný mimo Azure.

Příchozí přenos není měřennebo omezen přímo. Existují však další faktory, jako jsou limity procesoru a úložiště, které mohou ovlivnit schopnost virtuálního počítače zpracovávat příchozí data.

Zrychlená síť je navržena tak, aby zlepšila výkon sítě, včetně latence, propustnosti a využití procesoru. Zrychlená síť může zlepšit propustnost virtuálního počítače, ale může to udělat až do přidělené šířky pásma virtuálního počítače.

Virtuální počítače Azure mají k sobě připojené alespoň jedno síťové rozhraní. Mohli jich mít několik. Šířka pásma přidělená virtuálnímu počítači je součtem všech odchozích přenosů ve všech síťových rozhraních připojených k počítači. Jinými slovy, šířka pásma je přidělena na základě virtuálního počítače, bez ohledu na to, kolik síťových rozhraní jsou připojeny k počítači.

Očekávaná odchozí propustnost a počet síťových rozhraní podporovaných jednotlivými velikostmi virtuálních počítačů jsou podrobně popsány ve [velikostech virtuálních počítačů s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Chcete-li zobrazit maximální propustnost, vyberte typ, například **Obecné účely**, a pak vyhledejte oddíl o řadě velikostí na výsledné stránce (například "Řada Dv2"). Pro každou řadu je tabulka, která poskytuje síťové specifikace v posledním sloupci s názvem "Maximální síťové karty / Očekávaná šířka pásma sítě (Mbps)."

Limit propustnost platí pro virtuální počítač. Propustnost není ovlivněna těmito faktory:

- **Počet síťových rozhraní**: Omezení šířky pásma se vztahuje na součet všech odchozích přenosů z virtuálního počítače.

- **Zrychlené vytváření sítí**: Ačkoli tato funkce může být užitečná při dosahování publikovaného limitu, nemění limit.

- **Cíl provozu**: Všechny cíle se počítají do odchozího limitu.

- **Protokol**: Veškerý odchozí provoz ve všech protokolech se započítává do limitu.

Další informace naleznete v tématu [Šířka pásma sítě virtuálních strojů](https://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Důležité informace o výkonu internetu

Jak je popsáno v tomto článku, faktory na internetu a mimo kontrolu Azure může ovlivnit výkon sítě. Zde jsou některé z těchto faktorů:

- **Latence**: Doba odezvy mezi dvěma cíli může být ovlivněna problémy v zprostředkujících sítích, provozem, který netrvá "nejkratší" vzdálenost, a neoptimálními cestami partnerského vztahu.

- **Ztráta paketů**: Ztráta paketů může být způsobena zahlcením sítě, problémy s fyzickou cestou a nedostatečným výkonem síťových zařízení.

- **Velikost/fragmentace MTU**: Fragmentace podél cesty může vést ke zpoždění při doručení dat nebo v paketech přicházejících mimo pořadí, což může ovlivnit doručení paketů.

Traceroute je dobrý nástroj pro měření charakteristik výkonu sítě (jako je ztráta paketů a latence) podél každé síťové cesty mezi zdrojovým zařízením a cílovým zařízením.

### <a name="network-design-considerations"></a>Aspekty návrhu sítě

Spolu s aspekty popsané dříve v tomto článku topologie virtuální sítě může ovlivnit výkon sítě. Například návrh rozbočovače a paprsku, který globálně vyrovnává provoz do virtuální sítě s jedním rozbočovačem, zavede latenci sítě, což ovlivní celkový výkon sítě.

Počet síťových zařízení, kterými prochází síťový provoz, může také ovlivnit celkovou latenci. Pokud například v návrhu rozbočovače a paprsku prochází provoz virtuálním zařízením sítě paprsku a virtuálním zařízením rozbočovače před přechodem na internet, mohou virtuální síťová zařízení zavést latenci.

### <a name="azure-regions-virtual-networks-and-latency"></a>Oblasti Azure, virtuální sítě a latence

Oblasti Azure se shodnou z více datových center, které existují v rámci obecné geografické oblasti. Tato datová centra nemusí být fyzicky vedle sebe. V některých případech jsou odděleny až 10 kilometrů. Virtuální síť je logické překrytí nad sítí fyzického datového centra Azure. Virtuální síť neznamená žádnou konkrétní topologii sítě v rámci datového centra.

Například dva virtuální počítače, které jsou ve stejné virtuální síti a podsíti může být v různých racků, řádků nebo dokonce datových center. Mohly by být odděleny nohami optického kabelu nebo kilometry optického kabelu. Tato varianta může zavést proměnnou latenci (několik milisekund rozdíl) mezi různými virtuálními servery.

Geografické umístění virtuálních počítačů a potenciální výsledná latence mezi dvěma virtuálními počítači může být ovlivněna konfigurací sad dostupnosti a zón dostupnosti. Ale vzdálenost mezi datovými centry v oblasti je specifická pro oblast a primárně ovlivněna topologii datového centra v oblasti.

### <a name="source-nat-port-exhaustion"></a>Vyčerpání portu zdrojového nat

Nasazení v Azure může komunikovat s koncovými body mimo Azure na veřejném internetu nebo ve veřejném IP prostoru. Když instance iniciuje odchozí připojení, Azure dynamicky mapuje privátní IP adresu na veřejnou IP adresu. Po Azure vytvoří toto mapování, vrátí provoz pro odchozí pocházející tok můžete také dosáhnout privátní IP adresu, kde pochází toku.

Pro každé odchozí připojení azure vyrovnávání zatížení potřebuje udržovat toto mapování po určitou dobu. S víceklientské povahy Azure, udržování tohoto mapování pro každý odchozí tok pro každý virtuální počítač může být náročné na prostředky. Takže existují limity, které jsou nastaveny a na základě konfigurace virtuální sítě Azure. Nebo přesněji řečeno, virtuální počítač Azure můžete provést jenom určitý počet odchozí připojení v daném okamžiku. Po dosažení těchto omezení nebude možné vytvořit další odchozí připojení.

Ale toto chování je konfigurovatelné. Další informace o vyčerpání portů SNAT a SNAT naleznete v [tomto článku](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Měření výkonu sítě v Azure

Počet maximální výkon v tomto článku se vztahují k latence sítě / doba odezvy (RTT) mezi dvěma virtuálními servery. Tato část obsahuje některé návrhy, jak otestovat latenci/RTT a jak otestovat výkon protokolu TCP a výkon sítě virtuálních zařízení. Můžete optimalizovat a test výkonu TCP/IP a síťové hodnoty popsané dříve pomocí technik popsaných v této části. Můžete připojit latence, MTU, MSS a velikost i okna hodnoty do výpočtů uvedených dříve a porovnat teoretická maxima skutečné hodnoty, které pozorujete během testování.

### <a name="measure-round-trip-time-and-packet-loss"></a>Měření doby odezvy a ztráty paketů

Výkon protokolu TCP do značné míry závisí na RTT a ztrátě paketů. Nástroj PING dostupný v systému Windows a Linux poskytuje nejjednodušší způsob měření RTT a ztráty paketů. Výstup ping zobrazí minimální/maximální/průměrná latence mezi zdrojem a cílem. Zobrazí také ztrátu paketů. Ping ve výchozím nastavení používá protokol ICMP. PsPing můžete použít k testování TCP RTT. Další informace naleznete v tématu [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Měření skutečné propustnosti připojení TCP

NTttcp je nástroj pro testování výkonu TCP virtuálního počítače s Linuxem nebo Windows. Můžete změnit různá nastavení protokolu TCP a potom otestovat výhody pomocí protokolu NTttcp. Další informace naleznete v těchto zdrojích:

- [Testování šířky pásma/propustnost (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [Nástroj NTTTCP](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Měření skutečné šířky pásma virtuálního počítače

Můžete otestovat výkon různých typů virtuálních počítačů, zrychlené sítě a tak dále, pomocí nástroje s názvem iPerf. iPerf je také k dispozici na Linuxu a Windows. iPerf můžete použít TCP nebo UDP k testování celkové propustnost sítě. iPerf TCP propustnost testy jsou ovlivněny faktory popsané v tomto článku (jako latence a RTT). Takže UDP může přinést lepší výsledky, pokud chcete jen otestovat maximální propustnost.

Další informace najdete v těchto článcích:

- [Poradce při potížích s výkonem sítě Expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Ověření propustnosti sítě VPN do virtuální sítě](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Zjišťování neefektivního chování protokolu TCP

V zachycení paketů mohou zákazníci Azure zobrazit pakety TCP s příznaky TCP (SACK, DUP ACK, RETRANSMIT a FAST RETRANSMIT), které by mohly znamenat problémy s výkonem sítě. Tyto pakety konkrétně označují neefektivitu sítě, která je výsledkem ztráty paketů. Ale ztráta paketů nemusí být nutně způsobena problémy s výkonem Azure. Problémy s výkonem může být důsledkem problémů s aplikací, problémů s operačním systémem nebo jiných problémů, které nemusí přímo souviset s platformou Azure.

Mějte také na paměti, že některé opakované přenosy a duplicitní sady ACK jsou v síti normální. Protokoly TCP byly vytvořeny tak, aby byly spolehlivé. Důkazy o těchto paketech TCP v zachycení paketů nemusí nutně znamenat problém systémové sítě, pokud nejsou nadměrné.

Přesto tyto typy paketů jsou indikace, že propustnost Protokolu TCP není dosažení jeho maximální výkon, z důvodů popsaných v jiných částech tohoto článku.

## <a name="next-steps"></a>Další kroky

Teď, když jste se dozvěděli o optimalizaci výkonu TCP/IP pro virtuální počítače Azure, můžete si přečíst další důležité informace pro [plánování virtuálních sítí](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) nebo [získat další informace o připojení a konfiguraci virtuálních sítí](https://docs.microsoft.com/azure/virtual-network/).
