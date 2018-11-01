---
title: Přesun dat do Avere vFXT pro Azure
description: Přidání dat do nového svazku úložiště pro použití s Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: bf16c0fbc7090bf9b548796765502cde1731aef9
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50633824"
---
# <a name="moving-data-to-the-vfxt-cluster---parallel-data-ingest"></a>Přesun dat do clusteru vFXT - paralelních dat ingestu 

Po vytvoření nového clusteru vFXT, vaše první úkol může být pro přesun dat na nový svazek úložiště. Pokud vaše obvyklé metody pro přesun dat vydává příkaz jednoduché kopírování z jednoho klienta, uvidíte pravděpodobně výkonem pomalých kopírování. S jedním vláknem kopírování není vhodný pro kopírování dat do úložiště clusteru vFXT Avere back-endu.

Vzhledem k tomu, že je Avere vFXT cluster škálovatelné více klientů mezipaměti, nejúčinnější a nejrychlejší způsob, jak kopírovat data do ní je s více klienty. Tato technika parallelizes ingestování soubory a objekty.

![Diagram zobrazující více klientů a vícevláknové přesouvání dat: vlevo nahoře, ikona pro úložiště v místním hardwaru má více šipky pocházející z něj. Šipky odkazovat na čtyři počítače klienta. Z každý klientský počítač tři šipky ukazují směrem k Avere vFXT. Z Avere vFXT více šipky ukazují do úložiště objektů Blob.](media/avere-vfxt-parallel-ingest.png) 

``cp`` Nebo ``copy`` jsou příkazy, které se běžně používají k používání pro přenos dat z jednoho úložiště systému na jiný s jedním vláknem procesy, které kopírují pouze jeden soubor současně. To znamená, že je souborový server ingestovat pouze jeden soubor v době – které by o plýtvání prostředky clusteru.

Tento článek vysvětluje strategie pro vytvoření souboru více klientů, vícevláknové kopírování pro přesun dat do clusteru vFXT Avere systému. Vysvětluje koncepty přenosu souborů a rozhodovací body, které lze použít pro kopírování efektivní dat pomocí více klientů a příkazy pro jednoduché kopírování.

Také vysvětluje některé nástroje, které vám mohou pomoci. ``msrsync`` Částečně automatizovat proces rozdělení datové sady do bloků a použití příkazů rsync můžete použít nástroj. ``parallelcp`` Skript je jiný nástroj, který přečte zdrojový adresář a problémy automaticky kopírovat příkazy.  

Klikněte na odkaz pro přechod na oddíl:

