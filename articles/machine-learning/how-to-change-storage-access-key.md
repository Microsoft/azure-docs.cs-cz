---
title: Změna přístupových klíčů účtu úložiště
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak změnit přístupové klíče pro účet Azure Storage, který používá váš pracovní prostor. Azure Machine Learning používá účet Azure Storage k ukládání dat a modelů.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: f1541c177cea2d223a5e7df576d95fab7eafb310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296947"
---
# <a name="regenerate-storage-account-access-keys"></a>Obnovení přístupových klíčů účtu úložiště
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Zjistěte, jak změnit přístupové klíče pro účty Azure Storage používané Azure Machine Learning. Azure Machine Learning můžou používat účty úložiště k ukládání dat nebo trénovaného modelu.

Z bezpečnostních důvodů může být nutné změnit přístupové klíče pro účet Azure Storage. Když znovu vygenerujete přístupový klíč, azure machine learning musí být aktualizován, aby používal nový klíč. Azure Machine Learning může používat účet úložiště pro úložiště modelu i jako úložiště dat.

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace naleznete v článku [Vytvoření pracovního prostoru.](how-to-manage-workspace.md)

* Azure [Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* Rozšíření [ClI Azure Machine Learning](reference-azure-machine-learning-cli.md).

> [!NOTE]
> Fragmenty kódu v tomto dokumentu byly testovány s verzí 1.0.83 sady Python SDK.

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Co je třeba aktualizovat

Účty úložiště můžete používat pracovní prostor Azure Machine Learning (ukládání protokolů, modelů, snímků atd.) a jako úložiště dat. Proces aktualizace pracovního prostoru je jeden příkaz Azure CLI a lze jej schovat po aktualizaci klíče úložiště. Proces aktualizace úložiště dat je více zapojen a vyžaduje zjištění, jaké úložiště dat aktuálně používají účet úložiště a pak je znovu zaregistrovat.

> [!IMPORTANT]
> Aktualizujte pracovní prostor pomocí Azure CLI a úložiště dat pomocí Pythonu ve stejnou dobu. Aktualizace pouze jeden nebo druhý není dostatečná a může způsobit chyby, dokud obě jsou aktualizovány.

Chcete-li zjistit účty úložiště, které používají vaše úložiště dat, použijte následující kód:

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

Tento kód vyhledá všechna registrovaná úložiště dat, která používají Azure Storage, a uvádí následující informace:

* Název úložiště dat: Název úložiště dat, pod kterým je účet úložiště registrován.
* Název účtu úložiště: Název účtu Azure Storage.
* Kontejner: Kontejner v účtu úložiště, který se používá v této registraci.

Označuje také, zda úložiště dat je pro Azure Blob nebo sdílené složky Azure, protože existují různé metody pro opětovnou registraci každého typu úložiště dat.

Pokud existuje položka pro účet úložiště, pro který plánujete obnovit přístupové klíče, uložte název úložiště dat, název účtu úložiště a název kontejneru.

## <a name="update-the-access-key"></a>Aktualizace přístupového klíče

Chcete-li aktualizovat Azure Machine Learning používat nový klíč, použijte následující kroky:

> [!IMPORTANT]
> Proveďte všechny kroky a aktualizujte pracovní prostor pomocí rozhraní CLI i úložiště dat pomocí Pythonu. Aktualizace pouze jednoho nebo druhého může způsobit chyby, dokud nebudou obě aktualizovány.

1. Regenerovat klíč. Informace o obnovení přístupového klíče naleznete v [tématu Správa přístupových klíčů účtu úložiště](../storage/common/storage-account-keys-manage.md). Uložte nový klíč.

1. Chcete-li aktualizovat pracovní prostor tak, aby používal nový klíč, postupujte takto:

    1. Přihlášení k předplatnému Azure, které obsahuje váš pracovní prostor, pomocí následujícího příkazu Azure CLI:

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. Chcete-li aktualizovat pracovní prostor tak, aby používal nový klíč, použijte následující příkaz. Nahraďte `myworkspace` název pracovního prostoru Azure `myresourcegroup` Machine Learning a nahraďte názvem skupiny prostředků Azure, která obsahuje pracovní prostor.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        Tento příkaz automaticky synchronizuje nové klíče pro účet úložiště Azure používaný pracovním prostorem.

1. Chcete-li znovu zaregistrovat úložiště dat, které používají účet úložiště, použijte hodnoty z části [Co je potřeba aktualizovat](#whattoupdate) a klíč z kroku 1 s následujícím kódem:

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

    Vzhledem k tomu, `overwrite=True` že je zadán, tento kód přepíše existující registraci a aktualizuje ji použít nový klíč.

## <a name="next-steps"></a>Další kroky

Další informace o registraci úložiště [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) dat naleznete v odkazu na třídu.
