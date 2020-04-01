---
title: Velikosti virtuálních počítačů Azure – HPC | Dokumenty společnosti Microsoft
description: Uvádí různé velikosti, které jsou k dispozici pro virtuální počítače s vysokým výkonem v Azure. Obsahuje informace o počtu virtuálních procesorů, datových disků a síťových síťových karty, jakož i propustnost úložiště a šířku pásma sítě pro velikosti v této řadě.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: df22c857571e51bb886ff1d25db185a306999540
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420870"
---
# <a name="high-performance-computing-vm-sizes"></a>Vysoce výkonné výpočetní velikosti virtuálních počítačů

Virtuální počítače (VM) řady Azure H jsou navržené tak, aby poskytovaly výkon, škálovatelnost MPI a efektivitu nákladů pro celou řadu reálných úloh HPC.

[Řada HBv2](hbv2-series.md) Virtuální počítače jsou vybaveny 200 Gb/s Mellanox HDR InfiniBand, zatímco virtuální počítače řady HB i HC jsou vybaveny 100 Gb/s Mellanox EDR InfiniBand. Každý z těchto typů virtuálních počítačů jsou připojeny v neblokující tukové strompro optimalizovaný a konzistentní výkon RDMA. Virtuální servery HBv2 podporují adaptivní směrování a dynamické připojené přenosy (DCT, navíc ke standardním rc a UD přenosům). Tyto funkce zvyšují výkon aplikace, škálovatelnost a konzistenci a jejich použití se důrazně doporučuje.

[Řada HB](hb-series.md) Virtuální počítači jsou optimalizované pro aplikace řízené šířkou pásma paměti, jako je dynamika tekutin, explicitní analýza konečných prvků a modelování počasí. Virtuální počítače HB jsou vybaveny procesorovými jádry 60 AMD EPYC 7551, 4 GB paměti RAM na jádro procesoru a bez hyperthreadingu. Platforma AMD EPYC poskytuje šířku pásma více než 260 GB/s.

[Řada HC](hc-series.md) Virtuální virtuální počítač je optimalizovaný pro aplikace řízené hustým výpočtem, jako je implicitní analýza konečných prvků, molekulární dynamika a výpočetní chemie. Virtuální počítače HC jsou vybaveny procesorovými jádry 44 Intel Xeon Platinum 8168, 8 GB paměti RAM na jádro procesoru a bez hyperthreadingu. Platforma Intel Xeon Platinum podporuje bohatý ekosystém softwarových nástrojů společnosti Intel, jako je knihovna jádra Intel Math.

[Řada H](h-series.md) Virtuální počítače jsou optimalizované pro aplikace řízené vysokými frekvencemi procesoru nebo velkou pamětí na základní požadavky. Virtuální počítače řady H jsou vybaveny procesorovými jádry 8 nebo 16 Intel Xeon E5 2667 v3, 7 nebo 14 GB paměti RAM na jádro procesoru a bez hyperthreadingu. Řada H je vybavena 56 Gb/s Mellanox FDR InfiniBand v neblokující konfiguraci tuku pro konzistentní výkon RDMA. Virtuální servery řady H podporují technologie Intel MPI 5.x a MS-MPI.

