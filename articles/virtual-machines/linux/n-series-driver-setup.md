---
title: Azure instalace ovladačů v instancích N-series pro Linux | Dokumentace Microsoftu
description: Jak nastavit ovladače NVIDIA GPU pro virtuální počítače řady N-series s Linuxem v Azure
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
ms.date: 06/19/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c8f043fdcaa7554d73be6ac3928a37630baab845
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38630417"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Instalace ovladačů NVIDIA GPU na virtuálních počítačích řady N-series s Linuxem

Abyste mohli využívat výhod GPU virtuální počítače Azure řady N-series s Linuxem, musí být nainstalována ovladačů NVIDIA GPU. [Rozšíření ovladače GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) nainstaluje vhodných ovladačů NVIDIA CUDA nebo mřížky na virtuální počítač řady N-series. Instalovat ani spravovat rozšíření pomocí webu Azure portal nebo nástrojů, jako jsou šablony Azure Resource Manageru nebo rozhraní příkazového řádku Azure. Zobrazit [dokumentaci rozšíření ovladače GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) podporovaných distribucích a kroky nasazení.

Pokud se rozhodnete pro instalaci ovladače GPU ručně, tento článek obsahuje podporované distribuce, ovladačů a postup instalace a ověření. Informace o nastavení ruční ovladač je také k dispozici pro [virtuální počítače s Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Specifikace, kapacity úložiště a podrobných informací o discích virtuálních počítačů řady N-series najdete v části [velikosti virtuálního počítače s Linuxem GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>Instalace ovladačů CUDA na virtuální počítače řady N-series

Tady jsou kroky pro instalaci ovladače CUDA z Toolkit NVIDIA CUDA na virtuálních počítačích řady N-series. 


Vývojáře v C a C++ můžete volitelně nainstalovat úplnou sadu nástrojů k vytváření aplikací – hardwarově akcelerovanou. Další informace najdete v tématu [Průvodce instalací CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

Instalace ovladačů CUDA, vytvořte připojení SSH ke všem virtuálním počítačům. Pokud chcete ověřit, zda má systém podporující CUDA GPU, spusťte následující příkaz:

```bash
lspci | grep -i NVIDIA
```
Zobrazí se výstup jako v následujícím příkladu (zobrazuje se karty, která se NVIDIA Tesla K80):

![výstup příkazu lspci](./media/n-series-driver-setup/lspci.png)

Potom spusťte instalaci příkazů specifických pro vaši distribuci.

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

2. Pokud chcete volitelně nainstalovat úplnou sadu nástrojů CUDA, zadejte:

  ```bash
  sudo apt-get install cuda
  ```

3. Restartujte virtuální počítač a přejděte k ověření instalace.

#### <a name="cuda-driver-updates"></a>Aktualizace ovladačů CUDA

Doporučujeme pravidelně aktualizovat CUDA ovladače po nasazení.

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
 
3. Znovu připojit k virtuálnímu počítači a pokračovat v instalaci pomocí následujících příkazů:

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

4. Pokud chcete volitelně nainstalovat úplnou sadu nástrojů CUDA, zadejte:

  ```bash
  sudo yum install cuda
  ```

5. Restartujte virtuální počítač a přejděte k ověření instalace.

### <a name="verify-driver-installation"></a>Ověření instalace ovladače

K dotazování stavu zařízení GPU SSH k virtuálnímu počítači a spusťte [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface) s ovladačem nainstalovaný nástroj příkazového řádku. 

Pokud je nainstalovaný ovladač, zobrazí se výstup podobný následujícímu. Všimněte si, že **využití GPU** ukazuje 0 %, pokud máte právě spuštěnou úlohu GPU na virtuálním počítači. Podrobnosti o GPU a verze ovladače se může lišit od těch, které jsou zobrazeny.

![Stav zařízení NVIDIA](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>Připojení k síti přístup RDMA

Připojení k síti přístup RDMA dá nastavit pro virtuální počítače řady N-series s podporou RDMA, jako je NC24r nasazené ve stejné skupině dostupnosti nebo v jediné skupiny umístění ve škálovací sadě virtuálních počítačů. Sítě RDMA podporuje rozhraní MPI (Message Passing Interface) provozu pro aplikace s technologií Intel MPI 5.x nebo novější. Následují další požadavky:

### <a name="distributions"></a>Distribuce

Nasazení podporující RDMA virtuálních počítačů řady N-series z některou k imagí v Tržišti Azure Marketplace, který podporuje připojení RDMA na virtuálních počítačích řady N-series:
  
* **Ubuntu 16.04 LTS** – konfigurace ovladače RDMA na virtuálním počítači a registrace s technologií Intel stáhnout Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **Založené na centOS 7.4 HPC** -ovladače RDMA a Intel MPI 5.1 jsou nainstalovány ve virtuálním počítači.

## <a name="install-grid-drivers-on-nv-series-vms"></a>Instalace ovladačů mřížky na virtuálních počítačích řada NV

Instalace ovladačů NVIDIA GRID na NV-series virtuálních počítačů, vytvořte připojení SSH ke všem virtuálním počítačům a postupujte podle kroků pro vaši Linuxovou distribuci. 

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Spustit `lspci` příkazu. Ověřte, že karty NVIDIA M60 nebo karty se zobrazují jako PCI zařízení.

2. Nainstalujte aktualizace.

  ```bash
  sudo apt-get update

  sudo apt-get upgrade -y

  sudo apt-get dist-upgrade -y

  sudo apt-get install build-essential ubuntu-desktop -y
  ```
3. Zakážete Nouveau ovladač jádra, která není kompatibilní s ovladač NVIDIA. (Použijte pouze ovladač NVIDIA na virtuálních počítačích NV.) Chcete-li to provést, vytvořte soubor v `/etc/modprobe.d `s názvem `nouveau.conf` s následujícím obsahem:

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```


4. Restartujte virtuální počítač a znovu připojit. Konec X serveru:

  ```bash
  sudo systemctl stop lightdm.service
  ```

5. Stáhněte a nainstalujte ovladač mřížky:

  ```bash
  wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-grid.run

  sudo ./NVIDIA-Linux-x86_64-grid.run
  ``` 

6. Pokud se dotaz, zda chcete spustit nástroj nvidia xconfig a aktualizovat vaše konfigurační soubor X, vyberte **Ano**.

7. Po dokončení instalace, zkopírujte do nové gridd.conf souboru v umístění/etc/nvidia//etc/nvidia/gridd.conf.template

  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```

8. Přidejte následující text do `/etc/nvidia/gridd.conf`:
 
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

2. Zakážete Nouveau ovladač jádra, která není kompatibilní s ovladač NVIDIA. (Použijte pouze ovladač NVIDIA na virtuálních počítačích NV.) Chcete-li to provést, vytvořte soubor v `/etc/modprobe.d `s názvem `nouveau.conf` s následujícím obsahem:

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```
 
3. Restartujte virtuální počítač, opětovné připojení a nainstalujte nejnovější [integrační služby Linuxu pro Hyper-V a Azure](https://www.microsoft.com/download/details.aspx?id=55106).
 
  ```bash
  wget https://aka.ms/lis

  tar xvzf lis

  cd LISISO

  sudo ./install.sh

  sudo reboot

  ```
 
4. Znovu připojit k virtuálnímu počítači a spusťte `lspci` příkazu. Ověřte, že karty NVIDIA M60 nebo karty se zobrazují jako PCI zařízení.
 
5. Stáhněte a nainstalujte ovladač mřížky:

  ```bash
  wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-grid.run

  sudo ./NVIDIA-Linux-x86_64-grid.run
  ``` 
6. Pokud se dotaz, zda chcete spustit nástroj nvidia xconfig a aktualizovat vaše konfigurační soubor X, vyberte **Ano**.

7. Po dokončení instalace, zkopírujte do nové gridd.conf souboru v umístění/etc/nvidia//etc/nvidia/gridd.conf.template
  
  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```
  
8. Přidejte následující text do `/etc/nvidia/gridd.conf`:
 
  ```
  IgnoreSP=TRUE
  ```
9. Restartujte virtuální počítač a přejděte k ověření instalace.

### <a name="verify-driver-installation"></a>Ověření instalace ovladače


K dotazování stavu zařízení GPU SSH k virtuálnímu počítači a spusťte [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface) s ovladačem nainstalovaný nástroj příkazového řádku. 

Pokud je nainstalovaný ovladač, zobrazí se výstup podobný následujícímu. Všimněte si, že **využití GPU** ukazuje 0 %, pokud máte právě spuštěnou úlohu GPU na virtuálním počítači. Podrobnosti o GPU a verze ovladače se může lišit od těch, které jsou zobrazeny.

![Stav zařízení NVIDIA](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11 serveru
Pokud budete potřebovat X11 server pro vzdálená připojení k virtuálnímu počítači NV, [x11vnc](http://www.karlrunge.com/x11vnc/) se doporučuje, protože umožňuje hardwarovou akceleraci grafiky. BusID M60 zařízení je nutné ručně přidat do X11 konfigurační soubor (obvykle `etc/X11/xorg.conf`). Přidat `"Device"` části podobný následujícímu:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Kromě toho aktualizovat váš `"Screen"` části k použití tohoto zařízení.
 
Desetinné BusID můžete najít spuštěním

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
BusID lze změnit získá nevyčerpané nebo restartovat virtuální počítač. Proto můžete chtít vytvořit skript pro aktualizaci BusID v X11 konfigurace po restartování virtuálního počítače. Například vytvořit skript s názvem `busidupdate.sh` (nebo jiný název, který zvolíte) s obsahem podobný následujícímu:

```bash 
#!/bin/bash
XCONFIG="/etc/X11/xorg.conf"
OLDBUSID=`awk '/BusID/{gsub(/"/, "", $2); print $2}' ${XCONFIG}`
NEWBUSID=`nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'`

if [[ "${OLDBUSID}" == "${NEWBUSID}" ]] ; then
        echo "NVIDIA BUSID not changed - nothing to do"
else
        echo "NVIDIA BUSID changed from \"${OLDBUSID}\" to \"${NEWBUSID}\": Updating ${XCONFIG}" 
        sed -e 's|BusID.*|BusID          '\"${NEWBUSID}\"'|' -i ${XCONFIG}
fi
```

Vytvořte položku pro váš skript pro aktualizaci v `/etc/rc.d/rc3.d` tak skript je vyvolána jako uživatel root při spuštění.

## <a name="troubleshooting"></a>Řešení potíží

* Můžete nastavit pomocí režimu trvalosti `nvidia-smi` tak, že výstup příkazu je rychlejší, když budete chtít karty dotazu. Nastavení režimu trvalosti, spusťte `nvidia-smi -pm 1`. Všimněte si, že pokud virtuální počítač nerestartuje, nastavení režimu zmizí. Můžete používat vždy skripty pro nastavení režimu provést při spuštění.

## <a name="next-steps"></a>Další postup

* Zachycení image virtuálního počítače s Linuxem pomocí nainstalovaných ovladačů NVIDIA, najdete v článku [jak zachytit virtuální počítač s Linuxem a generalizace](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
