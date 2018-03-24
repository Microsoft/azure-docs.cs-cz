---
title: Rychlý úvodní kurz pro jazyk R pro Machine Learning | Microsoft Docs
description: Použijte tento kurz programovací R začít rychle vytvářet prognózy řešení pomocí jazyka R s Azure Machine Learning Studio.
keywords: Rychlý start, jazyk r, programovací jazyk r, programovací kurzu r
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 99a3a0fd-b359-481a-b236-66868deccd96
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.openlocfilehash: 231d505e91fc036b30344e2fd9971db8ba2fdf05
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="quickstart-tutorial-for-the-r-programming-language-for-azure-machine-learning"></a>Stručný úvodní kurz k programovacímu jazyku R pro službu Azure Machine Learning

<!-- Stephen F Elston, Ph.D. -->

## <a name="introduction"></a>Úvod
Tento rychlý úvodní kurz vám pomůže rychle spustit pomocí programovacího jazyka R rozšíření Azure Machine Learning. V tomto kurzu R programování pro vytvoření, testování a spouštění R kódu v rámci Azure Machine Learning. Při práci prostřednictvím kurzu vytvoříte pomocí jazyka R v Azure Machine Learning kompletního řešení prognózy.  

Microsoft Azure Machine Learning obsahuje mnoho výkonné machine learning a data moduly manipulaci s. Efektivní jazyk R popsán jako lingua franca obchodu Analytics. Manipulace s analytickými funkcemi a data v Azure Machine Learning brouka, lze rozšířit pomocí R. Tato kombinace poskytne škálovatelnost a snadné nasazení Azure Machine Learning flexibilitu a hloubkové analytics R.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

### <a name="forecasting-and-the-dataset"></a>Vytváření prognóz a datové sady
Prognózy je metoda analytical široce zaměstnání a velmi užitečné. Běžné používá rozsah od predikci prodeje sezónní položek, určení úrovní optimální inventáře pro predikci makroekonomické proměnné. Prognózy se většinou děje s modely časové řady.

Časových řad dat jsou data ve kterém mají hodnoty indexu čas. Index čas může být pravidelné, např. každý měsíc nebo každou minutu nebo nestandardní. Model časové řady podle data časové řady. R programovací jazyk obsahuje flexibilní framework a rozsáhlé analýzy pro data časové řady.

V této příručce rychlý start jsme bude pracovat s produkční dojnic kalifornské a ceny data. Tato data zahrnují měsíční informace o provozní mléčných produktů a cenu mléka fat komoditním srovnávacího testu.

Data použitá v tomto článku, spolu se skripty R, může být [stáhnout zde][download]. Tato data byla původně syntetizován z informací z univerzity Wisconsin v http://future.aae.wisc.edu/tab/production.html.

### <a name="organization"></a>Organizace
Jsme proběhne prostřednictvím několik kroků, jak zjistíte, jak k vytváření, testování a spouštění kódu manipulaci s R analytics a data v prostředí Azure Machine Learning.  

* Nejprve se podíváme na základní informace o použití jazyka R v prostředí Azure Machine Learning Studio.
* Potom jsme průběhu hovoříte o různé aspekty vstupně-výstupních operací pro data, kódu jazyka R a grafiky v prostředí Azure Machine Learning.
* První část naše řešení prognózy jsme se pak vytvořit tak, že vytvoříte kód pro čištění dat a transformace.
* Naše data připravený provedeme analýzu korelací mezi několika proměnných v naší datové sadě.
* Nakonec vytvoříme předpovědi modelu sezónní časové řady pro produkci mléka.

## <a id="mlstudio"></a>Komunikovat s jazyk R v Machine Learning Studio
Tato část vás provede některé základní informace o interakci s programovací jazyk R v prostředí nástroje Machine Learning Studio. Jazyk R poskytuje výkonný nástroj pro vytvoření přizpůsobené analýzy a moduly manipulaci dat v prostředí Azure Machine Learning.

K vývoji, testování a ladění kódu jazyka R v malém měřítku použiji Rstudia. Tento kód je pak vyjímání a vkládání do [spustit skript jazyka R] [ execute-r-script] modulu v nástroji Machine Learning Studio připraven ke spuštění.  

### <a name="the-execute-r-script-module"></a>Modul spustit skript jazyka R
V rámci Machine Learning Studio, R skripty se spouštějí v rámci [spustit skript jazyka R] [ execute-r-script] modulu. Příkladem [spustit skript jazyka R] [ execute-r-script] modulu v nástroji Machine Learning Studio je znázorněno na obrázku 1.

 ![Programovací jazyk R: spuštění skriptu R modulu vybrané v nástroji Machine Learning Studio][1]

*Obrázek 1. Zobrazuje vybraný modul spustit skript jazyka R prostředí Machine Learning Studio.*

Odkaz na obrázek 1, podíváme se na některé z klíčových částí Machine Learning Studio prostředí pro práci s [spustit skript jazyka R] [ execute-r-script] modulu.

* V prostředním podokně se zobrazí moduly v experimentu.
* Horní část v pravém podokně obsahuje okno zobrazení a úprava skripty R.  
* Dolní části pravém podokně jsou uvedeny některé vlastnosti [spustit skript jazyka R][execute-r-script]. Protokoly chyb a výstupu můžete zobrazit kliknutím na příslušné body této podokna.

Jsme bude samozřejmě být hovoříte o [spustit skript jazyka R] [ execute-r-script] podrobněji ve zbývající části tohoto dokumentu.

Při práci s funkcemi, komplexní R, doporučujeme úpravy, testování a ladění ve Rstudia. Stejně jako u jakékoli vývoj softwaru postupně rozšiřovat kódu a testování na malé jednoduchých testovacích případech. Potom kopírování a vkládání funkcí do okno skriptu R [spustit skript jazyka R] [ execute-r-script] modulu. Tento přístup umožňuje plně využívat Rstudia integrované vývojové prostředí (IDE) a možnosti Azure Machine Learning.  

#### <a name="execute-r-code"></a>Spuštění kódu jazyka R
Žádný kód R v [spustit skript jazyka R] [ execute-r-script] modulu se spustí, když spustíte kliknutím na experiment **spustit** tlačítko. Po dokončení provádění zaškrtnutí se zobrazí na [spustit skript jazyka R] [ execute-r-script] ikonu.

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Obranným R kódování pro Azure Machine Learning
Pokud vyvíjíte kódu jazyka R pro vyslovení, webové služby pomocí Azure Machine Learning, byste měli výborný naplánovat, jak váš kód se bude zabývat vstup neočekávaná data a výjimky. Pokud chcete zachovat přehlednost, nebyly připojená mnohem cestě kontrolují nebo zpracování výjimek v většinu příklady kódu ukazuje. Ale jak budeme pokračovat I získáte několik příkladů, funkce pomocí funkce pro zpracování výjimek na R.  

