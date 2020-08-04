---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 2b4f768b25917e712380ca4a7f8ac58cb6140777
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542766"
---
Vstupní skript přijímá data odeslaná do nasazené webové služby a předává je do modelu. Potom vezme odpověď vrácenou modelem a vrátí ji klientovi. *Skript je specifický pro váš model*. Musí pochopit data, která model očekává a vrátí.

Skript obsahuje dvě funkce, které načítají a spouštějí model:

* `init()`: Tato funkce obvykle načte model do globálního objektu. Tato funkce se spustí jenom jednou, když se spustí kontejner Docker pro vaši webovou službu.

* `run(input_data)`: Tato funkce používá model k předpovědi hodnoty založené na vstupních datech. Vstupy a výstupy spuštění obvykle pro serializaci a deserializaci používají JSON. Pracovat můžete také s nezpracovanými binárními daty. Data můžete transformovat před jejich odesláním do modelu nebo před jejich vrácením klientovi.

REST API očekává, že tělo požadavku bude dokument JSON s následující strukturou:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

Následující příklad ukazuje, jak načíst registrovaný model scikit-učení a určit jeho skóre pomocí numpy dat:

```python
#Example: scikit-learn and Swagger
import json
import numpy as np
import os
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment. Join this path with the filename of the model file.
    # It holds the path to the directory that contains the deployed model (./azureml-models/$MODEL_NAME/$VERSION).
    # If there are multiple models, this value is the path to the directory containing all deployed models (./azureml-models).
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')

    # If your model were stored in the same directory as your score.py, you could also use the following:
    # model_path = os.path.abspath(os.path.join(os.path.dirname(__file_), 'sklearn_mnist_model.pkl')

    # Deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Další příklady najdete v následujících skriptech:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* [Binární data](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#binary-data)