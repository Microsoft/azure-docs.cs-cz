---
title: Přesun dat do avere vFXT pro Azure
description: Jak přidat data do nového svazku úložiště pro použití s avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: 76bbe60397ebb01aed5694d933b3067f778a4c21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85505592"
---
# <a name="moving-data-to-the-vfxt-cluster---parallel-data-ingest"></a>Přesun dat do clusteru vFXT – paralelní data ingestování

Po vytvoření nového clusteru vFXT může být prvním úkolem přesunutí dat do nového svazku úložiště v Azure. Pokud však vaše Obvyklá metoda přesunu dat vydává jednoduchý příkaz kopírování z jednoho klienta, pravděpodobně se zobrazí pomalý výkon při kopírování. Kopírování s jedním vláknem není vhodnou možností pro kopírování dat do back-endu clusteru avere vFXT.

Vzhledem k tomu, že avere vFXT pro cluster Azure je škálovatelná mezipaměť více klientských počítačů, nejrychlejší a nejúčinnější způsob, jak kopírovat data, je více klientů. Tato technika parallelizes přijímání souborů a objektů.

![Diagram znázorňující pohyb vícevláknových dat s více klienty: vlevo nahoře je ikona pro místní hardwarové úložiště s více šipkami. Šipky ukazují na čtyři klientské počítače. Z každého klientského počítače tři šipky směřuje k avere vFXT. Z vFXT avere se několik šipek odkazuje na úložiště objektů BLOB.](media/avere-vfxt-parallel-ingest.png)

``cp``Příkazy nebo ``copy`` , které se běžně používají k přenosu dat z jednoho úložného systému do jiného, jsou procesy s jedním vláknem, které kopírují pouze jeden soubor v jednom okamžiku. To znamená, že souborový server bude v jednom okamžiku přijímat pouze jeden soubor, což je odpad z prostředků clusteru.

V tomto článku se dozvíte o strategiích pro vytvoření vícevláknového systému kopírování souborů s více vlákny k přesunu dat do clusteru avere vFXT. Vysvětluje koncepty přenosu souborů a body rozhodování, které lze použít k efektivnímu kopírování dat pomocí více klientů a jednoduchých příkazů kopírování.

Vysvětluje taky některé nástroje, které vám pomůžou. ``msrsync``Nástroj lze použít k částečnému automatizaci procesu rozdělení datové sady do kontejnerů a používání ``rsync`` příkazů. Tento ``parallelcp`` skript je další nástroj, který čte zdrojový adresář a automaticky vystavuje příkazy kopírování. ``rsync``Nástroj lze také použít ve dvou fázích k poskytnutí rychlejšího kopírování, které stále poskytuje konzistenci dat.

Kliknutím na odkaz přejdete do části:

