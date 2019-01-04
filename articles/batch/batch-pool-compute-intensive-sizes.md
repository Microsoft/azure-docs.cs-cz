---
title: Použijte virtuální počítače náročné na výpočetní prostředky Azure pomocí služby Batch | Dokumentace Microsoftu
description: Jak využít výhod prostředí HPC a virtuálního počítače s GPU velikosti ve fondech Azure Batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/17/2018
ms.author: lahugh
ms.openlocfilehash: 4663cb467d7d1d864425f4899c829b6618b9547c
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53584458"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>Použít RDMA nebo GPU instancí ve fondech služby Batch

Na spouštění určitých úloh služby Batch, můžete využít určená pro rozsáhlé výpočty velikostí virtuálních počítačů Azure. Příklad:

* Chcete-li spustit více instancí [úlohy MPI](batch-mpi.md), zvolte řady H-series nebo dalších velikostí, které mají síťové rozhraní pro vzdáleného přímého přístupu do paměti (RDMA). Tyto velikosti připojení k síti InfiniBand pro komunikaci mezi uzly, které můžou urychlit aplikací MPI. 

* Pro aplikace spouští CUDA zvolte velikosti N-series, které zahrnují NVIDIA Tesla grafických karet procesor (GPU).

Tento článek obsahuje pokyny a příklady pro použití některé z Azure specializovaných velikostí ve fondech Batch. Specifikace a pozadí naleznete v tématu:

* Vysokovýkonné výpočetní velikosti virtuálních počítačů ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* Velikosti virtuálních počítačů s podporou grafického procesoru ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 

> [!NOTE]
> Některé velikosti virtuálních počítačů nemusí být dostupné v oblastech, kde můžete vytvořit účty Batch. Zkontrolujte, že velikost je k dispozici, najdete v článku [dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/regions/services/) a [výběr velikosti virtuálního počítače pro fond služby Batch](batch-pool-vm-sizes.md).

## <a name="dependencies"></a>Závislosti

RDMA nebo GPU možnosti velikosti náročné na výpočetní prostředky ve službě Batch jsou podporovány pouze v určitých operačních systémů. (Seznam podporovaných operačních systémů je podmnožinou, které jsou podporovány pro virtuální počítače vytvořené v těchto velikostí.) V závislosti na tom, jak vytvořit váš fond služby Batch potřebujete nainstalovat nebo nakonfigurovat další ovladač nebo jiný software na uzlech. Následující tabulka představuje souhrn těchto závislostí. Najdete v odkazovaných článcích podrobnosti. Možnosti konfigurace fondů služby Batch najdete v tématu dále v tomto článku.

### <a name="linux-pools---virtual-machine-configuration"></a>Fondy Linux – konfigurace virtuálního počítače

