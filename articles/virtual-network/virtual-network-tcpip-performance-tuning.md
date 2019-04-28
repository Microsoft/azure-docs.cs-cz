---
title: TCP/IP optimalizace výkonu pro virtuální počítače Azure | Dokumentace Microsoftu
description: Přečtěte si různé běžné TCP/IP výkon ladění techniky a jejich vztah k virtuálním počítačům Azure.
services: virtual-network
documentationcenter: na
author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
ms.openlocfilehash: 1e8605a41cbe610c971b891309b2149d221b8b27
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032447"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>Pro virtuální počítače Azure pro optimalizaci výkonu protokolu TCP/IP

Tento článek popisuje běžné postupy optimalizace výkonu protokolu TCP/IP a některé věci k uvážení při použití pro virtuální počítače provozované v Azure. Bude poskytovat základní přehled technik a prozkoumejte, jak lze ladit.

## <a name="common-tcpip-tuning-techniques"></a>Běžné postupy optimalizace protokolu TCP/IP

### <a name="mtu-fragmentation-and-large-send-offload"></a>MTU fragmentace a snižování zátěže rozsáhlého odeslání

#### <a name="mtu"></a>MTU

Maximální přenosové jednotky (MTU) je největší velikost rámce (paket) zadaný v bajtech odeslaných prostřednictvím síťového rozhraní. MTU je konfigurovatelné nastavením. Výchozí nastavení jednotek MTU používat na virtuálních počítačích Azure a ve výchozím nastavení u většiny síťových zařízení je globálně, 1500 bajtů.

#### <a name="fragmentation"></a>Fragmentace

Fragmentace vyvolá se při odeslání paketu, MTU síťového rozhraní, který překračuje. Zásobník protokolu TCP/IP přeruší paket do menších (fragmenty), které odpovídají rozhraní MTU. Fragmentace dochází ve vrstvě protokolu IP a je nezávislá na základním protokolu (jako je například TCP). Při odesílání paketů 2 000 bajtů přes síťové rozhraní s 1 500 MTU, paket se rozdělí do jednoho paketu 1 500 bajtů a jeden paket 500 bajtů.

Síťová zařízení na cestě mezi zdrojem a cílem může buď přímé pakety, které překračují MTU nebo fragment paket do menších.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>Fragment není bit do paketů IP

Bit není Fragment (SV) se vlaječka v hlavičce protokolu IP. DF bit označuje, že síťová zařízení na cestě mezi odesílatelem a příjemcem nesmí fragment paketu. Pro mnoho důvodů, proč může nastavit tento bit. (Viz část "Zjišťování cesty MTU" jedním z příkladů tohoto článku). Pokud síťové zařízení obdrží paket s nastaveným bitem Don't Fragment a paketů překračuje rozhraní zařízení MTU, standardní chování je zařízení vyřadit paketu. Zařízení odešle zprávu potřeby fragmentace ICMP zpět do původního zdroje paketu.

#### <a name="performance-implications-of-fragmentation"></a>Rozbor aspektů fragmentace výkonu

Fragmentace může mít vliv na výkon negativní. Je jedním z hlavních důvodů pro vliv na výkon procesoru nebo paměti dopadu fragmentaci a nové sestavení paketů. Pokud síťové zařízení potřebuje fragment paket, bude mít k přidělení prostředků procesoru nebo paměti k provedení fragmentace.

Stejnou věc se stane, když je paket sestaveny. Síťové zařízení má pro uložení všech fragmenty, dokud jste dostali, takže ho můžete nedokonalostem do původního paketu. Tento proces fragmentace a opětovného sestavení může také způsobit zpoždění.

Další možné negativní dopad na výkon fragmentace je, že může obdržet fragmentaci paketů mimo pořadí. Když přijaté pakety jsou mimo pořadí, některé typy síťových zařízení můžete pustit. Pokud k tomu dojde, musí být přenášena celý paket.

Fragmenty jsou obvykle zrušených správcem zabezpečení zařízení, jako jsou síťové brány firewall nebo při příjmu síťovým zařízením byly vyčerpány. Při příjmu síťovým zařízením byly vyčerpány, síťové zařízení se pokouší o nedokonalostem fragmentované pakety, ale nemá prostředky k ukládání a reassume paketu.

Fragmentace se dají považovat za záporné operace, ale podpora pro fragmentaci je nezbytné, když se připojujete různé sítě přes internet.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Výhody a důsledky úprav MTU

Obecně řečeno můžete vytvořit síť efektivnější zvýšením MTU. Má každý paket, který je přenést informace hlavičky, který je přidán do původního paketu. Fragmentace vytvoří další, paketů, existuje další záhlaví režii a zajistíte, aby síť méně efektivní.

