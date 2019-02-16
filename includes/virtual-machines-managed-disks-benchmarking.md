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
ms.openlocfilehash: 04b6ad25c1ecd10a9480dcbf3e2b4f75e114a6f9
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331172"
---
*Zahřívá se do mezipaměti*  
Disk s použití mezipaměti u hostitele jen pro čtení se schopnost poskytovat vyšší vstupně-výstupních operací než limit na disku. Chcete-li získat tento maximálního výkonu při čtení z mezipaměti hostitele, nejprve je musí zahřívání mezipaměti tento disk. Tím se zajistí, že IOs pro čtení, že nástroj pro srovnávací testy, se bude řídit na čtení z mezipaměti svazku, ve skutečnosti přístupy do mezipaměti a nikoli disk přímo. Výsledek přístupy do mezipaměti v další vstupně-výstupních operací z jedné mezipaměti povolena disku.

> [!IMPORTANT]
> Mezipaměť musí zahřívání před spuštěním srovnávací testy, pokaždé, když se virtuální počítač nerestartuje.

## <a name="tools"></a>Nástroje

### <a name="iometer"></a>Iometer

[Stáhněte si nástroj Iometer](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) na virtuálním počítači.

#### <a name="test-file"></a>Soubor testu

Iometer používá testovací soubor, který je uložený na svazku, na kterém jste spustili test srovnávací testy. Jednotky čtení a zápisu u tohoto souboru testů chcete změřit disku za SEKUNDU a propustnost. Iometer vytvoří tento testovací soubor, pokud jste nezadali. Vytvořte soubor testovacího 200 GB s názvem iobw.tst na čtení z mezipaměti a NoCacheWrites svazky.

#### <a name="access-specifications"></a>Specifikace Access

Specifikace, požádat o velikost vstupně-výstupních operací, % r/w, % náhodných nebo sekvenčních konfigurují na kartě "Specifikace Access" v Iometer. Vytvořte specifikaci přístupu pro jednotlivé scénáře popsané níže. Vytvořte specifikace přístup s "Save (Uložit) s odpovídajícím názvem jako – RandomWrites\_8 kb RandomReads\_8 kB. Vyberte odpovídající specifikaci při spuštění testu scénáře.

