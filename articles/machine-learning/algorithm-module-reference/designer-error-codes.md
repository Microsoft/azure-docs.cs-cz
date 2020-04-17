---
title: Poradce při potížích s chybami modulu
titleSuffix: Azure Machine Learning
description: Poradce při potížích s výjimkami modulu v návrháři Azure Machine Learning pomocí kódů chyb
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/16/2020
ms.openlocfilehash: cc04d11475568af92ba6a617a1eb6b2b51accb45
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481666"
---
# <a name="exceptions-and-error-codes-for-the-designer-preview"></a>Výjimky a kódy chyb pro návrháře (náhled)

Tento článek popisuje chybové zprávy a kódy výjimek v návrháři Azure Machine Learning (preview), které vám pomohou vyřešit vaše kanály strojového učení.

Chybovou zprávu najdete v návrháři následujícíkroky:  

- Vyberte vadný modul, přejděte na kartu **Výstupy + protokoly,** můžete najít podrobný protokol v souboru **70_driver_log.txt** v kategorii **azureml-logs.**

- Pro podrobnou chybu modulu, můžete zkontrolovat v error_info.json v **module_statistics** kategorii.

Následují chybové kódy modulů v návrháři.

## <a name="error-0001"></a>Chyba 0001  
 K výjimce dochází, pokud nebyl nalezen jeden nebo více zadaných sloupců sady dat.  

 Tato chyba se zobrazí, pokud je pro modul proveden výběr sloupce, ale vybrané sloupce neexistují v sadě vstupních dat. K této chybě může dojít, pokud jste ručně zadali název sloupce nebo pokud volič sloupců poskytl navrhovaný sloupec, který v datové sadě při spuštění kanálu neexistoval.  

**Rozlišení:** Znovu navštivte modul vyvolání této výjimky a ověřte, zda jsou název sloupce nebo názvy správné a zda existují všechny odkazované sloupce.  

|Zprávy o výjimky|
|------------------------|
|Jeden nebo více zadaných sloupců nebylo nalezeno.|
|Sloupec s názvem nebo indexem {column_id} nebyl nalezen.|
|Sloupec s názvem nebo indexem {column_id} v {arg_name_missing_column} neexistuje.|
|Sloupec s názvem nebo indexem {column_id} neexistuje v {arg_name_missing_column}, ale existuje v {arg_name_has_column}.|
|Sloupce s názvem nebo indexem {column_names} nebyly nalezeny.|
|Sloupce s názvem nebo indexem {column_names} v {arg_name_missing_column} neexistují.|
|Sloupce s názvem nebo indexem {column_names} neexistují v {arg_name_missing_column}, ale existují v {arg_name_has_column}.|


## <a name="error-0002"></a>Chyba 0002  
 Výjimka nastává, pokud jeden nebo více parametrů nelze analyzovat nebo převést ze zadaného typu na požadované typem cílové metody.  

 K této chybě dochází v Azure Machine Learning při zadání parametru jako vstup a typ hodnoty se liší od typu, který je očekáván a implicitní převod nelze provést.  

**Rozlišení:** Zkontrolujte požadavky modulu a určete, který typ hodnoty je vyžadován (řetězec, celé číslo, dvojité atd.)  

|Zprávy o výjimky|
|------------------------|
|Nepodařilo se analyzovat parametr.|
|Analýza parametru {arg_name_or_column} se nezdařila.|
|Nepodařilo se převést parametr {arg_name_or_column} na {to_type}.|
|Nepodařilo se převést parametr {arg_name_or_column} z {from_type} na {to_type}.|
|Nepodařilo se převést hodnotu parametru {arg_name_or_column} {arg_value} z {from_type} na {to_type}.|
|Nepodařilo se převést hodnotu {arg_value} ve sloupci {arg_name_or_column} z {from_type} na {to_type} s použitím formátu {fmt}" za předpokladu.|


## <a name="error-0003"></a>Chyba 0003  
 K výjimce dochází, pokud jeden nebo více vstupů jsou null nebo prázdné.  

 Tuto chybu se zobrazí v Azure Machine Learning, pokud všechny vstupy nebo parametry modulu jsou nulové nebo prázdné.  K této chybě může dojít, například pokud jste nezadali žádnou hodnotu parametru. Může k tomu dojít také v případě, že jste zvolili datovou sadu, která obsahuje chybějící hodnoty, nebo prázdnou datovou sadu.  

**Rozlišení:**

+ Otevřete modul, který vytvořil výjimku a ověřte, zda byly zadány všechny vstupy. Ujistěte se, že jsou zadány všechny požadované vstupy. 
+ Ujistěte se, že data načtená z úložiště Azure jsou přístupná a že se nezměnil název účtu nebo klíč.  
+ Zkontrolujte vstupní data pro chybějící hodnoty nebo nulls.
+ Pokud používáte dotaz ve zdroji dat, ověřte, že data jsou vrácena ve formátu, který očekáváte. 
+ Zkontrolujte překlepy nebo jiné změny ve specifikaci dat.
  
|Zprávy o výjimky|
|------------------------|
|Jeden nebo více vstupů jsou null nebo prázdné.|
|Vstup {name}" je null nebo prázdný.|


## <a name="error-0004"></a>Chyba 0004  
 K výjimce dochází, pokud je parametr menší nebo roven určité hodnotě.  

 Tuto chybu obdržíte v Azure Machine Learning, pokud je parametr ve zprávě pod hraniční hodnotou požadovanou pro modul ke zpracování dat.  

**Rozlišení:** Znovu modul vyvolání výjimky a upravit parametr být větší než zadaná hodnota.  

|Zprávy o výjimky|
|------------------------|
|Parametr by měl být větší než hodnota hranice.|
|Hodnota parametru {arg_name} by měla být větší než {lower_boundary}.|
|Parametr {arg_name} má hodnotu {actual_value}, která by měla být větší než {lower_boundary}.|


## <a name="error-0005"></a>Chyba 0005  
 K výjimce dochází, pokud je parametr menší než určitá hodnota.  

 Tuto chybu obdržíte v Azure Machine Learning, pokud je parametr ve zprávě nižší nebo roven hodnotě hranice požadované pro modul ke zpracování dat.  

**Rozlišení:** Znovu modul vyvolání výjimky a upravit parametr být větší nebo rovna zadané hodnoty.  

|Zprávy o výjimky|
|------------------------|
|Parametr by měl být větší nebo rovno hodnotě hranice.|
|Hodnota parametru {arg_name} by měla být větší nebo rovna {lower_boundary}.|
|Parametr {arg_name} má hodnotu {value}, která by měla být větší nebo rovna {lower_boundary}.|


## <a name="error-0006"></a>Chyba 0006  
 K výjimce dochází, pokud je parametr větší nebo roven zadané hodnotě.  

 Tato chyba se zobrazí v Azure Machine Learning, pokud je parametr ve zprávě větší nebo roven hodnotě hranice požadované pro modul ke zpracování dat.  

**Rozlišení:** Znovu modul vyvolání výjimky a upravit parametr být menší než zadaná hodnota.  

|Zprávy o výjimky|
|------------------------|
|Neshoda parametrů. Jeden z parametrů by měl být menší než jiný.|
|Hodnota parametru {arg_name} by měla být menší než hodnota parametru {upper_boundary_parameter_name}.|
|Parametr {arg_name} má hodnotu {value}, která by měla být menší než {upper_boundary_parameter_name}.|


## <a name="error-0007"></a>Chyba 0007  
 K výjimce dochází, pokud je parametr větší než určitá hodnota.  

 Tuto chybu se zobrazí v Azure Machine Learning, pokud jste ve vlastnostech modulu zadali hodnotu, která je větší, než je povoleno. Můžete například zadat data, která jsou mimo rozsah podporovaných dat, nebo můžete označit, že bude použito pět sloupců, pokud jsou k dispozici pouze tři sloupce. 

 Tato chyba se může zobrazit také v případě, že zadáváte dvě sady dat, které je třeba nějakým způsobem shodovat. Pokud například přejmenováváte sloupce a určujete sloupce podle indexu, musí počet zadaných názvů odpovídat počtu indexů sloupců. Dalším příkladem může být matematická operace, která používá dva sloupce, kde sloupce musí mít stejný počet řádků. 

**Rozlišení:**

 + Otevřete daný modul a zkontrolujte všechna nastavení číselných vlastností.
 + Ujistěte se, že všechny hodnoty parametrů spadají do podporovaného rozsahu hodnot pro tuto vlastnost.
 + Pokud modul trvá více vstupů, ujistěte se, že vstupy mají stejnou velikost.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Zkontrolujte, zda se změnila datová sada nebo zdroj dat. Někdy hodnota, která pracovala s předchozí verzí dat se nezdaří po počet sloupců, datové typy sloupců nebo velikost dat se změní.  

|Zprávy o výjimce|
|------------------------|
|Neshoda parametrů. Jeden z parametrů by měl být menší nebo roven jinému.|
|Hodnota parametru {arg_name} by měla být menší nebo rovna hodnotě parametru {upper_boundary_parameter_name}.|
|Parametr {arg_name} má hodnotu {actual_value}, která by měla být menší nebo rovna {upper_boundary}.|
|Parametr {arg_name} hodnota {actual_value} by měl být menší nebo roven hodnotě parametru {upper_boundary_parameter_name} {upper_boundary}.|


## <a name="error-0008"></a>Chyba 0008  
 K výjimce dochází, pokud parametr není v rozsahu.  

 Tato chyba se zobrazí v Azure Machine Learning, pokud parametr ve zprávě je mimo hranice potřebné pro modul ke zpracování dat.  

 Například tato chyba se zobrazí, pokud se pokusíte použít [Přidat řádky](add-rows.md) kombinovat dvě datové sady, které mají jiný počet sloupců.  

**Rozlišení:** Znovu přístup k modulu vyvolání výjimky a upravit parametr být v rámci zadaného rozsahu.  

|Zprávy o výjimky|
|------------------------|
|Hodnota parametru není v zadaném rozsahu.|
|Hodnota parametru {arg_name} není v rozsahu.|
|Hodnota parametru {arg_name} by měla být v rozsahu [{lower_boundary}, {upper_boundary}].|
|Hodnota parametru {arg_name} není v rozsahu. {důvod}|


## <a name="error-0009"></a>Chyba 0009  
 K výjimce dochází, když je nesprávně zadán název účtu úložiště Azure nebo název kontejneru.  

K této chybě dochází v návrháři Azure Machine Learning, když zadáte parametry pro účet úložiště Azure, ale název nebo heslo nelze přeložit. Chyby v heslech nebo názvech účtů se mohou stát z mnoha důvodů:

 + Účet je nesprávný typ. Některé nové typy účtů nejsou podporovány pro použití s návrhářem Machine Learning. Podrobnosti [najdete v tématu Import dat.](import-data.md)
 + Zadali jste nesprávný název účtu.
 + Účet již neexistuje.
 + Heslo pro účet úložiště je nesprávné nebo se změnilo
 + Nezadali jste název kontejneru nebo kontejner neexistuje.
 + Nebyla plně zadaná cesta k souboru (cesta k objektu blob).
   

**Rozlišení:**

K takovým problémům často dochází při pokusu o ruční zadání názvu účtu, hesla nebo cesty kontejneru. Doporučujeme použít nového průvodce pro modul [Import dat,](import-data.md) který vám pomůže vyhledat a zkontrolovat názvy.

Zkontrolujte také, zda byl odstraněn účet, kontejner nebo objekt blob. Pomocí jiného nástroje úložiště Azure ověřte, že název účtu a heslo byly zadány správně a že kontejner existuje. 

