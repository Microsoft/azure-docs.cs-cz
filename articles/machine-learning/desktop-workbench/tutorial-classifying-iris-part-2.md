---
title: Kurz sestavení modelu pro služby Azure Machine Learning (Preview) | Microsoft Docs
description: V tomto kurzu na pokračování se dozvíte, jak komplexně používat služby Azure Machine Learning (Preview). Toto je druhá část, ve které se probírá experimentování.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/15/2018
ms.openlocfilehash: 4f9b14762332bd11fd69a855d8fabdb206e34919
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "41919522"
---
# <a name="tutorial-2-classify-iris---build-a-model"></a>Kurz 2: Klasifikace Iris – Sestavení modelu
Služby Azure Machine Learning (Preview) představují integrované řešení datové vědy a pokročilé analýzy pro profesionální datové vědce, které slouží k přípravě dat, vývoji experimentů a nasazování modelů na úrovni cloudu.

Tento kurz je **druhou částí z třídílné série**. V této části kurzu provedete pomocí služeb Azure Machine Learning následující úlohy:

> [!div class="checklist"]
> * Otevírání skriptů a kontrola kódu
> * Spouštění skriptů v místním prostředí
> * Kontrola historií spuštění
> * Spouštění skriptů v místním okně Azure CLI
> * Spouštění skriptů v místním prostředí Dockeru
> * Spouštění skriptů ve vzdáleném prostředí Dockeru
> * Spouštění skriptů v cloudovém prostředí Azure HDInsight

Tento kurz používá nadčasovou [datovou sadu Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set). 

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:
- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 
- Účet Experimentování a nainstalovanou aplikaci Azure Machine Learning Workbench, jak je popsáno v tomto [rychlém startu](../service/quickstart-installation.md).
- Projekt a připravená data Iris z [části 1 tohoto kurzu](tutorial-classifying-iris-part-1.md).
- Nainstalovaný a místně spuštěný modul Docker. Docker Community Edition je dostačující. Informace o instalaci Dockeru najdete tady: https://docs.docker.com/engine/installation/.

## <a name="review-irissklearnpy-and-the-configuration-files"></a>Kontrola skriptu iris_sklearn.py a konfiguračních souborů

1. Spusťte aplikaci Azure Machine Learning Workbench.

1. Otevřete projekt **myIris**, který jste vytvořili v [části 1 této série kurzů](tutorial-classifying-iris-part-1.md).

2. V otevřeném projektu výběrem tlačítka **Soubory** (ikona složky) v podokně úplně vlevo otevřete seznam souborů ve složce vašeho projektu.

   ![Otevření projektu aplikace Azure Machine Learning Workbench](media/tutorial-classifying-iris/2-project-open.png)

3. Vyberte soubor skriptu Pythonu **iris_sklearn.py**. 

   ![Výběr skriptu](media/tutorial-classifying-iris/2-choose-iris_sklearn.png)

   Kód se v aplikaci Workbench otevře na nové kartě textového editoru. Tento skript budete používat v celé této části kurzu. 

   >[!NOTE]
   >Kód, který se zobrazí, nemusí být přesně stejný jako předchozí kód, protože tento ukázkový projekt se často aktualizuje.
   
   ![Otevření souboru](media/tutorial-classifying-iris/open_iris_sklearn.png)

