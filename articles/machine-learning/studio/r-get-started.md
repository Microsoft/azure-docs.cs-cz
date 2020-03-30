---
title: Začínáme s R
titleSuffix: ML Studio (classic) - Azure
description: Pomocí tohoto kurzu programování jazyka R můžete začít používat jazyk R s Azure Machine Learning Studio (klasické) k vytvoření řešení prognózy.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: 1b347707b3c656bd692a29f0fd748c1503be4fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217996"
---
# <a name="getting-started-with-the-r-programming-language-in-azure-machine-learning-studio-classic"></a>Začínáme s programovacím jazykem R v Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

<!-- Stephen F Elston, Ph.D. -->

## <a name="introduction"></a>Úvod

Tento kurz vám pomůže začít rozšiřovat Azure Machine Learning Studio (klasické) pomocí programovacího jazyka R. Postupujte podle tohoto r programování kurz vytvořit, otestovat a spustit kód R v rámci studio (klasické). Při práci prostřednictvím kurzu vytvoříte kompletní řešení prognózy pomocí jazyka R ve studiu (klasické).  

Azure Machine Learning Studio (klasické) obsahuje mnoho výkonných modulů strojového učení a manipulace s daty. Silný jazyk R byl popsán jako lingua franca analytiky. Naštěstí, analýzy a manipulaci s daty ve Studiu (klasické) lze rozšířit pomocí R. Tato kombinace poskytuje škálovatelnost a snadné nasazení studia Studio (klasické) s flexibilitou a hloubkovou analýzou R.

### <a name="forecasting-and-the-dataset"></a>Prognózování a datová sada

Prognózování je široce používaná a docela užitečná analytická metoda. Běžné použití se pohybuje od předpovídání prodeje sezónních položek, určování optimálních úrovní zásob až po předpovídání makroekonomických proměnných. Prognózování se obvykle provádí pomocí modelů časových řad.

Data časových řad jsou data, ve kterých mají hodnoty časový index. Časový index může být pravidelný, například každý měsíc nebo každou minutu, nebo nepravidelný. Model časové řady je založen na datech časových řad. Programovací jazyk R obsahuje flexibilní rámec a rozsáhlou analýzu dat časových řad.

V této příručce budeme pracovat s kalifornskými údaji o produkci mléka a cenách. Tyto údaje zahrnují měsíční informace o výrobě několika mléčných výrobků a ceně mléčného tuku, což je referenční komodita.

