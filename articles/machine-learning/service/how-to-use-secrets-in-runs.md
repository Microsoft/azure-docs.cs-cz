---
title: Používání tajných kódů v školicích běhůch
titleSuffix: Azure Machine Learning
description: Bezpečné předávání tajných klíčů do školicích běhů pomocí Key Vault pracovního prostoru
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/23/2019
ms.custom: seodec18
ms.openlocfilehash: 4872ba8a707192cd61ec371fa982a076d410e918
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996576"
---
# <a name="use-secrets-in-training-runs"></a>Používání tajných kódů v školicích běhůch

V tomto článku se dozvíte, jak bezpečně používat tajné klíče v školicím běhu. Pokud se například chcete připojit k externí databázi pro dotazování školicích dat, budete muset předat uživatelské jméno a heslo do kontextu vzdáleného spuštění. Kódování takových hodnot do školicích skriptů v nešifrovaných textech je nezabezpečené, protože by to vystavilo tajný klíč. 

Místo toho pracovní prostor Azure Machine Learning [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) jako přidružený prostředek. Tento Key Vault se dá použít k zabezpečenému předávání tajných kódů ke vzdálenému spouštění prostřednictvím sady rozhraní API v Azure Machine Learning Python SDK.

Základní tok pro používání tajných klíčů je:
 1. V místním počítači se přihlaste k Azure a připojte se k vašemu pracovnímu prostoru.
 2. V místním počítači nastavte tajný klíč v pracovním prostoru Key Vault
 3. Odešlete vzdálené spuštění.
 4. V rámci vzdáleného spuštění Získejte tajný klíč z hodnoty klíče a použijte ho.

## <a name="set-secrets"></a>Nastavení tajných kódů

V sadě Azure Machine Learning Python SDK obsahuje Třída [trezoru](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) klíčů metody pro nastavení tajných kódů. V místní relaci Pythonu nejprve získejte odkaz na pracovní prostor Key Vault a pak použijte metodu [set_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) k nastavení tajného klíče podle názvu a hodnoty.

```python
from azureml.core import Workspace
import os

ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Neumísťujte tajnou hodnotu v kódu Pythonu, protože je nezabezpečená pro uložení v souboru jako nešifrovaný text. Místo toho Získejte tajnou hodnotu z proměnné prostředí, například tajný klíč sestavení Azure DevOps, nebo z interaktivního uživatelského vstupu.

Pomocí metody [list_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) můžete zobrazit seznam tajných názvů. Metoda __set_secret__ aktualizuje tajnou hodnotu, pokud název již existuje.

## <a name="get-secrets"></a>Získání tajných kódů

V místním kódu můžete použít metodu [trezoru klíčů. Get _secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) k získání tajné hodnoty podle názvu.

V běhu odeslané pomocí metody [experiment. Submit](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-)použijte metodu [Run. Get _secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) . Vzhledem k tomu, že odeslaný běh ví o svém pracovním prostoru, tato metoda vytvoří instanci pracovního prostoru a vrátí tajnou hodnotu přímo.

```python
# Code in submitted run
from azureml.core import Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Dejte pozor, abyste tajnou hodnotu nezveřejnili zápisem nebo tiskem.

Metody set a Get také mají dávkovou verzi [set_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) a [get_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) pro přístup k více tajným klíčům najednou.

## <a name="next-steps"></a>Další kroky

 * [Zobrazit ukázkový Poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Přečtěte si o podnikovém zabezpečení pomocí Azure Machine Learning](concept-enterprise-security.md)
