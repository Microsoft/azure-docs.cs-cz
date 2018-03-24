---
title: Vytvořit vlastní R moduly v Azure Machine Learning | Microsoft Docs
description: Rychlý start pro vytváření vlastních modulů R v Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 6cbc628a-7e60-42ce-9f90-20aaea7ba630
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/29/2017
ms.openlocfilehash: 945c238411dac8f2e64666935308ef3c1cb2f0ab
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="author-custom-r-modules-in-azure-machine-learning"></a>Vytváření vlastních modulů R ve službě Azure Machine Learning
Toto téma popisuje, jak vytvořit a nasadit vlastní modul R v Azure Machine Learning. Vysvětluje, co jsou vlastních modulů R a jaké soubory se používají k definovat. Ukazuje, jak vytvořit soubory, které definují modul a jak registrace modulu pro nasazení v pracovním prostoru Machine Learning. Elementy a atributy používané v definici vlastní modul jsou pak popsány podrobněji. Postup použití pomocného funkce a soubory a několik výstupů také popsané. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="what-is-a-custom-r-module"></a>Co je vlastní modul R?
A **vlastní modul** je modul definovaný uživatelem, který může být nahrán do pracovního prostoru a provést v rámci experimentu Azure Machine Learning. A **vlastní modul R** je vlastní modul, který provede uživatelsky definované funkce R. **R** je programovací jazyk pro statistické výpočty a obrázky, které se často používá podle vědců statistikami a dat pro implementaci algoritmy. V současné době R je jediným podporovaným v vlastní moduly, ale podpory pro další jazyky je naplánována na budoucích verzích jazykem.

Vlastní moduly mají **prvotřídní stav** v Azure Machine Learning v tom smyslu, že je lze použít stejně jako ostatní moduly. Mohou být provedeny další moduly zahrnuté v publikované experimenty nebo vizualizace. Budete mít kontrolu nad algoritmus implementovaný modul, vstup a výstupních portů, který se má použít, modelování parametry a další různé chování za běhu. Experimentu, který obsahuje vlastní moduly můžete také publikovat do Galerie AI Azure pro snadné sdílení.

## <a name="files-in-a-custom-r-module"></a>Soubory ve vlastní modul R
Vlastní modul R je definováno soubor .zip, který obsahuje minimálně dva soubory:

* A **zdrojový soubor** , která implementuje funkce R vystavené modulu
* **Soubor definice XML** rozhraní vlastní modul, který popisuje

Další pomocné soubory můžou být součástí souboru .zip, který poskytuje funkce, která je přístupná z vlastní modul. Tato možnost je podrobněji **argumenty** část oddílu referenční **elementy v definičním souboru XML** následující ukázka rychlý start.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Příklad rychlý start: Definujte, balíčků a zaregistrovat vlastní modul R
Tento příklad ukazuje, jak sestavit soubory vyžadované vlastní modul R, zabalit do souboru zip a potom proveďte registraci modulu v pracovním prostoru Machine Learning. Příklad zip balíčku a ukázkové soubory si můžete stáhnout z [CustomAddRows.zip stáhnout soubor](http://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>Zdrojový soubor
Podívejte se na příklad z **řádky přidat vlastní** modul, který upravuje standardní implementace **přidat řádky** modul použít ke zřetězení řádky (připomínky) z dvě datové sady (datových rámců). Standardní **přidat řádky** modulu připojí řádky druhé vstupní datové sady na konec první použití vstupní datové sady `rbind` algoritmus. Vlastní `CustomAddRows` funkce podobně přijímá dvě datové sady, ale také přijme parametr Boolean odkládacího souboru jako další vstup. Pokud parametr odkládacího souboru je nastaven na **FALSE**, vrátí sadu dat jako standardní implementace. Ale pokud je parametr swap **TRUE**, funkce připojí řádky první vstupní datové sady na konec druhý datovou sadu místo. CustomAddRows.R soubor, který obsahuje implementaci r `CustomAddRows` funkce vystavené **řádky přidat vlastní** modul má následující kód R.

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) 
    {
        if (swap)
        {
            return (rbind(dataset2, dataset1));
        }
        else
        {
            return (rbind(dataset1, dataset2));
        } 
    } 

