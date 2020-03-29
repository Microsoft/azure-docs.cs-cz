---
title: Nástroje pro výkon Linuxu
titleSuffix: Azure Kubernetes Service
description: Zjistěte, jak řešit a řešit běžné problémy při používání služby Azure Kubernetes Service (AKS).
services: container-service
author: alexeldeib
ms.service: container-service
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: aleldeib
ms.openlocfilehash: eb6b126b4d1794adf0380432040190b91a17a675
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925602"
---
# <a name="linux-performance-troubleshooting"></a>Řešení potíží s výkonem Linuxu

Vyčerpání prostředků na počítačích s Linuxem je běžný problém a může projevit prostřednictvím široké škály příznaků. Tento dokument poskytuje přehled na vysoké úrovni o nástrojích, které jsou k dispozici pro diagnostiku těchto problémů.

Mnoho z těchto nástrojů přijmout interval, na kterém chcete vyrábět klouzavý výstup. Tento výstupní formát obvykle usnadňuje zjišnit vzorky. Pokud je přijat, bude `[interval]`příklad vyvolání zahrnovat .

Mnoho z těchto nástrojů má rozsáhlou historii a širokou sadu možností konfigurace. Tato stránka obsahuje pouze jednoduchou podmnožinu vyvolání, která zvýrazňuje běžné problémy. Kanonickým zdrojem informací je vždy referenční dokumentace pro každý konkrétní nástroj. Tato dokumentace bude mnohem důkladnější, než to, co je zde k dispozici.

## <a name="guidance"></a>Doprovodné materiály

Buďte systematický ve svém přístupu k vyšetřování problémů s výkonem. Dva běžné přístupy jsou USE (využití, sytost, chyby) a RED (rychlost, chyby, doba trvání). RED se obvykle používá v kontextu služeb pro monitorování založené na požadavcích. POUŽITÍ se obvykle používá pro monitorování prostředků: pro každý prostředek v počítači, využití monitoru, sytost a chyby. Čtyři hlavní druhy prostředků v libovolném počítači jsou procesor, paměť, disk a síť. Vysoké využití, sytost nebo chybovost pro některý z těchto zdrojů označuje možný problém se systémem. Pokud problém existuje, prozkoumejte hlavní příčinu: proč je latence vi. Jsou disky nebo virtuální počítač sku omezen? Jaké procesy jsou zápis do zařízení, a na jaké soubory?

Některé příklady běžných problémů a ukazatelů pro jejich diagnostiku:
- Omezení IOPS: použijte iostat k měření viopů pro zařízení. Ujistěte se, že žádný jednotlivý disk není nad jeho limit a součet pro všechny disky je menší než limit pro virtuální počítač.
- Omezení šířky pásma: použijte iostat jako pro IOPS, ale měření propustnost pro čtení a zápisu. Ujistěte se, že propustnost pro jedno zařízení i agregátjsou pod limity šířky pásma.
- Vyčerpání SNAT: to se může projevit jako vysoká aktivní (odchozí) připojení v SAR. 
- Ztráta paketů: to lze měřit prostřednictvím proxy serveru prostřednictvím počtu opakovaného přenosu TCP vzhledem k počtu odeslaných/přijatých. Oba `sar` `netstat` a může zobrazit tyto informace.

## <a name="general"></a>Obecné

Tyto nástroje jsou univerzální a zahrnují základní systémové informace. Jsou dobrým výchozím bodem pro další vyšetřování.

### <a name="uptime"></a>Uptime

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

doba je v posuzovací choda a průměry zatížení 1, 5 a 15 minut. Tyto průměry zatížení zhruba odpovídají vláknům, která pracují nebo čekají na dokončení nepřerušitelné práce. V absolutních číslech mohou být tato čísla obtížně interpretovatelná, ale měřená v průběhu času nám mohou sdělit užitečné informace:

- 1-minutový průměr > 5-minutové průměrné znamená, že zatížení se zvyšuje.
- 1-minutový průměr < 5-minutové průměrné znamená, že zatížení klesá.

doba provozu může také osvětlit, proč nejsou informace k dispozici: problém mohl vyřešit samostatně nebo restartováním, než mohl uživatel přistupovat k počítači.

Průměry zatížení vyšší než počet vláken procesoru k dispozici může znamenat problém s výkonem s danou úlohou.

### <a name="dmesg"></a>Dmesg

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

dmesg vypíše vyrovnávací paměť jádra. Události jako OOMKill přidávají položku do vyrovnávací paměti jádra. Nalezení OOMKill nebo jiné zprávy vyčerpání prostředků v dmesg protokoly je silným ukazatelem problému.

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

`top`poskytuje široký přehled o aktuálním stavu systému. Záhlaví poskytují některé užitečné souhrnné informace:

- stav úkolů: běh, spánek, zastaveno.
- Využití procesoru, v tomto případě většinou ukazuje dobu nečinnosti.
- celková, bezplatná a použitá systémová paměť.

`top`mohou chybět krátkodobé procesy; alternativy, `htop` `atop` jako je a poskytují podobná rozhraní při stanovení některých z těchto nedostatků.

## <a name="cpu"></a>Procesor

Tyto nástroje poskytují informace o využití procesoru. To je užitečné zejména při válcování výstupu, kde se vzory snadno na místě.

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

