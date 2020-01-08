---
title: Změna přístupových klíčů účtu úložiště
titleSuffix: Azure Machine Learning
description: Naučte se, jak změnit přístupové klávesy pro účet Azure Storage používaný vaším pracovním prostorem. Azure Machine Learning používá k ukládání dat a modelů účet Azure Storage.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/06/2019
ms.openlocfilehash: becb05616ecac51fa4646836a6b1a2f3b71d3543
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540838"
---
# <a name="regenerate-storage-account-access-keys"></a>Znovu vygenerovat přístupové klíče účtu úložiště
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Naučte se, jak změnit přístupové klávesy pro účty Azure Storage používané v Azure Machine Learning. Azure Machine Learning můžou používat účty úložiště k ukládání dat nebo školených modelů.

Z bezpečnostních důvodů možná budete muset změnit přístupové klávesy pro účet Azure Storage. Po opětovném vygenerování přístupového klíče je nutné aktualizovat Azure Machine Learning, aby používala nový klíč. Azure Machine Learning může používat účet úložiště pro úložiště modelů i jako úložiště dat.

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v článku o [Vytvoření pracovního prostoru](how-to-manage-workspace.md) .

* [Sada Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [Rozšíření Azure Machine Learning CLI](reference-azure-machine-learning-cli.md).

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Co je potřeba aktualizovat

Účty úložiště můžou použít pracovní prostor Azure Machine Learning (ukládání protokolů, modelů, snímků atd.) a jako úložiště dat. Proces aktualizace pracovního prostoru je jedním příkazem rozhraní příkazového řádku Azure a může být spuštěn po aktualizaci klíče úložiště. Proces aktualizace úložiště dat je více zapojen a vyžaduje zjištění, jaké úložiště dat aktuálně používá účet úložiště, a pak je znovu zaregistrujete.

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

* Název úložiště dat: název úložiště dat, pod kterým je účet úložiště zaregistrován.
* Název účtu úložiště: název účtu Azure Storage.
* Container: kontejner v účtu úložiště, který se používá v této registraci.

Označuje také, zda je úložiště dat pro objekt blob Azure nebo sdílenou složku Azure, protože existují různé metody, jak znovu zaregistrovat každý typ úložiště dat.

Pokud existuje položka pro účet úložiště, pro který plánujete znovu vygenerovat přístupové klíče pro, uložte název úložiště dat, název účtu úložiště a název kontejneru.

## <a name="update-the-access-key"></a>Aktualizace přístupového klíče

Chcete-li aktualizovat Azure Machine Learning pro použití nového klíče, použijte následující postup:

> [!IMPORTANT]
> Proveďte všechny kroky, aktualizujte pracovní prostor pomocí rozhraní příkazového řádku a úložiště dat pomocí Pythonu. Aktualizace pouze jednoho nebo druhého může způsobit chyby, dokud se obě aktualizují.

1. Znovu vygenerujte klíč. Informace o opětovném generování přístupového klíče najdete v tématu [Správa přístupových klíčů účtu úložiště](../storage/common/storage-account-keys-manage.md). Uložte nový klíč.

1. Pokud chcete pracovní prostor aktualizovat tak, aby používal nový klíč, použijte následující postup:

    1. Přihlaste se k předplatnému Azure, které obsahuje váš pracovní prostor, pomocí následujícího příkazu Azure CLI:

        ```azurecli-interactive
        az login
        ```

    1. Pokud chcete pracovní prostor aktualizovat tak, aby používal nový klíč, použijte následující příkaz. Nahraďte `myworkspace` názvem pracovního prostoru Azure Machine Learning a nahraďte `myresourcegroup` názvem skupiny prostředků Azure, která obsahuje pracovní prostor.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

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

    Vzhledem k tomu, že je zadána `overwrite=True`, tento kód přepíše existující registraci a aktualizuje ji, aby používala nový klíč.

## <a name="next-steps"></a>Další kroky

Další informace o registraci úložiště dat naleznete v tématu [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) Class Reference třídy.