### <a name="the-xml-definition-file"></a>Soubor definice XML
To vystavit `CustomAddRows` funkce jako modul služby Azure Machine Learning, soubor definice XML musí být vytvořený zadat jak **řádky přidat vlastní** modul by měl vzhled a chování. 

    <!-- Defined a module using an R Script -->
    <Module name="Custom Add Rows">
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another. Dataset 2 is concatenated to Dataset 1 when Swap is FALSE, and vice versa when Swap is TRUE.</Description>

    <!-- Specify the base language, script file and R function to use for this module. -->        
        <Language name="R" 
         sourceFile="CustomAddRows.R" 
         entryPoint="CustomAddRows" />  

    <!-- Define module input and output ports -->
    <!-- Note: The values of the id attributes in the Input and Arg elements must match the parameter names in the R Function CustomAddRows defined in CustomAddRows.R. -->
        <Ports>
            <Input id="dataset1" name="Dataset 1" type="DataTable">
                <Description>First input dataset</Description>
            </Input>
            <Input id="dataset2" name="Dataset 2" type="DataTable">
                <Description>Second input dataset</Description>
            </Input>
            <Output id="dataset" name="Dataset" type="DataTable">
                <Description>The combined dataset</Description>
            </Output>
        </Ports>

    <!-- Define module parameters -->
        <Arguments>
            <Arg id="swap" name="Swap" type="bool" >
                <Description>Swap input datasets.</Description>
            </Arg>
        </Arguments>
    </Module>


Je důležité si uvědomit, že hodnota **id** atributy **vstup** a **Arg** elementů v souboru XML musí odpovídat názvům parametr funkce kód R CustomAddRows.R souboru přesně: (*dataset1*, *dataset2*, a *swap* v příkladu). Podobně, hodnota **entryPoint** atribut **jazyk** element musí přesně odpovídat názvu funkce ve skriptu jazyka R: (*CustomAddRows* v příkladu) . 

Naproti tomu **id** atribut pro **výstup** element neodpovídá žádné proměnné ve skriptu R. Pokud je vyžadován více než jeden výstup, jednoduše vrácení seznamu z funkce R s výsledky umístit *ve stejném pořadí* jako **výstupy** elementy jsou deklarované v souboru XML.

### <a name="package-and-register-the-module"></a>Balíček a registrace modulu
Uložit tyto dva soubory jako *CustomAddRows.R* a *CustomAddRows.xml* a pak zip společně do dvou souborech *CustomAddRows.zip* souboru.

Chcete-li zaregistrovat je v pracovním prostoru Machine Learning, přejděte do pracovního prostoru v nástroji Machine Learning Studio, klikněte na **+ nový** tlačítko v dolní a zvolte **modulu -> z ZIP balíčku** nahrát nový **Řádky přidat vlastní** modulu.

![Nahrát Zip](./media/custom-r-modules/upload-from-zip-package.png)

**Řádky přidat vlastní** modulu je nyní připravena ke kterým přistupují experimentů Machine Learning.

## <a name="elements-in-the-xml-definition-file"></a>Elementy v definičním souboru XML
### <a name="module-elements"></a>Modul elementy
**Modulu** element se používá k definování vlastní modul v souboru XML. Více modulů lze definovat v jednom souboru XML pomocí několika **modulu** elementy. Každý modulu v pracovním prostoru musí mít jedinečný název. Registrovat vlastní modul se stejným názvem jako stávající vlastní modul a nahrazuje existující modul s tímto novým připojením. Vlastní moduly lze však zaregistrována stejný název jako existující modul Azure Machine Learning. Pokud ano, se objeví v **vlastní** kategorii palety modulů.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


V rámci **modulu** elementu, můžete určit dva další volitelné prvky:

* **vlastníka** element, který se vloží do modulu  
* **popis** element, který obsahuje text, který se zobrazí v rychlé nápovědy pro modul a pokud ponecháte modul v uživatelském rozhraní Machine Learning.

Pravidla pro omezení znaků v elementech modul:

* Hodnota **název** atribut **modulu** element nesmí být delší než 64 znaků. 
* Obsah **popis** element nesmí být delší než 128 znaků.
* Obsah **vlastníka** element nesmí být delší než 32 znaků.

Výsledky modulu může být deterministický nebo nondeterministic.* * ve výchozím nastavení, všechny moduly se považují za deterministický. To znamená, danou neměnné sadu vstupní parametry a data, modul by měl vrátit stejné výsledky eacRAND nebo functionh čas, který je spuštěn. Vzhledem k tomuto chování, Azure Machine Learning Studio pouze opakovaně moduly, které jsou označeny jako deterministický, pokud parametr nebo vstupní data změnila. Vrací výsledky uložené v mezipaměti také poskytuje mnohem rychlejší provádění experimenty.

