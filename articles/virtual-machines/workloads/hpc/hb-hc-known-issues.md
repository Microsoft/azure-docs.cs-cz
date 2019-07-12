---
title: Známé problémy s HB-series a virtuální počítače řady hybridní připojení - Azure Virtual Machines | Dokumentace Microsoftu
description: Přečtěte si o známých problémech s velikostí HB-series virtuálních počítačů v Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 8d4b57fb2fee3849e102868c86fe3cab465fc70d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707780"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>Známé problémy s virtuálními počítači řady HB a HC

Tento článek obsahuje nejčastější problémy a řešení při použití HB-series a virtuální počítače řady hybridní připojení.

## <a name="dram-on-hb-series"></a>DRAM na HB-series

Virtuální počítače řady HB mohou vystavit pouze 228 GB paměti RAM pro virtuální počítače hostované v tuto chvíli. Příčinou je známé omezení Azure hypervisor zabránit stránek přidělení místní paměti DRAM z AMD CCX společnosti (NUMA domény) vyhrazené pro hosta virtuálního počítače.

## <a name="accelerated-networking"></a>Akcelerované síťové služby

Akcelerované síťové služby Azure není v tuto chvíli povoleno, ale bude jako jsme průběhu období Preview. Když tuto funkci podporuje budeme informovat zákazníky.

## <a name="qp0-access-restriction"></a>qp0 Access Restriction

Aby se zabránilo hardwaru nižší úrovně přístupu, který může vést k ohrožení zabezpečení, dvojice front není přístupná virtuálních počítačů hosta 0. To ovlivňuje pouze akce obvykle spojené se správou síťové karty ConnectX-5 a spouštění diagnostiky některé InfiniBand jako ibdiagnet, ale ne koncový uživatel aplikace sami.

## <a name="ud-transport"></a>UD přenosu

Při spuštění nepodporují řady hybridní připojení a HB dynamicky připojený přenos (DCT). Podpora pro DCT budou prováděny v čase. Jsou podporovány spolehlivé přenosy tohoto připojení (RC) a Datagram nespolehlivých (UD).

## <a name="gss-proxy"></a>GSS Proxy

GSS Proxy má známého problému v 7.5 CentOS/RHEL, který může manifestovat jako výkonu a snížení rychlosti odezvy při použití s systému souborů NFS. To můžete řešit s využitím:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Čištění mezipaměti

V systémech prostředí HPC často je užitečné pro vyčištění paměti po dokončení úlohy před dalšího uživatele je přiřazen stejný uzel. Po spuštění aplikace v Linuxu možná zjistíte, že dostupná paměť snižuje při vaší zvýšení paměti vyrovnávací paměti, bez ohledu na neběží. všechny aplikace.

![Snímek obrazovky z příkazového řádku](./media/known-issues/cache-cleaning-1.png)

Pomocí `numactl -H` zobrazí které NUMAnode(s) paměť ukládány do vyrovnávací paměti s (případně všem). V systému Linux, můžete uživatele vyčištění mezipaměti do tří způsobů, jak vrátit ukládány do vyrovnávací paměti nebo ukládání do mezipaměti paměti "free". Budete muset být kořenové nebo mít oprávnění sudo.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Snímek obrazovky z příkazového řádku](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Upozornění jádra

Při spouštění HB řady virtuálního počítače v systému Linux, mohou se zobrazit následující zprávy upozornění jádra.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```

Toto upozornění můžete ignorovat. Příčinou je známé omezení Azure hypervisoru, který bude vyřešen v čase.

## <a name="next-steps"></a>Další postup

Další informace o [vysokovýkonného výpočetního prostředí](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) v Azure.
