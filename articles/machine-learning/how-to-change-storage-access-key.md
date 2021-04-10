---
title: Změna přístupových klíčů účtu úložiště
titleSuffix: Azure Machine Learning
description: Naučte se, jak změnit přístupové klávesy pro účet Azure Storage používaný vaším pracovním prostorem. Azure Machine Learning používá k ukládání dat a modelů účet Azure Storage.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/19/2020
ms.openlocfilehash: 787f7727e18b7055055ed2b764ddce5be20f5afc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102503432"
---
# <a name="regenerate-storage-account-access-keys"></a>Znovu vygenerovat přístupové klíče účtu úložiště


Naučte se, jak změnit přístupové klávesy pro účty Azure Storage používané v Azure Machine Learning. Azure Machine Learning můžou používat účty úložiště k ukládání dat nebo školených modelů.

Z bezpečnostních důvodů možná budete muset změnit přístupové klávesy pro účet Azure Storage. Po opětovném vygenerování přístupového klíče je nutné aktualizovat Azure Machine Learning, aby používala nový klíč. Azure Machine Learning může používat účet úložiště pro úložiště modelů i jako úložiště dat.

> [!IMPORTANT]

> Přihlašovací údaje registrované pro úložiště dat se ukládají do Azure Key Vault přidružených k pracovnímu prostoru. Pokud máte v Key Vault povoleno [obnovitelné odstranění](../key-vault/general/soft-delete-overview.md) , v tomto článku najdete pokyny k aktualizaci přihlašovacích údajů. Pokud zrušíte registraci úložiště dat a pokusíte se ho znovu zaregistrovat pod stejným názvem, tato akce se nezdaří. Pokud chcete v tomto scénáři povolit obnovitelné odstranění, přečtěte si téma [Zapnutí obnovitelného odstranění pro existující Trezor klíčů]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault) .

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v článku o [Vytvoření pracovního prostoru](how-to-manage-workspace.md) .

* [Sada Azure Machine Learning SDK](/python/api/overview/azure/ml/install).

* [Rozšíření Azure Machine Learning CLI](reference-azure-machine-learning-cli.md).

> [!NOTE]
> Fragmenty kódu v tomto dokumentu byly testovány pomocí 1.0.83 verze sady Python SDK.

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

1. Pracovní prostor Azure Machine Learning automaticky provede synchronizaci nového klíče a začne ho používat po celou hodinu. Pokud chcete přinutit, aby se pracovní prostor automaticky synchronizoval k novému klíči, použijte následující postup:

    1. Přihlaste se k předplatnému Azure, které obsahuje váš pracovní prostor, pomocí následujícího příkazu Azure CLI:

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. Pokud chcete pracovní prostor aktualizovat tak, aby používal nový klíč, použijte následující příkaz. Nahraďte `myworkspace` názvem svého pracovního prostoru Azure Machine Learning a nahraďte `myresourcegroup` názvem skupiny prostředků Azure, která obsahuje pracovní prostor.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        Tento příkaz automaticky synchronizuje nové klíče pro účet služby Azure Storage, který používá pracovní prostor.

1. Úložiště dat, která používají účet úložiště, můžete znovu zaregistrovat pomocí sady SDK nebo [Azure Machine Learning studia](https://ml.azure.com).
    1. **Chcete-li znovu zaregistrovat úložiště dat prostřednictvím sady Python SDK**, použijte hodnoty z oddílu [co je potřeba aktualizovat](#whattoupdate) a klíč z kroku 1 s následujícím kódem. 
    
        Protože `overwrite=True` je zadán, tento kód přepíše existující registraci a aktualizuje ji, aby používala nový klíč.
    
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
    
    1. Pokud **Chcete úložiště dat znovu zaregistrovat přes Studio**, vyberte **úložiště dat** v levém podokně studia. 
        1. Vyberte úložiště dat, které chcete aktualizovat.
        1. V levém horním rohu vyberte tlačítko pro **aktualizaci přihlašovacích údajů** . 
        1. Pomocí nového přístupového klíče z kroku 1 Naplňte formulář a klikněte na **Uložit**.
        
            Pokud aktualizujete přihlašovací údaje pro vaše **výchozí úložiště dat**, proveďte tento krok a opakujte krok 2b a znovu synchronizujte nový klíč s výchozím úložištěm dat pracovního prostoru. 

## <a name="next-steps"></a>Další kroky

Další informace o registraci úložišť dat najdete v [`Datastore`](/python/api/azureml-core/azureml.core.datastore%28class%29) odkazu na třídu.