Některé novější typy účtů nejsou podporovány Azure Machine Learning. Například nové "horké" nebo "studené" typy úložišť nelze použít pro strojové učení. Klasické účty úložiště a účty úložiště vytvořené jako "obecné účely" fungují dobře.

Pokud byla zadána úplná cesta k objektu blob, ověřte, zda je cesta zadána jako **kontejner/název objektu blob**a zda v účtu existují kontejner i objekt blob.  

 Cesta by neměla obsahovat úvodní lomítko. Například **/container/blob** je nesprávná a měla by být zadána jako **kontejner/objekt blob**.  


|Zprávy o výjimky|
|------------------------|
|Název účtu úložiště Azure nebo název kontejneru je nesprávný.|
|Název účtu úložiště Azure {account_name} nebo název kontejneru {container_name} je nesprávný. byl očekáván název kontejneru/objektu blob formátu.|


## <a name="error-0010"></a>Chyba 0010  
 Výjimka nastane, pokud vstupní datové sady mají názvy sloupců, které by měly odpovídat, ale ne.  

 Tato chyba se zobrazí v Azure Machine Learning, pokud index sloupce ve zprávě má různé názvy sloupců ve dvou vstupních datových sad.  

**Rozlišení:** Použijte [upravit metadata](edit-metadata.md) nebo upravte původní datovou sadu tak, aby měla stejný název sloupce pro zadaný index sloupce.  

|Zprávy o výjimky|
|------------------------|
|Sloupce s odpovídajícím indexem ve vstupních datových sadách mají různé názvy.|
|Názvy sloupců nejsou stejné pro sloupec {col_index} (na základě nuly) vstupních datových sad ({dataset1} a {dataset2}).|


## <a name="error-0011"></a>Chyba 0011  
 Výjimka nastane, pokud předané argument sady sloupců se nevztahuje na žádný ze sloupců datové sady.  

 Tuto chybu se zobrazí v Azure Machine Learning, pokud zadaný výběr sloupce neodpovídá žádnému sloupci v dané datové sadě.  

 Tuto chybu můžete také získat, pokud jste nevybrali sloupec a pro práci modulu je vyžadován alespoň jeden sloupec.  

**Rozlišení:** Upravte výběr sloupců v modulu tak, aby se vztahoval na sloupce v datové sadě.  

 Pokud modul vyžaduje, abyste vybrali určitý sloupec, například sloupec popisku, ověřte, zda je vybrán pravý sloupec.  

 Pokud jsou vybrány nevhodné sloupce, odeberte je a znovu spusťte kanál.  

|Zprávy o výjimky|
|------------------------|
|Zadaná sada sloupců se nevztahuje na žádný ze sloupců datové sady.|
|Zadaná sada sloupců {column_set} se nevztahuje na žádný ze sloupců datové sady.|


## <a name="error-0012"></a>Chyba 0012  
 Výjimka nastane, pokud nelze vytvořit instanci třídy s předanou sadou argumentů.  

**Rozlišení:** Tato chyba není žalovatelné uživatelem a bude zastaralé v budoucí verzi.  

|Zprávy o výjimky|
|------------------------|
|Netrénovaný model, prosím, vlak model první.|
|Netrénovaný model ({arg_name}), použijte trénovaný model.|


## <a name="error-0013"></a>Chyba 0013  
 K výjimce dochází, pokud je žák předaný modulu neplatný typ.  

 K této chybě dochází vždy, když trénovaný model není kompatibilní s připojeným bodovacím modulem. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->

**Rozlišení:**

Určete typ studenta, který je produkován školicím modulem, a určete bodovací modul, který je vhodný pro studenta. 

Pokud byl model trénován pomocí některého ze specializovaných školicích modulů, připojte trénovaný model pouze k odpovídajícímu modulu specializovaného bodování. 


|Typ modelu|Tréninkový modul| Bodovací modul|
|----|----|----|
|libovolný klasifikátor|[Model vlaku](train-model.md) |[Model skóre](score-model.md)|
|jakýkoli regresní model|[Model vlaku](train-model.md) |[Model skóre](score-model.md)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The module does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->

|Zprávy o výjimky|
|------------------------|
|Student neplatného typu je předán.|
|Student {arg_name} má neplatný typ.|
|Student {arg_name} má neplatný typ {learner_type}.|


## <a name="error-0014"></a>Chyba 0014  
 K výjimce dochází, pokud je počet jedinečných hodnot sloupce větší, než je povoleno.  

 K této chybě dochází, pokud sloupec obsahuje příliš mnoho jedinečných hodnot.  Například se může zobrazit tato chyba, pokud zadáte, že sloupec bude zpracován jako kategorická data, ale ve sloupci je příliš mnoho jedinečných hodnot, než aby bylo možné zpracování dokončit. Tato chyba se může zobrazit také v případě, že existuje neshoda mezi počtem jedinečných hodnot ve dvou vstupech.   

**Rozlišení:**

Otevřete modul, který chybu vygeneroval, a identifikujte sloupce použité jako vstupy. U některých modulů můžete klepnout pravým tlačítkem myši na vstup datové sady a vybrat **možnost Visualize,** chcete-li získat statistiky o jednotlivých sloupcích, včetně počtu jedinečných hodnot a jejich distribuce.

U sloupců, které chcete použít pro seskupení nebo kategorizaci, postupujte tak, že snížíte počet jedinečných hodnot ve sloupcích. V závislosti na datovém typu sloupce můžete snížit různými způsoby. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Nelze najít řešení, které odpovídá vašemu scénáři? Můžete poskytnout zpětnou vazbu na toto téma, která obsahuje název modulu, který generoval chybu a datový typ a mohutnost sloupce. Tyto informace použijeme k poskytnutí cílenějších kroků řešení potíží pro běžné scénáře.   

|Zprávy o výjimky|
|------------------------|
|Množství jedinečných hodnot sloupce je větší, než je povoleno.|
|Počet jedinečných hodnot ve sloupci: {column_name} je větší, než je povoleno.|
|Počet jedinečných hodnot ve sloupci: {column_name}" překračuje počet n-ti- n-tice {limitation}.|


## <a name="error-0015"></a>Chyba 0015  
 K výjimce dochází, pokud se nezdařilo připojení databáze.  

 Tato chyba se zobrazí, pokud zadáte nesprávný název účtu SQL, heslo, databázový server nebo název databáze nebo pokud nelze navázat spojení s databází z důvodu problémů s databází nebo serverem.  

**Rozlišení:** Ověřte, zda byl název účtu, heslo, databázový server a databáze zadány správně a zda má zadaný účet správnou úroveň oprávnění. Ověřte, zda je databáze aktuálně přístupná.  

|Zprávy o výjimky|
|------------------------|
|Při vytváření připojení k databázi došlo k chybě.|
|Při vytváření databázového připojení došlo k chybě: {connection_str}.|


## <a name="error-0016"></a>Chyba 0016  
 K výjimce dochází, pokud vstupní datové sady předané modulu by měly mít kompatibilní typy sloupců, ale nikoli.  

 Tuto chybu se zobrazí v Azure Machine Learning, pokud typy sloupců předaných ve dvou nebo více datových sadách nejsou vzájemně kompatibilní.  

**Rozlišení:** Použití [upravit metadata](edit-metadata.md) nebo upravit původní vstupní datovou sadu<!--, or use [Convert to Dataset](convert-to-dataset.md)--> ujistěte se, že typy sloupců jsou kompatibilní.  

|Zprávy o výjimky|
|------------------------|
|Sloupce s odpovídajícím indexem ve vstupních datových sadách mají nekompatibilní typy.|
|Sloupce {first_col_names} nejsou mezi daty vlaku a testu kompatibilní.|
|Sloupce {first_col_names} a {second_col_names} nejsou kompatibilní.|
|Typy prvků sloupce nejsou kompatibilní pro sloupec {first_col_names} (na základě nuly) vstupních datových sad ({first_dataset_names} a {second_dataset_names}).|


## <a name="error-0017"></a>Chyba 0017  
 K výjimce dochází, pokud vybraný sloupec používá datový typ, který není podporován aktuálním modulem.  

 Například se může zobrazit tuto chybu v Azure Machine Learning, pokud výběr sloupce obsahuje sloupec s datovým typem, který nemůže být zpracován modulem, jako je například sloupec řetězce pro matematickou operaci nebo sloupec skóre, kde je vyžadován sloupec kategorických funkcí.  

**Rozlišení:**
 1. Identifikujte sloupec, který je problém.
 2. Zkontrolujte požadavky modulu.
 3. Upravte sloupec tak, aby splňoval požadavky. V závislosti na sloupci a převodu, o který se pokoušíte, může být nutné provést změny pomocí několika následujících modulů:
    + Pomocí [upravit metadata](edit-metadata.md) můžete změnit datový typ sloupců nebo změnit využití sloupce z funkce na číselné, kategorické na nekategorické a tak dále.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Jako poslední možnost může být nutné upravit původní vstupní datovou sadu.

> [!TIP]
> Nelze najít řešení, které odpovídá vašemu scénáři? Můžete poskytnout zpětnou vazbu na toto téma, která obsahuje název modulu, který generoval chybu a datový typ a mohutnost sloupce. Tyto informace použijeme k poskytnutí cílenějších kroků řešení potíží pro běžné scénáře. 

|Zprávy o výjimky|
|------------------------|
|Sloupec aktuálního typu nelze zpracovat. Typ není podporován modulem.|
|Sloupec typu {col_type} nelze zpracovat. Typ není podporován modulem.|
|Sloupec {col_name} typu {col_type} nelze zpracovat. Typ není podporován modulem.|
|Sloupec {col_name} typu {col_type} nelze zpracovat. Typ není podporován modulem. Název parametru: {arg_name}.|


## <a name="error-0018"></a>Chyba 0018  
 Výjimka nastane, pokud vstupní datová sada není platná.  

**Rozlišení:** Tato chyba v Azure Machine Learning se může zobrazit v mnoha kontextech, takže neexistuje jediné řešení. Obecně chyba označuje, že data poskytnutá jako vstup do modulu mají nesprávný počet sloupců nebo že datový typ neodpovídá požadavkům modulu. Příklad:  

-   Modul vyžaduje sloupec popisku, ale žádný sloupec není označen jako popisek nebo jste ještě nevybrali sloupec popisku.  
  
-   Modul vyžaduje, aby data byla kategorická, ale data jsou číselná.  

<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->

-   Data jsou ve špatném formátu.  
  
-   Importovaná data obsahují neplatné znaky, chybné hodnoty nebo hodnoty mimo rozsah.  
-   Sloupec je prázdný nebo obsahuje příliš mnoho chybějících hodnot.  

 Chcete-li zjistit požadavky a jak mohou data, přečtěte si téma nápovědy pro modul, který bude spotřebovávat datovou sadu jako vstup.  

 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  

|Zprávy o výjimky|
|------------------------|
|Datová sada není platná.|
|{dataset1} obsahuje neplatná data.|
|{dataset1} a {dataset2} by měly být konzistentní ve sloupci.|
|{dataset1} obsahuje neplatná data, {reason}.|
|{dataset1} obsahuje {invalid_data_category}. {troubleshoot_hint}|
|{dataset1} není platný, {reason}. {troubleshoot_hint}|


## <a name="error-0019"></a>Chyba 0019  
 K výjimce dochází, pokud se očekává, že sloupec bude obsahovat seřazené hodnoty, ale není.  

 Tuto chybu se zobrazí v Azure Machine Learning, pokud zadané hodnoty sloupců jsou mimo pořadí.  

**Rozlišení:** Seřaďte hodnoty sloupců ruční úpravou vstupní datové sady a znovu spusťte modul.  

