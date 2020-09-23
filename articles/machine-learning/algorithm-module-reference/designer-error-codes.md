---
title: Řešení chyb modulu návrháře
titleSuffix: Azure Machine Learning
description: Řešení potíží s kódy chyb modulu v Návrháři Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: troubleshooting
author: likebupt
ms.author: keli19
ms.date: 04/16/2020
ms.openlocfilehash: c0a55780687b4c03d6809d1d740bf0b0afcd63fd
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90908098"
---
# <a name="exceptions-and-error-codes-for-the-designer"></a>Výjimky a kódy chyb pro návrháře

Tento článek popisuje chybové zprávy a kódy výjimek v Návrháři Azure Machine Learning, které vám pomůžou při odstraňování potíží s kanály machine learningu.

Chybovou zprávu najdete v Návrháři následujícím způsobem:  

- Vyberte modul, který selhal, klikněte na kartu **výstupy + protokoly** , najdete podrobný protokol v souboru **70_driver_log.txt** v kategorii **AzureML-logs** .

- Podrobnou chybu modulu můžete zjistit v error_info.jsv části **module_statistics** kategorie.

Níže jsou uvedené chybové kódy modulů v návrháři.

## <a name="error-0001"></a>Chyba 0001  
 K výjimce dojde v případě, že se nepovedlo najít jeden nebo více zadaných sloupců datové sady.  

 Tato chyba se zobrazí, pokud je pro modul proveden výběr sloupce, ale ve vstupní datové sadě neexistují vybrané sloupce. K této chybě může dojít, pokud jste ručně zadali název sloupce nebo pokud selektor sloupců zadal navrhovaný sloupec, který v datové sadě neexistoval při spuštění kanálu.  

**Řešení:** Znovu navštivte modul, který tuto výjimku vyvolal, a ověřte, zda jsou název sloupce nebo názvy správné a zda existují všechny odkazované sloupce.  

|Zprávy výjimek|
|------------------------|
|Jeden nebo více zadaných sloupců nebylo nalezeno.|
|Sloupec s názvem nebo indexem {column_id} nebyl nalezen.|
|Sloupec s názvem nebo indexem "{column_id}" neexistuje v "{arg_name_missing_column}".|
|Sloupec s názvem nebo indexem "{column_id}" neexistuje v "{arg_name_missing_column}", ale existuje v "{arg_name_has_column}".|
|Sloupce s názvem nebo indexem {column_names} se nenašly.|
|Sloupce s názvem nebo indexem "{column_names}" v "{arg_name_missing_column}" neexistují.|
|Sloupce s názvem nebo indexem {column_names} v {arg_name_missing_column} neexistují, ale existují v {arg_name_has_column}.|


## <a name="error-0002"></a>Chyba 0,002  
 K výjimce dojde v případě, že jeden nebo více parametrů nelze analyzovat nebo převést ze zadaného typu na typ vyžadovaný cílovou metodou.  

 K této chybě dochází v Azure Machine Learning při zadání parametru jako vstupu a typ hodnoty se liší od očekávaného typu a implicitní převod nelze provést.  

**Řešení:** Zkontrolujte požadavky modulu a určete, který typ hodnoty je vyžadován (řetězec, celé číslo, dvojitá přesnost atd.).  

|Zprávy výjimek|
|------------------------|
|Nepovedlo se analyzovat parametr.|
|Nepovedlo se analyzovat parametr {arg_name_or_column}.|
|Nepovedlo se převést parametr {arg_name_or_column} na {to_type}.|
|Nepovedlo se převést parametr {arg_name_or_column} z {from_type} na {to_type}.|
|Nepovedlo se převést hodnotu parametru {arg_name_or_column} {arg_value} z {from_type} na {to_type}.|
|Nepovedlo se převést hodnotu {arg_value} ve sloupci {arg_name_or_column} z {from_type} na {to_type} s použitím zadaného formátu {FMT}.|


## <a name="error-0003"></a>Chyba 0003  
 K výjimce dojde v případě, že jeden nebo více vstupů má hodnotu null nebo je prázdné.  

 Tato chyba se zobrazí v Azure Machine Learning, pokud jsou jakékoli vstupy nebo parametry modulu null nebo prázdné.  K této chybě může dojít například v případě, že jste nezadali žádnou hodnotu parametru. Může k tomu také dojít, pokud vyberete datovou sadu, která obsahuje chybějící hodnoty, nebo prázdnou datovou sadu.  

**Rozhodnutí**

+ Otevřete modul, který vygeneroval výjimku, a ověřte, že byly zadány všechny vstupy. Zajistěte, aby byly zadány všechny požadované vstupy. 
+ Ujistěte se, že data načtená z Azure Storage jsou přístupná a že se název nebo klíč účtu nezměnil.  
+ Ověřte vstupní data pro chybějící hodnoty nebo hodnoty null.
+ Pokud používáte dotaz na zdroj dat, ověřte, že se data vracejí v očekávaném formátu. 
+ Zkontroluje překlepy nebo jiné změny ve specifikaci dat.
  
|Zprávy výjimek|
|------------------------|
|Jeden nebo více vstupů má hodnotu null nebo je prázdné.|
|Vstup {Name} má hodnotu null nebo je prázdný.|


## <a name="error-0004"></a>Chyba 0,0004  
 K výjimce dojde, pokud je parametr menší nebo roven konkrétní hodnotě.  

 Tato chyba se zobrazí v Azure Machine Learning, pokud se parametr ve zprávě nachází pod hranicí, která je potřebná pro zpracování dat modulem.  

**Řešení:** Přečtěte si modul výjimky a upravte parametr tak, aby byl větší než zadaná hodnota.  

|Zprávy výjimek|
|------------------------|
|Parametr by měl být větší než hodnota hranice.|
|Hodnota parametru {arg_name} by měla být větší než {lower_boundary}.|
|Parametr {arg_name} má hodnotu {actual_value}, která by měla být větší než {lower_boundary}.|


## <a name="error-0005"></a>Chyba 0,005  
 K výjimce dojde, pokud je parametr menší než konkrétní hodnota.  

 Tato chyba se zobrazí v Azure Machine Learning, pokud je parametr ve zprávě nižší nebo roven hodnotě hranice vyžadované pro modul, aby data zpracoval.  

**Řešení:** Znovu navštivte modul, který výjimku vyvolal, a upravte parametr tak, aby byl větší nebo roven zadané hodnotě.  

|Zprávy výjimek|
|------------------------|
|Parametr by měl být větší nebo roven hodnotě hranice.|
|Hodnota parametru {arg_name} by měla být větší nebo rovna {lower_boundary}.|
|Parametr {arg_name} má hodnotu {Value}, která by měla být větší nebo rovna {lower_boundary}.|


## <a name="error-0006"></a>Chyba 0006  
 K výjimce dojde, pokud je parametr větší nebo roven zadané hodnotě.  

 Tato chyba se zobrazí v Azure Machine Learning, pokud je parametr ve zprávě větší nebo roven hodnotě hranice vyžadované pro modul, aby data zpracoval.  

**Řešení:** Znovu navštivte modul, který výjimku vyvolal, a upravte parametr tak, aby byl menší než zadaná hodnota.  

|Zprávy výjimek|
|------------------------|
|Neshoda parametrů Jeden z parametrů by měl být menší než jiný.|
|Hodnota parametru {arg_name} by měla být menší než hodnota parametru {upper_boundary_parameter_name}.|
|Parametr {arg_name} má hodnotu {Value}, která by měla být menší než {upper_boundary_parameter_name}.|


## <a name="error-0007"></a>Chyba 0007  
 K výjimce dojde, pokud je parametr větší než konkrétní hodnota.  

 Tato chyba se zobrazí v Azure Machine Learning Pokud ve vlastnostech modulu jste zadali hodnotu, která je větší, než je povoleno. Můžete například zadat data, která jsou mimo rozsah podporovaných kalendářních dat, nebo můžete určit, že se mají použít pět sloupců, pokud jsou k dispozici pouze tři sloupce. 

 Tato chyba se může zobrazit také v případě, že zadáváte dvě sady dat, které se musí nějakým způsobem shodovat. Například pokud přejmenováváte sloupce a určíte sloupce podle indexu, počet názvů, které zadáte, musí odpovídat počtu indexů sloupců. Dalším příkladem může být Matematická operace, která používá dva sloupce, kde sloupce musí mít stejný počet řádků. 

**Rozhodnutí**

 + Otevřete příslušný modul a zkontrolujte všechna nastavení numerických vlastností.
 + Zajistěte, aby všechny hodnoty parametrů spadají do podporovaného rozsahu hodnot pro danou vlastnost.
 + Pokud modul přijímá více vstupů, ujistěte se, že vstupy mají stejnou velikost.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Ověřte, zda se datová sada nebo zdroj dat změnily. Někdy je hodnota, která se v předchozí verzi dat pracovala, nezdařila po počtu sloupců, datových typech sloupců nebo změně velikosti dat.  

|Zprávy výjimek|
|------------------------|
|Neshoda parametrů Jeden z parametrů by měl být menší nebo roven druhému.|
|Hodnota parametru {arg_name} by měla být menší nebo rovna hodnotě parametru {upper_boundary_parameter_name}.|
|Parametr {arg_name} má hodnotu {actual_value}, která by měla být menší nebo rovna {upper_boundary}.|
|Parametr {arg_name}, hodnota {actual_value}, by měla být menší nebo rovna parametru {upper_boundary_parameter_name} s hodnotou {upper_boundary}.|
|Parametr {arg_name}, hodnota {actual_value}, by měla být menší nebo rovna hodnotě {upper_boundary_meaning} {upper_boundary}.|


## <a name="error-0008"></a>Chyba 0008  
 K výjimce dojde, pokud parametr není v rozsahu.  

 Tato chyba se zobrazí v Azure Machine Learning, pokud je parametr ve zprávě mimo hranice vyžadované pro zpracování dat modulem.  

 Tato chyba se zobrazí například v případě, že se pokusíte použít příkaz [Přidat řádky](add-rows.md) pro kombinování dvou datových sad, které mají různý počet sloupců.  

**Řešení:** Přečtěte si modul výjimky a upravte parametr tak, aby byl v zadaném rozsahu.  

|Zprávy výjimek|
|------------------------|
|Hodnota parametru není v zadaném rozsahu.|
|Hodnota parametru {arg_name} není v rozsahu.|
|Hodnota parametru {arg_name} by měla být v rozsahu [{lower_boundary}, {upper_boundary}].|
|Hodnota parametru {arg_name} není v rozsahu. brání|


## <a name="error-0009"></a>Chyba 0009  
 K výjimce dojde, pokud je název účtu úložiště Azure nebo kontejner zadán nesprávně.  

K této chybě dochází v Azure Machine Learning designeru při zadávání parametrů pro účet úložiště Azure, ale jméno nebo heslo nejde přeložit. Chyby v heslech nebo názvech účtů se můžou vyskytnout z mnoha důvodů:

 + Účet je nesprávného typu. Některé nové typy účtů se nepodporují pro použití s nástrojem Machine Learning Designer. Podrobnosti najdete v tématu [Import dat](import-data.md) .
 + Zadali jste nesprávný název účtu.
 + Účet už neexistuje.
 + Heslo účtu úložiště je chybné nebo se změnilo.
 + Nezadali jste název kontejneru, nebo kontejner neexistuje.
 + Nezadali jste plnou cestu k souboru (cesta k objektu BLOB).
   

**Rozhodnutí**

K takovým problémům často dochází, když se pokusíte ručně zadat název účtu, heslo nebo cestu k kontejneru. Doporučujeme použít Průvodce pro [Import dat](import-data.md) , který vám pomůže vyhledat a zkontrolovat jména.

Také ověřte, zda byl odstraněn účet, kontejner nebo objekt BLOB. Pomocí jiného nástroje Azure Storage ověřte, zda byl název účtu a heslo správně zadáno a zda kontejner existuje. 

