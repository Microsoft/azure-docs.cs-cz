---
title: Ověřování tajných kódů v školení
titleSuffix: Azure Machine Learning
description: Naučte se, jak předávat tajné kódy do zabezpečených běhů pomocí Azure Key Vault pro váš pracovní prostor.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: b93da1e252357830578783c8f3ab5ca02f5a3e5b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520740"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>Použití tajných kódů přihlašovacích údajů v Azure Machine Learningch školicích běhů


V tomto článku se dozvíte, jak bezpečně používat tajné klíče v školicím běhu. Ověřovací údaje, jako je vaše uživatelské jméno a heslo, jsou tajné. Například pokud se připojíte k externí databázi, abyste mohli zadávat dotazy na školicí data, museli byste předat své uživatelské jméno a heslo do kontextu vzdáleného spuštění. Kódování takových hodnot do školicích skriptů v nešifrovaném textu je nezabezpečené, protože by to vystavilo tajný klíč. 

Místo toho má váš pracovní prostor Azure Machine Learning přidružený prostředek nazvaný [Azure Key Vault](../key-vault/general/overview.md). Pomocí tohoto Key Vault můžete přes sadu rozhraní API v sadě SDK pro Azure Machine Learning Python předat tajným klíčům ke vzdáleným zabezpečeným způsobem.

Standardní tok pro používání tajných klíčů je:
 1. V místním počítači se přihlaste k Azure a připojte se k pracovnímu prostoru.
 2. V místním počítači nastavte tajný klíč v Key Vault pracovního prostoru.
 3. Odešlete vzdálené spuštění.
 4. V rámci vzdáleného spuštění Získejte tajný klíč z Key Vault a použijte ho.

## <a name="set-secrets"></a>Nastavení tajných kódů

V Azure Machine Learning třída [trezoru](/python/api/azureml-core/azureml.core.keyvault.keyvault) klíčů obsahuje metody pro nastavení tajných kódů. V místní relaci Pythonu nejprve získejte odkaz na váš pracovní prostor Key Vault a pak použijte [`set_secret()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#set-secret-name--value-) metodu pro nastavení tajného klíče podle názvu a hodnoty. Metoda __set_secret__ aktualizuje tajnou hodnotu, pokud název již existuje.

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

Pomocí metody můžete vytvořit seznam tajných názvů [`list_secrets()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#list-secrets--) a také dávkovou verzi[set_secrets ()](/python/api/azureml-core/azureml.core.keyvault.keyvault#set-secrets-secrets-batch-) , která umožňuje nastavit více tajných klíčů současně.

## <a name="get-secrets"></a>Získání tajných kódů

V místním kódu můžete pomocí [`get_secret()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#get-secret-name-) metody získat tajnou hodnotu podle názvu.

Pro spuštění [`Experiment.submit`](/python/api/azureml-core/azureml.core.experiment.experiment#submit-config--tags-none----kwargs-)  , použijte [`get_secret()`](/python/api/azureml-core/azureml.core.run.run#get-secret-name-) metodu s [`Run`](/python/api/azureml-core/azureml.core.run%28class%29) třídou. Vzhledem k tomu, že odeslaný běh ví o svém pracovním prostoru, tato metoda vytvoří instanci pracovního prostoru a vrátí tajnou hodnotu přímo.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Dejte pozor, abyste tajnou hodnotu nezveřejnili zápisem nebo tiskem.

K dispozici je také verze dávky [get_secrets ()](/python/api/azureml-core/azureml.core.run.run#get-secrets-secrets-) pro přístup k více tajným klíčům najednou.

## <a name="next-steps"></a>Další kroky

 * [Zobrazit ukázkový Poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Přečtěte si o podnikovém zabezpečení pomocí Azure Machine Learning](concept-enterprise-security.md)