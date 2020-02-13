---
title: Definovat vlastní moduly R
titleSuffix: ML Studio (classic) - Azure
description: Toto téma popisuje, jak vytvořit a nasadit vlastní R Studio (Classic). Vysvětluje, co jsou vlastních modulů R a jaké soubory se používají k jejich definování.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 35046d33a85eaed913454f188f2a4526715526a9
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168788"
---
# <a name="define-custom-r-modules-for-azure-machine-learning-studio-classic"></a>Definování vlastních modulů R pro Azure Machine Learning Studio (Classic)

Toto téma popisuje, jak vytvořit a nasadit vlastní R Studio (Classic). Vysvětluje, co jsou vlastních modulů R a jaké soubory se používají k jejich definování. Ukazuje, jak vytvořit soubory, které definují modulu a zaregistrovat modul pro nasazení v pracovním prostoru Machine Learning. Elementy a atributy použité v definici vlastního modulu jsou pak popsány podrobněji. Použití pomocné funkce a soubory a několik výstupů se probírá také. 



## <a name="what-is-a-custom-r-module"></a>Co je vlastní modul R?
**Vlastní modul** je uživatelsky definovaný modul, který se dá nahrát do vašeho pracovního prostoru a provést jako součást experimentu Azure Machine Learning Studio (Classic). **Vlastní modul r** je vlastní modul, který provádí uživatelsky definovanou funkci jazyka r. **R** je programovací jazyk pro statistické výpočty a grafiky, které jsou široce používány statistikami a odborníky na data pro implementaci algoritmů. V současné době R je jediným podporovaným v vlastních modulů, ale podpora pro další jazyky je plánovaná pro budoucí verze jazykem.

Vlastní moduly mají **stav první třídy** v Azure Machine Learning Studio (Classic) v tom smyslu, že je lze použít stejně jako jakýkoli jiný modul. Je možné provést s ostatními moduly, zahrnuté v publikovaných experimentů nebo ve vizualizacích. Budete mít kontrolu nad algoritmus implementovaný pomocí modulu, vstupní a výstupní porty, který se má použít, modelování parametry a další různé chování za běhu. Experiment, který obsahuje vlastní moduly můžete taky publikovat v galerii Azure AI pro snadné sdílení.

## <a name="files-in-a-custom-r-module"></a>Soubory ve vlastní modul R
Vlastní modul R je definována v souboru ZIP, který obsahuje minimálně dva soubory:

* **Zdrojový soubor** , který implementuje funkci R vystavenou modulem
* **Definiční soubor XML** popisující rozhraní vlastního modulu