Azure Machine Learning nepodporuje některé novější typy účtů. Například nové typy úložiště Hot nebo studeno nelze použít pro strojové učení. Účty úložiště Classic i účty úložiště vytvořené jako "obecné účely" fungují správně.

Pokud byla zadána úplná cesta k objektu blob, ověřte, zda je cesta zadána jako **Container/BLOB**a zda v účtu existují oba kontejnery i objekty blob.  

 Cesta by neměla obsahovat počáteční lomítko. Například **/Container/BLOB** je nesprávný a měl by být zadán jako **kontejner nebo objekt BLOB**.  


|Zprávy výjimek|
|------------------------|
|Název nebo název kontejneru Azure Storage je nesprávný.|
|Název účtu úložiště Azure {account_name} nebo název kontejneru {container_name} je nesprávný. očekával se název kontejneru formátu nebo kontejneru objektů BLOB.|


## <a name="error-0010"></a>Chyba 0010  
 K výjimce dojde, pokud vstupní datové sady mají názvy sloupců, které by se měly shodovat, ale ne.  

 Tato chyba se zobrazí v Azure Machine Learning, pokud má index sloupce ve zprávě ve dvou vstupních datových sadách jiné názvy sloupců.  

**Řešení:** Použijte [Upravit metadata](edit-metadata.md) nebo upravte původní datovou sadu tak, aby měla stejný název sloupce pro zadaný index sloupce.  

|Zprávy výjimek|
|------------------------|
|Sloupce s odpovídajícím indexem ve vstupních datových sadách mají různé názvy.|
|Názvy sloupců nejsou stejné pro sloupec {col_index} (založený na nule) vstupních datových sad ({DataSet1.} a {Dataset2} v uvedeném pořadí).|


## <a name="error-0011"></a>Chyba 0011  
 K výjimce dojde, pokud se předaný argument sady sloupců nevztahuje na žádný sloupec DataSet.  

 Tato chyba se zobrazí v Azure Machine Learning, pokud vybraný výběr sloupce neodpovídá žádnému ze sloupců v dané datové sadě.  

 Tato chyba se může zobrazit i v případě, že jste nevybrali sloupec a k fungování tohoto modulu je nutný alespoň jeden sloupec.  

**Řešení:** Upravte výběr sloupce v modulu tak, aby byl použit pro sloupce v datové sadě.  

 Pokud modul vyžaduje, abyste vybrali konkrétní sloupec, jako je například sloupec popisku, ověřte, zda je vybrán správný sloupec.  

 Pokud jsou vybrány nevhodné sloupce, odeberte je a znovu spusťte kanál.  

|Zprávy výjimek|
|------------------------|
|Zadaná sada sloupců se nevztahuje na žádné sloupce datové sady.|
|Zadaná sada sloupců {column_set} se nevztahuje na žádné sloupce datové sady.|


## <a name="error-0012"></a>Chyba 0012  
 K výjimce dojde, pokud nelze vytvořit instanci třídy s předanou sadou argumentů.  

**Řešení:** Tuto chybu uživatel nemůže provést a v budoucí verzi bude zastaralá.  

|Zprávy výjimek|
|------------------------|
|Nevlakový model, nejprve prosím nahlaste model.|
|Nevlakový model ({arg_name}), použijte školený model.|


## <a name="error-0013"></a>Chyba 0013  
 K výjimce dojde, pokud se předaný modul předává do modulu neplatného typu.  

 K této chybě dochází pokaždé, když je trained model nekompatibilní s připojeným modulem bodování. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->

**Rozhodnutí**

Určete typ učení, který je vytvořen modulem školení, a určete modul bodování, který je vhodný pro tento seznam. 

Pokud byl model vyškolen pomocí některého ze specializovaných školicích modulů, připojte školený model pouze k odpovídajícímu specializovanému modulu bodování. 


|Typ modelu|Školicí modul| Modul bodování|
|----|----|----|
|jakékoli třídění|[Trénování modelu](train-model.md) |[Určení skóre modelu](score-model.md)|
|jakýkoli regresní model|[Trénování modelu](train-model.md) |[Určení skóre modelu](score-model.md)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The module does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->

|Zprávy výjimek|
|------------------------|
|Bylo předáno seznámení s neplatným typem.|
|Seznam známek {arg_name} je neplatného typu.|
|Seznam známek {arg_name} má neplatný typ {learner_type}.|
|Bylo předáno seznámení s neplatným typem. Zpráva o výjimce: {exception_message}|


## <a name="error-0014"></a>Chyba 0014  
 K výjimce dojde v případě, že počet jedinečných hodnot sloupce je větší, než je povoleno.  

 K této chybě dochází, pokud sloupec obsahuje příliš mnoho jedinečných hodnot.  Tato chyba se může zobrazit například v případě, že určíte, že sloupec bude zpracován jako kategoriíá data, ale ve sloupci je příliš mnoho jedinečných hodnot, aby bylo možné zpracování dokončit. Tato chyba se může zobrazit také v případě, že došlo k neshodě mezi počtem jedinečných hodnot ve dvou vstupech.   

**Rozhodnutí**

Otevřete modul, který chybu generoval, a Identifikujte sloupce používané jako vstupy. U některých modulů můžete kliknout pravým tlačítkem myši na vstup datové sady a vybrat **vizualizovat** a získat statistiku pro jednotlivé sloupce, včetně počtu jedinečných hodnot a jejich distribuce.

Pro sloupce, které chcete použít pro seskupování nebo kategorizaci, proveďte kroky ke snížení počtu jedinečných hodnot ve sloupcích. Můžete omezit různými způsoby v závislosti na typu dat sloupce. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Nepovedlo se najít řešení odpovídající vašemu scénáři? Můžete poskytnout zpětnou vazbu k tomuto tématu, který obsahuje název modulu, který chybu generoval, a datový typ a mohutnost sloupce. Tyto informace použijeme k zajištění dalších cílových kroků pro řešení potíží pro běžné scénáře.   

|Zprávy výjimek|
|------------------------|
|Počet jedinečných hodnot sloupce je větší, než je povoleno.|
|Počet jedinečných hodnot ve sloupci: {column_name} je větší než povolený.|
|Počet jedinečných hodnot ve sloupci {column_name} překračuje počet řazených kolekcí členů: {limit}.|


## <a name="error-0015"></a>Chyba 0015  
 K výjimce dojde, pokud selhalo připojení k databázi.  

 Tato chyba se zobrazí, pokud zadáte nesprávný název účtu SQL, heslo, databázový server nebo název databáze nebo pokud nelze navázat připojení k databázi z důvodu problémů s databází nebo serverem.  

**Řešení:** Ověřte, že název účtu, heslo, databázový server a databáze byly správně zadány a že zadaný účet má správnou úroveň oprávnění. Ověřte, zda je databáze aktuálně dostupná.  

|Zprávy výjimek|
|------------------------|
|Při vytváření připojení k databázi došlo k chybě.|
|Při vytváření připojení k databázi došlo k chybě: {connection_str}.|


## <a name="error-0016"></a>Chyba 0016  
 K výjimce dojde, pokud vstupní datové sady předané do modulu musí mít kompatibilní typy sloupců, ale ne.  

 Tato chyba se zobrazí v Azure Machine Learning, pokud typy sloupců předané ve dvou nebo více datových sadách nejsou vzájemně kompatibilní.  

**Řešení:** Použijte [Upravit metadata](edit-metadata.md) nebo upravte původní vstupní datovou sadu.<!--, or use [Convert to Dataset](convert-to-dataset.md)--> aby bylo zajištěno, že jsou typy sloupců kompatibilní.  

|Zprávy výjimek|
|------------------------|
|Sloupce s odpovídajícím indexem ve vstupních datových sadách mají nekompatibilní typy.|
|Sloupce {first_col_names} jsou nekompatibilní mezi výukou a testovacími daty.|
|Sloupce {first_col_names} a {second_col_names} jsou nekompatibilní.|
|Typy elementů sloupce nejsou kompatibilní pro sloupec {first_col_names} (počítáno od nuly) vstupních datových sad ({first_dataset_names} a {second_dataset_names} v uvedeném pořadí).|


## <a name="error-0017"></a>Chyba 0017  
 K výjimce dojde, pokud vybraný sloupec používá datový typ, který není podporovaný aktuálním modulem.  

 Tato chyba se může zobrazit například v Azure Machine Learning, pokud výběr sloupce zahrnuje sloupec s datovým typem, který nemůže být zpracován modulem, jako je například řetězcový sloupec pro operaci Math nebo sloupec skóre, ve kterém je sloupec funkce kategorií povinný.  

**Rozhodnutí**
 1. Identifikujte sloupec, který je problémem.
 2. Zkontrolujte požadavky modulu.
 3. Upravte sloupec tak, aby splňoval požadavky. V závislosti na sloupci a převodu, který se pokoušíte provést, možná budete muset použít několik následujících modulů:
    + Pomocí [Upravit metadata](edit-metadata.md) změňte datový typ sloupce nebo změňte využití sloupce z funkce na číselné, kategorií na kategorií a tak dále.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Jako poslední možnost může být nutné změnit původní vstupní datovou sadu.

> [!TIP]
> Nepovedlo se najít řešení odpovídající vašemu scénáři? Můžete poskytnout zpětnou vazbu k tomuto tématu, který obsahuje název modulu, který chybu generoval, a datový typ a mohutnost sloupce. Tyto informace použijeme k zajištění dalších cílových kroků pro řešení potíží pro běžné scénáře. 

|Zprávy výjimek|
|------------------------|
|Sloupec aktuálního typu nelze zpracovat. Typ není podporován modulem.|
|Sloupec typu {col_type} nelze zpracovat. Typ není podporován modulem.|
|Sloupec {col_name} typu {col_type} nelze zpracovat. Typ není podporován modulem.|
|Sloupec {col_name} typu {col_type} nelze zpracovat. Typ není podporován modulem. Název parametru: {arg_name}.|


## <a name="error-0018"></a>Chyba 0018  
 Pokud vstupní datová sada není platná, dojde k výjimce.  

**Řešení:** Tato chyba v Azure Machine Learning se může objevit v mnoha kontextech, takže neexistuje jedno řešení. Obecně se v případě chyby značí, že data zadaná jako vstup modulu mají nesprávný počet sloupců nebo že datový typ neodpovídá požadavkům modulu. Příklad:  

-   Modul vyžaduje sloupec popisku, ale žádný sloupec není označený jako popisek nebo jste ještě nevybrali sloupec popisku.  
  
-   Modul vyžaduje, aby data byla kategorií, ale vaše data jsou číselná.  

<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->

-   Data jsou v nesprávném formátu.  
  
-   Importovaná data obsahují neplatné znaky, chybné hodnoty nebo hodnoty z rozsahu.  
-   Sloupec je prázdný nebo obsahuje příliš mnoho chybějících hodnot.  

 K určení požadavků a způsobu, jakým může vaše data odpovídat, si přečtěte téma nápovědy pro modul, který bude datovou sadu spotřebovat jako vstup.  

 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  

|Zprávy výjimek|
|------------------------|
|Datová sada není platná.|
|{DataSet1.} obsahuje neplatná data.|
|{DataSet1.} a {Dataset2} by měly být konzistentní columnwise.|
|{DataSet1.} obsahuje neplatná data {důvod}.|
|{DataSet1.} obsahuje {invalid_data_category}. {troubleshoot_hint}|
|{DataSet1.} není platné, {důvod}. {troubleshoot_hint}|


## <a name="error-0019"></a>Chyba 0019  
 K výjimce dojde, pokud se očekává, že sloupec bude obsahovat seřazené hodnoty, ale ne.  

 Tato chyba se zobrazí v Azure Machine Learning, pokud nejsou zadané hodnoty sloupce mimo pořadí.  

**Řešení:** Seřaďte hodnoty sloupce ruční úpravou vstupní datové sady a spusťte modul znovu.  

