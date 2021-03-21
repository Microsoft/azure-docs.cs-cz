---
title: Použití studia k nasazení modelů vyškolených v Návrháři
titleSuffix: Azure Machine Learning
description: Pomocí nástroje Azure Machine Learning Studio můžete nasazovat modely strojového učení bez nutnosti psát jediný řádek kódu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.reviewer: peterlu
ms.date: 10/29/2020
ms.topic: conceptual
ms.custom: how-to, deploy, studio, designer
ms.openlocfilehash: 35acfc51ae76fdacef11f03b1fbd91ad58650ae6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97722619"
---
# <a name="use-the-studio-to-deploy-models-trained-in-the-designer"></a>Použití studia k nasazení modelů vyškolených v Návrháři

V tomto článku se dozvíte, jak nasadit model návrháře jako koncový bod v Azure Machine Learning studiu v reálném čase.

Po registraci nebo stažení můžete použít modely trained designeru stejně jako jakýkoliv jiný model. Exportované modely se dají nasadit v případech použití, jako je třeba Internet věcí (IoT) a místní nasazení.

Nasazení v nástroji Studio se skládá z následujících kroků:

1. Zaregistrujte vyškolený model.
1. Stáhněte si vstupní skript a soubor závislostí conda pro model.
1. Volitelné Nakonfigurujte vstupní skript.
1. Nasaďte model do cílového výpočetního prostředí.

Modely můžete nasadit přímo v návrháři, aby bylo možné přeskočit registraci modelu a postup stahování souborů. To může být užitečné pro rychlé nasazení. Další informace najdete v tématu [nasazení modelu pomocí návrháře](tutorial-designer-automobile-price-deploy.md).

Modely, které jsou vyškolené v návrháři, se dají nasadit taky prostřednictvím sady SDK nebo rozhraní příkazového řádku (CLI). Další informace najdete v tématu [nasazení stávajícího modelu pomocí Azure Machine Learning](how-to-deploy-existing-model.md).

## <a name="prerequisites"></a>Předpoklady

* [Pracovní prostor Azure Machine Learning](how-to-manage-workspace.md)

* Dokončený školicí kanál obsahující jeden z následujících modulů:
    - [Modul vlakového modelu](./algorithm-module-reference/train-model.md)
    - [Modul detekce anomálií pro vlaky](./algorithm-module-reference/train-anomaly-detection-model.md)
    - [Modul výukového modelu clusteringu](./algorithm-module-reference/train-clustering-model.md)
    - [Modul výukového modelu Pytorch](./algorithm-module-reference/train-pytorch-model.md)
    - [Modul doporučeného školení pro SVD](./algorithm-module-reference/train-svd-recommender.md)
    - [Modul pro dostupné modelu výuky](./algorithm-module-reference/train-vowpal-wabbit-model.md)
    - [Modul pro rozsáhlou škálu & pro vlak](./algorithm-module-reference/train-wide-and-deep-recommender.md)

## <a name="register-the-model"></a>Registrace modelu

Po dokončení kanálu školení Zaregistrujte vyškolený model do svého pracovního prostoru Azure Machine Learning, abyste měli přístup k modelu v jiných projektech.

1. Vyberte [modul vlakového modelu](./algorithm-module-reference/train-model.md).
1. V pravém podokně vyberte kartu **výstupy + protokoly** .
1. Vyberte snímek ikony **modelu registru** ![ ikony ozubeného kolečka ](./media/how-to-deploy-model-designer/register-model-icon.png) .

    ![Snímek obrazovky se pravým podoknem modulu výuka modelu](./media/how-to-deploy-model-designer/train-model-right-pane.png)

1. Zadejte název modelu a pak vyberte **Uložit**.

Po registraci modelu je můžete najít na stránce Asset **modely** v studiu.
    
![Snímek obrazovky registrovaného modelu na stránce Asset Models](./media/how-to-deploy-model-designer/models-asset-page.png)

## <a name="download-the-entry-script-file-and-conda-dependencies-file"></a>Stažení souboru skriptu vstupu a souboru závislostí conda

K nasazení modelu v Azure Machine Learning Studiu potřebujete následující soubory:

