---
title: Použití virtuálních počítačů Azure náročných na výpočetní výkon s využitím služby Batch | Microsoft Docs
description: Jak využít výhody a velikosti virtuálních počítačů HPC a GPU v Azure Batch fondech
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/17/2018
ms.author: lahugh
ms.openlocfilehash: 687783520b082cdfd1a6ffc91a8641ea35fafd68
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323355"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>Použití instancí RDMA nebo GPU ve fondech Batch

Pokud chcete spustit určité dávkové úlohy, můžete využít výhod velikosti virtuálních počítačů Azure určených pro velké výpočty. Příklad:

* Pokud chcete spouštět [úlohy MPI](batch-mpi.md)s více instancemi, vyberte H-Series nebo jiné velikosti, které mají síťové rozhraní pro přímý přístup do paměti vzdáleného počítače (RDMA). Tyto velikosti se připojují k InfiniBand síti pro komunikaci mezi uzly, což dokáže zrychlit aplikace MPI. 

* V případě aplikací CUDA vyberte velikosti N-Series, které zahrnují karty GPU (Graphics Processing Unit) NVIDIA Tesla.

Tento článek poskytuje pokyny a příklady použití některých specializovaných velikostí Azure ve fondech služby Batch. Informace o specifikacích a pozadí najdete v tématech:

* Vysoce výkonné výpočetní velikosti virtuálních počítačů ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* Velikosti virtuálních počítačů s podporou GPU ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 

