---
title: Optimalizace výkonu databáze MySQL na systému Linux | Microsoft Docs
description: Informace o optimalizaci MySQL spuštěna na virtuálním počítači Azure (VM) s Linuxem.
services: virtual-machines-linux
documentationcenter: ''
author: NingKuang
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 0c1c7fc5-a528-4d84-b65d-2df225f2233f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: ningk
ms.openlocfilehash: 447532452a848c88fd927f42e4263cef4742dd89
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="optimize-mysql-performance-on-azure-linux-vms"></a>Optimalizace výkonu databáze MySQL na virtuálních počítačích Azure Linux
Existuje celá řada faktorů, které ovlivňují výkon databáze MySQL na Azure, jak v výběr virtuální hardwarové a softwarové konfigurace. Tento článek se zaměřuje na optimalizace výkonu úložiště, systému a konfigurace databáze.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Azure Resource Manager](../../../resource-manager-deployment-model.md) a classic. Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Informace o optimalizace virtuálního počítače s Linuxem pomocí modelu Resource Manager najdete v tématu [optimalizovat virtuálním počítačům s Linuxem v Azure](../optimization.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="utilize-raid-on-an-azure-virtual-machine"></a>Využívat RAID na virtuální počítač Azure
Úložiště je klíčovým faktorem, který ovlivňuje výkon databáze v prostředí cloudu. Porovnání na jeden disk, RAID zajistí rychlejší přístup prostřednictvím souběžnosti. Další informace najdete v tématu [standardní RAID úrovně](http://en.wikipedia.org/wiki/Standard_RAID_levels).   

Propustnost vstupu/výstupu disku a vstupně-výstupních operací dobu odezvy v Azure je možné zlepšit prostřednictvím RAID. Naše testy testovacího prostředí zobrazit, může být dvojitá propustnost vstupu/výstupu disku a vstupně-výstupních operací odezvu může snížit půl v průměru při je dvojnásobný počet disků RAID (ze dvou na čtyři, čtyř do osmi atd.). V tématu [příloha A](#AppendixA) podrobnosti.  

Kromě diskových operací zlepšuje výkon MySQL když zvýšíte úroveň pole RAID.  V tématu [příloha B](#AppendixB) podrobnosti.  

Můžete také zvážit velikost bloku. Obecně platí když máte větší velikost bloku, získáte nižší nároky, hlavně pro velké zápisy. Ale pokud velikost bloku je příliš velký, může přidat další režie, které zabraňují využívat výhod RAID. Aktuální výchozí velikost je 512 KB, který je ověřené být optimální pro nejobecnější provozní prostředí. V tématu [příloha C](#AppendixC) podrobnosti.   

Existují omezení na tom, kolik disků můžete přidat pro typy jiný virtuální počítač. Tato omezení jsou podrobně popsané na [velikosti virtuálního počítače a cloudové služby pro Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). I když můžete nastavit RAID s méně disky, budete potřebovat čtyři připojené datových disků RAID příkladu v tomto článku.  

Tento článek předpokládá jste již vytvořili virtuální počítač s Linuxem a MYSQL nainstalován a nakonfigurován. Další informace o Začínáme najdete v části Jak nainstalovat MySQL v Azure.  

### <a name="set-up-raid-on-azure"></a>Nastavení diskového pole RAID na Azure
Následující kroky ukazují, jak vytvořit RAID na platformě Azure pomocí portálu Azure. Můžete také nastavit tak RAID pomocí skriptů prostředí Windows PowerShell.
V tomto příkladu nakonfigurujeme RAID 0 s čtyři disky.  

#### <a name="add-a-data-disk-to-your-virtual-machine"></a>Přidat datový disk k virtuálnímu počítači
Na portálu Azure přejděte do řídicího panelu a vyberte virtuální počítač, do které chcete přidat datový disk. V tomto příkladu je virtuální počítač mysqlnode1.  

<!--![Virtual machines][1]-->

Klikněte na tlačítko **disky** a pak klikněte na **připojit nový**.

![Virtuální počítače přidejte disk](media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-Disks-option.png)

Vytvoření nového disku 500 GB. Ujistěte se, že **předvoleb mezipaměti hostitele** je nastaven na **žádné**.  Až budete hotovi, klikněte na tlačítko **OK**.

![Připojte prázdný disk](media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-attach-empty-disk.png)


Tento postup přidá jeden prázdný disk do virtuálního počítače. Opakujte tento krok tři vícekrát, aby měli čtyři datových disků RAID.  

Přidání jednotky ve virtuálním počítači zobrazíte prohlížení protokolů zpráv jádra. Například je vidět na Ubuntu, použijte následující příkaz:  

    sudo grep SCSI /var/log/dmesg

#### <a name="create-raid-with-the-additional-disks"></a>Vytvoření RAID pomocí dalších disků.
Následující kroky popisují postup [konfigurace softwaru diskového pole RAID v systému Linux](../configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!NOTE]
> Pokud používáte systém souborů XFS, provést následující kroky po vytvoření RAID.
>
>

K instalaci XFS na Debian a Ubuntu a Linux máta, použijte následující příkaz:  

    apt-get -y install xfsprogs  

Nainstalovat XFS Fedora, CentOS nebo RHEL, použijte následující příkaz:  

    yum -y install xfsprogs  xfsdump


#### <a name="set-up-a-new-storage-path"></a>Nastavit novou cestu úložiště
Nastavit nové cesty úložiště, použijte následující příkaz:  

    root@mysqlnode1:~# mkdir -p /RAID0/mysql

#### <a name="copy-the-original-data-to-the-new-storage-path"></a>Zkopírujte původní data na novou cestu úložiště
Ke zkopírování dat na novou cestu úložiště použijte následující příkaz:  

    root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

#### <a name="modify-permissions-so-mysql-can-access-read-and-write-the-data-disk"></a>Upravit oprávnění, můžete přístup MySQL (čtení a zápisu) datový disk
Použijte následující příkaz pro úpravu oprávnění:  

    root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


## <a name="adjust-the-disk-io-scheduling-algorithm"></a>Upravit algoritmus plánování diskové vstupně-výstupních operací
Linux implementuje čtyři typy vstupně-výstupních operací plánování algoritmů:  

* Nedojde k žádné akci algoritmus (ne operace)
* Algoritmus konečného termínu (termín)
* Úplně správného algoritmu front zpráv (CFQ)
* Nároky období algoritmus (Anticipatory)  

Můžete vybrat jiný plánovače vstupně-výstupních operací v různých scénářích za účelem optimalizace výkonu. V prostředí s úplně náhodný přístup není velký rozdíl mezi algoritmy CFQ a konečný termín pro výkon. Doporučujeme nastavit prostředí databáze MySQL na konečný termín pro stabilitu. Pokud existuje mnoho sekvenčních vstupně-výstupních operací, CFQ může snížit výkon vstupně-výstupní operace disku.   

Pro SSD a dalších zařízení nedojde k žádné akci nebo konečný termín můžete dosáhnout lepší výkon než výchozí plánovače.   

Před jádra 2.5 výchozí algoritmus plánování vstupně-výstupních operací je konečný termín. Počínaje jádra 2.6.18, CFQ stala výchozí algoritmus plánování vstupně-výstupní operace.  Můžete určit toto nastavení při spuštění jádra nebo dynamicky upravit toto nastavení při spuštění systému.  

Následující příklad ukazuje, jak zkontrolovat a nastavit výchozí plánovač na řady Debian distribuční algoritmus nedojde k žádné akci.  

### <a name="view-the-current-io-scheduler"></a>Zobrazení aktuálního plánovače vstupně-výstupních operací
Chcete-li zobrazit Plánovač spusťte následující příkaz:  

    root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler

Zobrazí se následující výstup, který označuje aktuálního plánovače:  

    noop [deadline] cfq


### <a name="change-the-current-device-devsda-of-the-io-scheduling-algorithm"></a>Změňte aktuální zařízení (/ dev/sda) plánování algoritmu vstupně-výstupních operací
Spusťte následující příkazy a změňte aktuální zařízení:  

    azureuser@mysqlnode1:~$ sudo su -
    root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mysqlnode1:~# update-grub

> [!NOTE]
> Nastavení to samostatně/dev/sda není užitečné. Je nutné ji nastavit na všech discích data kde je umístěna databáze.  
>
>

Měli byste vidět následující výstup, oznamující, že tento grub.cfg byla znovu sestavena úspěšně a že plánovač výchozí se aktualizovalo a nedojde k žádné akci:  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Pro řadu distribuční Red Hat potřebujete jenom následující příkaz:

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## <a name="configure-system-file-operations-settings"></a>Konfigurace nastavení operace systému souborů
Jeden osvědčeným postupem je zakázat *atime* funkce protokolování v systému souborů. Atime je čas posledního přístupu k souboru. Vždy, když je přístup k souboru, systém souborů zaznamenává časové razítko v protokolu. Tyto informace se ale zřídka používá. Ji můžete vypnout, pokud tomu tak není, které se sníží celkový čas přístup k disku.  

Zakázat atime protokolování, budete muset upravit soubor system configuration soubor/etc / fstab a přidat **noatime** možnost.  

Můžete třeba upravte soubor /etc/fstab vim přidáním noatime, jak znázorňuje následující ukázka:  

    # CLOUD_IMG: This file was created/modified by the Cloud Image build process
    UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
    #Add the “noatime” option below to disable atime logging
    UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
    /dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

Potom se znovu připojte systém souborů pomocí následujícího příkazu:  

    mount -o remount /RAID0

Otestujte upravené výsledek. Když upravíte testovací soubor, čas přístupu se neaktualizuje. Následující příklady ukazují, jak kód vypadá před a po změnách.

Před:        

![Před úpravou přístupu kódu][5]

Po:

![Po změnách přístupu kódu][6]

## <a name="increase-the-maximum-number-of-system-handles-for-high-concurrency"></a>Zvýšit maximální počet popisovačů systému pro vysokou souběžnosti
MySQL je vysoká souběžnosti databáze. Výchozí počet souběžných obslužné rutiny je 1024 pro Linux, což není vždy dostatečná. Pomocí následujících kroků zvýšit maximální souběžných popisovačů systému pro podporu vysoké souběžnosti MySQL.

### <a name="modify-the-limitsconf-file"></a>Upravte soubor limits.conf
Pokud chcete zvýšit maximální povolené souběžných obslužných rutin, přidejte následující čtyři řádky v souboru /etc/security/limits.conf. Všimněte si, že 65536 je maximální počet, který podporuje systém.   

    * logicky nofile 65536
    * pevné nofile 65536
    * soft nproc 65536
    * pevné nproc 65536

### <a name="update-the-system-for-the-new-limits"></a>Aktualizujte systém na nový limity
Chcete-li aktualizovat systém, spusťte následující příkazy:  

    ulimit -SHn 65536
    ulimit -SHu 65536

### <a name="ensure-that-the-limits-are-updated-at-boot-time"></a>Ujistěte se, že omezení jsou aktualizovány při spuštění
Vložte následující příkazy spuštění souboru /etc/rc.local tak projeví při spuštění.  

    echo “ulimit -SHn 65536” >>/etc/rc.local
    echo “ulimit -SHu 65536” >>/etc/rc.local

## <a name="mysql-database-optimization"></a>Optimalizace databáze MySQL
Ke konfiguraci databáze MySQL na Azure, můžete použít stejné strategie optimalizace výkonu, který používáte v místním počítači.  

Hlavní pravidla optimalizace vstupně-výstupní operace jsou:   

* Zvětšete velikost mezipaměti.
* Snížení doby odezvy vstupně-výstupní operace.  

Chcete-li optimalizovat nastavení serveru MySQL, můžete aktualizovat my.cnf souboru, který je výchozí konfigurační soubor pro server a klientských počítačů.  

Hlavní faktory, které ovlivňují výkon MySQL jsou následující položky konfigurace:  

* **innodb_buffer_pool_size**: fondu vyrovnávací paměti obsahuje data ve vyrovnávací paměti a index. To je obvykle nastavena na 70 procent fyzické paměti.
* **innodb_log_file_size**: Toto je velikost protokolu operaci znovu. Opakování protokoly se použít k zajištění, že operace zápisu jsou rychlé, spolehlivé a použitelná pro obnovení po chybě. Je nastavena na 512 MB, který vám poskytne dostatek místa pro protokolování operace zápisu.
* **max_connections**: v některých případech aplikace neukončujte připojení správně. Větší hodnotu získáte víc času recyklace nečinný připojení serveru. Maximální počet připojení je 10 000, ale maximální Doporučená hodnota je 5 000.
* **Innodb_file_per_table**: Toto nastavení povolí nebo zakáže schopnost InnoDB ukládání tabulek v samostatné soubory. Zapněte možnost zajistit, že několik operací pokročilé správy může být použitá efektivně. Z výkonu hlediska může urychlit přenos místo tabulky a optimalizace výkonu správy zbytků. Doporučené nastavení pro tuto možnost je ON.</br></br>
Z databáze MySQL 5.6 výchozí nastavení je ON, takže není vyžadována žádná akce. U starších verzí je ve výchozím nastavení VYPNUTÝ. Nastavení by měl změnit před načtením dat, protože to ovlivňuje pouze nově vytvořené tabulky.
* **innodb_flush_log_at_trx_commit**: výchozí hodnota je 1, spolu s rozsahem nastaven na hodnotu 0 ~ 2. Výchozí hodnota je nejvhodnější možnost pro samostatnou databáze MySQL. Nastavení 2 umožňuje většinu integritu dat a je vhodný pro hlavní server v clusteru MySQL. Nastavení 0 umožňuje ztrátě dat, která může mít vliv na spolehlivost (v některých případech s lepším výkonem) a je vhodný pro podřízený v clusteru MySQL.
* **Innodb_log_buffer_size**: umožňuje vyrovnávací paměť protokolu transakcí spustit bez nutnosti před potvrzení transakce jsou zapsány disku v protokolu. Pokud je binární rozsáhlý objekt nebo textové pole, mezipaměti využijí rychle a aktivuje se často diskové vstupně-výstupní operace. Pokud proměnné stavu Innodb_log_waits není lépe zvětšete velikost vyrovnávací paměti je 0.
* **query_cache_size**: nejlepší možnost je zakázat od samého počátku. Nastavte query_cache_size na hodnotu 0 (Toto je výchozí nastavení v MySQL 5.6) a použít jiné metody pro urychlení dotazů.  

V tématu [Dodatek D](#AppendixD) porovnání před a po optimalizace výkonu.

## <a name="turn-on-the-mysql-slow-query-log-for-analyzing-the-performance-bottleneck"></a>Zapnout protokol pomalé dotazu MySQL pro analýzu kritická místa výkonu
Protokol dotazu pomalé MySQL můžete identifikovat pomalé dotazů pro databázi MySQL. Když povolíte protokol pomalé dotazu MySQL, můžete použít nástroje MySQL jako **mysqldumpslow** identifikovat kritická místa výkonu.  

Ve výchozím nastavení to není povoleno. Zapnutí protokol pomalé dotazu může využívat některé prostředky procesoru. Doporučujeme, abyste povolili to dočasně pro řešení potíží s kritické body. Chcete-li na protokol pomalé dotazu:

1. Upravte soubor my.cnf přidáním následující řádky na konec:

        long_query_time = 2
        slow_query_log = 1
        slow_query_log_file = /RAID0/mysql/mysql-slow.log

2. Restartujte server, MySQL.

        service  mysql  restart

3. Zkontrolujte, zda nastavení trvá vliv pomocí **zobrazit** příkaz.

![ON zpomalit protokol dotazu][7]   

![Výsledky zpomalit protokol dotazu][8]

V tomto příkladu vidíte, že byla zapnuta funkce pomalé dotazu. Pak můžete použít **mysqldumpslow** nástroj zjistit kritická místa výkonu a optimalizace výkonu, jako je například přidávání indexy.

## <a name="appendices"></a>Přílohy
Následuje ukázková výkonu testovací data vytvořeného v cílové testovacím prostředí. Poskytují obecné na trend data výkonu s jinou ladění přístupy výkonu. Výsledky se můžou lišit v rámci různých verzí prostředí nebo produktu.

### <a name="AppendixA"></a>Příloha A  
**Výkon disku (IOPS) s různými úrovněmi diskového pole RAID**

![Disk IOPS s různými úrovněmi diskového pole RAID][9]

**Test příkazy**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

> [!NOTE]
> Zatížení tento test používá 64 vláken, dostat horní limit počtu RAID.
>
>

### <a name="AppendixB"></a>Dodatek B  
**Porovnání výkonu (propustnost) MySQL s různými úrovněmi diskového pole RAID**   
(Systém souborů XFS)

![Porovnání výkonu MySQL s různými úrovněmi diskového pole RAID][10]  
![Porovnání výkonu MySQL s různými úrovněmi diskového pole RAID][11]

**Test příkazy**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**Porovnání výkonu (OLTP) MySQL s různými úrovněmi diskového pole RAID**  
![Porovnání výkonu (OLTP) MySQL s různými úrovněmi diskového pole RAID][12]

**Test příkazy**

    time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

### <a name="AppendixC"></a>Příloha C   
**Porovnání výkonu (IOPS) disku pro různé bloku velikosti**  
(Systém souborů XFS)

![][13]

**Test příkazy**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

Velikosti souborů použít pro toto testování 30 GB 1 GB, v uvedeném pořadí a s RAID 0 (4 disky) XFS systému souborů.

### <a name="AppendixD"></a>Dodatek D  
**Porovnání výkonu (propustnost) MySQL před a po optimalizace**  
(Systém souborů XFS)

![Porovnání výkonu (propustnost) MySQL před a po optimalizace][14]

**Test příkazy**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**Nastavení konfigurace pro výchozí a optimalizace vypadá takto:**

| Parametry | Výchozí | Optimalizace |
| --- | --- | --- |
| **innodb_buffer_pool_size** |Žádný |7 GB |
| **innodb_log_file_size** |5 MB |512 MB |
| **max_connections** |100 |5000 |
| **innodb_file_per_table** |0 |1 |
| **innodb_flush_log_at_trx_commit** |1 |2 |
| **innodb_log_buffer_size** |8 MB |128 MB |
| **query_cache_size** |16 MB |0 |

Další podrobné [parametry konfigurace optimalizace](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html), naleznete [oficiální pokyny MySQL](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method).  

  **Testovací prostředí**  

| Hardware | Podrobnosti |
| --- | --- |
| Procesor |AMD Opteron(tm) procesoru 4171 HE / 4 jádra |
| Memory (Paměť) |14 GB |
| Disk |10 GB místa na disku |
| Operační systém |Ubuntu 14.04.1 LTS |

[1]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-01.png
[2]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-02.png
[3]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-03.png
[4]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-04.png
[5]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-05.png
[6]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-06.png
[7]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-07.png
[8]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-08.png
[9]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-09.png
[10]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-10.png
[11]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-11.png
[12]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-12.png
[13]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-13.png
[14]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-14.png

