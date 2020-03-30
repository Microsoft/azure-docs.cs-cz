---
title: Přesouvání dat do Avere vFXT pro Azure
description: Jak přidat data do nového svazku úložiště pro použití s Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: c2a38b20fff789faf370e3161a92a31ed5f04c57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153714"
---
# <a name="moving-data-to-the-vfxt-cluster---parallel-data-ingest"></a>Přesouvání dat do clusteru vFXT – paralelní ingestování dat

Po vytvoření nového clusteru vFXT může být vaším prvním úkolem přesunout data do nového svazku úložiště v Azure. Pokud však obvyklý způsob přesouvání dat vydává jednoduchý příkaz pro kopírování z jednoho klienta, pravděpodobně se zobrazí pomalý výkon kopírování. Jednovláknové kopírování není dobrou volbou pro kopírování dat do back-endového úložiště clusteru Avere vFXT.

Vzhledem k tomu, že cluster Avere vFXT pro Azure je škálovatelná mezipaměť s více klienty, nejrychlejší a nejefektivnější způsob kopírování dat do něj je s více klienty. Tato technika paralelizuje ingestování souborů a objektů.

![Diagram znázorňující přesun dat s více klienty a více vlákny: V levém horním rohu je na ní několik šipek, které z něj pocházejí. Šipky ukazují na čtyři klientské počítače. Z každého klientského počítače tři šipky směřovat k Avere vFXT. Z Avere vFXT, více šipek přejděte na úložiště objektů Blob.](media/avere-vfxt-parallel-ingest.png)

``cp`` Příkazy ``copy`` nebo, které se běžně používají k přenosu dat z jednoho systému úložiště do jiného, jsou procesy s jedním podprocesem, které kopírují pouze jeden soubor najednou. To znamená, že souborový server ingestuje pouze jeden soubor najednou , což je plýtvání prostředky clusteru.

Tento článek vysvětluje strategie pro vytvoření víceklientského, vícevláknového systému kopírování souborů pro přesunutí dat do clusteru Avere vFXT. Vysvětluje koncepty přenosu souborů a rozhodovací body, které lze použít pro efektivní kopírování dat pomocí více klientů a jednoduchých příkazů pro kopírování.

To také vysvětluje některé nástroje, které mohou pomoci. Nástroj ``msrsync`` lze částečně automatizovat proces rozdělení datové sady do bloků ``rsync`` a pomocí příkazů. Skript ``parallelcp`` je jiný nástroj, který čte zdrojový adresář a vydává příkazy kopírování automaticky. ``rsync`` Nástroj lze také použít ve dvou fázích k zajištění rychlejší kopie, která stále poskytuje konzistenci dat.

Kliknutím na odkaz přejdete na oddíl:

