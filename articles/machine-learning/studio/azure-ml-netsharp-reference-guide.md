---
title: 'NET # Neuronové sítě specifikace jazyka průvodce – Azure Machine Learning | Microsoft Docs'
description: 'Syntaxe Net # neuronové sítě specifikace jazyka, společně s příklady způsobu vytvoření modelu vlastní neuronové sítě pomocí Net #'
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: cfd1454b-47df-4745-b064-ce5f9b3be303
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/01/2018
ms.openlocfilehash: 2571420e3497e85ec1bf37b340015b061e8c91d4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833909"
---
# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning"></a>Průvodce jazyk specifikace neuronové sítě Net # pro Azure Machine Learning

NET # je jazyk vyvinuté společností Microsoft, který se používá k definování architektury neuronové sítě. Pomocí Net #, abyste definovali strukturu neuronové sítě umožňuje definovat komplexní struktury například hluboké neuronové sítě nebo convolutions libovolný dimenzí, které jsou známé ke zlepšení programování pro data, jako jsou bitové kopie, zvuk a video.

Architektura specifikace Net # můžete použít v těchto kontexty:

+ Všechny moduly neuronové sítě v Microsoft Azure Machine Learning Studio: [Neuronové sítě Multiclass](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/multiclass-neural-network), [Neuronové sítě Two-Class](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/two-class-neural-network), a [regrese Neuronové sítě](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/neural-network-regression)
+ Funkce neuronové sítě v MicrosoftML: [NeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/neuralnet) a [rxNeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxneuralnet)pro jazyk R a [rx_neural_network](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-neural-network) pro jazyk Python.


Tento článek popisuje základní koncepty a syntaxe, které jsou potřebné pro vývoj vlastních neuronové sítě pomocí Net #: 

+ Požadavky na neuronové sítě a jak definovat primární součásti
+ Syntaxe a klíčová slova jazyka specifikace Net #
+ Příklady vlastních neuronové sítě vytvořené pomocí Net # 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="neural-network-basics"></a>Základy neuronové sítě

Struktura neuronové sítě se skládá z uzlů, které jsou uspořádány do vrstev a vyvážené připojení (nebo okraje) mezi uzly. Připojení jsou směrovou a každé připojení má zdrojový uzel a cílový uzel.  

Každý trainable vrstvy (skrytý nebo vrstvu výstup) má jednu nebo více **sady připojení**. Sady připojení se skládá z vrstvy zdroje a údaje o připojení z této zdrojové vrstvy. Všechna připojení v dané sadě sdílet stejný zdroj vrstvu a stejnou cílovou vrstvu. V Net # považuje připojení sady jako náležící do cílové vrstvy sadě.

NET # podporuje různé typy připojení jsou namapované na skryté vrstvy a mapované na výstupy sad, které umožňuje přizpůsobit způsob vstupy.

Je výchozí nebo standardní sady **úplné sady**, ve které je připojeno každý uzel ve zdrojové vrstvě na každý uzel v cílové vrstvy.

Kromě toho Net # podporuje následující čtyři typy sad typ připojení:

+ **Filtrované sady**. Uživatele můžete definovat predikát pomocí umístění uzlu vrstvy zdrojový a cílový uzel vrstvy. Uzly jsou připojené vždy, když predikát má hodnotu True.

+ **Convolutional sady**. Uživatele můžete definovat malé sousedství uzlů ve zdrojové vrstvě. Každý uzel v cílové vrstvě je připojený k jedné okolí uzlů ve zdrojové vrstvě.

+ **Sdružování sady** a **odpovědi normalizaci sady**. Ty jsou podobné convolutional sady, v tom, že uživatel definuje malé sousedství uzlů ve zdrojové vrstvě. Rozdílem je, že nejsou trainable váhu okraje v těchto sad. Předdefinované funkce místo toho se použije pro uzel hodnoty zdroje k určení hodnoty cílový uzel.


## <a name="supported-customizations"></a>Podporované vlastní nastavení

Architektura neuronové sítě modely, které vytvoříte v Azure Machine Learning lze hojně přizpůsobit pomocí Net #. Můžete:

+ Vytvořte skryté vrstvy a řídí počet uzlů v každé vrstvě.
+ Zadejte, jak mají být připojené k sobě navzájem vrstvy.
+ Definujte struktury speciální připojení, jako je například convolutions a váhy sdílení sady.
+ Zadejte jiný aktivace funkce.

