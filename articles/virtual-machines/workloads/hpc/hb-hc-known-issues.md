---
title: Známé problémy s virtuálními počítači s neznámým a HC-Series – Azure Virtual Machines | Microsoft Docs
description: Přečtěte si o známých problémech s velikostí virtuálních počítačů s velmi řadou v Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 6316bcc91bb381facb4f77b2d8dbd8b22f9ed387
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660091"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>Známé problémy s virtuálními počítači řady H a N

Tento článek popisuje nejběžnější problémy a řešení při použití virtuálních počítačů řady [H-Series](../../sizes-hpc.md) a [N-Series](../../sizes-gpu.md) .

## <a name="dram-on-hb-series"></a>DRAM v řadě 7000

Virtuální počítače s více procesory můžou v současnosti vystavovat 228 GB paměti RAM pro virtuální počítače hosta. Důvodem je známé omezení hypervisoru Azure, které zabraňuje tomu, aby se stránky přiřazují do místní adresy DRAM pro virtuální počítače AMD CCX (domény NUMA) rezervované pro virtuální počítač hosta.

## <a name="accelerated-networking"></a>Akcelerované síťové služby

Urychlené používání sítě Azure není v tuto chvíli povolené, ale v průběhu období Preview budeme postupovat. Pokud je tato funkce podporovaná, budeme na to informovat zákazníci.

## <a name="qp0-access-restriction"></a>Omezení přístupu qp0

Aby nedocházelo k hardwarovému přístupu nízké úrovně, který může mít za následek ohrožení zabezpečení, spárování fronty 0 není pro virtuální počítače hosta k dispozici. To by mělo mít vliv pouze na akce, které jsou obvykle spojeny s správou síťového rozhraní ConnectX-5, a spuštění některé diagnostiky InfiniBand jako ibdiagnet, ale nejedná se o samotné aplikace koncového uživatele.

## <a name="gss-proxy"></a>Proxy služby GSS

Proxy serveru služby GSS má známou chybu v CentOS/RHEL 7,5, která se může při použití se systémem souborů NFS projevit jako významné snížení výkonu a odezvy. Můžete to zmírnit:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Čištění mezipaměti

V systémech HPC je často užitečné vyčistit paměť po dokončení úlohy, ještě než se k dalšímu uživateli přiřadí stejný uzel. Po spuštění aplikací v systému Linux se můžete setkat s tím, že vaše dostupná paměť se zmenší, i když se zvyšuje paměť vyrovnávací paměti, i když aplikace neběží.

![Snímek obrazovky s příkazovým řádkem](./media/known-issues/cache-cleaning-1.png)

Pomocí `numactl -H` se zobrazí, které NUMAnode paměti jsou ukládány do vyrovnávací paměti (případně všechny). V systému Linux můžou uživatelé vyčistit mezipaměti třemi způsoby, jak vracet vyrovnávací paměť nebo paměť v mezipaměti do ' Free '. Musíte být kořen nebo mít oprávnění sudo.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Snímek obrazovky s příkazovým řádkem](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Upozornění jádra

Při spuštění virtuálního počítače s rozhraním "7000-Series" v systému Linux se může zobrazit následující zpráva s upozorněním na jádro.

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

Toto upozornění můžete ignorovat. Důvodem je známé omezení hypervisoru Azure, který bude vyřešen v průběhu času.


## <a name="infiniband-driver-installation-on-infiniband-enabled-n-series-vm-sizes"></a>Instalace ovladače InfiniBand ve velikosti virtuálních počítačů řady N-Series s povoleným InfiniBand

NC24r_v3 a ND40r_v2 mají povolený rozhraní SR-IOV, zatímco NC24r a NC24r_v2 nejsou povolené rozhraní SR-IOV. Některé podrobnosti o bifurcation [najdete tady](../../sizes-hpc.md#rdma-capable-instances).
InfiniBand (IB) je možné nakonfigurovat na velikosti virtuálních počítačů s podporou SR-IOV pomocí ovladačů OFED, zatímco velikosti virtuálních počítačů bez SR-IOV vyžadují ovladače ND. Tato podpora IB je k dispozici správně v [CentOS-HPC VMIs](configure.md). Ubuntu najdete [tady pokyny](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351) k instalaci ovladačů OFED a ND, jak je popsáno v [dokumentaci](enable-infiniband.md#vm-images-with-infiniband-drivers).


## <a name="next-steps"></a>Další kroky

- Seznamte se s přehledem a [řadou HC](hc-series-overview.md) - [Series](hb-series-overview.md) – přehled s optimální konfigurací úloh pro zajištění výkonu a škálovatelnosti.
- Přečtěte si o nejnovějších oznámeních a některých příkladech HPC a výsledcích na [blogu Azure COMPUTE tech Community](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pro zobrazení architektury na vyšší úrovni pro spouštění úloh HPC si přečtěte téma věnované technologii [HPC (High Performance Computing) v Azure](/azure/architecture/topics/high-performance-computing/).