|Zprávy o výjimky|
|------------------------|
|Hodnoty ve sloupci nejsou seřazeny.|
|Hodnoty ve sloupci {col_index} nejsou seřazeny.|
|Hodnoty ve sloupci {col_index} datové sady {dataset}nejsou seřazeny.|


## <a name="error-0020"></a>Chyba 0020  
 K výjimce dochází, pokud je počet sloupců v některých datových sadách předaných modulu příliš malý.  

 Tuto chybu se zobrazí v Azure Machine Learning, pokud pro modul nebyl vybrán dostatek sloupců.  

**Rozlišení:** Znovu zkontrolujte modul a ujistěte se, že volič sloupců má správný počet sloupců vybraných.  

|Zprávy o výjimky|
|------------------------|
|Počet sloupců ve vstupní datové sadě je menší než povolené minimum.|
|Počet sloupců ve vstupní datové sadě {arg_name} je menší než povolené minimum.|
|Počet sloupců ve vstupní datové sadě je menší než povolené minimum sloupců {required_columns_count}.|
|Počet sloupců ve vstupní datové sadě {arg_name} je menší než povolené minimum sloupců {required_columns_count}.|


## <a name="error-0021"></a>Chyba 0021  
 K výjimce dochází, pokud je příliš malý počet řádků v některých datových sadách předaných modulu.  

 Tato chyba v vidět v Azure Machine Learning, když není dostatek řádků v datové sadě k provedení zadané operace. Například se může zobrazit tato chyba, pokud je vstupní datová sada prázdná nebo pokud se pokoušíte provést operaci, která vyžaduje určitý minimální počet řádků, které mají být platné. Tyto operace mohou zahrnovat (ale nejsou omezeny na) seskupení nebo klasifikaci založenou na statistických metodách, určitých typech binningu a učení s počty.  

**Rozlišení:**

 + Otevřete modul, který vrátil chybu, a zkontrolujte vlastnosti vstupní datové sady a modulu. 
 + Ověřte, zda vstupní datová sada není prázdná a zda je k dispozici dostatek řádků dat, které splňují požadavky popsané v nápovědě modulu.  
 + Pokud jsou data načtena z externího zdroje, ujistěte se, že zdroj dat je k dispozici a že v definici dat není žádná chyba nebo změna, která by způsobila, že proces importu získá méně řádků.
 + Pokud provádíte operaci s daty před modulem, která mohou ovlivnit typ dat nebo počet hodnot, jako je například operace čištění, rozdělení nebo spojení, zkontrolujte výstupy těchto operací a určete počet vrácených řádků.  

|Zprávy o výjimky|
|------------------------|
|Počet řádků ve vstupní datové sadě je menší než povolené minimum.|
|Počet řádků ve vstupní datové sadě je menší než povolené minimum řádků {required_rows_count}).|
|Počet řádků ve vstupní datové sadě je menší než povolené minimum řádků {required_rows_count}). {důvod}|
|Počet řádků ve vstupní datové sadě {arg_name} je menší než povolené minimum řádků {required_rows_count}).|
|Počet řádků ve vstupní datové sadě {arg_name} je {actual_rows_count}, menší než povolené minimum {required_rows_count} řádků.|
|Počet řádků {row_type} ve vstupní datové sadě {arg_name} je {actual_rows_count}, menší než povolené minimum {required_rows_count} řádků.|


## <a name="error-0022"></a>Chyba 0022  
 K výjimce dochází, pokud se počet vybraných sloupců ve vstupní datové sadě nerovná očekávanému počtu.  

 K této chybě v Azure Machine Learning může dojít, když modul nebo operace příjem dat vyžaduje určitý počet sloupců nebo vstupů a jste poskytli příliš málo nebo příliš mnoho sloupců nebo vstupů. Příklad:  

-   Určíte sloupec s jedním popiskem nebo sloupec klíče a omylem vyberete více sloupců.  
  
-   Přejmenováváte sloupce, ale poskytujete více nebo méně názvů, než jsou sloupce.  
  
-   Počet sloupců ve zdroji nebo cíli se změnil nebo neodpovídá počtu sloupců používaných modulem.  
  
-   Zadali jste seznam hodnot pro vstupy oddělených čárkami, ale počet hodnot se neshoduje nebo není podporováno více vstupů.  

**Rozlišení:** Znovu navštivte modul a zkontrolujte výběr sloupců, abyste se ujistili, že je vybrán správný počet sloupců. Ověřte výstupy upstream modulů a požadavky na navazující operace.  

 Pokud jste použili jednu z možností výběru sloupců, která může vybrat více sloupců (sloupcové indexy, všechny prvky, všechny číselné atd.), ověřte přesný počet sloupců vrácených výběrem.  

 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->

 Ověřte, zda se počet nebo typ nadřazených sloupců nezměnil.  

 Pokud používáte datovou sadu doporučení k trénování modelu, nezapomeňte, že doporučující očekává omezený počet sloupců, které odpovídají dvojicím uživatelských položek nebo hodnocení uživatelských položek. Před trénováním datové sady doporučení odeberte další sloupce. Další informace naleznete v tématu [Rozdělení dat](split-data.md).  

|Zprávy o výjimky|
|------------------------|
|Počet vybraných sloupců ve vstupní datové sadě se nerovná očekávanému počtu.|
|Počet vybraných sloupců ve vstupní datové sadě se nerovná {expected_col_count}.|
|Vzorek výběru sloupce {selection_pattern_friendly_name} poskytuje počet vybraných sloupců ve vstupní datové sadě, která se nerovná {expected_col_count}.|
|Očekává se, že vzorek výběru sloupce {selection_pattern_friendly_name} bude poskytovat {expected_col_count} sloupců vybraných ve vstupní datové sadě, ale {selected_col_count} sloupců je nebo jsou skutečně k dispozici.|


## <a name="error-0023"></a>Chyba 0023  

Výjimka nastane, pokud cílový sloupec vstupní datové sady není platný pro aktuální modul školitele.  

K této chybě v Azure Machine Learning dochází, pokud cílový sloupec (vybraný v parametrech modulu) není platného datového typu, obsahuje všechny chybějící hodnoty nebo nebyl kategorický podle očekávání.  

**Rozlišení:** Znovu zkontrolujte vstup modulu a zkontrolujte obsah sloupce popisek/cílový. Ujistěte se, že nemá všechny chybějící hodnoty. Pokud modul očekává, že cílový sloupec bude kategorický, ujistěte se, že v cílovém sloupci je více než jedna odlišná hodnota.  

|Zprávy o výjimky|
|------------------------|
|Vstupní datová sada má nepodporovaný cílový sloupec.|
|Vstupní datová sada má nepodporovaný cílový sloupec {column_index}.|
|Vstupní datová sada má nepodporovaný cílový sloupec {column_index} pro studenta typu {learner_type}.|


## <a name="error-0024"></a>Chyba 0024  
Výjimka nastane, pokud datová sada neobsahuje sloupec popisku.  

 K této chybě v Azure Machine Learning dochází, když modul vyžaduje sloupec popisku a datová sada nemá sloupec popisku. Například vyhodnocení scored datové sady obvykle vyžaduje, aby sloupec popisek je k dispozici pro výpočet metriky přesnosti.  

Může se také stát, že sloupec popisek je k dispozici v datové sadě, ale není správně zjištěna Azure Machine Learning.

**Rozlišení:**

+ Otevřete modul, který chybu vygeneroval, a zjistěte, zda je k dispozici sloupec popisku. Na názvu nebo datovém typu sloupce nezáleží, pokud sloupec obsahuje jeden výsledek (nebo závislou proměnnou), který se pokoušíte předpovědět. Pokud si nejste jisti, který sloupec má popisek, vyhledejte obecný název, například *Třída* nebo *Cíl*. 
+  Pokud datová sada neobsahuje sloupec popisku, je možné, že sloupec popisku byl explicitně nebo omylem odebrán proti proudu. Může se také stát, že datová sada není výstupem modulu hodnocení upstream.
+ Chcete-li sloupec explicitně označit jako sloupec popisku, přidejte modul [Upravit metadata](edit-metadata.md) a připojte datovou sadu. Vyberte pouze sloupec popisku a v rozevíracím seznamu **Pole** vyberte **Popisník.** 
+ Pokud je jako popisek vybrán nesprávný sloupec, můžete vybrat **vymazat popisek** z **polí a** opravit metadata ve sloupci. 
  
|Zprávy o výjimky|
|------------------------|
|V datové sadě není žádný sloupec popisků.|
|V {dataset_name} není žádný sloupec popisku.|


## <a name="error-0025"></a>Chyba 0025  
 Výjimka nastane, pokud datová sada neobsahuje sloupec skóre.  

 K této chybě v Azure Machine Learning dochází, pokud vstup do modelu vyhodnocení neobsahuje platné sloupce skóre. Například uživatel se pokusí vyhodnotit datovou sadu před tím, než byla hodnocena s správně trénovaným modelem, nebo sloupec skóre byl explicitně vynechán proti proudu. K této výjimce dochází také v případě, že sloupce skóre na dvou datových sadách nejsou kompatibilní. Například se pokoušíte porovnat přesnost lineárníregresor s binární třídění.  

**Rozlišení:** Znovu zkontrolujte vstup do modelu vyhodnocení a zkontrolujte, zda obsahuje jeden nebo více sloupců skóre. Pokud tomu tak není, datová sada nebyla hodnocena nebo sloupce skóre byly vynechány v modulu upstream.  

|Zprávy o výjimky|
|------------------------|
|V datové sadě není žádný sloupec skóre.|
|Ve sloupci {dataset_name} není žádný sloupec skóre.|
|Ve sloupci {dataset_name} není žádný sloupec skóre, který je vytvořen {learner_type}. Skóre datové sady pomocí správného typu studenta.|


## <a name="error-0026"></a>Chyba 0026  
 K výjimce dochází, pokud sloupce se stejným názvem nejsou povoleny.  

 K této chybě v Azure Machine Learning dochází, pokud více sloupců mají stejný název. Jeden způsob, jak se může zobrazit tato chyba je, pokud datová sada nemá řádek záhlaví a názvy sloupců jsou automaticky přiřazeny: Col0, Col1, atd.  

**Rozlišení:** Pokud mají sloupce stejný název, vložte mezi vstupní datovou sadu a modul modul [Upravit metadata.](edit-metadata.md) Pomocí voliče sloupců v [části Upravit metadata](edit-metadata.md) vyberte sloupce, které chcete přejmenovat, a zadejte nové názvy do textového pole **Nové názvy sloupců.**  

|Zprávy o výjimky|
|------------------------|
|Stejné názvy sloupců jsou určeny v argumentech. Stejné názvy sloupců nejsou povoleny modulem.|
|Stejné názvy sloupců v argumentech {arg_name_1} a {arg_name_2} nejsou povoleny. Zadejte různé názvy.|


## <a name="error-0027"></a>Chyba 0027  
 Výjimka nastane v případě, že dva objekty musí být stejné velikosti, ale nejsou.  

 Toto je běžná chyba v Azure Machine Learning a může být způsobeno mnoha podmínkami.  

**Rozlišení:** Neexistuje žádné konkrétní řešení. Můžete však zkontrolovat podmínky, jako jsou následující:  

-   Pokud přejmenováváte sloupce, ujistěte se, že každý seznam (vstupní sloupce a seznam nových názvů) má stejný počet položek.  
  
-   Pokud připojujete nebo zřetězení dvě datové sady, ujistěte se, že mají stejné schéma.  
  
-   Pokud připojujete dvě datové sady, které mají více sloupců, ujistěte se, že klíčové sloupce mají stejný datový typ, a vyberte možnost **Povolit duplikáty a zachovat pořadí sloupců ve výběru**.  

