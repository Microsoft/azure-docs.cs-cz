---
title: Použití Visual Studio Code pro Machine Learning
titleSuffix: Azure Machine Learning
description: Naučte se, jak nainstalovat Azure Machine Learning pro Visual Studio Code a vytvořit jednoduchý experiment v Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: ronglu
author: ronglums
ms.date: 7/12/2019
ms.custom: seodec18
ms.openlocfilehash: 0507080a390a4bb9f981b0d911961337e9b2c62a
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997176"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Začínáme s Azure Machine Learning pro Visual Studio Code

V tomto článku se dozvíte, jak používat rozšíření Azure Machine Learning pro Visual Studio Code ke školení a nasazení modelů pro strojové učení a hloubkové učení.

[Azure Machine Learning](overview-what-is-azure-ml.md) poskytuje podporu pro experimenty spouštěné místně a na vzdálených výpočetních cílech. Každý experimentu je můžete udržovat přehled o různých spuštění často potřebujete zavádět postupně vyzkoušet s různými technikami, hyperparameters a další. Azure Machine Learning slouží ke sledování vlastní metriky a experimentovat spuštění, aktivace reprodukovatelnost datové vědy a umožňuje audity.

Tyto modely můžete nasadit i pro potřeby testování a produkce.

## <a name="prerequisites"></a>Požadavky

+ Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

