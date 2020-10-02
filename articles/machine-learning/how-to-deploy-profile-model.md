---
title: Paměť modelu profilu a využití CPU
titleSuffix: Azure Machine Learning
description: Naučte se profilovat paměť modelů a využití CPU
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
zone_pivot_groups: aml-control-methods
ms.openlocfilehash: aac5fba68c43892216cbd16dd99b0c6a9bf70217
ms.sourcegitcommit: 487a9f5272300d60df2622c3d13e794d54680f90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91660985"
---
# <a name="profile-your-model-to-determine-resource-utilization"></a>Profilace modelu a určení využití prostředků

V tomto článku se dozvíte, jak profilovat strojové učení s modelem, abyste zjistili, kolik CPU a paměti budete potřebovat pro model při jeho nasazování jako webové služby.

## <a name="prerequisites"></a>Požadované součásti

V tomto článku se předpokládá, že jste provedli školení a zaregistrovali model pomocí Azure Machine Learning. Příklad školení a registrace modelu scikit-učení s Azure Machine Learning najdete v tomto [ukázkovém kurzu](how-to-train-scikit-learn.md) .

## <a name="limitations"></a>Omezení

* Profilace nebude fungovat, když Azure Container Registry (ACR) pro váš pracovní prostor je za virtuální sítí.

## <a name="run-the-profiler"></a>Spuštění profileru

Po zaregistrování modelu a přípravě dalších komponent nezbytných pro příslušné nasazení můžete určit procesor a paměť, které bude nasazená služba potřebovat. Profilace testuje službu, která spouští váš model, a vrací informace, jako je využití CPU, využití paměti a latence odezvy. Poskytuje taky doporučení pro procesor a paměť na základě využití prostředků.

Aby bylo možné profilovat model, budete potřebovat:
* Registrovaný model.
* Odvozená konfigurace založená na vstupním skriptu a definici prostředí pro odvození.
* Tabulková datová sada s jedním sloupcem, kde každý řádek obsahuje řetězec reprezentující ukázková data požadavku.

> [!IMPORTANT]
> V tuto chvíli podporujeme profilaci služeb, které očekávají, že data požadavku jsou řetězcem, například: řetězec serializovaného JSON, text, String serializovaná image atd. Obsah každého řádku datové sady (řetězce) bude vložen do těla požadavku HTTP a odeslán do služby zapouzdřující model pro účely bodování.

> [!IMPORTANT]
> Podporujeme jenom profilaci až 2 procesorů v oblasti ChinaEast2 a USGovArizona.

Níže je uveden příklad, jak můžete vytvořit vstupní datovou sadu pro profilování služby, která očekává, že data příchozího požadavku obsahují serializované JSON. V tomto případě jsme vytvořili instance 100 na základě datové sady, která má stejný obsah dat požadavků. V reálných scénářích doporučujeme používat větší datové sady obsahující různé vstupy, zejména v případě, že využití prostředků modelu/chování je závislé na vstupu.

::: zone pivot="py-sdk"

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

Jakmile budete mít datovou sadu obsahující ukázková data požadavku připravené, vytvořte odvozenou konfiguraci. Odvozená konfigurace je založena na score.py a definici prostředí. Následující příklad ukazuje, jak vytvořit konfiguraci odvození a spustit profilaci:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

::: zone-end

::: zone pivot="cli"


Následující příkaz ukazuje, jak profilovat model pomocí rozhraní příkazového řádku:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> Chcete-li zachovat informace vrácené profilací, použijte pro model značky nebo vlastnosti. Použití značek nebo vlastností ukládá data s modelem v registru modelu. Následující příklady ukazují přidání nové značky obsahující `requestedCpu` `requestedMemoryInGb` informace a:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

::: zone-end

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s neúspěšným nasazením](how-to-troubleshoot-deployment.md)
* [Nasazení do Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Vytváření klientských aplikací pro využívání webových služeb](how-to-consume-web-service.md)
* [Aktualizace webové služby](how-to-deploy-update-web-service.md)
* [Postup nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md)
* [Zabezpečení webové služby prostřednictvím služby Azure Machine Learning s využitím protokolu TLS](how-to-secure-web-service.md)
* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)
* [Vytváření výstrah a triggerů událostí pro nasazení modelů](how-to-use-event-grid.md)