`mpstat`vytiskne podobné informace o procesoru nazačátek, ale člení podle vlákna PROCESORU. Zobrazení všech jader najednou může být užitečné pro detekci vysoce nevyváženévyužití procesoru, například když jedno vláknové aplikace používá jedno jádro při 100% využití. Tento problém může být obtížnější na místě při agregaci přes všechny procesory v systému.

### <a name="vmstat"></a>vmstat

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat`poskytuje podobné `mpstat` `top`informace a , výčet počet procesů čekání na CPU (r sloupec), statistiky paměti a procento času procesoru stráveného v každém stavu práce.

## <a name="memory"></a>Memory (Paměť)

Paměť je velmi důležité, a naštěstí snadné, zdroj sledovat. Některé nástroje mohou hlásit procesor `vmstat`i paměť, například . Ale nástroje, jako `free` je může být stále užitečné pro rychlé ladění.

### <a name="free"></a>free

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free`obsahuje základní informace o celkové paměti, stejně jako o použité a volné paměti. `vmstat`může být užitečnější i pro základní analýzu paměti díky své schopnosti poskytovat postupný výstup.

## <a name="disk"></a>Disk

Tyto nástroje měří vipony na disku, čekací fronty a celkovou propustnost. 

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

`iostat`poskytuje podrobné informace o využití disku. Toto vyvolání prochází `-x` `-y` pro rozšířené statistiky, přeskočit počáteční průměry výstupního tiskového systému od spuštění a `1 1` určit, že chceme interval 1 sekundy, končící po jednom bloku výstupu. 

`iostat`vystavuje mnoho užitečných statistik:

- `r/s`a `w/s` jsou čte za sekundu a zápisy za sekundu. Součet těchto hodnot je VOPS.
- `rkB/s`a `wkB/s` jsou kilobajty číst/zapisovat za sekundu. Součet těchto hodnot je propustnost.
- `await`je průměrná iowait čas v milisekundách pro požadavky ve frontě.
- `avgqu-sz`je průměrná velikost fronty za daný interval.

Na virtuálním počítači Azure:

- součet `r/s` a `w/s` pro jednotlivé blokové zařízení nesmí překročit limity skladové položky tohoto disku.
- součet `rkB/s` jednotlivých blokových zařízení a `wkB/s` pro jednotlivá bloková zařízení nesmí překročit limity skladové položky tohoto disku
- součet `r/s` a `w/s` pro všechna bloková zařízení nesmí překročit limity pro skladovou položku virtuálního zařízení.
- součet `rkB/s` a 'wkB/s pro všechna bloková zařízení nesmí překročit limity pro skladovou jednotku virtuálního zařízení.

Všimněte si, že disk operačního systému se počítá jako spravovaný disk nejmenší skladové položky odpovídající jeho kapacitě. Například 1024GB OS Disk odpovídá disku P30. Dočasné disky operačního systému a dočasné disky nemají individuální disková omezení. jsou omezeny pouze úplnými limity virtuálních mís.

Nenulové hodnoty await nebo avgqu-sz jsou také dobrými ukazateli tvrzení iO.

## <a name="network"></a>Network (Síť)

Tyto nástroje měří síťové statistiky, jako je propustnost, selhání přenosu a využití. Hlubší analýza může vystavit jemně odstupňované statistiky TCP o přetížení a vyřazených paketech.

### <a name="sar"></a>Sar

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

`sar`je mocnýnástroj pro širokou škálu analýz. Zatímco tento příklad používá svou schopnost měřit síťové statistiky, je stejně výkonný pro měření spotřeby procesoru a paměti. Tento příklad `sar` vyvolá `-n` s příznakem `DEV` určit (síťové zařízení) klíčové slovo, zobrazení propustnost sítě podle zařízení.

- Součet `rxKb/s` a `txKb/s` je celková propustnost pro dané zařízení. Když tato hodnota překročí limit pro zřízenou síťovou síťovou kinu Azure, úlohy v počítači budou mít zvýšenou latenci sítě.
- `%ifutil`opatření pro využití daného zařízení. Vzhledem k tomu, že se tato hodnota blíží 100 %, dojde ke zvýšení latence sítě.

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

Toto vyvolání `sar` `TCP,ETCP` používá klíčová slova ke kontrole připojení TCP. Třetí sloupec posledního řádku , "retrans", je počet tcp retransmits za sekundu. Vysoké hodnoty pro toto pole označují nespolehlivé síťové připojení. V prvním a třetím řádku "aktivní" znamená připojení pocházející z místního zařízení, zatímco "vzdálené" označuje příchozí připojení.  Běžným problémem v Azure je vyčerpání portů SNAT, které `sar` může pomoci zjistit. Vyčerpání portu SNAT by se projevilo jako vysoké "aktivní" hodnoty, protože problém je způsoben vysokou rychlostí odchozích připojení TCP iniciovaných místně.

Jako `sar` trvá interval, vytiskne průběžný výstup a pak vytiskne konečné řádky výstupu obsahující průměrné výsledky z vyvolání.

### <a name="netstat"></a>Netstat

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

`netstat`může introspekt širokou škálu síťových statistik, zde vyvolána se souhrnným výstupem. Existuje mnoho užitečných polí zde v závislosti na problému. Jedno užitečné pole v části TCP je "neúspěšné pokusy o připojení". To může být údaj vyčerpání portu SNAT nebo jiné problémy dělat odchozí připojení. Vysoká míra opakovaně přenášených segmentů (také v části TCP) může znamenat problémy s doručováním paketů. 
