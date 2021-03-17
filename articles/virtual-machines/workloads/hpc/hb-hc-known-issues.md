---
title: Řešení známých problémů s virtuálními počítači HPC a GPU – Azure Virtual Machines | Microsoft Docs
description: Přečtěte si informace o řešení známých problémů s velikostí virtuálních počítačů HPC a GPU v Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 0a0eaa18f5b120fcc9cbf0e4da470ee46772c925
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2021
ms.locfileid: "103470400"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>Známé problémy s virtuálními počítači řady H a N

Tento článek popisuje nejběžnější problémy a řešení při použití prostředí HPC [řady H-Series](../../sizes-hpc.md) a [N-Series](../../sizes-gpu.md) a virtuálních počítačů GPU.

## <a name="known-issues-on-hbv3"></a>Známé problémy v HBv3
- InfiniBand se momentálně podporuje jenom u virtuálního počítače 120 Core (Standard_HB120rs_v3). Podpora na jiné velikosti virtuálních počítačů bude brzy povolená.
- Ve všech oblastech HBv3-Series nepodporuje Azure akcelerované sítě. Tato funkce bude brzy povolená.

## <a name="accelerated-networking-on-hb-hc-hbv2-and-ndv2"></a>Urychlené síťové služby na neHBv2ch, HC, a NDv2

[Akcelerované síťové služby Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) jsou teď dostupné na velikostech virtuálních počítačů s podporou RDMA a INFINIBAND a SR [-IOV, která](../../hb-series.md) [je v](../../hc-series.md)paměti [.](../../ndv2-series.md) [](../../hbv2-series.md) Tato možnost teď umožňuje vylepšenou dobu (až 30 GB/s) a latence v síti Ethernet Azure. I když se jedná o možnosti RDMA přes síť InfiniBand, můžou některé změny platformy pro tuto funkci ovlivnit chování určitých MPI implementací při spouštění úloh přes InfiniBand. Konkrétně rozhraní InfiniBand na některých virtuálních počítačích může mít trochu odlišný název (mlx5_1 na rozdíl od dřívější mlx5_0) a to může vyžadovat seování příkazových řádků MPI zvláště při použití rozhraní UCX (obvykle se jedná o OpenMP a HPC-X).
Další podrobnosti najdete v tomto [článku na blogu](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-and-hbv2/ba-p/2067965) s pokyny, jak vyřešit všechny zjištěné problémy.

## <a name="infiniband-driver-installation-on-n-series-vms"></a>Instalace ovladače InfiniBand na virtuálních počítačích řady N-Series

NC24r_v3 a ND40r_v2 mají povolený rozhraní SR-IOV, zatímco NC24r a NC24r_v2 nejsou povolené rozhraní SR-IOV. Některé podrobnosti o bifurcation [najdete tady](../../sizes-hpc.md#rdma-capable-instances).
InfiniBand (IB) je možné nakonfigurovat na velikosti virtuálních počítačů s podporou SR-IOV pomocí ovladačů OFED, zatímco velikosti virtuálních počítačů bez SR-IOV vyžadují ovladače ND. Tato podpora IB je k dispozici správně v [CentOS-HPC VMIs](configure.md). Ubuntu najdete [tady pokyny](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351) k instalaci ovladačů OFED a ND, jak je popsáno v [dokumentaci](enable-infiniband.md#vm-images-with-infiniband-drivers).

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>Duplikovat MAC pomocí Cloud-init s Ubuntu na virtuálních počítačích řady H-Series a N-Series

Při pokusu o uvedení rozhraní IB došlo k známému problému s Ubuntu imagí Cloud-init na virtuálních počítačích. K tomu může dojít buď při restartování virtuálního počítače, nebo při pokusu o vytvoření image virtuálního počítače po generalizaci. Spouštěcí protokoly virtuálních počítačů mohou zobrazovat chybu, například: "spuštění síťové služby... RuntimeError: našla se duplicitní adresa MAC. eth1 i ib0 mají Mac.

Tento "duplicitní počítač MAC s cloudem-init na Ubuntu" je známý problém. Alternativní řešení:
1) Nasazení image virtuálního počítače (Ubuntu 18,04) Marketplace
2) Nainstalujte potřebné softwarové balíčky, aby bylo možné povolit IB ([pokyny najdete tady](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)).
3) Úpravou waagent. conf změňte EnableRDMA = y.
4) Zakázání sítě v cloudu – inicializace
    ```console
    echo network: {config: disabled} | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
    ```
