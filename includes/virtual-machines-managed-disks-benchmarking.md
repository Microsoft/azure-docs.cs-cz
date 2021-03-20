---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/29/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 25404837d5bc66ff415be8d8670eb6650475c30f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99094591"
---
## <a name="warm-up-the-cache"></a>Zahřívání mezipaměti

Disk s ukládáním do mezipaměti hostitele jen pro čtení může poskytovat vyšší IOPS než omezení disku. Chcete-li získat tento maximální výkon pro čtení z mezipaměti hostitele, nejprve je nutné zahříváním mezipaměti tohoto disku. Tím se zajistí, že se v IOs pro čtení, který nástroj pro srovnávací testy na svazek CacheReads, bude ve skutečnosti načítat mezipaměť, a ne přímo disk. Výsledkem přístupů do mezipaměti je více IOPS z disku s povolenou jedinou mezipamětí.

> [!IMPORTANT]
> Před spuštěním srovnávacích testů je nutné zahřívá mezipaměť při každém restartování virtuálního počítače.

## <a name="diskspd"></a>DISKSPD

[Stáhněte si nástroj DISKSP](https://github.com/Microsoft/diskspd) na virtuálním počítači. DISKSPD je nástroj, který můžete přizpůsobit a vytvořit tak vlastní syntetické úlohy. Pro spouštění testů srovnávacích testů použijeme stejné nastavení popsané výše. Můžete změnit specifikace pro testování různých úloh.

V tomto příkladu používáme následující sadu parametrů standardních hodnot:

- -c200G: vytvoří (nebo znovu vytvoří) vzorový soubor použitý v testu. Dá se nastavit v bajtech, KiB, MiB, GiB nebo blocích. V tomto případě se pro minimalizaci ukládání do mezipaměti používá velký soubor cílového souboru 200-GiB.
- -W100: Určuje procento operací, které jsou požadavky na zápis (-W0 je ekvivalentní 100% čtení).
- -b4K: Určuje velikost bloku v bajtech, KiB, MiB nebo GiB. V tomto případě se velikost bloku 4K používá k simulaci náhodných vstupně-výstupních testů.
- -F4: nastaví celkový počet čtyř vláken.
- -r: označuje test náhodných vstupně-výstupních operací (Přepisuje parametr-s).
- -o128: označuje počet nezpracovaných vstupně-výstupních požadavků na cíl na vlákno. Tato úroveň se označuje také jako hloubka fronty. V tomto případě se 128 používá ke zdůraznění procesoru.
- -W7200: Určuje dobu trvání zahřívání před začátkem měření.
- -D30: Určuje dobu trvání testu, včetně zahřívání.
- -SH: Zakažte ukládání do mezipaměti softwaru a hardwaru (ekvivalentní k-SUW).

Úplný seznam parametrů najdete v [úložišti GitHubu](https://github.com/Microsoft/diskspd/wiki/Command-line-and-parameters).

### <a name="maximum-write-iops"></a>Maximální počet IOPS zápisu
Používáme vysokou hloubku fronty 128, velikost bloku 8 KB a čtyři pracovní vlákna pro řízení operací zápisu. Pracovníci pro zápis jsou schopni provozovat na svazku "NoCacheWrites", který má tři disky s mezipamětí nastavenou na hodnotu "žádná".

Spusťte následující příkaz po dobu 30 sekund zahřívání a měření na 30 sekund:

`diskspd -c200G -w100 -b8K -F4 -r -o128 -W30 -d30 -Sh testfile.dat`

Výsledky ukazují, že Standard_D8ds_v4 virtuální počítač doručuje svůj maximální limit zápisu IOPS 12 800.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-write-io-per-second.png" alt-text="Pro 3208642560 celkový počet bajtů, maximální celkový počet vstupně-výstupních operací 391680, celkem 101,97 MiB/s a celkový počet 13052,65 I/O za sekundu.":::

### <a name="maximum-read-iops"></a>Maximální počet IOPS pro čtení

Používáme vysokou hloubku fronty 128, malý blok velikosti 4 KB a čtyři pracovní vlákna pro řízení operací čtení. Přípravní pracovníci načítají provoz na svazku "CacheReads", který má jeden disk s mezipamětí nastavenou na "ReadOnly".

Spusťte následující příkaz po dobu dvou hodin zahřívání a měření na 30 sekund:

`diskspd -c200G -b4K -F4 -r -o128 -W7200 -d30 -Sh testfile.dat`

Výsledky ukazují, že Standard_D8ds_v4 virtuální počítač doručuje svůj maximální limit pro čtení IOPS 77 000.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-read-io-per-second.png" alt-text="Pro 9652785152 celkový počet bajtů: 2356637 celkový počet vstupně-výstupních změn v/v, v 306,72 celkem MiB/s a celkem 78521,23 I/OS za sekundu.":::

### <a name="maximum-throughput"></a>Maximální propustnost

Chcete-li získat maximální propustnost čtení a zápisu, můžete přejít na větší velikost bloku 64 KB.
## <a name="fio"></a>FIO

FIO je oblíbený nástroj pro srovnávací úložiště virtuálních počítačů se systémem Linux. Nabízí flexibilitu pro výběr různých velikostí v/v, sekvenčních nebo náhodných operací čtení a zápisu. Vytvoří pracovní vlákna nebo procesy pro provedení zadaných vstupně-výstupních operací. Můžete určit typ vstupně-výstupních operací, které musí každá pracovní vlákno provádět pomocí souborů úloh. Vytvořili jsme jeden soubor úlohy pro každý scénář, který je znázorněný v níže uvedených příkladech. Specifikace v těchto souborech úloh můžete změnit na srovnávací testy různých úloh, které běží na Premium Storage. V příkladech používáme Standard_D8ds_v4, na kterém běží **Ubuntu**. Použijte stejné nastavení popsané na začátku srovnávacího testu a zazálohujte mezipaměť před spuštěním testů srovnávacích testů.

Než začnete, [Stáhněte si FIO](https://github.com/axboe/fio) a nainstalujte ho do svého virtuálního počítače.

Spusťte následující příkaz pro Ubuntu,

```
apt-get install fio
```

Pro řízení operací zápisu a čtyři pracovní vlákna pro řízení operací čtení na discích používáme čtyři pracovní vlákna. Pracovníci pro zápis jsou schopni provozovat na svazku "neuloženého", který má tři disky s mezipamětí nastavenou na hodnotu None. Přípravní pracovníci načítají provoz na svazku "readcache", který má jeden disk s mezipamětí nastavenou na "ReadOnly".

### <a name="maximum-write-iops"></a>Maximální počet IOPS zápisu

Vytvořte soubor úlohy s následujícími specifikacemi pro získání maximálního počtu vstupně-výstupních operací zápisu. Pojmenujte ho "fiowrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[writer1]
rw=randwrite
directory=/mnt/nocache
```

Poznamenejte si následující klíčové věci, které jsou v souladu s pokyny návrhu popsanými v předchozích částech. Tyto specifikace jsou nezbytné pro zajištění maximálního počtu IOPS,  

* Vysoká hloubka fronty 256.  
* Velikost malého bloku na 4 KB.  
* Více vláken provádějících náhodné zápisy.

Spusťte následující příkaz, který zahájí test FIO po dobu 30 sekund.  

```
sudo fio --runtime 30 fiowrite.ini
```

V průběhu testu můžete zobrazit počet vstupně-výstupních operací pro zápis, které virtuální počítač a prémiové disky přináší. Jak je znázorněno v následující ukázce, Standard_D8ds_v4 virtuální počítač doručuje svůj maximální limit zápisu IOPS 12 800 IOPS.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-uncached-writes-1.jpg" alt-text="Doručí se počet virtuálních počítačů zápisu IOPS a SSD Premium, což ukazuje, že zápisy jsou 13.1 k IOPS.":::

### <a name="maximum-read-iops"></a>Maximální počet IOPS pro čtení

Vytvořte soubor úlohy s následujícími specifikacemi pro získání maximálního počtu vstupně-výstupních operací čtení. Pojmenujte ho "fioread.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache
```

Poznamenejte si následující klíčové věci, které jsou v souladu s pokyny návrhu popsanými v předchozích částech. Tyto specifikace jsou nezbytné pro zajištění maximálního počtu IOPS,

* Vysoká hloubka fronty 256.  
* Velikost malého bloku na 4 KB.  
* Více vláken provádějících náhodné zápisy.

Spusťte následující příkaz, který zahájí test FIO po dobu 30 sekund.

```
sudo fio --runtime 30 fioread.ini
```

I když se test spustí, můžete zobrazit počet vstupně-výstupních operací, které virtuální počítače a disky Premium doručí. Jak je znázorněno v následující ukázce, Standard_D8ds_v4 virtuální počítač poskytuje více než 77 000 čtení IOPS. Toto je kombinace disku a výkonu mezipaměti.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-cached-reads-1.jpg" alt-text="Snímek obrazovky s množstvím virtuálních počítačů s zápisem a SSD úrovně Premium, který ukazuje, že čtení jsou 78.6 k.":::

### <a name="maximum-read-and-write-iops"></a>Maximální počet vstupně-výstupních operací čtení a zápisu

Vytvořte soubor úlohy s následujícími specifikacemi pro získání maximálního počtu vstupně-výstupních operací čtení a zápisu. Pojmenujte ho "fioreadwrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=3200
```

Poznamenejte si následující klíčové věci, které jsou v souladu s pokyny návrhu popsanými v předchozích částech. Tyto specifikace jsou nezbytné pro zajištění maximálního počtu IOPS,

* Vysoká hloubka fronty 128.  
* Velikost malého bloku na 4 KB.  
* Více vláken provádějících náhodné čtení a zápisy.

Spusťte následující příkaz, který zahájí test FIO po dobu 30 sekund.

```
sudo fio --runtime 30 fioreadwrite.ini
```

I když se test spustí, můžete zobrazit počet operací čtení a zápisu IOPS, které virtuální počítač a prémiové disky poskytují. Jak je znázorněno v následující ukázce, Standard_D8ds_v4 virtuální počítač nabízí více než 90 000 kombinovaných vstupně-výstupních operací čtení a zápisu. Toto je kombinace disku a výkonu mezipaměti.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-both-1.jpg" alt-text="Kombinované vstupně-výstupní operace čtení a zápisu ukazují, že čtení jsou 78.3 k a zápisy jsou 12.6 k IOPS.":::

### <a name="maximum-combined-throughput"></a>Maximální celková propustnost

Pro získání maximální propustnosti čtení a zápisu použijte větší velikost bloku a velkou hloubku fronty s více vlákny, které provádí čtení a zápis. Můžete použít velikost bloku 64 KB a hloubku fronty 128.