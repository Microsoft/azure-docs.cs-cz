---
title: 'ML Studio (Classic): NET # Custom neuronové Networks – Azure'
description: 'Průvodce syntaxí pro jazyk specifikace NET # neuronové Networks Naučte se vytvářet vlastní neuronové síťové modely v Azure Machine Learning Studio (Classic).'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2018
ms.openlocfilehash: b4441c58838c3c44b01e57642e4433b12b2d1bc1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342216"
---
# <a name="guide-to-net-neural-network-specification-language-for-machine-learning-studio-classic"></a>Příručka k jazyku .NET # neuronové Network Specification pro Machine Learning Studio (Classic)

**platí pro:** ![ Ano ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ bez](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  

NET # je jazyk vyvinutý Microsoftem, který se používá k definování složitých neuronové síťových architektur, jako jsou například hluboké neuronové sítě nebo konvoluce libovolných dimenzí. Složité struktury můžete použít ke zlepšení učení o datech, jako je obrázek, video nebo zvuk.

V těchto kontextech můžete použít specifikaci architektury NET:

+ Všechny neuronové síťové moduly v Microsoft Azure Machine Learning Studio (Classic): [neuronové síť s více třídami](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/multiclass-neural-network), [síť neuronové se dvěma třídami](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/two-class-neural-network)a [neuronovéová regrese sítě](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/neural-network-regression)
+ Neuronové síťové funkce v Microsoft ML Server: [NeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/neuralnet) a [RxNeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxneuralnet)pro jazyk R a [rx_neural_network](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-neural-network) pro Python.


Tento článek popisuje základní koncepty a syntaxi potřebné k vývoji vlastní sítě neuronové pomocí rozhraní NET #:

+ Neuronové požadavky na síť a definování primárních součástí
+ Syntaxe a klíčová slova jazyka .NET # Specification
+ Příklady vlastních neuronové sítí vytvořených pomocí příkazu NET #



## <a name="neural-network-basics"></a>Základy sítě neuronové

Struktura sítě neuronové se skládá z uzlů, které jsou uspořádány do vrstev, a váženého počtu připojení (nebo hran) mezi uzly. Připojení jsou směrová a každé připojení má zdrojový uzel a cílový uzel.

Každá vlakové vrstva (skrytá nebo výstupní vrstva) má minimálně jednu sadu **prostředků připojení**. Svazek připojení se skládá ze zdrojové vrstvy a specifikace připojení z této zdrojové vrstvy. Všechna připojení v dané sadě prostředků sdílejí zdrojové a cílové vrstvy. V části NET # se sada prostředků připojení považuje za patřící do cílové vrstvy sady.

NET # podporuje různé druhy připojovacích sad, které umožňují přizpůsobit způsob, jakým jsou vstupy namapovány na skryté vrstvy a namapovány na výstupy.

Výchozí nebo standardní svazek je **Úplná sada**, ve které je každý uzel ve zdrojové vrstvě připojen ke každému uzlu v cílové vrstvě.

NET # navíc podporuje následující čtyři druhy pokročilých sad připojení:

+ **Filtrované sady prostředků**. Můžete definovat predikát pomocí umístění uzlu zdrojové vrstvy a cílového uzlu vrstvy. Uzly jsou připojeny vždy, když je predikát true.

+ **Konvoluční sady**. Můžete definovat malé okolí uzlů ve zdrojové vrstvě. Každý uzel v cílové vrstvě je připojen k jednomu okolí uzlů ve zdrojové vrstvě.

+ Sady **fondů** a **sady normalizace odpovědí**. Jedná se o podobné sady konvoluční v tom, že uživatel definuje malé okolí uzlů ve zdrojové vrstvě. Rozdílem je, že váhy hran v těchto svazekích nejsou vlakem. Místo toho se pro hodnoty zdrojového uzlu použije předdefinovaná funkce k určení hodnoty cílového uzlu.


## <a name="supported-customizations"></a>Podporované kustomizace

Architektura neuronovéch síťových modelů, které vytvoříte v Azure Machine Learning Studio (Classic), se dá výrazně přizpůsobit pomocí příkazu NET #. Další možnosti:

+ Vytváření skrytých vrstev a řízení počtu uzlů v jednotlivých vrstvách.
+ Určete, jak mají být vrstvy vzájemně propojeny.
+ Definujte speciální struktury připojení, například konvoluce a sady pro sdílení váhy.
+ Zadejte jiné aktivační funkce.

Podrobnosti o syntaxi jazyka specifikace najdete v tématu [specifikace struktury](#structure-specifications).

Příklady definování neuronové sítí pro některé běžné úkoly strojového učení, od simplex až po komplexní, najdete v tématu [Příklady](#examples-of-net-usage).

## <a name="general-requirements"></a>Obecné požadavky

+ Musí existovat přesně jedna výstupní vrstva, aspoň jedna vstupní vrstva a nula nebo více skrytých vrstev.
+ Každá vrstva má pevný počet uzlů, který je koncepčně uspořádán v obdélníkovém poli libovolných dimenzí.
+ Vstupní vrstvy neobsahují žádné přidružené parametry, které by představovaly bod, ve kterém data instance vstupují do sítě.
+ Vlakové vrstvy (skryté a výstupní vrstvy) mají přidružené výukové parametry, označované jako váhy a posuny.
+ Zdrojový a cílový uzel musí být v samostatných vrstvách.
+ Připojení musí být acyklického; Jinými slovy, neexistuje žádný řetěz připojení, který vede zpět na počáteční zdrojový uzel.
+ Výstupní vrstva nemůže být zdrojovou vrstvou sady prostředků připojení.

## <a name="structure-specifications"></a>Specifikace struktury

Specifikace struktury sítě neuronové se skládá ze tří částí: **deklarace konstanty**, **deklarace vrstvy**, **deklarace připojení**. K dispozici je také volitelný oddíl **deklarace sdílení** . Oddíly lze zadat v libovolném pořadí.

## <a name="constant-declaration"></a>Konstantní deklarace

Deklarace konstanty je volitelná. Poskytuje způsob, jak definovat hodnoty používané jinde v definici sítě neuronové. Příkaz deklarace se skládá z identifikátoru následovaného rovnítkem a výrazem hodnoty.

Například následující příkaz definuje konstantu `x` :

`Const X = 28;`

Pro definování dvou nebo více konstant současně vložte názvy identifikátorů a hodnot do složených závorek a oddělte je pomocí středníků. Příklad:

`Const { X = 28; Y = 4; }`

Pravá strana každého výrazu přiřazení může být celé číslo, reálné číslo, logická hodnota (true nebo false) nebo matematický výraz. Příklad:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Deklarace vrstvy

Deklarace vrstvy je povinná. Definuje velikost a zdroj vrstvy, včetně jejích sad připojení a atributů. Příkaz deklarace začíná názvem vrstvy (vstup, skrytý nebo výstup) následovaný rozměry vrstvy (řazené kolekce členů s kladnými celými čísly). Příklad:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;
```

+ Součin dimenzí je počet uzlů ve vrstvě. V tomto příkladu existují dvě dimenze [5, 20], což znamená, že ve vrstvě jsou 100 uzly.
+ Vrstvy lze deklarovat v libovolném pořadí s jednou výjimkou: je-li definována více než jedna vstupní vrstva, pořadí, ve kterém jsou deklarovány, musí odpovídat pořadí funkcí ve vstupních datech.

Chcete-li určit, že počet uzlů ve vrstvě má být určen automaticky, použijte `auto` klíčové slovo. `auto`Klíčové slovo má jiné účinky v závislosti na vrstvě:

+ V deklaraci vstupní vrstvy je počet uzlů počtem funkcí ve vstupních datech.
+ V případě skryté deklarace vrstvy je počet uzlů číslo, které je zadáno hodnotou parametru pro **Počet skrytých uzlů**.
+ V deklaraci výstupní vrstvy je počet uzlů 2 pro klasifikaci dvou tříd, 1 pro regresi a roven počtu výstupních uzlů pro klasifikaci s více třídami.

Například následující definice sítě umožňuje, aby se automaticky určila velikost všech vrstev:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;
```

Deklarace vrstvy pro vlakovou vrstvu (skryté nebo výstupní vrstvy) může volitelně zahrnovat výstupní funkci (označovanou také jako aktivační funkce), která se standardně **sigmoid** pro klasifikace modelů a **lineární** pro regresní modely. I v případě, že použijete výchozí hodnotu, můžete explicitně uvést funkci aktivace, pokud je to potřeba pro přehlednost.

Podporují se následující výstupní funkce:

+ sigmoid
+ lineární
+ softmax
+ rlinear
+ čtvercové
+ SQRT
+ srlinear
+ ABS
+ tanh –
+ brlinear

Například následující deklarace používá funkci **softmax** :

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>Deklarace připojení

Ihned po definování vlakové vrstvy je nutné deklarovat připojení mezi vrstvami, které jste definovali. Deklarace sady prostředků připojení začíná klíčovým slovem `from` následovaným názvem zdrojové vrstvy svazku a typem sady připojení, která se má vytvořit.

V současné době jsou podporovány pět druhů sad připojení:

+ **Úplné** sady, označené klíčovým slovem `all`
+ **Filtrované** sady, označené klíčovým slovem `where` a výrazem predikátu
+ **Konvoluční** sady, které jsou označeny klíčovým slovem a jsou `convolve` následovány atributy konvoluce
+ **Sdružování** svazků, které jsou označeny klíčovým slovem **Maximum** nebo **střední fond**
+ Sady pro **normalizaci odpovědí** označené klíčovým slovem **Response norma**

## <a name="full-bundles"></a>Úplné sady prostředků

Sada kompletních připojení zahrnuje připojení z každého uzlu zdrojové vrstvy ke každému uzlu v cílové vrstvě. Toto je výchozí typ síťového připojení.

## <a name="filtered-bundles"></a>Filtrované sady prostředků

Filtrovaná specifikace sady prostředků připojení obsahuje predikát vyjádřený syntakticky, podobně jako výraz lambda jazyka C#. Následující příklad definuje dvě filtrované sady prostředků:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
```

+ V predikátu pro `ByRow` `s` je parametr reprezentující index do obdélníkového pole uzlů vstupní vrstvy, `Pixels` a `d` je parametr reprezentující index do pole uzlů skryté vrstvy `ByRow` . Typ `s` a `d` je řazená kolekce členů s celými čísly s délkou 2. V koncepčních `s` oblastech rozsahy přes všechny páry celých čísel s `0 <= s[0] < 10` a a `0 <= s[1] < 20` `d` rozsahy přes všechny páry celých čísel, s `0 <= d[0] < 10` a `0 <= d[1] < 12` .

+ Na pravé straně výrazu predikátu existuje podmínka. V tomto příkladu pro každou hodnotu a, `s` `d` aby byla podmínka pravdivá, existuje okraj z uzlu zdrojové vrstvy do cílového uzlu vrstvy. Proto tento výraz filtru označuje, že sada obsahuje připojení z uzlu definovaného `s` do uzlu definovaného ve `d` všech případech, kde s [0] je rovno d [0].

Volitelně můžete zadat sadu vah pro filtrovanou sadu. Hodnota atributu **váhy** musí být řazené kolekce členů hodnot s plovoucí desetinnou čárkou s délkou, která odpovídá počtu připojení definovaných v rámci sady. Ve výchozím nastavení se závaží náhodně generují.

Hodnoty váhy jsou seskupené podle indexu cílového uzlu. To znamená, že pokud je první cílový uzel připojen k zdrojovým uzlům, první `K` prvky n- **Weights** tice jsou váhy pro první cílový uzel v pořadí zdrojového indexu. Totéž platí pro zbývající cílové uzly.

Je možné zadat váhy přímo jako konstantní hodnoty. Pokud jste například dříve naučili váhy, můžete je zadat jako konstanty pomocí této syntaxe:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>Konvoluční sady

Pokud jsou školicí data v homogenní struktuře, často se používají připojení konvoluční k učení funkcí vysoké úrovně dat. Například v obrazovém, zvukovém nebo obrazovém datovém zařízení může být prostorová nebo dočasná dimenze poměrně jednotná.

Konvoluční sady využívají pravoúhlá **jádra** , která jsou SLID prostřednictvím dimenzí. V podstatě každá jádro definuje sadu vah aplikovaných v místních prostředích, označovaných jako **aplikace jádra**. Každá aplikace jádra odpovídá uzlu ve zdrojové vrstvě, který je označován jako **centrální uzel**. Váhy jádra se sdílejí mezi mnoha připojeními. U konvoluční sady je každé jádro obdélníkové a všechny aplikace jádra mají stejnou velikost.

Sady konvoluční podporují následující atributy:

**InputShape** definuje dimenzionální zdrojovou vrstvu pro účely tohoto balíčku konvoluční. Hodnota musí být řazené kolekce členů kladných celých čísel. Součin celých čísel musí být stejný jako počet uzlů ve zdrojové vrstvě, ale v opačném případě nemusí odpovídat rozměru deklarovanému pro zdrojovou vrstvu. Délka této řazené kolekce členů se bude hodnotou **aritou** pro sadu konvoluční. Typicky Arita odkazuje na počet argumentů nebo operandů, které může funkce provést.

Chcete-li definovat tvar a umístění jader, použijte atributy **KernelShape**, **Rozteč**, **odsazení**, **LowerPad**a **UpperPad**:

+ **KernelShape**: (povinné) definuje dimenzionální pro každé jádro sady konvoluční. Hodnota musí být řazené kolekce členů kladných celých čísel s délkou, která se rovná aritou sady. Každá součást této řazené kolekce členů nesmí být větší než odpovídající součást **InputShape**.

+ **Rozteč**: (volitelné) definuje velikost klouzavého kroku u konvoluce (jedna velikost kroku pro každou dimenzi), což je vzdálenost mezi centrálními uzly. Hodnota musí být řazené kolekce členů kladných celých čísel s délkou, která je aritou sady. Každá součást této řazené kolekce členů nesmí být větší než odpovídající součást **KernelShape**. Výchozí hodnota je řazená kolekce členů se všemi komponentami, které se rovnají jedné.

+ **Sdílení**: (volitelné) definuje váhu sdílení pro každou dimenzi konvoluce. Hodnotou může být jedna logická hodnota nebo n-tice logických hodnot s délkou, která je aritou sady. Jedna logická hodnota je rozšířena tak, aby byla řazená kolekce členů se správnou délkou se všemi komponentami, které se rovnají zadané hodnotě. Výchozí hodnota je řazená kolekce členů, která se skládá ze všech hodnot true.

+ **MapCount**: (nepovinný) definuje počet map funkcí pro sadu prostředků konvoluční. Hodnota může být jedno kladné celé číslo nebo řazená kolekce členů kladných celých čísel s délkou, která je aritou sady. Jedna celočíselná hodnota je rozšířena tak, aby byla řazená kolekce členů správné délky s prvními součástmi rovny zadané hodnotě a všemi zbývajícími součástmi, které se rovnají jedné. Výchozí hodnota je jedna. Celkový počet map funkcí je produktem součástí řazené kolekce členů. Faktoring tohoto celkového čísla napříč komponentami určuje, jak se hodnoty map funkcí seskupují v cílových uzlech.

+ **Závaží**: (volitelné) definuje počáteční váhu sady. Hodnota musí být řazené kolekce členů hodnot s plovoucí desetinnou čárkou s délkou, která představuje počet jader krát počtem vah na jádro, jak je definováno dále v tomto článku. Výchozí tloušťky se generují náhodně.

Existují dvě sady vlastností, které řídí odsazení, vlastnosti, které se vzájemně vylučují:

+ **Odsazení**: (volitelné) určuje, zda má být vstup doplněn pomocí **výchozího schématu odsazení**. Hodnota může být jedna logická hodnota, nebo může být řazená kolekce logických hodnot s délkou, která je aritou sady.

    Jedna logická hodnota je rozšířena tak, aby byla řazená kolekce členů se správnou délkou se všemi komponentami, které se rovnají zadané hodnotě.

    Pokud je hodnota pro dimenzi true, zdroj je logicky doplněn v této dimenzi s nulovými buňkami, aby podporovaly další aplikace jádra, jako je první a poslední uzel v této dimenzi v dané dimenzi na zdrojové vrstvě. Proto je počet "fiktivních" uzlů v každé dimenzi určen automaticky, aby vyhovovaly přesně `(InputShape[d] - 1) / Stride[d] + 1` jádrům do čalouněné zdrojové vrstvy.

    Je-li hodnota pro dimenzi false, jsou definovány jádro, aby počet uzlů na každé straně, které jsou vycházející z něj, byl stejný (až do rozdílu 1). Výchozí hodnota tohoto atributu je řazená kolekce členů se všemi komponentami, které se rovnají hodnotě false.

+ **UpperPad** a **LowerPad**: (volitelné) poskytují větší kontrolu nad množstvím odsazení, které se má použít. **Důležité informace:** Tyto atributy lze definovat, pokud a pouze v případě, že ***není*** definována vlastnost **odsazení** výše. Hodnoty by měly být celočíselné řazené kolekce členů s délkami, které jsou aritou sady. Při zadání těchto atributů jsou "fiktivní" uzly přidány do dolního a horního konce každé dimenze vstupní vrstvy. Počet uzlů přidaných do dolních a horních konců v každé dimenzi se určuje podle **LowerPad**[i] a **UpperPad**[i] v uvedeném pořadí.

    Chcete-li zajistit, že jádra odpovídají pouze skutečným uzlům a nikoli k uzlům "fiktivních", musí být splněny následující podmínky:
  - Každá součást **LowerPad** musí být výhradně menší než `KernelShape[d]/2` .
  - Každá součást **UpperPad** nesmí být větší než `KernelShape[d]/2` .
  - Výchozí hodnota těchto atributů je řazená kolekce členů se všemi komponentami rovnými 0.

    Nastavení **odsazení** = true umožňuje tolik odsazení, kolik je potřeba k udržení "středu" jádra uvnitř "reálného" vstupu. Tím se v matematickém výpočtu vypočítá bit velikosti výstupu. Obecně je výstupní velikost *D* vypočítána jako `D = (I - K) / S + 1` , kde `I` je vstupní velikost, `K` je velikost jádra, `S` je rozteč a `/` je celočíselné dělení (zaokrouhlení směrem k nule). Pokud nastavíte UpperPad = [1, 1], vstupní velikost `I` je efektivně 29, a tak dále `D = (29 - 5) / 2 + 1 = 13` . Nicméně, pokud je **odsazení** = true, v podstatě `I` vyplní `K - 1` `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14` . proto. Zadáním hodnot pro **UpperPad** a **LowerPad** získáte mnohem větší kontrolu nad odsazením, než když pouze nastavíte **odsazení** = true.

Další informace o sítích konvoluční a jejich aplikacích najdete v těchto článcích:

+ [http://deeplearning.net/tutorial/lenet.html](http://deeplearning.net/tutorial/lenet.html)
+ [https://research.microsoft.com/pubs/68920/icdar03.pdf](https://research.microsoft.com/pubs/68920/icdar03.pdf)

## <a name="pooling-bundles"></a>Sdružování svazků

**Sada fondů** používá geometrii podobnou konvoluční připojení, ale používá předdefinované funkce pro hodnoty zdrojového uzlu k odvození hodnoty cílového uzlu. Fondy proto nesmí obsahovat žádné výukové stavy (váhy nebo posuny). Sdružování svazků podporuje všechny atributy konvoluční s výjimkou **sdílení**, **MapCount**a **vah**.

Jádra sumarizovaná sousedními jednotkami sdružování se obvykle nepřekrývají. Pokud je mezera [d] rovna KernelShape [d] v každé dimenzi, je získaná vrstva tradiční místní vrstvou, která je běžně zaměstnaná v sítích konvoluční neuronové. Každý cílový uzel vypočítá maximální nebo průměr aktivity jeho jádra ve zdrojové vrstvě.

Následující příklad znázorňuje skupinu sdružování:

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }
```

+ Arita sady prostředků je 3: to znamená délka řazených kolekcí členů `InputShape` , `KernelShape` a `Stride` .
+ Počet uzlů ve zdrojové vrstvě je `5 * 24 * 24 = 2880` .
+ Jedná se o tradiční vrstvu s místními fondy, protože **KernelShape** a **Rozteč** jsou stejné.
+ Počet uzlů v cílové vrstvě je `5 * 12 * 12 = 1440` .

Další informace o vrstvách sdružování najdete v těchto článcích:

+ [https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Oddíl 3,4)
+ [https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf)
+ [https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Sady pro normalizaci odpovědí

**Normalizace odpovědí** je místní schéma normalizace, které bylo poprvé zavedeno pomocí Geoffrey Hinton, et al, v [klasifikaci papírového ImageNetu s hlubokými konvolučními neuronovémi sítěmi](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf).

Normalizace odezvy se používá k podpoře generalizace v neuronové sítích. Když se jedna neuron vychází na úrovni vysoké úrovně aktivace, potlačí se úroveň aktivace okolního neurons vrstvy pro normalizaci odezvy. K tomu je potřeba použít tři parametry ( `α` , a `β` `k` ) a strukturu konvoluční (neboli okolní tvar). Každý neuron v cílové vrstvě **y** odpovídá neuron **x** ve zdrojové vrstvě. Úroveň aktivace **y** je dána následujícím vzorcem, kde `f` je úroveň aktivace neuron a `Nx` je jádrem (nebo sadou, která obsahuje neurons v okolí **x**), jak je definováno následující strukturou konvoluční:

![vzorec pro strukturu konvoluční](./media/azure-ml-netsharp-reference-guide/formula_large.png)

Normalizované balíčky odezvy podporují všechny atributy konvoluční s výjimkou **sdílení**, **MapCount**a **vah**.

+ Pokud jádro obsahuje neurons ve stejné mapě jako ***x***, schéma normalizace se označuje jako **normalizace v mapě**. Pro definování stejné normalizace mapování musí mít první souřadnice v **InputShape** hodnotu 1.

+ Pokud jádro obsahuje neurons ve stejné prostorové pozici jako ***x***, ale neurons jsou v jiných mapách, je schéma normalizace voláno **napříč normalizací map**. Tento typ normalizace odezvy implementuje formu nechte inspirovatho inhibice podle typu nalezeného v reálných neuronsch, což vytváří konkurenci pro úrovně velkých aktivací mezi neuron výstupy vypočítanými v různých mapách. Chcete-li definovat napříč normalizačními mapami, první souřadnice musí být celé číslo větší než jedna a nesmí být větší než počet map a zbytek souřadnic musí mít hodnotu 1.

Vzhledem k tomu, že sady pro normalizaci odpovědí používají předdefinované funkce na hodnoty zdrojového uzlu k určení hodnoty cílového uzlu, nemají žádný stav vlaku (váhy nebo posuny).

> [!NOTE]
> Uzly v cílové vrstvě odpovídají neurons, které jsou centrálními uzly jader. Například pokud `KernelShape[d]` je lichá, pak `KernelShape[d]/2` odpovídá centrálnímu uzlu jádra. Pokud `KernelShape[d]` je i, centrální uzel je v `KernelShape[d]/2 - 1` . Proto pokud `Padding[d]` je hodnota false, první a poslední uzel nemají `KernelShape[d]/2` odpovídající uzly v cílové vrstvě. Chcete-li se této situaci vyhnout, definujte **odsazení** jako [true, true,..., true].

Kromě výše uvedených čtyř atributů podporují normalizované sady odpovědí také následující atributy:

+ **Alfa**: (povinné) určuje hodnotu s plovoucí desetinnou čárkou, která odpovídá `α` předchozímu vzorci.
+ **Beta**: (povinné) určuje hodnotu s plovoucí desetinnou čárkou, která odpovídá `β` předchozímu vzorci.
+ **Offset**: (volitelné) určuje hodnotu s plovoucí desetinnou čárkou, která odpovídá `k` předchozímu vzorci. Výchozí hodnota je 1.

V následujícím příkladu je definována normalizovaná sada odpovědí pomocí těchto atributů:

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }
```

+ Zdrojová vrstva obsahuje pět map, z nichž každý má AOF dimenzi 12x12, součet v 1440 uzlech.
+ Hodnota **KernelShape** značí, že se jedná o stejnou normalizační vrstvu mapy, kde okolí je obdélník 3x3.
+ Výchozí hodnota **odsazení** je false, takže cílová vrstva má v každé dimenzi pouze 10 uzlů. Chcete-li zahrnout jeden uzel do cílové vrstvy, který odpovídá každému uzlu ve zdrojové vrstvě, přidejte odsazení = [true, true, true]; a změňte velikost RN1 na [5, 12, 12].

## <a name="share-declaration"></a>Sdílet deklaraci

NET # volitelně podporuje definování více sad se sdílenými závažími. Váhy všech dvou svazků lze sdílet, pokud jsou jejich struktury stejné. Následující syntaxe definuje sady se sdílenými závažími:

```Net#
share-declaration:
  share    {    layer-list    }
  share    {    bundle-list    }
  share    {    bias-list    }

  layer-list:
    layer-name    ,    layer-name
    layer-list    ,    layer-name

  bundle-list:
    bundle-spec    ,    bundle-spec
    bundle-list    ,    bundle-spec

  bundle-spec:
    layer-name    =>     layer-name

  bias-list:
    bias-spec    ,    bias-spec
    bias-list    ,    bias-spec

  bias-spec:
    1    =>    layer-name

  layer-name:
    identifier
```

Například následující deklarace Share-určuje názvy vrstev, což značí, že se mají sdílet jak závaží, tak i bias:

```Net#
Const {
  InputSize = 37;
  HiddenSize = 50;
  }
input {
  Data1 [InputSize];
  Data2 [InputSize];
  }
hidden {
  H1 [HiddenSize] from Data1 all;
  H2 [HiddenSize] from Data2 all;
  }
output Result [2] {
  from H1 all;
  from H2 all;
  }
share { H1, H2 } // share both weights and biases
```

+ Vstupní funkce jsou rozdělené do dvou vstupních vrstev se stejnou velikostí.
+ Skryté vrstvy pak budou funkce vyšší úrovně COMPUTE na dvou vstupních vrstvách.
+ Deklarace Share-určuje, že *H1* a *H2* musí být počítány stejným způsobem než jejich příslušné vstupy.

Případně můžete tuto situaci zadat se dvěma samostatnými deklaracemi Share-, jak je znázorněno níže:

```Net#
share { Data1 => H1, Data2 => H2 } // share weights
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases
```

Můžete použít krátký tvar pouze v případě, že vrstvy obsahují jednu sadu prostředků. Obecně platí, že sdílení je možné pouze v případě, že je příslušná struktura shodná, což znamená, že mají stejnou velikost, stejnou geometrii konvoluční a tak dále.

## <a name="examples-of-net-usage"></a>Příklady využití NET #

V této části najdete několik příkladů, jak můžete pomocí rozhraní NET # přidat skryté vrstvy, definovat způsob, jakým budou skryté vrstvy pracovat s jinými vrstvami, a vytvářet konvoluční sítě.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Definování jednoduché vlastní neuronové sítě: příklad "Hello World"

Tento jednoduchý příklad ukazuje, jak vytvořit model sítě neuronové, který má jednu skrytou vrstvu.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;
```

Tento příklad znázorňuje některé základní příkazy následujícím způsobem:

+ První řádek definuje vstupní vrstvu (pojmenovanou `Data` ). Když použijete  `auto` klíčové slovo, síť neuronové automaticky zahrne všechny sloupce funkcí ve vstupních příkladech.
+ Druhá čára vytvoří skrytou vrstvu. Název `H` je přiřazen ke skryté vrstvě, která má 200 uzlů. Tato vrstva je plně připojená ke vstupní vrstvě.
+ Třetí řádek definuje výstupní vrstvu (s názvem `Out` ), která obsahuje 10 výstupních uzlů. Pokud se neuronové síť používá pro klasifikaci, existuje jeden výstupní uzel na jednu třídu. Klíčové slovo **sigmoid** označuje, že funkce Output je použita na výstupní vrstvu.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Definovat více skrytých vrstev: příklad počítačové vize

Následující příklad ukazuje, jak definovat mírně komplexnější neuronové síť s více vlastními skrytými vrstvami.

```Net#
// Define the input layers
input Pixels [10, 20];
input MetaData [7];

// Define the first two hidden layers, using data only from the Pixels input
hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;

// Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
hidden Gather [100]
{
from ByRow all;
from ByCol all;
}

// Define the output layer and its sources
output Result [10]
{
from Gather all;
from MetaData all;
}
```

Tento příklad znázorňuje několik funkcí jazyka specifikace neuronové Networks:

+ Struktura má dvě vstupní vrstvy `Pixels` a `MetaData` .
+ `Pixels`Vrstva je zdrojová vrstva pro dvě sady připojení, s cílovými vrstvami `ByRow` a `ByCol` .
+ Vrstvy `Gather` a `Result` cílové vrstvy ve více sad pro připojení.
+ Výstupní vrstva, `Result` , je cílová vrstva ve dvou sad pro připojení; jedna s skrytou vrstvou druhé úrovně `Gather` jako cílovou vrstvou a druhá s vstupní vrstvou `MetaData` jako cílovou vrstvou.
+ Skryté vrstvy `ByRow` a `ByCol` , určete filtrované připojení pomocí výrazů predikátů. Přesněji, uzel v `ByRow` umístění [x, y] je připojen k uzlům v `Pixels` , které mají první souřadnici indexu rovnající se první souřadnici uzlu, x. Podobně je uzel v `ByCol` umístění [x, y] připojen k uzlům v `Pixels` , které mají druhou souřadnici indexu v rámci jedné z jeho druhé souřadnice (y) uzlu.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Definování sítě konvoluční pro třídu klasifikace s více třídami: příklad rozpoznávání číslic

Definice následující sítě je navržena tak, aby rozpoznala čísla, a znázorňuje některé pokročilé techniky pro přizpůsobení neuronové sítě.

```Net#
input Image [29, 29];
hidden Conv1 [5, 13, 13] from Image convolve
  {
  InputShape  = [29, 29];
  KernelShape = [ 5,  5];
  Stride      = [ 2,  2];
  MapCount    = 5;
  }
hidden Conv2 [50, 5, 5]
from Conv1 convolve
  {
  InputShape  = [ 5, 13, 13];
  KernelShape = [ 1,  5,  5];
  Stride      = [ 1,  2,  2];
  Sharing     = [false, true, true];
  MapCount    = 10;
  }
hidden Hid3 [100] from Conv2 all;
output Digit [10] from Hid3 all;
```

+ Struktura má jednu vstupní vrstvu, `Image` .
+ Klíčové slovo `convolve` označuje, že vrstvy s názvem `Conv1` a `Conv2` jsou konvoluční vrstvy. Každá z těchto deklarací vrstev následuje seznam atributů konvoluce.
+ Síť obsahuje třetí skrytou vrstvu, `Hid3` , která je plně připojená k druhé skryté vrstvě, `Conv2` .
+ Výstupní vrstva, `Digit` je připojena pouze k třetí skryté vrstvě, `Hid3` . Klíčové slovo `all` označuje, že výstupní vrstva je plně připojená k `Hid3` .
+ Arita konvoluce je tři: délka řazených kolekcí členů `InputShape` , `KernelShape` , `Stride` a `Sharing` .
+ Počet vah na jádro je `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26` . Nebo `26 * 50 = 1300` .
+ Uzly v každé z skrytých vrstev můžete vypočítat následujícím způsobem:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5`
    `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ Celkový počet uzlů lze vypočítat pomocí deklarované dimenzionální velikosti vrstvy [50, 5, 5] následujícím způsobem: `MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ Protože `Sharing[d]` je hodnota false pouze pro `d == 0` , je počet jader `MapCount * NodeCount\[0] = 10 * 5 = 50` .

## <a name="acknowledgements"></a>Poděkování

Jazyk NET # pro přizpůsobení architektury sítí neuronové vyvinula společnost Microsoft o shon Katzenberger (architekt, Machine Learning) a Alexey Kamenev (softwarový inženýr, Microsoft Research). Používá se interně pro projekty strojového učení a aplikace od detekce obrázků až po textovou analýzu. Další informace najdete v tématu [neuronové sítě v Azure Machine Learning Studiu – Úvod do NET #](https://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx) .