Tady je příklad. Velikost hlavičky Ethernet je 14 bajtů plus kontrola sekvence rámce na 4 bajty. k zajištění konzistence rámce. Pokud se pošle jeden paket 2 000 bajtů a je v síti přidána 18 bajtů režijní náklady na síť Ethernet. Pokud paketu je fragmentované do 1 500 bajtů paketů a 500 bajtů paketů, bude mít každý paket 18 bajtů hlavičky Ethernet, celkem 36 bajtů.

Mějte na paměti, že zvýšení MTU nebudou nutně vytvořit síť efektivnější. Pokud aplikace odesílá jenom pakety 500 bajtů, nároky na stejné hlavičce bude existovat, jestli je MTU 1500 bajtů nebo 9 000 bajtů. Síť se stanou efektivnější, pouze pokud používá větší velikosti paketu, které jsou ovlivněny MTU.

#### <a name="azure-and-vm-mtu"></a>Azure a MTU virtuálního počítače

Výchozí hodnota MTU pro virtuální počítače Azure je 1500 bajtů. Virtuální síť Azure stack se pokusí o fragment paket 1400 bajtů. Ale zásobník virtuální sítě vám umožní pakety 2,006 bajtů při nastavení bitu v hlavičce protokolu IP.

Všimněte si, že zásobníku ve virtuální síti není ze své podstaty neefektivní, protože fragmenty pakety 1400 bajtů, i když mají MTU 1 500 virtuálních počítačů. Vysoké procento síťové pakety jsou mnohem menší než 1400 nebo 1500 bajtů.

#### <a name="azure-and-fragmentation"></a>Azure a fragmentace

Zásobník virtuální sítě je nastavený vyřadit "mimo pořadí fragmenty," tedy fragmentované pakety, které není v původním fragmentované pořadí doručení. Tyto pakety se zahodí hlavně kvůli jsme oznámili v listopadu 2018 volá FragmentSmack ohrožení zabezpečení sítě.

FragmentSmack je vadu způsobem linuxového jádra zpracovat nové sestavení fragmentaci paketů protokolů IPv4 a IPv6. Vzdálený útočník použít tuto chybu aktivační událost nákladné fragment nového sestavení operací, které mohou vést k vyšší využití procesoru a odepření služby v cílovém systému.

#### <a name="tune-the-mtu"></a>Vyladění MTU

MTU virtuálního počítače Azure, můžete nakonfigurovat jako v jakémkoli jiném operačním systému. Měli byste ale zvážit fragmentace nacházející se v Azure, je popsáno výše, když konfigurujete MTU.

Není doporučujeme zákazníkům ke zvýšení MTU virtuálního počítače. Smyslem této diskuse je vysvětlit podrobnosti o tom, jak Azure implementuje MTU a provádí fragmentace.

> [!IMPORTANT]
>Zvýšení MTU nezná zlepšit výkon a může mít negativní vliv na výkon aplikace.
>
>

#### <a name="large-send-offload"></a>Snižování zátěže rozsáhlého odeslání

