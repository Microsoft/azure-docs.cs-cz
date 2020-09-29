---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6f819d9b6ba4d74612da304aafea0118f9094bde
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91451546"
---
Azure Shared disks je nová funkce pro služby Azure Managed disks, která umožňuje současně připojit spravovaný disk k několika virtuálním počítačům (VM). Připojení spravovaného disku k několika virtuálním počítačům vám umožní nasadit do Azure nové nebo migrovat existující clusterové aplikace.

## <a name="how-it-works"></a>Jak to funguje

Virtuální počítače v clusteru můžou číst nebo zapisovat na jejich připojený disk na základě rezervace zvolené clusterovou aplikací pomocí [trvalých rezervací SCSI](https://www.t10.org/members/w_spc3.htm) (PR SCSI). SCSI PR je průmyslový standard, který využívají aplikace běžící v síti SAN (Storage Area Network) místně. Povolení SCSI PR na spravovaném disku vám umožní migrovat tyto aplikace do Azure tak, jak jsou.

Sdílené spravované disky nabízejí sdílené blokové úložiště, ke kterému se dá dostat z víc virtuálních počítačů. tyto služby se zveřejňují jako logická čísla jednotek (LUN). Logické jednotky (LUN) se pak prezentují iniciátoru (virtuálnímu počítači) z cíle (disku). Tyto logické jednotky (LUN) vypadají jako přímo připojené úložiště (DAS) nebo místní disk k virtuálnímu počítači.

Sdílené spravované disky nativně nenabízejí plně spravovaný systém souborů, ke kterému se dá dostat pomocí protokolu SMB/NFS. Musíte použít Správce clusteru, jako je Windows Server failover cluster (WSFC) nebo Pacemaker, který zpracovává komunikaci uzlu clusteru a uzamykání zápisu.

## <a name="limitations"></a>Omezení

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

### <a name="operating-system-requirements"></a>Požadavky na operační systém

Sdílené disky podporují více operačních systémů. Podporované operační systémy najdete v oddílech [Windows](#windows) a [Linux](#linux) .

## <a name="disk-sizes"></a>Velikosti disků

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Ukázkové úlohy

### <a name="windows"></a>Windows

Sdílené disky Azure jsou podporované na Windows serveru 2008 a novějších verzích. Většina sestavení clusteringu založeného na systému Windows ve službě WSFC, která zpracovává veškerou základní infrastrukturu pro komunikaci uzlu clusteru, umožňuje aplikacím využívat vzorce paralelního přístupu. Služba WSFC v závislosti na vaší verzi Windows Serveru umožňuje variantu založenou na sdílených svazcích clusteru i variantu bez nich. Podrobnosti najdete v tématu [Vytvoření clusteru s podporou převzetí služeb při selhání](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster).

Mezi některé oblíbené aplikace využívající službu WSFC patří:

- [Vytvoření FCI se sdílenými disky Azure (SQL Server na virtuálních počítačích Azure)](../articles/azure-sql/virtual-machines/windows/failover-cluster-instance-azure-shared-disks-manually-configure.md)
- Souborový server se škálováním na více instancí (SoFS) [Šablona] (https://aka.ms/azure-shared-disk-sofs-template)
- SAP ASCS/SCS [Šablona] (https://aka.ms/azure-shared-disk-sapacs-template)
- Souborový server pro obecné použití (úloha IW)
- Disk profilu uživatele na serveru vzdálené plochy (RDS UPD)

### <a name="linux"></a>Linux

Sdílené disky Azure jsou podporované na:
- [SUSE SLE for SAP and SUSE SLE HA 15 SP1 a vyšší](https://documentation.suse.com/sle-ha/15-SP1/single-html/SLE-HA-guide/index.html)
- [Ubuntu 18,04 a novější](https://discourse.ubuntu.com/t/ubuntu-high-availability-corosync-pacemaker-shared-disk-environments/14874)
- [RHEL Developer Preview na všech RHEL ve verzi 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_and_managing_high_availability_clusters/index)
- [Oracle Enterprise Linux](https://docs.oracle.com/en/operating-systems/oracle-linux/8/availability/hacluster-1.html)

Clustery se systémem Linux můžou využívat Správce clusterů, jako je [Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker). Pacemaker se vytváří na [Corosync](http://corosync.github.io/corosync/)a povoluje komunikaci clusteru pro aplikace nasazené v prostředích s vysokou dostupností. Mezi běžné Clusterové systémy souborů patří [OCFS2](https://oss.oracle.com/projects/ocfs2/) a [GFS2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). Pro arbitrating přístup k disku můžete použít modely clusteringu založené na trvalé rezervaci (SCSI PR) nebo STONITH Block Device (SBD). Při použití SCSI PR můžete manipulovat s rezervacemi a registrací pomocí nástrojů, jako jsou [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) a [sg_persist](https://linux.die.net/man/8/sg_persist).

## <a name="persistent-reservation-flow"></a>Trvalý tok rezervace

Následující diagram znázorňuje ukázkovou databázovou databázovou aplikaci v jednom uzlu, která využívá rozhraní SCSI PR a umožňuje převzetí služeb při selhání z jednoho uzlu na druhý.

![Cluster se dvěma uzly. Aplikace spuštěná v clusteru zpracovává přístup k disku.](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

Tok je následující:

1. Clusterovaná aplikace spuštěná v Azure VM1 i VM2 registruje svůj záměr na čtení nebo zápis na disk.
1. Instance aplikace v VM1 pak vezme exkluzivní rezervaci pro zápis na disk.
1. Tato rezervace se vynutila na disku Azure a databáze teď může na disk zapisovat výhradně. Jakékoli zápisy z instance aplikace v VM2 nebudou úspěšné.
1. Pokud instance aplikace v VM1 dojde k výpadku, instance v VM2 teď může iniciovat převzetí služeb při selhání databáze a převzít z disku.
1. Tato rezervace se teď vynutila na disku Azure a disk už nebude přijímat zápisy z VM1. Bude přijímat jenom zápisy z VM2.
1. Clusterová aplikace může dokončit převzetí služeb při selhání databáze a obsluhovat požadavky od VM2.

Následující diagram znázorňuje další běžné clusterované úlohy skládající se z několika uzlů, které čtou data z disku pro spouštění paralelních procesů, jako je například školení modelů strojového učení.

![Cluster virtuálních počítačů se čtyřmi uzly, každý uzel registruje záměr pro zápis, aplikace používá výhradní rezervaci pro správné zpracování výsledků zápisu](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

Tok je následující:

1. Clusterovaná aplikace spuštěná na všech virtuálních počítačích registruje záměr pro čtení nebo zápis na disk.
1. Instance aplikace v VM1 přebírá exkluzivní rezervaci pro zápis na disk při otevírání čtení na disk z jiných virtuálních počítačů.
1. Tato rezervace se vynutila na disku Azure.
1. Z disku se teď dají číst všechny uzly v clusteru. Pouze jeden uzel zapisuje zpět výsledky na disk jménem všech uzlů v clusteru.

### <a name="ultra-disks-reservation-flow"></a>Tok rezervací na disky Ultra

Disky Ultra nabízejí dodatečné omezení pro celkový počet dvou omezení. Z tohoto důvodu může tok rezervací Ultra discích fungovat jak je popsáno v předchozí části, nebo může lépe omezit a distribuovat výkon.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text="Obrázek tabulky, která znázorňuje přístup &quot;ReadOnly&quot; nebo &quot;čtení/zápisu&quot; pro rezervovaného držitele, registraci a další.":::

## <a name="performance-throttles"></a>Omezení výkonu

### <a name="premium-ssd-performance-throttles"></a>SSD úrovně Premium omezení výkonu

U jednotky SSD úrovně Premium je pevným počtem vstupně-výstupních operací disku a propustností například IOPS P30 5000. Tato hodnota zůstává bez ohledu na to, jestli se disk sdílí mezi 2 virtuálními počítači nebo 5 virtuálními počítači. Omezení disku můžete dosáhnout z jednoho virtuálního počítače nebo rozdělit na dva nebo více virtuálních počítačů. 

### <a name="ultra-disk-performance-throttles"></a>Omezení výkonu Ultra disk

Disky Ultra mají jedinečnou schopnost nastavit svůj výkon vyplněním upravitelných atributů a tím, že je budete moct upravit. Ve výchozím nastavení jsou k dispozici pouze dva atributy s modifikátorem, ale sdílené disky Ultra mají dva další atributy.


|Atribut  |Popis  |
|---------|---------|
|DiskIOPSReadWrite     |Celkový počet vstupně-výstupních operací povolených ve všech virtuálních počítačích, které namontují sdílený disk s přístupem pro zápis.         |
|DiskMBpsReadWrite     |Celková propustnost (MB/s) povolená napříč všemi virtuálními počítači, které namontují sdílený disk s přístupem pro zápis.         |
|DiskIOPSReadOnly*     |Celkový počet vstupně-výstupních operací povolených ve všech virtuálních počítačích, ve kterých se sdílený disk připojuje `ReadOnly` .         |
|DiskMBpsReadOnly*     |Celková propustnost (MB/s) povolená napříč všemi virtuálními počítači, které sdílí sdílený disk `ReadOnly` .         |

\* Platí jenom pro sdílené disky jen pro Ultra

Následující vzorce vysvětlují, jak lze nastavit atributy výkonu, protože jsou uživatelsky upravitelné:

- DiskIOPSReadWrite/DiskIOPSReadOnly: 
    - Omezení IOPS 300 IOPS/GiB, až do maximálního počtu 160K IOPS na disk
    - Minimálně 100 IOPS
    - DiskIOPSReadWrite + DiskIOPSReadOnly má alespoň 2 IOPS/GiB
- DiskMBpsRead Write/DiskMBpsReadOnly:
    - Pro každý zřízený IOPS je limit propustnosti jednoho disku 256 KiB/s, maximálně 2000 MB/s na disk.
    - Minimální zaručená propustnost na disk je 4KiB/s pro každý zřízený IOPS, přičemž celkové minimální hodnoty jsou 1 MB/s.

#### <a name="examples"></a>Příklady

V následujících příkladech je znázorněno několik scénářů, které ukazují, jak omezování dokáže pracovat se sdílenými disky Ultra, konkrétně.

##### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>Cluster se dvěma uzly pomocí sdílených svazků clusteru

Následuje příklad dvou uzlů služby WSFC pomocí clusterovaných sdílených svazků. V této konfiguraci mají oba virtuální počítače souběžný přístup pro zápis na disk. Výsledkem je, že se `ReadWrite` omezení rozděluje mezi tyto dva virtuální počítače a `ReadOnly` omezení se nepoužívá.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="Obrázek tabulky, která znázorňuje přístup &quot;ReadOnly&quot; nebo &quot;čtení/zápisu&quot; pro rezervovaného držitele, registraci a další.":::

##### <a name="two-node-cluster-without-cluster-share-volumes"></a>Cluster se dvěma uzly bez sdílených svazků clusteru

Níže je uveden příklad služby WSFC se dvěma uzly, který nepoužívá clusterované sdílené svazky. V této konfiguraci má disk přístup pro zápis jenom na jednom virtuálním počítači. Výsledkem je `ReadWrite` omezení používané výhradně pro primární virtuální počítač a omezení, které `ReadOnly` používá sekundární.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="Obrázek tabulky, která znázorňuje přístup &quot;ReadOnly&quot; nebo &quot;čtení/zápisu&quot; pro rezervovaného držitele, registraci a další.":::

##### <a name="four-node-linux-cluster"></a>Cluster se čtyřmi uzly Linux

Následuje příklad clusteru se čtyřmi uzly v systému Linux s jedním zapisovačem a třemi čtecími nástroji pro horizontální navýšení kapacity. V této konfiguraci má disk přístup pro zápis jenom na jednom virtuálním počítači. To vede k tomu, že se `ReadWrite` omezení používá výhradně pro primární virtuální počítač a `ReadOnly` omezuje se jejich rozdělení na sekundární virtuální počítače.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="Obrázek tabulky, která znázorňuje přístup &quot;ReadOnly&quot; nebo &quot;čtení/zápisu&quot; pro rezervovaného držitele, registraci a další.":::

#### <a name="ultra-pricing"></a>Ceny pro Ultra

Ceny za Ultra Share se účtují na základě zřízené kapacity, celkových zřizovacích IOPS (diskIOPSReadWrite + diskIOPSReadOnly) a celkové zajištěné propustnosti MB/s (diskMBpsReadWrite + diskMBpsReadOnly). Pro každé další připojení virtuálního počítače se neúčtují žádné další poplatky. Například Ultra Shared disk s následující konfigurací (diskSizeGB: 1024, DiskIOPSReadWrite: 10000, DiskMBpsReadWrite: 600, DiskIOPSReadOnly: 100, DiskMBpsReadOnly: 1) se účtuje pomocí 1024 GiB, 10100 IOPS a 601 MB/s, bez ohledu na to, jestli je připojený ke dvěma virtuálním počítačům nebo pěti virtuálním počítačům.