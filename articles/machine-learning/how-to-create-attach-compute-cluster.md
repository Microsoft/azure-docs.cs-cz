---
title: Vytváření výpočetních clusterů
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet výpočetní clustery v pracovním prostoru Azure Machine Learning. Výpočetní cluster použijte jako cíl výpočetní služby pro školení nebo odvození.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: f9b970f498db7530b35c57a75edeae55dec3f22d
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100098839"
---
# <a name="create-an-azure-machine-learning-compute-cluster"></a>Vytvoření clusteru Azure Machine Learning COMPUTE

Naučte se vytvářet a spravovat [výpočetní cluster](concept-compute-target.md#azure-machine-learning-compute-managed) v pracovním prostoru Azure Machine Learning.

Azure Machine Learning výpočetní cluster můžete použít k distribuci procesu vybírání školení nebo dávek v rámci clusteru výpočetních uzlů procesoru nebo GPU v cloudu. Další informace o velikostech virtuálních počítačů, které zahrnují GPU, najdete v tématu [velikosti virtuálních počítačů optimalizované pro GPU](../virtual-machines/sizes-gpu.md). 

V tomto článku se dozvíte, jak:

* Vytvoření výpočetního clusteru
* Snižte náklady na výpočetní cluster.
* Nastavení [spravované identity](../active-directory/managed-identities-azure-resources/overview.md) pro cluster

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

* [Rozšíření Azure CLI pro službu Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)nebo [rozšíření Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

## <a name="what-is-a-compute-cluster"></a>Co je výpočetní cluster?

Výpočetní cluster Azure Machine Learning je spravovaná výpočetní infrastruktura, která umožňuje snadno vytvořit výpočetní prostředí s jedním uzlem nebo několika uzly. Výpočetní prostředí se vytvoří v rámci vaší oblasti pracovního prostoru jako prostředek, který se dá sdílet s ostatními uživateli v pracovním prostoru. Výpočetní výkon se při odeslání úlohy automaticky škáluje a dá se umístit do Azure Virtual Network. Výpočetní výkon se spouští v kontejnerovém prostředí a zabalí závislosti vašich modelů v [kontejneru Docker](https://www.docker.com/why-docker).

Výpočetní clustery můžou úlohy bezpečně spouštět ve [virtuálním síťovém prostředí](how-to-secure-training-vnet.md), aniž by museli podniky otevírat porty SSH. Úloha se spustí v kontejnerovém prostředí a zabalí závislosti vašich modelů v kontejneru Docker. 

## <a name="limitations"></a>Omezení

* **Nevytvářejte více souběžných příloh se stejným výpočetním** prostředím z pracovního prostoru. Například připojení jednoho výpočetního clusteru k pracovnímu prostoru pomocí dvou různých názvů. Každá nová příloha zruší předchozí existující přílohy.

    Pokud chcete změnit připojení cíle výpočtů, například chcete-li změnit nastavení konfigurace clusteru, musíte nejprve odebrat existující přílohu.

* Některé scénáře uvedené v tomto dokumentu jsou označeny jako __Náhled__. Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Azure Machine Learning COMPUTE má výchozí omezení, například počet jader, které se dají přidělit. Další informace najdete v tématu [Správa a vyžádání kvót pro prostředky Azure](how-to-manage-quotas.md).

* Azure umožňuje umístit _zámky_ na prostředky, aby se nemohly odstranit nebo jsou jen pro čtení. __Neaplikujte zámky prostředků na skupinu prostředků, která obsahuje váš pracovní prostor__. Když použijete zámek pro skupinu prostředků, která obsahuje váš pracovní prostor, zabráníte operacím škálování pro výpočetní clustery Azure ML. Další informace o uzamykání prostředků najdete v tématu [uzamčení prostředků, aby nedocházelo k neočekávaným změnám](../azure-resource-manager/management/lock-resources.md).

> [!TIP]
> Clustery můžou obecně škálovat až 100 uzlů, pokud máte dostatečnou kvótu pro požadovaný počet jader. Ve výchozím nastavení jsou clustery nastavené s povolenou komunikací mezi uzly mezi uzly clusteru za účelem podpory MPI úloh. Můžete ale škálovat clustery na tisíce uzlů pouhým vyvoláním [lístku podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)a žádostí o povolení seznamu pro vaše předplatné nebo pracovní prostor nebo konkrétního clusteru pro zakázání komunikace mezi uzly. 


## <a name="create"></a>Vytvořit

**Časový odhad**: přibližně 5 minut.

Azure Machine Learning výpočetní prostředí je možné znovu použít v rámci spuštění. Výpočetní prostředky je možné sdílet s ostatními uživateli v pracovním prostoru a jsou mezi nimi zachované, automaticky škálovat uzly nahoru nebo dolů na základě počtu odeslaných běhů a max_nodes nastavených v clusteru. Nastavení min_nodes řídí minimální dostupné uzly.

Vyhrazená jádra pro každou oblast a kvótu na úrovni virtuálních počítačů, která platí pro vytvoření výpočetního clusteru, jsou sjednocená a sdílená s Azure Machine Learningm školením kvóty výpočetních instancí. 

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Výpočetní výkon se při použití vymění až na nula uzlů.   Vyhrazené virtuální počítače se vytvářejí ke spouštění vašich úloh podle potřeby.
    
# <a name="python"></a>[Python](#tab/python)

Pokud chcete v Pythonu vytvořit trvalý Azure Machine Learning výpočetní prostředek, zadejte vlastnosti **vm_size** a **max_nodes** . Azure Machine Learning pak pro ostatní vlastnosti používá inteligentní výchozí hodnoty. 
    
* **vm_size**: rodina virtuálních počítačů uzlů vytvořená Azure Machine Learning Compute.
* **max_nodes**: maximální počet uzlů pro automatické horizontální navýšení kapacity při spuštění úlohy v Azure Machine Learning Compute.


[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

Při vytváření Azure Machine Learning výpočetních prostředků můžete také nakonfigurovat několik pokročilých vlastností. Vlastnosti umožňují vytvořit trvalý cluster s pevnou velikostí nebo v rámci stávajícího Virtual Network Azure v rámci vašeho předplatného.  Podrobnosti najdete v tématu [Třída AmlCompute](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?preserve-view=true&view=azure-ml-py) .


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)


```azurecli-interactive
az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
```

Další informace najdete v tématu [AZ ml computetarget Create amlcompute](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute&preserve-view=true).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Informace o vytvoření výpočetního clusteru v nástroji Studio najdete v tématu [Vytvoření výpočetních cílů v Azure Machine Learning Studiu](how-to-create-attach-compute-studio.md#amlcompute).

---

 ## <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Snižte náklady na výpočetní cluster.

Můžete se také rozhodnout použít pro spuštění některých nebo všech úloh [virtuální počítače s nízkou prioritou](concept-plan-manage-cost.md#low-pri-vm) . Tyto virtuální počítače nemají zaručenou dostupnost a můžou být při použití přerušeny. Bude nutné restartovat úlohu s přerušením. 

K určení virtuálního počítače s nízkou prioritou použijte libovolný z těchto způsobů:
    
# <a name="python"></a>[Python](#tab/python)
    
```python
compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            vm_priority='lowpriority',
                                                            max_nodes=4)
```
    
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Nastavte `vm-priority` :
    
```azurecli-interactive
az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
```

# <a name="studio"></a>[Studio](#tab/azure-studio)

V nástroji Studio při vytváření virtuálního počítače vyberte možnost **Nízká priorita** .

--- 

## <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Nastavení spravované identity

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

# <a name="python"></a>[Python](#tab/python)

* Konfigurace spravované identity v konfiguraci zřizování:  

    * Spravovaná identita přiřazená systémem:
        ```python
        # configure cluster with a system-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="SystemAssigned",
                                                                )
        ```
    
    * Spravovaná identita přiřazená uživatelem:
    
        ```python
        # configure cluster with a user-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="UserAssigned",
                                                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```

* Přidat spravovanou identitu do existujícího výpočetního clusteru 
    
    * Spravovaná identita přiřazená systémem:
    
        ```python
        # add a system-assigned managed identity
        cpu_cluster.add_identity(identity_type="SystemAssigned")
        ````
    
    * Spravovaná identita přiřazená uživatelem:
    
        ```python
        # add a user-assigned managed identity
        cpu_cluster.add_identity(identity_type="UserAssigned", 
                                    identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
        ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Vytvoření nového spravovaného výpočetního clusteru se spravovanou identitou

  * Spravovaná identita přiřazená uživatelem

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  * Spravovaná identita přiřazená systémem

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
* Přidejte spravovanou identitu do existujícího clusteru:

    * Spravovaná identita přiřazená uživatelem
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    * Spravovaná identita přiřazená systémem

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

# <a name="studio"></a>[Studio](#tab/azure-studio)

Viz [nastavení spravované identity v nástroji Studio](how-to-create-attach-compute-studio.md#managed-identity).

---

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

### <a name="managed-identity-usage"></a>Využití spravované identity

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="troubleshooting"></a>Řešení potíží

Je pravděpodobné, že někteří uživatelé, kteří vytvořili svůj Azure Machine Learning pracovní prostor z Azure Portal před vydáním GA, nemusí být schopni vytvořit AmlCompute v tomto pracovním prostoru. Můžete buď vyvolat žádost o podporu na službu, nebo vytvořit nový pracovní prostor prostřednictvím portálu nebo sadu SDK pro okamžité odblokování.

Pokud se váš Azure Machine Learning výpočetní cluster zobrazí při změně velikosti (0-> 0) pro stav uzlu, může to být způsobeno zámky prostředků Azure.

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Další kroky

Cluster COMPUTE použijte k těmto akcím:

* [Odeslat školicí běh](how-to-set-up-training-targets.md) 
* [Spusťte odvození dávky](./tutorial-pipeline-batch-scoring-classification.md).
