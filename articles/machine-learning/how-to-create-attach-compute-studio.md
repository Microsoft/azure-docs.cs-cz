---
title: Vytvoření školicích & nasazení výpočetních prostředků (Studio)
titleSuffix: Azure Machine Learning
description: Použití studia k vytvoření výpočetních prostředků pro školení a nasazení (cíle výpočtů) pro Machine Learning
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.openlocfilehash: 37253ccfb249eb8b510af5e314c3167ddd979a87
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661965"
---
# <a name="create-compute-targets-for-model-training-and-deployment-in-azure-machine-learning-studio"></a>Vytváření výpočetních cílů pro školení a nasazení modelů v Azure Machine Learning Studiu
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak vytvářet a spravovat výpočetní cíle v Azure Machine studia.  Můžete také vytvořit a spravovat výpočetní cíle pomocí:

* [Sada Azure Machine Learning Learning SDK](how-to-create-attach-compute-sdk.md) 
* [Rozšíření CLI](reference-azure-machine-learning-cli.md#resource-management) pro Azure Machine Learning
* [Vs Code rozšíření](how-to-manage-resources-vscode.md#compute-clusters) pro Azure Machine Learning.


## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes
* [Pracovní prostor Azure Machine Learning](how-to-manage-workspace.md)

## <a name="whats-a-compute-target"></a>Co je cílový výpočetní výkon? 

Pomocí Azure Machine Learning můžete model vyškolit na nejrůznějších materiálech nebo prostředích, které se souhrnně označují jako [__výpočetní cíle__](concept-azure-machine-learning-architecture.md#compute-targets). Cílem výpočetní služby může být místní počítač nebo cloudový prostředek, jako je Azure Machine Learning COMPUTE, Azure HDInsight nebo vzdálený virtuální počítač.  Můžete také vytvořit výpočetní cíle pro nasazení modelu, jak je popsáno v [části "kde a jak nasadit vaše modely"](how-to-deploy-and-where.md).

## <a name="view-compute-targets"></a><a id="portal-view"></a>Zobrazit cíle výpočtů

Pokud chcete zobrazit všechny výpočetní cíle pro váš pracovní prostor, použijte následující postup:

1. Přejděte do [Azure Machine Learning studia](https://ml.azure.com).
 
1. V části __Spravovat__vyberte __COMPUTE__.

1. Výběrem karet v horní části zobrazíte každý typ cíle výpočtů.

    :::image type="content" source="media/how-to-create-attach-studio/view-compute-targets.png" alt-text="Zobrazit seznam cílů výpočetních prostředků":::

## <a name="create-compute-target"></a><a id="portal-create"></a>Vytvořit cíl výpočtů

Podle předchozích kroků zobrazte seznam cílů výpočtů. Pak pomocí těchto kroků vytvořte cíl výpočtů:

1. Vyberte kartu v horní části odpovídající typu výpočtů, které vytvoříte.

1. Pokud nemáte žádné výpočetní cíle, vyberte  **vytvořit** uprostřed stránky.
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="Vytvořit cíl výpočtů":::

1. Pokud se zobrazí seznam výpočetních prostředků, vyberte **+ Nový** nad seznamem.

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="Vybrat nový":::


1. Vyplňte formulář pro výpočetní typ:

  * [Instance služby Compute](#compute-instance)
  * [Výpočetní clustery](#amlcompute)
  * [Odvození clusterů](#inference-clusters)
  * [Připojené výpočetní prostředky](#attached-compute)

1. Vyberte __Vytvořit__.

1. Stav operace vytvoření si zobrazíte tak, že v seznamu vyberete cíl služby Compute:

    :::image type="content" source="media/how-to-create-attach-studio/view-list.png" alt-text="Zobrazení stavu služby COMPUTE ze seznamu":::


### <a name="compute-instance"></a>Instance služby Compute

Pomocí [výše uvedených kroků](#portal-create) vytvořte výpočetní instanci.  Pak vyplňte formulář následujícím způsobem:

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="Vytvořit novou výpočetní instanci":::


|Pole  |Popis  |
|---------|---------|
|Název výpočetních prostředků     |  <li>Název je povinný a musí mít délku 3 až 24 znaků.</li><li>Platné znaky jsou velká písmena a malá písmena, číslice a  **-** znak.</li><li>Název musí začínat písmenem.</li><li>Název musí být jedinečný v rámci všech stávajících výpočtů v oblasti Azure. Pokud zvolený název není jedinečný, zobrazí se upozornění.</li><li>Pokud **-**  se používá znak, musí následovat aspoň jedno písmeno později v názvu.</li>     |
|Typ virtuálního počítače |  Vyberte možnost procesor nebo GPU. Tento typ nelze po vytvoření změnit.     |
|Velikost virtuálního počítače     |  Podporované velikosti virtuálních počítačů můžou být ve vaší oblasti omezené. Kontrolovat [seznam dostupnosti](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Povolit/zakázat přístup přes SSH     |   Přístup SSH je ve výchozím nastavení zakázán.  Přístup SSH nemůže být. Po vytvoření se změnila. Pokud chcete interaktivně ladit pomocí [vs Code vzdálených](how-to-set-up-vs-code-remote.md) , Nezapomeňte povolit přístup.   |
|Rozšířená nastavení     |  Nepovinný parametr. Nakonfigurujte virtuální síť. Zadejte **skupinu prostředků**, **virtuální síť**a **podsíť** pro vytvoření výpočetní instance v rámci Azure Virtual Network (VNET). Další informace najdete v tématu tyto [požadavky na síť](how-to-enable-virtual-network.md#compute-instance) pro virtuální síť.  |

### <a name="compute-clusters"></a><a name="amlcompute"></a> Výpočetní clustery

Vytvořte jeden nebo více uzlů Compute Cluster pro školení, dávkové Inferencing nebo posílení studijních úloh. Pomocí [výše uvedeného postupu](#portal-create) vytvořte výpočetní cluster.  Pak vyplňte formulář následujícím způsobem:


|Pole  |Popis  |
|---------|---------|
|Název výpočetních prostředků     |  <li>Název je povinný a musí mít délku 3 až 24 znaků.</li><li>Platné znaky jsou velká písmena a malá písmena, číslice a  **-** znak.</li><li>Název musí začínat písmenem.</li><li>Název musí být jedinečný v rámci všech stávajících výpočtů v oblasti Azure. Pokud zvolený název není jedinečný, zobrazí se upozornění.</li><li>Pokud **-**  se používá znak, musí následovat aspoň jedno písmeno později v názvu.</li>     |
|Typ virtuálního počítače |  Vyberte možnost procesor nebo GPU. Tento typ nelze po vytvoření změnit.     |
|Priorita virtuálního počítače | Vyberte možnost **vyhrazená** nebo **Nízká priorita**.  Virtuální počítače s nízkou prioritou jsou levnější, ale nezaručují výpočetní uzly. Může dojít k přerušení úlohy.
|Velikost virtuálního počítače     |  Podporované velikosti virtuálních počítačů můžou být ve vaší oblasti omezené. Kontrolovat [seznam dostupnosti](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Minimální počet uzlů | Minimální počet uzlů, které chcete zřídit. Pokud chcete vyhrazený počet uzlů, nastavte tento počet. Ušetříte peníze nastavením minimální na 0, takže nebudete platit za žádné uzly, pokud je cluster nečinný. |
|Maximální počet uzlů | Maximální počet uzlů, které chcete zřídit. Výpočet bude při odeslání úlohy automatické škálování na maximum tohoto počtu uzlů. |
|Rozšířená nastavení     |  Nepovinný parametr. Nakonfigurujte virtuální síť. Zadejte **skupinu prostředků**, **virtuální síť**a **podsíť** pro vytvoření výpočetní instance v rámci Azure Virtual Network (VNET). Další informace najdete v tématu tyto [požadavky na síť](how-to-enable-virtual-network.md#compute-instance) pro virtuální síť.   Připojte taky [spravované identity](#managed-identity) pro udělení přístupu k prostředkům.     |

#### <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Nastavení spravované identity

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

Při vytváření clusteru nebo při úpravách podrobností výpočetního clusteru klikněte v **rozšířeném nastavení**na přepínač **přiřadit spravovanou identitu** a zadejte identitu přiřazenou systémem nebo uživatelem přiřazenou identitu.

#### <a name="managed-identity-usage"></a>Využití spravované identity

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

### <a name="inference-clusters"></a>Odvození clusterů

> [!IMPORTANT]
> Použití služby Azure Kubernetes s Azure Machine Learning má několik možností konfigurace. Některé scénáře, například sítě, vyžadují další nastavení a konfiguraci. Další informace o používání AKS se službou Azure ML najdete v tématu [Vytvoření a připojení clusteru služby Azure Kubernetes](how-to-create-attach-kubernetes.md).

Vytvořte nebo připojte cluster Azure Kubernetes Service (AKS) pro velké měřítko Inferencing. Pomocí [výše uvedeného postupu](#portal-create) vytvořte cluster AKS.  Pak vyplňte formulář následujícím způsobem:


|Pole  |Popis  |
|---------|---------|
|Název výpočetních prostředků     |  <li>Název je povinný. Název musí mít 2 až 16 znaků. </li><li>Platné znaky jsou velká písmena a malá písmena, číslice a  **-** znak.</li><li>Název musí začínat písmenem.</li><li>Název musí být jedinečný v rámci všech stávajících výpočtů v oblasti Azure. Pokud zvolený název není jedinečný, zobrazí se upozornění.</li><li>Pokud **-**  se používá znak, musí následovat aspoň jedno písmeno později v názvu.</li>     |
|Kubernetes Service | Vyberte **vytvořit novou** a vyplňte zbytek formuláře.  Nebo vyberte **použít existující** a pak z předplatného vyberte existující cluster AKS.
|Region (Oblast) |  Vyberte oblast, ve které se cluster vytvoří. |
|Velikost virtuálního počítače     |  Podporované velikosti virtuálních počítačů můžou být ve vaší oblasti omezené. Kontrolovat [seznam dostupnosti](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Účel clusteru  | Vybrat **produkci** nebo **vývoj a testování** |
|Počet uzlů | Počet uzlů vynásobený počtem jader (vCPU) virtuálního počítače musí být větší nebo roven 12. |
| Konfigurace sítě | Pokud chcete vytvořit výpočetní prostředí v existující virtuální síti, vyberte **Upřesnit** . Další informace o AKS ve virtuální síti najdete v tématu věnovaném [izolaci sítě během školení a odvození s privátními koncovými body a virtuálními sítěmi](how-to-enable-virtual-network.md#aksvnet). |
| Povolit konfiguraci SSL | Pomocí tohoto nastavení můžete nakonfigurovat certifikát SSL pro výpočetní prostředky. |

### <a name="attached-compute"></a>Připojené výpočetní prostředky

Chcete-li použít výpočetní cíle vytvořené mimo Azure Machine Learning pracovní prostor, je nutné je připojit. Připojení k cílovému cíli zpřístupníte pracovnímu prostoru.  K připojení cíle výpočetní služby pro **školení**použijte **připojené výpočetní** prostředky.  K připojení clusteru AKS pro **Inferencing**použijte **odvození clusterů** .

K připojení výpočetních prostředků použijte [výše uvedené kroky](#portal-create) .  Pak vyplňte formulář následujícím způsobem:

1. Zadejte název pro cíl služby Compute. 
1. Vyberte typ výpočetních prostředků, které se mají připojit. Z Azure Machine Learning studia se nedají připojit všechny výpočetní typy. Typy výpočetních prostředků, které se dají v současnosti připojit ke školením, zahrnují:
    * Vzdálený virtuální počítač
    * Azure Databricks (pro použití v kanálech strojového učení)
    * Azure Data Lake Analytics (pro použití v kanálech strojového učení)
    * Azure HDInsight

1. Vyplňte formulář a zadejte hodnoty požadovaných vlastností.

    > [!NOTE]
    > Microsoft doporučuje používat klíče SSH, které jsou bezpečnější než hesla. Hesla jsou zranitelná proti útokům hrubou silou. Klíče SSH spoléhají na kryptografické signatury. Informace o tom, jak vytvořit klíče SSH pro použití s Azure Virtual Machines, najdete v následujících dokumentech:
    >
    > * [Vytvoření a použití klíčů SSH v systému Linux nebo macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Vytvoření a použití klíčů SSH ve Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Vyberte __připojit__. 


## <a name="next-steps"></a>Další kroky

Po vytvoření a připojení cíle k vašemu pracovnímu prostoru ho použijete v [konfiguraci spuštění](how-to-set-up-training-targets.md) s `ComputeTarget` objektem:

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

* Použijte výpočetní prostředek k [odeslání školicího běhu](how-to-set-up-training-targets.md).
* [Kurz: výuka modelu](tutorial-train-models-with-aml.md) používá ke školení modelu spravovaný výpočetní cíl.
* Naučte se [efektivně ladit parametry](how-to-tune-hyperparameters.md) pro vytváření lepších modelů.
* Jakmile budete mít školený model, zjistěte, [jak a kde nasadit modely](how-to-deploy-and-where.md).
* [Použití Azure Machine Learning s Azure Virtual Networks](how-to-enable-virtual-network.md)