| Velikost | Schopnost | Operační systémy | Požadovaný software | Nastavení fondu |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r ND24rs NC24rs_v2 NC24rs_v3,<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Ubuntu 16.04 LTS, nebo<br/>Založené na centOS HPC<br/>(Azure Marketplace) | Intel MPI 5<br/><br/>Ovladače Linux RDMA | Komunikace v rámci uzlu povolit, zakázat provedení souběžné úlohy |
| [Síťový adaptér, řada NCv2, NCv3, NDv2 řady](../virtual-machines/linux/n-series-driver-setup.md) | GPU NVIDIA Tesla (se liší podle řady) | Ubuntu 16.04 LTS, nebo<br/>CentOS 7.3 nebo 7.4<br/>(Azure Marketplace) | NVIDIA CUDA nebo sadu nástrojů CUDA ovladače | neuvedeno | 
| [NV NVv2 řady](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS, nebo<br/>CentOS 7.3<br/>(Azure Marketplace) | Ovladače NVIDIA GRID | neuvedeno |
<sup>*</sup>Podporující RDMA velikostí řady N-series také zahrnovat využívá GPU NVIDIA Tesla

### <a name="windows-pools---virtual-machine-configuration"></a>Fondy Windows – konfigurace virtuálního počítače

| Velikost | Schopnost | Operační systémy | Požadovaný software | Nastavení fondu |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r ND24rs NC24rs_v2 NC24rs_v3,<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Windows Server 2016, 2012 R2, nebo<br/>2012 (azure Marketplace) | Microsoft MPI 2012 R2 nebo novější, nebo<br/> Intel MPI 5<br/><br/>Ovladače Windows RDMA | Komunikace v rámci uzlu povolit, zakázat provedení souběžné úlohy |
| [Síťový adaptér, NCv2, NCv3, ND, NDv2 řady](../virtual-machines/windows/n-series-driver-setup.md) | GPU NVIDIA Tesla (se liší podle řady) | Windows Server 2016 nebo <br/>2012 R2 (Azure Marketplace) | NVIDIA CUDA nebo sadu nástrojů CUDA ovladače| neuvedeno | 
| [NV NVv2 řady](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 nebo<br/>2012 R2 (Azure Marketplace) | Ovladače NVIDIA GRID | neuvedeno |
<sup>*</sup>Podporující RDMA velikostí řady N-series také zahrnovat využívá GPU NVIDIA Tesla

### <a name="windows-pools---cloud-services-configuration"></a>Fondy Windows – konfigurace služby Cloud services

> [!NOTE]
> Velikosti řady N-series nejsou podporovány ve fondech Batch s konfigurací cloudové služby.
>

| Velikost | Schopnost | Operační systémy | Požadovaný software | Nastavení fondu |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016, 2012 R2, 2012 nebo<br/>2008 R2 (hostovaného operačního systému řady) | Microsoft MPI 2012 R2 nebo novější, nebo<br/>Intel MPI 5<br/><br/>Ovladače Windows RDMA | Povolení komunikace mezi uzly<br/> Zakázání spuštění souběžných úloh |

## <a name="pool-configuration-options"></a>Možnosti konfigurace fondu

Pokud chcete konfigurovat specializované velikost virtuálního počítače pro váš fond služby Batch, máte několik možností instalace požadovaného softwaru a ovladače:

* Pro fondy v konfiguraci virtuálního počítače, zvolte předkonfigurované [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) image virtuálního počítače, který má ovladače a předinstalovaným softwarem. Příklady: 

  * [Založené na centOS 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) – včetně ovladačů RDMA a Intel MPI 5.1

  * [Virtuální počítač pro datové vědy](../machine-learning/data-science-virtual-machine/overview.md) pro systém Linux nebo Windows – obsahuje ovladače NVIDIA CUDA

  * Linuxové Image pro kontejner úloh služby Batch, které také obsahovat ovladače GPU a RDMA:

    * [CentOS (s ovladače GPU a RDMA) pro kontejner fondech Azure Batch](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Ubuntu Server (ovladače GPU a RDMA) pro kontejner fondech Azure Batch](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* Vytvoření [vlastní image Windows nebo virtuální počítač s Linuxem](batch-custom-images.md) na který jste nainstalovali ovladače, softwaru nebo jiná nastavení, vyžaduje se pro velikost virtuálního počítače. 

* Vytvořte [balíčku aplikace](batch-application-packages.md) z ZIP ovladače nebo instalační program aplikace a nakonfigurovat službu Batch k nasazení balíčku na uzly fondu a instalaci po při vytváření jednotlivých uzlů. Například pokud je balíček aplikace Instalační službu, vytvořit [spouštěcí úkol](batch-api-basics.md#start-task) příkazového řádku k tiché instalaci aplikace na všech uzlech fondu. Zvažte použití balíčku aplikace a fond spouštěcí úkol, pokud vaše úloha závisí na verzi ovladače.

  > [!NOTE] 
  > Spouštěcí úkol musíte spustit s oprávněními zvýšenými (správce) a je nutné čekat na úspěch. Dlouho běžících úloh prodlouží dobu ke zřízení fondu služby Batch.
  >

* [Batch loděnice](https://github.com/Azure/batch-shipyard) automaticky nakonfiguruje ovladače GPU a RDMA transparentně pracovat s kontejnerizované úlohy na Azure Batch. Batch loděnice zcela stanovených pro konfigurační soubory. Existuje mnoho ukázka předpisu konfigurace k dispozici, které umožňují používat úlohy GPU a RDMA, jako [CNTK GPU předpisu](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) který předem nakonfiguruje ovladače GPU na virtuálních počítačích řady N-series a načte softwaru Microsoft Cognitive Toolkit, image Dockeru.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>Příklad: Ovladačů NVIDIA GPU ve fondu virtuálních počítačů Windows síťový adaptér.

Ke spouštění aplikací CUDA v fond uzlů Windows síťovým Adaptérem, musíte nainstalovat ovladače NVDIA GPU. V následujících krocích Ukázka používá balíčku aplikace k instalaci ovladačů NVIDIA GPU. Tuto možnost můžete zvolit, pokud vaše úloha závisí na konkrétní verzi ovladače GPU.

1. Stáhnout instalační balíček ovladače GPU na Windows serveru 2016 z [NVIDIA webu](http://us.download.nvidia.com/Windows/) – například [verze 411.82](http://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe). Uložte soubor místně pomocí krátkého názvu jako *GPUDriverSetup.exe*.
2. Vytvořte soubor zip balíčku.
3. Nahrání balíčku k účtu Batch. Pokyny najdete v tématu [balíčky aplikací](batch-application-packages.md) pokyny. Zadejte id aplikace, například *GPUDriver*a verze, jako *411.82*.
1. Pomocí rozhraní API služby Batch na webu Azure portal, vytvořte fond v konfiguraci virtuálního počítače s požadovaný počet uzlů a škálování. Následující tabulka uvádí nastavení vzorku instalace ovladačů NVIDIA GPU tiše pomocí spouštěcího úkolu:

| Nastavení | Hodnota |
| ---- | ----- | 
| **Typ image** | Marketplace (Linux/Windows) |
| **Publisher** | MicrosoftWindowsServer |
| **Nabídka** | WindowsServer |
| **Sku** | 2016-Datacenter |
| **Velikost uzlu** | NC6 Standard |
| **Odkazy na balíček aplikace** | GPUDriver verze 411.82 |
| **Spouštěcí úkol povoleno** | True<br>**Příkazový řádek** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**Identita uživatele** -fondu automaticky vytvořený uživatel, správce<br/>**Čekat na úspěch** – True

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>Příklad: Ovladačů NVIDIA GPU ve fondu virtuálních počítačů s Linuxem síťový adaptér.

Ke spouštění aplikací CUDA na fondu uzlů Linux NC, budete muset nainstalujte potřebné ovladače NVIDIA Tesla GPU z CUDA Toolkit. Následující ukázkový postup vytvoření a nasazení vlastní image Ubuntu 16.04 LTS pomocí ovladače GPU:

1. Nasazení běží Ubuntu 16.04 LTS virtuálního počítače Azure řady NC-series. Například můžete vytvořte virtuální počítač v oblasti USA střed – jih. 
2. Přidat [rozšíření ovladače GPU NVIDIA](../virtual-machines/extensions/hpccompute-gpu-linux.md
) k virtuálnímu počítači pomocí webu Azure portal, klientský počítač, která se připojuje k předplatnému Azure nebo Azure Cloud Shell. Případně postupujte podle kroků pro připojení k virtuálnímu počítači a [instalovat ovladače CUDA](../virtual-machines/linux/n-series-driver-setup.md) ručně.
3. Postupujte podle kroků k vytvoření [snímků a vlastní image virtuálního počítače s Linuxem](batch-custom-images.md) pro službu Batch.
4. Vytvoření účtu Batch v oblasti, která podporuje virtuální počítače síťového adaptéru.
5. Pomocí rozhraní API služby Batch na webu Azure portal vytvořte fond [pomocí vlastní image](batch-custom-images.md) a požadovaný počet uzlů a škálování. V následující tabulce jsou uvedeny ukázkový fond nastavení bitové kopie:

| Nastavení | Hodnota |
| ---- | ---- |
| **Typ image** | Vlastní image |
| **Vlastní Image** | *Název bitové kopie* |
| **SKU agenta uzlu** | batch.Node.Ubuntu 16.04 |
| **Velikost uzlu** | NC6 Standard |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>Příklad: Microsoft MPI ve virtuálním počítači H16r Windows fondu

Pro spouštění aplikací Windows MPI na fondu uzlů virtuálních počítačů Azure H16r, budete potřebovat ke konfiguraci rozšíření HpcVmDrivers a instalaci [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi). Tady je ukázkový postup nasazení vlastní image Windows serveru 2016 s potřebné ovladače a software:

1. Nasazení Virtuálním počítači H16r Azure s Windows serverem 2016. Například můžete vytvořte virtuální počítač v oblasti USA – západ. 
2. Přidá do virtuálního počítače pomocí rozšíření HpcVmDrivers [spuštění příkazu k prostředí Azure PowerShell](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances
) z klientského počítače, který se připojí k vašemu předplatnému Azure nebo pomocí Azure Cloud Shell. 
1. Vytvoření připojení vzdálené plochy k virtuálnímu počítači.
1. Stáhněte si [instalační balíček](https://www.microsoft.com/download/details.aspx?id=57467) (MSMpiSetup.exe) pro nejnovější verzi Microsoft MPI a instalace Microsoft MPI.
1. Postupujte podle kroků k vytvoření [snímků a vlastní image virtuálního počítače Windows](batch-custom-images.md) pro službu Batch.
1. Pomocí rozhraní API služby Batch na webu Azure portal vytvořte fond [pomocí vlastní image](batch-custom-images.md) a požadovaný počet uzlů a škálování. V následující tabulce jsou uvedeny ukázkový fond nastavení bitové kopie:

| Nastavení | Hodnota |
| ---- | ---- |
| **Typ image** | Vlastní image |
| **Vlastní Image** | *Název bitové kopie* |
| **SKU agenta uzlu** | batch.Node.Windows amd64 |
| **Velikost uzlu** | H16r Standard |
| **Komunikace mezi uzly povolena** | True |
| **Maximální počet úkolů na uzlu** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>Příklad: Intel MPI ve fondu virtuálních počítačů s Linuxem H16r

Pro spouštění aplikací MPI na fond Linux H-series uzly, jednou z možností je použít [založené na CentOS 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) image z Azure Marketplace. Předinstalovanou RDMA Linuxu ovladače a Intel MPI. Tento obrázek také podporuje úlohy kontejneru Dockeru.

Pomocí rozhraní API služby Batch na webu Azure portal, vytvoření fondu pomocí této bitové kopie a požadovaný počet uzlů a škálování. Následující tabulka uvádí nastavení fondu vzorku:

| Nastavení | Hodnota |
| ---- | ---- |
| **Typ image** | Marketplace (Linux/Windows) |
| **Publisher** | OpenLogic |
| **Nabídka** | CentOS-HPC |
| **Sku** | 7.4 |
| **Velikost uzlu** | H16r Standard |
| **Komunikace mezi uzly povolena** | True |
| **Maximální počet úkolů na uzlu** | 1 |

## <a name="next-steps"></a>Další postup

* Spouštění úloh MPI ve fondu služby Azure Batch, najdete v článku [Windows](batch-mpi.md) nebo [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) příklady.

* Příklady GPU úloh v Batch, najdete v článku [Batch loděnice](https://github.com/Azure/batch-shipyard/) recepty.