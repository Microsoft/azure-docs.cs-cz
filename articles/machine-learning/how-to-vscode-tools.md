---
title: Azure Machine Learning v Visual Studio Code
titleSuffix: Azure Machine Learning
description: Naučte se, jak nainstalovat Azure Machine Learning pro Visual Studio Code a vytvořit experiment v Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jimgries
author: greazer
ms.date: 09/20/2019
ms.custom: seodec18
ms.openlocfilehash: d5843937233309e1b37f0127d364066a8a747e21
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75538238"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Začínáme s Azure Machine Learning pro Visual Studio Code

V tomto článku se dozvíte, jak používat **Azure Machine Learning pro Visual Studio Code** rozšíření pro výuku a nasazení modelů strojového učení.

[Azure Machine Learning](overview-what-is-azure-ml.md) zjednodušuje sestavování, školení a nasazení modelů strojového učení.
+ Pro účely školení poskytuje podporu pro spouštění experimentů místně nebo vzdáleně. Pro každý experiment můžete protokolovat vlastní metriky více spuštění pro doladění parametrů
+ Azure Machine Learning můžete použít také k snadnému nasazení modelů strojového učení pro potřeby testování a výroby.

## <a name="prerequisites"></a>Požadavky

+ Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

+ Nainstalujte [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview), zjednodušený Editor kódu, který běží v systémech Windows, Mac a Linux.

