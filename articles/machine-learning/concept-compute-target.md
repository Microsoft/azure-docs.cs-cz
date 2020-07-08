---
title: Co jsou výpočetní cíle
titleSuffix: Azure Machine Learning
description: Určete, kam chcete model vyškolit nebo nasadit pomocí Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 06/26/2020
ms.openlocfilehash: 8b0fa1402452d8e1f348cd353b00d0ef050d866c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483274"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Jaké jsou výpočetní cíle v Azure Machine Learning? 

**Cíl služby COMPUTE** je určený výpočetní prostředek nebo prostředí, ve kterém spouštíte školicí skript nebo hostuje nasazení služby. Toto umístění může být váš místní počítač nebo cloudový výpočetní prostředek. Použití výpočetních cílů usnadňuje pozdější změnu prostředí COMPUTE, aniž byste museli měnit kód.  

Typický životní cyklus vývoje modelu vám může:
1. Začněte vývojem a experimentováním s malým množstvím dat. V této fázi doporučujeme jako cíl výpočtů použít místní prostředí (místní počítač nebo cloudový virtuální počítač). 
2. Můžete škálovat až na větší objem dat nebo distribuované školení pomocí jednoho z těchto [školicích cílů](#train).  
3. Jakmile je model připravený, nasaďte ho do prostředí pro hostování webů nebo zařízení IoT s jedním z těchto [výpočetních cílů nasazení](#deploy).

Výpočetní prostředky, které používáte pro cíle výpočtů, jsou připojené k [pracovnímu prostoru](concept-workspace.md). Výpočetní prostředky jiné než místní počítač sdílí uživatelé pracovního prostoru.

## <a name="training-compute-targets"></a><a name="train"></a>Školení výpočetních cílů

Azure Machine Learning má různou podporu v různých výpočetních prostředcích.  Můžete také připojit vlastní výpočetní prostředek, i když se podpora různých scénářů může lišit.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Přečtěte si další informace o [nastavení a používání výpočetního cíle pro školení modelů](how-to-set-up-training-targets.md).

## <a name="deployment-targets"></a><a name="deploy"></a>Cíle nasazení

K hostování nasazení modelu lze použít následující výpočetní prostředky.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Naučte se [, jak a jak model nasadit do cílového výpočetního prostředí](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning COMPUTE (spravované)

Spravovaný výpočetní prostředek je vytvořený a spravovaný pomocí Azure Machine Learning. Tato výpočetní prostředí jsou optimalizovaná pro úlohy strojového učení. Jediným spravovaným výpočetním prostředím jsou Azure Machine Learning výpočetní clustery a [výpočetní instance](concept-compute-instance.md) . V budoucnu může být přidáno více spravovaných výpočetních prostředků.

Můžete vytvořit Azure Machine Learning výpočetních instancí (Preview) nebo výpočetních clusterů z:
* Azure Machine Learning Studio
* portál Azure
* Třídy Python SDK [ComputeInstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance(class)?view=azure-ml-py) a [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py)
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (Preview)
* Šablona Resource Manageru
* Rozšíření Machine Learning [pro rozhraní příkazového řádku Azure](reference-azure-machine-learning-cli.md#resource-management)  

Když se tyto výpočetní prostředky vytvoří automaticky, na rozdíl od jiných druhů výpočetních cílů.


|Schopnost  |Výpočtový cluster  |Instance služby Compute  |
|---------|---------|---------|
|Cluster s jedním nebo několika uzly     |    **&check;**       |         |
|Automatické škálování pokaždé, když odešlete běh     |     **&check;**      |         |
|Automatická správa clusteru a plánování úloh     |   **&check;**        |     **&check;**      |
|Podpora pro prostředky CPU a GPU     |  **&check;**         |    **&check;**       |


> [!NOTE]
> Pokud je výpočetní cluster nečinný, přiřadí se automatické škálování na 0 uzlů, takže nebudete platit, když se nepoužívá.  *Instance*COMPUTE je ale vždycky zapnutá a neprovádí automatické škálování.  [Výpočetní instanci](tutorial-1st-experiment-sdk-train.md#stop-the-compute-instance) byste měli zastavit, pokud ji nepoužíváte, abyste se vyhnuli dodatečným nákladům.

### <a name="supported-vm-series-and-sizes"></a>Podporované řady a velikosti virtuálních počítačů

Když v Azure Machine Learning vyberete velikost uzlu spravovaného výpočetního prostředku, můžete si vybrat z výběru velikostí virtuálních počítačů dostupných v Azure. Azure nabízí řadu velikostí pro Linux a Windows pro různé úlohy. Další informace o různých [typech a velikostech virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)najdete tady.

Pro výběr velikosti virtuálního počítače je k dispozici několik výjimek a omezení:
* Některé řady virtuálních počítačů nejsou v Azure Machine Learning podporovány.
* Některé řady virtuálních počítačů jsou omezené. Pokud chcete použít řadu s omezeným přístupem, obraťte se na podporu a požádejte o zvýšení kvóty pro řadu. Informace o tom, jak kontaktovat podporu, najdete v tématu [Možnosti podpory Azure](https://azure.microsoft.com/support/options/) .

Další informace o podporovaných řadách a omezeních najdete v následující tabulce. 

| **Podporovaná řada virtuálních počítačů**  | **Omezení** |
|------------|------------|
| D | Žádná |
| Dv2 | Žádná |  
| DSv2 | Žádná |  
| FSv2 | Žádná |  
| M | Vyžaduje schválení |
| NC | Žádná |    
| NCsv2 | Vyžaduje schválení |
| NCsv3 | Vyžaduje schválení |  
| NDs | Vyžaduje schválení |
| NDv2 | Vyžaduje schválení |
| NV | Žádná |
| NVv3 | Vyžaduje schválení | 


I když Azure Machine Learning podporuje tyto řady virtuálních počítačů, nemusí být k dispozici ve všech oblastech Azure. Řadu virtuálních počítačů, které jsou k dispozici, najdete tady: [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

## <a name="unmanaged-compute"></a>Nespravované výpočetní prostředky

Nespravovaný cíl výpočetní služby není *spravován nástrojem* Azure Machine Learning. Tento typ cíle výpočetní služby vytvoříte mimo Azure Machine Learning a pak ho připojíte k pracovnímu prostoru. Nespravované výpočetní prostředky můžou vyžadovat další kroky, abyste mohli udržovat nebo zvýšit výkon úloh strojového učení.

## <a name="next-steps"></a>Další kroky

Naučte se:
* [Nastavení cílového výpočetního prostředí pro výuku modelu](how-to-set-up-training-targets.md)
* [Nasazení modelu do cíle služby COMPUTE](how-to-deploy-and-where.md)