Snižování zátěže rozsáhlého odeslání (LSO) lze vylepšit výkon sítě snižování zátěže segmentace pakety pro adaptér sítě Ethernet. Když LSO zapnutý, zásobník protokolu TCP/IP vytvoří velké paket TCP a odešle ji na adaptér sítě Ethernet pro segmentace před předáním. Výhodou LSO je, že můžete uvolnit procesoru od segmentace paketů do velikosti, které odpovídají MTU a snižování zátěže zpracování do rozhraní sítě Ethernet, kde se provádí v hardwaru. Další informace o výhodách LSO najdete v tématu [snižováním zátěže rozsáhlého odeslání podporuje snižování zátěže](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

Když LSO zapnutý, může zákazníkům Azure zobrazit velké rámce velikosti při vykonávání zachytávání paketů. Tyto velikosti velký rámec by mohly vést zákazníci, kteří si myslí, že dochází k fragmentaci nebo, že velké MTU se používá, když není. Adaptér sítě Ethernet s LSO, můžete prezentovat větší velikost maximální segment (MSS) zásobník protokolu TCP/IP k vytvoření větší paket TCP. Tohoto celou nesegmentovaný rámce je pak předán adaptér sítě Ethernet a staly viditelnými v zachytávání paketů na virtuálním počítači provést. Ale paketu se dají rozdělit do menší počet snímků ve adaptér sítě Ethernet, podle MTU adaptér sítě Ethernet.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>Změna velikosti okna MSS protokolu TCP a PMTUD

#### <a name="tcp-maximum-segment-size"></a>Segment maximální velikost protokolu TCP

Segment maximální velikost protokolu TCP (MSS) je nastavení, která omezuje velikost segmentů TCP, která předchází fragmentaci paketů protokolu TCP. Operační systémy obvykle bude používat tento vzorec nastavit MSS:

`MSS = MTU - (IP header size + TCP header size)`

TCP a IP záhlaví jsou 20 bajtů nebo celkem 40 bajtů. Rozhraní s MTU 1 500 tak budou mít MSS 1,460. Ale v MSS je možné konfigurovat.

Toto nastavení je písemně uzavřené smlouvy v trojcestných handshake protokolu TCP při relaci protokolu TCP je nastavená mezi zdrojem a cílem. Obě strany odeslat hodnotu MSS a nižší z nich se používá pro připojení TCP.

Uvědomte si, že nejsou MTU zdroj a cíl pouze faktory, které určují hodnotu MSS. Zprostředkující síťová zařízení, jako jsou brány sítě VPN, včetně Azure VPN Gateway, můžete upravit MTU nezávisle na zdroji a cíli zajistit optimální síťový výkon.

#### <a name="path-mtu-discovery"></a>Zjišťování cesty MTU

Vyjedná MSS, ale to nemusí nutně znamenat skutečné MSS, který lze použít. Je, že jiná síťová zařízení na cestě mezi zdrojem a cílem může být nižší hodnota MTU než zdroj a cíl. V takovém případě zařízení je menší než paketu, MTU vyřadí paketu. Zařízení odešle zpět zprávu protokolu ICMP fragmentace potřeby (typ 3, 4 kódu), která obsahuje její MTU. Tato zpráva ICMP umožňuje zdrojového hostitele ke snížení jeho cesty MTU odpovídajícím způsobem. Proces se nazývá cesty MTU zjišťování (PMTUD).

Proces PMTUD je neefektivní a má vliv na výkon sítě. Při odesílání paketů, které překročit síťové cesty MTU, je potřeba přenášena s nižší MSS pakety. Pokud odesílatel neobdrží zprávu potřeby fragmentace ICMP možná z důvodu síťová brána firewall v cestě (obvykle označuje jako *směrovače PMTUD blackhole*), odesílatel neví, je nutné snížit na MSS a budou nepřetržitě přenos paketu. Z tohoto důvodu nedoporučujeme zvýšení MTU virtuálního počítače Azure.

#### <a name="vpn-and-mtu"></a>Připojení VPN a MTU

Pokud používáte virtuální počítače, které provádějí zapouzdření (jako jsou IPsec VPN), existují některé další důležité informace týkající se velikost paketu a MTU. Pomocí virtuální privátní sítě přidat další hlavičky paketů, což zvyšuje velikost paketu a vyžaduje menší MSS.

Pro Azure doporučujeme nastavit TCP MSS upnutí rovnaly 1 350 bajtů a tunelové propojení rozhraní MTU 1 400. Další informace najdete v tématu [VPN zařízení a stránka parametry protokolu IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Latence, dobu odezvy a škálování okna TCP

#### <a name="latency-and-round-trip-time"></a>Latence a zpátečního převodu času

Latence sítě se řídí rychlostí světla přes síť optického vlákna. Propustnost sítě pro TCP se také fakticky řídí dobu odezvy (požadavku) mezi dvěma síťovými zařízeními.

| | | | |
|-|-|-|-|
|**trasy**|**vzdálenost**|**Jednosměrná čas**|**RTT**|
|New York to San Francisco|4,148 km|21 ms|42 ms|
|New York do Londýna|5,585 km|28 ms|56 ms|
|New York to Sydney|15,993 km|80 ms|160 ms|

Tato tabulka ukazuje lineární vzdálenost mezi dvěma umístěními. Vzdálenost v sítích, je obvykle delší než lineární vzdálenost. Tady je jednoduchý vzorec, který vypočítá minimální požadavku řídí rychlostí světla:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

200 můžete použít pro rychlost šíření. Toto je vzdálenost v metrech světla, které se přenáší do 1 milisekunda.

Pojďme se na to New Yorku k San Francisco jako příklad. Lineární vzdálenost je 4,148 km. Tuto hodnotu zapojení do rovnice, můžeme získat následující:

`Minimum RTT = 2 * (4,148 / 200)`

Výstup rovnice je v milisekundách.

Pokud chcete získat nejlepší výkon sítě, je logický možnost Vybrat cíle s nejkratší vzdáleností mezi nimi. Měli byste také navrhnout své virtuální sítě a optimalizace cesty provoz a snižovat latenci. Další informace najdete v části "Aspekty návrhu sítě" část tohoto článku.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Latence a zpátečního převodu času dopady na TCP

Časem přenosu má přímý vliv na maximální propustnost protokolu TCP. V protokolu TCP *velikost okna* je maximální objem přenášených dat, kterou je možné odeslat prostřednictvím připojení protokolu TCP před odesílatele je potřeba příjem potvrzení z příjemce. Pokud MSS protokolu TCP je nastavena na 1,460 a velikost okna TCP je nastavená na 65 535, může odesílatel odeslat pakety pro 45 předtím, než má příjem potvrzení z příjemce. Pokud odesílatel nezíská potvrzení, bude znovu pošlou data. Tady je vzorec:

`TCP window size / TCP MSS = packets sent`

V tomto příkladu 65 535 / poloměr zaoblení 1,460 až 45.

Tento stav "čeká na potvrzení", mechanismus zajistit spolehlivé doručování dat, se, co způsobí, že požadavku má být ovlivněn propustnost protokolu TCP. Čím delší odesílatel čeká na potvrzení, čím déle je nutné vyčkat před odesláním další data.

Tady je vzorec pro výpočet maximální propustnost jednoho připojení TCP:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

Tato tabulka uvádí maximální MB za druhé propustnost jednoho připojení TCP. (Pro lepší čitelnost, se používá v megabajtech pro měrnou jednotku.)

| | | | |
|-|-|-|-|
|**Velikost okna TCP (bajty)**|**Čekací doba požadavku (ms)**|**Maximální počet megabajtů za sekundu propustnosti**|**Maximální megabit za sekundu propustnosti**|
|65,535|1|65.54|524.29|
|65,535|30|2.18|17.48|
|65,535|60|1.09|8.74|
|65,535|90|.73|5.83|
|65,535|120|.55|4.37|

Pokud ke ztrátě paketů se sníží maximální propustnost připojení TCP při odesílatel odešle data, která se už odeslal.

#### <a name="tcp-window-scaling"></a>Proměnná velikost okna TCP

Proměnná velikost okna TCP je technika, která dynamicky zvětšuje velikost okna TCP do většího množství dat k odeslání, bude nutné na potvrzení. V předchozím příkladu by se odeslaly 45 pakety předtím, než byla požadována, potvrzení. Pokud zvýšíte počet paketů, které lze odesílat, předtím, než je potřeba potvrzení, se snižuje počet pokusů, které odesílatele čeká na potvrzení, což zvyšuje maximální propustnost TCP.

Tato tabulka uvádí tyto relace:

| | | | |
|-|-|-|-|
|**Velikost okna TCP (bajty)**|**Čekací doba požadavku (ms)**|**Maximální počet megabajtů za sekundu propustnosti**|**Maximální megabit za sekundu propustnosti**|
|65,535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

Ale hodnota hlavičky protokolu TCP pro velikost okna TCP je pouze 2 bajty, což znamená, že maximální hodnota pro okno receive je 65 535. Pokud chcete zvýšit je maximální velikost okna, byla zavedená koeficient okno TCP.

Koeficient měřítka je taky nastavení, které můžete nakonfigurovat v operačním systému. Tady je vzorec pro výpočet velikost okna TCP pomocí škálování faktory:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Tady je výpočet koeficient okno 3 a velikosti okna 65 535:

`65,535 \* (2^3) = 262,140 bytes`

Koeficient měřítka 14 výsledkem velikost okna TCP 14 (maximální posun povolené). Velikost okna TCP bude 1,073,725,440 bajtů (šířka 8,5 gigabitů).

#### <a name="support-for-tcp-window-scaling"></a>Podpora pro změnu velikosti okna TCP

Windows můžete nastavit různé faktory měřítka různých typů připojení. (Třídy připojení patří datacentra, internet a tak dále). Můžete použít `Get-NetTCPConnection` příkaz prostředí PowerShell, chcete-li zobrazit okno škálování typ připojení:

```powershell
Get-NetTCPConnection
```

Můžete použít `Get-NetTCPSetting` příkaz prostředí PowerShell pro zobrazení hodnoty každá třída:

```powershell
Get-NetTCPSetting
```

Počáteční velikost okna TCP a měřítko TCP lze nastavit ve Windows pomocí `Set-NetTCPSetting` příkaz prostředí PowerShell. Další informace najdete v tématu [Set-NetTCPSetting](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps).

```powershell
Set-NetTCPSetting
```

Jedná se o platné nastavení protokolu TCP pro `AutoTuningLevel`:

| | | | |
|-|-|-|-|
|**AutoTuningLevel**|**Koeficient změny měřítka**|**Koeficient změny měřítka**|**Vzorec pro<br/>vypočítat maximální velikost okna**|
|Zakázáno|Žádný|Žádný|Velikost okna|
|Omezení|4|2^4|Velikost okna * (2 ^ 4)|
|Vysoce s omezením pomocí specifikátoru|2|2^2|Velikost okna * (2 ^ 2)|
|Normální|8|2^8|Velikost okna * (2 ^ 8)|
|Experimentální|14|2^14|Velikost okna * (2 ^ 14)|

Tato nastavení jsou pravděpodobně ovlivnit výkon TCP, ale mějte na paměti, že mnoho dalších faktorů přes internet, mimo ovládací prvek Azure, může také ovlivnit výkon TCP.

#### <a name="increase-mtu-size"></a>Zvětšete velikost MTU

Protože větší MTU znamená větší MSS, může vás zajímat, jestli zvýšení MTU může zvýšit výkon TCP. Pravděpodobně není. Existují výhody a nevýhody velikost paketu nad rámec pouze provoz TCP. Jak je uvedeno výše, nejdůležitějších faktorů, které ovlivňují výkon propustnost TCP se velikost okna TCP, ztráta paketů a požadavku.

> [!IMPORTANT]
> Nedoporučujeme ale, že zákazníkům Azure změnit výchozí hodnotu MTU na virtuálních počítačích.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Akcelerovanými síťovými službami a škálování na straně příjmu

#### <a name="accelerated-networking"></a>Urychlení sítě

Funkce sítě virtuálního počítače v minulosti byly na virtuálním počítači hosta a hostiteli hypervisoru/intenzivní nároky na procesor. Každý paket tranzitů přes hostitele, který zpracovává v softwaru hostitelského procesoru, včetně všech zapouzdření virtuální sítě a pokud. Tak větší provoz, který prochází hostitele, tím vyšší procesoru načíst. A pokud procesor hostitele je zaneprázdněn prováděním jiné operace, která ovlivní také latence a propustnosti sítě. Azure řeší tento problém s akcelerovanými síťovými službami.

Akcelerované síťové služby poskytuje konzistentní ultralow sítích s latencí prostřednictvím interní programovatelný hardware Azure a technologie, jako je SR-IOV. Akcelerované síťové služby přesouvá většinu Azure softwarově definované sítě zásobníku dojde k odsouhlasení procesory a na základě FPGA SmartNICs. Tato změna umožňuje koncový uživatel aplikace získat výpočetních cyklů, která odesílá menšími nároky na virtuálním počítači, zpoždění a nekonzistence snížení latence. Jinými slovy může být výkon deterministického.

Akcelerované síťové služby zvyšuje výkon tím, že hostovaný virtuální počítač hostitele obejít a navázat datapath přímo s SmartNIC hostitele. Tady je několik výhod akcelerované síťové služby:

- **Nižší latenci za vyšší pakety za sekundu (pps)**: Odebrání virtuálního přepínače datapath eliminuje čas, kdy pakety výdaje v hostiteli pro zpracování zásad a zvyšuje počet paketů, které lze zpracovat ve virtuálním počítači.

- **Snižuje zpoždění**: Virtuální přepínač zpracování závisí na množství zásady, které je potřeba použít a zatížení procesoru, který provádí zpracování. Snižování zátěže vynucení zásad hardwaru odebere této variabilitě poskytováním pakety přímo k virtuálnímu počítači, odstranění komunikace hostitele pro virtuální počítač a všechny softwaru přerušení a přepnutí kontextu.

- **Snížení využití procesoru**: Obcházení virtuální přepínač na hostiteli vede k méně využití procesoru pro zpracování síťového provozu.

Pokud chcete používat akcelerované síťové služby, musíte ji explicitně zapnout na každém virtuálním počítači použít. Zobrazit [vytvořit virtuální počítač s Linuxem s Akcelerovanými síťovými službami](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) pokyny.

#### <a name="receive-side-scaling"></a>Škálování na straně příjmu

Zobrazí se na straně příjmu (RSS) je ovladač technologie sítě, která distribuuje přijímají síťový provoz efektivněji díky distribuci zpracování příjmu napříč více procesorů v systému s více procesory. RSS umožňuje jednoduše řečeno, může systém zpracovávat více přijaté přenosy, protože používá všechny dostupné procesory ne o jeden. Další technické informace o RSS, najdete v článku [Úvod do škálování na straně příjmu](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

Pokud chcete získat nejlepší výkon obrysů akcelerované síťové služby na virtuálním počítači, je potřeba povolit RSS. RSS může poskytnout také výhody na virtuálních počítačích, které nepoužívají akcelerovanými síťovými službami. Přehled o tom, jak určit, pokud je technologie RSS zapnutá a jak se dá povolit, najdete v části [optimalizace propustnosti sítě pro virtuální počítače Azure](http://aka.ms/FastVM).

### <a name="tcp-timewait-and-timewait-assassination"></a>TCP TIME_WAIT a TIME_WAIT assassination

TCP TIME_WAIT je další z běžných nastavení, která má vliv na výkon sítě a aplikace. Na zaneprázdněné virtuální počítače, které jsou otvírání a zavírání mnoho soketů, jako klienty nebo jako servery (zdrojový IP:Source Port a cílový Port IP:Destination), při běžném provozu protokolu TCP daný soketu skončit ve stavu TIME_WAIT po dlouhou dobu. Stav TIME_WAIT slouží k povolení dalších dat doručit na soketu před jeho uzavřením. Proto TCP/IP zásobníky obecně zakázat opakované použití soketu tiše přetažením TCP SYN paket klienta.

Množství času, které soketu se TIME_WAIT je možné konfigurovat. Může sahat od 30 sekund až 240 sekund. Sokety jsou omezené prostředky a počet soketů, které je možné v daném okamžiku je možné konfigurovat. (Počet dostupných soketů je obvykle přibližně 30 000.) Pokud jsou využité dostupných soketů, nebo pokud neodpovídající nastavení TIME_WAIT mít klienti a servery a virtuální počítač pokusí pro opětovné použití soketů ve stavu TIME_WAIT, nové připojení se nezdaří jako TCP SYN tiše pakety se zahodí.

Hodnota pro rozsah portů pro odchozí sockets je obvykle možné konfigurovat v rámci zásobník protokolu TCP/IP operačního systému. Totéž platí pro nastavení TCP TIME_WAIT a opětovné použití soketů. Změna těchto čísel může zlepšit škálovatelnost. Ale v závislosti na situaci, tyto změny můžou způsobit problémy s interoperabilitou. Měli byste být opatrní při změně těchto hodnot.

TIME_WAIT assassination můžete použít k vyřešení tohoto omezení škálování. TIME_WAIT assassination umožňuje soketu opětovné použití v určitých situacích, jako když pořadové číslo IP paketu nového připojení překročí pořadové číslo poslední paketů z předchozí připojení. V takovém případě operační systém vám umožní navázání nové připojení (přijímal nové SYN/potvrzení) a vynutit ukončení předchozí připojení, která byla ve stavu TIME_WAIT. Tato funkce je podporována na virtuálních počítačích s Windows v Azure. Další informace o podpoře v jiných virtuálních počítačů, obraťte se na dodavatele operačního systému.

Další informace o konfiguraci nastavení TCP TIME_WAIT a rozsah zdrojových portů, naleznete v tématu [nastavení, které lze upravit a zlepšit výkon sítě](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Virtuální síť faktory, které mohou ovlivnit výkon

### <a name="vm-maximum-outbound-throughput"></a>Maximální propustnost odchozích virtuálních počítačů

Azure nabízí širokou škálu velikostí virtuálních počítačů a typy, každý s různou kombinaci možností výkonu. Jeden z těchto funkcí je síť propustnost (nebo šířky pásma), který se měří v MB za sekundu (MB/s). Vzhledem k tomu virtuální počítače hostují na sdíleném hardwaru, je potřeba poměrně sdílet mezi virtuálními počítači používat stejný hardware kapacita sítě. Větší virtuální počítače mají při přidělování větší šířku pásma než menších virtuálních počítačích.

Šířka pásma sítě, které jsou přidělené na každý virtuální počítač se měří na výchozí přenos (odchozí) z virtuálního počítače. Veškerý přenos v síti byste museli opustit virtuální počítač se počítá směrem k přidělené limit, bez ohledu na cílový. Například pokud virtuální počítač má limit 1 000 MB/s, toto omezení platí, zda je odchozí provoz určený pro jinému virtuálnímu počítači ve stejné virtuální síti nebo jeden mimo Azure.

Příchozí přenos dat se měří nebo jsou omezena přímo. Existuje ale dalších faktorů, jako jsou omezení úložiště a procesoru, které může mít vliv na možnost virtuální počítač ke zpracování příchozích dat.

Akcelerované síťové služby slouží ke zlepšení výkonu sítě, včetně latence, propustnosti a využití procesoru. Akcelerované síťové služby může zlepšit propustnost virtuálních počítačů, ale dělá to pouze do virtuálního počítače přidělené šířky pásma.

Virtuální počítače Azure mají alespoň jedno síťové rozhraní připojené k nim. Mohou mít několik. Šířka pásma přidělená k virtuálnímu počítači je součet veškerého odchozího provozu přes všechna síťová rozhraní připojená k počítači. Jinými slovy se přiděluje šířku pásma pro jednotlivé virtuální počítače, bez ohledu na to, kolik síťových rozhraní jsou připojené k počítači.

Očekávaná výstupní propustnost a počet síťových rozhraní, které podporuje všechny velikosti virtuálních počítačů jsou podrobně popsány v [velikosti pro Windows virtual machines v Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud chcete zobrazit maximální propustnost, vyberte typ, jako je třeba **Obecné**a pak najdete v části o velikost series na stránce výsledný (například "řady Dv2-series"). U každé datové řady, je tabulka, která poskytuje síťové specifikace v posledním sloupci, který má název "maximální počet síťových karet / očekávaný šířka pásma (MB/s)."

Omezení propustnosti platí pro virtuální počítač. Propustnost nemá vliv tyto faktory:

- **Počet síťových rozhraní**: Limit šířky pásma se vztahuje na součet veškerého odchozího provozu z virtuálního počítače.

- **Akcelerované síťové služby**: I když tato funkce může být užitečné při dosažení limitu publikované, nedojde ke změně limit.

- **Určení provozu**: Všechny cíle počítají odchozí limit.

- **Protokol**: Veškerý odchozí provoz přes všechny protokoly započítává limitu.

Další informace najdete v tématu [šířky pásma sítě virtuálních počítačů](http://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Důležité informace o výkonu Internet

Jak je popsáno v tomto článku, faktory na Internetu a mimo ovládací prvek Azure může ovlivnit výkon sítě. Tady jsou některé z těchto faktorů:

- **Latence**: Dobu odezvy mezi dvěma uzly může mít vliv na problémy na zprostředkující sítí, provoz, který nepřijímá "nejkratší" vzdálenost cesty a neoptimální cesty partnerského vztahu.

- **Ztráta paketů**: Ztráta paketů může být způsobeno zahlcení sítě, fyzickou cestu problémy a nedostatečně síťových zařízení.

- **Velikost MTU/fragmentace**: Fragmentaci na cestě může způsobit prodlevy v přijetí dat nebo v paketech přicházejících mimo pořadí, které mohou ovlivnit doručování paketů.

Nástroj vhodný pro měření výkonu charakteristiky sítě (třeba ztrátu paketů a latenci) podél každé síťová cesta mezi zařízením zdrojového a cílového zařízení, je Traceroute.

### <a name="network-design-considerations"></a>Aspekty návrhu sítě

Společně s požadavky popsané dříve v tomto článku topologie virtuální sítě může ovlivnit výkon vaší sítě. Například střed a paprsek návrhu globálně provoz Zpětná doprava k virtuální síti centra jedním představí latence sítě, která bude mít vliv na výkon sítě.

Počet síťových zařízení, které síťový provoz prochází může také ovlivnit celkovou latenci. Například v střed a paprsek návrhu, pokud provoz prochází přes síťové virtuální zařízení paprsku a virtuální zařízení centra před přechodem mezi protokoly k Internetu, síťová virtuální zařízení můžete zavést latence.

### <a name="azure-regions-virtual-networks-and-latency"></a>Oblasti Azure, virtuální sítě a latence

Oblasti Azure, které se skládá z několika datových centrech, které existují v rámci hlavní zeměpisné oblasti. Tato datová centra, nemusí být fyzicky vedle sebe. V některých případech rozdělíme tak co nejvíce 10 kilometrů. Virtuální síť je logický překrytí v datovém centru Azure fyzické síti. Virtuální síť nemá neznamená žádné konkrétní síťové topologie v rámci datového centra.

Například dva virtuální počítače, které jsou ve stejné virtuální sítě a podsítě může být v různých skříních, řádky nebo dokonce datových centrech. Může být odděleny nohou kabel optického vlákna nebo kilometrů kabel optického vlákna. Tato změna by mohla zanést variabilní latence (rozdíl několik milisekund) mezi různé virtuální počítače.

Geografické umístění virtuálních počítačů a potenciální způsobená latence mezi dvěma virtuálními počítači, mohou být ovlivněny konfiguraci skupiny dostupnosti a zóny dostupnosti. Ale vzdálenost mezi datacentry v oblasti, je specifický pro oblast a primárně k nim topologie datového centra v oblasti.

### <a name="source-nat-port-exhaustion"></a>Vyčerpání portů NAT zdroje

Nasazení v Azure může komunikovat s koncovými body mimo Azure na veřejném Internetu a/nebo v veřejný prostor IP adres. Když instance zahájí odchozí připojení, Azure dynamicky mapuje privátní IP adresu na veřejnou IP adresu. Poté, co Azure vytvoří toto mapování, návratový přenos pro odchozí tok pocházející ze dosáhnout taky privátní IP adresa původu toku.

Pro každou odchozí připojení nástroje pro vyrovnávání zatížení Azure je potřeba udržovat toto mapování nějakou dobu. Víceklientské povaze Azure udržování toto mapování pro každý odchozí tok pro každý virtuální počítač může být náročné. Proto existují omezení, které nastavují a v závislosti na konfiguraci služby Azure Virtual Network. Nebo pokud chcete říct, že přesněji, lze vytvořit virtuální počítač Azure pouze počet odchozích připojení v daném okamžiku. Když se dosáhne těchto limitů, virtuální počítač nebude možné vytvořit víc odchozích připojení.

Ale toto chování je možné konfigurovat. Další informace o SNAT a SNAT port vyčerpání, naleznete v tématu [v tomto článku](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Měřit výkon sítě v Azure

Počet maximální hodnoty výkonu v tomto článku se vztahují k latenci sítě / round-trip doba mezi dvěma virtuálními počítači. Tato část obsahuje několik návrhů pro testování latence/požadavku a testování protokolu TCP výkonu a výkon sítě virtuálních počítačů. Můžete vyladit a hodnoty protokolu TCP/IP a sítě pomocí technik popsaných v této části jsme probírali výše testování výkonu. Možné hodnoty latence, MTU, MSS a okna velikost pružný výpočty, které jste zadali dříve a porovnat teoretický maximální hodnoty skutečným hodnotám, které zjistíte při testování.

### <a name="measure-round-trip-time-and-packet-loss"></a>Měření času jejich návratu a ztráta paketů

TCP výkonu spoléhá na požadavku a ke ztrátě paketů. Nástroj PING dostupný ve Windows a Linux poskytuje nejjednodušší způsob, jak měřit ztráty požadavku a paketů. Minimální/maximální/průměrný latence mezi zdrojem a cílem se zobrazí výstup příkazu PING. Zobrazí také ztráta paketů. Ve výchozím nastavení používá příkaz PING protokolu ICMP. PsPing můžete použít k testování požadavku protokolu TCP. Další informace najdete v tématu [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Míra Skutečná propustnost připojení TCP

NTttcp je nástroj pro testování výkonu protokolu TCP na Linuxu nebo virtuálního počítače Windows. Můžete změnit různá nastavení protokol TCP a pak pomocí NTttcp otestovat výhody. Další informace naleznete v následujících zdrojích:

- [Šířka pásma nebo propustnosti testování (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [Nástroj NTttcp](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Skutečné šířky pásma míru virtuálního počítače

Můžete otestovat výkon různé typy virtuálních počítačů, accelerated networking a tak dále, pomocí nástroje volá iPerf. iPerf dočíst i v Linuxu a Windows. iPerf slouží k otestování celkovou propustnost sítě TCP nebo UDP. iPerf TCP propustnost testy jsou ovlivněny na faktorech popsaných v tomto článku (například. latence a požadavku). UDP tak může přinést lepší výsledky, pokud chcete otestovat maximální propustnost.

Další informace najdete v těchto článcích:

- [Řešení potíží s výkon sítě pro Expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Ověření propustnosti sítě VPN do virtuální sítě](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Zjištění neefektivní chování protokolu TCP

V zachytávání paketů může zákazníkům Azure najdete v článku pakety protokolu TCP s příznaky TCP (výběrové POTVRZOVÁNÍ, DUP ACK, opakování a rychlé opakování přenosu), které můžou značit problémy s výkonem sítě. Tyto pakety konkrétně určit nedostatečné efektivity sítě, které jsou výsledkem ztráta paketů. Ale ztráta paketů není nutně způsobeno problémy s výkonem Azure. Problémy s výkonem chybu může způsobovat problémy s aplikací, problémů operačního systému nebo jiné problémy, které nemusí být přímo související s platformou Azure.

Také mějte na paměti, že některé opakovaný přenos zpráv a duplicitní potvrzení jsou běžné v síti. Protokoly TCP byly vytvořeny spolehlivé. Doklad o tyto pakety protokolu TCP v zachytávání paketů nutně neznamená problém systémové sítě, pokud nejsou nadměrné.

Tyto typy paketů jsou stále, označení, že propustnost TCP nedosahuje jeho maximální výkon, z důvodů popsaných v dalších částech tohoto článku.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili o optimalizaci výkonu protokolu TCP/IP pro virtuální počítače Azure, můžete chtít prostudovat další důležité informace pro [plánování virtuálních sítí](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-vnet-plan-design-arm) nebo [Další informace o připojení a konfigurace virtuální sítě ](https://docs.microsoft.com/en-us/azure/virtual-network/).
