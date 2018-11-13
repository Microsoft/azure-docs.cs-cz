---
title: Použijte virtuální počítače náročné na výpočetní prostředky Azure pomocí služby Batch | Dokumentace Microsoftu
description: Jak využít RDMA podporovat nebo s podporou grafického procesoru velikosti virtuálních počítačů ve fondech Azure Batch
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: danlep
ms.openlocfilehash: 6969f0c6a05ebf5b34fb746d2a83b884687ad710
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258251"
---
# <a name="use-rdma-capable-or-gpu-enabled-instances-in-batch-pools"></a>Ve fondech služby Batch použít podporující RDMA instancí s podporou grafického procesoru

Na spouštění určitých úloh služby Batch, můžete chtít využít výhod určená pro rozsáhlé výpočty velikostí virtuálních počítačů Azure. Chcete-li například spustit víc instancí [úlohy MPI](batch-mpi.md), můžete použít A8, A9, nebo velikosti řady H-series, které máte síť rozhraní pro vzdáleného přímého přístupu do paměti (RDMA). Tyto velikosti připojení k síti InfiniBand pro komunikaci mezi uzly, které můžou urychlit aplikací MPI. Nebo pro aplikace spouští CUDA, můžete použít velikosti N-series, které zahrnují NVIDIA Tesla grafických karet procesor (GPU).

Tento článek obsahuje pokyny a příklady pro použití některé z Azure specializovaných velikostí ve fondech Batch. Specifikace a pozadí naleznete v tématu:

* Vysokovýkonné výpočetní velikosti virtuálních počítačů ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* Velikosti virtuálních počítačů s podporou grafického procesoru ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 


## <a name="subscription-and-account-limits"></a>Předplatné a omezení účtu