Pokud potřebujete podrobnější zacházení s zpracovávání výjimek v jazyce R, I doporučujeme číst v příslušných částech seznamu pomocí Wickham uvedené v [příloha B – další čtení](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio"></a>Ladění a testování R v Machine Learning Studio
Chcete-li znovu opakují, I doporučujeme testování a ladění kódu R v malém měřítku v Rstudia. Ale existují případy, kdy je potřeba sledovat potíže kód R v [spustit skript jazyka R] [ execute-r-script] sám sebe. Kromě toho je vhodné zkontrolovat výsledky v nástroji Machine Learning Studio.

Výstup z provádění kódu R a na platformě Azure Machine Learning především v souboru výstup.log nebyl nalezen. Některé další informace se zobrazí v error.log.  

Pokud při běhu kódu R dojde k chybě v nástroji Machine Learning Studio, vaše první postupu by měl být podívat se na error.log. Tento soubor může obsahovat užitečné chybové zprávy, které vám pomůžou pochopit a vaše chybu opravit. Chcete-li zobrazit error.log, klikněte na **zobrazení v protokolu chyb** na **podokno properties** pro [spustit skript jazyka R] [ execute-r-script] obsahující chyby.

Například byl spuštěn následující kód R nedefinované proměnné y, v [spustit skript jazyka R] [ execute-r-script] modul:

    x <- 1.0
    z <- x + y

Tento kód se nepodaří spustit, což vede k chybě. Kliknutím na **zobrazení v protokolu chyb** na **podokno properties** vytvoří zobrazení znázorňuje obrázek 2.

  ![Chybová zpráva otevíraném][2]

*Obrázek 2. Chybová zpráva automaticky otevírané okno.*

Zdá se, musíme naleznete v souboru výstup.log zobrazíte R chybová zpráva. Klikněte na [spustit skript jazyka R] [ execute-r-script] a potom klikněte na **zobrazení souboru výstup.log** položku **podokno properties** vpravo. Otevře nové okno prohlížeče a I v tomto tématu.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Tato chybová zpráva obsahuje žádný výskyt nečekaných událostí a jednoznačně identifikuje problém.

Chcete-li zjistit hodnotu všech objektů v R, můžete vytisknout tyto hodnoty do souboru výstup.log souboru. Pravidla pro zkoumání hodnoty objektu jsou v podstatě stejný jako interaktivní relace R zařízením. Například pokud zadáte název proměnné na řádek, hodnota objektu budou vytištěny do souboru výstup.log souboru.  

#### <a name="packages-in-machine-learning-studio"></a>Balíčky v nástroji Machine Learning Studio
Azure Machine Learning se dodává s více než 350 předinstalované balíčky jazyk R. Můžete použít následující kód [spustit skript jazyka R] [ execute-r-script] modul načíst seznam v předinstalovaných balíčcích.

    data.set <- data.frame(installed.packages())
    maml.mapOutputPort("data.set")

Pokud nerozumíte poslední řádek tohoto kódu v okamžiku, přečtěte si. Ve zbývající části tohoto dokumentu hojně pojednává o využití R v prostředí Azure Machine Learning.

### <a name="introduction-to-rstudio"></a>Úvod do Rstudia
Rstudia je často používaný IDE pro R. Pro úpravy, testování a ladění některé kód R použité v této úvodní příručce použiji Rstudia. Jakmile kódu jazyka R otestované a připravené, jednoduše kopírování a vkládání z editoru Rstudia do nástroje Machine Learning Studio [spustit skript jazyka R] [ execute-r-script] modulu.  

Pokud nemáte programovací jazyk R nainstalovaný na počítači klientů, doporučujeme, že udělejte to teď. Ke stažení zdarma jazyka R s otevřeným zdrojem jsou k dispozici na komplexní R archivu sítě (CRAN) v [ http://www.r-project.org/ ](http://www.r-project.org/). Nejsou k dispozici pro Windows, Mac OS a Linux a UNIX stáhne. Zvolte blízkým zrcadlení a postupujte podle pokynů ke stažení. Kromě toho CRAN obsahuje širokou řadu užitečné balíčky manipulaci s analytickými funkcemi a data.

Pokud jste ještě Rstudia, by měl stáhněte a nainstalujte verzi pro stolní počítače. Můžete najít Rstudia soubory ke stažení pro Windows, Mac OS a Linux a UNIX v http://www.rstudio.com/products/RStudio/. Postupujte podle pokynů k instalaci Rstudia na stolního počítače.  

Je k dispozici v kurzu Úvod do Rstudia https://support.rstudio.com/hc/sections/200107586-Using-RStudio.

Poskytují I některé další informace o používání Rstudia v [příloha A][appendixa].  

## <a id="scriptmodule"></a>Získání dat a deaktivovat modul spustit skript jazyka R
V této části probereme, jak načíst data do a z [spustit skript jazyka R] [ execute-r-script] modulu. Bude jsme si, jak zpracovávat různé datové typy číst do a z [spustit skript jazyka R] [ execute-r-script] modulu.

Kód dokončení pro tento oddíl je v souboru zip, které jste dříve stáhli.

### <a name="load-and-check-data-in-machine-learning-studio"></a>Načtení a zkontrolujte, zda data v nástroji Machine Learning Studio
#### <a id="loading"></a>Načíst datové sady
Spustíme načtením **csdairydata.csv** souboru do Azure Machine Learning Studio.

* Spuštění prostředí Azure Machine Learning Studio.
* Klikněte na **+ nový** v levém dolním rohu obrazovky a vyberte **datovou sadu**.
* Vyberte **z místního souboru**a potom **Procházet** a vyberte soubor.
* Ujistěte se, zda jste vybrali **souboru obecné CSV s hlavičkou (.csv)** jako typ pro datovou sadu.
* Kliknutím na značku zaškrtnutí.
* Až se nahrají datovou sadu, měli byste vidět nová datová sada kliknutím na **datové sady** kartě.  

#### <a name="create-an-experiment"></a>Vytvoření experimentu
Teď, když máme některá data v nástroji Machine Learning Studio, je potřeba vytvořit nový experiment, chcete-li provést analýzu.  

* Klikněte na **+ nový** na nižší vlevo a vyberte **experimentu**, pak **prázdný Experiment**.
* Výběrem a úpravy, můžete pojmenovat experimentu **experimentu vytvořena na...**  nadpis v horní části stránky. Například změna jeho **certifikační Autority mlékárny Analysis**.
* Na levé straně stránky experiment, rozbalte položku **uložit datové sady**a potom **Moje datové sady**. Měli byste vidět **cadairydata.csv** který jste dříve nahráli.
* Přetáhnout myší **datovou sadu csdairydata.csv** do experimentu.
* V **vyhledávání experimentovat položky** pole nahoře v levém podokně, typ [spustit skript jazyka R][execute-r-script]. Zobrazí se v modulu se zobrazí v seznamu hledání.
* Přetáhnout myší [spustit skript jazyka R] [ execute-r-script] modulu do vaší palety.  
* Připojit výstup **csdairydata.csv datovou sadu** krajní levé vstupu (**Dataset1**) z [spustit skript jazyka R][execute-r-script].
* **Nezapomeňte klikněte na 'Uložit'!**  

V tomto okamžiku by měl experimentu vypadat podobně jako obrázek 3.

![Analýza mlékárny certifikační Autority experimentovat s datovou sadu a modul pro spuštění skriptu jazyka R][3]

*Obrázek 3. Analýza mlékárny certifikační Autority experimentovat s datovou sadu a spustit skript jazyka R modulu.*

#### <a name="check-on-the-data"></a>Zkontrolujte na data
Pojďme Podíváme se na data, která mají byl načten do našich experimentu. V rámci experimentu klikněte na výstup **datovou sadu cadairydata.csv** a vyberte **vizualizovat**. Měli byste vidět něco podobného jako na obrázku 4.  

![Souhrn cadairydata.csv datové sady][4]

*Obrázek 4. Souhrn cadairydata.csv datové sady.*

V tomto zobrazení vidíte mnoho užitečných informací. Vidíme první několik řádků z této datové sady. Pokud jsme vyberte sloupec, v části Statistika zobrazíte další informace o sloupci. Typ funkce řádek příkladu nám jaké typy dat Azure Machine Learning Studio přiřazené ke sloupci. Rychle zkontrolovat tímto způsobem je kontrolu dobrý správností než začneme jakékoli závažné práci.

### <a name="first-r-script"></a>První skript jazyka R
Umožňuje vytvořit jednoduché první skript jazyka R a experimentovat s v Azure Machine Learning Studio. I vytvoření a otestování následující skript v Rstudia.  

    ## Only one of the following two lines should be used
    ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
    ## If in RStudio, use the second line with read.csv()
    cadairydata <- maml.mapInputPort(1)
    # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
    str(cadairydata)
    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
    ## The following line should be executed only when running in
    ## Azure Machine Learning Studio
    maml.mapOutputPort('cadairydata')

Nyní je třeba přenést do Azure Machine Learning Studio tento skript. Může stačí vyjmout a vložit. Ale v takovém případě bude přenést Moje skript jazyka R prostřednictvím soubor zip.

### <a name="data-input-to-the-execute-r-script-module"></a>Vložení dat do modulu spustit skript jazyka R
Pojďme Podíváme se na vstupy pro [spustit skript jazyka R] [ execute-r-script] modulu. V tomto příkladu jsme bude číst data dojnic kalifornské do [spustit skript jazyka R] [ execute-r-script] modulu.  

Existují tři možné vstupy pro [spustit skript jazyka R] [ execute-r-script] modulu. Může používat kterékoli nebo všechny tyto vstupy, v závislosti na vaší aplikace. Je také perfektně vhodné použít R skript, který nemá žádný vstup vůbec.  

Podívejme se na každý z těchto vstupy, budete zleva doprava. Se zobrazí názvy jednotlivých ze vstupních údajů umístěním kurzoru přes vstupu a čtení popisek.  

#### <a name="script-bundle"></a>Skript sady
Vstupní sadu skriptu můžete předat obsahu souboru zip do [spustit skript jazyka R] [ execute-r-script] modulu. Jeden z následujících příkazů můžete načíst obsah souboru zip do vašeho kódu jazyka R.

    source("src/yourfile.R") # Reads a zipped R script
    load("src/yourData.rdata") # Reads a zipped R data file

> [!NOTE]
> Azure Machine Learning zpracovává soubory v souboru zip, jako kdyby se src / adresáře, takže potřebujete předpony názvy souborů s tímto názvem adresáře. Například, pokud souboru zip obsahuje soubory `yourfile.R` a `yourData.rdata` v kořenovém souboru zip, které by vyřešit jako `src/yourfile.R` a `src/yourData.rdata` při použití `source` a `load`.
> 
> 

Už jsme probírali načítání datové sady v [načítání datovou sadu](#loading). Po vytvoření a testování skriptu jazyka R uvedené v předchozí části, postupujte takto:

1. Uložit do skriptu jazyka R. R soubor. Můžu volat Moje soubor skriptu "simpleplot. R". Zde je obsah.
   
        ## Only one of the following two lines should be used
        ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
        ## If in RStudio, use the second line with read.csv()
        cadairydata <- maml.mapInputPort(1)
        # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
        str(cadairydata)
        pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
        ## The following line should be executed only when running in
        ## Azure Machine Learning Studio
        maml.mapOutputPort('cadairydata')
2. Vytvořte soubor zip a zkopírujte skript do tohoto souboru zip. V systému Windows, klikněte pravým tlačítkem na soubor a vyberte **poslat**a potom **komprimované složky**. Tím se vytvoří nový soubor zip obsahující "simpleplot. Soubor R".
3. Přidání souboru do **datové sady** v nástroji Machine Learning Studio, určení typu jako **zip**. Měli byste vidět teď tento soubor zip v vaše datové sady.
4. Přetáhnout myší ze souboru zip **datové sady** na **ML Studio plátno**.
5. Připojit výstup **zip data** ikonu **skript sady** vstupní z [spustit skript jazyka R] [ execute-r-script] modulu.
6. Typ `source()` nahraďte názvem souboru zip do okna kódu pro funkce [spustit skript jazyka R] [ execute-r-script] modulu. V případě Moje zadali `source("src/simpleplot.R")`.  
7. Ujistěte se, kliknete na tlačítko **Uložit**.

Po dokončení těchto kroků [spustit skript jazyka R] [ execute-r-script] modul provede R skript v souboru zip při spuštění experimentu. V tomto okamžiku by měl experimentu vypadat podobně jako obrázek 5.

![Experiment pomocí komprimované skript jazyka R][6]

*Obrázek 5. Experiment pomocí komprimované R skript.*

#### <a name="dataset1"></a>Dataset1
Můžete předat obdélníková tabulku dat do kódu R pomocí Dataset1 vstup. V našem jednoduchého skriptu `maml.mapInputPort(1)` funkce čte data z portu 1. Tato data pak přiřazen název proměnné dataframe ve vašem kódu. V našem jednoduchého skriptu provede první řádek kódu přiřazení.

    cadairydata <- maml.mapInputPort(1)

Kliknutím na spustit experimentu **spustit** tlačítko. Pokud je spuštění hotové, klikněte na [spustit skript jazyka R] [ execute-r-script] modul a potom klikněte na **zobrazit výstup protokol** v podokně Vlastnosti. Nová stránka by se zobrazit v prohlížeči zobrazuje obsah souboru výstup.log souboru. Po přejděte dolů se měl zobrazit přibližně takto.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Dále dolní části stránky je že podrobnější informace o sloupce, které bude vypadat podobně jako následující.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput]
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput]
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
    [ModuleOutput]
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput]
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput]
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput]
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...

