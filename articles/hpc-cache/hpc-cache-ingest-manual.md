---
title: Ingestování dat mezipaměti HPC Azure – ruční kopírování
description: Použití příkazů CP k přesunu dat do cíle úložiště objektů BLOB v mezipaměti HPC Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: f96a0fa264124f9d050e667b003d98579da63b77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87092333"
---
# <a name="azure-hpc-cache-data-ingest---manual-copy-method"></a>Ingestování dat mezipaměti HPC Azure – metoda ručního kopírování

Tento článek obsahuje podrobné pokyny pro ruční kopírování dat do kontejneru úložiště objektů BLOB pro použití s mezipamětí Azure HPC. K optimalizaci rychlosti kopírování používá vícevláknové paralelní operace.

Další informace o přesouvání dat do úložiště objektů BLOB pro mezipaměť HPC Azure najdete v tématu [přesun dat do úložiště objektů BLOB v Azure](hpc-cache-ingest.md).

## <a name="simple-copy-example"></a>Příklad jednoduchého kopírování

Můžete ručně vytvořit vícevláknovou kopii na klientovi spuštěním více než jednoho příkazu kopírování na pozadí v předdefinovaných sadách souborů nebo cest.

Příkaz Linux/UNIX ``cp`` obsahuje argument ``-p`` pro zachování vlastnictví a mtime metadat. Přidání tohoto argumentu do příkazů níže je volitelné. (Přidáním argumentu se zvýší počet volání systému souborů odeslaných z klienta do cílového systému souborů pro úpravu metadat.)

Tento jednoduchý příklad kopíruje dva soubory paralelně:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Po vystavení tohoto příkazu se v `jobs` příkazu zobrazí, že jsou spuštěná dvě vlákna.

## <a name="copy-data-with-predictable-file-names"></a>Kopírování dat s předvídatelnými názvy souborů

Pokud jsou názvy souborů předvídatelné, můžete použít výrazy k vytvoření paralelních vláken kopírování.

Pokud například váš adresář obsahuje soubory 1000, které jsou očíslovány postupně z `0001` na `1000` , můžete použít následující výrazy k vytvoření 10 paralelních vláken, které každý soubor kopie 100:

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

## <a name="copy-data-with-unstructured-file-names"></a>Kopírování dat s nestrukturovanými názvy souborů

Pokud vaše struktura pojmenovávání souborů není předvídatelná, můžete soubory seskupit podle názvů adresářů.

Tento příklad shromažďuje celé adresáře pro odeslání do ``cp`` příkazů spouštěných jako úlohy na pozadí:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

Po shromáždění souborů můžete spustit paralelní příkazy kopírování pro rekurzivní kopírování podadresářů a veškerého jejich obsahu:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

## <a name="when-to-add-mount-points"></a>Kdy přidat přípojné body

Po zobrazení dostatečného množství paralelních vláken pro jeden přípojný bod systému souborů bude k dispozici bod, ve kterém přidání dalších vláken neposkytuje větší propustnost. (V závislosti na typu dat se měří propustnost v souborech za sekundu nebo v bajtech za sekundu.) Nebo horší, více vláken může někdy způsobit snížení propustnosti.

Pokud k tomu dojde, můžete přidat přípojné body na straně klienta do jiných adres připojení mezipaměti HPC Azure pomocí stejné cesty pro připojení ke vzdálenému systému souborů:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

Přidáním přípojných bodů na straně klienta můžete rozvětvit další příkazy kopírování do dalších `/mnt/destination[1-3]` přípojných bodů a dosáhnout tak dalších paralelismu.

Například pokud jsou soubory velmi velké, můžete definovat příkazy kopírování pro použití odlišných cílových cest a odeslání dalších příkazů paralelně z klienta provádějícího kopírování.

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

V předchozím příkladu jsou všechny tři cílové přípojné body cíleny procesy kopírování souborů klienta.

## <a name="when-to-add-clients"></a>Kdy přidat klienty

Nakonec, pokud jste dosáhli možností klienta, přidání dalších vláken kopírování nebo dalších přípojných bodů nebude zvyšovat žádné další soubory za sekundu nebo bajtů/s. V takové situaci můžete nasadit jiného klienta se stejnou sadou přípojných bodů, na kterých budou spuštěné vlastní sady procesů kopírování souborů.

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

## <a name="create-file-manifests"></a>Vytváření manifestů souborů

Po porozumění výše zmíněných přístupů (více než jedno umístění na jeden cíl, více cílů na klienta, více klientů na jeden zdrojový souborový systém) zvažte toto doporučení: manifesty souborů sestavení a pak je používejte s příkazy kopírování mezi více klienty.

V tomto scénáři se ``find`` k vytváření manifestů souborů nebo adresářů používá příkaz UNIX:

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

Přesměrovat tento výsledek do souboru: `find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Pak můžete iterovat v manifestu pomocí příkazů BASH pro počítání souborů a určení velikosti podadresářů:

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l` `du -sh ${i}`"; done
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

Nakonec je nutné, aby byly vlastní příkazy kopírování souborů pro klienty.

Pokud máte čtyři klienty, použijte tento příkaz:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Pokud máte pět klientů, použijte něco podobného:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

A šest.... Odvodit podle potřeby.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

Dostanete *N* výsledných souborů, jeden pro každého z vašich klientů *N* , který má názvy cest k adresářům úrovně čtyři, které byly získány jako součást výstupu z `find` příkazu.

Pomocí každého souboru Sestavte příkaz pro kopírování:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

Výše uvedené soubory vám poskytnou *N* souborů, každý s příkazem kopírování na řádek, který je možné spustit jako bash skript na klientovi.

Cílem je spouštět více vláken těchto skriptů souběžně na jednom klientovi paralelně na více klientech.
