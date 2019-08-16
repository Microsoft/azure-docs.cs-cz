---
title: Změna přístupových klíčů účtu úložiště
titleSuffix: Azure Machine Learning service
description: Naučte se, jak změnit přístupové klávesy pro účet Azure Storage používaný vaším pracovním prostorem. Služba Azure Machine Learning používá k ukládání dat a modelů Azure Storage účet. Po opětovném vygenerování přístupového klíče pro účet úložiště musíte aktualizovat službu Azure Machine Learning, aby používala nové klíče.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/16/2019
ms.openlocfilehash: e386e34a8326a51753631ee9ea4215d01ba7ceb3
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558225"
---
# <a name="regenerate-storage-account-access-keys"></a>Znovu vygenerovat přístupové klíče účtu úložiště

Naučte se, jak změnit přístupové klávesy pro účty Azure Storage používané službou Azure Machine Learning. Azure Machine Learning můžou používat účty úložiště k ukládání dat nebo školených modelů.

Z bezpečnostních důvodů možná budete muset změnit přístupové klávesy pro účet Azure Storage. Po opětovném vygenerování přístupového klíče je nutné aktualizovat Azure Machine Learning, aby používala nový klíč. Azure Machine Learning může používat účet úložiště pro úložiště modelů i jako úložiště dat.

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v článku o [Vytvoření pracovního prostoru](how-to-manage-workspace.md) .

* [Sada Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [Rozšíření Azure Machine Learning CLI](reference-azure-machine-learning-cli.md).

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Co je potřeba aktualizovat

Účty úložiště můžou použít pracovní prostor služby Azure Machine Learning (ukládání protokolů, modelů, snímků atd.) a jako úložiště dat. Proces aktualizace pracovního prostoru je jedním příkazem rozhraní příkazového řádku Azure a může být spuštěn po aktualizaci klíče úložiště. Proces aktualizace úložiště dat je více zapojen a vyžaduje zjištění, jaké úložiště dat aktuálně používá účet úložiště, a pak je znovu zaregistrujete.

> [!IMPORTANT]
> Aktualizujte pracovní prostor pomocí Azure CLI a úložiště dat pomocí Pythonu ve stejnou dobu. Aktualizace pouze jednoho nebo druhého není dostačující a může způsobit chyby, dokud se obě aktualizují.

Chcete-li zjistit účty úložiště, které jsou používány ve vašich úložištích dat, použijte následující kód:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

Tento kód vyhledá všechna registrovaná úložiště dat, která používají Azure Storage, a zobrazí následující informace:

* Název úložiště dat: Název úložiště dat, pod kterým je účet úložiště zaregistrován
* Název účtu úložiště: Název Azure Storage účtu.
* Vnitřního Kontejner v účtu úložiště, který se používá v této registraci.

Označuje také, zda je úložiště dat pro objekt blob Azure nebo sdílenou složku Azure, protože existují různé metody, jak znovu zaregistrovat každý typ úložiště dat.

Pokud existuje položka pro účet úložiště, pro který plánujete znovu vygenerovat přístupové klíče pro, uložte název úložiště dat, název účtu úložiště a název kontejneru.

## <a name="update-the-access-key"></a>Aktualizace přístupového klíče

Chcete-li aktualizovat službu Azure Machine Learning, aby používala nový klíč, použijte následující postup:

> [!IMPORTANT]
> Proveďte všechny kroky, aktualizujte pracovní prostor pomocí rozhraní příkazového řádku a úložiště dat pomocí Pythonu. Aktualizace pouze jednoho nebo druhého může způsobit chyby, dokud se obě aktualizují.

1. Znovu vygenerujte klíč. Informace o opětovném generování přístupového klíče najdete v článku [Správa účtu úložiště](/azure/storage/common/storage-account-manage#access-keys) . Uložte nový klíč.

1. Pokud chcete pracovní prostor aktualizovat tak, aby používal nový klíč, použijte následující postup:

    1. Přihlaste se k předplatnému Azure, které obsahuje váš pracovní prostor, pomocí následujícího příkazu Azure CLI:

        ```azurecli-interactive
        az login
        ```

    1. Pokud chcete pracovní prostor aktualizovat tak, aby používal nový klíč, použijte následující příkaz. Nahraďte `myworkspace` názvem svého pracovního prostoru Azure Machine Learning a nahraďte `myresourcegroup` názvem skupiny prostředků Azure, která obsahuje pracovní prostor.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

        Tento příkaz automaticky synchronizuje nové klíče pro účet služby Azure Storage, který používá pracovní prostor.

1. Pokud chcete znovu zaregistrovat úložiště dat, která používají účet úložiště, použijte hodnoty z části [co je potřeba aktualizovat](#whattoupdate) a klíč z kroku 1 s následujícím kódem:

    ```python
    # Re-register the blob container
    ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                              datastore_name='your datastore name',
                                              container_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
    # Re-register file shares
    ds_file = Datastore.register_azure_file_share(workspace=ws,
                                          datastore_name='your datastore name',
                                          file_share_name='your container name',
                                          account_name='your storage account name',
                                          account_key='new storage account key',
                                          overwrite=True)
    
    ```

    Protože `overwrite=True` je zadán, tento kód přepíše existující registraci a aktualizuje ji, aby používala nový klíč.

## <a name="next-steps"></a>Další kroky

Další informace o registraci úložišť dat najdete v odkazu na [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) třídu.
