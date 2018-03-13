---
title: "Kurz sestavení modelu pro služby Azure Machine Learning (Preview) | Microsoft Docs"
description: "V tomto kurzu na pokračování se dozvíte, jak komplexně používat služby Azure Machine Learning (Preview). Toto je druhá část, ve které se probírá experimentování."
services: machine-learning
author: hning86
ms.author: haining, j-martens
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/28/2018
ms.openlocfilehash: 9a1613e2137e178d00a24f9f5b3c802f8a894b15
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
---
# <a name="tutorial-classify-iris-part-2---build-a-model"></a>Kurz: Klasifikace Iris, část 2 – Sestavení modelu
Služby Azure Machine Learning (Preview) představují integrované, komplexní řešení datové vědy a pokročilé analýzy pro profesionální datové vědce, které slouží k přípravě dat, vývoji experimentů a nasazování modelů na úrovni cloudu.

Tento kurz je **druhou částí z třídílné série**. V této části kurzu provedete pomocí služeb Azure Machine Learning (Preview) následující úlohy:

> [!div class="checklist"]
> * Použití aplikace Azure Machine Learning Workbench
> * Otevírání skriptů a kontrola kódu
> * Spouštění skriptů v místním prostředí
> * Kontrola historie spuštění
> * Spouštění skriptů v místním prostředí Dockeru
> * Spouštění skriptů v místním okně Azure CLI
> * Spouštění skriptů ve vzdáleném prostředí Dockeru
> * Spouštění skriptů v cloudovém prostředí Azure HDInsight

Tento kurz používá nadčasovou [datovou sadu Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set). Snímky obrazovky jsou specifické pro systém Windows, ale prostředí v systému Mac OS je téměř shodné.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Dokončete první část této série kurzů. Před zahájením kroků v tomto kurzu vytvořte na základě [kurzu přípravy dat](tutorial-classifying-iris-part-1.md) prostředky služby Azure Machine Learning a nainstalujte aplikaci Azure Machine Learning Workbench.

