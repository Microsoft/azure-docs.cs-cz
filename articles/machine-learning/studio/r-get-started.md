---
title: Použití R s ML Studio (Classic) – Azure
description: Tento kurz programovacího jazyka R vám umožní začít s Azure Machine Learning Studio (Classic) v jazyce R a vytvořit řešení prognózy.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: 2c481fc2f435695b4b99b86411a2fcca27e97ab4
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117864"
---
# <a name="get-started-with-azure-machine-learning-studio-classic-in-r"></a>Začínáme s Azure Machine Learning Studio (Classic) v R

<!-- Stephen F Elston, Ph.D. -->
V tomto kurzu se naučíte, jak pomocí ML Studio (Classic) vytvořit, otestovat a spustit kód R. Na konci budete mít kompletní řešení předpovědi.  

> [!div class="checklist"]
> * Vytvořte kód pro čištění a transformaci dat.
> * Analyzujte korelace mezi několika proměnnými v naší datové sadě.
> * Vytvořte sezónní model předpovědi časových řad pro produkci mléka.


Azure Machine Learning Studio (Classic) obsahuje mnoho výkonného strojového učení a modulů manipulace s daty. A s programovacím jazykem R poskytuje tato kombinace škálovatelnost a snadné nasazení studia (Classic) s flexibilitou a hloubkovou analýzou jazyka R.

Prognózování je široce zaměstnaná a poměrně užitečná analytická metoda. Běžný používá rozsah z předpovědi prodej sezónních položek a určení optimálních úrovní inventáře k předvídání makroekonomických proměnných. Prognózy se obvykle provádějí s modely časových řad. Data časové řady jsou data, ve kterých mají hodnoty časový index. Časový index může být pravidelný, například každý měsíc nebo každou minutu nebo nepravidelně. Model časových řad je založený na datech časových řad. Programovací jazyk R obsahuje flexibilní rozhraní a rozsáhlou analýzu pro data časových řad.

## <a name="get-the-data"></a>Získání dat

V tomto kurzu použijete produkci dojnic a data o cenách v Kalifornii, což zahrnuje měsíční informace o produkci několika mléčných produktů a cenu mléčného tuku, srovnávací komodita.