Další pomocné soubory mohou být i součástí souboru ZIP, který poskytuje funkce, které lze přistupovat z vlastního modulu. Tato možnost je popsána **v části** elementy oddílu reference **v souboru definice XML** , který následuje příklad pro rychlý Start.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Příklad rychlý start: Definujte, balení a zaregistrovat vlastní modul R
Tento příklad ukazuje, jak vytvořit soubory vyžadují vlastní modul R, zabalit je do souboru zip a zaregistrujte modul ve vašem pracovním prostoru Machine Learning. Ukázkový balíček zip a ukázkové soubory si můžete stáhnout z části [Stažení souboru CustomAddRows. zip](https://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>Zdrojový soubor
Vezměte v úvahu příklad **vlastního modulu přidat řádky** , který upravuje standardní implementaci modulu **Přidat řádky** používané pro zřetězení řádků (pozorování) ze dvou datových sad (datové rámce). Modul standardní **Přidat řádky** připojí řádky druhé vstupní datové sady ke konci první datové sady dat pomocí `rbind`ho algoritmu. Přizpůsobená funkce `CustomAddRows` podobně přijímá dvě datové sady, ale také přijímá parametr Boolean swap jako další vstup. Pokud je parametr swapu nastaven na **hodnotu false**, vrátí stejnou sadu dat jako standardní implementace. Ale pokud má parametr swapu **hodnotu true**, funkce připojí řádky první vstupní datové sady k konci druhé datové sady místo toho. Soubor CustomAddRows. R, který obsahuje implementaci funkce R `CustomAddRows` zveřejněné modulem **Custom Add Rows** , má následující kód R.

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

### <a name="the-xml-definition-file"></a>Definiční soubor XML
Chcete-li tuto funkci `CustomAddRows` zpřístupnit jako modul Azure Machine Learning Studio (Classic), je nutné vytvořit definiční soubor XML, který určuje, jak má modul **vlastního přidávání řádků** vypadat a jak se chová. 

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


Je důležité si uvědomit, že hodnota atributů **ID** elementů **input** a **arg** v souboru XML musí odpovídat názvům parametrů funkce kódu R v souboru CustomAddRows. R přesně: (*DataSet1.* , *Dataset2*a *swap* v příkladu). Podobně hodnota atributu **EntryPoint** elementu Language se musí shodovat s názvem funkce ve skriptu **jazyka** R, a to přesně: (*CustomAddRows* v příkladu). 

Naproti tomu atribut **ID** pro element **Output** neodpovídá žádným proměnným ve skriptu jazyka R. Pokud je vyžadován více než jeden výstup, jednoduše vraťte seznam z funkce R s výsledky umístěnými *ve stejném pořadí* jako prvky **výstupů** v souboru XML.

### <a name="package-and-register-the-module"></a>Balení a zaregistrovat modul
Tyto dva soubory uložte jako *CustomAddRows. R* a *CustomAddRows. XML* a potom tyto dva soubory vytvořte společně do souboru *CustomAddRows. zip* .

Pokud je chcete zaregistrovat v pracovním prostoru Machine Learning, přejděte do pracovního prostoru v Azure Machine Learning Studio (Classic), klikněte na tlačítko **+ Nový** v dolní části a vyberte **modul-> z balíčku zip** a nahrajte tak nový modul **vlastní přidané řádky** .

![Nahrát Zip](./media/custom-r-modules/upload-from-zip-package.png)

**Vlastní modul přidat řádky** je teď připravený na vaše Machine Learning experimenty.

## <a name="elements-in-the-xml-definition-file"></a>Elementy v souboru definice XML
### <a name="module-elements"></a>Elementy modulu
Element **Module** slouží k definování vlastního modulu v souboru XML. Více modulů lze definovat v jednom souboru XML pomocí více prvků **modulu** . V pracovním prostoru každý modul musí mít jedinečný název. Registrovat vlastní modul se stejným názvem jako stávající vlastní modul a nahradí existující modul s novým. Vlastní moduly ale můžou být zaregistrované se stejným názvem jako stávající modul Azure Machine Learning Studio (Classic). V takovém případě se zobrazí ve **vlastní** kategorii palety modulu.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


V rámci elementu **modulu** můžete zadat dva další nepovinné prvky:

* prvek **vlastníka** , který je vložen do modulu  
* prvek **popisu** , který obsahuje text zobrazený v rychlé nápovědě pro modul a při najetí myší na modul v uživatelském rozhraní Machine Learning.

Pravidla pro omezení znaků v prvcích modulu:

* Hodnota atributu **Name** v elementu **Module** nesmí být delší než 64 znaků. 
* Obsah elementu **Description** nesmí mít délku maximálně 128 znaků.
* Obsah elementu **Owner** nesmí mít délku maximálně 32 znaků.

Může být deterministické výsledky modulu nebo nondeterministic.* * ve výchozím nastavení, všechny moduly jsou považovány za deterministický. To znamená, že s ohledem na nezměněnou sadu vstupních parametrů a dat by měl modul vracet stejné výsledky eacRAND nebo čas spuštění funkce. Vzhledem k tomuto chování Azure Machine Learning Studio (Classic) pouze znovu spustí moduly označené jako deterministické, pokud se změní parametr nebo vstupní data. Vrací výsledky uložené v mezipaměti obsahuje také mnohem rychlejší spouštění experimentů.

Jsou funkce, které jsou nedeterministické, jako je například RAND nebo funkci, která vrátí aktuální datum nebo čas. Pokud váš modul používá nedeterministické funkce, můžete určit, že modul je Nedeterministický, nastavením volitelného atributu ' **deterministické** ' na **hodnotu false**. To zajistí, že modul se znovu spustí při každém spuštění experimentu, i v případě, že nedošlo ke změně modulu vstup a parametry. 

### <a name="language-definition"></a>Definice jazyka
Prvek **jazyka** v souboru definice XML slouží k určení jazyka vlastního modulu. V současné době je R jediným podporovaným jazykem. Hodnota atributu **požadovaný sourcefile** musí být název souboru R, který obsahuje funkci, která se má volat při spuštění modulu. Tento soubor musí být součástí balíček zip. Hodnota atributu **EntryPoint** je název volané funkce a musí odpovídat platné funkci definované ve zdrojovém souboru.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>Porty
Vstupní a výstupní porty pro vlastní modul jsou zadány v podřízených prvcích oddílu **porty** souboru definice XML. Určuje pořadí z těchto elementů rozložení zkušení (UX) uživatelé. První podřízený **vstup** nebo **výstup** uvedený v prvku **porty** souboru XML se nachází v Machine Learningm uživatelském rozhraní jako levý krajní vstupní port.
Každý vstupní a výstupní port může mít nepovinný podřízený element **Description** , který určuje text zobrazený při přesunutí ukazatele myši na port v uživatelském rozhraní Machine Learning.

**Pravidla portů**:

* Maximální počet **vstupních a výstupních portů** pro každý z nich je 8.

### <a name="input-elements"></a>Elementy vstupu
Vstupní porty umožní předat data do R funkce a pracovní prostor. **Datové typy** , které jsou podporovány pro vstupní porty, jsou následující: 

**DataTable:** Tento typ je předán vaší funkci jazyka R jako data. Frame. Ve skutečnosti jsou všechny typy (například soubory CSV nebo soubory ARFF), které jsou podporovány Machine Learning a které jsou kompatibilní s **DataTable** , převedeny na data. Frame automaticky. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

Atribut **ID** přidružený ke každému vstupnímu portu **DataTable** musí mít jedinečnou hodnotu a tato hodnota musí odpovídat odpovídajícímu pojmenovanému parametru ve funkci jazyka R.
Volitelné porty **DataTable** , které nejsou předány jako vstup v experimentu, mají hodnotu **null** předanou funkci jazyka R a volitelné porty zip jsou ignorovány, pokud vstup není připojen. Atribut- **Option** je volitelný pro typy **DataTable** i **zip** a ve výchozím nastavení má *hodnotu false* .

**PSČ:** Vlastní moduly můžou jako vstup přijmout soubor zip. Tento vstup je vybaleno do pracovního adresáře r. vaší funkce

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

Pro vlastní moduly R nemusí ID pro port zip odpovídat žádným parametrům funkce R. Je to proto, že je soubor zip automaticky extrahován do pracovního adresáře r.

**Vstupní pravidla:**

* Hodnota atributu **ID** **vstupního** elementu musí být platný název proměnné R.
* Hodnota atributu **ID** **vstupního** elementu nesmí být delší než 64 znaků.
* Hodnota atributu **Name** elementu **input** nesmí být delší než 64 znaků.
* Obsah elementu **Description** nesmí být delší než 128 znaků.
* Hodnota atributu type **vstupního** elementu musí být **typu** *zip* nebo *DataTable*.
* Hodnota atributu **Option** elementu **input** není povinná (ve výchozím nastavení je to *false* , pokud není zadané); ale pokud je zadaný, musí mít *hodnotu true* nebo *false*.

### <a name="output-elements"></a>Výstup elementy
**Standardní výstupní porty:** Výstupní porty jsou namapovány na návratové hodnoty z funkce jazyka R, které mohou být následně použity v dalších modulech. *DataTable* je jediným aktuálně podporovaným typem výstupního portu Standard. (Podpora pro *učení* a *transformace* je k.) Výstup *DataTable* je definován jako:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

Pro výstupy ve vlastních modulech R nemusí hodnota atributu **ID** odpovídat cokoli ve skriptu R, ale musí být jedinečná. Pro výstup jednoho modulu musí být návratová hodnota z funkce R typu *data. Frame*. Aby bylo možné předat více než jeden objekt podporovaného typu, musí být zadána v definičním souboru XML odpovídající výstupní porty a objekty musí být vrácena jako seznam. Výstup objekty jsou přiřazeny k výstupní porty zleva doprava, odrážející pořadí, ve kterém jsou objekty umístěny ve vráceném seznamu.

Pokud například chcete upravit modul **vlastní přidané řádky** na výstup původních dvou datových sad, *DataSet1.* a *Dataset2*, kromě nové připojené datové sady, *datové sady*(v pořadí zleva doprava, jako: *DataSet*, *DataSet1.* , *Dataset2*), pak definujte výstupní porty v souboru CustomAddRows. xml takto:

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


A vrátí seznam objektů v seznamu ve správném pořadí v "CustomAddRows.R":

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 

**Výstup vizualizace:** Můžete také zadat výstupní port typu *vizualizace*, který zobrazuje výstup z grafického zařízení R a z výstupu konzoly. Tento port není součástí výstupu funkce R a není v konfliktu s pořadím z ostatních typů výstupní port. Chcete-li přidat port vizualizace do vlastních modulů, přidejte element **Output** s hodnotou *vizualizace* pro jeho **typ** atributu:

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**Výstupní pravidla:**

* Hodnota atributu **ID** elementu **Output** musí být platný název proměnné R.
* Hodnota atributu **ID** elementu **Output** nesmí být delší než 32 znaků.
* Hodnota atributu **Name** elementu **Output** nesmí být delší než 64 znaků.
* Hodnota atributu **Type** elementu **Output** musí být *vizualizace*.

### <a name="arguments"></a>Argumenty
Do funkce jazyka R lze předat další data prostřednictvím parametrů modulu, které jsou definovány v elementu **arguments** . Tyto parametry se zobrazí v podokně úplně vpravo vlastnosti uživatelského rozhraní Machine Learning, pokud je vybrána modulu. Argumenty mohou být některé z podporovaných typů nebo můžete vytvořit vlastní výčtu v případě potřeby. Podobně jako prvky **portů** mohou prvky **argumentů** mít volitelný element **Description** , který určuje text, který se zobrazí, když najedete myší na název parametru.
Volitelné vlastnosti pro modul, jako je například defaultValue, minValue a maxValue, lze přidat do libovolného argumentu jako atributy prvku **vlastnosti** . Platné vlastnosti pro element **Properties** závisí na typu argumentu a jsou popsány s podporovanými typy argumentů v následující části. Argumenty s vlastností **Option** nastavenou na **hodnotu true** nevyžadují, aby uživatel zadal hodnotu. Pokud hodnota není k dispozici na argument, není argument předaný funkci vstupního bodu. Je nutné explicitně zacházet funkcí, třeba přiřadit výchozí hodnotu NULL v definici vstupní bod funkce argumenty funkci vstupního bodu, které jsou volitelné. Volitelný argument bude vynucovat, jenom dalších argumentů omezení, například min nebo max, pokud je uživatel zadal hodnotu.
Stejně jako u vstupů a výstupů je velmi důležité, aby k jednotlivým parametrům byly přidruženy jedinečné hodnoty ID. V našem příkladu pro rychlý Start došlo k *záměně*přidruženého ID/parametru.

### <a name="arg-element"></a>Arg – element
Parametr modulu je definován pomocí podřízeného prvku **arg** oddílu **argumenty** souboru definice XML. Stejně jako u podřízených prvků v oddílu **porty** definuje řazení parametrů v oddílu **argumenty** rozložení, které se nachází v uživatelském rozhraní. Parametry zobrazí shora dolů v uživatelském rozhraní ve stejném pořadí, ve kterém jsou definovány v souboru XML. Typy podporované nástrojem Machine Learning pro parametry jsou uvedeny zde. 

**int** – parametr typu Integer (32-bit).

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *Volitelné vlastnosti*: **minimum**, **Max**, **výchozí** a **parametr-Option**

**Double** – parametr typu Double.

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* *Volitelné vlastnosti*: **minimum**, **Max**, **výchozí** a **parametr-Option**

**bool** – logický parametr, který je reprezentován kontrolním polem v uživatelském rozhraní.

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* *Volitelné vlastnosti*: **Default** -false, pokud není nastavené

**řetězec**: standardní řetězec

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* *Volitelné vlastnosti*: **výchozí** a **parametr-Option**

**ColumnPicker**: parametr výběru sloupce. Tento typ se zobrazí v uživatelském prostředí jako výběr sloupců. Element **Property** se tady používá k zadání ID portu, ze kterého se mají vybrat sloupce, kde cílový typ portu musí být *DataTable*. Výsledek výběr sloupce je předán do funkce R jako seznam řetězců obsahující názvy vybraných sloupců. 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *Požadované vlastnosti*: **identifikátor PORTID** – odpovídá ID vstupního elementu s typem *DataTable*.
* *Volitelné vlastnosti*:
  
  * **allowedTypes** – filtruje typy sloupců, ze kterých můžete vybírat. Platné hodnoty jsou: 
    
    * Numeric
    * Logická hodnota
    * Kategorické
    * String
    * Popisek
    * Funkce
    * Skóre
    * Všechny
  * **výchozí** – platné výchozí možnosti pro výběr sloupce zahrnují: 
    
    * Žádná
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
    * Všechny

**DropDown**: seznam výčtového (rozevíracího seznamu) zadaného uživatelem. Rozevírací položky jsou určeny v rámci elementu **Properties** pomocí elementu **Item** . **ID** každé **položky** musí být jedinečné a platnou proměnnou R. Hodnota **názvu** **položky** slouží jako text, který vidíte, a hodnotu, která je předána funkci jazyka R.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* *Volitelné vlastnosti*:
  * **výchozí** – hodnota pro výchozí vlastnost musí odpovídat hodnotě ID z jednoho elementu **Item** .

### <a name="auxiliary-files"></a>Pomocné soubory
Každý soubor, který je umístěn v souboru ZIP vlastní modul bude k dispozici pro použití při spuštění. Všechny adresáře struktury k dispozici jsou zachovány. To znamená, že u zdroje souborů funguje stejně místně i v Azure Machine Learning Studio (klasickém) spuštění. 

> [!NOTE]
> Všimněte si, že všechny soubory jsou extrahovány do adresáře "src", musí mít všechny cesty "src /" předponu.
> 
> 

Předpokládejme například, že chcete odebrat všechny řádky s NAs z datové sady a taky před výstupu do CustomAddRows odebrat všechny duplicitní řádky a už jste napsali R funkce, který činí v souboru RemoveDupNARows.R:

    RemoveDupNARows <- function(dataFrame) {
        #Remove Duplicate Rows:
        dataFrame <- unique(dataFrame)
        #Remove Rows with NAs:
        finalDataFrame <- dataFrame[complete.cases(dataFrame),]
        return(finalDataFrame)
    }
Pomocný soubor RemoveDupNARows.R ve funkci CustomAddRows mají možnost:

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

V dalším kroku nahrajte soubor zip obsahující "CustomAddRows.R", "CustomAddRows.xml" a "RemoveDupNARows.R" jako vlastní modul R.

## <a name="execution-environment"></a>Spuštění prostředí
Spouštěcí prostředí pro skript R používá stejnou verzi R jako modul **spuštění skriptu jazyka r** a může používat stejné výchozí balíčky. Další balíčky r. na vlastní modul můžete také přidat jejich zahrnutím do vlastního modulu zip balíčku. Stačí je načte ve skriptu R stejně jako v prostředí R. 

Mezi **omezení spouštěcího prostředí** patří:

* Dočasný soubor systému: během různých spuštění stejného modulu nejsou trvalé soubory zapsané při spuštění vlastního modulu.
* Žádný přístup k síti

