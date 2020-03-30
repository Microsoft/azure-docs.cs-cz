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
ms.openlocfilehash: e5148ff9e92a2e550a3117356a4e77cbac8fc6f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67673282"
---
*Zahřívání mezipaměti*  
Disk s mezipamětí hostitele ReadOnly jsou schopni poskytnout vyšší viops než limit disku. Chcete-li získat tento maximální výkon čtení z mezipaměti hostitele, musíte nejprve zahřívat mezipaměť tohoto disku. Tím je zajištěno, že read iOs, že srovnávací nástroj bude řídit na cachereads svazku, ve skutečnosti hity cache a nikoli disk přímo. Přístupy do mezipaměti mají za následek další viopy z disku s povolenou jednou mezipamětí.

> [!IMPORTANT]
> Před spuštěním srovnávacího testu je nutné zahřívat mezipaměť při každém restartování virtuálního počítače.

## <a name="tools"></a>Nástroje

### <a name="iometer"></a>Iometr

[Stáhněte si nástroj Iometer](https://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) na virtuálním počítači.

#### <a name="test-file"></a>Testovací soubor

Iometer používá testovací soubor, který je uložen na svazku, na kterém spustíte srovnávací test. Řídí čtení a zápisy na tento testovací soubor k měření vstupně-sad vstupně-up disku a propustnosti. Iometer vytvoří tento testovací soubor, pokud jste jej neposkytli. Vytvořte testovací soubor o velikosti 200 GB s názvem iobw.tst na svazcích CacheReads a NoCacheWrites.

#### <a name="access-specifications"></a>Specifikace přístupu

Specifikace, velikost požadavku vstupně-amp; vstupně-to, % čtení/zápis, % náhodné/sekvenční jsou konfigurovány pomocí karty "Přístupové specifikace" v iometru. Vytvořte specifikaci přístupu pro každý z níže popsaných scénářů. Vytvořte specifikace přístupu a "Uložit" s příslušným\_názvem jako\_- RandomWrites 8K, RandomReads 8K. Při spuštění testovacího scénáře vyberte odpovídající specifikaci.

Příklad specifikací přístupu pro scénář maximálního zápisu IOPS je uveden níže,  
    ![Příklad specifikací přístupu pro maximální zápis viops](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

#### <a name="maximum-iops-test-specifications"></a>Maximální specifikace testu VOPS

Chcete-li prokázat maximální iOP, použijte menší velikost požadavku. Použijte velikost požadavku 8 K a vytvořte specifikace pro náhodné zápisy a čtení.

| Specifikace přístupu | Velikost požadavku | Náhodné % | Číst % |
| --- | --- | --- | --- |
| RandomWrites\_8K |8 tis. |100 |0 |
| RandomReads\_8K |8 tis. |100 |100 |

#### <a name="maximum-throughput-test-specifications"></a>Specifikace testu maximální propustností

Chcete-li prokázat maximální propustnost, použijte větší velikost požadavku. Použijte velikost požadavku 64 K a vytvořte specifikace pro náhodné zápisy a čtení.

| Specifikace přístupu | Velikost požadavku | Náhodné % | Číst % |
| --- | --- | --- | --- |
| RandomWrites\_64K |64 K |100 |0 |
| RandomReads\_64K |64 K |100 |100 |

#### <a name="run-the-iometer-test"></a>Spuštění testu iometru

Provedení následujících kroků pro zahřátí mezipaměti

1. Vytvořte dvě specifikace přístupu s níže uvedenými hodnotami,

   | Name (Název) | Velikost požadavku | Náhodné % | Číst % |
   | --- | --- | --- | --- |
   | RandomWrites\_1MB |1 MB |100 |0 |
   | RandomReads\_1 MB |1 MB |100 |100 |
1. Spusťte test iometru pro inicializaci disku mezipaměti s následujícími parametry. Pro cílový svazek použijte tři pracovní podprocesy a hloubku fronty 128. Nastavte dobu běhu testu na 2 hodiny na kartě "Testovací nastavení".

   | Scénář | Cílový svazek | Name (Název) | Doba trvání |
   | --- | --- | --- | --- |
   | Inicializovat disk mezipaměti |Čtení z mezipaměti |RandomWrites\_1MB |2 hod. |
1. Spusťte test iometru pro zahřívání disku mezipaměti s následujícími parametry. Pro cílový svazek použijte tři pracovní podprocesy a hloubku fronty 128. Nastavte dobu běhu testu na 2 hodiny na kartě "Testovací nastavení".

   | Scénář | Cílový svazek | Name (Název) | Doba trvání |
   | --- | --- | --- | --- |
   | Zahřívání disku mezipaměti |Čtení z mezipaměti |RandomReads\_1 MB |2 hod. |

Po zahřátí disku mezipaměti pokračujte v níže uvedených testovacích scénářích. Chcete-li spustit test iometru, použijte alespoň tři pracovní podprocesy pro **každý** cílový svazek. Pro každý pracovní podproces vyberte cílový svazek, nastavte hloubku fronty a vyberte jednu z uložených testovacích specifikací, jak je znázorněno v následující tabulce, a spusťte odpovídající testovací scénář. Tabulka také ukazuje očekávané výsledky pro IOPS a propustnost při spuštění těchto testů. Pro všechny scénáře se používá malá velikost vi 8 kB a vysoká hloubka fronty 128.

| Testovací scénář | Cílový svazek | Name (Název) | Výsledek |
| --- | --- | --- | --- |
| Max. Čtení vops |Čtení z mezipaměti |RandomWrites\_8K |50 000 IOPS |
| Max. Zápis vops |NoCacheWrites |RandomReads\_8K |64 000 IOPS |
| Max. Kombinované IOPS |Čtení z mezipaměti |RandomWrites\_8K |100 000 IOPS |
| NoCacheWrites |RandomReads\_8K | &nbsp; | &nbsp; |
| Max. Čtení MB/s |Čtení z mezipaměti |RandomWrites\_64K |524 MB/s |
| Max. Zápis MB/s |NoCacheWrites |RandomReads\_64K |524 MB/s |
| Kombinovaná MB/s |Čtení z mezipaměti |RandomWrites\_64K |1000 MB/s |
| NoCacheWrites |RandomReads\_64K | &nbsp; | &nbsp; |

Níže jsou screenshoty z výsledků testů Iometer pro kombinované IOPS a propustnost scénáře.

#### <a name="combined-reads-and-writes-maximum-iops"></a>Kombinované čtení a zápisy maximální vstupně-operace

![Kombinované čtení a zápisy maximální vstupně-operace](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

#### <a name="combined-reads-and-writes-maximum-throughput"></a>Kombinovaná maximální propustnost čtení a zápisů

![Maximální propustnost kombinovaných čtení a zápisů](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO

FIO je populární nástroj pro benchmark úložiště na virtuálních počítačích SIP. Má flexibilitu pro výběr různých velikostí vstupně-up, sekvenční nebo náhodné čtení a zápisy. Spouští pracovní vlákna nebo procesy k provedení zadaných vstupně-va/o operací. Můžete určit typ vstupně-va/O operací, které musí každý pracovní podproces provádět pomocí souborů úloh. Vytvořili jsme jeden soubor úloh y pro každý scénář znázorněný v níže uvedených příkladech. Můžete změnit specifikace v těchto souborech úloh a opomíjet různé úlohy spuštěné v úložišti Premium. V příkladech používáme standardní virtuální ms DS 14 se systémem **Ubuntu**. Použijte stejné nastavení popsané na začátku sekce Benchmarking a před spuštěním srovnávacích testů zahřívat mezipaměť.

Než začnete, [stáhněte si FIO](https://github.com/axboe/fio) a nainstalujte jej do virtuálního počítače.

Spusťte následující příkaz pro Ubuntu,

```
apt-get install fio
```

Používáme čtyři pracovní podprocesy pro řízení operace zápisu a čtyři pracovní podprocesy pro řízení operací čtení na discích. Pracovníci zápisu řídí provoz na svazku "nocache", který má 10 disků s mezipamětí nastavenou na "Žádný". Pracovníci čtení řídí provoz na svazku "readcache", který má jeden disk s mezipamětí nastavenou na "ReadOnly".

#### <a name="maximum-write-iops"></a>Maximální vipops zápisu

Vytvořte soubor úlohy s následujícími specifikacemi, abyste získali maximální zápis viops. Pojmenujte jej "fiowrite.ini".

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

Všimněte si, postupujte podle klíčových věcí, které jsou v souladu s pokyny pro návrh popsané v předchozích částech. Tyto specifikace jsou nezbytné pro řízení maximálních  

* Vysoká hloubka fronty 256.  
* Malý blok o velikosti 8 KB.  
* Více vláken provádějících náhodné zápisy.

Spusťte následující příkaz, abyste zahájili test FIO po dobu 30 sekund,  

```
sudo fio --runtime 30 fiowrite.ini
```

Při spuštění testu můžete zobrazit počet zápisu VOPS, které disky virtuálního počítače a premium doručují. Jak je znázorněno v následující ukázce, virtuální ms DS14 poskytuje maximální limit zápisu viops 50 000 VOPS.  
    ![Počet disků VM zápisu AOPS a Premium doručují](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

#### <a name="maximum-read-iops"></a>Maximální čtení VOPS

Vytvořte soubor úlohy s následujícími specifikacemi, abyste získali maximální počet čtení vzpona. Pojmenujte to "fioread.ini".

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

Všimněte si, postupujte podle klíčových věcí, které jsou v souladu s pokyny pro návrh popsané v předchozích částech. Tyto specifikace jsou nezbytné pro řízení maximálních

* Vysoká hloubka fronty 256.  
* Malý blok o velikosti 8 KB.  
* Více vláken provádějících náhodné zápisy.

Spusťte následující příkaz, abyste zahájili test FIO po dobu 30 sekund,

```
sudo fio --runtime 30 fioread.ini
```

Při spuštění testu můžete zobrazit počet čtení VOPS virtuálního počítače a premium disky jsou doručována. Jak je znázorněno na ukázce níže, virtuální ms DS14 dodává více než 64 000 viponových operací pro čtení. Jedná se o kombinaci disku a výkonu mezipaměti.  
    ![](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

#### <a name="maximum-read-and-write-iops"></a>Maximální čtení a zápis viop

Vytvořte soubor úlohy s následujícími specifikacemi, abyste získali maximální kombinované čtení a zápis viopů. Pojmenujte jej "fioreadwrite.ini".

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

Všimněte si, postupujte podle klíčových věcí, které jsou v souladu s pokyny pro návrh popsané v předchozích částech. Tyto specifikace jsou nezbytné pro řízení maximálních

* Vysoká hloubka fronty 128.  
* Malý blok o velikosti 4 KB.  
* Více vláken provádějících náhodné čtení a zápisy.

Spusťte následující příkaz, abyste zahájili test FIO po dobu 30 sekund,

```
sudo fio --runtime 30 fioreadwrite.ini
```

Při spuštění testu můžete zobrazit počet kombinovaných čtení a zápisu viop, které virtuální počítače a premium disky doručují. Jak je znázorněno na ukázce níže, virtuální ms DS14 dodává více než 100 000 kombinovaných čtení a zápisu viops. Jedná se o kombinaci disku a výkonu mezipaměti.  
    ![Kombinované čtení a zápis VOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

#### <a name="maximum-combined-throughput"></a>Maximální kombinovaná propustnost

Chcete-li získat maximální kombinovanou propustnost pro čtení a zápisu, použijte větší velikost bloku a velkou hloubku fronty s více vlákny provádějícími čtení a zápisy. Můžete použít velikost bloku 64 KB a hloubku fronty 128.
