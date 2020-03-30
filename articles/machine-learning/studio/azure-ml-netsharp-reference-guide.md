---
title: Vlastní neuronové sítě Net#
titleSuffix: ML Studio (classic) - Azure
description: Průvodce syntaxí pro jazyk specifikace neuronových sítí Net# . Zjistěte, jak vytvořit vlastní modely neuronové sítě v Azure Machine Learning Studio (klasické).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2018
ms.openlocfilehash: c1912e670a9cf1c178b58cefbd33171f15be2483
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218254"
---
# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning-studio-classic"></a>Průvodce jazykem specifikace neuronové sítě Net# pro Azure Machine Learning Studio (klasický)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Net# je jazyk vyvinutý společností Microsoft, který se používá k definování složitých architektur neuronové sítě, jako jsou hluboké neuronové sítě nebo konvoluce libovolných dimenzí. Pomocí složitých struktur můžete zlepšit učení o datech, jako je obrázek, video nebo zvuk.

Specifikace architektury Net# můžete použít v těchto kontextech:

+ Všechny moduly neuronové sítě v Microsoft Azure Machine Learning Studio (klasické): [Vícetřídní neuronová síť](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/multiclass-neural-network), [dvoutřídní neuronová síť](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/two-class-neural-network)a [regrese neuronové sítě](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/neural-network-regression)
+ Funkce neuronové sítě v Microsoft ML Server: [NeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/neuralnet) a [rxNeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxneuralnet)pro jazyk R a [rx_neural_network](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-neural-network) pro Python.


Tento článek popisuje základní koncepty a syntaxe potřebné k vývoji vlastní neuronové sítě pomocí Net#:

+ Požadavky na neuronovou síť a jak definovat primární součásti
+ Syntaxe a klíčová slova jazyka specifikace Net#
+ Příklady vlastních neuronových sítí vytvořených pomocí sítě #



## <a name="neural-network-basics"></a>Základy neuronové sítě

Struktura neuronové sítě se skládá z uzlů, které jsou uspořádány ve vrstvách a vážené připojení (nebo hrany) mezi uzly. Připojení jsou směrové a každé připojení má zdrojový uzel a cílový uzel.

Každá trénovatelná vrstva (skrytá nebo výstupní vrstva) má jeden nebo více **svazků připojení**. Sada připojení se skládá ze zdrojové vrstvy a specifikace připojení z této zdrojové vrstvy. Všechna připojení v dané vrstvě sdílení sdílené položky a cílové vrstvy sady. V net#, připojení balíček je považován za patřící do cílové vrstvy balíčku.

Net# podporuje různé druhy připojení svazků, které umožňují přizpůsobit způsob, jakým jsou vstupy mapovány do skrytých vrstev a mapovány na výstupy.

Výchozí nebo standardní balíček je **úplný balíček**, ve kterém je každý uzel ve zdrojové vrstvě připojen ke každému uzlu v cílové vrstvě.

Síť Net# navíc podporuje následující čtyři druhy rozšířených svazků připojení:

+ **Filtrované svazky**. Predikát můžete definovat pomocí umístění uzlu zdrojové vrstvy a uzlu cílové vrstvy. Uzly jsou připojeny vždy, když je predikát True.

+ **Konvoluční svazky**. Ve zdrojové vrstvě můžete definovat malé čtvrti uzlů. Každý uzel v cílové vrstvě je připojen k jednomu sousedství uzlů ve zdrojové vrstvě.

+ **Sdružování svazků** a **balíčků normalizace odezvy**. Ty jsou podobné konvoluční svazky v tom, že uživatel definuje malé sousedství uzlů ve zdrojové vrstvě. Rozdíl je v tom, že hmotnosthrany v těchto svazcích nejsou trénovatelné. Místo toho je předdefinovaná funkce použita na hodnoty zdrojového uzlu k určení hodnoty cílového uzlu.