|Zprávy výjimek|
|------------------------|
|Hodnoty ve sloupci nejsou seřazeny.|
|Hodnoty ve sloupci {col_index} nejsou seřazené.|
|Hodnoty ve sloupci {col_index} sady dat {DataSet} nejsou seřazené.|
|Hodnoty v argumentu {arg_name} nejsou seřazené v pořadí {sorting_order}.|


## <a name="error-0020"></a>Chyba 0020  
 K výjimce dojde, pokud je počet sloupců v některých datových sadách předaných do modulu příliš malý.  

 Tato chyba se zobrazí v Azure Machine Learning, pokud není pro modul vybrán dostatek sloupců.  

**Řešení:** Přečtěte modul a ujistěte se, že selektor sloupců má vybraný správný počet sloupců.  

|Zprávy výjimek|
|------------------------|
|Počet sloupců ve vstupní datové sadě je menší než povolené minimum.|
|Počet sloupců ve vstupní datové sadě {arg_name} je menší než povolené minimum.|
|Počet sloupců ve vstupní datové sadě je menší než povolené minimum: {required_columns_count} sloupců.|
|Počet sloupců ve vstupní datové sadě {arg_name} je menší než povolené minimum (sloupce: {required_columns_count}).|


## <a name="error-0021"></a>Chyba 0021  
 K výjimce dojde v případě, že počet řádků v některých datových sadách předaných do modulu je příliš malý.  

 Tato chyba se zobrazila v Azure Machine Learning, pokud v datové sadě není dostatek řádků k provedení zadané operace. Tato chyba se může zobrazit například v případě, že je vstupní datová sada prázdná nebo pokud se pokoušíte provést operaci, která vyžaduje, aby určitý minimální počet řádků byl platný. Takové operace mohou zahrnovat (ale nejsou omezené) seskupení nebo klasifikace na základě statistických metod, určitých typů binningu a učení s počty.  

**Rozhodnutí**

 + Otevřete modul, který vrátil chybu, a ověřte vstupní datovou sadu a vlastnosti modulu. 
 + Ověřte, zda není vstupní datová sada prázdná a zda existuje dostatek řádků dat pro splnění požadavků popsaných v nápovědě k modulu.  
 + Pokud jsou data načtena z externího zdroje, ujistěte se, že je zdroj dat dostupný a že v definici dat nedochází k žádné chybě, která by mohla způsobit, že import získá méně řádků.
 + Provádíte-li operaci s datovým proudem dat modulu, který by mohl ovlivnit typ dat nebo počet hodnot, například operace vyčištění, rozdělení nebo spojení, zkontrolujte výstupy těchto operací a určete počet vrácených řádků.  

|Zprávy výjimek|
|------------------------|
|Počet řádků ve vstupní datové sadě je menší než povolené minimum.|
|Počet řádků ve vstupní datové sadě je menší než povolené minimum: {required_rows_count} řádků.|
|Počet řádků ve vstupní datové sadě je menší než povolené minimum: {required_rows_count} řádků. brání|
|Počet řádků ve vstupní datové sadě {arg_name} je menší než povolené minimum: {required_rows_count} řádků.|
|Počet řádků ve vstupní datové sadě {arg_name} je {actual_rows_count}, méně než povolené minimum: {required_rows_count} řádků.|
|Počet řádků {row_type} ve vstupní datové sadě {arg_name} je {actual_rows_count}, méně než povolené minimum z {required_rows_count} řádků.|


## <a name="error-0022"></a>Chyba 0022  
 K výjimce dojde v případě, že počet vybraných sloupců ve vstupní datové sadě se nerovná očekávanému počtu.  

 K této chybě v Azure Machine Learning může dojít v případě, že modul pro příjem dat nebo operace vyžaduje určitý počet sloupců nebo vstupů a zadali jste příliš málo nebo příliš mnoho sloupců nebo vstupů. Příklad:  

-   Zadejte sloupec nebo klíčový sloupec s jedním popiskem a náhodně vybrané více sloupců.  
  
-   Přejmenováváte sloupce, ale zadali jste více nebo méně názvů, než jsou sloupce.  
  
-   Počet sloupců ve zdroji nebo cíli se změnil nebo se neshoduje s počtem sloupců používaných modulem.  
  
-   Zadali jste čárkami oddělený seznam hodnot pro vstupy, ale počet hodnot se neshoduje, nebo není podporováno více vstupů.  

**Řešení:** Přečtěte modul a zkontrolujte výběr sloupce a ujistěte se, že je vybrán správný počet sloupců. Ověřte výstupy pro nadřazené moduly a požadavky na operace po jednotlivých operacích.  

 Pokud jste použili jednu z možností výběru sloupců, které mohou vybrat více sloupců (indexy sloupců, všechny funkce, všechny číselné atd.), ověřte přesný počet sloupců vrácených výběrem.  

 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->

 Ověřte, že se počet nebo typ nadřazených sloupců nezměnil.  

 Pokud používáte datovou sadu s doporučeními pro výuku modelu, pamatujte, že doporuční očekává omezený počet sloupců, které odpovídají dvojicím položek uživatele nebo uživatelem. Před školením modelu nebo rozdělením datových sad doporučení odeberte další sloupce. Další informace najdete v tématu [rozdělení dat](split-data.md).  

|Zprávy výjimek|
|------------------------|
|Počet vybraných sloupců ve vstupní datové sadě se nerovná očekávanému počtu.|
|Počet vybraných sloupců ve vstupní datové sadě se nerovná {expected_col_count}.|
|Vzor výběru sloupce {selection_pattern_friendly_name} poskytuje počet vybraných sloupců ve vstupní datové sadě nerovná se {expected_col_count}.|
|Ve vzorci výběru sloupce {selection_pattern_friendly_name} se očekává, že se ve vstupní datové sadě vybere tento počet sloupců: {expected_col_count}, ale ve skutečnosti jsou zadané sloupce {selected_col_count}.|


## <a name="error-0023"></a>Chyba 0023  

K výjimce dojde, pokud cílový sloupec vstupní datové sady není platný pro aktuální modul Trainer.  

K této chybě v Azure Machine Learning dochází, pokud cílový sloupec (jak je vybrán v parametrech modulu) není platný datový typ, obsahoval všechny chybějící hodnoty nebo nebyl kategorií podle očekávání.  

**Řešení:** Přejděte do vstupu modulu a zkontrolujte obsah sloupce popisek/cíl. Ujistěte se, že neobsahují všechny chybějící hodnoty. Pokud modul očekává, že cílový sloupec bude kategorií, ujistěte se, že cílový sloupec obsahuje více než jednu jedinečnou hodnotu.  

|Zprávy výjimek|
|------------------------|
|Vstupní datová sada obsahuje nepodporovaný cílový sloupec.|
|Vstupní datová sada má nepodporovaný cílový sloupec {column_index}.|
|Vstupní datová sada má nepodporovaný cílový sloupec {column_index} pro informace o typu {learner_type}.|


## <a name="error-0024"></a>Chyba 0024  
K výjimce dojde, pokud datová sada neobsahuje sloupec popisku.  

 K této chybě v Azure Machine Learning dochází, pokud modul vyžaduje sloupec popisku a datová sada nemá sloupec popisku. Například vyhodnocení pro datovou sadu obvykle vyžaduje, aby byl k dispozici sloupec popisku pro metriky přesnosti Compute.  

Může k tomu také dojít, když je v datové sadě přítomen sloupec popisku, ale Azure Machine Learning nebyl správně zjištěn.

**Rozhodnutí**

+ Otevřete modul, který chybu generoval, a zjistěte, jestli je přítomný sloupec popisku. Název nebo datový typ sloupce nezáleží na tom, jestli sloupec obsahuje jeden výsledek (nebo závislou proměnnou), se kterou se pokoušíte odhadnout. Pokud si nejste jistí, který sloupec má popisek, vyhledejte obecný název, jako je například  *Třída* nebo *cíl*. 
+  Pokud datová sada neobsahuje sloupec popisku, je možné, že sloupec popisku byl explicitně nebo omylem odebrán z nadřazeného objektu. Je také možné, že datová sada není výstupním modulem nadřazeného modulu pro vyhodnocování.
+ Chcete-li sloupec explicitně označit jako sloupec popisku, přidejte modul [Upravit metadata](edit-metadata.md) a připojte datovou sadu. Vyberte pouze sloupec popisek a v rozevíracím seznamu **pole** vyberte možnost **popisek** . 
+ Pokud je jako popisek vybrán špatný sloupec, můžete vybrat možnost **Vymazat popisek** z **polí** a opravit tak metadata sloupce. 
  
|Zprávy výjimek|
|------------------------|
|V datové sadě není žádný sloupec popisku.|
|V {dataset_name} není žádný sloupec popisku.|


## <a name="error-0025"></a>Chyba 0,025  
 K výjimce dojde, pokud datová sada neobsahuje sloupec skóre.  

 K této chybě v Azure Machine Learning dochází, pokud vstup do modelu vyhodnocení neobsahuje platné sloupce skóre. Uživatel se například pokusí vyhodnotit datovou sadu předtím, než se vyhodnotil správným trainem modelu, nebo sloupec skóre byl explicitně vyřazen z hlediska nadřazeného objektu. K této výjimce dojde také v případě, že sloupce skóre v obou datových sadách jsou nekompatibilní. Například se můžete pokusit porovnat přesnost lineárního regresor s binárním klasifikátorem.  

**Řešení:** Přečtěte si vstup do modelu vyhodnocení a prověřte, zda obsahuje jeden nebo více sloupců skóre. V opačném případě se datová sada nevrátila do skóre nebo sloupce skóre byly vyřazeny v modulu pro odesílání dat.  

|Zprávy výjimek|
|------------------------|
|V datové sadě není žádný sloupec skóre.|
|V "{dataset_name}" neexistuje žádný sloupec s skóre.|
|V {dataset_name} neexistuje žádný sloupec skóre, který je vytvořený pomocí {learner_type}. Vyhodnotit datovou sadu pomocí správného typu učení.|


## <a name="error-0026"></a>Chyba 0026  
 Pokud nejsou povoleny sloupce se stejným názvem, dojde k výjimce.  

 K této chybě v Azure Machine Learning dochází, pokud má více sloupců stejný název. Jednou z možností, jak se tato chyba může zobrazit, je, že datová sada nemá automaticky přiřazené řádky záhlaví a názvy sloupců: Col0, Sloupec1 atd.  

**Řešení:** Pokud mají sloupce stejný název, vložte modul [úprav metadat](edit-metadata.md) mezi vstupní datovou sadu a modul. Pomocí voliče sloupců v okně [Upravit metadata](edit-metadata.md) vyberte sloupce, které chcete přejmenovat, a zadejte nové názvy do textového pole **nové názvy sloupců** .  

|Zprávy výjimek|
|------------------------|
|V argumentech jsou zadány stejné názvy sloupců. Stejný název sloupce není povolený modulem.|
|Názvy stejných sloupců v argumentech {arg_name_1} a {arg_name_2} nejsou povolené. Zadejte prosím jiné názvy.|


## <a name="error-0027"></a>Chyba 0027  
 K výjimce dojde v případě, že dva objekty musí mít stejnou velikost, ale nejsou.  

 Jedná se o běžnou chybu v Azure Machine Learning a může to být způsobeno mnoha podmínkami.  

**Řešení:** Neexistuje žádné konkrétní řešení. Můžete ale kontrolovat následující podmínky:  

-   Pokud přejmenováváte sloupce, ujistěte se, že každý seznam (vstupní sloupce a seznam nových názvů) má stejný počet položek.  
  
-   Pokud se připojujete nebo zřetězete ze dvou datových sad, ujistěte se, že mají stejné schéma.  
  
-   Pokud se připojujete k dvěma datovým sadám, které mají více sloupců, ujistěte se, že sloupce klíče mají stejný datový typ a **v výběru vyberte možnost povolující duplicity a zachovat pořadí sloupců**.  

