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
ms.date: 3/30/2019
ms.author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
ms.openlocfilehash: c5d4f67e9c1e4e983133675c440b8c5b64183227
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851763"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>Pro virtuální počítače Azure pro optimalizaci výkonu protokolu TCP/IP

Účelem tohoto článku je diskutovat o běžné postupy optimalizace výkonu protokolu TCP/IP a jejich požadavky na virtuální počítače běžící v Microsoft Azure. Je důležité nejdřív základní znalosti konceptů a diskutovat o tom, jak lze ladit.

## <a name="common-tcpip-tuning-techniques"></a>Běžné postupy optimalizace protokolu TCP/IP

### <a name="mtu-fragmentation-and-large-send-offload-lso"></a>MTU fragmentace a velké odeslat snižování zátěže (LSO)

#### <a name="explanation-of-mtu"></a>Vysvětlení jednotek MTU

Maximální přenosové jednotky (MTU) je největší velikost rámce (paket) zadaný v bajtech odeslaných prostřednictvím síťového rozhraní. MTU je konfigurovatelné nastavení a ve výchozím nastavení jednotek MTU použít na virtuálních počítačích Azure a ve výchozím nastavení u většiny síťových zařízení je globálně, 1500 bajtů.

#### <a name="explanation-of-fragmentation"></a>Vysvětlení fragmentaci

Fragmentace vyvolá se při odeslání paketu, MTU síťového rozhraní, který překračuje. Zásobník protokolu TCP/IP přeruší paket do menších (fragmenty), které odpovídají rozhraní MTU. Fragmentace dochází ve vrstvě protokolu IP a je nezávislá na základním protokolu (jako je například TCP). Při odesílání paketů 2000 bajtů přes síťové rozhraní s MTU 1500, pak se rozdělí do jednoho paketu 1500 bajtů a jeden paket 500 bajtů.

Síťová zařízení na cestě mezi zdrojem a cílem mít možnost pakety, které překračují MTU nebo fragment paket do menších.

#### <a name="the-dont-fragment-df-bit-in-an-ip-packet"></a>Bit "není Fragment (SV)" v paketu IP

Bitu se vlaječka v hlavičce protokolu IP. Při DF bit nastaven, znamená to, že zprostředkující síťových zařízení na cestě mezi odesílatelem a příjemcem nesmí fragment paketu. Existuje mnoho důvodů, proč může být tento bit nastaven (viz části cesty zjišťování pod jedním z příkladů). Když síťového zařízení obdrží paket s nastaveným bitem Don't Fragment a paketů překračuje rozhraní zařízení MTU a potom standardní chování je pro zařízení a vyřadit paket odeslat paket "ICMP fragmentace potřeby" zpět na původní příčiny paketů.

#### <a name="performance-implications-of-fragmentation"></a>Rozbor aspektů fragmentace výkonu

Fragmentace může mít vliv na výkon negativní. Je jedním z hlavních důvodů pro dopad na výkon procesoru nebo paměti dopad fragmentace a nové sestavení paketů. Pokud síťové zařízení potřebuje fragment paket, bude mít k přidělení prostředků procesoru nebo paměti k provedení fragmentace. Stejné musí nastat, když je paket sestaveny. Síťové zařízení musí ukládat všechny fragmentů, dokud jste dostali, takže ho můžete nedokonalostem do původního paketu. Tento proces fragmentace/znovusestavení, může také způsobit latenci kvůli fragmentace/znovusestavení procesu.

Další možné negativní dopad na výkon fragmentace je, že může do fragmentaci paketů mimo pořadí. Určité typy síťových zařízení odpojení mimo pořadí paketů – což pak vyžaduje celý paket být přenášena může způsobit mimo pořadí paketů. Typické scénáře pro přetažení fragmenty zahrnout zařízení zabezpečení, jako jsou síťové brány firewall nebo při příjmu síťovým zařízením byly vyčerpány. Při příjmu síťovým zařízením byly vyčerpány, síťové zařízení se pokouší o nedokonalostem fragmentované pakety, ale nemá prostředky k ukládání a reassume paketu.

Fragmentace může být vnímané jako záporné operace, ale podpora pro fragmentaci je nezbytné pro propojení různých sítí přes Internet.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Výhody a důsledky úprav MTU

Jako obecné smlouvy můžete zvýšit MTU vytvořit síť efektivnější. Každý paket, který je přenést obsahuje další záhlaví informace, které se přidá do původního paketu. Další paketu znamená, že další záhlaví režii a sítě díky tomu je méně efektivní.

