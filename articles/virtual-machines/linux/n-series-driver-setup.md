---
title: Nastavení ovladače GPU řady Azure N-Series pro Linux
description: Postup nastavení ovladačů NVIDIA GPU pro virtuální počítače řady N-Series se systémem Linux v Azure
services: virtual-machines-linux
author: vikancha-MSFT
ms.service: virtual-machines-linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: vikancha
ms.openlocfilehash: 9b6e752f8352db565239aba4a990752b1c397f5f
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517255"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Instalace ovladačů NVIDIA GPU pro virtuální počítače řady N-Series se systémem Linux

Pokud chcete využívat možnosti GPU pro virtuální počítače řady Azure N-Series, které využívají grafické procesory NVIDIA, musíte nainstalovat ovladače GPU NVIDIA. [Rozšíření ovladače NVIDIA GPU](../extensions/hpccompute-gpu-linux.md) nainstaluje vhodné ovladače NVIDIA CUDA nebo Grid na virtuální počítač řady N-Series. Nainstalujte nebo spravujte rozšíření pomocí Azure Portal nebo nástrojů, jako je Azure CLI nebo šablony Azure Resource Manager. Podporované distribuce a kroky nasazení najdete v [dokumentaci k rozšíření ovladače GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) .

Pokud se rozhodnete nainstalovat ovladače NVIDIA GPU ručně, najdete v tomto článku Podporované distribuce, ovladače a postup instalace a ověření. Pro [virtuální počítače s Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)jsou k dispozici také informace o ruční instalaci ovladače.

