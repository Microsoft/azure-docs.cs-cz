---
title: "Vědecké zpracování dat s Linux datové vědy virtuálního počítače v Azure | Microsoft Docs"
description: "Jak provést několik běžných úkolů data vědecké účely pomocí virtuálního počítače s Linuxem dat vědecké účely."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: bradsev;paulsh
ms.openlocfilehash: 9b8a9b9bba242fd7c86dc285a77317a5821948df
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-on-azure"></a>Vědecké zpracování dat pomocí datové vědy virtuální počítač s Linuxem v Azure
Tento návod ukazuje, jak provést několik běžných úkolů data vědecké účely pomocí virtuálního počítače s Linuxem dat vědecké účely. Virtuální počítač pro vědecké účely Data Linux (DSVM) je bitová kopie virtuálního počítače, která je k dispozici v Azure, který je předem nainstalovaná s kolekcí nástrojů pro běžně používané k analýze dat a strojové učení. Klíčové softwarové komponenty je uvedeno v [zřízení virtuálního počítače Linux datové vědy](linux-dsvm-intro.md) tématu. Image virtuálního počítače lze snadno začít provádění vědecké zpracování dat v minutách, bez nutnosti instalace a konfigurace každého nástroje jednotlivě. Můžete snadno škálování virtuálních počítačů, v případě potřeby a zastavte ji když není používán. Proto tento prostředek je elastické a nákladově efektivní.

