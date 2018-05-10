---
title: Vytvořit a nasadit model klasifikace bitovou kopii pomocí Azure Machine Learning balíčku pro počítač vize.
description: Zjistěte, jak pro vytváření, trénování, testování a nasazení pomocí Azure Machine Learning balíčku pro počítač vize model klasifikace image počítače vize.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: bd9f01e76c68fa41616818251b5b54553059cbcc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="build-and-deploy-image-classification-models-with-azure-machine-learning"></a>Sestavení a nasazení bitové kopie klasifikační modely pomocí Azure Machine Learning

V tomto článku, Naučte se používat **Azure Machine Learning balíček pro počítač vize** (AMLPCV) pro trénování, testování a nasadit model klasifikace bitové kopie. 

Velký počet problémy v doméně vize počítače lze vyřešit pomocí bitové kopie klasifikace. Mezi tyto problémy patří vytváření modelů, které odpovědi na otázky, jako:
+ _OBJEKT nachází v bitové kopii? Například "pes", "auto", "dodávat" a tak dále_
+ _Jaké třída závažnosti nákazy oko je evinced retinal kontrolou tento pacienta?_

Při vytváření a nasazování tento model se AMLPCV, můžete přejít pomocí následujících kroků:
1. Vytvoření datové sady
2. Vizualizace bitové kopie a poznámky
3. Rozšíření bitové kopie
4. Definice modelu hluboké Neuronové sítě (DNN)
5. Školení třídění
6. Vyhodnocení a vizualizaci
7. Nasazení webové služby
8. Webová služba zátěžové testování