Informace o specifikacích virtuálních počítačů řady N-Series, kapacitách úložiště a podrobnostech o disku najdete v tématu [velikosti virtuálních počítačů se systémem GPU Linux](../sizes-gpu.md?toc=/azure/virtual-machines/linux/toc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>Nainstalovat ovladače CUDA pro virtuální počítače řady N-Series

Tady jsou kroky pro instalaci ovladačů CUDA ze sady NVIDIA CUDA Toolkit na virtuálních počítačích řady N-Series. 


Vývojáři jazyka C a C++ můžou volitelně nainstalovat úplnou sadu nástrojů pro vytváření aplikací akcelerovaných GPU. Další informace najdete v příručce pro [instalaci CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

Chcete-li nainstalovat ovladače CUDA, vytvořte připojení SSH ke každému virtuálnímu počítači. Pokud chcete ověřit, že systém má grafický procesor s podporou CUDA, spusťte následující příkaz:

```bash
lspci | grep -i NVIDIA
```
Zobrazí se výstup podobný následujícímu příkladu (zobrazuje se karta NVIDIA Tesla K80):

![výstup příkazu lspci](./media/n-series-driver-setup/lspci.png)

Pak spusťte instalační příkazy specifické pro vaši distribuci.

### <a name="ubuntu"></a>Ubuntu 

1. Stáhněte si a nainstalujte ovladače CUDA z webu NVIDIA. Například pro Ubuntu 16,04 LTS:
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

2. Pokud chcete volitelně nainstalovat úplnou CUDA sadu nástrojů, zadejte:

   ```bash
   sudo apt-get install cuda
   ```

3. Restartujte virtuální počítač a pokračujte v instalaci ověření.

#### <a name="cuda-driver-updates"></a>Aktualizace ovladačů CUDA

Doporučujeme, abyste po nasazení pravidelně aktualizovali CUDA ovladače.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS nebo Red Hat Enterprise Linux

1. Aktualizujte jádro (doporučeno). Pokud se rozhodnete neaktualizovat jádro, ujistěte se, že verze nástroje `kernel-devel` a `dkms` jsou vhodné pro vaše jádro.

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
 
3. Znovu se připojte k virtuálnímu počítači a pokračujte v instalaci pomocí následujících příkazů:

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

4. Pokud chcete volitelně nainstalovat úplnou CUDA sadu nástrojů, zadejte:

   ```bash
   sudo yum install cuda
   ```

5. Restartujte virtuální počítač a pokračujte v instalaci ověření.

### <a name="verify-driver-installation"></a>Ověřit instalaci ovladače

Pokud chcete zadat dotaz na stav zařízení GPU, SSH k virtuálnímu počítači a spustit nástroj příkazového řádku [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) , který je nainstalovaný s ovladačem. 

Pokud je ovladač nainstalovaný, zobrazí se výstup podobný následujícímu. Všimněte si, že **GPU-util** zobrazuje 0%, pokud momentálně na virtuálním počítači nespouštíte úlohu GPU. Podrobnosti o verzi ovladače a GPU se mohou lišit od zobrazených.

![Stav zařízení NVIDIA](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>Připojení k síti RDMA

Připojení k síti RDMA je možné povolit u virtuálních počítačů řady N-Series s podporou RDMA, jako je NC24r nasazených ve stejné skupině dostupnosti nebo v jedné skupině umístění v sadě škálování virtuálního machiine (VM). Síť RDMA podporuje provoz rozhraní MPI (Message Passing Interface) pro aplikace běžící s Intel MPI 5. x nebo novější verzí. Další požadavky jsou následující:

### <a name="distributions"></a>Distribuce

Nasaďte virtuální počítače s podporou RDMA řady N-Series z jedné bitové kopie v Azure Marketplace, která podporuje připojení RDMA na virtuálních počítačích řady N-series:
  
* **Ubuntu 16,04 LTS** – NAKONFIGURUJTE na virtuálním počítači ovladače RDMA a zaregistrujte se pomocí Intel pro stažení Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* Na virtuálním počítači jsou nainstalované ovladače **HPC-based 7,4 CentOS HPC** -RDMA a Intel MPI 5,1.

* **CentOS HPC** -CentOS-HPC 7,6 a novější (pro SKU, kde se InfiniBand podporuje přes SR-IOV). Tyto image mají Mellanox OFED a knihovny MPI, které jsou předem nainstalované.

> [!NOTE]
> Karty CX3-Pro jsou podporovány pouze prostřednictvím LTS verzí Mellanox OFED. Použijte LTS Mellanox OFED verze (4.9-0.1.7.0) na virtuálních počítačích řady N-Series s kartami ConnectX3-Pro. Další informace najdete v tématu [ovladače pro Linux](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed).
>
> Některé z nejnovějších Azure Marketplace imagí HPC mají také Mellanox OFED 5,1 a novější, které nepodporují ConnectX3-Pro karty. Než ho použijete na virtuálních počítačích s ConnectX3-Pro kartami, v imagi HPC ověřte verzi OFED Mellanox.
>
> Následující obrázky jsou nejnovější bitové kopie CentOS-HPC, které podporují ConnectX3-Pro karty:
>
> - OpenLogic: CentOS-HPC: 7.6:7.6.2020062900
> - OpenLogic: CentOS-HPC: 7_6gen2:7.6.2020062901
> - OpenLogic: CentOS-HPC: 7.7:7.7.2020062600
> - OpenLogic: CentOS-HPC: 7_7-Gen2:7.7.2020062601
> - OpenLogic: CentOS-HPC: 8_1:8.1.2020062400
> - OpenLogic: CentOS-HPC: 8_1-Gen2:8.1.2020062401
>

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>Instalace ovladačů mřížky na virtuálních počítačích NV nebo NVv3-Series

Chcete-li nainstalovat ovladače pro rozhraní NVIDIA GRID na virtuální počítače NV nebo NVv3-Series, vytvořte připojení SSH ke každému virtuálnímu počítači a postupujte podle kroků pro distribuci systému Linux. 

### <a name="ubuntu"></a>Ubuntu 

1. Spusťte příkaz `lspci`. Ověřte, že karta nebo karty NVIDIA M60 jsou viditelné jako zařízení PCI.

2. Nainstalujte aktualizace.

   ```bash
   sudo apt-get update

   sudo apt-get upgrade -y

   sudo apt-get dist-upgrade -y

   sudo apt-get install build-essential ubuntu-desktop -y
   
   sudo apt-get install linux-azure -y
   ```
3. Zakažte ovladač jádra Nouveau, který je nekompatibilní s ovladačem NVIDIA. (Použijte pouze ovladač NVIDIA na virtuálních počítačích NV nebo NVv2.) Chcete-li to provést, vytvořte soubor `/etc/modprobe.d` `nouveau.conf` s názvem s následujícím obsahem:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```


4. Restartujte virtuální počítač a znovu se připojte. Konec X serveru:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Stažení a instalace ovladače mřížky:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. Když se zobrazí dotaz, zda chcete spustit nástroj NVIDIA-xconfig pro aktualizaci konfiguračního souboru X, vyberte **Ano**.

7. Po dokončení instalace zkopírujte/etc/NVIDIA/GRIDD.conf.template do nového souboru grided. conf v umístění/etc/NVIDIA/

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Přidejte následující `/etc/nvidia/gridd.conf` :
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. Z tohoto pole odeberte následující z `/etc/nvidia/gridd.conf` , pokud je k dispozici:
 
   ```
   FeatureType=0
   ```
10. Restartujte virtuální počítač a pokračujte v instalaci ověření.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS nebo Red Hat Enterprise Linux 

1. Aktualizujte jádro a DKMS (doporučeno). Pokud se rozhodnete neaktualizovat jádro, ujistěte se, že verze nástroje `kernel-devel` a `dkms` jsou vhodné pro vaše jádro.
 
   ```bash  
   sudo yum update
 
   sudo yum install kernel-devel
 
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
   sudo yum install dkms
   
   sudo yum install hyperv-daemons
   ```

2. Zakažte ovladač jádra Nouveau, který je nekompatibilní s ovladačem NVIDIA. (Použijte pouze ovladač NVIDIA na virtuálních počítačích NV nebo NV2.) Chcete-li to provést, vytvořte soubor `/etc/modprobe.d` `nouveau.conf` s názvem s následujícím obsahem:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```
 
3. Restartujte virtuální počítač, znovu se připojte a nainstalujte nejnovější [integrační služby Linux pro Hyper-V a Azure](https://www.microsoft.com/download/details.aspx?id=55106).
 
   ```bash
   wget https://aka.ms/lis

   tar xvzf lis

   cd LISISO

   sudo ./install.sh

   sudo reboot

   ```
 
4. Znovu se připojte k virtuálnímu počítači a spusťte `lspci` příkaz. Ověřte, že karta nebo karty NVIDIA M60 jsou viditelné jako zařízení PCI.
 
5. Stažení a instalace ovladače mřížky:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. Když se zobrazí dotaz, zda chcete spustit nástroj NVIDIA-xconfig pro aktualizaci konfiguračního souboru X, vyberte **Ano**.

7. Po dokončení instalace zkopírujte/etc/NVIDIA/GRIDD.conf.template do nového souboru grided. conf v umístění/etc/NVIDIA/
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Přidejte následující `/etc/nvidia/gridd.conf` :
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. Z tohoto pole odeberte následující z `/etc/nvidia/gridd.conf` , pokud je k dispozici:
 
   ```
   FeatureType=0
   ```
10. Restartujte virtuální počítač a pokračujte v instalaci ověření.


### <a name="verify-driver-installation"></a>Ověřit instalaci ovladače


Pokud chcete zadat dotaz na stav zařízení GPU, SSH k virtuálnímu počítači a spustit nástroj příkazového řádku [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) , který je nainstalovaný s ovladačem. 

Pokud je ovladač nainstalovaný, zobrazí se výstup podobný následujícímu. Všimněte si, že **GPU-util** zobrazuje 0%, pokud momentálně na virtuálním počítači nespouštíte úlohu GPU. Podrobnosti o verzi ovladače a GPU se mohou lišit od zobrazených.

![Snímek obrazovky, který zobrazuje výstup při dotazování na stav zařízení GPU](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>Server X11
Pokud potřebujete server X11 pro vzdálené připojení k virtuálnímu počítači NV nebo NVv2, doporučuje se [x11vnc](http://www.karlrunge.com/x11vnc/) , protože umožňuje hardwarovou akceleraci grafiky. BusID zařízení M60 musí být ručně přidáno do konfiguračního souboru X11 (obvykle `etc/X11/xorg.conf` ). Přidejte `"Device"` oddíl podobný následujícímu:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Dále aktualizujte `"Screen"` oddíl, aby používal toto zařízení.
 
Desítkové BusID lze najít spuštěním

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
BusID se může změnit, když se virtuální počítač znovu přidělí nebo restartuje. Proto možná budete chtít vytvořit skript, který aktualizuje BusID v konfiguraci X11 při restartování virtuálního počítače. Například vytvořte skript s názvem `busidupdate.sh` (nebo jiný název, který zvolíte) s obsahem podobným následujícímu:

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

Pak vytvořte záznam pro skript pro aktualizaci v nástroji, `/etc/rc.d/rc3.d` aby se skript vyvolal jako kořenový při spuštění.

## <a name="troubleshooting"></a>Řešení potíží

* Můžete nastavit režim trvalosti pomocí `nvidia-smi` , takže výstup příkazu je rychlejší, když potřebujete zadat dotaz na karty. Chcete-li nastavit režim trvalosti, spusťte příkaz `nvidia-smi -pm 1` . Všimněte si, že pokud se virtuální počítač restartuje, nastavení režimu zmizí. Vždy můžete skriptovat nastavení režimu, které se spustí při spuštění.
* Pokud jste ovladače NVIDIA CUDA aktualizovali na nejnovější verzi a zjistíte, že připojení RDMA už nefunguje, [přeinstalujte ovladače RDMA pro opětovné](#rdma-network-connectivity) vytvoření tohoto připojení. 

## <a name="next-steps"></a>Další kroky

* Pokud chcete zachytit image virtuálního počítače se systémem Linux pomocí nainstalovaných ovladačů NVIDIA, přečtěte si téma [postup generalizace a zachycení virtuálního počítače se systémem Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