Ethernet velikost záhlaví je například 14 bajtů plus 4 bajty rámce Zkontrolujte pořadí FCS () k zajištění konzistence rámce. Pokud se pošle jeden paket 2000 bajtů a je přidána 18 bajtů Ethernet zatížení v síti. Pokud je paket fragmentované do 1500 bajtů paketů a 500 bajtů paketů, každý paket bude mít 18 bajtů Ethernet header - nebo 36 bajtů. Zatímco hlavičku Ethernet 18 bajtů byste měli jenom jeden paket 2000 bajtů.

Je důležité si uvědomit, že zvýšení MTU sám o sobě nevytvoří nutně efektivnější sítě. Pokud aplikace odesílá jenom pakety 500 bajtů, bude existovat nároky na stejné záhlaví, zda MTU je 1500 bajtů nebo 9000 bajtů. Aby sítě být další efektivní, pak musí také používat větší velikosti paketu, které jsou relativní vzhledem k MTU.

#### <a name="azure-and-vm-mtu"></a>Azure a MTU virtuálního počítače

Výchozí hodnota MTU pro virtuální počítače Azure je 1500 bajtů. Virtuální síť Azure stack se pokusí o fragment paket 1400 bajtů. Nicméně virtuální sítě Azure stack vám umožní pakety 2006 bajtů při nastavení "" bitu v hlavičce protokolu IP.

Je důležité si uvědomit, že není fragmentace znamenají, že virtuální síť Azure stack je ze své podstaty neefektivní, protože fragmenty pakety 1400 bajtů, i když má MTU 1 500 virtuálních počítačů. Ve skutečnosti je mnohem menší než 1400 bajtů nebo 1500 bajtů vysoké procento síťových paketů.

#### <a name="azure-and-fragmentation"></a>Azure a fragmentace

Zásobník virtuální sítě Azure ještě dnes nastaven na "Mimo pořadí of fragmenty" – to znamená fragmentované pakety, které není doručeny v pořadí podle jejich původní fragmentované vyřadit. Tyto pakety se zahodí hlavně kvůli jsme oznámili v listopadu 2018 volá FragmentStack ohrožení zabezpečení sítě.

FragmentSmack je vadu způsobem linuxového jádra zpracovat nové sestavení fragmentaci paketů protokolů IPv4 a IPv6. Vzdálený útočník použít tuto chybu operací nového sestavení nákladné fragment aktivační událost, které vedou ke zvýšení využití procesoru a odepření služby v cílovém systému.

#### <a name="tune-the-mtu"></a>Vyladění MTU

Virtuální počítače Azure podporují konfigurovatelné MTU stejně jako všechny ostatní operační systémy. Fragmentace, vyvolá se v rámci Azure, který je podrobně popsán výše, ale potřeba myslet, když konfigurace MTU.

Azure nemá Doporučte zákazníkům ke zvýšení jejich MTU virtuálního počítače. Tato diskuse se má podrobně popisují, jak Azure implementuje MTU a provádí fragmentace ještě dnes.

> [!IMPORTANT]
>Zvýšení MTU nebyla uvedeny ke zlepšení výkonu a může mít negativní vliv na výkon aplikace.
>
>

#### <a name="large-send-offload-lso"></a>Snižování zátěže rozsáhlého odeslání (LSO)

