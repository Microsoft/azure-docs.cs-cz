---
title: Naučte se používat virtuální počítač Linux datové vědy
titleSuffix: Azure
description: Jak provádět několik běžných úloh vědeckého zpracování virtuálního počítače s Linuxem datové vědy.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.custom: seodec18
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: gokuma
ms.openlocfilehash: fafa680f877060f1e7d96c60f52e3033eeb38553
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190631"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-on-azure"></a>Pro datovou vědu Data Science virtuálním počítačem s Linuxem v Azure
Tento návod ukazuje, jak provádět několik běžných úloh vědeckého zpracování virtuálního počítače s Linuxem datové vědy. Systému Linux dat virtuálního počítače VĚDY je image virtuálního počítače k dispozici v Azure, který může být předinstalovaný s kolekcí nástrojů používaných pro analýzu dat a strojové učení. Klíčové softwarové komponenty je uvedeno v [zřídit virtuální počítač Linux datové vědy](linux-dsvm-intro.md) tématu. Image virtuálního počítače umožňuje snadno a rychle začít, datových věd v řádech minut, aniž byste museli instalovat a konfigurovat každý z nástrojů jednotlivě. Můžete snadno vertikálně navýšit kapacitu virtuálních počítačů, v případě potřeby a zastavte ji není při použití. Proto tento prostředek je elastická a nákladově efektivní.

Úloh datových věd v tomto názorném postupu jsme vám ukázali, postupujte podle kroků uvedených v [vědecké zpracování týmových dat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview). Tento proces zajišťuje systematický přístup k datové vědy, které umožňuje týmům odborníků přes data efektivně spolupracovat přes životní cyklus sestavovat inteligentní aplikace. Vědecké zpracování dat také poskytuje rozšiřovatelnou platformu pro iterativní pro datové vědy, které může být následován jednotlivec.

Analyzujeme [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) datová sada v tomto názorném postupu. Toto je sada e-mailů, které jsou označené jako spam nebo šunku (tj. nejsou nevyžádané pošty), a také obsahuje statistikami o obsah e-mailů. Statistiky zahrnuté jsou popsány v následujících, ale jeden oddíl.

## <a name="prerequisites"></a>Požadavky
Než budete moct použít virtuální počítač s Linuxem datové vědy, musíte mít následující:

* **Předplatného Azure**. Pokud není ještě nemáte, přečtěte si téma [vytvořte si bezplatný účet Azure ještě dnes](https://azure.microsoft.com/free/).
* A [ **pro datovou vědu VM pro Linux**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Informace o zřizování tohoto virtuálního počítače, naleznete v tématu [zřídit virtuální počítač Linux datové vědy](linux-dsvm-intro.md).
* [X2Go](http://wiki.x2go.org/doku.php) v počítači nainstalovaný a otevřít relaci XFCE. Informace o instalaci a konfiguraci **X2Go klienta**, naleznete v tématu [instalace a konfigurace klienta X2Go](linux-dsvm-intro.md#installing-and-configuring-x2go-client).
* Pro posouvání plynulost přepnout příznak gfx.xrender.enabled v o: konfigurace v prohlížeči FireFox virtuálních počítačů. [Najdete v článku najdete tady. ](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Také vzít v úvahu při přepínání *mousewheel.enable_pixel_scrolling* na hodnotu False. [Pokyny v tomto poli.](https://support.mozilla.org/en-US/questions/981140)
* **AzureML účet**. Pokud již nemáte, zaregistrujte si novou na [domovskou stránku Azure ml](https://studio.azureml.net/). Je bezplatné využití vrstvu vám pomůžou začít.

## <a name="download-the-spambase-dataset"></a>Stáhnout spambase datovou sadu
[Spambase](https://archive.ics.uci.edu/ml/datasets/spambase) datová sada je relativně malou sadu dat, která obsahuje pouze 4601 příklady. Je to pohodlné velikost při představením toho, že některé z klíčových funkcí virtuální počítač pro datové vědy protože uchovává požadavky na prostředky středně velká.

> [!NOTE]
> Tento návod byl vytvořen na D2 v2 velikosti Data Science virtuálního počítače s Linuxem (CentOS Edition). Tato velikost DSVM je schopna zpracovávat postupy v tomto názorném postupu.
>
>

Pokud potřebujete další úložný prostor, můžete vytvořit další disky a připojte je k vašemu virtuálnímu počítači. Tyto disky prostřednictvím trvalého úložiště Azure, tak jejich data se zachovají i v případě, že server je znovu zajištěny kvůli změně velikosti nebo vypnutí. Přidejte disk a připojit ho k virtuálnímu počítači, postupujte podle pokynů v [přidání disku do virtuálního počítače s Linuxem](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Tyto kroky používají rozhraní příkazového řádku Azure (Azure CLI), který je už nainstalovaný na datové VĚDY. Tyto postupy můžete udělat tak zcela v samotných virtuálních počítačů. Další možností pro zvýšení úložiště je použití [soubory Azure](../../storage/files/storage-how-to-use-files-linux.md).

Chcete-li stáhnout data, otevřete okno terminálu a spusťte tento příkaz:

    wget http://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Stažený soubor nemá řádek záhlaví, Pojďme vytvořit jiný soubor, který mají záhlaví. Spusťte tento příkaz vytvoří soubor s odpovídající hlavičky:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Pak je zřetězí dva soubory společně se příkaz:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Objekt dataset obsahuje několik typů statistik v každém e-mailu:

* U sloupců obsahujících ***slovo\_frekvence\_slovo*** označuje procento slova v e-mailu, které odpovídají *slovo*. Například pokud *slovo\_frekvence\_zkontrolujte* je 1, pak bylo 1 % všech slov v e-mailu *Ujistěte se,*.
* U sloupců obsahujících ***char\_frekvence\_CHAR*** označuje procento všechny znaky v e-mailu, které byly *CHAR*.
* ***velké\_spustit\_délka\_nejdelší*** představuje nejdelší doba posloupnost velká písmena.
* ***velké\_spustit\_délka\_průměrné*** je průměrná délka všech pořadí velká písmena.
* ***velké\_spustit\_délka\_celkový*** je celková délka všech pořadí velká písmena.
* ***spam*** označuje, jestli e-mailu byla považována za spam nebo ne (1 = nevyžádané pošty, 0 = nevyžádanou).

## <a name="explore-the-dataset-with-microsoft-r-open"></a>Prozkoumejte datovou sadu s Microsoft R Open
Pojďme prozkoumat data a provádět některé základní strojového učení pomocí jazyka R. Virtuální počítač pro datové vědy se dodává s [Microsoft R Open](https://mran.revolutionanalytics.com/open/) předinstalovaným. Vícevláknové matematické knihovny v této verzi systému R nabízí lepší výkon než různé verze s jedním vláknem. Microsoft R Open také poskytuje reprodukovatelnost pomocí snímku úložiště balíčků CRAN.

K získání kopií ukázky kódu použít v tomto podrobném návodu, naklonujte **Azure-Machine-Learning-datové vědy** úložiště pomocí git, které je předem nainstalované na virtuálním počítači. Z příkazového řádku git spusťte:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Otevřete okno terminálu a spusťte novou relaci r. s interaktivní konzolou R nebo používat RStudio jako předinstalované na počítači.


Pokud chcete importovat data a nastavení prostředí, spusťte:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Chcete-li zobrazit souhrnné statistické údaje o jednotlivých sloupců:

    summary(data)

Pro jiné zobrazení dat:

    str(data)

Zobrazí typ každou proměnnou a první několik hodnot v datové sadě.

*Nevyžádané pošty* sloupec byl načten jako celé číslo, ale je ve skutečnosti kategorií proměnné (nebo faktor). Nastavte její typ:

    data$spam <- as.factor(data$spam)

Chcete-li provést průzkumné analýzy, použijte [ggplot2](http://ggplot2.org/) balíček, Oblíbené grafovým knihovny pro R, který je už nainstalovaný na virtuálním počítači. Mějte na paměti, z souhrnná data zobrazí dříve, vidíme, že souhrnné statistiky týkající se četnosti znak vykřičník. Pojďme vykreslení těchto frekvencí tady pomocí následujících příkazů:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Protože panelu nula je zkosení vykreslení zbavíme ho:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Je netriviální hustota výše 1, která vypadá zajímavé. Podívejme se na právě tato data:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Potom ji rozdělte podle šunka vs nevyžádané pošty:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Tyto příklady by měl umožňují vytvářet podobné vykreslení další sloupce, které chcete prozkoumat data obsažená v nich.

## <a name="train-and-test-an-ml-model"></a>Trénování a testování modelu ML
Nyní Pojďme trénování několik modelů strojového učení klasifikovat e-maily v datové sadě jako obsahující span nebo šunka. Jsme trénování modelu stromu rozhodnutí a model náhodného doménové struktury v této části a potom otestovat jejich přesnost jejich předpovědi.

> [!NOTE]
> Balíček rpart (rekurzivní dělení a regresní stromy) použitý v následujícím kódu je již nainstalována na virtuální počítač pro datové vědy.
>
>

První, Pojďme rozdělit datovou sadu na trénovací a testovací sady:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

A pak vytvořte rozhodovacího stromu a klasifikaci e-mailů.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Tady je výsledek:

![1](./media/linux-dsvm-walkthrough/decision-tree.png)

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

Také si vyzkoušíme model náhodného doménové struktury. Náhodné doménovými strukturami trénování velké množství rozhodovacích stromů a výstup třídu, která je režim klasifikace z všechny jednotlivé rozhodovacích stromů. Poskytují účinnější služby machine learning přístup, jak opravit tendenci modelu rozhodovací strom overfit trénovací datové sady.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-ml"></a>Nasazení modelu Azure ML
[Azure Machine Learning Studio](https://studio.azureml.net/) (AzureML) je Cloudová služba, která umožňuje snadno vytvářet a nasazovat modely prediktivní analýzy. Mezi skvělé funkce Azure ml je jeho schopnost publikovat všechny funkce R jako webovou službu. Balíček AzureML R usnadňuje nasazení provést přímo z našich relace jazyka R na datové VĚDY.

Nasazení kódu stromu rozhodnutí z předchozí části, budete muset přihlásit k Azure Machine Learning studiu. Potřebujete ID vašeho pracovního prostoru a autorizační token pro přihlášení. Tyto hodnoty vyhledat a inicializaci proměnných Azure ml s nimi:

Vyberte **nastavení** v nabídce vlevo. Poznámka: vaše **ID pracovního prostoru**. ![2](./media/linux-dsvm-walkthrough/workspace-id.png)

Vyberte **autorizačních tokenech** režie nabídce a Všimněte si vaše **primární autorizační Token**.![ 3](./media/linux-dsvm-walkthrough/workspace-token.png)

Zatížení **AzureML** balíček a pak nastavte hodnoty proměnných se token a pracovní prostor ID v relaci jazyka R na datové VĚDY:

    if(!require("AzureML")) install.packages("AzureML")
    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Pojďme model zjednodušit a aby tato ukázka usnadnil. Vyberte tří proměnných v rozhodovacím stromu nejbližší do kořenového adresáře a vytvářet nové větve pomocí jenom těchto tří proměnných:

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

Potřebujeme předpovědi funkci, která přebírá funkce jako vstup a vrátí predikované hodnoty:

    predictSpam <- function(newdata) {
      predictDF <- predict(model.rpart, newdata = newdata)
      return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }


Pomocí Azure ml publikovat predictSpam funkce **publishWebService** funkce:

    spamWebService <- publishWebService(ws, fun = predictSpam, name="spamWebService", inputSchema = smallTrainSet, data.frame=TRUE)


Tato funkce přijímá **predictSpam** fungovat, vytvoří webovou službu s názvem **spamWebService** s definované vstupy a výstupy a vrátí informace o nový koncový bod.

Zobrazit podrobnosti o nejnovější publikované webové služby, včetně jeho koncový bod rozhraní API a přístupové klíče pomocí příkazu:

    s<-tail(services(ws, name = "spamWebService"), 1)
    ep <- endpoints(ws,s)
    ep

Můžete vyzkoušet na prvních 10 řádků testu nastavte:

    consume(ep, smallTestSet[1:10, ])


## <a name="use-other-tools-available"></a>Použít jiné nástroje, které jsou k dispozici
Zbývající části ukazují, jak používat některé z nástroje nainstalované na virtuálním počítači Linux datové vědy. Tady je seznam nástrojů, které jsou popsány:

* XGBoost
* Python
* Jupyterhub
* Rattle
* PostgreSQL & Squirrel SQL
* SQL Server Data Warehouse

## <a name="xgboost"></a>XGBoost
[XGBoost](https://xgboost.readthedocs.org/en/latest/) je nástroj, který poskytuje rychlý a přesné Posílený stromu implementaci.

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

XGBoost můžete také volat z pythonu nebo příkazového řádku.

## <a name="python"></a>Python
Pro vývoj pomocí Pythonu distribuce Anaconda Python 2.7 a 3.5 nainstalovaných v datové VĚDY.

> [!NOTE]
> Zahrnuje distribuční Anaconda [Conda](http://conda.pydata.org/docs/index.html), který slouží k vytvoření vlastního prostředí pro Python, které mají různé verze a/nebo v nich nainstalované balíčky.
>
>

Umožňuje číst v některých spambase datové sady a klasifikovat e-maily s počítači vektorové podpory v scikit-informace:

    import pandas
    from sklearn import svm    
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

K vytvoření predikcí:

    clf.predict(X.ix[0:20, :])

Ukážeme, jak publikovat koncový bod Azure ml, vytvoříme jednodušší modelu tří proměnných jako jsme to udělali při jsme dříve publikovali R modelu.

    X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Publikování modelu Azure ml:

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
> To je dostupná jenom pro python 2.7 a není dosud nepodporováno u 3.5. Spustit s **/anaconda/bin/python2.7**.
>
>

## <a name="jupyterhub"></a>Jupyterhub
Anaconda distribuce v datové VĚDY se dodává s poznámkového bloku Jupyter, – multiplatformního prostředí pro sdílení kódu Python, R nebo Helena a analýzy. Poznámkový blok Jupyter je přístupný prostřednictvím JupyterHub. Přihlásit pomocí vašeho místního systému Linux uživatelské jméno a heslo na ***https://\<název DNS virtuálního počítače nebo IP adresu\>: 8000 /***. Všechny konfigurační soubory pro JupyterHub se nacházejí v adresáři **/etc/jupyterhub**.

> [!NOTE]
> Použití Správce balíčků Pythonu (prostřednictvím `pip` příkaz) z poznámkového bloku Jupyter v jádru aktuální příkaz lze použít v buňky kódu, například:
  ```python
   import sys
   ! {sys.executable} -m pip install numpy -y
  ```
>
>

> [!NOTE]
> Použití Instalační služby systému Conda (prostřednictvím `conda` příkaz) z poznámkového bloku Jupyter v jádru aktuální příkaz lze použít v buňky kódu, například:
  ```python
   import sys
   ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
  ```
>
>

Několik ukázkové poznámkové bloky jsou již nainstalovány ve virtuálním počítači:

* Zobrazit [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) pro ukázkový poznámkový blok Python.
* Zobrazit [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) ukázku **R** poznámkového bloku.
* Zobrazit [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) pro další ukázku **Python** poznámkového bloku.

> [!NOTE]
> Jazyk Julia je také k dispozici z příkazového řádku na virtuálním počítači Linux datové vědy.
>
>

## <a name="rattle"></a>Rattle
[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) grafického nástroje R pro dolování dat (R analytické nástroje pro další snadno) je. Obsahuje intuitivní rozhraní, které umožňuje snadno načíst, prozkoumat a transformace dat a sestavení a vyhodnocení modelů.  Tento článek [Rattle: Data Mining grafickým uživatelským rozhraním pro R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) poskytuje návod, který ukazuje jeho funkcí.

Instalace a spuštění Rattle pomocí následujících příkazů:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> Není nutné instalovat na datové VĚDY. Ale Rattle vás můžou přimět k instalaci dalších balíčků při načítání.
>
>

Rattle používá rozhraní založené na kartě. Většina karty odpovídají kroky v [vědecké zpracování dat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), jako je načítání dat nebo zkoumáte ho. Vědecké zpracování dat toky zleva doprava jednotlivé karty. Ale poslední karta obsahuje protokol R příkazy se spouští podle Rattle.

Načíst a nakonfigurovat datové sady:

* Chcete-li soubor načíst, vyberte **Data** kartu, pak
* Zvolte volič vedle **Filename** a zvolte **spambaseHeaders.data**.
* Načíst soubor. Vyberte **Execute** v horní řádek tlačítek. Měli byste vidět shrnutí každého sloupce, včetně jeho identifikované datového typu, ať už jde o vstup, cíl nebo jiným typem proměnné a počtem jedinečných hodnot.
* Rattle správně zjistila **nevyžádané pošty** sloupce jako cíl. Vyberte sloupec nevyžádané pošty a pak nastavit **cílový datový typ** k **Categoric**.

K prozkoumání dat:

* Vyberte **prozkoumat** kartu.
* Klikněte na tlačítko **souhrnu**, pak **Execute**, abyste si zobrazili některé informace o typech proměnných a některé souhrnnou statistiku.
* Chcete-li zobrazit další typy statistické údaje o každé proměnné, vyberte další možnosti, jako je **Describe** nebo **Základy**.

**Prozkoumat** kartě můžete také vytvořit mnoho přehledné grafy. K vykreslení histogram dat:

* Vyberte **distribuce**.
* Zkontrolujte **Histogram** pro **word_freq_remove** a **word_freq_you**.
* Vyberte **Provést**. Měli byste vidět oba hustota vykreslení v jednom grafu okno, kde je jasné, že slova "you" zobrazuje mnohem častěji v e-mailů než "remove".

Vykreslení korelace je také zajímavé. Chcete-li vytvořit:

* Zvolte **korelace** jako **typ**, pak
* Vyberte **Provést**.
* Rattle vás upozorní, doporučuje se maximálně 40 proměnné. Vyberte **Ano** k vykreslení zobrazení.

Existují některé zajímavé korelace, ke kterým: "technologie" je důrazně vztažen k "HP" a "labs", např. To jsou také silně korelována "650", protože kód oblasti dárců datová sada je 650.

V okně prozkoumat jsou k dispozici číselné hodnoty korelace mezi slovy. Zajímavé také je poznámka, například, že jsou "technologie" negativně korelována se "vaše" a "peníze".

Rattle můžete transformovat datové sady pro zpracování některé běžné problémy. Například umožňuje měřítko funkce, dává chybějící hodnoty, zpracování odlehlé hodnoty a odeberte proměnné nebo pozorování s chybějící data. Rattle můžete také určit pravidla přidružení mezi připomínky a/nebo proměnné. Tyto karty jsou mimo rozsah pro Tento názorný postup úvodní.

Rattle můžete také provádět analýzu clusteru. Umožňuje vyloučit některé funkce pro usnadnění čtení výstupu. Na **Data** kartě **Ignorovat** vedle každého proměnné s výjimkou těchto deset položek:

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

Pak přejděte zpátky do **clusteru** kartě **KMeans**a nastavte *počet clusterů* na 4. Potom **provést**. Výsledky se zobrazí v okně výstup. Jeden cluster má vysokou frekvencí "Milan" a "hp" a je pravděpodobně hlediska podniku oprávněný požadavek e-mailů.

Sestavení modelu strojového učení stromu jednoduché rozhodnutí:

* Vyberte **modelu** kartě
* Zvolte **stromu** jako **typ**.
* Vyberte **Execute** k zobrazení stromu v textové podobě, v okně výstup.
* Vyberte **nakreslit** tlačítko Zobrazit grafické verze. Ten vypadá velmi podobně jako stromu jsme získali dříve pomocí *rpart*.

Mezi skvělé funkce Rattle je schopnost rychle vyhodnotit a spustit několik metod machine learning. Tady je postup:

* Zvolte **všechny** pro **typ**.
* Vyberte **Provést**.
* Po dokončení můžete kliknout na kterýkoli jednotlivý **typ**, třeba **SVM**a prohlédněte si výsledky.
* Můžete také porovnat výkon jednotlivých modelů na ověření sady s použitím **vyhodnotit** kartu. Například **chybová matice** výběru se dozvíte, chybová matice, celkový chyba a chyba průměrné třídy pro každý model na sadě ověření.
* Můžete také vykreslení křivky roc s více TŘÍDAMI, provádět analýzu citlivosti a provádět jiné typy vyhodnocení modelu.

Jakmile budete hotovi, vytvářet modely, vyberte **protokolu** kartu k zobrazení kódu jazyka R spustit Rattle během vaší relace. Můžete vybrat **exportovat** tlačítko a uložte ho.

> [!NOTE]
> V aktuální verzi Rattle je chyba. K úpravě skriptu nebo ji použít k vaší kroky opakujte později, je třeba vložit před znak # * exportovat tento protokol... * text v protokolu.
>
>

## <a name="postgresql--squirrel-sql"></a>PostgreSQL & Squirrel SQL
Datové VĚDY se dodává s nainstalovaný systém PostgreSQL. PostgreSQL je relační databáze sofistikované, open source. Tato část ukazuje, jak načíst datovou sadu nevyžádané pošty do PostgreSQL a pak ji dotazujte.

Předtím, než mohou načíst data, budete muset povolit ověřování pomocí hesla z localhost. Na příkazovém řádku:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

V dolní části konfiguračního souboru se několik řádků, které podrobně popisují povolených připojení:

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Změňte řádek "Místního připojení prostřednictvím protokolu IPv4" md5 nahrazujícím ident, takže jsme se můžou přihlásit pomocí uživatelského jména a hesla:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

A potom restartujte službu postgres:

    sudo systemctl restart postgresql

Chcete-li spustit nástroj psql Terminálové interaktivní pro PostgreSQL jako uživatel integrované postgres, spusťte následující příkaz na příkazovém řádku:

    sudo -u postgres psql

Vytvořte nový uživatelský účet, pomocí stejné uživatelské jméno účtu systému Linux jste aktuálně přihlášeni jako a poskytněte heslo:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Potom připojte se k psql jako vaše uživatele:

    psql

A importovat data do nové databáze:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Teď Pojďme prozkoumat data a spustit potřebné dotazy pomocí **Squirrel SQL**, grafický nástroj, který umožňuje pracovat s databázemi prostřednictvím ovladač JDBC.

Abyste mohli začít, spusťte Squirrel SQL z nabídky aplikace. Nastavení ovladače:

* Vyberte **Windows**, pak **zobrazit ovladače**.
* Klikněte pravým tlačítkem na **PostgreSQL** a vyberte **upravit ovladače**.
* Vyberte **Extra třídy cesta**, pak **přidat**.
* Zadejte ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** pro **název souboru** a
* Vyberte **Open** (Otevřít).
* Zvolte seznam ovladače a potom vyberte **org.postgresql.Driver** v **název třídy**a vyberte **OK**.

Nastavit připojení k místnímu serveru:

* Vyberte **Windows**, pak **zobrazit aliasy.**
* Zvolte **+** tlačítko Vytvořit nový alias.
* Pojmenujte ji *nevyžádané pošty databáze*, zvolte **PostgreSQL** v **ovladač** rozevíracího seznamu.
* Nastavení adresy URL *jdbc:postgresql://localhost/spam*.
* Zadejte vaše *uživatelské jméno* a *heslo*.
* Klikněte na **OK**.
* Otevřete **připojení** okna, dvakrát klikněte ***nevyžádané pošty databáze*** alias.
* Vyberte **Connect** (Připojit).

Pokud chcete spustit některé dotazy:

* Vyberte **SQL** kartu.
* Například zadejte jednoduchý dotaz `SELECT * from data;` do textového pole dotaz v horní části karty SQL.
* Stisknutím klávesy **zadejte Ctrl** ho spustit. Ve výchozím nastavení Squirrel SQL vrátí prvních 100 řádků z dotazu.

Existuje mnoho více dotazů, že můžete spustit na tato data prozkoumat. Příklad, jak funguje frekvence slovo *zkontrolujte* se liší mezi nevyžádané pošty se šunkou?

    SELECT avg(word_freq_make), spam from data group by spam;

Nebo co jsou vlastnosti e-mailů, které často obsahují *3d*?

    SELECT * from data order by word_freq_3d desc;

Většina e-mailů, které mají vysokou výskyt *3d* jsou zdánlivě nevyžádané pošty, proto to může být užitečná funkce pro sestavení prediktivního modelu klasifikovat e-maily.

Pokud byste chtěli provést strojového učení s daty uloženými v databázi PostgreSQL, zvažte použití [MADlib](http://madlib.incubator.apache.org/).

## <a name="sql-server-data-warehouse"></a>SQL Server Data Warehouse
Azure SQL Data Warehouse je cloudová, škálovatelná databáze, která dokáže zpracovávat ohromné objemy dat, relačních i nerelačních. Další informace najdete v tématu [co je Azure SQL Data Warehouse?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Pro připojení k datovému skladu a vytvoření tabulky, spusťte následující příkaz z příkazového řádku:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Pak do příkazového řádku sqlcmd:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Kopírování dat pomocí bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> Konce řádků ve staženém souboru jsou Windows – vizuální styl, ale bcp očekává stylu systému UNIX, takže potřebujeme říct bcp, která s příznakem - r.
>
>

A dotazování pomocí sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

Také můžete dotazovat pomocí Squirrel SQL. Postupujte podle podobných kroků pro PostgreSQL, pomocí ovladače JDBC Microsoft MSSQL serveru, který se nachází v ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>Další postup
Přehled o tématech, které vás provedou úkoly, které tvoří procesu pro datové vědy v Azure, najdete v části [vědecké zpracování týmových dat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview).

Popis dalších začátku do konce postupy, které popisují kroky v vědecké zpracování týmových dat pro konkrétní scénáře, naleznete v tématu [názorné postupy vědeckého zpracování týmových dat](../team-data-science-process/walkthroughs.md). Názorné postupy také ukazují, jak zkombinovat cloudové a místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací.