Níže je uveden příklad specifikací přístup pro scénář maximální vstupně-výstupních operací zápisu  
    ![Příklad specifikací přístup pro zápis maximální IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

#### <a name="maximum-iops-test-specifications"></a>Maximální vstupně-výstupních operací testovat specifikace

Abychom si předvedli maximální IOPs, použijte menší velikost požadavku. Použít žádost o velikosti 8 kB a vytvoření specifikace pro náhodné zápisy a čtení.

| Specifikace přístupu | Velikost požadavku | Náhodné % | % Čtení |
| --- | --- | --- | --- |
| RandomWrites\_8 kb |8 KB |100 |0 |
| RandomReads\_8K |8 KB |100 |100 |

#### <a name="maximum-throughput-test-specifications"></a>Specifikace testů maximální propustnost

Abychom si předvedli maximální propustnost, použijte větší velikost požadavku. Použít žádost o velikosti 64 kB a vytvoření specifikace pro náhodné zápisy a čtení.

| Specifikace přístupu | Velikost požadavku | Náhodné % | % Čtení |
| --- | --- | --- | --- |
| RandomWrites\_64 kB |64 K |100 |0 |
| RandomReads\_64K |64 K |100 |100 |

#### <a name="run-the-iometer-test"></a>Spustit Iometer test

Provedení kroků zahřívání mezipaměti

1. Vytvořit dva specifikace přístup s hodnotami ukazuje následující příklad.

   | Název | Velikost požadavku | Náhodné % | % Čtení |
   | --- | --- | --- | --- |
   | RandomWrites\_1 MB |1 MB |100 |0 |
   | RandomReads\_1 MB |1 MB |100 |100 |
1. Spusťte test Iometer pro inicializaci mezipaměti disku s následujícími parametry. Použití tří pracovních vláken pro cílový svazek a hloubka fronty 128. Nastavení doby trvání "Doba běhu" testu do 2 hodin na kartě "Nastavení testu".

   | Scénář | Cílový svazek | Název | Doba trvání |
   | --- | --- | --- | --- |
   | Inicializovat Disk mezipaměti |CacheReads |RandomWrites\_1 MB |2 hodin |
1. Spusťte test Iometer pro zahájení práce s disk mezipaměti s následujícími parametry. Použití tří pracovních vláken pro cílový svazek a hloubka fronty 128. Nastavení doby trvání "Doba běhu" testu do 2 hodin na kartě "Nastavení testu".

   | Scénář | Cílový svazek | Název | Doba trvání |
   | --- | --- | --- | --- |
   | Zahřívání Disk mezipaměti |CacheReads |RandomReads\_1 MB |2 hodin |

Po disk mezipaměti je provozní teplotu, pokračujte v testu scénáře uvedené níže. Ke spuštění testů Iometer, použijte aspoň tři pracovní vlákna pro **každý** cílit na svazku. Pro každý pracovní podproces vyberte cílový svazek, nastavte hloubka fronty a vyberte jednu z uložené testovací specifikace, jak je znázorněno v následující tabulce, chcete-li spustit příslušný testovací scénář. Tabulka také ukazuje očekávané výsledky IOPS a propustnost při spuštění těchto testů. Ve všech případech se používá malou velikost vstupně-výstupních operací o velikosti 8 KB a hloubka vysokou fronty 128.

| Testovací scénář | Cílový svazek | Název | Výsledek |
| --- | --- | --- | --- |
| Max. Čtení vstupně-výstupních operací |CacheReads |RandomWrites\_8 kb |50 000 VSTUPNĚ-VÝSTUPNÍCH OPERACÍ |
| Max. Zápis IOPS |NoCacheWrites |RandomReads\_8K |64 000 VSTUPNĚ-VÝSTUPNÍCH OPERACÍ |
| Max. Kombinované vstupně-výstupních operací |CacheReads |RandomWrites\_8 kb |100 000 VSTUPNĚ-VÝSTUPNÍCH OPERACÍ |
| NoCacheWrites |RandomReads\_8K | &nbsp; | &nbsp; |
| Max. Přečtené MB/s |CacheReads |RandomWrites\_64 kB |524 MB/s |
| Max. Zápisy MB/s |NoCacheWrites |RandomReads\_64K |524 MB/s |
| Kombinované MB/s |CacheReads |RandomWrites\_64 kB |1 000 MB za sekundu |
| NoCacheWrites |RandomReads\_64K | &nbsp; | &nbsp; |

Tady jsou snímky obrazovky Iometer výsledky testu pro kombinované scénáře IOPS a propustnost.

#### <a name="combined-reads-and-writes-maximum-iops"></a>Kombinované čte a zapisuje maximální IOPS

![Kombinované přečtených a zapsaných maximální IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

#### <a name="combined-reads-and-writes-maximum-throughput"></a>Kombinované čte a zapisuje maximální propustnost

![Kombinované přečtených a zapsaných maximální propustnost](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO

FIO je oblíbený nástroj pro srovnávací test úložiště na virtuální počítače s Linuxem. Má flexibilitu k výběru různých velikostí vstupně-výstupních operací, sekvenční nebo náhodné čtení a zápisu. Vytvoří se pracovní vlákna a procesy k provedení zadané vstupně-výstupních operací. Můžete zadat typ každého pracovního vlákna musí provádět pomocí úlohy souborů vstupně-výstupních operací. Vytvořili jsme jeden soubor projektu scénář předvedené v příkladech níže. Můžete změnit podle požadavků v těchto souborech úlohy do testu výkonnosti různé úlohy běžící na Premium Storage. V příkladech používáme spuštěné virtuální počítače Standard DS 14 **Ubuntu**. Použijte stejné nastavení, které jsou popsané na začátku [srovnávací testy části](#Benchmarking) a zahřívání mezipaměti před spuštěním testů srovnávací testy.

Než začnete, [stáhnout FIO](https://github.com/axboe/fio) a nainstalujte ho na virtuálním počítači.

Spusťte následující příkaz pro Ubuntu

```
apt-get install fio
```

Čtyři pracovních vláken pro řízení operací zápisu a čtyři pracovních vláken slouží k řízení operace čtení na discích. Zápis pracovníci přinášejí provoz na svazku "nocache", který má 10 disků s mezipamětí na hodnotu "None". Čtení pracovníci přinášejí provoz na svazku "readcache", který obsahuje jeden disk s mezipaměti nastaveným na "Jen pro čtení".

#### <a name="maximum-write-iops"></a>Maximální zápis IOPS

Vytvořte soubor projektu s následujícími specifikacemi získat maximální zápis IOPS. Pojmenujte ji "fiowrite.ini".

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

Mějte na paměti následující základy, které jsou v souladu s pokyny návrhu, které jsou popsané v předchozích částech. Tyto specifikace jsou nezbytné k podpoře maximální IOPS  

* Hloubka fronty vysokou 256.  
* Malé velikosti 8 kB.  
* Více vláken provádění náhodných zápisů.

Spusťte následující příkaz a FIO test po dobu 30 sekund, podívejte se na  

```
sudo fio --runtime 30 fiowrite.ini
```

Při spuštění testů se budete moct zobrazit počet zápis IOPS virtuálního počítače a jsou doručováním disks úrovně Premium. Jak je znázorněno v následující ukázce, DS14 VM dosahuje své maximální zápis IOPS maximálně 50 000 vstupně-výstupních operací.  
    ![Počet disků virtuálního počítače vstupně-výstupních operací a Premium poskytují zápisu](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

#### <a name="maximum-read-iops"></a>Maximální počet vstupně-výstupních operací čtení

Vytvořte soubor projektu s následujícími specifikacemi získat maximální vstupně-výstupních operací čtení. Pojmenujte ji "fioread.ini".

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

Mějte na paměti následující základy, které jsou v souladu s pokyny návrhu, které jsou popsané v předchozích částech. Tyto specifikace jsou nezbytné k podpoře maximální IOPS

* Hloubka fronty vysokou 256.  
* Malé velikosti 8 kB.  
* Více vláken provádění náhodných zápisů.

Spusťte následující příkaz a FIO test po dobu 30 sekund, podívejte se na

```
sudo fio --runtime 30 fioread.ini
```

Při spuštění testů se budete moct zobrazit počet přečtených IOPS virtuálního počítače a jsou doručováním disks úrovně Premium. Jak je znázorněno v následující ukázce, virtuální počítač DS14 doručování víc než 64 000 vstupně-výstupních operací čtení. Jedná se o kombinaci disku a výkon mezipaměti.  
    ![](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

#### <a name="maximum-read-and-write-iops"></a>Maximální počet čtení a zápis IOPS

Vytvoření souboru projektu následující specifikace získat maximální kombinaci pro čtení a zápis IOPS. Pojmenujte ji "fioreadwrite.ini".

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

Mějte na paměti následující základy, které jsou v souladu s pokyny návrhu, které jsou popsané v předchozích částech. Tyto specifikace jsou nezbytné k podpoře maximální IOPS

* Hloubka fronty vysokou 128.  
* Malé velikosti 4 kB.  
* Více vláken provádění náhodné čte a zapisuje.

Spusťte následující příkaz a FIO test po dobu 30 sekund, podívejte se na

```
sudo fio --runtime 30 fioreadwrite.ini
```

Při spuštění testů se budete moct zobrazit počet kombinované čtení a zápis IOPS virtuálního počítače a jsou doručováním disks úrovně Premium. Jak je znázorněno v následující ukázce, virtuální počítač DS14 doručování více než 100 000 kombinované čtení a zápis IOPS. Jedná se o kombinaci disku a výkon mezipaměti.  
    ![Kombinované čtení a zápis IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

#### <a name="maximum-combined-throughput"></a>Maximální propustnost kombinované

Chcete-li získat maximální kombinovat pro čtení a zápis propustnost, větší velikosti bloku a hloubka fronty velké pomocí více vláken provádění operací čtení a zápisu. Můžete použít velikost bloku 64 kB a hloubka fronty 128.
