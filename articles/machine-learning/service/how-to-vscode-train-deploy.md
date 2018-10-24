---
title: Pomocí nástroje Visual Studio Code pro AI rozšíření pomocí služby Azure Machine Learning
description: Další informace o Visual Studio Code Tools pro AI a jak spustit trénovací a nasazení strojového učení a hloubkového učení modely se službou Azure Machine Learning v nástroji VS Code.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.reviewer: jmartens
ms.date: 10/1/2018
ms.openlocfilehash: 6372aada8c3d380f8d3cefb0479e45bbd7c63898
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945242"
---
# <a name="vs-code-tools-for-ai-train-and-deploy-ml-models-from-vs-code"></a>Nástroje VS Code pro AI: trénování a nasazovat modely ML z VS Code
V tomto článku se dozvíte, jak používat **nástroje VS Code pro AI** rozšíření trénovat a nasazovat strojového učení a hloubkového učení modely se službou Azure Machine Learning v nástroji VS Code.

Azure Machine Learning poskytuje podporu pro spouštění experimentů místně a v cílových výpočetních prostředí vzdálené. Každý experimentu je můžete udržovat přehled o různých spuštění často potřebujete zavádět postupně vyzkoušet s různými technikami, hyperparameters a další. Azure Machine Learning slouží ke sledování vlastní metriky a experimentovat spuštění, aktivace reprodukovatelnost datové vědy a umožňuje audity.

A můžete je nasadit tyto modely pro vaše potřeby testovacím nebo produkčním prostředí.

## <a name="prerequisites"></a>Požadavky

+ [Máte nástroje VS Code pro AI](how-to-vscode-tools.md) nastavenou službu Azure Machine Learning.

+ Máte [Azure Machine Learning SDK pro Python nainstalován](how-to-vscode-tools.md) s VS Code.

+ Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-and-manage-compute-targets"></a>Vytvoření a Správa cílových výpočetních prostředí

S Visual Studio Code Tools pro AI připravit data, natrénovat modely a je nasadit místně i v cílových výpočetních prostředí vzdálené.

Toto rozšíření podporuje několik cílových výpočetních prostředí různé vzdálené pro Azure Machine Learning. Zobrazit [úplný seznam podporovaných výpočetních cíle](how-to-set-up-training-targets.md) pro Azure Machine Learning.

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>Vytvoření cílových výpočetních prostředí pro Azure Machine Learning v nástroji VS Code

**Chcete-li vytvořit cílové výpočetní prostředí:**

1. Klepněte na ikonu na panelu aplikace Visual Studio Code aktivit Azure. Azure: Machine Learning postranního panelu se zobrazí.

2. Ve stromovém zobrazení rozbalte vašeho předplatného Azure a pracovních prostorů služby Azure Machine Learning. Animovaný obrázek název předplatného je "OpenMind Studio" a pracovní prostor je "MyWorkspace". 

3. Pod uzlem pracovního prostoru, klikněte pravým tlačítkem na **Compute** uzlu a zvolte **vytvořit výpočetní**.

4. Zvolte typ cílové výpočetní ze seznamu. 

5. V poli zadejte jedinečný název této cílové výpočetní prostředí a určete velikost virtuálního počítače.

6. Všechny upřesňující vlastnosti zadejte v konfiguračním souboru JSON, které se otevře na nové záložce. 

7. Po dokončení konfigurace vaší cílové výpočetní prostředí, klikněte na tlačítko **Dokončit** v vpravo dole.

