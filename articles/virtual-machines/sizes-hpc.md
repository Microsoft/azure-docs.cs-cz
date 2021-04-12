---
title: Velikosti virtuálních počítačů Azure – HPC | Microsoft Docs
description: Zobrazuje seznam různých velikostí dostupných pro vysoce výkonné výpočetní virtuální počítače v Azure. Uvádí informace o počtu vCPU, datových discích a síťových rozhraních a propustnosti úložiště a šířce pásma sítě pro velikosti v této sérii.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/19/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: a41dce28427db40dfd19879e4ada95add64009c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772429"
---
# <a name="high-performance-computing-vm-sizes"></a>Vysoce výkonné výpočetní velikosti virtuálních počítačů

Virtuální počítače Azure H-Series jsou navržené tak, aby poskytovaly výkon, škálovatelnost a cenovou efektivitu pro různé úlohy HPC reálného světa.

[HBv3-Series](hbv3-series.md) Virtuální počítače jsou optimalizované pro aplikace HPC, jako je kapalinový dynamika, explicitní a implicitní analýza nekonečných prvků, modelování počasí, zpracování seismických procesů, simulace zásobníku a simulaci RTL. Virtuální počítače s HBv3 jsou až 120 AMD EPYC™ 7003-Series (Milán) jádra procesoru, 448 GB paměti RAM a žádného podprocesu. Virtuální počítače řady HBv3-Series také poskytují 350 GB/s šířky pásma paměti, až 32 MB mezipaměti L3 na jádro, až 7 GB/s v případě výkonu disku SSD a frekvence hodin až 3,675 GHz. 

Všechny virtuální počítače HBv3-Series funkce 200 GB/s HDR InfiniBand ze sítě NVIDIA umožňují úlohy MPI s horizontálním škálováním na více počítačů. Tyto virtuální počítače jsou připojené v neblokujícím stromu FAT pro optimalizaci a konzistentní výkon RDMA. Topologie HDR InfiniBand také podporuje adaptivní směrování a dynamický propojený přenos (DCT, kromě standardních RC a UD transportů). Tyto funkce zvyšují výkon, škálovatelnost a konzistenci aplikací a jejich použití se důrazně doporučuje.

[HBv2-Series](hbv2-series.md) Virtuální počítače jsou optimalizované pro aplikace, které jsou založené na šířce pásma, jako je například kapalinová dynamika, analýza omezeného prvku a simulace zásobníku. Virtuální počítače s HBv2 120 AMD EPYC 7742 procesory, 4 GB paměti RAM na jádro procesoru a žádné souběžné multithreadingy. Každý virtuální počítač HBv2 poskytuje šířku pásma až 340 GB/s a až 4 teraFLOPSy FP64 Compute.

Virtuální počítače s HBv2 200 GB/s Mellanox HDR InfiniBand, zatímco virtuální počítače s funkcí geti HC-Series 100 GB/s Mellanox EDR InfiniBand. Každý z těchto typů virtuálních počítačů je připojen v neblokujícím stromu FAT pro optimalizaci a konzistentní výkon RDMA. Virtuální počítače s HBv2 podporují adaptivní směrování a dynamický propojený přenos (DCT, kromě standardních RC a UD transportů). Tyto funkce zvyšují výkon, škálovatelnost a konzistenci aplikací a jejich použití se důrazně doporučuje.

[Řady s více procesory](hb-series.md) Virtuální počítače jsou optimalizované pro aplikace, které jsou založené na šířce pásma, jako je například kapalinová dynamika, explicitní konečná analýza elementu a modelování počasí. Virtuální počítače s funkcí 60 AMD EPYC 7551 procesory, 4 GB paměti RAM na jádro procesoru a žádné podprocesy. Platforma AMD EPYC poskytuje šířku pásma větší než 260 GB/s.