- **Vstupní soubor skriptu** – načte školený model, zpracuje vstupní data z požadavků, provede odvození v reálném čase a vrátí výsledek. Návrhář `score.py` po dokončení modulu **výuka modelu** automaticky vygeneruje vstupní soubor skriptu.

- **Soubor závislostí conda** – určuje, na kterých balíčcích PIP a conda závisí vaše webová služba. Návrhář `conda_env.yaml` po dokončení modulu **výuka modelu** automaticky vytvoří soubor.

Tyto dva soubory si můžete stáhnout v pravém podokně modulu **výuka modelu** :

1. Vyberte modul **vlakového modelu** .
1. Na kartě **výstupy a protokoly** vyberte složku `trained_model_outputs` .
1. Stáhněte `conda_env.yaml` soubor a `score.py` soubor.

    ![Snímek obrazovky se soubory ke stažení pro nasazení v pravém podokně](./media/how-to-deploy-model-designer/download-artifacts-in-right-pane.png)

Případně můžete soubory stáhnout ze stránky **modely** Asset po registraci modelu:

1. Přejděte na stránku Asset **Models** .
1. Vyberte model, který chcete nasadit.
1. Vyberte kartu **artefakty** .
1. Vyberte složku `trained_model_outputs`.
1. Stáhněte `conda_env.yaml` soubor a `score.py` soubor.  

    ![Snímek obrazovky se soubory ke stažení pro nasazení na stránce s podrobnostmi modelu](./media/how-to-deploy-model-designer/download-artifacts-in-models-page.png)

