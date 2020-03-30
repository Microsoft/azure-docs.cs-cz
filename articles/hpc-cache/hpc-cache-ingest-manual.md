---
title: Ingestování dat mezipaměti Azure HPC – ruční kopírování
description: Použití příkazů cp k přesunutí dat do cíle úložiště objektů Blob v azure hpc mezipaměti
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: fc397088e46f0d2b623080f3deed24c386e7d8b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168479"
---
# <a name="azure-hpc-cache-data-ingest---manual-copy-method"></a>Ingestování dat mezipaměti Azure HPC – metoda ručního kopírování

Tento článek obsahuje podrobné pokyny pro ruční kopírování dat do kontejneru úložiště objektů Blob pro použití s mezipamětí Azure HPC. Používá vícevláknové paralelní operace pro optimalizaci rychlosti kopírování.

Další informace o přesunu dat do úložiště objektů Blob pro azure hpc mezipaměť najdete v článek [Přesun dat do úložiště objektů blob Azure](hpc-cache-ingest.md).

## <a name="simple-copy-example"></a>Příklad jednoduché kopie

Můžete ručně vytvořit vícevláknovou kopii na straně klienta spuštěním více než jeden příkaz kopie najednou na pozadí proti předdefinované sady souborů nebo cest.

Příkaz Linux/UNIX ``cp`` obsahuje ``-p`` argument pro zachování vlastnictví a metadat mtime. Přidání tohoto argumentu do níže uvedených příkazů je volitelné. (Přidání argumentu zvyšuje počet volání systému souborů odeslaných z klienta do cílového systému souborů pro úpravu metadat.)

Tento jednoduchý příklad zkopíruje paralelně dva soubory:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Po vydání tohoto příkazu `jobs` příkaz zobrazí, že jsou spuštěna dvě vlákna.

## <a name="copy-data-with-predictable-file-names"></a>Kopírování dat s předvídatelnými názvy souborů

Pokud jsou názvy souborů předvídatelné, můžete pomocí výrazů vytvořit paralelní kopírovací vlákna. 

Pokud například adresář obsahuje 1000 souborů, které `0001` jsou `1000`číslovány postupně od do , můžete pomocí následujících výrazů vytvořit deset paralelních vláken, z nichž každý zkopíruje 100 souborů:

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination1/ & \
cp /mnt/source/file2* /mnt/destination1/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination1/ & \
cp /mnt/source/file5* /mnt/destination1/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination1/ & \
cp /mnt/source/file8* /mnt/destination1/ & \
cp /mnt/source/file9* /mnt/destination1/
```

## <a name="copy-data-with-unstructured-file-names"></a>Kopírování dat pomocí nestrukturovaných názvů souborů

Pokud struktura pojmenování souborů není předvídatelná, můžete seskupit soubory podle názvů adresářů. 

Tento příklad shromažďuje celé adresáře, které mají být odeslány příkazům ``cp`` spuštěných jako úlohy na pozadí:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

Po shromáždění souborů můžete spustit příkazy paralelního kopírování a rekurzivně zkopírovat podadresáře a veškerý jejich obsah:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

## <a name="when-to-add-mount-points"></a>Kdy přidat přípojné body

Poté, co máte dostatek paralelních podprocesů, které jdou proti jedinému cílovému bodu připojení systému souborů, bude existovat bod, kde přidání dalších vláken neposkytuje větší propustnost. (Propustnost se měří v souborech za sekundu nebo v bajtech za sekundu v závislosti na typu dat.) Nebo horší, over-threading může někdy způsobit snížení propustnost.  

V takovém případě můžete přidat přípojné body na straně klienta na jiné adresy připojení Azure HPC Cache pomocí stejné cesty připojení vzdáleného systému souborů:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

Přidání přípojných bodů na straně klienta umožňuje vyčlenit další příkazy kopírování do dalších `/mnt/destination[1-3]` přípojných bodů a dosáhnout další paralelismu.  

Pokud jsou například soubory velmi velké, můžete definovat příkazy kopírování, které budou používat odlišné cílové cesty, a současně odesílat další příkazy od klienta provádějícího kopii.

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination2/ & \
cp /mnt/source/file2* /mnt/destination3/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination2/ & \
cp /mnt/source/file5* /mnt/destination3/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination2/ & \
cp /mnt/source/file8* /mnt/destination3/ & \
```

Ve výše uvedeném příkladu jsou všechny tři cílové přípojné body cíleny procesy kopírování souborů klienta.

