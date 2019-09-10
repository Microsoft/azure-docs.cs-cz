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
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 7218fceae71969f204c6c25ba4793a7c94341693
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858480"
---
# <a name="enable-infiniband-with-sr-iov"></a>Povolit InfiniBand s rozhraním SR-IOV

Nejjednodušší a doporučený způsob, jak začít s IaaS virtuálními počítači pro HPC, je použití image operačního systému CentOS-HPC 7,6. Pokud používáte vlastní image virtuálního počítače, nejjednodušší způsob, jak ho nakonfigurovat pomocí InfiniBand (IB), je přidat rozšíření virtuálního počítače InfiniBandDriverLinux nebo InfiniBandDriverWindows do svého nasazení.
Naučte se používat tato rozšíření virtuálních počítačů se systémy [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) a [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances) .

Pokud chcete ručně nakonfigurovat InfiniBand na virtuálních počítačích s povoleným rozhraním SR-IOV (v současnosti se jedná o sérii a HC), postupujte podle následujících pokynů. Tyto kroky platí jenom pro RHEL/CentOS. V případě Ubuntu (16,04 a 18,04) a SLES (12 SP4 a 15) fungují i ovladače doručené pošty.

## <a name="manually-install-ofed"></a>Ruční instalace OFED

Nainstalujte nejnovější ovladače MLNX_OFED pro ConnectX-5 z [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=26).

Pro RHEL/CentOS (příklad uvedený níže pro 7,6):

```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

Pro Windows Stáhněte a nainstalujte ovladače WinOF-2 pro ConnectX-5 z [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34) .

## <a name="enable-ipoib"></a>Povolit IPoIB

```bash
sudo sed -i 's/LOAD_EIPOIB=no/LOAD_EIPOIB=yes/g' /etc/infiniband/openib.conf
sudo /etc/init.d/openibd restart
if [ $? -eq 1 ]
then
  sudo modprobe -rv  ib_isert rpcrdma ib_srpt
  sudo /etc/init.d/openibd restart
fi
```

## <a name="assign-an-ip-address"></a>Přiřazení IP adresy

Přiřaďte IP adresu rozhraní ib0 pomocí některého z těchto:

- Ručně přiřaďte IP adresu ib0 rozhraní (jako root).

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

NEBO

- Pomocí WALinuxAgent přiřaďte IP adresu a nastavte ji jako trvalou.

    ```bash
    yum install -y epel-release
    yum install -y python-pip
    python -m pip install --upgrade pip setuptools wheel
    wget "https://github.com/Azure/WALinuxAgent/archive/release-2.2.36.zip"
    unzip release-2.2.36.zip
    cd WALinuxAgent*
    python setup.py install --register-service --force
    sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
    sed -i -e 's/# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
    systemctl restart waagent
    ```

## <a name="next-steps"></a>Další postup

Přečtěte si další informace o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) v Azure.
