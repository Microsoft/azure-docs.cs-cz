---
title: Povolení inifinbandu s SR-IOV – virtuální počítače Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak povolit InfiniBand s SR-IOV.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196772"
---
# <a name="enable-infiniband-with-sr-iov"></a>Povolení infinibandu s SR-IOV

Azure NC, ND a H-série virtuálních počítačů jsou všechny podporované vyhrazené sítě InfiniBand. Všechny velikosti s podporou RDMA jsou schopny využít tuto síť pomocí technologie Intel MPI. Některé řady virtuálních her rozšířily podporu pro všechny implementace MPI a slovesa RDMA prostřednictvím SR-IOV. Virtuální počítače s podporou RDMA zahrnují virtuální počítače [optimalizované pro GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) a [vysoce výkonné výpočetní (HPC)](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) virtuální počítače.

## <a name="choose-your-installation-path"></a>Vyberte si instalační cestu

Chcete-li začít, nejjednodušší možností je použít bitovou kopii platformy předem nakonfigurovanou pro InfiniBand, pokud je k dispozici:

- **Virtuální počítače HPC IaaS** – Chcete-li začít s virtuálními počítači IaaS pro HPC, nejjednodušším řešením je použití [bitové kopie operačního systému VM CentOS-HPC 7.6](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557), která je již nakonfigurována s infiniBandem. Vzhledem k tomu, že tato bitová kopie je již nakonfigurována pomocí aplikace InfiniBand, není nutné ji konfigurovat ručně. Kompatibilní verze systému Windows naleznete v tématu instance podporující verzi [systému Windows RDMA](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances).

- **Virtuální počítače IAAS GPU** – žádné image platformy nejsou momentálně předem nakonfigurované pro virtuální počítače optimalizované pro GPU, s výjimkou [image operačního systému VM CentOS-HPC 7.6](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557). Chcete-li nakonfigurovat vlastní bitovou kopii pomocí aplikace InfiniBand, přečtěte si [část Ruční instalace mellanoxu OFED](#manually-install-mellanox-ofed).

Pokud používáte vlastní image virtuálního počítače nebo virtuální počítač [optimalizovaný pro GPU,](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) měli byste ho nakonfigurovat pomocí InfiniBandu přidáním rozšíření infiniBandDriverLinux nebo InfiniBandDriverWindows do vašeho nasazení. Přečtěte si, jak používat tato rozšíření virtuálních aplikací s [Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) a [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances).

## <a name="manually-install-mellanox-ofed"></a>Ruční instalace Mellanox OFED

Chcete-li ručně nakonfigurovat aplikaci InfiniBand pomocí s rozhraním SR-IOV, postupujte podle následujících kroků. Příklad v těchto krocích ukazuje syntaxi pro RHEL/CentOS, ale kroky jsou obecné a lze je použít pro jakýkoli kompatibilní operační systém, jako je Ubuntu (16.04, 18.04 19.04) a SLES (12 SP4 a 15). Ovladače doručené pošty fungují také, ale ovladače Mellanox OpenFabrics poskytují více funkcí.

Další informace o podporovaných distribucích ovladače Mellanox naleznete v nejnovějších [ovladačích Mellanox OpenFabrics](https://www.mellanox.com/page/products_dyn?product_family=26). Další informace o ovladači Mellanox OpenFabrics naleznete v [uživatelské příručce Mellanox](https://docs.mellanox.com/category/mlnxofedib).

Postup konfigurace náramku InfiniBand na Linuxu najdete v následujícím příkladu:

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

Pro Windows stáhněte a nainstalujte [ovladače Mellanox OFED pro Windows](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34).

## <a name="enable-ip-over-infiniband"></a>Povolení protokolu IP přes síť InfiniBand

K povolení protokolu IP přes InfiniBand použijte následující příkazy.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [HPC v](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) Azure.