Jsou funkce, které jsou deterministický, jako je například rand – nebo funkci, která vrátí aktuální datum nebo čas. Pokud modul používá nedeterministická funkce, můžete určit, že modul bude Nedeterministický nastavením nepovinný **isDeterministic** atribut **FALSE**. To zajistí, že modul se znovu spustí při každém spuštění experimentu, i pokud nedošlo ke změně modulu vstup a parametry. 

### <a name="language-definition"></a>Definice jazyka
**Jazyk** element v souboru definice XML slouží k určení vlastní modul jazyk. V současné době R je jediný podporovaný jazyk. Hodnota **zdrojový soubor** atribut musí být název souboru R, který obsahuje funkce, které má být volána při spuštění modulu. Tento soubor musí být součástí balíček zip. Hodnota **entryPoint** atribut je název funkce volané a platný funkci definovanou s ve zdrojovém souboru se musí shodovat.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>Porty
Vstupní a výstupní porty pro vlastní modul jsou určené v podřízených elementů **porty** část definičního soubor XML. Pořadí těchto prvků určuje rozložení zkušeného (UX) uživatelé. Prvním podřízeným objektem **vstupní** nebo **výstup** uvedené v **porty** vstupní port nejvíce vlevo v UX Learning počítač se změní na element souboru XML
Každý vstupní a výstupní port je pravděpodobně volitelný **popis** podřízený element, který určuje text, který zobrazí, když najedete myší portu v uživatelském rozhraní Machine Learning.

**Porty pravidla**:

* Maximální počet **vstupní a výstupní porty** je 8 pro každou.

### <a name="input-elements"></a>Elementy vstupu
Vstupní porty umožňují předat data do funkce R a pracovního prostoru. **Datové typy** jsou podporovány pro vstupní porty jsou následujícím způsobem: 

**DataTable:** tento typ je předán funkce R jako data.frame. Ve skutečnosti všechny typy (například CSV soubory nebo soubory ARFF), které jsou podporovány nástrojem Machine Learning a že jsou kompatibilní s **DataTable** se převedou na data.frame automaticky. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

**Id** atribut spojený s každou **DataTable** vstupní port musí mít jedinečnou hodnotu a tato hodnota musí odpovídat jeho odpovídající parametr v R funkce s názvem.
Volitelné **DataTable** porty, které nejsou předány jako vstup v experimentu mají hodnotu **NULL** předaný funkci R a volitelné zip porty jsou ignorovány, pokud vstup není připojen. **IsOptional** atribut je volitelný pro oba **DataTable** a **Zip** typy a je *false* ve výchozím nastavení.

**ZIP:** vlastní moduly může přijmout soubor zip jako vstup. Tento vstup je vybaleno do R pracovní adresář vaší – funkce

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

Pro vlastních modulů R tak, aby odpovídaly žádné parametry funkce R nemá id pro Zip port. Je to proto, že je soubor zip automaticky extrahovány do R pracovní adresář.

**Vstupní pravidla:**

* Hodnota **id** atribut **vstup** element musí být platný název proměnné R.
* Hodnota **id** atribut **vstup** element nesmí být delší než 64 znaků.
* Hodnota **název** atribut **vstup** element nesmí být delší než 64 znaků.
* Obsah **popis** element nesmí být delší než 128 znaků.
* Hodnota **typ** atribut **vstup** element musí být *Zip* nebo *DataTable*.
* Hodnota **isOptional** atribut **vstup** element není požadovaná (a je *false* ve výchozím nastavení není-li zadána); ale pokud je zadán, musí být *true* nebo *false*.

### <a name="output-elements"></a>Výstup elementy
**Standardní výstupní porty:** výstupních portů jsou namapované na vrácené hodnoty z funkce R, který můžete použít následující moduly. *DataTable* je typ pouze standardní výstupní port v současné době podporován. (Podpora pro *inteligentních algoritmů* a *transformuje* je chystaný.) A *DataTable* výstup je definován jako:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

Pro výstupy ve vlastních modulů R, hodnota **id** atribut nemá tak, aby odpovídaly s nic v R skript, ale musí být jedinečný. Pro jeden modul výstupu, musí být návratovou hodnotou z funkce R *data.frame*. Chcete-li více než jeden objekt podporovaného typu výstupu odpovídající výstupních portů potřeba zadat v definičním souboru XML a objekty musí být vrácen jako seznam. Objektů výstupního jsou přiřazeny k vypsání porty zleva doprava, která znázorňuje pořadí, ve kterém jsou objekty umístěn ve vráceném seznamu.

