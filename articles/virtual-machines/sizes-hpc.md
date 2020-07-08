---
title: Velikosti virtuálních počítačů Azure – HPC | Microsoft Docs
description: Zobrazuje seznam různých velikostí dostupných pro vysoce výkonné výpočetní virtuální počítače v Azure. Uvádí informace o počtu vCPU, datových discích a síťových rozhraních a propustnosti úložiště a šířce pásma sítě pro velikosti v této sérii.
author: vermagit
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 2d52287d1c343ada58ed4f7e5e1d3e85a4e7162e
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85850444"
---
# <a name="high-performance-computing-vm-sizes"></a>Vysoce výkonné výpočetní velikosti virtuálních počítačů

Virtuální počítače Azure H-Series jsou navržené tak, aby poskytovaly výkon výkonné třídy, škálovatelnost MPI a cenovou efektivitu pro celou řadu úloh HPC ve skutečném světě.

[HBv2-Series](hbv2-series.md) Funkce virtuálních počítačů 200 GB/s Mellanox HDR InfiniBand, zatímco virtuální počítače s funkcí geti HC-Series 100 GB/s Mellanox EDR InfiniBand. Každý z těchto typů virtuálních počítačů je připojen v neblokujícím stromu FAT pro optimalizaci a konzistentní výkon RDMA. Virtuální počítače s HBv2 podporují adaptivní směrování a dynamický propojený přenos (DCT, ve více než standard RC a UD Transports). Tyto funkce zvyšují výkon, škálovatelnost a konzistenci aplikací a jejich využití se důrazně doporučuje.

[Řady s více procesory](hb-series.md) Virtuální počítače jsou optimalizované pro aplikace, které jsou založené na šířce pásma, jako je například kapalinová dynamika, explicitní konečná analýza elementu a modelování počasí. Virtuální počítače s funkcí 60 AMD EPYC 7551 procesory, 4 GB paměti RAM na jádro procesoru a žádné podprocesy. Platforma AMD EPYC poskytuje šířku pásma větší než 260 GB/s.

[Řada HC-Series](hc-series.md) Virtuální počítače jsou optimalizované pro aplikace, které jsou založené na hustém výpočtu, jako je například implicitní nekonečná analýza elementu, molekulová dynamika a výpočetní chemie. Virtuální počítače HC – funkce 44 Intel Xeon Platinum 8168, 8 GB paměti RAM na jádro procesoru a žádné podprocesy. Platforma Intel Xeon Platinum podporuje bohatě bohatý ekosystém softwarových nástrojů od společnosti Intel, jako je například knihovna Intel Math kernel.

[Řada H-Series](h-series.md) Virtuální počítače jsou optimalizované pro aplikace řízené vysokými kmitočty procesoru nebo velkým množstvím paměti podle základních požadavků. Virtuální počítače řady H-Series funkce 8 nebo 16 Intel Xeon E5 2667 V3 procesory, 7 nebo 14 GB paměti RAM na jádro procesoru a žádné podprocesy. Funkce H-Series 56 GB/s Mellanox FDR InfiniBand v neblokované konfiguraci stromu FAT pro zajištění konzistentního výkonu RDMA. Virtuální počítače H-series podporují Intel MPI 5. x a MS-MPI.

