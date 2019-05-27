---
title: Použití nástroje Visual Studio Code pro machine learning
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
ms.openlocfilehash: 70f9c34957b977aff9fc6211bf79415ed9abe255
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2019
ms.locfileid: "66016510"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Začínáme s Azure Machine Learning pro Visual Studio Code

V tomto článku se dozvíte, jak používat rozšíření pro Azure Machine Learning pro Visual Studio Code k trénování a strojové učení a hloubkového učení modely nasazení.

[Služby Azure Machine Learning](overview-what-is-azure-ml.md) poskytuje podporu pro experimenty, které spouštíte místně a ve vzdáleném cílových výpočetních prostředí. Každý experimentu je můžete udržovat přehled o různých spuštění často potřebujete zavádět postupně vyzkoušet s různými technikami, hyperparameters a další. Azure Machine Learning slouží ke sledování vlastní metriky a experimentovat spuštění, aktivace reprodukovatelnost datové vědy a umožňuje audity.

Můžete také nasadit tyto modely pro vaše potřeby testovacím nebo produkčním prostředí.

## <a name="prerequisites"></a>Požadavky

+ Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree).

+ Musí být nainstalované Visual Studio Code. Visual Studio Code je zdroj jednoduchý, ale výkonný editor kódu, který běží na vaší ploše. Obsahuje integrovanou podporu pro Python a jiných programovacích jazycích. Pokud jste ještě nenainstalovali aplikaci Visual Studio Code, [zjistěte, jak](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Nainstalujte Python 3.5 nebo novější](https://www.anaconda.com/download/).


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>Instalace rozšíření pro službu Azure Machine Learning pro Visual Studio Code

Při instalaci rozšíření Azure Machine Learning dvě další rozšíření jsou automaticky nainstalovány (Pokud máte přístup k Internetu). Jsou to [rozšíření Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) a [rozšíření Python pro Microsoft](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

Pro práci s Azure Machine Learning, budete muset zapnout Python integrované vývojové prostředí (IDE) Visual Studio Code. Je nutné použít rozšíření Python pro Microsoft [Pythonu ve Visual Studio Code](https://code.visualstudio.com/docs/languages/python). Toto rozšíření je automaticky nainstalována s rozšířením Azure Machine Learning. Rozšíření Visual Studio Code umožňuje být ideální integrované vývojové prostředí a funguje pro všechny operační systémy s řadou interpretů Pythonu. Rozšíření Python pro Microsoft používá všechny funkce služby Visual Studio Code pro zajištění automatického dokončování IntelliSense, linting, ladění a testování částí. Toto rozšíření také umožňuje snadno přepínat mezi prostředími Pythonu, včetně virtuální a prostředí conda. Další informace o úpravách, spouštění a ladění kódu v Pythonu, najdete v článku [Pythonu pro tento kurz hello-world](https://code.visualstudio.com/docs/python/python-tutorial).

Chcete-li nainstalovat rozšíření Azure Machine Learning:

1. Otevřete Visual Studio Code.

1. Ve webovém prohlížeči přejděte na [Azure Machine Learning pro rozšíření Visual Studio Code (preview)](https://aka.ms/vscodetoolsforai).

1. Na dané webové stránce vyberte **nainstalovat**. 

1. Na kartě rozšíření, vyberte **nainstalovat**.

1. Úvodní kartu pro rozšíření se otevře ve Visual Studio Code a Azure symbol (červeně na následujícím snímku obrazovky) se přidá do panelu aktivit.

   ![Azure ikonu na panelu aktivit Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. V dialogovém okně vyberte **Sign In** a postupujte podle výzev k ověření pomocí Azure. 
   
   Rozšíření Azure Account, která byla nainstalována spolu s Azure Machine Learning pro rozšíření Visual Studio Code, umožňuje ověření pomocí účtu Azure. Seznam příkazů, najdete na stránce [rozšíření Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Podívejte se [IntelliCode rozšíření pro Visual Studio Code (preview)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode poskytuje sadu funkcí s asistencí AI pro IntelliSense v jazyce Python, jako je například odvození nejrelevantnější autocompletions podle aktuální kontext kódu.

## <a name="install-the-azure-machine-learning-sdk"></a>Nainstalujte aplikaci Azure Machine Learning SDK

1. Ujistěte se, že je nainstalován Python 3.5 nebo novější a je rozpoznáván Visual Studio Code. Pokud se vám jeho instalaci, restartujte Visual Studio Code a [vyberte interpret Pythonu](https://code.visualstudio.com/docs/python/python-tutorial).

1. V okně integrovaného terminálu zadejte určený interpret Pythonu. Nebo stisknutím klávesy Enter používat vaše výchozí překladač Pythonu.

   ![Vyberte interpret](./media/vscode-tools-for-ai/python.png)

1. V pravém dolním rohu okna, zobrazí se upozornění, což indikuje, že [SDK služby Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) se instaluje automaticky. Nově vytvořený prostředí Pythonu je místní a privátní a má požadavky Visual Studio Code pro práci se službou Azure Machine Learning.

   ![Nainstalujte aplikaci Azure Machine Learning sady SDK pro Python](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Začínáme se službou Azure Machine Learning

Před zahájením školení a nasazení modelů strojového učení ve Visual Studio Code, je potřeba vytvořit [pracovního prostoru služby Azure Machine Learning](concept-workspace.md) v cloudu. Tento pracovní prostor bude obsahovat vaše modely a prostředky. 

Vytvoření pracovního prostoru a přidat váš první experiment s využitím:

1. Na panelu aktivit Visual Studio Code vyberte ikonu Azure. Zobrazí se na bočním panelu Azure Machine Learning.

   [![Vytvoření pracovního prostoru](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Klikněte pravým tlačítkem na vaše předplatné Azure a vyberte **vytvořit pracovní prostor**. Zobrazí se seznam. Animovaný obrázek příkladu je název předplatného **bezplatnou zkušební verzi**, a je pracovní prostor **TeamWorkspace**. 

1. Ze seznamu vyberte skupinu prostředků nebo vytvořte novou pomocí Průvodce v paletu příkazů.

1. V poli zadejte jedinečný a jasný název pro nový pracovní prostor. Obrázek příkladu má název pracovního prostoru **TeamWorkspace**.

1. Stiskněte klávesu Enter, chcete-li vytvořit nový pracovní prostor. Zobrazí se ve stromu níže název předplatného.

1. Klikněte pravým tlačítkem myši **Experiment** uzlu a zvolte **vytvoření experimentu** v místní nabídce.  Experimenty udržovat přehled o spuštěních pomocí Azure Machine Learning.

1. Do pole zadejte název svého experimentu. V příkladu snímky obrazovky, je název experimentu **mnist ručně**.
 
1. Stiskněte klávesu Enter a vytvořte nový experiment. Experimentu se zobrazí ve stromu níže název pracovního prostoru.

1. V pracovním prostoru, kliknete pravým tlačítkem ji jako experiment **aktivní** experimentovat. **Aktivní** experimentu je aktuální experimentu. Vaše otevřít složku v aplikaci Visual Studio Code se propojí s tohoto experimentu v cloudu. Tato složka by měla obsahovat vaše místní skriptů Pythonu.

Nyní klíčové metriky se uloží v rámci experimentu historie. Podobně modelů, které tréninku budou automaticky odeslán do Azure Machine Learning a uloženy experiment metriky a protokoly. 

[![Připojit do složky ve Visual Studio Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>Vytvoření a Správa cílových výpočetních prostředí

Pomocí služby Azure Machine Learning pro Visual Studio Code Příprava dat, trénování modelů a jejich nasazení, místně i v cílových výpočetních prostředí vzdálené.

Rozšíření podporuje několik cílových výpočetních prostředí vzdálené pro Azure Machine Learning. Další informace najdete v tématu úplný seznam podporovaných [cílových výpočetních prostředí pro Azure Machine Learning](how-to-set-up-training-targets.md).

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Vytvoření cílových výpočetních prostředí pro Azure Machine Learning ve Visual Studio Code

Chcete-li vytvořit cílové výpočetní prostředí:

1. Na panelu aktivit Visual Studio Code vyberte ikonu Azure. Zobrazí se na bočním panelu Azure Machine Learning.

2. Ve stromovém zobrazení rozbalte vašeho předplatného Azure a pracovních prostorů služby Azure Machine Learning. V následujícím obrázku je název předplatného **bezplatnou zkušební verzi**, a je pracovní prostor **TeamWorkspace**. 

3. Pod uzlem pracovního prostoru, klikněte pravým tlačítkem na **Compute** uzlu a zvolte **vytvořit výpočetní**.

4. Zvolte typ cílové výpočetní ze seznamu. 

5. Na paletu příkazů vyberte velikost virtuálního počítače.

6. Na paletu příkazů do pole zadejte název cílového výpočetního prostředí. 

7. V konfiguračním souboru JSON, které se otevře na nové záložce upřesněte vlastnosti. Můžete zadat vlastnosti, například maximálního počtu uzlů.

8. Po dokončení konfigurace vašeho cílového výpočetního prostředí v pravém dolním rohu okna, vyberte **odeslat**.

Tady je příklad toho, jak vytvořit výpočetní Azure Machine Learning (AMLCompute):

[![Vytvořte výpočetní AML ve Visual Studio Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>Spuštění konfiguračního souboru

Rozšíření Visual Studio Code automaticky vytvoří cílové místní výpočetní prostředí a spuštění konfigurace pro vaše prostředí místní verzi a docker v místním počítači. Můžete najít soubory do konfigurace spuštění přidružených výpočetních cílový uzel. 

## <a name="train-and-tune-models"></a>Trénování a optimalizaci modelů

Pomocí služby Azure Machine Learning pro Visual Studio Code (preview) rychle iterovat kódu, procházení a ladění a použít řešení pro správu zdrojového kódu. 

Spuštění experimentu místně pomocí Azure Machine Learning:

1. Na panelu aktivit Visual Studio Code vyberte ikonu Azure. Zobrazí se na bočním panelu Azure Machine Learning.

1. Ve stromovém zobrazení rozbalte vašeho předplatného Azure a pracovních prostorů služby Azure Machine Learning. 

1. Pod uzlem pracovního prostoru, rozbalte **Compute** uzlu a kliknutím pravým tlačítkem **konfigurace spuštění** výpočetního výkonu, kterou chcete použít. 

1. Vyberte **spuštění experimentu**.

1. V Průzkumníku souborů vyberte skript, který chcete spustit. 

1. Vyberte **zobrazení spuštění experimentu** zobrazíte na portálu Azure Machine Learning integrované monitorování vašeho spuštění a zobrazte trénované modely.

Tady je příklad toho, jak spustit experiment místně:

[![Místní spuštění experimentu](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>Použití vzdálené výpočetní prostředí pro experimenty v aplikaci Visual Studio Code

K použití cílové vzdálené výpočetní prostředí pro školení, budete muset vytvořit konfigurační soubor spuštění. Tento soubor informuje Azure Machine Learning nejen kde spustit experiment, ale také jak připravit prostředí.

#### <a name="the-conda-dependencies-file"></a>Souboru závislostí systému conda

Ve výchozím nastavení vytvoří se pro vás nové prostředí conda a vaše instalace závislosti se spravují. Ale musíte zadat závislostí a jejich verze *aml_config/conda_dependencies.yml* souboru. 

Následující fragment kódu z výchozího *aml_config/conda_dependencies.yml* Určuje `tensorflow=1.12.0`. Pokud neurčíte verzi závislost, použije se nejnovější verze. Můžete přidat další závislosti v konfiguračním souboru.

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

Ke spuštění experimentu s Azure Machine Learning na vzdálené cílové výpočetní prostředí:

1. Na panelu aktivit Visual Studio Code vyberte ikonu Azure. Zobrazí se na bočním panelu Azure Machine Learning.

1. Ve stromovém zobrazení rozbalte vašeho předplatného Azure a pracovních prostorů služby Azure Machine Learning. 

1. V okně editoru klikněte pravým tlačítkem na váš skript Python a vyberte **AML: Spustit jako Experiment v Azure**. 

1. Na paletu příkazů vyberte cílové výpočetní prostředí. 

1. Na paletu příkazů zadejte v poli Název konfigurace spuštění. 

1. Upravit *conda_dependencies.yml* soubor k určení experiment závislosti modulu runtime. V pravém dolním rohu okna, vyberte **odeslat**. 

1. Vyberte **zobrazení spuštění experimentu** zobrazíte na portálu Azure Machine Learning integrované monitorování vašeho spuštění a zobrazte trénované modely.

Tady je příklad toho, jak spustit experiment na cílové vzdálené výpočetní prostředí:

[![Spusťte experiment na vzdálené cílové](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Nasazení a správě modelů
V Azure Machine Learning můžete nasadit a spravovat vaše modely strojového učení v cloudu i na hraničních zařízeních. 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Zaregistrujte model Azure Machine Learning z Visual Studio Code

Teď, když jsme natrénovali model, můžete ho zaregistrovat ve vašem pracovním prostoru. Můžete sledovat a registrované modely nasazení.

Postup pro registraci modelu:

1. Na panelu aktivit Visual Studio Code vyberte ikonu Azure. Zobrazí se na bočním panelu Azure Machine Learning.

1. Ve stromovém zobrazení rozbalte vašeho předplatného Azure a pracovních prostorů služby Azure Machine Learning.

1. Pod uzlem pracovního prostoru, klikněte pravým tlačítkem na **modely** a zvolte **zaregistrujte Model**.

1. Na paletu příkazů zadejte v poli Název modelu. 

1. Ze seznamu, vyberte, jestli se má nahrát **souboru modelu** (pro jeden modely) nebo **modelu složky** (pro modely s více soubory, jako je TensorFlow). 

1. Vyberte složky nebo souboru.

1. Po dokončení konfigurace vlastnosti modelu, v pravém dolním rohu okna, vyberte **odeslat**. 

Tady je příklad toho, jak zaregistrovat váš model Azure Machine Learning:

[![Registrace modelu AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>Nasazení služby z Visual Studio Code

Ve Visual Studio Code můžete nasadit webovou službu, která:
+ Azure Container Instances (ACI) pro účely testování.
+ Azure Kubernetes Service (AKS) pro produkční prostředí.

Není nutné vytvořit kontejner služby ACI v předstihu, testování, protože kontejnerů ACI jsou vytvořeny v reálném čase. Však nutné předem nakonfigurovat clustery AKS. Další informace najdete v tématu [nasazujte modely pomocí služby Azure Machine Learning](how-to-deploy-and-where.md).

Nasazení webové služby:

1. Na panelu aktivit Visual Studio Code vyberte ikonu Azure. Zobrazí se na bočním panelu Azure Machine Learning.

1. Ve stromovém zobrazení rozbalte vaše předplatné Azure a váš pracovní prostor služby Azure Machine Learning.

1. V části pracovní prostor uzlu, rozbalte položku **modely** uzlu.

1. Klikněte pravým tlačítkem na model, kterou chcete nasadit a vyberte **nasadit službu z modelu zaregistrovaný** v místní nabídce.

1. Na paletu příkazů zvolte cílové výpočetní prostředí, které chcete nasadit. 

1. Na paletu příkazů do pole zadejte název pro tuto službu.  

1. Na paletu příkazů stisknutím klávesy Enter na klávesnici a procházet a vyberte soubor skriptu.

1. Na paletu příkazů stisknutím klávesy Enter na klávesnici a procházet a vyberte soubor závislosti systému conda.

1. Po dokončení konfigurace vlastností vaší služby v pravém dolním rohu okna, vyberte **odeslat** k nasazení. V souboru vlastnosti služby můžete zadat místní docker soubor nebo soubor schema.json.

Webová služba je momentálně nasazené.

Tady je příklad toho, jak nasadit webovou službu:

[![Nasazení webové služby](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Používání klávesových zkratek

Klávesnice můžete použít pro přístup k funkcím Azure Machine Learning ve Visual Studio Code. Nejdůležitější klávesové zkratky znát je kombinace kláves Ctrl + Shift + P, který zobrazuje paletu příkazů. Z palety příkazů máte přístup ke všem funkci Visual Studio Code, včetně klávesové zkratky pro většinu běžných operací.

[![Klávesové zkratky pro Azure Machine Learning pro Visual Studio Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Další postup

* Návod, jak trénovat službou Azure Machine Learning, mimo Visual Studio Code najdete v části [kurzu: Trénování modelů Azure Machine Learning](tutorial-train-models-with-aml.md).
* Návod, jak úprava, spouštění a ladění kódu místně, najdete v článku [Pythonu pro tento kurz hello-world](https://code.visualstudio.com/docs/python/python-tutorial).
