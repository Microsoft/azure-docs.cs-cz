---
title: Trénování a nasazovat modely z VS Code
titleSuffix: Azure Machine Learning service
description: Přečtěte si o Azure Machine Learning pro Visual Studio Code a jak spustit trénovací a nasazení strojového učení a hloubkového učení modelů ve službě Azure Machine Learning pomocí nástroje Visual Studio Code.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 409d1bb30dc956bb19e9a37212d93990d1401e7b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55240110"
---
# <a name="use-visual-studio-code-to-train-and-deploy-machine-learning-models"></a>Použití Visual Studio Code pro trénování a nasazovat modely machine learningu

V tomto článku se dozvíte, jak používat **Azure Machine Learning pro Visual Studio Code** rozšíření trénovat a nasazovat strojového učení a hloubkového učení modely se službou Azure Machine Learning v aplikaci Visual Studio Code (VS Code).

Azure Machine Learning poskytuje podporu pro spouštění experimentů místně a v cílových výpočetních prostředí vzdálené. Každý experimentu je můžete udržovat přehled o různých spuštění často potřebujete zavádět postupně vyzkoušet s různými technikami, hyperparameters a další. Azure Machine Learning slouží ke sledování vlastní metriky a experimentovat spuštění, aktivace reprodukovatelnost datové vědy a umožňuje audity.

A můžete je nasadit tyto modely pro vaše potřeby testovacím nebo produkčním prostředí.

## <a name="prerequisites"></a>Požadavky

+ Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](http://aka.ms/AMLFree) ještě dnes.

+ Máte [Azure Machine Learning pro VS Code](how-to-vscode-tools.md) nastavit rozšíření.

+ Máte [Azure Machine Learning SDK pro Python nainstalován](how-to-vscode-tools.md) s VS Code.

## <a name="create-and-manage-compute-targets"></a>Vytvoření a Správa cílových výpočetních prostředí

Pomocí služby Azure Machine Learning pro VS Code Příprava dat, trénování modelů a jejich nasazení, místně i v cílových výpočetních prostředí vzdálené.

Toto rozšíření podporuje několik cílových výpočetních prostředí různé vzdálené pro Azure Machine Learning. Zobrazit [úplný seznam podporovaných výpočetních cíle](how-to-set-up-training-targets.md) pro Azure Machine Learning.

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>Vytvoření cílových výpočetních prostředí pro Azure Machine Learning v nástroji VS Code

**Chcete-li vytvořit cílové výpočetní prostředí:**

1. Klepněte na ikonu na panelu aplikace Visual Studio Code aktivit Azure. Zobrazí se na bočním panelu Azure Machine Learning.

2. Ve stromovém zobrazení rozbalte vašeho předplatného Azure a pracovních prostorů služby Azure Machine Learning. Animovaný obrázek název předplatného je bezplatná zkušební verze a pracovní prostor je "TeamWorkspace".

3. Pod uzlem pracovního prostoru, klikněte pravým tlačítkem na **Compute** uzlu a zvolte **vytvořit výpočetní**.

4. Zvolte typ cílové výpočetní ze seznamu.

5. V paletu příkazů vyberte velikost virtuálního počítače.

6. V paletu příkazů zadejte název cílového výpočetního prostředí v poli.

7. Všechny upřesňující vlastnosti zadejte v konfiguračním souboru JSON, které se otevře na nové záložce. Můžete zadat vlastnosti, jako je počet maximální uzlů...

8. Po dokončení konfigurace vaší cílové výpočetní prostředí, klikněte na tlačítko **odeslat** v pravém dolním rohu obrazovky.

Tady je příklad pro vytvoření Azure Machine Learning Compute (AMLCompute): [![Vytvořte výpočetní AML ve VS Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>Soubor "Konfigurace spuštění.

Rozšíření VS Code automaticky vytvoří cílové místní výpočetní prostředí a konfigurace pro spuštění vašeho **místní** a **docker** prostředí v místním počítači. Konfigurace spuštění souborů najdete v části cílové výpočetní přidružené prostředí.

To je fragment z výchozí místní spuštění konfiguračního souboru. Ve výchozím nastavení `userManagedDependencies: True` tak musíte nainstalovat všechny knihovny a závislosti sami a poté spuštění experimentu místní použije vaše výchozí prostředí Pythonu podle rozšíření VS Code Python.

```yaml
# user_managed_dependencies = True indicates that the environment will be user managed. False indicates that Azure Machine Learning service will manage the user environment.
    userManagedDependencies: True
# The python interpreter path
    interpreterPath: python
# Path to the conda dependencies file to use for this run. If a project
# contains multiple programs with different sets of dependencies, it may be
# convenient to manage those environments with separate files.
    condaDependenciesFile: aml_config/conda_dependencies.yml
# Docker details
    docker:
# Set True to perform this run inside a Docker container.
    enabled: false
```

## <a name="train-and-tune-models"></a>Trénování a optimalizaci modelů

Pomocí služby Azure Machine Learning pro VS Code (Preview) rychle iterovat kódu, procházení a ladění a použít řešení řízení zdrojového kódu podle výběru.

**Spuštění experimentu místně pomocí služby Azure Machine Learning:**

1. Klepněte na ikonu na panelu aplikace Visual Studio Code aktivit Azure. Zobrazí se na bočním panelu Azure Machine Learning.

1. Ve stromovém zobrazení rozbalte vašeho předplatného Azure a pracovních prostorů služby Azure Machine Learning.

1. Pod uzlem pracovního prostoru, rozbalte **Compute** uzel a klikněte pravým tlačítkem na **spustit Config** výpočetního výkonu, kterou chcete použít.

1. Vyberte **spuštění experimentu**.

1. Vyberte skript, který chcete spustit z Průzkumníka souborů.

1. Klikněte na tlačítko **zobrazení spuštění experimentu** zobrazíte na portálu Azure Machine Learning integrované monitorování vašeho spuštění a zobrazte trénované modely.

Tady je příklad experimentu místně spuštěná: [![Místně spuštěná experimentu](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>Použití vzdálené výpočetní prostředí pro experimentů v nástroji VS Code

Použití cílové výpočetní prostředí vzdálené při cvičení, budete muset vytvořit konfigurační soubor spuštění. Tento soubor informuje Azure Machine Learning nejen kde spustit experiment, ale také jak připravit prostředí.

#### <a name="the-conda-dependencies-file"></a>Souboru závislostí systému conda

Ve výchozím nastavení vytvoří se pro vás nové prostředí conda a vaše instalace závislosti se spravují. Nicméně je nutné zadat v závislosti `aml_config/conda_dependencies.yml` souboru.

To je fragment z výchozí aml_config/conda_dependencies.yml.
Můžete přidat další závislosti v konfiguračním souboru.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure Machine Learning service only supports 3.5.2 and later.

- python=3.6.2
- tensorflow

- pip:
    # Required packages for Azure Machine Learning service execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

**Ke spuštění experimentu s Azure Machine Learning na vzdálené cílové výpočetní prostředí:**

1. Klepněte na ikonu na panelu aplikace Visual Studio Code aktivit Azure. Zobrazí se na bočním panelu Azure Machine Learning.

1. Ve stromovém zobrazení rozbalte vašeho předplatného Azure a pracovních prostorů služby Azure Machine Learning.

1. Klikněte pravým tlačítkem na váš skript python v okně editoru a vyberte **AML: Spustit jako Experiment v Azure**.

1. V paletu příkazů vyberte cílové výpočetní prostředí.

1. Paleta příkazů zadejte název konfigurace spuštění v poli.

1. Upravte soubor conda_dependencies.yml určit závislosti modulu runtime experiment a pak klikněte na **odeslat** v pravém dolním rohu obrazovky.

1. Klikněte na tlačítko **zobrazení spuštění experimentu** zobrazíte na portálu Azure Machine Learning integrované monitorování vašeho spuštění a zobrazte trénované modely.

Tady je příklad pro spuštění experimentu v cílové vzdálené výpočetní prostředí: [![Spuštění experimentu na vzdálené cílové](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Nasazení a správě modelů
Služba Azure Machine Learning umožňuje nasazení a správě vašich modelů strojového učení v cloudu i na hraničních zařízeních.

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>Zaregistrujte model Azure Machine Learning z VS Code

Teď, když máte Trénink modelu, můžete ho zaregistrovat ve vašem pracovním prostoru.
Registrované modely můžete sledovat a nasadit.

**Postup pro registraci modelu:**

1. Klepněte na ikonu na panelu aplikace Visual Studio Code aktivit Azure. Zobrazí se na bočním panelu Azure Machine Learning.

1. Ve stromovém zobrazení rozbalte vašeho předplatného Azure a pracovních prostorů služby Azure Machine Learning.

1. Pod uzlem pracovního prostoru, klikněte pravým tlačítkem na **modely** a zvolte **zaregistrujte Model**.

1. Do palety příkazů zadejte název pole do modelu.

1. Ze seznamu, vyberte, zda chcete nahrát **souboru modelu** (pro jeden modely) **modelu složky** (pro modely s více soubory, jako je Tensorflow).

1. Vyberte složky nebo souboru.

1. Po dokončení konfigurace vlastností váš model, klikněte na tlačítko **odeslat** v pravém dolním rohu obrazovky.

Tady je příklad pro registraci modelu k AML: [![Registrace modelu AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-vs-code"></a>Nasazení služby z VS Code

Použití VS Code, můžete nasadit webovou službu, která:
+ Azure Container Instance (ACI): pro účely testování
+ Azure Kubernetes Service (AKS): pro produkční prostředí

Není nutné vytvořit kontejner služby ACI otestovat předem, protože se vytvářejí v reálném čase. Clustery AKS však nutné předem nakonfigurovat.

Další informace o [nasazení pomocí Azure Machine Learning](how-to-deploy-and-where.md) obecně.

**Nasazení webové služby:**

1. Klepněte na ikonu na panelu aplikace Visual Studio Code aktivit Azure. Zobrazí se na bočním panelu Azure Machine Learning.

1. Ve stromovém zobrazení rozbalte vaše předplatné Azure a váš pracovní prostor služby Azure Machine Learning.

1. V části pracovní prostor uzlu, rozbalte položku **modely** uzlu.

1. Klikněte pravým tlačítkem na model, který chcete nasadit a zvolte **nasadit službu z modelu zaregistrovaný** příkazu v místní nabídce.

1. V paletu příkazů zvolte cílové výpočetní prostředí, do které chcete nasadit ze seznamu.

1. V paletu příkazů zadejte název pro tuto službu v poli.

1. V paletu příkazů stisknutím klávesy Enter na klávesnici a procházet a vybrat soubor skriptu.

1. Paleta příkazů stisknutím klávesy Enter na klávesnici a procházet a vybrat souboru závislostí systému conda.

1. Po dokončení konfigurace vlastností vaší služby, klikněte na tlačítko **odeslat** v pravém dolním rohu obrazovky pro nasazení. V tomto souboru vlastnosti služby můžete zadat místní soubor Docker nebo schema.json soubor, který chcete použít.

Webová služba je momentálně nasazené.

Tady je příklad pro nasazení webové služby: [![Nasazení webové služby](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

## <a name="next-steps"></a>Další postup

Návod, jak školení s Machine learningem mimo VS Code, přečtěte si [kurzu: Trénování modelů Azure Machine Learning](tutorial-train-models-with-aml.md).

Návod, jak úprav, spouštění a ladění kódu místně, najdete v článku [kurz Pythonu Hello World](https://code.visualstudio.com/docs/python/python-tutorial)
