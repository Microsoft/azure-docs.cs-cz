---
title: Ladění výkonu TCP/IP pro virtuální počítače Azure | Microsoft Docs
description: Seznamte se s různými běžnými postupy ladění výkonu protokolu TCP/IP a jejich vztahem k virtuálním počítačům Azure.
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
ms.openlocfilehash: 67b635f09cb9407279e89b5f7b8526dab3c08946
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017606"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>Ladění výkonu protokolu TCP/IP pro virtuální počítače Azure

Tento článek popisuje běžné techniky ladění výkonu protokolu TCP/IP a některé věci, které je potřeba vzít v úvahu při jejich použití pro virtuální počítače běžící v Azure. Poskytne vám základní přehled postupů a prozkoumejte, jak je možné je vyladit.

## <a name="common-tcpip-tuning-techniques"></a>Běžné techniky ladění protokolu TCP/IP

### <a name="mtu-fragmentation-and-large-send-offload"></a>MTU, fragmentace a velké přesměrování odesílání

#### <a name="mtu"></a>HODNOTU

Maximální přenosová jednotka (MTU) je největší rámec (paket), který je určen v bajtech, který lze odeslat přes síťové rozhraní. MTU je konfigurovatelné nastavení. Výchozí jednotka MTU používaná na virtuálních počítačích Azure a výchozí nastavení u většiny síťových zařízení je 1 500 bajtů.

#### <a name="fragmentation"></a>Fragmentace

Fragmentace probíhá při odeslání paketu, který překračuje jednotku MTU síťového rozhraní. Zásobník protokolu TCP/IP zruší paket na menší části (fragmenty), které odpovídají jednotce MTU rozhraní. Fragmentace probíhá na vrstvě IP a je nezávislá na základním protokolu (například TCP). Je-li paket 2 000 odeslán přes síťové rozhraní s jednotkou MTU 1 500, bude paket rozdělen na 1 1 500-bajtový paket a na 1 500 bajtů.

Síťová zařízení v cestě mezi zdrojem a cílem mohou buď vyřadit pakety, které překračují jednotku MTU, nebo fragmentovat paket do menších částí.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>Bit nefragmentování v paketu IP

Bit nefragmentování (DF) je příznak v hlavičce protokolu IP. Bit DF označuje, že síťová zařízení v cestě mezi odesílatelem a příjemcem nesmí fragmentovat paket. Tento bit lze nastavit z mnoha důvodů. (Příklad najdete v části "zjišťování MTU Path" v tomto článku.) Když síťové zařízení obdrží paket s nastaveným bitem nefragmentování a tento paket překračuje hodnotu MTU zařízení, standardní chování je, aby zařízení vynechal paket. Zařízení pošle zprávu potřebná ke fragmentaci ICMP zpátky do původního zdroje paketu.

#### <a name="performance-implications-of-fragmentation"></a>Dopad fragmentace na výkon

Fragmentace může mít negativní dopad na výkon. Jedním z hlavních důvodů pro účinek na výkon je dopad fragmentace a opětovného sestavení paketů na procesor nebo paměť. Když síťové zařízení potřebuje fragmentovat paket, bude muset přidělit prostředky procesoru nebo paměti, aby bylo možné provést fragmentaci.

Ke stejné situaci dojde, když se paket znovu sestaví. Síťové zařízení musí ukládat všechny fragmenty, dokud se neobdrží, aby je bylo možné znovu sestavit do původního paketu. Tento proces fragmentace a opětovného sestavení může také způsobit latenci.

Dalším možným negativním snížením výkonu fragmentace je, že fragmenty paketů mohou být doručeny mimo pořadí. V případě, že jsou pakety přijímány mimo pořadí, některé typy síťových zařízení je mohou odstranit. Pokud k tomu dojde, je nutné znovu přenést celý paket.

Fragmenty jsou obvykle vyhozeny bezpečnostními zařízeními, jako jsou síťové brány firewall, nebo když jsou vyčerpány vyrovnávací paměti pro příjem síťového zařízení. Když jsou vyčerpány vyrovnávací paměti pro příjem síťového zařízení, síťové zařízení se pokouší znovu sestavit fragmentovaný paket, ale nemá prostředky k uložení a přebírání paketů.

Fragmentace se může zobrazit jako negativní operace, ale podpora pro fragmentaci je nutná, když propojujete různé sítě přes Internet.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Výhody a důsledky změny jednotky MTU

Obecně řečeno, můžete vytvořit efektivnější síť zvýšením hodnoty MTU. Každý odeslaný paket má informace záhlaví, které jsou přidány do původního paketu. Když fragmentace vytváří více paketů, bude k dispozici více režijních nákladů na hlavičku a síť bude méně efektivní.

