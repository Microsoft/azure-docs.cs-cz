---
title: Co jsou výpočetní cíle
titleSuffix: Azure Machine Learning
description: Naučte se, jak určit výpočetní prostředek nebo prostředí pro výuku nebo nasazení modelu pomocí Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/29/2020
ms.openlocfilehash: bd83ccc4f834d6b52e1c2db15000eb695be85f30
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177194"
---
# <a name="what-are-compute-targets-in-azure-machine-learning"></a>Co jsou cílové výpočetní objekty ve službě Azure Machine Learning?

*Cíl výpočetní* služby je určený výpočetní prostředek nebo prostředí, kde spustíte školicí skript nebo nakonfigurujete nasazení služby. Toto umístění může být váš místní počítač nebo cloudový výpočetní prostředek. Použití výpočetních cílů vám usnadňuje později změnit výpočetní prostředí, aniž byste museli měnit kód.

Typický životní cyklus vývoje modelu vám může:

1. Začněte vývojem a experimentováním s malým množstvím dat. V této fázi můžete jako cíl služby COMPUTE použít místní prostředí, jako je například místní počítač nebo cloudový virtuální počítač (VM).
1. Můžete škálovat až na větší objem dat nebo distribuované školení pomocí jednoho z těchto [výpočetních cílů](#train).
1. Až bude model připravený, nasaďte ho do prostředí pro hostování webů nebo zařízení IoT s jedním z těchto [výpočetních cílů nasazení](#deploy).

Výpočetní prostředky, které používáte pro cíle výpočtů, jsou připojené k [pracovnímu prostoru](concept-workspace.md). Výpočetní prostředky jiné než místní počítač sdílí uživatelé pracovního prostoru.

## <a name="training-compute-targets"></a><a name="train"></a> Školení výpočetních cílů

Azure Machine Learning má různou podporu napříč různými výpočetními cíli. Typický životní cyklus vývoje modelu začíná vývojem nebo experimentováním s malým množstvím dat. V této fázi použijte místní prostředí, jako je váš místní počítač nebo cloudový virtuální počítač. Při horizontálním navýšení kapacity školení na větší datové sady nebo provádění distribuovaného školení použijte Azure Machine Learning výpočetní prostředí k vytvoření clusteru s jedním nebo několika uzly, který při každém odeslání běhu provádí automatické škálování. Můžete také připojit vlastní výpočetní prostředek, i když se podpora různých scénářů může lišit.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Přečtěte si další informace o tom, jak [Odeslat školicí běh do cílového výpočetního](how-to-set-up-training-targets.md)prostředí.

## <a name="compute-targets-for-inference"></a><a name="deploy"></a> Výpočetní cíle pro odvození

K hostování nasazení modelu lze použít následující výpočetní prostředky.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Při vykonávání odvození Azure Machine Learning vytvoří kontejner Docker, který je hostitelem modelu a přidružených prostředků potřebných k jeho použití. Tento kontejner se pak použije v jednom z následujících scénářů nasazení:

* Jako *webovou službu* , která se používá pro odvození v reálném čase. Nasazení webové služby používají jeden z následujících výpočetních cílů:

    * [Místní počítač](how-to-attach-compute-targets.md#local)
    * [Výpočetní instance Azure Machine Learningu](how-to-create-manage-compute-instance.md)
    * [Azure Container Instances](how-to-attach-compute-targets.md#aci)
    * [Azure Kubernetes Service](how-to-create-attach-kubernetes.md)
    * Azure Functions (Preview). Nasazení do funkcí spoléhá jenom na Azure Machine Learning k sestavení kontejneru Docker. Odtud je nasazen pomocí funkcí. Další informace najdete v tématu [nasazení modelu Machine Learning do Azure Functions (Preview)](how-to-deploy-functions.md).

* Jako koncový bod _odvození dávky_ , který slouží k pravidelnému zpracování dávek dat. Odvození dávky využívají [Azure Machine Learning výpočetních clusterů](how-to-create-attach-compute-cluster.md).

* Do _zařízení IoT_ (Preview). Nasazení do zařízení IoT spoléhá jenom na Azure Machine Learning k sestavení kontejneru Docker. Odtud je nasazena pomocí Azure IoT Edge. Další informace najdete v tématu [nasazení jako modulu IoT Edge (Preview)](../iot-edge/tutorial-deploy-machine-learning.md).

Naučte se [, jak a jak model nasadit do cílového výpočetního prostředí](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning COMPUTE (spravované)

Spravovaný výpočetní prostředek je vytvořený a spravovaný pomocí Azure Machine Learning. Tato výpočetní prostředí jsou optimalizovaná pro úlohy strojového učení. Jediným spravovaným výpočetním prostředím jsou Azure Machine Learning výpočetní clustery a [výpočetní instance](concept-compute-instance.md) .

Můžete vytvořit Azure Machine Learning výpočetní instance nebo výpočetní clustery z:

* [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md).
* Sada Python SDK a rozhraní příkazového řádku:
    * [Instance COMPUTE](how-to-create-manage-compute-instance.md)
    * [Výpočetní cluster](how-to-create-attach-compute-cluster.md).
* [Sada R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (Preview).
* Šablona Azure Resource Manager. Příklad šablony naleznete v tématu [Create a Azure Machine Learning Compute Cluster](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute).
* Rozšíření Machine Learning [pro rozhraní příkazového řádku Azure CLI](reference-azure-machine-learning-cli.md#resource-management).

Po vytvoření budou tyto výpočetní prostředky automaticky součástí pracovního prostoru, na rozdíl od jiných druhů výpočetních cílů.


|Schopnost  |Výpočtový cluster  |Instance služby Compute  |
|---------|---------|---------|
|Cluster s jedním nebo několika uzly     |    **&check;**       |         |
|Automatické škálování pokaždé, když odešlete běh     |     **&check;**      |         |
|Automatická správa clusteru a plánování úloh     |   **&check;**        |     **&check;**      |
|Podpora pro prostředky CPU a GPU     |  **&check;**         |    **&check;**       |


> [!NOTE]
> Pokud je výpočetní *cluster* nečinný, přiřadí se automatické škálování na 0 uzlů, takže nebudete platit, když se nepoužívá. *Instance* COMPUTE je vždycky zapnutá a neprovádí automatické škálování. [Výpočetní instanci](how-to-create-manage-compute-instance.md#manage) byste měli zastavit, pokud ji nepoužíváte, abyste se vyhnuli dodatečným nákladům.

### <a name="supported-vm-series-and-sizes"></a>Podporované řady a velikosti virtuálních počítačů

Když v Azure Machine Learning vyberete velikost uzlu spravovaného výpočetního prostředku, můžete si vybrat z výběru velikostí virtuálních počítačů dostupných v Azure. Azure nabízí řadu velikostí pro Linux a Windows pro různé úlohy. Další informace najdete v tématu [typy a velikosti virtuálních počítačů](../virtual-machines/sizes.md).

Pro výběr velikosti virtuálního počítače je k dispozici několik výjimek a omezení:

* Některé série virtuálních počítačů nejsou podporované v Azure Machine Learning.
* Některé řady virtuálních počítačů jsou omezené. Pokud chcete použít řadu s omezeným přístupem, obraťte se na podporu a požádejte o zvýšení kvóty pro řadu. Informace o tom, jak kontaktovat podporu, najdete v tématu [Možnosti podpory Azure](https://azure.microsoft.com/support/options/).

Další informace o podporovaných řadách a omezeních najdete v následující tabulce.

| **Podporovaná řada virtuálních počítačů**  | **Omezení** |
|------------|------------|
| D | Žádné |
| DDSv4 | Žádné |
| Dv2 | Žádné |  
| Dv3 | Žádné|
| DSv2 | Žádné | 
| DSv3 | Žádné|
| EAv4 | Žádné |
| Ev3 | Žádné |
| FSv2 | Žádné | 
| H | Žádné |
| HB | Vyžaduje schválení. |
| HBv2 | Vyžaduje schválení. |  
| KLIENTOVI HCS | Vyžaduje schválení. |  
| M | Vyžaduje schválení. |
| NC | Žádné |  
| Propagační akce síťového adaptéru | Žádné |
| NCsv2 | Vyžaduje schválení. |
| NCsv3 | Vyžaduje schválení. |  
| NDs | Vyžaduje schválení. |
| NDv2 | Vyžaduje schválení. |
| NV | Žádné |
| NVv3 | Vyžaduje schválení. | 


I když Azure Machine Learning podporuje tyto řady virtuálních počítačů, nemusí být k dispozici ve všech oblastech Azure. Pokud chcete zjistit, jestli jsou dostupné řady virtuálních počítačů, přečtěte si téma [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

> [!NOTE]
> Azure Machine Learning nepodporuje všechny velikosti virtuálních počítačů, které Azure COMPUTE podporuje. Chcete-li zobrazit seznam dostupných velikostí virtuálních počítačů, použijte jednu z následujících metod:
> * [REST API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable/2020-08-01/examples/ListVMSizesResult.json)
> * [Python SDK](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py.md#supported-vmsizes-workspace--location-none-)
>

### <a name="compute-isolation"></a>Izolace výpočtů

Azure Machine Learning COMPUTE nabízí velikosti virtuálních počítačů, které jsou izolované na konkrétní typ hardwaru a vyhrazené pro jediného zákazníka. Izolované velikosti virtuálních počítačů jsou nejvhodnější pro úlohy, které vyžadují vysokou úroveň izolace od úloh jiných zákazníků z důvodů, které zahrnují dodržování předpisů a zákonné požadavky na schůzku. Využitím izolované velikosti zaručujete, že váš virtuální počítač bude jediným operačním systémem, který běží na konkrétní instanci serveru.

K aktuálním nabídkám izolovaného virtuálního počítače patří:

* Standard_M128ms
* Standard_F72s_v2
* Standard_NC24s_v3
* Standard_NC24rs_v3 *

*Podpora RDMA

Další informace o izolaci najdete v tématu věnovaném [izolaci ve veřejném cloudu Azure](../security/fundamentals/isolation-choices.md).

## <a name="unmanaged-compute"></a>Nespravované výpočetní prostředky

Nespravovaný cíl výpočetní služby není *spravován nástrojem* Azure Machine Learning. Tento typ cíle služby COMPUTE vytvoříte mimo Azure Machine Learning a pak ho připojíte k pracovnímu prostoru. Nespravované výpočetní prostředky můžou vyžadovat další kroky, abyste mohli udržovat nebo zvýšit výkon úloh strojového učení.

## <a name="next-steps"></a>Další kroky

Naučte se:
* [Využijte výpočetní cíl ke školení modelu](how-to-set-up-training-targets.md)
* [Nasazení modelu do cíle služby COMPUTE](how-to-deploy-and-where.md)
