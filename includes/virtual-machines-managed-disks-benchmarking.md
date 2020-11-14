---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 09af5d9af749d43f9d15f42daee6b562a877397b
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2020
ms.locfileid: "94633437"
---
*Zahřívání mezipaměti*  
Disk s ukládáním do mezipaměti hostitele jen pro čtení umožňuje poskytnout vyšší IOPS, než je limit disku. Chcete-li získat tento maximální výkon pro čtení z mezipaměti hostitele, nejprve je nutné zahříváním mezipaměti tohoto disku. Tím se zajistí, že se v IOs pro čtení, který nástroj pro srovnávací testy na svazek CacheReads, bude ve skutečnosti načítat mezipaměť, a ne přímo disk. Výsledkem přístupů do mezipaměti je další IOPS z disku s povolenou jedinou mezipamětí.

> [!IMPORTANT]
> Před spuštěním srovnávacích testů je nutné zavěsit mezipaměť a pokaždé, když se virtuální počítač restartuje.

## <a name="iometer"></a>Iometer

[Stáhněte si nástroj IOMeter](http://sourceforge.net/projects/iometer/files/iometer-stable/1.1.0/iometer-1.1.0-win64.x86_64-bin.zip/download) na virtuálním počítači.

### <a name="test-file"></a>Testovací soubor

IOMeter používá testovací soubor, který je uložený na svazku, na kterém spouštíte test srovnávacích testů. Pro měření vstupně-výstupních operací disku a propustnosti disků v tomto testovacím souboru disky čte a zapisuje. IOMeter vytvoří tento testovací soubor, pokud jste ho nezadali. Vytvořte testovací soubor 200 GB s názvem iobw. tst na svazcích CacheReads a NoCacheWrites.

### <a name="access-specifications"></a>Specifikace přístupu

Specifikace, velikost vstupně-výstupních operací požadavků,% čtení/zápisu,% Random/sekvenční, se konfigurují pomocí karty specifikace přístupu v IOMeter. Vytvořte specifikaci přístupu pro každý ze scénářů popsaných níže. Vytvořte specifikace přístupu a "Uložit" s odpovídajícím názvem, například – RandomWrites \_ 8K, RandomReads \_ 8K. Vyberte odpovídající specifikaci při spuštění testovacího scénáře.

Příklad specifikací přístupu pro maximální scénář zápisu IOPS je uveden níže.  
    ![Příklad specifikací přístupu pro maximální zápis IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

### <a name="maximum-iops-test-specifications"></a>Specifikace maxima testů IOPS

K předvedení maximálního IOPs použijte menší velikost žádosti. Použijte 8K velikost žádosti a vytvořte specifikace pro náhodné zápisy a čtení.

| Specifikace přístupu | Velikost požadavku | Vybraných | Oprávnění |
| --- | --- | --- | --- |
| RandomWrites \_ 8K |8 tis. |100 |0 |
| RandomReads \_ 8K |8 tis. |100 |100 |

### <a name="maximum-throughput-test-specifications"></a>Specifikace maximální propustnosti testu

K předvedení maximální propustnosti použijte větší velikost žádosti. Použijte velikost žádosti 64 K a vytvořte specifikace pro náhodné zápisy a čtení.

| Specifikace přístupu | Velikost požadavku | Vybraných | Oprávnění |
| --- | --- | --- | --- |
| RandomWrites \_ 64 KB |64 K |100 |0 |
| RandomReads \_ 64 KB |64 K |100 |100 |

### <a name="run-the-iometer-test"></a>Spustit test IOMeter

Provedením následujících kroků zahříváte mezipaměť.

1. Vytvořte dvě specifikace přístupu s hodnotami uvedenými níže,

   | Name | Velikost požadavku | Vybraných | Oprávnění |
   | --- | --- | --- | --- |
   | RandomWrites \_ 1 MB |1 MB |100 |0 |
   | RandomReads \_ 1 MB |1 MB |100 |100 |
1. Spusťte test IOMeter pro inicializaci disku mezipaměti s následujícími parametry. Pro cílový svazek použijte tři pracovní vlákna a hloubku fronty 128. Nastavte hodnotu doba běhu testu na 2 hodiny na kartě nastavení testu.

   | Scénář | Cílový svazek | Name | Doba trvání |
   | --- | --- | --- | --- |
   | Inicializovat disk mezipaměti |CacheReads |RandomWrites \_ 1 MB |2 hodiny |
1. Spusťte test IOMeter pro vyhřívání disku mezipaměti s následujícími parametry. Pro cílový svazek použijte tři pracovní vlákna a hloubku fronty 128. Nastavte hodnotu doba běhu testu na 2 hodiny na kartě nastavení testu.

   | Scénář | Cílový svazek | Name | Doba trvání |
   | --- | --- | --- | --- |
   | Zahřívání disku mezipaměti |CacheReads |RandomReads \_ 1 MB |2 hodiny |

Po zahřívání disku mezipaměti pokračujte podle níže uvedených scénářů testování. Chcete-li spustit test IOMeter, použijte pro **každý** cílový svazek alespoň tři pracovní vlákna. Pro každé pracovní vlákno vyberte cílový svazek, nastavte hloubku fronty a vyberte jednu z uložených specifikací testu, jak je znázorněno v následující tabulce, aby se mohl spustit odpovídající testovací scénář. Tabulka také ukazuje očekávané výsledky pro IOPS a propustnost při spuštění těchto testů. Pro všechny scénáře se používá malá vstupně-výstupní operace o velikosti 8 KB a vysoká hloubka fronty 128.

| Scénář testu | Cílový svazek | Name | Výsledek |
| --- | --- | --- | --- |
| Max. Čtení IOPS |CacheReads |RandomWrites \_ 8K |50 000 IOPS |
| Max. Zápis IOPS |NoCacheWrites |RandomReads \_ 8K |64 000 IOPS |
| Max. Kombinované IOPS |CacheReads |RandomWrites \_ 8K |100 000 IOPS |
| NoCacheWrites |RandomReads \_ 8K | &nbsp; | &nbsp; |
| Max. Čtení MB/s |CacheReads |RandomWrites \_ 64 KB |524 MB/s |
| Max. Zápisy MB/s |NoCacheWrites |RandomReads \_ 64 KB |524 MB/s |
| Kombinované MB/s |CacheReads |RandomWrites \_ 64 KB |1000 MB/s |
| NoCacheWrites |RandomReads \_ 64 KB | &nbsp; | &nbsp; |

Níže jsou uvedeny snímky obrazovky výsledků IOMeter testů pro kombinované scénáře IOPS a propustnosti.

### <a name="combined-reads-and-writes-maximum-iops"></a>Kombinované čtení a zápisy – maximální počet IOPS

![Kombinované čtení a zápisy – maximální počet IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

### <a name="combined-reads-and-writes-maximum-throughput"></a>Kombinované čtení a zápis s maximální propustností

![Kombinované čtení a zápis s maximální propustností](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

## <a name="fio"></a>FIO

FIO je oblíbený nástroj pro srovnávací úložiště virtuálních počítačů se systémem Linux. Nabízí flexibilitu pro výběr různých velikostí v/v, sekvenčních nebo náhodných operací čtení a zápisu. Vytvoří pracovní vlákna nebo procesy pro provedení zadaných vstupně-výstupních operací. Můžete určit typ vstupně-výstupních operací, které musí každá pracovní vlákno provádět pomocí souborů úloh. Vytvořili jsme jeden soubor úlohy pro každý scénář, který je znázorněný v níže uvedených příkladech. Specifikace v těchto souborech úloh můžete změnit na srovnávací testy různých úloh, které běží na Premium Storage. V příkladech používáme standardní virtuální počítač DS 14 se systémem **Ubuntu**. Použijte stejné nastavení, které je popsané na začátku srovnávacího testu, a před spuštěním testů srovnávacích testů zahřívání mezipaměť.

Než začnete, [Stáhněte si FIO](https://github.com/axboe/fio) a nainstalujte ho do svého virtuálního počítače.

Spusťte následující příkaz pro Ubuntu,

```
apt-get install fio
```

Pro řízení operací zápisu a čtyři pracovní vlákna pro řízení operací čtení na discích používáme čtyři pracovní vlákna. Pracovníci pro zápis jsou schopni provozovat na svazku ". Cache", který má 10 disků s mezipamětí nastavenou na hodnotu None. Přípravní pracovníci načítají provoz na svazku "readcache", který má jeden disk s mezipamětí nastavenou na "ReadOnly".

### <a name="maximum-write-iops"></a>Maximální počet IOPS zápisu

Vytvořte soubor úlohy s následujícími specifikacemi pro získání maximálního počtu vstupně-výstupních operací zápisu. Pojmenujte ho "fiowrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Poznamenejte si následující klíčové věci, které jsou v souladu s pokyny návrhu popsanými v předchozích částech. Tyto specifikace jsou nezbytné pro zajištění maximálního počtu IOPS,  

* Vysoká hloubka fronty 256.  
* Velikost malého bloku na 8 KB.  
* Více vláken provádějících náhodné zápisy.

Spusťte následující příkaz, který zahájí test FIO po dobu 30 sekund.  

```
sudo fio --runtime 30 fiowrite.ini
```

V průběhu testu můžete zobrazit počet vstupně-výstupních operací pro zápis, které virtuální počítač a prémiové disky přináší. Jak je znázorněno v následující ukázce, virtuální počítač DS14 poskytuje maximální limit zápisu IOPS 50 000 IOPS.  
    ![Doručí se počet virtuálních počítačů pro zápis IOPS a prémiových disků.](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

### <a name="maximum-read-iops"></a>Maximální počet IOPS pro čtení

Vytvořte soubor úlohy s následujícími specifikacemi pro získání maximálního počtu vstupně-výstupních operací čtení. Pojmenujte ho "fioread.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Poznamenejte si následující klíčové věci, které jsou v souladu s pokyny návrhu popsanými v předchozích částech. Tyto specifikace jsou nezbytné pro zajištění maximálního počtu IOPS,

* Vysoká hloubka fronty 256.  
* Velikost malého bloku na 8 KB.  
* Více vláken provádějících náhodné zápisy.

Spusťte následující příkaz, který zahájí test FIO po dobu 30 sekund.

```
sudo fio --runtime 30 fioread.ini
```

I když se test spustí, můžete zobrazit počet vstupně-výstupních operací, které virtuální počítače a disky Premium doručí. Jak je znázorněno v následující ukázce, DS14 virtuální počítač nabízí více než 64 000 čtení IOPS. Toto je kombinace disku a výkonu mezipaměti.  
    ![Snímek obrazovky s množstvím virtuálních počítačů pro zápis IOPS a prémiových disků.](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

### <a name="maximum-read-and-write-iops"></a>Maximální počet vstupně-výstupních operací čtení a zápisu

Vytvořte soubor úlohy s následujícími specifikacemi pro získání maximálního počtu vstupně-výstupních operací čtení a zápisu. Pojmenujte ho "fioreadwrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Poznamenejte si následující klíčové věci, které jsou v souladu s pokyny návrhu popsanými v předchozích částech. Tyto specifikace jsou nezbytné pro zajištění maximálního počtu IOPS,

* Vysoká hloubka fronty 128.  
* Velikost malého bloku na 4 KB.  
* Více vláken provádějících náhodné čtení a zápisy.

Spusťte následující příkaz, který zahájí test FIO po dobu 30 sekund.

```
sudo fio --runtime 30 fioreadwrite.ini
```

I když se test spustí, můžete zobrazit počet operací čtení a zápisu IOPS, které virtuální počítač a prémiové disky poskytují. Jak je znázorněno v následující ukázce, DS14 virtuální počítač nabízí více než 100 000 kombinovaných vstupně-výstupních operací čtení a zápisu. Toto je kombinace disku a výkonu mezipaměti.  
    ![Kombinované vstupně-výstupní operace čtení a zápisu](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

### <a name="maximum-combined-throughput"></a>Maximální celková propustnost

Pro získání maximální propustnosti čtení a zápisu použijte větší velikost bloku a velkou hloubku fronty s více vlákny, které provádí čtení a zápis. Můžete použít velikost bloku 64 KB a hloubku fronty 128.
