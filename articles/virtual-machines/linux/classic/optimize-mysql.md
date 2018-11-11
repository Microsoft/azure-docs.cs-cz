---
title: Optimalizace výkonu MySQL v Linuxu | Dokumentace Microsoftu
description: Zjistěte, jak optimalizovat MySQL běžící na virtuálním počítači Azure (VM) s Linuxem.
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
ms.openlocfilehash: 0ba85e82824bc257869d9801f342bd6dbb0402d2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247445"
---
# <a name="optimize-mysql-performance-on-azure-linux-vms"></a>Optimalizace výkonu MySQL na virtuálních počítačích Azure s Linuxem
Existuje celá řada faktorů, které ovlivňují výkon MySQL v Azure, jak ve výběru virtuální hardware a konfiguraci softwaru. Tento článek se zaměřuje na optimalizace výkonu prostřednictvím úložiště systému a konfigurace databáze.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Azure Resource Manageru](../../../resource-manager-deployment-model.md) a classic. Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Informace o optimalizace virtuálního počítače s Linuxem pomocí modelu Resource Manageru najdete v tématu [optimalizovat virtuální počítač s Linuxem v Azure](../optimization.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="utilize-raid-on-an-azure-virtual-machine"></a>Využití diskového pole RAID na virtuálním počítači Azure
Úložiště je klíčovým faktorem, který ovlivňuje výkon databáze v cloudovém prostředí. Porovnání na jeden disk, RAID poskytuje rychlejší přístup prostřednictvím souběžnosti. Další informace najdete v tématu [úrovně Standard RAID](http://en.wikipedia.org/wiki/Standard_RAID_levels).   

Propustnost disku vstupně-výstupní operace a dobu odezvy vstupně-výstupních operací v Azure je možné zlepšit prostřednictvím RAID. Naše testy testovacího prostředí ukazují, že může být dvojitá propustnost vstupně-výstupních operací disku a vstupně-výstupních operací Doba odezvy lze snížit o polovinu v průměru když je počet disků RAID dvojnásobný (ze dvou čtyři, čtyři až osm atd.). Zobrazit [příloha A](#AppendixA) podrobnosti.  

Kromě disku vstupně-výstupních operací zlepšuje výkonu MySQL když zvýšíte úroveň pole RAID.  Zobrazit [dodatku B](#AppendixB) podrobnosti.  

Můžete také vezměte v úvahu velikost bloku. Obecně platí Pokud máte větší velikost bloku dat, získáte nižší režie, zejména u velkých zápisy. Ale když velikost deduplikačního bloku dat je příliš velká, může přidat další režie, který zabraňuje využití diskového pole RAID. Aktuální výchozí velikost je 512 KB, což je prověřené bude ideální pro většinu produkčních prostředí. Zobrazit [dodatku C](#AppendixC) podrobnosti.   

Existují omezení na tom, kolik disků můžete přidat pro typy jiný virtuální počítač. Tato omezení jsou podrobně popsány v [služby velikosti virtuálního počítače a cloud pro Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx). I když můžete také nastavit RAID s menším počtem disků, budete potřebovat čtyři připojené datové disky v našem příkladu diskového pole RAID v tomto článku.  

Tento článek předpokládá už máte vytvořený virtuální počítač s Linuxem a MYSQL nainstalován a nakonfigurován. Další informace o zahájení práce zjistit, jak nainstalovat MySQL v Azure.  

### <a name="set-up-raid-on-azure"></a>Nastavení pole RAID v Azure
Následující kroky ukazují, jak vytvořit pole RAID v Azure pomocí webu Azure portal. Pomocí skriptů Windows Powershellu můžete také nastavení diskového pole RAID.
V tomto příkladu nakonfigurujeme RAID 0 se čtyři disky.  

#### <a name="add-a-data-disk-to-your-virtual-machine"></a>Přidání datového disku k virtuálnímu počítači
Na webu Azure Portal přejděte na řídicí panel a vyberte virtuální počítač, ke kterému chcete přidat datový disk. V tomto příkladu je virtuální počítač mysqlnode1.  

<!--![Virtual machines][1]-->

Klikněte na tlačítko **disky** a potom klikněte na tlačítko **připojit nový**.

![Přidání disku virtuálního počítače](media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-Disks-option.png)

Vytvoření nového disku 500 GB. Ujistěte se, že **Preference mezipaměti hostitele** je nastavena na **žádný**.  Jakmile budete hotovi, klikněte na tlačítko **OK**.

![Připojte prázdný disk](media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-attach-empty-disk.png)


Tento postup přidá jeden prázdný disk k virtuálnímu počítači. Opakujte tento krok tři víckrát, abyste měli čtyři datové disky RAID.  

Zobrazí se přidání jednotky ve virtuálním počítači pohledem na protokol zpráv jádra. Například zobrazit na Ubuntu, použijte následující příkaz:  

    sudo grep SCSI /var/log/dmesg

#### <a name="create-raid-with-the-additional-disks"></a>Vytvoření RAID s další disky
Následující kroky popisují, jak [konfigurace softwarového pole RAID v Linuxu](../configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!NOTE]
> Pokud používáte systém souborů XFS, proveďte následující kroky po vytvoření RAID.
>
>

K instalaci v systému Debian, Ubuntu nebo Linux Mint XFS, použijte následující příkaz:  

    apt-get -y install xfsprogs  

Chcete-li nainstalovat XFS RHEL, Fedora nebo CentOS, použijte následující příkaz:  

    yum -y install xfsprogs  xfsdump


#### <a name="set-up-a-new-storage-path"></a>Nastavit novou cestu úložiště
Nastavit novou cestu úložiště použijte následující příkaz:  

    root@mysqlnode1:~# mkdir -p /RAID0/mysql

#### <a name="copy-the-original-data-to-the-new-storage-path"></a>Kopírování původní data, která mají novou cestu úložiště
Použijte následující příkaz pro kopírování dat na novou cestu úložiště:  

    root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

#### <a name="modify-permissions-so-mysql-can-access-read-and-write-the-data-disk"></a>Upravit oprávnění, aby měli přístup k MySQL (čtení a zápis) datového disku
Použijte následující příkaz k úpravě oprávnění:  

    root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


## <a name="adjust-the-disk-io-scheduling-algorithm"></a>Upravte plánování algoritmus vstupně-výstupní operace disku
Linux implementuje čtyři typy vstupně-výstupních operací plánování algoritmů:  

* Algoritmus NOOP (ne operace)
* Algoritmus konečného termínu (termínu)
* Zcela veletrh algoritmus řazení do fronty (CFQ)
* Rozpočet období algoritmus (Anticipatory)  

Můžete vybrat jiné vstupně-výstupních operací plánovače v různých scénářích za účelem optimalizace výkonu. V prostředí zcela náhodný přístup není značný rozdíl mezi algoritmy CFQ a konečný termín pro výkon. Doporučujeme nastavit prostředí databáze MySQL do konečného termínu pro zvýšení stability. Pokud dochází k mnoha sekvenčních vstupně-výstupních operací, CFQ může snížit výkon vstupně-výstupní operace disku.   

SSD a další zařízení můžete dosáhnout lepší výkon než výchozí plánovač NOOP nebo konečného termínu.   

Před jádra, 2,5 je výchozí algoritmus plánování vstupně-výstupních operací konečný termín. Od verze jádra 2.6.18, CFQ začal být výchozí algoritmus plánování vstupně-výstupních operací.  Můžete určit tato nastavení v době spuštění jádra nebo dynamicky upravit toto nastavení při spuštění systému.  

Následující příklad ukazuje, jak zkontrolovat a nastavit výchozí plánovač NOOP algoritmus řady distribuce Debian.  

### <a name="view-the-current-io-scheduler"></a>Zobrazit aktuální Plánovač vstupně-výstupních operací
Chcete-li zobrazit Plánovač, spusťte následující příkaz:  

    root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler

Zobrazí se následující výstup, který označuje aktuálního plánovače:  

    noop [deadline] cfq


### <a name="change-the-current-device-devsda-of-the-io-scheduling-algorithm"></a>Změňte aktuální zařízení (/ dev/sda) plánování algoritmů vstupně-výstupních operací
Spusťte následující příkazy, chcete-li změnit aktuální zařízení:  

    azureuser@mysqlnode1:~$ sudo su -
    root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mysqlnode1:~# update-grub

> [!NOTE]
> Nastavení pro/dev/sda samostatně není užitečné. Je nutné ji nastavit na všechny datové disky ve které se nachází v databázi.  
>
>

Byste měli vidět následující výstup, oznamující, že tento grub.cfg byla znovu sestavena úspěšně a že výchozím plánovačem se aktualizovala na NOOP:  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Distribuce řady Red Hat potřebujete pouze následující příkaz:

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## <a name="configure-system-file-operations-settings"></a>Nakonfigurujte nastavení operace systému souborů
Jeden osvědčeným postupem je zakázat *atime* funkce protokolování v systému souborů. Atime, je čas posledního přístupu k souboru. Pokaždé, když přistupuje k souboru, zaznamenává systém souborů časové razítko v protokolu. Tyto informace se však zřídka používají. To můžete zakázat, pokud ho nepotřebujete, které se sníží celkový čas přístupu k disku.  

Zakázat protokolování atime, je potřeba upravit soubor systému konfigurační soubor/etc / fstab a přidejte **noatime** možnost.  

Můžete třeba upravte soubor /etc/fstab vim přidáním noatime, jak je znázorněno v následujícím příkladu:  

    # CLOUD_IMG: This file was created/modified by the Cloud Image build process
    UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
    #Add the “noatime” option below to disable atime logging
    UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
    /dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

Potom připojte systém souborů pomocí následujícího příkazu:  

    mount -o remount /RAID0

Upravené výsledek testu. Když upravíte soubor testu, čas přístupu není aktualizován. Následující příklady ukazují, jak kód vypadá před a po změně.

Před:        

![Před úprava přístupu ke kódu][5]

Po:

![Po úprava přístupu ke kódu][6]

## <a name="increase-the-maximum-number-of-system-handles-for-high-concurrency"></a>Zvýšit maximální počet popisovačů systému pro vysokou souběžnosti
MySQL je vysoká souběžnosti databáze. Výchozí počet souběžných popisovače je 1024 pro Linux, která vždy není dostatečná. Pomocí následujících kroků ke zvýšení maximální souběžných popisovačů systému pro podporu vysoké souběžnosti MySQL.

### <a name="modify-the-limitsconf-file"></a>Upravte soubor limits.conf
Pokud chcete zvýšit maximální povolené souběžných obslužné rutiny, přidejte následující čtyři řádky do souboru /etc/security/limits.conf. Všimněte si, že 65536 je největší číslo, které může systém podporovat.   

    * obnovitelné nofile 65536
    * pevné nofile 65536
    * obnovitelné nproc 65536
    * pevné nproc 65536

### <a name="update-the-system-for-the-new-limits"></a>Aktualizovat systém, hledá nové omezení
Pokud chcete aktualizovat systém, spusťte následující příkazy:  

    ulimit -SHn 65536
    ulimit -SHu 65536

### <a name="ensure-that-the-limits-are-updated-at-boot-time"></a>Ujistěte se, že omezení jsou aktualizovány při spuštění
Vložte následující příkazy po spuštění souboru /etc/rc.local, projeví se při spuštění.  

    echo “ulimit -SHn 65536” >>/etc/rc.local
    echo “ulimit -SHu 65536” >>/etc/rc.local

## <a name="mysql-database-optimization"></a>Optimalizace databáze MySQL
Pokud chcete nakonfigurovat MySQL v Azure, můžete použít stejné strategie optimalizace výkonu, které používáte na místním počítači.  

Hlavní pravidla optimalizace vstupně-výstupní operace jsou:   

* Zvětšete velikost mezipaměti.
* Snížení doby odezvy vstupně-výstupních operací.  

K optimalizaci nastavení serveru MySQL, můžete aktualizovat soubor my.cnf, což je výchozí konfigurační soubor pro server a klientských počítačů.  

Následující položky jsou hlavní faktory ovlivňující výkon MySQL:  

* **innodb_buffer_pool_size**: fond vyrovnávacích pamětí obsahuje data ve vyrovnávací paměti a index. To je obvykle nastavena na 70 procent fyzické paměti.
* **innodb_log_file_size**: Toto je velikost protokolu znovu. Protokoly znovu použít k zajištění, že operace zápisu jsou rychlé, spolehlivé a zotavit po chybě. Je nastavené na 512 MB, což vám dostatek místa pro protokolování operace zápisu.
* **max_connections**: někdy aplikace nezavírejte připojení správně. Větší hodnotu vám poskytne serveru k recyklaci nečinný připojení více času. Maximální počet připojení je 10 000 operací, ale doporučený maximální počet je 5 000.
* **Innodb_file_per_table**: Toto nastavení povolí nebo zakáže možnost InnoDB k ukládání tabulek do samostatných souborů. Zapněte možnost chcete ujistit, že několik operací pokročilou správu mohou být efektivně. Z výkonu hlediska ho urychlit přenos místo tabulky a optimalizovat výkon správu odpad. Doporučené nastavení pro tuto možnost je ON.</br></br>
Z MySQL 5.6 výchozí nastavení je dále, takže není vyžadována žádná akce. U starších verzí je ve výchozím nastavení vypnuto. Nastavení by měl změnit před načtením dat, protože se vztahuje pouze nově vytvořené tabulky.
* **innodb_flush_log_at_trx_commit**: výchozí hodnota je 1, kde obor nastaven na hodnotu 0 ~ 2. Výchozí hodnota je nejvhodnější volba pro samostatné databáze MySQL. Nastavení 2 umožňuje většina integritu dat a je vhodný pro hlavní server v clusteru MySQL. Nastavení 0 umožňuje ztráty dat, což může ovlivnit spolehlivost (v některých případech s lepším výkonem) a je vhodný pro podřízený server v clusteru MySQL.
* **Innodb_log_buffer_size**: protokolu vyrovnávací paměti umožňuje spustit bez nutnosti před potvrzení transakce jsou zapsány disku v protokolu transakcí. Pokud je binární rozsáhlý objekt nebo textové pole, do mezipaměti bude rychle spotřebovávat a časté diskové vstupně-výstupní operace se aktivuje. Je lepší zvětšete velikost vyrovnávací paměti proměnné stavu Innodb_log_waits není-li 0.
* **query_cache_size**: nejlepší možností je pro jeho zakázání od samého počátku. Query_cache_size nastavena na hodnotu 0 (to je výchozí nastavení v MySQL 5.6) a použít jiné metody ke zrychlení dotazů.  

Zobrazit [Dodatek D](#AppendixD) porovnání před a po optimalizaci výkonu.

## <a name="turn-on-the-mysql-slow-query-log-for-analyzing-the-performance-bottleneck"></a>Zapnout protokol pomalých dotazů MySQL pro analýzu výkonu kritickým bodem
Protokol pomalých dotazů MySQL můžete identifikovat pomalých dotazů MySQL. Jakmile povolíte protokol pomalých dotazů MySQL, můžete použít nástroje MySQL jako **mysqldumpslow** k identifikaci snížení výkonu.  

Ve výchozím nastavení to není povoleno. Zapnutí protokolu pomalých dotazů může využívat některé prostředky procesoru. Doporučujeme, abyste povolili to dočasně pro řešení potíží s problémových míst výkonu. Chcete-li na protokol pomalého dotazu:

1. Upravte soubor my.cnf tak, že přidáte následující řádky na konec:

        long_query_time = 2
        slow_query_log = 1
        slow_query_log_file = /RAID0/mysql/mysql-slow.log

2. Restartujte MySQL server.

        service  mysql  restart

3. Zkontrolujte, zda nastavení se uplatní, s použitím **zobrazit** příkazu.

![DÁLE zpomalit protokol dotazů][7]   

![Zpomalit protokolu dotazu výsledky][8]

V tomto příkladu vidíte, že je zapnutý funkci pomalých dotazů. Pak můžete použít **mysqldumpslow** nástroj odhalit kritická místa výkonu a optimalizovat výkon, jako je například přidávání indexy.

## <a name="appendices"></a>Přílohy
Tady jsou ukázkové výkonu testovací data vytvořená v cílovém testovacím prostředí. Poskytují obecné na trend dat výkonu různých postupů pro optimalizaci výkonu. Výsledky můžou lišit podle různých verzí prostředí nebo produktu.

### <a name="AppendixA"></a>Příloha A  
**Výkon disku (IOPS) s různými úrovněmi diskového pole RAID**

![VSTUPNĚ-výstupních diskových s různými úrovněmi diskového pole RAID][9]

**Příkazy testu**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

> [!NOTE]
> Zatížení tohoto testu používá 64 vlákna pokusu dosahovat horního omezení RAID.
>
>

### <a name="AppendixB"></a>Dodatek B  
**Porovnání výkonu (propustnost) MySQL s různými úrovněmi diskového pole RAID**   
(Systém souborů XFS)

![Porovnání výkonu MySQL s různými úrovněmi diskového pole RAID][10]  
![Porovnání výkonu MySQL s různými úrovněmi diskového pole RAID][11]

**Příkazy testu**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**Porovnání výkonu (OLTP) MySQL s různými úrovněmi diskového pole RAID**  
![Porovnání výkonu (OLTP) MySQL s různými úrovněmi diskového pole RAID][12]

**Příkazy testu**

    time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

### <a name="AppendixC"></a>Dodatek C   
**Porovnání disků výkon (IOPS) pro různé bloků velikosti**  
(Systém souborů XFS)

![][13]

**Příkazy testu**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

Velikosti souborů pro tento test se 30 GB a 1 GB, respektive, RAID 0 (4 disky) XFS systém souborů.

### <a name="AppendixD"></a>Příloha D  
**Porovnání výkonu (propustnost) MySQL před a po optimalizaci**  
(Systém souborů XFS)

![Porovnání výkonu (propustnost) MySQL před a po optimalizaci][14]

**Příkazy testu**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**Konfigurace nastavení pro výchozí a optimalizace vypadá takto:**

| Parametry | Výchozí | Optimalizace |
| --- | --- | --- |
| **innodb_buffer_pool_size** |Žádný |7 GB |
| **innodb_log_file_size** |5 MB. |512 MB |
| **max_connections** |100 |5000 |
| **innodb_file_per_table** |0 |1 |
| **innodb_flush_log_at_trx_commit** |1 |2 |
| **innodb_log_buffer_size** |8 MB |128 MB |
| **query_cache_size** |16 MB |0 |

Pro podrobnější [parametry konfigurace optimalizace](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html), odkazovat [oficiální pokyny MySQL](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method).  

  **Testovací prostředí**  

| Hardware | Podrobnosti |
| --- | --- |
| Procesor |4171 procesor AMD Opteron(tm) HE / 4 jádra |
| Memory (Paměť) |14 GB |
| Disk |10 GB/disk |
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

