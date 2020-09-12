---
title: Ověřování tajných kódů v školení
titleSuffix: Azure Machine Learning
description: Bezpečné předávání tajných klíčů do školicích běhů pomocí Key Vault pracovního prostoru
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5c7568b0914b5e60d1a47971424f1f04f41745da
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89646953"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>Použití tajných kódů přihlašovacích údajů v Azure Machine Learningch školicích běhů
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak bezpečně používat tajné klíče v školicím běhu. Ověřovací údaje, jako je vaše uživatelské jméno a heslo, jsou tajné. Například pokud se připojíte k externí databázi, abyste mohli zadávat dotazy na školicí data, museli byste předat své uživatelské jméno a heslo do kontextu vzdáleného spuštění. Kódování takových hodnot do školicích skriptů v nešifrovaném textu je nezabezpečené, protože by to vystavilo tajný klíč. 

Místo toho má váš pracovní prostor Azure Machine Learning přidružený prostředek nazvaný [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Pomocí tohoto Key Vault můžete přes sadu rozhraní API v sadě SDK pro Azure Machine Learning Python předat tajným klíčům ke vzdáleným zabezpečeným způsobem.

Základní tok pro používání tajných klíčů je:
 1. V místním počítači se přihlaste k Azure a připojte se k pracovnímu prostoru.
 2. V místním počítači nastavte tajný klíč v Key Vault pracovního prostoru.
 3. Odešlete vzdálené spuštění.
 4. V rámci vzdáleného spuštění Získejte tajný klíč z Key Vault a použijte ho.

## <a name="set-secrets"></a>Nastavení tajných kódů

V Azure Machine Learning třída [trezoru](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true) klíčů obsahuje metody pro nastavení tajných kódů. V místní relaci Pythonu nejprve získejte odkaz na váš pracovní prostor Key Vault a pak použijte [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#&preserve-view=trueset-secret-name--value-) metodu pro nastavení tajného klíče podle názvu a hodnoty. Metoda __set_secret__ aktualizuje tajnou hodnotu, pokud název již existuje.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Neumísťujte tajnou hodnotu do kódu Pythonu, protože je nezabezpečený pro uložení v souboru jako nešifrovaný text. Místo toho Získejte tajnou hodnotu z proměnné prostředí, například tajného kódu sestavení Azure DevOps nebo interaktivního uživatelského vstupu.

Pomocí metody můžete vytvořit seznam tajných názvů [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#&preserve-view=truelist-secrets--) a také dávkovou verzi[set_secrets ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#&preserve-view=trueset-secrets-secrets-batch-) , která umožňuje nastavit více tajných klíčů současně.

## <a name="get-secrets"></a>Získání tajných kódů

V místním kódu můžete pomocí [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#&preserve-view=trueget-secret-name-) metody získat tajnou hodnotu podle názvu.

Pro spuštění [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#&preserve-view=truesubmit-config--tags-none----kwargs-)  , použijte [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#&preserve-view=trueget-secret-name-) metodu s [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py&preserve-view=true) třídou. Vzhledem k tomu, že odeslaný běh ví o svém pracovním prostoru, tato metoda vytvoří instanci pracovního prostoru a vrátí tajnou hodnotu přímo.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Dejte pozor, abyste tajnou hodnotu nezveřejnili zápisem nebo tiskem.

K dispozici je také verze dávky [get_secrets ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#&preserve-view=trueget-secrets-secrets-) pro přístup k více tajným klíčům najednou.

## <a name="next-steps"></a>Další kroky

 * [Zobrazit ukázkový Poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Přečtěte si o podnikovém zabezpečení pomocí Azure Machine Learning](concept-enterprise-security.md)