> [!NOTE]
> Virtuální počítače A8 – A11 jsou plánovány k vyřazení na 3/2021. Další informace najdete v tématu [Průvodce migrací HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>Instance s podporou RDMA

Většina velikostí virtuálních počítačů HPC (HBv2, Get, HC, H16r, H16mr, A8 a c) funguje jako síťové rozhraní pro připojení vzdáleného přímého přístupu do paměti (RDMA). Vybrané velikosti [řady N-Series](https://docs.microsoft.com/azure/virtual-machines/nc-series) označené "r", jako jsou například konfigurace NC24rs (NC24rs_v3, NC24rs_v2 a NC24r), jsou také podporující technologii RDMA. Toto rozhraní je navíc ke standardním síťovým rozhraním Azure, které je dostupné v dalších velikostech virtuálních počítačů.

Toto rozhraní umožňuje, aby instance s podporou RDMA komunikovaly přes síť InfiniBand (IB), která pracuje s sazbami HDR pro HBv2, EDR sazbami pro FDR, H16r a virtuálními počítači řady N-Series s podporou RDMA a s H16MR sazbami pro virtuální počítače A8 a c. Tyto možnosti RDMA můžou zvýšit škálovatelnost a výkon určitých aplikací MPI (Message Passing Interface). Další informace o rychlosti najdete v podrobnostech v tabulkách na této stránce.

> [!NOTE]
> V prostředí Azure HPC existují dvě třídy virtuálních počítačů v závislosti na tom, jestli mají rozhraní SR-IOV povolené pro InfiniBand. V současné době je rozhraní SR-IOV pro virtuální počítače s povolenou InfiniBand: HBv2,, HC, NCv3 a NDv2. Zbývající virtuální počítače s povolenou InfiniBand nemají povolený SR-IOV.
> U všech virtuálních počítačů podporujících RDMA je podpora RDMA přes IB podporovaná.
> IP přes IB se podporuje jenom na virtuálních počítačích s povolenou SR-IOV.

- **Operační systém** – Linux je velmi dobře podporovaný pro virtuální počítače HPC; distribuce, jako je CentOS, RHEL, Ubuntu, SUSE, se běžně používají. Týkající se podpory Windows je podpora Windows serveru 2016 a novějších verzí na všech virtuálních počítačích řady HPC. Windows Server 2012 R2, Windows Server 2012 se podporuje taky na virtuálních počítačích s povolenou instancí nevyužívajících SR-IOV (H16r, H16mr, A8 a c \ c). Upozorňujeme, že [systém Windows Server 2012 R2 není podporován na HBv2 a dalších virtuálních počítačích s více než 64 (virtuálními nebo fyzickými) jádry](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).

- **MPI** – velikosti virtuálních počítačů s povoleným rozhraním SR-IOV v Azure (HBv2,, HC, NCv3, NDv2) umožňují téměř jakýkoli charakter MPI pro použití s Mellanox OFED.
V případě virtuálních počítačů s podporou SR-IOV podporované implementace MPI používají ke komunikaci mezi virtuálními počítači rozhraní Microsoft Network Direct (ND). Proto jsou podporovány pouze verze Microsoft MPI (MS-MPI) 2012 R2 nebo novější a Intel MPI 5. x. Novější verze (2017, 2018) běhové knihovny Intel MPI mohou nebo nemusí být kompatibilní s ovladači Azure RDMA.

- **InfiniBandDriver<Linux | Rozšíření Windows> VM** – na virtuálních počítačích s podporou RDMA přidejte InfiniBandDriver<Linux | Rozšíření Windows> pro povolení InfiniBand V systému Linux rozšíření virtuálního počítače InfiniBandDriverLinux nainstaluje ovladače Mellanox OFED (na virtuálních počítačích SR-IOV) pro připojení RDMA. V systému Windows rozšíření virtuálního počítače InfiniBandDriverWindows nainstaluje ovladače síťové technologie Windows (na virtuálních počítačích bez SR-IOV) nebo ovladače Mellanox OFED (na virtuálních počítačích s SR-IOV) pro připojení RDMA.
V některých nasazeních instancí A8 a A8 se rozšíření HpcVmDrivers přidá automaticky. Všimněte si, že rozšíření virtuálního počítače HpcVmDrivers je zastaralé; nebude aktualizován.
Pokud chcete přidat rozšíření virtuálního počítače do virtuálního počítače, můžete použít rutiny [Azure PowerShell](/powershell/azure/overview) . 

  Následující příkaz nainstaluje nejnovější rozšíření verze 1,0 InfiniBandDriverWindows na existující virtuální počítač s podporou RDMA s názvem *myVM* nasazený ve skupině prostředků s názvem *myResourceGroup* v oblasti *západní USA* :

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```

  Alternativně lze rozšíření virtuálních počítačů zahrnout do šablon Azure Resource Manager pro snadné nasazení s následujícím elementem JSON:

  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  Následující příkaz nainstaluje nejnovější InfiniBandDriverWindows rozšíření verze 1,0 na všechny virtuální počítače podporující RDMA ve stávající sadě virtuálních počítačů s názvem *myVMSS* nasazenou ve skupině prostředků s názvem *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Další informace najdete v tématu [rozšíření a funkce virtuálních počítačů](./extensions/overview.md). Můžete také pracovat s rozšířeními pro virtuální počítače nasazené v [modelu nasazení Classic](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

- **Adresní prostor síťového adres RDMA** – síť RDMA v Azure rezervuje adresní prostor 172.16.0.0/16. Pokud chcete spouštět aplikace MPI na instancích nasazených ve službě Azure Virtual Network, ujistěte se, že adresní prostor virtuální sítě nepřekrývá síť RDMA.

## <a name="cluster-configuration-options"></a>Možnosti konfigurace clusteru

Azure poskytuje několik možností pro vytváření clusterů virtuálních počítačů Windows HPC, které mohou komunikovat pomocí sítě RDMA, včetně těchto: 

- **Virtuální počítače** – nasazení virtuálních počítačů HPC podporujících RDMA ve stejné sadě škálování nebo skupině dostupnosti (při použití modelu nasazení Azure Resource Manager). Pokud používáte model nasazení Classic, nasaďte virtuální počítače do stejné cloudové služby.

- **Virtual Machine Scale Sets** – ve virtuálním počítači škálované sadě (VMSS) nezapomeňte omezit nasazení na jednu skupinu umístění pro komunikaci InfiniBand v rámci VMSS. Například v šabloně Správce prostředků nastavte `singlePlacementGroup` vlastnost na `true` . Všimněte si, že maximální velikost VMSS, kterou je možné pomocí vlastnosti vymezit, `singlePlacementGroup` `true` je omezené na 100 virtuálních počítačů ve výchozím nastavení. Pokud vaše požadavky na škálování úlohy HPC jsou vyšší než 100 virtuálních počítačů v rámci jednoho tenanta VMSS, můžete požádat o zvýšení, [otevřít Online žádost o zákaznickou podporu](../azure-supportability/how-to-create-azure-support-request.md) bez poplatků. Omezení počtu virtuálních počítačů v jednom VMSS se dá zvýšit na 300. Všimněte si, že při nasazování virtuálních počítačů pomocí skupin dostupnosti je maximální limit na 200 virtuálních počítačů na skupinu dostupnosti.

- **MPI mezi virtuálními počítači** – Pokud se pro virtuální počítače (VM) vyžaduje RDMA (např. použití komunikace MPI), ujistěte se, že jsou virtuální počítače ve stejné sadě nebo skupině dostupnosti virtuálních počítačů.

- **Azure CycleCloud** – vytvoření clusteru HPC v [Azure CycleCloud](/azure/cyclecloud/) pro spouštění úloh MPI

- **Azure Batch** – vytvořte fond [Azure Batch](/azure/batch/) pro spouštění úloh MPI. Pokud chcete používat instance náročné na výpočetní výkon při spouštění aplikací MPI s Azure Batch, přečtěte si téma [použití úloh s více instancemi ke spouštění aplikací MPI (Message Passing Interface) v Azure Batch](../batch/batch-mpi.md).

- **Sada Microsoft HPC Pack**  -  [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) zahrnuje běhové prostředí pro MS-MPI, které používá síť Azure RDMA při nasazení na virtuální počítače Linux s podporou RDMA. Například nasazení najdete v tématu [Nastavení clusteru Linux RDMA se sadou HPC Pack pro spouštění aplikací MPI](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Aspekty nasazování

- **Předplatné Azure** – Chcete-li nasadit více než několik několika instancí náročných na výpočetní výkon, vezměte v úvahu předplatné s průběžnými platbami nebo jiné možnosti nákupu. Pokud používáte [bezplatný účet Azure](https://azure.microsoft.com/free/), můžete použít pouze omezený počet výpočetních jader Azure.

- **Ceny a dostupnost** – tyto velikosti virtuálních počítačů se nabízejí jenom v cenové úrovni Standard. Podívejte se na [produkty dostupné v oblasti a](https://azure.microsoft.com/global-infrastructure/services/) dostupnost v oblastech Azure.

- **Kvóty jader** – možná bude potřeba zvýšit kvótu jader v předplatném Azure z výchozí hodnoty. Vaše předplatné může také omezit počet jader, které můžete nasadit v určitých rodinách velikostí virtuálních počítačů, včetně řady H-Series. Chcete-li požádat o zvýšení kvóty, [otevřete online žádost o zákaznickou podporu](../azure-supportability/how-to-create-azure-support-request.md) zdarma. (Výchozí omezení se můžou lišit v závislosti na vaší kategorii předplatného.)

  > [!NOTE]
  > Pokud máte velké nároky na kapacitu, obraťte se na podporu Azure. Kvóty Azure jsou úvěrovými limity, které nezaručují kapacitu. Bez ohledu na vaši kvótu se účtují jenom ty jádra, které používáte.
  
- **Virtual Network** – [virtuální síť](https://azure.microsoft.com/documentation/services/virtual-network/) Azure není nutná k používání instancí náročných na výpočetní výkon. Pro mnoho nasazení ale potřebujete alespoň cloudovou virtuální síť Azure nebo připojení typu Site-to-site, pokud potřebujete přístup k místním prostředkům. V případě potřeby vytvořte novou virtuální síť pro nasazení instancí. Přidání virtuálních počítačů náročných na výpočetní výkon do virtuální sítě ve skupině vztahů se nepodporuje.

- **Změna velikosti** – z důvodu jejich specializovaného hardwaru můžete změnit velikost jenom těch instancí náročných na výpočetní výkon v rámci stejné řady velikostí (H-Series nebo výpočetní výkon a-Series). Například můžete změnit velikost virtuálního počítače H-Series jenom z jedné velikosti řady H-Series na jinou. Kromě toho se nepodporují změny velikosti z nevýpočetní velikosti s náročnou na výpočetní výkon.  


## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

- Další informace o optimalizaci aplikace HPC pro Azure a některých příkladů v [úlohách HPC](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/overview) 

- Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.
