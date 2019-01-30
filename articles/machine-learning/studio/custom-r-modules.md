---
Název: Definujte vlastní moduly titleSuffix R: Azure Machine Learning Studio Popis: Toto téma popisuje, jak vytvořit a nasadit vlastní modul R ve službě Azure Machine Learning. Vysvětluje, co jsou vlastních modulů R a jaké soubory se používají k jejich definování. Services: machine learningu ms.service: ms.subservice strojového učení: studio ms.topic: článku

Autor: ericlicoding ms.author: amlstudiodocs ms.custom: seodec18 ms.date: 11/29/2017
---
# <a name="define-custom-r-modules-for-azure-machine-learning-studio"></a>Definování vlastních modulů R pro Azure Machine Learning Studio

Toto téma popisuje, jak vytvořit a nasadit vlastní modul R v nástroji Azure Machine Learning Studio. Vysvětluje, co jsou vlastních modulů R a jaké soubory se používají k jejich definování. Ukazuje, jak vytvořit soubory, které definují modulu a zaregistrovat modul pro nasazení v pracovním prostoru Machine Learning. Elementy a atributy použité v definici vlastního modulu jsou pak popsány podrobněji. Použití pomocné funkce a soubory a několik výstupů se probírá také. 



## <a name="what-is-a-custom-r-module"></a>Co je vlastní modul R?
A **vlastního modulu** je modul definovaný uživatelem, který může odeslat do pracovního prostoru a spustit v rámci experimentu Azure Machine Learning. A **vlastní modul R** je vlastní modul, který se spustí uživatelem definovanou funkci jazyka R. **R** je programovací jazyk pro statistické výpočty a grafiku, které je běžně používaný v vědeckými pracovníky a odborníky přes data pro implementaci algoritmy. V současné době R je jediným podporovaným v vlastních modulů, ale podpora pro další jazyky je plánovaná pro budoucí verze jazykem.

Vlastní moduly mají **stavu hodnoty první kategorie** ve službě Azure Machine Learning v tom smyslu, že můžete použít stejně jako ostatní moduly. Je možné provést s ostatními moduly, zahrnuté v publikovaných experimentů nebo ve vizualizacích. Budete mít kontrolu nad algoritmus implementovaný pomocí modulu, vstupní a výstupní porty, který se má použít, modelování parametry a další různé chování za běhu. Experiment, který obsahuje vlastní moduly můžete taky publikovat v galerii Azure AI pro snadné sdílení.

## <a name="files-in-a-custom-r-module"></a>Soubory ve vlastní modul R
Vlastní modul R je definována v souboru ZIP, který obsahuje minimálně dva soubory:

* A **zdrojový soubor** , který implementuje funkci R vystavené modulu
* **Definiční soubor XML** popisující rozhraní vlastního modulu