Velké odeslat snižování zátěže (LSO) lze vylepšit výkon sítě snižování zátěže segmentace pakety pro adaptér sítě Ethernet. S LSO povolena zásobník protokolu TCP/IP vytvoří velké paket TCP a odešlete adaptéru Ethernet segmentace před předáním. Výhodou LSO je, že můžete uvolnit procesoru od segmentace paketů do velikosti paketů, které odpovídají MTU a snižování zátěže zpracování do rozhraní sítě Ethernet, kde se provádí v hardwaru. Další informace o výhodách LSO nacházely v [výkonu v dokumentaci k Microsoft síťový adaptér](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

Když LSO zapnutý, může zákazníkům Azure zobrazit velké rámce velikosti při provádění paketů zachytí. Tyto velikosti velký rámec může způsobit zákazníci, kteří se domnívat, že fragmentace nebo jumbo, MTU je používán, když není. S LSO můžete adaptér sítě ethernet inzerovat větší MSS na zásobník protokolu TCP/IP, aby bylo možné vytvořit větší paket TCP. Tohoto celou nesegmentovaný rámce je pak předán adaptér sítě Ethernet a staly viditelnými v zachytávání paketů na virtuálním počítači provést. Ale paket se rozdělí na menší počet snímků ve adaptér sítě Ethernet podle MTU adaptér sítě Ethernet.

### <a name="tcpmss-window-scaling-and-pmtud"></a>TCP/MSS okno škálování a PMTUD

#### <a name="explanation-of-tcp-mss"></a>Vysvětlení MSS protokolu TCP

TCP maximální velikost (MSS Segment) je nastavení měli v úmyslu nastavit maximální velikost segmentů TCP, aby se zabránilo fragmentaci paketů protokolu TCP. Operační systémy se obvykle nastavuje MSS jako MSS = MTU - IP zá & hlaví TCP velikost (20 bajtů nebo 40 celkový počet bajtů). Rozhraní s MTU 1500 tak budou mít MSS 1460. MSS, ale je možné konfigurovat.

Toto nastavení je v třícestné TCP písemně uzavřené smlouvy, když relace TCP je nastavená mezi zdrojem a cílem. Obě strany odeslat hodnotu MSS a nižší z nich se používá pro připojení TCP.

Zprostředkující síťová zařízení, jako jsou brány sítě VPN, včetně brány Azure VPN Gateway se budou moct upravit nezávisle na zdroji a cíli MTU zajistit optimální síťový výkon. Ano je třeba poznamenat, MTU zdrojových a cílových samostatně není jediným faktory skutečné hodnoty MSS.

#### <a name="explanation-of-path-mtu-discovery-pmtud"></a>Vysvětlení zjišťování cesty MTU (PMTUD)

Při vyjednávání MSS nemusí to znamenat, že skutečné MSS, který může sloužit jako jiná síťová zařízení na cestě mezi zdrojem a cílem může mít nižší hodnota MTU než zdrojový a cílový. V takovém případě bude zařízení je menší než paketu, MTU vyřadit paket a pošlete zpět zprávu fragmentace ovládacího prvku zprávy ICMP (Internet Protocol) potřeby (typ 3, 4 kódu) obsahující jeho MTU. Tato zpráva ICMP umožňuje zdrojového hostitele ke snížení jeho cesty MTU odpovídajícím způsobem. Proces se nazývá zjišťování cesty MTU.

Proces PMTUD je ze své podstaty neefektivní a má vliv na výkon sítě. Při odesílání paketů, které překročit síťové cesty MTU, musí tyto pakety přenášena s nižší MSS. Pokud odesílatel neobdrží paketu ICMP fragmentace potřeby, pravděpodobně kvůli síťová brána firewall v cestě (označované jako směrovače blackhole PMTUD), pak odesílatel neví, že je nutné snížit na MSS a průběžně se přenos paketu. Z tohoto důvodu nedoporučujeme zvýšení MTU virtuálního počítače Azure.

#### <a name="vpn-considerations-with-mtu"></a>Požadavky na VPN s MTU

Zákazníci, které používají virtuální počítače, které provádějí zapouzdření (například VPN IPSec) může mít vliv na další a velikost paketu, MTU. Dodatečné hlavičky se přidají do původního paketu tak zvýšit velikost paketu a vyžadují menší MSS přidat připojení VPN.

Aktuální doporučení pro Azure je nastavit upnutí TCP MSS na 1350 bajtů a rozhraní tunelu MTU 1400. Další informace najdete v [VPN zařízení a stránka parametry protokolu IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Latence, dobu odezvy a škálování okna TCP

#### <a name="latency-and-round-trip-time"></a>Latence a zpátečního převodu času

Latence sítě se řídí rychlostí světla přes síť optického vlákna. Ve skutečnosti je, propustnost sítě pro TCP i efektivně řízená (praktické maxima) z důvodu z času zpátečního převodu (požadavku) mezi dvěma síťovými zařízeními.

| | | | |
|-|-|-|-|
|Trasa|Vzdálenost|Jednosměrná čas|Operace round-trip doba|
|New York to San Francisco|4,148 km|21 ms|42 ms|
|New York do Londýna|5,585 km|28 ms|56 ms|
|New York to Sydney|15,993 km|80 ms|160 ms|

Tato tabulka ukazuje lineární vzdálenost mezi dvěma umístěními v sítích, vzdálenost je však obvykle delší než lineární vzdálenost. Se používá jednoduchý vzorec k výpočtu minimální požadavku řídí rychlostí světla: minimální požadavku = 2 * (vzdálenost v kilometrech / šíření zrychlení).

Standardní hodnotu 200 lze použít pro rychlost šíření hodnoty – hodnota je že vzdálenost v světla měřiče přenosu v 1 milisekunda.

V příkladu New York San Francisco je lineární vzdálenost 4,148 km. Minimální požadavku = 2 * (4,148 / 20). Výstup rovnice bude v milisekundách.

Fyzická vzdálenost mezi dvěma umístěními je dlouhodobý realitou, pokud je potřeba maximální síťový výkon, pak největší smysl možností je vybrat cíle s minimální vzdálenost mezi nimi. Sekundárně rozhodnutí o návrhu v rámci virtuální sítě můžete provést k optimalizaci cesta provoz a snižovat latenci. Tyto důležité informace o virtuální sítě jsou popsány v níže uvedené části aspekty návrhu sítě.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Latence a zpátečního převodu času dopady na TCP

Doba odezvy má přímý vliv na maximální propustnost protokolu TCP. Protokol TCP používá koncept velikost okna. Velikost okna je maximální objem přenášených dat, kterou je možné odeslat prostřednictvím připojení protokolu TCP před odesílatele musí přijmout potvrzení od příjemce. MSS protokolu TCP je nastavený na 1460 a velikost okna TCP nastavená na 65535 odesílatel může odešlete 45 pakety předtím, než ho musí přijmout potvrzení od příjemce. Pokud není přijato potvrzení bude vysílat odesílatele. V tomto příkladu velikost okna TCP / TCP MSS = odeslaných paketů. Nebo 65535 / poloměr zaoblení 1460 až 45.

Tento stav "čeká na potvrzení" jako mechanismus pro vytvoření spolehlivé doručování dat, je co účinně způsobí, že požadavku má být ovlivněn propustnost protokolu TCP. Čím delší odesílatel čeká na potvrzení, tím déle musí rovněž počkat před odesláním další data.

Vzorec pro výpočet maximální propustnost jednoho připojení TCP je následujícím způsobem: Velikost okna / (čekací doba požadavku v milisekundách / 1000) = maximální počet bajtů za sekundu. Následující tabulka formátována v megabajtech pro lepší čitelnost a zobrazí maximální počet megabajtů za druhé propustnost jednoho připojení TCP.

| | | | |
|-|-|-|-|
|Velikost okna TCP v bajtech|Čekací doba požadavku<br/>v milisekundách|Maximum<br/>Za druhé propustnost v megabajtech|Maximum<br/> Megabit za druhé propustnost|
|65535|1|65.54|524.29|
|65535|30|2.18|17.48|
|65535|60|1.09|8.74|
|65535|90|.73|5.83|
|65535|120|.55|4.37|

Pokud dojde ke ztrátě paketů, pak omezí maximální propustnost připojení TCP při odesílatel odešle data, která se už odeslal.

#### <a name="explanation-of-tcp-window-scaling"></a>Vysvětlení nastavení velikosti okna TCP

Změna velikosti okna TCP je koncept dynamicky zvětšuje velikost okna TCP umožňující další data k odeslání, bude nutné na potvrzení. V našem příkladu předchozí by se odeslaly 45 pakety předtím, než byla požadována, potvrzení. Pokud počet paketů, které se odesílají předtím, než je zvýšena na potvrzení, pak maximální propustnost TCP se také zvýší o snižuje počet, kolikrát odesílatele čeká na potvrzení.

Propustnost protokolu TCP je ukázáno v jednoduché tabulky níže:

| | | | |
|-|-|-|-|
|Velikost okna TCP<br/>v bajtech|Čekací doba požadavku v milisekundách|Maximum<br/>Za druhé propustnost v megabajtech|Maximum<br/> Megabit za druhé propustnost|
|65535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

Hodnota hlavičky protokolu TCP pro velikost okna TCP je však pouze 2 bajty, což znamená, že maximální hodnota pro okno receive je 65535. Za účelem zvýšení je maximální velikost okna TCP okno koeficient byla zavedena.

Koeficient měřítka je také nastavení, které lze nastavit v operačním systému. Vzorec pro výpočet velikost okna TCP pomocí měřítko vypadá takto: Velikost okna TCP = velikost okna TCP v bajtech \* (2 ^ škálování faktor). Pokud okno měřítko 3 a velikosti okna 65535, výpočtu vypadá takto: 65535 \* (2 ^ 3) = 262,140 bajtů. Koeficient měřítka 14 výsledkem velikost okna TCP 14 (maximální posun povolen) a velikost okna TCP bude 1,073,725,440 bajtů (šířka 8,5 gigabitů).

#### <a name="support-for-tcp-window-scaling"></a>Podpora pro změnu velikosti okna TCP

Má možnost nastavit různé faktory měřítka ve Windows na jednotlivá připojení typ - je několik tříd připojení (datacenter, internet a tak dále). Zobrazí se okno připojení klasifikace škálování pomocí příkazu powershellu Get-NetTCPConnection.

```powershell
Get-NetTCPConnection
```

Zobrazí se hodnoty pomocí příkazu powershellu Get-NetTCPSetting každé třídy.

```powershell
Get-NetTCPSetting
```

Počáteční velikost okna TCP a škálování faktor TCP lze nastavit ve Windows pomocí příkazu prostředí powershell Set-NetTCPSetting. Další informace najdete v [Set-NetTCPSetting stránky](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps)

```powershell
Set-NetTCPSetting
```

Efektivní nastavení protokolu TCP pro AutoTuningLevel jsou následující.

| | | | |
|-|-|-|-|
|AutoTuningLevel|Koeficient změny měřítka|Koeficient změny měřítka|Vzorec, který se<br/>Vypočítat maximální velikost okna|
|Zakázáno|Žádný|Žádný|Velikost okna|
|Omezení|4|2^4|Velikost okna * (2 ^ 4)|
|Vysoce s omezením|2|2^2|Velikost okna * (2 ^ 2)|
|Normální|8|2^8|Velikost okna * (2 ^ 8)|
|Experimentální|14|2^14|Velikost okna * (2 ^ 14)|

Tato nastavení jsou pravděpodobně ovlivňovat výkon TCP, je třeba poznamenat, že mnoho dalších faktorů přes Internet, mimo ovládací prvek Azure, může také ovlivnit výkon TCP.

#### <a name="increase-mtu-size"></a>Zvětšete velikost MTU

Logické otázku položenou je "zvýšení MTU zvýšit výkon TCP jako větší MTU znamená větší MSS"? Jednoduché otázky odpovíte – pravděpodobně není. Jak je popsáno, existují výhody a nevýhody velikost paketu, které se dají použít nad rámec pouze provoz TCP. Jak je popsáno výše, nejdůležitějších faktorů, které ovlivňují výkon propustnost TCP je velikost okna TCP, ztráta paketů a požadavku.

> [!IMPORTANT]
> Azure nedoporučuje, zákazníci Azure měnit výchozí hodnota MTU na virtuálních počítačích.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Akcelerovanými síťovými službami a škálování na straně příjmu

#### <a name="accelerated-networking"></a>Akcelerované síťové služby

Funkce sítě virtuálního počítače v minulosti byly na hostovi virtuálního počítače a hostiteli hypervisoru/intenzivní nároky na procesor. Každý paket tranzitů přes hostitele, který zpracovává v softwaru hostitelského procesoru – včetně všech virtuální sítě zapouzdření/de-capsulation. Ano větší provoz, který prochází hostitele a pak vyšší zatížení procesoru. A pokud procesor hostitele je zaneprázdněn prováděním jiné operace, pak, která ovlivní také latence a propustnosti sítě. Tento problém byl vyřešen prostřednictvím Akcelerovanými síťovými službami.

Akcelerované síťové služby poskytuje konzistentní sítě mimořádně nízkou latenci prostřednictvím interní programovatelný hardware Azure a technologie, jako je SR-IOV. Přesunutím velkou část síťového zásobníku softwarově definovaného pro Azure vypnout procesory a na základě FPGA SmartNICs výpočetní cykly jsou převzaty systémem aplikace koncového uživatele, uvedení menšími nároky na virtuálním počítači, zpoždění a nekonzistence snížení latence. Jinými slovy může být výkon deterministického.

Vylepšení výkonu akcelerovanými síťovými službami dosahuje tím, že hostitel obejít a navázat datapath přímo s SmartNIC hostitele virtuálního počítače hosta. Akcelerovanými síťovými službami výhody:

- **Nižší latenci za vyšší pakety za sekundu (pps)**: Odebrání virtuálního přepínače datapath odebere čas, kdy pakety výdajů v hostiteli pro zpracování zásad a zvyšuje počet paketů, které mohou být zpracovány v tomto virtuálním počítači.

- **Snižuje zpoždění**: Virtuální přepínač zpracování závisí na množství zásady, které je potřeba použít a zatížení procesoru, který provádí zpracování. Snižování zátěže vynucení zásad hardwaru odebere této variabilitě poskytováním pakety přímo k virtuálnímu počítači, odebrání hostitele tak, aby komunikace virtuálních počítačů a všechny softwaru přerušení a přepnutí kontextu.

- **Snížení využití procesoru**: Obcházení virtuální přepínač na hostiteli vede k méně využití procesoru pro zpracování síťového provozu.

Akcelerované síťové služby musí být explicitně povoleno na základě za virtuální počítač. Pokyny pro povolení Akcelerovanými síťovými službami na virtuálním počítači jsou k dispozici na [vytvořit virtuální počítač s Linuxem s Akcelerovanými síťovými službami stránky](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).

#### <a name="receive-side-scaling-rss"></a>Na straně příjmu (RSS)

Škálování na straně příjmu je ovladač technologie sítě, která distribuuje přijímají síťový provoz efektivněji díky distribuci zpracování příjmu napříč více procesorů v systému s více procesory. RSS umožňuje jednoduše řečeno, může systém zpracovávat větší objem přijatých provozu, protože používá všechny dostupné procesory ne o jeden. Další technické informace o RSS najdete tady [Úvod do škálování na straně příjmu stránky](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

RSS je potřebná k dosažení maximálního výkonu, když Akcelerovanými síťovými službami je povolená na virtuálním počítači. Pomocí kanálů RSS na virtuálních počítačích, které nemají povolenými akcelerovanými síťovými službami může být také výhody. Přehled o tom, jak zjistit, zda je technologie RSS zapnutá a konfigurace pro povolení ji najdete tady [optimalizace propustnosti sítě pro virtuální počítače Azure stránku](http://aka.ms/FastVM).

### <a name="tcp-time-wait-and-time-wait-assassination"></a>Čekací čas TCP a doba čekání Assassination

Jiné mají společný problém, který má vliv na výkon sítě a aplikace se nastavení TCP čas čekání. Na zaneprázdněné virtuální počítače, které jsou otevírání a zavírání soketů mnoho, buď jako klient nebo server (zdrojový IP:Source Port a cílový Port IP:Destination), při běžném provozu protokolu TCP daný soketu můžete ukládaly do stavu čekání čas pro podstatnou část času. Tento stav "doba čekání" slouží k povolení dalších dat doručit na soketu před jeho uzavřením. Proto TCP/IP zásobníky obecně zakázat opakované použití soketu tiše přetažením paket TCP SYN klientů.

Toto množství času soketu se v čase stavu čekání lze konfigurovat, ale může sahat od 30 sekund až 240 sekund. Sokety jsou omezené prostředky, a počet soketů, které je možné v daném okamžiku se dají konfigurovat (číslo obecně je přibližně 30 000 potenciálních sockets). Pokud toto číslo je vyčerpá, servery a klienti mají neodpovídající čas čekání nastavení nebo virtuální počítač pokusí pro opětovné použití soketů ve stavu čekání čas, pak nová připojení selže, protože TCP SYN pakety se zahodí bezobslužně.

Hodnota pro rozsah portů pro odchozí sokety, stejně jako nastavení TCP dobu čekat a opětovné použití soketů se obvykle dají konfigurovat v rámci zásobník protokolu TCP/IP operačního systému. Změna tato čísla může potenciálně tak vylepšit škálovatelnost, ale v závislosti na scénáři, může způsobit problémy s interoperabilitou a by měla být změněna opatrně.

Funkci s názvem čas čekání Assassination byl zaveden z tohoto omezení škálování. Doba čekání Assassination umožňuje soketu opětovné použití v rámci určitých scénářů, jako když pořadovému číslu v paketů IP z nové připojení překročí pořadové číslo poslední paketů z předchozí připojení. V takovém případě operační systém vám umožní navázání nové připojení (přijmout nové potvrzení SYN) a vynutit ukončení předchozí připojení, který byl v době čekání stav. Tato funkce se podporuje na virtuálních počítačích s Windows v Azure ještě dnes a podporu v rámci jiné virtuální počítače by se měl prověřit zákazníkům Azure poskytujeme příslušných dodavatelů operačního systému.

Dokumentace k tom, jak nakonfigurovat nastavení protokolu TCP dobu čekat a rozsah zdrojových portů je k dispozici na [nastavení, které můžete upravit tak, aby stránka zlepšit výkon sítě](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Virtuální síť faktory, které mohou ovlivnit výkon

### <a name="vm-maximum-outbound-throughput"></a>Maximální propustnost odchozích virtuálních počítačů

Azure nabízí širokou škálu velikostí virtuálních počítačů a typy, každý s různou kombinaci možností výkonu. Jeden takový výkonové schopnosti je síť propustnost (nebo šířky pásma) měří v MB za sekundu (MB/s). Vzhledem k tomu virtuální počítače hostují na sdíleném hardwaru, musí být kapacita šířky sdílené poměrně mezi virtuálními počítači, které sdílení stejného hardwaru. Větší virtuální počítače mají při přidělování relativně větší šířku pásma než menších virtuálních počítačích.

Šířka pásma sítě, které jsou přidělené na každý virtuální počítač se měří na výchozí přenos (odchozí) z virtuálního počítače. Veškerý přenos v síti byste museli opustit virtuální počítač se počítá směrem k přidělené limit, bez ohledu na cílový. Například pokud virtuální počítač má limit 1 000 MB/s, toto omezení platí, zda je odchozí provoz určený pro jiný virtuální počítač ve stejné virtuální síti nebo mimo Azure.
Příchozí přenos dat se měří nebo jsou omezena přímo. Existují však dalších faktorů, jako jsou omezení úložiště a procesoru, které můžou ovlivnit virtuálního počítače není schopen zpracovávat příchozí data.

Akcelerované síťové služby se funkci navrženou pro zvýšení výkonu sítě, včetně latence, propustnosti a využití procesoru. Akcelerovanými síťovými službami může zlepšit propustnost virtuálních počítačů, je tak učinit pouze až k virtuálnímu počítači přidělená šířky pásma.

Virtuální počítače Azure musí mít jeden, ale může mít několik, síťová rozhraní připojená k nim. Šířka pásma přidělená k virtuálnímu počítači je součet veškerého odchozího provozu ve všech síťových rozhraní připojených k virtuálnímu počítači. Jinými slovy přidělené šířky pásma je na virtuální počítač, bez ohledu na to, kolik síťových rozhraní, které jsou připojené k virtuálnímu počítači.
 
Očekávaná výstupní propustnost a počet síťových rozhraní, které podporuje všechny velikosti virtuálních počítačů je podrobně popsán tady. Pokud chcete zobrazit maximální propustnost, vyberte typ, jako je například pro obecné účely, pak vyberte velikost series na výsledný stránky, například řady Dv2-series. Každé datové řady obsahuje tabulku s sítě specifikace v poslední sloupec s názvem, maximální počet síťových karet / očekávaný výkon sítě (MB/s).

Omezení propustnosti platí pro virtuální počítač. Propustnost je tato akce vliv následující faktory:

- **Počet síťových rozhraní**: Limit šířky pásma je kumulativní z veškerého odchozího provozu z virtuálního počítače.

- **Akcelerované síťové služby**: I když tato funkce může být užitečné při dosažení limitu publikované, nezmění limit.

- **Určení provozu**: Všechny cíle počítají odchozí limit.

- **Protokol**: Veškerý odchozí provoz přes všechny protokoly započítává limitu.

A [tabulky maximální šířka pásma podle typu virtuálního počítače můžete zobrazit tak navštívit tuto stránku](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) a kliknete na příslušný typ virtuálního počítače. Na každé stránce Typ tabulky se zobrazí maximální síťových adaptérů a očekávané maximální šířku pásma.

Další informace o šířku pásma sítě virtuálních počítačů najdete v [šířky pásma sítě virtuálních počítačů](http://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Důležité informace o výkonu Internet

Jak je popsáno v tomto článku, faktory na Internetu a mimo ovládací prvek Azure může ovlivnit výkon sítě. Tyto faktory jsou:

- **Latence**: Dobu odezvy mezi dvěma uzly mohou být ovlivněny problémy na zprostředkující sítí, provoz nepřijetí "" nejkratší cesty je to možné a neoptimální cesty partnerského vztahu

- **Ztráta paketů**: Ztráta paketů může být způsobeno zahlcení sítě, fyzickou cestu problémy a v části provádění síťových zařízení

- **Velikost MTU/fragmentace**: Fragmentaci na cestě může mít za následek zpožděním při přijetí dat nebo pakety přicházejících mimo pořadí, které mohou ovlivnit doručování paketů

Nástroj vhodný pro měření výkonu charakteristiky sítě (třeba ztrátu paketů a latenci) podél každé cesty sítě mezi zdrojovým a cílovým zařízením, je Traceroute.

### <a name="network-design-considerations"></a>Aspekty návrhu sítě

Spolu s výše uvedené důležité informace o topologii virtuální sítě může ovlivnit výkon virtuální sítě. Například střed a paprsek návrhu, že se provoz Zpětná doprava globálně do jedné centrální virtuální síti představují latence sítě a tak ovlivnit celkový výkon sítě. Podobně platí počet síťových zařízení, které síťový provoz prochází může také ovlivnit celkovou latenci. Například v hvězdicové návrhu, pokud přenos prochází přes paprsku síťové virtuální zařízení a virtuální zařízení centra před přechodem mezi protokoly k Internetu, pak latence mohou být způsobeny síťových virtuálních zařízení.

### <a name="azure-regions-virtual-networks-and-latency"></a>Oblasti Azure, virtuální sítě a latence

Oblasti Azure, které se skládá z několika datových centrech, které existují v rámci hlavní zeměpisné oblasti. Tato datová centra nesmí být fyzicky vedle sebe a v některých případech může být odděleno co 10 kilometrů. Virtuální síť je logický překrytí přes síť fyzických datových center Azure a virtuální sítě není určeno žádné konkrétní síťové topologie v rámci datového centra. Například A virtuálních počítačů a virtuálních počítačů B jsou ve stejné virtuální sítě a podsítě, ale může být v různých skříních, i datová centra nebo řádků. Může být odděleny nohou kabel optického vlákna nebo kilometrů kabel optického vlákna. Tato skutečnost může představovat variabilní latence (rozdíl několik milisekund) mezi různé virtuální počítače.

Tento zeměpisné umístění a proto latence mezi dvěma virtuálními počítači, může být ovlivněny prostřednictvím konfigurace skupiny dostupnosti a zóny dostupnosti, ale vzdálenost mezi datacentry v oblasti jsou specifická pro oblasti a převážně ovlivněna topologie datového centra v oblasti.

### <a name="source-nat-port-exhaustion"></a>Vyčerpání portů NAT zdroje

Nasazení v Azure může komunikovat s koncovými body mimo Azure do veřejného Internetu nebo veřejný prostor IP adres. Když tento odchozí připojení iniciuje, instance, Azure dynamicky mapuje privátní IP adresu na veřejnou IP adresu. Po vytvoření tohoto mapování návratový přenos pro tento tok odchozí pocházející ze dosáhnout taky privátní IP adresa původu toku.

Pro každou odchozí připojení nástroje pro vyrovnávání zatížení Azure, musíte mít toto mapování nějakou dobu. S více tenanty povaze Azure údržbu toto mapování pro každý odchozí tok pro každý virtuální počítač může být náročná. Proto existují omezení, které nastavují a v závislosti na konfiguraci služby Azure Virtual Network. Nebo přesněji - uvedeno virtuálního počítače Azure lze vytvořit pouze počet odchozích připojení v daném okamžiku. Když tato omezení jsou vyčerpá, pak virtuální počítač Azure se zakáže jakékoli další odchozí připojení.

Toto chování je však konfigurovatelné. Další informace o [SNAT a SNAT portu vyčerpání], naleznete v tématu [v tomto článku](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Měřit výkon sítě v Azure

Počet maximální hodnoty výkonu v tomto článku se vztahují k latenci sítě / round-trip doba mezi dvěma virtuálními počítači. Tato část obsahuje několik návrhů pro testování latence/požadavku, jakož i TCP výkonu a výkon sítě virtuálních počítačů. TCP/IP & síťové hodnoty probírali výše můžete ladit a výkonu testovat pomocí technik popsaných níže. Hodnoty latence, MTU, MSS a velikost okna je možné ve výpočtech uvedené výše a teoretický maximální hodnoty je možné porovnat s skutečnými hodnotami zjištěnými během testování.

### <a name="measure-round-trip-time-and-packet-loss"></a>Měření času jejich návratu a ztráta paketů

TCP výkonu spoléhá na požadavku a ztráta paketů. Nejjednodušší způsob, jak měřit požadavku a ztráta paketů je pomocí příkazu ping nástroj, který je k dispozici ve Windows a Linux. Minimální/maximální/průměrný latence mezi zdrojový a cílový i ztráta paketů se zobrazí výstup příkazu ping. Ve výchozím nastavení používá příkaz ping protokolu ICMP. K testování protokolu TCP požadavku, pak PsPing lze použít. Další informace o PsPing je k dispozici na [tento odkaz](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Míra Skutečná propustnost připojení TCP

NTttcp je nástroj, který se používá k testování protokolu TCP výkonu systému Linux nebo Windows virtuální počítač. Může být různá nastavení protokol TCP tweaked a výhody testována pomocí NTttcp. Další informace o NTttcp najdete v níže uvedených odkazů.

- [Šířka pásma nebo propustnosti testování (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [Nástroj NTttcp](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Skutečné šířky pásma míru virtuálního počítače

Testování výkonu různých typů virtuálních počítačů, Akcelerovanými síťovými službami a tak dále, lze otestovat pomocí nástrojů volá Iperf také k dispozici v Linuxu a Windows. Iperf slouží k otestování celkovou propustnost sítě TCP nebo UDP. TCP propustnost testy pomocí Iperf jsou ovlivněny na faktorech popsaných v tomto článku (latenci požadavku a tak dále). UDP tedy může přinést lepší výsledky pro testování jednoduše maximální propustnost.

Další informace najdete níže:

- [Řešení potíží s výkon sítě pro Expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Ověření propustnosti sítě VPN do virtuální sítě](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Zjištění neefektivní chování protokolu TCP

Zákazníci Azure můžou zobrazit pakety protokolu TCP s příznaky TCP (výběrové POTVRZOVÁNÍ, DUP ACK, opakování a rychlé opakování přenosu) v zachytávání paketů, jež mohou značit problémy s výkonem sítě. Tyto pakety konkrétně určit síť nedostatků v důsledku ztráta paketů. Ztráta paketů je však není nutně z důvodu problémů s výkonem Azure. Problémy s výkonem, může být způsobeno aplikace, operačního systému nebo jiných problémů, které nemusí být přímo související s platformou Azure. Je také důležité si uvědomit, že některé opakovaný přenos zpráv nebo duplicitní potvrzení v síti je normální – protokoly TCP, které byly vytvořeny spolehlivé. A doklad o tyto pakety protokolu TCP v zachytávání paketů nemusí nutně znamenat problém systémové sítě Pokud nejsou nadměrné.

Nicméně by mělo být stanoveno jasně, že jsou tyto typy paketů označení, že propustnost TCP nedosahuje jeho maximální výkon – z důvodů popsaných v další části.
