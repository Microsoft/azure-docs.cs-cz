---
title: Kurz projektu BikeShare – pokročilá příprava dat pomocí aplikace Azure Machine Learning Workbench
description: V tomto kurzu provedete úlohu komplexní přípravy dat pomocí aplikace Azure Machine Learning Workbench.
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/21/2017
ms.openlocfilehash: 951ce8947d113eaad2ea0e3b5df5e9714aa33dd8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723183"
---
# <a name="tutorial-use-azure-machine-learning-workbench-for-advanced-data-preparation-bike-share-data"></a>Kurz: Použití aplikace Azure Machine Learning Workbench k pokročilé přípravě dat (data projektu BikeShare)
Služba Azure Machine Learning (Preview) představuje integrované, komplexní řešení datové vědy a pokročilé analýzy pro profesionální datové vědce, které slouží k přípravě dat, vývoji experimentů a nasazování modelů na úrovni cloudu.

V tomto kurzu se naučíte, jak pomocí služby Machine Learning (Preview) provést následující úlohy:
> [!div class="checklist"]
> * Interaktivní příprava dat pomocí nástroje pro přípravu dat služby Machine Learning
> * Import, transformace a vytvoření testovací datové sady
> * Vygenerování balíčku pro přípravu dat
> * Spuštění balíčku pro přípravu dat pomocí Pythonu
> * Vygenerování trénovací datové sady opětovným použitím balíčku pro přípravu dat s dalšími vstupními soubory
> * Spouštění skriptů v místním okně Azure CLI
> * Spouštění skriptů v cloudovém prostředí Azure HDInsight

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Místní instalace aplikace Azure Machine Learning Workbench. Další informace najdete ve [stručném průvodci instalací](../service/quickstart-installation.md).
* Pokud nemáte nainstalované Azure CLI, postupujte podle pokynů pro [instalaci nejnovější verze Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Cluster HDInsight Spark](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal) vytvořený v Azure.
* Účet úložiště Azure.
* Znalost vytvoření nového projektu v aplikaci Workbench.
* I když to není nutné, je užitečné mít nainstalovaného [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/), abyste mohli nahrávat, stahovat a zobrazovat objekty blob ve svém účtu úložiště.

