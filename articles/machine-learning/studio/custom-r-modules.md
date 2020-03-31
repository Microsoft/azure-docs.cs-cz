---
title: Definování vlastních modulů R
titleSuffix: ML Studio (classic) - Azure
description: Toto téma popisuje, jak vytvořit a nasadit vlastní R Studio (klasické). Vysvětluje, co jsou vlastní moduly R a jaké soubory se používají k jejich definování.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 5b8dab14a9416795eccef1f71988a048c8bedb48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218167"
---
# <a name="define-custom-r-modules-for-azure-machine-learning-studio-classic"></a>Definování vlastních modulů R pro Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Toto téma popisuje, jak vytvořit a nasadit vlastní R Studio (klasické). Vysvětluje, co jsou vlastní moduly R a jaké soubory se používají k jejich definování. Ukazuje, jak vytvořit soubory, které definují modul a jak zaregistrovat modul pro nasazení v pracovním prostoru Machine Learning. Prvky a atributy použité v definici vlastního modulu jsou pak podrobněji popsány. Jak používat pomocné funkce a soubory a více výstupů je také diskutována. 



## <a name="what-is-a-custom-r-module"></a>Co je vlastní modul R?
**Vlastní modul** je uživatelem definovaný modul, který lze nahrát do pracovního prostoru a spustit jako součást experimentu Azure Machine Learning Studio (klasické). **Vlastní modul R** je vlastní modul, který spouští uživatelem definovanou funkci R. **R** je programovací jazyk pro statistické výpočty a grafiku, který je široce používán statistiky a datovými vědci pro implementaci algoritmů. V současné době R je jediný jazyk podporovaný ve vlastních modulech, ale podpora pro další jazyky je naplánována pro budoucí verze.

Vlastní moduly mají **prvotřídní stav** v Azure Machine Learning Studio (klasické) v tom smyslu, že je lze použít stejně jako jakýkoli jiný modul. Mohou být prováděny s jinými moduly, které jsou součástí publikovaných experimentů nebo ve vizualizacích. Máte kontrolu nad algoritmus implementovaný modulem, vstupní a výstupní porty, které mají být použity, parametry modelování a další různé chování modulu runtime. Experiment, který obsahuje vlastní moduly, lze také publikovat v galerii Azure AI pro snadné sdílení.

## <a name="files-in-a-custom-r-module"></a>Soubory ve vlastním modulu R
Vlastní modul R je definován souborem ZIP, který obsahuje minimálně dva soubory:

* **Zdrojový soubor,** který implementuje funkci R vystavenou modulem
* **Definiční soubor XML,** který popisuje vlastní rozhraní modulu

