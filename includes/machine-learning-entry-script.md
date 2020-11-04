---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 95acb028867caf6f497dd99ad3082efcaab09c7b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325277"
---
Vstupní skript přijímá data odeslaná do nasazené webové služby a předává je do modelu. Potom vezme odpověď vrácenou modelem a vrátí ji klientovi. *Skript je specifický pro váš model*. Musí pochopit data, která model očekává a vrátí.

Následující dvě věci, které potřebujete k tomu, abyste dosáhli svého vstupního skriptu:

1. Načítají se model (pomocí funkce s názvem `init()` ).
1. Spuštění modelu na vstupních datech (pomocí funkce nazvané `run()` )

Pojďme si projít tyto kroky podrobněji.

### <a name="writing-init"></a>Zápis init () 

#### <a name="loading-a-registered-model"></a>Načítání registrovaného modelu

Vaše registrované modely jsou uloženy v cestě, na kterou odkazuje proměnná prostředí s názvem `AZUREML_MODEL_DIR` . Další informace o přesné adresářové struktuře najdete v tématu [vyhledání souborů modelu ve vstupním skriptu](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models) .

#### <a name="loading-a-local-model"></a>Načítá se místní model.

Pokud jste se rozhodli, že jste model zaregistrovali a úspěšně jste načetli model jako součást zdrojového adresáře, můžete si ho přečíst v takovém případě, že jste cestou k modelu přihlásili relativně ke skriptu bodování. Například pokud byste měli adresář strukturovaný jako:

```bash

- source_dir
    - score.py
    - models
        - model1.onnx

```

své modely můžete načíst pomocí následujícího kódu Pythonu:

```python
import os

model = open(os.path.join('.', 'models', 'model1.onnx'))
```

### <a name="writing-run"></a>Zápis Run ()

`run()` se spustí pokaždé, když model obdrží požadavek bodování a očekává, že tělo požadavku bude dokument JSON s následující strukturou:

```json
{
    "data": <model-specific-data-structure>
}

```

Vstupem do `run()` je řetězec Pythonu, který obsahuje libovolný klíč "data".

Následující příklad ukazuje, jak načíst registrovaný model scikit-učení a určit jeho skóre pomocí numpy dat:

```python
import json
import numpy as np
import os
from sklearn.externals import joblib


def init():
    global model
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(data):
    try:
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Pro pokročilejší příklady, včetně automatického generování schémat Swagger a binárních dat (tj. obrázků) si přečtěte [článek o vytváření dalších vstupních skriptů](../articles/machine-learning/how-to-deploy-advanced-entry-script.md) .