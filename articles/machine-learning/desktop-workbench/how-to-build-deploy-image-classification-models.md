---
title: Sestavit a nasadit model klasifikace obrázků pomocí balíčku Azure Machine Learning pro počítačové zpracování obrazu.
description: Zjistěte, jak sestavit, trénování, testování a nasazování modelem počítačového zpracování obrazu image klasifikace pomocí balíčku Azure Machine Learning pro počítačové zpracování obrazu.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ROBOTS: NOINDEX
ms.openlocfilehash: ab9e04b8a9eb4290891b3c7d55f8262c4ba64d44
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52584042"
---
# <a name="build-and-deploy-image-classification-models-with-azure-machine-learning"></a>Sestavování a nasazování modelů klasifikace obrázků s Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

V tomto článku najdete další informace o použití Azure Machine Learning balíčku pro zpracování obrazu počítače (AMLPCV) pro trénování, testování a nasazení modelu klasifikace obrázků. Přehledné informace o tento balíček a jeho podrobnou referenční dokumentaci [tady uvidíte](https://aka.ms/aml-packages/vision).

Velký počet problémů v doméně počítače pro zpracování obrazu můžete vyřešit pomocí klasifikace obrázků. Tyto problémy patří vytváření modelů, které odpovídají otázky jako:
+ _OBJEKT je k dispozici v bitové kopii? Například "pes", "auto", "dodání" a tak dále_
+ _Jaké třídy závažnost stádiu oka je evinced podle této pacienta retinal kontrolu?_

Při vytváření a nasazování tento model s AMLPCV, můžete přejít pomocí následujících kroků:
1. Vytvoření datové sady
2. Obrázek vizualizace a poznámky
3. Image rozšíření
4. Definice modelu hluboké Neuronové sítě (DNN)
5. Třídění školení
6. Vyhodnocení a vizualizace
7. Nasazení webové služby
8. Webová služba zátěžové testování

[CNTK](https://docs.microsoft.com/cognitive-toolkit/) slouží jako rozhraní hloubkového učení školení se provádí místně na počítači s využitím GPU, jako ([hloubkového učení virtuální počítač pro datové vědy](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)), a nasazení pomocí rozhraní příkazového řádku Azure ML Operacionalizace.

## <a name="prerequisites"></a>Požadavky

1. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

1. Následující účty a aplikace musí mít nastavení a instalaci:
   - Účet Experimentování ve službě Azure Machine Learning 
   - Účet správy modelů Azure Machine Learning
   - Nainstalovanou aplikaci Azure Machine Learning Workbench

   Pokud tyto tři jsou ještě vytvořen nebo nainstalován, postupujte [Quickstart pro Azure Machine Learning a Workbench instalace](../desktop-workbench/quickstart-installation.md) článku. 

1. Musíte nainstalovat balíček Azure Machine Learning pro počítačové zpracování obrazu. Zjistěte, jak [instalaci tohoto balíčku zde](https://aka.ms/aml-packages/vision).

## <a name="sample-data-and-notebook"></a>Ukázková data a poznámkového bloku

### <a name="get-the-jupyter-notebook"></a>Získání poznámkového bloku Jupyter

Stáhnout ho na spuštění ukázky popsané sami.

> [!div class="nextstepaction"]
> [Získání poznámkového bloku Jupyter](https://aka.ms/aml-packages/vision/notebooks/image_classification)

### <a name="load-the-sample-data"></a>Načtení ukázkových dat.

Následující příklad používá datovou sadu skládající se z 63 stolní imagí. Každé image je označen jako patřící do jednoho z čtyři různé třídy (bowl, cup, příbory, talíře). Počet obrázků v tomto příkladu je malá tak, aby tato ukázka je možné provést rychle. V praxi musí být zadána nejméně 100 obrázků na třídy. Všechny bitové kopie jsou umístěny na *".. /sample_data/imgs_recycling / "*, v podadresářích nazývá"bowl","cupu","příbory"a"štítku".

![Datová sada Azure Machine Learning](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)


```python
import warnings
warnings.filterwarnings("ignore")
import json, numpy as np, os, timeit 
from azureml.logging import get_azureml_logger
from imgaug import augmenters
from IPython.display import display
from sklearn import svm
from cvtk import ClassificationDataset, CNTKTLModel, Context, Splitter, StorageContext
from cvtk.augmentation import augment_dataset
from cvtk.core.classifier import ScikitClassifier
from cvtk.evaluation import ClassificationEvaluation, graph_roc_curve, graph_pr_curve, graph_confusion_matrix
import matplotlib.pyplot as plt

from classification.notebook.ui_utils.ui_annotation import AnnotationUI
from classification.notebook.ui_utils.ui_results_viewer import ResultsUI
from classification.notebook.ui_utils.ui_precision_recall import PrecisionRecallUI

%matplotlib inline

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)
```



## <a name="create-a-dataset"></a>Vytvoření datové sady

Po importu závislosti a nastavit kontext úložiště, můžete vytvořit objekt dataset.

Chcete-li vytvořit tento objekt pomocí Azure Machine Learning balíčku pro počítačové zpracování obrazu, zadejte kořenový adresář bitové kopie na místním disku. Tento adresář musí dodržet obecnou strukturu danou stejnou datovou sadou stolní, to znamená, obsahují podadresáře s skutečné imagí:
- Kořenové
    - Popisek 1
    - Popisek 2
    - ...
    - Popisek n
  
Trénování modelu klasifikace obrázků pro různé datové sady je stejně snadné jako změna kořenové cestě `dataset_location` v následujícím kódu tak, aby odkazovala na jinou Image.


```python
# Root image directory
dataset_location = os.path.abspath("classification/sample_data/imgs_recycling")

dataset_name = 'recycling'
dataset = ClassificationDataset.create_from_dir(dataset_name, dataset_location)
print("Dataset consists of {} images with {} labels.".format(len(dataset.images), len(dataset.labels)))
print("Select information for image 2: name={}, label={}, unique id={}.".format(
    dataset.images[2].name, dataset.images[2]._labels[0].name, dataset.images[2]._storage_id))
```

    F1 2018-04-23 17:12:57,593 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-04-23 17:12:57,599 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Dataset consists of 63 images with 4 labels.
    Select information for image 2: name=msft-plastic-bowl20170725152154282.jpg, label=bowl, unique id=3.

Objekt dataset poskytuje funkce ke stažení imagí pomocí [API pro vyhledávání obrázků Bingu](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/). 

Dva druhy vyhledávacích dotazů jsou podporovány: 
+ Běžný textový dotazy
+ Dotazy na adresu URL obrázku

Tyto dotazy společně s popisek třídy je třeba zadat uvnitř zakódovaná ve formátu JSON textového souboru. Příklad:

```json
{
    "bowl": [
                    "plastic bowl",
                    "../imgs_recycling/bowl"
    ],
    "cup": [
                    "plastic cup",
                    "../imgs_recycling/cup",
                    "http://cdnimg2.webstaurantstore.com/images/products/main/123662/268841/dart-solo-ultra-clear-conex-tp12-12-oz-pet-plastic-cold-cup-1000-case.jpg"
    ],
    "cutlery": [
                    "plastic cutlery",
                    "../imgs_recycling/cutlery",
                    "http://img4.foodservicewarehouse.com/Prd/1900SQ/Fineline_2514-BO.jpg"
    ],
    "plate": [
                    "plastic plate",
                    "../imgs_recycling/plate"
    ]
}
```

Kromě toho musíte explicitně vytvořit objekt kontextu obsahovat klíč rozhraní API pro vyhledávání obrázků Bingu. To vyžaduje předplatné API Bingu pro vyhledávání obrázků.

## <a name="visualize-and-annotate-images"></a>Vizualizace a imagí opatřit poznámkami

Můžete vizualizovat imagí a správné popisky v objektu dataset pomocí následující widgetu. 

Pokud narazíte na chybu "Widget Javascript nenajde", spusťte tento příkaz k jeho řešení:
<br>`jupyter nbextension enable --py --sys-prefix widgetsnbextension`


```python
annotation_ui = AnnotationUI(dataset, Context.get_global_context())
display(annotation_ui.ui)
```

![Datová sada Azure Machine Learning](media/how-to-build-deploy-image-classification-models/image_annotation.png)

## <a name="augment-images"></a>Posílení imagí

[ `augmentation` Modulu](/python/api/cvtk/cvtk.augmentation.augmentation) poskytuje funkce, které rozšiřují pomocí všechny transformace je popsáno v objektu dataset [imgaug](https://github.com/aleju/imgaug) knihovny. Transformace bitové kopie mohou být seskupeny do jednoho kanálu, v takovém případě jsou všechny transformace v kanálu použít současně každé image. 

Pokud chcete použít jiné rozšíření samostatně nebo v různých způsobem, můžete definovat více kanálů a předat je do *augment_dataset* funkce. Další informace a příklady rozšíření image, najdete v článku [imgaug dokumentaci](https://github.com/aleju/imgaug).

Přidání rozšířená obrázků na cvičnou sadou je obzvláště užitečné pro malé datové sady. Protože DNN školení proces je pomalejší z důvodu zvýšeného počtu trénovacích obrázků, doporučujeme, abyste že spuštění služby experimentování ve službě bez rozšíření.


```python
# Split the dataset into train and test  
train_set_orig, test_set = dataset.split(train_size = 0.66, stratify = "label")
print("Number of training images = {}, test images = {}.".format(train_set_orig.size(), test_set.size()))
```

    F1 2018-04-23 17:13:01,780 INFO azureml.vision:splitter splitting a dataset 
    F1 2018-04-23 17:13:01,805 INFO azureml.vision:dataset creating dataset for scenario=classification 
    F1 2018-04-23 17:13:01,809 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Number of training images = 41, test images = 22.
    


```python
augment_train_set = False

if augment_train_set:
    aug_sequence = augmenters.Sequential([
            augmenters.Fliplr(0.5),             # horizontally flip 50% of all images
            augmenters.Crop(percent=(0, 0.1)),  # crop images by 0-10% of their height/width
        ])
    train_set = augment_dataset(train_set_orig, [aug_sequence])
    print("Number of original training images = {}, with augmented images included = {}.".format(train_set_orig.size(), train_set.size()))
else:
    train_set = train_set_orig  
```

## <a name="define-dnn-models"></a>Definovat modely DNN

S tímto balíčkem jsou podporovány následující modely pretrained hluboké Neuronové sítě: 
+ Modelem Resnet-18
+ Modelem Resnet-34
+ Modelem Resnet-50
+ Modelem Resnet-101
+ Modelem Resnet-152

Tyto dopředné slouží jako třídění, nebo jako featurizer. 

Další informace o sítích najdete [tady](https://github.com/Microsoft/CNTK/blob/master/PretrainedModels/Image.md), a je základní informace o přenosu Learning [tady](https://blog.slavv.com/a-gentle-intro-to-transfer-learning-2c0b674375a0).

Výchozí parametry klasifikace obrázků pro tento balíček se rozlišení 224 x 224 pixelů a modelem Resnet-18 DNN. Tyto parametry byly vybrány pro práci i na širokou škálu úloh. Přesnost lze často vylepšit, například zvýšení rozlišení obrázku na 500 x 500 pixelů a/nebo výběrem hlubší modelu (modelem Resnet-50). Ale změna parametrů může nacházet pouze na velký nárůst čas školení. Přečtěte si článek na [jak zlepšit přesnost](https://docs.microsoft.com/azure/machine-learning/service/how-to-improve-accuracy-for-computer-vision-models).


```python
# Default parameters (224 x 224 pixels resolution, Resnet-18)
lr_per_mb = [0.05]*7 + [0.005]*7 +  [0.0005]
mb_size = 32
input_resoluton = 224
base_model_name = "ResNet18_ImageNet_CNTK"

# Suggested parameters for 500 x 500 pixels resolution, Resnet-50
# (see in the Appendix "How to improve accuracy", last row in table)
# lr_per_mb   = [0.01] * 7 + [0.001] * 7 + [0.0001]
# mb_size    = 8
# input_resoluton = 500
# base_model_name = "ResNet50_ImageNet_CNTK"

# Initialize model
dnn_model = CNTKTLModel(train_set.labels,
                       base_model_name=base_model_name,
                       image_dims = (3, input_resoluton, input_resoluton))
```

    Successfully downloaded ResNet18_ImageNet_CNTK
    

## <a name="train-the-classifier"></a>Trénování klasifikátoru

Můžete vybrat jednu z následujících metod pro předem vytrénovaných DNN.

  - **Vylepšení DNN**, který trénovat DNN ke klasifikaci přímo. DNN školení je pomalá, je obvykle vede k nejlepších výsledků protože všechny sítě vah se může zlepšit během cvičení poskytnout největší přesností.

  - **Snadné DNN**, která se spouští DNN jako-je použít k získání nižší rozměrné reprezentace bitovou kopii (512 a 2048, 4096 čísel s plovoucí čárkou). Tohoto vyjádření se pak použije jako vstup pro trénování zvláštní třídění. Vzhledem k tomu, DNN zůstane beze změny, tento přístup je mnohem rychleji ve srovnání s DNN vylepšení, ale není tak dobré přesnost. Však školení externí třídění například lineární SVM (jak je znázorněno v následujícím kódu) můžete poskytnout silné standardní hodnoty a usnadní pochopení proveditelnosti problém.
  
TensorBoard umožňuje znázornit průběh školení. Chcete-li aktivovat TensorBoard:
1. Přidat parametr `tensorboard_logdir=PATH` jak je znázorněno v následujícím kódu
1. Spuštění klienta TensorBoard pomocí příkazu `tensorboard --logdir=PATH` v nové konzole.
1. Podle pokynů TensorBoard, která ve výchozím nastavení je localhost:6006, otevřete webový prohlížeč. 


```python
# Train either the DNN or a SVM as classifier 
classifier_name = "dnn"

if classifier_name.lower() == "dnn":  
    dnn_model.train(train_set, lr_per_mb = lr_per_mb, mb_size = mb_size, eval_dataset=test_set) #, tensorboard_logdir=r"tensorboard"
    classifier = dnn_model
elif classifier_name.lower() == "svm":
    learner = svm.LinearSVC(C=1.0, class_weight='balanced', verbose=0)
    classifier = ScikitClassifier(dnn_model, learner = learner)
    classifier.train(train_set)
else:
    raise Exception("Classifier unknown: " + classifier)   
```

    F1 2018-04-23 17:13:28,238 INFO azureml.vision:Fit starting in experiment  1541466320 
    F1 2018-04-23 17:13:28,239 INFO azureml.vision:model starting training for scenario=classification 
    <class 'int'>
    1 worker
    Training transfer learning model for 15 epochs (epoch_size = 41).
    non-distributed mode
    Training 15741700 parameters in 53 parameter tensors.
    Training 15741700 parameters in 53 parameter tensors.
    Learning rate per minibatch: 0.05
    Momentum per minibatch: 0.9
    PROGRESS: 0.00%
    Finished Epoch[1 of 15]: [Training] loss = 2.820586 * 41, metric = 68.29% * 41 5.738s (  7.1 samples/s);
    Evaluation Set Error :: 29.27%
    Finished Epoch[2 of 15]: [Training] loss = 0.286728 * 41, metric = 9.76% * 41 0.752s ( 54.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[3 of 15]: [Training] loss = 0.206938 * 41, metric = 4.88% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 41.46%
    Finished Epoch[4 of 15]: [Training] loss = 0.098931 * 41, metric = 2.44% * 41 0.785s ( 52.2 samples/s);
    Evaluation Set Error :: 48.78%
    Finished Epoch[5 of 15]: [Training] loss = 0.046547 * 41, metric = 0.00% * 41 0.724s ( 56.6 samples/s);
    Evaluation Set Error :: 43.90%
    Finished Epoch[6 of 15]: [Training] loss = 0.059709 * 41, metric = 4.88% * 41 0.636s ( 64.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[7 of 15]: [Training] loss = 0.005817 * 41, metric = 0.00% * 41 0.710s ( 57.7 samples/s);
    Evaluation Set Error :: 14.63%
    Learning rate per minibatch: 0.005
    Finished Epoch[8 of 15]: [Training] loss = 0.014917 * 41, metric = 0.00% * 41 0.649s ( 63.2 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[9 of 15]: [Training] loss = 0.040539 * 41, metric = 2.44% * 41 0.777s ( 52.8 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[10 of 15]: [Training] loss = 0.024606 * 41, metric = 0.00% * 41 0.626s ( 65.5 samples/s);
    Evaluation Set Error :: 7.32%
    PROGRESS: 0.00%
    Finished Epoch[11 of 15]: [Training] loss = 0.004225 * 41, metric = 0.00% * 41 0.656s ( 62.5 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[12 of 15]: [Training] loss = 0.004364 * 41, metric = 0.00% * 41 0.702s ( 58.4 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[13 of 15]: [Training] loss = 0.007974 * 41, metric = 0.00% * 41 0.721s ( 56.9 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[14 of 15]: [Training] loss = 0.000655 * 41, metric = 0.00% * 41 0.711s ( 57.7 samples/s);
    Evaluation Set Error :: 4.88%
    Learning rate per minibatch: 0.0005
    Finished Epoch[15 of 15]: [Training] loss = 0.024865 * 41, metric = 0.00% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 4.88%
    Stored trained model at ../../../cvtk_output\model_trained\ImageClassification.model
    F1 2018-04-23 17:13:45,097 INFO azureml.vision:Fit finished in experiment  1541466320 
    


```python
# Plot how the training and test accuracy increases during gradient descent. 
if classifier_name == "dnn":
    [train_accs, test_accs, epoch_numbers] = classifier.train_eval_accs
    plt.xlabel("Number of training epochs") 
    plt.ylabel("Classification accuracy") 
    train_plot = plt.plot(epoch_numbers, train_accs, 'r-', label = "Training accuracy")
    test_plot = plt.plot(epoch_numbers, test_accs, 'b-.', label = "Test accuracy")
    plt.legend()
```

![PNG](media/how-to-build-deploy-image-classification-models/output_17_0.png)


## <a name="evaluate-and-visualize-model-performance"></a>Vyhodnocení a Vizualizujte výkon modelů

Můžete si vyzkoušet výkonu pro datovou sadu nezávislé testu pomocí modulu vyhodnocování trénovaného modelu. Metriky hodnocení, který vypočítává patří:
 
+ Přesnost (ve výchozím nastavení byla třída)
+ Žádost o přijetí změn křivky
+ Křivka roc s více TŘÍDAMI
+ Oblasti v rámci křivky
+ Chybová matice


```python
# Run the classifier on all test set images
ce = ClassificationEvaluation(classifier, test_set, minibatch_size = mb_size)

# Compute Accuracy and the confusion matrix
acc = ce.compute_accuracy()
print("Accuracy = {:2.2f}%".format(100*acc))
cm  = ce.compute_confusion_matrix()
print("Confusion matrix = \n{}".format(cm))

# Show PR curve, ROC curve, and confusion matrix
fig, ((ax1, ax2, ax3)) = plt.subplots(1,3)
fig.set_size_inches(18, 4)
graph_roc_curve(ce, ax=ax1)
graph_pr_curve(ce, ax=ax2)
graph_confusion_matrix(ce, ax=ax3)
plt.show()
```

    F1 2018-04-23 17:14:37,449 INFO azureml.vision:evaluation doing evaluation for scenario=classification 
    F1 2018-04-23 17:14:37,450 INFO azureml.vision:model scoring dataset for scenario=classification 
    Accuracy = 95.45%
    Confusion matrix = 
    [[ 0  1  0  1]
     [ 0  7  0  0]
     [ 0  0  2  0]
     [ 0  0  0 11]]
    


![PNG](media/how-to-build-deploy-image-classification-models/output_20_1.png)



```python
# Results viewer UI
labels = [l.name for l in dataset.labels] 
pred_scores = ce.scores #classification scores for all images and all classes
pred_labels = [labels[i] for i in np.argmax(pred_scores, axis=1)]

results_ui = ResultsUI(test_set, Context.get_global_context(), pred_scores, pred_labels)
display(results_ui.ui)
```

![Datová sada Azure Machine Learning](media/how-to-build-deploy-image-classification-models/Image_Classification_Results.png)


```python
# Precision / recall curve UI
precisions, recalls, thresholds = ce.compute_precision_recall_curve() 
thresholds = list(thresholds)
thresholds.append(thresholds[-1])
pr_ui = PrecisionRecallUI(100*precisions[::-1], 100*recalls[::-1], thresholds[::-1])
display(pr_ui.ui) 
```

![Datová sada Azure Machine Learning](media/how-to-build-deploy-image-classification-models/image_precision_curve.png)

## <a name="operationalization-deploy-and-consume"></a>Operacionalizace: nasazení a využití

Operacionalizace je proces publikování kódu jako webové služby a modely a využití těchto služeb obchodní výsledky. 

Jakmile váš model se trénuje, můžete nasadit tento model jako webovou službu pro spotřebu pomocí [CLI aplikace Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning). Je možné nasadit modely do místního počítače nebo clusteru Azure Container Service (ACS). Pomocí služby ACS, můžete ruční škálování webové služby nebo pomocí funkce automatického škálování.

**Přihlaste se pomocí Azure CLI**

Pomocí [Azure](https://azure.microsoft.com/) účet s platným předplatným, přihlaste se pomocí následujícího příkazu rozhraní příkazového řádku:
<br>`az login`

+ Pokud chcete přepnout do jiného předplatného Azure, použijte příkaz:
<br>`az account set --subscription [your subscription name]`

+ Pokud chcete zobrazit aktuální účet správy modelů, použijte příkaz:
  <br>`az ml account modelmanagement show`

**Vytvoření a nastavení prostředí pro nasazení clusteru**

Stačí nastavit prostředí pro nasazení jednou. Pokud nemáte ještě, nastavte si nyní pomocí prostředí pro nasazení [tyto pokyny](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup). 

Pokud chcete zobrazit aktivní nasazení prostředí, použijte následující příkaz rozhraní příkazového řádku:
<br>`az ml env show`
   
Ukázky rozhraní příkazového řádku Azure k vytvoření a nastavení prostředí nasazení

```CLI
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. westcentralus] [-g [resource group]]
az ml env set -n [environment name] -g [resource group]
az ml env cluster
```
    
### <a name="manage-web-services-and-deployments"></a>Správa webových služeb a nasazení

Následující rozhraní API slouží k nasazení modelů jako webové služby, spravovat tyto webové služby a správu nasazení.

|Úkol|Rozhraní API|
|----|----|
|Vytvoření objektu nasazení|`deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model, aml_env="cluster")`
|Nasazení webové služby|`deploy_obj.deploy()`|
|Obrázek skóre|`deploy_obj.score_image(local_image_path_or_image_url)`|
|Odstranit webovou službu|`deploy_obj.delete()`|
|Sestavit image dockeru bez webové služby|`deploy_obj.build_docker_image()`|
|Seznam existujících nasazení|`AMLDeployment.list_deployment()`|
|Odstranit, pokud služba existuje s názvem nasazení|`AMLDeployment.delete_if_service_exist(deployment_name)`|

**Dokumentace k rozhraní API:** najdete [balíček referenční dokumentaci](https://aka.ms/aml-packages/vision) pro podrobné referenční dokumentace pro každý modul a třídy.

**Referenční dokumentace rozhraní příkazového řádku:** pokročilejší operace související s nasazením, najdete [odkaz na rozhraní příkazového řádku pro správu modelů](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/model-management-cli-reference).

**Správa nasazení na webu Azure portal**: můžete sledovat a spravovat svá nasazení v [webu Azure portal](https://ms.portal.azure.com/). Na webu Azure Portal najdete stránce účtu správy modelů Machine Learning pomocí jeho názvu. Potom přejděte na stránku účtu správy modelů > Správa modelů ve službě > služby.


```python
# ##### OPTIONAL###### 
# Interactive CLI setup helper, including model management account and deployment environment.
# If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
#
# UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT
#
# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
# # Optional. Persist your model on disk and reuse it later for deployment. 
# from cvtk import TFFasterRCNN, Context
# import os
# save_model_path = os.path.join(Context.get_global_context().storage.persistent_path, "saved_classifier.model")
# # Save model to disk
# dnn_model.serialize(save_model_path)
# # Load model from disk
# dnn_model = CNTKTLModel.deserialize(save_model_path)
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Optional Azure Machine Learning deployment cluster name (environment name) and resource group name
# If you don't provide here. It will use the current deployment environment (you can check with CLI command "az ml env show").
azureml_rscgroup = "<resource group>"
cluster_name = "<cluster name>"

# If you provide the cluster information, it will use the provided cluster to deploy. 
# Example: deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Create deployment object
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=dnn_model, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# Create the web service
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>Používání této webové služby 

Po nasazení modelu jako webové služby můžete hodnotit bitové kopie s webovou službou pomocí jedné z těchto metod:

- Skóre webové služby přímo s použitím objektu nasazení `deploy_obj.score_image(image_path_or_url)`

- Pomocí služby klíče koncového bodu adresy URL a Service (žádné místní nasazení): `AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

- Vytvářejí požadavky HTTP přímo ke stanovení skóre pro koncový bod webové služby. Tato možnost je určená pro zkušené uživatele.

### <a name="score-with-existing-deployment-object"></a>Skóre pomocí existujícího objektu nasazení

```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url with added parameters. Add softmax to score.
print("Score image url with added parameters. Add softmax to score")
from cvtk.utils.constants import ClassificationRESTApiParameters
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224], parameters={ClassificationRESTApiParameters.ADD_SOFTMAX:True})
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Time image scoring
import timeit

for img_index, img_obj in enumerate(test_set.images[:10]):
    print("Calling API for image {} of {}: {}...".format(img_index, len(test_set.images), img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
    print(return_json)
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>Skóre se adresa url koncového bodu služby a klíč služby

`AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "" # please replace with your own service url
service_key = "" # please replace with your own service key
# score local image with file path
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key)
print("serialized_result_in_json:", serialized_result_in_json)

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```

### <a name="score-endpoint-with-http-request-directly"></a>Skóre přímo na koncový bod s požadavkem http

Následující příklad kódu vytvoří požadavek HTTP přímo v Pythonu. Však můžete provést v jiných programovacích jazycích.


```python
def score_image_list_with_http(images, service_endpoint_url, service_key=None, parameters={}):
    """Score image list with http request

    Args:
        images(list): list of (input image file path, base64 image string, url or buffer)
        service_endpoint_url(str): endpoint url
        service_key(str): service key, None for local deployment.
        parameters(dict): service additional parameters in dictionary


    Returns:
        result (list): list of serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    routing_id = ""

    if service_key is None:
        headers = {'Content-Type': 'application/json',
                   'X-Marathon-App-Id': routing_id}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key), 'X-Marathon-App-Id': routing_id}
    payload = []
    for image in images:
        encoded = None
        # read image
        with open(image,'rb') as f:
            image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        # convert your image to base64 string
        encoded = base64.b64encode(image_buffer.getvalue())
        image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
        payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result

# Test with images
images = [test_set.images[0].storage_path, test_set.images[1].storage_path] # A list of local image files
score_image_list_with_http(images, service_endpoint_url, service_key)
```

### <a name="parse-serialized-result-from-web-service"></a>Výsledek analýzy serializovat z webové služby

Výstup z webové služby je řetězec formátu JSON. Tento řetězec JSON s různými třídami DNN modelu je možné analyzovat.


```python
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Parse result from json string
import numpy as np
parsed_result = CNTKTLModel.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
# Map result to image class
class_index = np.argmax(np.array(parsed_result))
print("Class index:", class_index)
dnn_model.class_map
print("Class label:", dnn_model.class_map[class_index])
```


## <a name="next-steps"></a>Další postup

Další informace o balíčku pro Azure Machine Learning pro počítačové zpracování obrazu v těchto článcích:

+ Zjistěte, jak [zlepšit přesnost tohoto modelu](how-to-improve-accuracy-for-computer-vision-models.md).

+ Přečtěte si [balíček přehled](https://aka.ms/aml-packages/vision).

+ Prozkoumejte [referenční dokumentaci](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) pro tento balíček.

+ Další informace o [další balíčky Python pro Azure Machine Learning](reference-python-package-overview.md).