## <a name="supported-customizations"></a>Podporované kustomizace

Architekturu modelů neuronové sítě, které vytvoříte v Azure Machine Learning Studio (klasické) lze značně přizpůsobit pomocí Net#. Můžete:

+ Vytvořte skryté vrstvy a ovládejte počet uzlů v každé vrstvě.
+ Určete, jak mají být vrstvy vzájemně propojeny.
+ Definujte speciální struktury připojení, jako jsou konvoluce a balíčky pro sdílení hmotnosti.
+ Zadejte různé aktivační funkce.

Podrobnosti o syntaxi jazyka specifikace naleznete v [tématu Specifikace struktury](#structure-specifications).

Příklady definování neuronových sítí pro některé běžné úlohy strojového učení, od simplexu po komplex, najdete v [tématu Příklady](#examples-of-net-usage).

## <a name="general-requirements"></a>Obecné požadavky

+ Musí existovat přesně jedna výstupní vrstva, alespoň jedna vstupní vrstva a nula nebo více skrytých vrstev.
+ Každá vrstva má pevný počet uzlů, koncepčně uspořádaných do obdélníkového pole libovolných dimenzí.
+ Vstupní vrstvy nemají žádné přidružené trénované parametry a představují bod, kde data instance vstupují do sítě.
+ Trénovatelné vrstvy (skryté a výstupní vrstvy) mají přidružené trénované parametry, známé jako váhy a zkreslení.
+ Zdrojové a cílové uzly musí být v samostatných vrstvách.
+ Připojení musí být acyklické; jinými slovy nemůže existovat řetěz připojení vedoucí zpět do počátečního zdrojového uzlu.
+ Výstupní vrstva nemůže být zdrojovou vrstvou svazku připojení.

## <a name="structure-specifications"></a>Specifikace konstrukce

Specifikace struktury neuronové sítě se skládá ze tří částí: **konstantní deklarace**, **deklarace vrstvy**, **deklarace připojení**. K dispozici je také volitelný oddíl **prohlášení o sdílené složce.** Sekce lze zadat v libovolném pořadí.

## <a name="constant-declaration"></a>Konstantní prohlášení

Konstantní deklarace je nepovinná. Poskytuje prostředky k definování hodnot používaných jinde v definici neuronové sítě. Prohlášení se skládá z identifikátoru následovaného rovnítkem a výrazem hodnoty.

Například následující příkaz definuje konstantu `x`:

`Const X = 28;`

Chcete-li definovat dvě nebo více konstant současně, uzavřete názvy identifikátorů a hodnoty do složených závorek a oddělte je pomocí středníků. Například:

`Const { X = 28; Y = 4; }`

Pravá strana každého výrazu přiřazení může být celé číslo, reálné číslo, logická hodnota (Pravda nebo Nepravda) nebo matematický výraz. Například:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Deklarace vrstvy

Je vyžadována deklarace vrstvy. Definuje velikost a zdroj vrstvy, včetně jejích svazků připojení a atributů. Příkaz deklarace začíná názvem vrstvy (vstup, skrytý nebo výstup), následovaný rozměry vrstvy (řazená kolekce členů kladných celá čísla). Například:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;
```

+ Součinem dimenzí je počet uzlů ve vrstvě. V tomto příkladu existují dvě dimenze [5,20], což znamená, že ve vrstvě je 100 uzlů.
+ Hladiny mohou být deklarovány v libovolném pořadí, s jednou výjimkou: Pokud je definována více než jedna vstupní vrstva, pořadí, ve kterém jsou deklarovány, musí odpovídat pořadí prvků ve vstupních datech.

Chcete-li určit, že počet uzlů ve vrstvě `auto` bude určen automaticky, použijte klíčové slovo. Klíčové `auto` slovo má různé efekty, v závislosti na vrstvě:

+ V deklaraci vstupní vrstvy je počet uzlů počet funkcí ve vstupních datech.
+ V deklaraci skryté vrstvy je počet uzlů číslo, které je určeno hodnotou parametru pro **Počet skrytých uzlů**.
+ V deklaraci výstupní vrstvy je počet uzlů 2 pro dvoutřídní klasifikaci, 1 pro regresi a rovna počtu výstupních uzlů pro klasifikaci více tříd.

Například následující definice sítě umožňuje automaticky určit velikost všech vrstev:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;
```

Deklarace vrstvy pro trénovatelnou vrstvu (skryté nebo výstupní vrstvy) může volitelně obsahovat výstupní funkci (označovanou také jako aktivační funkce), která je výchozí pro **sigmoid** pro klasifikační modely a **lineární** pro regresní modely. I v případě, že použijete výchozí, můžete explicitně uvést funkci aktivace, pokud je to žádoucí pro přehlednost.

Podporovány jsou následující výstupní funkce:

+ Sigmoid
+ Lineární
+ softmax
+ rlineární
+ Náměstí
+ Sqrt
+ srlinear
+ Abs
+ tanh
+ brlineární

Například následující deklarace používá funkci **softmax:**

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>Prohlášení o připojení

Ihned po definování zaváděcí vrstvy je nutné deklarovat spojení mezi vrstvami, které jste definovali. Deklarace svazku připojení `from`začíná klíčovým slovem , následovaným názvem zdrojové vrstvy balíčku a druhem svazku připojení, který chcete vytvořit.

V současné době je podporováno pět druhů svazků připojení:

+ **Úplné** balíčky označené klíčovým slovem`all`
+ **Filtrované** svazky označené klíčovým `where`slovem , následované predikátovým výrazem
+ **Konvoluční** svazky označené klíčovým slovem `convolve`, následované atributy konvoluce
+ **Sdružování** svazků označených klíčovými slovy **max pool** nebo **střední fond**
+ Balíčky **pro normalizaci odpovědí,** označené **normou odezvy** klíčových slov

## <a name="full-bundles"></a>Kompletní balíčky

Úplná sada připojení obsahuje připojení z každého uzlu ve zdrojové vrstvě ke každému uzlu v cílové vrstvě. Toto je výchozí typ síťového připojení.

## <a name="filtered-bundles"></a>Filtrované svazky

Specifikace sady filtrovanépřipojení obsahuje predikát, vyjádřený syntakticky, podobně jako výraz lambda jazyka C#. Následující příklad definuje dva filtrované svazky:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
```

+ V predikátu `ByRow`pro `s` je parametr představující index do obdélníkového pole uzlů vstupní `Pixels`vrstvy `d` a je parametrem představujícím index do pole uzlů `ByRow`skryté vrstvy . Typ obou `s` a `d` je řazená kolekce členů celá čísla délky dva. Koncepčně `s` se pohybuje na všech dvojicích `0 <= s[0] < 10` `0 <= s[1] < 20`celých `d` čísel s a , a rozsahy `0 <= d[0] < 10` `0 <= d[1] < 12`přes všechny dvojice celých čísel, s a .

+ Na pravé straně predikátu výrazu je podmínka. V tomto příkladu pro `s` `d` každou hodnotu a tak, aby podmínka je True, je hrana z uzlu zdrojové vrstvy do uzlu cílové vrstvy. Tento výraz filtru tedy označuje, že sada obsahuje připojení `s` z uzlu definovaného k uzlu definovanému `d` ve všech případech, kdy s[0] se rovná d[0].

Volitelně můžete určit sadu závaží pro filtrovaný svazek. Hodnota atributu **Weights** musí být n-tice hodnot s plovoucí desetinnou útavou s délkou, která odpovídá počtu připojení definovaných balíčkem. Ve výchozím nastavení jsou váhy generovány náhodně.

Hodnoty hmotnosti jsou seskupeny podle indexu cílového uzlu. To znamená, že pokud je první cílový uzel připojen `K` ke zdrojovým uzlům K, první prvky řazené kolekce **vaho vaho** jsou váhy pro první cílový uzel v pořadí indexu zdroje. Totéž platí pro zbývající cílové uzly.

Je možné zadat váhy přímo jako konstantní hodnoty. Pokud jste například naučili váhy dříve, můžete je zadat jako konstanty pomocí této syntaxe:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>Konvoluční svazky

Pokud mají trénovací data homogenní strukturu, konvoluční připojení se běžně používají k naučení funkce na vysoké úrovni dat. Například v obraze, audio nebo video data, prostorové nebo časové dimenzionalita může být poměrně jednotná.

Konvoluční svazky používají **obdélníková jádra,** která jsou prosakována rozměry. V podstatě každé jádro definuje sadu vahou aplikovaných v místních čtvrtích, označovaných jako **aplikace jádra**. Každá aplikace jádra odpovídá uzlu ve zdrojové vrstvě, který se označuje jako **centrální uzel**. Váhy jádra jsou sdíleny mezi mnoha připojeními. V konvolučním balíčku je každé jádro obdélníkové a všechny aplikace jádra mají stejnou velikost.

Konvoluční svazky podporují následující atributy:

**InputShape** definuje dimenzionalitu zdrojové vrstvy pro účely tohoto konvolučního svazku. Hodnota musí být řazená kolekce členů kladných celá čísla. Součin celá čísla se musí rovnat počtu uzlů ve zdrojové vrstvě, ale jinak nemusí odpovídat dimenzionalitě deklarované pro zdrojovou vrstvu. Délka této n-tice se stane **arity** hodnotu pro konvoluční svazek. Obvykle arity odkazuje na počet argumentů nebo operandů, které funkce může trvat.

Chcete-li definovat tvar a umístění jader, použijte atributy **KernelShape**, **Stride**, **Padding**, **LowerPad**a **UpperPad**:

+ **KernelShape**: (povinné) Definuje dimenzionalitu každého jádra pro konvoluční svazek. Hodnota musí být řazená kolekce členů kladných celá čísla s délkou, která se rovná aritě svazku. Každá součást této n-tice nesmí být větší než odpovídající součást **InputShape**.

+ **Krok**: (volitelné) Definuje velikosti posuvných kroků konvoluce (velikost jednoho kroku pro každou dimenzi), což je vzdálenost mezi centrálními uzly. Hodnota musí být řazená kolekce členů kladných celá čísla s délkou, která je arity svazku. Každá součást této n-tice nesmí být větší než odpovídající součást **Shape .** Výchozí hodnota je řazená kolekce členů se všemi součástmi rovna jedné.

+ **Sdílení**: (nepovinné) Definuje sdílení hmotnosti pro každou dimenzi konvoluce. Hodnotou může být jedna logická hodnota nebo řazená kolekce členů logické hodnoty s délkou, která je arity sady. Jedna logická hodnota je rozšířena tak, aby byla n-tice správné délky se všemi součástmi rovnými zadané hodnotě. Výchozí hodnota je řazená kolekce členů, která se skládá ze všech hodnot True.

+ **MapCount**: (volitelně) Definuje počet map funkcí pro konvoluční balíček. Hodnota může být jedno kladné celé číslo nebo řazená kolekce členů s kladnými celáčísla s délkou, která je arity svazku. Hodnota jednoho celého čísla je rozšířena na n-tice správné délky s první součásti rovnající se zadané hodnotě a všechny zbývající součásti se rovnají jedné. Výchozí hodnota je jedna. Celkový počet map funkcí je součinem součástí řazené kolekce členů. Faktoring tohoto celkového počtu mezi součástmi určuje, jak jsou hodnoty mapy prvků seskupeny v cílových uzlech.

+ **Závaží**: (volitelné) Definuje počáteční závaží pro svazek. Hodnota musí být n-tice hodnoty s plovoucí desetinnou desetinnou desetinnou táhou s délkou, která je počet jader krát počet hmotností na jádro, jak je definováno dále v tomto článku. Výchozí váhy jsou náhodně generovány.

Existují dvě sady vlastností, které řídí odsazení, vlastnosti se vzájemně vylučují:

+ **Odsazení**: (volitelné) Určuje, zda má být vstup polstrovaný pomocí **výchozího schématu odsazení**. Hodnota může být jedna logická hodnota nebo může být n-tice logických hodnot s délkou, která je arity sady.

    Jedna logická hodnota je rozšířena tak, aby byla n-tice správné délky se všemi součástmi rovnými zadané hodnotě.

    Pokud je hodnota dimenze True, je zdroj v této dimenzi logicky doplněn buňkami s nulovou hodnotou, které podporují další aplikace jádra, takže centrální uzly prvního a posledního jádra v této dimenzi jsou prvním a posledním uzly v této dimenzi. ve zdrojové vrstvě. Počet "fiktivních" uzlů v každé dimenzi je tedy určen `(InputShape[d] - 1) / Stride[d] + 1` automaticky, aby se přesně vešly jádra do polstrované zdrojové vrstvy.

    Pokud je hodnota dimenze False, jsou jádra definována tak, aby počet uzlů na každé straně, které jsou vynechány, byl stejný (až do rozdílu 1). Výchozí hodnota tohoto atributu je řazená kolekce členů se všemi součástmi rovna False.

+ **UpperPad** a **LowerPad**: (volitelné) Poskytují větší kontrolu nad množstvím odsazení k použití. **Důležité:** Tyto atributy lze definovat pouze v případě, že výše uvedené **vlastnoste Odsazení** ***není*** definována. Hodnoty by měly být kolekce členů s celočíselnou hodnotou s délkami, které jsou arity svazku. Pokud jsou tyto atributy zadány, "fiktivní" uzly jsou přidány do dolního a horního konce každé dimenze vstupní vrstvy. Počet uzlů přidaných do dolnía horní části v každé dimenzi je určen **LowerPad**[i] a **UpperPad**[i] v uvedeném pořadí.

    Aby se zajistilo, že jádra odpovídají pouze "skutečným" uzlům a nikoli "fiktivním" uzlům, musí být splněny tyto podmínky:
  - Každá součást **LowerPadmusí** být `KernelShape[d]/2`přísně menší než .
  - Každá součást **UpperPadu** nesmí `KernelShape[d]/2`být větší než .
  - Výchozí hodnota těchto atributů je řazená kolekce členů se všemi součástmi rovna 0.

    Nastavení **Padding** = true umožňuje tolik odsazení, kolik je potřeba, aby se "střed" jádra uvnitř "skutečného" vstupu. To změní matematiku bit pro výpočet velikosti výstupu. Obecně platí, *D* že výstupní velikost `D = (I - K) / S + 1`D `I` je vypočítána `K` jako , `S` kde je vstupní `/` velikost, je velikost jádra, je krok a je dělení celé číslo (zaoblené směrem k nule). Pokud nastavíte UpperPad = [1, `I` 1], vstupní velikost `D = (29 - 5) / 2 + 1 = 13`je účinně 29, a proto . Však při **padding** = `I` true, v `K - 1`podstatě dostane narazil do ; proto `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14`. Zadáním hodnot pro **UpperPad** a **LowerPad** získáte mnohem větší kontrolu nad odsazením, než kdybyste právě nastavili **odsazení** = true.

Další informace o konvolučních sítích a jejich aplikacích naleznete v těchto článcích:

+ [http://deeplearning.net/tutorial/lenet.html](http://deeplearning.net/tutorial/lenet.html)
+ [https://research.microsoft.com/pubs/68920/icdar03.pdf](https://research.microsoft.com/pubs/68920/icdar03.pdf)

## <a name="pooling-bundles"></a>Sdružování svazků

**Svazek sdružování** aplikuje geometrii podobnou konvolučnímu připojení, ale používá předdefinované funkce ke zdrojovým hodnotám uzlů k odvození hodnoty cílového uzlu. Proto, sdružování svazky nemají trénovatelný stav (váhy nebo zkreslení). Sdružování svazků podporuje všechny konvoluční atributy kromě **sdílení**, **MapCount**a **Váhy**.

Jádra shrnutá sousedními sdružovacími jednotkami se obvykle nepřekrývají. Pokud Stride[d] se rovná KernelShape[d] v každé dimenzi, získaná vrstva je tradiční místní vrstva sdružování, která se běžně používá v konvolučních neuronových sítích. Každý cílový uzel vypočítá maximální nebo střední hodnotu aktivit jádra ve zdrojové vrstvě.

Následující příklad ilustruje sdružovací balíček:

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }
```

+ Arita svazku je 3: to znamená délka řazené kolekce členů `InputShape`, `KernelShape`a `Stride`.
+ Počet uzlů ve zdrojové vrstvě je `5 * 24 * 24 = 2880`.
+ Toto je tradiční místní sdružování vrstvy, protože **KernelShape** a **Stride** jsou stejné.
+ Počet uzlů v cílové vrstvě `5 * 12 * 12 = 1440`je .

Další informace o sdružování vrstev naleznete v těchto článcích:

+ [https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf)(Oddíl 3.4)
+ [https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf)
+ [https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Balíčky normalizace odezvy

**Normalizace odezvy** je lokální normalizační schéma, které poprvé zavedl Geoffrey Hinton, et al, v článku [Klasifikace ImageNet s hlubokými konvolučními neuronovými sítěmi](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf).

Normalizace odezvy se používá k podpoře generalizace v nervových sítích. Když jeden neuron je střelba na velmi vysoké úrovni aktivace, místní reakce normalizace vrstva potlačuje úroveň aktivace okolních neuronů. To se provádí pomocí tří`α` `β`parametrů `k`( , , a ) a konvoluční struktury (nebo tvaru okolí). Každý neuron v cílové vrstvě **y** odpovídá neuronu **x** ve zdrojové vrstvě. Aktivační úroveň **y** je dána následujícím `f` vzorcem, kde je úroveň `Nx` aktivace neuronu, a je jádro (nebo sada, která obsahuje neurony v okolí **x**), jak je definováno následující konvoluční strukturou:

![vzorec pro konvoluční strukturu](./media/azure-ml-netsharp-reference-guide/formula_large.png)

Balíčky normalizace odezvy podporují všechny konvoluční atributy kromě **sdílení**, **MapCount**a **Váhy**.

+ Pokud jádro obsahuje neurony ve stejné mapě jako ***x***, normalizační schéma je označováno jako **stejná normalizace mapy**. Chcete-li definovat stejnou normalizaci mapy, musí mít první souřadnice v **InputShape** hodnotu 1.

+ Pokud jádro obsahuje neurony ve stejné prostorové poloze jako ***x***, ale neurony jsou v jiných mapách, normalizační schéma se nazývá **přes normalizaci map**. Tento typ normalizace odezvy implementuje formu boční inhibice inspirované typem nalezeným v reálných neuronech, což vytváří konkurenci pro velké aktivační úrovně mezi výstupy neuronů vypočítanými na různých mapách. Chcete-li definovat napříč mapy normalizace, první souřadnice musí být celé číslo větší než jeden a ne větší než počet map a zbytek souřadnice musí mít hodnotu 1.

Vzhledem k tomu, že balíčky normalizace odezvy používají předdefinovanou funkci pro hodnoty zdrojového uzlu k určení hodnoty cílového uzlu, nemají žádný trénovatelný stav (váhy nebo zkreslení).

> [!NOTE]
> Uzly v cílové vrstvě odpovídají neuronům, které jsou centrálními uzly jader. Například pokud `KernelShape[d]` je lichý, pak `KernelShape[d]/2` odpovídá uzel centrálního jádra. Pokud `KernelShape[d]` je sudá, centrální `KernelShape[d]/2 - 1`uzel je na . Proto pokud `Padding[d]` je False, první `KernelShape[d]/2` a poslední uzly nemají odpovídající uzly v cílové vrstvě. Chcete-li se této situaci vyhnout, definujte **padding** jako [true, true, ..., true].

Kromě výše popsaných čtyř atributů podporují normalizační balíčky odpovědí také následující atributy:

+ **Alfa**: (povinné) Určuje hodnotu s plovoucí `α` desetinnou hodnotou, která odpovídá předchozímu vzorci.
+ **Beta**: (povinné) Určuje hodnotu s plovoucí `β` desetinnou hodnotou, která odpovídá v předchozím vzorci.
+ **Posun**: (volitelné) Určuje hodnotu s plovoucí `k` desetinnou hodnotou, která odpovídá předchozímu vzorci. Výchozí hodnota je 1.

Následující příklad definuje balíček normalizace odpovědí pomocí těchto atributů:

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }
```

+ Zdrojová vrstva obsahuje pět map, z nichž každá má rozměr 12x12, celkem v 1440 uzlech.
+ Hodnota **KernelShape** označuje, že se jedná o stejnou vrstvu normalizace mapy, kde okolí je obdélník 3x3.
+ Výchozí hodnota **odsazení** je False, proto má cílová vrstva v každé dimenzi pouze 10 uzlů. Chcete-li do cílové vrstvy zahrnout jeden uzel, který odpovídá každému uzlu ve zdrojové vrstvě, přidejte odsazení – [true, true, true]; a změnit velikost RN1 na [5, 12, 12].

## <a name="share-declaration"></a>Prohlášení o sdílení

Net# volitelně podporuje definování více svazků se sdílenými váhami. Váhy všech dvou svazků mohou být sdíleny, pokud jsou jejich struktury stejné. Následující syntaxe definuje svazky se sdílenými váhami:

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

Například následující deklarace sdílené položky určuje názvy vrstev, což znamená, že by měly být sdíleny váhy i zkreslení:

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

+ Vstupní prvky jsou rozděleny do dvou vstupních vrstev stejné velikosti.
+ Skryté vrstvy pak vypočítávají prvky vyšší úrovně ve dvou vstupních vrstvách.
+ Prohlášení sdílení určuje, že *H1* a *H2* musí být vypočítány stejným způsobem z jejich příslušných vstupů.

Alternativně by to mohlo být specifikováno dvěma samostatnými prohlášeními o akciích takto:

```Net#
share { Data1 => H1, Data2 => H2 } // share weights
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases
```

Krátký tvar můžete použít pouze v případě, že vrstvy obsahují jeden svazek. Obecně platí, že sdílení je možné pouze v případě, že příslušná struktura je totožná, což znamená, že mají stejnou velikost, stejnou konvoluční geometrii a tak dále.

## <a name="examples-of-net-usage"></a>Příklady použití programu Net#

Tato část obsahuje některé příklady, jak můžete použít Net# přidat skryté vrstvy, definovat způsob, jakým skryté vrstvy komunikovat s jinými vrstvami a vytvářet konvoluční sítě.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Definujte jednoduchou vlastní neuronovou síť: příklad "Hello World"

Tento jednoduchý příklad ukazuje, jak vytvořit model neuronové sítě, který má jednu skrytou vrstvu.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;
```

Příklad ilustruje některé základní příkazy takto:

+ První řádek definuje vstupní vrstvu `Data`(pojmenovanou). Při použití `auto` klíčového slova neuronové sítě automaticky zahrnuje všechny sloupce funkce ve vstupních příkladech.
+ Druhý řádek vytvoří skrytou vrstvu. Název `H` je přiřazen ke skryté vrstvě, která má 200 uzlů. Tato vrstva je plně připojena ke vstupní vrstvě.
+ Třetí řádek definuje výstupní vrstvu `Out`(s názvem), která obsahuje 10 výstupních uzlů. Pokud neuronové sítě se používá pro klasifikaci, je jeden výstupní uzel na třídu. Klíčové slovo **sigmoid** označuje, že výstupní funkce je použita na výstupní vrstvu.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Definování více skrytých vrstev: příklad počítačového vidění

Následující příklad ukazuje, jak definovat o něco složitější neuronové sítě, s více vlastní skryté vrstvy.

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

Tento příklad ilustruje několik funkcí jazyka specifikace neuronových sítí:

+ Struktura má dvě vstupní `Pixels` `MetaData`vrstvy a .
+ Vrstva `Pixels` je zdrojová vrstva pro dva svazky připojení s cílovými vrstvami `ByRow` a `ByCol`.
+ Vrstvy `Gather` `Result` a cílové vrstvy ve více svazkech připojení.
+ Výstupní vrstva `Result`, je cílová vrstva ve dvou svazkech připojení; jedna se skrytou `Gather` vrstvou druhé úrovně jako cílovou `MetaData` vrstvou a druhou se vstupní vrstvou jako cílovou vrstvou.
+ Skryté vrstvy `ByRow` `ByCol`a , určete filtrované připojení pomocí predikátových výrazů. Přesněji řečeno, uzel `ByRow` v na [x, y] je `Pixels` připojen k uzlům v které mají první souřadnici indexu rovnající se první souřadnici uzlu x. Podobně uzel v `ByCol` na [x, y] je připojen k `Pixels` uzlům v které mají druhý index souřadnice v rámci jednoho z druhého souřadnice uzlu, y.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Definování konvoluční sítě pro klasifikaci více tříd: příklad rozpoznávání číslic

Definice následující sítě je navržena tak, aby rozpoznala čísla, a ilustruje některé pokročilé techniky pro přizpůsobení neuronové sítě.

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

+ Struktura má jednu vstupní `Image`vrstvu .
+ Klíčové `convolve` slovo označuje, `Conv1` že `Conv2` vrstvy pojmenované a jsou konvoluční vrstvy. Každá z těchto deklarací vrstev následuje seznam atributů konvoluce.
+ Síť má třetí skrytou vrstvu , `Hid3`která je `Conv2`plně připojena k druhé skryté vrstvě .
+ Výstupní vrstva `Digit`, je připojena pouze `Hid3`ke třetí skryté vrstvě . Klíčové `all` slovo označuje, že výstupní `Hid3`vrstva je plně připojena k .
+ Arita konvoluce je tři: délka řazené `KernelShape` `Stride`kolekce `Sharing`členů `InputShape`, , a .
+ Počet hmotností na jedno `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26`jádro je . Nebo `26 * 50 = 1300`.
+ Uzly v každé skryté vrstvě můžete vypočítat následujícím způsobem:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5`
    `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ Celkový počet uzlů lze vypočítat pomocí deklarované dimenzionality vrstvy [50, 5, 5], takto:`MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ Protože `Sharing[d]` je False `d == 0`pouze pro , `MapCount * NodeCount\[0] = 10 * 5 = 50`počet jader je .

## <a name="acknowledgements"></a>Poděkování

Jazyk Net# pro přizpůsobení architektury neuronových sítí vyvinuli v Microsoftu Shon Katzenberger (architekt, strojové učení) a Alexey Kamenev (softwarový inženýr, Microsoft Research). Interně se používá pro projekty a aplikace strojového učení od detekce obrázků až po analýzu textu. Další informace najdete [v tématu Neural Nets ve studiu Azure Machine Learning – úvod do sítě#](https://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)