* [Ruční kopírování příkladů](#manual-copy-example) – důkladné vysvětlení, používání příkazy pro kopírování
* [Příklad částečně automatizovaný (msrsync)](#use-the-msrsync-utility-to-populate-cloud-volumes) 
* [Paralelní kopírování příkladů](#use-the-parallel-copy-script)

## <a name="data-ingestor-vm-template"></a>Šablonu virtuálního počítače přijímání dat

Šablona Resource Manageru je k dispozici na Githubu, automaticky vytvoří virtuální počítač pomocí nástroje pro ingestování paralelních dat uvedených v tomto článku. 

![Diagram znázorňující několik šipky každý z úložiště objektů blob, úložiště hardwaru a zdrojů Azure file. Šipky bod registrace k "data přijímač virtuální počítač" a odtud více šipky ukazují na Avere vFXT](media/avere-vfxt-ingestor-vm.png)

Přijímání dat virtuálního počítače je součástí kurzu, kde nově vytvořenému virtuálnímu počítači připojí Avere vFXT cluster a stáhne jeho spuštění skriptu clusteru. Čtení [Bootstrap přijímání dat virtuálního počítače](https://github.com/Azure/Avere/blob/master/docs/data_ingestor.md) podrobnosti.

## <a name="strategic-planning"></a>Strategické plánování

Při vytváření strategie pro kopírování dat v paralelní, měli byste porozumět kompromisy velikost souboru, počet souborů a hloubka adresáře.

* Po malých souborů je metrika zájmu soubory za sekundu.
* Když jsou soubory velké (10MiBi nebo vyšší), metriky, které vás zajímají je bajtů za sekundu.

Každý proces kopírování se propustnost a rychlost přenosu souborů, které lze změřit podle časování délka příkazu kopírování a které budou zohledňovat velikost souboru a počet souborů. Vám vysvětlíme, jak měřit sazby sahá nad rámec tohoto dokumentu, ale je nutné pochopit, jestli budete jednat s malé nebo velké soubory.

## <a name="manual-copy-example"></a>Ruční kopírování příkladů 

Můžete ručně vytvořit kopii vícevláknové na klientovi spuštěním více než jednoho příkazu kopírování najednou na pozadí proti předdefinované sady soubory nebo cesty.

Linux/UNIX ``cp`` příkaz zahrnuje argument ``-p`` zachovat vlastnictví a mtime metadat. Přidávání do níže uvedených příkazů tento argument je volitelný. (Přidání argument zvyšuje počet volání systému souborů odeslaných z klienta do cílového systému souborů pro úpravu metadat.)

Tento jednoduchý příklad zkopíruje paralelně dva soubory:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Po vydání tohoto příkazu `jobs` příkaz zobrazí, zda jsou spuštěny dvěma vlákny.

### <a name="predictable-filename-structure"></a>Struktura předvídatelný název souboru 

Pokud vaše názvy souborů se u nich předvídat, můžete použít výrazy k vytvoření kopie paralelních vláken. 

Například, pokud váš adresář obsahuje 1000 souborů, které jsou číslována od `0001` k `1000`, následující výrazy můžete použít k vytvoření deset paralelních vláken, že každý kopírování 100 souborů:

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

### <a name="unknown-filename-structure"></a>Neznámý název souboru struktura

Pokud strukturu pojmenování souboru není předvídatelné, můžete seskupit soubory podle názvů adresářů. 

V tomto příkladu shromažďuje celý adresář k odeslání ``cp`` příkazy se spouští jako úlohy na pozadí:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

Po shromáždění souborů, můžete spustit paralelní kopírovat příkazy k rekurzivnímu kopírování podadresářů a veškerý jejich obsah:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

### <a name="when-to-add-mount-points"></a>Přidání přípojné body

Jakmile budete mít dostatek paralelních vláken, že přejdete na jednom místě přípojný bod systému souborů, bude bod, ve kterém přidáte další vlákna neposkytuje větší propustnost. (Propustnost se měří v souborech za sekundu nebo počet bajtů za sekundu, v závislosti na typu dat.) Nebo horší, over-pass-the dělení na vlákna mohou někdy způsobit snížení propustnosti.  

Pokud k tomu dojde, můžete přidat na straně klienta přípojné body na jiné vFXT clusteru IP adresy, pomocí stejné cesty připojení vzdáleného systému souborů:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

Přidání bodů připojení na straně klienta vám umožňuje vytvořit fork vypne další kopie příkazy pro další `/mnt/destination[1-3]` přípojné body, další dosažení paralelismu.  

Například pokud jsou velmi velké soubory, můžete třeba definovat příkazy pro kopírování použít odlišné cílové cesty odesláním další příkazy paralelně z klienta vytváří kopii.

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

V předchozím příkladu jsou všechny tři cílové přípojné body cílený procesy kopírování souborů klienta.

### <a name="when-to-add-clients"></a>Kdy se má přidat klienty

A konečně, když dosáhli možnosti klienta, přidání více podprocesů kopírování další přípojné body, nepřinese žádné další soubory za sekundu nebo zvyšuje bajty/s. V takovém případě můžete nasadit jiného klienta se stejnou sadou přípojné body, které poběží vlastní sadu procesů kopírování souborů. 

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

### <a name="create-file-manifests"></a>Vytváření souboru manifestu

Po Principy přístupy výše (více kopírování vláken na cílový, více cílů jednoho klienta, více klientů za síťově přístupné zdrojového systému souborů), zvažte tato doporučení: sestavení souboru manifestů a používáte je se kopírování příkazy do více klientů.

Tento scénář využívá UNIX ``find`` příkaz pro vytvoření manifestů souborů nebo adresářů:

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

Tento výsledek přesměrujte do souboru: `find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Pak můžete iterovat manifest, počet souborů a určení velikosti podadresáře pomocí příkazy BASH:

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l`    `du -sh ${i}`"; done
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

A konečně musí si vytvořte skutečný soubor příkazy pro kopírování na klienty.  

Pokud máte čtyři klientů, použijte tento příkaz:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Pokud máte pět klientů, použijte vypadat přibližně takto:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

A pro šest... Potom údaje Extrapolujte podle potřeby.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

Zobrazí se *N* výsledné soubory, jeden pro každou z vašich *N* klientů, které obsahuje názvy cesty k adresářům úroveň 4 získaný jako součást výstup z `find` příkaz. 

Pomocí každého souboru sestavení kopírovací příkazy:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

Výše uvedeného vám poskytne *N* soubory, každá kopie příkazem na řádku, který může běžet jako BASH skript na straně klienta. 

Cílem je více vláken současně za klienta tyto skripty spustit souběžně na více klientů.

## <a name="use-the-msrsync-utility-to-populate-cloud-volumes"></a>Použijte nástroj msrsync k naplnění svazky cloudu

``msrsync`` Nástroj můžete použít také pro přesun dat do vyfiltrovat jádra back-endu pro Avere cluster. Tento nástroj je určená k optimalizaci využití šířky pásma spuštěním více paralelních ``rsync`` procesy. Je k dispozici na Githubu v https://github.com/jbd/msrsync.

``msrsync`` rozdělí zdrojový adresář do samostatných "bloky" a pak spustí jednotlivé ``rsync`` procesy na jednotlivé sektory.

Předběžné testování pomocí virtuálních počítačů čtyři jádra jsme si ukázali nejlepších výsledků, při použití 64 procesů. Použití ``msrsync`` možnost ``-p`` nastavit počet procesů na 64.

Všimněte si, že ``msrsync`` lze zapisovat pouze do a z místní svazky. Zdroj a cíl musí být přístupné jako místní připojení ve virtuální síti clusteru.

Pokud chcete použít k naplnění svazku cloudu Azure s clusterem Avere msrsync, postupujte podle těchto pokynů:

1. Nainstalujte msrsync a nezbytný software (rsync a Python 2.6 nebo novější)
1. Zjistěte celkový počet souborů a adresářů ke zkopírování.

   Třeba použít nástroj Avere ``prime.py`` s argumenty ```prime.py --directory /path/to/some/directory``` (k dispozici stažením url https://raw.githubusercontent.com/Azure/Avere/master/src/dataingestor/prime.py).

   Pokud nepoužíváte ``prime.py``, můžete vypočítat počet položek s Gnu ``find`` nástroj následujícím způsobem:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Počet položek, které dělit 64 k určení počtu položek na proces. Pomocí tohoto čísla s ``-f`` možnost nastavit velikost ze sektorů při spuštění příkazu.

1. Vydejte příkaz msrsync kopírování souborů:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Třeba tento příkaz slouží k přesunutí 11 000 souborů do 64 procesů z /test/source-repository do /mnt/vfxt/repository:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository``

## <a name="use-the-parallel-copy-script"></a>Použití paralelních kopírovat skript

``parallelcp`` Skript také může být užitečná pro přesun dat do vašeho clusteru vFXT back-endového úložiště. 

Níže uvedený skript bude spustitelný soubor přidáváte `parallelcp`. (Tento skript je navržená pro Ubuntu; Pokud používáte jiné distribuce, musíte nainstalovat ``parallel`` zvlášť.)

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

### <a name="parallel-copy-example"></a>Paralelní kopírování příkladů

Tento příklad používá ke kompilaci skriptu parallel kopírování ``glibc`` použití zdrojových souborů z Avere clusteru. 
<!-- xxx what is stored where? what is 'the avere cluster mount point'? xxx -->

Pro bod připojení clusteru Avere budou uložené zdrojové soubory a soubory objektů jsou uloženy na místní pevný disk.

Tento skript používá paralelní kopírovat skript výše. Možnost ``-j`` se používá s ``parallelcp`` a ``make`` získat paralelního zpracování.

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

