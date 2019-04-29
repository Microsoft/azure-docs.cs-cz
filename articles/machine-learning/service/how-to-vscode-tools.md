---
title: Použijte Visual Studio Code s
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak nainstalovat Azure Machine Learning pro Visual Studio Code a vytvoření jednoduchého experimentu v Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: bbeb0dbbd5e9c919eda4b298dc5bee31965e9bac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60818638"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Začínáme s Azure Machine Learning pro Visual Studio Code

V tomto článku se dozvíte, jak používat **Azure Machine Learning pro Visual Studio Code** rozšíření trénovat a nasazovat strojového učení a hloubkového učení modely se službou Azure Machine Learning v aplikaci Visual Studio Code (VS Code).

Služba Azure Machine Learning poskytuje podporu pro spouštění experimentů místně a v cílových výpočetních prostředí vzdálené. Každý experimentu je můžete udržovat přehled o různých spuštění často potřebujete zavádět postupně vyzkoušet s různými technikami, hyperparameters a další. Azure Machine Learning slouží ke sledování vlastní metriky a experimentovat spuštění, aktivace reprodukovatelnost datové vědy a umožňuje audity.

A můžete je nasadit tyto modely pro vaše potřeby testovacím nebo produkčním prostředí.

## <a name="prerequisites"></a>Požadavky

+ Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

