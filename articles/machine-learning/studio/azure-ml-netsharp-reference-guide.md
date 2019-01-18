---
Název: Vytvořte vlastní neuronových sítí Net # titleSuffix: Azure Machine Learning Studio Popis: Průvodce syntaxí pro specifikačnímu jazyku neuronových sítí Net #. Zjistěte, jak vytvářet modely vlastní neuronové sítě v nástroji Azure Machine Learning Studio.
služby: machine learningu ms.service: ms.component strojového učení: studio ms.topic: referenční dokumentace

Autor: ericlicoding ms.author: amlstudiodocs ms.custom: předchozí Autor = heatherbshapiro předchozí ms.author=hshapiro ms.date: 03/01/2018
---
# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning-studio"></a>Příručka ke specifikačnímu jazyku neuronových sítí Net # pro Azure Machine Learning Studio

NET # je jazyk vyvinutý microsoftem, který se používá k definování architektur komplexní neuronové sítě jako hluboké neuronové sítě nebo convolutions libovolného dimenzí. Složité struktury můžete použít ke zlepšení učení na data, jako jsou image, video nebo ve zvukovém souboru.

Architektura specifikace Net # můžete použít v těchto kontextech:

+ Všechny moduly neuronových sítí v Microsoft Azure Machine Learning Studio: [Víc tříd Neuronové sítě](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/multiclass-neural-network), [Two-Class Neuronové sítě](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/two-class-neural-network), a [regrese Neuronové sítě](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/neural-network-regression)
+ Funkce neuronových sítí v Microsoft ML Server: [NeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/neuralnet) a [rxNeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxneuralnet)jazyka R a [rx_neural_network](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-neural-network) pro Python.


Tento článek popisuje základní koncepty a syntaxe, které potřebujete pro vývoj vlastní neuronové sítě pomocí Net #: 

+ Požadavky na neuronové sítě a tom, jak definovat primární součásti
+ Syntaxi a klíčových slovech specifikace jazyka Net #
+ Příklady vlastních neuronových sítí vytvořené pomocí Net # 



## <a name="neural-network-basics"></a>Základy neuronové sítě

Struktura neuronové sítě se skládá z uzlů, které jsou uspořádány do vrstev a vážené připojení (nebo hrany) mezi uzly. Připojení se směrové a každé připojení má zdrojový uzel a cílový uzel.  

Každá trainable vrstva (skrytý nebo vrstvu výstupu) má jeden nebo více **připojení sady**. Připojení sady se skládá ze zdrojové vrstvy a specifikaci připojení z této zdrojové vrstvy. Všechna připojení v dané sadě sdílet zdrojové a cílové vrstvy. Net # sady připojení se považuje za jako patřící do cílové vrstvy do sady prostředků.

NET # podporuje různé typy připojení sad, které umožňují přizpůsobit vstupy způsob, jak jsou mapovány na skrytými vrstvami a mapovat na výstupy.

Výchozí nebo standardní sada je **úplnou sadu**, ve které je připojeno každý uzel ve vrstvě zdroje na každý uzel v cílové vrstvy.

Kromě toho Net # podporuje následující čtyři typy sad rozšířeného připojení:

+ **Filtrované sady**. Predikát můžete definovat pomocí umístění vrstvy zdrojový a cílový uzel vrstvy. Pokaždé, když predikát je hodnota True, jsou uzly připojeny.

+ **Svazky s využitím konvolučních**. Můžete definovat malé sousedství uzlů ve vrstvě zdroje. Každý uzel v cílové vrstvy je připojen k jedné okolí uzly ve zdrojové vrstvy.

+ **Sdružování sady** a **odpovědi normalizace sady**. Ty jsou podobné konvoluční sad v tom, že uživatel definuje malé sousedství uzly ve zdrojové vrstvy. Rozdíl je, že nejsou trainable váhy okraje v těchto svazcích. Místo toho předdefinované funkce použity na zdrojové hodnoty uzlu k určení hodnoty cílový uzel.


