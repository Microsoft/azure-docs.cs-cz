---
title: 'Kurz: Trénování a nasazování modelu pomocí rozšíření kódu sady Visual Studio'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak trénovat a nasazovat model klasifikace bitových obrázků pomocí TensorFlow a rozšíření kódu Azure Machine Learning Visual Studio
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 02/24/2020
ms.openlocfilehash: ba9cd2e7dc0248aa351cb7bc4519689763f1adda
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239879"
---
# <a name="train-and-deploy-an-image-classification-tensorflow-model-using-the-azure-machine-learning-visual-studio-code-extension"></a>Trénování a nasazování modelu TensorFlow klasifikace bitových obrázků pomocí rozšíření kódu Azure Machine Learning Visual Studio

Zjistěte, jak trénovat a nasazovat model klasifikace bitových obrázků k rozpoznání ručně psaných čísel pomocí TensorFlow a rozšíření kódu Azure Machine Learning Visual Studio.

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

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ho nemáte, zaregistrujte se a vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree).
- Nainstalujte [visual studio kód](https://code.visualstudio.com/docs/setup/setup-overview), lehký, cross-platformní editor kódu.
- Rozšíření kódu Visual Studia Azure Machine Learning Studio. Pokyny k instalaci najdete [v kurzu o rozšíření instalačního programu Visual Studio Visual Studio Pro nastavení Azure Machine Learning](./tutorial-setup-vscode-extension.md)

## <a name="understand-the-code"></a>Vysvětlení kódu

Kód pro tento kurz používá TensorFlow trénovat obraz klasifikace strojového učení model, který kategorizuje ručně psané číslice od 0-9. Činí tak vytvořením neuronové sítě, která bere hodnoty pixelů 28 px x 28 px obrazu jako vstup a výstupy seznam 10 pravděpodobností, jeden pro každou z číslic jsou klasifikovány. Níže je ukázka toho, jak data vypadají.  

![Číslice MNIST](./media/tutorial-train-deploy-image-classification-model-vscode/digits.png)

Získejte kód pro tento kurz stažením a rozepnutím [nástrojů kódu VS pro úložiště AI](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) kdekoli v počítači.

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

První věc, kterou musíte udělat pro vytvoření aplikace v Azure Machine Learning je vytvoření pracovního prostoru. Pracovní prostor obsahuje prostředky pro trénování modelů, stejně jako trénované modely samotné. Další informace naleznete [v tématu co je pracovní prostor](./concept-workspace.md). 

1. Na panelu aktivit kódu Visual Studia vyberte ikonu **Azure** a otevřete zobrazení Azure Machine Learning.
1. Klikněte pravým tlačítkem myši na předplatné Azure a vyberte **Vytvořit pracovní prostor**. 
    
    > [!div class="mx-imgBorder"]
    > ![Vytvoření pracovního prostoru](./media/tutorial-train-deploy-image-classification-model-vscode/create-workspace.png)

1. Ve výchozím nastavení je generován název obsahující datum a čas vytvoření. V textovém vstupním poli změňte název na "TeamWorkspace" a stiskněte **Enter**.
1. Vyberte **Možnost Vytvořit novou skupinu prostředků**. 
1. Pojmenujte skupinu prostředků "TeamWorkspace-rg" a stiskněte **Enter**. 
1. Zvolte umístění pracovního prostoru. Doporučujeme zvolit umístění, které je nejblíže umístění, které plánujete nasadit model. Například "Západní US 2".
1. Po zobrazení výzvy k výběru typu pracovního prostoru vyberte **základní a** vytvořte základní pracovní prostor. Další informace o různých nabídkách pracovního prostoru najdete v [tématu Přehled Azure Machine Learning](./overview-what-is-azure-ml.md#sku).

V tomto okamžiku se vytvoří požadavek na Azure, aby se ve vašem účtu vytvořil nový pracovní prostor. Po několika minutách se nový pracovní prostor zobrazí v uzlu předplatného. 

## <a name="create-an-experiment"></a>Vytvoření experimentu

Ve vašem pracovním prostoru lze vytvořit jeden nebo více experimentů pro sledování a analýzu jednotlivých trénovacích běhů jednotlivých modelů. Spuštění lze provést v cloudu Azure nebo na místním počítači.

1. Na panelu aktivit kódu Visual Studia vyberte ikonu **Azure.** Zobrazí se zobrazení Azure Machine Learning.
1. Rozbalte uzel předplatného.
1. Rozbalte uzel **TeamWorkspace.** 
1. Klikněte pravým tlačítkem myši na uzel **Experimenty.**
1. Z kontextové nabídky vyberte **Vytvořit experiment.**

    > [!div class="mx-imgBorder"]
    > ![Vytvoření experimentu](./media/tutorial-train-deploy-image-classification-model-vscode/create-experiment.png)

1. Pojmenujte experiment "MNIST" a stisknutím **klávesy Enter** vytvořte nový experiment. 

Stejně jako pracovní prostory, požadavek se odešle do Azure vytvořit experiment s poskytované konfigurace. Po několika minutách se nový experiment zobrazí v uzlu *Experimenty* pracovního prostoru. 

## <a name="configure-compute-targets"></a>Konfigurace výpočetních cílů

Výpočetní cíl je výpočetní prostředek nebo prostředí, kde můžete spouštět skripty a nasazovat trénované modely. Další informace najdete v dokumentaci k [výpočetním cílům Azure Machine Learning](./concept-compute-target.md).

Vytvoření výpočetního cíle:

1. Na panelu aktivit kódu Visual Studia vyberte ikonu **Azure.** Zobrazí se zobrazení Azure Machine Learning. 
1. Rozbalte uzel předplatného. 
1. Rozbalte uzel **TeamWorkspace.** 
1. V uzlu pracovního prostoru klikněte pravým tlačítkem myši na **výpočetní** uzel a zvolte **Vytvořit výpočetní výkon**. 

    > [!div class="mx-imgBorder"]
    > ![Vytvoření výpočetního cíle](./media/tutorial-train-deploy-image-classification-model-vscode/create-compute.png)

1. Vyberte **Azure Machine Learning Compute (AmlCompute).** Azure Machine Learning Compute je infrastruktura spravovaných výpočetních prostředků, která umožňuje uživateli snadno vytvořit jeden nebo více uzlů výpočetní chod, který lze použít s ostatními uživateli ve vašem pracovním prostoru.
1. Vyberte velikost virtuálního počítače. Ze seznamu možností vyberte **Standard_F2s_v2.** Velikost virtuálního počítače má vliv na dobu, kterou trvá trénování modelů. Další informace o velikostech virtuálních počítačů najdete v [tématu velikosti pro virtuální počítače s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).
1. Pojmenujte svůj výpočetní program TeamWkspc-com a stisknutím **klávesy Enter** vytvořte výpočetní prostředky.

Po několika minutách se nový výpočetní cíl zobrazí v *výpočetním* uzlu vašeho pracovního prostoru.

## <a name="create-a-run-configuration"></a>Vytvoření konfigurace spuštění

Když odešlete spuštění školení na cíl výpočetní ho diody, odešlete také konfiguraci potřebnou ke spuštění úlohy školení. Například skript, který obsahuje školicí kód a závislosti Pythonu potřebné ke spuštění.

Vytvoření konfigurace spuštění:

1. Na panelu aktivit kódu Visual Studia vyberte ikonu **Azure.** Zobrazí se zobrazení Azure Machine Learning. 
1. Rozbalte uzel předplatného. 
1. Rozbalte uzel **TeamWorkspace.** 
1. V uzlu pracovního prostoru klikněte pravým tlačítkem myši na výpočetní uzel **TeamWkspc-com** a zvolte **Vytvořit konfiguraci spuštění**.

    > [!div class="mx-imgBorder"]
    > ![Vytvoření konfigurace spuštění](./media/tutorial-train-deploy-image-classification-model-vscode/create-run-configuration.png)

1. Pojmenujte konfiguraci spuštění "MNIST-rc" a stisknutím **klávesy Enter** vytvořte konfiguraci spuštění.
1. Potom vyberte **TensorFlow jednouzelškolení** jako typ úlohy školení.
1. Stisknutím **klávesy Enter** procházejte soubor skriptu, který chcete spustit na výpočetních výkonech. V tomto případě skript trénovat model `train.py` je soubor `vscode-tools-for-ai/mnist-vscode-docs-sample` uvnitř adresáře.
1. Zadejte následující do vstupního pole a určete požadované balíčky.
    
    ```text
    pip: azureml-defaults; conda: python=3.6.2, tensorflow=1.15.0
    ```
    
    Soubor volaný `MNIST-rc.runconfig` se zobrazí v VS Code s obsahem podobným tomu níže:

    ```json
    {
        "script": "train.py",
        "framework": "Python",
        "communicator": "None",
        "target": "TeamWkspc-com",
        "environment": {
            "python": {
                "userManagedDependencies": false,
                "condaDependencies": {
                    "dependencies": [
                        "python=3.6.2",
                        "tensorflow=1.15.0",
                        {
                            "pip": [
                                "azureml-defaults"
                            ]
                        }
                    ]
                }
            },
            "docker": {
                "baseImage": "mcr.microsoft.com/azureml/base:0.2.4",
                "enabled": true,
                "baseImageRegistry": {
                    "address": null,
                    "username": null,
                    "password": null
                }
            }
        },
        "nodeCount": 1,
        "history": {
            "outputCollection": true,
            "snapshotProject": false,
            "directoriesToWatch": [
                "logs"
            ]
        }
    }
    ```

1. Pokud jste s konfigurací spokojeni, otevřete paletu příkazů výběrem **možnosti Zobrazit > paletu příkazů**.
1. Chcete-li uložit spuštěný konfigurační soubor, zadejte do palety příkazů následující příkaz.

    ```text
    Azure ML: Save and Continue
    ```

Konfigurace `MNIST-rc` spuštění je přidána pod výpočetní uzel *TeamWkspc-com.*

## <a name="train-the-model"></a>Trénování modelu

Během procesu školení je model TensorFlow vytvořen zpracováním trénovacích dat a vzorů učení, které jsou v něm vloženy pro každou z příslušných číslic, které jsou klasifikovány. 

Spuštění experimentu Azure Machine Learning:

1. Na panelu aktivit kódu Visual Studia vyberte ikonu **Azure.** Zobrazí se zobrazení Azure Machine Learning. 
1. Rozbalte uzel předplatného. 
1. Rozbalte uzel **TeamWorkspace > experimenty.** 
1. Klikněte pravým tlačítkem myši na experiment **MNIST.**
1. Vyberte **spustit experiment**.

    > [!div class="mx-imgBorder"]
    > ![Spuštění experimentu](./media/tutorial-train-deploy-image-classification-model-vscode/run-experiment.png)

1. Ze seznamu možností výpočetního cíle vyberte výpočetní cíl **TeamWkspc-com.**
1. Potom vyberte konfiguraci spuštění **MNIST-rc.**
1. V tomto okamžiku se do Azure odešle požadavek, aby se váš experiment spouštěl na vybraném výpočetním cíli ve vašem pracovním prostoru. Tento proces trvá několik minut. Doba spuštění úlohy školení je ovlivněna několika faktory, jako je typ výpočetního výkonu a velikost trénovacích dat. Chcete-li sledovat průběh experimentu, klikněte pravým tlačítkem myši na aktuální uzel spuštění a vyberte **zobrazit spustit na webu Azure Portal**.
1. Když se zobrazí dialogové okno požadující otevření externího webu, vyberte **Otevřít**.

    > [!div class="mx-imgBorder"]
    > ![Sledování průběhu experimentu](./media/tutorial-train-deploy-image-classification-model-vscode/track-experiment-progress.png)

Po dokončení školení modelu se popisek stavu vedle uzlu spuštění aktualizuje na "Dokončeno".

## <a name="register-the-model"></a>Registrace modelu

Nyní, když jste model vycvičili, můžete jej zaregistrovat ve svém pracovním prostoru. 

Registrace modelu:

1. Na panelu aktivit kódu Visual Studia vyberte ikonu **Azure.** Zobrazí se zobrazení Azure Machine Learning.
1. Rozbalte uzel předplatného. 
1. Rozbalte **teamworkspace > experimenty > uzlu MNIST.**
1. Získejte výstupy modelu generované z trénování modelu. Klepněte pravým tlačítkem myši na uzel **spuštění 1** a vyberte možnost **Stáhnout výstupy**. 

    > [!div class="mx-imgBorder"]
    > ![Stažení výstupů modelů](./media/tutorial-train-deploy-image-classification-model-vscode/download-outputs.png)

1. Zvolte adresář, do který chcete stažené výstupy uložit. Ve výchozím nastavení jsou výstupy umístěny v adresáři aktuálně otevřeném v kódu sady Visual Studio.
1. Klepněte pravým tlačítkem myši na uzel **Modely** a zvolte **Registrovat model**.

    > [!div class="mx-imgBorder"]
    > ![Registrace modelu](./media/tutorial-train-deploy-image-classification-model-vscode/register-model.png)

1. Pojmenujte model "MNIST-TensorFlow-model" a stiskněte **Enter**.
1. Model TensorFlow se skládá z několika souborů. Ze seznamu možností vyberte jako formát cesty modelu složku **Model.** 
1. Vyberte `azureml_outputs/Run_1/outputs/outputs/model` adresář.

    Soubor obsahující konfigurace modelu se zobrazí v kódu sady Visual Studio s podobným obsahem jako následující:

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

1. Jakmile budete s konfigurací spokojeni, uložte ji tak, že otevřete paletu příkazů a zadáte následující příkaz:

    ```text
    Azure ML: Save and Continue
    ```

Po několika minutách se model zobrazí pod uzěte *Modely.*

## <a name="deploy-the-model"></a>Nasazení modelu

V kódu Visual Studia můžete nasadit model jako webovou službu pro:

+ Instance kontejneru Azure (ACI).
+ Služba Azure Kubernetes (AKS).

Není nutné vytvořit kontejner ACI k testování předem, protože kontejnery ACI jsou vytvořeny podle potřeby. Je však nutné předem nakonfigurovat clustery AKS. Další informace o možnostech nasazení najdete v [tématu nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md) .

Nasazení webové služby jako Služby ACI :

1. Na panelu aktivit kódu Visual Studia vyberte ikonu **Azure.** Zobrazí se zobrazení Azure Machine Learning.
1. Rozbalte uzel předplatného. 
1. Rozbalte uzel **TeamWorkspace > Modely.** 
1. Klepněte pravým tlačítkem myši na **model MNIST-TensorFlow** a vyberte **možnost Nasadit službu z registrovaného modelu**.

    > [!div class="mx-imgBorder"]
    > ![Nasazení modelu](./media/tutorial-train-deploy-image-classification-model-vscode/register-model.png)

1. Vyberte **instance kontejneru Azure**.
1. Pojmenujte službu "mnist-tensorflow-svc" a stiskněte **enter**.
1. Vyberte skript, který chcete spustit v kontejneru, stisknutím `score.py` **klávesy Enter** ve vstupním poli a procházením souboru v adresáři. `mnist-vscode-docs-sample`
1. Zadejte závislosti potřebné ke spuštění skriptu stisknutím **klávesy** Enter `env.yml` ve `mnist-vscode-docs-sample` vstupním poli a procházením souboru v adresáři.

    Soubor obsahující konfigurace modelu se zobrazí v kódu sady Visual Studio s podobným obsahem jako následující:

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
1. Jakmile budete s konfigurací spokojeni, uložte ji tak, že otevřete paletu příkazů a zadáte následující příkaz:

    ```text
    Azure ML: Save and Continue
    ```

V tomto okamžiku se do Azure odešle požadavek na nasazení webové služby. Tento proces trvá několik minut. Po nasazení se nová služba zobrazí pod uzěte *Koncovými body.*

## <a name="next-steps"></a>Další kroky

* Návod, jak trénovat s Azure Machine Learning mimo Visual Studio Code, [najdete v tématu Kurz: Trénování modelů s Azure Machine Learning](tutorial-train-models-with-aml.md).
* Návod, jak upravit, spustit a ladit kód místně, naleznete v [Pythonu hello-world tutorial](https://code.visualstudio.com/docs/Python/Python-tutorial).