+ [Nainstalujte Python 3,5 nebo novější](https://www.anaconda.com/download/).


## <a name="install-the-extension"></a>Instalace rozšíření

Když nainstalujete rozšíření Azure Machine Learning, automaticky se nainstalují dvě další rozšíření. Jedná se o [rozšíření účtu Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) a [rozšíření Python společnosti Microsoft](https://marketplace.visualstudio.com/items?itemName=ms-Python.Python). Další informace o používání rozšíření Pythonu pro úpravy, spouštění a ladění kódu v Pythonu najdete v [kurzu pro Python Hello World](https://code.visualstudio.com/docs/Python/Python-tutorial).

Instalace rozšíření Azure Machine Learning:

1. Otevřete Visual Studio Code.

1. Přepněte na kartu rozšíření a vyhledejte "Azure Machine Learning".

1. Na kartě rozšíření vyberte **instalovat**.

1. Karta Vítejte pro rozšíření se otevře v Visual Studio Code a na řádku aktivity se přidá symbol Azure (zvýrazněný červeně na následujícím snímku obrazovky).

   ![Ikona Azure na řádku Visual Studio Code aktivity](./media/how-to-vscode-tools/azure-activity-bar.png)

1. V dialogovém okně vyberte **Přihlásit** se a postupujte podle výzev k ověření pomocí Azure.

   Rozšíření účtu Azure, které se nainstalovalo spolu s rozšířením Azure Machine Learning for Visual Studio Code, vám pomůže s vaším účtem Azure ověřit. Seznam příkazů najdete na stránce pro [rozšíření účtu Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!TIP]
> Instalační program rozšíření si můžete také stáhnout přímo z [Azure Machine Learning Visual Studio Code rozšíření (Preview)](https://aka.ms/vscodetoolsforai).

## <a name="quickstart-with-azure-machine-learning"></a>Rychlý Start s Azure Machine Learning
K dispozici je několik způsobů, jak spustit školicí skripty pomocí Azure Machine Learning. Pokud začínáte, Podívejme se, jak rychle odeslat školicí skript pro běh v Azure.

Pokud už jste se seznámili se Azure Machine Learning koncepty a chtěli bychom podrobnější informace o tom, jak je spravovat a používat s rozšířením, přečtěte si téma [Azure Machine Learning podrobnější s vs Code](how-to-vscode-tools.md#azure-machine-learning-in-depth-with-vs-code) níže.

## <a name="run-an-existing-python-training-script-in-azure"></a>Spuštění existujícího školicího skriptu Pythonu v Azure
Pokud máte existující školicí skript, Azure Machine Learning rozšíření pro VS Code nejen nabízí vynikající prostředí pro úpravy, ladění a správu zdrojového kódu, ale také usnadňuje spouštění a ukládání metrik pro váš skript v Azure.

Pusťme se do toho. Můžete použít vlastní školicí skript, pokud ho máte připravený, nebo naklonovat ukázkové [úložiště VSCode-Tools-for-AI](https://github.com/microsoft/vscode-tools-for-ai). Toto je veřejné úložiště pro vytváření potíží s tímto rozšířením. Obsahuje také malou ukázkovou složku **mnist ručně zapsaných** , kterou použijeme v tomto příkladu.

1. Otevřete složku **mnist ručně zapsaných** v vs Code.

1. Vytvořte nové prostředí Pythonu pomocí vašeho oblíbeného balíčku virtuálního prostředí nebo Anaconda a nainstalujte balíčky TensorFlow a numpy.

1. V levém dolním rohu stavového řádku VS Code vyberte nové prostředí, které jste vytvořili jako interpret Pythonu.

1. Otevřete **Train.py** a spusťte ho otevřením ladicího programu a stisknutím tlačítka Spustit (nebo stačí stisknout klávesu F5).

   [![spuštění školení MNIST ručně zapsaných](./media/how-to-vscode-tools/run-mnist.gif)](./media/how-to-vscode-tools/run-mnist.gif#lightbox)

Pokud je všechno nainstalovaná správně, skript se spustí a vytvoří TensorFlow model ve složce výstupy.

[![zobrazit model TensorFlow](./media/how-to-vscode-tools/show-tensorflow-model.gif)](./media/how-to-vscode-tools/show-tensorflow-model.gif#lightbox)

Když teď víte, že váš skript běží správně, můžeme ho spustit v Azure!

To se dá snadno udělat bez dalších úprav **Train.py**. Avšak v několika jednoduchých změnách můžete pomocí Azure Machine Learning automaticky sledovat důležité metriky podle vašeho výběru o jednotlivých školicích běhůch.

### <a name="make-azure-aware-of-your-run-metrics"></a>Zajistěte službě Azure vědět metriky spuštění
Úprava projektu, aby bylo možné Azure ve vašich spuštěních vědět o důležitých informacích:

1. Vytvořte soubor s názvem **amlrun.py** ve stejné složce jako **Train.py**

    ```Python
    import azureml
    from azureml.core import Run

    # Access the Azure ML run
    # Init run param to check if running within AML
    def get_AMLRun():
        try:
            run = Run.get_submitted_run()
            return run
        except Exception as e:
            print("Caught = {}".format(e.message))
            return None
    ```

2. Import souboru amlrun v **Train.py**

    ```Python
    ...
    from utils import prepare_data
    from amlrun import get_AMLRun
    ...
    ```
3. Inicializace objektu spuštění v **Train.py**

    ```Python
    ...
    init = tf.global_variables_initializer()
    saver = tf.train.Saver()
    run = get_AMLRun()
    ...
    ```
4. Protokolujte metriky do Azure pomocí funkce run. log ():

    ```Python
    ...
            acc_val = acc_op.eval(feed_dict = {X: X_test, y: y_test})

            # Log accuracies to AML logger if using AML
            if run != None:
                run.log('Validation Accuracy', np.float(acc_val))
                run.log('Training Accuracy', np.float(acc_train))

            print(epoch, '-- Training accuracy:', acc_train, '\b Validation
    ...
    ```
### <a name="run-the-script-in-azure"></a>Spuštění skriptu v Azure
A to je vše! Teď stačí použít rozšíření ke spuštění skriptu v cloudu! Všimněte si, že následující názorný postup povede ke snadnému komprimaci doby potřebné k vytvoření nového pracovního prostoru Azure Machine Learning a výpočetnímu prostředí a také k času potřebnýmu ke spuštění školicího skriptu.

   [![spuštění experimentu s Azure ML](./media/how-to-vscode-tools/start-golden-path.gif)](./media/how-to-vscode-tools/start-golden-path.gif#lightbox)

Po kliknutí na tlačítko pro spuštění experimentu odpovězte na následující výzvy:

1. Zvolte svoje předplatné Azure.
1. Vyberte, chcete-li vytvořit *Nový* pracovní prostor Azure Machine Learning.
1. Vyberte ze sady předem nakonfigurovaných šablon k inicializaci prostředí Python pro spuštění. Šablony poskytují počáteční bod a obsahují nastavení pro:
    1. **PyTorch**, **TensorFlow**nebo **Scikit – informace**
    1. **Jednoduché** nebo **distribuované** výpočetní školení
    1. **Obecné** pro vlastní prostředí
1. Zajistěte, aby byl pro váš skript dokončený seznam balíčků PIP a Conda, a to přidáním balíčků, které nejsou zahrnuté v šabloně.
1. Zkontrolujte výchozí názvy a specifikace pro spuštění experimentu a klikněte na odkaz **Odeslat experiment** v souboru JSON. Soubor JSON se neuloží, protože je jednoduše tam, kde můžete před odesláním zkontrolovat nebo změnit nastavení experimentu.
1. Nasaďte se zpátky a uvolněte, než rozšíření nastaví vše pro vás a spustí váš skript.

    [![vlak v cloudu](./media/how-to-vscode-tools/run-golden-path.gif)](./media/how-to-vscode-tools/run-golden-path.gif#lightbox)

Během několika sekund budete upozorněni na to, že experiment byl odeslán do Azure ve chvíli, kdy můžete zobrazit jeho průběh v Azure Machine Learning Studiu kliknutím na odkaz **Zobrazit experimenty** v vs Code oznámení nebo uvnitř vs Code, a to tak, že na kartě Azure kliknete na tlačítko Aktualizovat.

V tuto chvíli se zobrazení metriky spuštění podporuje jenom v studiu. Výše uvedený odkaz **Zobrazit experimenty pro zkoušku** vám umožní spustit, kde uvidíte metriky, které jste zaznamenali.
[![experiment spustit na portálu](./media/how-to-vscode-tools/experiment-run-on-portal.PNG)](./media/how-to-vscode-tools/experiment-run-on-portal.PNG#lightbox)

## <a name="azure-machine-learning-in-depth-with-vs-code"></a>Azure Machine Learning podrobně s VS Code

V předchozím návodu jsme odeslali experiment za nejjednodušší cestu. Jak jste si všimli, že rozšíření minimalizuje kroky, které je třeba spravovat, aby bylo možné spustit experiment. V této části se dozvíte, jak můžete spravovat všechny Azure Machine Learning koncepce jednotlivě a poskytnout maximální kontrolu.

Před zahájením školení a nasazení modelů strojového učení v Visual Studio Code musíte vytvořit [pracovní prostor Azure Machine Learning](concept-workspace.md) v cloudu. Tento pracovní prostor bude obsahovat vaše modely a prostředky.

### <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

1. Na řádku Visual Studio Code aktivity vyberte ikonu Azure. Zobrazí se postranní panel Azure Machine Learning.

    [![vytvořit pracovní prostor](./media/how-to-vscode-tools/create-workspace.gif)](./media/how-to-vscode-tools/create-workspace.gif#lightbox)


1. Klikněte pravým tlačítkem na předplatné Azure a vyberte **vytvořit pracovní prostor**. Ve výchozím nastavení je generován název obsahující datum a čas vytvoření. Změňte název na **TeamWorkspace** a stiskněte klávesu ENTER.

1. Vyberte skupinu prostředků ze seznamu, pokud víte, která z nich chcete vybrat nebo vytvořit novou. Pokud vytváříte nový, vyberte umístění, které je nejblíže umístění, do kterého plánujete model nasadit. V tomto případě jsme zvolili **západní USA 2**.

1. Po stisknutí klávesy ENTER se Azure Machine Learning dostane požadavek na vytvoření pracovního prostoru. Tento proces se vám bude informovat v oznamovací oblasti Visual Studio Code.

1. Rozbalte uzel předplatné, abyste našli nově vytvořený pracovní prostor.

### <a name="create-an-experiment"></a>Vytvoření experimentu
V pracovním prostoru se dá vytvořit jeden nebo více experimentů, abyste mohli sledovat a analyzovat jednotlivé běhy školení k jednotlivým modelům. Spuštění se dá provést v cloudu Azure nebo na místním počítači.

1. Rozbalte pracovní prostor **TeamWorkspace** . Klikněte pravým tlačítkem myši na uzel **experimenty** a v místní nabídce vyberte možnost **vytvořit experiment** .

1. Do příkazového řádku zadejte název experimentu. V ukázkových snímcích obrazovky se experiment jmenuje **mnist ručně zapsaných**.

1. Vyberte ENTER a vytvořte nový experiment. Nový experiment se zobrazí ve stromu jako podřízený uzel **Experiments** .

1. V pracovním prostoru můžete kliknout pravým tlačítkem na experiment a nastavit ho jako **aktivní** experiment. **Aktivní** odkazy na experimenty, které experimentují v cloudu, do složky, kterou momentálně otevřete v Visual Studio Code. Tato složka by měla obsahovat vaše místní skripty Pythonu. Když nastavíte aktivní experiment, budou se v rámci experimentu ukládat klíčová metrika pro všechna cvičení, a to bez ohledu na to, kde se spustí.

    [![vytvořit experiment](./media/how-to-vscode-tools/create-experiment.gif)](./media/how-to-vscode-tools/create-experiment.gif#lightbox)


### <a name="create-and-manage-compute-targets"></a>Vytvoření a Správa cílových výpočetních prostředí

Díky Azure Machine Learning pro Visual Studio Code můžete připravit vaše data, naučit modely a nasazovat je místně i na vzdálené výpočetní cíle.

Rozšíření podporuje několik vzdálených výpočetních cílů pro Azure Machine Learning. Další informace najdete v úplném seznamu podporovaných [výpočetních cílů pro Azure Machine Learning](how-to-set-up-training-targets.md).

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Vytváření výpočetních cílů pro Azure Machine Learning v Visual Studio Code

Postup vytvoření cíle výpočtů:

1. Na řádku Visual Studio Code aktivity vyberte ikonu Azure. Zobrazí se postranní panel Azure Machine Learning.

1. Ve stromovém zobrazení rozbalte předplatné Azure a pracovní prostor Azure Machine Learning.

1. Pod uzlem pracovního prostoru, klikněte pravým tlačítkem na **Compute** uzlu a zvolte **vytvořit výpočetní**.

1. Zvolte typ cílové výpočetní ze seznamu.

1. Na příkazovém řádku palety příkazů vyberte velikost virtuálního počítače. Výpočty můžete filtrovat pomocí textu, jako je například GPU.

1. Na příkazovém řádku palety příkazů zadejte název cílového výpočetního prostředí.

1. Po zadání názvu se výpočty vytvoří pomocí výchozích parametrů. Chcete-li změnit parametry, klikněte pravým tlačítkem na nový výpočet a vyberte možnost **Upravit výpočetní**prostředky.

1. V zobrazeném formátu JSON proveďte požadované změny a pak klikněte na CodeLens Uložit a pokračovat (pomocí klávesnice můžete stisknutím **kombinace kláves CTRL + SHIFT + p** vyvolat paletu příkazů a spustit **Azure ml: příkaz Uložit a pokračovat).**

Tady je příklad, jak vytvořit a upravit Azure Machine Learning COMPUTE (AMLCompute):

[![vytvořit COMPUTE AML v Visual Studio Code](./media/how-to-vscode-tools/create-remote-compute.gif)](./media/how-to-vscode-tools/create-remote-compute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>Konfigurační soubor spuštění

Pokud chcete spustit Azure Machine Learning experiment na výpočetním prostředí, je potřeba správně nakonfigurovat výpočetní výkon. Konfigurační soubor spuštění je mechanismus, podle kterého je toto prostředí zadáno.

Tady je příklad, jak vytvořit konfiguraci spuštění pro AmlCompute, která je vytvořená výše.

[![vytvoření konfigurace spuštění pro výpočetní prostředí](./media/how-to-vscode-tools/create-runconfig.gif)](./media/how-to-vscode-tools/create-runconfig.gif#lightbox)

Pokud chcete na svém místním počítači spouštět experimenty Azure ML, je potřeba, aby se konfigurační soubor pro spuštění pořád vyžadoval. Při vytváření místní konfigurace spuštění se použité prostředí Pythonu použije jako výchozí cesta k překladači, který jste nastavili v VS Code.

### <a name="train-and-tune-models"></a>Trénování a optimalizaci modelů

Pomocí rozšíření Azure ML pro VS Code existuje několik způsobů, jak spustit školicí skript v experimentu.

1. Klikněte pravým tlačítkem na skript školení a vyberte **Azure ml: spustit jako experiment v Azure.**
1. Klikněte na ikonu spustit experiment na panelu nástrojů.
1. Klikněte pravým tlačítkem na uzel Konfigurace spuštění.
1. Použití palety příkazů VS Code ke spuštění **Azure ml: Spuštění experimentu**

Spuštění Azure Machine Learning experimentu:

1. Na řádku Visual Studio Code aktivity vyberte ikonu Azure.

1. Ve stromovém zobrazení rozbalte předplatné Azure a pracovní prostor Azure Machine Learning.

1. Pod uzlem pracovní prostor rozbalte uzel **experimenty** a klikněte pravým tlačítkem myši na experiment, který chcete spustit.

1. Vyberte **spuštění experimentu**.

1. Zvolte název souboru Pythonu, který chcete spustit pro výuku modelu, a stisknutím klávesy ENTER odeslání spusťte. Poznámka: vybraný soubor se musí nacházet ve složce, kterou nyní máte otevřený v VS Code.

1. Po odeslání běhu se pod experimentem, který jste zvolili, zobrazí **uzel spustit** . Tento uzel použijte k monitorování stavu spuštění. Poznámka: může být nutné pravidelně aktualizovat okno, aby se zobrazil nejnovější stav.

Tady je příklad, jak spustit experiment v dříve vytvořeném výpočetním prostředí:

[![spustit experiment místně](./media/how-to-vscode-tools/run-experiment.gif)](./media/how-to-vscode-tools/run-experiment.gif#lightbox)

### <a name="deploy-and-manage-models"></a>Nasazení a správě modelů
V Azure Machine Learning můžete nasadit a spravovat modely strojového učení v cloudu i na hraničních zařízeních.

#### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Zaregistrujte si model pro Azure Machine Learning z Visual Studio Code

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

[![registrace modelu do AML](./media/how-to-vscode-tools/register-model.gif)](./media/how-to-vscode-tools/register-model.gif#lightbox)


#### <a name="deploy-your-service-from-visual-studio-code"></a>Nasazení služby z Visual Studio Code

V Visual Studio Code můžete nasadit webovou službu do:
+ Azure Container Instances (ACI) pro testování.
+ Služba Azure Kubernetes (AKS) pro produkční prostředí.

Nemusíte vytvářet kontejner ACI k testování předem, protože kontejnery ACI se vytvářejí podle potřeby. Je ale potřeba nakonfigurovat clustery AKS předem. Další informace najdete v tématu [nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

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

[![nasazení webové služby](./media/how-to-vscode-tools/create-image.gif)](./media/how-to-vscode-tools/create-image.gif#lightbox)

### <a name="experiment-with-additional-features"></a>Experimentování s dalšími funkcemi

Paleta příkazů můžete použít pro přístup k mnoha funkcím Azure Machine Learning v Visual Studio Code. K vyvolání typu palety příkazů CTRL + SHIFT + P. Tady můžete vyhledat další funkce rozšíření Azure ML.

[![klávesové zkratky pro Azure Machine Learning pro Visual Studio Code](./media/how-to-vscode-tools/commands.gif)](./media/how-to-vscode-tools/commands.gif#lightbox)

## <a name="next-steps"></a>Další kroky

* Návod, jak naučit se Azure Machine Learning mimo Visual Studio Code, najdete v tématu [kurz: výuka modelů pomocí Azure Machine Learning](tutorial-train-models-with-aml.md).
* Návod, jak upravovat, spouštět a ladit kód místně, najdete v [kurzu Python Hello World](https://code.visualstudio.com/docs/Python/Python-tutorial).