Volitelně můžete experimentovat se spouštěním skriptů proti místnímu kontejneru Dockeru. V takovém případě potřebujete nainstalovaný modul Docker (Community Edition je dostačující) spuštěný místně na počítači s Windows nebo Mac OS. Další informace o instalaci Dockeru najdete v [pokynech k instalaci Dockeru](https://docs.docker.com/engine/installation/).

Pokud chcete experimentovat s odesíláním skriptů ke spuštění v kontejneru Dockeru na vzdáleném virtuálním počítači Azure nebo v clusteru Azure HDInsight Spark, můžete postupovat podle [pokynů k vytvoření virtuálního počítače Azure pro datové vědy založeného na Ubuntu nebo clusteru HDInsight](how-to-create-dsvm-hdi.md).

## <a name="review-irissklearnpy-and-the-configuration-files"></a>Kontrola skriptu iris_sklearn.py a konfiguračních souborů
1. Otevřete aplikaci Azure Machine Learning Workbench a otevřete projekt **myIris**, který jste vytvořili v předchozí části této série kurzů.

2. Po otevření projektu výběrem tlačítka **Soubory** (ikona složky) v podokně úplně vlevo otevřete seznam souborů ve složce vašeho projektu.

3. Vyberte soubor **iris_sklearn.py**. V aplikaci Workbench se na nové kartě textového editoru otevře kód Pythonu.

   ![Otevření souboru](media/tutorial-classifying-iris/open_iris_sklearn.png)

   >[!NOTE]
   >Kód, který se zobrazí, nemusí být přesně stejný jako předchozí kód, protože tento ukázkový projekt se často aktualizuje.

4. Projděte si kód skriptu Python, abyste se seznámili se stylem kódování. Skript provádí následující úlohy:

   - Načte balíček pro přípravu dat **iris.dprep** pro vytvoření struktury [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). 

        >[!NOTE]
        >Použijte balíček pro přípravu dat `iris.dprep`, který je součástí ukázkového projektu a který by měl být totožný se souborem `iris-1.dprep` vytvořeným v 1. části tohoto kurzu.

   - Přidá náhodné funkce, aby bylo obtížnější problém vyřešit. Náhodnost je potřeba, protože Iris je malá datová sada, která se dá snadno klasifikovat s téměř 100% přesností.

   - Pomocí knihovny pro machine learning [scikit-learn](http://scikit-learn.org/stable/index.html) sestaví model logistické regrese. 

   - Serializuje model vložením knihovny [pickle](https://docs.python.org/2/library/pickle.html) do souboru ve složce `outputs`. Skript ho pak načte a deserializuje zpět do paměti.

   - Pomocí deserializovaného modelu vytvoří předpověď nového záznamu. 

   - Pomocí knihovny [matplotlib](https://matplotlib.org/) vytvoří dva grafy – chybovou matici a křivku ROC (Receiver Operating Characteristic) s více třídami – a pak je uloží do složky `outputs`.

   - V celém průběhu se pomocí objektu `run_logger` zaznamenává do protokolů míra regularizace a přesnost modelu. Protokoly se automaticky promítají do historie spouštění.


## <a name="execute-irissklearnpy-script-in-a-local-environment"></a>Spuštění skriptu iris_sklearn.py v místním prostředí

Teď se připravíme na první spuštění skriptu **iris_sklearn.py**. Tento skript vyžaduje balíčky **scikit-learn** a **matplotlib**. Balíček **scikit-learn** již nainstalovala aplikace Azure Machine Learning Workbench. Ještě musíte nainstalovat balíček **matplotlib**. 

1. V aplikaci Azure Machine Learning Workbench vyberte nabídku **Soubor** a výběrem možnosti **Otevřít příkazový řádek** otevřete příkazový řádek. Toto okno rozhraní příkazového řádku označujeme jako *okno CLI aplikace Azure Machine Learning Workbench* nebo zkráceně jenom jako *okno CLI*.

2. Do okna CLI zadejte následující příkaz, který slouží k instalaci balíčku **matplotlib** pro Python. Mělo by to trvat necelou minutu.

   ```azurecli
   pip install matplotlib
   ```

   >[!NOTE]
   >Pokud předchozí příkaz `pip install` přeskočíte, kód ve skriptu `iris_sklearn.py` se úspěšně spustí. Pokud spustíte pouze skript `iris_sklearn.py`, kód nevytvoří výstup v podobě grafů chybové matice a křivky ROC s více třídami, který je vidět ve vizualizacích historie.

3. Vraťte se do okna aplikace Workbench. 

4. Na panelu nástrojů v horní části karty **iris_sklearn.py** výběrem otevřete rozevírací nabídku vedle ikony **Uložit** a pak vyberte **Konfigurace spuštění**. Jako spouštěcí prostředí vyberte **local** a jako skript ke spuštění zadejte `iris_sklearn.py`.

5. Pak se přesuňte na pravou stranu panelu nástrojů a do pole **Argumenty** zadejte `0.01`. 

   ![Řízení spuštění](media/tutorial-classifying-iris/run_control.png)

6. Vyberte tlačítko **Spustit**. Okamžitě se naplánuje úloha. Tato úloha se objeví v podokně **Úlohy** na pravé straně okna aplikace Workbench. 

7. Po chvíli se stav úlohy změní z **Odesílání** na **Spuštěno** a pak na **Dokončeno**.

   ![Spuštění skriptu sklearn](media/tutorial-classifying-iris/run_sklearn.png)

8. V textu stavu úlohy na panelu **Úlohy** vyberte **Dokončeno**. Zobrazí se automaticky otevírané okno s textem standardního výstupu (stdout) spuštěného skriptu. Pokud chcete text standardního výstupu zavřít, vyberte tlačítko **Zavřít** (**x**) v pravém horním rohu automaticky otevíraného okna.

9. Ve stejném stavu úlohy v podokně **Úlohy** vyberte modrý text **iris_sklearn.py [n]** (_n_ je číslo spuštění) hned nad stavem **Dokončeno** a časem spuštění. Otevře se okno **Vlastnosti spuštění**, ve kterém se zobrazí následující informace o tomto konkrétním spuštění:
   - Informace o **vlastnostech spuštění**
   - Soubory s **výstupy**
   - **Vizualizace**, pokud nějaké existují
   - **Protokoly** 

   Po dokončení spuštění se v automaticky otevíraném okně zobrazí následující výsledky:

   >[!NOTE]
   >Vzhledem k tomu, že jsme u cvičné sady použili náhodnost, se můžou vaše přesné výsledky mírně lišit od zde uvedených výsledků.

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

11. Opakujte další spuštění. 

    Do pole **Argumenty** zadejte řadu různých číselných hodnot od `0.001` do `10`. Výběrem možnosti **Spustit** ještě několikrát spusťte kód. Hodnota argumentu, kterou pokaždé změníte, se předává algoritmu logistické regrese v kódu, takže se pokaždé zobrazí jiné závěry.

## <a name="review-the-run-history-in-detail"></a>Podrobná kontrola historie spuštění
V aplikaci Azure Machine Learning Workbench se každé spuštění skriptu zaznamená v podobě záznamu historie spuštění. Pokud otevřete zobrazení **Spuštění**, můžete si prohlédnout historii spuštění určitého skriptu.

1. Pokud chcete otevřít seznam **Spuštění**, vyberte tlačítko **Spuštění** (ikona hodin) na levém panelu nástrojů. Potom výběrem možnost **iris_sklearn.py** zobrazíte **Řídicí panel spuštění** skriptu `iris_sklearn.py`.

   ![Zobrazení spuštění](media/tutorial-classifying-iris/run_view.png)

2. Otevře se karta **řídicího panelu spuštění**. Zkontrolujte statistiky zaznamenané během různých spuštění. V horní části karty se vykreslují grafy. Všechna spuštění mají po sobě jdoucí čísla a podrobnosti o spuštění se zobrazí v tabulce v dolní části obrazovky.

   ![Řídicí panel spuštění](media/tutorial-classifying-iris/run_dashboard.png)

3. Tabulku můžete filtrovat a pak vybírat libovolné grafy, abyste zobrazili stav, dobu trvání, přesnost a míru regularizace jednotlivých spuštění. 

4. V tabulce **Spuštění** vyberte dvě nebo tři spuštění a výběrem tlačítka **Porovnat** otevřete stránku podrobného porovnání. Projděte si porovnání údajů vedle sebe. Výběrem tlačítka Zpět pro **Seznam spuštění** v levém horním rohu stránky **Porovnání** se vraťte na **Řídicí panel spuštění**.

5. Výběrem určitého spuštění otevřete jeho podrobné zobrazení. Všimněte si, že v části **Vlastností spuštění** se zobrazí statistika vybraného spuštění. Soubory zapsané do složky výstupu se zobrazí v části **Výstupy**, odkud si je můžete stáhnout.

   ![Podrobnosti o spuštění](media/tutorial-classifying-iris/run_details.png)

   V části **Vizualizace** se vykreslí dva grafy, chybová matice a křivka ROC s více třídami. Všechny soubory protokolu najdete také v části **Protokoly**.

## <a name="execute-scripts-in-the-local-docker-environment"></a>Spouštění skriptů v místním prostředí Dockeru

Pomocí služby Machine Learning můžete snadno konfigurovat další spouštěcí prostředí, jako je Docker, a spouštět v těchto prostředích skripty. 

>[!IMPORTANT]
>K provedení tohoto kroku je potřeba mít v místním počítači nainstalovaný a spuštěný modul Docker. Další informace najdete v pokynech k instalaci Dockeru.

1. Výběrem ikony **složky** v levém podokně otevřete seznam **Soubory** pro váš projekt. Rozbalte složku `aml_config`. 

2. Je tu několik předkonfigurovaných prostředí, například **docker-python**, **docker-spark** a **local**. 

   Každé prostředí má dva soubory, například `docker-python.compute` a `docker-python.runconfig`. Otevřete oba soubory a všimněte si, že v textovém editoru se dají konfigurovat některé možnosti.  

   Pokud chcete zavřít karty otevřených textových editorů vyberte na nich **Zavřít** (**x**).

3. Spusťte skript **iris_sklearn.py** v prostředí **docker-python**: 

   - Na levém panelu nástrojů otevřete výběrem ikony **hodin** podokno **Spuštění**. Vyberte **Všechna spuštění**. 
   - V horní části karty **Všechna spuštění** vyberte místo výchozího prostředí **local** cílové prostředí **docker-python**. 
   - Potom se přesuňte na pravou stranu a jako skript ke spuštění vyberte **iris_sklearn.py**. 
   - Pole **Argumenty** nechte prázdné, protože skript určuje výchozí hodnotu. 
   - Vyberte tlačítko **Spustit**.

4. Všimněte si, že se spustí nová úloha. Zobrazí se v podokně **Úlohy** na pravé straně okna aplikace Workbench.

   Pokud spouštíte skript v prostředí Docker poprvé, bude zpracování trvat o několik minut déle. 

   Aplikace Azure Machine Learning Workbench na pozadí sestaví nový soubor Docker. 
   Tento nový soubor odkazuje na základní image Dockeru zadanou v souboru `docker.compute` a závislé balíčky Pythonu zadané v souboru `conda_dependencies.yml`. 
   
   Modul Docker provede následující úlohy:

    - Stáhne základní image z Azure.
    - Nainstaluje balíčky Pythonu zadané v souboru `conda_dependencies.yml`.
    - Spustí kontejner Dockeru.
    - Zkopíruje lokální kopii (nebo na ni podle konfigurace spuštění vytvoří odkaz) složky projektu.      
    - Spustí skript `iris_sklearn.py`.

   Nakonec by se vám měl zobrazit naprosto stejný výsledek jako při použití prostředí **local**.

5. Teď vyzkoušíme prostředí Spark. Základní image Dockeru obsahuje předinstalovanou a nakonfigurovanou instanci prostředí Spark. Díky této instanci v ní můžete spustit skript PySpark. Je to snadný způsob, jak vyvíjet a testovat program v jazyce Spark, aniž byste museli ztrácet čas instalací a konfigurací prostředí Spark. 

   Otevřete soubor `iris_spark.py`. Tento skript načte datový soubor `iris.csv` a klasifikuje datovou sadu Iris pomocí algoritmu logistické regrese z knihovny Spark Machine Learning. Teď změňte prostředí pro spuštění na **docker-spark** a skript na **iris_spark.py** a pak ho znovu spusťte. Tento proces trvá o něco déle, protože je potřeba vytvořit relaci Spark a spustit ji uvnitř kontejneru Docker. Můžete si taky všimnout, že standardní výstup se liší od standardního výstupu skriptu `iris_spark.py`.

6. Proveďte pár dalších spuštění a vyzkoušejte různé argumenty. 

7. Otevřete soubor `iris_spark.py` a podívejte se na model logistické regrese sestavený pomocí knihovny Spark Machine Learning. 

8. Vyzkoušejte si práci s podoknem **Úlohy**, zobrazením seznamu historie spuštění a podrobným zobrazením vašich spuštění v různých spouštěcích prostředích.

## <a name="execute-scripts-in-the-azure-machine-learning-cli-window"></a>Spouštění skriptů v okně CLI služby Azure Machine Learning

1. V aplikaci Azure Machine Learning Workbench otevřete okno příkazového řádku, vyberte nabídku **Soubor** a pak vyberte **Otevřít příkazový řádek**. Příkazový řádek se spustí ve složce projektu s příkazem `C:\Temp\myIris\>`.

   >[!IMPORTANT]
   >Okno příkazového řádku (otevřené z aplikace Workbench) musíte použít k provedení následujících kroků.

2. Pomocí příkazového řádku se přihlaste k Azure. 

   Aplikace Workbench a CLI používají při ověřování prostředků Azure nezávislé mezipaměti přihlašovacích údajů. Tento krok je potřeba provést jenom jednou, než vyprší token v mezipaměti. Příkaz **az account list** vrátí seznam předplatných dostupných pro vaše přihlášení. Pokud jich je více než jedno, použijte hodnotu ID z požadovaného předplatného. Nastavte toto předplatné jako výchozí účet pro použití pomocí příkazu **az account set -s** a pak zadejte hodnotu ID předplatného. Potom nastavení ověřte pomocí příkazu **account show**.

   ```azurecli
   REM login by using the aka.ms/devicelogin site
   az login
   
   REM lists all Azure subscriptions you have access to 
   az account list -o table
   
   REM sets the current Azure subscription to the one you want to use
   az account set -s <subscriptionId>
   
   REM verifies that your current subscription is set correctly
   az account show
   ```

3. Jakmile proběhne ověření a nastavení aktuálního kontextu předplatného Azure, zadáním následujících příkazů do okna CLI nainstalujte balíček **matplotlib** a pak odešlete skript Pythonu jako experiment ke spuštění.

   ```azurecli
   REM you don't need to run this command if you have installed matplotlib locally from the previous steps
   pip install matplotlib
   
   REM kicks off an execution of the iris_sklearn.py file against the local compute context
   az ml experiment submit -c local .\iris_sklearn.py
   ```

4. Zkontrolujte výstup. Výstup a výsledky budou stejné jako při spuštění skriptu pomocí aplikace Workbench. 

5. Pokud máte v počítači nainstalovaný Docker, spusťte stejný skript znovu pomocí spouštěcího prostředí Docker.

   ```azurecli
   REM executes iris_sklearn.py in the local Docker container Python environment
   az ml experiment submit -c docker-python .\iris_sklearn.py 0.01
   
   REM executes iris_spark.py in the local Docker container Spark environment
   az ml experiment submit -c docker-spark .\iris_spark.py 0.1
   ```
6. V aplikaci Workbench výběrem ikony **složky** v levém podokně zobrazte seznam souborů projektu a otevřete skript Pythonu s názvem **run.py**. 

   Tento skript slouží k vytvoření smyčky s různými mírami regularizace. Spusťte několikrát experiment s těmito mírami. Tento skript spustí úlohu `iris_sklearn.py` s mírou regularizace `10.0` (absurdně vysoké číslo). Skript v následujícím spuštění tuto míru o polovinu sníží a tak dále, dokud nedosáhne hodnoty `0.005`. 

   ```python
   # run.py
   import os
   
   reg = 10
   while reg > 0.005:
       os.system('az ml experiment submit -c local ./iris_sklearn.py {}'.format(reg))
       reg = reg / 2
   ```

   Pokud chcete otevřít skript **run.py** z příkazového řádku, spusťte následující příkazy:

   ```cmd
   REM submits iris_sklearn.py multiple times with different regularization rates
   python run.py
   ```

   Když se skript `run.py` dokončí, uvidíte v zobrazení seznamu historie spuštění v aplikaci Workbench graf.

## <a name="execute-in-a-docker-container-on-a-remote-machine"></a>Spuštění v kontejneru Docker ve vzdáleném počítači
Pokud chcete skript spustit v kontejneru Docker ve vzdáleném počítači s Linuxem, potřebujete přístup SSH (uživatelské jméno a heslo) do tohoto vzdáleného počítače. Kromě toho musí být na vzdáleném počítači nainstalovaný a spuštěný modul Docker. Nejjednodušší způsob, jak takový počítač s Linuxem získat, spočívá ve vytvoření virtuálního počítače pro datové vědy se systémem Ubuntu v Azure. Přečtěte si víc o [postupu vytvoření virtuálního počítače pro datové vědy se systémem Ubuntu pro použití v aplikaci Azure ML Workbench](how-to-create-dsvm-hdi.md#create-an-ubuntu-dsvm-in-azure-portal).

>[!NOTE] 
>Virtuální počítače pro datové vědy se systémem CentOS se *nepodporují*.

1. Po vytvoření virtuálního počítače můžete tento virtuální počítač připojit jako spouštěcí prostředí vygenerováním dvojice souborů `.runconfig` a `.compute`. Vygenerujte tyto soubory pomocí následujícího příkazu. Nové prostředí si pojmenujeme `myvm`.
 
   ```azurecli
   REM creates an myvm compute target
   az ml computetarget attach remotedocker --name myvm --address <IP address> --username <username> --password <password>
   ```
   
   >[!NOTE]
   >IP adresa může být také veřejně adresovatelný plně kvalifikovaný název domény, například `vm-name.southcentralus.cloudapp.azure.com`. Je vhodné přidat do virtuálního počítače pro datové vědy plně kvalifikovaný název domény a používat ho místo IP adresy. Tento postup je vhodný, protože z důvodu úspory nákladů můžete někdy chtít virtuální počítač vypnout. Kromě toho se může při příštím spuštění virtuálního počítače IP adresa změnit.

   Potom spusťte následující příkaz pro vytvoření image Dockeru ve virtuálním počítači, abyste ho připravili na spouštění skriptů:
   
   ```azurecli
   REM prepares the myvm compute target
   az ml experiment prepare -c myvm
   ```
   >[!NOTE]
   >Můžete také změnit hodnotu položky `PrepareEnvironment` v souboru `myvm.runconfig` z výchozí hodnoty `false` na `true`. Tato změna automaticky připraví kontejner Dockeru pro první spuštění.

2. Upravte vygenerovaný soubor `myvm.runconfig` ve složce `aml_config` a změňte rozhraní z výchozí hodnoty `PySpark` na `Python`:

   ```yaml
   "Framework": "Python"
   ```
   >[!NOTE]
   >Pokud rozhraní necháte nastavené na hodnotu PySpark, mělo by také fungovat správně. Toto nastavení je ale neefektivní, pokud doopravdy nepotřebujete, aby váš skript Pythonu spouštěla relace Spark.

3. V okně CLI zadejte stejný příkaz jako předtím, jenom tentokrát s cílem _myvm_:
   ```azurecli
   REM executes iris_sklearn.py in a remote Docker container
   az ml experiment submit -c myvm iris_sklearn.py
   ```
   Příkaz se spustí stejně, jako když používáte prostředí `docker-python`, jenže tentokrát se spustí ve vzdáleném virtuálním počítači s Linuxem. V okně CLI se zobrazí stejné výstupní informace.

4. Teď si vyzkoušíme použití Sparku v kontejneru. Otevřete Průzkumníka souborů. Pokud znáte základní příkazy pro manipulaci se soubory, můžete to také udělat z okna CLI. Vytvořte kopii souboru `myvm.runconfig` a pojmenujte ji `myvm-spark.runconfig`. Upravte nový soubor tak, že změníte nastavení `Framework` z hodnoty `Python` na `PySpark`:
   ```yaml
   "Framework": "PySpark"
   ```
   Neprovádějte žádné změny souboru `myvm.compute`. Pro provádění příkazu Spark se používá stejná image Dockeru ve stejném virtuálním počítači. V novém souboru `myvm-spark.runconfig` pole `target` odkazuje na stejný soubor `myvm.compute` prostřednictvím jeho názvu `myvm`.

5. Zadáním následujícího příkazu ho spusťte v instanci Spark ve vzdáleném kontejneru Dockeru:
   ```azureli
   REM executes iris_spark.py in a Spark instance on a remote Docker container
   az ml experiment submit -c myvm-spark .\iris_spark.py
   ```

## <a name="execute-script-in-an-hdinsight-cluster"></a>Spuštění skriptu v clusteru služby HDInsight
Tento skript můžete také spustit v clusteru HDInsight Spark. Seznamte se s [postupem vytvoření clusteru HDInsight Spark pro použití v aplikaci Azure ML Workbench](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal).

>[!NOTE] 
>Cluster HDInsight musí jako primární úložiště používat Azure Blob. Použití úložiště Azure Data Lake se ještě nepodporuje.

1. Pokud máte přístup ke clusteru Spark pro Azure HDInsight, vygenerujte příkaz pro spuštění konfigurace služby HDInsight podle uvedeného příkladu. Jako parametry zadejte název clusteru HDInsight, vaše uživatelské jméno HDInsight a heslo. Použijte následující příkaz:

   ```azurecli
   REM creates a compute target that points to a HDInsight cluster
   az ml computetarget attach cluster --name myhdi --address <cluster head node FQDN> --username <username> --password <password>

   REM prepares the HDInsight cluster
   az ml experiment prepare -c myhdi
   ```

   Plně kvalifikovaný název domény hlavního uzlu clusteru je obvykle `<cluster_name>-ssh.azurehdinsight.net`.

   >[!NOTE]
   >`username` je uživatelské jméno SSH clusteru. Výchozí hodnota je `sshuser`, pokud ji nezměníte při nastavování služby HDInsight. Hodnota není `admin`, což je druhý uživatel vytvořený při nastavování za účelem povolení přístupu na web správce clusteru. 

2. Spusťte následující příkaz a skript se spustí v clusteru HDInsight:

   ```azurecli
   REM executes iris_spark on the HDInsight cluster
   az ml experiment submit -c myhdi .\iris_spark.py
   ```

   >[!NOTE]
   >Při spouštění ve vzdáleném clusteru HDInsight můžete také pomocí uživatelského účtu `admin` zobrazit na adrese `https://<cluster_name>.azurehdinsight.net/yarnui` podrobnosti provádění úlohy YARN (Yet Another Resource Negotiator).


## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky
V této druhé části třídílné série kurzů jste se naučili, jak pomocí služeb Azure Machine Learning provádět tyto úlohy:
> [!div class="checklist"]
> * Použití aplikace Azure Machine Learning Workbench
> * Otevírání skriptů a kontrola kódu
> * Spouštění skriptů v místním prostředí
> * Kontrola historie spuštění
> * Spouštění skriptů v místním prostředí Dockeru
> * Spouštění skriptů v místním okně Azure CLI
> * Spouštění skriptů ve vzdáleném prostředí Dockeru
> * Spouštění skriptů v cloudovém prostředí HDInsight

Teď můžete přejít ke třetí části série kurzů. Vytvořili jste model logistické regrese, takže ho teď můžete nasadit jako webovou službu v reálném čase.

> [!div class="nextstepaction"]
> [Kurz 3 – Klasifikace Iris: Nasazení modelů](tutorial-classifying-iris-part-3.md)