Například, pokud chcete upravit **řádky přidat vlastní** modulu výstup původní dvě datové sady, *dataset1* a *dataset2*, kromě nového připojené k datové sadě, *datovou sadu*, (v pořadí, zleva doprava, jako: *datovou sadu*, *dataset1*, *dataset2*), pak zadejte výstupních portů v CustomAddRows.xml souboru následujícím způsobem:

    <Ports> 
        <Output id="dataset" name="Dataset Out" type="DataTable"> 
            <Description>New Dataset</Description> 
        </Output> 
        <Output id="dataset1_out" name="Dataset 1 Out" type="DataTable"> 
            <Description>First Dataset</Description> 
        </Output> 
        <Output id="dataset2_out" name="Dataset 2 Out" type="DataTable"> 
            <Description>Second Dataset</Description> 
        </Output> 
        <Input id="dataset1" name="Dataset 1" type="DataTable"> 
            <Description>First Input Table</Description>
        </Input> 
        <Input id="dataset2" name="Dataset 2" type="DataTable"> 
            <Description>Second Input Table</Description> 
        </Input> 
    </Ports> 


A vrátí seznam objektů v seznamu ve správném pořadí v 'CustomAddRows.R':

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 

**Vizualizace výstup:** můžete také určit na výstupní port typu *vizualizace*, zobrazuje výstup z výstupu R grafiky zařízení a konzoly. Tento port není součástí výstup funkce R a nebudou v konfliktu s pořadí ostatních typů výstupní port. Pokud chcete přidat do vlastní moduly vizualizace port, přidejte **výstup** element s hodnotou *vizualizace* pro jeho **typ** atribut:

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**Výstup pravidla:**

* Hodnota **id** atribut **výstup** element musí být platný název proměnné R.
* Hodnota **id** atribut **výstup** element nesmí být delší než 32 znaků.
* Hodnota **název** atribut **výstup** element nesmí být delší než 64 znaků.
* Hodnota **typ** atribut **výstup** element musí být *vizualizace*.

### <a name="arguments"></a>Argumenty
Další data mohou být předána do funkce R prostřednictvím modulu parametry, které jsou definovány v **argumenty** elementu. Tyto parametry se zobrazí v podokně nejvíce vpravo vlastnosti UI Machine Learning, pokud je vybrána modul. Argumenty může být jakýkoli z podporovaných typů nebo můžete vytvořit vlastní výčet v případě potřeby. Podobně jako **porty** elementů **argumenty** elementy může mít volitelný **popis** element, který určuje text, který se zobrazí, když ukazatel myši Název parametru.
Volitelné vlastnosti pro modul, jako je například výchozí hodnota, minValue a maxValue lze přidat na některý argument jako atributy, které mají **vlastnosti** elementu. Platný vlastnosti **vlastnosti** element závisí na typ argumentu a jsou popsány s typy argumentů podporované v další části. Argumenty s **isOptional** vlastnost nastavena na hodnotu **"true"** nevyžadují, aby uživatel zadal hodnotu. Pokud není zadána hodnota pro argument, není argument předaný funkci vstupního bodu. Argumenty funkce vstupního bodu, které jsou volitelné nutné explicitně zpracovávat funkce, například přiřadit výchozí hodnotu NULL v definici funkce vstupního bodu. Nepovinný argument pouze vynutí jiných argument omezení, tj. min nebo max, pokud je hodnota zadaná uživatelem.
Stejně jako u vstupy a výstupy, je velmi důležité, že všechny parametry mají hodnoty jedinečné id s nimi spojených. V našem příkladu úvodní přidružené id a parametru se *swap*.

### <a name="arg-element"></a>Arg – element
Parametr modulu je definován pomocí **Arg** podřízený element **argumenty** část definičního soubor XML. Stejně jako u podřízených elementů v **porty** části řazení parametry v **argumenty** oddíl definuje rozložení v UX Parametry zobrazí shora dolů v uživatelském rozhraní ve stejném pořadí, ve kterém jsou definovány v souboru XML. Typy podporované nástrojem Machine Learning pro parametry jsou zde uvedeny. 

**int** – parametru typu (32 bitů) celé číslo.

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *Volitelné vlastnosti*: **min**, **maximální**, **výchozí** a **isOptional**

**dvojité** – parametr typu double.

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* *Volitelné vlastnosti*: **min**, **maximální**, **výchozí** a **isOptional**

