---
title: Použití tajných kódů v tréninkových běhech
titleSuffix: Azure Machine Learning
description: Předávat tajemství tréninkovým spuštěním bezpečným způsobem pomocí trezoru klíčů pracovního prostoru
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: d877794abf12b8b412cd1ecf4efd72fd1179d768
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942256"
---
# <a name="use-secrets-in-training-runs"></a>Použití tajných kódů v tréninkových běhech
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak používat tajné kódy v školení běží bezpečně. Ověřovací informace, jako je vaše uživatelské jméno a heslo, jsou tajné klíče. Pokud se například připojíte k externí databázi za účelem dotazování na trénovací data, budete muset předat uživatelské jméno a heslo do kontextu vzdáleného spuštění. Kódování těchto hodnot do školicích skriptů ve prostém textu je nezabezpečené, protože by odhalilo tajný klíč. 

Místo toho váš pracovní prostor Azure Machine Learning má přidružený prostředek s názvem [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Tento trezor klíčů slouží k předání tajných kódů vzdálenému spuštění bezpečně prostřednictvím sady api v sadě Azure Machine Learning Python SDK.

Základní tok pro použití tajných kódů je:
 1. V místním počítači se přihlaste do Azure a připojte se ke svému pracovnímu prostoru.
 2. V místním počítači nastavte tajný klíč v trezoru klíčů pracovního prostoru.
 3. Odešlete vzdálené spuštění.
 4. V rámci vzdáleného spuštění získejte tajný klíč z trezoru klíčů a použijte jej.

## <a name="set-secrets"></a>Nastavení tajných kódů

V Azure Machine Learning, [Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) třída obsahuje metody pro nastavení tajných kódů. V místní relaci Pythonu nejprve získejte odkaz na trezor [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) klíčů pracovního prostoru a potom pomocí metody nastavte tajný klíč podle názvu a hodnoty. Metoda __set_secret__ aktualizuje hodnotu tajného klíče, pokud název již existuje.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Nevkládejte tajnou hodnotu do kódu Pythonu, protože je nejistá pro uložení do souboru jako jasný text. Místo toho získat hodnotu tajného klíče z proměnné prostředí, například Azure DevOps sestavení tajného klíče nebo z interaktivního vstupu uživatele.

Můžete seznam tajných [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#list-secrets--) názvů pomocí metody a je také dávková verze,[set_secrets(),](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) který umožňuje nastavit více tajných kódů najednou.

## <a name="get-secrets"></a>Získejte tajemství

V místním kódu můžete použít[`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) metodu k získání tajné hodnoty podle názvu.

Pro spuštění [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-) odeslané [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) , použijte [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) metodu s třídou. Vzhledem k tomu, že odeslané spuštění si je vědomo svého pracovního prostoru, tato metoda zástupce instance pracovního prostoru a vrátí hodnotu tajného klíče přímo.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Dávejte pozor, abyste neodhalili tajnou hodnotu zápisem nebo tiskem.

K dispozici je také dávková [verze, get_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) pro přístup k více tajných kódů najednou.

## <a name="next-steps"></a>Další kroky

 * [Zobrazení ukázkového poznámkového bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Další informace o podnikovém zabezpečení pomocí Azure Machine Learning](concept-enterprise-security.md)
