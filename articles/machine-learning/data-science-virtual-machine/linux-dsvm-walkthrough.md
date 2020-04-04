---
title: Prozkoumejte Linux
titleSuffix: Azure Data Science Virtual Machine
description: Zjistěte, jak dokončit několik běžných úloh datové vědy pomocí virtuálního počítače Linux Data Science.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 7292064a1df8aa9bfffcd9a19a03f7b332c0615e
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632736"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-in-azure"></a>Datová věda s virtuálním počítačem pro datové vědy o Linuxu v Azure

Tento návod ukazuje, jak dokončit několik běžných úloh datové vědy pomocí virtuálního počítače Linux Data Science (DSVM). Linux DSVM je image virtuálního počítače dostupná v Azure, která je předinstalovaná s kolekcí nástrojů běžně používaných pro analýzu dat a strojové učení. Klíčové softwarové komponenty jsou rozepsány v [části Provision the Linux Data Science Virtual Machine](linux-dsvm-intro.md). Bitová kopie DSVM usnadňuje začít dělat datové vědy během několika minut, aniž byste museli instalovat a konfigurovat každý z nástrojů jednotlivě. Můžete snadno vertikálně navýšit kapacitu DSVM, pokud potřebujete, a můžete jej zastavit, když se nepoužívá. Prostředek DSVM je elastický a nákladově efektivní.

Úkoly datové vědy demonstrované v tomto návodu postupujte podle kroků popsaných v části [Co je proces vědecké správy týmových dat?](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) Team Data Science Process je systematický přístup k datové vědě, který pomáhá týmům datových vědců efektivně spolupracovat v průběhu celého životního cyklu vytváření inteligentních aplikací. Proces datové vědy také poskytuje iterativní rámec pro datové vědy, které mohou být následovány jednotlivcem.

