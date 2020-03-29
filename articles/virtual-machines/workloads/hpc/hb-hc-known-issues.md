---
title: Známé problémy s virtuálními počítači řady HB a HC - virtuální počítače Azure | Dokumenty společnosti Microsoft
description: Přečtěte si o známých problémech s velikostmi virtuálních zařízení řady HB v Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707780"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>Známé problémy s virtuálními počítači řady HB a HC

Tento článek obsahuje nejběžnější problémy a řešení při použití hb-series a HC-series virtuálních počítačů.

## <a name="dram-on-hb-series"></a>DRAM na HB-série

Virtuální počítače řady HB můžou v tuto chvíli zpřístupnit pouze 228 GB paměti RAM hostovanému virtuálním počítači. Důvodem je známé omezení hypervisoru Azure, aby se zabránilo přiřazení stránek k místní paměti DRAM domény AMD CCX (domény NUMA) vyhrazené pro hostovaný virtuální počítač.

## <a name="accelerated-networking"></a>Akcelerované síťové služby

Azure Accelerated Networking není povolena v tomto okamžiku, ale bude, jak budeme postupovat přes období náhledu. Budeme zákazníky upozorňovat, pokud je tato funkce podporována.

## <a name="qp0-access-restriction"></a>qp0 Omezení přístupu

Chcete-li zabránit přístupu k hardwaru nižší úrovně, který může mít za následek ohrožení zabezpečení, dvojice fronty 0 není přístupná pro hostované virtuální chody. To by mělo mít vliv pouze na akce obvykle spojené se správou síťové adaptéru ConnectX-5 a spuštění některých diagnostik InfiniBand, jako je ibdiagnet, ale ne samotné aplikace koncových uživatelů.

## <a name="ud-transport"></a>UD doprava

Při uvedení na trh nepodporují řady HB- a HC dynamicky připojené ho transporty (DCT). Podpora DCT bude realizována v průběhu času. Jsou podporovány přenosy spolehlivého připojení (RC) a nespolehlivého datagramu (UD).

## <a name="gss-proxy"></a>GSS Proxy

GSS Proxy má známou chybu v CentOS / RHEL 7.5, která se může projevit jako významný výkon a snížení rychlosti při použití s NFS. To lze zmírnit pomocí:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Čištění mezipaměti

V systémech HPC je často užitečné vyčistit paměť po dokončení úlohy před tím, než je dalšímu uživateli přiřazen stejný uzel. Po spuštění aplikací v Systému Linux můžete zjistit, že dostupná paměť se snižuje, zatímco paměť vyrovnávací paměti se zvyšuje, přestože neběží žádné aplikace.

![Snímek obrazovky s příkazovým řádku](./media/known-issues/cache-cleaning-1.png)

Pomocí `numactl -H` ukáže, které NUMAnode (y) paměti je do vyrovnávací paměti s (případně všechny). V Linuxu mohou uživatelé čistit mezipaměti třemi způsoby, jak vrátit paměť ve vyrovnávací paměti nebo paměť uloženou do mezipaměti do "volného". Musíte být kořen nebo mít oprávnění sudo.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Snímek obrazovky s příkazovým řádku](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Upozornění jádra

Při zavádění virtuálního počítače řady HB pod Linuxem se mohou zobrazit následující varovné zprávy jádra.

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

Toto upozornění můžete ignorovat. To je způsobeno známé omezení hypervisoru Azure, který bude vyřešen v průběhu času.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [vysoce výkonném výpočetním prostředí](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) v Azure.