Další pomocné soubory mohou být i součástí souboru ZIP, který poskytuje funkce, které lze přistupovat z vlastního modulu. Tato možnost je podrobněji popsána **argumenty** součástí referenční části **elementy v souboru definice XML** následujícího příkladu rychlého startu.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Příklad rychlý start: Definujte, balení a zaregistrovat vlastní modul R
Tento příklad ukazuje, jak vytvořit soubory vyžadují vlastní modul R, zabalit je do souboru zip a zaregistrujte modul ve vašem pracovním prostoru Machine Learning. Příklad zip balíčku a ukázkové soubory můžete stáhnout z [soubor stáhnout CustomAddRows.zip](https://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>Zdrojový soubor
Podívejte se na příklad z **vlastní přidat řádky** modul, který upravuje standardní implementace **přidat řádky** modul používaný ke zřetězení řádky (poznámky) z dvě datové sady (datové rámce). Standardní **přidat řádky** modulu připojí řádky druhé vstupní datovou sadu na konec první vstupní datové sady pomocí `rbind` algoritmus. Upravené `CustomAddRows` funkce podobně přijímá dvě datové sady, ale také přijímá parametr logická odkládacího souboru jako další vstup. Pokud parametr odkládacího souboru je nastaven na **FALSE**, vrátí stejnou sadu dat jako standardní implementace. Ale pokud je parametr prohození **TRUE**, funkce připojí řádky první vstupní datovou sadu na konec datové sady druhé místo. CustomAddRows.R souboru, který obsahuje implementace jazyka R `CustomAddRows` funkce vystavené **vlastní přidat řádky** module obsahuje následující kód R.

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
To vystavit `CustomAddRows` funkce jako modul služby Azure Machine Learning, definičního souboru XML musí být vytvořený zadat jak **vlastní přidat řádky** modul by měl vypadat a fungovat. 

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


Je důležité si uvědomit, že hodnota **id** atributy **vstup** a **Arg** elementy v souboru XML musí shodovat s názvy parametru funkce kódu R v CustomAddRows.R přesně souborů: (*dataset1*, *dataset2*, a *prohození* v příkladu). Podobně, hodnota **entryPoint** atribut **jazyka** element musí přesně odpovídat názvu funkce ve skriptu R: (*CustomAddRows* v příkladu). 

Naproti tomu **id** atribut pro **výstup** elementu neodpovídá žádné proměnné ve skriptu R. Když se vyžaduje více než jeden výstup, jednoduše vrátila seznam z funkce R s výsledky umístit *ve stejném pořadí* jako **výstupy** elementů jsou deklarovány v souboru XML.

### <a name="package-and-register-the-module"></a>Balení a zaregistrovat modul
Uložte tyto dva soubory jako *CustomAddRows.R* a *CustomAddRows.xml* a potom zip společně do příslušné dva soubory *CustomAddRows.zip* souboru.

K registraci je ve vašem pracovním prostoru Machine Learning, přejděte do pracovního prostoru Machine Learning Studio, klikněte na tlačítko **+ nová** tlačítko v dolní části a zvolte **modulu -> z ZIP balíčku** nahrát nový **Vlastní přidat řádky** modulu.

![Nahrát Zip](./media/custom-r-modules/upload-from-zip-package.png)

**Vlastní přidat řádky** modulu je teď připravený k přístupný vaše experimenty Machine Learning.

## <a name="elements-in-the-xml-definition-file"></a>Elementy v souboru definice XML
### <a name="module-elements"></a>Elementy modulu
**Modulu** element slouží k definování vlastní modul v souboru XML. Více modulů lze definovat v jednom souboru XML pomocí více **modulu** elementy. V pracovním prostoru každý modul musí mít jedinečný název. Registrovat vlastní modul se stejným názvem jako stávající vlastní modul a nahradí existující modul s novým. Vlastní moduly lze však registrována se stejným názvem jako existující modul Azure Machine Learning. Pokud ano, jsou uvedeny v **vlastní** kategorie palety modulů.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


V rámci **modulu** element, můžete zadat dva další volitelné prvky:

* **vlastníka** element, který je součástí modulu  
* **popis** element, který obsahuje text, který se zobrazí v rychlé nápovědě se dozvíte v modulu a když najedete myší modulu v uživatelském rozhraní Machine Learning.

Pravidla pro omezení znaků v prvcích modulu:

* Hodnota **název** atribut **modulu** prvek nesmí být delší než 64 znaků. 
* Obsah **popis** prvek nesmí být delší než 128 znaků.
* Obsah **vlastníka** prvek nesmí být delší než 32 znaků.

Může být deterministické výsledky modulu nebo nondeterministic.* * ve výchozím nastavení, všechny moduly jsou považovány za deterministický. To znamená, zadaný neměnné sady vstupní parametry a dat, modul by měl vrátit stejný eacRAND výsledky nebo functionh čas, ve kterém se spouští. Toto chování, Azure Machine Learning Studio zhodnotíte pouze moduly, které jsou označeny jako deterministické, pokud parametr nebo vstupní data se změnila. Vrací výsledky uložené v mezipaměti obsahuje také mnohem rychlejší spouštění experimentů.

Jsou funkce, které jsou nedeterministické, jako je například RAND nebo funkci, která vrátí aktuální datum nebo čas. Pokud modul používá nedeterministická funkci, můžete určit, že modul je Nedeterministický nastavením nepovinný **isDeterministic** atribut **FALSE**. To zajistí, že modul se znovu spustí při každém spuštění experimentu, i v případě, že nedošlo ke změně modulu vstup a parametry. 

### <a name="language-definition"></a>Definice jazyka
**Jazyk** element v souboru definice XML je možné určit jazyk vlastního modulu. V současné době je R jediným podporovaným jazykem. Hodnota **zdrojový soubor** atribut musí být název souboru R, který obsahuje funkce, která má být volána při spuštění modulu. Tento soubor musí být součástí balíček zip. Hodnota **entryPoint** atribut je název volané funkce a musí odpovídat platné funkce definované s ve zdrojovém souboru.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>Porty
Vstupní a výstupní porty pro vlastní modul jsou určené v podřízených elementů **porty** část definičního souboru XML. Určuje pořadí z těchto elementů rozložení zkušení (UX) uživatelé. Prvním podřízeným objektem **vstupní** nebo **výstup** uvedené v **porty** – element XML souboru stane krajní levý vstupní port v uživatelské prostředí Machine Learning
Každá vstupní a výstupní port může mít volitelně **popis** podřízený prvek, který určuje text zobrazený po najeďte myší do portu v uživatelském rozhraní Machine Learning.

**Pravidla portů**:

* Maximální počet **vstupní a výstupní porty** je 8 příkaz for each.

### <a name="input-elements"></a>Elementy vstupu
Vstupní porty umožní předat data do R funkce a pracovní prostor. **Datové typy** , které jsou podporovány pro vstupní porty jsou následující: 

**Objekt DataTable:** Tento typ je předán do funkce R jako data.frame. Ve skutečnosti všechny typy (například soubory CSV nebo ARFF soubory), které jsou podporovány v Machine Learning a, která jsou kompatibilní s **DataTable** jsou převedeny na data.frame automaticky. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

**Id** atribut spojené s jednotlivými **DataTable** vstupního portu musí mít jedinečnou hodnotu a tato hodnota se musí shodovat s názvem parametru ve funkci R odpovídající.
Volitelné **DataTable** porty, které nejsou předané jako vstup v jednom experimentu mají hodnotu **NULL** předaný funkci jazyka R a volitelné zip, porty jsou ignorovány, pokud vstup není připojený. **Schedule** atribut je volitelný pro obě **DataTable** a **Zip** typů a je *false* ve výchozím nastavení.

**ZIP:** Vlastní moduly můžete jako vstup přijmout soubor zip. Tento vstup je vybaleno do pracovního adresáře r. vaší funkce

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

U vlastních modulů R nemá identifikátor pro PSČ port tak, aby odpovídaly žádné parametry funkce jazyka R. Je to proto, že je soubor zip automaticky extrahován do pracovního adresáře r.

**Vstupní pravidla:**

* Hodnota **id** atribut **vstup** element musí být platný název proměnné R.
* Hodnota **id** atribut **vstup** prvek nesmí být delší než 64 znaků.
* Hodnota **název** atribut **vstup** prvek nesmí být delší než 64 znaků.
* Obsah **popis** prvek nesmí být delší než 128 znaků.
* Hodnota **typ** atribut **vstup** element musí být *Zip* nebo *DataTable*.
* Hodnota **Schedule** atribut **vstup** element není vyžadován (a je *false* ve výchozím nastavení, pokud není zadán); ale pokud je zadán, musí být *true* nebo *false*.

### <a name="output-elements"></a>Výstup elementy
**Standardní výstupní porty:** Výstupní porty jsou mapovány na návratové hodnoty z funkce R, který můžete použít následující moduly. *Objekt DataTable* je momentálně nepodporuje typ portu pouze standardní výstup. (Podpora *inteligentních algoritmů* a *transformuje* je připravovaný.) A *DataTable* výstup je definován jako:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

Pro výstupy ve vlastních modulů R, hodnota **id** atribut nemá odpovídat všechno ve skriptu R, ale musí být jedinečný. Pro jeden modul výstup, vrácená hodnota z funkce R musí být *data.frame*. Aby bylo možné předat více než jeden objekt podporovaného typu, musí být zadána v definičním souboru XML odpovídající výstupní porty a objekty musí být vrácena jako seznam. Výstup objekty jsou přiřazeny k výstupní porty zleva doprava, odrážející pořadí, ve kterém jsou objekty umístěny ve vráceném seznamu.

Například, pokud chcete změnit **vlastní přidat řádky** modulu do výstupního původní dvě datové sady, *dataset1* a *dataset2*, kromě novou datovou sadu připojené k doméně, *datovou sadu*, (v pořadí zleva doprava, jako: *datovou sadu*, *dataset1*, *dataset2*), pak definujte výstupní porty v CustomAddRows.xml souboru následujícím způsobem:

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

**Vizualizace výstupu:** Můžete také určit výstupní port modulu typu *vizualizace*, který se zobrazí výstup z výstupu R grafiky zařízení a konzoly. Tento port není součástí výstupu funkce R a není v konfliktu s pořadím z ostatních typů výstupní port. Pokud chcete přidat do modulů, které vlastní vizualizace port, přidejte **výstup** element s hodnotou *vizualizace* pro jeho **typ** atribut:

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**Výstup pravidla:**

* Hodnota **id** atribut **výstup** element musí být platný název proměnné R.
* Hodnota **id** atribut **výstup** prvek nesmí být delší než 32 znaků.
* Hodnota **název** atribut **výstup** prvek nesmí být delší než 64 znaků.
* Hodnota **typ** atribut **výstup** element musí být *vizualizace*.

### <a name="arguments"></a>Argumenty
Další data může být předán funkci R prostřednictvím modulu parametrů, které jsou definovány v **argumenty** elementu. Tyto parametry se zobrazí v podokně úplně vpravo vlastnosti uživatelského rozhraní Machine Learning, pokud je vybrána modulu. Argumenty mohou být některé z podporovaných typů nebo můžete vytvořit vlastní výčtu v případě potřeby. Podobně jako **porty** prvky, **argumenty** prvků může mít volitelně **popis** element, který určuje text, který se zobrazí, když myší najedete myší Název parametru.
Volitelné vlastnosti pro některý z modulů, jako je například výchozí hodnota, hodnota minValue a maxValue lze přidat na libovolný argument jako atributy, které mají **vlastnosti** elementu. Platné vlastnosti pro **vlastnosti** element závisí na typu argumentu a jsou popsané společně s typy argumentů podporovaných v další části. Argumenty s **Schedule** vlastnost nastavena na hodnotu **"true"** nevyžadují, aby uživatel zadal hodnotu. Pokud hodnota není k dispozici na argument, není argument předaný funkci vstupního bodu. Je nutné explicitně zacházet funkcí, třeba přiřadit výchozí hodnotu NULL v definici vstupní bod funkce argumenty funkci vstupního bodu, které jsou volitelné. Volitelný argument bude vynucovat, jenom dalších argumentů omezení, například min nebo max, pokud je uživatel zadal hodnotu.
Stejně jako u vstupy a výstupy, je velmi důležité, že parametry mají jedinečné id hodnoty k nim má přiřazené. V našem příkladu úvodní byla přidružená parametr/id *prohození*.

### <a name="arg-element"></a>Arg – element
Parametr modulu je definován pomocí **Arg** podřízený prvek **argumenty** část definičního souboru XML. Stejně jako u podřízené elementy v **porty** části pořadí parametrů **argumenty** oddíl definuje rozložení v uživatelské prostředí Parametry zobrazí shora dolů v uživatelském rozhraní ve stejném pořadí, ve kterém jsou definovány v souboru XML. Typy podporované nástrojem Machine Learning pro parametry jsou uvedeny zde. 

**int** – parametrem (32 bitů) typu celé číslo.

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *Volitelné vlastnosti*: **min**, **maximální**, **výchozí** a **Schedule**

**dvojité** – parametr typu double.

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* *Volitelné vlastnosti*: **min**, **maximální**, **výchozí** a **Schedule**

**BOOL** – parametr logické hodnoty, která je reprezentována zaškrtávací políčko uživatelské prostředí

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* *Volitelné vlastnosti*: **výchozí** -false v případě není nastavený.

**řetězec**: standardní řetězec

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* *Volitelné vlastnosti*: **výchozí** a **Schedule**

**ColumnPicker**: Parametr výběr sloupce. Tento typ se zobrazí v uživatelském prostředí jako výběr sloupců. **Vlastnost** element zde slouží k určení id portu, ze kterého jsou vybrané sloupce, kde cílový typ portu musí být *DataTable*. Výsledek výběr sloupce je předán do funkce R jako seznam řetězců obsahující názvy vybraných sloupců. 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *Požadované vlastnosti*: **identifikátor portId** -odpovídá id elementu Input s typem *DataTable*.
* *Volitelné vlastnosti*:
  
  * **allowedTypes** – filtry sloupci typy je možné vybrat. Platné hodnoty jsou: 
    
    * Čísla
    * Logická hodnota
    * Kategorické
    * Řetězec
    * Štítek
    * Funkce
    * Skóre
    * Vše
  * **výchozí** -platný výchozí výběry pro výběr sloupce zahrnují: 
    
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

**Rozevírací seznam**: uživatelem zadaný výčet (rozevírací seznam). Položky rozevíracího seznamu jsou uvedeny v rámci **vlastnosti** prvku pomocí **položky** elementu. **Id** pro každou **položky** musí být jedinečný a platná proměnná R. Hodnota **název** ze **položky** slouží jako text, který se zobrazí a hodnotu, která je předána funkci R.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* *Volitelné vlastnosti*:
  * **výchozí** -hodnota pro výchozí vlastnost musí odpovídat s hodnotou id z jednoho z **položky** elementy.

### <a name="auxiliary-files"></a>Pomocné soubory
Každý soubor, který je umístěn v souboru ZIP vlastní modul bude k dispozici pro použití při spuštění. Všechny adresáře struktury k dispozici jsou zachovány. To znamená, že tento soubor sourcing works stejné místně a v Azure Machine Learning spuštění. 

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
Prostředí pro spuštění skriptu R používá stejnou verzi jazyka R, jako **spustit skript jazyka R** modulu a můžete použít stejnou výchozí balíčky. Další balíčky r. na vlastní modul můžete také přidat jejich zahrnutím do vlastního modulu zip balíčku. Stačí je načte ve skriptu R stejně jako v prostředí R. 

**Omezení spouštěcí prostředí** patří:

* Dočasný soubor systému: Soubory zapsané při spuštění vlastního modulu nejsou trvalé během různých spuštění stejného modulu.
* Žádný přístup k síti