V tomto návodu analyzujeme datovou sadu [spamové základny.](https://archive.ics.uci.edu/ml/datasets/spambase) Spambase je sada e-mailů, které jsou označeny buď spam nebo šunka (ne spam). Spambase také obsahuje některé statistiky o obsahu e-mailů. O statistikách hovoříme později v návodu.

## <a name="prerequisites"></a>Požadavky

Než budete moci používat Linux DSVM, musíte mít následující předpoklady:

* **Předplatné Azure**. Pokud chcete získat předplatné Azure, [přečtěte si,](https://azure.microsoft.com/free/)že si ještě dnes vytvořte bezplatný účet Azure .
* [**Linux Data Science Virtuální stroj**](https://azure.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu). Informace o zřizování virtuálního počítače najdete v [tématu Zřízení virtuálního počítače pro datové vědy o Linuxu](linux-dsvm-intro.md).
* [**X2Go**](https://wiki.x2go.org/doku.php) nainstalován o vašem počítači s otevřenou relací XFCE. Další informace naleznete v [tématu Instalace a konfigurace klienta X2Go](linux-dsvm-intro.md#x2go).
* Pro plynulejší posouvání ve webovém prohlížeči Firefox od DSVM přepněte vlajku `gfx.xrender.enabled` v aplikaci `about:config`. [Další informace](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Zvažte `mousewheel.enable_pixel_scrolling` také `False`nastavení na . [Další informace](https://support.mozilla.org/questions/981140).
* **Účet Azure Machine Learning**. Pokud ještě nemáte, zaregistrujte si nový účet na [domovské stránce Azure Machine Learning](https://azure.microsoft.com/free/services/machine-learning//).

## <a name="download-the-spambase-dataset"></a>Stažení datové sady spamové základny

Datová sada [spamové základny](https://archive.ics.uci.edu/ml/datasets/spambase) je relativně malá sada dat, která obsahuje příklady 4 601. Datová sada je vhodná velikost pro demonstrování některé klíčové funkce DSVM, protože udržuje požadavky na prostředky skromné.

> [!NOTE]
> Tento návod byl vytvořen pomocí D2 v2 velikosti Linuxu DSVM (CentOS Edition). DSVM této velikosti můžete použít k dokončení postupů, které jsou demonstrovány v tomto návodu.

Pokud potřebujete více úložného prostoru, můžete vytvořit další disky a připojit je k vašemu dsvm. Disky používají trvalé úložiště Azure, takže jejich data se zachovají i v případě, že je server znovu zřízen z důvodu změna velikosti nebo je vypnut. Chcete-li přidat disk a připojit jej k modulu DSVM, proveďte kroky v části [Přidání disku do virtuálního počítače s Linuxem](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Postup přidání disku použít Azure CLI, který je již nainstalován na DSVM. Kroky můžete provést zcela ze samotného DSVM. Další možností, jak zvýšit úložiště, je použití [souborů Azure](../../storage/files/storage-how-to-use-files-linux.md).

Chcete-li stáhnout data, otevřete okno terminálu a spusťte tento příkaz:

```bash
wget --no-check-certificate https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data
```

Stažený soubor nemá řádek záhlaví. Pojďme vytvořit další soubor, který má záhlaví. Spuštěním tohoto příkazu vytvořte soubor s příslušnými záhlavími:

```bash
echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers
```

Potom zřetězte dva soubory dohromady:

```bash
cat spambase.data >> headers
mv headers spambaseHeaders.data
```

Datová sada má několik typů statistik pro každý e-mail:

* Sloupce jako **\_slovo\_freq WORD** označují procento slov v e-mailu, které odpovídají *WORD*. Například pokud **\_slovo\_freq make** je **1**, pak 1% všech slov v e-mailu byly *dělat*.
* Sloupce jako **\_char\_freq CHAR** označují procento všech znaků v e-mailu, které jsou *CHAR*.
* **délka\_\_hlavního\_běhu nejdelší** je nejdelší délka posloupnosti velkých písmen.
* **průměr\_\_délky\_hlavního běhu** je průměrná délka všech sekvencí velkých písmen.
* **celková\_\_\_** délka hlavního běhu je celková délka všech sekvencí velkých písmen.
* **spam** označuje, zda byl e-mail považován za spam nebo ne (1 = spam, 0 = ne spam).

## <a name="explore-the-dataset-by-using-r-open"></a>Prozkoumejte datovou sadu pomocí R Open

Pojďme prozkoumat data a provést některé základní strojové učení pomocí R. DSVM je dodáván s [předinstalovaným microsoft r open.](https://mran.revolutionanalytics.com/open/) Vícevláknové matematické knihovny v předinstalované verzi R nabízejí lepší výkon než verze s jedním podprocesem. R Open také poskytuje reprodukovatelnost prostřednictvím snímku úložiště balíčků CRAN.

Chcete-li získat kopie ukázky kódu, které se používají v tomto návodu, použijte git klonovat úložiště Azure-Machine-Learning-Data-Science. Git je předinstalovaný na DSVM. Na příkazovém řádku git spusťte:

```bash
git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git
```

Otevřete okno terminálu a spusťte novou relaci R v interaktivní konzole R. Můžete také použít RStudio, který je předinstalován na DSVM.

Import dat a nastavení prostředí:

```R
data <- read.csv("spambaseHeaders.data")
set.seed(123)
```

Zobrazení souhrnných statistik jednotlivých sloupců:

```R
summary(data)
```

Pro jiné zobrazení dat:

```R
str(data)
```

Toto zobrazení zobrazuje typ každé proměnné a několik prvních hodnot v datové sadě.

Sloupec **se spamem** byl přečten jako celé číslo, ale ve skutečnosti je to kategorická proměnná (nebo faktor). Nastavení jeho typu:

```R
data$spam <- as.factor(data$spam)
```

Chcete-li provést nějakou průzkumnou analýzu, použijte balíček [ggplot2,](https://ggplot2.tidyverse.org/) oblíbenou knihovnu grafů pro R, která je předinstalována na DSVM. Na základě dříve zobrazených souhrnných údajů máme souhrnné statistiky o četnosti znaku vykřičníku. Vykreslete zde tyto frekvence spuštěním následujících příkazů:

```R
library(ggplot2)
ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Protože nulová čára zkosení pozemku, pojďme ji odstranit:

```R
email_with_exclamation = data[data$char_freq_exclamation > 0, ]
ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Tam je netriviální hustota nad 1, která vypadá zajímavě. Podívejme se pouze na tato data:

```R
ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Pak, rozdělit ji spam versus šunka:

```R
ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
geom_density(lty=3) +
geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
xlab("spam") +
ggtitle("Distribution of spam \nby frequency of !") +
labs(fill="spam", y="Density")
```

Tyto příklady by vám měly pomoci vytvořit podobné obrázky a prozkoumat data v ostatních sloupcích.

## <a name="train-and-test-a-machine-learning-model"></a>Trénování a testování modelu strojového učení

Pojďme trénovat několik modelů strojového učení klasifikovat e-maily v datové sadě jako obsahující buď spam nebo šunku. V této části trénujeme model rozhodovacího stromu a náhodný model doménové struktury. Poté otestujeme přesnost předpovědí.

> [!NOTE]
> Balíček *rpart* (Rekurzivní dělení a regresní stromy) použitý v následujícím kódu je již nainstalován na DSVM.

Nejprve rozdělíme datovou sadu na trénovací sady a testovací sady:

```R
rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)
```

Potom vytvořte rozhodovací strom pro klasifikaci e-mailů:

```R
require(rpart)
model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
plot(model.rpart)
text(model.rpart)
```

Zde je výsledek:

![Diagram rozhodovacího stromu, který je vytvořen](./media/linux-dsvm-walkthrough/decision-tree.png)

Chcete-li zjistit, jak dobře se provádí na trénovací sadě, použijte následující kód:

```R
trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

Chcete-li zjistit, jak dobře se provádí na testovací sadě:

```R
testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

Zkusme také náhodný model lesa. Náhodné lesy trénovat velké množství rozhodnutí stromů a výstup třídy, která je režim klasifikace ze všech jednotlivých rozhodnutí stromů. Poskytují výkonnější přístup strojového učení, protože opravují tendenci modelu rozhodovacího stromu k nadměrnému přizpůsobení datové sady školení.

```R
require(randomForest)
trainVars <- setdiff(colnames(data), 'spam')
model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Výuka a návody k hloubkovému učení

Kromě ukázky založené na rozhraní je k dispozici také sada komplexních návodů. Tyto návody vám pomohou nastartovat vývoj aplikací pro hluboké učení v doménách, jako je porozumění obrázkům a textu a jazykům.

- [Spuštění neuronových sítí v různých architekturách](https://github.com/ilkarman/DeepLearningFrameworks): Komplexní návod, který ukazuje, jak migrovat kód z jednoho rozhraní do druhého. Také ukazuje, jak porovnat výkon modelu a běhu napříč rámci. 

- [Návod pro vytvoření komplexního řešení pro detekci produktů v obrazech](https://github.com/Azure/cortana-intelligence-product-detection-from-images): Detekce obrazu je technika, která dokáže najít a klasifikovat objekty v obrazech. Tato technologie má potenciál přinést obrovské odměny v mnoha reálných obchodních oblastech. Maloobchodníci mohou například tuto techniku použít k určení produktu, který zákazník vyzvedl z regálu. Tyto informace zase pomáhají ukládají spravovat zásoby produktů. 

- [Hluboké učení pro zvuk](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): Tento výukový program ukazuje, jak trénovat model hlubokého učení pro detekci zvukových událostí v [datové sadě městských zvuků](https://urbansounddataset.weebly.com/). Kurz poskytuje přehled o tom, jak pracovat se zvukovými daty.

- [Klasifikace textových dokumentů](https://github.com/anargyri/lstm_han): Tento návod ukazuje, jak vytvořit a trénovat dvě různé architektury neuronové sítě: Hierarchická síť pozornosti a dlouhodobá krátkodobá paměť (LSTM). Tyto neuronové sítě používají rozhraní KERAS API pro hluboké učení ke klasifikaci textových dokumentů. Keras je front-end ke třem nejoblíbenějším rámcům hlubokého učení: Microsoft Cognitive Toolkit, TensorFlow a Theano.

## <a name="other-tools"></a>Další nástroje

Zbývající části ukazují, jak používat některé nástroje, které jsou nainstalovány na Linux U DSVM. Diskutujeme o těchto nástrojích:

* XGBoost
* Python
* JupyterHub
* Rattle
* PostgreSQL a SquirreL SQL
* Datový sklad serveru SQL Server

### <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/) poskytuje rychlou a přesnou implementaci posíleného stromu.

```R
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
```

XGBoost také může volat z Pythonu nebo příkazového řádku.

### <a name="python"></a>Python

Pro vývoj Pythonu jsou distribuce Anaconda Python 3.5 a 2.7 nainstalovány na DSVM.

> [!NOTE]
> Distribuce Anaconda zahrnuje [Conda](https://conda.pydata.org/docs/index.html). Conda můžete použít k vytvoření vlastních prostředí Pythonu, které mají různé verze nebo balíčky nainstalované v nich.

Přečtěme si v některých datových sadách spamové základny a klasifikujme e-maily s podpůrnými vektorovými stroji v Scikit-learn:

```Python
import pandas
from sklearn import svm
data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
X = data.ix[:, 0:57]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

Chcete-li předpovědi:

```Python
clf.predict(X.ix[0:20, :])
```

Chcete-li ukázat, jak publikovat koncový bod Azure Machine Learning, vytvořte základní model. Použijeme tři proměnné, které jsme použili, když jsme dříve publikovali model R:

```Python
X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

Publikování modelu do Azure Machine Learning:

```Python
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
```

> [!NOTE]
> Tato možnost je dostupná pouze pro Python 2.7. V Pythonu 3.5 ještě není podporován. Chcete-li spustit, použijte **/anaconda/bin/python2.7**.

### <a name="jupyterhub"></a>JupyterHub

Distribuce Anaconda v DSVM je dodávána s jupyterovým poznámkovým blokem, prostředím pro různé platformy pro sdílení kódu a analýzy Pythonu, R nebo Julia. Jupyter Notebook je přístupný přes JupyterHub. Přihlašujete se pomocí místního\<uživatelského jména a hesla\>pro Linux na adrese https:// DSVM DNS nebo IP adrese :8000/. Všechny konfigurační soubory pro JupyterHub se nacházejí v /etc/jupyterhub.

> [!NOTE]
> Chcete-li použít Správce balíčků `pip` Pythonu (pomocí příkazu) z jupyterového poznámkového bloku v aktuálním jádře, použijte tento příkaz v buňce kódu:
>
>   ```Python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> Chcete-li použít instalační program Conda (pomocí příkazu) `conda` z Jupyter Notebook v aktuálním jádře, použijte tento příkaz v kódové buňce:
>
>   ```Python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

Na dsvm je již nainstalováno několik ukázkových poznámkových bloků:

* Ukázkové poznámkové bloky Pythonu:
  * [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
  * [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)
* Ukázkový poznámkový blok R:
  * [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> Jazyk Julia je také k dispozici z příkazového řádku na Linux DSVM.

### <a name="rattle"></a>Rattle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (*R* *A*nalytical *T*ool *T*o *L*earn *E*asily) je grafický R nástroj pro dolování dat. Rattle má intuitivní rozhraní, které usnadňuje načítání, zkoumání a transformaci dat a vytváření a vyhodnocování modelů. [Chrastítko: Dolování dat GUI pro R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) poskytuje návod, který ukazuje rattle funkce.

Nainstalujte a spusťte rattle spuštěním těchto příkazů:

```R
if(!require("rattle")) install.packages("rattle")
require(rattle)
rattle()
```

> [!NOTE]
> Není nutné instalovat Rattle na DSVM. Při otevření rattleva však můžete být vyzváni k instalaci dalších balíčků.

Chrastítko používá rozhraní založené na tabulcích. Většina karet odpovídá krokům v [procesu vědecké ho zpracování týmových dat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), jako je načítání dat nebo zkoumání dat. Proces datové vědy protéká zleva doprava přes karty. Poslední karta obsahuje protokol příkazů R, které byly spuštěny rattle.

Načtení a konfigurace datové sady:

1. Chcete-li soubor načíst, vyberte kartu **Data.**
1. Zvolte volič vedle **názvu souboru**a pak vyberte **spambaseHeaders.data**.
1. Chcete-li načíst soubor. vyberte **Spustit**. Měli byste vidět souhrn každého sloupce, včetně jeho identifikovaného datového typu; zda se jedná o vstup, cíl nebo jiný typ proměnné; a počet jedinečných hodnot.
1. Rattle správně identifikoval sloupec **spamu** jako cíl. Vyberte sloupec **spamu** a nastavte **cílový datový typ** na **categoric**.

Chcete-li data prozkoumat:

1. Vyberte kartu **Prozkoumat.**
1. Chcete-li zobrazit informace o typech proměnných a některé souhrnné statistiky, vyberte **možnost Provést souhrn** > **.**
1. Chcete-li zobrazit další typy statistik o jednotlivých proměnných, vyberte další možnosti, například **Popsat** nebo **Základy**.

Kartu **Prozkoumat** můžete také použít ke generování zasvěcených parcel. Chcete-li vykreslit histogram dat:

1. Vyberte **možnost Distribuce**.
1. Pro **word_freq_remove** a **word_freq_you**vyberte **možnost Histogram**.
1. Vyberte **Provést**. Měli byste vidět oba obrázky hustoty v jednom okně grafu, kde je jasné, že _slovo, které_ se v e-mailech zobrazuje mnohem častěji, než _odstranit_.

**Korelace** pozemky jsou také zajímavé. Vytvoření parcely:

1. V **pole Typ**vyberte možnost **Korelace**.
1. Vyberte **Provést**.
1. Rattle vás varuje, že doporučuje maximálně 40 proměnných. Chcete-li zobrazit vykreslení, vyberte **možnost Ano.**

Tam jsou některé zajímavé korelace, které přicházejí: _technologie_ je silně koreluje s _HP_ a _laboratoře_, například. Je to také silně koreluje s _650,_ protože směrové číslo oblasti dárců datové sady je 650.

Číselné hodnoty pro korelace mezi slovy jsou k dispozici v okně **Prozkoumat.** Je zajímavé, například, že _technologie_ je negativně koreluje s _vaší_ a _peníze_.

Chrastítko může transformovat datovou sadu pro zpracování některých běžných problémů. Může například změnit měřítko prvků, impute chybějící hodnoty, zpracování odlehlé hodnoty a odebrat proměnné nebo pozorování, které mají chybějící data. Chrastítko může také identifikovat pravidla přidružení mezi pozorováním a proměnnými. Tyto karty nejsou zahrnuty v tomto úvodním návodu.

Chrastítko také může spustit analýzu clusteru. Vyloučíme některé funkce, aby byl výstup čitelnější. Na kartě **Data** vyberte **Ignorovat** vedle každé z proměnných kromě těchto 10 položek:

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

Vraťte se na kartu **Cluster.** Vyberte **KMeans**a nastavte **počet clusterů** na **4**. Vyberte **Provést**. Výsledky jsou zobrazeny ve výstupním okně. Jeden cluster má vysokou frekvenci _george_ a _hp_, a je pravděpodobně legitimní obchodní e-mail.

Vytvoření základního modelu strojového učení rozhodovacího stromu:

1. Vyberte kartu **Model,**
1. V části **Typ**vyberte **strom**.
1. Vyberte **Spustit,** chcete-li strom zobrazit v textové podobě ve výstupním okně.
1. Chcete-li zobrazit grafickou verzi, vyberte tlačítko **Kreslit.** Rozhodovací strom vypadá podobně jako strom, který jsme získali dříve pomocí rpart.

Užitečnou vlastností Rattle je jeho schopnost spustit několik metod strojového učení a rychle je vyhodnotit. Zde jsou kroky:

1. V **pole Typ**vyberte možnost **Vše**.
1. Vyberte **Provést**.
1. Po spuštění rattle, můžete vybrat libovolnou hodnotu **Typu,** jako **je SVM**a zobrazit výsledky.
1. Můžete také porovnat výkon modelů na ověřovací sadě pomocí karty **Vyhodnotit.** Například výběr **Matice chyb** zobrazuje matici záměny, celkovou chybu a průměrnou chybu třídy pro každý model v sadě ověření. Můžete také vykreslit křivky ROC, spustit analýzu citlivosti a provést další typy vyhodnocení modelu.

Po dokončení vytváření modelů vyberte kartu **Protokol** a zobrazte kód R, který byl spuštěn rattle během relace. Můžete vybrat tlačítko **Exportovat** a uložit ho.

> [!NOTE]
> Aktuální verze Rattle obsahuje chybu. Chcete-li skript upravit nebo jej použít k opakování **#** kroků později, musíte vložit znak před *export tohoto protokolu ...* do textu protokolu.

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL a SquirreL SQL

DSVM je dodáván s nainstalovaným PostgreSQL. PostgreSQL je sofistikovaná, open-source relační databáze. Tato část ukazuje, jak načíst datovou sadu spamové základny do PostgreSQL a poté ji zadat dotaz.

Před načtením dat je nutné povolit ověřování hesla z localhost. Na příkazovém řádku spusťte:

```Bash
sudo gedit /var/lib/pgsql/data/pg_hba.conf
```

V dolní části konfiguračního souboru je několik řádků, které podrobně popisují povolená připojení:

```
# "local" is only for Unix domain socket connections:
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
# IPv6 local connections:
host    all             all             ::1/128                 ident
```

Změňte řádek **místních připojení IPv4** tak, aby **používalm md5** místo **ident**, abychom se mohli přihlásit pomocí uživatelského jména a hesla:

```
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
```

Potom restartujte službu PostgreSQL:

```Bash
sudo systemctl restart postgresql
```

Chcete-li spustit *psql* (interaktivní terminál pro PostgreSQL) jako vestavěný uživatel postgres, spusťte tento příkaz:

```Bash
sudo -u postgres psql
```

Vytvořte nový uživatelský účet pomocí uživatelského jména účtu Linux, který jste použili k přihlášení. Vytvořte heslo:

```Bash
CREATE USER <username> WITH CREATEDB;
CREATE DATABASE <username>;
ALTER USER <username> password '<password>';
\quit
```

Přihlaste se do psql:

```Bash
psql
```

Import dat do nové databáze:

```SQL
CREATE DATABASE spam;
\c spam
CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
\copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
\quit
```

Nyní pojďme prozkoumat data a spustit některé dotazy pomocí SQuirreL SQL, grafický nástroj, který můžete použít k interakci s databázemi prostřednictvím ovladače JDBC.

Chcete-li začít, v nabídce **Aplikace** otevřete SQuirreL SQL. Nastavení ovladače:

1. Vyberte**možnost Ovladače zobrazení systému** **Windows** > .
1. Klepněte pravým tlačítkem myši na **položku PostgreSQL** a vyberte příkaz **Změnit ovladač**.
1. Vyberte **Přidat cestu** > extra třídy **.**
1. Pro **název souboru**zadejte **/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar**.
1. Vyberte **Open** (Otevřít).
1. Vyberte **možnost Ovladače seznamu**. V **popřípadě Název třídy**vyberte **org.postgresql.Driver**a pak vyberte **OK**.

Nastavení připojení k místnímu serveru:

1. Vyberte**Aliasy zobrazení systému** **Windows.** > 
1. Vyberte **+** tlačítko pro vytvoření nového aliasu. Nový název aliasu zadejte **do databáze nevyžádané pošty**. 
1. V **aplikaci Driver**vyberte **možnost PostgreSQL**.
1. Nastavte adresu URL na **jdbc:postgresql://localhost/spam**.
1. Zadejte uživatelské jméno a heslo.
1. Vyberte **OK**.
1. Chcete-li otevřít okno **Připojení,** poklepejte na alias **databáze nevyžádané pošty.**
1. Vyberte **Connect** (Připojit).

Spuštění některých dotazů:

1. Vyberte kartu **SQL.**
1. Do pole dotazu v horní části karty **SQL** zadejte základní dotaz, například `SELECT * from data;`.
1. Stisknutím kláves Ctrl+Enter spusťte dotaz. Ve výchozím nastavení squirrel SQL vrátí prvních 100 řádků z dotazu.

Existuje mnoho dalších dotazů, které můžete spustit k prozkoumání těchto dat. Například, jak se frekvence *slova, aby* se liší mezi spamem a šunkou?

```SQL
SELECT avg(word_freq_make), spam from data group by spam;
```

Nebo jaké jsou vlastnosti e-mailu, které často obsahují *3d*?

```SQL
SELECT * from data order by word_freq_3d desc;
```

Většina e-mailů, které mají vysoký výskyt *3D* zřejmě jsou spam. Tyto informace mohou být užitečné pro vytváření prediktivní model pro klasifikaci e-mailů.

Pokud chcete dělat strojové učení pomocí dat uložených v databázi PostgreSQL, zvažte použití [MADlib](https://madlib.incubator.apache.org/).

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

Azure SQL Data Warehouse je cloudová databáze s horizontálním navýšením kapacity, která dokáže zpracovávat obrovské objemy dat, relační i nerelační. Další informace najdete v tématu [Co je Azure SQL Data Warehouse?](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Chcete-li se připojit k datovému skladu a vytvořit tabulku, spusťte z příkazového řádku následující příkaz:

```Bash
sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I
```

Na příkazovém řádku sqlcmd spusťte tento příkaz:

```SQL
CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
GO
```

Zkopírujte data pomocí bcp:

```bash
bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"
```

> [!NOTE]
> Stažený soubor obsahuje konce řádků ve stylu systému Windows. Nástroj bcp očekává konce čar ve stylu Unixu. Pomocí příznaku -r sdělte bcp.

Potom dotaz pomocí sqlcmd:

```sql
select top 10 spam, char_freq_dollar from spam;
GO
```

Můžete také dotaz pomocí SQuirreL SQL. Pomocí ovladače JDBC serveru SQL Server postupujte podobně jako PostgreSQL. Ovladač JDBC je ve složce /usr/share/java/jdbcdrivers/sqljdbc42.jar.

## <a name="next-steps"></a>Další kroky

Přehled článků, které vás provedou úkoly, které tvoří proces datové vědy v Azure, najdete v [tématu Proces vědecké ho spoje týmových dat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview).

Popis komplexních návodů, které ukazují kroky v procesu vědecké vědy o týmových datech pro konkrétní scénáře, naleznete v [návodech k procesu vědeckého procesu týmových dat](../team-data-science-process/walkthroughs.md). Návody také ilustrují, jak kombinovat cloudové a místní nástroje a služby do pracovního postupu nebo kanálu a vytvořit inteligentní aplikaci.
