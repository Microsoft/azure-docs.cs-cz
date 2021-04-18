---
title: Povolení InifinBand na virtuálních počítačích HPC – Azure Virtual Machines | Microsoft Docs
description: Naučte se, jak povolit InfiniBand na virtuálních počítačích Azure HPC.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: dba336c8690bba2bb388a8b9ab2d52b651166da5
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599600"
---
# <a name="enable-infiniband"></a>Povolení sítí Infiniband

Virtuální [počítače s](../../sizes-hpc.md) [podporou RDMA](../../sizes-hpc.md#rdma-capable-instances) a [řady N-Series](../../sizes-gpu.md) komunikují přes InfiniBand síť s nízkou latencí a velkou šířkou pásma. Schopnost RDMA v takovém propojení je zásadní pro zvýšení škálovatelnosti a výkonu funkcí HPC a AI distribuovaných uzlů. Virtuální počítače s povoleným InfiniBand H-Series a N-Series se připojují v neblokujícím stromu FAT s návrhem s nízkým průměrem pro optimalizaci a konzistentní výkon RDMA.

Existují různé způsoby, jak povolit InfiniBand ve velikostech virtuálních počítačů podporujících virtuální počítače.

## <a name="vm-images-with-infiniband-drivers"></a>Image virtuálních počítačů s ovladači InfiniBand
V tématu [image virtuálních počítačů](configure.md#vm-images) najdete seznam podporovaných imagí virtuálních počítačů na webu Marketplace, které jsou předem načtené pomocí ovladačů InfiniBand (pro virtuální počítače SR-IOV nebo non-SR-IOV), nebo se dají nakonfigurovat pomocí vhodných ovladačů pro [virtuální počítače podporující RDMA](../../sizes-hpc.md#rdma-capable-instances).
- Image virtuálních počítačů [CentOS-HPC](configure.md#centos-hpc-vm-images) na webu Marketplace představují nejjednodušší způsob, jak začít.
- Image virtuálních počítačů s [Ubuntu](configure.md#ubuntu-vm-images) se dají konfigurovat pomocí správných ovladačů IB.

## <a name="infiniband-driver-vm-extensions"></a>Rozšíření virtuálních počítačů s ovladačem InfiniBand
V systému Linux lze pomocí [rozšíření INFINIBANDDRIVERLINUX VM](../../extensions/hpc-compute-infiniband-linux.md) nainstalovat ovladače Mellanox OFED a povolit InfiniBand na virtuálních počítačích s podporou SR-IOV a N-Series.

V systému Windows [rozšíření virtuálního počítače InfiniBandDriverWindows](../../extensions/hpc-compute-infiniband-windows.md) nainstaluje ovladače síťové technologie Windows (na virtuálních počítačích bez SR-IOV) nebo ovladače Mellanox OFED (na virtuálních počítačích s SR-IOV) pro připojení RDMA. V některých nasazeních instancí A8 a A8 se rozšíření HpcVmDrivers přidá automaticky. Všimněte si, že rozšíření virtuálního počítače HpcVmDrivers je zastaralé; nebude aktualizován.

Pokud chcete přidat rozšíření virtuálního počítače do virtuálního počítače, můžete použít rutiny [Azure PowerShell](/powershell/azure/) . Další informace najdete v tématu [rozšíření a funkce virtuálních počítačů](../../extensions/overview.md). Můžete také pracovat s rozšířeními pro virtuální počítače nasazené v [modelu nasazení Classic](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

## <a name="manual-installation"></a>Ruční instalace
[Ovladače Mellanox OpenFabrics (OFED)](https://www.mellanox.com/products/InfiniBand-VPI-Software) je možné ručně nainstalovat na virtuální počítače s [podporou SR-IOV](../../sizes-hpc.md#rdma-capable-instances) [a](../../sizes-hpc.md) [N-Series](../../sizes-gpu.md) .

### <a name="linux"></a>Linux
[Ovladače OFED pro Linux](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed) se dají nainstalovat s následujícím příkladem. I když je tady příklad pro RHEL/CentOS, ale postup je obecný a dá se použít pro libovolný kompatibilní operační systém Linux, jako je například Ubuntu (16,04, 18,04 19,04, 20,04) a SLES (12 SP4 a 15). Další příklady pro jiné distribuce jsou v [úložišti azhpc-images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mellanoxofed.sh). Ovladače doručené pošty také fungují taky, ale ovladače Mellanox OFED poskytují více funkcí.

```bash
MLNX_OFED_DOWNLOAD_URL=http://content.mellanox.com/ofed/MLNX_OFED-5.0-2.1.8.0/MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz
# Optionally verify checksum
wget --retry-connrefused --tries=3 --waitretry=5 $MLNX_OFED_DOWNLOAD_URL
tar zxvf MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz

KERNEL=( $(rpm -q kernel | sed 's/kernel\-//g') )
KERNEL=${KERNEL[-1]}
# Uncomment the lines below if you are running this on a VM
#RELEASE=( $(cat /etc/centos-release | awk '{print $4}') )
#yum -y install http://olcentgbl.trafficmanager.net/centos/${RELEASE}/updates/x86_64/kernel-devel-${KERNEL}.rpm
yum install -y kernel-devel-${KERNEL}
./MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64/mlnxofedinstall --kernel $KERNEL --kernel-sources /usr/src/kernels/${KERNEL} --add-kernel-support --skip-repo
```

### <a name="windows"></a>Windows
Pro Windows Stáhněte a nainstalujte [ovladače Mellanox OFED for Windows](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2).

## <a name="enable-ip-over-infiniband-ib"></a>Povolit IP adresu přes InfiniBand (IB)
Pokud máte v plánu spouštět úlohy MPI, obvykle nepotřebujete IPoIB. Knihovna MPI bude používat rozhraní příkazů pro komunikaci IB (pokud explicitně nepoužíváte kanál TCP/IP knihovny MPI). Pokud ale máte aplikaci, která pro komunikaci používá protokol TCP/IP a chcete ji spustit přes IB, můžete IPoIB použít přes rozhraní IB. K povolení protokolu IP přes InfiniBand použijte následující příkazy (pro RHEL/CentOS).

```bash
sudo sed -i -e 's/# OS.EnableRDMA=n/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o instalaci a spuštění různých [podporovaných knihoven MPI](setup-mpi.md) na virtuálních počítačích.
- Přečtěte si přehled [HBv3-Series](hbv3-series-overview.md) Overview a [HC-Series](hc-series-overview.md).
- Přečtěte si o nejnovějších oznámeních, příkladech úloh HPC a výsledcích výkonu na [blogu Azure COMPUTE tech Community](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pro zobrazení architektury na vyšší úrovni pro spouštění úloh HPC si přečtěte téma věnované technologii [HPC (High Performance Computing) v Azure](/azure/architecture/topics/high-performance-computing/).