**BOOL** – logického parametru, která je reprezentována zaškrtávací políčko UX

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* *Volitelné vlastnosti*: **výchozí** -false v případě není nastavena.

**řetězec**: standardní řetězec

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* *Volitelné vlastnosti*: **výchozí** a **isOptional**

**ColumnPicker**: Parametr výběr sloupce. Tento typ se vykreslí v uživatelského jako zvolit sloupce. **Vlastnost** element zde slouží k určení id portu, ze které se vybírají sloupce, které musí být cílový port typ *DataTable*. Výsledek výběr sloupce je předaný funkci R jako seznam řetězců obsahujících názvy vybraných sloupců. 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *Požadované vlastnosti*: **portId** -odpovídá id elementu vstupu s typem *DataTable*.
* *Volitelné vlastnosti*:
  
  * **allowedTypes** -filtry typy sloupci, ze kterého můžete vybrat. Platné hodnoty patří: 
    
    * číselné
    * Logická hodnota
    * Kategorické
    * Řetězec
    * Štítek
    * Funkce
    * Skóre
    * Vše
  * **výchozí** -platný výchozí možnosti pro výběr sloupce patří: 
    
    * Žádný
    * NumericFeature
    * NumericLabel
    * NumericScore
    * NumericAll
    * BooleanFeature
    * BooleanLabel
    * BooleanScore
    * BooleanAll
    * CategoricalFeature
    * CategoricalLabel
    * CategoricalScore
    * CategoricalAll
    * StringFeature
    * StringLabel
    * StringScore
    * StringAll
    * AllLabel
    * AllFeature
    * AllScore
    * Vše

**Rozevírací seznam**: seznam uživatelská výčtového (rozevírací). Rozevírací seznam položek, které jsou určené v rámci **vlastnosti** pomocí elementu **položky** elementu. **Id** pro každou **položky** musí být jedinečný a platná proměnná R. Hodnota **název** z **položky** slouží jako text, který se zobrazí a hodnotu, která je předaný funkci R.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* *Volitelné vlastnosti*:
  * **výchozí** -hodnotu pro vlastnost výchozí, musí být shodná s hodnotou id z jednoho z **položky** elementy.

### <a name="auxiliary-files"></a>Pomocné soubory
Všechny soubory, které je umístěn v souboru ZIP vlastní modul bude k dispozici pro použití v době spuštění. Všechny adresáře struktury přítomen se zachovají. To znamená, že soubor sourcing funguje stejné místně a při provádění Azure Machine Learning. 

> [!NOTE]
> Všimněte si, že všechny soubory extrahují do adresáře, src, proto by měly mít všechny cesty ' src /' předponu.
> 
> 

Řekněme například, že chcete odebrat všechny řádky s NAs z datové sady a také odebrat všechny duplicitní řádky před výstup do CustomAddRows a jste již zapsány R funkce, která nemá v souboru RemoveDupNARows.R:

    RemoveDupNARows <- function(dataFrame) {
        #Remove Duplicate Rows:
        dataFrame <- unique(dataFrame)
        #Remove Rows with NAs:
        finalDataFrame <- dataFrame[complete.cases(dataFrame),]
        return(finalDataFrame)
    }
Zdrojem může být pomocného souboru RemoveDupNARows.R ve funkci CustomAddRows:

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) {
        source("src/RemoveDupNARows.R")
            if (swap) { 
                dataset <- rbind(dataset2, dataset1))
             } else { 
                  dataset <- rbind(dataset1, dataset2)) 
             } 
        dataset <- removeDupNARows(dataset)
        return (dataset)
    }

V dalším kroku nahrajte soubor zip obsahující 'CustomAddRows.R', 'CustomAddRows.xml' a 'RemoveDupNARows.R' jako vlastní modul R.

## <a name="execution-environment"></a>Prostředí pro spuštění
Prostředí pro spuštění skriptu R používá stejnou verzi nástroje R jako **spustit skript jazyka R** modulu a můžete použít stejné výchozí balíčky. Můžete také přidat další balíčky R pro vlastní modul zahrnutím v balíčku zip vlastní modul. Jenom je načte ve vašem skriptu R stejně jako v prostředí R. 

**Omezení prostředí pro spuštění** zahrnují:

* Systém souborů bez trvalé: soubory zapsané při spuštění vlastního modulu nejsou trvalé napříč více spustí stejný modulu.
* Žádný přístup k síti