> [!NOTE]
> Tento `score.py` soubor poskytuje téměř stejné funkce jako moduly **modelu skóre** . Některé moduly, jako je třeba [SKORE SVD Doporučené](./algorithm-module-reference/score-svd-recommender.md), [bodové hodnocení a hloubka pro hloubku](./algorithm-module-reference/score-wide-and-deep-recommender.md)a [hodnocení pro dostupné modelů](./algorithm-module-reference/score-vowpal-wabbit-model.md) , ale mají parametry pro různé režimy bodování. Tyto parametry můžete také změnit ve skriptu pro zadávání.
>
>Další informace o nastavení parametrů v `score.py` souboru najdete v části [Konfigurace vstupního skriptu](#configure-the-entry-script).

## <a name="deploy-the-model"></a>Nasazení modelu

Po stažení nezbytných souborů jste připraveni model nasadit.

1. Na stránce Asset **modelů** vyberte registrovaný model.
1. Vyberte tlačítko **nasadit** .
1. V nabídce konfigurace zadejte následující informace:

    - Zadejte název koncového bodu.
    - Tuto možnost vyberte, pokud chcete model nasadit do [služby Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md) nebo [Azure Container instance](how-to-deploy-azure-container-instance.md).
    - Nahrajte `score.py` soubor pro **vstupní skript**.
    - Nahrajte `conda_env.yml` soubor pro **conda závislosti**. 

    >[!TIP]
    > V **rozšířeném** nastavení můžete nastavit kapacitu procesoru/paměti a další parametry pro nasazení. Tato nastavení jsou důležitá pro určité modely, jako jsou například modely PyTorch, které využívají značné množství memery (přibližně 4 GB).

1. Vyberte **nasadit** a model nasaďte jako koncový bod v reálném čase.

    ![Snímek obrazovky modelu nasazení na stránce assetu modelu](./media/how-to-deploy-model-designer/deploy-model.png)

## <a name="consume-the-real-time-endpoint"></a>Využití koncového bodu v reálném čase

Po úspěšném nasazení můžete koncový bod v reálném čase najít na stránce prostředků **koncových bodů** . Jakmile tam budete, najdete koncový bod REST, který můžou klienti použít k odesílání požadavků do koncového bodu v reálném čase. 

> [!NOTE]
> Návrhář také generuje soubor JSON s ukázkovými daty pro testování, který můžete stáhnout `_samples.json` do složky **trained_model_outputs** .

Použijte následující ukázku kódu pro využití koncového bodu v reálném čase.

```python

import json
from pathlib import Path
from azureml.core.workspace import Workspace, Webservice
 
service_name = 'YOUR_SERVICE_NAME'
ws = Workspace.get(
    name='WORKSPACE_NAME',
    subscription_id='SUBSCRIPTION_ID',
    resource_group='RESOURCEGROUP_NAME'
)
service = Webservice(ws, service_name)
sample_file_path = '_samples.json'
 
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)
score_result = service.run(json.dumps(sample_data))
print(f'Inference result = {score_result}')
```

### <a name="consume-computer-vision-related-real-time-endpoints"></a>Využívání koncových bodů souvisejících s počítačem v reálném čase

Při využívání koncových bodů v reálném čase, je nutné převést obrázky na bajty, protože webová služba přijímá jako vstup pouze řetězec. Následuje ukázkový kód:

```python
import base64
import json
from copy import deepcopy
from pathlib import Path
from azureml.studio.core.io.image_directory import (IMG_EXTS, image_from_file, image_to_bytes)
from azureml.studio.core.io.transformation_directory import ImageTransformationDirectory

# image path
image_path = Path('YOUR_IMAGE_FILE_PATH')

# provide the same parameter setting as in the training pipeline. Just an example here.
image_transform = [
    # format: (op, args). {} means using default parameter values of torchvision.transforms.
    # See https://pytorch.org/docs/stable/torchvision/transforms.html
    ('Resize', 256),
    ('CenterCrop', 224),
    # ('Pad', 0),
    # ('ColorJitter', {}),
    # ('Grayscale', {}),
    # ('RandomResizedCrop', 256),
    # ('RandomCrop', 224),
    # ('RandomHorizontalFlip', {}),
    # ('RandomVerticalFlip', {}),
    # ('RandomRotation', 0),
    # ('RandomAffine', 0),
    # ('RandomGrayscale', {}),
    # ('RandomPerspective', {}),
]
transform = ImageTransformationDirectory.create(transforms=image_transform).torch_transform

# download _samples.json file under Outputs+logs tab in the right pane of Train Pytorch Model module
sample_file_path = '_samples.json'
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)

# use first sample item as the default value
default_data = sample_data[0]
data_list = []
for p in image_path.iterdir():
    if p.suffix.lower() in IMG_EXTS:
        data = deepcopy(default_data)
        # convert image to bytes
        data['image'] = base64.b64encode(image_to_bytes(transform(image_from_file(p)))).decode()
        data_list.append(data)

# use data.json as input of consuming the endpoint
data_file_path = 'data.json'
with open(data_file_path, 'w') as f:
    json.dump(data_list, f)
```

## <a name="configure-the-entry-script"></a>Konfigurace vstupního skriptu

Některé moduly v návrháři, jako je skóre SVD, jako je například [doporučení doporučeného](./algorithm-module-reference/score-svd-recommender.md) [a hloubkového hodnocení](./algorithm-module-reference/score-wide-and-deep-recommender.md)na vysokém rozsahu a doporučený [model pro dostupné](./algorithm-module-reference/score-vowpal-wabbit-model.md) , mají parametry pro různé režimy bodování. 

V této části se dozvíte, jak tyto parametry aktualizovat také v souboru s vstupním skriptem.

Následující příklad aktualizuje výchozí chování pro vyškolený model **doporučený & hluboko** . Ve výchozím nastavení `score.py` soubor oznamuje webové službě předpověď hodnocení mezi uživateli a položkami. 

Můžete upravit soubor skriptu pro zadávání, aby provedl doporučení položek, a vracet Doporučené položky tak, že změníte `recommender_prediction_kind` parametr.


```python
import os
import json
from pathlib import Path
from collections import defaultdict
from azureml.studio.core.io.model_directory import ModelDirectory
from azureml.designer.modules.recommendation.dnn.wide_and_deep.score. \
    score_wide_and_deep_recommender import ScoreWideAndDeepRecommenderModule
from azureml.designer.serving.dagengine.utils import decode_nan
from azureml.designer.serving.dagengine.converter import create_dfd_from_dict

model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'trained_model_outputs')
schema_file_path = Path(model_path) / '_schema.json'
with open(schema_file_path) as fp:
    schema_data = json.load(fp)


def init():
    global model
    model = ModelDirectory.load(load_from_dir=model_path)


def run(data):
    data = json.loads(data)
    input_entry = defaultdict(list)
    for row in data:
        for key, val in row.items():
            input_entry[key].append(decode_nan(val))

    data_frame_directory = create_dfd_from_dict(input_entry, schema_data)

    # The parameter names can be inferred from Score Wide and Deep Recommender module parameters:
    # convert the letters to lower cases and replace whitespaces to underscores.
    score_params = dict(
        trained_wide_and_deep_recommendation_model=model,
        dataset_to_score=data_frame_directory,
        training_data=None,
        user_features=None,
        item_features=None,
        ################### Note #################
        # Set 'Recommender prediction kind' parameter to enable item recommendation model
        recommender_prediction_kind='Item Recommendation',
        recommended_item_selection='From All Items',
        maximum_number_of_items_to_recommend_to_a_user=5,
        whether_to_return_the_predicted_ratings_of_the_items_along_with_the_labels='True')
    result_dfd, = ScoreWideAndDeepRecommenderModule().run(**score_params)
    result_df = result_dfd.data
    return json.dumps(result_df.to_dict("list"))
```

Pro zajištění **široké &** **Proch dostupné** modelů a modelů můžete nakonfigurovat parametr režimu bodování pomocí následujících metod:

- Názvy parametrů jsou kombinace malých a podtržítekových názvů parametrů pro [skore pro dostupné model](./algorithm-module-reference/score-vowpal-wabbit-model.md) a [bodového hodnocení široké a hloubkové](./algorithm-module-reference/score-wide-and-deep-recommender.md).
- Hodnoty parametrů typu režimu jsou řetězcem odpovídajících názvů možností. Ve výše uvedených kódech můžete jako příklad použít **druh předpovědi Doporučené** , hodnota může být `'Rating Prediction'` nebo `'Item Recommendation'` . Jiné hodnoty nejsou povoleny.

Pro **SVD doporučený** model je možné, že názvy parametrů a hodnoty jsou méně zřejmé a můžete vyhledat následující tabulky a rozhodnout se, jak nastavit parametry.

| Název parametru ve [SKORE SVD – doporučení](./algorithm-module-reference/score-svd-recommender.md)                           | Název parametru v souboru skriptu vstupu |
| ------------------------------------------------------------ | --------------------------------------- |
| Druh předpovědi doporučit                                  | prediction_kind                         |
| Výběr Doporučené položky                                   | recommended_item_selection              |
| Minimální velikost fondu doporučení pro jednoho uživatele    | min_recommendation_pool_size            |
| Maximální počet položek, které je potřeba doporučit pro uživatele               | max_recommended_item_count              |
| Zda se mají vracet předpovězené hodnocení položek spolu s popisky | return_ratings                          |

Následující kód ukazuje, jak nastavit parametry pro doporučení SVD, které pomocí všech šesti parametrů doporučí hodnocené položky s připojenými předpovězenými hodnoceními.

```python
score_params = dict(
        learner=model,
        test_data=DataTable.from_dfd(data_frame_directory),
        training_data=None,
        # RecommenderPredictionKind has 2 members, 'RatingPrediction' and 'ItemRecommendation'. You
        # can specify prediction_kind parameter with one of them.
        prediction_kind=RecommenderPredictionKind.ItemRecommendation,
        # RecommendedItemSelection has 3 members, 'FromAllItems', 'FromRatedItems', 'FromUndatedItems'.
        # You can specify recommended_item_selection parameter with one of them.
        recommended_item_selection=RecommendedItemSelection.FromRatedItems,
        min_recommendation_pool_size=1,
        max_recommended_item_count=3,
        return_ratings=True,
    )
```


## <a name="next-steps"></a>Další kroky

* [Výuka modelu v Návrháři](tutorial-designer-automobile-price-train-score.md)
* [Nasazení modelů pomocí sady Azure Machine Learning SDK](how-to-deploy-and-where.md)
* [Řešení potíží s neúspěšným nasazením](how-to-troubleshoot-deployment.md)
* [Nasazení do Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Vytváření klientských aplikací pro využívání webových služeb](how-to-consume-web-service.md)
* [Aktualizace webové služby](how-to-deploy-update-web-service.md)
