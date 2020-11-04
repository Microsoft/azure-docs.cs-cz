---
title: 'Kurz: výuka & nasazení modelů: VS Code (Preview)'
titleSuffix: Azure Machine Learning
description: Naučte se naučit a nasazovat model klasifikace imagí pomocí TensorFlow a rozšíření Azure Machine Learning Visual Studio Code.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 07/09/2020
ms.custom: contperfq4
ms.openlocfilehash: 98ca0f1e82b81c8aad53938a5475f1ece68c0e33
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321560"
---
# <a name="train-and-deploy-an-image-classification-tensorflow-model-using-the-azure-machine-learning-visual-studio-code-extension-preview"></a>Výuka a nasazení modelu TensorFlow pro klasifikaci imagí pomocí rozšíření Azure Machine Learning Visual Studio Code (Preview)

Naučte se, jak naučit a nasazovat model klasifikace imagí pro rozpoznávání ručně psaných čísel pomocí TensorFlow a rozšíření Azure Machine Learning Visual Studio Code.

V tomto kurzu se naučíte provádět následující úlohy:

> [!div class="checklist"]
> * Vysvětlení kódu
> * Vytvoření pracovního prostoru
> * Vytvoření experimentu
> * Konfigurace cílů počítače
> * Spuštění konfiguračního souboru
> * Učení modelu
> * Registrace modelu
> * Nasazení modelu

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure. Pokud ho ještě nemáte, zaregistrujte se a vyzkoušejte si [bezplatnou nebo placená verzi Azure Machine Learning](https://aka.ms/AMLFree).
- Nainstalujte [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview), odlehčený Editor kódu pro různé platformy.
- Rozšíření Azure Machine Learning Studio Visual Studio Code. Pokyny k instalaci najdete v [kurzu instalace Azure Machine Learning Visual Studio Code rozšíření](./tutorial-setup-vscode-extension.md)

## <a name="understand-the-code"></a>Vysvětlení kódu

Kód pro tento kurz používá TensorFlow k výuce modelu Machine Learning klasifikace imagí, který kategorizuje ručně psané číslice z 0-9. Vytvoří tak neuronové síť, která převezme obrazové hodnoty 28 px × 28 px jako vstup a vytvoří výstup seznamu 10 pravděpodobností, jednu pro každou klasifikovanou číslici. Níže je uveden příklad toho, co vypadá jako u dat.  

![Číslice MNIST ručně zapsaných](./media/tutorial-train-deploy-image-classification-model-vscode/digits.png)

Získejte kód pro tento kurz stažením a rozzipováváem [nástrojů pro vs Code pro úložiště AI](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) kdekoli v počítači.

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

První věc, kterou je třeba udělat k vytvoření aplikace v Azure Machine Learning, je vytvoření pracovního prostoru. Pracovní prostor obsahuje prostředky pro výukové modely i pro samotné školené modely. Další informace najdete v tématu [co je pracovní prostor](./concept-workspace.md). 

1. Na řádku Visual Studio Code aktivity výběrem ikony **Azure** otevřete Azure Machine Learning zobrazení.
1. Klikněte pravým tlačítkem na předplatné Azure a vyberte **vytvořit pracovní prostor**. 
    
    > [!div class="mx-imgBorder"]
    > ![Vytvoření pracovního prostoru](./media/tutorial-train-deploy-image-classification-model-vscode/create-workspace.png)

1. Ve výchozím nastavení je generován název obsahující datum a čas vytvoření. V poli textové zadání změňte název na "TeamWorkspace" a stiskněte klávesu **ENTER**.
1. Vyberte **vytvořit novou skupinu prostředků**. 
1. Pojmenujte skupinu prostředků "TeamWorkspace-RG" a stiskněte klávesu **ENTER**. 
1. Vyberte umístění pro váš pracovní prostor. Doporučuje se zvolit umístění, které je nejblíže umístění, do kterého plánujete model nasadit. Například "Západní USA 2".
1. Po zobrazení výzvy k výběru typu pracovního prostoru zvolte **základní**.

V tomto okamžiku se vytvoří požadavek na Azure, který vytvoří nový pracovní prostor ve vašem účtu. Po několika minutách se nový pracovní prostor zobrazí v uzlu vašeho předplatného. 

## <a name="create-an-experiment"></a>Vytvoření experimentu

V pracovním prostoru se dá vytvořit jeden nebo více experimentů, abyste mohli sledovat a analyzovat jednotlivé běhy školení k jednotlivým modelům. Spuštění se dá provést v cloudu Azure nebo na místním počítači.

1. Na řádku Visual Studio Code aktivity vyberte ikonu **Azure** . Zobrazí se zobrazení Azure Machine Learning.
1. Rozbalte uzel předplatného.
1. Rozbalte uzel **TeamWorkspace** . 
1. Klikněte pravým tlačítkem na uzel **experimenty** .
1. V místní nabídce vyberte **vytvořit experiment** .

    > [!div class="mx-imgBorder"]
    > ![Vytvoření experimentu](./media/tutorial-train-deploy-image-classification-model-vscode/create-experiment.png)

1. Pojmenujte experiment "MNIST ručně zapsaných" a stisknutím klávesy **ENTER** vytvořte nový experiment. 

Podobně jako pracovní prostory se do Azure pošle požadavek na vytvoření experimentu s poskytnutými konfiguracemi. Po několika minutách se nový experiment zobrazí v uzlu *experimenty* pracovního prostoru. 

## <a name="configure-compute-targets"></a>Konfigurace cílů výpočtů

Výpočetní cíl je výpočetní prostředek nebo prostředí, kde spouštíte skripty a nasazujete školené modely. Další informace najdete v dokumentaci k [cílům Azure Machine Learning COMPUTE](./concept-compute-target.md).

Postup vytvoření cíle výpočtů:

1. Na řádku Visual Studio Code aktivity vyberte ikonu **Azure** . Zobrazí se zobrazení Azure Machine Learning. 
1. Rozbalte uzel předplatného. 
1. Rozbalte uzel **TeamWorkspace** . 
1. Pod uzlem pracovní prostor klikněte pravým tlačítkem na uzel **COMPUTE clustery** a vyberte **vytvořit výpočetní** prostředky. 

    > [!div class="mx-imgBorder"]
    > ![Vytvořit cíl výpočtů](./media/tutorial-train-deploy-image-classification-model-vscode/create-compute.png)

1. Vyberte **Azure Machine Learning COMPUTE (AmlCompute)**. Azure Machine Learning COMPUTE je spravovaná a výpočetní infrastruktura, která umožňuje uživateli snadno vytvořit jeden nebo více uzlů, které se dají použít s ostatními uživateli v pracovním prostoru.
1. Vyberte velikost virtuálního počítače. V seznamu možností vyberte **Standard_F2s_v2** . Velikost virtuálního počítače má vliv na dobu potřebnou k učení vašich modelů. Další informace o velikostech virtuálních počítačů najdete v tématu [velikosti pro virtuální počítače Linux v Azure](../virtual-machines/sizes.md).
1. Pojmenujte svůj COMPUTE "TeamWkspc-com" a stisknutím klávesy **ENTER** vytvořte výpočetní výkon.

    Soubor se zobrazí v VS Code s obsahem podobným následujícímu:

    ```json
    {
        "location": "westus2",
        "tags": {},
        "properties": {
            "computeType": "AmlCompute",
            "description": "",
            "properties": {
                "vmSize": "Standard_F2s_v2",
                "vmPriority": "dedicated",
                "scaleSettings": {
                    "maxNodeCount": 4,
                    "minNodeCount": 0,
                    "nodeIdleTimeBeforeScaleDown": "PT120S"
                }
            }
        }
    }
    ```

1. Pokud jste s konfigurací spokojeni, otevřete paletu příkazů výběrem možnosti **zobrazit > paleta příkazů**.
1. Zadáním následujícího příkazu do palety příkazů uložte konfigurační soubor spuštění.

    ```text
    Azure ML: Save and Continue
    ```

Po několika minutách se nový cíl výpočtů zobrazí v uzlu *výpočetní clustery* v pracovním prostoru.

## <a name="create-a-run-configuration"></a>Vytvořit konfiguraci spuštění

Když odešlete školicí běh do cílového výpočetního prostředí, odešlete také konfiguraci potřebnou ke spuštění školicí úlohy. Například skript, který obsahuje školicí kód a závislosti Pythonu potřebné ke spuštění.

Vytvoření konfigurace spuštění:

1. Na řádku Visual Studio Code aktivity vyberte ikonu **Azure** . Zobrazí se zobrazení Azure Machine Learning. 
1. Rozbalte uzel předplatného. 
1. Rozbalte uzel **TeamWorkspace > výpočetních clusterů** . 
1. Pod výpočetním uzlem klikněte pravým tlačítkem na výpočetní uzel **TeamWkspc-com** a vyberte **vytvořit konfiguraci spuštění**.

    > [!div class="mx-imgBorder"]
    > ![Vytvořit konfiguraci spuštění](./media/tutorial-train-deploy-image-classification-model-vscode/create-run-configuration.png)

1. Zadejte název konfigurace spuštění "MNIST ručně zapsaných-RC" a stisknutím klávesy **ENTER** vytvořte konfiguraci spuštění.
1. Pak vyberte **vytvořit nové prostředí Azure ml**. Prostředí definují závislosti potřebné ke spouštění skriptů.
1. Pojmenujte prostředí "MNIST ručně zapsaných-ENV" a stiskněte klávesu **ENTER**.
1. V seznamu vyberte **soubor conda závislosti** .
1. Stiskněte klávesu **ENTER** pro procházení souboru závislostí conda. V tomto případě je soubor závislostí souborem v `env.yml` `vscode-tools-for-ai/mnist-vscode-docs-sample` adresáři.

    Soubor se zobrazí v VS Code s obsahem podobným následujícímu:

    ```json
    {
        "name": "MNIST-env",
        "version": "1",
        "python": {
            "interpreterPath": "python",
            "userManagedDependencies": false,
            "condaDependencies": {
                "name": "vs-code-azure-ml-tutorial",
                "channels": [
                    "defaults"
                ],
                "dependencies": [
                    "python=3.6.2",
                    "tensorflow=1.15.0",
                    "pip",
                    {
                        "pip": [
                            "azureml-defaults"
                        ]
                    }
                ]
            },
            "baseCondaEnvironment": null
        },
        "environmentVariables": {},
        "docker": {
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "baseDockerfile": null,
            "baseImageRegistry": {
                "address": null,
                "username": null,
                "password": null
            },
            "enabled": false,
            "arguments": []
        },
        "spark": {
            "repositories": [],
            "packages": [],
            "precachePackages": true
        },
        "inferencingStackVersion": null
    }
    ```

1. Až budete s konfigurací spokojeni, uložte ji tak, že otevřete paletu příkazů a zadáte následující příkaz:

    ```text
    Azure ML: Save and Continue
    ```

1. Tato ukázka nepoužívá datovou sadu registrovanou v Azure Machine Learning. Místo toho se načte při spuštění *Train.py* . Po zobrazení výzvy k vytvoření odkazu na data pro váš školicí běh zadejte do příkazového řádku "n" a stiskněte klávesu **ENTER**.
1. Stiskněte klávesu **ENTER** a procházejte soubor skriptu, který chcete spustit na výpočetním prostředí. V tomto případě skript pro výuku modelu je `train.py` soubor v `vscode-tools-for-ai/mnist-vscode-docs-sample` adresáři.

    Soubor s názvem `MNIST-rc.runconfig` se zobrazí v vs Code s obsahem podobným následujícímu:

    ```json
    {
        "script": "train.py",
        "arguments": [],
        "framework": "Python",
        "communicator": "None",
        "target": "TeamWkspc-com",
        "environment": {
            "name": "MNIST-env",
            "version": "1",
            "python": {
                "interpreterPath": "python",
                "userManagedDependencies": false,
                "condaDependencies": {
                    "name": "vs-code-azure-ml-tutorial",
                    "channels": [
                        "defaults"
                    ],
                    "dependencies": [
                        "python=3.6.2",
                        "tensorflow=1.15.0",
                        "pip",
                        {
                            "pip": [
                                "azureml-defaults"
                            ]
                        }
                    ]
                },
                "baseCondaEnvironment": null
            },
            "environmentVariables": {},
            "docker": {
                "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
                "baseDockerfile": null,
                "baseImageRegistry": {
                    "address": null,
                    "username": null,
                    "password": null
                },
                "enabled": false,
                "arguments": []
            },
            "spark": {
                "repositories": [],
                "packages": [],
                "precachePackages": true
            },
            "inferencingStackVersion": null
        },
        "history": {
            "outputCollection": true,
            "snapshotProject": false,
            "directoriesToWatch": [
                "logs"
            ]
        }
    }
    ```

1. Až budete s konfigurací spokojeni, uložte ji tak, že otevřete paletu příkazů a zadáte následující příkaz:

    ```text
    Azure ML: Save and Continue
    ```

`MNIST-rc`Konfigurace spuštění se přidá do výpočetního uzlu *TeamWkspc-com* a v `MNIST-env` uzlu *prostředí* se přidá konfigurace prostředí.

## <a name="train-the-model"></a>Trénování modelu

Během procesu školení se vytvoří model TensorFlow zpracováním školicích dat a vzorů učení, které jsou v něm vložené, pro každou z příslušných číslic, které jsou klasifikovány. 

Spuštění Azure Machine Learning experimentu:

1. Na řádku Visual Studio Code aktivity vyberte ikonu **Azure** . Zobrazí se zobrazení Azure Machine Learning. 
1. Rozbalte uzel předplatného. 
1. Rozbalte uzel **TeamWorkspace > experimenty** . 
1. Klikněte pravým tlačítkem na **mnist ručně zapsaných** experiment.
1. Vyberte možnost **Spustit experiment**.

    > [!div class="mx-imgBorder"]
    > ![Spuštění experimentu](./media/tutorial-train-deploy-image-classification-model-vscode/run-experiment.png)

1. V seznamu možností cíle služby COMPUTE vyberte cíl výpočetního prostředí **TeamWkspc-com** .
1. Pak vyberte konfiguraci spuštění **mnist ručně zapsaných-RC** .
1. V tuto chvíli se do Azure pošle žádost, aby se váš experiment spouštěl na vybraném výpočetním cíli ve vašem pracovním prostoru. Tento proces trvá několik minut. Doba, po kterou se spustí školicí úkol, bude mít vliv na několik faktorů, jako je výpočetní typ a velikost dat školení. Pokud chcete sledovat průběh experimentu, klikněte pravým tlačítkem na aktuální uzel spuštění a vyberte **Zobrazit spustit v Azure Portal**.
1. Když se zobrazí dialogové okno požadující otevření externího webu, vyberte **otevřít**.

    > [!div class="mx-imgBorder"]
    > ![Sledovat pokrok experimentu](./media/tutorial-train-deploy-image-classification-model-vscode/track-experiment-progress.png)

Po dokončení přípravy modelu se popisek stavu vedle uzlu spustit aktualizuje na dokončeno.

## <a name="register-the-model"></a>Registrace modelu

Teď, když jste proškole svůj model, ho můžete zaregistrovat ve svém pracovním prostoru. 

Postup registrace modelu:

1. Na řádku Visual Studio Code aktivity vyberte ikonu **Azure** . Zobrazí se zobrazení Azure Machine Learning.
1. Rozbalte uzel předplatného. 
1. Rozbalte uzel **TeamWorkspace > experimenty > mnist ručně zapsaných** .
1. Získá výstupy modelu generované z školení modelu. Klikněte pravým tlačítkem na uzel spustit **1** a vyberte **Stáhnout výstupy**. 

    > [!div class="mx-imgBorder"]
    > ![Stáhnout výstupy modelu](./media/tutorial-train-deploy-image-classification-model-vscode/download-outputs.png)

1. Vyberte adresář, do kterého se mají stažené výstupy Uložit. Ve výchozím nastavení se výstupy nacházejí v adresáři aktuálně otevřeném v Visual Studio Code.
1. Klikněte pravým tlačítkem na uzel **modely** a vyberte **Registrovat model**.

    > [!div class="mx-imgBorder"]
    > ![Registrace modelu](./media/tutorial-train-deploy-image-classification-model-vscode/register-model.png)

1. Pojmenujte model "MNIST ručně zapsaných-TensorFlow-model" a stiskněte klávesu **ENTER**.
1. Model TensorFlow se skládá z několika souborů. V seznamu možností vyberte **složku modelu** jako formát cesty modelu. 
1. Vyberte `azureml_outputs/Run_1/outputs/outputs/model` adresář.

    Soubor obsahující vaše konfigurace modelů se zobrazí v Visual Studio Code s podobným obsahem v následujícím seznamu:

    ```json
    {
        "modelName": "MNIST-TensorFlow-model",
        "tags": {
            "": ""
        },
        "modelPath": "c:\\Dev\\vscode-tools-for-ai\\mnist-vscode-docs-sample\\azureml_outputs\\Run_1\\outputs\\outputs\\model",
        "description": ""
    }
    ```

1. Až budete s konfigurací spokojeni, uložte ji tak, že otevřete paletu příkazů a zadáte následující příkaz:

    ```text
    Azure ML: Save and Continue
    ```

Po několika minutách se model zobrazí pod uzlem *modely* .

## <a name="deploy-the-model"></a>Nasazení modelu

V Visual Studio Code můžete model nasadit jako webovou službu pro:

+ Azure Container Instances (ACI).
+ Služba Azure Kubernetes (AKS).

Nemusíte vytvářet kontejner ACI k testování předem, protože kontejnery ACI se vytvářejí podle potřeby. Je ale potřeba nakonfigurovat clustery AKS předem. Další informace o možnostech nasazení najdete v tématu [nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md) .

Nasazení webové služby jako ACI:

1. Na řádku Visual Studio Code aktivity vyberte ikonu **Azure** . Zobrazí se zobrazení Azure Machine Learning.
1. Rozbalte uzel předplatného. 
1. Rozbalte uzel **TeamWorkspace > modely** . 
1. Klikněte pravým tlačítkem na **mnist ručně zapsaných-TensorFlow-model** a vyberte **nasadit službu z registrovaného modelu**.

    > [!div class="mx-imgBorder"]
    > ![Nasazení modelu](./media/tutorial-train-deploy-image-classification-model-vscode/deploy-model.png)

1. Vyberte **Azure Container Instances**.
1. Pojmenujte službu "mnist ručně zapsaných-tensorflow-SVC" a stiskněte klávesu **ENTER**.
1. Vyberte skript, který chcete spustit v kontejneru stisknutím klávesy **ENTER** ve vstupním poli a procházením `score.py` souboru v `mnist-vscode-docs-sample` adresáři.
1. Zadejte závislosti potřebné ke spuštění skriptu stisknutím klávesy **ENTER** ve vstupním poli a procházením `env.yml` souboru v `mnist-vscode-docs-sample` adresáři.

    Soubor obsahující vaše konfigurace modelů se zobrazí v Visual Studio Code s podobným obsahem v následujícím seznamu:

    ```json
    {
        "name": "mnist-tensorflow-svc",
        "imageConfig": {
            "runtime": "python",
            "executionScript": "score.py",
            "dockerFile": null,
            "condaFile": "env.yml",
            "dependencies": [],
            "schemaFile": null,
            "enableGpu": false,
            "description": ""
        },
        "deploymentConfig": {
            "cpu_cores": 1,
            "memory_gb": 10,
            "tags": {
                "": ""
            },
            "description": ""
        },
        "deploymentType": "ACI",
        "modelIds": [
            "MNIST-TensorFlow-model:1"
        ]
    }
    ```

1. Až budete s konfigurací spokojeni, uložte ji tak, že otevřete paletu příkazů a zadáte následující příkaz:

    ```text
    Azure ML: Save and Continue
    ```

V tuto chvíli se k nasazení webové služby pošle žádost do Azure. Tento proces trvá několik minut. Po nasazení se nová služba zobrazí v uzlu *koncové body* .

## <a name="next-steps"></a>Další kroky

* Návod, jak naučit se Azure Machine Learning mimo Visual Studio Code, najdete v tématu [kurz: výuka modelů pomocí Azure Machine Learning](tutorial-train-models-with-aml.md).
* Návod, jak upravovat, spouštět a ladit kód místně, najdete v [kurzu Python Hello World](https://code.visualstudio.com/docs/Python/Python-tutorial).