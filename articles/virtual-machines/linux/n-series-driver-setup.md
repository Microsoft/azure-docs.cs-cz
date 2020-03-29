---
title: Nastavení ovladače GPU řady Azure N pro Linux
description: Jak nastavit ovladače NVIDIA GPU pro virtuální počítače řady N se systémem Linux v Azure
services: virtual-machines-linux
author: cynthn
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: cynthn
ms.openlocfilehash: b424361f318504f96a57ee67722e725fbafc6561
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944561"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Instalace ovladačů NVIDIA GPU na virtuální počítače řady N se systémem Linux

Chcete-li využít funkce GPU virtuálních zařízení řady Azure N se systémem Linux, musí být nainstalovány ovladače GPU NVIDIA. [Rozšíření ovladače GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) nainstaluje příslušné ovladače NVIDIA CUDA nebo GRID na virtuální počítač řady N. Nainstalujte nebo spravujte rozšíření pomocí portálu Azure nebo nástrojů, jako jsou šablony Azure CLI nebo Azure Resource Manager. Podporované distribuce a kroky nasazení naleznete v [dokumentaci k rozšíření ovladače GPU NVIDIA.](../extensions/hpccompute-gpu-linux.md)

Pokud se rozhodnete nainstalovat ovladače GPU ručně, tento článek obsahuje podporované distribuce, ovladače a kroky instalace a ověření. Informace o ručním nastavení ovladače jsou k dispozici také pro [virtuální počítače se systémem Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Specifikace virtuálních počítačů řady N, kapacity úložiště a podrobnosti o disku najdete [v tématu velikosti virtuálních počítačů GPU Linux](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>Instalace ovladačů CUDA na virtuální chod řady N

Zde jsou kroky k instalaci ovladačů CUDA z sady nástrojů NVIDIA CUDA na virtuálních počítačích řady N. 


Vývojáři c a c++ mohou volitelně nainstalovat úplnou sadu nástrojů a vytvářet aplikace akcelerované gpu. Další informace naleznete v [instalační příručce CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

Chcete-li nainstalovat ovladače CUDA, vytvořte připojení SSH ke každému virtuálnímu virtuálnímu zařízení. Chcete-li ověřit, zda má systém grafický procesor podporující cuda, spusťte následující příkaz:

```bash
lspci | grep -i NVIDIA
```
Uvidíte výstup podobný následujícímu příkladu (zobrazující kartu NVIDIA Tesla K80):

![Výstup příkazu lspci](./media/n-series-driver-setup/lspci.png)

Potom spusťte instalační příkazy specifické pro vaši distribuci.

### <a name="ubuntu"></a>Ubuntu 

1. Stáhněte a nainstalujte ovladače CUDA z webu NVIDIA. Například pro Ubuntu 16.04 LTS:
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb

   wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

   sudo dpkg -i /tmp/${CUDA_REPO_PKG}

   sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo apt-get update

   sudo apt-get install cuda-drivers

   ```

   Instalace může trvat několik minut.

2. Chcete-li volitelně nainstalovat kompletní sadu nástrojů CUDA, zadejte:

   ```bash
   sudo apt-get install cuda
   ```

3. Restartujte virtuální počítač a pokračujte ověřením instalace.

#### <a name="cuda-driver-updates"></a>Cuda aktualizace ovladačů

Doporučujeme pravidelně aktualizovat ovladače CUDA po nasazení.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS nebo Red Hat Enterprise Linux

1. Aktualizujte jádro (doporučeno). Pokud se rozhodnete jádro neaktualizovat, ujistěte `kernel-devel` se, že verze jádra jsou vhodné a `dkms` jsou pro vaše jádro vhodné.

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
 
3. Znovu se připojte k virtuálnímu virtuálnímu virtuálnímu zařízení a pokračujte v instalaci pomocí následujících příkazů:

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

   sudo yum install dkms

   CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm

   wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   Instalace může trvat několik minut. 

4. Chcete-li volitelně nainstalovat kompletní sadu nástrojů CUDA, zadejte:

   ```bash
   sudo yum install cuda
   ```

5. Restartujte virtuální počítač a pokračujte ověřením instalace.

### <a name="verify-driver-installation"></a>Ověření instalace ovladače

Chcete-li dotaz na stav zařízení GPU, SSH na Virtuální mě a spustit [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) příkazový řádek nástroj nainstalovaný s ovladačem. 

Pokud je nainstalován ovladač, zobrazí se výstup podobný následujícímu. Všimněte si, že **GPU-Util** zobrazuje 0 %, pokud aktuálně spouštějíte úlohu GPU na virtuálním počítači. Vaše verze ovladače a podrobnosti gpu se mohou lišit od zobrazených verzí.

![Stav zařízení NVIDIA](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>Připojení k síti RDMA

Připojení k síti RDMA lze povolit na virtuálních počítačích řady N s podporou RDMA, jako je NC24r nasazený ve stejné skupině dostupnosti nebo v jedné skupině umístění ve škálovací sadě virtuálních počítačů. Síť RDMA podporuje přenosy mpi (Message Passing Interface) pro aplikace spuštěné s technologií Intel MPI 5.x nebo novější verzí. Následují další požadavky:

### <a name="distributions"></a>Distribuce

Nasazení virtuálních počítačů N-series s podporou RDMA z jedné z ibi na Azure Marketplace, která podporuje připojení RDMA na virtuálních počítačích řady N:
  
* **Ubuntu 16.04 LTS** - Konfigurace ovladačů RDMA na virtuálním počítači a zaregistrujte se u společnosti Intel ke stažení technologie Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **CentOS založené 7.4 HPC** - RDMA ovladače a Intel MPI 5.1 jsou nainstalovány na virtuálním počítači.

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>Instalace ovladačů GRID na virtuálních počítačích řady NV nebo NVv3

Chcete-li nainstalovat ovladače NVIDIA GRID na virtuální počítače řady NV nebo NVv3, vytvořte připojení SSH ke každému virtuálnímu počítači a postupujte podle pokynů pro distribuci Linuxu. 

### <a name="ubuntu"></a>Ubuntu 

1. Spusťte příkaz `lspci`. Ověřte, zda jsou karty NEBO karty NVIDIA M60 viditelné jako zařízení PCI.

2. Nainstalujte aktualizace.

   ```bash
   sudo apt-get update

   sudo apt-get upgrade -y

   sudo apt-get dist-upgrade -y

   sudo apt-get install build-essential ubuntu-desktop -y
   
   sudo apt-get install linux-azure -y
   ```
3. Zakažte ovladač jádra Nouveau, který není kompatibilní s ovladačem NVIDIA. (Ovladač NVIDIA používejte jenom na virtuálních počítačích NV nebo NVv2.) Chcete-li to provést, `/etc/modprobe.d` `nouveau.conf` vytvořte soubor s názvem s následujícím obsahem:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```


4. Restartujte virtuální počítač a znovu se připojte. Ukončit server X:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Stáhněte a nainstalujte ovladač GRID:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. Až budete dotázáni, zda chcete spustit nástroj nvidia-xconfig pro aktualizaci konfiguračního souboru X, vyberte **Ano**.

7. Po dokončení instalace zkopírujte /etc/nvidia/gridd.conf.template do nového souboru gridd.conf na místě /etc/nvidia/

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Doudá `/etc/nvidia/gridd.conf`následující:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. Pokud je `/etc/nvidia/gridd.conf` k dispozici, odeberte následující:
 
   ```
   FeatureType=0
   ```
10. Restartujte virtuální počítač a pokračujte ověřením instalace.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS nebo Red Hat Enterprise Linux 

1. Aktualizujte jádro a DKMS (doporučeno). Pokud se rozhodnete jádro neaktualizovat, ujistěte `kernel-devel` se, že verze jádra jsou vhodné a `dkms` jsou pro vaše jádro vhodné.
 
   ```bash  
   sudo yum update
 
   sudo yum install kernel-devel
 
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
   sudo yum install dkms
   
   sudo yum install hyperv-daemons
   ```

2. Zakažte ovladač jádra Nouveau, který není kompatibilní s ovladačem NVIDIA. (Ovladač NVIDIA používejte jenom na virtuálních počítačích NV nebo NV2.) Chcete-li to provést, `/etc/modprobe.d` `nouveau.conf` vytvořte soubor s názvem s následujícím obsahem:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```
 
3. Restartujte virtuální počítač, znovu se připojte a nainstalujte nejnovější [služby integrace Linuxu pro Technologie Hyper-V a Azure](https://www.microsoft.com/download/details.aspx?id=55106).
 
   ```bash
   wget https://aka.ms/lis

   tar xvzf lis

   cd LISISO

   sudo ./install.sh

   sudo reboot

   ```
 
4. Znovu se připojte k virtuálnímu virtuálnímu zařízení a spusťte `lspci` příkaz. Ověřte, zda jsou karty NEBO karty NVIDIA M60 viditelné jako zařízení PCI.
 
5. Stáhněte a nainstalujte ovladač GRID:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. Až budete dotázáni, zda chcete spustit nástroj nvidia-xconfig pro aktualizaci konfiguračního souboru X, vyberte **Ano**.

7. Po dokončení instalace zkopírujte /etc/nvidia/gridd.conf.template do nového souboru gridd.conf na místě /etc/nvidia/
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Doudá `/etc/nvidia/gridd.conf`následující:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. Pokud je `/etc/nvidia/gridd.conf` k dispozici, odeberte následující:
 
   ```
   FeatureType=0
   ```
10. Restartujte virtuální počítač a pokračujte ověřením instalace.


### <a name="verify-driver-installation"></a>Ověření instalace ovladače


Chcete-li dotaz na stav zařízení GPU, SSH na Virtuální mě a spustit [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) příkazový řádek nástroj nainstalovaný s ovladačem. 

Pokud je nainstalován ovladač, zobrazí se výstup podobný následujícímu. Všimněte si, že **GPU-Util** zobrazuje 0 %, pokud aktuálně spouštějíte úlohu GPU na virtuálním počítači. Vaše verze ovladače a podrobnosti gpu se mohou lišit od zobrazených verzí.

![Stav zařízení NVIDIA](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11 server
Pokud potřebujete server X11 pro vzdálená připojení k virtuálnímu virtuálnímu zařízení NV nebo NVv2, doporučuje se [x11vnc,](http://www.karlrunge.com/x11vnc/) protože umožňuje hardwarovou akceleraci grafiky. BusID zařízení M60 musí být ručně přidáno do konfiguračního souboru X11 (obvykle). `etc/X11/xorg.conf` Přidejte `"Device"` část podobnou následující:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Kromě toho aktualizujte oddíl, `"Screen"` abyste používali toto zařízení.
 
Desítkové BusID lze nalézt spuštěním

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
BusID můžete změnit, když virtuální počítač získá přerozděleny nebo restartování. Proto můžete chtít vytvořit skript pro aktualizaci BusID v konfiguraci X11 při restartování virtuálního počítače. Můžete například vytvořit `busidupdate.sh` skript s názvem (nebo jiný název, který zvolíte) s obsahem podobným následujícímu:

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

Potom vytvořte položku pro `/etc/rc.d/rc3.d` skript aktualizace, aby byl skript vyvolán jako root při startu.

## <a name="troubleshooting"></a>Řešení potíží

* Režim trvalosti můžete `nvidia-smi` nastavit pomocí tak, aby výstup příkazu byl rychlejší, když potřebujete dotazovat karty. Chcete-li nastavit režim `nvidia-smi -pm 1`trvalosti, spusťte . Všimněte si, že pokud je virtuální mísa restartován, nastavení režimu zmizí. Vždy můžete skript nastavení režimu spustit při spuštění.
* Pokud jste aktualizovali ovladače NVIDIA CUDA na nejnovější verzi a zjistíte, že připojení RDMA již nefunguje, [přeinstalujte ovladače RDMA,](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#rdma-network-connectivity) abyste toto připojení znovu zpracovali. 

## <a name="next-steps"></a>Další kroky

* Pokud chcete zachytit bitovou kopii virtuálního počítače s Linuxem nainstalovanými ovladači NVIDIA, přečtěte si informace o [tom, jak zobecnit a zachytit virtuální počítač s Linuxem](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