Další pomocné soubory mohou být také zahrnuty do souboru ZIP, který poskytuje funkce, které jsou přístupné z vlastního modulu. Tato možnost je popsána v části **Argumenty** v referenční části **Elementy v souboru definice XML** v následujícím příkladu rychlého startu.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Příklad rychlého startu: definování, balíček a registrace vlastního modulu R
Tento příklad ukazuje, jak vytvořit soubory vyžadované vlastní modul R, zabalit je do souboru zip a potom zaregistrovat modul v pracovním prostoru Machine Learning. Příklad zip balíček a ukázkové soubory lze stáhnout z [download CustomAddRows.zip souboru](https://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>Zdrojový soubor
Vezměme si příklad modulu **Vlastní přidat řádky,** který upravuje standardní implementaci modulu **Přidat řádky,** který slouží ke zřetězení řádků (pozorování) ze dvou datových sad (datových rámců). Standardní modul **Přidat řádky** připojí řádky druhé vstupní datové sady na konec první `rbind` vstupní datové sady pomocí algoritmu. Přizpůsobená `CustomAddRows` funkce podobně přijímá dvě datové sady, ale také přijímá logický parametr prohození jako další vstup. Pokud je parametr prohození nastaven na **hodnotu NEPRAVDA**, vrátí stejnou sadu dat jako standardní implementace. Pokud je však parametr **prohození TRUE**, funkce místo toho připojí řádky první vstupní datové sady na konec druhé datové sady. Soubor CustomAddRows.R, který obsahuje implementaci `CustomAddRows` funkce R vystavené modulem **Vlastní přidat řádky,** má následující kód R.

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
Chcete-li `CustomAddRows` vystavit tuto funkci jako modul Azure Machine Learning Studio (klasické), musí být vytvořen soubor definice XML určit, jak by měl vypadat a chovat **se modul Vlastní přidat řádky.** 

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


Je důležité si uvědomit, že hodnota **id** atributy **Input** a **Arg** prvky v souboru XML musí odpovídat názvy parametrů funkce kódu R v souboru CustomAddRows.R přesně: (*dataset1*, *dataset2*, a *swap* v příkladu). Podobně hodnota **entryPoint** atribut **Language** element musí odpovídat název funkce ve skriptu R přesně: (*CustomAddRows* v příkladu). 

Naproti tomu atribut **id** pro element **Výstup** neodpovídá žádné proměnné ve skriptu R. Pokud je požadováno více než jeden výstup, jednoduše vraťte seznam z funkce R s výsledky umístěnými *ve stejném pořadí* jako **výstupy,** které jsou deklarovány v souboru XML.

### <a name="package-and-register-the-module"></a>Zabalte a zaregistrujte modul
Uložte tyto dva soubory jako *CustomAddRows.R* a *CustomAddRows.xml* a potom oba soubory sklopte do souboru *CustomAddRows.zip.*

Pokud je chcete zaregistrovat v pracovním prostoru Machine Learning, přejděte do svého pracovního prostoru ve službě Azure Machine Learning Studio (klasická), klikněte na tlačítko **+NEW** v dolní části a zvolte **MODUL -> ZE ZIP BALÍČEK** PRO NAHRÁNÍ nového modulu **Vlastní přidat řádky.**

![Nahrát zip](./media/custom-r-modules/upload-from-zip-package.png)

Modul **Vlastní přidat řádky** je nyní připraven k přístupu experimenty machine learningu.

## <a name="elements-in-the-xml-definition-file"></a>Elementy v definičním souboru XML
### <a name="module-elements"></a>Prvky modulu
Prvek **Module** se používá k definování vlastního modulu v souboru XML. Více modulů lze definovat v jednom souboru XML pomocí více prvků **modulu.** Každý modul v pracovním prostoru musí mít jedinečný název. Zaregistrujte vlastní modul se stejným názvem jako existující vlastní modul a nahradí existující modul novým. Vlastní moduly však mohou být registrovány se stejným názvem jako existující modul Azure Machine Learning Studio (klasický). Pokud ano, zobrazí se v kategorii **Vlastní** palety modulů.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


V rámci prvku **Module** můžete zadat dva další volitelné prvky:

* Prvek **Vlastník,** který je vložen do modulu  
* a **Description** element, který obsahuje text, který je zobrazen v rychlé nápovědě pro modul a při najetí nad modulem v machine learning uI.

Omezení pravidel pro znaky v prvcích modulu:

* Hodnota atributu **name** v elementu **Module** nesmí přesáhnout délku 64 znaků. 
* Obsah **Description** element nesmí překročit 128 znaků na délku.
* Obsah **Owner** element nesmí překročit 32 znaků na délku.

Výsledky modulu mohou být deterministické nebo nedeterministické.** Ve výchozím nastavení jsou všechny moduly považovány za deterministické. To znamená, že vzhledem k neměnné sadě vstupních parametrů a dat by modul měl vrátit stejné výsledky eacRAND nebo čas funkce, který je spuštěn. Vzhledem k tomuto chování Azure Machine Learning Studio (klasické) pouze reruns moduly označené jako deterministický, pokud parametr nebo vstupní data změnila. Vrácení výsledků uložených v mezipaměti také poskytuje mnohem rychlejší provádění experimentů.

Existují funkce, které jsou nedeterministické, například NÁHČÍSLO nebo funkce, která vrací aktuální datum nebo čas. Pokud modul používá nedeterministickou funkci, můžete určit, že modul je nedeterministický, nastavením volitelného atributu **isDeterministic** na **HODNOTU NEPRAVDA**. To zajišťuje, že modul je znovu spustit při každém spuštění experimentu, i v případě, že vstup modulu a parametry se nezměnily. 

### <a name="language-definition"></a>Definice jazyka
Element **Language** v definičním souboru XML se používá k určení vlastního jazyka modulu. V současné době je R jediným podporovaným jazykem. Hodnota atributu **sourceFile** musí být název souboru R, který obsahuje funkci pro volání při spuštění modulu. Tento soubor musí být součástí balíčku zip. Hodnota atributu **entryPoint** je název volané funkce a musí odpovídat platné funkci definované ve zdrojovém souboru.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>Porty
Vstupní a výstupní porty pro vlastní modul jsou určeny v podřízených prvcích oddílu **Porty** definičního souboru XML. Pořadí těchto prvků určuje rozložení zkušený (UX) uživateli. První podřízený **vstup** nebo **výstup** uvedený v **ports** elementu souboru XML se stane vstupním portem zcela vlevo v uživatelském rozhraní Machine Learning.
Každý vstupní a výstupní port může mít volitelný podřízený prvek **Description,** který určuje text zobrazený při najetí kurzorem myši nad port v rozhraní Machine Learning.

**Pravidla přístavů**:

* Maximální počet **vstupních a výstupních portů** je 8 pro každý.

### <a name="input-elements"></a>Vstupní prvky
Vstupní porty umožňují předávat data do funkce R a pracovního prostoru. Datové **typy,** které jsou podporovány pro vstupní porty jsou následující: 

**DataTable:** Tento typ je předán funkci R jako data.frame. Ve skutečnosti všechny typy (například soubory CSV nebo SOUBORY ARFF), které jsou podporovány Machine Learning a které jsou kompatibilní s **DataTable** jsou převedeny na data.frame automaticky. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

Atribut **id** přidružený ke každému vstupnímu portu **DataTable** musí mít jedinečnou hodnotu a tato hodnota musí odpovídat odpovídajícímu pojmenovanému parametru ve funkci R.
Volitelné **DataTable** porty, které nejsou předány jako vstup v experimentu mají hodnotu **NULL** předána funkci R a volitelné zip porty jsou ignorovány, pokud vstup není připojen. Atribut **isOptional** je volitelný pro typy **DataTable** i **Zip** a ve výchozím nastavení je *nepravdivý.*

**Zip:** Vlastní moduly mohou jako vstup přijmout soubor ZIP. Tento vstup je rozbalen do pracovního adresáře R vaší funkce

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

Pro vlastní moduly R ID pro port ZIP nemusí odpovídat žádné parametry funkce R. Důvodem je, že soubor zip je automaticky extrahován do pracovního adresáře R.

**Vstupní pravidla:**

* Hodnota atributu **id** **vstupního** prvku musí být platný název proměnné R.
* Hodnota atributu **id** **vstupního** prvku nesmí být delší než 64 znaků.
* Hodnota atributu **name** elementu **Input** nesmí být delší než 64 znaků.
* Obsah **Description** element nesmí být delší než 128 znaků
* Hodnota atributu **type** **elementu Input** musí být *Zip* nebo *DataTable*.
* Hodnota **isOptional** atribut **Input** element není vyžadována (a je *false* ve výchozím nastavení, pokud není zadán); ale pokud je zadán, musí být *pravdivé* nebo *nepravdivé*.

### <a name="output-elements"></a>Výstupní prvky
**Standardní výstupní porty:** Výstupní porty jsou mapovány na vrácené hodnoty z funkce R, které pak mohou být použity následujícími moduly. *DataTable* je pouze standardní typ výstupního portu, který je aktuálně podporován. (Podpora pro *studenty* a *transformace* je na obzoru.) Výstup *DataTable* je definován jako:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

Pro výstupy ve vlastních modulech R hodnota atributu **id** nemusí odpovídat ničemu ve skriptu R, ale musí být jedinečná. Pro výstup jednoho modulu musí být vrácená hodnota z funkce R *data.frame*. Aby bylo možné vytvořit více než jeden objekt podporovaného datového typu, je třeba zadat příslušné výstupní porty v souboru definice XML a objekty musí být vráceny jako seznam. Výstupní objekty jsou přiřazeny k výstupním portům zleva doprava, což odráží pořadí, ve kterém jsou objekty umístěny v vráceném seznamu.

Chcete-li například upravit modul **Vlastní přidat řádky** tak, aby výstup původní ch dva datové sady, *dataset1* a *dataset2*, kromě nové spojené datové sady, *datové sady*, (v pořadí zleva doprava, jako: *datová sada*, *datová sada1*, *dataset2*), pak definovat výstupní porty v souboru CustomAddRows.xml takto:

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


A vrátit seznam objektů v seznamu ve správném pořadí v 'CustomAddRows.R':

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 

**Vizualizační výstup:** Můžete také určit výstupní port typu *Vizualizace*, který zobrazuje výstup z grafického zařízení R a výstupu konzoly. Tento port není součástí výstupu funkce R a nenarušuje pořadí ostatních typů výstupních portů. Chcete-li přidat vizualizační port do vlastních modulů, přidejte element **Výstup** s hodnotou *Vizualizace* pro atribut **typu:**

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**Výstupní pravidla:**

* Hodnota atributu **id** elementu **Output** musí být platný název proměnné R.
* Hodnota atributu **id** elementu **Výstup** nesmí být delší než 32 znaků.
* Hodnota atributu **name** elementu **Output** nesmí být delší než 64 znaků.
* Hodnota atributu **typu** **výstupního** prvku musí být *Vizualizace*.

### <a name="arguments"></a>Argumenty
Další data mohou být předána funkci R prostřednictvím parametrů modulu, které jsou definovány v elementu **Arguments.** Tyto parametry se zobrazí v podokně vlastností zcela vpravo v rozhraní Machine Learning UI, když je vybrán modul. Argumenty mohou být libovolný z podporovaných typů nebo můžete v případě potřeby vytvořit vlastní výčet. Podobně jako **ports** elementy mohou mít **elementargumenty** **volitelný** description element, který určuje text, který se zobrazí při najetí myší na název parametru.
Volitelné vlastnosti modulu, například defaultValue, minValue a maxValue, lze přidat k libovolnému argumentu jako atributy prvku **Properties.** Platné vlastnosti elementu **Properties** závisí na typu argumentu a jsou popsány s podporovanými typy argumentů v další části. Argumenty s **vlastností isOptional** nastavenou na **hodnotu "true"** nevyžadují, aby uživatel zadával hodnotu. Pokud hodnota není k dispozici argument, pak argument není předán funkci vstupního bodu. Argumenty funkce vstupního bodu, které jsou volitelné, musí být explicitně zpracovány funkcí, například přiřazenou výchozí hodnotu NULL v definici funkce vstupního bodu. Volitelný argument vynutí pouze ostatní omezení argumentu, tj.
Stejně jako u vstupů a výstupů je důležité, aby každý z parametrů měl jedinečné hodnoty ID, které jsou k nim přidruženy. V našem příkladu rychlého startu bylo přidružené id/parametr *swap*.

### <a name="arg-element"></a>Arg prvek
Parametr modulu je definován pomocí **podřízeného** prvku Arg v části **Arguments** v definičním souboru XML. Stejně jako u podřízených prvků v části **Porty,** řazení parametrů v části **Argumenty** definuje rozložení, ke kterým došlo v uživatelském rozhraní. Parametry se zobrazí shora dolů v ui ve stejném pořadí, ve kterém jsou definovány v souboru XML. Zde jsou uvedeny typy podporované machine learningem pro parametry. 

**int** – parametr typu Integer (32-bit).

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *Volitelné vlastnosti:* **min**, **max**, **výchozí** a **isOptional**

**double** – parametr dvojitého typu.

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* *Volitelné vlastnosti:* **min**, **max**, **výchozí** a **isOptional**

**bool** – logický parametr, který je reprezentován zaškrtávacím políčkem v uživatelském rozhraní.

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* *Volitelné vlastnosti*: **výchozí** - false, pokud není nastaveno

**řetězec**: standardní řetězec

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* *Volitelné vlastnosti:* **výchozí** a **isOptional**

**ColumnPicker**: parametr výběru sloupce. Tento typ se vykreslí v uživatelském nastavení jako výběr sloupce. Element **Vlastnost** i) se zde používá k určení ID portu, ze kterého jsou vybrány sloupce, kde cílový typ portu musí být *DataTable*. Výsledek výběru sloupce je předán funkci R jako seznam řetězců obsahujících vybrané názvy sloupců. 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *Povinné vlastnosti*: **portId** - odpovídá ID vstupního prvku s typem *DataTable*.
* *Volitelné vlastnosti*:
  
  * **allowedTypes** - Filtruje typy sloupců, ze kterých můžete vybrat. Mezi platné hodnoty patří: 
    
    * Numeric
    * Logická hodnota
    * Kategorické
    * Řetězec
    * Popisek
    * Funkce
    * Skóre
    * Všechny
  * **výchozí** – Platné výchozí výběry pro výběr sloupců zahrnují: 
    
    * Žádný
    * Numerická funkce
    * Numericlabel
    * Početní skóre
    * Numericall
    * Logická hodnota
    * Logická značka
    * Logické skóre
    * Logická všechna
    * Kategorický prvek
    * Kategorický štítek
    * Kategorické skóre
    * Kategorie Vše
    * Funkce stringfeature
    * Popisek řetězce
    * Skóre řetězce
    * Stringall (Vše)
    * Popisek AllLabel
    * Funkce AllFeature
    * AllScore
    * Všechny