Podrobnosti syntaxe specifikace jazyka najdete v tématu [struktura specifikace](#Structure-specifications).  

Příklady definice neuronové sítě pro některé běžné strojového učení úlohy z simplex pro komplexní, najdete v části [příklady](#Examples-of-Net#-usage).

## <a name="general-requirements"></a>Obecné požadavky

+ Musí být přesně jeden výstup vrstvě, alespoň jeden vstupní a v počtu nula či více skryté vrstvy. 
+ Jednotlivé úrovně má pevný počet uzlů, koncepčně uspořádané obdélníková pole libovolný dimenzí. 
+ Vstupní vrstvy mít žádné přidružené vyškolení parametry a představují místa, kde instance data do sítě. 
+ Trainable vrstev (vrstvy skrytá a výstupní) mají přiřazeny vyškolení parametry, označuje jako váhu a tendence z. 
+ Zdrojové a cílové uzly musí být v samostatné vrstvy. 
+ Připojení musí být Acyklické; jinými slovy nemůže být řetězec připojení úvodní zpět na původní zdrojový uzel.
+ Vrstva výstupu nemůže být zdroj vrstvu sady připojení.  

## <a name="structure-specifications"></a>Struktura specifikace

Struktura specifikaci neuronové sítě se skládá ze tří částí: **deklarace konstanty**, **layer prohlášení**, **připojení deklarace**. K dispozici je také volitelný **sdílet deklarace** části. Části lze zadat v libovolném pořadí.

## <a name="constant-declaration"></a>Deklarace konstant

Deklarace konstanty je volitelný. Poskytuje prostředky ke definovat hodnoty používané jinde v definici neuronové sítě. Příkaz deklarace se skládá z identifikátor následuje znak rovná a výraz hodnotu.

Například následující příkaz definuje konstanta `x`:  

`Const X = 28;`

K definování dvou nebo více konstant současně, uzavřete identifikátor názvy a hodnoty do složených závorek a oddělte je středníky. Příklad:

`Const { X = 28; Y = 4; }`

Na pravé straně každý přiřazení výraz může být celé číslo, reálné číslo, logickou hodnotu (True nebo False) nebo matematickém výrazu. Příklad:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Deklarace vrstvy

Je nutná deklarace vrstvy. Určuje velikost a zdroj vrstvy, včetně sady připojení a atributy. Příkaz deklarace začíná název vrstvy (vstup, skrytá nebo výstupní), za nímž následuje dimenze vrstvy (řazená kolekce členů kladná celá čísla). Příklad:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;  
```

+ Produkt dimenze je počet uzlů ve vrstvě. V tomto příkladu se dvěma rozměry [5,20], což znamená, že se 100 uzly ve vrstvě.
+ Vrstvy lze deklarovat v libovolném pořadí, s jednou výjimkou: Pokud je definován více než jeden vstupní vrstvy, pořadí, ve které jsou deklarovány musí odpovídat pořadí funkcí ve vstupní data.

Chcete-li určit, že počet uzlů ve vrstvě určen automaticky, použijte `auto` – klíčové slovo. `auto` – Klíčové slovo má jiný důsledky, v závislosti na vrstvě:

+ V deklaraci vstupní vrstvy je počet uzlů počet funkcí v vstupní data.
+ V deklaraci skryté vrstvě počet uzlů je číslo, která je zadána hodnota parametru pro **počet skryté uzly**.
+ Počet uzlů v deklarace výstupu pro vrstvu, je 2 pro dvě třídy klasifikaci, 1 pro regresní a rovná se počet výstupních uzlů pro více třídami klasifikaci.

Například následující definice sítě umožňuje velikost všech vrstev a automaticky určit:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;  
```

Layer prohlášení pro trainable vrstvu (vrstvy skrytý nebo výstupní) může volitelně obsahovat výstup funkce (také nazývané aktivace funkce), kde je použit výchozí **sigmoid** pro modely klasifikace a  **Lineární** pro regresní modely. I když použijete výchozí nastavení, můžete funkci Aktivace stavu explicitně v případě potřeby pro přehlednost.

Podporovány jsou následující funkce výstup:

+ sigmoid
+ lineární
+ softmax
+ rlinear
+ hranaté
+ Sqrt
+ srlinear
+ Abs
+ TANH
+ brlinear

Například používá toto prohlášení **softmax** funkce:

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>Deklarace připojení

Okamžitě po definování trainable vrstvu, je potřeba deklarovat připojení mezi vrstvy, které jste definovali. Deklarace sady připojení začíná klíčové slovo `from`, následuje název zdrojové vrstvy sadě a druh sady připojení k vytvoření.

V současné době jsou podporovány pět druhy sad připojení:

+ **Úplné** sad, indikován klíčové slovo `all`
+ **Filtrované** sad, indikován klíčové slovo `where`, za nímž následují výraz predikátu
+ **Convolutional** sad, indikován klíčové slovo `convolve`, za nímž následují konvoluce atributy
+ **Sdružování** sad, indikován klíčová slova **maximálního počtu fondu** nebo **znamenat fondu**
+ **Odpověď normalizaci** sad, indikován klíčové slovo **norm odpovědi**

## <a name="full-bundles"></a>Úplné sady

Sady úplný připojovací zahrnuje připojení z každého uzlu ve vrstvě zdroje pro každý uzel v cílové vrstvě. Toto je výchozí typ připojení.

## <a name="filtered-bundles"></a>Filtrované sady

Specifikaci sady filtrovaném připojení zahrnuje predikátu, vyjádřené syntakticky, velmi podobně jako výrazu lambda C#. V následujícím příkladu definuje dvě filtrované sady:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;  
```

+ V predikátu pro `ByRow`, `s` je parametr představující index do pole obdélníková uzlů vrstvě vstupní `Pixels`, a `d` je parametr představující index do pole uzlů ve skryté vrstvě `ByRow`. Typ těchto dvou možností `s` a `d` je celých čísel délky dvě řazené kolekce členů. Koncepčně `s` rozsahy přes všechny páry celých čísel s `0 <= s[0] < 10` a `0 <= s[1] < 20`, a `d` rozsahy přes všechny páry celých čísel, s `0 <= d[0] < 10` a `0 <= d[1] < 12`. 

+ Na pravé straně výraz predikátu je podmínku. V tomto příkladu se pro každou hodnotu `s` a `d` tak, že je podmínka vyhodnocena jako True, je okraj ze zdrojového uzlu vrstvy do cílového uzlu vrstvy. Proto tento výraz filtru znamená, že sada zahrnuje připojení z uzlu definované `s` do uzlu definované `d` ve všech případech, kde se rovná d [0] [0] s.

Volitelně můžete zadat sadu váhu pro filtrovanou sadu. Hodnota **váhu** atribut musí být číslo s plovoucí čárkou hodnoty bodu s délkou, která odpovídá počtu připojení definované sadě řazená kolekce členů. Ve výchozím nastavení jsou váhu náhodně vygenerované.

Hodnoty váhy jsou seskupené podle indexu cílový uzel. To znamená, pokud první cílový uzel je připojen k tisíc zdroj uzly, první `K` prvky **váhu** vah pro první cílový uzel v pořadí zdrojů indexu jsou řazené kolekce členů. Totéž platí i pro zbývající cílové uzly.

Je možné určit váhu přímo jako konstantní hodnoty. Například pokud jste se naučili dříve vah, můžete je zadat jako konstanty pomocí této syntaxe:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>Convolutional sady

Pokud jsou Cvičná data homogenní struktura, convolutional připojení se běžně používají další nejdůležitější funkce data. Například v bitové kopii, data zvuku a videa, prostorových nebo dočasné dimenzionalitu může být poměrně uniform.  

Convolutional sady využívají obdélníková **jádra** , se posouvá prostřednictvím dimenze. V podstatě každý jádra definuje sadu váhu použijí v místní sousedství, označuje jako **aplikací jádra**. Každá aplikace jádra odpovídá uzlu ve vrstvě zdroj, který se označuje jako **centrálního uzlu**. Váhu jádro jsou sdílena mezi mnoha připojení. Ve svazku convolutional jádra, každého je obdélníková a všechny aplikace jádra mají stejnou velikost.  

Convolutional sady podporují následující atributy:

**InputShape** definuje dimenzionalitu vrstvy zdroj pro účely tohoto convolutional sady. Hodnota musí být kladná celá čísla řazená kolekce členů. Produkt celá čísla musí být roven počtu uzlů ve zdrojové vrstvě, ale jinak, nemusí tak, aby odpovídaly dimenzionalitu deklarovat pro zdrojové vrstvy. Délka této řazené kolekce členů změní **Arita** hodnotu pro sadu convolutional. Obvykle Arita odkazuje na počet argumentů nebo operandy, které může provádět funkce.

K definování tvar a umístění jader, použijte atributy **KernelShape**, **Stride**, **odsazení**, **LowerPad**a  **UpperPad**:   

+ **KernelShape**: (povinné) definuje dimenzionalitě každý jádra pro sadu convolutional. Hodnota musí být kladná celá čísla o délce, který se rovná aritu sady řazená kolekce členů. Jednotlivé komponenty této řazené kolekce členů musí být větší než odpovídající součást **InputShape**. 

+ **STRIDE**: (volitelné) definuje posuvné krok velikosti konvoluce (jeden krok velikost každé dimenze), který je vzdálenost mezi centrální uzly. Hodnota musí být kladná celá čísla o délce, který je aritu sady řazená kolekce členů. Jednotlivé komponenty této řazené kolekce členů musí být větší než odpovídající součást **KernelShape**. Výchozí hodnota je se všemi součástmi jednu řazené kolekce členů. 

+ **Sdílení**: (volitelné) definuje váhu sdílení Každá dimenze konvoluce. Hodnota může být jedna logická hodnota nebo řazená kolekce členů logické hodnoty s délkou, která představuje aritu sady. Jednu logickou hodnotu je rozšířeno na řazené kolekce členů správnou délku se všemi součástmi rovna zadané hodnotě. Výchozí hodnota je řazené kolekce členů, která se skládá z všechny hodnoty True. 

+ **MapCount**: (volitelné) definuje maximální počet funkce mapuje convolutional sady. Hodnota může být jeden kladné celé číslo nebo kladných celých čísel s délkou, která představuje aritu sady řazené kolekce členů. Jeden celočíselná hodnota je rozšířený být řazené kolekce členů správnou délku s první součástí, která je rovna zadané hodnotě a všechny zbývající součásti rovno jeden. Výchozí hodnota je 1. Celkový počet funkce mapy je produkt komponent řazenou kolekci členů. Řešení celkový počet součástí určuje způsob seskupení hodnoty mapy funkce v cílové uzly. 

+ **Váhu**: (volitelné) definuje počáteční vah k sadě. Hodnota musí být číslo s plovoucí čárkou bodu hodnoty o délce, která je počet jader vah musí za jádra, jak jsou definovány dále v tomto článku řazená kolekce členů. Výchozí vah se náhodně vygenerované.  

Existují dvě sady vlastností, které řídí odsazení, vlastnosti se vzájemně vylučují:

+ **Odsazení**: (volitelné) určuje zda by měl bude doplněn vstupu pomocí **výchozí schéma odsazení**. Hodnota může být jednu logickou hodnotu, nebo může být logická hodnot s délkou, která představuje aritu sady řazené kolekce členů. 

    Jednu logickou hodnotu je rozšířeno na řazené kolekce členů správnou délku se všemi součástmi rovna zadané hodnotě. 
    
    Pokud je hodnota pro dimenzi hodnotu True, zdroj je logicky vyplní v této dimenzi s buňky s hodnotou nula pro podporu dalších jádra aplikací tak, aby první a poslední uzly v tom, že jsou centrální uzly jádrech první a poslední v této dimenzi dimenze ve zdrojové vrstvě. Proto je počet "fiktivní" uzlů v Každá dimenze určen automaticky, přesně podle `(InputShape[d] - 1) / Stride[d] + 1` jádra do vrstvy vyplněný zdroje. 
    
    Pokud je hodnota pro dimenzi hodnotu False, jsou definovány jádrech tak, aby počet uzlů na každé straně, které jsou ponechány na stejné (až rozdíl 1). Výchozí hodnota tohoto atributu je řazené kolekce členů se všemi součástmi rovnou na hodnotu False.

+ **UpperPad** a **LowerPad**: (volitelné) zadejte větší kontrolu nad velikost odsazení používat. **Důležité:** tyto atributy mohou být definované v případě a pouze v případě **odsazení** vlastnost výše je ***není*** definované. Hodnoty musí být celé číslo s hodnotou řazené kolekce členů s délky, které jsou aritu sady. Jsou-li tyto atributy, "fiktivní" uzly se přidají do horní a dolní konce Každá dimenze vstupní vrstvy. Počet uzlů, které jsou přidány do skončení horní a dolní v Každá dimenze je dáno **LowerPad**[i] a **UpperPad**[i] v uvedeném pořadí. 

    Aby se zajistilo, že jádra odpovídají pouze pro uzly "Skutečná" a "fiktivní" uzly, musí být splněny následující podmínky:
      - Jednotlivé komponenty **LowerPad** musí být striktně menší než `KernelShape[d]/2`. 
      - Jednotlivé komponenty **UpperPad** musí být větší než `KernelShape[d]/2`. 
      - Výchozí hodnota těchto atributů je řazené kolekce členů se všemi součástmi rovna 0. 

    Nastavení **odsazení** = true umožňuje tolik odsazení potřeby zachovat "středu" jádra uvnitř "reálné" vstup. Tato operace změní matematické trochu pro výpočty velikost výstupu. Obecně platí, velikost výstupní *D* se vypočítá jako `D = (I - K) / S + 1`, kde `I` je velikost vstupní `K` je velikost jádra `S` je stride, a `/` je dělení celého čísla (zaokrouhlí směrem k nule. ). Pokud nastavíte UpperPad = [1, 1], vstupní velikost `I` je efektivně 29 a proto `D = (29 - 5) / 2 + 1 = 13`. Ale když **odsazení** = true, v podstatě `I` získá bumped podle `K - 1`; proto `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14`. Zadáním hodnoty pro **UpperPad** a **LowerPad** získat lepší kontrolu nad odsazení než pokud stačí nastavit **odsazení** = true.

Další informace o convolutional sítě a jejich využití naleznete v článcích: 

+ [http://deeplearning.net/tutorial/lenet.html ](http://deeplearning.net/tutorial/lenet.html)
+ [http://research.microsoft.com/pubs/68920/icdar03.pdf](http://research.microsoft.com/pubs/68920/icdar03.pdf) 
+ [http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf](http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf)  

## <a name="pooling-bundles"></a>Sdružování sady

A **sdružování sady** platí geometrie podobná convolutional připojení, ale používá předdefinované funkce zdroj uzlu hodnoty odvození hodnota cílového uzlu. Sdružování sady proto mít žádný trainable stav (váhu nebo tendence z). Sdružování sady podporují všechny convolutional atributy s výjimkou **sdílení**, **MapCount**, a **váhu**.

Obvykle se nepřekrývají jádrech souhrnu přiléhající sdružování jednotkami. Pokud Stride [d] je rovno KernelShape [d] Každá dimenze, je vrstva získat tradiční místní sdružování vrstvu, která se běžně používané v convolutional neuronové sítě. Každý cílový uzel vypočítá maximální nebo střední aktivity jeho jádra ve zdrojové vrstvě.  

Následující příklad ilustruje sdružování sady: 

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }  
```

+ Arita sady je 3: tedy délka řazené kolekce členů `InputShape`, `KernelShape`, a `Stride`. 
+ Počet uzlů ve zdrojové vrstvě je `5 * 24 * 24 = 2880`. 
+ Toto je tradiční místní sdružování vrstvu, protože **KernelShape** a **Stride** jsou stejné. 
+ Počet uzlů v cílové vrstvě je `5 * 12 * 12 = 1440`.

Další informace o sdružování vrstev najdete v těchto článcích:  

+ [http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Část 3.4)
+ [http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf) 
+ [http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Odpověď normalizaci sady

**Odpověď normalizaci** je místní normalizaci schéma, které bylo poprvé dostupné ve Geoffrey Hinton, a další v papíru [ImageNet Classiﬁcation s Convolutional hluboké Neuronové sítě](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf). 

Odpověď normalizaci se používá na podporu Generalizace v neuronové sítě. Když jeden neuron se aktivuje na velmi vysokou aktivace úrovni, potlačí vrstva normalizaci místní odpovědi úroveň aktivace okolního neurons. To se provádí pomocí tří parametry (`α`, `β`, a `k`) a convolutional struktury (nebo tvar Okolní počítače). Každý neuron ve vrstvě cílové **y** odpovídá neuron **x** ve zdrojové vrstvě. Úroveň aktivace **y** podle následujícího vzorce, kde `f` je aktivace úroveň neuron, a `Nx` je jádra (nebo sady, který obsahuje neurons v okolí z **x**), jak je definované následující convolutional strukturu:  

![Vzorec pro convolutional strukturu](./media/azure-ml-netsharp-reference-guide/formula_large.png)

Odpověď sady normalizaci podporují všechny convolutional atributy s výjimkou **sdílení**, **MapCount**, a **váhu**.  

+ Pokud jádra obsahuje neurons v mapě stejné jako ***x***, schéma normalizaci se označuje jako **stejné mapování normalizaci**. Chcete-li definovat stejné normalizaci mapy, první souřadnice v **InputShape** musí mít hodnotu 1.

+ Pokud jádra obsahuje neurons v prostorových stejně jako při ***x***, ale neurons jsou v jiné mapy, schéma normalizaci se nazývá **napříč mapuje normalizaci**. Tento typ odpovědi normalizaci implementuje formu laterální zabránění vycházející v skutečné neurons byl nalezen typ vytváření soutěž o úrovně big aktivace mezi výstupy neuron počítaný na různých mapy. K definování napříč normalizaci mapy, první souřadnice musí být celé číslo větší než 1 a větší než počet mapy a zbytek souřadnice musí mít hodnotu 1.

Protože odpovědi normalizaci sady použít předdefinované funkce pro hodnoty uzlu zdroje k určení hodnoty cílový uzel, nemají žádný trainable stav (váhu nebo tendence z).

> [!NOTE]
> Uzly v cílové vrstvě odpovídají neurons, které jsou centrální uzly jader. Například pokud `KernelShape[d]` je liché, pak `KernelShape[d]/2` odpovídá uzlu centrální jádra. Pokud `KernelShape[d]` je i, centrálního uzlu se na `KernelShape[d]/2 - 1`. Proto pokud `Padding[d]` NEPRAVDA, první a poslední `KernelShape[d]/2` uzly nemají odpovídající uzly ve vrstvě cílový. Chcete-li se vyhnout této situaci, definovat **odsazení** jako [true nastavena hodnota true, true...,].

Kromě čtyři atributy dříve popisované odpověď normalizaci sady také podporují následující atributy:

+ **Alpha**: (povinné) určuje hodnotu s plovoucí desetinnou čárkou, která odpovídá `α` v předchozí vzorec. 
+ **Beta**: (povinné) určuje hodnotu s plovoucí desetinnou čárkou, která odpovídá `β` v předchozí vzorec. 
+ **Posun**: (volitelné) určuje hodnotu s plovoucí desetinnou čárkou, která odpovídá `k` v předchozí vzorec. Výchozí hodnota 1.

V následujícím příkladu definuje sady normalizaci odpovědi pomocí tyto atributy:  

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }  
```

+ Zdroj vrstva obsahuje pět maps, každý s dimenzí aof 12 x 12, ale v uzlech 1440. 
+ Hodnota **KernelShape** označuje, že toto je stejný vrstvu normalizaci mapy, kde je okolí obdélníku 3 x 3. 
+ Výchozí hodnota **odsazení** hodnotu False, proto cílové vrstvy má jenom 10 uzly v Každá dimenze. Zahrnout jeden uzel cílové vrstva, která odpovídá každý uzel ve zdrojové vrstvě, přidejte odsazení = [hodnotu true, true, true]; a změňte velikost RN1 [5, 12, 12].  

## <a name="share-declaration"></a>Deklarace sdílené složky

NET # volitelně podporuje definování víc sad s sdílené váhou. Váhu jakékoli dvě sady lze sdílet, pokud jejich struktury jsou stejné. Následující syntaxí definuje sady s váhou sdílené:  

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

Například následující sdílenou složku – deklarace určuje názvy vrstvy, která určuje, že by měla být sdílena váhu i tendence z:  

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

+ Vstupní funkce jsou rozděleny do dvou rovna velikosti vstupní vrstev. 
+ Skryté vrstvy pak výpočetní vyšší úrovně funkcí na dvou vstupních vrstev. 
+ Sdílené složky deklaraci Určuje, že *H1* a *H2* musí být vypočteny stejným způsobem z jejich odpovídajících vstupy.  

Alternativně to může být určen pomocí dvou samostatných deklarace sdílené složky následujícím způsobem:  

```Net#
share { Data1 => H1, Data2 => H2 } // share weights  
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases  
```

Zkratka můžete použít pouze v případě, že v jedné sadě obsahovat vrstvy. Obecně platí sdílení je možné, pouze pokud je relevantní struktura identické, což znamená, že mají stejnou velikost, stejné convolutional geometrie a tak dále.  

## <a name="examples-of-net-usage"></a>Příklady Net # využití

Tato část obsahuje příklady použití Net # k přidání skryté vrstvy, definování způsobu, jakým skryté vrstvy komunikovat s jinými vrstvami a sestavení convolutional sítě.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Definovat jednoduchý vlastní neuronové sítě: příkladu "Hello World"

Tento jednoduchý příklad ukazuje, jak pro vytvoření modelu neuronové sítě, který má jeden skryté vrstvě.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;  
```

Příklad ukazuje některé základní příkazy následujícím způsobem:  

+ První řádek definuje vstupní vrstvy (s názvem `Data`). Při použití `auto` – klíčové slovo, neuronové sítě automaticky obsahuje všechny funkce sloupce vstupní příklady. 
+ Druhý řádek vytvoří skryté vrstvě. Název `H` je přiřazen k skryté vrstvě, který má 200 uzly. Tuto vrstvu je plně připojený k vrstvě vstupní.
+ Ve třetím řádku definuje vrstvě výstup (s názvem `O`), který obsahuje 10 výstupních uzlů. Pokud neuronové sítě se používá pro klasifikaci, je jeden uzel výstup na třídu. Klíčové slovo **sigmoid** udává, že funkce výstup je použít pro výstup vrstvy.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Definovat více skryté vrstvy: Příklad vize počítače

Následující příklad ukazuje, jak definovat trochu složitější neuronové sítě s více vlastní skryté vrstvy.  

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

Tento příklad znázorňuje několik funkcí specifikace jazyka neuronové sítě:

+ Struktura má dva vstupní vrstev, `Pixels` a `MetaData`.
+ `Pixels` Vrstvy je vrstva zdroje pro dvě sady připojení, s vrstvami cílové `ByRow` a `ByCol`.
+ Vrstvy `Gather` a `Result` jsou cílové vrstvy do více svazků připojení.
+ Vrstvě výstup `Result`, je vrstva cílové vede ke dvěma připojení sady; s druhé úrovně skryté vrstvě `Gather` jako cílové vrstvy a ostatní vrstvě vstupní `MetaData` jako cílové vrstvě.
+ Skryté vrstvy `ByRow` a `ByCol`, zadejte filtrované připojení pomocí predikátu výrazy. Přesněji řečeno, v uzlu `ByRow` v [x, y] je připojené k uzlům v `Pixels` , mají koordinaci rovna uzlu první index je první souřadnic, x. Podobně uzlu v `ByCol` v [x, y] je připojené k uzlům v `Pixels` , mají druhý index koordinaci v rámci jednoho uzlu je druhý souřadnic, y.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Zadejte síť convolutional pro více třídami klasifikace: Příklad rozpoznávání číslice

Definice následující sítě je určen k rozpoznání čísla a ilustruje některé pokročilé techniky pro přizpůsobení neuronové sítě.  

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

+ Struktura má jeden vstupní vrstvu, `Image`.
+ Klíčové slovo `convolve` označuje, že vrstvy s názvem `Conv1` a `Conv2` jsou convolutional vrstvy. Každý z těchto layer prohlášení je následovaný seznamem atributů konvoluce.
+ Sítě má třetí skryté vrstvy `Hid3`, která je plně připojená k druhý skryté vrstvě `Conv2`.
+ Vrstvě výstup `Digit`, je připojený jenom k třetí skryté vrstvě `Hid3`. Klíčové slovo `all` označuje, že výstupní layer plně připojeno k `Hid3`.
+ Aritu konvoluce je tři: délka řazené kolekce členů `InputShape`, `KernelShape`, `Stride, and `sdílení '. 
+ Počet váhu za jádra je `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26`. nebo `26 * 50 = 1300`.
+ Uzly v každé skryté vrstvě můžete vypočítat následujícím způsobem:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5` `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ Celkový počet uzlů, lze vypočítat pomocí deklarované dimenzionalitu vrstvy, [50, 5, 5], následujícím způsobem: `MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ Protože `Sharing[d]` skončí s výsledkem False pouze pro `d == 0`, počet jader je `MapCount * NodeCount\[0] = 10 * 5 = 50`. 

## <a name="acknowledgements"></a>Potvrzení

Jazyk Net # pro přizpůsobení architektuře neuronové sítě byla vyvinuta v Microsoftu Shon Katzenberger (Architekti, Machine Learning) a Alexey Kamenev (pracovník softwaru, Microsoft Research). Se používá interně pro strojové učení, projekty a aplikace od detekce image až Analýza textu. Další informace najdete v tématu [Neuronové sítě v Azure ML – Úvod do Net #](http://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)