[Řada HC-Series](hc-series.md) Virtuální počítače jsou optimalizované pro aplikace, které jsou založené na hustém výpočtu, jako je například implicitní nekonečná analýza elementu, molekulová dynamika a výpočetní chemie. Virtuální počítače HC – funkce 44 Intel Xeon Platinum 8168, 8 GB paměti RAM na jádro procesoru a žádné podprocesy. Platforma Intel Xeon Platinum podporuje bohatě bohatý ekosystém softwarových nástrojů od společnosti Intel, jako je například knihovna Intel Math kernel.

[Řada H-Series](h-series.md) Virtuální počítače jsou optimalizované pro aplikace řízené vysokými kmitočty procesoru nebo velkým množstvím paměti podle základních požadavků. Virtuální počítače řady H-Series funkce 8 nebo 16 Intel Xeon E5 2667 V3 procesory, 7 nebo 14 GB paměti RAM na jádro procesoru a žádné podprocesy. Funkce H-Series 56 GB/s Mellanox FDR InfiniBand v neblokované konfiguraci stromu FAT pro zajištění konzistentního výkonu RDMA. Virtuální počítače H-series podporují Intel MPI 5. x a MS-MPI.

> [!NOTE]
> Všechny virtuální počítače HBv3, HBv2, Exclusive a HC-Series mají exkluzivní přístup k fyzickým serverům. K dispozici je jen 1 virtuální počítač na fyzický server a pro tyto velikosti virtuálních počítačů není k dispozici žádná sdílená víceklientská aplikace s žádnými jinými virtuálními počítači.