Tyto výsledky se většinou podle očekávání, s 228 připomínky a 9 sloupců v dataframe. Jsme můžete zobrazit názvy sloupců, datový typ R a ukázku jednotlivých sloupců.

> [!NOTE]
> Tento stejný tištěné výstup je pohodlně dostupná z výstupu R zařízení [spustit skript jazyka R] [ execute-r-script] modulu. Podrobně probereme výstupů [spustit skript jazyka R] [ execute-r-script] modulu v další části.  
> 
> 

#### <a name="dataset2"></a>Dataset2
Chování Dataset2 vstupu je shodná s Dataset1. Pomocí tento vstup lze předat v druhé tabulce obdélníková dat do vašeho kódu jazyka R. Funkce `maml.mapInputPort(2)`, s argumentem 2, slouží k předávání tato data.  

### <a name="execute-r-script-outputs"></a>Spustit skript jazyka R výstupy
#### <a name="output-a-dataframe"></a>Výstup a dataframe
Výstup můžete obsah dataframe R jako obdélníková tabulku přes port výsledek Dataset1 pomocí `maml.mapOutputPort()` funkce. V našem jednoduchý skript jazyka R se provádí pomocí následujícího řádku.

    maml.mapOutputPort('cadairydata')

Po spuštění experimentu, klikněte na výstupní port Dataset1 výsledek a pak klikněte na **vizualizovat**. Měli byste vidět něco podobného jako obrázek 6.

![Vizualizaci výstupních dat dojnic kalifornské][7]

*Obrázek 6. Vizualizaci výstupních dat dojnic kalifornské.*

Tento výstup vypadá stejný jako vstup, přesně tak, jak očekávali jsme.  

### <a name="r-device-output"></a>Výstup R zařízení
Výstup zařízení [spustit skript jazyka R] [ execute-r-script] modul obsahuje zprávy a grafický výstup. Standardní výstupní zařízení a standardní cíl chybové zprávy i z R jsou odesílány na výstupní port R zařízení.  

Chcete-li zobrazit výstup R zařízení, klikněte na port a pak na **vizualizovat**. Vidíte standardní výstupní zařízení a standardní chyba ze skriptu R na obrázku 7.

![Standardní výstupní zařízení a standardní chyba z portu R zařízení][8]

*Obrázek 7. Standardní výstupní zařízení a standardní chyba z portu R zařízení.*

Posouvání dolů nemůžeme zobrazit výstup grafiky z našich skript jazyka R na obrázku 8.  

![Grafického výstupu z portu R zařízení][9]

*Obrázek 8. Grafika výstup z portu R zařízení.*  

## <a id="filtering"></a>Filtrování dat a transformace
V této části provedeme některé operace transformace na datech dojnic kalifornské a základní data filtrování. Na konci této části máme data ve formátu, který je vhodný pro sestavování analytického modelu.  

Přesněji řečeno, v této části provedeme několik běžných čištění a transformace úlohy dat: zadejte transformaci, filtrování dataframes, přidání nové počítaných sloupcích a hodnota transformace. Tato pozadí pomáhají řešit mnoho variant v reálných problémy.

Kód dokončení R pro tento oddíl je k dispozici v souboru zip, které jste dříve stáhli.

### <a name="type-transformations"></a>Typ transformace
Teď, když jsme můžete číst data dojnic kalifornské do kód R [spustit skript jazyka R] [ execute-r-script] modulu, musíme zkontrolujte, zda má určený typ a formát data ve sloupcích.  

