---
title: Povolit InifinBand s SR-IOV – Azure Virtual Machines | Microsoft Docs
description: Naučte se, jak povolit InfiniBand s rozhraním SR-IOV.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/17/2019
ms.author: amverma
ms.openlocfilehash: 7f7907482da886d9da17ef1e7844b205f3e4b906
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "74196772"
---
# <a name="enable-infiniband-with-sr-iov"></a>Povolit InfiniBand s rozhraním SR-IOV

Řada virtuálních počítačů Azure NC, ND a H-Series je zajištěná vyhrazenou InfiniBand sítí. Všechny velikosti podporující RDMA jsou schopné využití této sítě pomocí Intel MPI. Některé řady virtuálních počítačů mají rozšířenou podporu pro všechny implementace MPI a akce RDMA prostřednictvím rozhraní SR-IOV. Virtuální počítače s podporou RDMA zahrnují [optimalizované grafické procesory](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) a virtuální počítače [HPC (High Performance COMPUTE)](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) .

## <a name="choose-your-installation-path"></a>Zvolit cestu instalace

Chcete-li začít, nejjednodušší možností je použít bitovou kopii platformy, která je předem nakonfigurovaná pro InfiniBand, pokud je k dispozici:

- **Virtuální počítače HPC IaaS** – Pokud chcete začít s virtuálními počítači IaaS pro HPC, nejjednodušší řešení je použít [bitovou kopii operačního systému CentOS-HPC 7,6](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557), která je už nakonfigurovaná s InfiniBand. Vzhledem k tomu, že je tato image už nakonfigurovaná pomocí InfiniBand, nemusíte ji konfigurovat ručně. Kompatibilní verze systému Windows najdete v tématu [instance podporující Windows RDMA](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances).

- **Virtuální počítače IaaS GPU** – pro virtuální počítače optimalizované pro GPU se momentálně nenakonfigurovaly žádné image platforem, s výjimkou [image operačního systému CentOS-HPC 7,6](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557). Pokud chcete nakonfigurovat vlastní image pomocí InfiniBand, přečtěte si téma [Ruční instalace Mellanox OFED](#manually-install-mellanox-ofed).

Pokud používáte vlastní image virtuálního počítače nebo virtuální počítač s [optimalizovaným grafickým procesorem](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) , měli byste ho nakonfigurovat pomocí InfiniBand přidáním rozšíření virtuálního počítače InfiniBandDriverLinux nebo InfiniBandDriverWindows do svého nasazení. Naučte se používat tato rozšíření virtuálních počítačů se systémy [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) a [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances).

## <a name="manually-install-mellanox-ofed"></a>Ruční instalace Mellanox OFED

Pokud chcete ručně nakonfigurovat InfiniBand s rozhraním SR-IOV, použijte následující postup. Příklad v těchto krocích zobrazuje syntaxi pro RHEL/CentOS, ale postup je obecný a dá se použít pro libovolný kompatibilní operační systém, jako je například Ubuntu (16,04, 18,04 19,04) a SLES (12 SP4 a 15). Ovladače doručené pošty jsou také funkční, ale ovladače Mellanox OpenFabrics poskytují více funkcí.

Další informace o podporovaných distribucích pro ovladač Mellanox najdete v nejnovějších [ovladačích OpenFabrics Mellanox](https://www.mellanox.com/page/products_dyn?product_family=26). Další informace o ovladači Mellanox OpenFabrics najdete v [uživatelské příručce pro Mellanox](https://docs.mellanox.com/category/mlnxofedib).

V následujícím příkladu se naučíte konfigurovat InfiniBand pro Linux:

```bash
# Modify the variable to desired Mellanox OFED version
MOFED_VERSION=#4.7-1.0.0.1
# Modify the variable to desired OS
MOFED_OS=#rhel7.6
pushd /tmp
curl -fSsL https://www.mellanox.com/downloads/ofed/MLNX_OFED-${MOFED_VERSION}/MLNX_OFED_LINUX-${MOFED_VERSION}-${MOFED_OS}-x86_64.tgz | tar -zxpf -
cd MLNX_OFED_LINUX-*
sudo ./mlnxofedinstall
popd
```

Pro Windows Stáhněte a nainstalujte [ovladače Mellanox OFED for Windows](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34).

## <a name="enable-ip-over-infiniband"></a>Povolit IP adresu přes InfiniBand

K povolení protokolu IP přes InfiniBand použijte následující příkazy.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) v Azure.
