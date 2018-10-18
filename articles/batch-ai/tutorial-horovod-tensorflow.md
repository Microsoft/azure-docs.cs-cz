---
title: 'Kurz: Distribuované trénování pomocí Azure Batch AI a Horovodu | Microsoft Docs'
description: 'Kurz: Jak natrénovat distribuovaný model s Horovodem pomocí služby Azure Batch AI a Azure CLI'
services: batch-ai
author: johnwu10
manager: jeconnoc
ms.service: batch-ai
ms.topic: tutorial
ms.date: 09/03/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: de19b20865127fd37cd7bc1ac854288a95a68091
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44058102"
---
# <a name="tutorial-train-a-distributed-model-with-horovod"></a>Kurz: Trénování distribuovaného modelu pomocí Horovodu

V tomto kurzu natrénujete distribuovaný model hloubkového učení tak, že ho spustíte paralelně napříč několika uzly v clusteru Batch AI. Batch AI je spravovaná služba pro trénování strojového učení a škálovatelných modelů AI v clusterech Azure GPU. 

Tento kurz představuje běžný pracovní postup služby Batch AI a interakci s prostředky Batch AI prostřednictvím Azure CLI. Probíraná témata zahrnují:

> [!div class="checklist"]
> * Nastavení pracovního prostoru, experimentu a clusteru Batch AI
> * Nastavení sdílené složky Azure pro vstup a výstup
> * Paralelní spuštění modelu hloubkového učení pomocí Horovodu
> * Odeslání trénovací úlohy
> * Monitorování úlohy
> * Načtení výsledků trénování