4. Projděte si kód skriptu Pythonu, abyste se seznámili se stylem kódování. 

   Skript **iris_sklearn.py** provádí následující úlohy:

   * Načte výchozí balíček pro přípravu dat **iris.dprep** pro vytvoření [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). 

   * Přidá náhodné funkce, aby bylo obtížnější problém vyřešit. Náhodnost je potřeba, protože Iris je malá datová sada, která se dá snadno klasifikovat s téměř 100% přesností.

   * Pomocí knihovny pro machine learning [scikit-learn](http://scikit-learn.org/stable/index.html) sestaví model logistické regrese.  Tato knihovna je standardně součástí aplikace Azure Machine Learning Workbench.

   * Serializuje model vložením knihovny [pickle](https://docs.python.org/3/library/pickle.html) do souboru ve složce `outputs`. 
   
   * Načte serializovaný model a pak ho deserializuje zpět do paměti.

   * Pomocí deserializovaného modelu vytvoří předpověď nového záznamu. 

   * Pomocí knihovny [matplotlib](https://matplotlib.org/) vytvoří dva grafy – chybovou matici a křivku ROC (Receiver Operating Characteristic) s více třídami – a pak je uloží do složky `outputs`. Tuto knihovnu můžete ve svém prostředí nainstalovat, pokud tam ještě není.

   * Automaticky v historii spuštění vykreslí míru regularizace a přesnost modelu. V celém průběhu se pomocí objektu `run_logger` zaznamenává do protokolů míra regularizace a přesnost modelu. 


## <a name="run-irissklearnpy-in-your-local-environment"></a>Spuštění skriptu iris_sklearn.py v místním prostředí

1. Spusťte rozhraní příkazového řádku služby Azure Machine Learning:
   1. Spusťte aplikaci Azure Machine Learning Workbench.

   1. V nabídce aplikace Workbench vyberte **Soubor** > **Otevřít příkazový řádek**. 
   
   Otevře se okno rozhraní příkazového řádku služby Azure Machine Learning ve složce projektu `C:\Temp\myIris\>` ve Windows. Jedná se o stejný projekt, který jste vytvořili v části 1 tohoto kurzu.

   >[!IMPORTANT]
   >K provedení dalších kroků musíte použít toto okno rozhraní příkazového řádku.

1. V okně rozhraní příkazového řádku nainstalujte knihovnu Pythonu pro vykreslování **matplotlib**, pokud ji ještě nemáte.

   Skript **iris_sklearn.py** obsahuje závislosti na dvou balíčcích Pythonu: **scikit-learn** a **matplotlib**.  Balíček **scikit-learn** pro usnadnění práce nainstalovala aplikace Azure Machine Learning Workbench. Je však potřeba nainstalovat balíček **matplotlib**, pokud ho ještě nemáte nainstalovaný.

   Kód v tomto kurzu se může úspěšně spustit i v případě, že budete pokračovat bez instalace balíčku **matplotlib**. Kód však nebude moct vytvořit výstup v podobě grafů chybové matice a křivky ROC s více třídami, které jsou vidět ve vizualizacích historie.

   ```azurecli
   pip install matplotlib
   ```

   Tato instalace trvá přibližně minutu.

1. Vraťte se do aplikace Workbench. 

1. Vyhledejte kartu **iris_sklearn.py**. 

   ![Vyhledání karty se skriptem](media/tutorial-classifying-iris/2-iris_sklearn-tab.png)

1. Na panelu nástrojů této karty vyberte jako spouštěcí prostředí **local** a jako skript ke spuštění vyberte `iris_sklearn.py`. Tyto hodnoty již můžou být vybrané.

   ![Výběr spouštěcího prostředí local a skriptu](media/tutorial-classifying-iris/2-local-script.png)

1. Přesuňte se na pravou stranu panelu nástrojů a do pole **Argumenty** zadejte `0.01`. 

   Tato hodnota odpovídá míře regularizace modelu logistické regrese.

   ![Výběr spouštěcího prostředí local a skriptu](media/tutorial-classifying-iris/2-local-script-arguments.png)

1. Vyberte tlačítko **Spustit**. Okamžitě se naplánuje úloha. Tato úloha se objeví v podokně **Úlohy** na pravé straně okna aplikace Workbench. 

   ![Výběr spouštěcího prostředí local a skriptu](media/tutorial-classifying-iris/2-local-script-arguments-run.png)

   Po chvíli se stav úlohy změní z **Odesílání** na **Spuštěno** a nakonec na **Dokončeno**.

1. V textu stavu úlohy na panelu **Úlohy** vyberte **Dokončeno**. 

   ![Spuštění skriptu sklearn](media/tutorial-classifying-iris/2-completed.png)

   Zobrazí se automaticky otevírané okno s textem standardního výstupu (stdout) spuštění. Pokud chcete text standardního výstupu zavřít, vyberte tlačítko **Zavřít** (**x**) v pravém horním rohu automaticky otevíraného okna.

   ![Standardní výstup](media/tutorial-classifying-iris/2-standard-output.png)

9. Ve stejném stavu úlohy v podokně **Úlohy** vyberte modrý text **iris_sklearn.py [n]** (_n_ je číslo spuštění) hned nad stavem **Dokončeno** a časem spuštění. Otevře se okno **Vlastnosti spuštění**, ve kterém se zobrazí následující informace o tomto konkrétním spuštění:
   - Informace o **vlastnostech spuštění**
   - **Výstupy**
   - **Metriky**
   - **Vizualizace**, pokud nějaké existují
   - **Protokoly** 

   Po dokončení spuštění se v automaticky otevíraném okně zobrazí následující výsledky:

   >[!NOTE]
   >Vzhledem k tomu, že s v tomto kurzu u cvičné sady použila náhodnost, se můžou vaše přesné výsledky mírně lišit od zde uvedených výsledků.

   ```text
   Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]
   
   Iris dataset shape: (150, 5)
   Regularization rate is 0.01
   LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
          intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
          penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
          verbose=0, warm_start=False)
   Accuracy is 0.6792452830188679
   
   ==========================================
   Serialize and deserialize using the outputs folder.
   
   Export the model to model.pkl
   Import the model from model.pkl
   New sample: [[3.0, 3.6, 1.3, 0.25]]
   Predicted class is ['Iris-setosa']
   Plotting confusion matrix...
   Confusion matrix in text:
   [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
   Confusion matrix plotted.
   Plotting ROC curve....
   ROC curve plotted.
   Confusion matrix and ROC curve plotted. See them in Run History details pane.
   ```
    
10. Zavřete kartu **Vlastnosti spuštění** a vraťte se na kartu **iris_sklearn.py**. 

11. Opakujte pro další spuštění. 

    Do pole **Argumenty** zadejte řadu hodnot od `0.001` do `10`. Výběrem možnosti **Spustit** ještě několikrát spusťte kód. Hodnota argumentu, kterou pokaždé změníte, se předává modelu logistické regrese v kódu, takže se pokaždé zobrazí jiné závěry.

## <a name="review-the-run-history-in-detail"></a>Podrobná kontrola historie spuštění
V aplikaci Azure Machine Learning Workbench se každé spuštění skriptu zaznamená v podobě záznamu historie spuštění. Pokud otevřete zobrazení **Spuštění**, můžete si prohlédnout historii spuštění určitého skriptu.

1. Pokud chcete otevřít seznam **Spuštění**, vyberte tlačítko **Spuštění** (ikona hodin) na levém panelu nástrojů. Potom výběrem možnost **iris_sklearn.py** zobrazíte **Řídicí panel spuštění** skriptu `iris_sklearn.py`.

   ![Zobrazení spuštění](media/tutorial-classifying-iris/run_view.png)

1. Otevře se karta **řídicího panelu spuštění**. 

   Zkontrolujte statistiky zaznamenané během různých spuštění. V horní části karty se vykreslují grafy. Všechna spuštění mají po sobě jdoucí čísla a podrobnosti o spuštění se zobrazí v tabulce v dolní části obrazovky.

   ![Řídicí panel spuštění](media/tutorial-classifying-iris/run_dashboard.png)

1. Tabulku můžete filtrovat a pak vybírat libovolné grafy, abyste zobrazili stav, dobu trvání, přesnost a míru regularizace jednotlivých spuštění. 

1. Zaškrtněte políčka vedle dvou nebo více spuštění v tabulce **Spuštění**. Výběrem tlačítka **Porovnat** otevřete podokno s podrobným porovnáním. Projděte si porovnání údajů vedle sebe. 

1. Pokud se chcete vrátit na **Řídicí panel spuštění**, vyberte tlačítko Zpět pro **Seznam spuštění** v levém horním rohu podokna **Porovnání**.

   ![Návrat na Seznam spuštění](media/tutorial-classifying-iris/2-compare-back.png)

1. Výběrem určitého spuštění otevřete jeho podrobné zobrazení. Všimněte si, že v části **Vlastností spuštění** se zobrazí statistika vybraného spuštění. Soubory zapsané do složky výstupu se zobrazí v části **Výstupy**, odkud si je můžete stáhnout.

   ![Podrobnosti o spuštění](media/tutorial-classifying-iris/run_details.png)

   V části **Vizualizace** se vykreslí dva grafy, chybová matice a křivka ROC s více třídami. Všechny soubory protokolu najdete také v části **Protokoly**.


## <a name="run-scripts-in-local-docker-environments"></a>Spouštění skriptů v místních prostředích Dockeru

Volitelně můžete experimentovat se spouštěním skriptů proti místnímu kontejneru Dockeru. Můžete nakonfigurovat další spouštěcí prostředí, jako je Docker, a spouštět skript v těchto prostředích. 

>[!NOTE]
>Pokud chcete experimentovat s odesíláním skriptů ke spuštění v kontejneru Dockeru na vzdáleném virtuálním počítači Azure nebo v clusteru Azure HDInsight Spark, můžete postupovat podle [pokynů k vytvoření virtuálního počítače Azure pro datové vědy založeného na Ubuntu nebo clusteru HDInsight](how-to-create-dsvm-hdi.md).

1. Pokud jste to ještě neudělali, nainstalujte a místně spusťte Docker na svém počítači s Windows nebo MacOS. Další informace najdete v pokynech k instalaci Dockeru na adrese https://docs.docker.com/install/. Edice Community je dostačující.

1. Výběrem ikony **složky** v levém podokně otevřete seznam **Soubory** pro váš projekt. Rozbalte složku `aml_config`. 

2. Je tu několik předkonfigurovaných prostředí: **docker-python**, **docker-spark** a **local**. 

   Každé prostředí obsahuje dva soubory, jako je například `docker.compute` (pro prostředí **docker-python** i **docker-spark**) a `docker-python.runconfig`. Otevřete oba soubory a všimněte si, že v textovém editoru se dají konfigurovat některé možnosti.  

   Pokud chcete zavřít karty otevřených textových editorů vyberte na nich **Zavřít** (**x**).

3. Spusťte skript **iris_sklearn.py** v prostředí **docker-python**: 

   - Na levém panelu nástrojů otevřete výběrem ikony **hodin** podokno **Spuštění**. Vyberte **Všechna spuštění**. 

   - V horní části karty **Všechna spuštění** vyberte místo výchozího prostředí **local** cílové prostředí **docker-python**. 

   - Potom se přesuňte na pravou stranu a jako skript ke spuštění vyberte **iris_sklearn.py**. 

   - Pole **Argumenty** nechte prázdné, protože skript určuje výchozí hodnotu. 

   - Vyberte tlačítko **Spustit**.

4. Všimněte si, že se spustí nová úloha. Zobrazí se v podokně **Úlohy** na pravé straně okna aplikace Workbench.

   Pokud spouštíte skript v Dockeru poprvé, bude dokončení úlohy trvat o několik minut déle. 

   Aplikace Azure Machine Learning Workbench na pozadí sestaví nový soubor Docker. 
   Tento nový soubor odkazuje na základní image Dockeru zadanou v souboru `docker.compute` a závislé balíčky Pythonu zadané v souboru `conda_dependencies.yml`. 
   
   Modul Docker provede následující úlohy:

    - Stáhne základní image z Azure.
    - Nainstaluje balíčky Pythonu zadané v souboru `conda_dependencies.yml`.
    - Spustí kontejner Dockeru.
    - Zkopíruje lokální kopii (nebo na ni podle konfigurace spuštění vytvoří odkaz) složky projektu.      
    - Spustí skript `iris_sklearn.py`.

   Nakonec by se vám měly zobrazit naprosto stejné výsledky jako při použití prostředí **local**.

5. Teď vyzkoušíme prostředí Spark. Základní image Dockeru obsahuje předinstalovanou a nakonfigurovanou instanci prostředí Spark, kterou můžete použít ke spuštění skriptu PySpark. Použití této základní image představuje snadný způsob, jak vyvíjet a testovat program Spark, aniž byste museli ztrácet čas instalací a konfigurací prostředí Spark. 

   Otevřete soubor `iris_spark.py`. Tento skript načte datový soubor `iris.csv` a klasifikuje datovou sadu Iris pomocí algoritmu logistické regrese z knihovny Spark Machine Learning. Teď změňte prostředí pro spuštění na **docker-spark** a skript na **iris_spark.py** a pak ho znovu spusťte. Tento proces trvá o něco déle, protože je potřeba vytvořit relaci Spark a spustit ji uvnitř kontejneru Docker. Můžete si taky všimnout, že standardní výstup se liší od standardního výstupu skriptu `iris_spark.py`.

6. Proveďte pár dalších spuštění a vyzkoušejte různé argumenty. 

7. Otevřete soubor `iris_spark.py` a podívejte se na model logistické regrese sestavený pomocí knihovny Spark Machine Learning. 

8. Vyzkoušejte si práci s podoknem **Úlohy**, zobrazením seznamu historie spuštění a podrobným zobrazením vašich spuštění v různých spouštěcích prostředích.

## <a name="run-scripts-in-the-cli-window"></a>Spouštění skriptů v okně rozhraní příkazového řádku

1. Spusťte rozhraní příkazového řádku služby Azure Machine Learning:
   1. Spusťte aplikaci Azure Machine Learning Workbench.

   1. V nabídce aplikace Workbench vyberte **Soubor** > **Otevřít příkazový řádek**. 
   
   Spustí se rozhraní příkazového řádku ve složce projektu `C:\Temp\myIris\>` ve Windows. Toto je projekt, který jste vytvořili v části 1 tohoto kurzu.

   >[!IMPORTANT]
   >K provedení dalších kroků musíte použít toto okno rozhraní příkazového řádku.

1. V okně rozhraní příkazového řádku se přihlaste k Azure. [Další informace o příkazu az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

   Možná už jste přihlášeni. V takovém případě můžete tento krok přeskočit.

   1. Na příkazovém řádku zadejte:
      ```azurecli
      az login
      ```

      Tento příkaz vrátí kód, který použijete v prohlížeči na adrese https://aka.ms/devicelogin.

   1. V prohlížeči přejděte na adresu https://aka.ms/devicelogin.

   1. Po zobrazení výzvy zadejte do prohlížeče kód, který jste získali v rozhraní příkazového řádku.

   Aplikace Workbench a rozhraní příkazového řádku používají při ověřování prostředků Azure nezávislé mezipaměti přihlašovacích údajů. Po přihlášení se nebudete muset znovu ověřovat, dokud nevyprší platnost tokenu uloženého v mezipaměti. 

1. Pokud má vaše organizace více předplatných Azure (podnikové prostředí), musíte nastavit předplatné, které se má použít. Vyhledejte své předplatné, nastavte ho pomocí ID předplatného a pak ho otestujte.

   1. Pomocí tohoto příkazu vypište všechna předplatná Azure, ke kterým máte přístup:
   
      ```azurecli
      az account list -o table
      ```

      Příkaz **az account list** vrátí seznam předplatných dostupných pro vaše přihlášení. 
      Pokud existuje více než jedno, určete hodnotu ID předplatného, které chcete použít.

   1. Nastavte předplatné Azure, které chcete použít, jako výchozí účet:
   
      ```azurecli
      az account set -s <your-subscription-id>
      ```
      kde \<your-subscription-id\> je hodnota ID předplatného, které chcete použít. Závorky nezadávejte.

   1. Potvrďte nové nastavení předplatného vyžádáním podrobností o aktuálním předplatném. 

      ```azurecli
      az account show
      ```    

1. V okně rozhraní příkazového řádku nainstalujte knihovnu Pythonu pro vykreslování **matplotlib**, pokud ji ještě nemáte.

   ```azurecli
   pip install matplotlib
   ```

1. V okně rozhraní příkazového řádku odešlete skript **iris_sklearn.py** jako experiment.

   Provádění skriptu iris_sklearn.py se spustí v místním výpočetním kontextu.

   + Ve Windows:
     ```azurecli
     az ml experiment submit -c local .\iris_sklearn.py
     ```

   + V systému MacOS:
     ```azurecli
     az ml experiment submit -c local iris_sklearn.py
     ```
   
   Váš výstup by měl vypadat přibližně takto:
    ```text
    RunId: myIris_1521077190506
    
    Executing user inputs .....
    ===========================
    
    Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  2 2016, 17:52:12) 
    [GCC 4.2.1 Compatible Apple LLVM 4.2 (clang-425.0.28)]
    
    Iris dataset shape: (150, 5)
    Regularization rate is 0.01
    LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
              intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
              penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
              verbose=0, warm_start=False)
    Accuracy is 0.6792452830188679
        
    ==========================================
    Serialize and deserialize using the outputs folder.
    
    Export the model to model.pkl
    Import the model from model.pkl
    New sample: [[3.0, 3.6, 1.3, 0.25]]
    Predicted class is ['Iris-setosa']
    Plotting confusion matrix...
    Confusion matrix in text:
    [[50  0  0]
     [ 1 37 12]
     [ 0  4 46]]
    Confusion matrix plotted.
    Plotting ROC curve....
    ROC curve plotted.
    Confusion matrix and ROC curve plotted. See them in Run History details page.
    
    Execution Details
    =================
    RunId: myIris_1521077190506
    ```

1. Zkontrolujte výstup. Výstup a výsledky budou stejné jako při spuštění skriptu pomocí aplikace Workbench. 

1. V okně rozhraní příkazového řádku znovu spusťte skript Pythonu **iris_sklearn.py** s použitím spouštěcího prostředí Docker (pokud na svém počítači máte Docker nainstalovaný).

   + Pokud je váš kontejner ve Windows: 
     |Spouštěcí<br/>environment|Příkaz ve Windows|
     |---------------------|------------------|
     |Python|`az ml experiment submit -c docker-python .\iris_sklearn.py 0.01`|
     |Spark|`az ml experiment submit -c docker-spark .\iris_spark.py 0.1`|

   + Pokud je váš kontejner v systému MacOS: 
     |Spouštěcí<br/>environment|Příkaz ve Windows|
     |---------------------|------------------|
     |Python|`az ml experiment submit -c docker-python iris_sklearn.py 0.01`|
     |Spark|`az ml experiment submit -c docker-spark iris_spark.py 0.1`|

1. Vraťte se do aplikace Workbench a:
   1. Výběrem ikony složky v levém podokně vypište soubory projektu.
   
   1. Otevřete skript Pythonu **run.py**. Tento skript slouží k vytvoření smyčky s různými mírami regularizace. 

   ![Návrat na Seznam spuštění](media/tutorial-classifying-iris/2-runpy.png)

1. Spusťte několikrát experiment s těmito mírami. 

   Tento skript spustí úlohu `iris_sklearn.py` s mírou regularizace `10.0` (absurdně vysoké číslo). Skript v následujícím spuštění tuto míru o polovinu sníží a tak dále, dokud nedosáhne hodnoty `0.005`. 

   Skript obsahuje následující kód:

   ![Návrat na Seznam spuštění](media/tutorial-classifying-iris/2-runpy-code.png)

1. Z příkazového řádku spusťte skript **run.py** následujícím způsobem:

   ```cmd
   python run.py
   ```

   Tento příkaz několikrát odešle skript iris_sklearn.py s různými mírami regularizace.

   Když se skript `run.py` dokončí, uvidíte v zobrazení seznamu historie spuštění v aplikaci Workbench grafy různých metrik.

## <a name="run-scripts-in-a-remote-docker-container"></a>Spouštění skriptů ve vzdáleném kontejneru Dockeru
Pokud chcete skript spustit v kontejneru Docker ve vzdáleném počítači s Linuxem, potřebujete přístup SSH (uživatelské jméno a heslo) do tohoto vzdáleného počítače. Kromě toho musí být na počítači nainstalovaný a spuštěný modul Docker. Nejjednodušší způsob, jak takový počítač s Linuxem získat, spočívá ve vytvoření virtuálního počítače pro datové vědy se systémem Ubuntu v Azure. Přečtěte si víc o [postupu vytvoření virtuálního počítače pro datové vědy se systémem Ubuntu pro použití v aplikaci Azure ML Workbench](how-to-create-dsvm-hdi.md#create-an-ubuntu-dsvm-in-azure-portal).

>[!NOTE] 
>Virtuální počítače pro datové vědy se systémem CentOS se *nepodporují*.

1. Po vytvoření virtuálního počítače můžete tento virtuální počítač připojit jako spouštěcí prostředí vygenerováním dvojice souborů `.runconfig` a `.compute`. Vygenerujte tyto soubory pomocí následujícího příkazu. 

 Nové cílové výpočetní prostředí pojmenujeme `myvm`.
 
   ```azurecli
   az ml computetarget attach remotedocker --name myvm --address <your-IP> --username <your-username> --password <your-password>
   ```
   
   >[!NOTE]
   >IP adresa může být také veřejně adresovatelný plně kvalifikovaný název domény, například `vm-name.southcentralus.cloudapp.azure.com`. Je vhodné přidat do virtuálního počítače pro datové vědy plně kvalifikovaný název domény a používat ho místo IP adresy. Tento postup je vhodný, protože z důvodu úspory nákladů můžete někdy chtít virtuální počítač vypnout. Kromě toho se může při příštím spuštění virtuálního počítače IP adresa změnit.

   >[!NOTE]
   >Kromě ověřování uživatelského jména a hesla můžete zadat privátní klíč a odpovídající heslo (pokud existuje) pomocí možností `--private-key-file` a (volitelně) `--private-key-passphrase`. Pokud chcete použít privátní klíč, který jste použili při vytváření DSVM, musíte zadat možnost `--use-azureml-ssh-key`.

   Dále připravte cílové výpočetní prostředí **myvm** spuštěním tohoto příkazu.
   
   ```azurecli
   az ml experiment prepare -c myvm
   ```
   
   Předchozí příkaz ve virtuálním počítači vytvoří image Dockeru, aby se připravil na spouštění skriptů.
   
   >[!NOTE]
   >Můžete také změnit hodnotu položky `PrepareEnvironment` v souboru `myvm.runconfig` z výchozí hodnoty `false` na `true`. Tato změna automaticky připraví kontejner Dockeru v rámci prvního spuštění.

2. Upravte vygenerovaný soubor `myvm.runconfig` ve složce `aml_config` a změňte rozhraní z výchozí hodnoty `PySpark` na `Python`:

   ```yaml
   Framework: Python
   ```
   >[!NOTE]
   >Přestože by měl fungovat i PySpark, efektivnější je použít Python, pokud doopravdy nepotřebujete, aby váš skript Python spouštěla relace Spark.

3. V okně rozhraní příkazového řádku zadejte stejný příkaz jako předtím, jenom tentokrát pro spuštění skriptu iris_sklearn.py ve vzdáleném kontejneru Dockeru použijte cíl _myvm_:
   ```azurecli
   az ml experiment submit -c myvm iris_sklearn.py
   ```
   Příkaz se spustí stejně, jako když používáte prostředí `docker-python`, jenže tentokrát se spustí ve vzdáleném virtuálním počítači s Linuxem. V okně CLI se zobrazí stejné výstupní informace.

4. Teď si vyzkoušíme použití Sparku v kontejneru. Otevřete Průzkumníka souborů. Vytvořte kopii souboru `myvm.runconfig` a pojmenujte ji `myvm-spark.runconfig`. Upravte nový soubor tak, že změníte nastavení `Framework` z hodnoty `Python` na `PySpark`:
   ```yaml
   Framework: PySpark
   ```
   Neprovádějte žádné změny souboru `myvm.compute`. Pro provádění příkazu Spark se používá stejná image Dockeru ve stejném virtuálním počítači. V novém souboru `myvm-spark.runconfig` pole `Target` odkazuje na stejný soubor `myvm.compute` prostřednictvím jeho názvu `myvm`.

5. Zadáním následujícího příkazu spusťte skript **iris_spark.py** v instanci Spark spuštěné ve vzdáleném kontejneru Dockeru:
   ```azureli
   az ml experiment submit -c myvm-spark .\iris_spark.py
   ```

## <a name="run-scripts-in-hdinsight-clusters"></a>Spouštění skriptů v clusterech HDInsight
Tento skript můžete také spustit v clusteru HDInsight Spark. Seznamte se s [postupem vytvoření clusteru HDInsight Spark pro použití v aplikaci Azure ML Workbench](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal).

>[!NOTE] 
>Cluster HDInsight musí jako primární úložiště používat Azure Blob. Použití úložiště Azure Data Lake se ještě nepodporuje.

1. Pokud máte přístup ke clusteru Spark pro Azure HDInsight, vygenerujte příkaz pro spuštění konfigurace služby HDInsight podle uvedeného příkladu. Jako parametry zadejte název clusteru HDInsight, vaše uživatelské jméno HDInsight a heslo. 

   Pomocí následujícího příkazu vytvořte cílové výpočetní prostředí odkazující na cluster HDInsight:

   ```azurecli
   az ml computetarget attach cluster --name myhdi --address <cluster head node FQDN> --username <your-username> --password <your-password>
   ```

   Cluster HDInsight připravíte spuštěním tohoto příkazu:

   ```
   az ml experiment prepare -c myhdi
   ```

   Plně kvalifikovaný název domény hlavního uzlu clusteru je obvykle `<your_cluster_name>-ssh.azurehdinsight.net`.

   >[!NOTE]
   >`username` je uživatelské jméno SSH clusteru definované při nastavování služby HDInsight. Výchozí hodnota je `sshuser`. Hodnota není `admin`, což je druhý uživatel vytvořený při nastavování za účelem povolení přístupu na web správce clusteru. 

2. Spusťte skript **iris_spark.py** v clusteru HDInsight pomocí tohoto příkazu:

   ```azurecli
   az ml experiment submit -c myhdi .\iris_spark.py
   ```

   >[!NOTE]
   >Při spouštění ve vzdáleném clusteru HDInsight můžete také pomocí uživatelského účtu `admin` zobrazit na adrese `https://<your_cluster_name>.azurehdinsight.net/yarnui` podrobnosti provádění úlohy YARN (Yet Another Resource Negotiator).

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky
V této druhé části třídílné série kurzů jste se naučili tyto postupy:
> [!div class="checklist"]
> * Otevírání skriptů a kontrola kódu v aplikaci Workbench
> * Spouštění skriptů v místním prostředí
> * Kontrola historie spuštění
> * Spouštění skriptů v místním prostředí Dockeru

Teď můžete vyzkoušet třetí část této série kurzů, ve které můžete vytvořený model logistické regrese nasadit jako webovou službu v reálném čase.

> [!div class="nextstepaction"]
> [Kurz 3 – Nasazení modelů](tutorial-classifying-iris-part-3.md)