Tady je příklad. Velikost hlavičky sítě Ethernet je 14 bajtů plus sekvence kontroly snímků se čtyřmi bajty, aby se zajistila konzistence snímků. Pokud se pošle paket 1 2 000, do sítě se přidá 18 bajtů režie v síti Ethernet. Pokud je paket fragmentován do paketu 1 500-Byte a na 500 paketu, bude mít každý paket 18 bajtů hlavičky Ethernet, celkem 36 bajtů.

Mějte na paměti, že zvýšení hodnoty MTU nemusí nutně vytvořit efektivnější síť. Pokud aplikace odesílá jenom 500 paketů, bude se stejná režie hlavičky vyskytovat, pokud je jednotka MTU 1 500 bajtů nebo 9 000 bajtů. Síť bude efektivnější jenom v případě, že využívá větší velikosti paketů, na které má jednotka MTU vliv.

#### <a name="azure-and-vm-mtu"></a>MTU Azure a virtuálního počítače

Výchozí jednotka MTU pro virtuální počítače Azure je 1 500 bajtů. Sada Azure Virtual Network stack se pokusí fragmentovat paket 1 400 bajtů.

Všimněte si, že zásobník Virtual Network není ve své podstatě neefektivní, protože fragmentuje pakety 1 400 bajtů, i když virtuální počítače mají jednotku MTU 1 500. Vysoké procento síťových paketů je mnohem menší než 1 400 nebo 1 500 bajtů.

#### <a name="azure-and-fragmentation"></a>Azure a fragmentace

Sada Virtual Network Stack je nastavená tak, aby vynechal "neúplné fragmenty", tj. fragmentované pakety, které nepřicházejí do původní fragmentované objednávky. Tyto pakety jsou vyhozeny hlavně z důvodu ohrožení zabezpečení sítě v listopadu 2018 s názvem FragmentSmack.

FragmentSmack je vada způsobu, jakým jádro systému Linux zpracovává opětovné sestavení fragmentovaných paketů protokolu IPv4 a IPv6. Vzdálený útočník může tuto chybu zneužít ke spuštění náročné operace opětovného sestavení fragmentu, což by mohlo vést ke zvýšení kapacity procesoru a k odepření služby v cílovém systému.

#### <a name="tune-the-mtu"></a>Vyladění jednotky MTU

Můžete nakonfigurovat jednotku MTU virtuálního počítače Azure, jak můžete v jakémkoli jiném operačním systému. Při konfiguraci jednotky MTU byste ale měli zvážit fragmentaci, ke které dochází v Azure, popsané výše.

Zákazníkům nedoporučujeme zvyšovat jednotky MTU virtuálních počítačů. Tato diskuze má vysvětlit podrobnosti o tom, jak Azure implementuje MTU a provede fragmentaci.

> [!IMPORTANT]
>Zvýšení hodnoty MTU není známo ke zvýšení výkonu a může mít negativní vliv na výkon aplikace.
>
>

#### <a name="large-send-offload"></a>Velké přesměrování odesílání