[CNTK](https://www.microsoft.com/cognitive-toolkit/) slouží jako rozhraní hloubkové learning školení je prováděn lokálně na počítači GPU používá technologii, jako ([hloubkové učení datové vědy VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)), a nasazení používá rozhraní příkazového řádku Azure ML Operationalization.

Obrátit [balíček referenční dokumentaci k nástroji](https://aka.ms/aml-packages/vision) pro podrobné referenční informace pro každý modul a třída.

## <a name="prerequisites"></a>Požadavky

1. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

1. Následující účty a aplikace musí být nastavení a nainstalován:
   - Účet Experimentování ve službě Azure Machine Learning 
   - Účet Azure Machine Learning Model správy
   - Nainstalovanou aplikaci Azure Machine Learning Workbench

   Pokud tyto tři jsou ještě vytvořen nebo nainstalován, postupujte podle kroků [Azure Machine Learning Quickstart a Workbench instalace](../service/quickstart-installation.md) článku. 

1. Balíček Azure Machine Learning pro vize počítače musí být nainstalován. Zjistěte, jak [instalaci tohoto balíčku zde](https://aka.ms/aml-packages/vision).

## <a name="sample-data-and-notebook"></a>Ukázková data a poznámkového bloku

### <a name="get-the-jupyter-notebook"></a>Získat poznámkového bloku Jupyter

Stažení poznámkového bloku ke spuštění ukázky popsaného sami.

> [!div class="nextstepaction"]
> [Získat poznámkového bloku Jupyter](https://aka.ms/aml-packages/vision/notebooks/image_classification)

### <a name="load-the-sample-data"></a>Načíst ukázková data

Následující příklad používá skládající se z bitové kopie 63 stolní datové sady. Každé bitové kopie je označený jako náležící do jedné ze čtyř různých tříd (misku, cup, příbory, štítek). Počet bitové kopie v tomto příkladu je malá, aby tato ukázka lze rychle provést. V praxi by je třeba zadat nejméně 100 bitové kopie na třídu. Všechny bitové kopie jsou umístěné na *".. /sample_data/imgs_recycling / "*, podadresáře s názvem"misku","cup","příbory"a"štítku".

![Datové sadě služby Azure Machine Learning](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)

## <a name="storage-context"></a>Kontext úložiště

Kontext úložiště slouží k určení, kde bude uložena různých výstupních souborů, jako je například rozšířená bitové kopie nebo DNN modelu soubory. Další informace o kontextech úložiště najdete v tématu [StorageContext dokumentaci](https://review.docs.microsoft.com/en-us/python/api/cvtk.core.context.storagecontext?view=azure-python&branch=smoke-test). 

Obsah úložiště za normálních okolností nemusí být explicitně nastaveno. Ale abyste se vyhnuli limitu 25 MB na velikost projektu způsobené Azure Machine Learning Workbench, nastavit výstupy adresář pro balíček Azure Machine Learning pro počítač vize do umístění mimo projekt Azure Machine Learning (".. /.. /.. /.. / cvtk_output "). Ujistěte se, že jakmile již není potřeba odebrat adresář "cvtk_output".


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
from cvtk.evaluation import ClassificationEvaluation, graph_roc_curve, graph_pr_curve, graph_confusion_matrix, basic_plot
import matplotlib.pyplot as plt
%matplotlib inline

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)

# Set storage context.
out_root_path = "../../../cvtk_output"
Context.create(outputs_path=out_root_path, persistent_path=out_root_path, temp_path=out_root_path)
```




    {
        "storage": {
            "outputs_path": "../../../cvtk_output",
            "persistent_path": "../../../cvtk_output",
            "temp_path": "../../../cvtk_output"
        }
    }



## <a name="create-a-dataset"></a>Vytvoření datové sady

Po importu závislosti a nastavit kontext úložiště, můžete vytvořit objekt datové sady.

Pokud chcete vytvořit tento objekt s Azure Machine Learning balíčkem pro počítač vize, zadejte do kořenového adresáře bitové kopie na místním disku. Tento adresář musí vycházet ze stejné obecné struktury jako stolní datovou sadu, to znamená, obsahovat podadresářů pomocí skutečného bitové kopie:
- kořenové
    - Popisek 1
    - Popisek 2
    - ...
    - Popisek n
  
Cvičení model klasifikace image pro různé datové sady je stejně snadná jako změna kořenovou cestu `dataset_location` v následujícím kódu tak, aby odkazoval na jiné bitové kopie.


```python
# Root image directory 
dataset_location = os.path.abspath(os.path.join(os.getcwd(), "../sample_data/imgs_recycling"))

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

Datové sady objektu poskytuje funkce, které stáhnout pomocí bitové kopie [rozhraní API Search bitové kopie Bingu](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/). 

Podporuje dva typy vyhledávací dotazy: 
+ Běžný textový dotazy
+ Dotazy na adresu URL obrázku

Je třeba zadat tyto dotazy společně s popisek třída uvnitř zakódovaná ve formátu JSON textového souboru. Příklad:

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

Kromě toho musí explicitně vytvořit objekt kontextu obsahovat klíč rozhraní API pro vyhledávání bitové kopie Bingu. To vyžaduje rozhraní API Search bitové kopie Bingu předplatné.

## <a name="visualize-and-annotate-images"></a>Vizualizaci a opatřit poznámkami bitové kopie

Můžete vizualizovat správné popisky v objektu datové sady pomocí následující pomůcky a bitové kopie. 

Pokud dojde k chybě "Widgetů Javascript nebyl zjištěn", spusťte tento příkaz jeho řešení:
<br>`jupyter nbextension enable --py --sys-prefix widgetsnbextension`


```python
from ui_utils.ui_annotation import AnnotationUI
annotation_ui = AnnotationUI(dataset, Context.get_global_context())
display(annotation_ui.ui)
```

![Datové sadě služby Azure Machine Learning](media/how-to-build-deploy-image-classification-models/image_annotation.png)

## <a name="augment-images"></a>Posílení bitové kopie

[ `augmentation` Modulu](https://docs.microsoft.com/en-us/python/api/cvtk.augmentation) poskytuje funkce, které posílení objekt datové sady pomocí těchto transformací popsané v [imgaug](https://github.com/aleju/imgaug) knihovny. Transformace bitové kopie mohou být seskupeny do jednoho kanálu, v takovém případě jsou všechny transformace v kanálu současně použít každé bitové kopie. 

Pokud byste chtěli použít kroky různých rozšíření, samostatně nebo v různých způsobem, můžete definovat více kanálů a předat je do *augment_dataset* funkce. Další informace a příklady rozšíření image, najdete v článku [imgaug dokumentaci](https://github.com/aleju/imgaug).

Přidávání obrázků rozšířená do trénovací sady je obzvláště užitečné pro malé datové sady. Vzhledem k tomu, že je proces školení DNN pomalejší z důvodu vyšší počet bitových kopií, školení, doporučujeme že spustit experimentování bez rozšíření.


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

## <a name="define-dnn-models"></a>Definovat DNN modely

S tímto balíčkem jsou podporovány následující pretrained modely hluboké Neuronové sítě: 
+ AlexNet
+ Resnet 18
+ Resnet 34
+ Resnet – 50
+ Resnet 101
+ Resnet 152

Tyto DNNs slouží jako třídění, nebo jako featurizer. 

Další informace o sítích naleznete [zde](https://github.com/Microsoft/CNTK/blob/master/PretrainedModels/Image.md), a základní informace o přenosu Learning [zde](https://blog.slavv.com/a-gentle-intro-to-transfer-learning-2c0b674375a0).

Výchozí parametry klasifikace bitovou kopii tohoto balíčku jsou rozlišení 224 x 224 pixelů a DNN Resnet 18. Tyto parametry byly vybrány pro dobré fungování i na širokou škálu úloh. Přesnost lze často zvýšit, například zvýšení rozlišení obrázku na 500 x 500 pixelů, nebo výběrem hlubší modelu (Resnet – 50). Ale změna parametrů můžou mít v významné zvýšení čas školení. Najdete v článku na [postupy a zvyšte tak přesnost](https://docs.microsoft.com/azure/machine-learning/service/how-to-improve-accuracy-for-computer-vision-models).


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
    

## <a name="train-the-classifier"></a>Cvičení třídění

Vyberte jednu z následujících metod pro předem vyškolení DNN.

  - **Vylepšení DNN**, který provede DNN ke klasifikaci přímo. Při školení DNN je pomalé, ho obvykle vede k dosažení nejlepších výsledků vzhledem k tomu, že všechny váhu sítě je možné zlepšit během cvičení umožnit nejlepší přesnost.

  - **DNN featurization**, která se spouští DNN jako-je použít k získání znázornění nižší dimenzí bitové kopie (512, 2048 nebo 4096 jako plovoucí). Aby reprezentace pak používá jako vstup pro učení samostatné třídění. Vzhledem k tomu, že DNN je udržováno beze změny, tento přístup je mnohem rychlejší ve srovnání s DNN upřesnění, ale přesnost není stejně dobře. Nicméně školení externí třídění například lineární SVM (jak je znázorněno v následujícím kódu) můžete poskytnout základní přehled o silné a pomůže při pochopení, vhodnosti k potížím.
  
TensorBoard umožňuje vizualizace průběhu školení. Chcete-li aktivovat TensorBoard:
1. Přidejte parametr `tensorboard_logdir=PATH` jak je znázorněno v následujícím kódu
1. Spusťte službu klienta TensorBoard pomocí příkazu `tensorboard --logdir=PATH` v nové konzole.
1. Podle pokynů TensorBoard, který ve výchozím nastavení je localhost:6006, otevřete webový prohlížeč. 


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


## <a name="evaluate-and-visualize-model-performance"></a>Vyhodnocení a vizualizovat výkon modelu

Můžete vyhodnotit výkon pro cvičný model u nezávislé testovací datové pomocí modulu vyhodnocení. Vyhodnocení metriky, které se vypočítá patří:
 
+ Přesnost (ve výchozím nastavení třídy průměrem)
+ Kód PR křivky
+ Křivka ROC
+ Oblasti v rámci křivky
+ Matice nedorozuměním


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

from ui_utils.ui_results_viewer import ResultsUI
results_ui = ResultsUI(test_set, Context.get_global_context(), pred_scores, pred_labels)
display(results_ui.ui)
```

![Datové sadě služby Azure Machine Learning](media/how-to-build-deploy-image-classification-models/Image_Classification_Results.png)


```python
# Precision / recall curve UI
precisions, recalls, thresholds = ce.compute_precision_recall_curve() 
thresholds = list(thresholds)
thresholds.append(thresholds[-1])
from ui_utils.ui_precision_recall import PrecisionRecallUI
pr_ui = PrecisionRecallUI(100*precisions[::-1], 100*recalls[::-1], thresholds[::-1])
display(pr_ui.ui) 
```

![Datové sadě služby Azure Machine Learning](media/how-to-build-deploy-image-classification-models/image_precision_curve.png)

## <a name="operationalization-deploy-and-consume"></a>Operationalization: nasazení a využívat

Operationalization se proces publikování modely a kód jako webové služby a spotřebě těchto služeb k vytvoření obchodní výsledky. 

Jakmile je cvičení modelu, můžete nasadit tento model jako webovou službu pro spotřebu pomocí [příkazového řádku Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning). Modely se dá nasadit do místního počítače nebo clusteru Azure Container Service (ACS). Pomocí služby ACS, můžete škálovat webovou službu ručně nebo použít funkci Automatické škálování.

**Přihlaste se pomocí rozhraní příkazového řádku Azure**

Pomocí [Azure](https://azure.microsoft.com/) účet s platným předplatným, přihlaste se pomocí rozhraní příkazového řádku následující příkaz:
<br>`az login`

+ Chcete-li přepnout do jiného předplatného Azure, použijte příkaz:
<br>`az account set --subscription [your subscription name]`

+ Pokud chcete zobrazit aktuální účet správy modelu, použijte příkaz:
  <br>`az ml account modelmanagement show`

**Vytvoření a nastavení prostředí pro nasazení**

Stačí nastavit prostředí pro nasazení jednou. Pokud nemáte ještě, nastavit prostředí pro nasazení teď pomocí [tyto pokyny](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup). 

Postupujte podle místního nebo kroky nastavení nasazení clusteru správně podle vašim potřebám.
+ Místní nasazení jsou podporovány pro počítače se systémy Linux a Windows 10, ale není pro virtuální počítač Windows datové vědy nebo virtuální počítač hloubkového učení. 
+ Nasazení v prostředí clusteru jsou podporovány pro Linux a Windows. 

Pokud chcete zobrazit prostředí aktivní nasazení, použijte následující příkaz rozhraní příkazového řádku:
<br>`az ml env show`
   
Ukázka příkazu příkazového řádku Azure CLI pro vytvoření a nastavení prostředí pro nasazení

```CLI
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. westcentralus] [-g [resource group]]
az ml env set -n [environment name] -g [resource group]
az ml env cluster
```
    
### <a name="manage-web-services-and-deployments"></a>Správa webové služby a nasazení

Následující rozhraní API umožňuje nasazovat modely jako webové služby, spravovat tyto webové služby a správu nasazení.

|Úkol|Rozhraní API|
|----|----|
|Vytvoření objektu nasazení|`deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model, aml_env="cluster")`
|Nasazení webové služby|`deploy_obj.deploy()`|
|Obrázek skóre|`deploy_obj.score_image(local_image_path_or_image_url)`|
|Odstranění webové služby|`deploy_obj.delete()`|
|Sestavení docker bitová kopie bez webové služby|`deploy_obj.build_docker_image()`|
|Seznam stávajícího nasazení|`AMLDeployment.list_deployment()`|
|Odstranit, pokud tato služba existuje s názvem nasazení|`AMLDeployment.delete_if_service_exist(deployment_name)`|

**Dokumentaci k rozhraní API:** obrátit [balíček referenční dokumentaci k nástroji](https://aka.ms/aml-packages/vision) pro podrobné referenční informace pro každý modul a třída.

**Referenční dokumentace rozhraní příkazového řádku:** více pokročilé operace související s nasazením, naleznete [model správy referenční dokumentace rozhraní příkazového řádku](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/model-management-cli-reference).

**Správa nasazení na portálu Azure**: můžete sledovat a spravovat nasazení v [portál Azure](https://ms.portal.azure.com/). Z portálu Azure najít stránku účtu Machine Learning Model správy pomocí jeho názvu. Potom přejděte na stránku účtu Model správy > Model správy > služby.


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

### <a name="consume-the-web-service"></a>Využívají webové služby 

Jakmile nasadíte model jako webovou službu, můžete stanovení skóre bitové kopie k webové službě pomocí jedné z těchto metod:

- Stanovení skóre přímo s objekt nasazení pomocí webové služby `deploy_obj.score_image(image_path_or_url)`

- Pomocí koncový bod adresy URL a služby klíče služby (žádné místní nasazení): `AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

- Tvoří své žádosti HTTP přímo ke stanovení skóre koncový bod webové služby. Tato možnost je pro zkušené uživatele.

### <a name="score-with-existing-deployment-object"></a>Stanovení skóre s existující objekt nasazení

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

# Score image url with added paramters. Add softmax to score.
print("Score image url with added paramters. Add softmax to score")
from cvtk.utils.constants import ClassificationRESTApiParamters
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224], parameters={ClassificationRESTApiParamters.ADD_SOFTMAX:True})
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

### <a name="score-with-service-endpoint-url-and-service-key"></a>Stanovení skóre s adresu url koncového bodu služby a klíč služby

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

### <a name="score-endpoint-with-http-request-directly"></a>Koncový bod skóre s přímo požadavku http

Následující příklad kódu tvoří požadavek HTTP přímo v Pythonu. Však můžete provést v jinými programovací jazyky.


```python
def score_image_list_with_http(images, service_endpoint_url, service_key=None, parameters={}):
    """Score image list with http request

    Args:
        images(list): list of (input image file path, base64 image string, url or buffer)
        service_endpoint_url(str): endpoint url
        service_key(str): service key, None for local deployment.
        parameters(dict): service additional paramters in dictionary


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

Výstup z webové služby je řetězec formátu JSON. Tento řetězec formátu JSON s různými třídami modelu DNN můžete analyzovat.


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

Další informace o Azure Machine Learning balíčku pro počítač vize v těchto článcích:

+ Zjistěte, jak [zlepšit přesnost tohoto modelu](how-to-improve-accuracy-for-computer-vision-models.md).

+ Pro čtení [balíček přehled a zjistěte, jak ji nainstalovat](https://aka.ms/aml-packages/vision).

+ Prozkoumejte [referenční dokumentace](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) pro tento balíček.

+ Další informace o [další balíčky Python pro Azure Machine Learning](reference-python-package-overview.md).
