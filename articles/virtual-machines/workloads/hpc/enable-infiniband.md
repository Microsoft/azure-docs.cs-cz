---
title: Povolit InifinBand s SR-IOV – virtuální počítače Azure | Dokumentace Microsoftu
description: Zjistěte, jak povolit InfiniBand s SR-IOV.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 879b1eed7bf4778d4d49f6f991d6d74214d33823
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537667"
---
# <a name="enable-infiniband-with-sr-iov"></a>Povolit InfiniBand s SR-IOV


Doporučený a nejjednodušší způsob, jak nakonfigurovat vlastní image virtuálního počítače s InfiniBand (IB) je přidání rozšíření InfiniBandDriverLinux nebo InfiniBandDriverWindows virtuálních počítačů k nasazení.
Další informace o použití těchto rozšíření virtuálních počítačů se [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) a [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

Chcete-li ručně konfigurovat InfiniBand rozhraní SR-IOV virtuálních počítačů (aktuálně řady HB a HC) s povoleným, použijte následující postup. Tyto kroky jsou k dispozici pouze pro RHEL nebo CentOS. Ubuntu (16.04 a 18.04) a SLES (12 SP4 a 15) ovladače Doručená pošta fungovat dobře. Pro Ubuntu 


## <a name="manually-install-ofed"></a>Ruční instalace OFED

Nainstalujte nejnovější ovladače MLNX_OFED ConnectX-5 z [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=26).

Pro RHEL/CentOS (příklad níže pro 7.6):
```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

Pro Windows, stáhněte a nainstalujte ovladače WinOF-2 pro ConnectX-5 z [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

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

## <a name="assign-an-ip-address"></a>Přiřaďte IP adresu

Přiřadíte IP adresu rozhraní ib0, buď pomocí:

- Ručně přiřadíte IP adresu rozhraní ib0 (jako uživatel root).

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

NEBO

- Použijte WALinuxAgent přiřadit IP adresu a usnadnit zachování.

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

Další informace o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) v Azure.
