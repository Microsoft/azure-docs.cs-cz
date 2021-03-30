---
title: Linuxové nástroje pro měření výkonu
titleSuffix: Azure Kubernetes Service
description: Naučte se používat nástroje pro sledování výkonu pro Linux k řešení potíží a řešení běžných problémů při použití služby Azure Kubernetes Service (AKS).
services: container-service
author: alexeldeib
ms.service: container-service
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: aleldeib
ms.openlocfilehash: 74f65780594c7bc938ed6d59437473c4363e5848
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90982044"
---
# <a name="linux-performance-troubleshooting"></a>Řešení potíží s výkonem pro Linux

Vyčerpání prostředků na počítačích se systémem Linux je běžný problém a může se vydávat za nejrůznějších symptomů. Tento dokument poskytuje podrobný přehled dostupných nástrojů, které vám pomůžou diagnostikovat tyto problémy.

Mnohé z těchto nástrojů přijímají interval, ve kterém se má vydávat výstup za provozu. Tento výstupní formát obvykle usnadňuje vzory hledání. V případě přijetí bude ukázkové volání zahrnovat `[interval]` .

Mnohé z těchto nástrojů mají rozsáhlou historii a širokou škálu možností konfigurace. Tato stránka poskytuje pouze jednoduchou podmnožinu vyvolání pro zdůraznění běžných problémů. Kanonický zdroj informací je vždy referenční dokumentaci pro každý konkrétní nástroj. Tato dokumentace bude mnohem důkladnější, než je zde uvedeno.

## <a name="guidance"></a>Pokyny

Při zkoumání problémů s výkonem byste měli systematicky postupovat. POUŽÍVAJÍ se dva běžné přístupy (využití, sytost, chyby) a červená (rychlost, chyby, doba trvání). ČERVENÁ se obvykle používá v kontextu služeb pro monitorování na základě požadavků. POUŽITÍ se obvykle používá pro monitorování prostředků: pro každý prostředek v počítači, využití monitorování, sytost a chyby. Mezi čtyři hlavní druhy prostředků na jakémkoli počítači patří procesor, paměť, disk a síť. Vysoké využití, sytost nebo chybové sazby pro některý z těchto prostředků označují možný problém se systémem. Pokud problém existuje, prozkoumejte hlavní příčinu: Proč je vysoká latence v/v disku? Jsou disky nebo SKU virtuálního počítače omezené? Které procesy zapisují do zařízení a do jakých souborů?

Některé příklady běžných problémů a indikátorů pro jejich diagnostiku:
- Omezování IOPS: k měření IOPS za zařízení použít iostat. Zajistěte, aby žádný jednotlivý disk nebyl nad rámec limitu, a součet pro všechny disky je menší než limit pro virtuální počítač.
- Omezení šířky pásma: použijte iostat jako pro IOPS, ale měření propustnosti čtení a zápisu. Zajistěte, aby obě zařízení i agregovaná propustnost byly pod limity šířky pásma.
- Vyčerpání SNAT: Tato možnost může v administrativních oblastech v případě vysokého aktivního (odchozího) připojení. 
- Ztráta paketů: dá se měřit proxy serverem prostřednictvím počtu opakovaných přenosů TCP vzhledem k počtu odeslaných nebo přijatých položek. Obojí `sar` i `netstat` může zobrazit tyto informace.

## <a name="general"></a>Obecné

Tyto nástroje jsou pro obecné účely a zahrnují základní systémové informace. Jsou dobrým výchozím bodem pro další šetření.

### <a name="uptime"></a>Doba provozu

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

Doba provozu zajišťuje dobu provozu systému a 1, 5 a 15 minutový průměr zatížení. Tyto průměry zatížení zhruba odpovídají vláknům, které provádějí práci nebo čekají na dokončení nepřerušitelného práce. V absolutních těchto číslech může být obtížné je interpretovat, ale měří se v čase, kdy nám mohou sdělit užitečné informace:

- Průměrná hodnota 1 minuty >a 5 minut – průměr znamená, že se zátěž zvyšuje.
- Průměrná hodnota 1 minuty <a 5 minut průměrně znamená zatížení klesá.

Doba provozu také může zjistit, proč nejsou k dispozici informace: problém byl pravděpodobně vyřešen vlastním nebo restartováním, než uživatel bude moci získat přístup k počítači.

Průměrné zatížení, které je vyšší než počet dostupných vláken procesoru, může signalizovat problémy s výkonem u dané úlohy.

