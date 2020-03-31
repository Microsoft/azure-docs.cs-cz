---
title: Použití výpočetních virtuálních počítačů Azure s batch
description: Jak využít výhod velikosti virtuálních počítačů HPC a GPU ve fondech Azure Batch. Seznamte se se závislostmi operačního spoje a podívejte se na několik příkladů scénářů.
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/17/2018
ms.author: labrenne
ms.openlocfilehash: d0238e0b5b882748218835d7f06a147d435a9f90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245051"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>Použití instancí RDMA nebo GPU ve fondech dávek

Chcete-li spustit určité dávkové úlohy, můžete využít velikosti virtuálních zařízení Azure určené pro rozsáhlé výpočty. Například:

* Chcete-li spustit [úlohy MPI](batch-mpi.md)s více instancemi , zvolte řadu H nebo jiné velikosti, které mají síťové rozhraní pro vzdálený přímý přístup do paměti (RDMA). Tyto velikosti se připojují k síti InfiniBand pro komunikaci mezi uzly, což může urychlit aplikace MPI. 

* Pro aplikace CUDA zvolte velikosti n-series, které obsahují karty grafické jednotky NVIDIA Tesla (GPU).

Tento článek obsahuje pokyny a příklady použití některých specializovaných velikostí Azure ve fondech dávek. Specifikace a pozadí naleznete v těchto tématech:

* Vysoce výkonné výpočetní velikosti virtuálních počítačů[(Linux](../virtual-machines/linux/sizes-hpc.md), [Windows)](../virtual-machines/windows/sizes-hpc.md) 

* Velikosti virtuálních zařízení s podporou GPU[(Linux](../virtual-machines/linux/sizes-gpu.md), [Windows)](../virtual-machines/windows/sizes-gpu.md) 