* **Kvóty a omezení** – [kvótu jader na účet Batch](batch-quota-limit.md#resource-quotas) můžete omezit počet uzlů danou velikost můžete přidat do fondu služby Batch. Pravděpodobněji k dosažení kvóty při výběru podporující RDMA, s podporou grafického procesoru nebo jiné vícejádrovými velikosti virtuálních počítačů. 

  Kromě toho používání určitých rodiny virtuálních počítačů ve svém účtu Batch, jako je například NCv2, NCv3 a ND, omezen z důvodu omezené kapacity. Použití těchto skupin je dostupný jenom můžete si vyžádat zvýšení kvóty z výchozí hodnoty 0 jader.  

  Pokud potřebujete, [požádat o zvýšení kvóty](batch-quota-limit.md#increase-a-quota) bez poplatků.

* **Dostupnost v oblastech** – náročné na výpočetní virtuální počítače nemusí být k dispozici v oblastech, kde můžete vytvořit účty Batch. Zkontrolujte, že velikost je k dispozici, najdete v článku [dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/regions/services/).


## <a name="dependencies"></a>Závislosti

Velikosti náročné na výpočetní funkce RDMA a GPU jsou podporovány pouze v určitých operačních systémů. V závislosti na operačním systému potřebujete nainstalovat nebo nakonfigurovat další ovladač nebo jiný software. Následující tabulka představuje souhrn těchto závislostí. Najdete v odkazovaných článcích podrobnosti. Možnosti konfigurace fondů služby Batch najdete v tématu dále v tomto článku.


### <a name="linux-pools---virtual-machine-configuration"></a>Fondy Linux – konfigurace virtuálního počítače

| Velikost | Schopnost | Operační systémy | Požadovaný software | Nastavení fondu |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances) | RDMA | Ubuntu 16.04 LTS,<br/>SUSE Linux Enterprise Server 12 prostředí HPC, nebo<br/>Založené na centOS HPC<br/>(Azure Marketplace) | Intel MPI 5 | Komunikace v rámci uzlu povolit, zakázat provedení souběžné úlohy |
| [Síťový adaptér, řada NCv2, NCv3, řada ND *](../virtual-machines/linux/n-series-driver-setup.md) | GPU NVIDIA Tesla (se liší podle řady) | Ubuntu 16.04 LTS,<br/>Red Hat Enterprise Linux 7.3 nebo 7.4, nebo<br/>CentOS 7.3 nebo 7.4<br/>(Azure Marketplace) | Ovladače NVIDIA CUDA Toolkit | neuvedeno | 
| [Řada NV](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS,<br/>Red Hat Enterprise Linux 7.3, nebo<br/>CentOS 7.3<br/>(Azure Marketplace) | Ovladače NVIDIA GRID | neuvedeno |

* Může vyžadovat připojení RDMA na virtuálních počítačích s podporou RDMA N-series [další konfiguraci](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) , která se liší podle distribuce.



### <a name="windows-pools---virtual-machine-configuration"></a>Fondy Windows – konfigurace virtuálního počítače

| Velikost | Schopnost | Operační systémy | Požadovaný software | Nastavení fondu |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016, 2012 R2, nebo<br/>2012 (azure Marketplace) | Microsoft MPI 2012 R2 nebo novější, nebo<br/> Intel MPI 5<br/><br/>Rozšíření virtuálního počítače Azure HpcVMDrivers | Komunikace v rámci uzlu povolit, zakázat provedení souběžné úlohy |
| [Síťový adaptér, řada NCv2, NCv3, řada ND *](../virtual-machines/windows/n-series-driver-setup.md) | GPU NVIDIA Tesla (se liší podle řady) | Windows Server 2016 nebo <br/>2012 R2 (Azure Marketplace) | Ovladače NVIDIA Tesla nebo ovladače CUDA Toolkit| neuvedeno | 
| [Řada NV](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 nebo<br/>2012 R2 (Azure Marketplace) | Ovladače NVIDIA GRID | neuvedeno |

* Připojení RDMA na virtuálních počítačích s podporou RDMA N-series se podporuje na Windows Server 2016 nebo Windows Server 2012 R2 (z Azure Marketplace) s příponou HpcVMDrivers a Microsoft MPI nebo Intel MPI.

### <a name="windows-pools---cloud-services-configuration"></a>Fondy Windows – konfigurace služby Cloud services

> [!NOTE]
> Velikosti řady N-series nejsou podporovány ve fondech Batch s konfigurací cloudové služby.
>

| Velikost | Schopnost | Operační systémy | Požadovaný software | Nastavení fondu |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016, 2012 R2, 2012 nebo<br/>2008 R2 (hostovaného operačního systému řady) | Microsoft MPI 2012 R2 nebo novější, nebo<br/>Intel MPI 5<br/><br/>Rozšíření virtuálního počítače Azure HpcVMDrivers | Povolení komunikace mezi uzly<br/> Zakázání spuštění souběžných úloh |





## <a name="pool-configuration-options"></a>Možnosti konfigurace fondu

Pokud chcete konfigurovat specializované velikost virtuálního počítače pro váš fond služby Batch, rozhraní API služby Batch a nástroje poskytují několik možností, jak nainstalovat požadovaný software nebo ovladačů, včetně:

* [Spouštěcí úkol](batch-api-basics.md#start-task) – odeslání instalačního balíčku jako soubor prostředků do účtu služby Azure storage ve stejné oblasti jako účet Batch. Vytvořte počáteční úlohy příkazového řádku pro tichou instalaci souboru prostředků při spuštění ve fondu. Další informace najdete v [dokumentaci k rozhraní REST API](/rest/api/batchservice/add-a-pool-to-an-account#bk_starttask).

  > [!NOTE] 
  > Spouštěcí úkol musíte spustit s oprávněními zvýšenými (správce) a je nutné čekat na úspěch.
  >

* [Balíček aplikace](batch-application-packages.md) – přidání komprimovaný instalační balíček do vašeho účtu Batch a nakonfigurovat odkaz na balíček ve fondu. Toto nastavení nahraje a unzips balíček na všech uzlech ve fondu. Pokud balíček instalačního programu, vytvořte počáteční úlohy příkazového řádku k tiché instalaci aplikace na všech uzlech fondu. Volitelně můžete nainstalujte balíček, když je úloha naplánována ke spuštění na uzlu.

* [Obrázek vlastního fondu](batch-custom-images.md) – vytvoření vlastní image Windows nebo linuxového virtuálního počítače, který obsahuje ovladače, softwaru, nebo jiné nastavení potřebných pro velikost virtuálního počítače. 

* [Batch loděnice](https://github.com/Azure/batch-shipyard) automaticky nakonfiguruje GPU a RDMA transparentně pracovat s kontejnerizované úlohy na Azure Batch. Batch loděnice zcela stanovených pro konfigurační soubory. Existuje mnoho ukázka předpisu konfigurace k dispozici, které umožňují používat úlohy GPU a RDMA, jako [CNTK GPU předpisu](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) který předem nakonfiguruje ovladače GPU na virtuálních počítačích řady N-series a načte softwaru Microsoft Cognitive Toolkit, image Dockeru.


## <a name="example-microsoft-mpi-on-an-a8-vm-pool"></a>Příklad: Microsoft MPI u fondu virtuální počítač A8

Pro spouštění aplikací Windows MPI na fondu uzlů Azure A8, musíte nainstalovat podporovanou implementaci MPI. Tady je ukázkový postup instalace [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) na Windows fondu pomocí balíčku pro aplikace Batch.

1. Stáhněte si [instalační balíček](https://go.microsoft.com/FWLink/p/?LinkID=389556) (MSMpiSetup.exe) pro nejnovější verzi Microsoft MPI.
2. Vytvořte soubor zip balíčku.
3. Nahrání balíčku k účtu Batch. Pokyny najdete v tématu [balíčky aplikací](batch-application-packages.md) pokyny. Zadejte id aplikace, například *MSMPI*a verze, jako *8.1*. 
4. Pomocí rozhraní API služby Batch na webu Azure portal, vytvořte fond do konfigurace služby cloud services s požadovaný počtem uzlů a škálování. Následující tabulka uvádí nastavení vzorku nastavit MPI v bezobslužném režimu pomocí spouštěcího úkolu:

| Nastavení | Hodnota |
| ---- | ----- | 
| **Typ image** | Cloud Services |
| **Řada operačního systému** | Windows Server 2012 R2 (operačního systému řady 4) |
| **Velikost uzlu** | Standardní a8 |
| **Komunikace mezi uzly povolena** | True |
| **Maximální počet úkolů na uzlu** | 1 |
| **Odkazy na balíček aplikace** | MSMPI |
| **Spouštěcí úkol povoleno** | True<br>**Příkazový řádek** - `"cmd /c %AZ_BATCH_APP_PACKAGE_MSMPI#8.1%\\MSMpiSetup.exe -unattend -force"`<br/>**Identita uživatele** -fondu automaticky vytvořený uživatel, správce<br/>**Čekat na úspěch** – True

## <a name="example-nvidia-tesla-drivers-on-nc-vm-pool"></a>Příklad: Ovladače NVIDIA Tesla ve fondu virtuálních počítačů síťového adaptéru

Ke spouštění aplikací CUDA na fondu uzlů Linux síťovým Adaptérem, musíte nainstalovat CUDA Toolkit 9.0 na uzly. Se sadou nástrojů nainstaluje potřebné ovladače NVIDIA Tesla GPU. Tady je ukázkový postup nasazení vlastní image Ubuntu 16.04 LTS pomocí ovladače GPU:

1. Nasazení běží Ubuntu 16.04 LTS virtuálního počítače Azure řady NC-series. Například můžete vytvořte virtuální počítač v oblasti USA střed – jih. Ujistěte se, že vytvoříte virtuální počítač při použití spravovaného disku.
2. Postupujte podle kroků pro připojení k virtuálnímu počítači a [instalovat ovladače CUDA](../virtual-machines/linux/n-series-driver-setup.md).
3. Zrušení zřízení agenta pro Linux a potom [zachycení image virtuálního počítače s Linuxem](../virtual-machines/linux/capture-image.md).
4. Vytvoření účtu Batch v oblasti, která podporuje virtuální počítače síťového adaptéru.
5. Pomocí rozhraní API služby Batch na webu Azure portal vytvořte fond [pomocí vlastní image](batch-custom-images.md) a požadovaný počet uzlů a škálování. V následující tabulce jsou uvedeny ukázkový fond nastavení bitové kopie:

| Nastavení | Hodnota |
| ---- | ---- |
| **Typ image** | Vlastní Image |
| **Vlastní Image** | Název bitové kopie |
| **SKU agenta uzlu** | batch.Node.Ubuntu 16.04 |
| **Velikost uzlu** | NC6 Standard |



## <a name="next-steps"></a>Další postup

* Spouštění úloh MPI ve fondu služby Azure Batch, najdete v článku [Windows](batch-mpi.md) nebo [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) příklady.

* Příklady GPU úloh v Batch, najdete v článku [Batch loděnice](https://github.com/Azure/batch-shipyard/) recepty.