### <a name="dmesg"></a>dmesg

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

dmesg vypíše vyrovnávací paměť jádra. Události jako OOMKill přidávají položku do vyrovnávací paměti jádra. Hledání OOMKill nebo jiných zpráv o vyčerpání prostředků v protokolech dmesg je silným indikátorem problému.

### <a name="top"></a>top

```
$ top
Tasks: 249 total,   1 running, 158 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.2 us,  1.3 sy,  0.0 ni, 95.4 id,  1.0 wa,  0.0 hi,  0.2 si,  0.0 st
KiB Mem : 65949064 total, 43415136 free,  2349328 used, 20184600 buff/cache
KiB Swap:        0 total,        0 free,        0 used. 62739060 avail Mem

   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
116004 root      20   0  144400  41124  27028 S  11.8  0.1 248:45.45 coredns
  4503 root      20   0 1677980 167964  89464 S   5.9  0.3   1326:25 kubelet
     1 root      20   0  120212   6404   4044 S   0.0  0.0  48:20.38 systemd
     ...
```

`top` poskytuje komplexní přehled aktuálního stavu systému. Hlavičky obsahují několik užitečných agregačních informací:

- stav úloh: spuštěno, v režimu spánku, zastaveno.
- Využití procesoru, v tomto případě se většinou zobrazuje doba nečinnosti.
- Celková, volná a využitá systémová paměť.

`top` můžou přijít na krátkodobé procesy; alternativy jako `htop` a `atop` poskytují podobná rozhraní při odstraňování některých z těchto nedostatků.

## <a name="cpu"></a>Procesor

Tyto nástroje poskytují informace o využití procesoru. To je užitečné hlavně při zavedení výstupu, kde se vzory snadno zahodí.

### <a name="mpstat"></a>mpstat

```
$ mpstat -P ALL [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

19:49:03     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
19:49:04     all    1.01    0.00    0.63    2.14    0.00    0.13    0.00    0.00    0.00   96.11
19:49:04       0    1.01    0.00    1.01   17.17    0.00    0.00    0.00    0.00    0.00   80.81
19:49:04       1    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
19:49:04       2    1.01    0.00    0.00    0.00    0.00    1.01    0.00    0.00    0.00   97.98
19:49:04       3    0.00    0.00    0.99    0.00    0.00    0.99    0.00    0.00    0.00   98.02
19:49:04       4    1.98    0.00    1.98    0.00    0.00    0.00    0.00    0.00    0.00   96.04
19:49:04       5    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       6    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       7    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
```

`mpstat` zobrazí podobné informace o procesoru nahoře, ale je rozděleno podle vlákna procesoru. Zobrazení všech jader najednou může být užitečné pro detekci vysoce vyrovnaných využití CPU, například když jediná aplikace s vláknem používá jedno jádro při 100% využití. Tento problém může být obtížnější, pokud je agregovaný pro všechny procesory v systému.

### <a name="vmstat"></a>vmstat

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat` poskytuje podobné informace `mpstat` a `top` výčet počtu procesů, které čekají na procesor (sloupec r), statistiku paměti a procento času procesoru stráveného v jednotlivých pracovních stavech.

## <a name="memory"></a>Memory (Paměť)

Paměť je velice důležitá a naštěstí snadné, což je prostředek ke sledování. Některé nástroje mohou hlásit procesor i paměť, například `vmstat` . Ale nástroje, jako například `free` , mohou být stále užitečné pro rychlé ladění.

### <a name="free"></a>free

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free` prezentuje základní informace o celkové paměti a využité paměti a volnou paměť. `vmstat` může být užitečnější i pro základní analýzu paměti z důvodu jeho schopnosti poskytnout výstup do provozu.

## <a name="disk"></a>Disk

Tyto nástroje měří počet vstupně-výstupních operací disku, čekacích front a celkové propustnosti. 

### <a name="iostat"></a>iostat

```
$ iostat -xy [interval] [count]
$ iostat -xy 1 1
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           3.42    0.00    2.92    1.90    0.00   91.76

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
loop0             0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sdb               0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sda               0.00    56.00    0.00   65.00     0.00   504.00    15.51     0.01    3.02    0.00    3.02   0.12   0.80
scd0              0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
```

`iostat` poskytuje podrobné přehledy o využití disku. Toto vyvolání vyvoláno `-x` pro rozšířená statistiku, `-y` aby se od spuštění přeskočila průměrná hodnota počátečního výstupního tiskového tisku, a `1 1` aby bylo možné zadat interval 1 sekund, který končí po jednom bloku výstupu. 