## <a name="data-acquisition"></a>Získání dat
Tento kurz používá [sadu dat programu Hubway z Bostonu](https://s3.amazonaws.com/hubway-data/index.html) a data o počasí v Bostonu agentury [NOAA](http://www.noaa.gov/).

1. Stáhněte si do svého místního vývojového prostředí datové soubory z následujících odkazů:

   * [Data o počasí v Bostonu](https://azuremluxcdnprod001.blob.core.windows.net/docs/azureml/bikeshare/BostonWeather.csv)

   * Data o jízdách na kolech Hubway z webu programu Hubway:

      - [201501-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201501-hubway-tripdata.zip)
      - [201504-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201504-hubway-tripdata.zip)
      - [201510-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201510-hubway-tripdata.zip)
      - [201601-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201601-hubway-tripdata.zip)
      - [201604-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201604-hubway-tripdata.zip)
      - [201610-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201610-hubway-tripdata.zip)
      - [201701-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201701-hubway-tripdata.zip)

2. Po stažení všechny soubory .zip rozzipujte.

## <a name="upload-data-files-to-azure-blob-storage"></a>Nahrání datových souborů do úložiště objektů blob v Azure
K hostování vašich datových souborů můžete použít úložiště objektů blob v Azure.

1. Použijte stejný účet úložiště, jaký se používání pro váš cluster HDInsight.

    ![Účet úložiště clusteru HDInsight](media/tutorial-bikeshare-dataprep/hdinsightstorageaccount.png)

2. Vytvořte nový kontejner **data-files**, ve kterém se budou ukládat datové soubory projektu **BikeShare**.

3. Nahrajte datové soubory. Soubor `BostonWeather.csv` nahrajte do složky `weather`. Soubory dat o jízdách nahrajte do složky `tripdata`.

    ![Nahrání datových souborů](media/tutorial-bikeshare-dataprep/azurestoragedatafile.png)

> [!TIP]
> K nahrání objektů blob můžete použít také Průzkumníka služby Storage. Tento nástroj použijte také v případě, že budete chtít zobrazit obsah jakýchkoli souborů vygenerovaných v tomto kurzu.

## <a name="learn-about-the-datasets"></a>Informace o datových sadách
1. Soubor s __daty o počasí v Bostonu__ obsahuje následující pole týkající se počasí s informacemi aktualizovanými každou hodinu:

   * **DATE** (Datum)

   * **REPORTTYPE** (Typ zprávy)

   * **HOURLYDRYBULBTEMPF** (Teplota suchého teploměru ve stupních Fahrenheita)

   * **HOURLYRelativeHumidity** (Hodinová relativní vlhkost)

   * **HOURLYWindSpeed** (Hodinová rychlost větru)

2. Data programu __Hubway__ jsou uspořádána do souborů podle roků a měsíců. Například soubor s názvem `201501-hubway-tripdata.zip` obsahuje soubor .csv s daty z ledna 2015. Data obsahují následující pole, přičemž každý řádek představuje jednu jízdu na kole:

   * **Doba trvání jízdy (v sekundách)**

   * **Čas a datum začátku jízdy**

   * **Čas a datum konce jízdy**

   * **Název a ID stanice začátku jízdy**

   * **Název a ID stanice konce jízdy**

   * **ID kola**

   * **Typ uživatele (běžný uživatel = uživatel s 24hodinovým nebo 72hodinovým kupónem; člen = člen programu s ročním nebo měsíčním předplatným)**

   * **Směrovací číslo (pokud se jedná o člena programu)**

   * **Pohlaví (uvedené členem)**

## <a name="create-a-new-project"></a>Vytvoření nového projektu
1. Z nabídky Start nebo spouštěče aplikací spusťte aplikaci **Machine Learning Workbench**.

2. Vytvořte nový projekt služby Machine Learning. Vyberte tlačítko **+** na stránce **Projects** (Projekty) nebo vyberte **File** (Soubor) > **New** (Nový).

   * Použijte šablonu **Sdílení kol**.

   * Projekt pojmenujte **BikeShare** (Sdílení kol). 

## <a id="newdatasource"></a>Vytvoření nového zdroje dat

1. Vytvořte nový zdroj dat. Výběrem tlačítka **Data** (ikona válce) na levém panelu nástrojů zobrazte zobrazení **Data**.

   ![Karta Data view (Zobrazení dat)](media/tutorial-bikeshare-dataprep/navigatetodatatab.png)

2. Přidejte zdroj dat. Vyberte ikonu **+** a pak vyberte **Add Data Source** (Přidat zdroj dat).

   ![Možnost Add Data Source (Přidat zdroj dat)](media/tutorial-bikeshare-dataprep/newdatasource.png)

## <a name="add-weather-data"></a>Přidání dat o počasí

1. **Data Store** (Úložiště dat): Vyberte úložiště dat, které obsahuje příslušná data. Vzhledem k tomu, že používáte soubory, vyberte **File(s)/Directory** (Soubory/adresář). Pokračujte výběrem tlačítka **Next** (Další).

   ![Položka File(s)/Directory (Soubory/adresář)](media/tutorial-bikeshare-dataprep/datasources.png)

2. **File Selection** (Výběr souboru): Přidejte data o počasí. Procházením vyberte soubor `BostonWeather.csv`, který jste dříve nahráli do úložiště objektů blob. Vyberte **Next** (Další).

   ![Výběr souboru s vybraným souborem BostonWeather.csv](media/tutorial-bikeshare-dataprep/azureblobpickweatherdatafile.png)

3. **File Details** (Podrobnosti souboru): Zkontrolujte rozpoznané schéma souboru. Aplikace Machine Learning Workbench analyzuje data v souboru a odvodí z nich schéma, které se má použít.

   ![Ověření podrobností o souboru](media/tutorial-bikeshare-dataprep/fileparameters.png)

   > [!IMPORTANT]
   > Aplikace Workbench nemusí v některých případech rozpoznat správné schéma. Vždy zkontrolujte správnost parametrů pro vaši datovou sadu. U dat o počasí zkontrolujte, že jsou nastaveny následující hodnoty:
   >
   > * __File Type__ (Typ souboru): Delimited File (csv, tsv, txt, etc.) (Soubor s oddělovači)
   > * __Separator__ (Oddělovač): Comma [,] (Čárka)
   > * __Comment Line Character__ (Znak řádku s poznámkami): Není nastavena žádná hodnota.
   > * __Skip Lines Mode__ (Režim přeskakování řádků): Don't skip (Nepřeskakovat)
   > * __File Encoding__ (Kódování souborů): utf-8
   > * __Promote Headers Mode__ (Režim povýšení záhlaví): Use Headers From First File (Používat záhlaví z prvního souboru)

   V náhledu dat by se měly zobrazit následující sloupce:

   * **Cesta**

   * **DATE** (Datum)

   * **REPORTTYPE** (Typ zprávy)

   * **HOURLYDRYBULBTEMPF** (Teplota suchého teploměru ve stupních Fahrenheita)
   
   * **HOURLYRelativeHumidity** (Hodinová relativní vlhkost)

   * **HOURLYWindSpeed** (Hodinová rychlost větru)

   Pokračujte výběrem tlačítka **Next** (Další).

4. **Data Types** (Datové typy): Zkontrolujte automaticky rozpoznané datové typy. Aplikace Machine Learning Workbench analyzuje data v souboru a odvodí z nich datové typy, které se mají použít.

   a. Pro tato data změňte **DATA TYPE** (Datový typ) pro všechny sloupce na **String** (Řetězec).

   > [!NOTE]
   > Řetězec použijeme proto, abychom dále v tomto kurzu mohli ukázat schopnosti aplikace Workbench. 

   ![Kontrola datových typů](media/tutorial-bikeshare-dataprep/datatypedetection.png)

   b. Pokračujte výběrem tlačítka __Next__ (Další). 

5. **Sampling** (Vzorkování): Vyberte **Edit** (Upravit) a vytvořte schéma vzorkování. Vyberte nový přidaný řádek __Top 10000__ (Nejvyšších 10 000) a pak vyberte __Edit__ (Upravit). V poli __Sample Strategy__ (Strategie vzorku) nastavte hodnotu **Full File** (Celý soubor) a pak vyberte **Apply** (Použít).

   ![Přidání nové strategie vzorkování](media/tutorial-bikeshare-dataprep/weatherdatasamplingfullfile.png)

   Aby se strategie __Full File__ (Celý soubor) použila, vyberte položku __Full File__ (Celý soubor) a pak vyberte __Set as Active__ (Nastavit jako aktivní). Vedle položky __Full File__ (Celý soubor) se zobrazí hvězdička, která označuje aktivní strategii.

   ![Položka Full File (Celý soubor) nastavená jako aktivní strategie](media/tutorial-bikeshare-dataprep/fullfileactive.png)

   Pokračujte výběrem tlačítka **Next** (Další).

6. **Path Column** (Sloupec s cestou): Část __Path Column__ (Sloupec s cestou) použijte k zahrnutí úplné cesty k souboru jako sloupce v importovaných datech. Vyberte __Do Not Include Path Column__ (Nezahrnovat sloupec s cestou).

   > [!TIP]
   > Zahrnutí sloupce s cestou je užitečné, když importujete složku s mnoha soubory s různými názvy. Je to také užitečné, když názvy souborů obsahují informace, které budete chtít později extrahovat.

   ![Nastavení možnosti Do Not Include Path Column (Nezahrnovat sloupec s cestou)](media/tutorial-bikeshare-dataprep/pathcolumn.png)

7. **Finish** (Dokončit): Vytváření zdroje dat dokončíte výběrem možnosti **Finish** (Dokončit).

    Otevře se karta s novým zdrojem dat s názvem __BostonWeather__. V zobrazení mřížky se zobrazí vzorek dat. Vzorek je založený na aktivním schématu vzorkování, které jste předtím zadali.

    Všimněte si, že v podokně **Steps** (Kroky) na pravé straně obrazovky se zobrazí jednotlivé akce provedené při vytváření tohoto zdroje dat.

   ![Zobrazení zdroje dat, vzorku dat a podokna s kroky](media/tutorial-bikeshare-dataprep/weatherdataloaded.png)

### <a name="view-data-source-metrics"></a>Zobrazení metriky zdroje dat

Vyberte __Metrics__ (Metriky) vlevo nahoře na kartě se zobrazením mřížky. Toto zobrazení poskytuje informace o rozdělení dat a další agregované statistické informace týkající se daného vzorku dat.

![Zobrazení metrik](media/tutorial-bikeshare-dataprep/weathermetrics.png)

> [!NOTE]
> Viditelnost statistických informací můžete nakonfigurovat pomocí rozevíracího seznamu **Choose Metric** (Zvolit metriku). Výběrem nebo zrušením výběru jednotlivých metrik v seznamu změníte zobrazení mřížky.

Pokud se chcete vrátit do zobrazení __Data__, vyberte __Data__ v levém horním rohu stránky.

## <a name="add-a-data-source-to-the-data-preparation-package"></a>Přidání zdroje dat do balíčku pro přípravu dat

1. Přípravu dat začněte výběrem možnosti __Prepare__ (Příprava). 

2. Po zobrazení výzvy zadejte název balíčku pro přípravu dat, například **BikeShare Data Prep**. 

3. Pokračujte výběrem tlačítka __OK__.

   ![Dialogové okno Prepare (Příprava)](media/tutorial-bikeshare-dataprep/dataprepdialog.png)

4. V části __Data Preparation__ (Příprava dat) na kartě __Data__ se zobrazí nový balíček s názvem **BikeShare Data Prep**. 

   Výběrem této položky balíček zobrazíte. 

5. Výběrem tlačítka **>>** rozbalte seznam __Dataflows__ (Toky dat) a zobrazte toky dat, které balíček obsahuje. V tomto příkladu je to jediný tok dat __BostonWeather__.

   > [!IMPORTANT]
   > Balíček může obsahovat více toků dat.

   ![Toky dat v balíčku](media/tutorial-bikeshare-dataprep/weatherdataloadedingrid.png)

## <a name="filter-data-by-value"></a>Filtrování dat podle hodnot
1. Data můžete filtrovat tak, že kliknete pravým tlačítkem na buňku s určitou hodnotou a vyberete možnost __Filter__ (Filtr). Pak vyberte typ filtru.

2. Pro účely tohoto kurzu vyberte buňku obsahující hodnotu `FM-15`. Pak nastavte filtr na **equals** (rovná se).  Teď budou data filtrována tak, aby vracela jen řádky, které mají v poli __REPORTTYPE__ (Typ zprávy) hodnotu `FM-15`.

   ![Dialogové okno Filter (Filtr)](media/tutorial-bikeshare-dataprep/weatherfilterinfm15.png)

   > [!NOTE]
   > FM-15 je typ zprávy o počasí METAR (Meteorological Terminal Aviation Routine). Na základě empirických pozorování jsou zprávy FM-15 považovány za nejúplnější zprávy s minimem chybějících dat.

## <a name="remove-a-column"></a>Odebrání sloupce

Sloupec __REPORTTYPE__ (Typ zprávy) už nepotřebujete. Klikněte pravým tlačítkem na záhlaví sloupce a vyberte **Remove Column** (Odebrat sloupec).

   ![Možnost Remove Column (Odebrat sloupec)](media/tutorial-bikeshare-dataprep/weatherremovereporttype.png)

## <a name="change-datatypes-and-remove-errors"></a>Změna datových typů a odebrání chyb
1. Při výběru záhlaví sloupců stiskněte klávesu Ctrl (na Macu klávesu Command ⌘), abyste mohli zvolit více sloupců současně. Pomocí této techniky vyberte následující záhlaví sloupců:

   * **HOURLYDRYBULBTEMPF** (Teplota suchého teploměru ve stupních Fahrenheita)

   * **HOURLYRelativeHumidity** (Hodinová relativní vlhkost)

   * **HOURLYWindSpeed** (Hodinová rychlost větru)

2. Klikněte pravým tlačítkem na jedno z vybraných záhlaví sloupců a pak vyberte **Convert Field Type to Numeric** (Převést typ pole na číselný). Tato možnost převede datový typ sloupců na číselný.

   ![Převod více sloupců na číselný datový typ](media/tutorial-bikeshare-dataprep/weatherconverttonumeric.png)

3. Odfiltrování chybových hodnot U některých sloupců můžou při převodu datového typu vzniknout určité problémy. Tyto problémy jsou označeny červenou barvou na panelu __Data Quality Bar__ (Panel kvality dat) příslušného sloupce.

   Pokud chcete řádky s chybami odebrat, klikněte pravým tlačítkem na záhlaví sloupce **HOURLYDRYBULBTEMPF** (Teplota suchého teploměru ve stupních Fahrenheita). Vyberte **Filter Column** (Filtrovat sloupec). U možnosti **I Want To** (Chci) použijte výchozí hodnotu **Keep Rows** (Zachovat řádky). V rozevíracím seznamu **Conditions** (Podmínky) změňte hodnotu výběrem možnosti **is not error** (není chyba). Výběrem tlačítka **OK** filtr použijte.

   ![Filtrování chybových hodnot](media/tutorial-bikeshare-dataprep/filtererrorvalues.png)

4. Abyste odebrali zbývající řádky s chybami v ostatních sloupcích, opakujte tento postup filtrování u sloupců **HOURLYRelativeHumidity** (Hodinová relativní vlhkost) a **HOURLYWindSpeed** (Hodinová rychlost větru).

## <a name="use-by-example-transformations"></a>Použití příkladů transformací

Abyste mohli data použít v předpovědi s dvouhodinovými časovými bloky, musíte vypočítat průměrný stav počasí v dvouhodinových intervalech. Použijte následující akce:

* Sloupec **DATE** (Datum) rozdělte na samostatné sloupce **Date** (Datum) a **Time** (Čas). Podrobný postup najdete v následující části.

* Ze sloupce **Time** (Čas) odvoďte sloupec **Hour_Range** (Hodinové rozmezí). Podrobný postup najdete v následující části.

* Sloupec **Date\_Hour\_Range** (Hodinové rozmezí k datu) odvoďte ze sloupců **DATE** (Datum) a **Hour_Range** (Hodinové rozmezí). Podrobný postup najdete v následující části.

### <a name="split-column-by-example"></a>Rozdělení sloupce podle příkladu

1. Sloupec **DATE** (Datum) rozdělte na samostatné sloupce **Date** (Datum) a **Time** (Čas). Pravým tlačítkem klikněte na záhlaví sloupce **DATE** (Datum) a vyberte **Split Column by Example** (Rozdělit sloupec podle příkladu).

   ![Položka Split Column by Example (Rozdělit sloupec podle příkladu)](media/tutorial-bikeshare-dataprep/weathersplitcolumnbyexample.png)

2. Aplikace Machine Learning Workbench automaticky identifikuje smysluplný oddělovač a vytvoří dva sloupce rozdělením dat na hodnoty data a času. 

3. Výběrem tlačítka __OK__ přijměte výsledky operace rozdělení.

   ![Rozdělené sloupce DATE_1 a DATE_2](media/tutorial-bikeshare-dataprep/weatherdatesplitted.png)

### <a name="derive-column-by-example"></a>Odvození sloupce podle příkladu

1. Abyste odvodili dvouhodinová rozmezí, klikněte pravým tlačítkem na záhlaví sloupce __DATE\_2__ (Datum_2) a vyberte **Derive Column by Example** (Odvodit sloupec podle příkladu).

   ![Položka Derive Column by Example (Odvodit sloupec podle příkladu)](media/tutorial-bikeshare-dataprep/weatherdate2range.png)

   Přidá se nový prázdný sloupec s hodnotami null.

2. V tomto novém sloupci vyberte první prázdnou buňku. Uveďte příklad požadovaného časového rozmezí tím, že do nového sloupce zadáte **12AM-2AM** (0:00–2:00), a pak stisknete Enter.

   ![Nový sloupec s hodnotou 12AM-2AM (0:00–2:00)](media/tutorial-bikeshare-dataprep/weathertimerangeexample.png)

   > [!NOTE]
   > Aplikace Machine Learning Workbench syntetizuje program na základě vámi zadaných příkladů a použije stejný program u zbývajících řádků. Všechny ostatní řádky se vyplní automaticky na základě příkladu, který jste zadali. Aplikace Workbench také analyzuje vaše data a pokusí se identifikovat hraniční případy. 

   > [!IMPORTANT]
   > V aktuální verzi aplikace Workbench nemusí identifikace hraničních případů fungovat na počítačích Mac. Na počítači Mac přeskočte následující kroky 3 a 4. Místo toho po naplnění všech řádků odvozenými hodnotami vyberte __OK__.
   
3. Text **Analyzing Data** (Analyzují se data) nad mřížkou označuje, že se aplikace Workbench pokouší rozpoznat hraniční případy. Jakmile to dokončí, změní se stav na **Review next suggested row** (Zkontrolovat další navržený řádek) nebo **No suggestions** (Žádné návrhy). V tomto příkladu se zobrazí stav **Review next suggested row** (Zkontrolovat další navržený řádek).

4. Pokud chcete zkontrolovat navrhované změny, vyberte odkaz **Review next suggested row** (Zkontrolovat další navržený řádek). Buňka, kterou byste měli zkontrolovat a v případě potřeby opravit, je na obrazovce zvýrazněna.

   ![Review next suggested row (Zkontrolovat další navržený řádek)](media/tutorial-bikeshare-dataprep/weatherreviewnextsuggested.png)

    Výběrem tlačítka __OK__ transformaci přijměte.
 
5. Tím se vrátíte do zobrazení mřížky dat __BostonWeather__. Mřížka teď obsahuje tři sloupce, které jste přidali v předchozích krocích.

   ![Zobrazení mřížky s přidanými řádky](media/tutorial-bikeshare-dataprep/timerangecomputed.png)

   > [!TIP]
   > Všechny změny, které jste provedli, jsou uvedeny v podokně **Steps** (Kroky). V podokně **Steps** (Kroky) přejděte ke kroku, který jste vytvořili, vyberte šipku dolů a pak vyberte **Edit** (Upravit). Zobrazí se upřesňující dialogové okno **Derive Column by Example** (Odvodit sloupec podle příkladu). Tady jsou zachovány všechny vaše příklady. Příklady můžete přidat také ručně dvojím kliknutím na řádek v následující mřížce. Výběrem tlačítka **Cancel** (Zrušit) se vrátíte zpět do hlavní mřížky bez použití změn. Do tohoto zobrazení můžete také přejít výběrem možnosti **Advanced mode** (Rozšířený režim) při provádění transformace **Derive Column by Example** (Odvodit sloupec podle příkladu).

6. Sloupec přejmenujte dvojím kliknutím na záhlaví sloupce a zadáním názvu **Hour Range** (Hodinové rozmezí). Stisknutím klávesy Enter změnu uložte.

   ![Přejmenování sloupce](media/tutorial-bikeshare-dataprep/weatherhourrangecolumnrename.png)

7. Abyste odvodili rozmezí data a času, vyberte současně sloupce **Date\_1** (Datum_1) a **Hour Range** (Hodinové rozmezí), klikněte pravým tlačítkem a pak vyberte **Derive Column by Example** (Odvodit sloupec podle příkladu).

   ![Derive Column by Example (Odvodit sloupec podle příkladu)](media/tutorial-bikeshare-dataprep/weatherderivedatehourrange.png)

   Do prvního řádku zadejte jako příklad **Jan 01, 2015 12AM-2AM** (1. ledna 2015, 0:00–2:00) a stiskněte Enter.

   Aplikace Workbench určí transformaci podle příkladu, který poskytnete. V tomto příkladu je výsledkem to, že se formát data změní a zřetězí se na dvouhodinové intervaly.

   ![Příklad Jan 01, 2015 12AM-2AM (1. ledna 2015, 0:00–2:00)](media/tutorial-bikeshare-dataprep/wetherdatehourrangeexample.png)

   > [!IMPORTANT]
   > Na počítači Mac proveďte následující krok místo kroku 8:
   >
   > * Přejděte do první buňky obsahující hodnotu **Feb 01, 2015 12AM-2AM** (1. února 2015, 0:00–2:00). Měla by být na řádku 15. Opravte hodnotu na **Jan 02, 2015 12AM-2AM** (2. ledna 2015, 0:00–2:00) a stiskněte Enter. 
   

8. Počkejte, než se stav změní z **Analyzing Data** (Analyzují se data) na **Review next suggested row** (Zkontrolovat další navržený řádek). Tato změna může trvat několik sekund. Výběrem odkazu stavu přejděte na navrhovaný řádek. 

   ![Navrhovaný řádek ke kontrole](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguate.png)

   Řádek má hodnotu null, protože zdrojová hodnota data může být buď dd/mm/rrrr, anebo mm/dd/rrrr. Zadejte správnou hodnotu **Jan 13, 2015 2AM-4AM** (13. ledna 2015, 2:00–4:00) a stiskněte Enter. Aplikace Workbench použije tyto dva příklady ke zlepšení odvozování zbývajících řádků.

   ![Správně formátovaná data](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguated.png)

9. Výběrem tlačítka **OK** transformaci přijměte.

   ![Dokončená mřížka transformace](media/tutorial-bikeshare-dataprep/weatherdatehourrangecomputed.png)

   > [!TIP]
   > Pokud chcete u tohoto kroku použít **Advanced mode** (Pokročilý režim) **Derive Column by Example** (Odvodit sloupec podle příkladu), vyberte šipku dolů v podokně **Steps** (Kroky). V datové mřížce jsou vedle sloupců **DATE\_1** (Datum_1) a **Hour Range** (Hodinové rozmezí) zaškrtávací políčka. Zrušte zaškrtnutí políčka vedle sloupce **Hour range** (Hodinové rozmezí) a podívejte se, jak se změní výstup. Protože sloupec **Hour Range** (Hodinové rozmezí) jako vstup chybí, považuje se hodnota **12AM-2AM** (0:00–2:00) za konstantu a je připojena k odvozeným hodnotám. Výběrem tlačítka **Cancel** (Zrušit) se vraťte zpět do hlavní mřížky bez použití změn.
   ![Rozšířený režim](media/tutorial-bikeshare-dataprep/derivedcolumnadvancededitdeselectcolumn.png)

10. Abyste mohli sloupec přejmenovat, poklikejte na jeho záhlaví. Změňte název na **Date Hour Range** (Hodinové rozmezí k datu) a pak stiskněte Enter.

11. Vyberte současně sloupce **DATE** (Datum), **DATE\_1** (Datum_1), **DATE\_2** (Datum_2) a **Hour Range** (Hodinové rozmezí). Klikněte pravým tlačítkem a pak vyberte **Remove column** (Odebrat sloupec).

## <a name="summarize-data-mean"></a>Sumarizace dat (střední hodnota)

Dalším krokem je sumarizace stavu počasí získáním středních hodnot z hodnot seskupených podle hodinových rozmezí.

1. Vyberte sloupec **Date Hour Range** (Hodinové rozmezí k datu) a potom v nabídce **Transforms** (Transformace) vyberte **Summarize** (Sumarizovat).

    ![Nabídka Transforms (Transformace)](media/tutorial-bikeshare-dataprep/weathersummarizemenu.png)

2. Data můžete sumarizovat přetažením sloupců z mřížky v dolní části stránky do podoken vlevo a vpravo nahoře. Levé podokno obsahuje text **Drag columns here to group data** (Přetažením sloupců sem data seskupíte). Pravé podokno obsahuje text **Drag columns here to summarize data** (Přetažením sloupců sem data sumarizujete). 

    a. Přetáhněte sloupec **Date Hour Range** (Hodinové rozmezí k datu) z mřížky v dolní části stránky do levého podokna. Přetáhněte sloupce **HOURLYDRYBULBTEMPF** (Teplota suchého teploměru ve stupních Fahrenheita), **HOURLYRelativeHumidity** (Hodinová relativní vlhkost) a **HOURLYWindSpeed** (Hodinová rychlost větru) do pravého podokna. 

    b. V pravém podokně vyberte v části **Aggregate** (Agregace) možnost **Mean** (Střední hodnota) jako metriku u všech sloupců. Výběrem **OK** dokončete sumarizaci.

    ![Obrazovka se sumarizovanými daty](media/tutorial-bikeshare-dataprep/weathersummarize.png)

## <a name="transform-dataflow-by-using-script"></a>Transformace toku dat pomocí skriptu

Změna dat v číselných sloupcích na rozmezí 0 až 1 umožňuje u některých modelů rychlou konvergenci. Momentálně není k dispozici žádná předdefinovaná transformace, která by tuto transformaci prováděla genericky. K provedení této operace použijte skript v jazyce Python.

1. V nabídce **Transform** (Transformace) vyberte **Transform Dataflow (Script)** (Transformovat tok dat pomocí skriptu).

2. Do textového pole, které se zobrazí, zadejte následující kód. Pokud jste používali stejné názvy sloupců, měl by kód fungovat bez úprav. V jazyce Python vytváříte jednoduchou logiku normalizace minimálních a maximálních hodnot.

    > [!WARNING]
    > Tento skript předpokládá použití stejných názvů sloupců, jaké se používaly v předchozích částech tohoto kurzu. Pokud používáte jiné názvy sloupců, musíte názvy ve skriptu změnit.

   ```python
   maxVal = max(df["HOURLYDRYBULBTEMPF_Mean"])
   minVal = min(df["HOURLYDRYBULBTEMPF_Mean"])
   df["HOURLYDRYBULBTEMPF_Mean"] = (df["HOURLYDRYBULBTEMPF_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYDRYBULBTEMPF_Mean":"N_DryBulbTemp"},inplace=True)

   maxVal = max(df["HOURLYRelativeHumidity_Mean"])
   minVal = min(df["HOURLYRelativeHumidity_Mean"])
   df["HOURLYRelativeHumidity_Mean"] = (df["HOURLYRelativeHumidity_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYRelativeHumidity_Mean":"N_RelativeHumidity"},inplace=True)

   maxVal = max(df["HOURLYWindSpeed_Mean"])
   minVal = min(df["HOURLYWindSpeed_Mean"])
   df["HOURLYWindSpeed_Mean"] = (df["HOURLYWindSpeed_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYWindSpeed_Mean":"N_WindSpeed"},inplace=True)

   df
   ```

    > [!TIP]
    > Skript v jazyce Python musí na konci vrátit hodnotu `df`. Tato hodnota se používá k naplnění mřížky.
    
   ![Dialogové okno Transform Dataflow (Script) (Transformovat tok dat pomocí skriptu)](media/tutorial-bikeshare-dataprep/transformdataflowscript.png)

3. Výběrem tlačítka __OK__ skript použijte. Číselné sloupce v mřížce teď obsahují hodnoty v rozmezí 0 až 1.

    ![Mřížka obsahující hodnoty mezi 0 a 1](media/tutorial-bikeshare-dataprep/datagridwithdecimals.png)

Právě jste dokončili přípravu dat o počasí. Teď připravíte data o jízdách.

## <a name="load-trip-data"></a>Načtení dat o jízdách

1. Naimportujte soubor `201701-hubway-tripdata.csv` podle postupu v části [Vytvoření nového zdroje dat](#newdatasource). Během procesu importu použijte následující možnosti:

    * V části __File Selection__ (Výběr souboru) vyberte při procházení a vybírání souboru **Azure Blob**.

    * V části __Sampling Scheme__ (Schéma vzorkování) vyberte schéma vzorkování **Full File** (Celý soubor) a nastavte ho jako aktivní.

    * V části __Data Type__ (Datový typ) přijměte výchozí hodnoty.

2. Po importu dat začněte připravovat data výběrem možnosti __Prepare__ (Příprava). Vyberte existující balíček **BikeShare Data Prep.dprep** a potom vyberte __OK__.

    Tento proces nevytvoří nový soubor, ale přidá **tok dat** do stávajícího souboru **pro přípravu dat**.

    ![Výběr existujícího balíčku](media/tutorial-bikeshare-dataprep/addjandatatodprep.png)

3. Po načtení mřížky rozbalte položku __DATAFLOWS__ (Toky dat). Teď máte dva toky dat: **BostonWeather** a **201701-hubway-tripdata**. Vyberte položku **201701-hubway-tripdata**.

    ![Položka 201701-hubway-tripdata](media/tutorial-bikeshare-dataprep/twodfsindprep.png)

## <a name="use-the-map-inspector"></a>Použití mapového inspektoru

Pro přípravu řetězcových, číselných a geografických dat jsou k dispozici užitečné vizualizace označované jako inspektory. Ty pomáhají lépe porozumět datům a identifikovat odlehlé hodnoty. Podle následujícího postupu použijte mapový inspektor.

1. Vyberte současně sloupce **start station latitude** (Zeměpisná šířka stanice začátku jízdy) a **start station longitude** (Zeměpisná délka stanice začátku jízdy). Klikněte pravým tlačítkem na některý ze sloupců a pak vyberte **Map** (Mapa).

    > [!TIP]
    > Pokud chcete povolit vícenásobný výběr, podržte stisknutou klávesu Ctrl (na Macu klávesu Command ⌘) a vyberte záhlaví jednotlivých sloupců.

    ![Vizualizace mapy](media/tutorial-bikeshare-dataprep/launchMapInspector.png)

2. Mapovou vizualizaci můžete maximalizovat výběrem ikony **Maximize** (Maximalizovat). Pokud chcete mapu přizpůsobit velikosti okna, vyberte ikonu **E** v levé horní části vizualizace.

    ![Obrázek maximalizované mapy](media/tutorial-bikeshare-dataprep/maximizedmap.png)

3. Výběrem tlačítka **Minimize** (Minimalizovat) se vrátíte do zobrazení mřížky.

## <a name="use-the-column-statistics-inspector"></a>Použití inspektoru statistiky sloupců

Když chcete použít inspektor statistiky sloupců, klikněte pravým tlačítkem na sloupec __tripduration__ (Trvání jízdy) a vyberte __Column Statistics__ (Statistika sloupce).

![Položka Column Statistics (Statistika sloupce)](media/tutorial-bikeshare-dataprep/tripdurationcolstats.png)

Tento postup přidá novou vizualizaci s názvem __tripduration Statistics__ (Statistika trvání jízd) do podokna __INSPECTORS__ (Inspektory).

![Inspektor tripduration Statistics (Statistika trvání jízd)](media/tutorial-bikeshare-dataprep/tripdurationcolstatsinwell.png)

> [!IMPORTANT]
> Maximální hodnota trvání jízdy je 961 814 minut, což jsou asi dva roky. Zdá se, že datová sada obsahuje nějaké odlehlé hodnoty.

## <a name="use-the-histogram-inspector"></a>Použití inspektoru s histogramem

Pokud se chcete pokusit identifikovat odlehlé hodnoty, klikněte pravým tlačítkem na sloupec __tripduration__ (Trvání jízdy) a vyberte __Histogram__.

![Inspektor s histogramem](media/tutorial-bikeshare-dataprep/tripdurationhistogram.png)

Histogram není užitečný, protože odlehlé hodnoty zkreslují graf.

## <a name="add-a-column-by-using-script"></a>Přidání sloupce pomocí skriptu

1. Klikněte pravým tlačítkem na sloupec **tripduration** (Trvání jízdy) a vyberte **Add Column (Script)** (Přidat sloupec pomocí skriptu).

    ![Nabídka Add Column (Script) (Přidat sloupec pomocí skriptu)](media/tutorial-bikeshare-dataprep/computecolscript.png)

2. V dialogovém okně __Add Column (Script)__ (Přidat sloupec pomocí skriptu) použijte následující hodnoty:

    * __New Column Name__ (Název nového sloupce): logtripduration (Logaritmus trvání jízdy)

    * __Insert this New Column After__ (Vložit tento nový sloupec za): tripduration (Trvání jízdy)

    * __New Column Code__ (Kód nového sloupce): `math.log(row.tripduration)`

    * __Code Block Type__ (Typ bloku kódu): Expression (Výraz)

   ![Dialogové okno Add Column (Script) (Přidat sloupec pomocí skriptu)](media/tutorial-bikeshare-dataprep/computecolscriptdialog.png)

3. Výběrem tlačítka __OK__ sloupec **logtripduration** (Logaritmus trvání jízdy) přidejte.

4. Klikněte na sloupec pravým tlačítkem a vyberte **Histogram**.

    ![Histogram sloupce logtripduration (Logaritmus trvání jízdy)](media/tutorial-bikeshare-dataprep/logtriphistogram.png)

    Tento histogram vypadá jako normální rozdělení dat s neobvyklým zakončením.

## <a name="use-an-advanced-filter"></a>Použití rozšířeného filtru

Když u dat použijete filtr, inspektor se zaktualizuje a zobrazí se nové rozdělení dat. 

1. Klikněte pravým tlačítkem na sloupec **logtripduration** (Logaritmus trvání jízdy) a vyberte **Filter Column** (Filtrovat sloupec). 

2. V dialogovém okně __Edit__ (Upravit) použijte následující hodnoty:

    * __Filter this Number Column__ (Filtrovat tento číselný sloupec): logtripduration (Logaritmus trvání jízdy)

    * __I Want To__ (Chci): Keep Rows (Zachovat řádky)

    * __When__ (Když): Any of the Conditions below are True (logical OR) (Všechny podmínky níže mají hodnotu Pravda neboli logickou hodnotu OR)

    * __If this Column__ (Pokud je tento sloupec): less than (menší než)

    * __The Value__ (Hodnota): 9

    ![Možnosti filtru](media/tutorial-bikeshare-dataprep/loftripfilter.png)

3. Výběrem tlačítka __OK__ filtr použijte.

    ![Aktualizované histogramy po použití filtru](media/tutorial-bikeshare-dataprep/loftripfilteredinspector.png)

### <a name="the-halo-effect"></a>Haló efekt

1. Maximalizujte histogram **logtripduration** (Logaritmus trvání jízdy). Zobrazí se modrý histogram překrývající šedý histogram. Toto zobrazení se nazývá **haló efekt**:

    * Šedý histogram představuje rozdělení před provedením operace (v tomto případě je to operace filtrování).

    * Modrý histogram představuje histogram po provedení této operace. 

   Haló efekt pomáhá vizualizovat, jaký má operace vliv na data.

   ![Haló efekt](media/tutorial-bikeshare-dataprep/loftripfilteredinspectormaximized.png)

    > [!NOTE]
    > Modrý histogram vypadá menší ve srovnání s předchozím histogramem. Tento rozdíl je způsobený automatickým přerozdělením dat v rámci nového rozmezí.

2. Pokud chcete haló efekt odebrat, vyberte __Edit__ (Upravit) a zrušte zaškrtnutí políčka __Show halo__ (Zobrazit haló efekt).

    ![Možnosti histogramu](media/tutorial-bikeshare-dataprep/uncheckhalo.png)

3. Výběrem **OK** zakažte haló efekt. Potom histogram minimalizujte.

### <a name="remove-columns"></a>Odebrání sloupců

V datech o jízdách představuje každý řádek jednu událost vyzvednutí kola. Pro účely tohoto kurzu potřebujete jenom sloupce **starttime** (Čas začátku jízdy) a **start station id** (ID stanice začátku jízdy). Ostatní sloupce odeberete tak, že současně vyberete tyto dva sloupce, kliknete pravým tlačítkem na záhlaví sloupce a pak vyberete **Keep Column** (Zachovat sloupec). Ostatní sloupce se odeberou.

![Možnost Keep Column (Zachovat sloupec)](media/tutorial-bikeshare-dataprep/tripdatakeepcolumn.png)

### <a name="summarize-data-count"></a>Sumarizace dat (počet)

K sumarizaci poptávky po kolech ve dvouhodinových intervalech použijte odvozené sloupce.

1. Klikněte pravým tlačítkem na sloupec **starttime** (Čas začátku jízdy) a vyberte **Derive Column by Example** (Odvodit sloupec podle příkladu).

    ![Možnosti Derive Column by Example (Odvodit sloupec podle příkladu)](media/tutorial-bikeshare-dataprep/tripdataderivebyexample.png)

2. Do prvního řádku zadejte jako příklad hodnotu **Jan 01, 2017 12AM-2AM** (1. ledna 2017, 0:00–2:00).

    > [!IMPORTANT]
    > V předchozím příkladu odvozování sloupců jste k odvození sloupce, který obsahoval datum a časový interval, museli provést několik kroků. V tomto příkladu uvidíte, že tuto operaci je možné provést v jednom kroku tím, že poskytnete příklad konečného výstupu.

    > [!NOTE]
    > Příklad můžete zadat do libovolného řádku. V tomto příkladu je hodnota **Jan 01, 2017 12AM-2AM** (1. ledna 2017, 0:00–2:00) platnou hodnotou prvního řádku dat.

    ![Příklad dat](media/tutorial-bikeshare-dataprep/tripdataderivebyexamplefirstexample.png)

   > [!IMPORTANT]
   > Na počítači Mac proveďte tento krok místo kroku 3:
   >
   > * Přejděte do první buňky obsahující hodnotu **Jan 01, 2017 1AM-2AM** (1. ledna 2017, 1:00–2:00). Měla by být na řádku 14. Opravte hodnotu na **Jan 01, 2017 12AM-2AM** (1. ledna 2017, 0:00–2:00) a stiskněte Enter. 

3. Počkejte, než aplikace vypočítá hodnoty pro všechny řádky. Tento proces může trvat několik sekund. Po dokončení analýzy zkontrolujte data pomocí odkazu __Review next suggested row__ (Zkontrolovat další navržený řádek).

   ![Dokončená analýza s odkazem na kontrolu](media/tutorial-bikeshare-dataprep/tripdatabyexanalysiscomplete.png)

    Ujistěte se, že jsou vypočítané hodnoty správné. Pokud nejsou, aktualizujte příslušnou hodnotu na očekávanou hodnotu a stiskněte Enter. Potom počkejte na dokončení analýzy. Pokračujte v provádění procesu **Review next suggested row** (Zkontrolovat další navržený řádek), dokud se nezobrazí text **No suggestions** (Žádné návrhy). Text **No suggestions** (Žádné návrhy) znamená, že aplikace prohlédla hraniční případy a považuje syntetizovaný program za dokončený. V rámci osvědčených postupů je před přijetím transformace vhodné provést vizuální kontrolu transformovaných dat. 

4. Výběrem tlačítka **OK** transformaci přijměte. Přejmenujte nově vytvořený sloupec na **Date Hour Range** (Hodinové rozmezí k datu).

    ![Přejmenovaný sloupec](media/tutorial-bikeshare-dataprep/tripdatasummarize.png)

5. Klikněte pravým tlačítkem na záhlaví sloupce **starttime** (Čas začátku jízdy) a vyberte **Remove column** (Odebrat sloupec).

6. Data můžete sumarizovat výběrem možnosti __Summarize__ (Sumarizovat) v nabídce __Transform__ (Transformace). Transformaci vytvoříte pomocí následujících kroků:

    * Přetáhněte sloupce __Date Hour Range__ (Hodinové rozmezí k datu) a __start station id__ (ID stanice začátku jízdy) do podokna **Group By** (Seskupit podle) na levé straně.

    * Sloupec __start station id__ (ID stanice začátku jízdy) přetáhněte do podokna **summarize data** (Sumarizace dat) na pravé straně.

   ![Možnosti sumarizace](media/tutorial-bikeshare-dataprep/tripdatacount.png)

7. Výběrem tlačítka **OK** přijměte výsledek sumarizace.

## <a name="join-dataflows"></a>Spojení toků dat

Podle následujícího postupu spojte data o počasí s daty o jízdách:

1. V nabídce __Transforms__ (Transformace) vyberte __Join__ (Spojit).

2. V části __Tables__ (Tabulky) vyberte **BostonWeather** jako **Left** (Levý) tok dat a **201701-hubway-tripdata** jako **Right** (Pravý) tok dat. Pokračujte výběrem tlačítka **Next** (Další).

    ![Výběry tabulek](media/tutorial-bikeshare-dataprep/jointableselection.png)

3. V části __Key Columns__ (Klíčové sloupce) vyberte u obou tabulek sloupec **Date Hour Range** (Hodinové rozmezí k datu) a pak vyberte __Next__ (Další).

    ![Výběry klíčových sloupců](media/tutorial-bikeshare-dataprep/joinkeyselection.png)

4. V části __Join Type__ (Typ spojení) vyberte jako typ spojení možnost __Matching rows__ (Odpovídající řádky) a pak vyberte __Finish__ (Dokončit).

    ![Typ spojení Matching row (Odpovídající řádky)](media/tutorial-bikeshare-dataprep/joinscreen.png)

    Tento proces vytvoří nový tok dat s názvem __Join Result__ (Výsledek spojení).

## <a name="create-additional-features"></a>Vytvoření dalších funkcí

1. Když chcete vytvořit sloupec, který bude obsahovat den v týdnu, klikněte pravým tlačítkem na sloupec **Date Hour Range** (Hodinové rozmezí k datu) a vyberte **Derive Column by Example** (Odvodit sloupec podle příkladu). U data, které připadá na neděli, použijte hodnotu __Sun__ (neděle). Příklad: **Jan 01, 2017 12AM-2AM** (1. ledna 2017, 0:00–2:00). Stiskněte Enter a pak vyberte **OK**. Přejmenujte tento sloupec na __Weekday__ (Den v týdnu).

    ![Vytvoření nového sloupce pro den v týdnu](media/tutorial-bikeshare-dataprep/featureweekday.png)

2. Když chcete vytvořit sloupec, který bude obsahovat časový interval z příslušného řádku, klikněte pravým tlačítkem na sloupec **Date Hour Range** (Hodinové rozmezí k datu) a vyberte **Derive Column by Example** (Odvodit sloupec podle příkladu). U řádku, který obsahuje hodnotu **Jan 01, 2017 12AM-2AM** (1. ledna 2017, 0:00–2:00), použijte hodnotu **12AM-2AM** (0:00–2:00). Stiskněte Enter a pak vyberte **OK**. Přejmenujte tento sloupec na **Period** (Časový interval).

    ![Sloupec Period (Časový interval)](media/tutorial-bikeshare-dataprep/featurehourrange.png)

3. Pokud chcete odebrat sloupce **Date Hour Range** (Hodinové rozmezí k datu) a **r_Date Hour Range** (r_Hodinové rozmezí k datu), stiskněte klávesu Ctrl (na Macu klávesu Command ⌘) a pak vyberte jednotlivá záhlaví sloupců. Klikněte pravým tlačítkem a vyberte **Remove column** (Odebrat sloupec).

## <a name="read-data-from-python"></a>Čtení dat z prostředí Python

Balíček pro přípravu dat můžete spustit v prostředí Python nebo PySpark a výsledek načíst v podobě **datového rámce**.

Pokud chcete vygenerovat ukázku skriptu v jazyce Python, klikněte pravým tlačítkem na balíček __BikeShare Data Prep__ a vyberte __Generate Data Access Code File__ (Vygenerovat soubor s kódem pro přístup k datům). Ukázkový soubor v jazyce Python se vytvoří ve vaší **projektové složce** a načte se také na kartě aplikace Workbench. Následující skript jazyka Python je příkladem takto vygenerovaného kódu:

```python
# Use the Azure Machine Learning data preparation package
from azureml.dataprep import package

# Use the Azure Machine Learning data collector to log various metrics
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# This call will load the referenced package and return a DataFrame.
# If run in a PySpark environment, this call returns a
# Spark DataFrame. If not, it will return a Pandas DataFrame.
df = package.run('BikeShare Data Prep.dprep', dataflow_idx=0)

# Remove this line and add code that uses the DataFrame
df.head(10)
```

V tomto kurzu je název souboru `BikeShare Data Prep.py`. Tento soubor se používá později v tomto kurzu.

## <a name="save-test-data-as-a-csv-file"></a>Uložení testovacích dat do souboru .csv

Pokud chcete uložit tok dat **Join Result** (Výsledek spojení) do souboru .csv, musíte změnit skript `BikeShare Data Prep.py`. 

1. Otevřete projekt pro úpravy ve Visual Studio Code.

    ![Otevření projektu ve Visual Studio Code](media/tutorial-bikeshare-dataprep/openprojectinvscode.png)

2. Aktualizujte skript v jazyce Python v souboru `BikeShare Data Prep.py` pomocí následujícího kódu:

    ```python
    import pyspark

    from azureml.dataprep.package import run
    from pyspark.sql.functions import *

    # start Spark session
    spark = pyspark.sql.SparkSession.builder.appName('BikeShare').getOrCreate()

    # dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
    df = run('BikeShare Data Prep.dprep', dataflow_idx=2)
    df.show(n=10)
    row_count_first = df.count()

    # Example file name: 'wasb://data-files@bikesharestorage.blob.core.windows.net/testata'
    # 'wasb://<your container name>@<your azure storage name>.blob.core.windows.net/<csv folder name>
    blobfolder = 'Your Azure Storage blob path'

    df.write.csv(blobfolder, mode='overwrite') 

    # retrieve csv file parts into one data frame
    csvfiles = "<Your Azure Storage blob path>/*.csv"
    df = spark.read.option("header", "false").csv(csvfiles)
    row_count_result = df.count()
    print(row_count_result)
    if (row_count_first == row_count_result):
        print('counts match')
    else:
        print('counts do not match')
    print('done')
    ```

3. Nahraďte `Your Azure Storage blob path` cestou k výstupnímu souboru, který se má vytvořit. Nahraďte proměnné `blobfolder` a `csvfiles`.

## <a name="create-an-hdinsight-run-configuration"></a>Vytvoření konfigurace spuštění služby HDInsight

1. V aplikaci Machine Learning Workbench otevřete okno příkazového řádku, vyberte nabídku **Soubor** a pak vyberte **Otevřít příkazový řádek**. Příkazový řádek se spustí ve složce projektu s příkazem `C:\Projects\BikeShare>`.

    ![Otevřít příkazový řádek](media/tutorial-bikeshare-dataprep/opencommandprompt.png)

   >[!IMPORTANT]
   >Okno příkazového řádku (otevřené z aplikace Workbench) musíte použít k provedení následujících kroků.

2. Pomocí příkazového řádku se přihlaste k Azure. 

   Aplikace Workbench a rozhraní příkazového řádku používají při ověřování prostředků Azure nezávislé mezipaměti přihlašovacích údajů. Tento krok je potřeba provést jenom jednou, než vyprší platnost tokenu v mezipaměti. Příkaz `az account list` vrátí seznam předplatných dostupných pro vaše přihlášení. Pokud jich je více než jedno, použijte hodnotu ID z požadovaného předplatného. Nastavte toto předplatné jako výchozí účet pro použití pomocí příkazu `az account set -s` a pak zadejte hodnotu ID předplatného. Potom nastavení ověřte pomocí příkazu account `show`.

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

3. Vytvořte konfiguraci spuštění služby HDInsight. Potřebujete název vašeho clusteru a heslo uživatele `sshuser`.

    ```azurecli
    az ml computetarget attach cluster --name hdinsight --address <yourclustername>.azurehdinsight.net --username sshuser --password <your password>
    az ml experiment prepare -c hdinsight
    ```
> [!NOTE]
> Když se vytvoří prázdný projekt, jsou výchozí konfigurace spuštění **local** (místní) a **docker**. Tento krok vytvoří novou konfiguraci spuštění, která bude dostupná v aplikaci Workbench při spouštění vašich skriptů. 

## <a name="run-in-an-hdinsight-cluster"></a>Spuštění v clusteru HDInsight

Vraťte se do aplikace Machine Learning Workbench a spusťte svůj skript v clusteru HDInsight.

1. Vraťte se na domovskou stránku vašeho projektu výběrem ikony **Home** (Domů) na levé straně.

2. V rozevíracím seznamu vyberte **hdinsight**, aby se váš skript spustil v clusteru HDInsight.

3. Vyberte **Run** (Spustit). Skript se odešle jako úloha. Po zapsání souboru do zadaného umístění ve vašem kontejneru úložiště se stav úlohy změní na __Completed__(Dokončeno).

    ![Spuštění skriptu ve službě HDInsight](media/tutorial-bikeshare-dataprep/hdinsightrunscript.png)


## <a name="substitute-data-sources"></a>Nahrazení zdrojů dat

V předchozích krocích jste k přípravě testovacích dat použili zdroje dat `201701-hubway-tripdata.csv` a `BostonWeather.csv`. Pokud chcete balíček použít s ostatními soubory dat o jízdách, použijte následující postup:

1. Vytvořte nový zdroj dat pomocí dříve uvedeného postupu s následujícími změnami:

    * V části __File Selection__ (Výběr souboru) vyberte současně všech šest zbývajících souborů .csv s daty o jízdách.

    ![Načtení šesti zbývajících souborů](media/tutorial-bikeshare-dataprep/browseazurestoragefortripdatafiles.png)

    > [!NOTE]
     > Položka __+5__ označuje, že kromě uvedeného souboru je vybráno pět dalších souborů.

    * V části __File Details__ (Podrobnosti souboru) nastavte v poli __Promote Headers Mode__ (Režim povýšení záhlaví) hodnotu **All Files Have The Same Headers** (Všechny soubory mají stejná záhlaví). Tato hodnota označuje, že všechny soubory obsahují stejné záhlaví.

    ![Výběr podrobností souborů](media/tutorial-bikeshare-dataprep/headerfromeachfile.png) 

   Název tohoto zdroje dat uložte, protože se používá v pozdějších krocích.

2. Vyberte ikonu složky, abyste si mohli prohlédnout soubory v projektu. Rozbalte adresář __aml\_config__ a pak vyberte soubor `hdinsight.runconfig`.

    ![Umístění souboru hdinsight.runconfig](media/tutorial-bikeshare-dataprep/hdinsightsubstitutedatasources.png) 

3. Výběrem tlačítka **Edit** (Upravit) otevřete soubor ve Visual Studio Code.

4. Na konec souboru `hdinsight.runconfig` přidejte následující řádky a potom soubor uložte výběrem ikony disku.

    ```yaml
    DataSourceSubstitutions:
      201701-hubway-tripdata.dsource: 201501-hubway-tripdata.dsource
    ```

    Tato změna nahradí původní zdroj dat zdrojem, který obsahuje šest souborů s daty o jízdách.

## <a name="save-training-data-as-a-csv-file"></a>Uložení trénovacích dat do souboru .csv

1. Přejděte do souboru `BikeShare Data Prep.py` v jazyce Python, který jste upravili dříve. Zadejte jinou cestu k souboru pro uložení trénovacích dat.

    ```python
    import pyspark

    from azureml.dataprep.package import run
    from pyspark.sql.functions import *

    # start Spark session
    spark = pyspark.sql.SparkSession.builder.appName('BikeShare').getOrCreate()

    # dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
    df = run('BikeShare Data Prep.dprep', dataflow_idx=2)
    df.show(n=10)
    row_count_first = df.count()

    # Example file name: 'wasb://data-files@bikesharestorage.blob.core.windows.net/traindata'
    # 'wasb://<your container name>@<your azure storage name>.blob.core.windows.net/<csv folder name>
    blobfolder = 'Your Azure Storage blob path'

    df.write.csv(blobfolder, mode='overwrite') 

    # retrieve csv file parts into one data frame
    csvfiles = "<Your Azure Storage blob path>/*.csv"
    df = spark.read.option("header", "false").csv(csvfiles)
    row_count_result = df.count()
    print(row_count_result)
    if (row_count_first == row_count_result):
        print('counts match')
    else:
        print('counts do not match')
    print('done')
    ```

2. Pro výstup trénovacích dat použijte složku `traindata`.

3. Odešlete novou úlohu výběrem možnosti **Run** (Spustit). Ujistěte se, že je vybraná možnost **hdinsight**. Úloha se odešle s novou konfigurací. Výstupem této úlohy jsou trénovací data. Tato data se vytvoří pomocí stejného postupu přípravy dat, který jste používali v předchozích krocích. Dokončení úlohy může trvat několik minut.


## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky
Dokončili jste kurz přípravy dat projektu BikeShare. V tomto kurzu jste se naučili, jak pomocí služby Machine Learning (Preview) provést následující úlohy:
> [!div class="checklist"]
> * Interaktivní příprava dat pomocí nástroje pro přípravu dat služby Machine Learning
> * Import, transformace a vytvoření testovací datové sady
> * Vygenerování balíčku pro přípravu dat
> * Spuštění balíčku pro přípravu dat pomocí Pythonu
> * Vygenerování trénovací datové sady opětovným použitím balíčku pro přípravu dat s dalšími vstupními soubory

Dále si můžete přečíst další informace o přípravě dat:
> [!div class="nextstepaction"]
> [Uživatelský průvodce přípravou dat](data-prep-user-guide.md)