Tady je příklad pro Azure Batch AI: [ ![výpočetních prostředků vytvořit Azure Batch AI ve VS Code](./media/vscode-tools-for-ai/createcompute.gif)](./media/vscode-tools-for-ai/createcompute.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>Použití vzdálené výpočetní prostředí pro experimentů v nástroji VS Code

Použití cílové výpočetní prostředí vzdálené při cvičení, budete muset vytvořit konfigurační soubor spuštění. Tento soubor informuje Azure Machine Learning nejen kde spustit experiment, ale také jak připravit prostředí.

#### <a name="the-run-configuration-file"></a>Soubor "Konfigurace spuštění.

Rozšíření VS Code automaticky vytvoří konfigurace spuštění pro vaše **místní** a **docker** prostředí v místním počítači.

To je fragment z výchozí spuštění konfiguračního souboru.

Pokud chcete instalovat všechny knihovny a závislosti sami, nastavte `userManagedDependencies: True` a poté spuštění experimentu místní použije vaše výchozí prostředí Pythonu podle rozšíření VS Code Python.

```yaml
# user_managed_dependencies=True indicates that the environment will be user managed. False indicates that AzureML will manage the user environment.
    userManagedDependencies: False
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

#### <a name="the-conda-dependencies-file"></a>Souboru závislostí systému conda

Ve výchozím nastavení vytvoří se pro vás nové prostředí conda a vaše instalace závislosti se spravují. Nicméně je nutné zadat v závislosti `aml_config/conda_dependencies.yml` souboru.

To je fragment z výchozí aml_config/conda_dependencies.yml.
Můžete přidat další závislosti v konfiguračním souboru.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

## <a name="train-and-tune-models"></a>Trénování a optimalizaci modelů

Pomocí služby Azure Machine Learning z VS Code rychle iterovat kódu, procházení a ladění a použít řešení řízení zdrojového kódu podle výběru. 

**Spuštění experimentu s Azure Machine Learning:**

1. Klepněte na ikonu na panelu aplikace Visual Studio Code aktivit Azure. Azure: Machine Learning postranního panelu se zobrazí.

1. Ve stromovém zobrazení rozbalte vašeho předplatného Azure a pracovních prostorů služby Azure Machine Learning. Animovaný obrázek název předplatného je "OpenMind Studio" a pracovní prostor je "MyWorkspace". 

1. Pod uzlem pracovního prostoru, rozbalte **Compute** uzlu a kliknutím pravým tlačítkem **konfigurace spuštění** výpočetního výkonu, kterou chcete použít. 

1. Vyberte **spuštění experimentu**.

1. Klikněte na tlačítko **zobrazení spuštění experimentu** zobrazíte na portálu Azure Machine Learning integrované monitorování vašeho spuštění a zobrazte trénované modely.

   [![Spuštění experimentu služby machine learning z VS Code](./media/vscode-tools-for-ai/runexperiment.gif)](./media/vscode-tools-for-ai/runexperiment.gif#lightbox)

## <a name="deploy-and-manage-models"></a>Nasazení a správě modelů
Služba Azure Machine Learning umožňuje nasazení a správě vašich modelů strojového učení v cloudu i na hraničních zařízeních. 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>Zaregistrujte model Azure Machine Learning z VS Code

Teď, když máte Trénink modelu, můžete ho zaregistrovat ve vašem pracovním prostoru.
Registrované modely můžete sledovat a nasadit.

**Postup pro registraci modelu:**

1. Klepněte na ikonu na panelu aplikace Visual Studio Code aktivit Azure. Azure: Machine Learning postranního panelu se zobrazí.

1. Ve stromovém zobrazení rozbalte vašeho předplatného Azure a pracovních prostorů služby Azure Machine Learning.

1. Pod uzlem pracovního prostoru, klikněte pravým tlačítkem na **modely** a zvolte **zaregistrujte Model**.

1. Ze seznamu, vyberte, zda chcete nahrát **souboru modelu** (pro jeden modely) **modelu složky** (pro modely s více soubory, jako je Tensorflow). 

1. Pomocí dialogového okna Výběr souborů vyberte požadovaný soubor nebo složku.

   [![COMPUTE](./media/vscode-tools-for-ai/registermodel.gif)](./media/vscode-tools-for-ai/registermodel.gif#lightbox)

> [!Warning]
> Prozatím odeberte ze souboru json vygenerované značky.

### <a name="deploy-your-service-from-vs-code"></a>Nasazení služby z VS Code

Použití VS Code, můžete nasadit webovou službu, která:
+ Azure Container Instance (ACI): pro účely testování
+ Azure Kubernetes Service (AKS): pro produkční prostředí 

Není nutné vytvořit kontejner služby ACI otestovat předem, protože se vytvářejí v reálném čase. Clustery AKS však nutné předem nakonfigurovat. 

Další informace o [nasazení pomocí Azure Machine Learning](how-to-deploy-and-where.md) obecně.

**Nasazení webové služby:**

1. Klepněte na ikonu na panelu aplikace Visual Studio Code aktivit Azure. Azure: Machine Learning postranního panelu se zobrazí.

1. Ve stromovém zobrazení rozbalte vaše předplatné Azure a váš pracovní prostor služby Azure Machine Learning.

1. V části pracovní prostor uzlu, rozbalte položku **modely** uzlu.

1. Klikněte pravým tlačítkem na model, který chcete nasadit a zvolte **nasadit službu z modelu zaregistrovaný** příkazu v místní nabídce.

1. V paletu příkazů VS Code vyberte cílové výpočetní prostředí, do které chcete nasadit ze seznamu. 

1. Do pole zadejte název pro tuto službu. 

1. V dialogovém okně vyberte v pravém dolním rohu klikněte na tlačítko **Procházet** a vyberte hodnoticí skript. Dialogové okno se zavře.

1. Pokud máte místní soubor Dockeru, klikněte na tlačítko **Procházet** v dialogovém okně druhý, který se zobrazí. 

   Je-li zrušit dialogových oken a nechcete zadat místní soubor Docker, "Azure Machine Learning" jeden se používá ve výchozím nastavení.

1. V třetím dialogovém okně, které se zobrazí, klikněte na tlačítko **Procházet** a cesta k souboru místního systému conda vybrat nebo zadat cesta k souboru v editoru json později.

1. Pokud máte soubor schema.json chcete použít, klikněte na tlačítko **Procházet** v dialogovém okně čtvrtý, který se zobrazí a vyberte soubor.

Webová služba je momentálně nasazené.

Tady je příklad pro Instance kontejneru Azure: [ ![Instance kontejneru Azure z VS Code](./media/vscode-tools-for-ai/deploy.gif)](./media/vscode-tools-for-ai/deploy.gif#lightbox)

## <a name="next-steps"></a>Další postup

Návod, jak školení s Machine learningem mimo VS Code, přečtěte si [kurz: trénování modelů Azure Machine Learning](tutorial-train-models-with-aml.md).

Návod, jak úprav, spouštění a ladění kódu místně, najdete v článku [kurz Pythonu Hello World](https://code.visualstudio.com/docs/python/python-tutorial)