`iostat` zpřístupňuje mnoho užitečných statistik:

- `r/s` a `w/s` jsou čtení za sekundu a zápisy za sekundu. Součet těchto hodnot je IOPS.
- `rkB/s` a `wkB/s` mají počet kilobajtů přečtených a zapsaných za sekundu. Součet těchto hodnot je propustnost.
- `await` je průměrná doba iowait v milisekundách pro požadavky zařazené do fronty.
- `avgqu-sz` je průměrná velikost fronty v zadaném intervalu.

Na virtuálním počítači Azure:

- součet `r/s` a `w/s` pro jednotlivá bloková zařízení nesmí překročit limity SKU tohoto disku.
- součet `rkB/s` a `wkB/s`  pro jednotlivá bloková zařízení nesmí překročit limity SKU tohoto disku.
- součet `r/s` a `w/s` pro všechna bloková zařízení nesmí překročit omezení pro SKU virtuálního počítače.
- součet  `rkB/s` a wkB/s pro všechna blokovaná zařízení nesmí překročit limity pro SKU virtuálního počítače.

Počítejte s tím, že disk s operačním systémem se počítá jako spravovaný disk nejnižší SKU odpovídající jeho kapacitě. Například disk s operačním systémem 1024GB odpovídá disku P30. Dočasné disky s operačním systémem a dočasné disky nemají omezení jednotlivých disků. jsou omezené jenom na omezení celého virtuálního počítače.

Nenulové hodnoty operátoru await nebo avgqu-SZ jsou také dobrým indikátorem kolizí v/v.

## <a name="network"></a>Síť

Tyto nástroje měří statistiku sítě, jako je propustnost, selhání přenosu a využití. Hlubší analýza může vystavovat jemně odstupňované statistiky TCP týkající se zahlcení a vyřazených paketů.

### <a name="sar"></a>Hongkong

```
$ sar -n DEV [interval]
22:36:57        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
22:36:58      docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azv604be75d832      1.00      9.00      0.06      1.04      0.00      0.00      0.00      0.00
22:36:58       azure0     68.00     79.00     27.79     52.79      0.00      0.00      0.00      0.00
22:36:58    azv4a8e7704a5b    202.00    207.00     37.51     21.86      0.00      0.00      0.00      0.00
22:36:58    azve83c28f6d1c     21.00     30.00     24.12      4.11      0.00      0.00      0.00      0.00
22:36:58         eth0    314.00    321.00     70.87    163.28      0.00      0.00      0.00      0.00
22:36:58    azva3128390bff     12.00     20.00      1.14      2.29      0.00      0.00      0.00      0.00
22:36:58    azvf46c95ddea3     10.00     18.00     31.47      1.36      0.00      0.00      0.00      0.00
22:36:58       enP1s1     74.00    374.00     29.36    166.94      0.00      0.00      0.00      0.00
22:36:58           lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azvdbf16b0b2fc      9.00     19.00      3.36      1.18      0.00      0.00      0.00      0.00
```

`sar` je výkonný nástroj pro rozsáhlou škálu analýz. I když tento příklad používá svoji možnost měření statistiky sítě, je stejně efektivní pro měření spotřeby procesoru a paměti. Tento příklad vyvolá `sar` příznak s `-n` příznakem pro zadání `DEV` klíčového slova (síťové zařízení), které zobrazuje propustnost sítě podle zařízení.

- Součet `rxKb/s` a `txKb/s` je celková propustnost pro dané zařízení. Pokud tato hodnota překročí limit zřízené síťové karty Azure, budou mít úlohy na počítači zvýšenou latenci sítě.
- `%ifutil` využití měr pro dané zařízení. Vzhledem k tomu, že se tato hodnota blíží 100%, budou mít úlohy zvýšenou latenci sítě.

```
$ sar -n TCP,ETCP [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

22:50:08     active/s passive/s    iseg/s    oseg/s
22:50:09         2.00      0.00     19.00     24.00

22:50:08     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
22:50:09         0.00      0.00      0.00      0.00      0.00

Average:     active/s passive/s    iseg/s    oseg/s
Average:         2.00      0.00     19.00     24.00

Average:     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
Average:         0.00      0.00      0.00      0.00      0.00
```