* [Příklad ruční kopírování](#manual-copy-example) – důkladné vysvětlení pomocí příkazů kopírování
* [Dvoufázový příklad rsync](#use-a-two-phase-rsync-process)
* [Částečně automatizovaný příklad (msrsync)](#use-the-msrsync-utility)
* [Příklad paralelní kopie](#use-the-parallel-copy-script)

## <a name="data-ingestor-vm-template"></a>Šablona virtuálního počítače pro ingestování dat

Šablona Správce prostředků je k dispozici na GitHubu, aby automaticky vytvořila virtuální počítač s nástroji pro paralelní přijímaní dat uvedenými v tomto článku.

![diagram zobrazující více šipek z úložiště objektů blob, hardwarového úložiště a zdrojů souborů Azure. Šipky ukazují na "data ingestor vm" a odtud více šipek ukazuje na Avere vFXT](media/avere-vfxt-ingestor-vm.png)

Virtuální počítače pro ingestování dat je součástí kurzu, kde nově vytvořený virtuální počítače připojí cluster Avere vFXT a stáhne jeho zaváděcí skript z clusteru. Přečtěte si [Bootstrap data ingestor Virtuální počítače](https://github.com/Azure/Avere/blob/master/docs/data_ingestor.md) pro podrobnosti.

## <a name="strategic-planning"></a>Strategické plánování

Při navrhování strategie pro paralelní kopírování dat byste měli pochopit kompromisy v velikosti souboru, počtu souborů a hloubce adresáře.

* Pokud jsou soubory malé, metrika zájmu je soubory za sekundu.
* Pokud jsou soubory velké (10MiBi nebo vyšší), metrika zájmu je bajtů za sekundu.

Každý proces kopírování má propustnost a rychlost přenesené soubory, kterou lze měřit časováním délky příkazu kopírování a faktoringem velikosti souboru a počtu souborů. Vysvětlení, jak měřit sazby, je mimo rozsah tohoto dokumentu, ale je důležité pochopit, zda se budete zabývat malými nebo velkými soubory.

## <a name="manual-copy-example"></a>Příklad ruční kopírování

Můžete ručně vytvořit vícevláknovou kopii na straně klienta spuštěním více než jeden příkaz kopie najednou na pozadí proti předdefinované sady souborů nebo cest.

Příkaz Linux/UNIX ``cp`` obsahuje ``-p`` argument pro zachování vlastnictví a metadat mtime. Přidání tohoto argumentu do níže uvedených příkazů je volitelné. (Přidání argumentu zvyšuje počet volání souborového systému odeslaných z klienta do cílového souborového systému pro úpravu metadat.)

Tento jednoduchý příklad zkopíruje paralelně dva soubory:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Po vydání tohoto příkazu `jobs` příkaz zobrazí, že jsou spuštěna dvě vlákna.

### <a name="predictable-filename-structure"></a>Předvídatelná struktura názvů souborů

Pokud jsou názvy souborů předvídatelné, můžete použít výrazy k vytvoření paralelních kopírovacích vláken.

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

### <a name="unknown-filename-structure"></a>Neznámá struktura názvu souboru

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

### <a name="when-to-add-mount-points"></a>Kdy přidat přípojné body

Poté, co máte dostatek paralelních podprocesů, které jdou proti jedinému cílovému bodu připojení souborového systému, bude existovat bod, kde přidání dalších vláken neposkytuje větší propustnost. (Propustnost se měří v souborech za sekundu nebo v bajtech za sekundu v závislosti na typu dat.) Nebo horší, over-threading může někdy způsobit snížení propustnost.

V takovém případě můžete přidat přípojné body na straně klienta na jiné IP adresy clusteru vFXT pomocí stejné cesty připojení vzdáleného souborového systému:

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

### <a name="when-to-add-clients"></a>Kdy přidat klienty

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

### <a name="create-file-manifests"></a>Vytvoření manifestů souborů

Po pochopení výše uvedených přístupů (více kopírovacích vláken na cíl, více cílů na klienta, více klientů na zdrojový souborový systém přístupný ze sítě), zvažte toto doporučení: Sestavte manifesty souborů a pak je použijte s kopií příkazy napříč více klienty.

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
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l` `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
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
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
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
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_vfxt_catchup
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

## <a name="use-a-two-phase-rsync-process"></a>Použití dvoufázového procesu rsync

Standardní ``rsync`` nástroj nefunguje dobře pro vyplnění cloudového úložiště prostřednictvím systému Avere vFXT pro Azure, protože generuje velký počet operací vytváření a přejmenování souborů, aby byla zaručena integrita dat. Tuto ``--inplace`` možnost však můžete ``rsync`` bezpečně použít k přeskočení pečlivějšího postupu kopírování, pokud budete postupovat podle druhého spuštění, které kontroluje integritu souboru.

Standardní ``rsync`` operace kopírování vytvoří dočasný soubor a vyplní jej daty. Pokud se přenos dat úspěšně dokončí, dočasný soubor se přejmenuje na původní název souboru. Tato metoda zaručuje konzistenci i v případě, že soubory jsou přístupné během kopírování. Ale tato metoda generuje další operace zápisu, což zpomaluje pohyb souborů prostřednictvím mezipaměti.

Možnost ``--inplace`` zapíše nový soubor přímo do konečného umístění. Soubory nejsou zaručeny konzistentní během přenosu, ale to není důležité, pokud připravujete úložný systém pro pozdější použití.

Druhá ``rsync`` operace slouží jako kontrola konzistence první operace. Vzhledem k tomu, že soubory již byly zkopírovány, druhá fáze je rychlá kontrola, aby bylo zajištěno, že soubory v cílovém umístění odpovídají souborům ve zdroji. Pokud se některé soubory neshodují, jsou znovu zkopírovány.

Obě fáze můžete vydat společně v jednom příkazu:

```bash
rsync -azh --inplace <source> <destination> && rsync -azh <source> <destination>
```

Tato metoda je jednoduchá a časově efektivní metoda pro datové sady až do počtu souborů, které může interní správce adresářů zpracovat. (Obvykle se jedná o 200 milionů souborů pro cluster s 3 uzny, 500 milionů souborů pro cluster se šesti uzny a tak dále.)

## <a name="use-the-msrsync-utility"></a>Použití nástroje msrsync

Nástroj ``msrsync`` lze také použít k přesunutí dat do back-endového základního fileru pro cluster Avere. Tento nástroj je navržen tak, aby ``rsync`` optimalizoval využití šířky pásma spuštěním více paralelních procesů. Je k dispozici na <https://github.com/jbd/msrsync>GitHubu na adrese .

``msrsync``rozdělí zdrojový adresář na samostatné "kbelíky" ``rsync`` a pak spustí jednotlivé procesy na každém bloku.

Předběžné testování pomocí čtyřjádrového virtuálního virtuálního uživatele ukázalo nejlepší efektivitu při použití 64 procesů. Pomocí ``msrsync`` této ``-p`` možnosti můžete nastavit počet procesů na 64.

``--inplace`` Argument můžete také použít ``msrsync`` s příkazy. Pokud použijete tuto možnost, zvažte spuštění druhého příkazu (jako u [rsync](#use-a-two-phase-rsync-process), popsaného výše) k zajištění integrity dat.

``msrsync``může zapisovat pouze do a z místních svazků. Zdroj a cíl musí být přístupné jako místní připojení ve virtuální síti clusteru.

Chcete-li použít ``msrsync`` k naplnění cloudového svazku Azure clusterem Avere, postupujte podle následujících pokynů:

1. Instalace ``msrsync`` a její předpoklady (rsync a Python 2.6 nebo novější)
1. Určete celkový počet souborů a adresářů, které mají být zkopírovány.

   Použijte například nástroj ``prime.py`` Avere s ```prime.py --directory /path/to/some/directory``` argumenty (k <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>dispozici stažením adresy URL).

   Pokud nepoužíváte ``prime.py``, můžete vypočítat počet položek ``find`` pomocí nástroje GNU následujícím způsobem:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Vydělte počet položek číslem 64 a určete počet položek na proces. Toto číslo ``-f`` s možností můžete nastavit velikost bloků při spuštění příkazu.

1. Vydat ``msrsync`` příkaz pro kopírování souborů:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Pokud ``--inplace``používáte , přidejte druhé spuštění bez možnosti zkontrolovat, zda jsou data správně zkopírována:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH> && msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Tento příkaz je například navržen tak, aby přesunul 11 000 souborů v 64 procesech z /test/source-repository na /mnt/vfxt/repository:

   ``msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository && msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository``

## <a name="use-the-parallel-copy-script"></a>Použití skriptu paralelního kopírování

Skript ``parallelcp`` může být také užitečné pro přesunutí dat do back-end úložiště clusteru vFXT.

Níže uvedený skript přidá `parallelcp`spustitelný soubor . (Tento skript je určen pro Ubuntu; pokud ``parallel`` používáte jinou distribuci, musíte nainstalovat samostatně.)

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM
#!/bin/bash

display_usage() {
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n"
}

if [  \$# -le 1 ] ; then
    display_usage
    exit 1
fi

if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then
    display_usage
    exit 0
fi

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

### <a name="parallel-copy-example"></a>Příklad paralelní kopie

Tento příklad používá skript paralelní ``glibc`` kopie ke kompilaci pomocí zdrojových souborů z clusteru Avere.
<!-- xxx what is stored where? what is 'the avere cluster mount point'? xxx -->

Zdrojové soubory jsou uloženy v přípojném bodě clusteru Avere a soubory objektů jsou uloženy na místním pevném disku.

Tento skript používá výše uvedený skript paralelní kopie. Tato ``-j`` možnost se ``parallelcp`` ``make`` používá s a získat paralelizaci.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/node1 avere
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
