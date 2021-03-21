---
title: Konfigurace a optimalizace InfiniBand s povolenými aktualizacemi řady H-Series a N-Series Azure Virtual Machines
description: Přečtěte si o konfiguraci a optimalizaci virtuálních počítačů s podporou InfiniBand a N-Series pro HPC.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 10/23/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 94334e54865b3a3b603cbd0b3943899a375d894e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101675664"
---
# <a name="configure-and-optimize-vms"></a>Konfigurace a optimalizace virtuálních počítačů

Tento článek sdílí známé techniky pro konfiguraci a optimalizaci virtuálních počítačů řady [H-Series](../../sizes-hpc.md) s podporou InfiniBand a [N-Series](../../sizes-gpu.md) pro HPC.

## <a name="vm-images"></a>Image virtuálních počítačů
Na virtuálních počítačích s povolenou funkcí InfiniBand je nutné, aby byly k dispozici vhodné ovladače pro povolení RDMA. V systému Linux jsou image virtuálních počítačů CentOS-HPC na webu Marketplace předem nakonfigurované s příslušnými ovladači. Image virtuálních počítačů s Ubuntu se dají nakonfigurovat pomocí správných ovladačů podle [pokynů uvedených tady](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351). Doporučuje se také vytvořit [vlastní image virtuálních počítačů](../../linux/tutorial-custom-images.md) s příslušnými ovladači a konfigurací a znovu je použít.

> [!NOTE]
> U virtuálních počítačů s povoleným GPU [řady N-Series](../../sizes-gpu.md) se navíc vyžadují příslušné ovladače GPU, které je možné přidat pomocí [rozšíření virtuálních počítačů](../../extensions/hpccompute-gpu-linux.md) nebo [ručně](../../linux/n-series-driver-setup.md). Některé image virtuálních počítačů na webu Marketplace jsou také předem nainstalovány s ovladači GPU NVIDIA.

### <a name="centos-hpc-vm-images"></a>Image virtuálních počítačů CentOS-HPC