Toto vyvolání `sar` používá `TCP,ETCP` klíčová slova k prohlédnutí připojení TCP. Třetí sloupec posledního řádku, "retrans", je počet opakovaných přenosů TCP za sekundu. Vysoké hodnoty pro toto pole označují nespolehlivé síťové připojení. V prvním a třetím řádku znamená "aktivní" připojení pocházející z místního zařízení, zatímco "vzdálené" označuje příchozí připojení.  Běžný problém v Azure je vyčerpání portů SNAT, což `sar` může pomáhat detekovat. Vyčerpání portů SNAT by jako vysoké "aktivní" hodnoty znamenalo, vzhledem k tomu, že je příčinou vysoké míry odchozího, místně iniciovaná připojení TCP.

Jak `sar` přebírá určitý interval, vytiskne výstup výstupu a pak vytiskne konečné řádky výstupu, které obsahují průměrné výsledky volání.

### <a name="netstat"></a>netstat

```
$ netstat -s
Ip:
    71046295 total packets received
    78 forwarded
    0 incoming packets discarded
    71046066 incoming packets delivered
    83774622 requests sent out
    40 outgoing packets dropped
Icmp:
    103 ICMP messages received
    0 input ICMP message failed.
    ICMP input histogram:
        destination unreachable: 103
    412802 ICMP messages sent
    0 ICMP messages failed
    ICMP output histogram:
        destination unreachable: 412802
IcmpMsg:
        InType3: 103
        OutType3: 412802
Tcp:
    11487089 active connections openings
    592 passive connection openings
    1137 failed connection attempts
    404 connection resets received
    17 connections established
    70880911 segments received
    95242567 segments send out
    176658 segments retransmited
    3 bad segments received.
    163295 resets sent
Udp:
    164968 packets received
    84 packets to unknown port received.
    0 packet receive errors
    165082 packets sent
UdpLite:
TcpExt:
    5 resets received for embryonic SYN_RECV sockets
    1670559 TCP sockets finished time wait in fast timer
    95 packets rejects in established connections because of timestamp
    756870 delayed acks sent
    2236 delayed acks further delayed because of locked socket
    Quick ack mode was activated 479 times
    11983969 packet headers predicted
    25061447 acknowledgments not containing data payload received
    5596263 predicted acknowledgments
    19 times recovered from packet loss by selective acknowledgements
    Detected reordering 114 times using SACK
    Detected reordering 4 times using time stamp
    5 congestion windows fully recovered without slow start
    1 congestion windows partially recovered using Hoe heuristic
    5 congestion windows recovered without slow start by DSACK
    111 congestion windows recovered without slow start after partial ack
    73 fast retransmits
    26 retransmits in slow start
    311 other TCP timeouts
    TCPLossProbes: 198845
    TCPLossProbeRecovery: 147
    480 DSACKs sent for old packets
    175310 DSACKs received
    316 connections reset due to unexpected data
    272 connections reset due to early user close
    5 connections aborted due to timeout
    TCPDSACKIgnoredNoUndo: 8498
    TCPSpuriousRTOs: 1
    TCPSackShifted: 3
    TCPSackMerged: 9
    TCPSackShiftFallback: 177
    IPReversePathFilter: 4
    TCPRcvCoalesce: 1501457
    TCPOFOQueue: 9898
    TCPChallengeACK: 342
    TCPSYNChallenge: 3
    TCPSpuriousRtxHostQueues: 17
    TCPAutoCorking: 2315642
    TCPFromZeroWindowAdv: 483
    TCPToZeroWindowAdv: 483
    TCPWantZeroWindowAdv: 115
    TCPSynRetrans: 885
    TCPOrigDataSent: 51140171
    TCPHystartTrainDetect: 349
    TCPHystartTrainCwnd: 7045
    TCPHystartDelayDetect: 26
    TCPHystartDelayCwnd: 862
    TCPACKSkippedPAWS: 3
    TCPACKSkippedSeq: 4
    TCPKeepAlive: 62517
IpExt:
    InOctets: 36416951539
    OutOctets: 41520580596
    InNoECTPkts: 86631440
    InECT0Pkts: 14
```

`netstat` může introspect širokou škálu statistik sítě, která se tady vyvolala se souhrnným výstupem. V závislosti na problému se tady vyskytuje mnoho užitečných polí. Jedno užitečné pole v části TCP je "nezdařené pokusy o připojení". Může to znamenat vyčerpání portů SNAT nebo jiné problémy při vytváření odchozích připojení. Vysoká míra přenesených segmentů (také v části protokolu TCP) může označovat problémy se doručením paketů. 
