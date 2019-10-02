---
title: Prozkoumat Linux
titleSuffix: Azure Data Science Virtual Machine
description: Naučte se, jak dokončit několik běžných úloh pro datové vědy pomocí Data Science Virtual Machine pro Linux.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 07/16/2018
ms.openlocfilehash: f9d4b933bc9c6e11dde8168d9797a1b6196e6f47
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170697"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-in-azure"></a>Datové vědy s Data Science Virtual Machine pro Linux v Azure

V tomto návodu se dozvíte, jak dokončit několik běžných úloh pro datové vědy pomocí Data Science Virtual Machine pro Linux (DSVM). Linux DSVM je image virtuálního počítače, která je k dispozici v Azure, která je předinstalována s kolekcí nástrojů běžně používaných pro analýzu dat a strojové učení. Klíčové softwarové komponenty se účtují v [rámci zřízení Data Science Virtual Machine pro Linux](linux-dsvm-intro.md). DSVM image usnadňuje zprovoznění datových věd během několika minut, aniž byste museli instalovat a konfigurovat jednotlivé nástroje samostatně. DSVM můžete snadno škálovat, pokud potřebujete, a můžete ji zastavit, když se nepoužívá. Prostředek DSVM je elastický a nákladově efektivní.

Úkoly spojené s datovou vědy, které jsou uvedené v tomto návodu, se řídí postupem popsaným v [části Co je vědecké zpracování týmových dat?](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) Vědecké zpracování týmových dat je systematický přístup k datovému vědy, který pomáhá týmům s daty efektivně spolupracovat nad životním cyklem vytváření inteligentních aplikací. Vědecké zpracování dat také poskytuje rozšiřovatelnou platformu pro iterativní pro datové vědy, které může být následován jednotlivec.

V tomto návodu analyzujeme datovou sadu [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) . Spambase je sada e-mailů, které jsou označené buď spam, nebo HAM (nikoli spam). Spambase také obsahuje statistiku o obsahu e-mailů. V tomto návodu budeme mluvit o statistice později.

## <a name="prerequisites"></a>Požadavky

Než budete moct použít DSVM pro Linux, musíte mít následující požadavky:

* **Předplatné Azure**. Pokud chcete získat předplatné Azure, přečtěte si téma [Vytvoření bezplatného účtu Azure ještě dnes](https://azure.microsoft.com/free/).
* [**Data Science Virtual Machine Linux**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Informace o zřizování virtuálního počítače najdete v tématu [zřízení Data Science Virtual Machine pro Linux](linux-dsvm-intro.md).
* V počítači je nainstalovaná [**X2Go**](https://wiki.x2go.org/doku.php) s otevřenou relací desktop Xfce. Další informace najdete v tématu [instalace a konfigurace klienta X2Go](linux-dsvm-intro.md#x2go).
* Chcete-li plynulejší posouvání, přepněte `gfx.xrender.enabled` ve webovém prohlížeči DSVM na prohlížeč Firefox `about:config`příznaku. [Další informace](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Zvažte také nastavení `mousewheel.enable_pixel_scrolling` na `False`. [Další informace](https://support.mozilla.org/questions/981140).
* **Účet Azure Machine Learning**. Pokud ho ještě nemáte, zaregistrujte si nový účet na [domovské stránce Azure Machine Learning](https://studio.azureml.net/). Můžete si to vyzkoušet zdarma, abychom vám pomohli začít.

## <a name="download-the-spambase-dataset"></a>Stáhnout spambase datovou sadu

Datová sada [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) je relativně malá sada dat, která obsahuje příklady 4 601. Datová sada je vhodná velikost pro demonstraci některých klíčových funkcí DSVM, protože udržuje požadavky na prostředky mírné.

> [!NOTE]
> Tento návod byl vytvořen pomocí sady D2 v2 DSVM (CentOS Edition) s velikostí D2. Tuto velikost můžete použít DSVM k dokončení postupů, které jsou znázorněny v tomto návodu.

Pokud potřebujete více úložného prostoru, můžete vytvořit další disky a připojit je k DSVM. Disky používají trvalé úložiště Azure, takže jejich data se zachovají i v případě, že dojde k opětovnému zřízení serveru z důvodu změny velikosti nebo vypnutí. Pokud chcete přidat disk a připojit ho k DSVM, proveďte kroky v části [Přidání disku do virtuálního počítače se systémem Linux](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Postup přidání disku pomocí rozhraní příkazového řádku Azure, který je už nainstalovaný v DSVM. Kroky můžete dokončit úplně z samotného DSVM. Další možností, jak úložiště zvýšit, je použití [souborů Azure](../../storage/files/storage-how-to-use-files-linux.md).

Chcete-li stáhnout data, otevřete okno terminálu a pak spusťte tento příkaz:

    wget https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Stažený soubor neobsahuje řádek záhlaví. Pojďme vytvořit další soubor, který má hlavičku. Spusťte tento příkaz vytvoří soubor s odpovídající hlavičky:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Pak zřetězení dvou souborů dohromady:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Datová sada obsahuje několik typů statistik pro každý e-mail:

* Sloupce jako **\_wordové\_frekvence *Word*** označují procento slov v e-mailu, která odpovídají *Wordu*. Pokud je například **\_slovo frekvence\_** nastavené na **1**, pak se v e-mailu *provedlo*1% všech slov.
* Sloupce jako **char\_frekvence\_*char*** označují procento všech znaků v e-mailu, které jsou typu *char*.
* **velké\_spustit\_délka\_nejdelší** představuje nejdelší doba posloupnost velká písmena.
* **velké\_spustit\_délka\_průměrné** je průměrná délka všech pořadí velká písmena.
* **velké\_spustit\_délka\_celkový** je celková délka všech pořadí velká písmena.
* **spam** označuje, jestli e-mailu byla považována za spam nebo ne (1 = nevyžádané pošty, 0 = nevyžádanou).

## <a name="explore-the-dataset-by-using-r-open"></a>Prozkoumat datovou sadu pomocí jazyka R Open

Pojďme se podívat na data a udělat několik základních strojových kurzů pomocí jazyka R. DSVM se dodává s předinstalovaným [Microsoft R Open](https://mran.revolutionanalytics.com/open/) . Vícevláknové matematické knihovny v předinstalované verzi jazyka R nabízejí lepší výkon než verze s jedním vláknem. Jazyk R Open poskytuje také reprodukovatelnost prostřednictvím snímku úložiště balíčků CRAN.

Pokud chcete získat kopie ukázek kódu, které se používají v tomto průvodci, použijte Git a naklonujte úložiště Azure-Machine-Learning-data-věda. Git je předem nainstalovaný na DSVM. Na příkazovém řádku Gitu spusťte:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Otevřete okno terminálu a spusťte novou relaci R v interaktivní konzole R. Můžete také použít RStudio, který je předinstalován na DSVM.

Import dat a nastavení prostředí:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Chcete-li zobrazit souhrnné statistické údaje o jednotlivých sloupců:

    summary(data)

Pro jiné zobrazení dat:

    str(data)

Toto zobrazení ukazuje typ každé proměnné a prvních několik hodnot v datové sadě.

**Nevyžádané pošty** sloupec byl načten jako celé číslo, ale je ve skutečnosti kategorií proměnné (nebo faktor). Nastavte její typ:

    data$spam <- as.factor(data$spam)

K provedení některých průzkumné analýzy použijte balíček [ggplot2](https://ggplot2.tidyverse.org/) , oblíbenou knihovnu grafů pro R, která je předinstalována na DSVM. Na základě souhrnných dat zobrazených dříve máme souhrnnou statistiku o frekvenci znaku vykřičníku. Nyní tyto frekvence vykreslíme tak, že spustíte následující příkazy:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Vzhledem k tomu, že se nulový pruh grafu zkosí, Pojďme ho eliminovat:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Je netriviálníá hustota nad 1, která vypadá zajímavě. Pojďme se podívat jenom na tato data:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Pak ho rozdělte pomocí spamu oproti HAM:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Tyto příklady by vám pomohly dělat podobné možnosti a prozkoumat data v ostatních sloupcích.

## <a name="train-and-test-a-machine-learning-model"></a>Výuka a testování modelu strojového učení

Vydejte si několik modelů strojového učení pro klasifikaci e-mailů v datové sadě, které obsahují buď spam, nebo HAM. V této části se naučíme model rozhodovacího stromu a náhodný model doménové struktury. Pak otestujeme přesnost předpovědi.

> [!NOTE]
> Balíček *rpart* (rekurzivní vytváření oddílů a regrese) použitý v následujícím kódu je již na DSVM nainstalován.

Nejprve rozdělit datovou sadu na sady školení a sady testů:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

Pak vytvořte rozhodovací strom pro klasifikaci e-mailů:

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Tady je výsledek:

![Diagram vytvořeného rozhodovacího stromu](./media/linux-dsvm-walkthrough/decision-tree.png)

Pokud chcete zjistit, jak dobře funguje na cvičnou sadou, použijte následující kód:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Chcete-li zjistit, jak dobře funguje v sadě testů:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Také si vyzkoušíme model náhodného doménové struktury. Náhodné doménové struktury proškolují velké množství rozhodovacích stromů a zaoutputují třídu, která je režimem klasifikací ze všech jednotlivých rozhodovacích stromů. Poskytují výkonnější přístup strojového učení, protože jsou správné pro dosažení většího počtu rozhodovacích stromů modelu, aby overfit školicí datovou sadu.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-machine-learning-studio"></a>Nasazení modelu pro Azure Machine Learning Studio

[Azure Machine Learning Studio](https://studio.azureml.net/) je cloudová služba usnadňující sestavování a nasazování modelů prediktivní analýzy. Dobrým prvkem Azure Machine Learning Studio je jeho schopnost publikovat všechny funkce R jako webovou službu. Balíček Azure Machine Learning Studio R usnadňuje nasazení přímo z relace jazyka R v DSVM.

Pokud chcete nasadit kód rozhodovacího stromu z předchozí části, přihlaste se k Azure Machine Learning Studio. Potřebujete ID vašeho pracovního prostoru a autorizační token pro přihlášení. Chcete-li najít tyto hodnoty a inicializovat proměnné Azure Machine Learning, proveďte tyto kroky:

1. V nabídce vlevo vyberte **Nastavení**. Poznamenejte si hodnotu **ID pracovního prostoru**.

   ![ID pracovního prostoru Azure Machine Learning Studio](./media/linux-dsvm-walkthrough/workspace-id.png)

1. Vyberte kartu **tokeny autorizace** . Poznamenejte si hodnotu **primárního autorizačního tokenu**.

   ![Primární autorizační token Azure Machine Learning Studio](./media/linux-dsvm-walkthrough/workspace-token.png)
1. Načtěte balíček **AzureML** a pak nastavte hodnoty proměnných pomocí tokenu a ID pracovního prostoru v relaci jazyka R na DSVM:

        if(!require("AzureML")) install.packages("AzureML")
        require(AzureML)
        wsAuth = "<authorization-token>"
        wsID = "<workspace-id>"

1. Pojďme model zjednodušit a aby tato ukázka usnadnil. Vyberte tři proměnné ve stromovém stromu, které jsou nejblíže kořenovému adresáři a sestavte nový strom pomocí pouze těch tří proměnných:

        colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
        smallTrainSet <- trainSet[, colNames]
        smallTestSet <- testSet[, colNames]
        model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

1. Potřebujeme předpovědi funkci, která přebírá funkce jako vstup a vrátí predikované hodnoty:

        predictSpam <- function(newdata) {
        predictDF <- predict(model.rpart, newdata = newdata)
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
        }


1. Publikujte funkci **predictSpam** do AzureML pomocí funkce **publishWebService** :

        spamWebService <- publishWebService(ws, fun = predictSpam, name="spamWebService", inputSchema = smallTrainSet, data.frame=TRUE)

1. Tato funkce přijímá funkci **predictSpam** , vytvoří webovou službu s názvem **spamWebService** , která má definované vstupy a výstupy, a potom vrátí informace o novém koncovém bodu.

    Tento příkaz slouží k zobrazení podrobností o nejnovější publikované webové službě, včetně koncového bodu rozhraní API a přístupových klíčů:

        s<-tail(services(ws, name = "spamWebService"), 1)
        ep <- endpoints(ws,s)
        ep

1. Můžete vyzkoušet na prvních 10 řádků testu nastavte:

        consume(ep, smallTestSet[1:10, ])

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Výukové kurzy a návody pro hloubkové učení

Kromě ukázek založených na rozhraní je k dispozici také sada komplexních návodů. Tyto kurzy vám nastartování vývoje aplikací obsáhlého learningu v doménách, jako je obrázek a text/language understanding.

- [Spouštění neuronovéch sítí napříč různými architekturami](https://github.com/ilkarman/DeepLearningFrameworks): Podrobný návod, který ukazuje, jak migrovat kód z jednoho rozhraní do jiného. Také ukazuje, jak porovnat model a běhový výkon napříč platformami. 

- Návod, [jak sestavit ucelené řešení pro detekci produktů v rámci imagí](https://github.com/Azure/cortana-intelligence-product-detection-from-images): Detekce obrázku je technika, která může vyhledat a klasifikovat objekty v rámci imagí. Technologie nabízí možnost přinést velké výhody v mnoha obchodních doménách, které jsou v reálném čase. Například prodejci, můžete použít tuto techniku k určení, který produkt a zákazníka má neexistoval z police. Tyto informace pomáhají zase úložiště spravovat inventář produktů. 

- [Obsáhlý Learning pro zvuk](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): V tomto kurzu se dozvíte, jak vytvořit model hloubkového učení pro detekci zvukových událostí v [datové sadě městských zvuků](https://urbansounddataset.weebly.com/). Tento kurz poskytuje přehled o tom, jak pracovat se zvukovými daty.

- [Klasifikace textových dokumentů](https://github.com/anargyri/lstm_han): Tento návod ukazuje, jak sestavit a vytvořit výuku dvou různých neuronovéch síťových architektur: Hierarchická síťová pozornost a dlouhodobá krátkodobá paměť (LSTM). Tyto neuronové sítě pomocí rozhraní Keras API pro obsáhlý learning zařazoval dokumenty do textu. Keras je front-end pro tři nejoblíbenější architektury hloubkového učení: Microsoft Cognitive Toolkit, TensorFlow a Theano.

## <a name="other-tools"></a>Jiné nástroje

V dalších částech se dozvíte, jak používat některé nástroje, které jsou nainstalované na DSVM Linux. Probereme tyto nástroje:

* XGBoost
* Python
* JupyterHub
* Rattle
* PostgreSQL a SQuirreL SQL
* SQL Server Data Warehouse

### <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/) poskytuje rychlou a přesnou zesílenou implementaci stromu.

    require(xgboost)
    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

    bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

    pred <- predict(bst, data.matrix(testSet[, 0:57]))
    accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
    print(paste("test accuracy = ", accuracy))

XGBoost může také volat z Pythonu nebo z příkazového řádku.

### <a name="python"></a>Python

V případě vývoje v jazyce Python jsou v DSVM nainstalovány distribuce Anaconda Pythonu 3,5 a 2,7.

> [!NOTE]
> Anaconda distribuce zahrnuje [conda](https://conda.pydata.org/docs/index.html). Pomocí conda můžete vytvářet vlastní prostředí Pythonu, která mají v nich nainstalované různé verze nebo balíčky.

Pojďme si přečtěte část spambase DataSet a klasifikovat e-maily s podporou vektorových počítačů v Scikit-učení:

    import pandas
    from sklearn import svm
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

K vytvoření predikcí:

    clf.predict(X.ix[0:20, :])

Abychom předvedli, jak publikovat Azure Machine Learning koncový bod, vytvoříme ještě více základních modelů. Použijeme tři proměnné, které jsme použili při předchozím publikování modelu R:

    X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Postup publikování modelu pro Azure Machine Learning:

    # Publish the model.
    workspace_id = "<workspace-id>"
    workspace_token = "<workspace-token>"
    from azureml import services
    @services.publish(workspace_id, workspace_token)
    @services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
    @services.returns(int) # 0 or 1
    def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
        inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
        return clf.predict(inputArray)

    # Get some info about the resulting model.
    predictSpam.service.url
    predictSpam.service.api_key

    # Call the model
    predictSpam.service(1, 1, 1)


> [!NOTE]
> Tato možnost je k dispozici pouze pro Python 2,7. V Pythonu 3,5 zatím není podporována. Chcete-li spustit, použijte **/Anaconda/bin/python2.7**.

### <a name="jupyterhub"></a>JupyterHub

Anaconda distribuce v DSVM se dodává s Jupyter Notebook, prostředím pro různé platformy pro sdílení kódu Python, R nebo Helena a analýzy. K Jupyter Notebook k dispozici prostřednictvím JupyterHub. Přihlašujete se pomocí místního uživatelského jména a hesla pro Linux na adrese\<https://DSVM DNS nebo IP adrese\>: 8000/. Všechny konfigurační soubory pro JupyterHub se nacházejí v/etc/jupyterhub.

> [!NOTE]
> Chcete-li použít Správce balíčků Pythonu ( `pip` prostřednictvím příkazu) z Jupyter notebook v aktuálním jádru, použijte tento příkaz v buňce kódu:
>
>   ```python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> Chcete-li použít instalační program conda ( `conda` prostřednictvím příkazu) z Jupyter notebook v aktuálním jádru, použijte tento příkaz v buňce kódu:
>
>   ```python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

V DSVM je již nainstalováno několik ukázkových poznámkových bloků:

* Ukázkové poznámkové bloky Pythonu:
  * [IntroToJupyterPython. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
  * [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)
* Ukázka poznámkového bloku R:
  * [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> Jazyk Helena je k dispozici také z příkazového řádku na DSVM Linux.

### <a name="rattle"></a>Rattle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (*R* *a*nalytická *t*OOL *t*o *L*získat *E*asily) je grafický nástroj R pro dolování dat. Rattle má intuitivní rozhraní, které usnadňuje načítání, prozkoumávání a transformaci dat a vytváření a vyhodnocování modelů. [Rattle: Grafické rozhraní dolování dat pro R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) poskytuje návod, který ukazuje funkce Rattle.

Nainstalujte a spusťte Rattle spuštěním těchto příkazů:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> Nemusíte instalovat Rattle na DSVM. Při otevření Rattle se ale může zobrazit výzva k instalaci dalších balíčků.

Rattle používá rozhraní založené na kartě. Většina karet odpovídá krokům v rámci [vědeckého zpracování týmových dat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), jako je načítání dat nebo zkoumání dat. Vědecké zpracování dat toky zleva doprava jednotlivé karty. Poslední karta obsahuje protokol příkazů jazyka R, které byly spuštěny pomocí Rattle.

Načíst a nakonfigurovat datové sady:

1. Chcete-li načíst soubor, vyberte kartu **data** .
1. Zvolte selektor vedle **filename**a pak vyberte **spambaseHeaders. data**.
1. Načíst soubor. Vyberte **provést**. Měl by se zobrazit shrnutí každého sloupce, včetně jeho identifikovaného datového typu. bez ohledu na to, zda se jedná o vstup, cíl nebo jiný typ proměnné; a počet jedinečných hodnot.
1. Rattle správně zjistila **nevyžádané pošty** sloupce jako cíl. Vyberte sloupec **spam** a pak nastavte **cílový datový typ** na **Categoric**.

K prozkoumání dat:

1. Vyberte **prozkoumat** kartu.
1. Chcete-li zobrazit informace o typech proměnných a některých souhrnných statistik, vyberte možnost **Souhrn** > **provést**.
1. Chcete-li zobrazit další typy statistik o jednotlivých proměnných, vyberte další možnosti, například **Popis** nebo **základy**.

Můžete také použít kartu **prozkoumat** k vygenerování přehlednéch ploch. K vykreslení histogram dat:

1. Vyberte **distribuce**.
1. V případě **word_freq_remove** a **word_freq_you**vyberte **histogram**.
1. Vyberte **Provést**. V jednom okně grafu _by se měla_ zobrazit jak zobrazení hustoty, kde je jasné, že se v e-mailech zdá mnohem častěji, než je třeba _Odebrat_.

Tato **korelace** je také zajímavá. Vytvoření grafu:

1. Jako **typ**vyberte **korelace**.
1. Vyberte **Provést**.
1. Rattle vás upozorní, doporučuje se maximálně 40 proměnné. Vyberte **Ano** k vykreslení zobrazení.

Existují některé zajímavé korelace, které se přidávají: _technologie_ se silně korelují se _HP_ a _Labs_, například. Také se silně koreluje s _650_ , protože kód oblasti dárce datové sady je 650.

Číselné hodnoty pro korelace mezi slovy jsou k dispozici v okně **prozkoumat** . Je zajímavá Poznámka, například tato _technologie_ se negativně koreluje s _vašimi_ _peníze_a.

Rattle můžete transformovat datové sady pro zpracování některé běžné problémy. Může například měnit škálování funkcí, imputace chybějících hodnot, zpracovávat odlehlé hodnoty a odstraňovat proměnné nebo pozorování, které mají chybějící data. Rattle může také identifikovat pravidla přidružení mezi pozorováními a proměnnými. Tyto karty nejsou pokryté v tomto úvodním návodu.

Rattle také může spustit analýzu clusteru. Umožňuje vyloučit některé funkce pro usnadnění čtení výstupu. Na kartě **data** vyberte **Ignorovat** vedle každé z těchto proměnných s výjimkou těchto 10 položek:

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* nevyžádané pošty

Vraťte se na kartu **clusteru** . Vyberte **KMeans**a pak nastavte **počet clusterů** na **4**. Vyberte **Provést**. Výsledky se zobrazí v okně výstup. Jeden cluster má vysokou frekvenci _Jiří_ a _HP_a je pravděpodobně legitimním podnikovým e-mailem.

Postup sestavení základního modelu Machine Learning pro rozhodovací strom:

1. Vyberte **modelu** kartě
1. Pro **typ**vyberte **strom**.
1. Vyberte **Execute** k zobrazení stromu v textové podobě, v okně výstup.
1. Vyberte **nakreslit** tlačítko Zobrazit grafické verze. Rozhodovací strom vypadá podobně jako strom, který jsme dříve získali pomocí rpart.

Užitečnou funkcí Rattle je schopnost spustit několik metod strojového učení a rychle je vyhodnotit. Tady je postup:

1. Jako **typ**vyberte **vše**.
1. Vyberte **Provést**.
1. Po dokončení běhu Rattle můžete vybrat libovolnou hodnotu **typu** , jako je **SVM**, a zobrazit výsledky.
1. Můžete také porovnat výkon modelů v sadě ověřování pomocí karty **vyhodnocení** . Například **chybová matice** výběru se dozvíte, chybová matice, celkový chyba a chyba průměrné třídy pro každý model na sadě ověření. Můžete také kreslit křivky ROC, spustit analýzu citlivosti a provádět další typy vyhodnocení modelu.

Po dokončení vytváření modelů vyberte kartu **protokol** . zobrazí se kód R, který byl spuštěn Rattle během vaší relace. Můžete vybrat **exportovat** tlačítko a uložte ho.

> [!NOTE]
> Aktuální verze Rattle obsahuje chybu. Chcete-li změnit skript nebo ho použít k opakování kroků později, je nutné před **#** *exportem tohoto protokolu* vložit znak... v textu protokolu.

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL a SQuirreL SQL

Datové VĚDY se dodává s nainstalovaný systém PostgreSQL. PostgreSQL je relační databáze sofistikované, open source. V této části se dozvíte, jak načíst datovou sadu spambase do PostgreSQL a pak na ni zadat dotaz.

Než budete moci načíst data, je nutné, abyste povolili ověřování pomocí hesla od místního hostitele. Na příkazovém řádku spusťte:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

V dolní části konfiguračního souboru se několik řádků, které podrobně popisují povolených připojení:

    # "local" is only for Unix domain socket connections:
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Změňte řádek **místních připojení IPv4** tak, aby používal **MD5** místo **Ident**, abyste se mohli přihlásit pomocí uživatelského jména a hesla:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Pak restartujte službu PostgreSQL:

    sudo systemctl restart postgresql

Pokud chcete jako integrovaného uživatele Postgres spustit *psql* (interaktivní terminál pro PostgreSQL), spusťte tento příkaz:

    sudo -u postgres psql

Vytvořte nový uživatelský účet pomocí uživatelského jména účtu systému Linux, který jste použili k přihlášení. Vytvořit heslo:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Přihlaste se k psql:

    psql

Importujte data do nové databáze:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Teď se podívejme na data a spustíme některé dotazy pomocí SQuirreL SQL, což je grafický nástroj, který můžete použít k interakci s databázemi prostřednictvím ovladače JDBC.

Začněte tím, že v nabídce **aplikace** otevřete SQuirreL SQL. Nastavení ovladače:

1. Vyberte možnost **Windows** > **View Drivers**.
1. Klikněte pravým tlačítkem na **PostgreSQL** a vyberte **Upravit ovladač**.
1. Vyberte **Další cesta** > ke třídě**Přidat**.
1. Jako **název souboru**zadejte **/usr/share/Java/jdbcdrivers/PostgreSQL-9.4.1208.jre6.jar**.
1. Vyberte **Open** (Otevřít).
1. Vyberte možnost **seznam ovladačů**. Jako **název třídy**vyberte **org. PostgreSQL. Driver**a pak vyberte **OK**.

Nastavit připojení k místnímu serveru:

1. Vyberte možnost **Windows** > **View aliasy.**
1. Kliknutím na **+** tlačítko vytvořte nový alias. Jako název nového aliasu zadejte **Nevyžádaná databáze**. 
1. V případě **ovladače**vyberte **PostgreSQL**.
1. Nastavení adresy URL **jdbc:postgresql://localhost/spam**.
1. Zadejte své uživatelské jméno a heslo.
1. Vyberte **OK**.
1. Otevřete **připojení** okna, dvakrát klikněte **nevyžádané pošty databáze** alias.
1. Vyberte **Connect** (Připojit).

Pokud chcete spustit některé dotazy:

1. Vyberte **SQL** kartu.
1. Do pole dotaz v horní části karty **SQL** zadejte základní dotaz, například `SELECT * from data;`.
1. Spusťte dotaz stisknutím kombinace kláves CTRL + ENTER. Ve výchozím nastavení vrátí SQuirreL SQL prvních 100 řádků z dotazu.

Existuje mnoho dalších dotazů, které můžete použít k prozkoumání těchto dat. Příklad, jak funguje frekvence slovo *zkontrolujte* se liší mezi nevyžádané pošty se šunkou?

    SELECT avg(word_freq_make), spam from data group by spam;

Nebo, jaké jsou charakteristiky e-mailu, které často obsahují *3D*?

    SELECT * from data order by word_freq_3d desc;

Většina e-mailů, které mají velký výskyt *3D* , je nevyžádaná. Tyto informace mohou být užitečné při vytváření prediktivního modelu pro klasifikaci e-mailů.

Pokud chcete strojové učení dělat pomocí dat uložených v databázi PostgreSQL, zvažte použití [MADlib](https://madlib.incubator.apache.org/).

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

Azure SQL Data Warehouse je cloudová, škálovatelná databáze, která dokáže zpracovávat obrovské objemy dat, relačních i nerelačních. Další informace najdete v tématu [co je Azure SQL Data Warehouse?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Pro připojení k datovému skladu a vytvoření tabulky, spusťte následující příkaz z příkazového řádku:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Na příkazovém řádku nástroje Sqlcmd spusťte tento příkaz:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Zkopírujte data pomocí BCP:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> Stažený soubor obsahuje konce řádků ve stylu systému Windows. Nástroj BCP očekává konce řádku ve stylu UNIX. K informování BCP použijte příznak-r.

Pak dotaz pomocí Sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

Můžete také zadat dotaz pomocí SQuirreL SQL. Použijte postup podobný PostgreSQL pomocí ovladače SQL Server JDBC. Ovladač JDBC je ve složce/usr/share/Java/jdbcdrivers/sqljdbc42.jar.

## <a name="next-steps"></a>Další kroky

Přehled článků, které vás provedou úkoly, které tvoří proces pro datové vědy v Azure, najdete v tématu věnovaném [vědeckému zpracování týmových dat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview).

Popis komplexních návodů, které ukazují kroky v vědeckém procesu týmového zpracování dat pro konkrétní scénáře, najdete v tématu návody k [týmovým procesům zpracování dat](../team-data-science-process/walkthroughs.md). Názorné postupy také ukazují, jak zkombinovat cloudové a místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací.