5) Umožňuje upravit konfigurační soubor netplan sítě generovaný modulem Cloud-init a odebrat tak počítač MAC.
    ```console
    sudo bash -c "cat > /etc/netplan/50-cloud-init.yaml" <<'EOF'
    network:
      ethernets:
        eth0:
          dhcp4: true
      version: 2
    EOF
    ```

## <a name="dram-on-hb-series"></a>DRAM v řadě 7000

Virtuální počítače s více procesory můžou v současnosti vystavovat 228 GB paměti RAM pro virtuální počítače hosta. Podobně 458 GB na HBv2 a 448 GB na virtuálních počítačích s HBv3. Důvodem je známé omezení hypervisoru Azure, které zabraňuje tomu, aby se stránky přiřazují do místní adresy DRAM pro virtuální počítače AMD CCX (domény NUMA) rezervované pro virtuální počítač hosta.

## <a name="qp0-access-restriction"></a>Omezení přístupu qp0

Aby nedocházelo k hardwarovému přístupu nízké úrovně, který může mít za následek ohrožení zabezpečení, spárování fronty 0 není pro virtuální počítače hosta k dispozici. To by mělo mít vliv pouze na akce, které jsou obvykle spojeny s správou síťového rozhraní ConnectX-5, a spuštění některé diagnostiky InfiniBand jako ibdiagnet, ale nejedná se o samotné aplikace koncového uživatele.

## <a name="gss-proxy"></a>Proxy služby GSS

Proxy serveru služby GSS má známou chybu v CentOS/RHEL 7,5, která se může při použití se systémem souborů NFS projevit jako významné snížení výkonu a odezvy. Můžete to zmírnit:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Čištění mezipaměti

V systémech HPC je často užitečné vyčistit paměť po dokončení úlohy, ještě než se k dalšímu uživateli přiřadí stejný uzel. Po spuštění aplikací v systému Linux se můžete setkat s tím, že vaše dostupná paměť se zmenší, i když se zvyšuje paměť vyrovnávací paměti, i když aplikace neběží.

![Snímek obrazovky s příkazovým řádkem před vyčištěním](./media/known-issues/cache-cleaning-1.png)

Pomocí `numactl -H` se zobrazí, které NUMAnode paměti jsou ukládány do vyrovnávací paměti (případně všechny). V systému Linux můžou uživatelé vyčistit mezipaměti třemi způsoby, jak vracet vyrovnávací paměť nebo paměť v mezipaměti do ' Free '. Musíte být kořen nebo mít oprávnění sudo.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Snímek obrazovky s příkazovým řádkem po vyčištění](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Upozornění jádra

Při spuštění virtuálního počítače s rozhraním "7000-Series" v systému Linux můžete ignorovat následující zprávy upozornění jádra. Důvodem je známé omezení hypervisoru Azure, který bude vyřešen v průběhu času.

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


## <a name="next-steps"></a>Další kroky

- Seznamte se s přehledem a [řadou HC](hc-series-overview.md) - [Series](hb-series-overview.md) – přehled s optimální konfigurací úloh pro zajištění výkonu a škálovatelnosti.
- Přečtěte si o nejnovějších oznámeních, příkladech úloh HPC a výsledcích výkonu na [blogu Azure COMPUTE tech Community](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Podrobné zobrazení architektury prostředí HPC, které běží na vyšší úrovni, najdete v tématu věnovaném technologii [HPC (High Performance Computing) v Azure](/azure/architecture/topics/high-performance-computing/).