|Zprávy výjimek|
|------------------------|
|Velikost předaných objektů je nekonzistentní.|
|Velikost {friendly_name1} je nekonzistentní s velikostí {friendly_name2}.|


## <a name="error-0028"></a>Chyba 0028  
 V případě, že sada sloupců obsahuje duplicitní názvy sloupců a není povolená, dojde k výjimce.  

 K této chybě v Azure Machine Learning dochází, když jsou názvy sloupců duplikovány; To znamená, že není jedinečné.  

**Řešení:** Pokud mají všechny sloupce stejný název, přidejte instanci [metadat Edit](edit-metadata.md) mezi vstupní datovou sadu a modul, který vyvolává chybu. Pomocí voliče sloupců v části [Upravit metadata](edit-metadata.md) vyberte sloupce, které chcete přejmenovat, a do textového pole **nové názvy** sloupců zadejte názvy nových sloupců. Pokud přejmenováváte více sloupců, zajistěte, aby hodnoty, které zadáte do **nových názvů sloupců** , byly jedinečné.  

|Zprávy výjimek|
|------------------------|
|Sada sloupců obsahuje duplicitní názvy sloupců.|
|Název {duplicated_name} je duplicitní.|
|Název "{duplicated_name}" je duplikován v "{arg_name}".|
|Název {duplicated_name} je duplicitní. Podrobnosti: {details}|


## <a name="error-0029"></a>Chyba 0029  
 K výjimce dojde v případě, že je předán neplatný identifikátor URI.  

 K této chybě v Azure Machine Learning dojde v případě, že je předán neplatný identifikátor URI.  Tato chyba se zobrazí, pokud platí kterákoli z následujících podmínek:  

-   Veřejný nebo SAS identifikátor URI poskytnutý pro Azure Blob Storage pro čtení nebo zápis obsahuje chybu.  
  
-   Časový interval pro podpis SAS vypršel.  
  
-   Webová adresa URL prostřednictvím zdroje HTTP představuje soubor nebo identifikátor URI zpětné smyčky.  
  
-   Adresa URL webu prostřednictvím HTTP obsahuje nesprávně naformátovanou adresu URL.  
  
-   Vzdálený zdroj nemůže přeložit adresu URL.  

**Řešení:** Znovu navštivte modul a ověřte formát identifikátoru URI. Pokud je zdrojem dat webová adresa URL prostřednictvím protokolu HTTP, ověřte, že zamýšlený zdroj není soubor nebo identifikátor URI zpětné smyčky (localhost).  

|Zprávy výjimek|
|------------------------|
|Byl předán neplatný identifikátor URI.|
|Identifikátor URI {invalid_url} je neplatný.|


## <a name="error-0030"></a>Chyba 0030  
 V případě, že není možné stáhnout soubor, dojde k výjimce.  

 K této výjimce v Azure Machine Learning dochází, pokud není možné stáhnout soubor. Tato výjimka se zobrazí, pokud se pokus o čtení ze zdroje HTTP nezdařil po třech (3) opakovaných pokusech.  

**Řešení:** Ověřte, zda je identifikátor URI pro zdroj HTTP správný a zda je web aktuálně přístupný prostřednictvím Internetu.  

|Zprávy výjimek|
|------------------------|
|Soubor nelze stáhnout.|
|Při stahování souboru došlo k chybě: {file_url}.|


## <a name="error-0031"></a>Chyba 0031  
 K výjimce dojde, pokud je počet sloupců v sadě sloupců menší, než je potřeba.  

 K této chybě v Azure Machine Learning dojde, pokud počet vybraných sloupců není menší, než je potřeba.  Tato chyba se zobrazí, pokud není vybrán minimální požadovaný počet sloupců.  

**Řešení:** Přidejte další sloupce do výběru sloupce pomocí **voliče sloupců**.  

|Zprávy výjimek|
|------------------------|
|Počet sloupců v sadě sloupců je menší, než je vyžadováno.|
|Pro vstupní argument {arg_name} by měla být zadána alespoň tato sloupce: {required_columns_count}.|
|Pro vstupní argument {arg_name} by měla být zadána alespoň tato sloupce: {required_columns_count}. Skutečný počet zadaných sloupců je {input_columns_count}.|


## <a name="error-0032"></a>Chyba 0032  
 K výjimce dojde, pokud argument není číslo.  

 Tato chyba se zobrazí v Azure Machine Learning, pokud je argumentem typ Double nebo NaN.  

**Řešení:** Upravte zadaný argument tak, aby používal platnou hodnotu.  

|Zprávy výjimek|
|------------------------|
|Argument není číslo.|
|{arg_name} není číslo.|


## <a name="error-0033"></a>Chyba 0033  
 K výjimce dojde, pokud je argumentem nekonečno.  

 K této chybě v Azure Machine Learning dojde, pokud je argument nekonečný. Tato chyba se zobrazí, pokud je argument buď `double.NegativeInfinity` nebo `double.PositiveInfinity` .  

**Řešení:** Upravte zadaný argument tak, aby byl platnou hodnotou.  

|Zprávy výjimek|
|------------------------|
|Argument musí být konečný.|
|{arg_name} není konečný.|
|Sloupec {column_name} obsahuje nekonečné hodnoty.|


## <a name="error-0034"></a>Chyba 0034  
 K výjimce dojde, pokud pro daný pár uživatel-položka existuje více než jedno hodnocení.  

 K této chybě v Azure Machine Learning dochází v doporučení, pokud dvojice uživatel-položka má více než jednu hodnotu hodnocení.  

**Řešení:** Ujistěte se, že dvojice uživatel-položka má pouze jednu hodnotu hodnocení.  

|Zprávy výjimek|
|------------------------|
|Pro hodnoty v datové sadě existuje více než jedno hodnocení.|
|Více než jedno hodnocení pro uživatele {User} a položku {Item} v tabulce dat předpovědi hodnocení.|
|Pro uživatele {User} a položku {Item} v objektu {DataSet} bylo více než jedno hodnocení.|


## <a name="error-0035"></a>Chyba 0035  
 K výjimce dojde, pokud nebyly pro daného uživatele nebo položku zadány žádné funkce.  

 K této chybě v Azure Machine Learning dochází k tomu, že se snažíte použít model doporučení pro hodnocení, ale vektor funkce se nenašel.  

**Rozhodnutí**

Doporučení Matchbox má určité požadavky, které je nutné splnit při použití funkcí nebo funkcí uživatele.  Tato chyba označuje, že pro uživatele nebo položku, kterou jste zadali jako vstup, chybí vektor funkce. Ujistěte se, že je v datech pro každého uživatele nebo položku k dispozici vektor funkcí.  

 Pokud jste například vypracovali doporučení modelu pomocí funkcí, jako je například stáří, umístění nebo příjem, ale nyní chcete vytvořit skóre pro nové uživatele, kteří nebyli během školení viděli, je nutné poskytnout pro nové uživatele stejnou sadu funkcí (konkrétně, stáří, umístění a příjmové hodnoty), aby pro ně mohli zajistit vhodné předpovědi. 

 Pokud pro tyto uživatele nemáte nějaké funkce, zvažte možnost vygenerovat vhodné funkce v technickém oddělení funkcí.  Pokud například nemáte individuální stáří nebo hodnoty příjmů, můžete vygenerovat přibližné hodnoty, které se mají použít pro skupinu uživatelů. 

<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).

For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->

 > [!TIP]
 > Řešení neplatí pro váš případ? Vítá vás váš názor na tento článek a poskytuje informace o scénáři, včetně modulu a počtu řádků ve sloupci. Tyto informace budeme používat k poskytnutí podrobnějších kroků pro řešení potíží v budoucnu.

|Zprávy výjimek|
|------------------------|
|Pro požadovaného uživatele nebo položku nebyly zadány žádné funkce.|
|Jsou požadovány funkce pro {required_feature_name}, ale nebyly k dispozici.|


## <a name="error-0036"></a>Chyba 0036  
 K výjimce dojde, pokud pro daného uživatele nebo položku byly zadány více vektorů funkcí.  

 K této chybě v Azure Machine Learning dochází, je-li vektor funkce definován více než jednou.  

**Řešení:** Ujistěte se, že vektor funkce není definován více než jednou.  

|Zprávy výjimek|
|------------------------|
|Duplicitní definice funkce pro uživatele nebo položku.|


## <a name="error-0037"></a>Chyba 0037  
 K výjimce dojde, pokud je zadáno více sloupců popisku a je povolena pouze jedna.  

 K této chybě v Azure Machine Learning dojde, pokud je vybráno více než jeden sloupec jako nový sloupec popisku. Většina kontrolních algoritmů pro vzdělávání vyžaduje, aby byl jeden sloupec označený jako cílový nebo popisek.  

**Řešení:** Ujistěte se, že jste vybrali jeden sloupec jako nový sloupec popisku.  

|Zprávy výjimek|
|------------------------|
|Je zadáno více sloupců popisku.|
|V "{dataset_name}" je zadáno více sloupců popisku.|


## <a name="error-0039"></a>Chyba 0039  
 K výjimce dojde v případě, že operace se nezdařila.  

 K této chybě v Azure Machine Learning dochází, pokud nelze dokončit interní operaci.  