Data použitá v tomto článku, spolu se skripty R, lze stáhnout z [MachineLearningSamples-Notebooks/studio-ukázky](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples). Data v `cadairydata.csv` souboru byla původně syntetizována z [https://dairymarkets.com](https://dairymarkets.com)informací dostupných z University of Wisconsin at .

### <a name="organization"></a>Organizace

Budeme postupovat prostřednictvím několika kroky, jak se dozvíte, jak vytvořit, otestovat a spustit analýzy a manipulaci s daty R kód v prostředí Azure Machine Learning Studio (klasické) prostředí.  

* Nejprve se podíváme na základy používání jazyka R v prostředí Azure Machine Learning Studio (klasické).
* Pak se budeme diskutovat o různých aspektech vstupně-v pro data, R kód a grafiku v prostředí Azure Machine Learning Studio (klasické).
* Poté vytvoříme první část našeho řešení prognóz vytvořením kódu pro čištění a transformaci dat.
* S připravenými daty provedeme analýzu korelace mezi několika proměnnými v naší datové sadě.
* Nakonec vytvoříme sezónní model předpovídání časových řad pro výrobu mléka.

## <a name="interact-with-r-language-in-machine-learning-studio-classic"></a><a id="mlstudio"></a>Interakce s jazykem R ve studiu strojového učení (klasika)

Tato část vás provede některými základy interakce s programovacím jazykem R v klasickém prostředí Machine Learning Studio. Jazyk R poskytuje výkonný nástroj pro vytváření přizpůsobených modulů analýzy a manipulace s daty v prostředí Azure Machine Learning Studio (klasické).

Budu používat RStudio vyvíjet, testovat a ladit R kód v malém měřítku. Tento kód se pak vyjme a vloží do modulu [Spouštět skript R][execute-r-script] připravený ke spuštění v Azure Machine Learning Studio (klasické).  

### <a name="the-execute-r-script-module"></a>Modul Spustit skript R

V rámci Machine Learning Studio (klasické), R skripty jsou spouštěny v rámci [modulu Spustit Skript R.][execute-r-script] Příklad modulu [Execute R Script][execute-r-script] v Machine Learning Studio (klasické) je znázorněn na obrázku 1.

 ![Programovací jazyk R: Modul Execute R Script vybraný v Machine Learning Studiu (klasický)](./media/r-quickstart/fig1.png)

*Obrázek 1. Prostředí Machine Learning Studio (klasické) zobrazující vybraný modul Spouštění jazyka R.*

S odkazem na obrázek 1 se podívejme na některé klíčové části prostředí Machine Learning Studio (klasické) pro práci s modulem [Execute R Script.][execute-r-script]

* Moduly v experimentu jsou zobrazeny v prostředním podokně.
* Horní část pravého podokna obsahuje okno pro zobrazení a úpravu skriptů R.  
* V dolní části pravého podokna jsou uvedeny některé vlastnosti [skriptu Execute R][execute-r-script]. Chybové a výstupní protokoly můžete zobrazit výběrem příslušných bodů tohoto podokna.

Budeme samozřejmě diskutovat o [spuštění Skriptu R][execute-r-script] podrobněji ve zbytku tohoto článku.

Při práci se složitými funkcemi R doporučuji upravit, testovat a ladit v RStudiu. Stejně jako u jakékoli vývoj softwaru, rozšířit kód postupně a otestovat na malé jednoduché testovacích případů. Poté vyjměte a vložte funkce do okna skriptu R modulu [Spustit skript R.][execute-r-script] Tento přístup umožňuje využít integrované vývojové prostředí RStudio (IDE) a výkon Azure Machine Learning Studio (klasické).  

#### <a name="execute-r-code"></a>Spustit kód R

Jakýkoli kód R v modulu [Spustit skript R][execute-r-script] se spustí při spuštění experimentu výběrem tlačítka **Spustit.** Po dokončení spuštění se na ikoně [Skript Execute R][execute-r-script] zobrazí zaškrtnutí.

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Defenzivní r kódování pro Azure Machine Learning

Pokud vyvíjíte r kód pro, řekněme, webové služby pomocí Azure Machine Learning Studio (klasické), měli byste určitě naplánovat, jak váš kód bude zacházet s neočekávaným vstupem dat a výjimky. Chcete-li zachovat jasnost, jsem nezahrnul mnoho ve způsobu kontroly nebo zpracování výjimek ve většině příkladů kódu zobrazených. Nicméně, jak budeme pokračovat, dám vám několik příkladů funkcí pomocí R je zpracování výjimek schopnosti.  

Pokud potřebujete úplnější zpracování r výjimky manipulace, doporučuji si přečíst příslušné části knihy Wickham uvedeny níže v [další čtení](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Ladění a test R v Machine Learning Studiu (klasika)

Chcete-li zopakovat, doporučuji vám vyzkoušet a ladit r kód v malém měřítku v RStudio. Existují však případy, kdy budete muset vysledovat problémy r kód v [samotném spuštění skriptu R.][execute-r-script] Kromě toho je vhodné zkontrolovat výsledky v Machine Learning Studio (klasické).

Výstup z provádění kódu R a na platformě Azure Machine Learning Studio (klasické) se nachází především v output.log. Některé další informace budou vidět v error.log.  

Pokud dojde k chybě v Machine Learning Studio (klasické) při spuštění kódu R, první postup by měl být podívat se na error.log. Tento soubor může obsahovat užitečné chybové zprávy, které vám pomohou pochopit a opravit chybu. Chcete-li zobrazit soubor error.log, vyberte **možnost Zobrazit protokol chyb** v **podokně vlastností** [skriptu Spustit r][execute-r-script] obsahující chybu.

Například jsem spustil následující kód R s nedefinovanou proměnnou y v modulu [Execute R Script:][execute-r-script]

```R
x <- 1.0
z <- x + y
```

Tento kód se nezdaří spustit, výsledkem je chybový stav. Když v **podokně vlastností** **vyberete protokol chyb zobrazení,** zobrazí se zobrazení znázorněné na obrázku 2.

  ![Vyskakovací okno s chybovou zprávou](./media/r-quickstart/fig2.png)

*Obrázek 2. Automaticky otevíraná zpráva s chybovou zprávou.*

Vypadá to, že musíme podívat do output.log vidět chybovou zprávu R. Vyberte [skript Spustit R][execute-r-script] a pak v **podokně vlastností** vpravo vyberte položku **View output.log.** Otevře se nové okno prohlížeče a zobrazí se následující.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Tato chybová zpráva neobsahuje žádná překvapení a jasně identifikuje problém.

Chcete-li zkontrolovat hodnotu libovolného objektu v R, můžete vytisknout tyto hodnoty do souboru output.log. Pravidla pro zkoumání hodnot objektů jsou v podstatě stejné jako v interaktivní relaci R. Pokud například zadáte název proměnné na řádku, bude hodnota objektu vytištěna do souboru output.log.  

#### <a name="packages-in-machine-learning-studio-classic"></a>Balíčky ve studiu strojového učení (klasické)

Studio je dodáváno s více než 350 předinstalovanými jazykovými balíčky R. Následující kód v modulu [Spustit skript R][execute-r-script] můžete použít k načtení seznamu předinstalovaných balíčků.

```R
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Pokud v tuto chvíli nerozumíte poslednímu řádku tohoto kódu, čtěte dál. Ve zbytku tohoto článku budeme rozsáhle diskutovat o použití R ve studiu (klasické) prostředí.

### <a name="introduction-to-rstudio"></a>Úvod do RStudia

RStudio je široce používané IDE pro R. Budu používat RStudio pro editaci, testování a ladění některé kód R použité v této příručce. Jakmile je kód R testován a připraven, můžete jednoduše vyjmout a vložit z editoru RStudio do modulu Machine Learning Studio (klasický) [Spustit skript R.][execute-r-script]  

Pokud nemáte programovací jazyk R nainstalovaný na vašem stolním počítači, doporučuji vám tak učinit nyní. Bezplatné stažení jazyka R s otevřeným zdrojovým kódem jsou k [https://www.r-project.org/](https://www.r-project.org/)dispozici na adrese Comprehensive R Archive Network (CRAN) na adrese . K dispozici jsou soubory ke stažení pro Windows, Mac OS a Linux/UNIX. Vyberte si blízké zrcadlo a postupujte podle pokynů ke stažení. Kromě toho CRAN obsahuje nepřeberné množství užitečných analytických balíčků a balíčků pro manipulaci s daty.

Pokud s RStudio teče, měli byste si stáhnout a nainstalovat verzi pro stolní počítače. Soubory ke stažení rstudio pro Windows, Mac OS a http://www.rstudio.com/products/RStudio/Linux/UNIX najdete na adrese . Postupujte podle pokynů pro instalaci RStudio na stolním počítači.  

Výukový program úvod do RStudio je k dispozici na [použití RStudio IDE](https://support.rstudio.com/hc/sections/200107586-Using-RStudio).

Poskytnu některé další informace o používání RStudio v [průvodci dokumentací RStudio](#appendixa) níže.  

## <a name="get-data-in-and-out-of-the-execute-r-script-module"></a><a id="scriptmodule"></a>Získání dat do a z modulu Spustit skript R

V této části budeme diskutovat o tom, jak získat data do a z [modulu Spustit Skript R.][execute-r-script] Zkontrolujeme, jak zpracovat různé datové typy, které jsou čteny do a z modulu [Execute R Script.][execute-r-script]

Kompletní kód pro tuto část je v [MachineLearningSamples-Notebooks/studio-ukázky](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="load-and-check-data-in-machine-learning-studio-classic"></a>Načítání a kontrola dat ve studiu Machine Learning Studio (klasické)

#### <a name="load-the-dataset"></a><a id="loading"></a>Načtení datové sady

Začneme načtením souboru **csdairydata.csv** do Azure Machine Learning Studio (klasické).

1. Spusťte prostředí Azure Machine Learning Studio (klasické).
1. V levém dolním rohu obrazovky vyberte **+ NOVÝ** a vyberte **Datová sada**.
1. Vyberte **Z místního souboru**a pak **Procházet** vyberte soubor.
1. Ujistěte se, že jste jako typ datové sady vybrali **obecný soubor CSV s hlavičkou (.csv).**
1. Zaškrtněte políčko zaškrtnutí.
1. Po nahrání datové sady byste měli novou datovou sadu zobrazit výběrem karty **Datové sady.**  

#### <a name="create-an-experiment"></a>Vytvoření experimentu

Teď, když máme nějaká data v Machine Learning Studio (klasické), musíme vytvořit experiment pro analýzu.  

1. V levém dolním rohu vyberte **+ NOVÝ** a vyberte **Experimentovat**, pak **Prázdný experiment**.
1. Experiment můžete pojmenovat tak, že vyberete a upravíte **experiment vytvořený na ...** názvu v horní části stránky. Například změna na **CA Dairy Analysis**.
1. Na levé straně stránky experimentu rozbalte **položku Uložené datové sady**a potom **položky Moje datové sady**. Měli byste vidět **cadairydata.csv,** které jste nahráli dříve.
1. Přetáhněte **datovou sadu csdairydata.csv** do experimentu.
1. Do pole **Hledat položky experimentu** v horní části levého podokna zadejte [příkaz Execute R Script][execute-r-script]. Zobrazí se modul v seznamu hledání.
1. Přetáhněte modul [Spustit skript R][execute-r-script] na paletu.  
1. Připojte výstup **datové sady csdairydata.csv** k levému vstupu (**Dataset1**) [skriptu Execute R][execute-r-script].
1. **Nezapomeňte vybrat 'Uložit'!**  

V tomto okamžiku by váš experiment měl vypadat podobně jako obrázek 3.

![Ca Dairy Analysis experimentuje s datovou sadou a modulem Execute R Script](./media/r-quickstart/fig3.png)

*Obrázek 3. Analýza mléka a mléčných výrobků CA experimentuje s datovou sadou a modulem Execute R Script.*

#### <a name="check-on-the-data"></a>Kontrola dat

Podívejme se na data, která jsme načetli do našeho experimentu. V experimentu vyberte výstup **datové sady cadairydata.csv** a vyberte **vizualizovat**. Měli byste vidět něco jako obrázek 4.  

![Souhrn datové sady cadairydata.csv](./media/r-quickstart/fig4.png)

*Obrázek 4. Souhrn datové sady cadairydata.csv.*

V tomto pohledu vidíme mnoho užitečných informací. Můžeme vidět prvních několik řádků této datové sady. Pokud vybereme sloupec, zobrazí se v části Statistika další informace o tomto sloupci. Například řádek Typ funkce nám ukazuje, jaké datové typy Azure Machine Learning Studio (klasické) přiřazené ke sloupci. Mít rychlý vzhled, jako je to je dobrá kontrola příčetnosti, než začneme dělat nějakou vážnou práci.

### <a name="first-r-script"></a>První skript R

Pojďme vytvořit jednoduchý první Skript R experimentovat v rámci Azure Machine Learning Studio (klasické). Vytvořil jsem a testoval následující skript v RStudiu.  

```R
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Teď musím převést tento skript do Azure Machine Learning Studio (klasické). Mohl bych jednoduše vyjmout a vložit. Nicméně, v tomto případě, budu přenášet můj R skript přes zip soubor.

### <a name="data-input-to-the-execute-r-script-module"></a>Vstup dat do modulu Spustit skript R

Podívejme se na vstupy do modulu [Execute R Script.][execute-r-script] V tomto příkladu budeme číst kalifornská data mléka do modulu [Execute R Script.][execute-r-script]  

Existují tři možné vstupy pro modul [Execute R Script.][execute-r-script] V závislosti na aplikaci můžete použít některý z těchto vstupů. Je také naprosto rozumné použít skript R, který nepřijímá žádný vstup vůbec.  

Podívejme se na každý z těchto vstupů, jít zleva doprava. Názvy jednotlivých vstupů můžete zobrazit umístěním kurzoru nad vstup a čtením popisku.  

#### <a name="script-bundle"></a>Sada skriptů

Vstup sady Script Bundle umožňuje předat obsah souboru zip do modulu [Spustit skript R.][execute-r-script] Můžete použít jeden z následujících příkazů ke čtení obsahu souboru ZIP do kódu R.

```R
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Azure Machine Learning Studio (klasické) zachází se soubory v zip, jako kdyby jsou v adresáři src/, takže je potřeba předponu názvy souborů s tímto názvem adresáře. Pokud například zip obsahuje `yourfile.R` soubory `yourData.rdata` a v kořenovém adresáři `src/yourfile.R` zip, budete je adresovat jako a `src/yourData.rdata` při použití `source` a `load`.

Už jsme diskutovali načítání datových sad v [Načíst datovou sadu](#loading). Jakmile vytvoříte a otestujete skript R zobrazený v předchozí části, postupujte takto:

1. Uložte skript R do . R soubor. Říkám můj soubor skriptu "simpleplot. R". Tady je obsah.

   ```R
   ## Only one of the following two lines should be used
   ## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
   ## If in RStudio, use the second line with read.csv()
   cadairydata <- maml.mapInputPort(1)
   # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
   str(cadairydata)
   pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
   ## The following line should be executed only when running in
   ## Azure Machine Learning Studio (classic)
   maml.mapOutputPort('cadairydata')
   ```

1. Vytvořte soubor zip a zkopírujte skript do tohoto souboru zip. V systému Windows můžete klepnout pravým tlačítkem myši na soubor a vybrat **možnost Odeslat do**a potom **komprimovovat složku**. Tím se vytvoří nový zip soubor obsahující "simpleplot. R" soubor.

1. Přidejte soubor do **datových sad** v Azure Machine Learning Studio (klasické), určující typ jako **zip**. Nyní byste měli vidět soubor zip v datových sadách.

1. Přetáhněte soubor ZIP z **datových sad** na **plátno ML Studio (klasické)**.

1. Připojte výstup ikony **zip data** ke vstupu **skriptovacího svazku** modulu [Spustit skript R.][execute-r-script]

1. Do `source()` okna kódu modulu [Execute R Script][execute-r-script] zadejte funkci s názvem souboru ZIP. V mém případě `source("src/simpleplot.R")`jsem napsal .  

1. Ujistěte se, že jste vybrali **uložit**.

Po dokončení těchto kroků spustí modul [Skript U Spuštění jazyka R][execute-r-script] skript u souboru ZIP při spuštění experimentu. V tomto okamžiku by váš experiment měl vypadat podobně jako obrázek 5.

![Experimentování pomocí skriptu R se zipem](./media/r-quickstart/fig6.png)

*Obrázek 5. Experimentovat pomocí zip R skriptu.*

#### <a name="dataset1"></a>Datový soubor1

Obdélníkovou tabulku dat můžete předat kódu R pomocí vstupu Dataset1. V našem jednoduchém skriptu `maml.mapInputPort(1)` funkce čte data z portu 1. Tato data jsou pak přiřazena k názvu proměnné datového rámce v kódu. V našem jednoduchém skriptu provádí zadání první řádek kódu.

```R
cadairydata <- maml.mapInputPort(1)
```

Experiment spusťte výběrem tlačítka **Spustit.** Po dokončení spuštění vyberte modul [Spustit skript R][execute-r-script] a pak v yberte Zobrazit výstupní **protokol** v podokně vlastností. V prohlížeči by se měla zobrazit nová stránka s obsahem souboru output.log. Když se posunete dolů, měli byste vidět něco jako následující.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Dále na stránce jsou podrobnější informace o sloupcích, které budou vypadat podobně jako následující.

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

Tyto výsledky jsou většinou podle očekávání, s 228 pozorování a 9 sloupců v datovém rámci. Můžeme vidět názvy sloupců, datový typ R a ukázku každého sloupce.

> [!NOTE]
> Stejný tištěný výstup je pohodlně dostupný z výstupu zařízení R modulu [Execute R Script.][execute-r-script] Výstupy modulu Execute [R Script][execute-r-script] budeme diskutovat v další části.  

#### <a name="dataset2"></a>Datový soubor2

Chování Dataset2 vstup je shodný s Dataset1. Pomocí tohoto vstupu můžete předat druhou obdélníkovou tabulku dat do kódu R. Funkce `maml.mapInputPort(2)`s argumentem 2 se používá k předání těchto dat.  

### <a name="execute-r-script-outputs"></a>Spuštění výstupů skriptu R

#### <a name="output-a-dataframe"></a>Výstup datového rámce

Pomocí funkce můžete výstup obsahu datového rámce R jako obdélníkové tabulky prostřednictvím portu `maml.mapOutputPort()` Result Dataset1. V našem jednoduchém skriptu R to provádí následující řádek.

```
maml.mapOutputPort('cadairydata')
```

Po spuštění experimentu vyberte výstupní port Result Dataset1 a pak vyberte **Visualize**. Měli byste vidět něco jako obrázek 6.

![Vizualizace výstupu kalifornských mléčných dat](./media/r-quickstart/fig7.png)

*Obrázek 6. Vizualizace výstupu kalifornských mléčných dat.*

Tento výstup vypadá totožný se vstupem, přesně tak, jak jsme očekávali.  

### <a name="r-device-output"></a>R Výstup zařízení

Výstup zařízení modulu [Execute R Script][execute-r-script] obsahuje zprávy a grafický výstup. Standardní výstupní i standardní chybové zprávy z R jsou odesílány do výstupního portu zařízení R.  

Chcete-li zobrazit výstup zařízení R, vyberte port a potom v **aplikaci Visualize**. Vidíme standardní výstup a standardní chybu ze skriptu R na obrázku 7.

![Standardní výstup a standardní chyba z portu zařízení R](./media/r-quickstart/fig8.png)

*Obrázek 7. Standardní výstup a standardní chyba z portu zařízení R.*

Posouváním dolů vidíme grafický výstup z našeho skriptu R na obrázku 8.  

![Grafický výstup z portu zařízení R](./media/r-quickstart/fig9.png)

*Obrázek 8. Grafický výstup z portu zařízení R.*  

## <a name="data-filtering-and-transformation"></a><a id="filtering"></a>Filtrování a transformace dat

V této části provedeme některé základní operace filtrování a transformace dat na kalifornských mléčných datech. Do konce této části budeme mít data ve formátu vhodném pro sestavení analytického modelu.  

Přesněji řečeno, v této části provedeme několik běžných úloh čištění a transformace dat: transformace typu, filtrování datových rámců, přidávání nových vypočítaných sloupců a transformace hodnot. Toto pozadí by vám mělo pomoci vypořádat se s mnoha variantami, se kterými se setkáváte v reálných problémech.

Kompletní kód R pro tuto část je k dispozici v [MachineLearningSamples-Notebooks/studio-ukázky](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="type-transformations"></a>Transformace typů

Nyní, když můžeme číst kalifornská data mléka do kódu R v modulu [Execute R Script,][execute-r-script] musíme zajistit, aby data ve sloupcích měla zamýšlený typ a formát.  

R je dynamicky zadaný jazyk, což znamená, že datové typy jsou vykonavovány z jednoho do druhého podle potřeby. Atomické datové typy v R zahrnují číselné, logické a znakové. Typ faktoru se používá k kompaktnímu ukládání kategorických dat. Mnohem více informací o datových typech naleznete v odkazech v [části Další čtení](#appendixb) níže.

Při tabulkových dat je číst do R z externího zdroje, je vždy vhodné zkontrolovat výsledné typy ve sloupcích. Můžete chtít sloupec typu znaku, ale v mnoha případech se to zobrazí jako faktor nebo naopak. V ostatních případech je sloupec, o kterých si myslíte, že by měl být číselný, reprezentován údaji o znaku, například "1.23" spíše než 1.23 jako číslo s plovoucí desetinnou čárkou.  

Naštěstí je snadné převést jeden typ na jiný, pokud je možné mapování. Například nelze převést 'Nevada' na číselnou hodnotu, ale můžete převést na faktor (kategorická proměnná). Jako další příklad můžete převést číselnou hodnotu 1 na znak 1 nebo faktor.  

Syntaxe pro některý z těchto `as.datatype()`převodů je jednoduchá: . Tyto funkce převodu typu zahrnují následující.

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Při pohledu na datové typy sloupců, které jsme zadali v předchozí části: všechny sloupce jsou typu číselné, s výjimkou sloupce označeného "Měsíc", který je typu znaků. Převedeme to na faktor a otestujeme výsledky.  

Vymazal jsem řádek, který vytvořil matici scatterplot a přidal řádek převádí 'Měsíc' sloupec faktor. V mém experimentu budu jen vyjmout a vložit kód R do okna kódu [spustit R Script][execute-r-script] Module. Můžete také aktualizovat soubor zip a nahrát jej do Azure Machine Learning Studio (klasické), ale to trvá několik kroků.  

```R
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Pojďme spustit tento kód a podívejte se na výstupní protokol pro skript R. Příslušné údaje z protokolu jsou znázorněny na obrázku 9.

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

*Obrázek 9. Souhrn datového rámce s proměnnou faktoru.*

Typ pro měsíc by měl nyní říci '**Faktor w / 14 úrovní**'. To je problém, protože v roce je pouze 12 měsíců. Můžete také zkontrolovat, zda je typ v **aplikaci Visualize** portu Dataset výsledků "**Kategorický**".

Problém je v tom, že sloupec "Měsíc" nebyl systematicky kódován. V některých případech měsíc se nazývá duben a v jiných je zkrácena jako duben. Tento problém můžeme vyřešit oříznutím řetězce na 3 znaky. Řádek kódu nyní vypadá takto:

```R
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Znovu spusťte experiment a zobrazte výstupní protokol. Očekávané výsledky jsou uvedeny na obrázku 10.  

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

*Obrázek 10. Souhrn datového rámce se správným počtem úrovní faktorů.*

Naše faktorová proměnná má nyní požadovaných 12 úrovní.

### <a name="basic-data-frame-filtering"></a>Základní filtrování datových rámů

Datové rámce R podporují výkonné možnosti filtrování. Datové sady lze podmnožinou pomocí logických filtrů na řádcích nebo sloupcích. V mnoha případech budou vyžadována komplexní kritéria filtru. Odkazy v [dalším čtení](#appendixb) níže obsahují rozsáhlé příklady filtrování datových rámců.  

Existuje jeden kousek filtrování bychom měli udělat na naší datové sady. Pokud se podíváte na sloupce v datovém rámci cadairydata, zobrazí se dva nepotřebné sloupce. První sloupec obsahuje pouze číslo řádku, což není příliš užitečné. Druhý sloupec Year.Month obsahuje nadbytečné informace. Tyto sloupce můžeme snadno vyloučit pomocí následujícího kódu R.

> [!NOTE]
> Od této chvíle v této sekci vám ukážu další kód, který přidávám do modulu [Execute R Script.][execute-r-script] Přidám každý nový **before** řádek `str()` před funkci. Tuto funkci používám k ověření výsledků ve službě Azure Machine Learning Studio (klasické).

Následující řádek přidám do kódu R v modulu [Execute R Script.][execute-r-script]

```R
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Spusťte tento kód v experimentu a zkontrolujte výsledek z výstupního protokolu. Tyto výsledky jsou znázorněny na obrázku 11.

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

*Obrázek 11. Souhrn datového rámce s odstraněnými dvěma sloupci.*

Máme pro vás dobré zprávy! Dostáváme očekávané výsledky.

### <a name="add-a-new-column"></a>Přidání nového sloupce

Chcete-li vytvořit modely časových řad, bude vhodné mít sloupec obsahující měsíce od začátku časové řady. Vytvoříme nový sloupec Month.Count.

Chcete-li pomoci organizovat kód vytvoříme `num.month()`naši první jednoduchou funkci, . Tuto funkci pak použijeme k vytvoření nového sloupce v datovém rámci. Nový kód je následující.

```R
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
```

Nyní spusťte aktualizovaný experiment a pomocí výstupního protokolu zobrazte výsledky. Tyto výsledky jsou znázorněny na obrázku 12.

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

*Obrázek 12. Souhrn datového rámce s dalším sloupcem.*

Vypadá to, že všechno funguje. Máme nový sloupec s očekávanými hodnotami v našem datovém rámci.

### <a name="value-transformations"></a>Transformace hodnot

V této části provedeme několik jednoduchých transformací hodnot v některých sloupcích našeho datového rámce. Jazyk R podporuje téměř libovolné transformace hodnoty. Odkazy v [dalším čtení](#appendixb) níže obsahují rozsáhlé příklady.

Pokud se podíváte na hodnoty v souhrnech našeho datového rámce, měli byste zde vidět něco zvláštního. Je více zmrzliny než mléka vyrobeného v Kalifornii? Ne, samozřejmě, že ne, protože to nedává smysl, smutné, protože tato skutečnost může být pro některé z nás milovníky zmrzliny. Jednotky jsou jiné. Cena je v jednotkách amerických liber, mléko je v jednotkách 1 M amerických liber, zmrzlina je v jednotkách 1000 amerických galonů a tvaroh je v jednotkách 1000 amerických liber. Za předpokladu, že zmrzlina váží asi 6,5 liber na galon, můžeme snadno udělat násobení převést tyto hodnoty, takže jsou všechny ve stejných jednotkách 1000 liber.

Pro náš prognostický model používáme multiplikativní model pro trendové a sezónní úpravy těchto dat. Transformace protokolu nám umožňuje použít lineární model, což tento proces zjednodušuje. Můžeme použít transformaci protokolu ve stejné funkci, kde je aplikován multiplikátor.

V následujícím kódu definuji novou `log.transform()`funkci a použiji ji na řádky obsahující číselné hodnoty. Funkce `Map()` R se používá `log.transform()` k aplikování funkce na vybrané sloupce datového rámce. `Map()`je podobný, `apply()` ale umožňuje více než jeden seznam argumentů funkce. Všimněte si, že seznam multiplikátorů poskytuje druhý argument `log.transform()` funkce. Funkce `na.omit()` se používá jako trochu vyčištění, aby chomáč nemáme chybějící nebo nedefinované hodnoty v datovém rámci.

```R
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
```

Tam je docela dost `log.transform()` děje ve funkci. Většina tohoto kódu je kontrola potenciální problémy s argumenty nebo řešení s výjimkami, které mohou stále vzniknout během výpočtů. Pouze několik řádků tohoto kódu skutečně provést výpočty.

Cílem obranného programování je zabránit selhání jediné funkce, která brání pokračování zpracování. Náhlé selhání dlouhotrvající analýzy může být pro uživatele poměrně frustrující. Chcete-li se této situaci vyhnout, musí být vybrány výchozí vrácené hodnoty, které omezí poškození následného zpracování. Zpráva je také vytvořena upozornit uživatele, že se něco pokazilo.

Pokud nejste zvyklí na obranné programování v R, může se tento kód zdát trochu ohromující. Doprovodím vás hlavními kroky:

1. Je definován vektor čtyř zpráv. Tyto zprávy slouží ke sdělování informací o některé možné chyby a výjimky, které mohou nastat s tímto kódem.
2. Vracím hodnotu NA pro každý případ. Existuje mnoho dalších možností, které by mohly mít méně vedlejších účinků. Mohl bych vrátit vektor nul, nebo původní vstupní vektor, například.
3. Kontroly jsou spuštěny na argumenty funkce. V každém případě, pokud je zjištěna chyba, je vrácena `warning()` výchozí hodnota a funkce je vytvořena zpráva. Já používám `warning()` spíše `stop()` než jako ten bude ukončena exekuce, přesně to, co se snažím vyhnout. Všimněte si, že jsem napsal tento kód v procedurálním stylu, jako v tomto případě funkční přístup se zdálo složité a obskurní.
4. Výpočty protokolu jsou zabaleny `tryCatch()` tak, aby výjimky nezpůsobí náhlé zastavení zpracování. Bez `tryCatch()` většiny chyb vyvolaná funkcemi R za následek signál stop, což dělá právě to.

Spusťte tento kód R v experimentu a podívejte se na tištěný výstup v souboru output.log. Nyní uvidíte transformované hodnoty čtyř sloupců v protokolu, jak je znázorněno na obrázku 13.

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

*Obrázek 13. Souhrn transformovaných hodnot v datovém rámci.*

Vidíme, že hodnoty byly transformovány. Produkce mléka nyní výrazně převyšuje veškerou ostatní produkci mléčných výrobků a připomíná, že se nyní díváme na stupnici logů.

V tomto okamžiku jsou naše data vyčištěna a jsme připraveni na nějaké modelování. Při pohledu na souhrn vizualizace pro výstup result dataset našeho modulu [Execute R Script][execute-r-script] uvidíte sloupec "Měsíc" s 12 jedinečnými hodnotami, opět tak, jak jsme chtěli.

## <a name="time-series-objects-and-correlation-analysis"></a><a id="timeseries"></a>Objekty časových řad a analýza korelace

V této části prozkoumáme několik základních objektů časové řady R a analyzujeme korelace mezi některými proměnnými. Naším cílem je výstup datového rámce obsahujícího informace o párové korelaci při několika zpožděních.

Kompletní kód R pro tuto část je v [MachineLearningSamples-Notebooks/studio-ukázky](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="time-series-objects-in-r"></a>Objekty časových řad v R

Jak již bylo zmíněno, časové řady jsou řadou datových hodnot indexovaných podle času. Objekty časové řady R se používají k vytvoření a správě časového indexu. Použití objektů časových řad má několik výhod. Objekty časových řad vás osvobodí od mnoha podrobností správy hodnot indexu časových řad, které jsou zapouzdřeny v objektu. Kromě toho objekty časových řad umožňují používat metody mnoha časových řad pro vykreslování, tisk, modelování atd.

Třída časových řad POSIXCT se běžně používá a je poměrně jednoduchá. Tato časová řada třídy měří čas od začátku epochy, leden 1, 1970. V tomto příkladu použijeme objekty časových řad POSIXct. Mezi další široce používané třídy objektů časových řad R patří zoo a xts, rozšiřitelné časové řady.

### <a name="time-series-object-example"></a>Příklad objektu časové řady

Začněme s naším příkladem. Přetáhněte **nový** modul [Spustit skript R][execute-r-script] do experimentu. Připojte výstupní port Result Dataset1 existujícího modulu [Execute R Script][execute-r-script] ke vstupnímu portu Dataset1 nového modulu Spustit skript [R.][execute-r-script]

Stejně jako u prvních příkladů, jak postupujeme přes příklad, v některých bodech se zobrazí pouze přírůstkové další řádky kódu R v každém kroku.  

#### <a name="reading-the-dataframe"></a>Čtení datového rámce

Jako první krok si přečtěte v datovém rámci a ujistěte se, že dostaneme očekávané výsledky. Následující kód by měl dělat práci.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Teď spusťte ten experiment. Protokol nového tvaru Spustit skript R by měl vypadat jako obrázek 14.

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

*Obrázek 14. Souhrn datového rámce v modulu Spustit skript R.*

Tato data jsou očekávaných typů a formátu. Všimněte si, že sloupec Měsíc je typového faktoru a má očekávaný počet úrovní.

#### <a name="creating-a-time-series-object"></a>Vytvoření objektu časové řady

Do našeho datového rámce musíme přidat objekt časové řady. Nahraďte aktuální kód následujícím, který přidá nový sloupec třídy POSIXct.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Teď se podívejte do deníku. Mělo by to vypadat jako obrázek 15.

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

*Obrázek 15. Souhrn datového rámce s objektem časové řady.*

Ze souhrnu vidíme, že nový sloupec je ve skutečnosti třídy POSIXct.

### <a name="exploring-and-transforming-the-data"></a>Zkoumání a transformace dat

Podívejme se na některé proměnné v této datové sadě. Matice scatterplot je dobrý způsob, jak vytvořit rychlý vzhled. Jsem nahrazení `str()` funkce v předchozím kódu R s následujícím řádkem.

```R
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Spusťte tento kód a uvidíte, co se stane. Obrázek vytvořený na portu zařízení R by měl vypadat jako obrázek 16.

![Matice bodového grafu vybraných proměnných](./media/r-quickstart/fig17.png)

*Obrázek 16. Matice bodového grafu vybraných proměnných.*

Ve vztazích mezi těmito proměnnými je nějaká lichá struktura. Možná to vyplývá z trendů v datech a ze skutečnosti, že jsme nestandardizovali proměnné.

### <a name="correlation-analysis"></a>analýza korelací.

K provedení korelační analýzy musíme jak de-trend a standardizovat proměnné. Mohli bychom jednoduše `scale()` použít funkci R, která oba centra a měřítko proměnné. Tato funkce může běžet rychleji. Nicméně, chci vám ukázat příklad obranného programování v R.

Funkce `ts.detrend()` uvedená níže provádí obě tyto operace. Následující dva řádky kódu de-trend data a potom standardizovat hodnoty.

```R
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
```

Tam je docela dost `ts.detrend()` děje ve funkci. Většina tohoto kódu je kontrola potenciální problémy s argumenty nebo řešení s výjimkami, které mohou stále vzniknout během výpočtů. Pouze několik řádků tohoto kódu skutečně provést výpočty.

Již jsme diskutovali o příkladu obranného programování v transformace hodnoty. Oba výpočetní bloky jsou zabaleny do . `tryCatch()` Pro některé chyby má smysl vrátit původní vstupní vektor, a v jiných případech, vrátím vektor nul.  

Všimněte si, že lineární regrese slouží k de-trendy je regrese časové řady. Proměnná prediktoru je objekt časové řady.  

Jakmile `ts.detrend()` je definován, aplikujeme ji na proměnné zájmu v našem datovém rámci. Musíme donutí výsledný seznam vytvořený `lapply()` do datového `as.data.frame()`rámce pomocí . Z důvodu obranných `ts.detrend()`aspektů , selhání zpracování jedné z proměnných nezabrání správnému zpracování ostatních.  

Poslední řádek kódu vytvoří párový bodový graf. Po spuštění kódu R jsou výsledky bodového grafu zobrazeny na obrázku 17.

![Párová rozptylová skvrna detrendovaných a standardizovaných časových řad](./media/r-quickstart/fig18.png)

*Obrázek 17. Párová rozptylová skvrna de-trendovaných a standardizovaných časových řad.*

Tyto výsledky můžete porovnat s výsledky znázorněnými na obrázku 16. S trend odstraněny a proměnné standardizované, vidíme mnohem menší strukturu ve vztazích mezi těmito proměnnými.

Kód pro výpočet korelace jako R ccf objekty je následující.

```R
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
```

Spuštění tohoto kódu vytvoří protokol uvedený na obrázku 18.

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

*Obrázek 18. Seznam objektů ccf z analýzy párové korelace.*

Pro každé zpoždění existuje hodnota korelace. Žádná z těchto korelačních hodnot není dostatečně velká, aby byla významná. Můžeme tedy dojít k závěru, že každou proměnnou můžeme modelovat nezávisle.

### <a name="output-a-dataframe"></a>Výstup datového rámce
Vypočítali jsme párové korelace jako seznam R ccf objektů. To představuje trochu problém jako výstupní port datové sady výsledků skutečně vyžaduje datový rámec. Dále, ccf objekt je sám o sobě seznam a chceme pouze hodnoty v prvním prvku tohoto seznamu, korelace na různé lagy.

Následující kód extrahuje hodnoty zpoždění ze seznamu objektů ccf, které jsou samy o sobě seznamy.

```R
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
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

První řádek kódu je trochu složitější a některé vysvětlení vám může pomoci pochopit. Práce zevnitř ven máme následující:

1. Operátor [**[[** s argumentem**1**" vybere vektor korelací při zpožděních z prvního prvku seznamu objektů ccf.
2. Funkce `do.call()` použije `rbind()` funkci nad prvky listu vrátí `lapply()`.
3. Funkce `data.frame()` vykonavuje `do.call()` výsledek vytvořený datovým rámcem.

Všimněte si, že názvy řádků jsou ve sloupci datového rámce. Tím zachováte názvy řádků, pokud jsou výstupem ze [skriptu Execute R][execute-r-script]Script .

Spuštění kódu vytvoří výstup znázorněný na obrázku 19 při **vizualizaci** výstupu na portu dataset výsledků. Názvy řádků jsou v prvním sloupci, jak bylo zamýšleno.

![Výstup výsledků z korelační analýzy](./media/r-quickstart/fig20.png)

*Obrázek 19. Výstup výsledků z korelační analýzy.*

## <a name="time-series-example-seasonal-forecasting"></a><a id="seasonalforecasting"></a>Příklad časové řady: sezónní prognóza

Naše data jsou nyní ve formě vhodné pro analýzu a zjistili jsme, že mezi proměnnými neexistují žádné významné korelace. Pojďme dál a vytvořme model prognózy časových řad. Pomocí tohoto modelu budeme předpovídat kalifornskou produkci mléka pro 12 měsíců roku 2013.

Náš prognostické modely budou mít dvě součásti, trendovou složku a sezónní složku. Kompletní prognóza je součinem těchto dvou složek. Tento typ modelu se označuje jako multiplikativní model. Alternativou je aditivní model. Již jsme použili transformaci protokolu na proměnné zájmu, díky čemuž je tato analýza zvladatelná.

Kompletní kód R pro tuto část je v [MachineLearningSamples-Notebooks/studio-ukázky](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="creating-the-dataframe-for-analysis"></a>Vytvoření datového rámce pro analýzu

Začněte přidáním **nového** modulu [Spustit skript R][execute-r-script] do experimentu. Připojte výstup **datové sady výsledků** existujícího modulu Execute R [Script][execute-r-script] ke vstupu **Dataset1** nového modulu. Výsledek by měl vypadat nějak jako obrázek 20.

![Experiment s novým modulem Execute R Script byl přidán](./media/r-quickstart/fig21.png)

*Obrázek 20. Byl přidán experiment s novým modulem Spustit skript R.*

Stejně jako u korelační analýzy, kterou jsme právě dokončili, musíme přidat sloupec s objektem časových řad POSIXCT. Následující kód bude dělat právě toto.

```R
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Spusťte tento kód a podívejte se do protokolu. Výsledek by měl vypadat jako obrázek 21.

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

*Obrázek 21. Souhrn datového rámce.*

S tímto výsledkem jsme připraveni zahájit naši analýzu.

### <a name="create-a-training-dataset"></a>Vytvoření trénovací datové sady

S vytvořeným datovým rámcem musíme vytvořit trénovací datovou sadu. Tyto údaje budou zahrnovat všechna pozorování s výjimkou posledních 12 roku 2013, což je naše testovací datová sada. Následující kód podmívací datový rámec a vytvoří obrázky produkce mléka a cenové proměnné. Pak jsem vytvořit pozemky ze čtyř výrobních a cenových proměnných. Anonymní funkce se používá k definování některých vylepšení pro vykreslení a potom iterát přes seznam dalších dvou argumentů s `Map()`. Pokud si myslíte, že pro smyčku by fungovalo dobře tady, máte pravdu. Ale protože R je funkční jazyk, ukazuji vám funkční přístup.

```R
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

Spuštěním kódu vytvoříte řadu vykreslení časových řad z výstupu zařízení R znázorněného na obrázku 22. Všimněte si, že časová osa je v jednotkách dat, což je příjemná výhoda metody vykreslení časové řady.

![První čassérie pozemků kalifornské produkce mléka a cenových údajů](./media/r-quickstart/unnamed-chunk-161.png)

![Druhý čas série pozemků kalifornské produkce mléka a cenové údaje](./media/r-quickstart/unnamed-chunk-162.png)

![Třetina časových řad pozemků kalifornské produkce mléka a cenových údajů](./media/r-quickstart/unnamed-chunk-163.png)

![Čtvrtý čassérie pozemků kalifornské produkce mléka a cenové údaje](./media/r-quickstart/unnamed-chunk-164.png)

*Obrázek 22. Časové řady pozemků kalifornské produkce mléka a cenových údajů.*

### <a name="a-trend-model"></a>Trendový model

Po vytvoření objektu časových řad a po zobrazení dat začneme vytvářet trendový model pro data výroby mléka v Kalifornii. Můžeme to udělat s regresí časové řady. Z pozemku je však zřejmé, že budeme potřebovat více než sklon a zachytit, abychom přesně modelovat pozorovaný trend v tréninkových datech.

Vzhledem k malému rozsahu dat vytvořím model pro trend v RStudiu a potom vyjmu a vložím výsledný model do Azure Machine Learning Studio (classic). RStudio poskytuje interaktivní prostředí pro tento typ interaktivní analýzy.

Jako první pokus se pokusím o polynomické regrese s výkonem až 3. Existuje reálné nebezpečí nadměrnémontáže těchto typů modelů. Proto je nejlepší vyhnout se podmínkám vysokého řádu. Funkce `I()` inhibuje interpretaci obsahu (interpretuje obsah tak, jak je) a umožňuje napsat doslova interpretovní funkci v regresní rovnici.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

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

Z hodnot`Pr(>|t|)`P ( ) v tomto výstupu vidíme, že kvadrace termín nemusí být významné. Budu používat `update()` funkci upravit tento model tím, že upustí kvadrač termín.

```R
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

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

Tohle vypadá líp. Všechny podmínky jsou významné. Hodnota 2e-16 je však výchozí hodnota a neměla by být brána příliš vážně.  

Jako test příčetnosti udělejme časovou sérii údajů o produkci mléka v Kalifornii s uvedenou křivkou trendu. Přidal jsem následující kód v Azure Machine Learning Studio (klasické) [Spustit R Script][execute-r-script] model (ne RStudio) k vytvoření modelu a vytvořit spiknutí. Výsledek je znázorněn na obrázku 23.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Údaje o produkci kalifornského mléka s modelem trendu](./media/r-quickstart/unnamed-chunk-18.png)

*Obrázek 23. Údaje o produkci kalifornského mléka se zobrazeným trendovým modelem.*

Vypadá to, že trendový model odpovídá datům poměrně dobře. Dále se nezdá, že by existovaly důkazy o nadměrné montáži, jako jsou liché vrtí v modelové křivce.  

### <a name="seasonal-model"></a>Sezónní model

S trendovým modelem v ruce musíme pokračovat a zahrnout sezónní účinky. Měsíc v roce použijeme jako fiktivní proměnnou v lineárním modelu k zachycení měsíčního efektu. Všimněte si, že při zavádění proměnných faktoru do modelu, intercept nesmí být vypočítány. Pokud tak neučiníte, vzorec je přezadaný a R klesne jeden z požadovaných faktorů, ale zachovat termín zachycení.

Vzhledem k tomu, že máme `update()` uspokojivý trendový model, můžeme použít funkci pro přidání nových termínů do stávajícího modelu. -1 ve vzorci aktualizace klesne intercept termín. Pokračování v RStudiu pro tuto chvíli:

```R
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

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

Vidíme, že model již nemá termín zachycení a má 12 významných měsíčních faktorů. To je přesně to, co jsme chtěli vidět.

Udělejme další časové řady spiknutí dat produkce mléka v Kalifornii vidět, jak dobře sezónní model funguje. Přidal jsem následující kód v Azure Machine Learning Studio (klasické) [Spusťte R Skript][execute-r-script] vytvořit model a vytvořit spiknutí.

```R
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

Spuštění tohoto kódu v Azure Machine Learning Studio (klasické) vytvoří vykreslení uvedené na obrázku 24.

![Kalifornská produkce mléka s modelem včetně sezónních účinků](./media/r-quickstart/unnamed-chunk-20.png)

*Obrázek 24. Kalifornská produkce mléka s modelem včetně sezónních účinků.*

Přizpůsobení údajům uvedeným na obrázku 24 je spíše povzbudivé. Jak trend, tak sezónní efekt (měsíční variace) vypadají rozumně.

Jako další kontrolu našeho modelu se podíváme na zbytky. Následující kód vypočítá předpovídané hodnoty z našich dvou modelů, vypočítá rezidua pro sezónní model a pak vykreslí tyto rezidua pro trénovací data.

```R
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

Zbytková zápletka je znázorněna na obrázku 25.

![Zbytky sezónního modelu pro údaje o školení](./media/r-quickstart/unnamed-chunk-21.png)

*Obrázek 25. Zbytky sezónního modelu pro údaje o školení.*

Tyhle zbytky vypadají rozumně. Neexistuje žádná konkrétní struktura, s výjimkou vlivu recese v letech 2008-2009, kterou náš model nezohledňuje příliš dobře.

Obrázek znázorněný na obrázku 25 je užitečný pro detekci časově závislých vzorů ve zbytkových vzorech. Explicitní přístup výpočetní techniky a vykreslování zbytků jsem použil umístí zbytky v časovém pořadí na pozemku. Kdyby naopak, naplánoval jsem to `milk.lm$residuals`, zápletka by nebyla v časovém pořádku.

Můžete také `plot.lm()` použít k vytvoření řady diagnostických pozemků.

```R
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Tento kód vytváří řadu diagnostických zkusných ploch znázorněných na obrázku 26.

![První z diagnostických pozemků pro sezónní model](./media/r-quickstart/unnamed-chunk-221.png)

![Druhý z diagnostických pozemků pro sezónní model](./media/r-quickstart/unnamed-chunk-222.png)

![Třetina diagnostických zkusných ploch pro sezónní model](./media/r-quickstart/unnamed-chunk-223.png)

![Čtvrtý z diagnostických zkusných ploch pro sezónní model](./media/r-quickstart/unnamed-chunk-224.png)

*Obrázek 26. Diagnostické obrázky pro sezónní model.*

Existuje několik velmi vlivných bodů uvedených v těchto spiknutí, ale nic způsobit velké obavy. Dále můžeme vidět z normálního Q-Q grafu, že zbytky jsou blízko k normálně distribuovány, důležitý předpoklad pro lineární modely.

### <a name="forecasting-and-model-evaluation"></a>Prognózování a hodnocení modelu

Je tu ještě jedna věc, kterou je třeba udělat pro dokončení našeho příkladu. Musíme vypočítat prognózy a změřit chybu oproti skutečným datům. Naše předpověď bude na 12 měsíců roku 2013. Můžeme vypočítat míra chyb pro tuto prognózu na skutečná data, která není součástí naší trénovací datové sady. Kromě toho můžeme porovnat výkon na 18 let školení dat na 12 měsíců testovacích dat.  

K měření výkonu modelů časových řad se používá řada metrik. V našem případě použijeme kořenovou střední kvadračovou (RMS) chybu. Následující funkce vypočítá chybu služby RMS mezi dvěma řadami.  

```R
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
```

Stejně `log.transform()` jako u funkce, kterou jsme diskutovali v části "Transformace hodnoty", je v této funkci poměrně velké množství chyb a kódu pro obnovení výjimek. Použité zásady jsou stejné. Práce se provádí na dvou `tryCatch()`místech zabalených v . Za prvé, časové řady jsou exponenciálně, protože jsme pracovali s protokoly hodnot. Za druhé je vypočítána skutečná chyba služby RMS.  

Vybaven funkcí pro měření chyby RMS, pojďme vytvořit a výstup datového rámce obsahujícího chyby RMS. Budeme zahrnovat termíny pro model trendu sám a kompletní model se sezónními faktory. Následující kód provádí práci pomocí dvou lineárních modelů, které jsme vytvořili.

```R
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
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('RMS.df')
```

Spuštěnítohoto kódu vytvoří výstup znázorněný na obrázku 27 na výstupním portu výsledovky datové sady.

![Porovnání chyb RMS pro modely](./media/r-quickstart/fig26.png)

*Obrázek 27. Porovnání chyb RMS pro modely.*

Z těchto výsledků vidíme, že přidání sezónní faktory do modelu výrazně snižuje rms chybu. Není divu, že chyba RMS pro trénovací data je o něco menší než pro prognózu.

## <a name="guide-to-rstudio-documentation"></a><a id="appendixa"></a>Průvodce dokumentací RStudio

RStudio je docela dobře zdokumentováno. Zde jsou některé odkazy na klíčové části dokumentace RStudio, které vám pomohou začít.

* **Vytváření projektů** – můžete uspořádat a spravovat r kód do projektů pomocí RStudio. Podrobnosti najdete [v tématu Použití projektů.](https://support.rstudio.com/hc/articles/200526207-Using-Projects) Doporučuji postupovat podle těchto pokynů a vytvořit projekt pro příklady kódu R v tomto článku.  
* **Editace a provádění R kódu** - RStudio poskytuje integrované prostředí pro úpravy a provádění R kódu. Podrobnosti najdete [v tématu Úpravy a provádění kódu.](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code)
* **Ladění** - RStudio obsahuje výkonné možnosti ladění. Další informace o těchto funkcích naleznete [v tématu Ladění pomocí aplikace RStudio.](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio) Informace o funkcích řešení potíží s zarážkem naleznete v [tématu Řešení potíží s zarážkym](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting).

## <a name="further-reading"></a><a id="appendixb"></a>Další čtení

Tento kurz programování R popisuje základy toho, co potřebujete k použití jazyka R s Azure Machine Learning Studio (klasické). Pokud nejste obeznámeni s R, dva úvody jsou k dispozici na CRAN:

* [R pro začátečníky](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) od Emmanuelparadis je dobrým místem pro začátek.  
* [Úvod do R](https://cran.r-project.org/doc/manuals/R-intro.html) od W. N. Venables et. Al. jde do trochu větší hloubky.

Existuje mnoho knih o R, které vám pomohou začít. Zde je několik najdu užitečné:

* **Umění R programování: Prohlídka statistického softwarového designu** Norman Matloff je vynikající úvod do programování v R.  
* **R Kuchařka** Paul Teetor poskytuje problém a řešení přístupu k použití R.  
* **R v akci** Robert Kabacoff je další užitečná úvodní kniha. Doprovodný [web Quick R](https://www.statmethods.net/) je užitečným zdrojem.
* **R Inferno** Patrick Burns je překvapivě humorná kniha, která se zabývá řadou záludných a obtížných témat, se kterými se lze setkat při programování v R. Kniha je k dispozici zdarma na [R Inferno](https://www.burns-stat.com/documents/books/the-r-inferno/).
* Pokud se chcete ponořit do pokročilých témat v R, podívejte se na knihu **Advanced R** hadley wickham. Online verze této knihy je k [http://adv-r.had.co.nz/](http://adv-r.had.co.nz/)dispozici zdarma na .

Katalog balíčků časových řad R naleznete v [zobrazení úloh CRAN: Analýza časových řad](https://cran.r-project.org/web/views/TimeSeries.html). Informace o konkrétních balíčcích objektů časové řady byste měli naleznete v dokumentaci k tomuto balíčku.

Kniha **Úvodní časová řada** s R Paulcowpertwait a Andrew Metcalfe poskytuje úvod do použití R pro analýzu časových řad. Mnoho dalších teoretických textů poskytuje příklady R.

Zde jsou některé skvělé internetové zdroje:

* DataCamp učí R v pohodlí vašeho prohlížeče s video lekce a kódování cvičení. K dispozici jsou interaktivní kurzy o nejnovějších technikách a balíčcích R. Vezměte si zdarma [interaktivní R tutorial](https://www.datacamp.com/courses/introduction-to-r).
* [Naučte se R programování, Definitivní průvodce](https://www.programiz.com/r-programming) od Programiz.
* Rychlý [R Tutorial](https://www.cyclismo.org/tutorial/R/) Kelly Black z Clarkson University.
* Existuje více než 60 R prostředky uvedené na [Top R jazykové zdroje ke zlepšení vašich dovedností v oblasti dat](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html).

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