Vysoké snižování zátěže (IEEE) může zlepšit výkon sítě snižováním zátěže segmentů paketů na adaptér sítě Ethernet. Pokud je povolený IEEE, vytvoří zásobník protokolu TCP/IP velký paket TCP a pošle ho do adaptéru Ethernet pro segmentaci před přesměrováním. Výhodou přesměrování je to, že může uvolnit procesor z segmentace paketů do velikosti, které odpovídají jednotce MTU, a přesměrovat zpracování na rozhraní sítě Ethernet, kde se provádí v hardwaru. Další informace o výhodách IEEE najdete v tématu [Podpora většího snižování zátěže](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

Když je přípravek povolený, můžou zákazníci Azure při provádění zachycení paketů zobrazovat velké velikosti snímků. Tyto velké velikosti snímků můžou vést k tomu, že někteří zákazníci dostanou fragmentaci nebo že se používá velká jednotka MTU, pokud není. Pomocí technologie IEEE může adaptér sítě Ethernet inzerovat větší maximální velikost segmentu (MSS) do zásobníku protokolu TCP/IP a vytvořit tak větší paket TCP. Celý nesegmentující rámec se pak přepošle na adaptér Ethernet a bude viditelný v zachytávání paketů provedeném na virtuálním počítači. Paket se ale bude rozdělit do několika menších snímků, a to na základě MTU adaptéru Ethernet.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>Škálování okna TCP MSS a PMTUD

#### <a name="tcp-maximum-segment-size"></a>Maximální velikost segmentu TCP

Maximální velikost segmentu protokolu TCP (MSS) je nastavení, které omezuje velikost segmentů TCP, což zabrání fragmentaci paketů TCP. Operační systémy obvykle pomocí tohoto vzorce nasadí hodnotu MSS:

`MSS = MTU - (IP header size + TCP header size)`

Záhlaví protokolu IP a hlavičkou TCP jsou 20 bajtů nebo celkem 40 bajtů. Takže rozhraní s jednotkou MTU 1 500 bude mít velikost MSS 1 460. Ale MSS je konfigurovatelné.

Toto nastavení se při nastavení relace TCP mezi zdrojovým a cílovým způsobem dohodlo na třícestných handshakích handshake protokolu TCP. Obě strany odesílají hodnotu MSS a nižší z nich se používá pro připojení TCP.

Pamatujte, že jednotky MTU zdroje a cíle nejsou jediným činitelům, které určují hodnotu MSS. Zprostředkující síťová zařízení, jako jsou brány VPN, včetně služby Azure VPN Gateway, můžou upravit jednotku MTU nezávisle na zdroji a cíli, aby se zajistil optimální výkon sítě.

#### <a name="path-mtu-discovery"></a>Zjišťování jednotky MTU cesty

MSS je vyjednáno, ale nemusí indikovat skutečnou hodnotu MSS, kterou lze použít. Důvodem je, že jiná síťová zařízení v cestě mezi zdrojem a cílem mohou mít nižší hodnotu MTU než zdroj a cíl. V takovém případě zařízení, jejichž jednotka MTU je menší než paket, tento paket vynechá. Zařízení pošle zpět potřebné fragmentace ICMP (typ 3, kód 4), který obsahuje jeho jednotku MTU. Tato zpráva ICMP umožňuje zdrojovému hostiteli odpovídajícím způsobem zmenšit cestu jednotky MTU. Tento proces se nazývá zjišťování MTU Path (PMTUD).

Proces PMTUD je neefektivní a má vliv na výkon sítě. Po odeslání paketů, které překračují velikost jednotky MTU síťové cesty, je nutné pakety znovu přenést pomocí nižší hodnoty MSS. Pokud odesílatel neobdrží zprávu potřebná ke fragmentaci ICMP, možná z důvodu síťové brány firewall v cestě (obvykle označované jako *PMTUD Blackhole*), odesílatel neví, že potřebuje snížit hodnotu MSS a bude nepřetržitě znovu přenášet paket. Z tohoto důvodu nedoporučujeme zvyšovat velikost jednotky MTU virtuálního počítače Azure.

#### <a name="vpn-and-mtu"></a>Sítě VPN a MTU

Pokud používáte virtuální počítače, které provádějí zapouzdření (například sítě VPN IPsec), existují další požadavky týkající se velikosti paketů a jednotky MTU. Sítě VPN přidávají k paketům další hlavičky, které zvyšují velikost paketů a vyžadují menší hodnotu MSS.

V případě Azure doporučujeme, abyste nastavili připojení TCP MSS k 1 350 bajtů a MTU rozhraní pro tunelové připojení na 1 400. Další informace naleznete na [stránce zařízení VPN a parametry protokolu IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Doba odezvy, doba odezvy a škálování okna protokolu TCP

#### <a name="latency-and-round-trip-time"></a>Latence a doba odezvy

Latence sítě závisí na rychlosti světla v síti s optickým vláknem. Propustnost sítě protokolu TCP je také efektivně řízena dobou odezvy (RTT) mezi dvěma síťovými zařízeními.

| Trasa | Vzdálenost | Jednosměrný čas | Doba odezvy |
| ----- | -------- | ------------ | --- |
|New York do San Francisco|4 148 km|21 MS|42 MS|
|Praha až Londýn|5 585 km|28 MS|56 MS|
|Praha až Sydney|15 993 km|80 MS|160 MS|

Tato tabulka zobrazuje vzdálenost mezi dvěma místy v přímém směru. V sítích je vzdálenost obvykle delší než rovná se vzdálenost přímo na řádku. Tady je jednoduchý vzorec pro výpočet minimální doby odezvy podle rychlosti světla:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

Pro rychlost šíření můžete použít 200. Jedná se o vzdálenost v kilometrech, která se v kilometrech prochází za 1 milisekundu.

Pojďme jako příklad využít New York do sítě San Francisco. Lineární vzdálenost je 4 148 km. Když se tato hodnota připojíte k rovnici, získáme následující:

`Minimum RTT = 2 * (4,148 / 200)`

Výstup rovnice je v milisekundách.

Pokud chcete dosáhnout nejlepšího výkonu sítě, logické možnosti je vybrat cílová umístění s nejkratší vzdáleností mezi nimi. Měli byste také navrhnout virtuální síť, aby se optimalizoval cesta k provozu a snížila latence. Další informace najdete v části požadavky na návrh sítě v tomto článku.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Vliv na latenci a dobu odezvy v protokolu TCP

Doba odezvy má přímý vliv na maximální propustnost protokolu TCP. V protokolu TCP je *velikost okna* maximální množství přenosů, které je možné odeslat přes připojení TCP před tím, než odesílatel potřebuje přijmout potvrzení od příjemce. Pokud je hodnota protokolu TCP MSS nastavená na 1 460 a velikost okna TCP je nastavená na 65 535, může odesilatel odesílat pakety 45, aby bylo možné přijímat potvrzení od příjemce. Pokud odesílatel neobdrží potvrzení, přenáší data znovu. Tento vzorec vypadá takto:

`TCP window size / TCP MSS = packets sent`

V tomto příkladu se 65 535/1 460 zaokrouhlí na 45.

Toto "čekání na potvrzení" znamená mechanismus pro zajištění spolehlivého doručování dat, což způsobí, že čas RTT má vliv na propustnost protokolu TCP. Čím delší odesilatel čeká na potvrzení, tím déle potřebuje počkat, než se pošle víc dat.

Tady je vzorec pro výpočet maximální propustnosti jednoho připojení TCP:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

Tato tabulka uvádí maximální propustnost v megabajtech/za sekundu jednoho připojení TCP. (V případě čitelnosti se pro měrnou jednotku používá megabajtů.)

| Velikost okna TCP (bajty) | Latence RTT (MS) | Maximální propustnost za sekundu v megabajtech | Maximální propustnost megabitů za sekundu |
| ----------------------- | ---------------- | ---------------------------------- | --------------------------------- |
|65 535|1|65,54|524,29|
|65 535|30|2,18|17,48|
|65 535|60|1.09|8,74|
|65 535|90|.73|5,83|
|65 535|120|.55|4,37|

Pokud dojde ke ztrátě paketů, bude maximální propustnost připojení TCP snížena, zatímco odesilatel znovu odešle data, která již byla odeslána.

#### <a name="tcp-window-scaling"></a>Škálování okna protokolu TCP

Škálování okna protokolu TCP je technika, která dynamicky zvětšuje velikost okna protokolu TCP, aby bylo možné odeslat více dat, než bude vyžadováno potvrzení. V předchozím příkladu by se pakety 45 poslaly předtím, než se vyžaduje potvrzení. Pokud zvýšíte počet paketů, které je možné odeslat předtím, než je vyžadováno potvrzení, snížíte počet, kolikrát odesílatel čeká na potvrzení, což zvyšuje maximální propustnost TCP.

Tato tabulka znázorňuje tyto vztahy:

| Velikost okna TCP (bajty) | Latence RTT (MS) | Maximální propustnost za sekundu v megabajtech | Maximální propustnost megabitů za sekundu |
| ----------------------- | ---------------- | ---------------------------------- | --------------------------------- |
|65 535|30|2,18|17,48|
|131 070|30|4,37|34,95|
|262 140|30|8,74|69,91|
|524 280|30|17,48|139,81|

Ale hodnota hlavičky TCP pro velikost okna protokolu TCP je dlouhá jenom 2 bajty. to znamená, že maximální hodnota pro přijímané okno je 65 535. Pro zvýšení maximální velikosti okna byl zaveden faktor škálování okna TCP.

Faktor škálování je také nastavení, které můžete nakonfigurovat v operačním systému. Tady je vzorec pro výpočet velikosti okna TCP pomocí faktorů škálování:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Zde je výpočet pro faktor zmenšení okna na 3 a velikost okna 65 535:

`65,535 \* (2^3) = 262,140 bytes`

Faktor škálování o hodnotě 14 má za následek velikost okna protokolu TCP o hodnotě 14 (maximální povolený posun). Velikost okna protokolu TCP bude 1 073 725 440 bajtů (8,5 gigabites).

#### <a name="support-for-tcp-window-scaling"></a>Podpora škálování okna protokolu TCP

Systém Windows může nastavit různé faktory škálování pro různé typy připojení. (Třídy připojení zahrnují datové centrum, Internet atd.) Pomocí `Get-NetTCPConnection` příkazu PowerShellu zobrazíte typ připojení škálování okna:

```powershell
Get-NetTCPConnection
```

`Get-NetTCPSetting`K zobrazení hodnot jednotlivých tříd můžete použít příkaz prostředí PowerShell:

```powershell
Get-NetTCPSetting
```

Počáteční velikost okna TCP a faktor škálování TCP v systému Windows můžete nastavit pomocí `Set-NetTCPSetting` příkazu prostředí PowerShell. Další informace najdete v tématu  [set-NetTCPSetting](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps).

```powershell
Set-NetTCPSetting
```

Jedná se o platná nastavení TCP pro `AutoTuningLevel` :

| AutoTuningLevel | Faktor škálování | Násobení měřítka | Vzorec do<br/>vypočítat maximální velikost okna |
| --------------- | -------------- | ------------------ | -------------------------------------------- |
|Zakázáno|Žádné|Žádné|Velikost okna|
|S omezeným přístupem|4|2 ^ 4|Velikost okna * (2 ^ 4)|
|Vysoce omezené|2|2 ^ 2|Velikost okna * (2 ^ 2)|
|Normální|8|2 ^ 8|Velikost okna * (2 ^ 8)|
|Experimentální|14|2 ^ 14|Velikost okna * (2 ^ 14)|

Tato nastavení jsou pravděpodobně ovlivněná výkonem protokolu TCP, ale mějte na paměti, že výkon protokolu TCP může mít i mnoho dalších faktorů přes Internet, mimo kontrolu Azure.

#### <a name="increase-mtu-size"></a>Zvětšit velikost jednotky MTU

Protože větší jednotka MTU znamená větší MSS, můžete se setkat s tím, že zvýšení hodnoty MTU může zvýšit výkon protokolu TCP. Pravděpodobně ne. Existují specialisté a nevýhodné velikosti paketů nad rámec pouze provozu TCP. Jak je popsáno výše, nejdůležitější faktory ovlivňující propustnost protokolu TCP jsou velikost okna TCP, ztráty paketů a čas RTT.

> [!IMPORTANT]
> Nedoporučujeme, aby zákazníci Azure změnili výchozí hodnotu MTU na virtuálních počítačích.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Urychlené síťové škálování a škálování na straně příjmu

#### <a name="accelerated-networking"></a>Urychlení sítě

Síťové funkce virtuálních počítačů byly v minulosti náročné na výkon procesoru na virtuálním počítači hosta i na hypervisoru nebo na hostiteli. Každý paket, který přechází přes hostitele, se zpracovává na softwaru prostřednictvím procesoru hostitele, včetně zapouzdření a tehdyů virtuální sítě. Čím víc přenosů prochází hostiteli, tím vyšší zatížení procesoru. A pokud je procesor hostitele zaneprázdněný jinými operacemi, bude mít vliv také na propustnost a latenci sítě. Azure tento problém řeší pomocí akcelerovaných síťových služeb.

Akcelerovaná síť zajišťuje konzistentní latenci ultralow sítě prostřednictvím programovatelnýho programovatelného hardwaru Azure a technologií, jako je SR-IOV. Urychlené síťové zapojení z mnoha softwarově definovaných sítí Azure vychází z procesorů a do SmartNICs založených na FPGA. Tato změna umožňuje aplikacím koncových uživatelů uvolnit výpočetní cykly, což vede k menšímu zatížení virtuálního počítače, snížení kolísání a nekonzistence při latenci. Jinými slovy, výkon může být více deterministický.

Urychlené síťové funkce zvyšují výkon tím, že virtuálnímu počítači hosta obchází hostitele a naváže datacesta přímo k SmartNIC hostitele. Zde jsou některé výhody akcelerovaného síťového připojení:

- **Nižší latence/vyšší počet paketů za sekundu (PPS)**: Odebrání virtuálního přepínače z DataPath eliminuje dobu trvání paketů v hostiteli pro zpracování zásad a zvyšuje počet paketů, které mohou být zpracovány na virtuálním počítači.

- **Snížená kolísání**: zpracování virtuálních přepínačů závisí na množství zásad, které je potřeba použít, a na zatížení procesoru, který provádí zpracování. Přesměrování vynucení zásad na hardware odstraní tuto variabilitu tím, že doručí pakety přímo virtuálnímu počítači a odstraní komunikaci mezi hostiteli a všemi softwarovými přerušeními a přepínači kontextu.

- **Snížení využití CPU**: obejít virtuální přepínač v hostiteli vede k menšímu využití procesoru při zpracování síťového provozu.

Pokud chcete používat urychlené síťové služby, musíte je explicitně povolit na každém příslušném virtuálním počítači. Pokyny najdete v tématu [Vytvoření virtuálního počítače se systémem Linux s akcelerovanými síťovými](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) službami.

#### <a name="receive-side-scaling"></a>Škálování na straně příjmu

Škálování na straně příjmu (RSS) je technologie síťového ovladače, která distribuuje příjem síťového provozu efektivněji distribucí procesu příjmu mezi více procesorů v systému s více procesory. Technologie RSS v jednoduchém smyslu umožňuje systému zpracovat více přijímaných přenosů, protože používá všechny dostupné procesory, nikoli jenom jeden. Další technické diskuzi o technologii RSS najdete v tématu [Úvod do škálování na straně příjmu](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

Pokud chcete získat nejlepší výkon, když je na virtuálním počítači povolené urychlené síťové služby, je potřeba povolit RSS. RSS může také poskytovat výhody pro virtuální počítače, které nevyužívají urychlené síťové služby. Přehled toho, jak zjistit, jestli je povolený RSS a jak ho povolit, najdete v tématu [optimalizace propustnosti sítě pro virtuální počítače Azure](https://aka.ms/FastVM).

### <a name="tcp-time_wait-and-time_wait-assassination"></a>TIME_WAIT TCP a TIME_WAIT Assassination

TIME_WAIT TCP je další běžné nastavení, které má vliv na výkon sítě a aplikace. V případě zaneprázdněných virtuálních počítačů, které otevírají a uzavírá mnoho soketů, buď jako klienti nebo servery (zdrojová IP adresa: zdrojový port + cílová IP adresa: cílový port), může během normálního provozu protokolu TCP skončit daný soket ve stavu TIME_WAIT po dlouhou dobu. Stav TIME_WAIT slouží k tomu, aby bylo možné doručovat další data do soketu před jeho zavřením. Protokoly TCP/IP obecně zabraňují opakovanému použití soketu tím, že budou tiše vycházet z paketu TCP SYN klienta.

Množství času, ve kterém je soket v TIME_WAIT lze konfigurovat. Může být v rozsahu 30 sekund až 240 sekund. Sokety jsou konečným prostředkem a je možné nakonfigurovat počet soketů, které lze v daném čase použít. (Počet dostupných soketů je obvykle přibližně 30 000.) Pokud se dostupné sokety vybírají nebo pokud se neshodují klienti a servery TIME_WAIT nastavení a virtuální počítač se pokusí znovu použít soket ve stavu TIME_WAIT, nová připojení selžou, protože pakety TCP SYN budou tiše vyřazeny.

Hodnota pro rozsah portů pro odchozí sokety je obvykle konfigurovatelná v zásobníku protokolu TCP/IP operačního systému. Totéž platí pro nastavení TIME_WAIT protokolu TCP a opakované použití soketu. Změna těchto čísel může potenciálně zlepšit škálovatelnost. V závislosti na situaci ale můžou tyto změny způsobovat problémy s interoperabilitou. Pokud tyto hodnoty změníte, měli byste být opatrní.

K vyřešení tohoto omezení škálování můžete použít TIME_WAIT Assassination. TIME_WAIT Assassination umožňuje znovu použít soket v určitých situacích, například když pořadové číslo v paketu IP nového připojení přesáhne pořadové číslo posledního paketu z předchozího připojení. V takovém případě bude operační systém umožňovat navázání nového připojení (přijme novou hodnotu SYN/ACK) a vynutí zavření předchozího připojení, které bylo ve stavu TIME_WAIT. Tato funkce se podporuje na virtuálních počítačích s Windows v Azure. Pokud se chcete dozvědět o podpoře jiných virtuálních počítačů, obraťte se na dodavatele operačního systému.

Další informace o konfiguraci nastavení TIME_WAIT TCP a rozsahu zdrojových portů najdete v tématu [nastavení, která je možné upravit za účelem zlepšení výkonu sítě](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Faktory virtuální sítě, které mohou ovlivnit výkon

### <a name="vm-maximum-outbound-throughput"></a>Maximální odchozí propustnost virtuálního počítače

Azure poskytuje nejrůznější velikosti a typy virtuálních počítačů, z nichž každá má různou kombinaci možností výkonu. Jednou z těchto funkcí je propustnost sítě (neboli šířka pásma), která se měří v megabajtech za sekundu (MB/s). Vzhledem k tomu, že virtuální počítače jsou hostované na sdíleném hardwaru, musí být síťová kapacita sdílená mezi virtuálními počítači, které používají stejný hardware. Větším virtuálním počítačům se přidělí větší šířka pásma než menší virtuální počítače.

Šířka pásma sítě přidělená každému virtuálnímu počítači se měří na odchozím (odchozím) provozu z virtuálního počítače. Veškerý síťový provoz opouštějící virtuální počítač se započítává k přidělenému limitu bez ohledu na cíl. Pokud má virtuální počítač například omezení 1 000 – MB/s, platí toto omezení, zda je odchozí provoz určen pro jiný virtuální počítač ve stejné virtuální síti nebo v jednom mimo Azure.

Příchozí přenos dat není měřený ani omezený přímo. Existují však i jiné faktory, jako jsou limity procesoru a úložiště, které mohou ovlivnit schopnost virtuálního počítače zpracovávat příchozí data.

Akcelerované síťové služby jsou navržené tak, aby vylepšily výkon sítě, včetně latence, propustnosti a využití procesoru. Akcelerované síťové služby můžou zlepšit propustnost virtuálního počítače, ale můžou to udělat jenom s přidělenou šířkou pásma virtuálního počítače.

Virtuální počítače Azure mají k těmto počítačům připojené aspoň jedno síťové rozhraní. Můžou mít několik. Šířka pásma přidělená virtuálnímu počítači je součet všech odchozích přenosů napříč všemi síťovými rozhraními připojenými k počítači. Jinými slovy, Šířka pásma se přiděluje na základě jednotlivých virtuálních počítačů bez ohledu na to, kolik síťových rozhraní je k počítači připojené.

Očekávaná odchozí propustnost a počet síťových rozhraní podporovaných jednotlivými velikostmi virtuálních počítačů jsou podrobně popsány v části [velikosti pro virtuální počítače s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Chcete-li zobrazit maximální propustnost, vyberte typ, například **obecný účel**, a pak vyhledejte část o řadě velikostí na výsledné stránce (například "Dv2-Series"). Pro každou řadu je k dispozici tabulka, která poskytuje specifikace sítě v posledním sloupci s názvem "maximální počet síťových adaptérů/očekávaná šířka pásma sítě (MB/s)".

Limit propustnosti se vztahuje na virtuální počítač. Propustnost není ovlivněná těmito faktory:

- **Počet síťových rozhraní**: omezení šířky pásma se vztahuje na součet všech odchozích přenosů z virtuálního počítače.

- **Akcelerované síťové služby**: i když tato funkce může být užitečná při dosahování publikovaného limitu, tento limit nemění.

- **Cíl provozu**: celkový počet cílů směrem k odchozímu limitu.

- **Protokol**: veškerý odchozí provoz ve všech protokolech počítá směrem k limitu.

Další informace najdete v tématu [Šířka pásma sítě virtuálních počítačů](https://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Požadavky na výkon Internetu

Jak je popsáno v tomto článku, faktory na internetu a mimo kontrolu Azure mohou ovlivnit výkon sítě. Tady jsou některé z těchto faktorů:

- **Latence**: doba odezvy mezi dvěma místy může být ovlivněná chybami v zprostředkujících sítích, a to pomocí provozu, který nepřijímá "nejkratší" cestu o vzdálenosti a má dílčí optimální cesty partnerských vztahů.

- **Ztráta paketů**: ztráta paketů může být způsobena zahlcením sítě, problémy fyzické cesty a podsítěmi síťových zařízení.

- **Velikost nebo fragmentace jednotky MTU**: fragmentaci podél cesty může vést k prodlevám při přijímání dat nebo v paketech přicházejících mimo pořadí, což může ovlivnit doručování paketů.

Traceroute je dobrým nástrojem pro měření charakteristik výkonu sítě (jako jsou ztráty paketů a latence) společně s každou síťovou cestou mezi zdrojovým a cílovým zařízením.

### <a name="network-design-considerations"></a>Požadavky na návrh sítě

Spolu s důležitými informacemi, které jsou popsané dříve v tomto článku, může topologie virtuální sítě ovlivnit výkon sítě. Například návrh centra a paprsků, který zastavuje provoz globálně do jediného rozbočovače, zavede latenci sítě, což bude mít vliv na celkový výkon sítě.

Počet síťových zařízení, která procházejí přenosem přes síť, může také ovlivnit celkovou latenci. Například v případě návrhu centra a paprsků, pokud provoz projde virtuálním zařízením s paprsky a virtuálním zařízením rozbočovače před přechodem na Internet, může tato síťová virtuální zařízení způsobit latenci.

### <a name="azure-regions-virtual-networks-and-latency"></a>Oblasti, virtuální sítě a latence Azure

Oblasti Azure jsou tvořené více datacentry, které existují v obecné geografické oblasti. Tato datová centra nemusí být fyzicky vedle sebe. V některých případech jsou oddělené až o 10 km. Virtuální síť je logický překryv nad fyzickou sítí datacenter Azure. Virtuální síť neznamená v datacentru žádnou konkrétní topologii sítě.

Například dva virtuální počítače, které jsou ve stejné virtuální síti a v podsíti, mohou být v různých skříních, řádcích nebo i datových centrech. Mohou být odděleny nohou kabel optického kabelu nebo kilometry kabelů optického kabelu. Tato variace by mohla zavést proměnlivou latenci (rozdíl mezi několika milisekundami) mezi různými virtuálními počítači.

Zeměpisné umístění virtuálních počítačů a potenciální latence mezi dvěma virtuálními počítači může být ovlivněné konfigurací skupin dostupnosti a Zóny dostupnosti. Ale vzdálenost mezi datovými centry v oblasti je specifická pro oblast a primárně je ovlivněná topologií Datacenter v oblasti.

### <a name="source-nat-port-exhaustion"></a>Vyčerpání portů NAT zdrojového kódu

Nasazení v Azure může komunikovat s koncovými body mimo Azure na veřejném Internetu a na veřejném IP prostoru. Když instance inicializuje odchozí připojení, Azure dynamicky mapuje privátní IP adresu na veřejnou IP adresu. Když Azure toto mapování vytvoří, může návratový přenos pro odchozí tok, který tok vytvořil, získat taky privátní IP adresu.

Pro každé odchozí připojení Azure Load Balancer potřebuje toto mapování spravovat po určitou dobu. S využitím víceklientské architektury Azure je udržování tohoto mapování pro každý výstupní tok každého virtuálního počítače náročné na prostředky. Existují však omezení nastavená a založená na konfiguraci Virtual Network Azure. Nebo pokud chcete přesněji říci, že virtuální počítač Azure může v daném okamžiku provádět jenom určitý počet odchozích připojení. Po dosažení tohoto limitu nebude moct virtuální počítač vytvořit další odchozí připojení.

Toto chování je ale možné nakonfigurovat. Další informace o vyčerpání portů SNAT a SNAT najdete v [tomto článku](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Měření výkonu sítě v Azure

Množství maximálního výkonu v tomto článku se vztahuje na latenci sítě/dobu odezvy (RTT) mezi dvěma virtuálními počítači. V této části najdete několik návrhů na testování latence/RTT a postup testování výkonu TCP a sítě virtuálních počítačů. Pomocí technik popsaných v této části můžete ladit a testovat výkon a hodnoty protokolu TCP/IP a sítě, které jsou popsány dříve. Hodnoty pro latenci, MTU, MSS a velikost okna můžete připojit do výše uvedených výpočtů a porovnat teoreticky maximální hodnoty se skutečnými hodnotami, které během testování provedete.

### <a name="measure-round-trip-time-and-packet-loss"></a>Měření doby odezvy a ztráty paketů

Výkon protokolu TCP se intenzivně spoléhá na čas RTT a ztrátu paketů. Nástroj pro odesílání informací v systému Windows a Linux poskytuje nejjednodušší způsob, jak změřit čas a ztrátu paketů. Výstupem nástroje test dat se zobrazí latence minimální/maximální/průměrné doby mezi zdrojem a cílem. Zobrazí se také ztráta paketů. Ve výchozím nastavení používá nástroj test protokolu ICMP protokol ICMP. Pomocí PsPing můžete testovat čas RTT protokolu TCP. Další informace najdete v tématu [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Měření skutečné propustnosti připojení TCP

NTttcp je nástroj pro testování výkonu protokolu TCP pro Linux nebo virtuální počítač s Windows. Můžete změnit různá nastavení TCP a pak testovat výhody pomocí NTttcp. Další informace najdete v těchto zdrojích:

- [Testování šířky pásma a propustnosti (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [Nástroj NTttcp](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Měření skutečné šířky pásma virtuálního počítače

Pomocí nástroje s názvem iPerf můžete testovat výkon různých typů virtuálních počítačů, akcelerovaných síťových a tak dále. iPerf je také k dispozici v systémech Linux a Windows. iPerf může k testování celkové propustnosti sítě použít protokol TCP nebo UDP. testy propustnosti iPerf protokolu TCP ovlivňují faktory popsané v tomto článku (jako latence a čas RTT). To znamená, že pokud chcete pouze testovat maximální propustnost, může vám protokol UDP přinést lepší výsledky.

Další informace najdete v těchto článcích:

- [Řešení potíží s výkonem ExpressRoute sítě](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Ověření propustnosti sítě VPN do virtuální sítě](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Zjištění neefektivních chování protokolu TCP

V záznamech o paketech můžou zákazníci se systémem Azure vidět pakety TCP s příznaky TCP (více než, DUP ACK, znovu přenést a rychle přenášet), které by mohly signalizovat problémy s výkonem sítě. Tyto pakety konkrétně označují neefektivitu sítě, které jsou výsledkem ztráty paketů. Ale ztráta paketů nemusí nutně způsobovat problémy s výkonem Azure. Problémy s výkonem by mohly být výsledkem problémů s aplikacemi, problémy s operačním systémem nebo jinými problémy, které by nemusely být přímo v souvislosti s platformou Azure.

Nezapomeňte také, že některá opakovaná a duplicitní potvrzení jsou v síti normální. Protokoly TCP byly sestaveny tak, aby byly spolehlivé. Legitimace těchto paketů TCP v zachytávání paketů nutně neznamená problém systému sítě, pokud nejsou nadměrné.

Tyto typy paketů jsou pořád označením, že propustnost TCP nedosahuje maximálního výkonu z důvodů popsaných v dalších částech tohoto článku.

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s optimalizací výkonu protokolu TCP/IP pro virtuální počítače Azure, si můžete přečíst další informace o [Plánování virtuálních sítí](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) nebo další [informace o připojení a konfiguraci virtuálních sítí](https://docs.microsoft.com/azure/virtual-network/).