**Řešení:** Tato chyba je způsobená mnoha podmínkami a neexistuje žádná zvláštní náprava.  
 Následující tabulka obsahuje obecné zprávy pro tuto chybu, za kterými následuje konkrétní popis podmínky. 

 Pokud nejsou k dispozici žádné podrobnosti, [aplikace Microsoft Q&stránku s otázkou pro odeslání zpětné vazby](https://docs.microsoft.com/answers/topics/azure-machine-learning-studio-classic.html) a poskytuje informace o modulech, které generovaly chybu a související podmínky.

|Zprávy výjimek|
|------------------------|
|Operace se nezdařila.|
|Chyba při dokončování operace: {failed_operation}.|
|Chyba při dokončování operace: {failed_operation}. Důvod: {důvod}|


## <a name="error-0042"></a>Chyba 0042  
 K výjimce dojde, pokud není možné převést sloupec na jiný typ.  

 K této chybě v Azure Machine Learning dochází, pokud není možné převést sloupec na zadaný typ.  Tato chyba se zobrazí, pokud modul vyžaduje konkrétní datový typ, například DateTime, text, číslo s plovoucí desetinnou čárkou nebo celé číslo, ale není možné převést existující sloupec na požadovaný typ.  

Můžete například vybrat sloupec a zkusit ho převést na číselný datový typ pro použití v matematické operaci a tato chyba se zobrazí, pokud sloupec obsahoval neplatná data. 

Další důvod: Tato chyba se může zobrazit, pokud se pokusíte použít sloupec obsahující čísla s plovoucí desetinnou čárkou nebo mnoho jedinečných hodnot jako sloupec kategorií. 

**Rozhodnutí**

+ Otevřete stránku s nápovědu pro modul, který chybu generoval, a ověřte požadavky na datový typ.
+ Zkontrolujte datové typy sloupců ve vstupní datové sadě.
+ Zkontrolujte data pocházející z, což se označuje jako zdroje dat bez schématu.
+ Ověřte datovou sadu pro chybějící hodnoty nebo speciální znaky, které by mohly blokovat převod na požadovaný datový typ. 
    + Číselné datové typy by měly být konzistentní: například kontrolovat čísla s plovoucí desetinnou čárkou ve sloupci celých čísel.
    + Hledejte textové řetězce nebo hodnoty NA v číselném sloupci. 
    + Logické hodnoty lze převést na odpovídající reprezentace v závislosti na požadovaném datovém typu.
    + Kontrola textových sloupců pro znaky jiné než Unicode, znaky tabulátoru nebo řídicí znaky
    + Data DateTime by měla být konzistentní, aby se předešlo chybám modelování, ale vyčištění může být složité vzhledem k mnoha formátům. Zvažte použití <!--the [Execute R Script](execute-r-script.md) or -->Proveďte vyčištění [spuštěním modulů skriptu Pythonu](execute-python-script.md) .  
+ V případě potřeby upravte hodnoty ve vstupní datové sadě tak, aby byl sloupec možné úspěšně převést. Úpravy mohou zahrnovat operace binningu, zkrácení nebo zaokrouhlování, eliminace nerovnosti nebo imputace chybějících hodnot. Některé běžné scénáře transformace dat ve službě Machine Learning najdete v následujících článcích:
    + [Vyčištění chybějících dat](clean-missing-data.md)
    + [Normalizace dat](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->

> [!TIP]
> Řešení nejasných nebo neplatí pro váš případ? Vítá vás váš názor na tento článek a poskytuje informace o scénáři, včetně modulu a datového typu sloupce. Tyto informace budeme používat k poskytnutí podrobnějších kroků pro řešení potíží v budoucnu.  

|Zprávy výjimek|
|------------------------|
|Převod není povolen.|
|Sloupec typu {typ1} nelze převést na sloupec typu {typ2}.|
|Nelze převést sloupec {col_name1} typu {typ1} na sloupec typu {typ2}.|
|Sloupec {col_name1} typu {typ1} nelze převést na sloupec {col_name2} typu {typ2}.|


## <a name="error-0044"></a>Chyba 0044  
 K výjimce dojde, pokud není možné odvodit typ prvku sloupce z existujících hodnot.  

 K této chybě v Azure Machine Learning dochází, pokud není možné odvodit typ sloupce nebo sloupce v datové sadě. K tomu obvykle dochází při zřetězení dvou nebo více datových sad s různými typy prvků. Pokud Azure Machine Learning není schopen určit společný typ, který je schopný reprezentovat všechny hodnoty ve sloupci nebo sloupcích bez ztráty informací, vygeneruje tuto chybu.  

**Řešení:** Zajistěte, aby všechny hodnoty v daném sloupci v obou datových sadách byly buď stejného typu (číselné, logické, kategorií, String, Date atd.), nebo mohou být převedeny na stejný typ.  

|Zprávy výjimek|
|------------------------|
|Typ elementu sloupce nelze odvodit.|
|Typ elementu pro sloupec {column_name} nelze odvodit – všechny elementy jsou odkazy s hodnotou null.|
|Typ elementu pro sloupec {column_name} datové sady {dataset_name} nelze odvodit – všechny elementy jsou odkazy s hodnotou null.|


## <a name="error-0045"></a>Chyba 0045  
 K výjimce dojde, pokud není možné vytvořit sloupec z důvodu smíšených typů prvků ve zdroji.  

 Tato chyba ve Azure Machine Learning je vytvořena, když typy prvků dvou datových sad, které jsou kombinovány, jsou odlišné.  

**Řešení:** Zajistěte, aby všechny hodnoty v daném sloupci v obou datových sadách byly stejného typu (číselné, logické, kategorií, String, Date atd.).  

|Zprávy výjimek|
|------------------------|
|Nelze vytvořit sloupec se smíšenými typy prvků.|
|Sloupec s ID "{column_id}" smíšených typů prvků nelze vytvořit:<br />Typ dat [{row_1}, {column_id}] je {type_1}. <br />Typ dat [{row_2}, {column_id}] je {type_2}.|
|Sloupec s ID "{column_id}" smíšených typů prvků nelze vytvořit:<br />Typ v bloku {chunk_id_1} je {type_1}. <br />Typ v bloku {chunk_id_2} je {type_2} a velikost bloku: {chunk_size}.|


## <a name="error-0046"></a>Chyba 0046  
 K výjimce dojde, pokud není možné vytvořit adresář v zadané cestě.  

 K této chybě v Azure Machine Learning dochází, pokud není možné vytvořit adresář v zadané cestě. Tato chyba se zobrazí, pokud je libovolná část cesty k výstupnímu adresáři pro dotaz na podregistr nesprávná nebo nepřístupná.  

**Řešení:** Znovu navštivte modul a ověřte, zda je cesta k adresáři správně naformátovaná a zda je přístupná pomocí aktuálních přihlašovacích údajů.  

|Zprávy výjimek|
|------------------------|
|Zadejte prosím platný výstupní adresář.|
|Adresář: {path} nelze vytvořit. Zadejte prosím platnou cestu.|


## <a name="error-0047"></a>Chyba 0047  
 K výjimce dojde, pokud je počet sloupců funkce v některých datových sadách předaných do modulu příliš malý.  

 K této chybě v Azure Machine Learning dochází, pokud vstupní datová sada pro školení neobsahuje minimální počet sloupců vyžadovaný algoritmem. Tato datová sada je typicky prázdná nebo obsahuje pouze školicí sloupce.  

**Řešení:** Přečtěte si vstupní datovou sadu, abyste měli jistotu, že existuje jeden nebo více dalších sloupců od sloupce popisek.  

|Zprávy výjimek|
|------------------------|
|Počet sloupců funkce ve vstupní datové sadě je menší než povolené minimum.|
|Počet sloupců funkce ve vstupní datové sadě je menší než povolené minimum: {required_columns_count} sloupců.|
|Počet sloupců funkce ve vstupní datové sadě {arg_name} je menší než povolené minimum (sloupce: {required_columns_count}).|


## <a name="error-0048"></a>Chyba 0048  
 V případě, že není možné otevřít soubor, dojde k výjimce.  

 K této chybě v Azure Machine Learning dochází, když není možné otevřít soubor pro čtení nebo zápis. Tato chyba se může zobrazit z těchto důvodů:  

-   Kontejner nebo soubor (BLOB) neexistuje.  
  
-   Úroveň přístupu souboru nebo kontejneru vám neumožňuje přístup k souboru.  
  
-   Soubor je příliš velký pro čtení nebo špatný formát.  

**Řešení:** Znovu navštivte modul a soubor, který se pokoušíte přečíst.  

 Ověřte, zda jsou názvy kontejneru a souboru správné.  

 Pomocí portálu Azure Classic nebo nástroje Azure Storage ověřte, zda máte oprávnění pro přístup k souboru.  

  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->

|Zprávy výjimek|
|------------------------|
|Soubor nelze otevřít.|
|Při otevírání souboru došlo k chybě: {file_name}.|
|Při otevírání souboru došlo k chybě: {file_name}. Zpráva o výjimce úložiště: {Exception}.|


## <a name="error-0049"></a>Chyba 0049  
 V případě, že není možné analyzovat soubor, dojde k výjimce.  

 K této chybě v Azure Machine Learning dochází, když není možné analyzovat soubor. Tato chyba se zobrazí, pokud formát souboru vybraný v modulu [Import dat](import-data.md) neodpovídá skutečnému formátu souboru nebo pokud soubor obsahuje nerozpoznatelný znak.  

**Řešení:** Přečtěte modul a opravte výběr formátu souboru, pokud se neshoduje s formátem souboru. Pokud je to možné, zkontrolujte soubor a potvrďte, že neobsahuje žádné neplatné znaky.  

|Zprávy výjimek|
|------------------------|
|Soubor nelze analyzovat.|
|Při analýze souboru {file_format} došlo k chybě.|
|Při analýze souboru {file_format} došlo k chybě: {file_name}.|
|Při analýze souboru {file_format} došlo k chybě. Důvod: {failure_reason}.|
|Při analýze souboru {file_format} došlo k chybě: {file_name}. Důvod: {failure_reason}.|


## <a name="error-0052"></a>Chyba 0052  
 K výjimce dojde, pokud je klíč účtu úložiště Azure zadaný nesprávně.  

 K této chybě v Azure Machine Learning dochází, pokud klíč používaný pro přístup k účtu služby Azure Storage není správný. Tato chyba se může zobrazit například v případě, že klíč úložiště Azure byl po zkopírování a vložení zkrácen nebo pokud byl použit nesprávný klíč.  

 Další informace o tom, jak získat klíč pro účet úložiště Azure, najdete v tématu [zobrazení, kopírování a opětovné vygenerování přístupových klíčů k úložišti](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/).  

**Řešení:** Přečtěte si modul a ověřte, jestli je pro účet správný klíč úložiště Azure. v případě potřeby Zkopírujte klíč znovu z portálu Azure Classic.  

|Zprávy výjimek|
|------------------------|
|Klíč účtu úložiště Azure je nesprávný.|


## <a name="error-0053"></a>Chyba 0053  
 K výjimce dojde v případě, že nejsou k dispozici žádné uživatelské funkce ani položky pro Matchbox doporučení.  

 Tato chyba v Azure Machine Learning je vytvořena, když nelze najít vektor funkce.  

**Řešení:** Ujistěte se, že je ve vstupní datové sadě přítomen vektor funkce.  

|Zprávy výjimek|
|------------------------|
|K dispozici jsou uživatelské funkce nebo položky, ale nejsou k dispozici.|


## <a name="error-0056"></a>Chyba 0056  
 K výjimce dojde, pokud sloupce vybrané pro operaci porušují požadavky.  

 K této chybě v Azure Machine Learning dochází, když zvolíte sloupce pro operaci, která vyžaduje, aby sloupec byl určitého datového typu. 

 K této chybě může také dojít, pokud je sloupec správným datovým typem, ale modul, který používáte, vyžaduje, aby byl sloupec také označený jako funkce, popisek nebo sloupec kategorií.  

  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->

**Rozhodnutí**

1.  Zkontrolujte datový typ aktuálně vybraných sloupců. 

2. Ověří, jestli jsou vybrané sloupce kategorií, Label nebo Feature.  
  
3.  Projděte si téma nápovědy pro modul, ve kterém jste provedli výběr sloupce, abyste zjistili, jestli existují konkrétní požadavky na datový typ nebo využití sloupce.  
  
3.  Pomocí [Upravit metadata](edit-metadata.md) změňte typ sloupce po dobu trvání této operace. Nezapomeňte změnit typ sloupce zpátky na původní hodnotu pomocí jiné instance [úprav metadat](edit-metadata.md), pokud ho pro podřízené operace potřebujete.  

|Zprávy výjimek|
|------------------------|
|Jeden nebo více vybraných sloupců nebylo v povolené kategorii.|
|Sloupec s názvem {col_name} není v povolené kategorii.|


## <a name="error-0057"></a>Chyba 0057  
 Při pokusu o vytvoření souboru nebo objektu blob, který již existuje, dojde k výjimce.  

 K této výjimce dochází při použití modulu [exportu dat](export-data.md) nebo jiného modulu k uložení výsledků kanálu v Azure Machine Learning do úložiště objektů BLOB v Azure, ale pokusíte se vytvořit soubor nebo objekt blob, který už existuje.   

**Rozhodnutí**

 Tato chyba se zobrazí jenom v případě, že jste předtím nastavili vlastnost **režim zápisu úložiště objektů BLOB v Azure** na hodnotu **Chyba**. Podle návrhu tento modul vyvolá chybu, pokud se pokusíte zapsat datovou sadu do objektu blob, který již existuje.

 - Otevřete vlastnosti modulu a změňte vlastnost **režim zápisu úložiště objektů BLOB v Azure** na **přepsání**.
 - Případně můžete zadat název jiného cílového objektu BLOB nebo souboru a nezapomeňte zadat objekt blob, který ještě neexistuje.  

|Zprávy výjimek|
|------------------------|
|Soubor nebo objekt BLOB již existuje.|
|Soubor nebo objekt blob "{file_path}" již existuje.|


## <a name="error-0058"></a>Chyba 0058  
 K této chybě v Azure Machine Learning dojde, pokud datová sada neobsahuje očekávaný sloupec popisku.  

 K této výjimce může dojít také v případě, že zadaný sloupec popisku neodpovídá datovým nebo datovým hodnotám, které očekává daný modul pro učení, nebo má nesprávné hodnoty. Tato výjimka se například vytvoří při použití sloupce popisku reálného čísla při výuce binárního třídění.  

**Řešení:** Řešení závisí na seznámcích nebo Trainer, které používáte, a na datových typech sloupců ve vaší datové sadě. Nejdřív ověřte požadavky na algoritmus Machine Learning nebo školicí modul.  

 Znovu navštivte vstupní datovou sadu. Ověřte, že sloupec, který má být považován za popisek, má správný datový typ pro model, který vytváříte.  

 Zkontrolujte vstupy pro chybějící hodnoty a v případě potřeby je odstraňte nebo nahraďte.  

 V případě potřeby přidejte modul [Upravit metadata](edit-metadata.md) a ujistěte se, že sloupec popisek je označený jako popisek.  

|Zprávy výjimek|
|------------------------|
|Hodnoty sloupce popisku a hodnoty sloupce popisku s skóre nejsou srovnatelné.|
|Sloupec popisku není v {dataset_name} očekávaný.|
|Sloupec popisku není podle očekávání v {dataset_name}, {důvod}.|
|Sloupec popisku {column_name} se v {dataset_name} neočekává.|
|Sloupec popisku {column_name} se v {dataset_name}, {důvod} neočekává.|


## <a name="error-0059"></a>Chyba 0059  
 K výjimce dojde, pokud nelze analyzovat index sloupce zadaný v ovládacím prvku pro výběr sloupce.  

 K této chybě v Azure Machine Learning dochází, pokud nelze analyzovat index sloupce při použití selektoru sloupců.  Tato chyba se zobrazí, pokud je index sloupce v neplatném formátu, který nelze analyzovat.  

**Řešení:** Upravte index sloupce tak, aby používal platnou hodnotu indexu.  

|Zprávy výjimek|
|------------------------|
|Jeden nebo více zadaných indexů sloupců nebo rozsahů indexu nelze analyzovat.|
|Index sloupce nebo rozsah "{column_index_or_range}" nelze analyzovat.|


## <a name="error-0060"></a>Chyba 0060  
 K výjimce dojde, pokud je v rámci výběru sloupce zadán rozsah sloupce mimo rozsah.  

 K této chybě v Azure Machine Learning dojde v případě, že je v selektoru sloupců uveden rozsah sloupce mimo rozsah. Tato chyba se zobrazí, pokud rozsah sloupců v rámci výběru sloupce neodpovídá sloupcům v datové sadě.  

**Řešení:** Upravte rozsah sloupců ve výběru sloupce tak, aby odpovídal sloupcům v datové sadě.  

|Zprávy výjimek|
|------------------------|
|Zadaný rozsah indexu sloupce je neplatný nebo mimo rozsah.|
|Rozsah sloupců {column_range} je neplatný nebo mimo rozsah.|


## <a name="error-0061"></a>Chyba 0061  
 Při pokusu o přidání řádku do objektu DataTable, který má jiný počet sloupců než tabulka, dojde k výjimce.  

 K této chybě v Azure Machine Learning dochází, když se pokusíte přidat řádek do datové sady, která má jiný počet sloupců než datová sada.  Tato chyba se zobrazí, pokud má řádek, který je přidán do datové sady, jiný počet sloupců ze vstupní datové sady.  Řádek nelze připojit k datové sadě, pokud je počet sloupců jiný.  

**Řešení:** Upravte vstupní datovou sadu tak, aby měla stejný počet sloupců jako přidaný řádek, nebo upravte přidaný řádek tak, aby měl stejný počet sloupců jako datová sada.  

|Zprávy výjimek|
|------------------------|
|Všechny tabulky musí mít stejný počet sloupců.|
|Sloupce v bloku "{chunk_id_1}" jsou jiné než blok "{chunk_id_2}" a velikost bloku: {chunk_size}.|
|Počet sloupců v souboru {filename_1} (Count = {column_count_1}) se liší od souboru {filename_2} (Count = {column_count_2}).|


## <a name="error-0062"></a>Chyba 0062  
 Při pokusu o porovnání dvou modelů s různými typy informací dojde k výjimce.  

 Tato chyba ve Azure Machine Learning je vytvořena, když nelze porovnat metriky vyhodnocení pro dvě různé datové sady s skóre. V tomto případě není možné porovnat efektivitu modelů používaných k vytváření dvou datových sad s skóre.  

**Řešení:** Ověřte, že výsledky skóre jsou vytvářeny stejným druhem modelu strojového učení (binární klasifikace, regrese, klasifikace více tříd, doporučení, clusteringu, detekce anomálií atd.). Všechny modely, které porovnáváte, musí mít stejný typ informací.  

|Zprávy výjimek|
|------------------------|
|Všechny modely musí mít stejný typ informací.|
|Byl obdržen nekompatibilní typ informací: {actual_learner_type}. Očekávané typy známek: "{expected_learner_type_list}".|


## <a name="error-0064"></a>Chyba 0064  
 K výjimce dojde, pokud je název účtu úložiště Azure nebo klíč úložiště nesprávně zadaný.  

 K této chybě v Azure Machine Learning dochází, pokud je název účtu úložiště Azure nebo klíč úložiště nesprávně zadaný. Tato chyba se zobrazí, pokud zadáte nesprávný název účtu nebo heslo pro účet úložiště. Tato situace může nastat, pokud ručně zadáte název účtu nebo heslo. Může k tomu dojít i v případě, že byl účet odstraněn.  

**Řešení:** Ověřte, že název účtu a heslo byly zadány správně a že účet existuje.  

|Zprávy výjimek|
|------------------------|
|Název účtu úložiště Azure nebo klíč úložiště nejsou správné.|
|Název účtu služby Azure Storage "{account_name}" nebo klíč úložiště pro název účtu není správný.|


## <a name="error-0065"></a>Chyba 0065  
 K výjimce dojde, pokud je název objektu blob Azure zadán nesprávně.  

 K této chybě v Azure Machine Learning dojde, pokud je název objektu BLOB v Azure zadán nesprávně.  Tato chyba se zobrazí, pokud:  

-   V zadaném kontejneru nelze najít objekt BLOB.  

 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->

-   V požadavku na [Import dat](import-data.md) byl jako zdroj zadán pouze kontejner, pokud byl formát Excel nebo CSV s kódováním. zřetězení obsahu všech objektů BLOB v kontejneru není u těchto formátů povoleno.  
  
-   Identifikátor URI SAS neobsahuje název platného objektu BLOB.  

**Řešení:** Znovu navštivte modul, který výjimku vyvolal. Ověřte, že zadaný objekt BLOB v kontejneru v účtu úložiště existuje a že tato oprávnění umožňují zobrazit objekt BLOB. Pokud máte Excel nebo CSV s formáty kódování, ověřte, jestli je vstup ve formátu **ContainerName/filename** . Ověřte, že identifikátor URI SAS obsahuje název platného objektu BLOB.  

|Zprávy výjimek|
|------------------------|
|Název objektu BLOB služby Azure Storage je nesprávný.|
|Název objektu BLOB služby Azure Storage {blob_name} je nesprávný.|
|Název objektu BLOB služby Azure Storage s předponou {blob_name_prefix} neexistuje.|
|Nepovedlo se najít žádné objekty blob služby Azure Storage v kontejneru {container_name}.|
|Nepovedlo se najít žádné objekty blob služby Azure Storage se zástupnou cestou {blob_wildcard_path}.|


## <a name="error-0066"></a>Chyba 0066  
 Pokud se prostředek nepovedlo nahrát do objektu blob Azure, dojde k výjimce.  

 K této chybě v Azure Machine Learning dochází, pokud se prostředek nepovedlo nahrát do objektu blob Azure.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Obě jsou uložené na stejný účet služby Azure Storage jako účet, který obsahuje vstupní soubor.  

**Řešení:** Znovu navštivte modul. Ověřte, že je název účtu Azure, klíč úložiště a kontejner správný a že účet má oprávnění k zápisu do kontejneru.  

|Zprávy výjimek|
|------------------------|
|Prostředek se nepovedlo nahrát do úložiště Azure.|
|Soubor {source_path} se nepovedlo nahrát do služby Azure Storage jako {dest_path}.|


## <a name="error-0067"></a>Chyba 0067  
 K výjimce dojde, pokud má datová sada jiný počet sloupců, než se očekávalo.  

 K této chybě v Azure Machine Learning dochází, pokud má datová sada jiný počet sloupců, než se očekávalo.  Tato chyba se zobrazí, když se počet sloupců v datové sadě liší od počtu sloupců, které modul během provádění očekává.  

**Řešení:** Upravte vstupní datovou sadu nebo parametry.  

|Zprávy výjimek|
|------------------------|
|V objektu DataTable je neočekávaný počet sloupců.|
|Neočekávaný počet sloupců v datové sadě {dataset_name}.|
|Očekával se tento počet sloupců: {expected_column_count}, ale místo toho se našel tento počet sloupců: {actual_column_count}.|
|Ve vstupní datové sadě {dataset_name} se očekávaly sloupce {expected_column_count}, ale místo toho se našel tento počet sloupců: {actual_column_count}.|


## <a name="error-0068"></a>Chyba 0068  
 K výjimce dojde, pokud není zadaný skript podregistru správný.  

 K této chybě v Azure Machine Learning dojde v případě, že se vyskytly chyby syntaxe ve skriptu QL podregistru, nebo pokud překladač podregistru při provádění dotazu nebo skriptu narazí na chybu.  

**Rozhodnutí**

Chybová zpráva z podregistru se obvykle hlásí zpět v protokolu chyb, takže můžete provést akci na základě konkrétní chyby. 

+ Otevřete modul a prozkoumejte dotaz na chyby.  
+ Ověřte, že dotaz funguje správně mimo Azure Machine Learning tím, že se přihlásí do konzoly podregistru clusteru Hadoop a spustí se dotaz.  
+ Zkuste umístit komentáře do skriptu v podregistru do samostatného řádku, a to na rozdíl v kombinování spustitelných příkazů a komentářů na jednom řádku.  

### <a name="resources"></a>Zdroje informací

Nápovědu k dotazům na podregistr pro strojové učení najdete v následujících článcích:

+ [Vytváření tabulek podregistru a načítání dat z Azure Blob Storage](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Prozkoumat data v tabulkách pomocí dotazů na podregistry](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Vytvoření funkcí pro data v clusteru Hadoop pomocí dotazů Hivu](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Podregistr pro uživatele SQL list tahák (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Zprávy výjimek|
|------------------------|
|Skript podregistru je nesprávný.|


## <a name="error-0069"></a>Chyba 0069  
 K výjimce dojde, pokud není zadaný skript SQL správný.  

 K této chybě v Azure Machine Learning dojde, pokud má zadaný skript SQL problémy se syntaxí nebo pokud sloupce nebo tabulka zadané ve skriptu nejsou platné. 

 Tato chyba se zobrazí, pokud modul SQL Engine při provádění dotazu nebo skriptu najde jakoukoli chybu. Chybová zpráva SQL se obvykle hlásí zpět v protokolu chyb, takže můžete provést akci na základě konkrétní chyby.  

**Řešení:** Přečtěte si modul a zkontrolujte chyby v dotazu SQL.  

 Přihlaste se k databázovému serveru přímo a spuštěním dotazu, abyste ověřili, že dotaz funguje správně mimo Azure ML.  

 Pokud dojde k vygenerování zprávy generované modulem SQL, proveďte akci na základě hlášené chyby. Chybové zprávy například někdy obsahují konkrétní pokyny o pravděpodobnou chybu:
+ *Neexistuje žádný takový sloupec nebo chybějící databáze*, což značí, že je možné, že jste zadali nesprávný název sloupce. Pokud jste si jisti, že je název sloupce správný, zkuste použít hranaté závorky nebo uvozovky k uzavření identifikátoru sloupce.
+ *Chyba logiky SQL \<SQL keyword\> poblíž *, což znamená, že před zadaným klíčovým slovem může být chyba syntaxe.

  
|Zprávy výjimek|
|------------------------|
|Skript SQL je nesprávný.|
|Dotaz SQL {sql_query} není správný.|
|Dotaz SQL {sql_query} není správný. Zpráva výjimky: {Exception}.|


## <a name="error-0070"></a>Chyba 0070  
 Při pokusu o přístup k neexistující tabulce Azure dojde k výjimce.  

 K této chybě v Azure Machine Learning dochází, když se pokusíte o přístup k neexistující tabulce Azure. Tato chyba se zobrazí, pokud zadáte tabulku ve službě Azure Storage, která neexistuje při čtení z nebo zápis do Azure Table Storage. K tomu může dojít, pokud chybné zadání názvu požadované tabulky nebo Neshoda mezi cílovým názvem a typem úložiště. Například jste chtěli číst z tabulky, ale místo toho jste zadali název objektu BLOB.  

**Řešení:** Znovu navštivte modul a ověřte, zda je název tabulky správný.  

|Zprávy výjimek|
|------------------------|
|Tabulka Azure neexistuje.|
|Tabulka Azure {table_name} neexistuje.|


## <a name="error-0072"></a>Chyba 0072  
 V případě časového limitu připojení dojde k výjimce.  

 K této chybě v Azure Machine Learning dochází, když dojde k vypršení časového limitu připojení. Tato chyba se zobrazí, pokud dojde k problémům s připojením ke zdroji dat nebo cíli, jako je například pomalé připojení k Internetu, nebo pokud je datová sada velká a/nebo dotaz SQL pro čtení dat provádí složité zpracování.  

**Řešení:** Zjistěte, jestli aktuálně dochází k problémům s pomalým připojením k úložišti Azure nebo k Internetu.  

|Zprávy výjimek|
|------------------------|
|Došlo k vypršení časového limitu připojení.|


## <a name="error-0073"></a>Chyba 0073  
 K výjimce dojde, pokud dojde k chybě při převodu sloupce na jiný typ.  

 K této chybě v Azure Machine Learning dochází, pokud není možné převést sloupec na jiný typ.  Tato chyba se zobrazí, pokud modul vyžaduje konkrétní typ a není možné převést sloupec na nový typ.  

**Řešení:** Upravte vstupní datovou sadu tak, aby sloupec bylo možné převést na základě vnitřní výjimky.  

|Zprávy výjimek|
|------------------------|
|Nepovedlo se převést sloupec.|
|Nepovedlo se převést sloupec na {target_type}.|


## <a name="error-0075"></a>Chyba 0075  
K výjimce dojde, pokud je při quantizing datové sady použita neplatná funkce binningu.  

K této chybě v Azure Machine Learning dochází při pokusu o data z přihrádky pomocí nepodporované metody nebo v případě, že kombinace parametrů nejsou platné.  

**Rozhodnutí**

Zpracování chyb pro tuto událost bylo představeno v dřívější verzi Azure Machine Learning, která umožňovala další přizpůsobení metod binningu. Všechny metody binningu jsou v současné době založené na výběru z rozevíracího seznamu, a proto by tato chyba neměla být možná.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Microsoft Q&A question page for Azure Machine Learning](https://docs.microsoft.com/answers/topics/azure-machine-learning-studio-classic.html), providing the data types, parameter settings, and the exact error message.  -->

|Zprávy výjimek|
|------------------------|
|Použila se neplatná funkce binningu.|


## <a name="error-0077"></a>Chyba 0077  
 K výjimce dojde v případě, že byl předán neznámý režim zapisování souborů BLOB.  

 K této chybě v Azure Machine Learning dojde, pokud je předán neplatný argument ve specifikacích pro cíl souboru objektu BLOB nebo zdroj.  

**Řešení:** Ve skoro všech modulech, které importují nebo exportují data do a z úložiště objektů BLOB v Azure, jsou hodnoty parametrů řídící režim zápisu přiřazené pomocí rozevíracího seznamu. Proto není možné předat neplatnou hodnotu a tato chyba by se neměla zobrazovat. Tato chyba bude v pozdější verzi zastaralá.  

|Zprávy výjimek|
|------------------------|
|Nepodporovaný režim zápisu objektů BLOB|
|Nepodporovaný režim zápisu objektu BLOB: {blob_write_mode}.|


## <a name="error-0078"></a>Chyba 0078  
 K výjimce dojde, pokud možnost HTTP pro [Import dat](import-data.md) obdrží stavový kód 3xx označující přesměrování.  

 K této chybě v Azure Machine Learning dochází, pokud možnost HTTP pro [Import dat](import-data.md) obdrží stavový kód 3xx (301, 302, 304 atd.) indikující přesměrování. Tato chyba se zobrazí, pokud se pokusíte připojit ke zdroji HTTP, který přesměruje prohlížeč na jinou stránku. Z bezpečnostních důvodů není možné přesměrovat weby jako zdroje dat pro Azure Machine Learning.  

**Řešení:** Pokud je web důvěryhodným webem, zadejte přesměrovanou adresu URL přímo.  

|Zprávy výjimek|
|------------------------|
|Přesměrování protokolu HTTP není povoleno.|


## <a name="error-0079"></a>Chyba 0079  
 K výjimce dojde, pokud je název kontejneru úložiště Azure zadán nesprávně.  

 K této chybě v Azure Machine Learning dochází, pokud je název kontejneru úložiště Azure zadaný nesprávně. Tato chyba se zobrazí, pokud jste při zápisu do Azure Blob Storage neurčili jak kontejner, tak i název objektu BLOB (soubor) s použitím **cesty k objektu BLOB** , který začíná na kontejneru.  

**Řešení:** Přečtěte si modul [Export dat](export-data.md) a ověřte, zda zadaná cesta k objektu BLOB obsahuje kontejner i název souboru ve formátu **kontejner/název**souboru.  

|Zprávy výjimek|
|------------------------|
|Název kontejneru úložiště Azure je nesprávný.|
|Název kontejneru úložiště Azure {container_name} je nesprávný. očekával se název kontejneru formátu nebo kontejneru objektů BLOB.|


## <a name="error-0080"></a>Chyba 0080  
 K výjimce dojde v případě, že v modulu není povolen sloupec se všemi hodnotami.  

 Tato chyba v Azure Machine Learning je vytvořena, když jeden nebo více sloupců spotřebovaných modulem obsahuje všechny chybějící hodnoty. Pokud například modul počítá souhrnnou statistiku pro každý sloupec, nemůže pracovat se sloupcem, který neobsahuje žádná data. V takových případech se spuštění modulu zastaví s touto výjimkou.  

**Řešení:** Znovu navštivte vstupní datovou sadu a odeberte všechny sloupce, které obsahují všechny chybějící hodnoty.  

|Zprávy výjimek|
|------------------------|
|Sloupce se všemi chybějícími hodnotami nejsou povoleny.|
|Sloupec {col_index_or_name} neobsahuje všechny hodnoty.|


## <a name="error-0081"></a>Chyba 0081  
 V modulu DPS dojde k výjimce, pokud se počet dimenzí, které se mají snížit, rovná počtu sloupců funkce ve vstupní datové sadě, který obsahuje alespoň jeden sloupec se zhuštěnými funkcemi.  

 Tato chyba ve Azure Machine Learning je vytvořena, pokud jsou splněny následující podmínky: (a) vstupní datová sada má alespoň jeden zhuštěný sloupec a (b) konečný počet požadovaných dimenzí je stejný jako počet vstupních dimenzí.  

**Řešení:** Zvažte snížení počtu rozměrů ve výstupu tak, aby bylo menší než počet rozměrů ve vstupu. To je obvyklé v aplikacích pro DPS.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->

|Zprávy výjimek|
|------------------------|
|Pro datovou sadu obsahující zhuštěné sloupce funkce počet rozměrů, které mají být zmenšeny na hodnotu by měl být menší než počet sloupců funkce.|


## <a name="error-0082"></a>Chyba 0082  
 K výjimce dojde v případě, že model nelze úspěšně deserializovat.  

 K této chybě v Azure Machine Learning dochází, když v důsledku zásadní změny nelze načíst model nebo transformaci uloženého strojového učení pomocí novější verze modulu runtime Azure Machine Learning.  

**Řešení:** Kanál pro školení, který vytvořil model nebo transformaci, musí být znovu spuštěn a model nebo transformace musí být znovu uloženy.  

|Zprávy výjimek|
|------------------------|
|Model nelze deserializovat, protože je pravděpodobně serializován se starším formátem serializace. Přečtěte si prosím model a znovu ho uložte.|


## <a name="error-0083"></a>Chyba 0083  
 K výjimce dojde, pokud datovou sadu použitou pro školení nelze použít pro konkrétní typ učení.  

 Tato chyba v Azure Machine Learning se vytvoří, když je datová sada nekompatibilní s výukovým nástrojem. Datová sada může například obsahovat alespoň jednu chybějící hodnotu v každém řádku a v důsledku toho by byla během školení přeskočena Celá datová sada. V ostatních případech některé algoritmy strojového učení, jako je detekce anomálií, neočekávají, že popisky jsou přítomné a můžou tuto výjimku vyvolat, pokud se v datové sadě nacházejí popisky.  

**Řešení:** Projděte si dokumentaci k nástroji, která se používá ke kontrole požadavků na vstupní datovou sadu. Zkontrolujte sloupce, abyste viděli, že jsou k dispozici všechny požadované sloupce.  

|Zprávy výjimek|
|------------------------|
|Datová sada použitá pro školení není platná.|
|{data_name} obsahuje neplatná data pro školení.|
|{data_name} obsahuje neplatná data pro školení. Typ učení: {learner_type}.|
|{data_name} obsahuje neplatná data pro školení. Typ učení: {learner_type}. Důvod: {důvod}|
|Nepovedlo se použít akci {action_name} u školicích dat {data_name}. Důvod: {důvod}|


## <a name="error-0084"></a>Chyba 0084  
 K výjimce dojde, když se vyhodnotí skóre vytvářené ze skriptu jazyka R. Toto není aktuálně podporováno.  

 K této chybě v Azure Machine Learning dochází, pokud se pokusíte použít jeden z modulů pro vyhodnocení modelu s výstupem z skriptu jazyka R, který obsahuje skóre.  

**Rozhodnutí**

|Zprávy výjimek|
|------------------------|
|Hodnocení skóre vytvořených vlastním modelem není aktuálně podporováno.|


## <a name="error-0085"></a>Chyba 0085  
 K výjimce dojde v případě, že vyhodnocení skriptu je neúspěšné s chybou.  

 K této chybě v Azure Machine Learning dochází, když spouštíte vlastní skript, který obsahuje syntaktické chyby.  

**Řešení:** Zkontrolujte svůj kód v externím editoru a zkontrolujte chyby.  

|Zprávy výjimek|
|------------------------|
|Při vyhodnocování skriptu došlo k chybě.|
|Při vyhodnocování skriptu došlo k následující chybě. Další informace najdete v protokolu výstupu:<br />----------Spuštění chybové zprávy z překladače {script_language}----------<br />Zpráva<br />----------Konec chybové zprávy z překladače {script_language}----------|


## <a name="error-0090"></a>Chyba 0090  
 K výjimce dojde, když se nepovede vytvořit tabulku podregistru.  

 K této chybě v Azure Machine Learning dochází, když používáte [exportovaná data](export-data.md) nebo jinou možnost k uložení dat do clusteru HDInsight a zadanou tabulku podregistru nelze vytvořit.  

**Řešení:** Zkontrolujte název účtu služby Azure Storage, který je přidružený ke clusteru, a ověřte, že ve vlastnostech modulu používáte stejný účet.  

|Zprávy výjimek|
|------------------------|
|Tabulku podregistru nešlo vytvořit. V případě clusteru HDInsight prosím zkontrolujte, že název účtu služby Azure Storage přidružený ke clusteru je stejný jako při předání parametru modulu.|
|Nepovedlo se vytvořit tabulku podregistru {table_name}. V případě clusteru HDInsight prosím zkontrolujte, že název účtu služby Azure Storage přidružený ke clusteru je stejný jako při předání parametru modulu.|
|Nepovedlo se vytvořit tabulku podregistru {table_name}. Pro cluster HDInsight Prosím zajistěte, aby byl název účtu úložiště Azure přidružený ke clusteru {cluster_name}.|


## <a name="error-0102"></a>Chyba 0102  
 Vyvoláno, když soubor ZIP nelze extrahovat.  

 K této chybě v Azure Machine Learning dochází při importu balíčku zip s příponou. zip, ale balíček není buď soubor zip, nebo soubor nepoužívá podporovaný formát zip.  

**Řešení:** Ujistěte se, že je vybraný soubor platný soubor zip a že byl komprimován pomocí některého z podporovaných kompresních algoritmů.  

 Pokud se tato chyba zobrazí při importu datových sad v komprimovaném formátu, ověřte, zda všechny obsažené soubory používají jeden z podporovaných formátů souborů a zda jsou ve formátu Unicode.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->

 Zkuste načíst požadované soubory do nové komprimované složky zip a zkuste znovu přidat vlastní modul.  

|Zprávy výjimek|
|------------------------|
|Daný soubor ZIP nemá správný formát.|


## <a name="error-0105"></a>Chyba 0105  
 Tato chyba se zobrazí, pokud soubor definice modulu obsahuje nepodporovaný typ parametru.  
  
 Tato chyba v Azure Machine Learning je vytvořena, když vytvoříte definici XML vlastního modulu a typ parametru nebo argumentu v definici neodpovídá podporovanému typu.  
  
**Řešení:** Ujistěte se, že vlastnost Type jakéhokoli prvku **arg** v souboru definice XML vlastního modulu je podporovaného typu.  
  
|Zprávy výjimek|  
|------------------------|  
|Nepodporovaný typ parametru.|  
|Byl zadán nepodporovaný typ parametru {0} .|  


## <a name="error-0107"></a>Chyba 0107  
 Vyvolá se, když soubor definice modulu definuje nepodporovaný typ výstupu.  
  
 Tato chyba ve Azure Machine Learning je vytvořena, když typ výstupního portu v definici XML vlastního modulu neodpovídá podporovanému typu.  
  
**Řešení:** Ujistěte se, že vlastnost Type elementu Output v souboru definice XML vlastního modulu je podporovaného typu.  
  
|Zprávy výjimek|  
|------------------------|  
|Nepodporovaný typ výstupu.|  
|Byl zadán nepodporovaný typ výstupu {output_type}.|  


## <a name="error-0125"></a>Chyba 0125  
 Vyvolána, když se schéma pro více datových sad neshoduje.  

**Rozhodnutí**

|Zprávy výjimek|
|------------------------|
|Schéma datové sady se neshoduje.|


## <a name="error-0127"></a>Chyba 0127  
 Velikost pixelu obrázku překračuje povolený limit.  

 K této chybě dochází, pokud načítáte obrázky z datové sady imagí pro klasifikaci a obrázky jsou větší, než může model zpracovat.  

 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  

-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->

|Zprávy výjimek|
|------------------------|
|Velikost pixelu obrázku překračuje povolený limit.|
|Velikost pixelu obrázku v souboru {file_path} překračuje povolený limit {size_limit}.|


## <a name="error-0128"></a>Chyba 0128  
 Počet podmíněných pravděpodobností pro sloupce kategorií překračuje limit.  

**Rozhodnutí**

|Zprávy výjimek|
|------------------------|
|Počet podmíněných pravděpodobností pro sloupce kategorií překračuje limit.|
|Počet podmíněných pravděpodobností pro sloupce kategorií překračuje limit. Dvojice sloupců {column_name_or_index_1} a {column_name_or_index_2} je problematická.|


## <a name="error-0129"></a>Chyba 0129  
 Počet sloupců v datové sadě překračuje povolený limit.  

**Rozhodnutí**

|Zprávy výjimek|
|------------------------|
|Počet sloupců v datové sadě překračuje povolený limit.|
|Počet sloupců v datové sadě v {dataset_name} překračuje povolenou hodnotu.|
|Počet sloupců v datové sadě v {dataset_name} překračuje povolený limit {component_name}.|
|Počet sloupců v datové sadě v {dataset_name} překračuje povolený limit {limit_columns_count} {component_name}.|


## <a name="error-0134"></a>Chyba 0134
K výjimce dojde v případě, že sloupec popisku chybí nebo má nedostatečný počet řádků s popiskem.  

K této chybě dochází, pokud modul vyžaduje sloupec popisku, ale nezahrnuli jste ho do výběru sloupce nebo sloupec popisku neobsahuje příliš mnoho hodnot.

K této chybě může dojít také v případě, že předchozí operace změní datovou sadu tak, že pro operaci pro příjem dat nejsou k dispozici dostatečné řádky. Předpokládejme například, že použijete výraz v **oddílu a vzorový** modul pro rozdělení datové sady podle hodnot. Pokud se pro váš výraz nenašly žádné shody, jedna z datových sad, která je výsledkem oddílu, by byla prázdná.

Řešení: 

 Pokud zahrnete sloupec popisku do výběru sloupce, ale není známý, použijte modul [Upravit metadata](edit-metadata.md) a označte ho jako sloupec popisku.

  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->
  Pak můžete pomocí modulu [Vyčištění chybějících dat](clean-missing-data.md) odebrat řádky s chybějícími hodnotami ve sloupci popisek. 

 Zkontrolujte vstupní datové sady, abyste měli jistotu, že obsahují platná data a dostatek řádků pro splnění požadavků operace. Mnoho algoritmů vygeneruje chybovou zprávu, pokud vyžaduje některé minimální číselné řádky dat, ale data obsahují pouze několik řádků nebo pouze záhlaví.

|Zprávy výjimek|
|------------------------|
|K výjimce dojde v případě, že sloupec popisku chybí nebo má nedostatečný počet řádků s popiskem.|
|K výjimce dojde, pokud sloupec popisku chybí nebo má méně než {required_rows_count} označených řádků.|
|K výjimce dojde, pokud sloupec popisku v datové sadě {dataset_name} chybí nebo má méně než {required_rows_count} označených řádků.|


## <a name="error-0138"></a>Chyba 0138  
 Paměť byla vyčerpána, nelze dokončit běh modulu. Převzorkování na datovou sadu může pomáhat s tím, jak problém vyřešit.  

 K této chybě dochází, pokud modul, který běží, vyžaduje více paměti, než je v kontejneru Azure k dispozici. K tomu může dojít, pokud pracujete s velkou datovou sadou a aktuální operace se nemůže vejít do paměti.  

**Řešení:** Pokud se pokoušíte přečíst velkou datovou sadu a operaci nelze dokončit, může to povést k převzorkování datové sady.  

  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  

 Sometimes transient loads can lead to this error. Machine support also changes over time. 

 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->

|Zprávy výjimek|
|------------------------|
|Paměť byla vyčerpána, nelze dokončit běh modulu.|
|Paměť byla vyčerpána, nelze dokončit běh modulu. Podrobnosti: {details}|


## <a name="error-0141"></a>Chyba 0141  
 K výjimce dojde, pokud počet vybraných číselných sloupců a jedinečné hodnoty ve sloupcích kategorií a řetězec je příliš malá.  

 K této chybě v Azure Machine Learning dochází, pokud ve vybraném sloupci není k provedení operace dostatek jedinečných hodnot.  

**Řešení:** Některé operace provádějí statistické operace se sloupci funkcí a kategorií a pokud není dostatek hodnot, operace může selhat nebo vracet neplatný výsledek. Zkontrolujte datovou sadu, abyste viděli, kolik hodnot je ve sloupcích funkce a popisek, a určete, jestli je operace, kterou se pokoušíte provést, statisticky platná.  

 Pokud je zdrojová datová sada platná, můžete také ověřit, zda některá z operací manipulace s nadřazenými daty nebo metadat změnila data a odstranila některé hodnoty.  

 Pokud nadřazené operace zahrnují rozdělení, vzorkování nebo převzorkování, ověřte, že výstupy obsahují očekávaný počet řádků a hodnot.  

|Zprávy výjimek|
|------------------------|
|Počet vybraných číselných sloupců a jedinečných hodnot ve sloupcích kategorií a řetězec je příliš malý.|
|Celkový počet vybraných číselných sloupců a jedinečných hodnot ve sloupcích kategorií a String (aktuálně {actual_num}) by měly být aspoň {lower_boundary}.|


## <a name="error-0154"></a>Chyba 0154  
 K výjimce dojde, když se uživatel pokusí připojit data ke klíčovým sloupcům s nekompatibilním typem sloupce.

|Zprávy výjimek|
|------------------------|
|Typy elementů klíčového sloupce nejsou kompatibilní.|
|Typy elementů klíčového sloupce nejsou kompatibilní. (zbývá: {keys_left}; Right: {keys_right})|


## <a name="error-0155"></a>Chyba 0155  
 K výjimce dojde v případě, že názvy sloupců datové sady nejsou řetězce.

|Zprávy výjimek|
|------------------------|
|Název sloupce datového rámce musí být typ String. Názvy sloupců nejsou řetězce.|
|Název sloupce datového rámce musí být typ String. Názvy sloupců {column_names} nejsou řetězcem.|


## <a name="error-0156"></a>Chyba 0156  
 Při čtení dat z Azure SQL Database došlo k výjimce.

|Zprávy výjimek|
|------------------------|
|Čtení dat z Azure SQL Database se nezdařilo.|
|Nepovedlo se přečíst data z Azure SQL Database: {detailed_message} DB: {database_server_name}: {database_name} dotaz: {sql_statement}|


## <a name="error-0157"></a>Chyba 0157  
 Úložiště dat nebylo nalezeno.

|Zprávy výjimek|
|------------------------|
|Informace o úložišti dat jsou neplatné.|
|Informace o úložišti dat jsou neplatné. Nepovedlo se získat úložiště dat AzureML {datastore_name} v pracovním prostoru {workspace_name}.|


## <a name="error-0158"></a>Chyba 0158
 Vyvoláno, když je transformační adresář neplatný.

|Zprávy výjimek|
|------------------------------------------------------------|
|Zadané TransformationDirectory je neplatné.|
|TransformationDirectory "{arg_name}" je neplatné. Důvod: {důvod} Spusťte prosím znovu školicí experiment, který generuje transformační soubor. Pokud se zkušební experiment odstranil, vytvořte a uložte prosím transformační soubor znovu.|
|TransformationDirectory "{arg_name}" je neplatné. Důvod: {důvod} {troubleshoot_hint}|


## <a name="error-0159"></a>Chyba 0159
 K výjimce dojde v případě, že adresář modelu modulu je neplatný. 

|Zprávy výjimek|
|------------------------------------------------------------|
|Zadané ModelDirectory je neplatné.|
|ModelDirectory "{arg_name}" je neplatné.|
|ModelDirectory "{arg_name}" je neplatné. Důvod: {důvod}|
|ModelDirectory "{arg_name}" je neplatné. Důvod: {důvod} {troubleshoot_hint}|


## <a name="error-1000"></a>Chyba 1000  
Interní výjimka knihovny  

Tato chyba je k dispozici pro zachycení v jiných nezpracovaných chybách interního motoru. Proto se příčina této chyby může lišit v závislosti na modulu, který chybu generoval.  

Pokud chcete získat další pomoc, doporučujeme, abyste si podrobnou zprávu doprovází tuto chybu do [fóra Azure Machine Learning](https://docs.microsoft.com/answers/topics/azure-machine-learning.html)společně s popisem scénáře, včetně dat, která se používají jako vstupy. Tato zpětná vazba nám pomůže určit prioritu chyb a zjistit nejdůležitější problémy pro další práci.  

|Zprávy výjimek|
|------------------------|
|Výjimka knihovny|
|Výjimka knihovny: {Exception}.|
|Neznámá výjimka knihovny: {Exception} {customer_support_guidance}.|

