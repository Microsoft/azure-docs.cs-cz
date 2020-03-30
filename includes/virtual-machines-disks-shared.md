---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a14ae76e15c1adb59917e61fbcbdaa34a7efa2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77472008"
---
Sdílené disky (preview) Azure je nová funkce pro spravované disky Azure, která umožňuje připojení spravovaného disku Azure k více virtuálním počítačům (VM) současně. Připojení spravovaného disku k více virtuálním počítačům umožňuje nasadit nové nebo migrovat existující clusterované aplikace do Azure.

## <a name="how-it-works"></a>Jak to funguje

Virtuální počítače v clusteru můžete číst nebo zapisovat na připojený disk na základě rezervace zvolené clusterované aplikace pomocí [SCSI trvalé rezervace](https://www.t10.org/members/w_spc3.htm) (SCSI PR). SCSI PR je známý průmyslový standard, který využívají aplikace spuštěné v místním prostředí sítě SAN (Storage Area Network). Povolení SCSI PR na spravovaném disku umožňuje migrovat tyto aplikace do Azure tak, jak je.

Spravované disky s povolenými sdílenými disky nabízejí sdílené blokové úložiště, ke kterému má přístup více virtuálních počítačů, což je vystaveno jako čísla logických jednotek (LUN). LUN jsou pak prezentovány iniciátoru (VM) z cíle (disku). Tyto luny vypadají jako přímo připojené úložiště (DAS) nebo místní jednotky k virtuálnímu virtuálnímu zařízení.

Spravované disky s povolenými sdílenými disky nativně nenabízejí plně spravovaný systém souborů, ke kterému lze přistupovat pomocí systému SMB/NFS. Budete muset použít správce clusteru, jako je windows server s podporou převzetí služeb při selhání clusteru (WSFC) nebo kardiostimulátoru, který zpracovává komunikaci uzlu clusteru, stejně jako zamykání zápisu.

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

## <a name="persistent-reservation-flow"></a>Trvalý tok rezervace

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