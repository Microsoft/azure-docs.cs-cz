---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6e7294f10ba094a1adaae399187fb9973397a561
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/26/2020
ms.locfileid: "83868098"
---
Sdílené disky Azure (Preview) je nová funkce pro služby Azure Managed disks, která umožňuje připojení spravovaného disku k několika virtuálním počítačům současně. Připojení spravovaného disku k několika virtuálním počítačům vám umožní nasadit do Azure nové nebo migrovat existující clusterové aplikace.

## <a name="how-it-works"></a>Jak to funguje

Virtuální počítače v clusteru můžou číst nebo zapisovat do připojeného disku na základě rezervace, kterou vybrala Clusterová aplikace, pomocí [trvalých rezervací SCSI](https://www.t10.org/members/w_spc3.htm) (SCSI PR). SCSI PR je průmyslový standard, který využívají aplikace běžící v síti SAN (Storage Area Network) místně. Povolení SCSI PR na spravovaném disku vám umožní migrovat tyto aplikace do Azure tak, jak jsou.

Sdílení spravovaných disků nabízí sdílené blokové úložiště, které je přístupné z více virtuálních počítačů. tyto služby se zveřejňují jako logická čísla jednotek (LUN). Logické jednotky (LUN) se pak prezentují iniciátoru (virtuálnímu počítači) z cíle (disku). Tyto logické jednotky (LUN) vypadají jako přímo připojené úložiště (DAS) nebo místní disk k virtuálnímu počítači.

Sdílené spravované disky nativně nenabízejí plně spravovaný systém souborů, ke kterému se dá dostat pomocí protokolu SMB/NFS. Musíte použít Správce clusterů, jako je cluster Windows Server failover cluster (WSFC) nebo Pacemaker, který zpracovává komunikaci uzlu clusteru i uzamykání zápisu.

## <a name="limitations"></a>Omezení

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Velikosti disků

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Ukázkové úlohy

### <a name="windows"></a>Windows

Většina clusterů založených na Windows využívá službu WSFC, která zajišťuje veškerou základní infrastrukturu pro komunikaci mezi uzly clusteru a umožňuje aplikacím využívat vzory paralelního přístupu. Služba WSFC v závislosti na vaší verzi Windows Serveru umožňuje variantu založenou na sdílených svazcích clusteru i variantu bez nich. Podrobnosti najdete v tématu [Vytvoření clusteru s podporou převzetí služeb při selhání](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster).

Mezi některé oblíbené aplikace využívající službu WSFC patří:

- Instance clusteru s podporou převzetí služeb při selhání (FCI) SQL Serveru
- Souborový server se škálováním na více systémů (SoFS)
- Souborový server pro obecné použití (úloha IW)
- Disk profilu uživatele na serveru vzdálené plochy (RDS UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

Clustery se systémem Linux můžou využívat Správce clusterů, jako je [Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker). Pacemaker se vytváří na [Corosync](http://corosync.github.io/corosync/)a povoluje komunikaci clusteru pro aplikace nasazené v prostředích s vysokou dostupností. Mezi běžné Clusterové systémy souborů patří [OCFS2](https://oss.oracle.com/projects/ocfs2/) a [GFS2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). Pomocí nástrojů, jako jsou [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) a [sg_persist](https://linux.die.net/man/8/sg_persist), můžete manipulovat s rezervacemi a registrací.

#### <a name="ubuntu"></a>Ubuntu

Informace o tom, jak nastavit vysokou dostupnost Ubuntu pomocí Corosync a Pacemaker na sdílených discích Azure, najdete v tématu věnovaném [Ubuntu komunitě](https://discourse.ubuntu.com/t/ubuntu-high-availability-corosync-pacemaker-shared-disk-environments/14874).

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

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text="Obrázek tabulky, která znázorňuje přístup jen pro čtení nebo čtení/zápis pro rezervovaného držitele, registraci a další.":::

## <a name="ultra-disk-performance-throttles"></a>Omezení výkonu Ultra disk

Disky Ultra mají jedinečnou schopnost nastavit svůj výkon vyplněním upravitelných atributů a tím, že je budete moct upravit. Ve výchozím nastavení jsou k dispozici pouze dva atributy s modifikátorem, ale sdílené disky Ultra mají dva další atributy.


|Atribut  |Popis  |
|---------|---------|
|DiskIOPSReadWrite     |Celkový počet vstupně-výstupních operací povolených ve všech virtuálních počítačích, které namontují sdílený disk s přístupem pro zápis.         |
|DiskMBpsReadWrite     |Celková propustnost (MB/s) povolená napříč všemi virtuálními počítači, které namontují sdílený disk s přístupem pro zápis.         |
|DiskIOPSReadOnly*     |Celkový počet vstupně-výstupních operací povolených napříč všemi virtuálními počítači, které sdílí sdílený disk jako jen pro čtení.         |
|DiskMBpsReadOnly*     |Celková propustnost (MB/s) povolená napříč všemi virtuálními počítači, které sdílený disk namontují jako jen pro čtení.         |

\*Platí jenom pro sdílené disky jen pro Ultra

Následující vzorce vysvětlují, jak lze nastavit atributy výkonu, protože jsou uživatelsky upravitelné:

- DiskIOPSReadWrite/DiskIOPSReadOnly: 
    - Omezení IOPS 300 IOPS/GiB, až do maximálního počtu 160K IOPS na disk
    - Minimálně 100 IOPS
    - DiskIOPSReadWrite + DiskIOPSReadOnly má alespoň 2 IOPS/GiB
- DiskMBpsRead Write/DiskMBpsReadOnly:
    - Pro každý zřízený IOPS je limit propustnosti jednoho disku 256 KiB/s, maximálně 2000 MB/s na disk.
    - Minimální zaručená propustnost na disk je 4KiB/s pro každý zřízený IOPS, přičemž celkové minimální hodnoty jsou 1 MB/s.

### <a name="examples"></a>Příklady

V následujících příkladech je znázorněno několik scénářů, které ukazují, jak omezování dokáže pracovat se sdílenými disky Ultra, konkrétně.

#### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>Cluster se dvěma uzly pomocí sdílených svazků clusteru

Následuje příklad dvou uzlů služby WSFC pomocí clusterovaných sdílených svazků. V této konfiguraci mají oba virtuální počítače souběžný přístup pro zápis na disk, což vede k rozdělení omezení pro čtení do dvou virtuálních počítačů a omezení jen pro čtení, které se nepoužívá.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="Ultra example v CSV – dva uzly":::

#### <a name="two-node-cluster-without-cluster-share-volumes"></a>Cluster se dvěma uzly bez sdílených svazků clusteru

Níže je uveden příklad služby WSFC se dvěma uzly, který nepoužívá clusterované sdílené svazky. V této konfiguraci má disk přístup pro zápis jenom na jednom virtuálním počítači. Výsledkem je omezení pro čtení a použití výhradně pro primární virtuální počítač a omezení jen pro čtení, které používá sekundární.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="Sdílený svazek clusteru – dva uzly žádný příklad CSV Ultra disk":::

#### <a name="four-node-linux-cluster"></a>Cluster se čtyřmi uzly Linux

Následuje příklad clusteru se čtyřmi uzly v systému Linux s jedním zapisovačem a třemi čtecími nástroji pro horizontální navýšení kapacity. V této konfiguraci má disk přístup pro zápis jenom na jednom virtuálním počítači. To vede k tomu, že se omezení pro čtení a čtení používá výhradně pro primární virtuální počítač a omezení jen pro čtení, které jsou rozdělené do sekundárních virtuálních počítačů.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="Příklad omezení míry Ultra na čtyři uzly":::