R je dynamicky zadávaných jazyk, což znamená, že datové typy jsou má mezi sebou podle potřeby. Atomic datových typů v R zahrnují číselné literály, logické a znak. Faktor typ se používá k uložení kompaktně kategorizovaná data. Mnohem Další informace o typech dat najdete v odkazech na v [příloha B – další čtení](#appendixb).

Když tabulková data je pro čtení do R z externího zdroje, vždycky je vhodné zkontrolovat výsledné typy ve sloupcích. Může být vhodné sloupec – znak typu, ale v mnoha případech to se zobrazí jako faktor nebo naopak. V ostatních případech a sloupec, který si myslíte, že by měl být číselný je reprezentována textová data, například '1,23' než 1,23 jako plovoucí bodu číslo.  

Naštěstí je snadné jeden typ převést do jiného, tak dlouho, dokud je možné mapování. Například 'Nevada' nelze převést číselnou hodnotu, ale můžete ji převést faktor (kategorií proměnné). Například můžete převést číselnou 1 znak '1' nebo koeficient.  

Syntaxe pro některý z těchto převody je jednoduchý: `as.datatype()`. Tyto funkce pro převod typů patří.

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Prohlížení datové typy sloupců nemůžeme vstup v předchozí části: jsou všechny sloupce typu číselné, s výjimkou sloupec s názvem 'Měsíc', který je – znak typu. Umožňuje převést to faktor a testovat výsledky.  

Odstraněný řádek, který vytvoří matice scatterplot a přidat řádek převádění sloupci, měsíc, faktor. V experimentu se právě kopírování a vkládání kódu jazyka R do okna kódu [spustit skript jazyka R] [ execute-r-script] modulu. Můžete také aktualizovat soubor zip a nahrajte ho do Azure Machine Learning Studio, ale tato akce trvá několik kroků.  

    ## Only one of the following two lines should be used
    ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
    ## If in RStudio, use the second line with read.csv()
    cadairydata <- maml.mapInputPort(1)
    # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
    ## Ensure the coding is consistent and convert column to a factor
    cadairydata$Month <- as.factor(cadairydata$Month)
    str(cadairydata) # Check the result
    ## The following line should be executed only when running in
    ## Azure Machine Learning Studio
    maml.mapOutputPort('cadairydata')

Umožňuje spustit tento kód a podívejte se na výstup protokolu pro R skript. Příslušných dat z protokolu je vidět na obrázku 9.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Obrázek 9. Shrnutí dataframe s Multi-Factor proměnné.*

Nyní by mělo být uvedeno na typ pro měsíc '**Multi-Factor s 14 úrovně**'. Tento problém je vzhledem k tomu, že existují pouze po dobu 12 měsíců v roce. Můžete také zkontrolovat, zjistíte, že je typu v **vizualizovat** datové sady výsledků port je '**Categorical**'.

Problém je, že sloupci, měsíc, nebyl systematičtěji programového. V některých případech se nazývá měsíc duben a k ostatním se zkracuje dubna. Ořízne řetězec, který má 3 znaky jsme můžete tento problém vyřešit. Řádek kódu teď vypadá takto:

    ## Ensure the coding is consistent and convert column to a factor
    cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))

Spusťte experiment a zobrazte protokol výstup. Očekávané výsledky jsou zobrazené na obrázku 10.  

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Obrázek 10. Shrnutí dataframe s správný počet úrovní faktor.*

Naše Multi-Factor proměnná teď má požadovanou 12 úrovně.

### <a name="basic-data-frame-filtering"></a>Filtrování rámce základní data
R dataframes podporovat výkonné možnosti filtrování. Datové sady, může být podsady pomocí logických filtry na řádky nebo sloupce. V mnoha případech se bude vyžadovat složitá kritéria filtru. Odkazy v [příloha B – další čtení](#appendixb) obsahovat rozsáhlé příklady filtrování dataframes.  

Je jeden bit filtrování by měl provedeme v naší datové sadě. Pokud se podíváte na sloupce v cadairydata dataframe, zobrazí se dva nepotřebných sloupců. První sloupec obsahuje jenom číslo řádku, které není velmi užitečné. Druhý sloupec Year.Month, obsahuje redundantní informace. Pomocí následujícího kódu R jsme můžete snadno vyloučit tyto sloupce.

> [!NOTE]
> Od této chvíle v této části I právě zjistíte další kód přidám v [spustit skript jazyka R] [ execute-r-script] modulu. Přidat každý nový řádek **před** `str()` funkce. Mohu pomocí této funkce můžete ověřit Moje výsledky v Azure Machine Learning Studio.
> 
> 

I na můj kód R v přidejte následující řádek [spustit skript jazyka R] [ execute-r-script] modulu.

    # Remove two columns we do not need
    cadairydata <- cadairydata[, c(-1, -2)]

Spusťte tento kód v experimentu a zkontrolujte výsledek z výstupu protokolu. Tyto výsledky se zobrazí v obrázek 11.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  7 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Obrázek 11. Shrnutí dataframe s dva sloupce a bude odebrána.*

Dobrá zpráva! Nemůžeme získat očekávané výsledky.

### <a name="add-a-new-column"></a>Umožňuje přidat nový sloupec
Chcete-li vytvořit modely časové řady bude vhodné mít sloupce obsahujícího měsíců od začátku časové řady. Vytvoříme nový sloupec, Month.Count'.

Kvůli lepší organizaci kód vytvoříme naše první jednoduché funkce `num.month()`. Pak budou použity této funkci můžete vytvořit nový sloupec v dataframe. Nový kód je následující.

    ## Create a new column with the month count
    ## Function to find the number of months from the first
    ## month of the time series
    num.month <- function(Year, Month) {
      ## Find the starting year
      min.year  <- min(Year)

      ## Compute the number of months from the start of the time series
      12 * (Year - min.year) + Month - 1
    }

    ## Compute the new column for the dataframe
    cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)

Teď spusťte aktualizované experiment a používat protokol výstup zobrazíte výsledky. Tyto výsledky se zobrazí obrázek 12.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Obrázek 12. Shrnutí dataframe s další sloupec.*

Vypadá to vše funguje. V našem dataframe máme nový sloupec s očekávaných hodnot.

### <a name="value-transformations"></a>Hodnota transformace
V této části provedeme některé jednoduché transformace na hodnoty v některé z našich dataframe sloupce. Jazyk R podporuje transformace téměř libovolná hodnota. Odkazy v [příloha B – další čtení](#appendixb) obsahovat rozsáhlé příklady.

Pokud se podíváte na hodnoty v souhrnných informací o našem dataframe měli byste vidět něco liché sem. Další zmrzlinová než mléka vytváří v kalifornské? Ne, samozřejmě není, jak to nemá smysl sad jako tento fakt může být pro některé nám zmrzlinová lovers. Jednotky se liší. Ceny se nám jednotky libra mléka je v jednotkách 1 milion libra USA, zmrzlinová je v jednotkách 1 000 nám galony a byt sýr je v jednotkách 1 000 libra USA. Za předpokladu, že zmrzlinová váží asi 6,5 libra za spotřeby, můžete snadno provedeme násobení převést tyto hodnoty tak, aby byly všechny ve stejné jednotky libra 1 000.

Pro náš model prognózy používáme multiplikativní model pro trendu a sezónní úpravu tato data. Transformace protokolu budeme tak moct používat model lineární ke zjednodušení tohoto procesu. Transformace protokolu ve stejné funkci, kdy se používá násobitel jsme můžete použít.

V následujícím kódu I definovat novou funkci, `log.transform()`a použijte ho pro řádky obsahující číselné hodnoty. R `Map()` funkce se používá k aplikování `log.transform()` funkce na vybrané sloupce dataframe. `Map()` je podobná `apply()` , ale umožňuje více než jeden seznam argumentů funkce. Všimněte si, že poskytuje seznam multiplikátory druhý argument `log.transform()` funkce. `na.omit()` Funkce slouží jako kousek čištění k zajištění nemáme chybějící nebo nedefinované hodnoty dataframe.

    log.transform <- function(invec, multiplier = 1) {
      ## Function for the transformation, which is the log
      ## of the input value times a multiplier

      warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                           "ERROR: Arguments to function log.transform must be greate than zero",
                           "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                           "ERROR: Invalid time seies value encountered in function log.transform"
                           )

      ## Check the input arguments
      if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
      if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
      if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

      ## Wrap the multiplication in tryCatch
      ## If there is an exception, print the warningmessage to
      ## standard error and return NA
      tryCatch(log(multiplier * invec),
               error = function(e){warning(warningmessages[4]); NA})
    }


    ## Apply the transformation function to the 4 columns
    ## of the dataframe with production data
    multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
    cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

    ## Get rid of any rows with NA values
    cadairydata <- na.omit(cadairydata)  