## <a name="when-to-add-clients"></a>Kdy přidat klienty

A konečně, když jste dosáhli možnosti klienta, přidání dalších zkopírovat podprocesů nebo další přípojné body nepřinese žádné další soubory/s nebo bajty/s zvyšuje. V takovém případě můžete nasadit jiného klienta se stejnou sadou přípojných bodů, které budou spouštět vlastní sady procesů kopírování souborů. 

Příklad:

```bash
Client1: cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
Client1: cp -R /mnt/source/dir2/dir2a /mnt/destination/dir2/ &
Client1: cp -R /mnt/source/dir3/dir3a /mnt/destination/dir3/ &

Client2: cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
Client2: cp -R /mnt/source/dir2/dir2b /mnt/destination/dir2/ &
Client2: cp -R /mnt/source/dir3/dir3b /mnt/destination/dir3/ &

Client3: cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ &
Client3: cp -R /mnt/source/dir2/dir2c /mnt/destination/dir2/ &
Client3: cp -R /mnt/source/dir3/dir3c /mnt/destination/dir3/ &

Client4: cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
Client4: cp -R /mnt/source/dir2/dir2d /mnt/destination/dir2/ &
Client4: cp -R /mnt/source/dir3/dir3d /mnt/destination/dir3/ &
```

## <a name="create-file-manifests"></a>Vytvoření manifestů souborů

Po pochopení výše uvedených přístupů (více kopírovacích vláken na cíl, více cílů na klienta, více klientů na zdrojový systém souborů přístupný v síti), zvažte toto doporučení: Sestavte manifesty souborů a pak je použijte s kopií příkazy napříč více klienty.

Tento scénář používá ``find`` příkaz UNIX k vytvoření manifestů souborů nebo adresářů:

```bash
user@build:/mnt/source > find . -mindepth 4 -maxdepth 4 -type d
./atj5b55c53be6-01/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-01/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-01/support/trace/rolling
./atj5b55c53be6-03/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-03/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-03/support/trace/rolling
./atj5b55c53be6-02/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-02/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-02/support/trace/rolling
```

Přesměrovat tento výsledek na soubor:`find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Pak můžete iteraci prostřednictvím manifestu, pomocí příkazů BASH počítat soubory a určit velikosti podadresářů:

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l`    `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
7      16K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:12:19UTC
8      83K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:17:17UTC
575    7.7M    ./atj5b5ab44b7f-02/support/cores/armada_main.2000.1531980253.gsi
33     4.4G    ./atj5b5ab44b7f-02/support/trace/rolling
281    6.6M    ./atj5b5ab44b7f-01/support/gsi/2018-07-18T00:07:03EDT
15     182K    ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-18T05:01:00UTC
244    17M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-19T01:01:01UTC
299    31M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T01:01:00UTC
256    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T21:59:41UTC_partial
889    7.7M    ./atj5b5ab44b7f-01/support/gsi/2018-07-20T21:59:41UTC
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
11     248K    ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:12:19UTC
11     88K     ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:17:17UTC
645    11M     ./atj5b5ab44b7f-01/support/cores/armada_main.2019.1531980253.gsi
33     4.0G    ./atj5b5ab44b7f-01/support/trace/rolling
244    2.1M    ./atj5b5ab44b7f-03/support/gsi/2018-07-18T00:07:03EDT
9      158K    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.3M    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T01:01:00UTC
131    12M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    8.4M    ./atj5b5ab44b7f-03/support/gsi/2018-07-20T21:59:41UTC
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_hpccache_catchup
7      159K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:12:19UTC
7      157K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:17:17UTC
576    12M     ./atj5b5ab44b7f-03/support/cores/armada_main.2013.1531980253.gsi
33     2.8G    ./atj5b5ab44b7f-03/support/trace/rolling
```

Nakonec je nutné vytvořit příkazy pro kopírování skutečných souborů klientům.  

Pokud máte čtyři klienty, použijte tento příkaz:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Pokud máte pět klientů, použijte něco takového:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

A za šest... Extrapolovat podle potřeby.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

Získáte *N* výsledné soubory, jeden pro každého z vašich *klientů N,* který má názvy cest na `find` úroveň čtyři adresáře získané jako součást výstupu z příkazu. 

K vytvoření příkazu copy použijte každý soubor:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

Výše uvedené *vám* N soubory, každý s příkazem kopírovat na řádek, který lze spustit jako bash skript na straně klienta. 

Cílem je spustit více vláken těchto skriptů současně na klienta paralelně na více klientů.
