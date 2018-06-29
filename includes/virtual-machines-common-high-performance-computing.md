---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-linux, virtual-machines-windows
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 05/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 32a438d393077cfe4cb7f6ee62f3a01edfce0571
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "34152219"
---
Organizace mají rozsáhlé výpočetní potřebám. Tyto úlohy Big Compute zahrnují technického návrhu a analýzy, výpočtů finančních rizik, vykreslování obrázků, komplexního modelování, simulací typu Monte Carlo a další. 

Použijte Azure cloud pro efektivní spouštění výpočetně náročných úloh Linux a Windows, z paralelní dávkové úlohy pro tradiční HPC simulace. Spusťte vaše prostředí HPC a úloh služby batch na infrastrukturu Azure, s možností výpočetní služby, správci mřížky, Marketplace řešení a aplikace hostované dodavatele (SaaS). Azure poskytuje flexibilní řešení k rozložení práce a škálování na tisíce virtuálních počítačů nebo jader a pak vertikálně snížit kapacitu, až budete potřebovat méně prostředků. 



## <a name="solution-options"></a>Možnosti řešení



* **Samoobslužné řešení**
    * Nastavit vlastní prostředí clusteru na virtuálních počítačích Azure nebo [sady škálování virtuálního počítače](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). 
    * Navýšení a posunutí místní cluster nebo nasazení do nového clusteru v Azure pro dodatečnou kapacitu. 
    * Použití šablon Azure Resource Manager k nasazení úvodní [zatížení správci](#workload-managers), infrastruktury, a [aplikace](#hpc-applications). 
    * Zvolte [velikosti HPC a virtuálních počítačů GPU](#hpc-and-gpu-sizes) , zahrnout speciální hardware a připojení k síti pro úlohy MPI nebo GPU. 
    * Přidat [vysoký výkon úložiště](#hpc-storage) pro I náročnými úlohy.
* **Hybridní řešení**
    * Rozšířit vaše místní řešení pro přesměrování zpracování úloh ve špičce ("shluků") na infrastrukturu Azure
    * Cloudové výpočetní na vyžádání pomocí stávající [Správce úloh](#workload-manager).
    * Využít výhod [velikosti HPC a virtuálních počítačů GPU](#hpc-and-gpu-sizes) pro úlohy MPI nebo GPU.
* **Velkých výpočetních řešení jako služby**
    * Vývoj vlastních řešení Big Compute a pracovních postupů pomocí [Azure Batch](#azure-batch) a související [služby Azure](#related-azure-services).
    * Spustit Azure inženýrství a simulace řešeních s infrastrukturou od dodavatelů, včetně [Altair](http://www.altair.com/), [nastavit velikost](https://www.rescale.com/azure/), a [Cycle Computing](https://cyclecomputing.com/) (teď [propojit s Microsoft](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/)).
    * Použití [Cray superpočítače](https://www.cray.com/solutions/supercomputing-as-a-service/cray-in-azure) jako služby hostované v Azure.
* **Řešení Marketplace.**
    * Použití rozsahu [aplikace prostředí HPC](#hpc-applications) a [řešení](#marketplace-solutions) nenabízí [Azure Marketplace](https://azuremarketplace.microsoft.com/). 
    


Další informace o podpůrné technologie a odkazy na pokyny naleznete v následujících částech.



## <a name="marketplace-solutions"></a>Řešení na Marketplace

Přejděte [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) pro systémy Linux a virtuální počítač s Windows Image a řešení pro prostředí HPC. Příklady obsahují:

* [RogueWave na základě CentOS HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased73HPC?tab=Overview)
* [SUSE Linux Enterprise Server pro HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
*  [Stroj Server TIBCO mřížky](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
* [Vědecké zpracování dat Azure virtuálního počítače pro systém Windows a Linux](../articles/machine-learning/machine-learning-data-science-virtual-machine-overview.md)
* [D3View](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/xfinityinc.d3view-v5?tab=Overview)
* [UberCloud](https://azure.microsoft.com/search/marketplace/?q=ubercloud)
* [Intel cloudu Edition pro počítače s Lustre](https://azuremarketplace.microsoft.com/marketplace/apps/intel.lustre-cloud-edition-eval?tab=Overview)


 
## <a name="hpc-applications"></a>Aplikace prostředí HPC

Spusťte vlastní nebo obchodní aplikace prostředí HPC v Azure. Několik příkladů v této části jsou benchmarked efektivní škálování s další virtuální počítače nebo výpočetní jader. Přejděte [Azure Marketplace](https://marketplace.azure.com) připravená k nasazení řešení.

> [!NOTE]
> Zkontrolujte s dodavateli komerčních aplikací se vždy pro licencování a dalších omezení při spouštění v cloudu. Ne všichni dodavatelé nabízejí licencování formou průběžných plateb. Může potřebovat licenční server v cloudu pro vaše řešení nebo připojit k místní licence serveru.

### <a name="engineering-applications"></a>Technici aplikace


* [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
* [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
* [MATLAB distribuovat výpočetní Server](../articles/virtual-machines/windows/matlab-mdcs-cluster.md)
* [StarCCM+](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
* [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



### <a name="graphics-and-rendering"></a>Grafika a vykreslování

* [Autodesk Maya, 3ds Max a Arnold](../articles/batch/batch-rendering-service.md) na Azure Batch 

### <a name="ai-and-deep-learning"></a>AI a hloubkové učení

* [Batch AI](../articles/batch-ai/overview.md) školení pro modely hloubkové učení
* [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/cntk-on-azure)
* [Přímý učení virtuálních počítačů](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* [Batch loděnice recepty pro přímý learning](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)






## <a name="hpc-and-gpu-vm-sizes"></a>Velikosti HPC a virtuálních počítačů GPU
Azure nabízí celou řadu velikosti [Linux](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a [Windows](../articles/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) virtuálních počítačů, včetně velikosti určené pro výpočetně náročných úloh. Například H16r a H16mr virtuální počítače můžete připojit k síti RDMA back-end vysoké propustnosti. Tato cloudové sítě můžete zlepšit výkon úzce párované paralelní aplikace spuštěna pod [Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) nebo Intel MPI. 

N-series virtuální počítače funkce grafickými procesory NVIDIA určené pro aplikace náročné na výkon nebo velmi náročná na výkon grafiky včetně learning umělé intelligence (AI) a vizualizace. 

Další informace:

* Vysoký výkon výpočetní velikosti [Linux](../articles/virtual-machines/linux/sizes-hpc.md) a [Windows](../articles/virtual-machines/windows/sizes-hpc.md) virtuální počítače 
* Grafický procesor s podporou velikosti [Linux](../articles/virtual-machines/linux/sizes-gpu.md) a [Windows](../articles/virtual-machines/windows/sizes-gpu.md) virtuální počítače 

Naučte se:

* [Nastavení clusteru s podporou Linux RDMA ke spuštění aplikací MPI](../articles/virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Nastavení clusteru s podporou Windows RDMA pomocí sady Microsoft HPC Pack ke spouštění aplikací MPI](../articles/virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Použít ve fondech Batch náročné na výkon virtuálních počítačů](../articles/batch/batch-pool-compute-intensive-sizes.md)



## <a name="azure-batch"></a>Azure Batch
[Batch](../articles/batch/batch-technical-overview.md) je platforma služby pro spuštění rozsáhlé paralelní a vysoce výkonné výpočty aplikace (HPC) efektivně v cloudu. Azure Batch plány náročné práce ke spuštění v rámci spravovaného fondu virtuálních počítačů, a dokáže automaticky škálovat výpočetní prostředky ke splnění potřeb vašich úloh. 

Poskytovatelů SaaS a vývojářům umožňuje HPC aplikacím nebo úlohám kontejneru integrovat Azure, fáze dat do Azure, SDK služby Batch a nástroje a sestavení kanály provádění úlohy. 

Naučte se:

* [Začít s vývojem pomocí služby Batch](../articles/batch/quick-run-dotnet.md)
* [Použít ukázky kódu Azure Batch](https://github.com/Azure/azure-batch-samples)
* [Pomocí služby Batch použijte virtuální počítače s nízkou prioritou](../articles/batch/batch-low-pri-vms.md)
* [Spusťte kontejnerizované úlohy HPC s loděnice Batch](https://github.com/Azure/batch-shipyard)
* [Paralelní úlohy R spustili Batch](https://github.com/Azure/doAzureParallel)
* [Spouštění úloh na vyžádání Spark na dávky](https://github.com/Azure/aztk)

## <a name="workload-managers"></a>Správce úloh

Následují příklady manažerů clusteru a úlohy, které můžou běžet v infrastruktury Azure. Vytvoření samostatné clusterů ve virtuálních počítačích Azure nebo shluků k virtuálním počítačům Azure z místního clusteru. 
* [Výpočetní Alces letu](https://azuremarketplace.microsoft.com/marketplace/apps/alces-flight-limited.alces-flight-compute-solo?tab=Overview)
* [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/) 
* [Jasně Správce clusteru](http://www.brightcomputing.com/technology-partners/microsoft)
* [IBM spektrum Symphony a Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
* [PBS Pro](http://pbspro.org)
* [Microsoft HPC Pack](https://technet.microsoft.com/library/mt744885.aspx) -najdete v části Možnosti spuštění v [Windows](../articles/virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a [Linux](../articles/virtual-machines/linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) virtuální počítače 



## <a name="hpc-storage"></a>HPC úložiště

Ve velkém měřítku Batch a prostředí HPC zatížení mají požadavky pro úložiště dat a přístupu, které překračují možnosti tradiční cloudové systémy souborů. Implementovat řešení pro systém paralelní souboru v Azure, jako [Lustre](http://lustre.org/) a [BeeGFS](http://www.beegfs.com/content/).

Další informace:

* [Paralelní systémy virtuální souborů v Azure](https://azure.microsoft.com/resources/parallel-virtual-file-systems-on-microsoft-azure/)
* Vysoce výkonná řešení cloudové úložiště z [Avere](http://www.averesystems.com/about-us/about-avere) (teď [propojit s Microsoft](https://blogs.microsoft.com/blog/2018/01/03/microsoft-to-acquire-avere-systems-accelerating-high-performance-computing-innovation-for-media-and-entertainment-industry-and-beyond/))


## <a name="related-azure-services"></a>Související služby Azure

Virtuální počítače Azure, sady škálování virtuálního počítače, Batch a související výpočetní služby jsou základ pro většinu řešení Azure HPC. Řešení však můžete využít výhod mnoha související služby Azure. Následuje částečný seznam:

### <a name="storage"></a>Úložiště

* [Objekt BLOB, table a queue storage](../articles/storage/storage-introduction.md)
* [Úložiště souborů](../articles/storage/storage-files-introduction.md)

### <a name="data-and-analytics"></a>Data a analýza
* [HDInsight](../articles/hdinsight/hadoop/apache-hadoop-introduction.md)
* [Data Factory](../articles/data-factory/introduction.md)
* [Data Lake Store](../articles/data-lake-store/data-lake-store-overview.md)
* [Databricks](../articles/azure-databricks/what-is-azure-databricks.md)
* [SQL Database](../articles/sql-database/sql-database-technical-overview.md)

### <a name="ai-and-machine-learning"></a>AI a machine learningu
* [Služby Machine Learning](../articles/machine-learning/service/overview-what-is-azure-ml.md)
* [Batch AI](../articles/batch-ai/overview.md)
* [Genomika](../articles/genomics/overview-what-is-genomics.md)

### <a name="networking"></a>Sítě
* [Virtual Network](../articles/virtual-network/virtual-networks-overview.md)
* [ExpressRoute](../articles/expressroute/expressroute-introduction.md)

### <a name="containers"></a>Containers
* [Container Service](../articles/container-service/dcos-swarm/container-service-intro.md)
* [Služba Azure Kubernetes (AKS)](../articles/aks/intro-kubernetes.md)
* [Container Registry](../articles/container-registry/container-registry-intro.md)



## <a name="customer-stories"></a>Příběhy zákazníků

Příklady zákazníky, kteří mají vyřešit obchodní problémy s řešeními Azure HPC:

* [ANEO](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu) 
* [AXA globální P & C](https://customers.microsoft.com/story/axa-global-p-and-c)
* [Axioma](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
* [d3View](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
* [SYSTÉM SOUBORŮ EFS](https://customers.microsoft.com/story/efs-professionalservices-azure)
* [Hymans Robertson](https://customers.microsoft.com/story/hymans-robertson)
* [MetLife](https://enterprise.microsoft.com/en-us/customer-story/industries/insurance/metlife/)
* [Microsoft Research](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
* [Milliman](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
* [Mezinárodní cenné Mitsubishi UFJ](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)


## <a name="next-steps"></a>Další postup
* Další informace o řešeních pro Big Compute pro [technici simulace](https://simulation.azure.com/), [vykreslování](https://azure.microsoft.com/solutions/big-compute/rendering/), [bankovnictví a kapitálové trhů](https://finance.azure.com/), a [genomika](https://enterprise.microsoft.com/en-us/industries/health/genomics/) .
* Podívejte se na aktuální novinky na [blogu týmu pro Microsoft HPC a Batch](http://blogs.technet.com/b/windowshpc/) a [blogu Azure](https://azure.microsoft.com/blog/tag/hpc/).

* Používat spravované a škálovatelné Azure [Batch](https://azure.microsoft.com/services/batch/) služby spouštění výpočetně náročných úloh bez Správa základní infrastruktury [Další informace](https://azure.microsoft.com/solutions/architecture/hpc-big-compute-saas/)