> [!NOTE]
> [Virtuální počítače A8 – A11](./sizes-previous-gen.md#a-series---compute-intensive-instances) jsou vyřazeny od 3/2021. Žádná nová nasazení virtuálních počítačů těchto velikostí teď nejsou možná. Pokud máte existující virtuální počítače, přečtěte si e-mailová oznámení o dalších krocích, včetně migrace na jiné velikosti virtuálních počítačů v [příručce pro migraci HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>Instance s podporou RDMA

Většina velikostí virtuálních počítačů HPC funguje jako síťové rozhraní pro připojení vzdáleného přímého přístupu do paměti (RDMA). Vybrané velikosti [N-Series](./nc-series.md) označené řetězcem r jsou taky podporující RDMA. Toto rozhraní je kromě standardního síťového rozhraní sítě Azure dostupné v dalších velikostech virtuálních počítačů.

Toto sekundární rozhraní umožňuje, aby instance s podporou RDMA komunikovaly přes síť InfiniBand (IB), která pracuje s sazbami za HDR pro HBv3, HBv2, EDR sazby pro třídy pro NDv2, FDR a další virtuální počítače s podporou RDMA N-Series. Tyto možnosti RDMA můžou zvýšit škálovatelnost a výkon aplikací založených na rozhraní MPI (Message Passing Interface).

> [!NOTE]
> **Podpora SR-IOV**: ve službě Azure HPC v současné době existují dvě třídy virtuálních počítačů v závislosti na tom, jestli jsou pro InfiniBand povolené rozhraní SR-IOV. V současné době skoro všechny novější generace a virtuální počítače s podporou RDMA nebo InfiniBand v Azure mají povolený rozhraní SR-IOV s výjimkou H16r, H16mr a NC24r.
> RDMA se povoluje jenom přes síť InfiniBand (IB) a podporuje se u všech virtuálních počítačů podporujících RDMA.
> IP přes IB se podporuje jenom na virtuálních počítačích s povolenou SR-IOV.
> RDMA není povolený přes síť Ethernet.

- Často se používají distribuce **operačního systému** , jako jsou CentOS, RHEL, Ubuntu, SUSE. Windows Server 2016 a novější verze jsou podporovány ve všech virtuálních počítačích řady HPC. Systémy Windows Server 2012 R2 a Windows Server 2012 jsou podporovány také na virtuálních počítačích s povolenou podporou rozhraní SR-IOV. Počítejte s tím, že [systém Windows Server 2012 R2 není podporován v HBv2 a vyšší jako velikosti virtuálních počítačů s více než 64 (virtuálními nebo fyzickými) jádry](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows). Seznam podporovaných imagí virtuálních počítačů na webu Marketplace najdete v tématu [image virtuálních počítačů](./workloads/hpc/configure.md) a jejich správné konfigurace. Příslušné stránky velikosti virtuálních počítačů také uvádějí podporu softwarového zásobníku.

- **InfiniBand a ovladače** – na virtuálních počítačích s povolenou InfiniBand jsou k povolení RDMA potřeba příslušné ovladače. Seznam podporovaných imagí virtuálních počítačů na webu Marketplace najdete v tématu [image virtuálních počítačů](./workloads/hpc/configure.md) a jejich správné konfigurace. Další informace o rozšířeních virtuálních počítačů nebo ruční instalaci ovladačů InfiniBand najdete také v tématu [Povolení InfiniBand](./workloads/hpc/enable-infiniband.md) .

- **MPI** – velikosti virtuálních počítačů s povoleným rozhraním SR-IOV v Azure umožňují téměř jakýkoli charakter MPI pro použití s Mellanox OFED. V případě virtuálních počítačů s podporou SR-IOV podporované implementace MPI používají ke komunikaci mezi virtuálními počítači rozhraní Microsoft Network Direct (ND). Proto jsou podporovány pouze Intel MPI 5. x a Microsoft MPI (MS-MPI) 2012 R2 nebo novější verze. Novější verze běhové knihovny Intel MPI můžou nebo nemusí být kompatibilní s ovladači Azure RDMA. Další podrobnosti o nastavení MPI na virtuálních počítačích HPC v Azure najdete v tématu [Nastavení MPI pro HPC](./workloads/hpc/setup-mpi.md) .

  > [!NOTE]
  > **Adresní prostor RDMA sítě**: síť RDMA v Azure rezervuje adresní prostor 172.16.0.0/16. Pokud chcete spouštět aplikace MPI na instancích nasazených ve službě Azure Virtual Network, ujistěte se, že adresní prostor virtuální sítě nepřekrývá síť RDMA.

## <a name="cluster-configuration-options"></a>Možnosti konfigurace clusteru

Azure nabízí několik možností, jak vytvořit clustery virtuálních počítačů HPC, které mohou komunikovat pomocí sítě RDMA, včetně těchto: 

- **Virtuální počítače**  – nasazení virtuálních počítačů HPC podporujících RDMA ve stejné sadě škálování nebo skupině dostupnosti (při použití modelu nasazení Azure Resource Manager). Pokud používáte model nasazení Classic, nasaďte virtuální počítače do stejné cloudové služby.

- **Virtual Machine Scale Sets** – v sadě škálování virtuálního počítače Nezapomeňte toto nasazení omezit na jednu skupinu umístění pro InfiniBand komunikaci v rámci sady škálování. Například v šabloně Správce prostředků nastavte `singlePlacementGroup` vlastnost na `true` . Všimněte si, že maximální velikost sady škálování, kterou `singlePlacementGroup=true` je možné proomezené, je ve výchozím nastavení na 100 virtuálních počítačů. Pokud vaše požadavky na škálování úlohy HPC jsou vyšší než 100 virtuálních počítačů v jednom tenantovi, můžete požádat o zvýšení, [otevřít Online žádost o zákaznickou podporu](../azure-portal/supportability/how-to-create-azure-support-request.md) bez poplatků. Omezení počtu virtuálních počítačů v jedné sadě škálování se dá zvýšit na 300. Všimněte si, že při nasazování virtuálních počítačů pomocí skupin dostupnosti je maximální limit na 200 virtuálních počítačů na skupinu dostupnosti.

  > [!NOTE]
  > **MPI mezi virtuálními počítači**: Pokud je mezi virtuálními počítači (VM) požadováno RDMA (například použití komunikace MPI), ujistěte se, že jsou virtuální počítače ve stejné sadě nebo skupině dostupnosti virtuálních počítačů.

- **Azure CycleCloud** – vytvoření clusteru HPC pomocí [Azure CycleCloud](/azure/cyclecloud/) ke spouštění úloh MPI

- **Azure Batch** – vytvořte fond [Azure Batch](../batch/index.yml) pro spouštění úloh MPI. Pokud chcete používat instance náročné na výpočetní výkon při spouštění aplikací MPI s Azure Batch, přečtěte si téma [použití úloh s více instancemi ke spouštění aplikací MPI (Message Passing Interface) v Azure Batch](../batch/batch-mpi.md).

- **Sada Microsoft HPC Pack**  -  [HPC Pack](/powershell/high-performance-computing/overview) zahrnuje běhové prostředí pro MS-MPI, které používá síť Azure RDMA při nasazení na virtuální počítače Linux s podporou RDMA. Například nasazení najdete v tématu [Nastavení clusteru Linux RDMA se sadou HPC Pack pro spouštění aplikací MPI](/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Aspekty nasazování

- **Předplatné Azure** – Chcete-li nasadit více než několik několika instancí náročných na výpočetní výkon, vezměte v úvahu předplatné s průběžnými platbami nebo jiné možnosti nákupu. Pokud používáte [bezplatný účet Azure](https://azure.microsoft.com/free/), můžete použít pouze omezený počet výpočetních jader Azure.

- **Ceny a dostupnost** – zkontroluje ceny a [dostupnost](https://azure.microsoft.com/global-infrastructure/services/) [virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) pomocí oblastí Azure.

- **Kvóty jader** – možná bude potřeba zvýšit kvótu jader v předplatném Azure z výchozí hodnoty. Vaše předplatné může také omezit počet jader, které můžete nasadit v určitých rodinách velikostí virtuálních počítačů, včetně řady H-Series. Chcete-li požádat o zvýšení kvóty, [otevřete online žádost o zákaznickou podporu](../azure-portal/supportability/how-to-create-azure-support-request.md) zdarma. (Výchozí omezení se můžou lišit v závislosti na vaší kategorii předplatného.)

  > [!NOTE]
  > Pokud máte velké nároky na kapacitu, obraťte se na podporu Azure. Kvóty Azure jsou úvěrovými limity, které nezaručují kapacitu. Bez ohledu na vaši kvótu se účtují jenom ty jádra, které používáte.
  
- **Virtual Network** – [virtuální síť](https://azure.microsoft.com/documentation/services/virtual-network/) Azure není nutná k používání instancí náročných na výpočetní výkon. Pro mnoho nasazení ale potřebujete alespoň cloudovou virtuální síť Azure nebo připojení typu Site-to-site, pokud potřebujete přístup k místním prostředkům. V případě potřeby vytvořte novou virtuální síť pro nasazení instancí. Přidání virtuálních počítačů náročných na výpočetní výkon do virtuální sítě ve skupině vztahů se nepodporuje.

- **Změna velikosti** – z důvodu jejich specializovaného hardwaru můžete měnit velikost jenom pro instance náročné na výpočetní výkon v rámci stejné řady velikostí (H-Series nebo N-Series). Například můžete změnit velikost virtuálního počítače H-Series jenom z jedné velikosti řady H-Series na jinou. Další okolnosti týkající se podpory ovladačů InfiniBand a disků NVMe může být potřeba vzít v úvahu pro některé virtuální počítače.


## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované pro výpočty](sizes-compute.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU.](sizes-gpu.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [konfiguraci virtuálních počítačů](./workloads/hpc/configure.md), [Povolení INFINIBAND](./workloads/hpc/enable-infiniband.md), [Nastavení MPI](./workloads/hpc/setup-mpi.md) a optimalizaci aplikací HPC pro Azure v [úlohách HPC](./workloads/hpc/overview.md).
- Přečtěte si přehled [HBv3-Series](./workloads/hpc/hbv3-series-overview.md) Overview a [HC-Series](./workloads/hpc/hc-series-overview.md).
- Přečtěte si o nejnovějších oznámeních, příkladech úloh HPC a výsledcích výkonu na [blogu Azure COMPUTE tech Community](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pro zobrazení architektury na vyšší úrovni pro spouštění úloh HPC si přečtěte téma věnované technologii [HPC (High Performance Computing) v Azure](/azure/architecture/topics/high-performance-computing/).