> [!NOTE]
> Některé velikosti virtuálních počítačů nemusí být k dispozici v oblastech, kde vytvoříte účty Batch. Chcete-li ověřit, zda je velikost k dispozici, přečtěte si téma [Dostupné produkty v oblasti](https://azure.microsoft.com/regions/services/) a [Vyberte velikost virtuálního počítače pro fond Batch](batch-pool-vm-sizes.md).

## <a name="dependencies"></a>Závislosti

Schopnosti RDMA nebo GPU pro velikosti náročné na výpočetní výkon ve službě Batch se podporují jenom v určitých operačních systémech. (Seznam podporovaných operačních systémů je podmnožinou těch, které jsou podporovány pro virtuální počítače vytvořené v těchto velikostech.) V závislosti na tom, jak fond služby Batch vytvoříte, možná budete muset nainstalovat nebo nakonfigurovat další ovladač nebo jiný software na uzlech. Tyto závislosti jsou shrnuté v následujících tabulkách. Podrobnosti najdete v propojených článcích. Možnosti konfigurace fondů služby Batch najdete v části dále v tomto článku.

### <a name="linux-pools---virtual-machine-configuration"></a>Fondy Linux – konfigurace virtuálních počítačů

| Size | Funkce | Operační systémy | Požadovaný software | Nastavení fondu |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | PODPORUJÍ | Ubuntu 16,04 LTS nebo<br/>HPC založené na CentOS<br/>(Azure Marketplace) | Intel MPI 5<br/><br/>Ovladače systému Linux RDMA | Povolit komunikaci mezi uzly, zakázat souběžné provádění úloh |
| [Řady NC, NCv2, NCv3, NDv2 Series](../virtual-machines/linux/n-series-driver-setup.md) | Grafický procesor NVIDIA Tesla (liší se podle řad) | Ubuntu 16,04 LTS nebo<br/>CentOS 7,3 nebo 7,4<br/>(Azure Marketplace) | Ovladače NVIDIA CUDA nebo CUDA Toolkit | Není k dispozici | 
| [NV, NVv2 Series](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16,04 LTS nebo<br/>CentOS 7.3<br/>(Azure Marketplace) | Ovladače pro MŘÍŽKu NVIDIA | Není k dispozici |

<sup>*</sup>Velikosti N-Series podporující RDMA obsahují také NVIDIA Tesla GPU

### <a name="windows-pools---virtual-machine-configuration"></a>Fondy Windows – konfigurace virtuálních počítačů

| Size | Funkce | Operační systémy | Požadovaný software | Nastavení fondu |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | PODPORUJÍ | Windows Server 2016, 2012 R2 nebo<br/>2012 (Azure Marketplace) | Microsoft MPI 2012 R2 nebo novější nebo<br/> Intel MPI 5<br/><br/>Ovladače Windows RDMA | Povolit komunikaci mezi uzly, zakázat souběžné provádění úloh |
| [Řady NC, NCv2, NCv3, ND, NDv2 Series](../virtual-machines/windows/n-series-driver-setup.md) | Grafický procesor NVIDIA Tesla (liší se podle řad) | Windows Server 2016 nebo <br/>2012 R2 (Azure Marketplace) | Ovladače NVIDIA CUDA nebo CUDA Toolkit| Není k dispozici | 
| [NV, NVv2 Series](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 nebo<br/>2012 R2 (Azure Marketplace) | Ovladače pro MŘÍŽKu NVIDIA | Není k dispozici |

<sup>*</sup>Velikosti N-Series podporující RDMA obsahují také NVIDIA Tesla GPU

### <a name="windows-pools---cloud-services-configuration"></a>Fondy Windows – konfigurace cloudových služeb

> [!NOTE]
> U fondů služby Batch s konfigurací cloudové služby se nepodporují velikosti řady N-Series.
>

| Size | Funkce | Operační systémy | Požadovaný software | Nastavení fondu |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | PODPORUJÍ | Windows Server 2016, 2012 R2, 2012 nebo<br/>2008 R2 (řada operačních systémů Host) | Microsoft MPI 2012 R2 nebo novější nebo<br/>Intel MPI 5<br/><br/>Ovladače Windows RDMA | Povolit komunikaci mezi uzly,<br/> zakázat souběžné provádění úloh |

## <a name="pool-configuration-options"></a>Možnosti konfigurace fondu

Pokud chcete pro fond Batch nakonfigurovat specializovanou velikost virtuálního počítače, máte k dispozici několik možností, jak nainstalovat požadovaný software nebo ovladače:

* Pro fondy v konfiguraci virtuálního počítače vyberte předkonfigurovaný [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) image virtuálního počítače, která má předinstalované ovladače a software. Příklady: 

  * [CentOS HPC-based 7,4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) -zahrnuje ovladače RDMA a Intel MPI 5,1.

  * [Data Science Virtual Machine](../machine-learning/data-science-virtual-machine/overview.md) pro Linux nebo Windows – obsahuje ovladače NVIDIA CUDA

  * Image pro Linux pro úlohy kontejnerů služby Batch, které zahrnují také ovladače GPU a RDMA:

    * [CentOS (s ovladači GPU a RDMA) pro Azure Batch fondy kontejnerů](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Server Ubuntu (s ovladači GPU a RDMA) pro Azure Batch fondy kontejnerů](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* Vytvořte [vlastní image virtuálního počítače se systémem Windows nebo Linux](batch-custom-images.md) , na které máte nainstalované ovladače, software nebo jiná nastavení potřebná pro velikost virtuálního počítače. 

* Vytvořte [balíček aplikace](batch-application-packages.md) Batch z ovladače zip nebo instalačního programu aplikace a nakonfigurujte dávku pro nasazení balíčku do fondu uzlů a nainstalujte se jednou při vytvoření každého uzlu. Například pokud je balíček aplikace instalačním programem, vytvořte příkazový řádek [spouštěcího úkolu](batch-api-basics.md#start-task) , který bude tiše instalovat aplikaci na všechny uzly fondu. Pokud vaše zatížení závisí na konkrétní verzi ovladače, zvažte použití balíčku aplikace a spouštěcího úkolu fondu.

  > [!NOTE] 
  > Spouštěcí úkol musí běžet se zvýšenými oprávněními (správce) a musí čekat na úspěch. Dlouhotrvající úlohy prodlouží dobu zřízení fondu služby Batch.
  >

* [Batch loděnice](https://github.com/Azure/batch-shipyard) automaticky KONFIGURUJE ovladače GPU a RDMA, aby fungovaly transparentně s využitím kontejnerových úloh na Azure Batch. Dávková loděnice je zcela řízená pomocí konfiguračních souborů. K dispozici je celá řada ukázkových konfigurací, které umožňují úlohy GPU a RDMA, jako je [CNTKý recept](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) na procesor řady N-Series, který nakonfiguruje ovladače GPU pro virtuální počítače řady N-Series a načítá Microsoft Cognitive Toolkit software jako image Docker.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>Příklad: Ovladače NVIDIA GPU ve fondu virtuálních počítačů s Windows NC

Pokud chcete spouštět aplikace CUDA ve fondu uzlů Windows NC, musíte nainstalovat ovladače NVDIA GPU. Následující ukázkový postup slouží k instalaci ovladačů NVIDIA GPU pomocí balíčku aplikace. Tuto možnost můžete zvolit, pokud vaše zatížení závisí na konkrétní verzi ovladače GPU.

1. Stáhněte instalační balíček pro ovladače GPU na Windows serveru 2016 na [webu NVIDIA](https://www.nvidia.com/Download/index.aspx) – například [verze 411,82](https://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe). Uložte soubor místně pomocí krátkého názvu, jako je *GPUDriverSetup. exe*.
2. Vytvořte soubor zip balíčku.
3. Nahrajte balíček na účet Batch. Postup najdete v doprovodné příručce k [balíčkům aplikací](batch-application-packages.md) . Zadejte ID aplikace, například *GPUDriver*, a verzi, například *411,82*.
1. Pomocí rozhraní API nebo Azure Portal dávky vytvořte fond v konfiguraci virtuálního počítače s požadovaným počtem uzlů a škálování. V následující tabulce jsou uvedena ukázková nastavení pro bezobslužnou instalaci ovladačů NVIDIA GPU pomocí spouštěcího úkolu:

| Nastavení | Hodnota |
| ---- | ----- | 
| **Typ image** | Marketplace (Linux/Windows) |
| **Publisher** | MicrosoftWindowsServer |
| **Nabídka** | WindowsServer |
| **Sku** | 2016 – Datacenter |
| **Velikost uzlu** | NC6 Standard |
| **Odkazy na balíček aplikace** | GPUDriver verze 411,82 |
| **Spouštěcí úkol povolen** | Pravda<br>**Příkazový řádek** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**Identita uživatele** – fond – autouser, správce<br/>**Počkat na úspěch** – pravda

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>Příklad: Ovladače NVIDIA GPU na fondu virtuálních počítačů se systémem Linux NC

Chcete-li spouštět aplikace CUDA ve fondu uzlů systému Linux NC, je nutné nainstalovat potřebné ovladače NVIDIA Tesla GPU ze sady CUDA Toolkit. Následující příklady kroků vytvoří a nasadí vlastní image Ubuntu 16,04 LTS s ovladači GPU:

1. Nasazení virtuálního počítače Azure NC-Series se systémem Ubuntu 16,04 LTS. Vytvořte například virtuální počítač v oblasti USA (střed) – jih. 
2. Přidejte do virtuálního počítače rozšíření](../virtual-machines/extensions/hpccompute-gpu-linux.md
) NVIDIAGPUovladačepomocíAzurePortal,klientskéhopočítače,kterýsepřipojujekpředplatnémuAzureneboAzureCloudShell.[ Případně postupujte podle pokynů pro připojení k virtuálnímu počítači a [nainstalujte ovladače CUDA](../virtual-machines/linux/n-series-driver-setup.md) ručně.
3. Použijte postup vytvoření [snímku a vlastní image virtuálního počítače se systémem Linux](batch-custom-images.md) pro službu Batch.
4. Vytvořte účet Batch v oblasti, která podporuje virtuální počítače NC.
5. Pomocí rozhraní API nebo Azure Portal dávky vytvořte fond s [použitím vlastní image](batch-custom-images.md) a požadovaného počtu uzlů a škálování. V následující tabulce jsou uvedena ukázková nastavení fondu pro bitovou kopii:

| Nastavení | Value |
| ---- | ---- |
| **Typ image** | Vlastní image |
| **Vlastní image** | *Název obrázku* |
| **SKU agenta uzlu** | batch.node.ubuntu 16.04 |
| **Velikost uzlu** | NC6 Standard |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>Příklad: Microsoft MPI ve fondu virtuálních počítačů s Windows H16r

Pokud chcete spouštět aplikace Windows MPI ve fondu uzlů virtuálních počítačů Azure H16r, musíte nakonfigurovat rozšíření HpcVmDrivers a nainstalovat [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi). Tady je ukázkový postup nasazení vlastní image Windows serveru 2016 s potřebnými ovladači a softwarem:

1. Nasaďte virtuální počítač Azure H16r s Windows serverem 2016. Vytvořte například virtuální počítač v oblasti USA – západ. 
2. Pomocí příkazu [](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances
) Azure PowerShell z klientského počítače, který se připojuje k vašemu předplatnému Azure nebo pomocí Azure Cloud Shell, přidejte do virtuálního počítače rozšíření HpcVmDrivers. 
1. Vytvořte připojení ke vzdálené ploše virtuálního počítače.
1. Stáhněte [instalační balíček](https://www.microsoft.com/download/details.aspx?id=57467) (MSMpiSetup. exe) pro nejnovější verzi Microsoft MPI a nainstalujte Microsoft MPI.
1. Použijte postup vytvoření [snímku a vlastní image virtuálního počítače s Windows](batch-custom-images.md) pro službu Batch.
1. Pomocí rozhraní API nebo Azure Portal dávky vytvořte fond s [použitím vlastní image](batch-custom-images.md) a požadovaného počtu uzlů a škálování. V následující tabulce jsou uvedena ukázková nastavení fondu pro bitovou kopii:

| Nastavení | Value |
| ---- | ---- |
| **Typ image** | Vlastní image |
| **Vlastní image** | *Název obrázku* |
| **SKU agenta uzlu** | Batch. Node. Windows amd64 |
| **Velikost uzlu** | H16r Standard |
| **Komunikace mezi uzly povolena** | Pravda |
| **Maximální počet úkolů na uzel** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>Příklad: Intel MPI ve fondu virtuálních počítačů se systémem Linux H16r

Pokud chcete spouštět aplikace MPI ve fondu uzlů H-Series, jedna možnost je použít z Azure Marketplace obrázek [HPC založený na CentOS 7,4](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) . Jsou předinstalované ovladače systému Linux RDMA a Intel MPI. Tento obrázek podporuje také úlohy kontejneru Docker.

Pomocí rozhraní API nebo Azure Portal dávky vytvořte fond s použitím tohoto obrázku a s požadovaným počtem uzlů a škálování. V následující tabulce jsou uvedena ukázková nastavení fondu:

| Nastavení | Hodnota |
| ---- | ---- |
| **Typ image** | Marketplace (Linux/Windows) |
| **Publisher** | OpenLogic |
| **Nabídka** | CentOS-HPC |
| **Sku** | 7.4 |
| **Velikost uzlu** | H16r Standard |
| **Komunikace mezi uzly povolena** | Pravda |
| **Maximální počet úkolů na uzel** | 1 |

## <a name="next-steps"></a>Další postup

* Pokud chcete spouštět úlohy MPI ve fondu Azure Batch, přečtěte si příklady pro [Windows](batch-mpi.md) nebo [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) .

* Příklady úloh GPU ve službě Batch najdete v tématu recepty [Batch](https://github.com/Azure/batch-shipyard/) .