Je s bit situaci v `log.transform()` funkce. Většina tento kód program kontroluje potenciální problémy s argumenty nebo práci s výjimky, které mohou stále nastat během výpočtů. Tento kód jenom pár řádků ve skutečnosti to výpočtů.

Aby se zabránilo selhání jedné funkce, která zabraňuje zpracování pokračovat je cílem Obranným programování. K náhlému selhání dlouho běžící analysis může být poměrně frustrující pro uživatele. Abyste předešli této situaci, musí být vybrána výchozí návratové hodnoty, která omezí škody podřízené zpracování. Zprávy také vytváří které uživatele upozorní, že něco nepovede.

Pokud není jste zvyklí Obranným programování v R, tento kód se může zdát trochu čtenáře. I vás provede důležitými kroky:

1. Vektor čtyři zprávy je definována. Tyto zprávy se používají ke sdělování informací o některých možné chyby a výjimky, které se můžou vyskytnout s tímto kódem.
2. Návratu NA hodnotu v obou případech. Existuje mnoho dalších možností, které by mohly mít méně vedlejší účinky. Vektor nul nebo původní vstupní vektoru, může například návratu.
3. Kontroluje se spouštějí na argumenty funkce. V každém případě pokud je detekována chyba, je vrácen výchozí hodnotu a je produkovaný zprávu `warning()` funkce. Používám `warning()` místo `stop()` jako druhá možnost se ukončí provádění, přesně co chcete vyhnout. Všimněte si, že I mají zapisovat tento kód v procedurální styl, jako v tomto případě funkční přístup mailech vypadalo komplexní a skrytého.
4. Výpočty protokolu jsou uzavřen do `tryCatch()` tak, aby výjimky nezpůsobí náhlému zastavení na zpracování. Bez `tryCatch()` aktivováno R funkce povede signál k zastavení, která zajišťuje právě, většina chyb.

Spustit tento kód R v experimentu a podíváme se na tištěných výstup v souboru výstup.log souboru. Nyní uvidíte transformovaných hodnoty čtyři sloupce v protokolu, jak ukazuje obrázek 13.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Obrázek 13. Souhrn transformovaných hodnot v dataframe.*

Vidíte, že byly transformovány hodnoty. Nyní produkce mléka výrazně přesahuje všechny ostatní mléčný výrobek produkční, vrací, že jsme teď vyhledávání škálované protokolu.

V tomto okamžiku je naše data vyčištěna a jsme připraveni pro některé modelování. Prohlížení vizualizace shrnutí výstupní datovou sadu výsledků z našich [spustit skript jazyka R] [ execute-r-script] modulu, zobrazí se vám sloupci, měsíc, je 'Categorical' s 12 jedinečné hodnoty, znovu, stejně jako jsme chtěli.

## <a id="timeseries"></a>Časové řady objekty a analýzy korelace
V této části jsme se několika základních objektů řady čas R zkoumat a analyzovat korelací mezi určité proměnné. Naším cílem je výstup dataframe obsahujícího informace o pairwise korelace na několik pomalou.

Kód dokončení R pro tento oddíl je v souboru zip, které jste dříve stáhli.

### <a name="time-series-objects-in-r"></a>Časové řady objekty v R
Jak už jsme už zmínili, časové řady jsou řady hodnot dat indexované podle času. R časové řady objekty se používají k vytváření a správě index čas. Existuje několik výhod používání objektů časové řady. Časové řady objekty vás zbaví mnoho podrobnosti o správě časových řad index hodnoty, které jsou zapouzdřené v objektu. Kromě toho časové řady objekty umožňují používat mnoho způsobů časové řady pro vykreslení, tisk, modelování a podobně.

Třída POSIXct čas řady se často používá a je poměrně jednoduché. Toto časové řady třídy míry čas od začátku epoch, 1. ledna 1970. V tomto příkladu použijeme POSIXct časové řady objekty. Další často používaný R časové řady tříd objektů zahrnují zoo a xts, extensible časové řady.
<!-- Additional information on R time series objects is provided in the references in Section 5.7. [commenting because this section doesn't exist, even in the original] -->

### <a name="time-series-object-example"></a>Příklad objekt časové řady
Můžeme začít pracovat s našem příkladu. Přetáhnout myší **nové** [spustit skript jazyka R] [ execute-r-script] modulu do experimentu. Připojit existující výstupní port Dataset1 výsledek [spustit skript jazyka R] [ execute-r-script] modulu Dataset1 vstupní port nové [spustit skript jazyka R] [ execute-r-script] modulu.

Jako jsme to udělali pro první příklady, jak jsme průběhu procházení v příkladu, v některých bodech I se zobrazí pouze přírůstkové další řádky kódu jazyka R při každém kroku.  

#### <a name="reading-the-dataframe"></a>Čtení dataframe
Jako první krok můžeme načtení dataframe a ujistěte se, že se nám získat očekávané výsledky. Následující kód by měl provést úlohu.

    # Comment the following if using RStudio
    cadairydata <- maml.mapInputPort(1)
    str(cadairydata) # Check the results

Teď spusťte experiment. Protokol nový tvar spustit skript jazyka R by měl vypadat jako obrázek 14.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...

*Obrázek 14. Shrnutí dataframe v modulu skriptu jazyka R provést.*

Tato data jsou očekávané typy a formát. Upozorňujeme, že sloupec 'Měsíc' je typ faktoru a má očekávaný počet úrovní, které.

#### <a name="creating-a-time-series-object"></a>Vytvoření objektu časové řady
Je potřeba přidat do našich dataframe objekt časové řady. Nahraďte kód aktuální následující text, který přidá nový sloupec třídy POSIXct.

    # Comment the following if using RStudio
    cadairydata <- maml.mapInputPort(1)

    ## Create a new column as a POSIXct object
    Sys.setenv(TZ = "PST8PDT")
    cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

    str(cadairydata) # Check the results

Nyní vyhledejte v protokolu. By měl vypadat jako obrázek 15.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*Obrázek 15. Shrnutí dataframe s objektem časové řady.*

Jsme můžete zobrazit z souhrn, který nového sloupce, který je ve skutečnosti třídy POSIXct.

### <a name="exploring-and-transforming-the-data"></a>Zkoumání a transformace dat
Podíváme se na určité proměnné v této datové sadě. Matice scatterplot je dobrý způsob, jak vytvořit rychle zkontrolovat. I mě nahrazení `str()` funkce v předchozí kód R pomocí následujícího řádku.

    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")

Spustit tento kód a zobrazit, co se stane. Vykreslení vytvořeného v portu R zařízení by měl vypadat jako obrázek 16.

![Scatterplot matice vybrané proměnné][17]

*Obrázek 16. Matice Scatterplot vybrané proměnné.*

Je některé odd-looking struktura vztahy mezi tyto proměnné. Možná to mohou nastat z trendů v datech a ze skutečnosti, že jsme nebyly standardizované proměnné.

### <a name="correlation-analysis"></a>Analýza korelace
K provedení analýzy korelace musíme zrušte trendů i standardizovat proměnné. Můžeme jednoduše použít R `scale()` funkci, která centra i škáluje proměnné. Tato funkce může také pracovat rychleji. Ale chcete zobrazit příklad Obranným programing v jazyce R.

`ts.detrend()` Provádí následující funkce obou těchto operací. Následující dva řádky kódu zrušte trendů data a pak standardizovat hodnoty.

    ts.detrend <- function(ts, Time, min.length = 3){
      ## Function to de-trend and standardize a time series

      ## Define some messages if they are NULL  
      messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                    'ERROR: ts.detrend requires argument ts to be of type numeric',
                    paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                    'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                    'ERROR: Detrend regression has failed in ts.detrend',
                    'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
      )
      # Create a vector of zeros to return as a default in some cases
      zerovec  <- rep(length(ts), 0.0)

      # The input arguments are not of the same length, return ts and quit
      if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

      # If the ts is not numeric, just return a zero vector and quit
      if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

      # If the ts is too short, just return it and quit
      if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

      ## Check that the Time variable is of class POSIXct
      if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

      ## De-trend the time series by using a linear model
      ts.frame  <- data.frame(ts = ts, Time = Time)
      tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
               error = function(e){warning(messages[5]); zerovec})

      tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
                 ts <- ts/stdev},
                error = function(e){warning(messages[6]); zerovec})

      ts
    }  
    ## Apply the detrend.ts function to the variables of interest
    df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

    ## Plot the results to look at the relationships
    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")