#### <a name="non-sr-iov-enabled-vms"></a>Virtuální počítače s podporou nevyužívající rozhraní SR-IOV
Pro [virtuální počítače](../../sizes-hpc.md#rdma-capable-instances)podporující technologii RDMA s podporou SR-IOV, CentOS-HPC verze 6,5 nebo novější, jsou vhodné až 7,5 na webu Marketplace. Například pro [virtuální počítače řady H16 úrovně-Series](../../h-series.md)se doporučuje verze 7,1 až 7,5. Tyto image virtuálních počítačů jsou předem načtené pomocí síťových přímých ovladačů pro RDMA a Intel MPI verze 5,1.

> [!NOTE]
> V těchto imagích HPC založených na CentOS pro virtuální počítače, které nejsou povolené SR-IOV, jsou aktualizace jádra v konfiguračním souboru **Yumu** zakázané. Důvodem je to, že ovladače RDMA pro NetworkDirect Linux jsou distribuované jako balíček ot./min. a aktualizace ovladačů nemusí fungovat, pokud je jádro aktualizované.

#### <a name="sr-iov-enabled-vms"></a>Virtuální počítače s podporou SR-IOV
  Pro [virtuální počítače podporující](../../sizes-hpc.md#rdma-capable-instances)rozhraní SR-IOV, které podporuje RDMA, jsou vhodné image [CentOS-HPC verze 7,6 nebo novější](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) verze virtuálních počítačů na webu Marketplace. Tyto image virtuálních počítačů přináší optimalizované a předem načtené ovladače OFED pro RDMA a různé běžně používané knihovny MPI a vědecké výpočetní balíčky a představují nejjednodušší způsob, jak začít.

  Příklady skriptů použitých při vytváření imagí virtuálních počítačů CentOS-HPC verze 7,6 a novější z image základního CentOS na webu Marketplace jsou v [úložišti azhpc-images](https://github.com/Azure/azhpc-images/tree/master/centos).
  
  > [!NOTE] 
  > Nejnovější image Azure HPC Marketplace mají Mellanox OFED 5,1 a vyšší, které nepodporují ConnectX3-Pro InfiniBand karty. Velikosti virtuálních počítačů s povoleným rozhraním SR-IOV s FDR InfiniBand (např. NCv3) budou moci používat následující verze imagí virtuálních počítačů CentOS-HPC nebo starší:
  >- OpenLogic: CentOS-HPC: 7.6:7.6.2020062900
  >- OpenLogic: CentOS-HPC: 7_6gen2:7.6.2020062901
  >- OpenLogic: CentOS-HPC: 7.7:7.7.2020062600
  >- OpenLogic: CentOS-HPC: 7_7-Gen2:7.7.2020062601
  >- OpenLogic: CentOS-HPC: 8_1:8.1.2020062400
  >- OpenLogic: CentOS-HPC: 8_1-Gen2:8.1.2020062401


### <a name="rhelcentos-vm-images"></a>Image virtuálních počítačů s RHEL/CentOS
Image virtuálních počítačů s RHEL nebo CentOS na webu Marketplace se dají nakonfigurovat tak, aby se používaly na [virtuálních počítačích](../../sizes-hpc.md#rdma-capable-instances)podporujících rozhraní SR-IOV s podporou RDMA. Přečtěte si další informace o [Povolení InfiniBand](enable-infiniband.md) a [Nastavení MPI](setup-mpi.md) na virtuálních počítačích.

  Příklady skriptů použitých při vytváření imagí virtuálních počítačů CentOS-HPC verze 7,6 a novější z image základního CentOS na webu Marketplace jsou v [úložišti azhpc-images](https://github.com/Azure/azhpc-images/tree/master/centos).
  
  > [!NOTE]
  > Mellanox OFED 5,1 a novější nepodporují ConnectX3-Pro InfiniBand karty na velikosti virtuálních počítačů s podporou SR-IOV s FDR InfiniBand (např. NCv3). Použijte prosím LTS Mellanox OFED verze 4.9-0.1.7.0 nebo starší na virtuálním počítači řady N-Series s kartami ConnectX3-Pro. Další podrobnosti najdete [tady](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed).

### <a name="ubuntu-vm-images"></a>Image virtuálních počítačů s Ubuntu
Ubuntu Server 16,04 LTS, 18,04 LTS a 20,04 image virtuálních počítačů LTS na webu Marketplace se podporují pro [virtuální počítače podporující](../../sizes-hpc.md#rdma-capable-instances)rozhraní SR-IOV a non-SR-IOV RDMA. Přečtěte si další informace o [Povolení InfiniBand](enable-infiniband.md) a [Nastavení MPI](setup-mpi.md) na virtuálních počítačích.

  Příklady skriptů, které se dají použít při vytváření imagí virtuálních počítačů HPC založených na Ubuntu 18,04 LTS, najdete v [úložišti azhpc-images](https://github.com/Azure/azhpc-images/tree/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc).

### <a name="suse-linux-enterprise-server-vm-images"></a>SUSE Linux Enterprise Server imagí virtuálních počítačů
SLES 12 SP3 pro HPC, SLES 12 SP3 pro HPC (Premium), SLES 12 SP1 pro HPC, SLES 12 SP1 pro HPC (Premium), SLES 12 SP4 a SLES 15 image virtuálních počítačů na webu Marketplace se podporují. Tyto image virtuálních počítačů jsou předem načtené pomocí síťových přímých ovladačů pro RDMA a Intel MPI verze 5,1. Přečtěte si další informace o [Nastavení MPI](setup-mpi.md) na virtuálních počítačích.

## <a name="optimize-vms"></a>Optimalizace virtuálních počítačů

Toto jsou některá volitelná nastavení optimalizace pro zlepšení výkonu virtuálního počítače.

### <a name="update-lis"></a>Aktualizace LIS

V případě potřeby je možné nainstalovat nebo aktualizovat [ovladače služby Linux Integration Services (LIS)](../../linux/endorsed-distros.md) v podporovaném operačním systému distribuce, zejména při nasazení pomocí vlastní image nebo starší verze operačního systému, jako je CentOS/RHEL 6. x nebo starší verze 7. x.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>Uvolnit paměť

Vylepšete výkon tím, že automaticky uvolní paměť, aby nedocházelo k vzdálenému přístupu do paměti.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Chcete-li toto zachovat po restartování virtuálního počítače:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>Zakázat bránu firewall a SELinux

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### <a name="disable-cpupower"></a>Zakázat cpupower

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

### <a name="configure-walinuxagent"></a>Konfigurace WALinuxAgent

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
Volitelně může být WALinuxAgent zakázaný krok před úlohou a povolený back-Job pro maximální dostupnost prostředků virtuálního počítače pro úlohu HPC.


## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Povolení InfiniBand](enable-infiniband.md) na virtuálních počítačích s InfiniBand a [N-Series](../../sizes-gpu.md) [s podporou řady d-Series](../../sizes-hpc.md) .
- Přečtěte si další informace o instalaci různých [podporovaných knihoven MPI](setup-mpi.md) a jejich optimálních konfiguracích na virtuálních počítačích.
- Seznamte se s přehledem a [řadou HC](hc-series-overview.md) - [Series](hb-series-overview.md) – přehled s optimální konfigurací úloh pro zajištění výkonu a škálovatelnosti.
- Přečtěte si o nejnovějších oznámeních a některých příkladech HPC a výsledcích na [blogu Azure COMPUTE tech Community](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pro zobrazení architektury na vyšší úrovni pro spouštění úloh HPC si přečtěte téma věnované technologii [HPC (High Performance Computing) v Azure](/azure/architecture/topics/high-performance-computing/).
