---
title: Vytvoření propojené služby s synapse a pracovními prostory Azure Machine Learning (Preview)
titleSuffix: Azure Machine Learning
description: Naučte se, jak propojit pracovní prostory Azure synapse a Azure Machine Learning pro sjednocení tahání datových prostředí.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/08/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: dd62fb5e3c7450d50b9837ee5484ca480cab78aa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640842"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-preview"></a>Propojení Azure synapse Analytics a Azure Machine Learning pracovních prostorů (Preview)

V tomto článku se naučíte, jak vytvořit propojenou službu, která propojuje váš pracovní prostor [Azure synapse Analytics](/synapse-analytics/overview-what-is.md) a [pracovní prostor Azure Machine Learning](concept-workspace.md).

S vaším pracovním prostorem Azure Machine Learning propojeným s vaším pracovním prostorem Azure synapse můžete připojit fond Apache Spark jako vyhrazený výpočetní výkon pro data tahání ve velkém měřítku a provádět školení modelu ze stejného poznámkového bloku.

Pracovní prostor ML a pracovní prostor synapse můžete propojit pomocí [sady Python SDK](#link-sdk) nebo [Azure Machine Learning Studio](#link-studio).

Pracovní prostory můžete také propojit a připojit fond synapse Spark s jednou [šablonou Azure Resource Manager (ARM)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json).

>[!IMPORTANT]
> Integrace Azure Machine Learning a Azure synapse je ve verzi Public Preview. Funkce prezentované z `azureml-synapse` balíčku jsou [experimentální](/python/api/overview/azure/ml/#stable-vs-experimental) funkce verze Preview a můžou se kdykoli změnit.

## <a name="prerequisites"></a>Požadavky

* [Vytvořte pracovní prostor Azure Machine Learning](how-to-manage-workspace.md?tabs=python).

* [Vytvořte pracovní prostor synapse v Azure Portal](/azure/synapse-analytics/quickstart-create-workspace).

* [Vytvoření fondu Apache Spark pomocí Azure Portal, webových nástrojů nebo synapse studia](/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio)

* Instalace [sady Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)

* Přístup k [Azure Machine Learning Studiu](https://ml.azure.com/).

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>Propojení pracovních prostorů se sadou Python SDK

> [!IMPORTANT]
> Pokud chcete úspěšně propojit pracovní prostor synapse, musíte mít přidělenou roli **vlastníka** pracovního prostoru synapse. Ověřte přístup v [Azure Portal](https://ms.portal.azure.com/).
>
> Pokud nejste **vlastníkem** a máte jenom **přispěvatele** k pracovnímu prostoru synapse, můžete použít jenom existující propojené služby. Přečtěte si [, jak načíst a použít existující propojenou službu](how-to-data-prep-synapse-spark-pool.md#get-an-existing-linked-service).

Následující kód využívá [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice) [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration) třídy a k,

* Propojte pracovní prostor Machine Learning `ws` s vaším pracovním prostorem Azure synapse.
* Zaregistrujte svůj pracovní prostor synapse s Azure Machine Learning jako propojenou službu.

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```

> [!IMPORTANT] 
> `system_assigned_identity_principal_id`Pro každou propojenou službu se vytvoří spravovaná identita. Této spravované identitě musí být role **Synapse Apache Spark správce** pracovního prostoru synapse udělena předtím, než začnete relaci synapse. [Přiřaďte roli správce Synapse Apache Spark ke spravované identitě v synapse studiu](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md).
>
> Chcete-li najít `system_assigned_identity_principal_id` konkrétní propojenou službu, použijte `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` .

### <a name="manage-linked-services"></a>Správa propojených služeb

Zobrazení všech propojených služeb přidružených k pracovnímu prostoru Machine Learning.

```python
LinkedService.list(ws)
```

Pokud chcete odpojit své pracovní prostory, použijte `unregister()` metodu.

``` python
linked_service.unregister()
```

<a name="link-studio"></a>
## <a name="link-workspaces-via-studio"></a>Propojení pracovních prostorů přes Studio

Propojte pracovní prostor Machine Learning a pracovní prostor synapse pomocí nástroje Azure Machine Learning Studio a proveďte následující kroky: 

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com/).
1. V levém podokně vyberte **propojené služby** v části **Správa** .
1. Vyberte **Přidat integraci**.
1. Na formuláři **pracovního prostoru propojení** vyplňte pole.

    |Pole| Popis    
    |---|---
    |Název| Zadejte název propojené služby. Tento název se použije pro odkaz na tuto konkrétní propojenou službu.
    |Název předplatného | Vyberte název vašeho předplatného, které je přidružené k pracovnímu prostoru Machine Learning. 
    |Pracovní prostor synapse | Vyberte pracovní prostor synapse, ke kterému se chcete připojit.
    
1. Výběrem **Další** otevřete formulář **Vybrat fondy Spark (volitelné)** . V tomto formuláři můžete vybrat, který fond synapse Spark se má připojit k vašemu pracovnímu prostoru.

1. Výběrem možnosti **Další** otevřete formulář **Revize** a zkontrolujte výběr.
1. Vyberte **vytvořit** k dokončení procesu vytváření propojené služby.

## <a name="next-steps"></a>Další kroky

* [Připojte fondy synapse Spark pro přípravu dat pomocí Azure synapse (Preview)](how-to-data-prep-synapse-spark-pool.md).
* [Jak používat Apache Spark v kanálu Machine Learning s Azure synapse (Preview)](how-to-use-synapsesparkstep.md)
* [Výuka modelu](how-to-set-up-training-targets.md).