Data použitá v tomto článku spolu se skripty jazyka R lze stáhnout z [MachineLearningSamples-poznámkových bloků/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples). Data v souboru `cadairydata.csv` byla původně syntetizovaná z informací, které jsou k dispozici na University of Wisconsin v [https://dairymarkets.com](https://dairymarkets.com) .



## <a name="interact-with-r-language-in-machine-learning-studio-classic"></a><a id="mlstudio"></a>Interakce s jazykem R v Machine Learning Studio (Classic)

V této části se seznámíte se základy práce s programovacím jazykem R v prostředí Machine Learning Studio (Classic). Jazyk R poskytuje výkonný nástroj pro vytváření přizpůsobených modulů analýzy a manipulace s daty v prostředí Azure Machine Learning Studio (Classic).

Využijem RStudio k vývoji, testování a ladění kódu R v malém měřítku. Tento kód se pak vyjme a vloží do modulu [spuštění skriptu jazyka R][execute-r-script] , který je připravený ke spuštění v Azure Machine Learning Studio (Classic).  

### <a name="the-execute-r-script-module"></a>Modul spuštění skriptu jazyka R

V rámci Machine Learning Studio (Classic) se skripty R spouštějí v modulu [spuštění skriptu jazyka r][execute-r-script] . Příklad modulu [spuštění skriptu R][execute-r-script] v Machine Learning Studio (Classic) je znázorněn na obrázku 1.

 ![Programovací jazyk r: modul spuštění skriptu jazyka R vybraný v Machine Learning Studio (klasický)](./media/r-quickstart/fig1.png)

*Obrázek 1. Prostředí Machine Learning Studio (Classic) znázorňující vybraný modul skriptu pro spuštění jazyka R.*

S odkazem na obrázek 1 se podíváme na některé z klíčových částí prostředí Machine Learning Studio (Classic) pro práci s modulem [skriptu Execute R][execute-r-script] .

* Moduly v experimentu se zobrazí v prostředním podokně.
* Horní část pravého podokna obsahuje okno pro zobrazení a úpravy skriptů jazyka R.  
* Dolní část pravého podokna zobrazuje některé vlastnosti [skriptu Execute R][execute-r-script]. Protokoly chyb a výstupu můžete zobrazit tak, že vyberete vhodné body v tomto podokně.

Budeme samozřejmě diskutovat o [skriptu Execute R][execute-r-script] podrobněji ve zbývající části tohoto článku.

Když pracujete se složitými funkcemi jazyka R, doporučujeme, abyste v RStudio provedete úpravy, testování a ladění. Stejně jako u jakéhokoli vývoje softwaru rozšíříte kód přírůstkově a otestujete ho v malých jednoduchých testovacích případech. Potom tyto funkce vyjměte a vložte do okna skript R v modulu [spuštění skriptu jazyka r][execute-r-script] . Tento přístup vám umožní využít integrované vývojové prostředí (IDE) RStudio a výkon Azure Machine Learning Studio (Classic).  

#### <a name="execute-r-code"></a>Spustit kód R

Jakýkoli kód R v modulu [spouštění skriptu jazyka r][execute-r-script] se spustí při spuštění experimentu výběrem tlačítka **Spustit** . Po dokončení spuštění se na ikoně [Spustit skript jazyka R][execute-r-script] zobrazí zaškrtnutí.

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Obrannou linií R kódování pro Azure Machine Learning

Pokud vyvíjíte kód R pro, řekněme, že webová služba používá Azure Machine Learning Studio (Classic), měli byste bez omezení naplánovat, jak váš kód bude pracovat s neočekávaným vstupem a výjimkami dat. Aby se zachovala jasnost, nezahrnuli jsme do většiny zobrazených příkladů kódu mnoho možností kontroly nebo zpracování výjimek. Jak ale budeme pokračovat, nabídneme vám několik příkladů funkcí pomocí schopnosti zpracování výjimek jazyka R.  

Pokud potřebujete úplnější zpracování výjimek R, doporučujeme si přečíst si příslušné části knihy podle Wickham uvedeného níže v tématu Další informace o [čtení](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Ladění a testování R v Machine Learning Studio (Classic)

K opakování iterace doporučujeme testovat a ladit kód R v malém měřítku v RStudio. Existují však případy, kdy budete muset sledovat problémy s kódem R v samotném [spuštění skriptu jazyka r][execute-r-script] . Kromě toho je dobrým zvykem kontrolovat výsledky v Machine Learning Studio (Classic).

Výstup z provádění kódu R a na platformě Azure Machine Learning Studio (Classic) se najde primárně v části Output. log. Některé další informace se zobrazí v Error. log.  

Pokud při spuštění kódu R dojde k chybě v Machine Learning Studio (Classic), měli byste se podívat na chybu. log v prvním postupu. Tento soubor může obsahovat užitečné chybové zprávy, které vám pomůžou pochopit a opravit chybu. Chcete-li zobrazit Error. log, vyberte **Zobrazit protokol chyb** v **podokně vlastnosti** pro [skript Execute R][execute-r-script] obsahující chybu.

Například jsem spustil následující kód R s nedefinovanou proměnnou y v modulu [spuštění skriptu jazyka r][execute-r-script] :

```R
x <- 1.0
z <- x + y
```

Spuštění tohoto kódu se nepovede, takže dojde k chybě. Výběr možnosti **Zobrazit protokol chyb** v **podokně vlastnosti** vytvoří zobrazení zobrazené na obrázku 2.

  ![Chybová zpráva – překryvný stav](./media/r-quickstart/fig2.png)

*Obrázek 2. Automaticky otevíraná okna chybové zprávy.*

Vypadá to, že je potřeba vyhledat výstup. log a zobrazit chybovou zprávu R. Vyberte [skript spustit][execute-r-script] v jazyce R a potom v **podokně vlastností** vpravo vyberte položku **Zobrazit výstup. log** . Otevře se nové okno prohlížeče a zobrazí se následující.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Tato chybová zpráva neobsahuje žádné překvapením a jasně identifikuje problém.

Chcete-li zkontrolovat hodnotu libovolného objektu v R, můžete tyto hodnoty vytisknout do souboru Output. log. Pravidla pro zkoumání hodnot objektu jsou v podstatě stejná jako v interaktivní relaci jazyka R. Například pokud zadáte název proměnné na řádku, hodnota objektu bude vytištěna do souboru Output. log.  

#### <a name="packages-in-machine-learning-studio-classic"></a>Balíčky v Machine Learning Studio (klasické)

Studio přichází s více než 350 předinstalovanými balíčky jazyka R. K načtení seznamu předinstalovaných balíčků můžete použít následující kód v modulu [spuštění skriptu jazyka R][execute-r-script] .

```R
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Pokud v tuto chvíli nerozumíte poslednímu řádku tohoto kódu, přečtěte si. Ve zbývající části tohoto článku budeme v prostředí studia (Classic) podrobně projednávat použití R.

### <a name="introduction-to-rstudio"></a>Úvod do RStudio

RStudio je široce používané integrované vývojové prostředí (IDE) pro R. Používám RStudio pro úpravy, testování a ladění kódu R použitého v této příručce. Po otestování a přípravě kódu R můžete jednoduše vyjmout a vložit z editoru RStudio do Machine Learning Studio (Classic) [spustit modul skriptu R][execute-r-script] .  

Pokud nemáte na stolním počítači nainstalovaný programovací jazyk R, doporučujeme to udělat teď. Bezplatné stahování Open Source jazyka R je k dispozici v komplexní síti archivu R (CRAN) na adrese [https://www.r-project.org/](https://www.r-project.org/) . K dispozici jsou soubory ke stažení pro Windows, Mac OS a Linux/UNIX. Vyberte okolní zrcadlo a postupujte podle pokynů ke stažení. Kromě toho CRAN obsahuje širokou spoustu užitečných analýz a datových sad pro manipulaci s daty.

Pokud s RStudio začínáte, měli byste si stáhnout a nainstalovat desktopovou verzi. Soubory ke stažení RStudio pro Windows, Mac OS a Linux/UNIX najdete na adrese http://www.rstudio.com/products/RStudio/ . Postupujte podle pokynů uvedených k instalaci RStudio na stolní počítač.  

Kurz Úvod do RStudio je k dispozici v [části použití prostředí IDE RStudio](https://support.rstudio.com/hc/sections/200107586-Using-RStudio).

V této příručce najdete další informace o používání RStudio v [dokumentaci k RStudio](#appendixa) .  

## <a name="get-data-in-and-out-of-the-execute-r-script-module"></a><a id="scriptmodule"></a>Načtení dat z modulu spuštění skriptu R a z něj

V této části se dozvíte, jak získat data do a z modulu [spuštění skriptu jazyka R][execute-r-script] . Přečtěte si, jak zpracovávat různé datové typy, které jsou čteny do modulu [spuštění skriptu jazyka R][execute-r-script] a z něj.

Úplný kód této části je v [MachineLearningSamples-poznámkách/studiu-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="load-and-check-data"></a>Načíst a ověřit data 

#### <a name="load-the-dataset"></a><a id="loading"></a>Načíst datovou sadu

Začneme tak, že načteme soubor **csdairydata. csv** do Azure Machine Learning Studio (Classic).

1. Spusťte prostředí Azure Machine Learning Studio (Classic).
1. V levém dolním rohu obrazovky vyberte **+ Nový** a vyberte **datová sada**.
1. Vyberte možnost **z místního souboru**a pak **procházením** vyberte soubor.
1. Ujistěte se, že jste jako typ pro datovou sadu vybrali **obecný soubor CSV s hlavičkou (. csv)** .
1. Zaškrtněte políčko.
1. Po nahrání datové sady byste měli zobrazit novou datovou sadu výběrem karty datové **sady** .  

#### <a name="create-an-experiment"></a>Vytvoření experimentu

Teď, když máme nějaká data v Machine Learning Studio (Classic), musíme vytvořit experiment pro analýzu.  

1. V levém dolním rohu vyberte **+ Nový** a vyberte **experiment**a pak **prázdný experiment**.
1. Experiment můžete pojmenovat tak, že vyberete a upravíte v horní části stránky název experimentu, který jste **vytvořili...** . Například změna na **analýzu mléčných mléka certifikační autority**.
1. Na levé straně stránky experimentu rozbalte **uložené datové sady**a pak **Moje datové sady**. Měl by se zobrazit soubor **cadairydata. csv** , který jste nahráli dříve.
1. Přetáhněte **datovou sadu csdairydata. csv** do experimentu.
1. Do pole **Hledat položky experimentu** v horní části levého podokna zadejte [Execute Script script][execute-r-script]. V seznamu hledání se zobrazí modul.
1. Přetáhněte na paletu modul [spuštění skriptu jazyka R][execute-r-script] .  
1. Připojte výstup **datové sady csdairydata. csv** k levému vstupu (**DataSet1.**) [skriptu Execute jazyka R][execute-r-script].
1. **Nezapomeňte vybrat Save (Uložit).**  

V tomto okamžiku by experiment měl vypadat přibližně jako obrázek 3.

![Experiment s datovou sadou a spuštěným modulem skriptu R pro certifikační autoritu](./media/r-quickstart/fig3.png)

*Obrázek 3. Experiment s datovou sadou a spuštěným modulem skriptu jazyka R v certifikační autoritě.*

#### <a name="check-on-the-data"></a>Kontrolovat data

Pojďme se podívat na data, která jsme načetli do našeho experimentu. V experimentu vyberte výstup **datové sady cadairydata. csv** a vyberte **vizualizovat**. Mělo by se zobrazit něco jako obrázek 4.  

![Souhrn datové sady cadairydata. csv](./media/r-quickstart/fig4.png)

*Obrázek 4. Souhrn datové sady cadairydata. csv*

V tomto zobrazení se zobrazí spousta užitečných informací. Můžeme zobrazit prvních několik řádků této datové sady. Když vybereme sloupec, v části Statistika se zobrazí další informace o sloupci. Například řádek typ funkce zobrazuje, jaké typy dat Azure Machine Learning Studio (Classic) přiřazené sloupci. Rychlý vzhled je dobrý správnosti před zahájením provádění všech závažných prací.

### <a name="first-r-script"></a>První skript R

Pojďme vytvořit jednoduchý první skript R, který vám umožní experimentovat se v Azure Machine Learning Studio (Classic). V RStudio jsem vytvořil a otestoval následující skript.  

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

Nyní Potřebuji přenést tento skript do Azure Machine Learning Studio (Classic). Můžu ho jednoduše vyjímat a vkládat. V tomto případě se ale skript R převede přes soubor zip.

### <a name="data-input-to-the-execute-r-script-module"></a>Vstup dat do modulu spuštění skriptu jazyka R

Pojďme se podívat na vstupy pro modul [spuštění skriptu jazyka R][execute-r-script] . V tomto příkladu přečteme data o mlékárnách z Brna do modulu [spuštění skriptu jazyka R][execute-r-script] .  

Existují tři možné vstupy pro modul [spuštění skriptu jazyka R][execute-r-script] . V závislosti na vaší aplikaci můžete použít libovolný nebo všechny tyto vstupy. Je také naprosto rozumné použít skript R, který neprovádí žádné vstupy.  

Pojďme se podívat na každý z těchto vstupů zleva doprava. Můžete zobrazit názvy všech vstupů tak, že umístíte kurzor na vstup a přečtete popis.  

#### <a name="script-bundle"></a>Sada skriptů

Vstup sady prostředků skriptu umožňuje předat obsah souboru zip do [spouštěného modulu skriptu jazyka R][execute-r-script] . K načtení obsahu souboru zip do kódu jazyka R můžete použít jeden z následujících příkazů.

```R
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Azure Machine Learning Studio (Classic) zpracovává soubory ve formátu zip, jako kdyby byly v souboru src/Directory, takže je nutné názvy souborů pro tento název adresáře předponovat. Například pokud zip obsahuje soubory `yourfile.R` a `yourData.rdata` v kořenovém adresáři zip, budete je řešit jako `src/yourfile.R` a `src/yourData.rdata` při použití `source` a `load` .

Již jsme probrali načítání datových sad v [načtení datové](#loading)sady. Jakmile vytvoříte a otestujete skript R zobrazený v předchozí části, udělejte toto:

1. Uložte skript R do. Soubor R. Já jsem volat můj soubor skriptu "simpleplot. R. Tady je obsah.

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

1. Vytvořte soubor zip a zkopírujte skript do tohoto souboru ZIP. V systému Windows můžete kliknout pravým tlačítkem na soubor a vybrat **Odeslat do**a pak na **komprimovanou složku**. Tím se vytvoří nový soubor zip, který obsahuje "simpleplot". R "soubor.

1. Přidejte soubor do **datových sad** v Azure Machine Learning Studio (Classic), zadejte typ jako **zip**. V datových sadách by se teď měl zobrazit soubor zip.

1. Přetáhněte soubor zip z **datových sad** na **plátno ml Studio (Classic)**.

1. Připojte výstup ikony **dat zip** ke vstupu **sady prostředků skriptu** [spustit modul skriptu jazyka R][execute-r-script] .

1. Zadejte `source()` funkci s názvem souboru zip do okna Code pro modul [spouštění skriptu jazyka R][execute-r-script] . V `source("src/simpleplot.R")` mém zadaném případu  

1. Ujistěte se, že jste vybrali **Uložit**.

Po dokončení těchto kroků se v souboru ZIP při spuštění experimentu spustí modul [skriptu Run r][execute-r-script] . V tomto okamžiku by experiment měl vypadat přibližně jako obrázek 5.

![Experimentování s použitím skriptu zip R](./media/r-quickstart/fig6.png)

*Obrázek 5. Experimentujte pomocí skriptu zip R.*

#### <a name="dataset1"></a>DataSet1.

Pomocí vstupu DataSet1. můžete předat obdélníkovou tabulku dat kódu jazyka R. V našem jednoduchém skriptu `maml.mapInputPort(1)` načte funkce data z portu 1. Tato data se pak přiřazují k názvu proměnné datového rámce v kódu. V našem jednoduchém skriptu provede první řádek kódu přiřazení.

```R
cadairydata <- maml.mapInputPort(1)
```

Spusťte experiment výběrem tlačítka **Spustit** . Po dokončení spuštění vyberte modul skriptu pro [spuštění R][execute-r-script] a v podokně Vlastnosti vyberte **Zobrazit výstupní protokol** . V prohlížeči by se měla zobrazit nová stránka, která zobrazuje obsah výstupního souboru. log. Když se posunete dolů, měla by se zobrazit něco podobného jako následující.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Další možností na stránce je podrobnější informace o sloupcích, což bude vypadat přibližně takto.

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

Tyto výsledky jsou převážně podle očekávání, přičemž 228 pozorování a 9 sloupců v dataframe. Zobrazí se názvy sloupců, datový typ R a ukázka každého sloupce.

> [!NOTE]
> Stejný vytištěný výstup je pohodlně dostupný z výstupu zařízení R v modulu [spouštění skriptu jazyka r][execute-r-script] . V další části se podíváme na výstupy modulu [spuštění skriptu jazyka R][execute-r-script] .  

#### <a name="dataset2"></a>Dataset2

Chování vstupu Dataset2 je stejné jako u DataSet1.. Pomocí tohoto vstupu můžete do kódu R předat druhou obdélníkovou tabulku dat. Funkce `maml.mapInputPort(2)` s argumentem 2 se používá k předání těchto dat.  

### <a name="execute-r-script-outputs"></a>Spouštění výstupů skriptů R

#### <a name="output-a-dataframe"></a>Výstup datového rámce

Obsah datového rámce R můžete vystavit jako obdélníkovou tabulku prostřednictvím DataSet1. portu výsledku pomocí `maml.mapOutputPort()` funkce. V našem jednoduchém skriptu jazyka R se to provádí na následujícím řádku.

```
maml.mapOutputPort('cadairydata')
```

Po spuštění experimentu vyberte výstupní port DataSet1. výsledku a pak vyberte **vizualizovat**. Měl by se zobrazit něco podobného jako obrázek 6.

![Vizualizace výstupu dat pro mléčné výrobky v Kalifornii](./media/r-quickstart/fig7.png)

*Obrázek 6. Vizualizace výstupu dat o mlékárnách v Kalifornii*

Tento výstup bude stejný jako u vstupu, přesně podle očekávání.  

### <a name="r-device-output"></a>Výstup zařízení v R

Výstup zařízení modulu [spuštění skriptu jazyka R][execute-r-script] obsahuje zprávy a výstup grafiky. Do výstupního portu zařízení R se odesílají standardní výstupní i standardní chybové zprávy z R.  

Chcete-li zobrazit výstup zařízení R, vyberte port a pak na **vizualizaci**. Ve skriptu jazyka R na obrázku 7 se zobrazuje standardní výstup a standardní chyba.

![Standardní výstup a standardní chyba z portu zařízení R](./media/r-quickstart/fig8.png)

*Obrázek 7. Standardní výstup a standardní chyba z portu zařízení R.*

Posouváním dolů vidíte výstup grafiky z našeho skriptu jazyka R na obrázku 8.  

![Výstup grafiky z portu zařízení R](./media/r-quickstart/fig9.png)

*Obrázek 8. Výstup grafiky z portu zařízení R*  

## <a name="data-filtering-and-transformation"></a><a id="filtering"></a>Filtrování a transformace dat

V této části provedeme několik základních operací pro filtrování a transformaci dat v rámci dat o mlékárnách v Kalifornii. Na konci této části budeme mít data ve formátu vhodném pro vytvoření analytického modelu.  

Konkrétně v této části provedeme několik běžných úloh čištění a transformace dat: transformace typu, filtrování na datových snímcích, přidávání nových vypočítaných sloupců a transformace hodnot. Toto pozadí by vám mělo pomáhat při práci s mnoha variantami zjištěnými v reálných problémech.

Úplný kód R této části je k dispozici v [MachineLearningSamples – poznámkových blocích/studiu-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="type-transformations"></a>Transformace typu

Teď, když můžeme přečíst data o mlékárnách v Kalifornii do kódu R v modulu [spuštění skriptu jazyka r][execute-r-script] , musíme zajistit, aby data ve sloupcích měla zamýšlený typ a formát.  

R je dynamicky typový jazyk, což znamená, že datové typy jsou v případě potřeby z jednoho na jiné. Atomické datové typy v jazyce R obsahují číselné, logické a znakové. Typ faktoru se používá k komprimaci dat kategorií. Další informace o datových typech [najdete v odkazech níže.](#appendixb)

Když jsou tabulková data načítána z externího zdroje na R, je vždy vhodné kontrolovat výsledné typy ve sloupcích. Je možné, že budete chtít sloupec typu znak, ale v mnoha případech se to zobrazí jako faktor nebo naopak. V ostatních případech by měla být číselná hodnota, která je vyjádřená jako znaková data, třeba 1,23, nikoli 1,23 jako číslo s plovoucí desetinnou čárkou.  

Naštěstí je snadné převést jeden typ na jiný, pokud je mapování možné. Například nelze převést ' Nevada ' na číselnou hodnotu, ale lze jej převést na faktor (kategorií proměnná). V jiném příkladu můžete převést číslo 1 na znak 1 nebo faktor.  

Syntaxe pro některý z těchto převodů je jednoduchá: `as.datatype()` . Tyto funkce pro převod typů zahrnují následující.

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Podívejte se na datové typy sloupců, které zadáte v předchozí části: všechny sloupce jsou typu numeric, s výjimkou sloupce označeného ' month ', který je typu Character. Pojďme to převést na faktor a otestovat výsledky.  

Odstranil (a) jsem řádek, který vytvořil matici scatterplot, a přidal (a) řádek, který převádí sloupec ' month ' na faktor. V mém experimentu budu jenom vyjímat a vkládat kód R do okna Code modulu [spouštěného skriptu jazyka R][execute-r-script] . Můžete také aktualizovat soubor zip a odeslat ho do Azure Machine Learning Studio (Classic), ale toto nastavení trvá několik kroků.  

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

Pojďme tento kód spustit a podívat se na výstupní protokol pro skript R. Relevantní data z protokolu jsou uvedena na obrázku 9.

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

*Obrázek 9: Souhrn datového rámce s proměnnou faktor.*

Typ pro měsíc by teď měl vyslovit "**faktor w/14 úrovně**". Jedná se o problém, protože v roce je jenom 12 měsíců. Můžete také zjistit, že typ v **vizualizaci** portu datové sady výsledku je '**kategorií**'.

Problémem je, že sloupec ' month ' nebyl kódovaný systematicky. V některých případech se za měsíc zavolá duben a v ostatních případech se zkrátí jako APR. Tento problém můžeme vyřešit oříznutím řetězce na 3 znaky. Řádek kódu nyní vypadá takto:

```R
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Znovu spusťte experiment a zobrazte výstupní protokol. Očekávané výsledky jsou zobrazeny na obrázku 10.  

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

*Obrázek 10. Souhrn datového rámce se správným počtem úrovní faktoru.*

Naše proměnná faktoru má teď požadované 12 úrovní.

### <a name="basic-data-frame-filtering"></a>Základní filtrování datových snímků

Datový rámec R podporuje výkonné možnosti filtrování. Datové sady mohou být subsetted pomocí logických filtrů na jednom řádku nebo ve sloupcích. V mnoha případech se vyžadují složitá kritéria filtru. [Níže uvedené](#appendixb) odkazy obsahují podrobné příklady filtrování dataframes.  

Pro naši datovou sadu bychom měli dělat jeden bit filtrování. Pokud se podíváte na sloupce v dataframe cadairydata, zobrazí se dva nepotřebné sloupce. První sloupec má pouze číslo řádku, což není velmi užitečné. Druhý sloupec year. Month obsahuje nadbytečné informace. Tyto sloupce můžeme snadno vyloučit pomocí následujícího kódu R.

> [!NOTE]
> Od tohoto oddílu teď v této části ukážeme jenom další kód, který přidáváte do modulu [spuštění skriptu jazyka R][execute-r-script] . Přidám všechny nové řádky **před** `str()` funkci. Pomocí této funkce lze ověřit výsledky v Azure Machine Learning Studio (Classic).

Do kódu R v modulu [spuštění skriptu jazyka r][execute-r-script] přidám následující řádek.

```R
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Spusťte tento kód v experimentu a Prohlédněte si výsledek z výstupního protokolu. Tyto výsledky se zobrazují na obrázku 11.

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

*Obrázek 11. Souhrn datového rámce se dvěma odebranými sloupci*

Máme pro vás dobré zprávy! Získáte očekávané výsledky.

### <a name="add-a-new-column"></a>Přidat nový sloupec

Aby bylo možné vytvářet modely časových řad, bude vhodné mít sloupec, který obsahuje měsíce od začátku časové řady. Vytvoří se nový sloupec Month. Count.

Abychom vám usnadnili uspořádání kódu, vytvoříme naši první jednoduchou funkci `num.month()` . Pak tuto funkci použijeme k vytvoření nového sloupce v dataframe. Nový kód je následující.

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

Nyní spusťte aktualizovaný experiment a použijte výstupní protokol k zobrazení výsledků. Tyto výsledky se zobrazují na obrázku 12.

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

*Obrázek 12. Souhrn datového rámce s dalším sloupcem*

Vypadá to, že všechno funguje. V našem dataframe máme nový sloupec s očekávanými hodnotami.

### <a name="value-transformations"></a>Transformace hodnot

V této části provedeme některé jednoduché transformace na hodnoty v některých sloupcích našeho datového rámce. Jazyk R podporuje skoro libovolné transformace hodnot. [Níže uvedené](#appendixb) odkazy níže obsahují rozsáhlé příklady.

Pokud se podíváte na hodnoty v souhrnech našeho dataframe, měla by se tady zobrazit něco odlišného. Je zmrzlina zmrzlina než mléko vyráběná v Kalifornii? Ne, samozřejmě nemusíte mít žádný smysl, jako by se jednalo o tento fakt jako na některém ze smetany zmrzliny Lovers. Jednotky se liší. Cena je v jednotkách USA – libry, mléko v jednotkách 1 – US libry, zmrzlina v jednotkách 1 000 amerických a domáckých sýrů je v jednotkách 1 000 amerických Libr. Za předpokladu, že zmrzlina v 6,5 librách za galon, můžeme jednoduše provést násobení, aby se tyto hodnoty převedly na stejné jednotky 1 000 Libr.

Pro náš model prognózy používáme model multiplikativní pro vývoj a sezónní úpravu těchto dat. Transformace protokolu nám umožňuje použít lineární model a zjednodušit tento proces. Transformaci protokolu můžeme použít ve stejné funkci, kde se aplikuje násobitel.

V následujícím kódu definujeme novou funkci, `log.transform()` a použijete ji na řádky obsahující číselné hodnoty. Funkce jazyka R `Map()` se používá pro použití `log.transform()` funkce na vybrané sloupce datového rámce. `Map()`je podobná, `apply()` ale umožňuje více než jednomu seznamu argumentů funkce. Všimněte si, že seznam násobitelů poskytuje druhý argument `log.transform()` funkci. `na.omit()`Funkce se používá jako bitová kopie, aby nedošlo k chybějícím nebo nedefinovaným hodnotám v dataframe.

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

Ve funkci je něco nového `log.transform()` . Většina tohoto kódu kontroluje možné problémy s argumenty nebo řešení potíží s výjimkami, které mohou být stále v průběhu výpočtů. Pouze pár řádků tohoto kódu ve skutečnosti provádí výpočty.

Cílem programování v obrannou linií je zabránit selhání jedné funkce, která brání v pokračování zpracování. Náhlé selhání dlouhotrvající analýzy může být pro uživatele poměrně frustrující. Aby k této situaci nedocházelo, je nutné zvolit výchozí návratové hodnoty, které omezují škodu na zpracování pro příjem dat. Také se vytvoří zpráva upozorňující uživatele, že došlo k nějakému problému.

Pokud se nepoužíváte k obrannou linií programování v jazyce R, může se stát, že se veškerý tento kód může zdát příliš náročný. Provede vás hlavními kroky:

1. Je definován vektor čtyř zpráv. Tyto zprávy slouží ke sdělování informací o některých možných chybách a výjimkách, které mohou s tímto kódem nastat.
2. Vrátím hodnotu NEDEF pro každý případ. Existuje mnoho dalších možností, které mohou mít méně vedlejších účinků. Mohl by vracet vektor nul nebo původní vstupní vektor, například.
3. Kontroly jsou spouštěny na argumentech funkce. V každém případě, pokud je zjištěna chyba, je vrácena výchozí hodnota a zpráva je vytvořena `warning()` funkcí. Používám místo toho, aby se `warning()` `stop()` ukončilo provádění, přesně to, co se mi snažím zabránit. Všimněte si, že jsem tento kód napsal ve stylu procedurální, protože v tomto případě se jedná o funkční přístup, který je složitý a zakrývá se.
4. Výpočty protokolu jsou zabaleny `tryCatch()` tak, aby výjimky nezpůsobily náhlé zastavení zpracování. Bez `tryCatch()` většiny chyb vyvolaných funkcí jazyka R má za následek signál stop, který to dělá pouze.

Spusťte tento kód R v experimentu a podívejte se na vytištěný výstup v souboru Output. log. Nyní se zobrazí transformované hodnoty čtyř sloupců v protokolu, jak je znázorněno na obrázku 13.

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

*Obrázek 13. Souhrn transformovaných hodnot v rámci datového rámce.*

Uvidíme hodnoty, které byly transformované. Mléčná produkce nyní výrazně přesáhne veškerou další produkci produktů v mléčném prostředí a znovu volá, že teď se díváte na škálování protokolu.

V tomto okamžiku se vyčistí naše data a budeme připraveni na nějaké modelování. Podívejte se na Shrnutí vizualizace výstup výsledné sady výsledků v modulu [spuštění skriptu jazyka R][execute-r-script] . sloupec ' month ' je ' kategorií ' s 12 jedinečnými hodnotami, a to stejně, jako jsme chtěli.

## <a name="time-series-objects-and-correlation-analysis"></a><a id="timeseries"></a>Objekty časové řady a analýza korelace

V této části prozkoumáme několik základních objektů časových řad jazyka R a analyzujete korelace mezi některými proměnnými. Naším cílem je výstup datového rámce obsahujícího informace o korelační korelaci v několika prodlevy.

Úplný kód R tohoto oddílu je v [MachineLearningSamples-poznámkách/studiu-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="time-series-objects-in-r"></a>Objekty časové řady v R

Jak už bylo zmíněno, časové řady představují řadu hodnot dat indexovaných podle času. Objekty časové řady R slouží k vytvoření a správě časového indexu. Použití objektů časových řad má několik výhod. Objekty Time Series uvolňují z mnoha podrobností o správě hodnot indexu časové řady, které jsou zapouzdřeny v objektu. Kromě toho objekty Time Series umožňují použít řadu metod časové řady pro vykreslování, tisk, modelování atd.

Třída POSIXct Time Series se běžně používá a je relativně jednoduchá. Tato časová osa měří čas od začátku epocha, od 1. ledna 1970. V tomto příkladu použijeme objekty POSIXct Time Series. Mezi další široce používané třídy objektů R časové řady patří XTS, rozšiřitelná časová řada.

### <a name="time-series-object-example"></a>Příklad objektu Time Series

Pojďme začít s naším příkladem. Přetáhněte **Nový** modul [spuštění skriptu jazyka R][execute-r-script] do experimentu. Připojte výstupní port DataSet1. pro existující modul [skriptu Execute r][execute-r-script] ke vstupnímu portu DataSet1. nového modulu [skriptu pro spuštění r][execute-r-script] .

Jak jsme pracovali v prvních příkladech, jak postupovat v příkladu, v některých bodech se zobrazí pouze přírůstkové další řádky kódu R v každém kroku.  

#### <a name="reading-the-dataframe"></a>Čtení datového rámce

Jako první krok si přečteme v dataframe a ujistěte se, že jsme získali očekávané výsledky. Následující kód by měl provést úlohu.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Nyní spusťte experiment. Protokol nového obrazce skriptu pro spuštění jazyka R by měl vypadat jako obrázek 14.

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

*Obrázek 14. Souhrn datového rámce v modulu spuštění skriptu jazyka R.*

Tato data mají očekávané typy a formát. Všimněte si, že sloupec ' month ' je typu faktor a má očekávaný počet úrovní.

#### <a name="creating-a-time-series-object"></a>Vytvoření objektu časové řady

Musíme do našeho datového rámce přidat objekt časové řady. Nahraďte aktuální kód následujícím kódem, který přidá nový sloupec třídy POSIXct.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Teď si Projděte protokol. Měl by vypadat jako obrázek 15.

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

*Obrázek 15. Souhrn objektu dataframe s objektem časové řady*

V souhrnu jsme viděli, že nový sloupec je ve skutečnosti pro třídu POSIXct.

### <a name="exploring-and-transforming-the-data"></a>Zkoumání a transformace dat

Pojďme prozkoumat některé proměnné v této datové sadě. Scatterplot matice je dobrým způsobem, jak vytvořit rychlý vzhled. Nahrazujem `str()` funkci v předchozím kódu R následujícím řádkem.

```R
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Spusťte tento kód a podívejte se, co se stane. Vykreslení vyprodukované na portu zařízení R by mělo vypadat jako obrázek 16.

![Matice scatterplot vybraných proměnných](./media/r-quickstart/fig17.png)

*Obrázek 16. Matice scatterplot vybraných proměnných*

Mezi těmito proměnnými jsou některé struktury s lichým vzhledem. To může být způsobeno trendy v datech a ze skutečnosti, že proměnné jsme nemuseli standardizovaně roznikat.

### <a name="correlation-analysis"></a>analýza korelací.

Aby bylo možné provést analýzu korelace, musíme použít jak de-LINTREND, tak i standardizovat proměnné. Můžeme jednoduše použít `scale()` funkci R, která obě středy a škáluje proměnné. Tato funkce může být dobře spouštěna rychleji. Chci si ale zobrazit příklad programu obrannou linií v jazyce R.

`ts.detrend()`Níže uvedená funkce provádí obě tyto operace. Následující dva řádky kódu detrendují data a pak tyto hodnoty standardizovat.

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

Ve funkci je něco nového `ts.detrend()` . Většina tohoto kódu kontroluje možné problémy s argumenty nebo řešení potíží s výjimkami, které mohou být stále v průběhu výpočtů. Pouze pár řádků tohoto kódu ve skutečnosti provádí výpočty.

V transformacích hodnot jsme už probrali příklad programování v obrannou linií. Oba bloky výpočtů jsou zabaleny do `tryCatch()` . V případě některých chyb má smysl vrátit původní vstupní vektor a v ostatních případech vrátí Vektor nul.  

Všimněte si, že lineární regrese, která se používá pro detrendování, je regrese časové řady. Proměnná prediktivního je objekt časové řady.  

Jakmile `ts.detrend()` je tato definice definovaná, použijeme ji pro proměnné zájmu v našem dataframe. Výsledný seznam vytvořený pomocí `lapply()` pro datový rámec data musí být převeden pomocí `as.data.frame()` . Kvůli obrannou linií aspektům pro `ts.detrend()` neúspěšné zpracování jedné z proměnných nebrání správnému zpracování ostatních.  

Poslední řádek kódu vytvoří scatterplot. Po spuštění kódu R se výsledky scatterplot zobrazí na obrázku 17.

![Scatterplot z netrendových a standardizovaných časových řad](./media/r-quickstart/fig18.png)

*Obrázek 17. Scatterplot z netrendové a standardizované časové řady.*

Tyto výsledky můžete porovnat s hodnotami uvedenými na obrázku 16. S odebraným trendem a s proměnnými, které jsou standardizovány, se v relacích mezi těmito proměnnými zobrazuje struktura s menším množstvím.

Kód pro výpočet korelace jako objektů R CCF je následující.

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

*Obrázek 18. Seznam objektů CCF z analýzy párových korelace.*

Pro každou prodlevu existuje korelační hodnota. Žádná z těchto hodnot korelace není dostatečně velká, aby mohla být významná. Můžeme proto uzavřít, abychom každou proměnnou mohli modelovat nezávisle.

### <a name="output-a-dataframe"></a>Výstup datového rámce
Vypočítali jsme relace párových relací jako seznam objektů CCF jazyka R. To představuje bitovou příčinu problému, protože výstupní port výsledné sady dat skutečně vyžaduje datový rámec. Kromě toho objekt CCF je sám seznam a chceme, aby v prvním elementu tohoto seznamu byly pouze hodnoty, korelace v různých prodlevy.

Následující kód extrahuje hodnoty prodlevy ze seznamu objektů CCF, které jsou vlastními seznamy.

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

První řádek kódu je trochu obtížné a některé vysvětlení vám může porozumět. Při práci z interního prostředí máme následující:

1. Operátor '**[**' s argumentem '**1**' vybere vektor korelace na prodlevy z prvního prvku seznamu objektů CCF.
2. `do.call()`Funkce aplikuje `rbind()` funkci na prvky seznamu, kterou vrátí `lapply()` .
3. `data.frame()`Funkce převede výsledek vyprodukovaný `do.call()` pro datový rámec.

Všimněte si, že názvy řádků jsou ve sloupci datového rámce. Tím se zachová názvy řádků při výstupu ze [skriptu Execute jazyka R][execute-r-script].

Spuštění kódu vytvoří výstup uvedený na obrázku 19 při **vizualizaci** výstupu na portu datové sady výsledků. Názvy řádků jsou v prvním sloupci, jak je určeno.

![Výstup výsledků analýzy korelace](./media/r-quickstart/fig20.png)

*Obrázek 19. Výstup výsledků analýzy korelace.*

## <a name="time-series-example-seasonal-forecasting"></a><a id="seasonalforecasting"></a>Příklad časové řady: sezónní prognózování

Naše data jsou teď ve formě vhodné k analýze a zjistili jsme, že mezi proměnnými neexistují žádné významné korelace. Pojďme se přesunout a vytvořit model prognózy časových řad. Pomocí tohoto modelu budeme předpovědět, že se v Kalifornii po dobu 12 měsíců 2013.

Náš model prognózy bude mít dvě komponenty, komponentu trendu a sezónní komponentu. Kompletní prognóza je produktem těchto dvou součástí. Tento typ modelu je známý jako multiplikativní model. Alternativou je model doplňku. Transformaci protokolu jsme už použili na proměnné zájmu, což usnadňuje tuto analýzu.

Úplný kód R tohoto oddílu je v [MachineLearningSamples-poznámkách/studiu-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="creating-the-dataframe-for-analysis"></a>Vytvoření datového rámce pro analýzu

Začněte přidáním nového modulu **new** [skriptu pro spuštění R][execute-r-script] do experimentu. Připojte výstup **výsledné datové sady** existujícího modulu [skriptu pro spuštění R][execute-r-script] k **DataSet1.** vstupu nového modulu. Výsledek by měl vypadat přibližně takto: obrázek 20.

![Experiment s novým modulem pro spuštění skriptu jazyka R se přidal.](./media/r-quickstart/fig21.png)

*Obrázek 20. Došlo k pokusu o přidání nového modulu skriptu pro spuštění jazyka R.*

Stejně jako u analýzy korelace jsme právě dokončili přidání sloupce s objektem POSIXct Time Series. Následující kód provede pouze to.

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

V důsledku toho jsme připraveni začít s analýzou.

### <a name="create-a-training-dataset"></a>Vytvoření datové sady školení

S vytvořeným datovým rámcem musíme vytvořit školicí datovou sadu. Tato data budou zahrnovat všechna pozorování kromě posledních 12, roku 2013, což je naše testovací datová sada. Následující kód podmnožinou datového rámce a vytvoří parcely produkčních a cenových proměnných pro mléčné výrobky. Vytvořím zkusnou řadu čtyř produkčních a cenových proměnných. Anonymní funkce se používá k definování některých rozšíření pro vykreslení a pak iterování nad seznamem dalších dvou argumentů s `Map()` . Pokud si myslíte, že smyčka for by v tomto případě fungovala správně, je to správné. Ale vzhledem k tomu, že R je funkční jazyk, se mi zobrazuje funkční přístup.

```R
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

Spuštění kódu vytvoří řadu časových řad z výstupu zařízení R, které vidíte na obrázku 22. Všimněte si, že časová osa je v jednotkách dat, skvělé výhody metody grafu Time Series.

![První z časů řady Brna pro produkci mléčných a cenových dat v Kalifornii](./media/r-quickstart/unnamed-chunk-161.png)

![Druhá z časů řady Brna pro produkci mléčných a cenových dat v Kalifornii](./media/r-quickstart/unnamed-chunk-162.png)

![Třetí z řady časových řad v Kalifornii pro produkci mléčných a cenových údajů](./media/r-quickstart/unnamed-chunk-163.png)

![Čtvrtá řada časových řad v Kalifornii pro produkci dojnic a data o cenách](./media/r-quickstart/unnamed-chunk-164.png)

*Obrázek 22. Časová řada řady Kalifornie pro produkci mléčných a cenových dat v Kalifornii.*

### <a name="a-trend-model"></a>Model trendu

Když jste vytvořili objekt časové řady a museli jste se podívat na data, začali začít sestavovat model trendu pro data o produkci mléka v Kalifornii. Můžeme to provést s regresí časových řad. Je ale jasné, že z tohoto grafu nebudeme potřebovat více než sklon a zachytit k přesnému modelování pozorovaného trendu v školicích datech.

Vzhledem k malé škále dat sestavíme model pro vývoj v RStudio a pak vyjmete a vložíte výsledný model do Azure Machine Learning Studio (Classic). RStudio poskytuje interaktivní prostředí pro tento typ interaktivní analýzy.

Při prvním pokusu se pokusíte o polynomickou regresi s pravomocemi až na 3. Existují reálné nebezpečí přebudování těchto druhů modelů. Proto je nejlepší vyhnout se podmínkám vysokého řádu. `I()`Funkce brání interpretaci obsahu (interpretuje obsah "tak, jak je") a umožňuje napsat doslova interpretovaná funkce v regresní rovnici.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Tím vygenerujete následující.

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

Na základě hodnot P ( `Pr(>|t|)` ) v tomto výstupu zjistíme, že čtvercový termín nemusí být významný. Tuto funkci použijeme `update()` k úpravě tohoto modelu vyřazením čtvercového období.

```R
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Tím vygenerujete následující.

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

To vypadá lépe. Všechny tyto výrazy jsou významné. Hodnota 2E-16 je však výchozí hodnotou a neměla by být pořízena příliš vážně.  

Jako správnosti test si probereme časovou řadu mléčných dat z Brna v Kalifornii pomocí zobrazené křivky trendu. Přidal (a) jsem následující kód v Azure Machine Learning Studio (Classic) [spuštění modelu skriptu R][execute-r-script] (ne RStudio) pro vytvoření modelu a vytvoření grafu. Výsledek je znázorněn na obrázku 23.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Údaje o produkci mléčných mléka v Kalifornii pomocí zobrazeného modelu trendu](./media/r-quickstart/unnamed-chunk-18.png)

*Obrázek 23. Údaje o produkci mléčných mléka v Kalifornii se zobrazeným modelem trendu.*

Vypadá to, že model trendu přesně odpovídá datům. Dále se zdá, že se nejeví jako nedostatečné množství, jako je například lichá Wiggles v křivce modelu.  

### <a name="seasonal-model"></a>Sezónní model

S modelem trendu je potřeba nasdílet a zahrnovat sezónní účinky. Měsíc v roce použijeme jako fiktivní proměnnou v lineárním modelu pro zachycení měsíčního efektu. Všimněte si, že při zavedení proměnných faktoru do modelu nesmí být zachytávání vypočítáno. Pokud to neuděláte, vzorec se zachová a R vynechá jeden z požadovaných faktorů, ale ponechá termín zachytávání.

Vzhledem k tomu, že máme uspokojivý model trendů, můžeme k `update()` Přidání nových podmínek do existujícího modelu použít funkci. Výraz-1 ve vzorci aktualizace zruší termín zachycení. Pokračuje se v RStudio a teď:

```R
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Tím vygenerujete následující.

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

Zjistili jsme, že model už nemá termín zachycení a má 12 významných měsíčních faktorů. To je přesně to, co jsme chtěli vidět.

Pojďme udělat další graf časových řad v rámci produkčních dat v Kalifornii a zjistit, jak dobře funguje sezónní model. Přidal (a) jsem do [skriptu jazyka R][execute-r-script] Azure Machine Learning Studio (Classic) následující kód, který vytvoří model a vytvoří vykreslení.

```R
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

Spuštění tohoto kódu v Azure Machine Learning Studio (Classic) vytvoří vykreslení znázorněné na obrázku 24.

![Výroba mléka v Kalifornii s modelem včetně sezónních účinků](./media/r-quickstart/unnamed-chunk-20.png)

*Obrázek 24. V Kalifornii mléčné výroby s modelem včetně sezónních účinků.*

Místo toho se hodí pro data uvedená na obrázku 24. Trend i sezónní účinek (měsíční variace) vypadají jako rozumné.

Jako další kontrolu našeho modelu se podívejme na zbytky. Následující kód vypočítá předpovězené hodnoty z našich dvou modelů, vypočítá zbytky pro sezónní model a potom tyto zbytky vykreslí pro školicí data.

```R
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

Zbytkový graf je znázorněn na obrázku 25.

![Zbytky sezónního modelu pro školicí data](./media/r-quickstart/unnamed-chunk-21.png)

*Obrázek 25. Zbytky sezónního modelu pro školicí data.*

Tyto zbytky vypadají rozumným způsobem. Neexistuje žádná konkrétní struktura, s výjimkou účinku 2008-2009 recesí, který náš model nepoužívá zejména k tomu.

Vykreslení znázorněné na obrázku 25 je užitečné pro detekci všech vzorů závislých na čase ve zbytkech. Explicitní přístup k výpočetnímu prostředí a vykreslení zbytků, které jsem používá, umístí zbytky v časovém pořadí na vykreslení. Pokud na druhé straně jsem vykreslil `milk.lm$residuals` , vykreslení by nebylo v časovém pořadí.

Můžete také použít `plot.lm()` k vytvoření řady diagnostických parcel.

```R
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Tento kód vytváří řadu diagnostických zobrazení na obrázku 26.

![První z diagnostických parcel pro sezónní model](./media/r-quickstart/unnamed-chunk-221.png)

![Druhá z diagnostických parcel pro sezónní model](./media/r-quickstart/unnamed-chunk-222.png)

![Třetí z diagnostických parcel pro sezónní model](./media/r-quickstart/unnamed-chunk-223.png)

![Čtvrtá část diagnostických nástrojů pro sezónní model](./media/r-quickstart/unnamed-chunk-224.png)

*Obrázek 26. Diagnostické parcely pro sezónní model.*

V těchto zkusných grafech je několik vysoce kvalitních bodových bodů, ale nic nezpůsobuje Skvělé obavy. Dále uvidíte z normálního vykreslování Q-Q, že zbytky jsou blízko normálního rozdělení, což je důležitý předpoklad pro lineární modely.

### <a name="forecasting-and-model-evaluation"></a>Prognózování a vyhodnocení modelu

K dokončení našeho příkladu je tu jenom jedna věc. Musíme vypočítat předpovědi a změřit chybu proti skutečným datům. Naše předpověď bude na 12 měsíců od 2013. Pro tuto prognózu můžeme vypočítat chybovou míru na skutečná data, která nejsou součástí naší datové sady školení. Kromě toho můžeme porovnat výkon na 18 let školicích dat až po dobu 12 měsíců testovacích dat.  

K měření výkonu modelů časových řad se používá řada metrik. V našem případě použijeme chybu root střed_hodn Square (RMS). Následující funkce vypočítá chybu služby RMS mezi dvěma řadami.  

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

Stejně jako u `log.transform()` funkce, kterou jsme provedli v části "transformace hodnot", je v této funkci dost velký počet kontrol chyb a kód pro obnovení výjimek. Použité zásady jsou stejné. Práce se provádí na dvou místech zabalených do `tryCatch()` . Nejdřív se exponentiated časová řada, protože jsme pracovali s protokoly těchto hodnot. Za druhé se počítá skutečná Chyba služby RMS.  

Je vybavená funkcí k měření chyby služby RMS a vytvoří a vytvoří výstup dataframe obsahující chyby RMS. Budeme zahrnovat výrazy pro model trendů a kompletní model s sezónními faktory. Následující kód provede úlohu pomocí dvou lineárních modelů, které jsme sestavili.

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

Spuštění tohoto kódu vytvoří výstup uvedený na obrázku 27 na výstupním portu výsledné sady dat.

![Porovnání chyb RMS pro modely](./media/r-quickstart/fig26.png)

*Obrázek 27. Porovnání chyb služby RMS pro modely.*

Z těchto výsledků vidíte, že přidání sezónních faktorů do modelu snižuje chybu služby RMS významně. Není příliš překvapivě, Chyba služby RMS pro školicí data je trochu méně než u prognózy.

## <a name="guide-to-rstudio-documentation"></a><a id="appendixa"></a>Příručka k dokumentaci k RStudio

RStudio je poměrně dobře zdokumentovaná. Tady jsou některé odkazy na klíčové oddíly dokumentace k RStudio, které vám pomohou začít.

* **Vytváření projektů** – kód R můžete organizovat a spravovat v projektech pomocí RStudio. Podrobnosti najdete v tématu [použití projektů](https://support.rstudio.com/hc/articles/200526207-Using-Projects) . Doporučujeme vám postupovat podle těchto pokynů a vytvořit projekt pro příklady kódu R v tomto článku.  
* **Úprava a spuštění kódu r** -RStudio poskytuje integrované prostředí pro úpravy a spouštění kódu r. Podrobnosti najdete v tématu [Úpravy a spouštění kódu](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code) .
* **Ladění** – RStudio zahrnuje výkonné funkce ladění. Další informace o těchto funkcích najdete v tématu [ladění pomocí RStudio](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio) . Informace o funkcích řešení potíží s zarážkou najdete v tématu [řešení potíží s zarážkou](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting).

## <a name="further-reading"></a><a id="appendixb"></a>Další čtení

Tento kurz programování v R se zabývá základními informacemi o tom, co potřebujete k používání jazyka R s Azure Machine Learning Studio (Classic). Pokud R neznáte, jsou v CRAN k dispozici dva úvody:

* [R pro začátečníky](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) by Emmanuel Paradis je dobrým místem, kde začít.  
* [Úvod do jazyka R](https://cran.r-project.org/doc/manuals/R-intro.html) pomocí W. N. Venables et VŠ. přejde do trochu větší hloubky.

K dispozici je mnoho knih v jazyce R, které vám pomůžou začít. Tady je několik užitečných:

* **Obrázek programování v jazyce r: prohlídka statistického návrhu softwaru pomocí programu** Norman Matloff je vynikající Úvod do programování v jazyce r.  
* **R kuchařka** podle Paul Teetor poskytuje přístup k problému a řešení pro použití R.  
* **R v akci** Robert Kabacoff je další užitečnou úvodní knihou. Doprovodný [web s rychlým R](https://www.statmethods.net/) je užitečným prostředkem.
* **R Inferno** by se překvapivěou poradním sloupkuou knihou, která se zabývá několika obtížemi a obtížnémi tématy, která se dají při programování v jazyce r setkat. Kniha je k dispozici zdarma na adrese [R Inferno](https://www.burns-stat.com/documents/books/the-r-inferno/).
* Pokud chcete hluboko podrobně do pokročilých témat v jazyce R, podívejte se na příručku **Advanced R** by Hadley Wickham. Online verze této knihy je k dispozici zdarma na adrese [http://adv-r.had.co.nz/](http://adv-r.had.co.nz/) .

Katalog balíčků časových řad R se dá najít v [zobrazení úloh Cran: analýza časových řad](https://cran.r-project.org/web/views/TimeSeries.html). Informace o konkrétních balíčcích objektů časových řad najdete v dokumentaci k tomuto balíčku.

**Úvodní časová řada** knih s R podle Paul Cowpertwait a Andrew Metcalfe představuje úvod k použití R pro analýzu časových řad. Mnoho dalších teoretických textů poskytuje příklady jazyka R.

Tady je několik skvělých internetových prostředků:

* DataCamp učí R v pohodlí vašeho prohlížeče s využitím lekcí a výukových cvičení. K dispozici jsou interaktivní kurzy k nejnovějším technikám a balíčkům jazyka R. Využijte bezplatný [Interaktivní kurz jazyka R](https://www.datacamp.com/courses/introduction-to-r).
* [Naučte se programování v jazyce R, což je konečný průvodce](https://www.programiz.com/r-programming) z Programiz.
* Rychlý [kurz R](https://www.cyclismo.org/tutorial/R/) od irské černé z Clarkson University.
* K dispozici jsou více než 60 prostředků R [, které jsou v horních prostředcích jazyka r k dispozici pro zlepšení dovedností dat](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html)

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