|Zprávy o výjimky|
|------------------------|
|Velikost předaných objektů je nekonzistentní.|
|Velikost {friendly_name1} není konzistentní s velikostí {friendly_name2}.|


## <a name="error-0028"></a>Chyba 0028  
 Výjimka nastává v případě, kdy sada sloupců obsahuje duplicitní názvy sloupců a není povolena.  

 K této chybě v Azure Machine Learning dochází, když jsou duplicitní názvy sloupců; to znamená, že není jedinečný.  

**Rozlišení:** Pokud mají některé sloupce stejný název, přidejte instanci [Upravit metadata](edit-metadata.md) mezi vstupní datovou sadu a modul, který chybu vyvolává. Pomocí voliče sloupců v [části Upravit metadata](edit-metadata.md) vyberte sloupce, které chcete přejmenovat, a zadejte nové názvy sloupců do textového pole **Nové názvy sloupců.** Pokud přejmenováváte více sloupců, ujistěte se, že hodnoty, které zadáte do **názvů nových sloupců,** jsou jedinečné.  

|Zprávy o výjimky|
|------------------------|
|Sada sloupců obsahuje duplicitní názvy sloupců.|
|Název {duplicated_name} je duplikován.|
|Název {duplicated_name} je duplikován v {arg_name}.|
|Název {duplicated_name} je duplikován. Podrobnosti: {details}|


## <a name="error-0029"></a>Chyba 0029  
 Výjimka nastane v případě, že je předán neplatný identifikátor URI.  

 K této chybě v Azure Machine Learning dochází v případě, že je předán neplatný identifikátor URI.  Tato chyba se zobrazí, pokud platí některá z následujících podmínek:  

-   Veřejné nebo SAS URI poskytované pro Azure Blob Storage pro čtení nebo zápis obsahuje chybu.  
  
-   Časový limit pro SAS vypršela.  
  
-   Webová adresa URL prostřednictvím zdroje HTTP představuje soubor nebo identifikátor URI zpětné smyčky.  
  
-   Webová adresa URL prostřednictvím protokolu HTTP obsahuje nesprávně formátovanou adresu URL.  
  
-   Adresu URL nelze přeložit vzdáleným zdrojem.  

**Rozlišení:** Znovu navštivte modul a ověřte formát identifikátoru URI. Pokud je zdrojem dat webová adresa URL prostřednictvím protokolu HTTP, ověřte, zda zamýšleným zdrojem není soubor nebo identifikátor URI zpětné smyčky (localhost).  

|Zprávy o výjimky|
|------------------------|
|Je předán neplatný identifikátor Uri.|
|Identifikátor Uri {invalid_url} je neplatný.|


## <a name="error-0030"></a>Chyba 0030  
 Výjimka nastává v případě, kdy není možné stáhnout soubor.  

 K této výjimce v Azure Machine Learning dochází, když není možné stáhnout soubor. Tato výjimka se zobrazí, pokud pokus o čtení ze zdroje HTTP selhal po třech (3) pokusech o opakování.  

**Rozlišení:** Ověřte, zda je identifikátor URI ke zdroji HTTP správný a zda je web aktuálně přístupný prostřednictvím Internetu.  

|Zprávy o výjimky|
|------------------------|
|Soubor nelze stáhnout.|
|Při stahování souboru došlo k chybě: {file_url}.|


## <a name="error-0031"></a>Chyba 0031  
 K výjimce dochází, pokud je počet sloupců v sadě sloupců menší, než je potřeba.  

 K této chybě v Azure Machine Learning dochází, pokud je počet vybraných sloupců menší, než je potřeba.  Tato chyba se zobrazí, pokud není vybrán minimální požadovaný počet sloupců.  

**Rozlišení:** Přidání dalších sloupců do výběru sloupců pomocí **voliče sloupců**.  

|Zprávy o výjimky|
|------------------------|
|Počet sloupců v sadě sloupců je menší, než je požadováno.|
|Pro vstupní argument {arg_name} by měl být určen alespoň {required_columns_count} sloupců.|
|Pro vstupní argument {arg_name} by měl být určen alespoň {required_columns_count} sloupců. Skutečný počet zadaných sloupců je {input_columns_count}.|


## <a name="error-0032"></a>Chyba 0032  
 Výjimka nastane, pokud argument není číslo.  

 Tato chyba se zobrazí v Azure Machine Learning, pokud je argument double nebo NaN.  

**Rozlišení:** Upravte zadaný argument tak, aby používal platnou hodnotu.  

|Zprávy o výjimky|
|------------------------|
|Argument není číslo.|
|{arg_name} není číslo.|


## <a name="error-0033"></a>Chyba 0033  
 Výjimka nastane, pokud je argument Infinity.  

 K této chybě v Azure Machine Learning dochází, pokud argument je nekonečný. Tato chyba se zobrazí, pokud `double.NegativeInfinity` `double.PositiveInfinity`je argument buď nebo .  

**Rozlišení:** Upravte zadaný argument jako platnou hodnotu.  

|Zprávy o výjimky|
|------------------------|
|Argument musí být konečný.|
|{arg_name} není konečný.|


## <a name="error-0034"></a>Chyba 0034  
 K výjimce dochází, pokud pro daný pár uživatelských položek existuje více než jedno hodnocení.  

 K této chybě v Azure Machine Learning dochází v doporučení, pokud má pár uživatelských položek více než jednu hodnotu hodnocení.  

**Rozlišení:** Ujistěte se, že dvojice uživatelských položek má pouze jednu hodnotu hodnocení.  

|Zprávy o výjimky|
|------------------------|
|Existuje více než jedno hodnocení pro hodnoty v datové sadě.|
|Více než jedno hodnocení pro uživatele {user} a položku {item} v tabulce dat předpovědi hodnocení.|
|Více než jedno hodnocení pro uživatele {user} a položku {item} v {dataset}.|


## <a name="error-0035"></a>Chyba 0035  
 K výjimce dochází, pokud pro daného uživatele nebo položku nebyly poskytnuty žádné funkce.  

 K této chybě v Azure Machine Learning dochází, že se pokoušíte použít model doporučení pro vyhodnocování, ale vektor funkce nelze najít.  

**Rozlišení:**

Matchbox doporučující má určité požadavky, které musí být splněny při použití funkce položky nebo uživatelské funkce.  Tato chyba označuje, že pro uživatele nebo položku, kterou jste zadali jako vstup, chybí vektor funkce. Ujistěte se, že vektor funkcí je k dispozici v datech pro každého uživatele nebo položku.  

 Pokud jste například vyškolili model doporučení pomocí funkcí, jako je věk, umístění nebo příjem uživatele, ale nyní chcete vytvořit skóre pro nové uživatele, kteří nebyli během školení zobrazeni, musíte pro nové uživatele poskytnout odpovídající sadu funkcí (jmenovitě hodnoty věku, umístění a příjmu), aby pro ně bylo možné provést odpovídající předpovědi. 

 Pokud pro tyto uživatele nemáte žádné funkce, zvažte vytvoření příslušných funkcí v případě, že tyto funkce vygenerují příslušné funkce.  Pokud například nemáte hodnoty věku nebo příjmu jednotlivých uživatelů, můžete vygenerovat přibližné hodnoty, které se použijí pro skupinu uživatelů. 

<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).

For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->

 > [!TIP]
 > Usnesení se nevztahuje na váš případ? Můžete odeslat zpětnou vazbu k tomuto článku a poskytnout informace o scénáři, včetně modulu a počtu řádků ve sloupci. Tyto informace použijeme k tomu, abychom v budoucnu poskytli podrobnější kroky k řešení potíží.

|Zprávy o výjimky|
|------------------------|
|Pro požadovaného uživatele nebo položku nebyly poskytnuty žádné funkce.|
|Funkce pro {required_feature_name} jsou povinné, ale nejsou k dispozici.|


## <a name="error-0036"></a>Chyba 0036  
 K výjimce dochází, pokud bylo pro daného uživatele nebo položku poskytnuto více vektorů funkcí.  

 K této chybě v Azure Machine Learning dochází, pokud je vektor funkce definován více než jednou.  

**Rozlišení:** Ujistěte se, že vektor prvku není definován více než jednou.  

|Zprávy o výjimky|
|------------------------|
|Duplicitní definice prvku pro uživatele nebo položku.|


## <a name="error-0037"></a>Chyba 0037  
 Výjimka nastane, pokud jsou zadány více sloupců popisku a je povolen pouze jeden.  

 K této chybě v Azure Machine Learning dochází, pokud je vybráno více než jeden sloupec jako nový sloupec popisku. Většina algoritmů učení pod dohledem vyžaduje, aby byl jeden sloupec označen jako cíl nebo popisek.  

**Rozlišení:** Nezapomeňte vybrat jeden sloupec jako nový sloupec popisku.  

|Zprávy o výjimky|
|------------------------|
|Jsou zadány více sloupců popisku.|
|V poli {dataset_name} je zadáno více sloupců popisků.|


## <a name="error-0039"></a>Chyba 0039  
 K výjimce dochází, pokud se operace nezdařila.  

 K této chybě v Azure Machine Learning dochází, když nelze dokončit interní operaci.  

**Rozlišení:** Tato chyba je způsobena mnoha podmínkami a neexistuje žádný konkrétní lék.  
 Následující tabulka obsahuje obecné zprávy pro tuto chybu, za kterou následuje konkrétní popis podmínky. 

 Pokud nejsou k dispozici žádné podrobnosti, [odešlete zpětnou vazbu](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) a poskytněte informace o modulech, které generovaly chybu a související podmínky.

|Zprávy o výjimky|
|------------------------|
|Operace se nezdařila.|
|Při dokončení operace došlo k chybě: {failed_operation}.|
|Při dokončení operace došlo k chybě: {failed_operation}. Důvod: "{reason}".|


## <a name="error-0042"></a>Chyba 0042  
 K výjimce dochází, když není možné převést sloupec na jiný typ.  

 K této chybě v Azure Machine Learning dochází, když není možné převést sloupec na zadaný typ.  Tato chyba se zobrazí, pokud modul vyžaduje určitý datový typ, například datetime, text, číslo s plovoucí desetinnou tištěnou nebo celé číslo, ale není možné převést existující sloupec na požadovaný typ.  

Můžete například vybrat sloupec a pokusit se jej převést na číselný datový typ pro použití v matematické operaci a tuto chybu získat, pokud sloupec obsahoval neplatná data. 

Dalším důvodem, proč se může zobrazit tato chyba, pokud se pokusíte použít sloupec obsahující čísla s plovoucí desetinnou tágo nebo mnoho jedinečných hodnot jako kategorický sloupec. 

**Rozlišení:**

+ Otevřete stránku nápovědy pro modul, který chybu vygeneroval, a ověřte požadavky na datový typ.
+ Zkontrolujte datové typy sloupců ve vstupní datové sadě.
+ Zkontrolujte data pocházející z takzvaných zdrojů dat bez schématu.
+ V datové sadě zkontrolujte chybějící hodnoty nebo speciální znaky, které mohou blokovat převod na požadovaný datový typ. 
    + Číselné datové typy by měly být konzistentní: například zkontrolujte čísla s plovoucí desetinnou tištěnou hodnotou ve sloupci celých čísel.
    + Vyhledejte textové řetězce nebo hodnoty NA ve sloupci čísel. 
    + Logické hodnoty lze převést na příslušnou reprezentaci v závislosti na požadovaném datovém typu.
    + Kontrola textových sloupců pro znaky, které nejsou znaky unicode, znaky tabulátoru nebo řídicí znaky
    + Data data data by měla být konzistentní, aby se zabránilo chybám modelování, ale vyčištění může být složité vzhledem k mnoha formátů. Zvažte použití <!--the [Execute R Script](execute-r-script.md) or -->[Spusťte](execute-python-script.md) moduly skriptu Pythonu k provedení vyčištění.  