* [Ukázka ručního kopírování](#manual-copy-example) – důkladné vysvětlení pomocí příkazů kopírování
* [Příklad dvoufázové rsync](#use-a-two-phase-rsync-process)
* [Příklad částečně automatizovaného (msrsync)](#use-the-msrsync-utility)
* [Příklad paralelního kopírování](#use-the-parallel-copy-script)

## <a name="data-ingestor-vm-template"></a>Šablona virtuálního počítače pro přijímání dat

K automatickému vytvoření virtuálního počítače pomocí nástrojů pro příjem paralelních dat uvedených v tomto článku je k dispozici Správce prostředků šablona na GitHubu.

![Diagram znázorňující několik šipek od úložiště objektů blob, hardwarového úložiště a zdrojů souborů Azure. Šipky ukazují na "virtuální počítač pro ingestování dat" a odtud více šipek ukazují na avere vFXT.](media/avere-vfxt-ingestor-vm.png)

Virtuální počítač pro ingestování dat je součástí kurzu, ve kterém nově vytvořený virtuální počítač připojí cluster avere vFXT a stáhne spouštěcí skript z clusteru. Další informace najdete v tématu spuštění [virtuálního počítače](https://github.com/Azure/Avere/blob/master/docs/data_ingestor.md) pro ingestování dat.

## <a name="strategic-planning"></a>Strategické plánování

Při navrhování strategie pro paralelní kopírování dat byste měli pochopit kompromisy v velikosti souborů, počtu souborů a hloubkě adresáře.

* Když jsou soubory malé, je metrika zájmu soubory za sekundu.
* Když jsou soubory velké (10MiBi nebo větší), je metrika zájmu v bajtech za sekundu.

Každý proces kopírování má míru propustnosti a přenosovou rychlost přenosu souborů, která se dá změřit časováním příkazu pro kopírování a určením velikosti souboru a počtu souborů. Vysvětluje, jak změřit sazby jsou mimo rozsah tohoto dokumentu, ale je důležité pochopit, zda budete pracovat s malými nebo velkými soubory.

## <a name="manual-copy-example"></a>Příklad ručního kopírování

Můžete ručně vytvořit vícevláknovou kopii na klientovi spuštěním více než jednoho příkazu kopírování na pozadí v předdefinovaných sadách souborů nebo cest.

Příkaz Linux/UNIX ``cp`` obsahuje argument ``-p`` pro zachování vlastnictví a mtime metadat. Přidání tohoto argumentu do příkazů níže je volitelné. (Přidáním argumentu se zvýší počet volání systému souborů odeslaných z klienta do cílového systému souborů pro úpravu metadat.)

Tento jednoduchý příklad kopíruje dva soubory paralelně:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Po vystavení tohoto příkazu se v `jobs` příkazu zobrazí, že jsou spuštěná dvě vlákna.

### <a name="predictable-filename-structure"></a>Předvídatelná struktura názvů souborů

Pokud jsou názvy souborů předvídatelné, můžete použít výrazy k vytvoření paralelních vláken kopírování.

Pokud například váš adresář obsahuje soubory 1000, které jsou očíslovány postupně z `0001` na `1000` , můžete použít následující výrazy k vytvoření deseti paralelních vláken, které každý soubor kopie 100:

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

Pokud není struktura názvů souborů předvídatelná, můžete soubory seskupit podle názvů adresářů.

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

### <a name="when-to-add-mount-points"></a>Kdy přidat přípojné body

Po zobrazení dostatečného množství paralelních vláken proti jednomu přípojnému bodu systému souborů aplikace bude k dispozici bod, ve kterém přidání dalších vláken neposkytuje větší propustnost. (V závislosti na typu dat se měří propustnost v souborech za sekundu nebo v bajtech za sekundu.) Nebo horší, více vláken může někdy způsobit snížení propustnosti.

Pokud k tomu dojde, můžete přidat přípojné body na straně klienta do dalších IP adres clusteru vFXT pomocí stejné cesty vzdáleného připojení systému souborů:

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

### <a name="when-to-add-clients"></a>Kdy přidat klienty

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

### <a name="create-file-manifests"></a>Vytváření manifestů souborů

Po porozumění výše zmíněných přístupů (více než jedno umístění na jeden cíl, více cílů na každého klienta, více klientů na zdrojový systém souborů přístupný pro síť) zvažte toto doporučení: manifesty souborů sestavení a pak je používejte s příkazy kopírování mezi více klienty.

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

## <a name="use-a-two-phase-rsync-process"></a>Použití dvoufázové rsync procesu

Standardní ``rsync`` Nástroj nefunguje dobře pro naplnění cloudového úložiště prostřednictvím avere vFXT pro systém Azure, protože generuje velký počet operací vytvoření a přejmenování souboru, aby se zajistila integrita dat. Tuto možnost můžete ale bezpečně použít ``--inplace`` ``rsync`` k přeskočení podrobnějšího kopírování, pokud budete postupovat s druhým spuštěním, který kontroluje integritu souboru.

Standardní ``rsync`` operace kopírování vytvoří dočasný soubor a naplní ho daty. Pokud se přenos dat úspěšně dokončí, dočasný soubor se přejmenuje na původní název souboru. Tato metoda zaručuje konzistenci i v případě, že soubory jsou během kopírování k dispozici. Tato metoda ale generuje více operací zápisu, což zpomaluje přesun souborů přes mezipaměť.

Možnost ``--inplace`` zapíše nový soubor přímo do konečného umístění. U souborů není zaručena konzistence během přenosu, ale to není důležité, pokud vytváříte systém úložiště pro pozdější použití.

Druhá ``rsync`` operace slouží jako kontrola konzistence první operace. Vzhledem k tomu, že soubory již byly zkopírovány, druhá fáze představuje rychlou kontrolu, aby soubory v cílovém umístění odpovídaly souborům na zdroji. Pokud se některé soubory neshodují, jsou překopírovány.

Obě fáze můžete vydávat současně v jednom příkazu:

```bash
rsync -azh --inplace <source> <destination> && rsync -azh <source> <destination>
```

Tato metoda představuje jednoduchou a časově efektivní metodu pro datové sady až po počet souborů, které může interní správce adresářů zpracovat. (Obvykle se jedná o 200 000 000 souborů pro cluster se 3 uzly, 500 000 000 souborů pro cluster se šesti uzly atd.)

## <a name="use-the-msrsync-utility"></a>Použití nástroje msrsync

``msrsync``Nástroj lze také použít k přesunu dat do back-endové základní souborového clusteru avere. Tento nástroj je určený k optimalizaci využití šířky pásma spuštěním několika paralelních ``rsync`` procesů. Je k dispozici z GitHubu na adrese <https://github.com/jbd/msrsync> .

``msrsync`` rozdělí zdrojový adresář do samostatných "intervalů" a potom spustí jednotlivé ``rsync`` procesy v jednotlivých intervalech.

Předběžné testování pomocí virtuálního počítače se čtyřmi jádry ukázalo při použití procesů 64 nejlepší efektivitu. Pomocí ``msrsync`` možnosti ``-p`` nastavte počet procesů na 64.

Můžete také použít ``--inplace`` argument s ``msrsync`` příkazy. Pokud použijete tuto možnost, zvažte spuštění druhého příkazu (stejně jako u [rsync](#use-a-two-phase-rsync-process)popsané výše), aby se zajistila integrita dat.

``msrsync`` lze zapisovat pouze do místních svazků a z nich. Zdroj a cíl musí být přístupné jako místní připojení ve virtuální síti clusteru.

Pokud chcete použít ``msrsync`` k naplnění cloudového svazku Azure pomocí clusteru avere, postupujte podle těchto pokynů:

1. Nainstalovat ``msrsync`` a jeho požadavky (rsync a Python 2,6 nebo novější)
1. Určete celkový počet souborů a adresářů, které mají být zkopírovány.

   Použijte například nástroj avere ``prime.py`` s argumenty ```prime.py --directory /path/to/some/directory``` (k dispozici stažením adresy URL <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py> ).

   Pokud nepoužíváte ``prime.py`` , můžete vypočítat počet položek pomocí nástroje GNU následujícím ``find`` způsobem:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Rozdělte počet položek podle 64 k určení počtu položek na proces. Toto číslo použijte s ``-f`` možností nastavení velikosti intervalů při spuštění příkazu.

1. Vydejte ``msrsync`` příkaz ke kopírování souborů:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Pokud používáte ``--inplace`` , přidejte druhé spuštění bez možnosti, abyste zkontrolovali, jestli jsou data správně zkopírovaná:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH> && msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Například tento příkaz je navržen pro přesun 11 000 souborů v 64 Process z/test/source-repository na/mnt/vfxt/Repository:

   ``msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository && msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository``

## <a name="use-the-parallel-copy-script"></a>Použití skriptu paralelního kopírování

Tento ``parallelcp`` skript může být vhodný také pro přesun dat do back-endu clusteru vFXT.

Do následujícího skriptu se přidá spustitelný soubor `parallelcp` . (Tento skript je určený pro Ubuntu; Pokud používáte jinou distribuci, musíte nainstalovat ``parallel`` samostatně.)

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

### <a name="parallel-copy-example"></a>Příklad paralelního kopírování

V tomto příkladu se používá skript paralelního kopírování ke kompilaci ``glibc`` pomocí zdrojových souborů z clusteru avere.
<!-- xxx what is stored where? what is 'the avere cluster mount point'? xxx -->

Zdrojové soubory jsou uloženy v přípojném bodu clusteru avere a soubory objektů jsou uloženy na místním pevném disku.

Tento skript používá skript paralelního kopírování. Možnost se ``-j`` používá s ``parallelcp`` a ``make`` k získání paralelismu.

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