Je s bit situaci v `ts.detrend()` funkce. Většina tento kód program kontroluje potenciální problémy s argumenty nebo práci s výjimky, které mohou stále nastat během výpočtů. Tento kód jenom pár řádků ve skutečnosti to výpočtů.

Už jsme projednat příkladem Obranným programování v [hodnota transformace](#valuetransformations). Obě bloky výpočtu je uzavřen do `tryCatch()`. Některé chyby má smysl vrátit původní vstupní vektoru a v ostatních případech návratu vektoru nul.  

Všimněte si, že lineární regrese, použít pro vytvoření trendu zrušte regrese časové řady. Proměnná předpověď je objekt časové řady.  

Jednou `ts.detrend()` je definována jsme použít k proměnným zájem o náš dataframe. Jsme musí coerce – v rozevíracím seznamu vytvořené `lapply()` k dataframe dat pomocí `as.data.frame()`. Z důvodu Obranným aspektů `ts.detrend()`, se nezdařilo zpracovat jeden z proměnné nezabrání správné zpracování ostatní.  

Poslední řádek kódu vytvoří pairwise scatterplot. Po spuštění kódu jazyka R, jsou výsledky scatterplot uvedené v obrázek 17.

![Pairwise scatterplot zrušte trendu a standardizovaném časové řady][18]

*Obrázek 17. Pairwise scatterplot zrušte trendu a standardizovaném časové řady.*

Tyto výsledky jsou uvedeny v 16 obrázek, můžete porovnat. Trend odebrat a proměnné standardizované vidíte mnohem menší struktury vztahy mezi tyto proměnné.

Kód k výpočtu korelací jako objekty PVJS R je následující.

    ## A function to compute pairwise correlations from a
    ## list of time series value vectors
    pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
      ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
    }

    ## A list of the pairwise indices
    corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

    ## Compute the list of ccf objects
    cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

    cadairycorrelations

Spuštění tento kód vytvoří protokol znázorňuje obrázek 18.

    [ModuleOutput] Loading objects:
    [ModuleOutput]   port1
    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] [[1]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.148 0.358 0.317 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[2]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.395 -0.186 -0.238 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[3]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.059 -0.089 -0.127 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[4]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.140 0.294 0.293 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[5]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.002 -0.074 -0.124 

*Obrázek 18. Seznam PVJS objekty z pairwise korelace analýzy.*

Je hodnota korelace pro každý prodleva. Žádná z těchto hodnot korelace není dostatečně velký pro za významný. Jsme proto uzavřít, že jsme každou proměnnou modelu nezávisle.

### <a name="output-a-dataframe"></a>Výstup a dataframe
Pairwise korelací jsme mít počítá jako seznam R PVJS objektů. To představuje bit problému jako výstupním portem datové sady výsledků skutečně vyžaduje dataframe. Navíc objekt PVJS je sám seznam a chceme, aby pouze hodnoty v první prvek seznamu, korelací v různých pomalou.

Následující kód extrahuje hodnoty prodleva ze seznamu PVJS objekty, které jsou sami seznamy.

    df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

    c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
    r.names  <- c("Corr Cot Cheese - Ice Cream",
                  "Corr Cot Cheese - Milk Prod",
                  "Corr Cot Cheese - Fat Price",
                  "Corr Ice Cream - Mik Prod",
                  "Corr Ice Cream - Fat Price",
                  "Corr Milk Prod - Fat Price")

    ## Build a dataframe with the row names column and the
    ## correlation data frame and assign the column names
    outframe <- cbind(r.names, df.correlations)
    colnames(outframe) <- c.names
    outframe


    ## WARNING!
    ## The following line works only in Azure Machine Learning
    ## When running in RStudio, this code will result in an error
    #maml.mapOutputPort('outframe')

První řádek kódu je trochu složité a vysvětlení mohou pomoci porozumět. Práce zevnitř ven. máme následující:

1. '**[[**'Operátor s argumentem'**1**se vybere vektoru korelací na pomalou z první prvek seznamu PVJS objektů.
2. `do.call()` Funkce se vztahuje `rbind()` funkce přes prvky v seznamu vrátí podle `lapply()`.
3. `data.frame()` Funkce převede typ výsledku vytvořeného `do.call()` k dataframe.

Všimněte si, že názvy řádků ve sloupci dataframe. Provádění Ano uchovává řádek názvy, když se výstup z [spustit skript jazyka R][execute-r-script].

Spuštění kódu vytvoří výstup ukazuje obrázek 19 při I **vizualizovat** výstup na port datovou sadu výsledků. Názvy řádků jsou z prvního sloupce tak, jak má.

![Výstup výsledků z analýzy korelace][20]

*Obrázek 19. Výstup z analysis korelace výsledky.*

## <a id="seasonalforecasting"></a>Příklad časové řady: sezónní prognózy
Naše data je teď ve formě vhodné pro analýzu a bylo zjištěno, že neexistují žádné významné korelací mezi proměnné. Umožňuje přesunout a vytvořte časové řady modelu prognózy. Pomocí tohoto modelu jsme se prognózy produkce mléka kalifornské po dobu 12 měsíců produktu 2013.

Naše předpovědi modelu bude mít dvě součásti, součást trendu a sezónní součást. Dokončení prognózy je produkt tyto dvě součásti. Tento typ modelu se označuje jako multiplikativní modelu. Alternativou je sčítání modelu. K proměnné, které vás zajímají, což může této analýze tractable jsme již nainstalovali protokolu transformace.

Kód dokončení R pro tento oddíl je v souboru zip, které jste dříve stáhli.

### <a name="creating-the-dataframe-for-analysis"></a>Vytváření dataframe pro analýzu
Začněte přidáním **nové** [spustit skript jazyka R] [ execute-r-script] modulu experimentu. Připojení **datovou sadu výsledků** výstup existující [spustit skript jazyka R] [ execute-r-script] modulu **Dataset1** vstupní části nového modulu. Výsledek by měl vypadat podobně jako obrázek 20.

![Experiment s modulem nové spuštění skriptu jazyka R přidán][21]

*Obrázek 20. Experiment s modulem nové spuštění skriptu jazyka R přidat.*

Jako korelace analýzy, kterou jsme právě dokončili, je potřeba přidat sloupec s objektem POSIXct časové řady. Následující kód a to stejně.

    # If running in Machine Learning Studio, uncomment the first line with maml.mapInputPort()
    cadairydata <- maml.mapInputPort(1)

    ## Create a new column as a POSIXct object
    Sys.setenv(TZ = "PST8PDT")
    cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

    str(cadairydata)

Spusťte tento kód a naleznete v protokolu. Výsledek by měl vypadat jako obrázek 21.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*Obrázek 21. Přehled dataframe.*

S tímto výsledkem jsou připravení začít Naše analýzy.

### <a name="create-a-training-dataset"></a>Vytvoření datové sady školení
S dataframe sestavený musíme vytvořit datovou sadu školení. Tato data budou zahrnovat všechny připomínky kromě posledních 12 roku 2013, což je naše testovací datové sady. Následující kód podmnožin dataframe a vytvoří pozemků proměnných dojnic produkčního prostředí a ceny. Potom vytvořit pozemků čtyři produkční a cena proměnné. Anonymní funkce se používá k definování rozšiřuje pro vykreslení a pak iterace v seznamu další dva argumenty s `Map()`. Pokud přemýšlíte o, pro smyčky by mít fungovala bez problémů v tomto poli, jsou správná. Ale vzhledem k tomu, že je funkční jazyk R I mě ukazuje funkční přístup.

    cadairytrain <- cadairydata[1:216, ]

    Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
                   "Log CA Ice Cream Production, 1000s lb",
                   "Log CA Milk Production 1000s lb",
                   "Log North CA Milk Milk Fat Price per 1000 lb")

    Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)

Spuštění kódu vytvoří řadu časové řady ukazuje zeměpisný z výstupu R zařízení znázorňuje obrázek 22. Všimněte si, že časová osa je v jednotkách kalendářních dat, dobrý výhodou časové řady vykreslení metoda.

