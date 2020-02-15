---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 09e5a6c5eee21e5432c4cf96a63fd2337307954a
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211377"
---
Azure Shared Disks (Preview) je nová funkce pro Azure Managed disks, která umožňuje připojení spravovaného disku Azure k několika virtuálním počítačům současně. Připojení spravovaného disku k několika virtuálním počítačům vám umožní nasadit do Azure nové nebo migrovat existující clusterové aplikace.

## <a name="how-it-works"></a>Jak to funguje

Virtuální počítače v clusteru můžou číst nebo zapisovat do připojeného disku na základě rezervace, kterou vybrala Clusterová aplikace, pomocí [trvalých rezervací SCSI](https://www.t10.org/members/w_spc3.htm) (SCSI PR). SCSI PR je známý oborový standard, který využívají aplikace běžící v síti SAN (Storage Area Network) místně. Povolení SCSI PR na spravovaném disku vám umožní migrovat tyto aplikace do Azure tak, jak jsou.

Služba Managed disks s povolenými sdílenými disky nabízí sdílené blokové úložiště, ke kterému může mít přístup více virtuálních počítačů, což se zveřejňuje jako logická čísla jednotek (LUN). Logické jednotky (LUN) se pak prezentují iniciátoru (virtuálnímu počítači) z cíle (disku). Tyto logické jednotky (LUN) vypadají jako přímo připojené úložiště (DAS) nebo místní disk k virtuálnímu počítači.

Spravované disky se zapnutými sdílenými disky nejsou nativně nabízeny plně spravovaným systémem souborů, ke kterému lze přistupovat pomocí protokolu SMB/NFS. Budete muset použít Správce clusterů, jako je cluster Windows Server failover cluster (WSFC) nebo Pacemaker, který zpracovává komunikaci uzlu clusteru i uzamykání zápisu.

## <a name="limitations"></a>Omezení

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Velikosti disků

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Ukázkové úlohy

### <a name="windows"></a>Windows

Většina sestavení clusteringu založeného na systému Windows ve službě WSFC, která zpracovává veškerou základní infrastrukturu pro komunikaci uzlu clusteru, umožňuje vašim aplikacím využívat vzorce paralelního přístupu. WSFC povoluje v závislosti na vaší verzi Windows serveru i možnosti, které nejsou založené na CSV. Podrobnosti najdete v tématu [Vytvoření clusteru s podporou převzetí služeb při selhání](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster).

Mezi oblíbené aplikace běžící v WSFC patří:

- SQL Server instancí clusteru s podporou převzetí služeb při selhání (FCI)
- Souborový server se škálováním na více instancí (SoFS)
- Souborový server pro obecné použití (IW úlohy)
- Disk profilu uživatele serveru vzdálené plochy (RDS UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

Clustery se systémem Linux můžou využívat Správce clusterů, jako je [Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker). Pacemaker se vytváří na [Corosync](http://corosync.github.io/corosync/)a povoluje komunikaci clusteru pro aplikace nasazené v prostředích s vysokou dostupností. Mezi běžné Clusterové systémy souborů patří [OCFS2](https://oss.oracle.com/projects/ocfs2/) a [GFS2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). Pomocí nástrojů, jako jsou [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) a [sg_persist](https://linux.die.net/man/8/sg_persist), můžete manipulovat s rezervacemi a registrací.

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