+ Visual Studio Code musí být nainstalované. Visual Studio Code je jednoduchý, ale výkonný editor zdrojového kódu, který běží na vašem počítači. Obsahuje integrovanou podporu Pythonu a dalších programovacích jazyků. Pokud jste ještě nenainstalovali Visual Studio Code, přečtěte si, [jak](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Nainstalujte Python 3,5 nebo novější](https://www.anaconda.com/download/).


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>Nainstalovat rozšíření pro Azure Machine Learning Visual Studio Code

Když nainstalujete rozšíření Azure Machine Learning, automaticky se nainstalují dvě další rozšíření (Pokud máte přístup k Internetu). Jedná se o [rozšíření účtu Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) a [rozšíření Python společnosti Microsoft](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

Chcete-li pracovat s Azure Machine Learning, je nutné zapnout Visual Studio Code integrované vývojové prostředí (IDE) Python. Pro použití [Pythonu v Visual Studio Code](https://code.visualstudio.com/docs/languages/python)potřebujete rozšíření Microsoft Python. Toto rozšíření se automaticky nainstaluje s rozšířením Azure Machine Learning. Rozšíření zajišťuje Visual Studio Code skvělého integrovaného vývojového prostředí (IDE) a funguje na jakémkoli operačním systému s celou řadou překladačů Pythonu. Rozšíření Microsoft Python používá celou sílu Visual Studio Code k poskytování funkcí automatického dokončování, IntelliSense, linting, ladění a testování částí. Rozšíření také umožňuje snadno přepínat mezi prostředími Python, včetně virtuálních a conda prostředí. Další informace o úpravách, spouštění a ladění kódu v Pythonu najdete v [kurzu pro Python Hello World](https://code.visualstudio.com/docs/python/python-tutorial).

Instalace rozšíření Azure Machine Learning:

1. Otevřete Visual Studio Code.

1. Ve webovém prohlížeči přejdete na [Azure Machine Learning for Visual Studio Code Extension (Preview)](https://aka.ms/vscodetoolsforai).

1. Na této webové stránce vyberte **nainstalovat**. 

1. Na kartě rozšíření vyberte **instalovat**.

1. Karta Vítejte pro rozšíření se otevře v Visual Studio Code a symbol Azure (zobrazený červeně na následujícím snímku obrazovky) se přidá do řádku aktivity.

   ![Ikona Azure na řádku Visual Studio Code aktivity](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. V dialogovém okně vyberte **Přihlásit** se a postupujte podle výzev k ověření pomocí Azure. 
   
   Rozšíření účtu Azure, které se nainstalovalo spolu s rozšířením Azure Machine Learning for Visual Studio Code, vám pomůže s vaším účtem Azure ověřit. Seznam příkazů najdete na stránce pro [rozšíření účtu Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Podívejte se na [rozšíření IntelliCode pro Visual Studio Code (Preview)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode poskytuje sadu funkcí s podporou AI pro technologii IntelliSense v Pythonu, jako je odvození nejvíce relevantního automatického dokončování na základě aktuálního kontextu kódu.

## <a name="install-the-azure-machine-learning-sdk"></a>Instalace sady Azure Machine Learning SDK

1. Ujistěte se, že je nainstalována Python 3,5 nebo novější a že je rozpoznána nástrojem Visual Studio Code. Pokud ji nainstalujete nyní, restartujte Visual Studio Code a [Vyberte interpret Pythonu](https://code.visualstudio.com/docs/python/python-tutorial).

1. V okně integrovaného terminálu zadejte interpret Pythonu, který se má použít. Nebo vyberte zadat pro použití výchozího interpretu Pythonu.

   ![Výběr interpretu](./media/vscode-tools-for-ai/python.png)

1. V pravém dolním rohu okna se zobrazí oznámení oznamující, že se automaticky instaluje [sada Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) . Nově vytvořené prostředí Pythonu je místní a soukromé a má Visual Studio Code předpoklady pro práci s Azure Machine Learning.

   ![Instalace sady Azure Machine Learning SDK pro Python](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Začínáme se službou Azure Machine Learning

Před zahájením školení a nasazení modelů strojového učení v Visual Studio Code musíte vytvořit [pracovní prostor Azure Machine Learning](concept-workspace.md) v cloudu. Tento pracovní prostor bude obsahovat vaše modely a prostředky. 

Vytvoření pracovního prostoru a přidání prvního experimentu:

1. Na řádku Visual Studio Code aktivity vyberte ikonu Azure. Zobrazí se postranní panel Azure Machine Learning.

   [![Vytvořit pracovní prostor](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Klikněte pravým tlačítkem na předplatné Azure a vyberte **vytvořit pracovní prostor**. Zobrazí se seznam. V příkladu animovaného obrázku je název odběru **bezplatný zkušební verze**a pracovní prostor je **TeamWorkspace**. 

1. Vyberte skupinu prostředků ze seznamu nebo vytvořte novou pomocí Průvodce v paletě příkazů.

1. Do pole zadejte jedinečný a jasný název nového pracovního prostoru. V příkladu obrázku má pracovní prostor název **TeamWorkspace**.

1. Vyberte ENTER a vytvořte nový pracovní prostor. Ve stromové struktuře se zobrazí pod názvem předplatného.

1. Klikněte pravým tlačítkem na uzel **experiment** a v místní nabídce vyberte možnost **vytvořit experiment** .  Experimenty sledují vaše běhy pomocí Azure Machine Learning.

1. Do pole zadejte název experimentu. V ukázkových snímcích obrazovky se experiment jmenuje **mnist ručně zapsaných**.
 
1. Vyberte ENTER a vytvořte nový experiment. Experiment se zobrazí ve stromové struktuře pod názvem pracovního prostoru.

1. V pracovním prostoru můžete kliknout pravým tlačítkem na experiment a nastavit ho jako **aktivní** experiment. **Aktivní** experiment je vaším aktuálním experimentem. Otevřená složka v Visual Studio Code bude propojena s tímto experimentem v cloudu. Tato složka by měla obsahovat vaše místní skripty Pythonu.

Nyní budou vaše klíčové metriky uloženy v historii experimentů. Podobně se modely, které vytvoříte, budou automaticky nahrány do Azure Machine Learning a ukládají se společně se svými metrikami a protokoly experimentů. 

[![Připojit složku v Visual Studio Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>Vytvoření a Správa cílových výpočetních prostředí

Díky Azure Machine Learning pro Visual Studio Code můžete připravit vaše data, naučit modely a nasazovat je místně i na vzdálené výpočetní cíle.

Rozšíření podporuje několik vzdálených výpočetních cílů pro Azure Machine Learning. Další informace najdete v úplném seznamu podporovaných výpočetních [cílů pro Azure Machine Learning](how-to-set-up-training-targets.md).

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Vytváření výpočetních cílů pro Azure Machine Learning v Visual Studio Code

Postup vytvoření cíle výpočtů:

1. Na řádku Visual Studio Code aktivity vyberte ikonu Azure. Zobrazí se postranní panel Azure Machine Learning.

2. Ve stromovém zobrazení rozbalte předplatné Azure a pracovní prostor Azure Machine Learning. V následujícím příkladu obrázku je název odběru **bezplatný zkušební verze**a pracovní prostor je **TeamWorkspace**. 

3. Pod uzlem pracovního prostoru, klikněte pravým tlačítkem na **Compute** uzlu a zvolte **vytvořit výpočetní**.

4. Zvolte typ cílové výpočetní ze seznamu. 

5. Na paletě příkazů vyberte velikost virtuálního počítače.

6. V paletě příkazů zadejte do pole název pro cíl výpočtů. 

7. V konfiguračním souboru JSON, který se otevře na nové kartě, zadejte všechny rozšířené vlastnosti. Můžete určit vlastnosti, jako je třeba maximální počet uzlů.

8. Po dokončení konfigurace služby COMPUTE Target klikněte v pravém dolním rohu okna na **Odeslat**.

Tady je příklad vytvoření Azure Machine Learning COMPUTE (AMLCompute):

[![Vytvoření AML COMPUTE v Visual Studio Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>Konfigurační soubor spuštění

Rozšíření Visual Studio Code automaticky vytvoří místní cíl výpočtů a konfigurace spuštění pro místní a Dock prostředí v místním počítači. Konfigurační soubory spouštění najdete v rámci přidruženého cílového uzlu Compute. 

## <a name="train-and-tune-models"></a>Trénování a optimalizaci modelů

Pomocí Azure Machine Learning for Visual Studio Code (Preview) můžete rychle iterovat v kódu, krokovat a ladit a používat řešení pro správu zdrojového kódu. 

Chcete-li experiment spustit místně pomocí Azure Machine Learning:

1. Na řádku Visual Studio Code aktivity vyberte ikonu Azure. Zobrazí se postranní panel Azure Machine Learning.

1. Ve stromovém zobrazení rozbalte předplatné Azure a pracovní prostor Azure Machine Learning. 

1. Pod uzlem pracovní prostor rozbalte **výpočetní** uzel a klikněte pravým tlačítkem na **konfiguraci spuštění** výpočetního prostředí, které chcete použít. 

1. Vyberte **spuštění experimentu**.

1. V Průzkumníku souborů vyberte skript, který chcete spustit. 

1. Vyberte **Zobrazit experimenty** . zobrazí se integrovaný Azure Machine Learning portál, ve kterém se budou monitorovat vaše běhy a zobrazit vaše vyškolené modely.

Tady je příklad, jak spustit experiment místně:

[![Místní spuštění experimentu](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>Použití vzdálených výpočtů při experimentech v Visual Studio Code

Chcete-li použít vzdálený výpočetní cíl pro školení, je nutné vytvořit konfigurační soubor spuštění. Tento soubor informuje Azure Machine Learning nejen kde spustit experiment, ale také jak připravit prostředí.

#### <a name="the-conda-dependencies-file"></a>Souboru závislostí systému conda

Ve výchozím nastavení se pro vás vytvoří nové prostředí conda a vaše závislosti při instalaci se spravují. V souboru *aml_config/conda_dependencies. yml* je však nutné zadat závislosti a jejich verze. 

Následující fragment kódu z výchozí *aml_config/conda_dependencies. yml* Určuje `tensorflow=1.12.0`. Pokud nezadáte verzi závislosti, použije se nejnovější verze. Můžete přidat další závislosti v konfiguračním souboru.

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

Spuštění experimentu s Azure Machine Learning ve vzdáleném cílovém výpočetním prostředí:

1. Na řádku Visual Studio Code aktivity vyberte ikonu Azure. Zobrazí se postranní panel Azure Machine Learning.

1. Ve stromovém zobrazení rozbalte předplatné Azure a pracovní prostor Azure Machine Learning. 

1. V okně Editoru klikněte pravým tlačítkem na skript Python a vyberte **AML: Spouštějte jako experiment v Azure**. 

1. Na paletě příkazů vyberte cíl výpočtů. 

1. V paletě příkazů zadejte do pole název konfigurace spuštění. 

1. Upravte soubor *conda_dependencies. yml* a určete závislosti za běhu experimentu. Pak v pravém dolním rohu okna vyberte **Odeslat**. 

1. Vyberte **Zobrazit experimenty** . zobrazí se integrovaný Azure Machine Learning portál, ve kterém se budou monitorovat vaše běhy a zobrazit vaše vyškolené modely.

Tady je příklad, jak spustit experiment na vzdáleném cílovém výpočetním prostředí:

[![Spuštění experimentu na vzdáleném cíli](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Nasazení a správě modelů
V Azure Machine Learning můžete nasadit a spravovat modely strojového učení v cloudu i na hraničních zařízeních. 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Zaregistrujte si model pro Azure Machine Learning z Visual Studio Code

Teď, když jste proškole svůj model, ho můžete zaregistrovat ve svém pracovním prostoru. Můžete sledovat a nasazovat registrované modely.

Postup registrace modelu:

1. Na řádku Visual Studio Code aktivity vyberte ikonu Azure. Zobrazí se postranní panel Azure Machine Learning.

1. Ve stromovém zobrazení rozbalte předplatné Azure a pracovní prostor Azure Machine Learning.

1. Pod uzlem pracovního prostoru, klikněte pravým tlačítkem na **modely** a zvolte **zaregistrujte Model**.

1. V paletě příkazů zadejte do pole název modelu. 

1. V seznamu vyberte, zda chcete nahrát **soubor modelu** (pro jednotlivé modely) nebo **složku modelu** (pro modely s více soubory, například TensorFlow). 

1. Vyberte složky nebo souboru.

1. Po dokončení konfigurace vlastností modelu klikněte v pravém dolním rohu okna na **Odeslat**. 

Tady je příklad, jak zaregistrovat model pro Azure Machine Learning:

[![Registrace modelu do AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>Nasazení služby z Visual Studio Code

V Visual Studio Code můžete nasadit webovou službu do:
+ Azure Container Instances (ACI) pro testování.
+ Služba Azure Kubernetes (AKS) pro produkční prostředí.

Nemusíte vytvářet kontejner ACI k testování předem, protože se vytvářejí kontejnery ACI za běhu. Je ale potřeba nakonfigurovat clustery AKS předem. Další informace najdete v tématu [nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

Nasazení webové služby:

1. Na řádku Visual Studio Code aktivity vyberte ikonu Azure. Zobrazí se postranní panel Azure Machine Learning.

1. Ve stromovém zobrazení rozbalte své předplatné Azure a pracovní prostor Azure Machine Learning.

1. V části pracovní prostor uzlu, rozbalte položku **modely** uzlu.

1. Klikněte pravým tlačítkem na model, který chcete nasadit, a v místní nabídce vyberte **nasadit službu z registrovaného modelu** .

1. Na paletě příkazů vyberte cíl výpočtů, na který chcete provést nasazení. 

1. V paletě příkazů zadejte do pole název této služby.  

1. Na paletě příkazů vyberte klávesu ENTER na klávesnici a vyhledejte a vyberte soubor skriptu.

1. Na paletě příkazů vyberte klávesu ENTER na klávesnici a vyhledejte a vyberte soubor závislosti conda.

1. Po dokončení konfigurace vlastností služby v pravém dolním rohu okna vyberte **Odeslat** a nasaďte. V souboru vlastností služby můžete zadat místní soubor Docker nebo soubor Schema. JSON.

Webová služba je momentálně nasazené.

Tady je příklad, jak nasadit webovou službu:

[![Nasazení webové služby](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Použití klávesových zkratek

K přístupu k funkcím Azure Machine Learning v Visual Studio Code můžete použít klávesnici. Nejdůležitější klávesové zkratky, které je třeba znát, je Ctrl + Shift + P, která zobrazuje paletu příkazů. Z palety příkazů máte přístup ke všem funkcím Visual Studio Code, včetně klávesových zkratek pro nejběžnější operace.

[![Klávesové zkratky pro Azure Machine Learning Visual Studio Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Další postup

* Návod, jak vyškolit Azure Machine Learning mimo Visual Studio Code, najdete v tématu [kurz: Výukové modely pomocí](tutorial-train-models-with-aml.md)Azure Machine Learning.
* Návod, jak upravovat, spouštět a ladit kód místně, najdete v [kurzu Python Hello World](https://code.visualstudio.com/docs/python/python-tutorial).