Data úlohy vědecké účely ukázáno v tomto návodu postupujte podle kroků uvedených v [proces vědecké účely dat Team](https://azure.microsoft.com/documentation/learning-paths/data-science-process/). Tento proces zajišťuje systematicky k vědecké zpracování dat, které týmům datových vědců efektivně spolupracovat přes životního cyklu vytváření inteligentní aplikace. Proces vědecké účely dat také poskytuje iterativní framework vědecké zpracování dat, který může následovat jednotlivce.

Budeme analyzovat [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) datovou sadu v tomto návodu. Je to sada e-mailů, které jsou označeny jako nevyžádané pošty nebo šunkou (tj. nejsou nevyžádané pošty), a také obsahuje statistikami o obsah e-mailů. Statistiky zahrnuty jsou popsané v dalším ale jeden oddíl.

## <a name="prerequisites"></a>Požadavky
Než budete moct použít virtuální počítač s Linuxem dat vědecké účely, musíte mít následující:

* **Předplatné**. Pokud není již nemáte, přečtěte si téma [vytvořit účet Azure zdarma Dnes](https://azure.microsoft.com/free/).
* A [ **Linux vědecké zpracování dat virtuálních počítačů**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Informace o zřizování tohoto virtuálního počítače najdete v tématu [zřízení virtuálního počítače Linux datové vědy](linux-dsvm-intro.md).
* [X2Go](http://wiki.x2go.org/doku.php) v počítači nainstalován a otevřít relaci XFCE. Informace o instalaci a konfiguraci **X2Go klienta**, najdete v části [instalace a konfigurace klienta X2Go](linux-dsvm-intro.md#installing-and-configuring-x2go-client).
* Pro hladší posouvání prostředí, přepněte příznak gfx.xrender.enabled v o: konfigurace prohlížeče FireFox virtuálních počítačů. [Najdete zde. ](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Zvažte také přepnutím *mousewheel.enable_pixel_scrolling* na hodnotu False. [Pokyny v tomto poli.](https://support.mozilla.org/en-US/questions/981140)
* **AzureML účet**. Pokud jste ještě nemáte, zaregistrujte si novým na [domovské stránky AzureML](https://studio.azureml.net/). Není k dispozici bezplatná využití vrstvu vám pomůže začít.

## <a name="download-the-spambase-dataset"></a>Stáhněte si spambase datové sady
[Spambase](https://archive.ics.uci.edu/ml/datasets/spambase) datové sady je poměrně malý sada dat, která obsahuje jenom 4601 příklady. Toto je vhodné velikost pro použití při demonstraci toho, že některé klíčové funkce virtuálního počítače vědecké účely Data, jak se udržuje požadavky na prostředky mírné.

> [!NOTE]
> Tento návod byl vytvořen na D2 v2 velikost datové vědy virtuální počítač s Linuxem. Umožňuje zpracovávat postupy v tomto návodu je této velikosti DSVM.
>
>

Pokud potřebujete další prostor úložiště, můžete vytvořit další disky a připojte je k virtuálnímu počítači. Tyto disky používají trvalé úložiště Azure, takže jejich data se zachová, i když server je znovu poskytnuto kvůli změně velikosti nebo je vypnutý. Přidejte disk a jeho připojení k virtuálnímu počítači, postupujte podle pokynů v [přidejte disk do virtuálního počítače s Linuxem](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Tyto kroky použijte rozhraní příkazového řádku Azure (Azure CLI), který je již nainstalován na DSVM. Proto tyto postupy lze provést zcela z virtuální počítač. Chcete-li zvýšit velikost úložiště Další možností je použít [soubory Azure](../../storage/files/storage-how-to-use-files-linux.md).

Chcete-li stáhnout data, otevřete okno terminálu a spusťte tento příkaz:

    wget http://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Stažený soubor nemá řádek záhlaví, můžeme vytvořit jiný soubor, který má hlavičku. Spusťte tento příkaz k vytvoření souboru s příslušnou hlavičky:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Potom řetězení dva soubory společně s příkaz:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Datová sada má několik typů statistik na každou e-mailu:

* Sloupce jako ***word\_frekvence\_WORD*** s procentem slova v e-mailu, které odpovídají *WORD*. Například pokud *word\_frekvence\_zkontrolujte* je 1, 1 % všechna slova v e-mailu měla *zkontrolujte*.
* Sloupce jako ***char\_frekvence\_CHAR*** znamenat procento všech znaků v e-mailu, které byly *CHAR*.
* ***kapitálové\_spustit\_délka\_nejdelší*** je nejdelší délka posloupnost velkých písmen.
* ***kapitálové\_spustit\_délka\_průměrná*** je průměrná délka všech pořadí velkých písmen.
* ***kapitálové\_spustit\_délka\_celkový*** je celková délka všech pořadí velkých písmen.
* ***nevyžádané pošty*** označuje, zda e-mailu byla považována za spam nebo ne (1 = nevyžádané pošty, 0 = není nevyžádané poště).

## <a name="explore-the-dataset-with-microsoft-r-open"></a>Prozkoumejte datovou sadu s Microsoft R otevřete
Umožňuje kontrolovat data a provádět některé základní strojového učení s R. Virtuální počítač vědecké účely dat se dodává s [Microsoft R otevřete](https://mran.revolutionanalytics.com/open/) předinstalován. Knihovny math s více vlákny v této verzi R nabízí lepší výkon než různých verzí jednovláknové. Microsoft R otevřete také poskytuje reprodukovatelnosti pomocí snímku úložiště balíčků CRAN.

Kopií ukázky kódu, který je použit v tomto názorném postupu získáte klonovat **Azure-Machine-Learning--vědecké zpracování dat** úložiště pomocí git, který je předem nainstalovaná ve virtuálním počítači. Z příkazového řádku git spusťte příkaz:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Otevřete okno terminálu a spusťte novou relaci R s R interaktivní konzoly.

> [!NOTE]
> Následující postupy můžete použít také Rstudia. Chcete-li nainstalovat Rstudia, spusťte tento příkaz v terminálu: `./Desktop/DSVM\ tools/installRStudio.sh`
>
>

Pokud chcete importovat data a nastavení prostředí, spusťte:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Pokud chcete zobrazit souhrnné statistické údaje o jednotlivých sloupců:

    summary(data)

Pro jiné zobrazení dat:

    str(data)

To ukazuje typ každou proměnnou a první několik hodnot v datové sadě.

*Nevyžádané pošty* sloupec byl načten jako celé číslo, ale je ve skutečnosti kategorií proměnné (nebo multi-Factor). Nastavte její typ:

    data$spam <- as.factor(data$spam)

Chcete-li provést některé průzkumné analýzy, použijte [ggplot2](http://ggplot2.org/) balíček oblíbených grafovým knihovny pro R, který už je nainstalovaný na virtuálním počítači. Poznámka od souhrnná data zobrazit dřív, máme souhrnné statistiky o frekvenci znak vykřičník. Umožňuje vykreslení těchto frekvencí zde pomocí následujících příkazů:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Vzhledem k tomu, že nulové panelu je zkosení vykreslení, budeme se jich zbavit:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Je větší než 1, která vypadá zajímavé netriviální hustotu. Podívejme se na právě tato data:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Potom ji rozdělte podle šunkou vs nevyžádané pošty:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Tyto příklady měli povolit vám umožní provádět podobné pozemků ostatních sloupců a prozkoumejte data obsažená v nich.

## <a name="train-and-test-an-ml-model"></a>Natrénuje a otestuje ML model
Nyní Pojďme cvičení několik modelů machine learning klasifikovat e-mailů v datové sadě jako obsahující span nebo šunkou. Jsme učení rozhodovacího stromu modelu a modelu náhodných doménové struktury v této části a pak testování jejich přesnost své předpovědi.

> [!NOTE]
> Použitý v následujícím kódu balíček rpart (rekurzivní vytváření oddílů a regresní stromy) je již nainstalován ve virtuálním počítači vědecké účely Data.
>
>

První, můžeme rozdělit datové sady na školení a testovací sady:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

A pak vytvořte rozhodovací strom klasifikovat e-maily.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Tady je výsledek:

![1](./media/linux-dsvm-walkthrough/decision-tree.png)

Pokud chcete zjistit, jak dobře provádí na trénovací sady, použijte následující kód:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Chcete-li zjistit, jak dobře provádí testovací sada:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Zkuste taky umožňuje model náhodných doménové struktury. Náhodné doménovými strukturami cvičení velkého množství rozhodovací stromy a výstup třídu, která je režim klasifikace ze všech jednotlivých rozhodovací stromy. Poskytují účinnější strojového učení a přístup podle jejich odstraňte tendence, že se stromu modelu rozhodnutí overfit školení datové sady.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-ml"></a>Model nasazení do Azure ML
[Azure Machine Learning Studio](https://studio.azureml.net/) (AzureML) je Cloudová služba, která umožňuje snadno vytvářet a nasazovat řešení prediktivní analýzy. Jeden z dobrý funkce AzureML je schopnost publikovat všechny funkce R jako webovou službu. Balíček AzureML R snadné nasazení provést přímo z relace naše R na DSVM.

Chcete-li nasadit kód rozhodovací strom z předchozí části, přihlaste se k Azure Machine Learning Studio. Potřebujete ID vašeho pracovního prostoru a autorizační token k přihlášení. K vyhledání tyto hodnoty a inicializaci AzureML proměnné s nimi:

Vyberte **nastavení** v levé nabídce. Poznámka: vaše **ID pracovního prostoru**. ![2](./media/linux-dsvm-walkthrough/workspace-id.png)

Vyberte **autorizace tokeny** z nabídky režijních nákladů a Poznámka vaše **primární autorizační Token**.![ 3](./media/linux-dsvm-walkthrough/workspace-token.png)

Zatížení **AzureML** balíček a potom nastavit hodnoty proměnných pomocí svého ID token a pracovního prostoru v relaci prostředí R na DSVM:

    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Umožňuje zjednodušit model usnadnění této ukázce k implementaci. Vyberte tří proměnných v rozhodovacím stromu nejbližší do kořenového adresáře a vytvořit novou větev pomocí právě těchto tří proměnných:

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

Potřebujeme předpovědi funkci, která přebírá funkce jako vstup a vrátí předpovězené hodnoty:

    predictSpam <- function(char_freq_dollar, word_freq_remove, word_freq_hp) {
        predictDF <- predict(model.rpart, data.frame("char_freq_dollar" = char_freq_dollar,
        "word_freq_remove" = word_freq_remove, "word_freq_hp" = word_freq_hp))
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }

Publikování funkce predictSpam AzureML pomocí **publishWebService** funkce:

    spamWebService <- publishWebService("predictSpam",
        "spamWebService",
        list("char_freq_dollar"="float", "word_freq_remove"="float","word_freq_hp"="float"),
        list("spam"="int"),
        wsID, wsAuth)

Tato funkce přebírá **predictSpam** fungovat, vytvoří webové služby s názvem **spamWebService** s definované vstupy a výstupy a vrátí informace o nový koncový bod.

Zobrazit podrobnosti o publikované webové služby, včetně jeho koncový bod rozhraní API a přístupové klíče pomocí příkazu:

    spamWebService[[2]]

Vyzkoušejte si to na prvním nastavte 10 řádků testu:

    consumeDataframe(spamWebService$endpoints[[1]]$PrimaryKey, spamWebService$endpoints[[1]]$ApiLocation, smallTestSet[1:10, 1:3])


## <a name="use-other-tools-available"></a>Použít jiné nástroje, které jsou k dispozici
Zbývající části ukazují, jak používat některé z nástroje nainstalované v virtuálního počítače s Linuxem dat vědecké účely. Tady je seznam nástrojů, které jsou popsané:

* XGBoost
* Python
* Jupyterhub
* Rattle
* PostgreSQL & Squirrel SQL
* SQL Server datového skladu

## <a name="xgboost"></a>XGBoost
[XGBoost](https://xgboost.readthedocs.org/en/latest/) je nástroj, který poskytuje rychlé a přesné boosted stromu implementaci.

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
Pro vývoj pomocí Python se nainstalovaly v DSVM distribuce Anaconda Python 2.7 a 3.5.

> [!NOTE]
> Zahrnuje distribuční Anaconda [Condas](http://conda.pydata.org/docs/index.html), který slouží k vytvoření vlastního prostředí pro jazyk Python, které mají různé verze nebo balíčky nainstalované v nich.
>
>

Umožňuje číst v některých spambase datové sady a klasifikovat e-maily s support vector počítačů v scikit-Další informace:

    import pandas
    from sklearn import svm    
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Chcete-li předpovědi:

    clf.predict(X.ix[0:20, :])

Pokud chcete zobrazit, jak publikovat koncového bodu AzureML, provedeme jednodušší model tří proměnných jako jsme to udělali při jsme dříve publikované R modelu.

    X = data.ix[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Publikování modelu AzureML:

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
Anaconda distribuce v DSVM se dodává s poznámkového bloku Jupyter, prostředí napříč platformami sdílet kód Python, R nebo Dita a analýzy. Poznámkového bloku Jupyter přistupuje prostřednictvím JupyterHub. Přihlášení pomocí místních Linux uživatelské jméno a heslo v ***https://\<název DNS virtuálního počítače nebo IP adresu\>: 8000 /***. Všechny konfigurační soubory pro JupyterHub se nacházejí v adresáři **/etc/jupyterhub**.

Několik ukázkových poznámkových bloků jsou již nainstalovány ve virtuálním počítači:

* Najdete v článku [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) pro ukázkové Python Poznámkový blok.
* V tématu [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) pro ukázku **R** poznámkového bloku.
* Najdete v článku [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) pro jiné ukázku **Python** poznámkového bloku.

> [!NOTE]
> Dále je dostupný z příkazového řádku pro virtuální počítač Linux datové vědy Dita jazyk.
>
>

## <a name="rattle"></a>Rattle
[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) grafický nástroj R pro dolování dat (R Analytical nástroj pro další snadno) je. Obsahuje intuitivní rozhraní, které umožňuje snadno načíst, prozkoumat a transformovat data a vytvářet a vyhodnocení modelů.  Článek [Rattle: A Data Mining grafického uživatelského rozhraní pro R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) poskytuje návod, který ukazuje její funkce.

Instalace a spuštění Rattle pomocí následujících příkazů:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> Na DSVM není nutné instalovat. Ale Rattle můžete být vyzváni k instalaci dalších balíčků, jakmile ho načte.
>
>

Rattle používá rozhraní založené na kartě. Většina karty odpovídají kroky v [proces vědecké účely dat](https://azure.microsoft.com/documentation/learning-paths/data-science-process/), jako jsou načítání dat nebo zkoumat ho. Proces vědecké účely dat toky zleva doprava mezi kartami. Ale poslední karta obsahuje protokolu R příkazy spustit Rattle.

Načíst a nakonfigurovat datové sady:

* Chcete-li načíst soubor, vyberte **Data** kartě, pak
* Zvolte modulu pro výběr vedle **Filename** a zvolte **spambaseHeaders.data**.
* Načíst soubor. Vyberte **Execute** horní řádek tlačítek. Měli byste vidět souhrn jednotlivých sloupců, včetně jeho identifikovaných datového typu, ať už se jedná o vstup, cíl nebo jiný typ proměnné a počet jedinečných hodnot.
* Rattle má identifikovány správně **nevyžádané pošty** sloupec jako cíl. Vyberte sloupec nevyžádané pošty, pak nastavte **cílový datový typ** k **Categoric**.

K prozkoumání dat:

* Vyberte **prozkoumat** kartě.
* Klikněte na tlačítko **souhrnné**, pak **Execute**, určité informace o typy proměnných a některé souhrnné statistiky.
* Chcete-li zobrazit jiné typy Statistika každou proměnnou, vyberte jiné možnosti jako **popisujících** nebo **Základy**.

**Prozkoumat** kartě můžete také vygenerovat mnoho pronikavého pozemků. K vykreslení histogram dat:

* Vyberte **distribuce**.
* Zkontrolujte **Histogram** pro **word_freq_remove** a **word_freq_you**.
* Vyberte **provést**. Měli byste vidět obou hustotu pozemků v okně jednoho grafu, pokud je zřejmé, že je slovo "vy" zobrazí mnohem častěji v e-mailů než "Odebrat".

Korelace pozemků jsou také zajímavé. Chcete vytvořit:

* Zvolte **korelace** jako **typu**, pak
* Vyberte **provést**.
* Rattle vás varuje, že doporučí maximálně 40 proměnné. Vyberte **Ano** zobrazíte vykreslení.

Existují některé zajímavé korelací, které se spustit: "technologie" je důrazně korelační "HP" a "labs", například. Ho je také důrazně vztažen k "650", protože je 650 kód oblasti dárců datovou sadu.

V okně Explore jsou k dispozici číselné hodnoty korelací mezi slovy. Je zajímavé Poznámka, například, že je "technologie" negativní korelační s "vaše" a "peníze".

Rattle můžete převést datovou sadu, která zpracovává některé běžné problémy. Například umožňuje nastavit velikost funkce, dává chybějící hodnoty, zpracování extrémních a odebrání proměnné nebo připomínky s chybějící data. Rattle můžete také určit pravidla přidružení mezi připomínky nebo proměnné. Tyto karty jsou nad rámec této úvodní prohlídka.

Rattle můžete také provést analýzu clusteru. Umožňuje vyloučit některé funkce usnadnění výstup. Na **Data** , zvolte **Ignorovat** vedle každého proměnné s výjimkou těchto deset položek:

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* spam

Poté přejděte zpět na **clusteru** , zvolte **KMeans**a nastavte *počtu clusterů, které* na 4. Potom **provést**. Výsledky jsou zobrazeny v okně výstupu. Jeden cluster má vysoká frekvence "Jirka" a "hp" a je pravděpodobně legitimní firemní e-mail.

K sestavení modelu strojového učení stromu jednoduché rozhodnutí:

* Vyberte **modelu** kartě
* Zvolte **stromu** jako **typu**.
* Vyberte **Execute** a zobrazení stromu v textové podobě, v okně výstupu.
* Vyberte **kreslení** tlačítko Zobrazit grafické verze. To vypadá podobný tomu stromu jsme získali dříve pomocí *rpart*.

Jeden z dobrý funkce Rattle je schopnost spustit několik metod machine learning a rychle vyhodnocovat jejich výsledky. Tady je postup:

* Zvolte **všechny** pro **typu**.
* Vyberte **provést**.
* Po jejím dokončení můžete kliknout na všechny jedním **typ**, například **SVM**a zobrazit výsledky.
* Také můžete porovnat výkon modely na ověřování, nastavit pomocí **Evaluate** kartě. Například **chyba matice** výběr ukazuje matice nejasnostem, celkový chyb a chyba zprůměrovanou třídy pro každý model na sadu ověření.
* Můžete také vykreslení křivek ROC, provádět analýzy velkých a malých písmen a dělat jiné typy modelu hodnocení.

Jakmile budete hotovi, vytváření modelů, vyberte **protokolu** zobrazíte kód R spustit Rattle během relace. Můžete vybrat **exportovat** tlačítko ji uložit.

> [!NOTE]
> V aktuální verzi Rattle je chyba. Chcete-li změnit skriptu nebo ji použít k vaší kroky opakujte později, musíte vložit znak # před * exportovat tento protokol... * v textu protokolu.
>
>

## <a name="postgresql--squirrel-sql"></a>PostgreSQL & Squirrel SQL
DSVM se dodává s PostgreSQL nainstalována. PostgreSQL je relační databáze sofistikované, open source. V této části ukazuje, jak načíst naší datové sadě nevyžádané pošty do PostgreSQL a pak zadat dotaz.

Před načtením dat, budete muset povolit ověřování hesla z localhost. Na příkazovém řádku:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

V dolní části do konfiguračního souboru jsou několik řádků, které podrobností povolených připojení:

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Změňte řádek "IPv4 místní připojení" pro použití md5 místo ident, takže jsme umožní přihlásit se pomocí uživatelského jména a hesla:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

A restartujte službu postgres:

    sudo systemctl restart postgresql

Spusťte psql, interaktivní terminálu PostgreSQL jako uživatel předdefinované postgres, spusťte následující příkaz z řádku:

    sudo -u postgres psql

Vytvořte nový uživatelský účet, pomocí stejného uživatelského jména jako Linux účet aktuálně přihlášeni jako a poskytněte heslo:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Pak se přihlaste k psql jako vaše uživatele:

    psql

A importovat data do nové databáze:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Nyní Pojďme data prozkoumat a spustit některé dotazy pomocí **Squirrel SQL**, grafický nástroj, který umožňuje pracovat s databází prostřednictvím ovladač JDBC.

Abyste mohli začít, spusťte Squirrel SQL v nabídce aplikace. Nastavení ovladače:

* Vyberte **Windows**, pak **zobrazit ovladače**.
* Klikněte pravým tlačítkem na **PostgreSQL** a vyberte **upravit ovladač**.
* Vyberte **velmi třídy cesta**, pak **přidat**.
* Zadejte ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** pro **název souboru** a
* Vyberte **otevřete**.
* Vyberte ovladače seznamu a pak vyberte **org.postgresql.Driver** v **název třídy**a vyberte **OK**.

Nastavení připojení k místnímu serveru:

* Vyberte **Windows**, pak **zobrazit aliasy.**
* Vyberte  **+**  tlačítko Vytvořit nový alias.
* Pojmenujte ji *nevyžádané pošty databáze*, zvolte **PostgreSQL** v **ovladač** rozevíracího seznamu.
* Nastavení adresy URL *jdbc:postgresql://localhost/spam*.
* Zadejte vaše *uživatelské jméno* a *heslo*.
* Klikněte na **OK**.
* Otevřete **připojení** okno, dvakrát klikněte ***nevyžádané pošty databáze*** alias.
* Vyberte **Connect** (Připojit).

Spuštění některých dotazů:

* Vyberte **SQL** kartě.
* Například zadejte jednoduchý dotaz `SELECT * from data;` do textového pole dotazu v horní části na kartě SQL.
* Stiskněte klávesu **zadejte Ctrl** ji spustit. Ve výchozím nastavení Squirrel SQL vrátí prvních 100 řádků z dotazu.

Nejsou k dispozici mnoho další dotazy, že můžete spustit a prozkoumejte tato data. Například jak funguje frekvence aplikace word *zkontrolujte* liší nevyžádané pošty se šunkou?

    SELECT avg(word_freq_make), spam from data group by spam;

Nebo jaké jsou vlastnosti e-mailů, které často obsahují *3d*?

    SELECT * from data order by word_freq_3d desc;

Většina e-mailů, které mají vysokou výskytem *3d* jsou zjevně nevyžádané pošty, takže může být užitečná pro vytvoření prediktivního modelu klasifikovat e-maily.

Pokud jste chtěli provést machine learning s daty uloženými v databázi PostgreSQL, zvažte použití [MADlib](http://madlib.incubator.apache.org/).

## <a name="sql-server-data-warehouse"></a>SQL Server datového skladu
Azure SQL Data Warehouse je cloudová, škálovatelná databáze, která dokáže zpracovávat ohromné objemy dat, relačních i nerelačních. Další informace najdete v tématu [co je Azure SQL Data Warehouse?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

K připojení k datovému skladu a vytvořit tabulku, spusťte z příkazového řádku následující příkaz:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Pak do příkazového řádku sqlcmd:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Kopírování dat pomocí bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> Konce řádků ve staženém souboru jsou styl systému Windows, ale bcp očekává stylu systému UNIX, takže potřebujeme říct bcp, s příznakem - r.
>
>

A dotazování pomocí sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

Také můžete dotazovat pomocí Squirrel SQL. Podobný postup PostgreSQL, ovladač JDBC Microsoft MSSQL serveru, pomocí kterého lze nalézt v ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>Další postup
Přehled témata, která vás provede procesem úlohy, které tvoří proces vědecké zpracování dat v Azure najdete v tématu [proces vědecké účely dat Team](http://aka.ms/datascienceprocess).

Popis dalších návody začátku do konce, které ukazují kroků v procesu vědecké účely Team dat u konkrétních scénářů najdete v tématu [proces vědecké účely dat Team návody](../team-data-science-process/walkthroughs.md). Názorné postupy také ukazují, jak kombinovat cloudové a místní nástroje a služby do pracovního postupu nebo kanálu vytvoření inteligentního aplikace.
