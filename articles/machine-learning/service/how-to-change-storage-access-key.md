---
title: Změna přístupové klíče účtu úložiště
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak změnit přístupových klíčů pro účet služby Azure Storage používaný pracovního prostoru. Služba Azure Machine Learning používá účet Azure Storage k ukládání dat a modelů. Při opětovném generování přístupového klíče pro účet úložiště, je nutné aktualizovat službu Azure Machine Learning, aby se používaly nové klíče.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 05/17/2019
ms.openlocfilehash: 488a032e177897caf2897ba6335f4e7f64dc0e4d
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543829"
---
# <a name="regenerate-storage-account-access-keys"></a>Znovu vygenerovat přístupové klíče účtu úložiště

Zjistěte, jak změnit přístupových klíčů pro účty Azure Storage používá služba Azure Machine Learning. Azure Machine Learning můžete použít účty úložiště k ukládání dat nebo trénované modely.

Z bezpečnostních důvodů budete muset změnit přístupových klíčů pro účet služby Azure Storage. Když znovu vygenerujete přístupové klávesy, Azure Machine Learning musí aktualizovat na používání nového klíče. Azure Machine Learning může pomocí účtu úložiště, jako i modelu úložiště a jako datového úložiště.

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [vytvořit pracovní prostor](setup-create-workspace.md) článku.

* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [Rozšíření rozhraní příkazového řádku pro Azure Machine Learning](reference-azure-machine-learning-cli.md).

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Co je potřeba aktualizovat

Pracovní prostor služby Azure Machine Learning (uložení protokolů, modely, snímky, atd.) a jako úložiště dat je možné účty úložiště. Proces pro aktualizaci pracovního prostoru je jediným příkazem rozhraní příkazového řádku Azure a můžete spustit po aktualizaci klíče úložiště. Proces aktualizace úložišť je složitější a vyžaduje identifikaci úložišť, které právě používáte účet úložiště a pak je znovu zaregistrovat.

> [!IMPORTANT]
> Aktualizujte pracovní prostor pomocí rozhraní příkazového řádku Azure a úložiště dat pomocí Pythonu, ve stejnou dobu. Aktualizuje jenom jedna z nich není dostatečná a může způsobit chyby, dokud se obě jsou aktualizována.

Ke zjišťování účtů úložiště, které využívají vaše úložiště, použijte následující kód:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " + default_ds.account_name + ", container name: " + ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob" or ds.datastore_type == "AzureFile":
        print("datastore name: " + name + ", storage account name: " + ds.account_name + ", container name: " + ds.container_name)
```

Tento kód vyhledá všechny registrované úložišť, která používá službu Azure Storage a obsahuje následující informace:

* Název úložiště dat: Název úložiště dat, který účet úložiště je zaregistrovaný pod.
* Název účtu úložiště: Název účtu služby Azure Storage.
* Kontejner: Kontejner v účtu úložiště, který používá tuto registraci.

Pokud záznam existuje, kterou chcete použít v opětovné generování přístupových klíčů pro účet úložiště, uložte název úložiště dat, název účtu úložiště a název kontejneru.

## <a name="update-the-access-key"></a>Aktualizovat přístupový klíč

Pokud chcete aktualizovat službu Azure Machine Learning, aby používala nový klíč, použijte následující kroky:

> [!IMPORTANT]
> Proveďte všechny kroky, aktualizace i pracovním prostoru pomocí rozhraní příkazového řádku a úložišť pomocí Pythonu. Aktualizuje se jenom jeden z nich může způsobit chyby, dokud obě jsou aktualizována.

1. Znovu vygenerujte klíč. Informace o opětovné generování přístupového klíče najdete v tématu [Správa účtu úložiště](/azure/storage/common/storage-account-manage#access-keys) článku. Uložte nový klíč.

1. K aktualizaci pracovního prostoru na používání nového klíče, použijte následující kroky:

    1. Pro přihlášení k předplatnému Azure, která obsahuje váš pracovní prostor pomocí následujícího příkazu rozhraní příkazového řádku Azure:

        ```azurecli-interactive
        az login
        ```

    1. Pokud chcete nainstalovat rozšíření Azure Machine Learning, použijte následující příkaz:

        ```azurecli-interactive
        az extension add -n azure-cli-ml 
        ```

    1. Pokud chcete aktualizovat pracovní prostor používat nový klíč, použijte následující příkaz. Nahraďte `myworkspace` se název pracovního prostoru Azure Machine Learning a nahradit `myresourcegroup` s názvem skupiny prostředků Azure, který obsahuje tento pracovní prostor.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        Tento příkaz se automaticky synchronizuje nové klíče pro účet úložiště Azure používají pracovní prostor.

1. Pro opětovné zaregistrování úložiště dat, použít účet úložiště, použijte hodnoty z [co je potřeba aktualizovat](#whattoupdate) oddílu a klíče z kroku 1 následujícím kódem:

    ```python
    ds = Datastore.register_azure_blob_container(workspace=ws, 
                                              datastore_name='your datastore name', 
                                              container_name='your container name',
                                              account_name='your storage account name', 
                                              account_key='new storage account key',
                                              overwrite=True)
    ```

    Protože `overwrite=True` není zadána, tento kód přepíše existující registraci a aktualizuje na používání nového klíče.

## <a name="next-steps"></a>Další postup

Další informace o registraci úložišť najdete v tématu [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) referenční třídy.