## <a name="supported-customizations"></a>Podporovaná vlastní nastavení

Architektura modely neuronových sítí, které vytvoříte ve službě Azure Machine Learning je možné výrazně přizpůsobit pomocí Net #. Můžete:

+ Vytvoření skrytými vrstvami a řídí počet uzlů v každé vrstvě.
+ Zadejte, jak mají být připojené k sobě navzájem vrstvy.
+ Definujte zvláštní připojení struktury, jako je například convolutions a váhy sdílení svazků.
+ Zadejte jiný aktivace funkce.

Podrobnosti o syntaxi specifikaci jazyka naleznete v tématu [struktura specifikace](#Structure-specifications).  

Příklady definice neuronových sítí pro některé běžné strojového učení úloh, od simplexně pro komplexní, najdete v článku [příklady](#Examples-of-Net#-usage).

## <a name="general-requirements"></a>Obecné požadavky

+ Musí být právě jeden výstupní vrstvě, aspoň jeden vstupní a nula nebo více skrytými vrstvami. 
+ Každá vrstva má pevný počet uzlů, koncepčně uspořádané v pravoúhlého pole libovolného dimenzí. 
+ Vstupní vrstvy mít žádné parametry přidružené trénovaného a představují místa, kde instance data do sítě. 
+ Trainable vrstev (vrstvy skryté a výstupní) mají přidružené trénovaného parametry, označované jako váhy a tendence. 
+ Zdrojový a cílový uzel musí být v samostatné vrstvy. 
+ Připojení musí být Acyklické; jinými slovy nemůže být řetězec připojení vede zpět na původní zdrojový uzel.
+ Vrstva výstupu nemůže být zdrojovou vrstvu sady připojení.  

## <a name="structure-specifications"></a>Struktura specifikace

Specifikace struktura neuronové sítě se skládá ze tří částí: **deklarace konstanty**, **vrstvy deklarace**, **připojení deklarace**. K dispozici je také volitelné **sdílet deklarace** oddílu. Oddíly lze zadat v libovolném pořadí.

## <a name="constant-declaration"></a>Deklarace konstanty

Deklarace konstanty je volitelný. Poskytuje prostředky k definování hodnot použitých v definici neuronové sítě jinde. Příkaz deklarace se skládá z identifikátoru následovaný rovnítko a hodnota výrazu.

Například následující příkaz definuje konstantu `x`:  

`Const X = 28;`

Pro definování současně dva nebo více konstant, uzavřete názvy identifikátorů a hodnoty ve složených závorkách a oddělte je středníky. Příklad:

`Const { X = 28; Y = 4; }`

Pravá strana výrazu přiřazení jednotlivých může být celé číslo, reálné číslo, logickou hodnotu (True nebo False) nebo matematický výraz. Příklad:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Deklarace vrstvy

Je nutná deklarace vrstvy. Definuje velikost a zdrojové vrstvy, včetně sady připojení a atributy. Příkaz deklarace začíná název vrstvy (vstup, skrytá nebo výstupní), za nímž následuje dimenze vrstvy (řazené kolekce členů kladných celých čísel). Příklad:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;  
```

+ Dimenze je počet uzlů ve vrstvě. V tomto příkladu jsou dvě dimenze [5,20], což znamená, že ve vrstvě 100 uzlů.
+ Vrstvy mohou být deklarovány v libovolném pořadí, s jednou výjimkou: Pokud je definován více než jeden vstupní vrstvy, pořadí, ve kterém jsou deklarovány musí odpovídat pořadí funkcí ve vstupních datech.

Chcete-li určit počet uzlů ve vrstvě automaticky určit, použijte `auto` – klíčové slovo. `auto` – Klíčové slovo má různé účinky, v závislosti na vrstvě:

+ Počet uzlů v deklaraci vstupní vrstvy je mnoho funkcí v vstupní data.
+ V deklaraci skryté vrstvě počet uzlů, je číslo, které je zadána hodnota parametru pro **počet skrytých uzlů:**.
+ Počet uzlů v deklaraci výstupu vrstvy je 2 pro klasifikaci dvěma třídami, 1 pro regresní a stejný počet výstupních uzlů pro klasifikace víc tříd.

Například následující definice sítě umožňuje všechny vrstvy se automaticky určit velikost:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;  
```

Vrstva deklaraci trainable vrstvy (vrstvy skrytý nebo výstupní) může volitelně zahrnovat výstup funkci (také nazývané aktivace funkce), kde je použit výchozí **sigmoid** modelů klasifikace a  **Lineární** pro regresní modely. I v případě, že používáte výchozí nastavení, můžete explicitně stavu aktivace funkce, v případě potřeby pro přehlednost.

Jsou podporovány následující funkce výstup:

+ sigmoid
+ Lineární
+ softmax
+ rlinear
+ Čtverec
+ Sqrt
+ srlinear
+ Abs
+ TANH –
+ brlinear

Například následující deklarace používá **softmax** funkce:

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>Deklarace připojení

Okamžitě po definování trainable vrstvu, je třeba deklarovat připojení mezi vrstvami, které jste definovali. Deklarace sady připojení začíná klíčovým slovem `from`následovaný název zdrojové vrstvy do sady prostředků a druh sady připojení prostředků k vytvoření.

V současné době jsou podporovány pět typů sad připojení:

+ **Úplné** sady uvedeno klíčové slovo `all`
+ **Filtrovat** svazky označeny klíčovým slovem `where`následovaný výraz predikátu
+ **Konvoluční** svazky označeny klíčovým slovem `convolve`následovaný konvoluce atributy
+ **Sdružování** sady označená klíčovými slovy **max. fondu** nebo **znamenat fondu**
+ **Odpověď normalizace** svazky označeny klíčovým slovem **norm odpovědi**

## <a name="full-bundles"></a>Úplné sady

Připojení úplná sada zahrnuje připojení z každého uzlu ve vrstvě zdroje do každého uzlu v cílové vrstvy. Toto je výchozí typ připojení.

## <a name="filtered-bundles"></a>Filtrované sady

Specifikace sady filtrovaném připojení zahrnuje predikátu, vyjádřené syntakticky, velmi podobně jako C# výrazu lambda. Následující příklad definuje dvě filtrované sady:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;  
```

+ V predikátu pro `ByRow`, `s` je parametr představující index do obdélníkové pole uzly vstupní vrstvy `Pixels`, a `d` je parametr představující index do pole uzlů ve skryté vrstvě `ByRow`. Typ obou `s` a `d` je řazená kolekce členů celých čísel o délce 2. Koncepčně `s` rozsahy adres přes všechny páry celých čísel s `0 <= s[0] < 10` a `0 <= s[1] < 20`, a `d` rozsahy adres přes všechny páry celých čísel, s `0 <= d[0] < 10` a `0 <= d[1] < 12`. 

+ Na pravé straně výraz predikátu je podmínkou. V tomto příkladu, pro každou hodnotu `s` a `d` tak, že je podmínka True, je okraj ze zdrojového uzlu vrstvy do cílového uzlu vrstvy. Proto tento výraz filtru znamená, že sada zahrnuje připojení z uzlu definované `s` k uzlu definované `d` ve všech případech, kde je rovna d [0] s [0].

Volitelně můžete zadat sadu váhy k filtrované sadě. Hodnota **váhy** atribut musí být z hodnoty plovoucího bodu s délkou, která se shoduje s počtem připojení definované sadě řazené kolekce členů. Ve výchozím nastavení generují náhodně váhy.

Vážené hodnoty jsou seskupené podle index cílového uzlu. To znamená, pokud první cílový uzel je připojen k K zdrojové uzly, první `K` prvky **váhy** vah pro první cílový uzel v pořadí zdrojů rejstřík jsou řazené kolekce členů. To platí i pro zbývající uzly cíl.

Je možné zadat přímo jako konstantních hodnot váhu. Například pokud jste se dozvěděli, vah dříve, můžete je zadat jako konstanty, použijte tuto syntaxi:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>Konvoluční sady

Když trénovacích dat má homogenní strukturu, konvoluční připojení se běžně používají k další data mezi důležité funkce. Například obrázek, zvukový nebo video, prostorových nebo dočasné dimenzionalitu lze data poměrně jednotné.  

Konvoluční svazky využívat obdélníkové **jádrech** , který se posouvá prostřednictvím dimenze. V podstatě každý jádra definuje sadu váhy použitý v místní sousedství říká **aplikací jádra**. Každá aplikace jádra odpovídá uzlu ve vrstvě zdroj, který se označuje jako **centrálního uzlu**. Váhy jádra jsou sdílena mezi mnoha připojení. Ve svazku konvoluční každý jádra je obdélníková a všechny aplikace jádra mají stejnou velikost.  

Konvoluční sady podporují následující atributy:

**InputShape** definuje dimenzionalitu zdrojová vrstva pro účely této konvoluční sady. Hodnota musí být kladná celá čísla se řazená kolekce členů. Produkt celá čísla musí být roven počtu uzlů ve vrstvě zdroje, ale jinak není nutné tak, aby odpovídaly dimenzionalitu deklarované pro vrstvu zdroj. Délka této řazené kolekce členů se stane **Arita** hodnotu konvoluční sady. Arity obvykle odkazuje na počet argumentů nebo operandy, které můžete využít funkci.

Chcete-li definovat tvar a umístění jader, použijte atributy **KernelShape**, **Stride**, **odsazení**, **LowerPad**a  **UpperPad**:   

+ **KernelShape**: (povinné) definuje dimenzionalitu každé jádro, konvoluční sady. Hodnota musí být kladná celá čísla, jehož délka se rovná Arita sady se řazená kolekce členů. Jednotlivé komponenty této řazené kolekce členů nesmí být větší než odpovídající komponenta **InputShape**. 

+ **STRIDE**: (volitelné) definuje posuvného krok velikostí konvoluce (jeden krok velikost pro jednotlivé rozměry), který je vzdálenost mezi centrální uzly. Hodnota musí být typu tuple kladná celá čísla s délkou, která je Arita sady. Jednotlivé komponenty této řazené kolekce členů nesmí být větší než odpovídající komponenta **KernelShape**. Výchozí hodnota je řazená kolekce členů se všemi součástmi rovna jedné. 

+ **Sdílení**: (volitelné) určuje váhu sdílení pro jednotlivé rozměry konvoluce. Hodnota může být jeden logická hodnota nebo logické hodnoty s délkou, která je Arita sady se řazená kolekce členů. Jednu logickou hodnotu rozšířen tak, aby se řazené kolekce členů správnou délku se všemi součástmi rovna zadané hodnotě. Výchozí hodnota je řazená kolekce členů, které obsahuje všechny hodnoty True. 

+ **MapCount**: (volitelné) definuje maximální počet funkcí mapuje konvoluční sady. Hodnota může být jeden kladné celé číslo nebo kladných celých čísel s délkou, která je Arita sady řazené kolekce členů. Být správnou délku s rovna zadaná hodnota první součásti řazené kolekce členů je rozšířený hodnotu celá čísla a všechny zbývající součásti rovno jedné. Výchozí hodnota je 1. Total number of funkce mapy je produkt součásti řazené kolekce členů. Řešení celkový počet komponent určuje způsob seskupení hodnoty funkci mapy v cílové uzly. 

+ **Váhy**: (volitelné) definuje počáteční váhy k sadě. Hodnota musí být se řazená kolekce členů hodnoty plovoucího bodu s délkou, která je počet jader počet váhy za jádra, jak je definováno dále v tomto článku. Výchozí vah se vygenerují, náhodně.  

Existují dvě sady vlastností, které řídí odsazení, vlastnosti, které se vzájemně vylučují:

+ **Odsazení**: (volitelné) určuje, zda by měl bude doplněn vstup pomocí **výchozí odsazení schéma**. Hodnota může být jednu logickou hodnotu, nebo může být logických hodnot s délkou, která je Arita sady řazené kolekce členů. 

    Jednu logickou hodnotu rozšířen tak, aby se řazené kolekce členů správnou délku se všemi součástmi rovna zadané hodnotě. 
    
    Pokud pro dimenzi hodnotu True, zdroj je logicky, aby bylo vytvořeno v dané dimenzi s buňky s hodnotou nula pro podporu aplikací další jádra tak, aby centrální uzly první a poslední jádrech v dané dimenzi první a poslední uzly v tomto dimenze ve vrstvě zdroje. Díky tomu se počet "fiktivní" uzlů v každém rozměru určit automaticky, podle přesně `(InputShape[d] - 1) / Stride[d] + 1` jádrech do vrstvy vyplněný zdroje. 
    
    Pokud pro dimenzi hodnotu False, jádrech jsou definovány, tak, aby počet uzlů na každé straně, které jsou ponechány navýšení kapacity je stejné (až rozdíl 1). Výchozí hodnota tohoto atributu je řazená kolekce členů se všemi součástmi rovná na hodnotu False.

+ **UpperPad** a **LowerPad**: (volitelné) zadejte větší kontrolu nad velikost odsazení používat. **Důležité:** Tyto atributy mohou být definované Pokud a pouze tehdy, pokud **odsazení** výše uvedené vlastnosti je ***není*** definované. Hodnoty by měl být celočíselný řazené kolekce členů s, které jsou Arita sady. Když jsou zadané tyto atributy, "fiktivní" uzly se přidají do horní a dolní konce každé dimenze vstupní vrstvy. Počet uzlů, které jsou přidány do horní a dolní zakončení v každém rozměru je určeno **LowerPad**[i] a **UpperPad**[i] v uvedeném pořadí. 

    Pokud chcete mít jistotu, že odpovídají jádrech pouze pro uzly "text real" a "fiktivní" uzlů, musí být splněny následující podmínky:
      - Jednotlivé komponenty **LowerPad** musí být striktně menší než `KernelShape[d]/2`. 
      - Jednotlivé komponenty **UpperPad** nesmí být větší než `KernelShape[d]/2`. 
      - Výchozí hodnota z těchto atributů je řazená kolekce členů se všemi součástmi rovnat 0. 

    Nastavení **odsazení** = true umožňuje tolik odsazení, jako je třeba zachovat "střed" běžícího uvnitř "real" vstupu. Tím se změní matematické trochu pro výpočty výstupní velikost. Obecně platí, velikost výstupní *D* je vypočítán jako `D = (I - K) / S + 1`, kde `I` vstupní velikost, `K` velikost jádra, `S` je stride, a `/` je dělení celého čísla (zaokrouhluje se směrem k nule. ). Pokud nastavíte UpperPad = [1, 1], vstupní velikost `I` je v podstatě 29 a proto `D = (29 - 5) / 2 + 1 = 13`. Ale když **odsazení** = true, v podstatě `I` získá sadu podle `K - 1`; proto `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14`. Zadáním hodnoty pro **UpperPad** a **LowerPad** získáte mnohem lepší kontrolu nad odsazení než pokud stačí nastavit **odsazení** = true.

Další informace o konvoluční sítě a jejich aplikací najdete v těchto článcích: 

+ [http://deeplearning.net/tutorial/lenet.html ](http://deeplearning.net/tutorial/lenet.html)
+ [http://research.microsoft.com/pubs/68920/icdar03.pdf](https://research.microsoft.com/pubs/68920/icdar03.pdf) 
+ [http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf](http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf)  

## <a name="pooling-bundles"></a>Sdružování sady

A **sdružování sady** platí geometrie podobný konvoluční připojení, ale používá předdefinované funkce na zdrojový uzel hodnoty k odvození hodnoty cílový uzel. Tudíž sdružování sady nemáte žádný trainable stav (váhy nebo chyb). Sdružování sady podporují konvoluční atributů s výjimkou **sdílení**, **MapCount**, a **váhy**.

Obvykle se nepřekrývají jádrech automaticky shrnutý podle sousedních sdružování jednotky. Pokud Stride [d] je rovna KernelShape [d] v každém rozměru, vrstva získali je tradiční místní sdružování vrstvu, která se běžně používané v využitím konvolučních neuronových sítí. Každý cílový uzel vypočítá maximální nebo střední aktivity jeho jádra ve vrstvě zdroje.  

Následující příklad ukazuje sdružování sady prostředků: 

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }  
```

+ Arity sady je 3: to znamená, délka řazené kolekce členů `InputShape`, `KernelShape`, a `Stride`. 
+ Počet uzlů ve vrstvě zdroje je `5 * 24 * 24 = 2880`. 
+ Toto je tradiční místní sdružování vrstvy, protože **KernelShape** a **Stride** jsou si rovny. 
+ Počet uzlů v cílové vrstvy je `5 * 12 * 12 = 1440`.

Další informace o sdružování vrstvy najdete v těchto článcích:  

+ [http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Části 3.4)
+ [http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf) 
+ [http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Odpověď normalizace sady

**Odpověď normalizace** je místní normalizace schéma, které bylo poprvé dostupné ve Geoffrey Hinton nedostupným v knize [ImageNet Classiﬁcation s využitím Konvolučních Neuronových sítí hloubkové](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf). 

Normalizace odpovědi se používá na podporu Generalizace v neuronových sítí. Když jeden neuron se spouští na aktivaci velmi vysoké úrovni, potlačí vrstvu normalizace místní odpovědi úroveň aktivace okolního neurons. To se provádí pomocí tří parametrů (`α`, `β`, a `k`) a využitím konvolučních strukturu (nebo detekovaná sousední obrazec). Každý neuron v cílové vrstvy **y** odpovídá neuron **x** ve vrstvě zdroje. Úroveň aktivace **y** je dán na následující vzorec, kde `f` je úroveň aktivace neuron, a `Nx` je jádro (nebo sadu, která obsahuje neurons v okolí z **x**), jak jsou definovány následující konvoluční konstrukcí:  

![Vzorec pro konvoluční strukturu](./media/azure-ml-netsharp-reference-guide/formula_large.png)

Odpověď normalizace sady podporují konvoluční atributů s výjimkou **sdílení**, **MapCount**, a **váhy**.  

+ Pokud jádro obsahuje neurons v mapě stejnou jako ***x***, schéma normalizace se označuje jako **stejná mapování normalizace**. K definování stejné mapování normalizace, první bod se souřadnicemi v **InputShape** musí mít hodnotu 1.

+ Pokud jádro obsahuje neurons na stejné pozici prostorových jako ***x***, ale neurons jsou v jiné mapování, se nazývá schéma normalizace **napříč mapuje normalizace**. Tento typ odpovědi normalizace implementuje formu laterální zabránění INSPIROVANÉ v reálné neurons byl nalezen typ vytváření soutěže pro velké objemy aktivace úrovně mezi neuron výstupy vypočítat na různých map. K definování napříč normalizace mapy, první souřadnice musí být celé číslo větší než a větší než počet mapy a zbytek souřadnice musí mít hodnotu 1.

Protože odpověď normalizace sady použít předdefinované funkce pro zdrojový uzel hodnoty k určení hodnoty cílový uzel, nemají žádný trainable stav (váhy nebo chyb).

> [!NOTE]
> Uzly v cílové vrstvě odpovídají neurons, které jsou centrální uzly jádrech. Například pokud `KernelShape[d]` je liché, pak `KernelShape[d]/2` odpovídá uzel centrální jádra. Pokud `KernelShape[d]` je sudé, do centrálního uzlu se na `KernelShape[d]/2 - 1`. Proto pokud `Padding[d]` NEPRAVDA, první a poslední `KernelShape[d]/2` uzly nemají odpovídající uzly v cílové vrstvy. Chcete-li této situaci vyhnout, definujte **odsazení** jako [true hodnotu true, true,...].

Kromě čtyři atributy je popsáno výše odpověď normalizace sady podporují také následující atributy:

+ **Systém Alpha**: (povinné) určuje hodnotu s plovoucí desetinnou čárkou, která odpovídá `α` v předchozí vzorec. 
+ **Beta verze**: (povinné) určuje hodnotu s plovoucí desetinnou čárkou, která odpovídá `β` v předchozí vzorec. 
+ **Posun**: (volitelné) určuje hodnotu s plovoucí desetinnou čárkou, která odpovídá `k` v předchozí vzorec. Je výchozí hodnota je 1.

Následující příklad definuje sady normalizace odpovědi pomocí těchto atributů:  

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }  
```

+ Zdrojová vrstva obsahuje pět mapování, každý s aof dimenze 12 x 12, celkem tedy 1 440 uzly. 
+ Hodnota **KernelShape** označuje, že se jedná o stejné normalizace vrstvu mapy, kde je čtvrti obdélník velikosti 3 × 3. 
+ Výchozí hodnota **odsazení** má hodnotu False, tedy cílové vrstvě má jenom 10 uzlů v každém rozměru. Chcete-li zahrnout jeden uzel v cílové vrstvy, která odpovídá na každý uzel ve zdrojové vrstvy, přidejte odsazení = [true, true, true]; a změnit velikost RN1 [5, 12, 12].  

## <a name="share-declaration"></a>Deklarace sdílené složky

NET # podporuje volitelně definování víc sad s sdílené váhy. Váhy jakékoli dvě sady je možné sdílet, pokud jejich struktur jsou stejné. Následující syntaxe definuje sady s sdílené vah:  

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

Například následující deklaraci sdílené složky určuje názvy vrstev, označující, že by měla být sdílena váhy a tendence:  

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

+ Vstupní funkce jsou rozdělené do dvou rovná velikosti vstupní vrstev. 
+ Skrytými vrstvami pak výpočetních funkcí vyšší úrovně na dva vstupní vrstvy. 
+ Deklaraci sdílenou složku, která určuje *H1* a *H2* musí být vypočítán stejným způsobem z jejich odpovídajících vstupy.  

Můžete také to může být zadaný pomocí dvě samostatné prohlášení sdílené složky následujícím způsobem:  

```Net#
share { Data1 => H1, Data2 => H2 } // share weights  
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases  
```

Krátký tvar můžete použít pouze v případě, že v jedné sadě obsahovat vrstvy. Obecně platí sdílení je možné jenom v případě relevantní struktura je identická, což znamená, že mají stejnou velikost, stejné konvoluční geometrie a tak dále.  

## <a name="examples-of-net-usage"></a>Příklady použití Net #

Tato část obsahuje příklady použití Net # k přidání skrytými vrstvami, definování způsobu, jakým komunikovat s jinými vrstvami skrytými vrstvami a vytvářet konvoluční sítě.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Definujte jednoduchý vlastní neuronové sítě: Příkladu "Hello World"

Tento jednoduchý příklad ukazuje, jak vytvořit model neuronové sítě, která má jeden skryté vrstvě.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;  
```

Příklad ukazuje některé základní příkazy následujícím způsobem:  

+ První řádek definuje vstupní vrstvy (s názvem `Data`). Při použití `auto` – klíčové slovo, neuronové sítě automaticky obsahuje všechny funkce sloupce vstupní příklady. 
+ Druhý řádek vytvoří skryté vrstvě. Název `H` přiřazen skryté vrstvě, který má 200 uzly. Tato vrstva je plně připojení k vrstvě vstupu.
+ Definuje ve třetím řádku výstupu vrstvy (s názvem `Out`), který obsahuje 10 výstupních uzlů. Pokud neuronové sítě se používá pro klasifikaci, je jeden uzel výstupu na třídě. Klíčové slovo **sigmoid** označuje, že výstup funkce použity na vrstvě výstup.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Definování více skrytými vrstvami: příklad počítače pro zpracování obrazu

Následující příklad ukazuje, jak definovat o něco složitější neuronové sítě s více vlastních skrytými vrstvami.  

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

Tento příklad ukazuje několik funkcí specifikačnímu jazyku neuronových sítí:

+ Struktura obsahuje dvě vstupní vrstvy, `Pixels` a `MetaData`.
+ `Pixels` Vrstva je vrstva zdroje pro dvě sady připojení s cílové vrstvy `ByRow` a `ByCol`.
+ Vrstvy `Gather` a `Result` jsou cílové vrstvy do víc sad připojení.
+ Vrstvy výstup `Result`, je cílové vrstvy ve dvou sad připojení; jeden s druhou úroveň skryté vrstvě `Gather` jako cílové vrstvy a další vrstvou vstupní `MetaData` jako cílové vrstvy.
+ Skrytými vrstvami, `ByRow` a `ByCol`, zadejte filtrované připojení pomocí predikátu výrazů. Přesněji řečeno, uzel v `ByRow` na [x, y] je připojený k uzlům v `Pixels` , že máte koordinovat rovna uzel prvního indexu je první souřadnic x. Podobně uzlu v `ByCol` na [x, y] je připojený k uzlům v `Pixels` , že máte druhý index koordinaci v jednom uzlu v druhé souřadnice, y.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Definování konvoluční sítě pro klasifikace víc tříd: Příklad rozpoznávání číslice

Definice následující síť slouží k rozpoznání čísla a ukazuje některé pokročilé techniky pro přizpůsobení neuronové sítě.  

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
+ Klíčové slovo `convolve` označuje, že vrstvy s názvem `Conv1` a `Conv2` jsou konvoluční vrstvy. Každá z těchto vrstev deklarace je následovaný seznamem atributů konvoluce.
+ Net obsahuje třetí skryté vrstvě `Hid3`, což je plně připojení k druhé skryté vrstvě `Conv2`.
+ Vrstva výstup `Digit`, je připojený jenom k třetí skryté vrstvě `Hid3`. Klíčové slovo `all` označuje, že výstupní vrstvě plně připojení k `Hid3`.
+ Arita konvoluce je třetí: délka řazené kolekce členů `InputShape`, `KernelShape`, `Stride, and `sdílení ". 
+ Počet váhy za jádra je `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26`. Or `26 * 50 = 1300`.
+ Uzly v každé skryté vrstvě můžete vypočítat následujícím způsobem:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5`
    `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ Celkový počet uzlů můžete vypočítat pomocí toho deklarované dimenzionalitu vrstvy, [50, 5, 5], následujícím způsobem: `MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ Protože `Sharing[d]` je pouze pro `d == 0`, počet jader je `MapCount * NodeCount\[0] = 10 * 5 = 50`. 

## <a name="acknowledgements"></a>Potvrzení

Jazyka Net # pro přizpůsobení architektura neuronové sítě byla vyvinuta v Microsoftu Shon Katzenberger (architekt, strojové učení) a Alexey Kamenev (softwarový inženýr, Microsoft Research). Se používá interně pro strojové učení, projekty a aplikace, od rozpoznávání obrazu pro analýzu textu. Další informace najdete v tématu [Neuronových sítí v Azure ML – Úvod do jazyka Net #](http://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)
