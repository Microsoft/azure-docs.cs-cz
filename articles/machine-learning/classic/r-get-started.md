---
title: Použití R s Machine Learning Studio (Classic) – Azure
description: Tento kurz programovacího jazyka R vám umožní začít s Azure Machine Learning Studio (Classic) v jazyce R a vytvořit řešení prognózy.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: b7c442aaf6484e8e47bd6d00c91023fba43af75d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325025"
---
# <a name="get-started-with-azure-machine-learning-studio-classic-in-r"></a>Začínáme s Azure Machine Learning Studio (Classic) v R

**platí pro:** ![ Ano ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ bez ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


<!-- Stephen F Elston, Ph.D. -->
V tomto kurzu se naučíte, jak pomocí Azure Machine Learning Studio (Classic) vytvořit, otestovat a spustit kód R. Na konci budete mít kompletní řešení předpovědi.

> [!div class="checklist"]
> * Vytvořte kód pro čištění a transformaci dat.
> * Analyzujte korelace mezi několika proměnnými v naší datové sadě.
> * Vytvořte sezónní model předpovědi časových řad pro produkci mléka.


Machine Learning Studio (Classic) obsahuje mnoho výkonného strojového učení a modulů manipulace s daty. Pomocí programovacího jazyka R poskytuje tato kombinace škálovatelnost a snadné nasazení Machine Learning Studio (Classic) s flexibilitou a hloubkovou analýzou jazyka R.

Prognózování je široce zaměstnaná a užitečná analytická metoda. Běžný používá rozsah od předvídání prodejů sezónních položek a určení optimálních úrovní zásob pro předpověď makroekonomických proměnných. Prognózy se obvykle provádějí s modely časových řad. Data časové řady jsou data, ve kterých mají hodnoty časový index. Časový index může být pravidelný, například každý měsíc nebo každou minutu. Časový index může být také nepravidelný. Model časových řad je založený na datech časových řad. Programovací jazyk R obsahuje flexibilní rozhraní a rozsáhlou analýzu pro data časových řad.

## <a name="get-the-data"></a>Získání dat

V tomto kurzu použijete data o produkci a cenách v Kalifornii v Kalifornii, které zahrnuje měsíční informace o produkci několika mléčných produktů a cenu mléčného tuku, což je srovnávací zboží.

Data použitá v tomto článku spolu se skripty jazyka R lze stáhnout z [MachineLearningSamples-poznámkových bloků/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples). Data v souboru `cadairydata.csv` byla původně syntetizovaná z informací, které jsou k dispozici na [webu trhy](https://dairymarkets.com)University of Wisconsin dojnic.

## <a name="interact-with-r-language-in-machine-learning-studio-classic"></a><a id="mlstudio"></a>Interakce s jazykem R v Machine Learning Studio (Classic)

V této části se seznámíte se základy práce s programovacím jazykem R v prostředí Machine Learning Studio (Classic). Jazyk R poskytuje výkonný nástroj pro vytváření přizpůsobených modulů analýzy a manipulace s daty v prostředí Machine Learning Studio (Classic).

RStudio použijeme k vývoji, testování a ladění kódu R v malém měřítku. Tento kód se pak vyjme a vloží do modulu rutiny [spouštěného skriptu jazyka R][execute-r-script] , který je připravený ke spuštění v Machine Learning Studio (Classic).

### <a name="the-execute-r-script-module"></a>Modul spuštění skriptu jazyka R

V rámci Machine Learning Studio (Classic) se skripty R spouštějí v modulu [spuštění skriptu jazyka r][execute-r-script] . Tady je zobrazený příklad modulu [spuštění skriptu R][execute-r-script] v Machine Learning Studio (Classic).

 ![Snímek obrazovky zobrazující programovací jazyk R: modul spuštění skriptu jazyka R vybraný v Machine Learning Studio (Classic).](./media/r-quickstart/fig1.png)

Předchozí obrázek ukazuje některé klíčové části prostředí Machine Learning Studio (Classic) pro práci s modulem [skriptu spouštěným R][execute-r-script] :

* Moduly v experimentu se zobrazí v prostředním podokně.
* Horní část pravého podokna obsahuje okno, pomocí kterého můžete zobrazit a upravit skripty jazyka R.
* Dolní část pravého podokna zobrazuje některé vlastnosti [skriptu Execute R][execute-r-script]. Protokoly chyb a výstupu můžete zobrazit tak, že vyberete příslušné oblasti v tomto podokně.

Ve zbývající části tohoto článku se podíváme na [skript Execute R][execute-r-script] podrobněji.

Když pracujete se složitými funkcemi jazyka R, doporučujeme, abyste v RStudio mohli upravovat, testovat a ladit. Stejně jako u jakéhokoli vývoje softwaru rozšíříte kód přírůstkově a otestujete ho v malých, jednoduchých testovacích případech. Potom tyto funkce vyjměte a vložte do okna skript R v modulu [spuštění skriptu jazyka r][execute-r-script] . Tento přístup vám umožní využít integrované vývojové prostředí (IDE) RStudio a výkon Machine Learning Studio (Classic).

#### <a name="execute-r-code"></a>Spustit kód R

Jakýkoli kód R v modulu [spouštění skriptu jazyka r][execute-r-script] se spustí při spuštění experimentu výběrem tlačítka **Spustit** . Po dokončení spuštění se na ikoně [Spustit skript jazyka R][execute-r-script] zobrazí zaškrtnutí.

#### <a name="defensive-r-coding-for-machine-learning-studio-classic"></a>Obrannou linií R kódování pro Machine Learning Studio (Classic)

Pokud vyvíjíte kód R pro, řekněme, že webová služba používá Machine Learning Studio (Classic), měli byste bez omezení naplánovat, jak váš kód bude pracovat s neočekávaným vstupem a výjimkami dat. Abychom zachovali přehlednost, nezahrnuli jsme do většiny zobrazených příkladů kódu spoustu možností kontroly a zpracování výjimek. Jak budeme pokračovat, poskytneme vám několik příkladů funkcí pomocí schopnosti zpracování výjimek jazyka R.

Pokud potřebujete úplnější zpracování výjimek R, přečtěte si příslušné části knihy podle Wickham, které jsou uvedené [dál](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Ladění a testování R v Machine Learning Studio (Classic)

Otestujte a ladit kód R v malém měřítku v RStudio. Existují také případy, kdy budete potřebovat sledovat problémy s kódem R v samotném [spuštění skriptu jazyka r][execute-r-script] . Kromě toho je dobrým zvykem kontrolovat výsledky v Machine Learning Studio (Classic).

Výstup z provádění kódu R a na platformě Machine Learning Studio (Classic) se najde primárně v části Output. log. Některé další informace jsou v Error. log.

Pokud při spuštění kódu R dojde k chybě v Machine Learning Studio (Classic), měli byste se podívat na chybu. log v prvním postupu. Tento soubor může obsahovat užitečné chybové zprávy, které vám pomůžou pochopit a opravit chybu. Chcete-li zobrazit Error. log, vyberte **Zobrazit protokol chyb** v podokně vlastnosti pro [skript spustit R][execute-r-script] , který obsahuje chybu.

Například jsme spustili následující kód jazyka R s nedefinovanou proměnnou y v modulu [spuštění skriptu jazyka r][execute-r-script] .

```r
x <- 1.0
z <- x + y
```

Spuštění tohoto kódu se nepovede, takže dojde k chybě. Výběr možnosti **Zobrazit protokol chyb** v podokně vlastnosti vytvoří následující zobrazení.

  ![Snímek obrazovky, který zobrazuje automaticky otevírané okno s chybovou zprávou](./media/r-quickstart/fig2.png)

Vypadá to, že je potřeba vyhledat výstup. log a zobrazit chybovou zprávu R. Vyberte modul [spuštění skriptu jazyka R][execute-r-script] a potom v podokně vlastností vpravo vyberte položku **Zobrazit výstup. log** . Otevře se nové okno prohlížeče a zobrazí se následující chybová zpráva.

```output
[Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
---------- Start of error message from R ----------
object 'y' not found


object 'y' not found
----------- End of error message from R -----------
```

Tato chybová zpráva neobsahuje žádné překvapením a jasně identifikuje problém.

Chcete-li zkontrolovat hodnotu libovolného objektu v R, můžete tyto hodnoty vytisknout do souboru Output. log. Pravidla pro zkoumání hodnot objektu jsou v podstatě stejná jako v interaktivní relaci jazyka R. Například pokud zadáte název proměnné na řádku, hodnota objektu bude vytištěna do souboru Output. log.

#### <a name="packages-in-machine-learning-studio-classic"></a>Balíčky v Machine Learning Studio (klasické)

Machine Learning Studio (Classic) obsahuje více než 350 předinstalovaných balíčků jazyka R. K načtení seznamu předinstalovaných balíčků můžete použít následující kód v modulu [spuštění skriptu jazyka R][execute-r-script] .

```r
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Pokud v tuto chvíli nerozumíte poslednímu řádku tohoto kódu, přečtěte si. Ve zbývající části tohoto článku probereme v prostředí Machine Learning Studio (Classic) velké množství R.

### <a name="introduction-to-rstudio"></a>Úvod do RStudio

RStudio je široce používané integrované vývojové prostředí (IDE) pro R. RStudio použijeme k úpravě, testování a ladění kódu R použitého v tomto průvodci. Po otestování a přípravě kódu R můžete z editoru RStudio vyjímat a vkládat do Machine Learning Studio (Classic) spustit modul skriptu jazyka [r][execute-r-script] .

Pokud nemáte na stolním počítači nainstalovaný programovací jazyk R, udělejte to teď. Bezplatné stahování Open Source jazyka R je k dispozici v [komplexní síti archivu r (Cran)](https://www.r-project.org/). Soubory ke stažení jsou k dispozici pro Windows, Mac OS a Linux/UNIX. Vyberte okolní zrcadlo a postupujte podle pokynů ke stažení. Kromě toho CRAN obsahuje mnoho užitečných analýz a datových balíčků pro manipulaci s daty.

Pokud s RStudio začínáte, měli byste si stáhnout a nainstalovat desktopovou verzi. Soubory ke stažení RStudio pro Windows, Mac OS a Linux/UNIX najdete na adrese [RStudio](http://www.rstudio.com/products/RStudio/). Postupujte podle pokynů uvedených k instalaci RStudio na stolní počítač.

Kurz Úvod do RStudio je k dispozici v [části použití prostředí IDE RStudio](https://support.rstudio.com/hc/sections/200107586-Using-RStudio).

Další informace o použití RStudio najdete v [příručce k dokumentaci RStudio](#appendixa).

## <a name="get-data-in-and-out-of-the-execute-r-script-module"></a><a id="scriptmodule"></a>Načtení dat z modulu spuštění skriptu R a z něj

V této části probereme, jak získat data do a z modulu [spuštění skriptu jazyka R][execute-r-script] . Také si probereme, jak zpracovat různé datové typy, které jsou čteny do modulu [spuštění skriptu jazyka R][execute-r-script] a z něj.

Úplný kód této části je v [MachineLearningSamples-poznámkách/studiu-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="load-and-check-data"></a>Načíst a ověřit data

#### <a name="load-the-dataset"></a><a id="loading"></a>Načíst datovou sadu

Začneme tak, že soubor **csdairydata.csv** načteme do Machine Learning Studio (Classic).

1. Spusťte prostředí Machine Learning Studio (Classic).
1. V levém dolním rohu obrazovky vyberte **+ Nový** a vyberte **datová sada**.
1. Vyberte **z místního souboru** a potom vyberte **Procházet** a vyberte soubor.
1. Ujistěte se, že jste jako typ pro datovou sadu vybrali **obecný soubor CSV s hlavičkou (. csv)** .
1. Zaškrtněte políčko.
1. Po nahrání datové sady byste měli po výběru karty datové **sady** zobrazit novou datovou sadu.

#### <a name="create-an-experiment"></a>Vytvoření experimentu

Teď, když máme nějaká data v Machine Learning Studio (Classic), musíme vytvořit experiment pro analýzu.  

1. V levém dolním rohu obrazovky vyberte **+ Nový** a vyberte **experimentovat**  >  **prázdný experiment**.
1. Pojmenujte experiment tak, že vyberete a upravíte experiment, který jste **vytvořili v** nadpisu v horní části stránky. Můžete ho například změnit na **analýzu mléčných mléka certifikační autority**.
1. Vlevo na stránce experimenty vyberte **uložené datové sady**  >  **Moje datové sady**. Měl by se zobrazit soubor **cadairydata.csv** , který jste nahráli dříve.
1. Přetáhněte do experimentu **csdairydata.csv datovou sadu** .
1. V poli **Hledat položky experimentu** v horní části levého podokna zadejte příkaz [Spustit skript jazyka R][execute-r-script]. Modul se zobrazí v seznamu hledání.
1. Přetáhněte na paletu modul [spuštění skriptu jazyka R][execute-r-script] .
1. Připojte výstup **csdairydata.csv datové sady** ke vstupu úplně vlevo ( **DataSet1.** ) [skriptu Execute jazyka R][execute-r-script].
1. Vyberte **Uložit**.

V tomto okamžiku by experiment měl vypadat podobně jako v tomto příkladu.

![Diagram znázorňující experimenty analýzy mléčných dojnic s datovou sadou a spuštěným modulem skriptu jazyka R.](./media/r-quickstart/fig3.png)


#### <a name="check-on-the-data"></a>Kontrolovat data

Pojďme se podívat na data, která jsme načetli do našeho experimentu. V experimentu vyberte výstup **cadairydata.csv datové sady** a vyberte **vizualizovat**. Mělo by se zobrazit něco jako v tomto souhrnu.

![Snímek obrazovky, který zobrazuje souhrn cadairydata.csv datové sady.](./media/r-quickstart/fig4.png)

Toto zobrazení ukazuje spoustu užitečných informací. Můžeme zobrazit prvních několik řádků datové sady. Když vybereme sloupec, v části **Statistika** se zobrazí další informace o sloupci. Například řádek **typ funkce** zobrazuje, jaké typy dat Machine Learning Studio (Classic) přiřazené sloupci. Než začnete provádět žádnou závažnou práci, zkontrolujte toto zobrazení.

### <a name="first-r-script"></a>První skript R

Pojďme vytvořit jednoduchý první skript R, který vám umožní experimentovat se v Machine Learning Studio (Classic). Vytvořili jsme a otestovali jsme následující skript v RStudio.

```r
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

Nyní musíme tento skript přenést do Machine Learning Studio (Classic). Můžete vyjmout a vložit, ale v takovém případě přeneste skript R pomocí souboru ZIP.

### <a name="data-input-to-the-execute-r-script-module"></a>Vstup dat do modulu spuštění skriptu jazyka R

Pojďme se podívat na vstupy pro modul [spuštění skriptu jazyka R][execute-r-script] . V tomto příkladu přečteme data o mlékárnách z Brna do modulu [spuštění skriptu jazyka R][execute-r-script] .

Existují tři možné vstupy pro modul [spuštění skriptu jazyka R][execute-r-script] . V závislosti na vaší aplikaci můžete použít libovolný nebo všechny tyto vstupy. Můžete také použít skript R, který neprovádí žádné vstupy.

Pojďme se podívat na každý z těchto vstupů zleva doprava. Můžete zobrazit názvy všech vstupů tak, že umístíte kurzor na vstup a přečtete popis.

#### <a name="script-bundle"></a>Sada skriptů

Vstup sady prostředků skriptu umožňuje předat obsah souboru zip do modulu [spuštění skriptu jazyka R][execute-r-script] . K načtení obsahu souboru zip do kódu jazyka R můžete použít jeden z následujících příkazů.

```r
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Machine Learning Studio (Classic) zpracovává soubory v souboru ZIP jako v případě, že se nacházejí v adresáři src/Directory, takže je nutné názvy souborů pro tento název adresáře předponovat. Například pokud soubor zip obsahuje soubory `yourfile.R` a `yourData.rdata` v kořenové složce souboru zip, tyto soubory vyřešíte jako `src/yourfile.R` a `src/yourData.rdata` při použití `source` a `load` .

Již jsme probrali načítání datových sad v [načtení datové](#loading)sady. Po vytvoření a otestování skriptu jazyka R zobrazeného v předchozí části proveďte následující kroky.

1. Uložte skript R do. Soubor R. Zavoláme soubor skriptu **simpleplot. R**. Tady je co je v souboru:

   ```r
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

1. Vytvořte soubor zip a zkopírujte skript do tohoto souboru ZIP. V systému Windows můžete kliknout pravým tlačítkem na soubor a vybrat **Odeslat do**  >  **komprimované složky**. Tato akce vytvoří nový soubor zip, který obsahuje **simpleplot. Soubor R** .

1. Přidejte soubor do **datových sad** v Machine Learning Studio (Classic) a zadejte typ jako **zip**. V datových sadách by se teď měl zobrazit soubor zip.

1. Přetáhněte soubor zip z **datových sad** na **plátno ml Studio (Classic)**.

1. Připojte výstup ikony **dat zip** ke vstupu **sady prostředků skriptu** [spustit modul skriptu jazyka R][execute-r-script] .

1. Zadejte `source()` funkci s názvem souboru zip do okna Code pro modul [spouštění skriptu jazyka R][execute-r-script] . V tomto případě jsme zadali `source("src/simpleplot.R")` .

1. Vyberte **Uložit**.

Po dokončení těchto kroků spustí modul [r skript spustit][execute-r-script] skript r v souboru ZIP při spuštění experimentu. V tomto okamžiku by experiment měl vypadat podobně jako v tomto příkladu.

![Diagram, který znázorňuje experiment pomocí skriptu zip jazyka R.](./media/r-quickstart/fig6.png)

#### <a name="dataset1"></a>DataSet1.

Pomocí vstupu DataSet1. můžete předat obdélníkovou tabulku dat kódu jazyka R. V našem jednoduchém skriptu `maml.mapInputPort(1)` načte funkce data z portu 1. Tato data se pak přiřazují k názvu proměnné datového rámce v kódu. V našem jednoduchém skriptu provede první řádek kódu přiřazení.

```r
cadairydata <- maml.mapInputPort(1)
```

Spusťte experiment výběrem tlačítka **Spustit** . Po dokončení spuštění vyberte modul skriptu pro [spuštění R][execute-r-script] a v podokně Vlastnosti vyberte **Zobrazit výstupní protokol** . V prohlížeči by se měla zobrazit nová stránka, která zobrazuje obsah výstupního souboru. log. Když se posunete dolů, měla by se zobrazit něco podobného jako v následujícím výstupu.

```output
[ModuleOutput] InputDataStructure
[ModuleOutput]
[ModuleOutput] {
[ModuleOutput]  "InputName":Dataset1
[ModuleOutput]  "Rows":228
[ModuleOutput]  "Cols":9
[ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
[ModuleOutput] }
```

Dále je na stránce podrobnější informace o sloupcích, které budou vypadat podobně jako následující výstup.

```output
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
```

Tyto výsledky jsou převážně podle očekávání, přičemž 228 pozorování a 9 sloupců v dataframe. Zobrazí se názvy sloupců, datový typ R a ukázka každého sloupce.

> [!NOTE]
> Stejný vytištěný výstup je pohodlně dostupný z výstupu zařízení R v modulu [spouštění skriptu jazyka r][execute-r-script] . V další části se podíváme na výstupy modulu [spuštění skriptu jazyka R][execute-r-script] .  

#### <a name="dataset2"></a>Dataset2

Chování vstupu Dataset2 je stejné jako u DataSet1.. Pomocí tohoto vstupu můžete do kódu R předat druhou obdélníkovou tabulku dat. Funkce `maml.mapInputPort(2)` s argumentem 2 se používá k předání těchto dat.  

### <a name="execute-r-script-outputs"></a>Spouštění výstupů skriptů R

#### <a name="output-a-dataframe"></a>Výstup datového rámce

Obsah datového rámce R můžete vystavit jako obdélníkovou tabulku prostřednictvím DataSet1. portu výsledku pomocí `maml.mapOutputPort()` funkce. V našem jednoduchém skriptu jazyka R se tato akce provádí na následujícím řádku.

```r
maml.mapOutputPort('cadairydata')
```

Po spuštění experimentu vyberte výstupní port DataSet1. výsledku a pak vyberte **vizualizovat**. Mělo by se zobrazit něco jako v tomto příkladu.

![Snímek obrazovky zobrazující vizualizaci výstupu dat o mlékárnách v Kalifornii](./media/r-quickstart/fig7.png)

Tento výstup bude stejný jako u vstupu, přesně podle očekávání.

### <a name="r-device-output"></a>Výstup zařízení v R

Výstup zařízení modulu [spuštění skriptu jazyka R][execute-r-script] obsahuje zprávy a výstup grafiky. Do výstupního portu zařízení R se odesílají standardní výstupní i standardní chybové zprávy z R.

Chcete-li zobrazit výstup zařízení R, vyberte port a pak vyberte **vizualizovat**. Na tomto místě se zobrazí standardní výstup a standardní chyba ze skriptu jazyka R.

![Snímek obrazovky, který zobrazuje standardní výstup a standardní chybu z portu zařízení R.](./media/r-quickstart/fig8.png)

Když se posunete dolů, uvidíme výstup grafiky z našeho skriptu jazyka R.

![Snímek obrazovky zobrazující výstup grafiky z portu zařízení R](./media/r-quickstart/fig9.png)

## <a name="data-filtering-and-transformation"></a><a id="filtering"></a>Filtrování a transformace dat

V této části provedeme několik základních operací pro filtrování a transformaci dat pro mléčné výrobky v Kalifornii. Na konci této části budeme mít data ve formátu vhodném pro vytvoření analytického modelu.

Konkrétně v této části provedeme několik běžných úloh čištění a transformace dat: transformace typu, filtrování u datových snímků, přidání nových počítaných sloupců a transformace hodnot. Toto pozadí by vám mělo pomáhat při práci s mnoha variantami zjištěnými v reálných problémech.

Úplný kód R této části je k dispozici v [MachineLearningSamples – poznámkových blocích/studiu-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="type-transformations"></a>Transformace typu

Teď, když můžeme přečíst data o mlékárnách v Kalifornii do kódu R v modulu [spuštění skriptu jazyka r][execute-r-script] , musíme zajistit, aby data ve sloupcích měla zamýšlený typ a formát.

R je dynamicky typový jazyk, což znamená, že datové typy jsou v případě potřeby z jednoho na jiné. Atomické datové typy v jazyce R obsahují číselné, logické a znakové. Typ faktoru se používá k komprimaci dat kategorií. Další informace o datových typech najdete v odkazech [dále](#appendixb).

Když jsou tabulková data načítána z externího zdroje na R, je vždy dobré kontrolovat výsledné typy ve sloupcích. Můžete chtít sloupec typu znak, ale v mnoha případech se sloupec zobrazí jako faktor nebo naopak. V ostatních případech by měl být sloupec, který si myslíte číselnou, reprezentován znakovým datům, například "1,23" místo 1,23 jako číslo s plovoucí desetinnou čárkou.

Naštěstí je snadné převést jeden typ na jiný, pokud je mapování možné. Například "Nevada" nelze převést na číselnou hodnotu, ale lze jej převést na faktor (kategorií proměnná). V jiném příkladu můžete převést číslo 1 na znak "1" nebo faktor.

Syntaxe pro některý z těchto převodů je jednoduchá: `as.datatype()` . Tyto funkce pro převod typu obsahují následující funkce:

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Zobrazení datových typů sloupců, které jsme zadali v předchozí části, jsou všechny sloupce typu numeric. Výjimkou je sloupec označený jako "Month" (měsíc), který je typu znak. Pojďme tento typ převést na faktor a otestovat výsledky.

Odstranili jsme řádek, který vytvořil matici bodového grafu, a přidali čáru k převedení sloupce month na faktor. V tomto experimentu vydělíme a vložíte kód R do okna Code modulu [Spustit skript jazyka R][execute-r-script] . Můžete také aktualizovat soubor zip a odeslat ho do Machine Learning Studio (Classic), ale tato možnost provede několik kroků.

```r
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

Pojďme tento kód spustit a podívat se na výstupní protokol pro skript R. Zde jsou uvedena relevantní data z protokolu.

```output
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
```

Typ pro měsíc by nyní měl sestavovat **úrovně faktoru w/14**. Tento typ je problém, protože je v roce jenom 12 měsíců. Můžete také zjistit, že typ v **vizualizaci** portu výsledné sady výsledků je **kategorií**.

Problémem je to, že sloupec month se nekóduje systematicky. V některých případech se měsíc zavolá duben a v ostatních případech se zkrátí jako APR. Tento problém můžeme vyřešit oříznutím řetězce na tři znaky. Řádek kódu teď vypadá podobně jako v následujícím příkladu.

```r
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Znovu spusťte experiment a zobrazte výstupní protokol. Tady jsou uvedené očekávané výsledky.

```output
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
```


Naše proměnná faktoru má teď požadované 12 úrovní.

### <a name="basic-dataframe-filtering"></a>Základní filtrování dataframe

Datový rámec R podporuje výkonné možnosti filtrování. Datové sady mohou být subsetted pomocí logických filtrů na jednom řádku nebo ve sloupcích. V mnoha případech se vyžadují složitá kritéria filtru. Podrobné příklady filtrování dataframes najdete v tématu [Další](#appendixb)informace o odkazech.

Existuje jeden bit filtrování, které bychom měli dělat na naší datové sadě. Pokud se podíváte na sloupce v dataframe cadairydata, zobrazí se dva nepotřebné sloupce. První sloupec obsahuje číslo řádku, což není velmi užitečné. Druhý sloupec year. Month obsahuje nadbytečné informace. Tyto sloupce můžeme snadno vyloučit pomocí následujícího kódu R.

> [!NOTE]
> Od teď v této části vám ukážeme další kód, který přidáváme v modulu [spuštění skriptu jazyka R][execute-r-script] . Každý nový řádek přidáme *před* `str()` funkci. Pomocí této funkce lze ověřit výsledky v Machine Learning Studio (Classic).

Do kódu R v modulu [spuštění skriptu jazyka r][execute-r-script] přidáme následující řádek.

```r
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Spusťte tento kód v experimentu a kontrolujte výsledek z výstupního protokolu. Tyto výsledky se zobrazí zde.

```output
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
```

Nyní získáte očekávané výsledky.

### <a name="add-a-new-column"></a>Přidat nový sloupec

Aby bylo možné vytvářet modely časových řad, bude vhodné mít sloupec, který obsahuje měsíce od začátku časové řady. Vytvoříme nový sloupec měsíc. Count.

Abychom vám pomohli organizovat kód, vytvoříme naši první jednoduchou funkci `num.month()` . Pak tuto funkci použijeme k vytvoření nového sloupce v rámci datového rámce. Nový kód je následující:

```r
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

Nyní spusťte aktualizovaný experiment a použijte výstupní protokol k zobrazení výsledků. Tyto výsledky se zobrazí zde.

```output
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
```


Vypadá to, že všechno funguje. V našem dataframe máme nový sloupec s očekávanými hodnotami.

### <a name="value-transformations"></a>Transformace hodnot

V této části provedeme některé jednoduché transformace na hodnoty v některých sloupcích datového rámce. Jazyk R podporuje skoro libovolné transformace hodnot. Další příklady naleznete v tématu [Další](#appendixb)informace o odkazech.

Pokud se podíváte na hodnoty v části datový rámec, měla by se zobrazit něco, co je tady. Je zmrzlina zmrzlina než mléko vyráběná v Kalifornii? Ne, samozřejmě. Problémem je to, že se jednotky liší. Cena je v jednotkách v amerických librách, mléko je v jednotkách 1 000 000 USA a zmrzliny je v jednotkách 1 000 amerických galonů a domácký sýr se nachází v jednotkách 1 000 amerických. Za předpokladu, že Ice zmrzlina váží přibližně 6,5 libry za galon, můžeme jednoduše provést násobení, aby se tyto hodnoty převedly na stejné jednotky 1 000 Libr.

Pro náš model prognózy používáme multiplikativní model pro vývoj a sezónní úpravu těchto dat. Transformace protokolu nám umožňuje používat lineární model, který tento proces zjednodušuje. Transformaci protokolu můžeme použít ve stejné funkci, kde se aplikuje násobitel.

V následujícím kódu definujeme novou funkci, `log.transform()` a použijeme ji na řádky, které obsahují číselné hodnoty. Funkce jazyka R `Map()` se používá pro použití `log.transform()` funkce na vybrané sloupce datového rámce. `Map()`Funkce je podobná `apply()` , ale umožňuje více než jeden seznam argumentů funkce. Všimněte si, že seznam násobitelů poskytuje druhý argument `log.transform()` funkci. `na.omit()`Funkce se používá jako bitová kopie, aby nedošlo k chybějícím nebo nedefinovaným hodnotám v dataframe.

```r
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
  ## If there is an exception, print the warning message to
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

Ve funkci se vyskytuje hodně bitů `log.transform()` . Většina tohoto kódu kontroluje možné problémy s argumenty nebo řešení potíží s výjimkami, které mohou být stále v průběhu výpočtů. Pouze pár řádků tohoto kódu provede výpočty.

Cílem programování v obrannou linií je zabránit selhání jedné funkce, která brání v pokračování zpracování. Pro uživatele se dá frustrující náhlé selhání dlouhotrvající analýzy. Aby k této situaci nedocházelo, je nutné zvolit výchozí návratové hodnoty, které omezují škodu na zpracování pro příjem dat. Také se vytvoří zpráva upozorňující uživatele, že došlo k nějakému problému.

Pokud nepoužíváte k obrannou linií programování v jazyce R, může být celý tento kód zahlcený. Pojďme si projít hlavními kroky:

1. Je definován vektor čtyř zpráv. Tyto zprávy slouží ke sdělování informací o některých možných chybách a výjimkách, které mohou s tímto kódem nastat.
1. U každého případu vrátíme hodnotu NEDEF. Existuje mnoho dalších možností, které mohou mít méně vedlejších účinků. Mohli bychom například vracet vektor nuly nebo původní vstupní vektor, například.
1. Kontroly jsou spouštěny na argumentech funkce. V každém případě, pokud je zjištěna chyba, je vrácena výchozí hodnota a zpráva je vytvořena `warning()` funkcí. Používáme `warning()` místo toho `stop()` , aby se ukončilo provádění, což znamená, že se snažíme vyhnout. Tento kód je napsán ve stylu procedurální, protože v tomto případě se jedná o funkční přístup, který je v tomto případě složitý a skrytý.
1. Výpočty protokolu jsou zabaleny `tryCatch()` , takže výjimky nezpůsobí náhlé zastavení zpracování. Bez `tryCatch()` toho většina chyb vyvolaných funkcí jazyka R má za následek signál stop, který to dělá pouze.

Spusťte tento kód R v experimentu a podívejte se na vytištěný výstup v souboru Output. log. Nyní uvidíte transformované hodnoty čtyř sloupců v protokolu, jak je znázorněno zde.

```output
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
```

Uvidíme hodnoty, které byly transformované. Mléčná produkce teď výrazně přesáhne veškerou další produkci produktů v mléčném prostředí a znovu volá, že teď se díváte na škálování protokolu.

V tomto okamžiku se data vyčistí a jsou připravená na nějaké modelování. Pokud se podíváte na souhrn vizualizace výstupu výsledné sady výsledků v modulu [spuštění skriptu jazyka R][execute-r-script] , uvidíte, že sloupec month je kategorií s 12 jedinečnými hodnotami, což je stejně tak, jak jsme chtěli.

## <a name="time-series-objects-and-correlation-analysis"></a><a id="timeseries"></a>Objekty časové řady a analýza korelace

V této části prozkoumáme několik základních objektů časových řad jazyka R a analyzujete korelace mezi některými proměnnými. Naším cílem je výstup datového rámce, který obsahuje informace o korelační korelaci v několika prodlevy.

Úplný kód R tohoto oddílu je v [MachineLearningSamples-poznámkách/studiu-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="time-series-objects-in-r"></a>Objekty časové řady v R

Jak už bylo zmíněno, časové řady představují řadu hodnot dat indexovaných podle času. Objekty časové řady R slouží k vytvoření a správě časového indexu. Použití objektů časových řad má několik výhod. Objekty Time Series uvolňují z mnoha podrobností o správě hodnot indexu časové řady, které jsou zapouzdřeny v objektu. Kromě toho objekty Time Series umožňují použít řadu metod časové řady pro vykreslování, tisk, modelování atd.

Třída POSIXct Time Series se běžně používá a je relativně jednoduchá. Tato časová osa měří čas od začátku epocha, od 1. ledna 1970. V tomto příkladu použijeme objekty POSIXct Time Series. Mezi další široce používané třídy objektů s časovou řadou R patří XTS (rozšiřitelná časová řada).

### <a name="time-series-object-example"></a>Příklad objektu Time Series

Pojďme začít s naším příkladem. Přetáhněte nový modul [spuštění skriptu jazyka R][execute-r-script] do experimentu. Připojte výstupní port DataSet1. pro existující modul [skriptu Execute r][execute-r-script] ke vstupnímu portu DataSet1. nového modulu [skriptu pro spuštění r][execute-r-script] .

Stejně jako v prvních příkladech budeme postupovat podle příkladu. V některých případech budeme v každém kroku zobrazovat jenom přírůstkové další řádky kódu R.

#### <a name="read-the-dataframe"></a>Přečtěte si datový rámec

Jako první krok si přečteme v dataframe a ujistěte se, že jsme získali očekávané výsledky. Následující kód by měl provést úlohu.

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Nyní spusťte experiment. Protokol nového obrazce spustit skript jazyka R by měl vypadat jako v tomto příkladu.

```output
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
```

Tato data mají očekávané typy a formát. Nyní je sloupec month typu Factor a má očekávaný počet úrovní.

#### <a name="create-a-time-series-object"></a>Vytvoření objektu časové řady

Musíme do našeho datového rámce přidat objekt časové řady. Nahraďte aktuální kód následujícím kódem, který přidá nový sloupec třídy POSIXct.

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Teď si Projděte protokol. Mělo by to vypadat jako v tomto příkladu.

```output
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
```

V souhrnu jsme viděli, že nový sloupec je ve skutečnosti pro třídu POSIXct.

### <a name="explore-and-transform-the-data"></a>Prozkoumat a transformovat data

Pojďme prozkoumat některé proměnné v této datové sadě. Matice bodového grafu je dobrým způsobem, jak vytvořit rychlý vzhled. `str()`Funkci v předchozím kódu R nahradíme následujícím řádkem.

```r
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Spusťte tento kód a podívejte se, co se stane. Vykreslení, které je vyrobeno na portu zařízení R, by mělo vypadat jako v tomto příkladu.

![Snímek obrazovky znázorňující matici bodového grafu vybraných proměnných](./media/r-quickstart/fig17.png)

Mezi těmito proměnnými jsou některé struktury s lichým vzhledem. Příčinou může být to, že tato struktura vznikne trendy v datech a ze skutečnosti, že jsme tyto proměnné nepoužili standardizovaně.

### <a name="correlation-analysis"></a>analýza korelací.

Aby bylo možné provést analýzu korelace, musíme použít jak de-LINTREND, tak i standardizovat proměnné. Můžeme jednoduše použít `scale()` funkci R, která obě středy a škáluje proměnné. Tato funkce může být dobře spouštěna rychleji. Pojďme se ale podívat na příklad obrannou linií programování v jazyce R.

`ts.detrend()`Následující funkce provádí obě tyto operace. Následující dva řádky kódu detrendují data a pak tyto hodnoty standardizovat.

```r
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

Ve funkci se vyskytuje hodně bitů `ts.detrend()` . Většina tohoto kódu kontroluje možné problémy s argumenty nebo řešení potíží s výjimkami, které mohou být stále v průběhu výpočtů. Pouze pár řádků tohoto kódu ve skutečnosti provádí výpočty.

Již jsme probrali příklad programování obrannou linií v transformacích hodnot. Oba bloky výpočtů jsou zabaleny do `tryCatch()` . V případě některých chyb má smysl vrátit původní vstupní vektor. V ostatních případech vrátíme vektor nul.

Všimněte si, že lineární regrese, která se používá pro detrendování, je regrese časové řady. Proměnná prediktivního je objekt časové řady.

Po `ts.detrend()` Definování je použijeme na proměnné zájmu v našem dataframe. Výsledný seznam, který byl vytvořen pomocí, je nutné převést `lapply()` na data v dataframe pomocí `as.data.frame()` . Kvůli obrannou linií aspektům pro `ts.detrend()` neúspěšné zpracování jedné z proměnných nebrání správnému zpracování ostatních.

Poslední řádek kódu vytvoří párový bodový graf. Po spuštění kódu R jsou zde zobrazeny výsledky bodového grafu.

![Snímek obrazovky, který ukazuje párový bodový graf z netrendových a standardizovaných časových řad.](./media/r-quickstart/fig18.png)

Tyto výsledky můžete porovnat s hodnotami uvedenými v předchozím příkladu. S odebraným trendem a s proměnnými, které jsou standardizovány, se v relacích mezi těmito proměnnými zobrazuje struktura s menším množstvím.

Kód pro výpočet korelace jako objektů R CCF je následující.

```r
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

Spuštění tohoto kódu vytvoří protokol zobrazený zde.

```output
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
```

Pro každé zpoždění je k dispozici korelační hodnota. Žádná z těchto hodnot korelace není dostatečně velká, aby mohla být významná. Můžeme uzavřít, že každou proměnnou můžete modelovat nezávisle.

### <a name="output-a-dataframe"></a>Výstup datového rámce
Vypočítali jsme relace párových relací jako seznam objektů CCF jazyka R. To představuje bitovou příčinu problému, protože výstupní port výsledné sady dat skutečně vyžaduje datový rámec. Kromě toho objekt CCF je sám seznam a chceme, aby v prvním elementu tohoto seznamu byly pouze hodnoty, korelace v různých prodlevy.

Následující kód extrahuje hodnoty prodlevy ze seznamu objektů CCF, které jsou vlastními seznamy.

```r
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation dataframe and assign the column names
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

První řádek kódu je trochu obtížné a některé vysvětlení vám může porozumět. Při práci z interního prostředí máme:

1. Operátor **[[** s argumentem **1** vybere vektor korelace na prodlevy z prvního prvku seznamu objektů CCF.
1. `do.call()`Funkce aplikuje `rbind()` funkci na prvky seznamu, kterou vrátí `lapply()` .
1. `data.frame()`Funkce převede výsledek vyprodukovaný `do.call()` pro datový rámec.

Všimněte si, že názvy řádků jsou ve sloupci datového rámce. Tím se zachová názvy řádků při výstupu ze [skriptu Execute jazyka R][execute-r-script].

Spuštění kódu vytvoří výstup, který je zde zobrazen, když vybereme **vizualizuji** a zobrazí se výstup na portu datové sady výsledků. Názvy řádků jsou v prvním sloupci, jak je určeno.

![Snímek obrazovky, který zobrazuje výstup výsledků z analýzy korelace.](./media/r-quickstart/fig20.png)

## <a name="time-series-example-seasonal-forecasting"></a><a id="seasonalforecasting"></a>Příklad časové řady: sezónní prognózování

Naše data jsou teď ve formě vhodné k analýze a zjistili jsme, že mezi proměnnými neexistují žádné významné korelace. Pojďme se přesunout a vytvořit model prognózy časových řad. S využitím tohoto modelu budeme předpovědět, že se v Kalifornii po dobu 12 měsíců 2013.

Náš model prognózy bude mít dvě komponenty, komponentu trendu a sezónní komponentu. Kompletní prognóza je produktem těchto dvou součástí. Tento typ modelu je známý jako multiplikativní model. Alternativou je model doplňku. Transformaci protokolu jsme už použili u proměnných zájmu, což zjednodušuje tuto analýzu.

Úplný kód R tohoto oddílu je v [MachineLearningSamples-poznámkách/studiu-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="create-the-dataframe-for-analysis"></a>Vytvořit datový rámec pro analýzu

Začněte přidáním nového modulu [skriptu pro spuštění R][execute-r-script] do experimentu. Připojte výstup výsledné datové sady existujícího modulu [skriptu pro spuštění R][execute-r-script] k **DataSet1.** vstupu nového modulu. Výsledek by měl vypadat přibližně jako v tomto příkladu.

![Diagram, který znázorňuje experiment s přidaným novým modulem skriptu pro spuštění jazyka R.](./media/r-quickstart/fig21.png)

Stejně jako u analýzy korelace jsme právě dokončili přidání sloupce s objektem POSIXct Time Series. Tento sloupec se přidá do následujícího kódu.

```r
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Spusťte tento kód a podívejte se do protokolu. Výsledek by měl vypadat jako v tomto příkladu.

```output
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
```

V důsledku toho jsme připraveni začít s analýzou.

### <a name="create-a-training-dataset"></a>Vytvoření datové sady školení

Když je vytvořen datový rámec, musíme vytvořit datovou sadu školení. Tato data budou zahrnovat všechna pozorování kromě posledních 12, roku 2013, což je naše testovací datová sada. Následující kód podmnožinou datového rámce a vytvoří parcely produkčních a cenových proměnných pro mléčné výrobky. Pak vytvoříme parcely čtyř produkčních a cenových proměnných. Anonymní funkce se používá k definování některých rozšíření pro vykreslení a pak iterování nad seznamem dalších dvou argumentů s `Map()` . Pokud si myslíte, že smyčka for by v tomto případě fungovala správně, je to správné. Ale vzhledem k tomu, že R je funkční jazyk, se díváte na funkční přístup.

```r
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

Spuštění kódu vytvoří řadu časových řad z výstupu zařízení R, které vidíte zde. Časová osa je v jednotkách dat, což je skvělé využití metody grafu Time Series.

![První graf řady Brna v produkci mléčných a cenových údajů.](./media/r-quickstart/unnamed-chunk-161.png)

![Druhá série časových řad v Kalifornii v produkci mléčných a cenových dat v Kalifornii](./media/r-quickstart/unnamed-chunk-162.png)

![Třetí série časových řad v Kalifornii pro produkci mléčných a cenových dat v Kalifornii](./media/r-quickstart/unnamed-chunk-163.png).

![Sestavování čtvrté časové řady v Kalifornii v produkci mléčných a cenových údajů](./media/r-quickstart/unnamed-chunk-164.png)

### <a name="a-trend-model"></a>Model trendu

Teď, když jsme vytvořili objekt časové řady a podíváme se na data, začneme sestavovat model trendu pro data o produkci mléka v Kalifornii. Můžeme použít regresi časových řad. Je jasné, že vykreslení, které potřebujeme více než sklon, a zachytit k přesnému modelování pozorovaného trendu v školicích datech.

Vzhledem k malé škále dat sestavíme model pro vývoj v RStudio a pak vymažete a vložíte výsledný model do Machine Learning Studio (Classic). RStudio poskytuje interaktivní prostředí pro tento typ interaktivní analýzy.

Při prvním pokusu zkusíme provést polynomickou regresi s pravomocemi až tři. Existují reálné nebezpečí přebudování těchto druhů modelů. Je nejlepší vyhnout se podmínkám ve vysokém pořadí. `I()`Funkce brání interpretaci obsahu (interpretuje obsah tak, jak je) a umožňuje napsat doslova interpretovaná funkce v regresní rovnici.

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Tato funkce generuje následující výstup.

```output
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
## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 **_
## Time              1.63e-09   1.72e-10    9.47   <2e-16 _*_
## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 _  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0418 on 212 degrees of freedom
## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16
```

Na základě hodnot P ( `Pr(>|t|)` ) v tomto výstupu zjistíme, že druhá část slova nemusí být významná. Tuto funkci použijeme `update()` k úpravě tohoto modelu vyřazením čtvercového období.

```r
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Tato funkce generuje následující výstup.

```output
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
## Time              1.57e-09   4.32e-11    36.3   <2e-16 **_
## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 _*_
## ---
## Signif. codes:  0 '_*_' 0.001 '_*' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0417 on 213 degrees of freedom
## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16
```

Tento výstup vypadá lépe. Všechny tyto výrazy jsou významné. Hodnota 2E-16 je výchozí hodnotou a neměla by být příliš závažná.  

Jako správnosti test si probereme časovou řadu mléčných dat z Brna v Kalifornii pomocí zobrazené křivky trendu. Přidali jsme následující kód do Machine Learning Studio (Classic) [spuštění modelu skriptu R][execute-r-script] (ne RStudio) pro vytvoření modelu a vytvoření grafu. Výsledek je znázorněn v následujícím příkladu.

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Údaje o produkci mléčných mléka v Kalifornii se zobrazeným modelem trendu.](./media/r-quickstart/unnamed-chunk-18.png)

Vypadá to, že model trendu přesně odpovídá datům. Dále se nejeví jako legitimace při přebudování, jako je například lichá Wiggles v křivce modelu.

### <a name="seasonal-model"></a>Sezónní model

S modelem trendu je potřeba nasdílet a zahrnovat sezónní účinky. Měsíc v roce použijeme jako fiktivní proměnnou v lineárním modelu pro zachycení měsíčního efektu. Při zavedení proměnných faktoru do modelu nesmí být zachytávání vypočítáno. Pokud to neuděláte, vzorec se zachová a R vynechá jeden z požadovaných faktorů, ale ponechá termín zachytávání.

Vzhledem k tomu, že máme k dispozici uspokojivý model trendu, můžeme pomocí `update()` funkce Přidat nové podmínka do existujícího modelu. Výraz-1 ve vzorci aktualizace zruší termín zachycení. Pokračuje se v RStudio a teď:

```r
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Tato funkce generuje následující výstup.

```output
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
## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 **_
## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 _*_
## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 _*_
## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 _*_
## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 _*_
## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 _*_
## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 _*_
## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 _*_
## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 _*_
## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 _*_
## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 _*_
## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 _*_
## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 _*_
## ---
## Signif. codes:  0 '_*_' 0.001 '_*' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0263 on 202 degrees of freedom
## Multiple R-squared:     1,    Adjusted R-squared:     1
## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16
```

Zjistili jsme, že model už nemá termín zachycení a má 12 významných měsíčních faktorů. Výsledkem je přesně to, co jsme chtěli vidět.

Pojďme udělat další graf časových řad v rámci produkčních dat v Kalifornii a zjistit, jak dobře funguje sezónní model. Do [skriptu jazyka R][execute-r-script] pro Machine Learning Studio (Classic) se přidal následující kód, který vytvoří model a vytvoří vykreslení.

```r
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

Spuštění tohoto kódu v Machine Learning Studio (Classic) vytvoří graf, který je zde zobrazen.

![V Kalifornii mléčné výroby s modelem včetně sezónních účinků.](./media/r-quickstart/unnamed-chunk-20.png)

Místo toho, aby se přizpůsobila datům uvedeným v tomto příkladu, je podpora. Trend i sezónní účinek (měsíční variace) vypadají jako rozumné.

Jako další kontrolu našeho modelu se podívejme na zbytky. Následující kód vypočítá předpovězené hodnoty z našich dvou modelů, vypočítá zbytky pro sezónní model a potom tyto zbytky vykreslí pro školicí data.

```r
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

Zbývající graf se zobrazí zde.

![Zbytky sezónního modelu pro školicí data.](./media/r-quickstart/unnamed-chunk-21.png)

Tyto zbytky vypadají rozumným způsobem. Neexistuje žádná konkrétní struktura, s výjimkou účinku 2008-2009 recesí, který náš model nepoužívá zejména.

Vykreslení znázorněné v tomto příkladu je užitečné pro zjištění všech vzorů závislých na čase ve zbytkech. Explicitní přístup k výpočetnímu prostředí a vykreslení zbytků, které jsme použili, umístí zbývající množství do grafu v časovém pořadí. Pokud jsme vykreslili `milk.lm$residuals` , vykreslení by nebylo v časovém pořadí.

Můžete také použít `plot.lm()` k vytvoření řady diagnostických parcel.

```r
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Tento kód vytváří řadu diagnostických zobrazení, která jsou znázorněna v následujících příkladech.

![První diagnostický graf pro sezónní model](./media/r-quickstart/unnamed-chunk-221.png)

![Druhý diagnostický graf pro sezónní model.](./media/r-quickstart/unnamed-chunk-222.png)

![Třetí diagnostický graf pro sezónní model.](./media/r-quickstart/unnamed-chunk-223.png)

![Čtvrtý diagnostický graf pro sezónní model](./media/r-quickstart/unnamed-chunk-224.png)

V těchto zkusných grafech je několik vysoce kvalitních bodových bodů, ale nic nezpůsobuje Skvělé obavy. Dále uvidíte z normálního vykreslování Q-Q, že zbytky jsou blízko normální distribuce, což je důležitý předpoklad pro lineární modely.

### <a name="forecasting-and-model-evaluation"></a>Prognózování a vyhodnocení modelu

K dokončení našeho příkladu je tu jenom jedna věc. Musíme vypočítat předpovědi a změřit chybu proti skutečným datům. Naše předpověď bude na 12 měsíců od 2013. Pro tuto prognózu můžeme vypočítat chybovou míru na skutečná data, která nejsou součástí naší datové sady školení. Kromě toho můžeme porovnat výkon na 18 let školicích dat až po dobu 12 měsíců testovacích dat.

K měření výkonu modelů časových řad se používá řada metrik. V našem případě použijeme chybu root střed_hodn Square (RMS). Následující funkce vypočítá chybu služby RMS mezi dvěma řadami.

```r
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

Stejně jako u `log.transform()` funkce, kterou jsme provedli v části "transformace hodnot", je v této funkci dost velké množství kontroly chyb a kód pro obnovení výjimek. Použité zásady jsou stejné. Práce se provádí na dvou místech zabalených do `tryCatch()` . Nejdřív se exponentiated časová řada, protože jsme pracovali s protokoly těchto hodnot. Za druhé se počítá skutečná Chyba služby RMS.

Je vybavená funkcí k měření chyby služby RMS a vytvoří a vytvoří výstup datového rámce, který obsahuje chyby služby RMS. Budeme zahrnovat výrazy pro model trendů a kompletní model s sezónními faktory. Následující kód provede úlohu pomocí dvou lineárních modelů, které jsme sestavili.

```r
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

Spuštění tohoto kódu vytvoří výstup, který je zde zobrazen na výstupním portu výsledné sady dat.

![Snímek obrazovky, který zobrazuje porovnání chyb služby RMS pro modely.](./media/r-quickstart/fig26.png)

Z těchto výsledků vidíte, že přidání sezónních faktorů do modelu snižuje chybu služby RMS významně. Není příliš překvapivě, Chyba služby RMS pro školicí data je trochu méně než u prognózy.

## <a name="guide-to-rstudio-documentation"></a><a id="appendixa"></a>Příručka k dokumentaci k RStudio

RStudio je dobře zdokumentováná. Tady jsou některé odkazy na klíčové oddíly dokumentace k RStudio, které vám pomohou začít.

* **Vytváření projektů** : pomocí RStudio můžete organizovat a spravovat kód R v projektech. Další informace naleznete v tématu [using Projects](https://support.rstudio.com/hc/articles/200526207-Using-Projects). Postupujte podle těchto pokynů a vytvořte projekt pro příklady kódu R v tomto článku.
* **Upravit a spustit kód r** : RStudio poskytuje integrované prostředí pro úpravy a spouštění kódu r. Další informace naleznete v tématu [Editing and vykonávající Code](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code).
* **Ladění** : RStudio zahrnuje výkonné funkce ladění. Další informace o těchto funkcích naleznete v tématu [ladění pomocí RStudio](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio). Informace o funkcích řešení potíží s zarážkou najdete v tématu [řešení potíží s zarážkou](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting).

## <a name="further-reading"></a><a id="appendixb"></a>Další čtení

Tento kurz programování v R se zabývá základními informacemi o tom, co potřebujete k používání jazyka R s Machine Learning Studio (Classic). Pokud R neznáte, jsou v CRAN k dispozici dva úvody:

* [R pro začátečníky](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) by Emmanuel Paradis je dobrým místem, kde začít.
* [Úvod do jazyka R](https://cran.r-project.org/doc/manuals/R-intro.html) pomocí W. N. Venables et al. přejde do větší hloubky.

K dispozici je mnoho knih v jazyce R, které vám pomůžou začít:

* **Obrázek programování v jazyce r: prohlídku statistického návrhu softwaru pomocí programu** Norman Matloff je vynikající Úvod do programování v jazyce r.
* **R kuchařka** podle Paul Teetor poskytuje přístup k problému a řešení pro použití R.
* **R v akci** Robert Kabacoff je další užitečnou úvodní knihou. Doprovodný [web s rychlým R](https://www.statmethods.net/) je užitečným prostředkem.
* **Inferno** v jazyce r je překvapivě kniha poradním sloupku, která se zabývá několika obtížemi a obtížnémi tématy, která se můžou při programování v jazyce r vyskytnout. Kniha je k dispozici zdarma na adrese [R Inferno](https://www.burns-stat.com/documents/books/the-r-inferno/).
* **Pokročilý r** by Hadley Wickham poskytuje hluboké podrobně do pokročilých témat v jazyce r. Online verze této knihy je dostupná zdarma v jazyce [R](http://adv-r.had.co.nz/).
* **Úvodní časová řada s R** by Paul Cowpertwait a Andrew Metcalfe představuje úvod k použití R pro analýzu časových řad. Mnoho dalších teoretických textů poskytuje příklady jazyka R.

Tady je několik skvělých internetových prostředků:

* [Zobrazení úloh Cran: analýza časových řad](https://cran.r-project.org/web/views/TimeSeries.html) obsahuje katalog balíčků R Time Series. Informace o konkrétních balíčcích objektů časových řad najdete v dokumentaci k tomuto balíčku.
* [Seznámení s jazykem R](https://www.datacamp.com/courses/introduction-to-r) je bezplatný Interaktivní kurz z DataCamp, který učí R v pohodlí prohlížeče s využitím výukových lekcí a programovacích cvičení. K dispozici jsou interaktivní kurzy k nejnovějším technikám a balíčkům jazyka R.
* [Přečtěte si informace o programování R, konečné příručce](https://www.datamentor.io/r-programming/) od datainstruktora.
* [Programátor r](https://r-coder.com/) obsahuje podrobné kurzy r a bezplatný kurz r pro začátečníky.
* [Kurz R](https://www.cyclismo.org/tutorial/R/) od irské černé z Clarkson University je rychlý kurz.
* [Hlavní prostředky jazyka R, které vylepšují vaše datové dovednosti,](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html) obsahují více než 60 prostředků R.

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script