+ Musí být nainstalované Visual Studio Code. VS Code je zdroj jednoduchý, ale výkonný editor kódu, který běží na vaší ploše. Obsahuje integrovanou podporu pro Python a další.  [Informace o instalaci VS Code](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Nainstalujte Python 3.5 nebo vyšší](https://www.anaconda.com/download/).


## <a name="install-the-azure-machine-learning-for-vs-code-extension"></a>Instalace Azure Machine Learning pro rozšíření VS Code

Při instalaci **Azure Machine Learning** rozšíření, dvě další rozšíření jsou automaticky nainstalovány (Pokud máte přístup k Internetu). Jsou [účet Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) rozšíření a [Python pro Microsoft](https://marketplace.visualstudio.com/items?itemName=ms-python.python) rozšíření

Pro práci s Azure Machine Learning, musíme proměnit Python IDE VS Code. Práce s [Pythonu ve Visual Studio Code](https://code.visualstudio.com/docs/languages/python), vyžaduje rozšíření Python pro Microsoft, které se automaticky nainstaluje s rozšířením Azure Machine Learning. Rozšíření je vynikající integrované vývojové prostředí VS Code a funguje pro všechny operační systémy s širokou škálu interpretů Pythonu. Využívá všechny VS Code výkon k poskytování automatické dokončování a technologie IntelliSense, linting, ladění a testování, spolu se schopností snadno přepínat mezi prostředími Pythonu, včetně virtuální a prostředí conda. Přečtěte si tento návod, jak úpravy, spuštění a ladění kódu v Pythonu, najdete v článku [kurz Pythonu Hello World](https://code.visualstudio.com/docs/python/python-tutorial)

**Chcete-li nainstalovat rozšíření Azure Machine Learning:**

1. Spusťte VS Code.

1. V prohlížeči navštivte: [Azure Machine Learning pro Visual Studio Code (Preview)](https://aka.ms/vscodetoolsforai) rozšíření

1. V této webové stránce klikněte na tlačítko **nainstalovat**. 

1. Na kartě rozšíření, klikněte na tlačítko **nainstalovat**.

1. Vítejte v kartě se otevře v nástroji VS Code pro rozšíření a symbol Azure (popsaných červeným rámečkem na obrázku níže) se přidá na panel aktivity.

   ![Azure ikonu na panelu aktivit Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. V dialogovém okně klikněte na tlačítko **Sign In** a postupujte podle pokynů na obrazovce výzva k ověření pomocí Azure. 
   
   Rozšíření Azure Account, která byla nainstalována spolu s Azure Machine Learning pro rozšíření VS Code, umožňuje ověření pomocí účtu Azure. Zobrazit seznam příkazů v [rozšíření Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) stránky.

> [!Tip] 
> Podívejte se [IntelliCode rozšíření pro VS Code (preview)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode poskytuje sadu funkcí s asistencí AI pro IntelliSense v jazyce Python, jako je například odvození nejrelevantnější auto dokončování podle aktuální kontext kódu.

## <a name="azure-ml-sdk-installation"></a>Instalace sady SDK Azure ML

1. Ujistěte se, že je nainstalovaná a rozpoznaná službou VS Code Python 3.5 nebo vyšší. Pokud vám jeho instalaci, pak znovu spusťte VS Code a vyberte interpret Pythonu pomocí pokynů na adrese https://code.visualstudio.com/docs/python/python-tutorial.

1. V okně integrovaného terminálu zadejte určený interpret Pythonu nebo mohli dostat **Enter** určený interpret Pythonu vaše výchozí.

   ![Vyberte interpret](./media/vscode-tools-for-ai/python.png)

1. V pravém dolním rohu okna se zobrazí oznámení, označující, že sadě SDK Azure ML se automaticky instaluje.    Místní privátní prostředí Python se vytvoří, která má požadavky Visual Studio Code pro práci se službou Azure Machine Learning.

   ![nainstalovat sadu SDK Azure Machine Learning pro Python](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Začínáme se službou Azure Machine Learning

Před zahájením školení a nasazení modelů strojového učení pomocí VS Code, je potřeba vytvořit [pracovního prostoru služby Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace) v cloudu, které obsahují vaše modely a prostředky. Zjistěte, jak vytvořit a vytvoření prvního experimentu v daném pracovním prostoru.

1. Klepněte na ikonu na panelu aplikace Visual Studio Code aktivit Azure. Zobrazí se na bočním panelu Azure Machine Learning.

   [![Instalace](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Klikněte pravým tlačítkem na vaše předplatné Azure a vyberte **vytvořit pracovní prostor**. Zobrazí se seznam. Animovaný obrázek název předplatného je bezplatná zkušební verze a pracovní prostor je "TeamWorkspace". 

1. Ze seznamu vyberte existující skupinu prostředků nebo vytvořte novou pomocí Průvodce v paletu příkazů.

1. V poli zadejte jedinečný a jasný název pro nový pracovní prostor. Na snímcích obrazovky je pracovní prostor s názvem "TeamWorkspace".

1. Stiskněte enter a vytvoří se nový pracovní prostor. Zobrazí se ve stromu níže název předplatného.

1. Klikněte pravým tlačítkem na uzel Experiment a vyberte **vytvoření experimentu** v místní nabídce.  Experimenty udržovat přehled o spuštěních pomocí Azure Machine Learning.

1. Do pole zadejte název svého experimentu. Na snímcích obrazovky je experimentu s názvem "Mnist ručně".
 
1. Stiskněte enter a se vytvoří nový experiment. Zobrazí se ve stromu níže název pracovního prostoru.

1. Můžete kliknout pravým tlačítkem na Experiment v pracovním prostoru a vyberte "Nastavit jako aktivní Experiment". **"Aktivní"** experimentu je experiment teď a vaše otevřít složku v nástroji VS Code se propojí s tohoto experimentu v cloudu. Tato složka by měla obsahovat vaše místní skriptů Pythonu.

   Teď každá experimentu běží s experimentu, tak, aby všechny klíčové metriky se uloží v historii experimentů a modelů, které tréninku získat automaticky nahráli do Azure Machine Learning a ukládají s protokoly a metriky experimentu.

   [![Připojit do složky ve VS Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


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

7. Všechny upřesňující vlastnosti zadejte v konfiguračním souboru JSON, které se otevře na nové záložce. Můžete zadat vlastnosti, například maximálního počtu uzlů.

8. Po dokončení konfigurace vaší cílové výpočetní prostředí, klikněte na tlačítko **odeslat** v pravém dolním rohu obrazovky.

Tady je příklad pro vytvoření Azure Machine Learning Compute (AMLCompute): [![Vytvořte výpočetní AML ve VS Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>Soubor "Konfigurace spuštění.

Rozšíření VS Code automaticky vytvoří cílové místní výpočetní prostředí a konfigurace pro spuštění vašeho **místní** a **docker** prostředí v místním počítači. Konfigurace spuštění souborů najdete v části přidružených výpočetních cílový uzel. 

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

Ve výchozím nastavení vytvoří se pro vás nové prostředí conda a vaše instalace závislosti se spravují. Ale musíte zadat závislostí a jejich verze `aml_config/conda_dependencies.yml` souboru. 

To je fragment z výchozí aml_config/conda_dependencies.yml. Můžete například zadat "tensorflow = 1.12.0' jak vidíte níže. Pokud nezadáte verzi závislost, se používá nejnovější verzi.  
Můžete přidat další závislosti v konfiguračním souboru.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2
- tensorflow=1.12.0

- pip:
    # Required packages for AzureML execution, history, and data preparation.

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

### <a name="use-keyboard-shortcuts"></a>Používání klávesových zkratek

Oblíbili VS Code funkce Azure Machine Learning v nástroji VS Code jsou přístupná z klávesnice. Nejdůležitější kombinace kláves znát je kombinace kláves Ctrl + Shift + P, kterým se zobrazí paletu příkazů. Tady máte přístup ke všem funkcí VS Code, včetně klávesové zkratky pro většinu běžných operací.

[![Klávesové zkratky pro Azure Machine Learning pro VS Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Další postup

Návod, jak školení s Machine learningem mimo VS Code, přečtěte si [kurzu: Trénování modelů Azure Machine Learning](tutorial-train-models-with-aml.md).

Návod, jak úprav, spouštění a ladění kódu místně, najdete v článku [kurz Pythonu Hello World](https://code.visualstudio.com/docs/python/python-tutorial)