+ V případě potřeby upravte hodnoty ve vstupní datové sadě tak, aby bylo možné sloupec úspěšně převést. Modifikace může zahrnovat binning, zkrácení nebo zaokrouhlení operace, odstranění odlehlé hodnoty nebo imputace chybějící hodnoty. V následujících článcích naleznete některé běžné scénáře transformace dat ve strojovém učení:
    + [Vyčištění chybějících dat](clean-missing-data.md)
    + [Normalizace dat](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->

> [!TIP]
> Usnesení nejasné, nebo se nevztahuje na váš případ? Můžete odeslat zpětnou vazbu k tomuto článku a poskytnout informace o scénáři, včetně modulu a datového typu sloupce. Tyto informace použijeme k tomu, abychom v budoucnu poskytli podrobnější kroky k řešení potíží.  

|Zprávy o výjimky|
|------------------------|
|Převod není povolen.|
|Nelze převést sloupec typu {type1} na sloupec typu {type2}.|
|Sloupec {col_name1} typu {type1} nelze převést na sloupec typu {type2}.|
|Sloupec {col_name1} typu {type1} nelze převést na sloupec {col_name2} typu {type2}.|


## <a name="error-0044"></a>Chyba 0044  
 K výjimce dochází, když není možné odvodit typ prvku sloupce z existujících hodnot.  

 K této chybě v Azure Machine Learning dochází, když není možné odvodit typ sloupce nebo sloupců v datové sadě. K tomu obvykle dochází při zřetězení dvou nebo více datových sad s různými typy prvků. Pokud Azure Machine Learning není schopen určit běžný typ, který je schopen reprezentovat všechny hodnoty ve sloupci nebo sloupcích bez ztráty informací, bude generovat tuto chybu.  

**Rozlišení:** Ujistěte se, že všechny hodnoty v daném sloupci v obou datových sadách, které jsou kombinovány, jsou buď stejného typu (číselné, logické, kategorické, řetězec, datum atd.) nebo mohou být dotáženy ke stejnému typu.  

|Zprávy o výjimky|
|------------------------|
|Nelze odvodit typ prvku sloupce.|
|Nelze odvodit typ prvku pro sloupec {column_name}" – všechny prvky jsou nulové odkazy.|
|Nelze odvodit typ prvku pro sloupec {column_name} datové sady {dataset_name} – všechny prvky jsou nulovým odkazem.|


## <a name="error-0045"></a>Chyba 0045  
 K výjimce dochází, když není možné vytvořit sloupec z důvodu smíšených typů prvků ve zdroji.  

 Tato chyba v Azure Machine Learning se vytváří, když se liší typy elementů dvou datových sad, které se kombinují.  

**Rozlišení:** Ujistěte se, že všechny hodnoty v daném sloupci v obou datových sadách, které jsou kombinovány, jsou stejného typu (číselné, logické, kategorické, řetězec, datum atd.).  

|Zprávy o výjimky|
|------------------------|
|Nelze vytvořit sloupec se smíšenými typy prvků.|
|Nelze vytvořit sloupec s id {column_id} smíšených typů prvků:<br />Typ dat[{row_1}, {column_id}] je {type_1}. <br />Typ dat[{row_2}, {column_id}] je {type_2}.|
|Nelze vytvořit sloupec s id {column_id} smíšených typů prvků:<br />Zadejte blok {chunk_id_1} je {type_1}. <br />Zadejte blok {chunk_id_2} je {type_2} s velikostí bloku: {chunk_size}.|


## <a name="error-0046"></a>Chyba 0046  
 K výjimce dochází, pokud není možné vytvořit adresář na zadané cestě.  

 K této chybě v Azure Machine Learning dochází, když není možné vytvořit adresář na zadané cestě. Tato chyba se zobrazí, pokud je jakákoli část cesty k výstupnímu adresáři pro dotaz Hive nesprávná nebo nepřístupná.  

**Rozlišení:** Znovu navštivte modul a ověřte, zda je cesta k adresáři správně naformátována a zda je přístupná s aktuálními pověřeními.  

|Zprávy o výjimky|
|------------------------|
|Zadejte platný výstupní adresář.|
|Adresář: {path} nelze vytvořit. Zadejte platnou cestu.|


## <a name="error-0047"></a>Chyba 0047  
 K výjimce dochází, pokud je počet sloupců funkce v některých datových sadách předaných modulu příliš malý.  

 K této chybě v Azure Machine Learning dochází, pokud vstupní datová sada na školení neobsahuje minimální počet sloupců požadovaných algoritmem. Obvykle je datová sada prázdná nebo obsahuje pouze sloupce školení.  

**Rozlišení:** Znovu navštivte vstupní datovou sadu a ujistěte se, že existují jeden nebo více dalších sloupců kromě sloupce popisek.  

|Zprávy o výjimky|
|------------------------|
|Počet sloupců funkce ve vstupní datové sadě je menší než povolené minimum.|
|Počet sloupců funkce ve vstupní datové sadě je menší než povolené minimum sloupců {required_columns_count}.|
|Počet sloupců funkce ve vstupní datové sadě {arg_name} je menší než povolené minimum sloupců {required_columns_count}.|


## <a name="error-0048"></a>Chyba 0048  
 Výjimka nastává v případě, kdy není možné otevřít soubor.  

 K této chybě v Azure Machine Learning dochází, když není možné otevřít soubor pro čtení nebo zápis. Tato chyba se může zobrazit z těchto důvodů:  

-   Kontejner nebo soubor (objekt blob) neexistuje  
  
-   Úroveň přístupu souboru nebo kontejneru neumožňuje přístup k souboru  
  
-   Soubor je příliš velký na čtení nebo nesprávný formát  

**Rozlišení:** Znovu navštivte modul a soubor, který se pokoušíte číst.  

 Ověřte, zda jsou názvy kontejneru a souboru správné.  

 Pomocí klasického portálu Azure nebo nástroje úložiště Azure ověřte, zda máte oprávnění k přístupu k souboru.  

  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->

|Zprávy o výjimky|
|------------------------|
|Soubor nelze otevřít.|
|Při otevírání souboru došlo k chybě: {file_name}.|
|Při otevírání souboru došlo k chybě: {file_name}. Zpráva o výjimce úložiště: {exception}.|


## <a name="error-0049"></a>Chyba 0049  
 Výjimka nastává v případě, kdy není možné analyzovat soubor.  

 K této chybě v Azure Machine Learning dochází, když není možné analyzovat soubor. Tato chyba se zobrazí, pokud formát souboru vybraný v modulu [Import dat](import-data.md) neodpovídá skutečnému formátu souboru nebo pokud soubor obsahuje nerozpoznatelný znak.  

**Rozlišení:** Znovu navštivte modul a opravte výběr formátu souboru, pokud neodpovídá formátu souboru. Pokud je to možné, zkontrolujte soubor a ověřte, zda neobsahuje žádné neplatné znaky.  

|Zprávy o výjimky|
|------------------------|
|Soubor nelze analyzovat.|
|Při analýzě souboru {file_format} došlo k chybě.|
|Při analýzě souboru {file_format} došlo k chybě: {file_name}.|
|Při analýzě souboru {file_format} došlo k chybě. Důvod: {failure_reason}.|
|Při analýzě souboru {file_format} došlo k chybě: {file_name}. Důvod: {failure_reason}.|


## <a name="error-0052"></a>Chyba 0052  
 Výjimka nastane, pokud je nesprávně zadán klíč účtu úložiště Azure.  

 K této chybě ve službě Azure Machine Learning dochází, pokud je klíč použitý pro přístup k účtu úložiště Azure nesprávný. Například se může zobrazit tato chyba, pokud klíč úložiště Azure byl zkrácen při kopírování a vložení, nebo pokud byl použit nesprávný klíč.  

 Další informace o tom, jak získat klíč pro účet úložiště Azure, najdete v tématu [Zobrazení, kopírování a obnovení přístupových klíčů úložiště](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/).  

**Rozlišení:** Znovu zkontrolujte modul a ověřte, zda je klíč úložiště Azure správný pro účet; v případě potřeby znovu zkopírujte klíč z klasického portálu Azure.  

|Zprávy o výjimky|
|------------------------|
|Klíč účtu úložiště Azure je nesprávný.|


## <a name="error-0053"></a>Chyba 0053  
 Výjimka nastane v případě, že neexistují žádné uživatelské funkce nebo položky pro doporučení matchbox.  

 Tato chyba v Azure Machine Learning se vytváří, když nelze najít vektor funkce.  

**Rozlišení:** Ujistěte se, že je ve vstupní datové sadě přítomen vektor prvku.  

|Zprávy o výjimky|
|------------------------|
|Uživatelské funkce nebo/a položky jsou povinné, ale nejsou k dispozici.|


## <a name="error-0056"></a>Chyba 0056  
 K výjimce dochází, pokud sloupce vybrané pro operaci porušují požadavky.  

 K této chybě v Azure Machine Learning dochází, když vybíráte sloupce pro operaci, která vyžaduje sloupec být určitého datového typu. 

 K této chybě může dojít také v případě, že sloupec je správný datový typ, ale modul, který používáte, vyžaduje, aby byl sloupec také označen jako prvek, popisek nebo kategorický sloupec.  

  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->

**Rozlišení:**

1.  Zkontrolujte datový typ sloupců, které jsou aktuálně vybrány. 

2. Zjišťte, zda jsou vybrané sloupce kategorické, popisky nebo sloupce prvků.  
  
3.  Projděte si téma nápovědy pro modul, ve kterém jste provedli výběr sloupce, a zjistěte, zda existují specifické požadavky na typ dat nebo použití sloupce.  
  
3.  Pomocí [funkce Upravit metadata](edit-metadata.md) můžete změnit typ sloupce po dobu trvání této operace. Nezapomeňte změnit typ sloupce zpět na původní hodnotu pomocí jiné instance [Upravit metadata](edit-metadata.md), pokud jej potřebujete pro operace navazujících dat.  

|Zprávy o výjimky|
|------------------------|
|Jeden nebo více vybraných sloupců nebylo v povolené kategorii.|
|Sloupec s názvem {col_name} není v povolené kategorii.|


## <a name="error-0057"></a>Chyba 0057  
 Výjimka nastane při pokusu o vytvoření souboru nebo objektu blob, který již existuje.  

 K této výjimce dochází, když používáte modul [Export dat](export-data.md) nebo jiný modul k uložení výsledků kanálu v Azure Machine Learning do úložiště objektů blob Azure, ale pokusíte se vytvořit soubor nebo objekt blob, který již existuje.   

**Rozlišení:**

 Tato chyba se zobrazí pouze v případě, že jste dříve nastavili vlastnost **Azure objektu blob režim zápisu** na **Error**. Podle návrhu tento modul vyvolá chybu, pokud se pokusíte zapsat datovou sadu do objektu blob, který již existuje.

 - Otevřete vlastnosti modulu a změňte vlastnost **Azure blob režim zápisu** **přepsat**.
 - Případně můžete zadat název jiného cílového objektu blob nebo souboru a určit objekt blob, který ještě neexistuje.  

|Zprávy o výjimky|
|------------------------|
|Soubor nebo objekt blob již existuje.|
|Soubor nebo objekt blob {file_path} již existuje.|


## <a name="error-0058"></a>Chyba 0058  
 K této chybě v Azure Machine Learning dochází, pokud datová sada neobsahuje sloupec očekávaný popisek.  

 K této výjimce může dojít také v případě, že zadaný sloupec popisku neodpovídá datům nebo datovému typu očekávaným studentem nebo má nesprávné hodnoty. Například tato výjimka se vytváří při použití sloupce popisek s reálnou hodnotou při trénování binární třídění.  

**Rozlišení:** Rozlišení závisí na studenta nebo školitele, který používáte, a na datových typech sloupců v datové sadě. Nejprve ověřte požadavky algoritmu strojového učení nebo školicího modulu.  

 Znovu navštivte vstupní datovou sadu. Ověřte, zda sloupec, který očekáváte, že bude považován za popisek, obsahuje správný datový typ pro model, který vytváříte.  

 Zkontrolujte, zda vstupy nechybí hodnoty a v případě potřeby je vyloučí nebo je nahraďte.  

 V případě potřeby přidejte modul [Upravit metadata](edit-metadata.md) a ujistěte se, že sloupec popisku je označen jako popisek.  

|Zprávy o výjimky|
|------------------------|
|Hodnoty sloupců popisku a hodnoty sloupců se s konstatovám popisku nejsou srovnatelné.|
|Sloupec popisek není v {dataset_name}podle očekávání.|
|Sloupec popisek není očekávaný v {dataset_name}, {reason}.|
|Sloupec popisku {column_name} není v {dataset_name}očekáván.|
|Sloupec popisku {column_name} není očekáván v {dataset_name}, {reason}.|


## <a name="error-0059"></a>Chyba 0059  
 K výjimce dochází, pokud nelze analyzovat index sloupce zadaný ve výběru sloupců.  

 K této chybě v Azure Machine Learning dochází, pokud nelze analyzovat index sloupce zadaný při použití voliče sloupců.  Tato chyba se zobrazí, pokud je index sloupce v neplatném formátu, který nelze analyzovat.  

**Rozlišení:** Upravte index sloupce tak, aby používal platnou hodnotu indexu.  

|Zprávy o výjimky|
|------------------------|
|Jeden nebo více zadaných sloupcových indexů nebo rozsahů indexů nelze analyzovat.|
|Index sloupce nebo oblast {column_index_or_range} nelze analyzovat.|


## <a name="error-0060"></a>Chyba 0060  
 K výjimce dochází, když je ve výběru sloupců zadán rozsah sloupců mimo rozsah.  

 K této chybě v Azure Machine Learning dochází, když je v voliči sloupců zadán rozsah sloupců mimo rozsah. Tato chyba se zobrazí, pokud oblast sloupců ve výběru sloupců neodpovídá sloupcům v datové sadě.  

**Rozlišení:** Upravte oblast sloupců ve výběru sloupců tak, aby odpovídala sloupcům v datové sadě.  

|Zprávy o výjimky|
|------------------------|
|Neplatný rozsah indexu sloupců mimo rozsah byl zadán.|
|Rozsah sloupců {column_range} je neplatný nebo mimo rozsah.|


## <a name="error-0061"></a>Chyba 0061  
 Výjimka nastane při pokusu o přidání řádku datatable, který má jiný počet sloupců než tabulka.  

 K této chybě v Azure Machine Learning dochází, když se pokusíte přidat řádek do datové sady, která má jiný počet sloupců než datová sada.  Tato chyba se zobrazí, pokud řádek, který je přidáván do datové sady, má jiný počet sloupců ze vstupní datové sady.  Řádek nelze připojit k datové sadě, pokud se počet sloupců liší.  

**Rozlišení:** Upravte vstupní datovou sadu tak, aby měla stejný počet sloupců jako řádek, nebo upravte řádek přidaný tak, aby měl stejný počet sloupců jako datová sada.  

|Zprávy o výjimky|
|------------------------|
|Všechny tabulky musí mít stejný počet sloupců.|
|Sloupce v bloku dat {chunk_id_1} se liší u bloku {chunk_id_2} s velikostí bloku: {chunk_size}.|
|Počet sloupců v souboru {filename_1}" (count={column_count_1}) se liší souborem {filename_2}" (count={column_count_2}).|


## <a name="error-0062"></a>Chyba 0062  
 Výjimka nastane při pokusu o porovnání dvou modelů s různými typy studenta.  

 Tato chyba v Azure Machine Learning se vytváří při vyhodnocení metriky pro dvě různé scored datové sady nelze porovnat. V tomto případě není možné porovnat účinnost modelů použitých k výrobě dvou datových sad s hodnocením.  

**Rozlišení:** Ověřte, zda výsledky skóre jsou vytvářeny stejným druhem modelu strojového učení (binární klasifikace, regrese, klasifikace více tříd, doporučení, clustering, detekce anomálií atd.) Všechny modely, které porovnáváte, musí mít stejný typ studenta.  

|Zprávy o výjimky|
|------------------------|
|Všechny modely musí mít stejný typ žáka.|
|Mám nekompatibilní typ žáka: "{actual_learner_type}". Očekávané typy studentů jsou: "{expected_learner_type_list}".|


## <a name="error-0064"></a>Chyba 0064  
 Výjimka nastane, pokud je nesprávně zadán název účtu úložiště Azure nebo klíč úložiště.  

 K této chybě v Azure Machine Learning dochází, pokud je nesprávně zadán název účtu úložiště Azure nebo klíč úložiště. Tato chyba se zobrazí, pokud zadáte nesprávný název účtu nebo heslo pro účet úložiště. Tato situace může nastat, pokud ručně zadáte název účtu nebo heslo. Může také dojít, pokud byl účet odstraněn.  

**Rozlišení:** Ověřte, zda byl název účtu a heslo zadány správně a zda účet existuje.  

|Zprávy o výjimky|
|------------------------|
|Název účtu úložiště Azure nebo klíč úložiště je nesprávný.|
|Název účtu úložiště Azure {account_name} nebo klíč úložiště pro název účtu je nesprávný.|


## <a name="error-0065"></a>Chyba 0065  
 Výjimka nastane, pokud je název objektu blob Azure zadán nesprávně.  

 K této chybě v Azure Machine Learning dochází, pokud je název objektu blob Azure zadán nesprávně.  Chybu se zobrazí, pokud:  

-   Objekt blob nebyl nalezen v zadaném kontejneru.  

 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->

-   Pouze kontejner byl zadán jako zdroj v požadavku [importu dat,](import-data.md) když formát byl Excel nebo CSV s kódováním; zřetězení obsahu všech objektů BLOB v kontejneru není povoleno s těmito formáty.  
  
-   Identifikátor URI SAS neobsahuje název platného objektu blob.  

**Rozlišení:** Znovu modul vyvolání výjimky. Ověřte, zda zadaný objekt blob existuje v kontejneru v účtu úložiště a že oprávnění umožňují zobrazit objekt blob. Pokud máte Excel nebo CSV s formáty kódování, ověřte, zda je vstup **názvu kontejneru formuláře nebo názvu souboru.** Ověřte, zda identifikátor URI SAS obsahuje název platného objektu blob.  

|Zprávy o výjimky|
|------------------------|
|Název objektu blob úložiště Azure je nesprávný.|
|Název objektu blob úložiště Azure {blob_name} je nesprávný.|
|Název objektu blob úložiště Azure s předponou "{blob_name_prefix}" neexistuje.|
|Nepodařilo se najít žádné objekty BLOB úložiště Azure v kontejneru {container_name}.|
|Nepodařilo se najít žádné objekty BLOB úložiště Azure se zástupnou cestou {blob_wildcard_path}.|


## <a name="error-0066"></a>Chyba 0066  
 Výjimka nastane, pokud prostředek nelze odeslat do objektu blob Azure.  

 K této chybě v Azure Machine Learning dochází, pokud prostředek nelze odeslat do objektu blob Azure.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Obě se ukládají do stejného účtu úložiště Azure jako účet obsahující vstupní soubor.  

**Rozlišení:** Znovu modul. Ověřte, zda je název účtu Azure, klíč úložiště a kontejner správné a že účet má oprávnění k zápisu do kontejneru.  

|Zprávy o výjimky|
|------------------------|
|Prostředek nelze odeslat do úložiště Azure.|
|Soubor {source_path} nelze odeslat do úložiště Azure jako {dest_path}.|


## <a name="error-0067"></a>Chyba 0067  
 K výjimce dochází, pokud má datová sada jiný počet sloupců, než bylo očekáváno.  

 K této chybě v Azure Machine Learning dochází, pokud má datová sada jiný počet sloupců, než bylo očekáváno.  Tato chyba se zobrazí, pokud se počet sloupců v datové sadě liší od počtu sloupců, které modul očekává během provádění.  

**Rozlišení:** Upravte vstupní datovou sadu nebo parametry.  

|Zprávy o výjimky|
|------------------------|
|Neočekávaný počet sloupců v datové tabulce.|
|Neočekávaný počet sloupců v datové sadě {dataset_name}.|
|Byly očekávány sloupce {expected_column_count}, ale místo toho byly nalezeny sloupce {actual_column_count}.|
|Ve vstupní datové sadě {dataset_name}byl ve sloupci (sloupech) {expected_column_count}, ale místo toho byl nalezen sloupec (sloupce) {actual_column_count}.|


## <a name="error-0068"></a>Chyba 0068  
 Výjimka nastane, pokud zadaný skript Hive není správný.  

 K této chybě v Azure Machine Learning dochází, pokud jsou ve skriptu QL Hive syntaktické chyby nebo pokud interpret Hive narazí na chybu při provádění dotazu nebo skriptu.  

**Rozlišení:**

Chybová zpráva z Hive je obvykle hlášena zpět do protokolu chyb, takže můžete provést akci na základě konkrétní chyby. 

+ Otevřete modul a zkontrolujte, zda dotaz neobsahuje chyby.  
+ Ověřte, zda dotaz funguje správně mimo Azure Machine Learning přihlášením ke konzoli Hive clusteru Hadoop a spuštěním dotazu.  
+ Zkuste umístit komentáře do skriptu Hive do samostatného řádku, na rozdíl od míchání spustitelných příkazů a komentářů do jednoho řádku.  

### <a name="resources"></a>Zdroje a prostředky

Nápovědu k dotazům Hive pro strojové učení naleznete v následujících článcích:

+ [Vytváření tabulek hive a načítání dat z úložiště objektů blob Azure](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Zkoumání dat v tabulkách pomocí dotazů Hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Vytvoření funkcí pro data v clusteru Hadoop pomocí dotazů Hivu](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Hive pro sql uživatele Cheat Sheet (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Zprávy o výjimky|
|------------------------|
|Hive skript je nesprávný.|


## <a name="error-0069"></a>Chyba 0069  
 Výjimka nastane, pokud zadaný skript SQL není správný.  

 K této chybě v Azure Machine Learning dochází, pokud zadaný skript SQL má problémy se syntaxí nebo pokud sloupce nebo tabulka zadaná ve skriptu není platná. 

 Tato chyba se zobrazí, pokud modul SQL narazí na nějakou chybu při provádění dotazu nebo skriptu. Chybová zpráva SQL je obvykle hlášena zpět v protokolu chyb, takže můžete provést akci na základě konkrétní chyby.  

**Rozlišení:** Znovu modul a zkontrolujte dotaz SQL pro chyby.  

 Ověřte, zda dotaz funguje správně mimo Azure ML přihlášením k databázovému serveru přímo a spuštěním dotazu.  

 Pokud je zpráva vyprošená SQL hlášena výjimkou modulu, provést akci na základě hlášené chyby. Například chybové zprávy někdy obsahují konkrétní pokyny k pravděpodobné chybě:
+ *Žádný takový sloupec nebo chybějící databáze*, což znamená, že jste zadali název sloupce nesprávně. Pokud jste si jisti, že název sloupce je správný, zkuste k uzavření identifikátoru sloupce použít závorky nebo uvozovky.
+ *Chyba logiky \<SQL\>poblíž klíčového slova SQL*, což znamená, že před zadaným klíčovým slovem může být chyba syntaxe

  
|Zprávy o výjimky|
|------------------------|
|Skript SQL je nesprávný.|
|Dotaz SQL {sql_query} není správný.|
|Dotaz SQL {sql_query} není správný. Zpráva o výjimce: {exception}.|


## <a name="error-0070"></a>Chyba 0070  
 Výjimka nastane při pokusu o přístup k neexistující tabulce Azure.  

 K této chybě v Azure Machine Learning dochází při pokusu o přístup k neexistující tabulce Azure. Tato chyba se zobrazí, pokud zadáte tabulku v úložišti Azure, která neexistuje při čtení z nebo zápisu do Azure Table Storage. K tomu může dojít, pokud zadejte název požadované tabulky nebo máte neshodu mezi cílovým názvem a typem úložiště. Například jste chtěli číst z tabulky, ale místo toho zadali název objektu blob.  

**Rozlišení:** Znovu zkontrolujte modul a ověřte, zda je název tabulky správný.  

|Zprávy o výjimky|
|------------------------|
|Tabulka Azure neexistuje.|
|Tabulka Azure {table_name} neexistuje.|


## <a name="error-0072"></a>Chyba 0072  
 Výjimka nastane v případě časového limitu připojení.  

 K této chybě v Azure Machine Learning dochází, když časový limit připojení. Tato chyba se zobrazí, pokud aktuálně existují problémy s připojením se zdrojem dat nebo cílem, jako je například pomalé připojení k internetu, nebo pokud je datová sada velká a/nebo dotaz SQL ke čtení v datech provádí složité zpracování.  

**Rozlišení:** Zjistěte, jestli se aktuálně objevují problémy s pomalým připojením k úložišti Azure nebo k internetu.  

|Zprávy o výjimky|
|------------------------|
|Došlo k časovému limitu připojení.|


## <a name="error-0073"></a>Chyba 0073  
 Výjimka nastane, pokud dojde k chybě při převodu sloupce na jiný typ.  

 K této chybě v Azure Machine Learning dochází, když není možné převést sloupec na jiný typ.  Tato chyba se zobrazí, pokud modul vyžaduje určitý typ a není možné převést sloupec na nový typ.  

**Rozlišení:** Upravte vstupní datovou sadu tak, aby sloupec mohl být převeden na základě vnitřní výjimky.  

|Zprávy o výjimky|
|------------------------|
|Převod sloupce se nezdařil.|
|Převod sloupce na {target_type} se nezdařil.|


## <a name="error-0075"></a>Chyba 0075  
K výjimce dochází, když se při kvantizaci datové sady používá neplatná funkce binningu.  

K této chybě v Azure Machine Learning dochází, když se pokoušíte přihrádat data pomocí nepodporované metody nebo když jsou neplatné kombinace parametrů.  

**Rozlišení:**

Zpracování chyb pro tuto událost byla zavedena v dřívější verzi Azure Machine Learning, která umožnila další přizpůsobení binning metody. V současné době jsou všechny binning metody založeny na výběru z rozevíracího seznamu, takže technicky by již nemělo být možné získat tuto chybu.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->

|Zprávy o výjimky|
|------------------------|
|Byla použita neplatná binningová funkce.|


## <a name="error-0077"></a>Chyba 0077  
 K výjimce dochází, když je předán neznámý režim zápisu souboru blob.  

 K této chybě v Azure Machine Learning dochází, pokud je ve specifikacích pro cíl nebo zdroj objektu blob předán neplatný argument.  

**Rozlišení:** Téměř ve všech modulech, které importují nebo exportují data do úložiště objektů blob Azure a z něj, jsou hodnoty parametrů ovládající režim zápisu přiřazeny pomocí rozevíracího seznamu. proto není možné předat neplatnou hodnotu a tato chyba by se neměla zobrazit. Tato chyba bude v pozdější verzi zastaralá.  

|Zprávy o výjimky|
|------------------------|
|Nepodporovaný režim zápisu objektu blob.|
|Nepodporovaný režim zápisu objektu blob: {blob_write_mode}.|


## <a name="error-0078"></a>Chyba 0078  
 K výjimce dochází, když možnost HTTP pro [import dat](import-data.md) obdrží stavový kód 3xx označující přesměrování.  

 K této chybě ve službě Azure Machine Learning dochází, když možnost HTTP pro [import dat](import-data.md) obdrží stavový kód 3xx (301, 302, 304 atd.) označující přesměrování. Tato chyba se zobrazí, pokud se pokusíte připojit ke zdroji HTTP, který přesměruje prohlížeč na jinou stránku. Z bezpečnostních důvodů není přesměrování webů povoleno jako zdroje dat pro Azure Machine Learning.  

**Rozlišení:** Pokud je web důvěryhodným webem, zadejte přímo přesměrovanou adresu URL.  

|Zprávy o výjimky|
|------------------------|
|Přesměrování http není povoleno.|


## <a name="error-0079"></a>Chyba 0079  
 Výjimka nastane, pokud je nesprávně zadán název kontejneru úložiště Azure.  

 K této chybě v Azure Machine Learning dochází, pokud je nesprávně zadán název kontejneru úložiště Azure. Tato chyba se zobrazí, pokud jste nezadali kontejner a název objektu blob (soubor) pomocí **cesty k objektu blob možnost** při zápisu do úložiště objektů blob Azure.  

**Rozlišení:** Znovu zkontrolujte modul [Exportovat data](export-data.md) a ověřte, zda zadaná cesta k objektu blob obsahuje kontejner i název souboru ve formátu **kontejneru nebo názvu souboru**.  

|Zprávy o výjimky|
|------------------------|
|Název kontejneru úložiště Azure je nesprávný.|
|Název kontejneru úložiště Azure "{container_name}" je nesprávný; byl očekáván název kontejneru/objektu blob formátu.|


## <a name="error-0080"></a>Chyba 0080  
 K výjimce dochází, když sloupec se všemi chybějícími hodnotami není povolen modulem.  

 Tato chyba v Azure Machine Learning se vytváří, když jeden nebo více sloupců spotřebovaných modulem obsahuje všechny chybějící hodnoty. Například pokud modul je výpočet agregační statistiky pro každý sloupec, nemůže pracovat na sloupec obsahující žádná data. V takových případech je zastavení provádění modulu s touto výjimkou.  

**Rozlišení:** Znovu navštivte vstupní datovou sadu a odeberte všechny sloupce, které obsahují všechny chybějící hodnoty.  

|Zprávy o výjimky|
|------------------------|
|Sloupce se všemi chybějícími hodnotami nejsou povoleny.|
|Ve sloupci {col_index_or_name} chybí všechny hodnoty.|


## <a name="error-0081"></a>Chyba 0081  
 Výjimka nastane v modulu PCA, pokud se počet dimenzí, na které chcete snížit, rovná počtu sloupců prvků ve vstupní datové sadě, které obsahují alespoň jeden sloupec řídkých prvků.  

 Tato chyba v Azure Machine Learning se vytváří, pokud jsou splněny následující podmínky: (a) vstupní datová sada má alespoň jeden řídký sloupec a (b) konečný počet požadovaných dimenzí je stejný jako počet vstupních dimenzí.  

**Rozlišení:** Zvažte snížení počtu dimenzí ve výstupu na menší než počet dimenzí ve vstupu. To je typické v aplikacích PCA.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->

|Zprávy o výjimky|
|------------------------|
|Pro datovou sadu obsahující řídké sloupce prvků by měl být počet dimenzí, na které se má snížit, menší než počet sloupců prvku.|


## <a name="error-0082"></a>Chyba 0082  
 K výjimce dochází, pokud model nelze úspěšně rekonstruovat.  

 K této chybě v Azure Machine Learning dochází, když uložený model strojového učení nebo transformace nelze načíst novější verzí runtime Azure Machine Learning v důsledku narušující změny.  

**Rozlišení:** Trénovací kanál, který vytvořil model nebo transformaci, musí být znovu spuštěn a model nebo transformace musí být znovu uloženy.  

|Zprávy o výjimky|
|------------------------|
|Model nelze rekonstruovat, protože je pravděpodobně serializován se starším formátem serializace. Přeškolte a znovu jej uložte.|


## <a name="error-0083"></a>Chyba 0083  
 Výjimka nastane, pokud datovou sadu použitou pro školení nelze použít pro konkrétní typ studenta.  

 Tato chyba v Azure Machine Learning se vytváří, když je datová sada nekompatibilní s trénovaným studentem. Například datová sada může obsahovat alespoň jednu chybějící hodnotu v každém řádku a v důsledku toho by byla během trénování přeskočena celá datová sada. V ostatních případech některé algoritmy strojového učení, jako je například detekce anomálií neočekávají popisky, které mají být k dispozici a může vyvolat tuto výjimku, pokud popisky jsou k dispozici v datové sadě.  

**Rozlišení:** Prokontrolu požadavků na vstupní datovou sadu se podívejte do dokumentace studenta, který se používá ke kontrole požadavků na vstupní datovou sadu. Zkontrolujte sloupce, abyste viděli, že jsou k dispozici všechny požadované sloupce.  

|Zprávy o výjimky|
|------------------------|
|Datová sada použitá pro trénování je neplatná.|
|{data_name} obsahuje neplatná data pro školení.|
|{data_name} obsahuje neplatná data pro školení. Typ studenta: {learner_type}.|
|{data_name} obsahuje neplatná data pro školení. Typ studenta: {learner_type}. Důvod: {důvod}.|
|Nepodařilo se použít akci {action_name} na trénovací data {data_name}. Důvod: {důvod}.|


## <a name="error-0084"></a>Chyba 0084  
 Výjimka nastane, když jsou vyhodnocovány skóre vyrobené ze skriptu R. Tato aplikace není v současné době podporována.  

 K této chybě ve službě Azure Machine Learning dochází, pokud se pokusíte použít jeden z modulů pro vyhodnocení modelu s výstupem ze skriptu R, který obsahuje skóre.  

**Rozlišení:**

|Zprávy o výjimky|
|------------------------|
|Vyhodnocení skóre vyrobené vlastní model je v současné době nepodporuje.|


## <a name="error-0085"></a>Chyba 0085  
 K výjimce dochází, když se vyhodnocení skriptu nezdaří s chybou.  

 K této chybě v Azure Machine Learning dochází, když spouštěte vlastní skript, který obsahuje chyby syntaxe.  

**Rozlišení:** Zkontrolujte kód v externím editoru a zkontrolujte chyby.  

|Zprávy o výjimky|
|------------------------|
|Při vyhodnocování skriptu došlo k chybě.|
|Při vyhodnocování skriptu došlo k následující chybě, další informace naleznete v protokolu výstupu:<br />---------- Úvodní chybová zpráva z překladače {script_language} ----------<br />{zpráva}<br />---------- Konec chybové zprávy z ---------- interpretu {script_language}|


## <a name="error-0090"></a>Chyba 0090  
 K výjimce dochází, když se nezdaří vytvoření tabulky Hive.  

 K této chybě v Azure Machine Learning dochází, když používáte [export dat](export-data.md) nebo jinou možnost pro uložení dat do clusteru HDInsight a zadanou tabulku Hive nelze vytvořit.  

**Rozlišení:** Zkontrolujte název účtu úložiště Azure přidružený ke clusteru a ověřte, že používáte stejný účet ve vlastnostech modulu.  

|Zprávy o výjimky|
|------------------------|
|Tabulku Hive nelze vytvořit. U clusteru HDInsight se ujistěte, že název účtu úložiště Azure přidružený ke clusteru je stejný jako název předávaný parametrem modulu.|
|Tabulku Hive {table_name} nelze vytvořit. U clusteru HDInsight se ujistěte, že název účtu úložiště Azure přidružený ke clusteru je stejný jako název předávaný parametrem modulu.|
|Tabulku Hive {table_name} nelze vytvořit. U clusteru HDInsight se ujistěte, že název účtu úložiště Azure přidružený ke clusteru je {cluster_name}.|


## <a name="error-0102"></a>Chyba 0102  
 Vyvolána, když soubor ZIP nelze extrahovat.  

 K této chybě v Azure Machine Learning dochází při importu zip balíček s příponou ZIP, ale balíček buď není soubor zip, nebo soubor nepoužívá podporovaný formát zip.  

**Rozlišení:** Zkontrolujte, zda je vybraný soubor platným souborem ZIP a zda byl komprimován pomocí jednoho z podporovaných kompresních algoritmů.  

 Pokud se tato chyba zobrazí při importu datových sad v komprimovaném formátu, ověřte, zda všechny obsažené soubory používají jeden z podporovaných formátů souborů a zda jsou ve formátu Unicode.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->

 Zkuste si přečíst požadované soubory do nové komprimované složky zip a zkuste znovu přidat vlastní modul.  

|Zprávy o výjimky|
|------------------------|
|Daný soubor ZIP není ve správném formátu.|


## <a name="error-0105"></a>Chyba 0105  
 Tato chyba se zobrazí, pokud definiční soubor modulu obsahuje nepodporovaný typ parametru  
  
 Tato chyba v Azure Machine Learning se vytvoří při vytvoření vlastní definice xml modulu a typ parametru nebo argumentu v definici neodpovídá podporovanému typu.  
  
**Rozlišení:** Ujistěte se, že vlastnost typu libovolného prvku **Arg** ve vlastním definičním souboru XML modulu je podporovaným typem.  
  
|Zprávy o výjimky|  
|------------------------|  
|Nepodporovaný typ parametru.|  
|Nepodporovaný typ{0}parametru ' ' zadán.|  


## <a name="error-0107"></a>Chyba 0107  
 Vyvolána, když definiční soubor modulu definuje nepodporovaný typ výstupu  
  
 Tato chyba v Azure Machine Learning se vytváří, když typ výstupního portu ve vlastní definici xml modulu neodpovídá podporovanému typu.  
  
**Rozlišení:** Ujistěte se, že vlastnost typu výstupního prvku ve vlastním definičním souboru XML modulu je podporovaným typem.  
  
|Zprávy o výjimky|  
|------------------------|  
|Nepodporovaný typ výstupu.|  
|Byl zadán nepodporovaný typ výstupu {output_type}.|  


## <a name="error-0125"></a>Chyba 0125  
 Vyvolána při schématu pro více datových sad neodpovídá.  

**Rozlišení:**

|Zprávy o výjimky|
|------------------------|
|Schéma datové sady se neshoduje.|


## <a name="error-0127"></a>Chyba 0127  
 Velikost obrazových bodů obrázku překračuje povolený limit  

 K této chybě dochází, pokud čtete obrázky z datové sady obrazu pro klasifikaci a obrázky jsou větší, než model zvládne.  

 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  

-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->

|Zprávy o výjimky|
|------------------------|
|Velikost obrazových bodů obrázku překračuje povolený limit.|
|Velikost obrazových bodů v souboru {file_path} překračuje povolený limit: {size_limit}.|


## <a name="error-0128"></a>Chyba 0128  
 Počet podmíněných pravděpodobností pro sloupce kategorií překračuje limit.  

**Rozlišení:**

|Zprávy o výjimky|
|------------------------|
|Počet podmíněných pravděpodobností pro sloupce kategorií překračuje limit.|
|Počet podmíněných pravděpodobností pro sloupce kategorií překračuje limit. Sloupce {column_name_or_index_1} a {column_name_or_index_2} jsou problematické dvojice.|


## <a name="error-0129"></a>Chyba 0129  
 Počet sloupců v datové sadě překračuje povolený limit.  

**Rozlišení:**

|Zprávy o výjimky|
|------------------------|
|Počet sloupců v datové sadě překračuje povolený limit.|
|Počet sloupců v datové sadě v {dataset_name} překračuje povolený počet sloupců.|
|Počet sloupců v datové sadě v {dataset_name} překračuje povolený limit {component_name}.|
|Počet sloupců v datové sadě v {dataset_name} překračuje povolený limit {limit_columns_count} {component_name}.|


## <a name="error-0134"></a>Chyba 0134
K výjimce dochází, když sloupec popisku chybí nebo má nedostatečný počet řádků označených popiskem.  

K této chybě dochází, když modul vyžaduje sloupec popisku, ale nezahrnuli jste jeden do výběru sloupce nebo sloupec popisku chybí příliš mnoho hodnot.

K této chybě může dojít také při předchozí operaci změní datovou sadu tak, že nedostatečné řádky jsou k dispozici pro operace příjem dat. Předpokládejme například, že používáte výraz v modulu **Oddíl a Ukázkový** k rozdělení datové sady hodnotami. Pokud nejsou nalezeny žádné shody pro váš výraz, jedna z datových sad vyplývajících z oddílu by byla prázdná.

Řešení: 

 Pokud do výběru sloupce zahrnete sloupec popisku, ale není rozpoznán, označte jej jako sloupec popisku pomocí modulu [Upravit metadata.](edit-metadata.md)

  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->
  Potom můžete pomocí modulu [Vyčistit chybějící data](clean-missing-data.md) odebrat řádky s chybějícími hodnotami ve sloupci popisku. 

 Zkontrolujte vstupní datové sady a ujistěte se, že obsahují platná data a dostatek řádků, aby splňovaly požadavky operace. Mnoho algoritmů vygeneruje chybovou zprávu, pokud vyžadují některé řádky minimálního počtu dat, ale data obsahují pouze několik řádků nebo pouze záhlaví.

|Zprávy o výjimky|
|------------------------|
|K výjimce dochází, když sloupec popisku chybí nebo má nedostatečný počet řádků označených popiskem.|
|K výjimce dochází, pokud sloupec popisku chybí nebo má méně než řádky s popiskem {required_rows_count}.|
|K výjimce dochází, pokud sloupec popisků v datové sadě {dataset_name} chybí nebo má méně než řádky s popiskem {required_rows_count}.|


## <a name="error-0138"></a>Chyba 0138  
 Paměť byla vyčerpána, nelze dokončit spuštění modulu. Převzorkování datové sady dolů může pomoci zmírnit problém.  

 K této chybě dochází, když modul, který běží vyžaduje více paměti, než je k dispozici v kontejneru Azure. K tomu může dojít, pokud pracujete s velkou datovou sadou a aktuální operace se nevejde do paměti.  

**Rozlišení:** Pokud se pokoušíte číst velkou datovou sadu a operaci nelze dokončit, může pomoci převzorkování datové sady dolů.  

  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  

 Sometimes transient loads can lead to this error. Machine support also changes over time. 

 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->

|Zprávy o výjimky|
|------------------------|
|Paměť byla vyčerpána, nelze dokončit spuštění modulu.|
|Paměť byla vyčerpána, nelze dokončit spuštění modulu. Podrobnosti: {details}|


## <a name="error-0141"></a>Chyba 0141  
 K výjimce dochází, pokud je počet vybraných číselných sloupců a jedinečných hodnot v kategoriích a řetězec sloupcích příliš malý.  

 K této chybě v Azure Machine Learning dochází, když ve vybraném sloupci není dostatek jedinečných hodnot k provedení operace.  

**Rozlišení:** Některé operace provádět statistické operace na funkce a kategorické sloupce a pokud není dostatek hodnot, operace může selhat nebo vrátit neplatný výsledek. Zkontrolujte datovou sadu, abyste zjistili, kolik hodnot je ve sloupcích prvků a popisků, a zjistěte, zda je operace, kterou se pokoušíte provést, statisticky platná.  

 Pokud je zdrojová datová sada platná, můžete také zkontrolovat, zda některé operace dat nebo metadat změnily data a odebraly některé hodnoty.  

 Pokud operace předcházející zahrnují rozdělení, vzorkování nebo převzorkování, ověřte, zda výstupy obsahují očekávaný počet řádků a hodnot.  

|Zprávy o výjimky|
|------------------------|
|Počet vybraných číselných sloupců a jedinečných hodnot v kategoriích a řetězec sloupcích je příliš malý.|
|Celkový počet vybraných číselných sloupců a jedinečné hodnoty v kategoriích a sloupcích řetězců (aktuálně {actual_num}) by měly být alespoň {lower_boundary}.|


## <a name="error-0154"></a>Chyba 0154  
 K výjimce dochází, když se uživatel pokusí připojit data v klíčových sloupcích s nekompatibilním typem sloupce.

|Zprávy o výjimky|
|------------------------|
|Typy prvků klíčových sloupců nejsou kompatibilní.|
|Typy prvků klíčových sloupců nejsou kompatibilní. (vlevo: {keys_left}; vpravo: {keys_right})|


## <a name="error-0155"></a>Chyba 0155  
 K výjimce dochází, pokud názvy sloupců datové sady nejsou řetězce.

|Zprávy o výjimky|
|------------------------|
|Název sloupce datového rámce musí být typ řetězce. Názvy sloupců nejsou řetězce.|
|Název sloupce datového rámce musí být typ řetězce. Názvy sloupců {column_names} nejsou řetězce.|


## <a name="error-0156"></a>Chyba 0156  
 K výjimce dochází, když se nepodařilo číst data z Azure SQL Database.

|Zprávy o výjimky|
|------------------------|
|Nepodařilo se číst data z Azure SQL Database.|
|Nepodařilo se číst data z databáze Azure SQL: {detailed_message} DB: {database_server_name}:{database_name} Dotaz: {sql_statement}|


## <a name="error-0157"></a>Chyba 0157  
 Úložiště dat nebylo nalezeno.

|Zprávy o výjimky|
|------------------------|
|Informace o úložišti dat jsou neplatné.|
|Informace o úložišti dat jsou neplatné. Nepodařilo se získat úložiště dat AzureML {datastore_name} v pracovním prostoru {workspace_name}.|


## <a name="error-0158"></a>Chyba 0158
 Vyvolána, pokud je adresář transformace neplatný.

|Zprávy o výjimky|
|------------------------------------------------------------|
|Given TransformationDirectory je neplatný.|
|Adresář Transformár "{arg_name} je neplatný. Důvod: {důvod}. Spusťte znovu trénovací experiment, který generuje soubor Transformace. Pokud byl tréninkový experiment odstraněn, vytvořte a uložte soubor Transformace.|


## <a name="error-1000"></a>Chyba 1000  
Výjimka interní knihovny.  

Tato chyba je k dispozici zachytit jinak neošetřené vnitřní chyby motoru. Příčina této chyby se proto může lišit v závislosti na modulu, který chybu vygeneroval.  

Chcete-li získat další nápovědu, doporučujeme zaúčtovat podrobnou zprávu, která doprovází chybu na fóru Azure Machine Learning, spolu s popisem scénáře, včetně dat používaných jako vstupy. Tato zpětná vazba nám pomůže určit priority chyb a identifikovat nejdůležitější problémy pro další práci.  

|Zprávy o výjimky|
|------------------------|
|Výjimka knihovny.|
|Výjimka knihovny: {exception}.|
|Neznámá výjimka knihovny: {exception}. {customer_support_guidance}.|