> [!NOTE]
> Některé velikosti virtuálních počítačů nemusí být dostupné v oblastech, kde vytváříte dávkové účty. Pokud chcete zkontrolovat, jestli je k dispozici velikost, [přečtěte si informace o produktech dostupných podle oblasti](https://azure.microsoft.com/regions/services/) a [vyberte velikost virtuálního počítače pro fond dávek](batch-pool-vm-sizes.md).

## <a name="dependencies"></a>Závislosti

Funkce RDMA nebo GPU velikosti náročné na výpočetní výkon v batchjsou podporovány pouze v určitých operačních systémech. (Seznam podporovaných operačních systémů je podmnožinou těch podporovaných pro virtuální počítače vytvořené v těchto velikostech.) V závislosti na způsobu vytvoření fondu dávek může být nutné nainstalovat nebo nakonfigurovat další ovladač nebo jiný software v uzlech. Následující tabulky shrnují tyto závislosti. Podrobnosti najdete v odkazovaných článcích. Možnosti konfigurace fondů dávek naleznete dále v tomto článku.

### <a name="linux-pools---virtual-machine-configuration"></a>Fondy Linuxu – konfigurace virtuálního počítače

| Velikost | Schopnost | Operační systémy | Požadovaný software | Nastavení fondu |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Ubuntu 16.04 LTS, nebo<br/>HPC založená na CentOS<br/>(Azure Marketplace) | Intel MPI 5<br/><br/>Linux RDMA ovladače | Povolit komunikaci mezi uzlami, zakázat souběžné provádění úloh |
| [Řada NC, NCv2, NCv3, NDv2](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla GPU (liší se podle řady) | Ubuntu 16.04 LTS, nebo<br/>CentOS 7,3 nebo 7,4<br/>(Azure Marketplace) | Ovladače NVIDIA CUDA nebo CUDA Toolkit | Není dostupné. | 
| [Řada NV, NVv2](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS, nebo<br/>Centos 7,3<br/>(Azure Marketplace) | Ovladače NVIDIA GRID | Není dostupné. |

<sup>*</sup>RdMA-schopné n-série velikosti také nvidia tesla GPU

### <a name="windows-pools---virtual-machine-configuration"></a>Fondy Windows – konfigurace virtuálního počítače

| Velikost | Schopnost | Operační systémy | Požadovaný software | Nastavení fondu |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Windows Server 2016, 2012 R2 nebo<br/>2012 (Azure Marketplace) | Microsoft MPI 2012 R2 nebo novější, nebo<br/> Intel MPI 5<br/><br/>Ovladače rdma systému Windows | Povolit komunikaci mezi uzlami, zakázat souběžné provádění úloh |
| [Řada NC, NCv2, NCv3, ND, NDv2](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla GPU (liší se podle řady) | Windows Server 2016 nebo <br/>2012 R2 (Azure Marketplace) | Ovladače NVIDIA CUDA nebo CUDA Toolkit| Není dostupné. | 
| [Řada NV, NVv2](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 nebo<br/>2012 R2 (Azure Marketplace) | Ovladače NVIDIA GRID | Není dostupné. |

<sup>*</sup>RdMA-schopné n-série velikosti také nvidia tesla GPU

### <a name="windows-pools---cloud-services-configuration"></a>Fondy Windows – konfigurace cloudových služeb

> [!NOTE]
> Velikosti n-series nejsou podporovány ve fondech dávek s konfigurací cloudové služby.
>

| Velikost | Schopnost | Operační systémy | Požadovaný software | Nastavení fondu |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md) | RDMA | Windows Server 2016, 2012 R2, 2012 nebo<br/>2008 R2 (Rodina hostovaného operačního es) | Microsoft MPI 2012 R2 nebo novější, nebo<br/>Intel MPI 5<br/><br/>Ovladače rdma systému Windows | Povolit komunikaci mezi uzlami,<br/> zakázat souběžné provádění úloh |

## <a name="pool-configuration-options"></a>Možnosti konfigurace fondu

Chcete-li nakonfigurovat specializovanou velikost virtuálního počítače pro fond dávek, máte několik možností instalace požadovaného softwaru nebo ovladačů:

* Pro fondy v konfiguraci virtuálního počítače zvolte předkonfigurovanou image virtuálního počítače [Azure Marketplace,](https://azuremarketplace.microsoft.com/marketplace/) která má předinstalované ovladače a software. Příklady: 

  * [7.4 HPC se sídlem v CentOS](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) - zahrnuje ovladače RDMA a Intel MPI 5.1

  * [Virtuální stroj pro datové vědy](../machine-learning/data-science-virtual-machine/overview.md) pro Linux nebo Windows - obsahuje ovladače NVIDIA CUDA

  * Linuxové bitové kopie pro úlohy dávkových kontejnerů, které obsahují také ovladače GPU a RDMA:

    * [CentOS (s ovladači GPU a RDMA) pro fondy kontejnerů Azure Batch](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Ubuntu Server (s ovladači GPU a RDMA) pro fondy kontejnerů Azure Batch](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* Vytvořte [si vlastní bitovou kopii virtuálního počítače s Windows nebo Linuxem,](batch-sig-images.md) na které jste nainstalovali ovladače, software nebo jiná nastavení požadovaná pro velikost virtuálního počítače. 

* Vytvořte [balíček dávkové aplikace](batch-application-packages.md) z ovladače zip nebo instalačního programu aplikace a nakonfigurujte Batch pro nasazení balíčku do fondu uzlů a instalaci jednou při vytvoření každého uzlu. Pokud je například balíček aplikace instalační program, vytvořte příkazový řádek [úlohy spuštění,](batch-api-basics.md#start-task) který bezobslužně nainstaluje aplikaci do všech uzlů fondu. Zvažte použití balíčku aplikace a úlohy spuštění fondu, pokud vaše úloha závisí na konkrétní verzi ovladače.

  > [!NOTE] 
  > Počáteční úloha musí být spuštěna se zvýšenými oprávněními (oprávněními správce) a musí čekat na úspěch. Dlouhotrvající úlohy zvýší čas na zřízení fondu dávek.
  >

* [Dávková loděnice](https://github.com/Azure/batch-shipyard) automaticky nakonfiguruje ovladače GPU a RDMA tak, aby transparentně fungovaly s kontejnerizovanými úlohami v Azure Batch. Batch Loděnice je zcela poháněn konfiguračnísoubory. K dispozici je mnoho ukázkových konfigurací receptů, které umožňují úlohy GPU a RDMA, jako je [například recept GPU CNTK,](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) který předkonfiguruje ovladače GPU na virtuálních počítačích řady N a načte software Microsoft Cognitive Toolkit jako bitovou kopii Dockeru.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>Příklad: Ovladače GPU NVIDIA ve fondu virtuálních zařízení windows NC

Chcete-li spustit aplikace CUDA ve fondu uzlů WINDOWS NC, je třeba nainstalovat ovladače GPU NVDIA. Následující ukázkové kroky slouží k instalaci ovladačů GPU NVIDIA pomocí balíčku aplikace. Tuto možnost můžete zvolit, pokud vaše úloha závisí na konkrétní verzi ovladače GPU.

1. Stáhněte si instalační balíček pro ovladače GPU na Windows Server 2016 z [webu NVIDIA](https://www.nvidia.com/Download/index.aspx) - například [verze 411.82](https://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe). Uložte soubor místně pomocí krátkého názvu, jako je *gpuDriverSetup.exe*.
2. Vytvořte soubor zip balíčku.
3. Nahrajte balíček do svého dávkového účtu. Postup naleznete v pokynech [k balíčkům aplikace.](batch-application-packages.md) Zadejte ID aplikace, například *GPUDriver*, a verzi například *411.82*.
1. Pomocí dávkových api nebo portálu Azure vytvořte fond v konfiguraci virtuálního počítače s požadovaným počtem uzlů a škálování. V následující tabulce jsou uvedena ukázková nastavení pro tichou instalaci ovladačů GRAFICKÉKARTY NVIDIA pomocí úlohy spuštění:

| Nastavení | Hodnota |
| ---- | ----- | 
| **Typ image** | Marketplace (Linux/Windows) |
| **Vydavatel** | MicrosoftWindowsServer |
| **Nabízejí** | WindowsServer |
| **Sku** | 2016-Datové centrum |
| **Velikost uzlu** | NC6 Standard |
| **Odkazy na balíčky aplikací** | GPUDriver, verze 411.82 |
| **Spuštění úlohy povoleno.** | True<br>**Příkazový řádek** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**Identita uživatele** – automatický uživatel fondu, správce<br/>**Čekat na úspěch** - Pravda

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>Příklad: Ovladače GPU NVIDIA ve fondu virtuálních virtuálních zařízení pro Linux NC

Chcete-li spustit aplikace CUDA ve fondu uzlů Linux NC, musíte nainstalovat potřebné ovladače NVIDIA Tesla GPU z CUDA Toolkit. Následující ukázkové kroky vytvoří a nasadí vlastní bitovou kopii Ubuntu 16.04 LTS s ovladači GPU:

1. Nasaďte virtuální počítač řady Azure řady NC s Ubuntu 16.04 LTS. Například vytvořte virtuální virtuální ms v oblasti USA – jižní střed. 
2. Přidejte [rozšíření ovladačů GPU NVIDIA](../virtual-machines/extensions/hpccompute-gpu-linux.md
) k virtuálnímu počítači pomocí portálu Azure, klientského počítače, který se připojuje k předplatnému Azure, nebo Azure Cloud Shell. Případně postupujte podle pokynů pro připojení k virtuálnímu zařízení a [nainstalovat ovladače CUDA](../virtual-machines/linux/n-series-driver-setup.md) ručně.
3. Podle pokynů vytvořte [obrázek Galerie sdílených obrázků](batch-sig-images.md) pro Batch.
4. Vytvořte dávkový účet v oblasti, která podporuje virtuální virtuální listy NC.
5. Pomocí dávkových api nebo portálu Azure vytvořte fond [pomocí vlastní image](batch-sig-images.md) a s požadovaným počtem uzlů a škálování. V následující tabulce jsou uvedena ukázková nastavení fondu pro obrázek:

| Nastavení | Hodnota |
| ---- | ---- |
| **Typ image** | Vlastní image |
| **Vlastní obrázek** | *Název obrázku* |
| **Skladová položka agenta uzlu** | batch.node.ubuntu 16.04 |
| **Velikost uzlu** | NC6 Standard |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>Příklad: Microsoft MPI ve fondu virtuálních aplikací H16r se systémem Windows

Chcete-li spustit aplikace MpI systému Windows ve fondu uzlů virtuálních počítačů Azure H16r, je třeba nakonfigurovat rozšíření HpcVmDrivers a nainstalovat [rozhraní Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi). Tady jsou ukázkové kroky k nasazení vlastní bitové kopie Windows Serveru 2016 s potřebnými ovladači a softwarem:

1. Nasaďte virtuální počítač Azure H16r se systémem Windows Server 2016. Například vytvořte virtuální ho v oblasti USA – západ. 
2. Přidejte rozšíření HpcVmDrivers do virtuálního počítače [spuštěním příkazu Azure PowerShell](../virtual-machines/sizes-hpc.md) z klientského počítače, který se připojuje k předplatnému Azure, nebo pomocí Azure Cloud Shell. 
1. Vytvořte připojení ke vzdálené ploše k virtuálnímu počítači.
1. Stáhněte si [instalační balíček](https://www.microsoft.com/download/details.aspx?id=57467) (MSMpiSetup.exe) pro nejnovější verzi rozhraní Microsoft MPI a nainstalujte rozhraní Microsoft MPI.
1. Podle pokynů vytvořte [obrázek Galerie sdílených obrázků](batch-sig-images.md) pro Batch.
1. Pomocí dávkových api nebo portálu Azure vytvořte fond [pomocí Galerie sdílených bitových obrázků](batch-sig-images.md) a s požadovaným počtem uzlů a škálování. V následující tabulce jsou uvedena ukázková nastavení fondu pro obrázek:

| Nastavení | Hodnota |
| ---- | ---- |
| **Typ image** | Vlastní image |
| **Vlastní obrázek** | *Název obrázku* |
| **Skladová položka agenta uzlu** | batch.node.windows amd64 |
| **Velikost uzlu** | H16r Standard |
| **Internode komunikace povolena** | True |
| **Maximální počet úkolů na uzel** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>Příklad: Technologie Intel MPI ve fondu virtuálních aplikací Linux H16r

Chcete-li spouštět aplikace MPI ve fondu uzlů řady H v Linuxu, je jednou z možností použití bitové kopie [7.4 HPC založené na CentOS](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) z Azure Marketplace. Linux RDMA ovladače a Intel MPI jsou předinstalovány. Tato image také podporuje úlohy kontejnerů Dockeru.

Pomocí dávkových api nebo portálu Azure vytvořte fond pomocí této bitové kopie a s požadovaným počtem uzlů a škálování. V následující tabulce jsou uvedena nastavení ukázkového fondu:

| Nastavení | Hodnota |
| ---- | ---- |
| **Typ image** | Marketplace (Linux/Windows) |
| **Vydavatel** | OpenLogic |
| **Nabízejí** | Centos-HPC |
| **Sku** | 7.4 |
| **Velikost uzlu** | H16r Standard |
| **Internode komunikace povolena** | True |
| **Maximální počet úkolů na uzel** | 1 |

## <a name="next-steps"></a>Další kroky

* Pokud chcete spustit úlohy MPI ve fondu Azure Batch, přečtěte si příklady [Windows](batch-mpi.md) nebo [Linuxu.](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/)

* Příklady úloh GPU na Batch, viz [dávkové lodě](https://github.com/Azure/batch-shipyard/) recepty.
