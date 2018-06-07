---
title: Instalace nástroje Azure ovladač N-series pro Linux | Microsoft Docs
description: Jak nastavit NVIDIA GPU ovladače pro N-series virtuální počítače s Linuxem v Azure
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 807af10c0655d9d1728a80a47d1f8f9c2a16fb84
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654279"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Instalace ovladačů NVIDIA GPU v N-series virtuální počítače se systémem Linux

Abyste mohli využívat možnosti GPU Azure N-series virtuální počítače se systémem Linux, musí být nainstalován NVIDIA grafické ovladače. Tento článek obsahuje kroky instalace ovladačů po nasadit virtuální počítač s N-series. Informace o instalaci ovladačů je také k dispozici pro [virtuálních počítačů Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Virtuální počítač N-series specifikace, kapacity úložiště a disku podrobnosti najdete v tématu [velikosti virtuálních počítačů Linux GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>Instalace ovladačů CUDA na virtuálních počítačích N-series

Tady jsou kroky pro instalaci ovladače CUDA z nástrojů CUDA NVIDIA na virtuálních počítačích N-series. 


Jazyk C a C++ vývojáři Volitelně můžete nainstalovat úplnou sadu nástrojů k vytváření aplikací GPU accelerated. Další informace najdete v tématu [Průvodce instalací CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

K instalaci ovladačů CUDA, zkontrolujte připojení SSH pro každý virtuální počítač. Pokud chcete ověřit, že systém má podporující CUDA grafického procesoru, spusťte následující příkaz:

```bash
lspci | grep -i NVIDIA
```
Zobrazí se výstup podobný v následujícím příkladu (zobrazující pomocí karty NVIDIA tesla – měrná K80):

![výstup příkazu lspci](./media/n-series-driver-setup/lspci.png)

Potom spusťte instalaci příkazy, které jsou specifické pro distribuční.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Stáhněte a nainstalujte CUDA ovladače.
  ```bash
  CUDA_REPO_PKG=cuda-repo-ubuntu1604_9.1.85-1_amd64.deb

  wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

  sudo dpkg -i /tmp/${CUDA_REPO_PKG}

  sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo apt-get update

  sudo apt-get install cuda-drivers

  ```

  Instalace může trvat několik minut.

2. Volitelně můžete nainstalovat úplnou sadu CUDA, zadejte:

  ```bash
  sudo apt-get install cuda
  ```

3. Restartujte virtuální počítač a přejděte k ověření instalace.

#### <a name="cuda-driver-updates"></a>Aktualizace ovladačů CUDA

Doporučujeme pravidelně aktualizovat ovladače CUDA po nasazení.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux-73-or-74"></a>CentOS nebo Red Hat Enterprise Linux 7.3 nebo 7.4

1. Aktualizujte jádra.

  ```
  sudo yum install kernel kernel-tools kernel-headers kernel-devel
  
  sudo reboot

2. Install the latest [Linux Integration Services for Hyper-V and Azure](https://www.microsoft.com/download/details.aspx?id=55106).

  ```bash
  wget https://aka.ms/lis
 
  tar xvzf lis
 
  cd LISISO
 
  sudo ./install.sh
 
  sudo reboot
  ```
 
3. Připojení k virtuálnímu počítači a pokračujte v instalaci pomocí následujících příkazů:

  ```bash
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

  sudo yum install dkms

  CUDA_REPO_PKG=cuda-repo-rhel7-9.1.85-1.x86_64.rpm

  wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

  sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo yum install cuda-drivers
  ```

  Instalace může trvat několik minut. 

4. Volitelně můžete nainstalovat úplnou sadu CUDA, zadejte:

  ```bash
  sudo yum install cuda
  ```

5. Restartujte virtuální počítač a přejděte k ověření instalace.

### <a name="verify-driver-installation"></a>Ověření instalace ovladačů

K dotazování na GPU zařízení stav, SSH pro virtuální počítač a spusťte [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface) pomocí ovladače nainstalovaný nástroj příkazového řádku. 

Pokud je nainstalovaný ovladač, zobrazí se výstup podobný následujícímu. Všimněte si, že **GPU Util** ukazuje 0 %, pokud aktuálně používáte zatížení grafického procesoru na virtuálním počítači. Verze ovladače a GPU podrobnosti se může lišit od těch vidět.

![Stav zařízení NVIDIA](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>Připojení k síti RDMA

Síťové připojení RDMA se dá nastavit na virtuálních počítačích podporující RDMA N-series, jako je NC24r nasadit ve stejné sadě dostupnosti nebo v jednom umístění skupiny ve škálovací sadě virtuálních počítačů. Síť RDMA podporuje rozhraní MPI (Message Passing) provozu pro aplikace spuštěné s Intel MPI 5.x nebo novější. Následují další požadavky:

### <a name="distributions"></a>Distribuce

Nasazení podporující RDMA N-series virtuálních počítačů z jedné bitové kopie v Azure Marketplace, která podporuje připojení RDMA na virtuálních počítačích N-series:
  
* **Ubuntu 16.04 LTS** – konfigurace ovladače RDMA na virtuálním počítači a registrace s Intel ke stažení Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **Na základě centOS 7.4 HPC** -RDMA ovladače a Intel MPI 5.1 jsou nainstalovány ve virtuálním počítači.

## <a name="install-grid-drivers-on-nv-series-vms"></a>Instalace ovladačů mřížky na virtuálních počítačích vs series

K instalaci ovladačů NVIDIA mřížky na virtuálních počítačích vs series, zkontrolujte připojení SSH pro každý virtuální počítač a postupujte podle kroků pro vaše distribuci systému Linux. 

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Spustit `lspci` příkaz. Ověřte, zda karta NVIDIA M60 nebo karty jsou viditelné jako PCI zařízení.

2. Nainstalujte aktualizace.

  ```bash
  sudo apt-get update

  sudo apt-get upgrade -y

  sudo apt-get dist-upgrade -y

  sudo apt-get install build-essential ubuntu-desktop -y
  ```
3. Zakážete Nouveau ovladač jádra, který není kompatibilní s NVIDIA ovladače. (Použijte pouze NVIDIA ovladač na virtuálních počítačích vs.) Chcete-li to provést, vytvořte soubor v `/etc/modprobe.d `s názvem `nouveau.conf` s tímto obsahem:

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```


4. Restartujte virtuální počítač a znovu připojit. Ukončení X server:

  ```bash
  sudo systemctl stop lightdm.service
  ```

5. Stáhněte a nainstalujte ovladač mřížky:

  ```bash
  wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-grid.run

  sudo ./NVIDIA-Linux-x86_64-grid.run
  ``` 

6. Pokud se dotaz, zda chcete spustit nástroj nvidia xconfig aktualizovat vaše X konfigurační soubor, vyberte **Ano**.

7. Po dokončení instalace, zkopírujte do nové gridd.conf soubor v umístění/etc/nvidia//etc/nvidia/gridd.conf.template

  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```

8. Přidejte následující `/etc/nvidia/gridd.conf`:
 
  ```
  IgnoreSP=TRUE
  ```
9. Restartujte virtuální počítač a přejděte k ověření instalace.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS nebo Red Hat Enterprise Linux 

1. Aktualizace jádra a DKMS.
 
  ```bash  
  sudo yum update
 
  sudo yum install kernel-devel
 
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
  sudo yum install dkms
  ```

2. Zakážete Nouveau ovladač jádra, který není kompatibilní s NVIDIA ovladače. (Použijte pouze NVIDIA ovladač na virtuálních počítačích vs.) Chcete-li to provést, vytvořte soubor v `/etc/modprobe.d `s názvem `nouveau.conf` s tímto obsahem:

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```
 
3. Restartovat virtuální počítač, připojte se znovu a nainstalujte si nejnovější verzi [integrační služby Linuxu pro Hyper-V a Azure](https://www.microsoft.com/download/details.aspx?id=55106).
 
  ```bash
  wget https://aka.ms/lis

  tar xvzf lis

  cd LISISO

  sudo ./install.sh

  sudo reboot

  ```
 
4. Znovu se připojte k virtuální počítač a spustit `lspci` příkaz. Ověřte, zda karta NVIDIA M60 nebo karty jsou viditelné jako PCI zařízení.
 
5. Stáhněte a nainstalujte ovladač mřížky:

  ```bash
  wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-grid.run

  sudo ./NVIDIA-Linux-x86_64-grid.run
  ``` 
6. Pokud se dotaz, zda chcete spustit nástroj nvidia xconfig aktualizovat vaše X konfigurační soubor, vyberte **Ano**.

7. Po dokončení instalace, zkopírujte do nové gridd.conf soubor v umístění/etc/nvidia//etc/nvidia/gridd.conf.template
  
  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```
  
8. Přidejte následující `/etc/nvidia/gridd.conf`:
 
  ```
  IgnoreSP=TRUE
  ```
9. Restartujte virtuální počítač a přejděte k ověření instalace.

### <a name="verify-driver-installation"></a>Ověření instalace ovladačů


K dotazování na GPU zařízení stav, SSH pro virtuální počítač a spusťte [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface) pomocí ovladače nainstalovaný nástroj příkazového řádku. 

Pokud je nainstalovaný ovladač, zobrazí se výstup podobný následujícímu. Všimněte si, že **GPU Util** ukazuje 0 %, pokud aktuálně používáte zatížení grafického procesoru na virtuálním počítači. Verze ovladače a GPU podrobnosti se může lišit od těch vidět.

![Stav zařízení NVIDIA](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11 serveru
Pokud budete potřebovat X11 serveru pro vzdálená připojení na virtuální počítač vs, [x11vnc](http://www.karlrunge.com/x11vnc/) se doporučuje, protože umožňuje hardwarovou akceleraci grafiky. BusID M60 zařízení musí ručně přidat do souboru xconfig (`etc/X11/xorg.conf` na Ubuntu 16.04 LTS, `/etc/X11/XF86config` na CentOS 7.3 nebo Red Hat Enterprise Server 7.3). Přidat `"Device"` části podobný následujícímu:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "your-BusID:0:0:0"
EndSection
```
 
Kromě toho aktualizovat vaše `"Screen"` části k použití tohoto zařízení.
 
Desetinné BusID můžete najít spuštěním

```bash
echo $((16#`/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1`))
```
 
BusID lze změnit, pokud virtuální počítač získá znovu přidělit, nebo restartovat. Proto můžete chtít vytvořit skript určený k aktualizaci BusID v X11 konfigurace, pokud je virtuální počítač restartovat. Můžete například vytvořit skript s názvem `busidupdate.sh` (nebo si vybrat jiný název) s tímto obsahem:

```bash 
#!/bin/bash
BUSID=$((16#`/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1`))

if grep -Fxq "${BUSID}" /etc/X11/XF86Config; then     echo "BUSID is matching"; else   echo "BUSID changed to ${BUSID}" && sed -i '/BusID/c\    BusID          \"PCI:0@'${BUSID}':0:0:0\"' /etc/X11/XF86Config; fi
```

Pak vytvořte záznam pro váš skript aktualizace v `/etc/rc.d/rc3.d` tak skript je vyvolána jako kořenová na spuštění.

## <a name="troubleshooting"></a>Řešení potíží

* Můžete nastavit pomocí režimu trvalost `nvidia-smi` tak výstup příkazu je rychlejší, když potřebujete karty dotazu. Nastavení režimu trvalost, provést `nvidia-smi -pm 1`. Všimněte si, že pokud restartování virtuálního počítače s nastavením režimu Vyčkat. Vždy můžete skript režim provést při spuštění.

## <a name="next-steps"></a>Další postup

* K zachycení bitové kopie virtuálního počítače s Linuxem s vaší nainstalované ovladače NVIDIA, najdete v části [generalize a zachycení virtuální počítač s Linuxem](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
