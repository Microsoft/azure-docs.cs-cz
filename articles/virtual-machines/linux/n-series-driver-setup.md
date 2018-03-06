---
title: "Instalace nástroje Azure ovladač N-series pro Linux | Microsoft Docs"
description: "Jak nastavit NVIDIA GPU ovladače pro N-series virtuální počítače s Linuxem v Azure"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 201734661873c7ac7f7a5dd710009eb324cedc86
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Instalace ovladačů NVIDIA GPU v N-series virtuální počítače se systémem Linux

Abyste mohli využívat možnosti GPU Azure N-series virtuální počítače se systémem Linux, nainstalujte podporované NVIDIA grafické ovladače. Tento článek obsahuje kroky instalace ovladačů po nasadit virtuální počítač s N-series. Informace o instalaci ovladačů je také k dispozici pro [virtuálních počítačů Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Virtuální počítač N-series specifikace, kapacity úložiště a disku podrobnosti najdete v tématu [velikosti virtuálních počítačů Linux GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-for-nc-ncv2-ncv3-and-nd-series-vms"></a>Instalace ovladačů CUDA NC, NCv2, NCv3 a virtuální počítače a series

Tady jsou kroky pro instalaci ovladače NVIDIA z nástrojů CUDA NVIDIA na virtuálních počítačích N-series. 

Jazyk C a C++ vývojáři Volitelně můžete nainstalovat úplnou sadu nástrojů k vytváření aplikací GPU accelerated. Další informace najdete v tématu [Průvodce instalací CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

> [!NOTE]
> Tady jsou aktuální v době publikace k dispozici odkazy stahování ovladačů CUDA. Nejnovější ovladače CUDA, najdete v článku [NVIDIA](https://developer.nvidia.com/cuda-zone) webu.
>

K instalaci nástrojů CUDA, zkontrolujte připojení SSH pro každý virtuální počítač. Pokud chcete ověřit, že systém má podporující CUDA grafického procesoru, spusťte následující příkaz:

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

2. Install the latest Linux Integration Services for Hyper-V.

  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.4.tar.gz
 
  tar xvzf lis-rpms-4.2.4.tar.gz
 
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

Síťové připojení RDMA se dá nastavit na virtuálních počítačích podporující RDMA N-series, jako je NC24r nasazené ve stejné sadě dostupnosti. Síť RDMA podporuje rozhraní MPI (Message Passing) provozu pro aplikace spuštěné s Intel MPI 5.x nebo novější. Následují další požadavky:

### <a name="distributions"></a>Distribuce

Nasazení podporující RDMA N-series virtuální počítače z bitové kopie v Azure Marketplace, která podporuje připojení RDMA na virtuálních počítačích N-series:
  
* **Ubuntu 16.04 LTS** – konfigurace ovladače RDMA na virtuálním počítači a registrace s Intel ke stažení Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

> [!NOTE]
> Na základě centOS obrázky HPC není aktuálně vhodné pro připojení RDMA na virtuálních počítačích N-series. RDMA není podporována na nejnovější CentOS 7.4 jádra, která podporuje NVIDIA grafickými procesory.
> 


## <a name="install-grid-drivers-for-nv-series-vms"></a>Instalace ovladačů mřížky pro virtuální počítače vs series

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
  wget -O NVIDIA-Linux-x86_64-384.111-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-384.111-grid.run

  sudo ./NVIDIA-Linux-x86_64-384.111-grid.run
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
 
3. Restartovat virtuální počítač, připojte se znovu a nainstalujte nejnovější integrační služby Linuxu pro Hyper-V:
 
  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.4.tar.gz

  tar xvzf lis-rpms-4.2.4.tar.gz

  cd LISISO

  sudo ./install.sh

  sudo reboot

  ```
 
4. Znovu se připojte k virtuální počítač a spustit `lspci` příkaz. Ověřte, zda karta NVIDIA M60 nebo karty jsou viditelné jako PCI zařízení.
 
5. Stáhněte a nainstalujte ovladač mřížky:

  ```bash
  wget -O NVIDIA-Linux-x86_64-384.111-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-384.111-grid.run

  sudo ./NVIDIA-Linux-x86_64-384.111-grid.run
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
 
BusID můžete najít spuštěním

```bash
/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1
```
 
BusID lze změnit, pokud virtuální počítač získá znovu přidělit, nebo restartovat. Proto můžete chtít použít skript k aktualizaci BusID v X11 konfigurace, pokud je virtuální počítač restartovat. Příklad:

```bash 
#!/bin/bash
BUSID=$((16#`/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1`))

if grep -Fxq "${BUSID}" /etc/X11/XF86Config; then     echo "BUSID is matching"; else   echo "BUSID changed to ${BUSID}" && sed -i '/BusID/c\    BusID          \"PCI:0@'${BUSID}':0:0:0\"' /etc/X11/XF86Config; fi
```

Tento soubor nelze vyvolat jako kořenová na spouštěcí tak, že vytvoříte položku pro něj v `/etc/rc.d/rc3.d`.

## <a name="troubleshooting"></a>Řešení potíží

* Můžete nastavit pomocí režimu trvalost `nvidia-smi` tak výstup příkazu je rychlejší, když potřebujete karty dotazu. Nastavení režimu trvalost, provést `nvidia-smi -pm 1`. Všimněte si, že pokud restartování virtuálního počítače s nastavením režimu Vyčkat. Vždy můžete skript režim provést při spuštění.

## <a name="next-steps"></a>Další postup

* K zachycení bitové kopie virtuálního počítače s Linuxem s vaší nainstalované ovladače NVIDIA, najdete v části [generalize a zachycení virtuální počítač s Linuxem](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