**DropDown**: uživatelem zadaný seznam s výčtem (rozevírací seznam). Rozevírací položky jsou určeny v rámci **vlastnosti** elementpomocí **Item** element. **ID** pro každou **položku** musí být jedinečné a platná proměnná R. Hodnota **názvu** **Item** slouží jako text, který vidíte, a hodnota, která je předána funkci R.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* *Volitelné vlastnosti*:
  * **výchozí** - Hodnota výchozí vlastnosti musí odpovídat hodnotě ID z jednoho z prvků **Item.**

### <a name="auxiliary-files"></a>Pomocné soubory
Jakýkoli soubor, který je umístěn ve vašem vlastním souboru ZIP modulu bude k dispozici pro použití během doby provádění. Všechny adresářové struktury jsou zachovány. To znamená, že získávání souborů funguje stejně místně a v Azure Machine Learning Studio (klasické) spuštění. 

> [!NOTE]
> Všimněte si, že všechny soubory jsou extrahovány do adresáře 'src', takže všechny cesty by měly mít 'src/' předponou.
> 
> 

Řekněme například, že chcete odebrat všechny řádky s místními názvy z datové sady a také odebrat všechny duplicitní řádky před jejich přepsáním do customaddrows a již jste napsali funkci R, která to dělá v souboru RemoveDupNARows.R:

    RemoveDupNARows <- function(dataFrame) {
        #Remove Duplicate Rows:
        dataFrame <- unique(dataFrame)
        #Remove Rows with NAs:
        finalDataFrame <- dataFrame[complete.cases(dataFrame),]
        return(finalDataFrame)
    }
Pomocný soubor RemoveDupNARows.R můžete zadat ve funkci CustomAddRows:

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

Dále nahrajte soubor ZIP obsahující "CustomAddRows.R", CustomAddRows.xml a RemoveDupNARows.R jako vlastní modul R.

## <a name="execution-environment"></a>Prostředí provádění
Spuštění prostředí pro skript R používá stejnou verzi R jako **modul Spustit Skript R** a můžete použít stejné výchozí balíčky. Můžete také přidat další balíčky R do vlastního modulu jejich zahrnutím do balíčku zip vlastního modulu. Stačí je načíst do skriptu R stejně jako ve vlastním prostředí R. 

**Omezení prostředí spuštění** patří:

* Netrvalý systém souborů: Soubory napsané při spuštění vlastního modulu nejsou trvalé ve více spuštěních stejného modulu.
* Žádný přístup k síti