![První z řady pozemků čas kalifornské dojnic produkčního prostředí a cena dat](./media/r-quickstart/unnamed-chunk-161.png)

![Druhý čas řady pozemků kalifornské dojnic produkčního prostředí a cena dat](./media/r-quickstart/unnamed-chunk-162.png)

![Třetí čas řady pozemků kalifornské dojnic produkčního prostředí a cena dat](./media/r-quickstart/unnamed-chunk-163.png)

![Čtvrtý čas řady pozemků kalifornské dojnic produkčního prostředí a cena dat](./media/r-quickstart/unnamed-chunk-164.png)

*Obrázek 22. Čas řady pozemků produkci mléka kalifornské a cena data.*

### <a name="a-trend-model"></a>Model trendu
S vytvořili objekt řady čas a nutnosti seznámili data, Začněme vytvořit model trend kalifornské mléka provozními daty. Jsme to lze provést pomocí regrese časové řady. Je však vymazat z výkresu, který jsme bude potřebovat víc než sklon a zachycení k přesnému modelování zjištěnou trend v Cvičná data.

Zadané malém měřítku dat, bude sestavení modelu pro trend Rstudia a pak kopírování a vkládání výsledný model do Azure Machine Learning. Rstudia poskytuje interaktivní prostředí pro tento typ interaktivní analýzu.

Jako první pokus o bude proveden pokus polynomické regrese s zajišťuje až 3. Je-li skutečné riziko z přepsání hodí tyto druhy modelů. Proto je vyhýbat se nejvyšších podmínky. `I()` Funkce omezuje výklad obsah (interpretuje obsah, jako je") a umožňuje oznámena interpretovaný funkci napsat během rovnice regrese.

    milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
    summary(milk.lm)

Tím se vygeneruje následující.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12667 -0.02730  0.00236  0.02943  0.10586
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
    ## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
    ## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
    ## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0418 on 212 degrees of freedom
    ## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
    ## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16

Z hodnot P (Pr (> | t |)) v tento výstup, uvidíme, že kvadratických termín nemusí být důležité. Použiji `update()` funkce Upravit tento model umístěním kvadratických termín.

    milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
    summary(milk.lm)

Tím se vygeneruje následující.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12597 -0.02659  0.00185  0.02963  0.10696
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
    ## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
    ## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0417 on 213 degrees of freedom
    ## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
    ## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16

To vypadá lepší. Všechny podmínky jsou důležitá. Hodnota 2e-16 však je výchozí hodnota a by neměly být příliš vážně brány.  

Jako testu správností vytvoříme vykreslení řad čas kalifornské produkci mléka dat s křivku trendu vidět. Přidaný následující kód v Azure Machine Learning [spustit skript jazyka R] [ execute-r-script] modelu (ne Rstudia) pro vytvoření modelu a ujistěte se, vykreslení. Výsledek vidíte na obrázku 23.

    milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

    plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
    lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)

![Kalifornské mléka provozními daty s zobrazený model trendu](./media/r-quickstart/unnamed-chunk-18.png)

*Obrázek 23. Kalifornské mléka provozními daty s trend vzoru.*

Zdá se, trend modelu dostatečně dobře vyhovuje data. Navíc není nejspíš důkaz přečerpání vhodnosti, například v křivce modelu rychlé pohyby lichá.  

### <a name="seasonal-model"></a>Sezónní modelu
S modelem trend v dolním musíme push a zahrnují sezónní důsledky. Měsíc v roce použijeme jako proměnnou fiktivní ve model lineární k zachycení účinek po měsících. Poznámka: když zavedete Multi-Factor proměnné do modelu, nesmí počítaný intercept. Pokud není to uděláte, je přepsání zadané vzorec a R bude odstraňte jednu z požadovaného faktorů, ale ponechat termín zachycení.

Vzhledem k tomu, že máme model uspokojivé trend můžeme použít `update()` funkce pro přidání nové podmínky do existujícího modelu. -1 ve vzorci aktualizace zahodí termín zachycení. Pokračování v Rstudia momentálně:

    milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
    summary(milk.lm2)

Tím se vygeneruje následující.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.06879 -0.01693  0.00346  0.01543  0.08726
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
    ## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
    ## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
    ## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
    ## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
    ## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
    ## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
    ## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
    ## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
    ## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
    ## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
    ## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
    ## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0263 on 202 degrees of freedom
    ## Multiple R-squared:     1,    Adjusted R-squared:     1
    ## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16

Vidíte, že model už má některý výraz zachycení a má 12 měsíc důležité faktory. Toto je přesně co jsme chtěli najdete v článku.

Provedeme jiné výkresu čas řady dat produkci mléka kalifornské jak dobře funguje sezónní modelu. Přidaný následující kód v Azure Machine Learning [spustit skript jazyka R] [ execute-r-script] pro vytvoření modelu a ujistěte se, vykreslení.

    milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

    plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
    lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)

Spuštění tohoto kódu v Azure Machine Learning vytvoří výkresu znázorňuje obrázek 24.

![Produkce mléka kalifornské s modelem včetně sezónní efekty](./media/r-quickstart/unnamed-chunk-20.png)

*Obrázek 24. Produkce mléka kalifornské s modelem včetně sezónní účinky.*

Přizpůsobit data zobrazená na obrázek 24 je spíš podporovat. Vyhledejte přiměřené sezónní efekt (měsíční variace) i trend.

Jako další kontrolu na našem modelu můžeme Podíváme se na toto políčko, budou. Následující kód vypočítá předpovězené hodnoty z našich dva modely, vypočítá toto políčko, budou pro sezónní model a pak ukazuje zeměpisný tyto zbytky pro Cvičná data.

    ## Compute predictions from our models
    predict1  <- predict(milk.lm, cadairydata)
    predict2  <- predict(milk.lm2, cadairydata)

    ## Compute and plot the residuals
    residuals <- cadairydata$Milk.Prod - predict2
    plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")

Zbytkové výkresu vidíte na obrázku 25.

![Toto políčko, budou sezónní modelu pro Cvičná data](./media/r-quickstart/unnamed-chunk-21.png)

*Obrázek 25. Toto políčko, budou sezónní modelu pro Cvičná data.*

Tyto zbytky vypadat přiměřené. Neexistuje žádná konkrétní struktura, s výjimkou účinek poklesu 2008-2009, který našeho modelu neanalyzuje zvlášť dobře.

Vykreslení znázorňuje obrázek 25 je užitečné pro zjišťování v toto políčko, budou všechny vzory závislá na čase. Explicitní přístup výpočetních a vykreslení toto políčko, budou I používá umístí toto políčko, budou v pořadí časů na vykreslení. Pokud na druhé straně I měl vykreslí `milk.lm$residuals`, v pořadí časů by byly vykreslení.

Můžete také použít `plot.lm()` k vytvoření řady diagnostických pozemků.

    ## Show the diagnostic plots for the model
    plot(milk.lm2, ask = FALSE)

Tento kód vytvoří řady diagnostických pozemků znázorňuje obrázek 26.

![První diagnostiky pozemků sezónní modelu](./media/r-quickstart/unnamed-chunk-221.png)

![Druhý diagnostiky pozemků sezónní modelu](./media/r-quickstart/unnamed-chunk-222.png)

![Třetí diagnostiky pozemků sezónní modelu](./media/r-quickstart/unnamed-chunk-223.png)

![Čtvrtý diagnostiky pozemků sezónní modelu](./media/r-quickstart/unnamed-chunk-224.png)

*Obrázek 26. Diagnostika ukazuje zeměpisný sezónní modelu.*

Existuje několik vysoké míry body určené v těchto pozemků, ale nic způsobit velmi významný. Navíc můžete vidíte z výkresu normální Q-Q, toto políčko, budou se za normálních okolností distribuované důležité předpokládá pro lineární modely.

### <a name="forecasting-and-model-evaluation"></a>Vyhodnocení prognózy a modelu
Je právě jeden krok udělat, aby dokončení našem příkladu. Potřebujeme výpočetní prognózy a měřit chyba proti skutečná data. Naše prognózy bude po dobu 12 měsíců produktu 2013. Jsme můžete vypočítat k chybě měr pro Prognóza na skutečná data, která není součástí naší datové sadě školení. Kromě toho jsme můžete porovnat výkon na 18 let Cvičná data do 12 měsíců od testovacích datech.  

