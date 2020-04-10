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
ms.openlocfilehash: c3e5beaef7fcc9d407103834e2040957ff32984c
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008512"
---
Sdílené disky (preview) Azure je nová funkce pro spravované disky Azure, které umožňují připojení spravovaného disku k více virtuálním počítačům (VM) současně. Připojení spravovaného disku k více virtuálním počítačům umožňuje nasadit nové nebo migrovat existující clusterované aplikace do Azure.

## <a name="how-it-works"></a>Jak to funguje

Virtuální počítače v clusteru můžete číst nebo zapisovat na připojený disk na základě rezervace zvolené clusterované aplikace pomocí [SCSI trvalé rezervace](https://www.t10.org/members/w_spc3.htm) (SCSI PR). SCSI PR je oborový standard, který využívají aplikace spuštěné v místním prostředí sítě SAN (Storage Area Network). Povolení SCSI PR na spravovaném disku umožňuje migrovat tyto aplikace do Azure tak, jak je.

Sdílení spravovaných disků nabízí sdílené blokové úložiště, ke kterému lze přistupovat z více virtuálních počítačů, jsou vystaveny jako čísla logických jednotek (LUN). LUN jsou pak prezentovány iniciátoru (VM) z cíle (disku). Tyto luny vypadají jako přímo připojené úložiště (DAS) nebo místní jednotky k virtuálnímu virtuálnímu zařízení.

Sdílené spravované disky nativně nenabízejí plně spravovaný systém souborů, ke kterému lze přistupovat pomocí systému SMB/NFS. Musíte použít správce clusteru, jako je cluster WSFC (Windows Server Failover Cluster) nebo Pacemaker, který zpracovává komunikaci uzlů clusteru a také zamykání zapisování.

## <a name="limitations"></a>Omezení

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Velikosti disků

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Ukázkové úlohy

### <a name="windows"></a>Windows

Většina clustering ů založených na systému Windows na WSFC, který zpracovává všechny základní infrastruktury pro komunikaci uzlů clusteru, což umožňuje aplikacím využívat paralelní přístup vzory. WSFC umožňuje možnosti csv i nezaložené na csv v závislosti na verzi systému Windows Server. Podrobnosti naleznete v části [Vytvoření clusteru s podporou převzetí služeb při selhání](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster).

Některé oblíbené aplikace spuštěné na WSFC patří:

- Instance clusteru s podporou převzetí služeb při selhání serveru SQL Server (FCI)
- Souborový server s horizontálním navýšením kapacity (SoFS)
- Souborový server pro obecné použití (zatížení IW)
- Disk profilu uživatele serveru Vzdálené plochy (RDS UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

Linuxové clustery mohou využívat clusterové manažery, jako je [Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker). Kardiostimulátor staví na [corosync](http://corosync.github.io/corosync/), umožňuje komunikaci clusterů pro aplikace nasazené ve vysoce dostupných prostředích. Některé společné clusterované souborové systémy zahrnují [ocfs2](https://oss.oracle.com/projects/ocfs2/) a [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). Rezervace a registrace můžete manipulovat pomocí nástrojů, jako jsou [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) a [sg_persist](https://linux.die.net/man/8/sg_persist).

## <a name="persistent-reservation-flow"></a>Trvalý tok rezervací

Následující diagram znázorňuje ukázkovou databázovou aplikaci s 2 uzly, která využívá scsi PR k povolení převzetí služeb při selhání z jednoho uzlu do druhého.

![Cluster dvou uzlů. Aplikace spuštěná v clusteru zpracovává přístup k disku.](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

Tok je následující:

1. Clusterovaná aplikace spuštěná na Azure VM1 i VM2 registruje svůj záměr číst nebo zapisovat na disk.
1. Instance aplikace na VM1 pak trvá výhradní rezervace pro zápis na disk.
1. Tato rezervace je vynucena na disku Azure a databáze teď může výhradně zapisovat na disk. Všechny zápisy z instance aplikace na VM2 nebude úspěšné.
1. Pokud instance aplikace na VM1 přejde dolů, instance na VM2 nyní můžete zahájit převzetí služeb při selhání databáze a převzetí disku.
1. Tato rezervace je teď vynucena na disku Azure a disk už nebude přijímat zápisy z VM1. Bude přijímat pouze zápisy z VM2.
1. Clusterovaná aplikace může dokončit převzetí služeb při selhání databáze a obsluhovat požadavky z VM2.

Následující diagram znázorňuje další společné clusterované úlohy skládající se z více uzlů čtení dat z disku pro spuštění paralelních procesů, jako je například školení modelů strojového učení.

![Cluster virtuálních her se čtyřmi uzly, každý uzel registruje záměr k zápisu, aplikace přebírá výhradní rezervaci pro správné zpracování výsledků zápisu](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

Tok je následující:

1. Clusterovaná aplikace spuštěná na všech virtuálních počítačích registruje záměr číst nebo zapisovat na disk.
1. Instance aplikace na VM1 trvá výhradní rezervace pro zápis na disk při otevírání čtení na disk z jiných virtuálních počítačů.
1. Tato rezervace je vynucena na disku Azure.
1. Všechny uzly v clusteru nyní mohou číst z disku. Pouze jeden uzel zapisuje výsledky zpět na disk jménem všech uzlů v clusteru.

### <a name="ultra-disks-reservation-flow"></a>Tok rezervací ultra disků

Ultra disky nabízejí další škrticí klapku, celkem dva škrticí klapky. Z tohoto důvodu může tok rezervací ultra disků fungovat, jak je popsáno v předchozí části, nebo může omezit a distribuovat výkon podrobněji.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text=" ":::

## <a name="ultra-disk-performance-throttles"></a>Omezení výkonu ultra disku

Ultra disky mají jedinečnou schopnost, která vám umožní nastavit výkon tím, že odhalí modifikovatelné atributy a umožní jim je upravit. Ve výchozím nastavení existují pouze dva upravitelné atributy, ale sdílené ultra disky mají dva další atributy.


|Atribut  |Popis  |
|---------|---------|
|DiskIOPSReadWrite     |Celkový počet viops povolený ve všech virtuálních počítačů připojení disku sdílení s přístupem pro zápis.         |
|DiskMBpsReadWrite     |Celková propustnost (MB/s) povolená ve všech virtuálních počítačů připojení sdíleného disku s přístupem pro zápis.         |
|DiskIOPSOnly*     |Celkový počet viops povolený ve všech virtuálních počítačů připojení sdíleného disku jako jen pro čtení.         |
|DiskMBpsReadOnly*     |Celková propustnost (MB/s) povolená ve všech virtuálních počítačů připojení sdíleného disku jako jen pro čtení.         |

\*Platí pouze pro sdílené ultra disky

Následující vzorce vysvětlují, jak lze nastavit atributy výkonu, protože jsou upravitelné uživatelem:

- DiskIOPSReadWrite/DiskIOPSOnly: 
    - Limity IOPS 300 IOPS/GiB, maximálně 160 kB VOPS na disk
    - Minimálně 100 IOPS
    - DiskIOPSReadWrite + DiskIOPSReadOnly je alespoň 2 IOPS/GiB
- DiskMBpsRead Write/DiskMBpsReadOnly:
    - Limit propustnost jednoho disku je 256 KiB/s pro každý zřízený vstupně-sad, maximálně 2000 MB/s na disk
    - Minimální zaručená propustnost na disk je 4KiB/s pro každý zřízený vstupně-sad, s celkovým základním minimem 1 MB/s

### <a name="examples"></a>Příklady

Následující příklady zobrazují několik scénářů, které ukazují, jak omezení může pracovat se sdílenými ultra disky, konkrétně.

#### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>Cluster se dvěma uzly používající sdílené svazky clusteru

Následuje příklad wsfc se 2 uzly používající clusterované sdílené svazky. S touto konfigurací mají oba virtuální počítače simultánní přístup pro zápis na disk, což vede k rozdělení omezení ReadWrite mezi dva virtuální počítače a omezení jen pro čtení, které se nepoužívají.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="CSV dva uzlina ultra příklad":::

:::image-end:::

#### <a name="two-node-cluster-without-cluster-share-volumes"></a>Cluster se dvěma uzly bez svazků sdílené složky clusteru

Následuje příklad wsfc se 2 uzly, který nepoužívá clusterované sdílené svazky. Při této konfiguraci má přístup pro zápis na disk pouze jeden virtuální modul. To má za následek omezení ReadWrite používá výhradně pro primární virtuální hod a jen pro čtení omezení používá pouze sekundární.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="CSV dva uzly žádný csv ultra disk příklad":::

:::image-end:::

#### <a name="four-node-linux-cluster"></a>Cluster Linux se čtyřmi uzly

Následuje příklad clusteru Linux se 4 uzly s jedním zapisovačem a třemi čtečkami s horizontálním navýšením kapacity. Při této konfiguraci má přístup pro zápis na disk pouze jeden virtuální modul. To má za následek omezení ReadWrite se používá výhradně pro primární virtuální hod a omezení Jen pro čtení je rozdělena sekundární virtuálních stránek.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="Příklad ultra omezení čtyř uzlů":::

:::image-end:::