> [!NOTE]
> Virtuální počítače A8 – A11 jsou plánovány na vyřazení do důchodu na 3/2021. Další informace naleznete v [průvodci migrací HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>Instance s podporou RDMA

Většina velikostí virtuálních počítačů HPC (HBv2, HB, HC, H16r, H16mr, A8 a A9) je vybavena síťovým rozhraním pro připojení vzdáleného přímého přístupu do paměti (RDMA). Vybrané [N-series]https://docs.microsoft.com/azure/virtual-machines/nc-series) (velikosti označené "r", jako jsou konfigurace NC24rs (NC24rs_v3, NC24rs_v2 a NC24r) jsou také schopné RDMA. Toto rozhraní je navíc ke standardnímu síťovému rozhraní Azure, které je dostupné v ostatních velikostech virtuálních zařízení.

Toto rozhraní umožňuje instancím podporujícím RDMA komunikovat prostředpou prostředpou sítě InfiniBand (IB), pracující s rychlostí HDR pro HBv2, sazby EDR pro hb, HC, FDR pro virtuální počítače řady N S podporou H16r, H16mr a RDMA a sazby QDR pro virtuální počítače A8 a A9. Tyto funkce RDMA můžete zvýšit škálovatelnost a výkon některých aplikací message passing interface (MPI). Další informace o rychlosti naleznete v podrobnostech v tabulkách na této stránce.

> [!NOTE]
> V Azure HPC existují dvě třídy virtuálních počítačů v závislosti na tom, zda jsou sR-IOV povolené pro InfiniBand. V současné době jsou virtuální počítače s povolenou technologií SR-IOV pro InfiniBand: HBv2, HB, HC a NCv3. Ostatní virtuální chod infinibandu nejsou povoleny SR-IOV.
> RDMA přes IB je podporována pro všechny virtuální společnosti podporující RDMA.
> IP přes IB je podporována jenom na virtuálních počítačích s povolenou SR-IOV.

- **Operační systém** - Linux je velmi dobře podporován pro HPC VM, distribuce jako CentOS, RHEL, Ubuntu, SUSE jsou běžné. Pokud jde o podporu Windows, Windows Server 2016 je podporován na všech virtuálních počítačích řady HPC. Windows Server 2012 R2, Windows Server 2012 jsou také podporované na virtuálních počítačích s nepovoleným SR-IOV.

- **MPI** – Velikosti virtuálních zařízení s povoleným SR-IOV v Azure (HBv2, HB, HC, NCv3) umožňují použití téměř libovolné varianty MPI s Mellanox OFED.
U virtuálních počítačů s podporou SR-IOV používají podporované implementace MPI ke komunikaci mezi virtuálními počítači rozhraní Microsoft Network Direct (ND) rozhraní Microsoft Network Direct (ND). Proto jsou podporovány pouze microsoft mpi (MS-MPI) 2012 R2 nebo novější a Intel MPI 5.x verze. Novější verze (2017, 2018) knihovny runtime Intel MPI může nebo nemusí být kompatibilní s ovladači Azure RDMA.

- **InfiniBandDriver<Linux| Rozšíření virtuálního počítače s> Windows** – na virtuálních počítačích podporujících RDMA přidejte ovladač InfiniBandDriver<Linuxem| Rozšíření windows> pro povolení infinibandu. V Linuxu nainstaluje rozšíření virtuálního počítače InfiniBandDriverLinux ovladače Mellanox OFED (na virtuálních počítačích SR-IOV) pro připojení RDMA. V systému Windows instaluje rozšíření virtuálního zařízení InfiniBandDriverWindows ovladače windows network direct (na ovladačích bez virtuálních zařízení SR-IOV) nebo ovladače Mellanox OFED (na virtuálních počítačích RDMA SR-IOV) pro připojení.
V některých nasazeních a8 a a9 instance hpcVmdrivers rozšíření je přidán automaticky. Všimněte si, že rozšíření virtuálního počítače HpcVmDrivers je zastaralé; nebude aktualizován.
Pokud chcete přidat rozšíření virtuálního počítače k virtuálnímu počítači, můžete použít rutiny [Azure PowerShell.](/powershell/azure/overview) 

  Následující příkaz nainstaluje nejnovější verzi 1.0 InfiniBandDriverWindows rozšíření na existující virtuální počítač podporující RDMA s názvem *myVM* nasazený ve skupině prostředků s názvem *myResourceGroup* v oblasti *Západní USA:*

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```

  Rozšíření virtuálních počítačů lze také zahrnout do šablon Azure Resource Manager pro snadné nasazení s následujícím prvkem JSON:

  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  Následující příkaz nainstaluje nejnovější verzi 1.0 InfiniBandDriverWindows rozšíření na všechny virtuální počítače podporující RDMA v existující škálovací sadě virtuálních strojů s názvem *myVMSS* nasazené ve skupině prostředků s názvem *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Další informace naleznete v [tématu Rozšíření a funkce virtuálních strojů](./extensions/overview.md). Můžete také pracovat s rozšířeními pro virtuální počítače nasazené v [klasickém modelu nasazení](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

- **Síťový adresní prostor RDMA** – síť RDMA v Azure rezervuje adresní prostor 172.16.0.0/16. Chcete-li spouštět aplikace MPI v instancích nasazených ve virtuální síti Azure, ujistěte se, že adresní prostor virtuální sítě nepřekrývá síť RDMA.

## <a name="cluster-configuration-options"></a>Možnosti konfigurace clusteru

Azure nabízí několik možností pro vytváření clusterů virtuálních počítačů HPC s Windows, které mohou komunikovat pomocí sítě RDMA, včetně: 

- **Virtuální počítače** – nasaďte virtuální počítače HPC podporující RDMA ve stejné škálovací sadě nebo dostupnosti (při použití modelu nasazení Azure Resource Manageru). Pokud používáte klasický model nasazení, nasaďte virtuální počítače ve stejné cloudové službě.

- **Škálovací sady virtuálních strojů** – ve škálovací sadě virtuálních strojů (VMSS) zajistíte omezení nasazení na jednu skupinu umístění. Například v šabloně Správce prostředků `singlePlacementGroup` nastavte `true`vlastnost na . Všimněte si, že maximální velikost VMSS, který lze spředit s `singlePlacementGroup` vlastností `true` je omezena na 100 virtuálních počítače ve výchozím nastavení. Pokud jsou vaše potřeby škálování úloh HPC vyšší než 100 virtuálních počítačů v jednom tenantovi VMSS, můžete požádat o zvýšení a otevřít [online žádost o zákaznickou podporu](../azure-supportability/how-to-create-azure-support-request.md) zdarma.

- **MPI mezi virtuálními počítači** – pokud rdma (např. pomocí mpi komunikace) je vyžadováno mezi virtuálními počítači (VM), ujistěte se, že virtuální počítače jsou ve stejné škálovací sady virtuálních počítačů nebo dostupnosti.

- **Azure CycleCloud** – vytvořte cluster HPC v [Azure CycleCloud](/azure/cyclecloud/) pro spuštění úloh MPI.

- **Azure Batch** – vytvořte fond [Azure Batch](/azure/batch/) pro spuštění úloh MPI. Pokud chcete při spouštění aplikací MPI s Azure Batch používat instance náročné na výpočetní výkon, přečtěte si část [Použití úloh s více instancemi ke spuštění aplikací MPI (Message Passing Interface) v Azure Batch](../batch/batch-mpi.md).

- **Sada Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) obsahuje runtime prostředí pro MS-MPI, které používá síť Azure RDMA při nasazení na virtuálnípočítače s podporou RDMA pro Linux. Například nasazení, najdete [v tématu Nastavení linuxového clusteru RDMA s HPC Pack pro spouštění aplikací MPI](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Aspekty nasazování

- **Předplatné Azure** – Chcete-li nasadit více než několik instancí náročných na výpočetní výkon, zvažte předplatné s průběžným platbou nebo jiné možnosti nákupu. Pokud používáte [bezplatný účet Azure](https://azure.microsoft.com/free/), můžete použít pouze omezený počet výpočetních jader Azure.

- **Ceny a dostupnost** – tyto velikosti virtuálních počítače jsou nabízeny jenom v cenové úrovni Standard. Dostupnost v oblastech Azure [najdete v produktech dostupných podle oblastí.](https://azure.microsoft.com/global-infrastructure/services/)

- **Kvóta jader** – možná budete muset zvýšit kvótu jader ve vašem předplatném Azure z výchozí hodnoty. Vaše předplatné může také omezit počet jader, které můžete nasadit v určitých rodinách velikostí virtuálních zařízení, včetně řady H.Your subscription might also limit the number of cores you can deploy in certain VM size families, including the H-series. Chcete-li požádat o zvýšení kvóty, [otevřete online žádost o zákaznickou podporu](../azure-supportability/how-to-create-azure-support-request.md) zdarma. (Výchozí limity se mohou lišit v závislosti na kategorii předplatného.)

  > [!NOTE]
  > Pokud máte rozsáhlé kapacitní potřeby, obraťte se na podporu Azure. Kvóty Azure jsou úvěrové limity, nikoli záruky kapacity. Bez ohledu na kvótu se vám účtují pouze jádra, která používáte.
  
- **Virtuální síť** – [virtuální síť](https://azure.microsoft.com/documentation/services/virtual-network/) Azure není nutné používat instance náročné na výpočetní výkon. Pro mnoho nasazení však potřebujete alespoň cloudovou virtuální síť Azure nebo připojení mezi lokalitami, pokud potřebujete přístup k místním prostředkům. V případě potřeby vytvořte novou virtuální síť pro nasazení instancí. Přidání virtuálních počítačů náročných na výpočetní výkon do virtuální sítě ve skupině spřažení není podporováno.

- **Změna velikosti** – Vzhledem k jejich specializovanéhardware, můžete změnit velikost pouze instance náročné na výpočetní výkon v rámci stejné velikosti rodiny (H-series nebo compute-intensive A-series). Například můžete změnit pouze velikost virtuálního počítače řady H z jedné velikosti řady H na jinou. Kromě toho není podporována změna velikosti z velikosti nenáročné na výpočetní prostředky na velikost náročnou na výpočetní prostředky.  


## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

- Další informace o optimalizaci aplikace HPC pro Azure a některé příklady najdete na [HPC Workloads] (https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/overview) 

- Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám můžou pomoct porovnat výpočetní výkon napříč virtuálními jednotkami Azure.
