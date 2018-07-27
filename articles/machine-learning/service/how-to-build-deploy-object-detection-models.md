---
title: Sestavujte a nasazujte objektový model zjišťování pomocí balíčku Azure Machine Learning pro počítačové zpracování obrazu.
description: Zjistěte, jak sestavit, trénování, testování a nasazování modelem počítačového zpracování obrazu objektu zjišťování pomocí balíčku Azure Machine Learning pro počítačové zpracování obrazu.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 06/01/2018
ms.openlocfilehash: 44059de5a0ef0667b4268d9cdc2997162bab474a
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285746"
---
# <a name="build-and-deploy-object-detection-models-with-azure-machine-learning"></a>Vytváření a nasazování modelů zjišťování objektu pomocí Azure Machine Learning

V tomto článku najdete další informace o použití **Azure Machine Learning balíček pro počítačové zpracování obrazu** pro trénování, testování a nasazení [rychleji R CNN](https://arxiv.org/abs/1506.01497) objektový model zjišťování. 

Velký počet problémů v doméně počítače pro zpracování obrazu můžete vyřešit pomocí zjišťování objektu. Tyto problémy patří vytváření modelů, které najít proměnný počet objektů na obrázku. 

Při vytváření a nasazování tento model s tímto balíčkem, můžete přejít pomocí následujících kroků:
1.  Vytvoření datové sady
2.  Definice modelu hluboké Neuronové sítě (DNN)
3.  Cvičení modelu
4.  Vyhodnocení a vizualizace
5.  Nasazení webové služby
6.  Webová služba zátěžové testování

V tomto příkladu se používá TensorFlow jako rozhraní hloubkového učení, školení se provádí místně na počítači s využitím GPU, jako [hloubkového učení virtuální počítač pro datové vědy](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview), a nasazení pomocí rozhraní příkazového řádku Azure ML Operacionalizace.

Najdete [balíček referenční dokumentaci](https://aka.ms/aml-packages/vision) pro podrobné referenční dokumentace pro každý modul a třídy.

## <a name="prerequisites"></a>Požadavky

1. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

1. Následující účty a aplikace musí mít nastavení a instalaci:
   - Účet Experimentování ve službě Azure Machine Learning 
   - Účet správy modelů Azure Machine Learning
   - Nainstalovanou aplikaci Azure Machine Learning Workbench

   Pokud tyto tři jsou ještě vytvořen nebo nainstalován, postupujte [Quickstart pro Azure Machine Learning a Workbench instalace](../service/quickstart-installation.md) článku. 

1. Musíte nainstalovat balíček Azure Machine Learning pro počítačové zpracování obrazu. Zjistěte, jak [instalaci tohoto balíčku zde](https://aka.ms/aml-packages/vision).

## <a name="sample-data-and-notebook"></a>Ukázková data a poznámkového bloku

### <a name="get-the-jupyter-notebook"></a>Získání poznámkového bloku Jupyter

Stáhnout ho na spuštění ukázky popsané sami.

> [!div class="nextstepaction"]
> [Získání poznámkového bloku Jupyter](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>Načtení ukázkových dat.

Pro tuto ukázku je součástí blízkým položek uvnitř chladniček datovou sadu skládající se z 30 imagí a 8 třídy (eggBox, joghurt, kečup, hub, hořčice, orange, spojování squash a vody). Pro každý obrázek jpg je poznámka – soubor xml s podobným názvem. 

Následující obrázek znázorňuje strukturu složek doporučené. 

![struktura složek](media/how-to-build-deploy-object-detection-models/data_directory.JPG)

## <a name="image-annotation"></a>Opatřování obrázků poznámkami

Anotovaný objekt, který umístění jsou nutné k natrénování a vyhodnocení detektor objektu. [LabelImg](https://tzutalin.github.io/labelImg) je open source nástroj anotace, který slouží k přidání poznámek ke bitové kopie. LabelImg zapíše soubor xml na obrázek ve formátu těkavých Pascal, který může přečíst tento balíček. 


```python
import warnings
warnings.filterwarnings("ignore")
import os, time
from cvtk.core import Context, ObjectDetectionDataset, TFFasterRCNN
from cvtk.evaluation import DetectionEvaluation
from cvtk.evaluation.evaluation_utils import graph_error_counts
from cvtk.utils import detection_utils

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)

from matplotlib import pyplot as plt
# Display the images
%matplotlib inline
```

## <a name="create-a-dataset"></a>Vytvoření datové sady

Vytvoření datové sady CVTK, který se skládá ze sady obrázků s jejich odpovídajících ohraničující pole poznámky. V tomto příkladu lednice imagí, které jsou součástí ".. / sample_data/foods/školení"složky se používají. Jsou podporovány pouze obrázky ve formátu JPEG.


```python
image_folder = "detection/sample_data/foods/train"
data_train = ObjectDetectionDataset.create_from_dir(dataset_name='training_dataset', data_dir=image_folder,
                                                    annotations_dir="Annotations", image_subdirectory='JPEGImages')

# Show some statistics of the training image, and also give one example of the ground truth rectangle annotations
data_train.print_info()
_ = data_train.images[2].visualize_bounding_boxes(image_size = (10,10))
```

    F1 2018-05-25 23:12:21,727 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-05-25 23:12:21,733 INFO azureml.vision:dataset creating dataset for scenario=detection 
    Dataset name: training_dataset
    Total classes: 8, total images: 25
    Label-wise object counts:
        Label eggBox: 20 objects
        Label joghurt: 20 objects
        Label ketchup: 20 objects
        Label mushroom: 20 objects
        Label mustard: 20 objects
        Label orange: 20 objects
        Label squash: 40 objects
        Label water: 20 objects
    Bounding box width and height distribution:
        Bounding box widths  0/5/25/50/75/95/100-percentile: 54/61/79/117/133/165/311 pixels
        Bounding box heights 0/5/25/50/75/95/100-percentile: 48/58/75/124/142/170/212 pixels
    


![PNG](media/how-to-build-deploy-object-detection-models/output_6_1.JPG)


## <a name="define-a-model"></a>Definovat model

V tomto příkladu je model rychleji CNN R použít. Různé parametry lze zadat, při definování tento model. Význam těchto parametrů, jakož i parametrů použitých pro trénování (viz další část) najdete v obou CVTK dokumenty k rozhraní API nebo na [webu zjišťování objektu Tensorflow](https://github.com/tensorflow/models/tree/master/research/object_detection). Další informace o modelu rychleji CNN R najdete [tento odkaz](https://docs.microsoft.com/en-us/cognitive-toolkit/Object-Detection-using-Faster-R-CNN#technical-details). Tento model je založen na rychlé R-CNN a další informace lze nalézt [tady](https://docs.microsoft.com/en-us/cognitive-toolkit/Object-Detection-using-Fast-R-CNN#algorithm-details).


```python
score_threshold = 0.0       # Threshold on the detection score, use to discard lower-confidence detections.
max_total_detections = 300  # Maximum number of detections. A high value slows down training but might increase accuracy.
my_detector = TFFasterRCNN(labels=data_train.labels, 
                           score_threshold=score_threshold, 
                           max_total_detections=max_total_detections)
```

## <a name="train-the-model"></a>Trénování modelu

Model školení COCO rychleji R-CNN s ResNet50 slouží jako výchozí bod pro vzdělávání. 

K trénování detektoru, počet kroků školení v kódu je nastaven na 350, tak, aby školení běží rychleji (přibližně 5 minut s GPU). V praxi nastavte ji na alespoň 10krát počet obrázků do trénovací sady.

V tomto příkladu se počet kroků školení detektor nastaven na 350 pro rychlé školení. V praxi však základním pravidlem je nastavit kroky 10 nebo vícekrát počet imagí trénovací sady.

Jsou dvě klíčové parametry pro vzdělávání:
- Počet kroků pro trénování modelu reprezentován num_seps argumentem. Každý krok trénovat model s minibatch jeden velikosti dávky.
- Učení procenta, který je možné nastavit initial_learning_rate

```python
print("tensorboard --logdir={}".format(my_detector.train_dir))

# to get good results, use a larger value for num_steps, e.g., 5000.
num_steps = 350
learning_rate = 0.001 # learning rate

start_train = time.time()
my_detector.train(dataset=data_train, num_steps=num_steps, 
                  initial_learning_rate=learning_rate)
end_train = time.time()
print(end_train-start_train)
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\train
    F1 2018-05-25 23:12:22,764 INFO azureml.vision:Fit starting in experiment  1125722225 
    F1 2018-05-25 23:12:22,767 INFO azureml.vision:model starting trainging for scenario=detection 
    Using existing checkpoint file that's saved at 'C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\models\detection\faster_rcnn_resnet50_coco_2018_01_28\model.ckpt.index'.
    TFRecords creation started.
    F1 2018-05-25 23:12:22,773 INFO On image 0 of 25
    TFRecords creation completed.
    Training started.
    Training progressing: step 0 ...
    Training progressing: step 100 ...
    Training progressing: step 200 ...
    Training progressing: step 300 ...
    F1 2018-05-25 23:18:02,730 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:18:10,722 INFO 2953 ops in the final graph.
    F1 2018-05-25 23:18:24,244 INFO azureml.vision:Fit finished in experiment  1125722225 
    Training completed.
    361.604615688324
    

TensorBoard umožňuje znázornit průběh školení. TensorBoard události jsou umístěny ve složce určené atributem train_dir objekt modelu. Chcete-li zobrazit TensorBoard, postupujte takto:
1. Kopírování, tisku, který začíná "tensorboard – logdir" na příkazový řádek a spusťte ho. 
2. Zkopírujte adresu URL vrácené z příkazového řádku do webového prohlížeče a zobrazit TensorBoard. 

TensorBoard by měl vypadat jako na následujícím snímku obrazovky. Trvá ještě chvilku na složce školení bude zaplněn. Proto pokud není uveden TensorBoard nahoru správně první čas zkuste opakující se kroky 1-2.  

![tensorboard](media/how-to-build-deploy-object-detection-models/tensorboard.JPG)

## <a name="evaluate-the-model"></a>Vyhodnocení modelu

Metoda "vyhodnotit" slouží k vyhodnocení modelu. Tato funkce vyžaduje jako vstup objekt ObjectDetectionDataset. Vyhodnocení datové sady je možné vytvořit pomocí stejné funkce jako jste použili pro trénovací datové sady. Podporované metriky je průměrná přesnost, jak jsou definovány pro [PASCAL těkavých Challenge](http://host.robots.ox.ac.uk/pascal/VOC/pubs/everingham10.pdf).  


```python
image_folder = "detection/sample_data/foods/test"
data_val = ObjectDetectionDataset.create_from_dir(dataset_name='val_dataset', data_dir=image_folder)
eval_result = my_detector.evaluate(dataset=data_val)
```

    F1 2018-05-25 23:18:24,253 INFO azureml.vision:dataset creating dataset for scenario=detection 
    F1 2018-05-25 23:18:24,286 INFO On image 0 of 5
    F1 2018-05-25 23:18:29,300 INFO Starting evaluation at 2018-05-26-03:18:29
    F1 2018-05-25 23:18:32,403 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:33,158 INFO Detection visualizations written to summary with tag image-0.
    F1 2018-05-25 23:18:33,518 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:34,342 INFO Detection visualizations written to summary with tag image-1.
    F1 2018-05-25 23:18:34,714 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:35,470 INFO Detection visualizations written to summary with tag image-2.
    F1 2018-05-25 23:18:35,835 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:36,654 INFO Detection visualizations written to summary with tag image-3.
    F1 2018-05-25 23:18:37,010 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:37,798 INFO Detection visualizations written to summary with tag image-4.
    F1 2018-05-25 23:18:37,804 INFO Running eval batches done.
    F1 2018-05-25 23:18:37,805 INFO # success: 5
    F1 2018-05-25 23:18:37,806 INFO # skipped: 0
    F1 2018-05-25 23:18:38,119 INFO Writing metrics to tf summary.
    F1 2018-05-25 23:18:38,121 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/eggBox: 1.000000
    F1 2018-05-25 23:18:38,205 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/joghurt: 0.942857
    F1 2018-05-25 23:18:38,206 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/ketchup: 1.000000
    F1 2018-05-25 23:18:38,207 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mushroom: 1.000000
    F1 2018-05-25 23:18:38,208 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mustard: 1.000000
    F1 2018-05-25 23:18:38,209 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/orange: 1.000000
    F1 2018-05-25 23:18:38,210 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/squash: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/water: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/Precision/mAP@0.5IOU: 0.992857
    F1 2018-05-25 23:18:38,253 INFO Metrics written to tf summary.
    F1 2018-05-25 23:18:38,254 INFO Finished evaluation!
    

Výsledky vyhodnocení můžete vytisknout ve formátu vyčistit.


```python
# print out the performance metric values
for label_obj in data_train.labels:
    label = label_obj.name
    key = 'PASCAL/PerformanceByCategory/AP@0.5IOU/' + label
    print('{0: <15}: {1: <3}'.format(label, round(eval_result[key], 2)))
print('{0: <15}: {1: <3}'.format("overall:", round(eval_result['PASCAL/Precision/mAP@0.5IOU'], 2))) 
```

    joghurt        : 0.94
    squash         : 1.0
    mushroom       : 1.0
    eggBox         : 1.0
    ketchup        : 1.0
    mustard        : 1.0
    water          : 1.0
    orange         : 1.0
    overall:       : 0.99
    

Podobně můžete vypočítat přesnost modelu na trénovací sady. Tím usnadníte Ujistěte se, že školení konvergované na dobrým řešením. Přesnost na cvičnou sadou po úspěšné školení často blíží 100 %.

Výsledky vyhodnocení lze také zobrazit z TensorBoard, včetně hodnoty mapy a obrázky s předpokládanou ohraničující polí. Zkopírujte tisku z následující kód do okna příkazového řádku spusťte klienta TensorBoard. Tady je použita hodnota portu 8008 aby nedošlo ke konfliktu s výchozí hodnotou 6006, který se používal pro zobrazení stavu školení.


```python
print("tensorboard --logdir={} --port=8008".format(my_detector.eval_dir))
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\eval --port=8008
    

## <a name="score-an-image"></a>Skóre bitovou kopii

Jakmile budete spokojeni s výkonem trénovaného modelu, "skóre: funkce objekt modelu lze použít ke stanovení skóre nových imagí. Vrácené výsledky lze vizualizovat pomocí funkce "vizualizace". 


```python
image_path = data_val.images[1].storage_path
detections_dict = my_detector.score(image_path)
path_save = "./scored_images/scored_image_preloaded.jpg"
ax = detection_utils.visualize(image_path, detections_dict, image_size=(8, 12))
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

![PNG](media/how-to-build-deploy-object-detection-models/output_20_0.JPG)

##  <a name="save-the-model"></a>Uložit model

Trénovaného modelu můžete uložit na disk a načtena do paměti, jak je znázorněno v následujícím příkladu kódu.


```python
save_model_path = "./frozen_model/faster_rcnn.model"
my_detector.save(save_model_path)
```

    F1 2018-05-25 23:18:55,166 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:19:03,867 INFO 2953 ops in the final graph.
    

## <a name="load-the-saved-model-for-scoring"></a>Načtení uložené modelu pro vyhodnocení

K použití uloženého modelu, načtěte model do paměti s funkcí "zatížení". Stačí jednou načíst. 

```python
my_detector_loaded = TFFasterRCNN.load(save_model_path)
```

Po načtení modelu lze použít ke stanovení skóre pro bitovou kopii nebo seznam imagí. Pro jednu bitovou kopii je vrácena slovník pomocí klíče, jako je například "detection_boxes", "detection_scores" a "num_detections". Pokud je vstup seznam imagí, je vrácen seznam slovník s jeden slovník odpovídající jednu image. 


```python
detections_dict = my_detector_loaded.score(image_path)
```

Zjištěné objekty se skóre nad 0,5, včetně popisků, skóre a souřadnice lze vytisknout navýšení kapacity.


```python
look_up = dict((v,k) for k,v in my_detector.class_map.items())
n_obj = 0
for i in range(detections_dict['num_detections']):
    if detections_dict['detection_scores'][i] > 0.5:
        n_obj += 1
        print("Object {}: label={:11}, score={:.2f}, location=(top: {:.2f}, left: {:.2f}, bottom: {:.2f}, right: {:.2f})".format(
            i, look_up[detections_dict['detection_classes'][i]], 
            detections_dict['detection_scores'][i], 
            detections_dict['detection_boxes'][i][0],
            detections_dict['detection_boxes'][i][1], 
            detections_dict['detection_boxes'][i][2],
            detections_dict['detection_boxes'][i][3]))    
        
print("\nFound {} objects in image {}.".format(n_obj, image_path))           
```

    Object 0: label=squash     , score=0.99, location=(top: 0.74, left: 0.30, bottom: 0.84, right: 0.42)
    Object 1: label=squash     , score=0.98, location=(top: 0.27, left: 0.21, bottom: 0.37, right: 0.33)
    Object 2: label=orange     , score=0.98, location=(top: 0.31, left: 0.39, bottom: 0.37, right: 0.48)
    Object 3: label=joghurt    , score=0.98, location=(top: 0.57, left: 0.29, bottom: 0.67, right: 0.39)
    Object 4: label=eggBox     , score=0.97, location=(top: 0.41, left: 0.53, bottom: 0.49, right: 0.69)
    Object 5: label=water      , score=0.95, location=(top: 0.23, left: 0.51, bottom: 0.37, right: 0.57)
    Object 6: label=mustard    , score=0.88, location=(top: 0.61, left: 0.47, bottom: 0.66, right: 0.57)
    Object 7: label=ketchup    , score=0.80, location=(top: 0.62, left: 0.62, bottom: 0.68, right: 0.72)
    
    Found 8 objects in image ../sample_data/foods/test\JPEGImages\10.jpg.
    

Vizualizujte skóre stejně jako před.


```python
path_save = "./scored_images/scored_image_frozen_graph.jpg"
ax = detection_utils.visualize(image_path, detections_dict, path_save=path_save, image_size=(8, 12))
# ax.get_figure() # use this code extract the returned image
```

![PNG](media/how-to-build-deploy-object-detection-models/output_30_0.JPG)

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
# ##### OPTIONAL - Interactive CLI setup helper ###### 
# # Interactive CLI setup helper, including model management account and deployment environment.
# # If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
# # UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT

# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Create deployment object
# It will use the current deployment environment (you can check it with CLI command "az ml env show").
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=my_detector, replicas=1)

# Alternatively, you can provide azure machine learning deployment cluster name (environment name) and resource group name
# to deploy your model. It will use the provided cluster to deploy. To do that, please uncomment the following lines to create 
# the deployment object.

# azureml_rscgroup = "<resource group>"
# cluster_name = "<cluster name>"
# deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=my_detector,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# create the webservice
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>Používání této webové služby

Po vytvoření webové služby můžete skóre obrázků s nasazenou webovou službu. Máte několik možností:

   - Můžete přímo skóre webové služby s objektem nasazení s: deploy_obj.score_image(image_path_or_url) 
   - Nebo můžete pomocí adresy url koncového bodu služby a klíč služby, (žádné místní nasazení): AMLDeployment.score_existing_service_with_image (image_path_or_url service_endpoint_url, service_key = None)
   - Vytvářejí požadavky http přímo ke stanovení skóre pro koncový bod webové služby (pro pokročilé uživatele).

### <a name="score-with-existing-deployment-object"></a>Skóre pomocí existujícího objektu nasazení
```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = data_train.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])

```


```python
# Time image scoring
import timeit

num_images = 3
for img_index, img_obj in enumerate(data_train.images[:num_images]):
    print("Calling API for image {} of {}: {}...".format(img_index, num_images, img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>Skóre se adresa url koncového bodu služby a klíč služby
```
    AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)
```


```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "http://xxx" # please replace with your own service url
service_key = "xxx" # please replace with your own service key

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])
```

### <a name="score-endpoint-with-http-request-directly"></a>Skóre přímo na koncový bod s požadavkem http
Tady je příklad kódu pro formulář žádosti http přímo v Pythonu. Můžete to provést v jiných programovacích jazycích.


```python
def score_image_with_http(image, service_endpoint_url, service_key=None, parameters={}):
    """Score local image with http request

    Args:
        image (str): Image file path
        service_endpoint_url(str): web service endpoint url
        service_key(str): Service key. None for local deployment.
        parameters (dict): Additional request paramters in dictionary. Default is {}.


    Returns:
        str: serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    import json

    if service_key is None:
        headers = {'Content-Type': 'application/json'}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key)}
    payload = []
    encoded = None
    
    # Read image
    with open(image,'rb') as f:
        image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        
    # Convert your image to base64 string
    # image_in_base64 : "b'{base64}'"
    encoded = base64.b64encode(image_buffer.getvalue())
    image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
    payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
        json.loads(result[0])
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result[0]

```

### <a name="parse-serialized-result-from-webservice"></a>Výsledek analýzy serializovat z webové služby
Výsledek z webové služby je v řetězci json, který může být analyzován.


```python
image_path_or_url = image_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])
```


```python
# Parse result from json string
import numpy as np
parsed_result = TFFasterRCNN.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
```


```python
ax = detection_utils.visualize(image_path, parsed_result)
path_save = "./scored_images/scored_image_web.jpg"
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

## <a name="next-steps"></a>Další postup

Další informace o balíčku pro Azure Machine Learning pro počítačové zpracování obrazu v těchto článcích:

+ Přečtěte si [balení – přehled a zjistěte, jak ji nainstalovat](https://aka.ms/aml-packages/vision).

+ Prozkoumejte [referenční dokumentaci](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) pro tento balíček.

+ Další informace o [další balíčky Python pro Azure Machine Learning](reference-python-package-overview.md).