Pro účely tohoto kurzu je model detekce objektů upravený tak, aby ho bylo možné spouštět paralelně pomocí [Horovodu](https://github.com/uber/horovod). Model se trénuje na imagích z [datové sady CIFAR-10](https://www.cs.toronto.edu/~kriz/cifar.html). Trénovací úloha se spouští na clusteru obsahujícím 24 virtuálních procesorů a 4 GPU a trvá přibližně 60 minut.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI ve verzi 2.0.38 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

## <a name="why-use-horovod"></a>Proč používáme Horovod?

Pro účely tohoto kurzu se používá Horovod, což je architektura distribuovaného trénování pro Tensorflow, Keras a PyTorch. Při použití Horovodu můžete pomocí několika řádků kódu převést trénovací skript, který je určený ke spuštění na jednom GPU, na takový, který běží efektivně v distribuovaném systému.

Batch AI kromě Horovodu podporuje pro distribuované trénování i několik dalších oblíbených open source architektur. Seznamte se s licenčními podmínkami pro každou architekturu, kterou budete používat k trénování modelů v produkčním prostředí.

## <a name="prepare-the-batch-ai-environment"></a>Příprava prostředí Batch AI

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pomocí příkazu `az group create` vytvořte skupinu prostředků s názvem `batchai.horovod` v oblasti `eastus`. Tuto skupinu prostředků použijete k nasazení prostředků Batch AI.

```azurecli-interactive
az group create --name batchai.horovod --location eastus
```

### <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Vytvořte pracovní prostor Batch AI pomocí příkazu `az batchai workspace create`. Pracovní prostor je kolekce jiných prostředků Batch AI nejvyšší úrovně. Následující příkaz vytvoří ve skupině prostředků pracovní prostor s názvem `batchaidev`.

```azurecli-interactive
az batchai workspace create --resource-group batchai.horovod --workspace batchaidev 
```

### <a name="create-an-experiment"></a>Vytvoření experimentu

Experiment Batch AI seskupuje jednu nebo několik úloh, na které se dotazujete a které spravujete společně. Následující příkaz `az batchai experiment create` vytvoří v pracovním prostoru ve skupině prostředků experiment s názvem `cifar`.

```azurecli-interactive
az batchai experiment create --resource-group batchai.horovod --workspace batchaidev --name cifar 
```

## <a name="set-up-a-gpu-cluster"></a>Nastavení clusteru GPU

Pak nastavte cluster GPU pro spuštění experimentu. Batch AI poskytuje flexibilní rozsah možností pro přizpůsobení clusterů pro konkrétní potřeby.

Následující příkaz `az batchai cluster create` vytvoří v pracovním prostoru ve skupině prostředků cluster se 4 uzly a s názvem `nc6cluster`. Ve výchozím nastavení používají virtuální počítače v tomto clusteru image Ubuntu Server, která je určená pro hostování aplikací založených na kontejnerech. Uzly clusteru v tomto příkladu používají velikost `Standard_NC6`, která obsahuje jedno GPU NVIDIA Tesla K80.

```azurecli-interactive
az batchai cluster create --resource-group batchai.horovod --workspace batchaidev --name nc6cluster --vm-priority dedicated  --vm-size Standard_NC6 --target 4 --generate-ssh-keys
```

Spusťte příkaz `az batchai cluster show`, aby se zobrazil stav clusteru. Úplné zřízení clusteru obvykle trvá několik minut.

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --output table
```

V rané fázi vytváření je cluster ve stavu `resizing`. Během změn stavu clusteru pokračujte v následujícím postupu. Cluster je připravený ke spuštění trénovací úlohy, když jeho stav je `steady` a jeho uzly jsou `idle`. Příklad:

```
Name        Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
----------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
nc6cluster  batchai.horovod  batchaidev   STANDARD_NC6  steady        4          0            0          0           0
```

## <a name="set-up-storage"></a>Nastavení úložiště

Pomocí příkazu `az storage account create` vytvořte účet úložiště, do kterého se uloží trénovací skript a výstup.

```azurecli-interactive
az storage account create --resource-group batchai.horovod --name mystorageaccount --location eastus --sku Standard_LRS
```

Pomocí příkazu `az storage share create` vytvořte v tomto účtu sdílenou složku Azure s názvem `myshare`:

```azurecli-interactive
az storage share create --name myshare --account-name mystorageaccount
```

V praxi lze toto stejné úložiště používat pro více úloh a experimentů. Abyste měli vše uspořádané, vytvořte v dané sdílené složce adresář pro ukládání souborů, které souvisejí s tímto konkrétním experimentem. Následující příkaz `az storage directory create` vytvoří adresář s názvem `cifar`.

```azurecli-interactive
az storage directory create --name cifar --share-name myshare --account-name mystorageaccount
```

Dalším krokem bude příprava skutečného trénovacího skriptu, který pak nahrajete do nově vytvořeného adresáře.

## <a name="create-the-training-script"></a>Vytvoření trénovacího skriptu

Pro účely tohoto experimentu spusťte skript Pythonu, který je aktualizovaný několika změnami, aby bylo možné model detekce objektů spouštět paralelně pomocí Horovodu. [Původní model](https://raw.githubusercontent.com/keras-team/keras/master/examples/cifar10_cnn.py) používá Keras s back-endem TensorFlow. 

V pracovním adresáři ve vašem prostředí pomocí svého oblíbeného textového editoru vytvořte soubor s názvem `cifar_cnn_distributed.py` s následujícím obsahem. Změny původního zdrojového kódu jsou okomentované s prefixem `HOROVOD`.

```python
from __future__ import print_function
import keras
from keras.datasets import cifar10
from keras.preprocessing.image import ImageDataGenerator
from keras.models import Sequential
from keras.layers import Dense, Dropout, Activation, Flatten
from keras.layers import Conv2D, MaxPooling2D
import tensorflow as tf
import horovod.keras as hvd
import os
from keras import backend as K
import math
import argparse 

# HOROVOD: initialize Horovod.
hvd.init()

# HOROVOD: pin GPU to be used to process local rank (one GPU per process)
config = tf.ConfigProto()
config.gpu_options.allow_growth = True
config.gpu_options.visible_device_list = str(hvd.local_rank())
K.set_session(tf.Session(config=config))

batch_size = 32
num_classes = 10
# HOROVOD: adjust number of epochs based on number of GPUs.
epochs = int(math.ceil(100.0 / hvd.size()))

data_augmentation = True
num_predictions = 20
# BATCH AI: change save directory to mounted storage path
parser = argparse.ArgumentParser()
parser.add_argument("-d", "--dir", help="directory to save model to")
args = parser.parse_args()
save_dir = os.path.join(args.dir, 'saved_models')
model_name = 'keras_cifar10_trained_model.h5'

# The data, split between train and test sets:
(x_train, y_train), (x_test, y_test) = cifar10.load_data()
print('x_train shape:', x_train.shape)
print(x_train.shape[0], 'train samples')
print(x_test.shape[0], 'test samples')

# Convert class vectors to binary class matrices.
y_train = keras.utils.to_categorical(y_train, num_classes)
y_test = keras.utils.to_categorical(y_test, num_classes)

model = Sequential()
model.add(Conv2D(32, (3, 3), padding='same',
                 input_shape=x_train.shape[1:]))
model.add(Activation('relu'))
model.add(Conv2D(32, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Conv2D(64, (3, 3), padding='same'))
model.add(Activation('relu'))
model.add(Conv2D(64, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Flatten())
model.add(Dense(512))
model.add(Activation('relu'))
model.add(Dropout(0.5))
model.add(Dense(num_classes))
model.add(Activation('softmax'))

# HOROVOD: adjust learning rate based on number of GPUs.
opt = keras.optimizers.rmsprop(lr=0.0001 * hvd.size(), decay=1e-6)

# HOROVOD: add Horovod Distributed Optimizer.
opt = hvd.DistributedOptimizer(opt)

# Let's train the model using RMSprop
model.compile(loss='categorical_crossentropy',
              optimizer=opt,
              metrics=['accuracy'])

callbacks = [
    # HOROVOD: broadcast initial variable states from rank 0 to all other processes.
    # This is necessary to ensure consistent initialization of all workers when
    # training is started with random weights or restored from a checkpoint.
    hvd.callbacks.BroadcastGlobalVariablesCallback(0),
]

# HOROVOD: save checkpoints only on worker 0 to prevent other workers from corrupting them.
if hvd.rank() == 0:
    callbacks.append(keras.callbacks.ModelCheckpoint('./checkpoint-{epoch}.h5'))

x_train = x_train.astype('float32')
x_test = x_test.astype('float32')
x_train /= 255
x_test /= 255

if not data_augmentation:
    print('Not using data augmentation.')
    model.fit(x_train, y_train,
              batch_size=batch_size,
              epochs=epochs,
              validation_data=(x_test, y_test),
              shuffle=True)
else:
    print('Using real-time data augmentation.')
    # This will do preprocessing and realtime data augmentation:
    datagen = ImageDataGenerator(
        featurewise_center=False,  # set input mean to 0 over the dataset
        samplewise_center=False,  # set each sample mean to 0
        featurewise_std_normalization=False,  # divide inputs by std of the dataset
        samplewise_std_normalization=False,  # divide each input by its std
        zca_whitening=False,  # apply ZCA whitening
        zca_epsilon=1e-06,  # epsilon for ZCA whitening
        rotation_range=0,  # randomly rotate images in the range (degrees, 0 to 180)
        width_shift_range=0.1,  # randomly shift images horizontally (fraction of total width)
        height_shift_range=0.1,  # randomly shift images vertically (fraction of total height)
        shear_range=0.,  # set range for random shear
        zoom_range=0.,  # set range for random zoom
        channel_shift_range=0.,  # set range for random channel shifts
        fill_mode='nearest',  # set mode for filling points outside the input boundaries
        cval=0.,  # value used for fill_mode = "constant"
        horizontal_flip=True,  # randomly flip images
        vertical_flip=False,  # randomly flip images
        rescale=None,  # set rescaling factor (applied before any other transformation)
        preprocessing_function=None,  # set function that will be applied on each input
        data_format=None,  # image data format, either "channels_first" or "channels_last"
        validation_split=0.0)  # fraction of images reserved for validation (strictly between 0 and 1)

    # Compute quantities required for feature-wise normalization
    # (std, mean, and principal components if ZCA whitening is applied).
    datagen.fit(x_train)

    # Fit the model on the batches generated by datagen.flow().
    model.fit_generator(datagen.flow(x_train, y_train,
                                     batch_size=batch_size),
                        epochs=epochs,
                        validation_data=(x_test, y_test),
                        workers=4)

# Save model and weights
if not os.path.isdir(save_dir):
    os.makedirs(save_dir)
model_path = os.path.join(save_dir, model_name)
model.save(model_path)
print('Saved trained model at %s ' % model_path)

# Score trained model.
scores = model.evaluate(x_test, y_test, verbose=1)
print('Test loss:', scores[0])
print('Test accuracy:', scores[1])
```

Jak ukazuje tento příklad, k aktivaci distribuovaného trénování s využitím architektury Horovod je potřeba jenom několik aktualizací modelu. 

Upozorňujeme, že tento skript používá pro účely ukázky poměrně malý model a datovou sadu, a proto tento distribuovaný model nebude nutně vykazovat významné zlepšení výkonu. Pokud chcete vidět skutečné možnosti distribuovaného trénování, použijte mnohem větší model a datovou sadu.

## <a name="upload-the-training-script"></a>Nahrání trénovacího skriptu

Když je skript připravený, bude dalším krokem jeho nahrání do adresáře ve sdílené složce, který jste vytvořili dříve. Následující příkaz `az storage file upload` nahraje příslušný skript z místního pracovního adresáře do správného umístění.

```azurecli-interactive
az storage file upload --path cifar --share-name myshare --source cifar_cnn_distributed.py --account-name mystorageaccount
```

## <a name="submit-the-training-job"></a>Odeslání trénovací úlohy

Po dokončení předchozího postupu vytvořte trénovací úlohu. V Batch AI pomocí souboru `job.json` definujte parametry pro spuštění úlohy. Pomocí svého oblíbeného textového editoru vytvořte konfigurační soubor úlohy s názvem `job.json` s následujícím obsahem.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 4,
        "horovodSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar/cifar_cnn_distributed.py",
            "commandLineArgs": "--dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/myshare",
                    "relativeMountPath": "myshare"
                }
            ]
        },
        "jobPreparation": {
            "commandLine": "apt update; apt install mpi-default-dev mpi-default-bin -y; pip install keras horovod"
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

Vysvětlení vlastností:

| Vlastnost | Popis |
| --------- | --------- |
| `nodeCount` | Počet uzlů vyhrazených pro danou úlohu. V tomto případě se úloha spustí paralelně ve `4` uzlech. |
| `horovodSettings` | Pole `pythonScriptFilePath` definuje cestu ke skriptu Horovodu, který se nachází ve dříve vytvořeném adresáři `cifar`. Pole `commandLineArgs` tvoří argumenty příkazového řádku pro spuštění daného skriptu. Pro tento experiment je jediným povinným argumentem adresář pro uložení modelu. `$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare` je cesta pro připojení sdílené složky. | 
| `stdOutErrPathPrefix` | Cesta pro uložení výstupů a protokolů úlohy. Pro tento příklad je touto cestou stejný adresář `cifar`. |
| `jobPreparation` | Jakékoli zvláštní pokyny pro přípravu prostředí pro spuštění dané úlohy. Tento skript vyžaduje instalaci uvedených balíčků MPI a Horovod. |
| `containerSettings` | Nastavení kontejneru, ve kterém se daná úloha spouští. Tato úloha používá kontejner Dockeru, který byl sestaven pomocí `tensorflow`.

S využitím této konfigurace vytvořte úlohu pomocí příkazu `az batchai job create`. Následující příkaz zařadí novou úlohu s názvem `cifar_distributed` do fronty s využitím všech prostředků, které byly do této chvíle nastaveny. 

```azurecli-interactive
az batchai job create --cluster nc6cluster --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --experiment cifar --config-file job.json --storage-account-name mystorageaccount
```

Pokud jsou uzly momentálně zaneprázdněné, může chvíli trvat, než se úloha spustí. Pomocí příkazu `az batchai job show` zobrazte stav provádění dané úlohy.

```azurecli-interactive
az batchai job show --experiment cifar --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --query "executionState"
```

### <a name="visualize-the-distributed-training"></a>Vizualizace distribuovaného trénování

Po spuštění úlohy použijte znovu příkaz `az batchai cluster show`, abyste zjistili stav uzlů clusteru. 

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --query "nodeStateCounts"
```

Výstup by se měl podobat následujícímu výstupu, ve kterém se zobrazují všechny čtyři uzly ve spuštěném stavu. Tento výsledek ukazuje, že pro distribuované trénování se momentálně využívají všechny čtyři uzly.

```
{
  "idleNodeCount": 0,
  "leavingNodeCount": 0,
  "preparingNodeCount": 0,
  "runningNodeCount": 4,
  "unusableNodeCount": 0
}
```

## <a name="monitor-the-job"></a>Monitorování úlohy

### <a name="list-output-files"></a>Zobrazení výpisu výstupních souborů

Během spuštění úlohy použijte příkaz `az batchai job file list` k zobrazení výpisu výstupních souborů, které daná úloha generuje.

```azurecli-interactive
az batchai job file list --experiment cifar --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev --output table
```

Výstup pro tento konkrétní experiment by se měl podobat následujícímu výstupu. Celkový výstup pro danou úlohu se zaznamenává do `stdout.txt`, zatímco do `stderr.txt` se zaznamenávají všechny chyby, ke kterým dochází při provádění hlavní úlohy. Ostatní soubory jsou protokoly výstupu, chyb a přípravy úloh, které odpovídají jednotlivým uzlům.

```
Name                                                    Type       Size  Modified
------------------------------------------------------  ------  -------  -------------------------
execution-tvm-676767296_1-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_2-20180718t174802z-p.log        file      15094  2018-07-18T22:41:55+00:00
execution-tvm-676767296_3-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_4-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
stderr-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr.txt                                              file       7653  2018-07-18T22:46:32+00:00
stdout-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout.txt                                              file    2316480  2018-07-18T22:46:32+00:00
```

### <a name="stream-an-output-file"></a>Streamování výstupního souboru

Ke streamování obsahu souboru použijte příkaz `az batchai job file stream`. V následujícím příkladu se streamuje hlavní výstupní protokol.

```azurecli-interactive
az batchai job file stream --experiment cifar --file-name stdout.txt --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev
```

Během spuštění úlohy příkaz streamuje standardní výstup trénovací úlohy a zobrazuje se výstup podobný následujícímu výstupu.

```
...
50000 train samples
10000 test samples
Using real-time data augmentation.
Epoch 1/25


   1/1563 [..............................] - ETA: 2:42:25 - loss: 2.3334 - acc: 0.0312   1/1563 [..............................] - ETA: 2:30:42 - loss: 2.2973 - acc: 0.0938
   1/1563 [..............................] - ETA: 30:36 - loss: 2.3175 - acc: 0.1250
   1/1563 [..............................] - ETA: 2:32:58 - loss: 2.3489 - acc: 0.0625
   2/1563 [..............................] - ETA: 1:21:59 - loss: 2.3230 - acc: 0.0625

   2/1563 [..............................]   2/1563 [..............................] - ETA: 1:16:09 - loss: 2.2913 - acc: 0.0938 - ETA: 1:17:15 - loss: 2.3147 - acc: 0.0781
   2/1563 [..............................] - ETA: 16:07 - loss: 2.3678 - acc: 0.0938
   3/1563 [..............................] - ETA: 55:05 - loss: 2.3232 - acc: 0.0938  
   3/1563 [..............................] - ETA: 51:57 - loss: 2.3185 - acc: 0.1146  
   3/1563 [..............................] - ETA: 51:12 - loss: 2.3179 - acc: 0.1042  
   3/1563 [..............................] - ETA: 11:13 - loss: 2.3504 - acc: 0.0833
   4/1563 [..............................] - ETA: 39:43 - loss: 2.3224 - acc: 0.1094
   4/1563 [..............................] - ETA: 42:09 - loss: 2.3049 - acc: 0.1250
   4/1563 [..............................] - ETA: 39:15 - loss: 2.3089 - acc: 0.1094
   4/1563 [..............................] - ETA: 9:16 - loss: 2.3316 - acc: 0.1016 
   5/1563 [..............................] - ETA: 39:51 - loss: 2.3153 - acc: 0.1125
   5/1563 [..............................] - ETA: 37:58 - loss: 2.3197 - acc: 0.1125
   5/1563 [..............................] - ETA: 37:35 - loss: 2.3148 - acc: 0.1062
   5/1563 [..............................] - ETA: 13:38 - loss: 2.3263 - acc: 0.1062
   6/1563 [..............................] - ETA: 35:48 - loss: 2.3168 - acc: 0.1198

   6/1563 [..............................]   6/1563 [..............................] - ETA: 34:13 - loss: 2.3142 - acc: 0.1198 - ETA: 33:51 - loss: 2.3162 - acc: 0.1042
   6/1563 [..............................] - ETA: 13:54 - loss: 2.3225 - acc: 0.1094
   7/1563 [..............................] - ETA: 30:53 - loss: 2.3181 - acc: 0.1071

   7/1563 [..............................]   7/1563 [..............................] - ETA: 29:32 - loss: 2.3149 - acc: 0.1161 - ETA: 29:13 - loss: 2.3140 - acc: 0.0938
   7/1563 [..............................] - ETA: 12:09 - loss: 2.3174 - acc: 0.1205
   8/1563 [..............................] - ETA: 26:04 - loss: 2.3113 - acc: 0.1133
   8/1563 [..............................] - ETA: 27:15 - loss: 2.3169 - acc: 0.1133
   8/1563 [..............................] - ETA: 10:51 - loss: 2.3152 - acc: 0.1172
...
```

Skript trénuje přes 25 epoch neboli průchodů přes trénovací datovou sadu. Tento proces trvá přibližně 60 minut. 

## <a name="retrieve-the-results"></a>Načtení výsledků

Pokud vše proběhlo správně, po dokončení skriptu by přesnost ověření měla být přibližně 70–75 % a natrénovaný model je uložený ve sdílené složce v adresáři `cifar/saved_models/keras_cifar10_trained_model.h5`. 

Trénování modelu je obvykle součástí rozsáhlejšího pracovního postupu. Můžete například zpřístupnit natrénovaný model v další aplikaci. Pokud chcete stáhnout natrénovaný model místně, použijte příkaz `az storage file download`. 

```azurecli-interactive
az storage file download --path cifar/saved_models/keras_cifar10_trained_model.h5 --share-name myshare --account-name mystorageaccount
```
## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení úloh se doporučuje z důvodu úspory nákladů na výpočetní prostředky snížit velikost všech clusterů na `0 nodes`, aby se vám neúčtoval čas nečinnosti. Použijte následující příkaz `az batchai cluster resize`. 

```azurecli-interactive
az batchai cluster resize --name nc6cluster --resource-group batchai.horovod --target 0 --workspace batchaidev
```

Později můžete velikost změnit na 1 nebo více uzlů, na kterých spustíte své úlohy. 

Pokud nebudete chtít tento pracovní prostor a účet úložiště používat v budoucnu, odstraňte skupinu prostředků pomocí příkazu `az group delete`. Odstraněním skupiny prostředků se odstraní všechny prostředky, které jsou součástí dané skupiny.

```azurecli-interactive
az group delete --name batchai.horovod
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili tyto postupy:

> [!div class="checklist"]
> * Nastavení pracovního prostoru, experimentu a clusteru Batch AI
> * Nastavení sdílené složky Azure pro vstup a výstup
> * Paralelní spuštění modelu pomocí Horovodu
> * Odeslání trénovací úlohy
> * Monitorování úlohy
> * Načtení výsledků trénování

Příklady použití Batch AI s různými architekturami najdete v návodech na GitHubu.

> [!div class="nextstepaction"]
> [Návody pro Batch AI](https://github.com/Azure/BatchAI/tree/master/recipes)