Počet metriky slouží k měření výkonu modelů časové řady. V našem případě použijeme chyba kořenové směrodatná (RMS). Následující funkce vypočítá chybě RMS mezi dvou řad.  

    RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
      ## Function to compute the RMS error or difference between two
      ## series or vectors

      messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                    "ERROR: Input vector to function RMS.error is too short",
                    "ERROR: Input vectors to function RMS.error must be of same length",
                    "WARNING: Funtion rms.error has received invald input time series.")

      ## Check the arguments
      if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
        warning(messages[1])
        return(NA)}

      if(length(series1) < min.length) {
        warning(messages[2])
        return(NA)}

      if((length(series1) != length(series2))) {
           warning(messages[3])
        return(NA)}

      ## If is.log is TRUE exponentiate the values, else just copy
      if(is.log) {
        tryCatch( {
          temp1 <- exp(series1)
          temp2 <- exp(series2) },
          error = function(e){warning(messages[4]); NA}
        )
      } else {
        temp1 <- series1
        temp2 <- series2
      }

     ## Compute predictions from our models
    predict1  <- predict(milk.lm, cadairydata)
    predict2  <- predict(milk.lm2, cadairydata)

    ## Compute the RMS error in a dataframe
      tryCatch( {
        sqrt(sum((temp1 - temp2)^2) / length(temp1))},
        error = function(e){warning(messages[4]); NA})
    }

Stejně jako u `log.transform()` funkce jsme popsané v části "Hodnoty transformace" je poměrně velké množství kontrolu a výjimky obnovení kód chyby v této funkci. Principy těmto nekompatibilitám jsou stejné. Práce se provádí na dvou místech uzavřen do `tryCatch()`. Nejprve časové řady jsou exponentiated, protože Pracujeme s protokoly hodnot. Druhý se počítá skutečné chybové RMS.  

Vybaven funkci k měření chybu RMS, umožňuje vytvářet a výstupní dataframe, který obsahuje chyby služby RMS. Jsme bude obsahovat podmínky pro samotné trend model a model dokončení sezónní faktory. Následující kód nemá úlohy pomocí obou lineární modelů, které jsme mít sestavený.

    ## Compute the RMS error in a dataframe
    ## Include the row names in the first column so they will
    ## appear in the output of the Execute R Script
    RMS.df  <-  data.frame(
    rowNames = c("Trend Model", "Seasonal Model"),
      Traing = c(
      RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
      RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
      Forecast = c(
        RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
        RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
    )
    RMS.df

    ## The following line should be executed only when running in
    ## Azure Machine Learning Studio
    maml.mapOutputPort('RMS.df')

Spuštění tento kód vytvoří výstup ukazuje obrázek 27 na výstupní port datovou sadu výsledků.

![Porovnání RMS chyb pro modely][26]

*Obrázek 27. Porovnání RMS chyb pro modely.*

Z těchto výsledků vidíte, že přidávání sezónní faktory do modelu snižuje chyby RMS výrazně. Chyba služby RMS pro Cvičná data příliš logicky je chvíli menší než prognózy.

## <a id="appendixa"></a>Příloha A: Průvodce Rstudia
Rstudia je velmi dobře zdokumentovat, tak v tomto dodatku I zajistí některé odkazy na klíče části dokumentace Rstudia, které vám pomůžou začít.

1. Vytváření projektů
   
   Můžete uspořádat a spravovat váš kód R do projektů pomocí Rstudia. Dokumentace, která používá projekty najdete na https://support.rstudio.com/hc/articles/200526207-Using-Projects.
   
   Doporučujeme I postupujte podle těchto pokynů a vytvoření projektu pro R příklady kódu v tomto dokumentu.  
2. Úpravy a spouštění kódu jazyka R
   
   Rstudia poskytuje integrované prostředí pro úpravy a provádění kódu jazyka R. Dokumentace naleznete na https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code.
3. Ladění
   
   Rstudia zahrnuje výkonné možnosti ladění. Dokumentace pro tyto funkce je v https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio.
   
   Řešení potíží funkce zarážek jsou popsány v https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting.

## <a id="appendixb"></a>Příloha B: Další čtení
V tomto kurzu programovací R popisuje základy toho, co je potřeba použít jazyk R s Azure Machine Learning Studio. Pokud nejste obeznámeni s R, jsou k dispozici na CRAN dva úvodní informace:

* R pro začátečníky podle Emmanuel Paradis je vhodná pro spuštění na http://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf.  
* Úvod do R n. dokončeno Venables et. al. Klient se přepne do hloubky o něco víc, v http://cran.r-project.org/doc/manuals/R-intro.html.

Neexistují mnoho knih na R, který můžete začít pracovat. Zde najdete několik, které užitečné:

* Obrázky R programování: prohlídka z statistické softwaru návrh podle Norman Matloff je vynikající Úvod do programování v jazyce R.  
* R kuchařka podle Paul Teetor poskytuje přístup problému a řešení pomocí R.  
* R v akce Robert Kabacoff je další užitečné úvodní adresáře. Stránku rychlý R doprovodné je užitečné prostředků v http://www.statmethods.net/.
* R Inferno podle Patrik popáleniny je překvapivě vážný adresáře, která pracuje s počtem složité a obtížně témata, která může být zjistil při programování v jazyce R. Seznamu je k dispozici zdarma na http://www.burns-stat.com/documents/books/the-r-inferno/.
* Pokud chcete podrobné informace do Pokročilá témata v R, podívejte se na seznam upřesnit R podle Hadley Wickham. Online verze této příručky je k dispozici zdarma na http://adv-r.had.co.nz/.

Katalog R časové řady balíčků naleznete v zobrazení úlohy CRAN pro analýzu časových řad: http://cran.r-project.org/web/views/TimeSeries.html. Informace o určité časové řady objektu balíčky by měl naleznete v dokumentaci pro tento balíček.

Příručka úvodní časové řady s R Paul Cowpertwait a Andrew Metcalfe obsahuje úvod do používání R pro analýzu časových řad. Mnoho více teoretické texty R příklady.

Některé skvělé prostředků z Internetu:

* DataCamp: DataCamp učí R v pohodlí prohlížeč s video lekce a kódování cvičení. Existují interaktivní kurzy o nejnovější techniky R a balíčků. Trvat volné Interaktivní kurz R na https://www.datacamp.com/courses/introduction-to-r
* Spuštění Průvodce Začínáme s R z Programiz https://www.programiz.com/r-programming
* Rychlý kurz R podle Jan černé z Clarkson univerzity http://www.cyclismo.org/tutorial/R/
* 60 + R prostředky uvedené v http://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html

<!--Image references-->
[1]: ./media/r-quickstart/fig1.png
[2]: ./media/r-quickstart/fig2.png
[3]: ./media/r-quickstart/fig3.png
[4]: ./media/r-quickstart/fig4.png
[5]: ./media/r-quickstart/fig5.png
[6]: ./media/r-quickstart/fig6.png
[7]: ./media/r-quickstart/fig7.png
[8]: ./media/r-quickstart/fig8.png
[9]: ./media/r-quickstart/fig9.png
[10]: ./media/r-quickstart/fig10.png
[11]: ./media/r-quickstart/fig11.png
[12]: ./media/r-quickstart/fig12.png
[13]: ./media/r-quickstart/fig13.png
[14]: ./media/r-quickstart/fig14.png
[15]: ./media/r-quickstart/fig15.png
[16]: ./media/r-quickstart/fig16.png
[17]: ./media/r-quickstart/fig17.png
[18]: ./media/r-quickstart/fig18.png
[19]: ./media/r-quickstart/fig19.png
[20]: ./media/r-quickstart/fig20.png
[21]: ./media/r-quickstart/fig21.png
[22]: ./media/r-quickstart/fig22.png

[26]: ./media/r-quickstart/fig26.png

<!--links-->
[appendixa]: #appendixa
[download]: https://azurebigdatatutorials.blob.core.windows.net/rquickstart/RFiles.zip


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
