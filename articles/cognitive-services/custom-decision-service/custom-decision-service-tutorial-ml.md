---
title: 'Kurz: Snadné a funkce specifikace – Custom Decision Service'
titlesuffix: Azure Cognitive Services
description: Kurz pro funkcionalizaci a specifikaci funkcí strojového učení ve službě Custom Decision Service.
services: cognitive-services
author: slivkins
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: slivkins
ms.openlocfilehash: 1c701cbe1a71ed48c71a9441c05a7fb4b63e3814
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226056"
---
# <a name="tutorial-featurization-and-feature-specification"></a>Kurz: Specifikace snadné a funkce

Tento kurz se zabývá pokročilým strojovým učením ve službě Custom Decision Service. Kurz se skládá ze dvou částí: [funkcionalizace](#featurization-concepts-and-implementation) a [specifikace funkcí](#feature-specification-format-and-apis). Funkcionalizací se rozumí reprezentace vašich dat ve formě „funkcí“ pro účely strojového učení. Specifikace funkcí pak zabývá formátem JSON a pomocnými rozhraními API pro zadávání funkcí.

Strojové učení ve službě Custom Decision Service je ve výchozím nastavení pro zákazníka transparentní. Z vašeho obsahu se automaticky extrahují funkce a použije se standardní algoritmus zpětnovazebního učení. Extrakce funkce využívá několik dalších služeb Azure Cognitive Services: [Propojování entit](../entitylinking/home.md), [rozhraní Text Analytics](../text-analytics/overview.md), [pro rozpoznávání Emocí](../emotion/home.md), a [pro počítačové zpracování obrazu](../computer-vision/home.md). Pokud budete používat pouze výchozí režim služby, můžete tento kurz přeskočit.

## <a name="featurization-concepts-and-implementation"></a>Funkcionalizace: koncepty a implementace

Služba Custom Decision Service dělá rozhodnutí, jedno po druhém. Každé rozhodnutí zahrnuje výběr mezi několika alternativami, označovanými také jako akce. V závislosti na aplikaci může rozhodnutí zvolit jednu akci, nebo seřazený seznam (krátký) akcí.

Jako příklad si vezměme personalizaci výběru článků na úvodní stránce webu. Akce v tomto případě odpovídají článkům a rozhodnutí tkví v tom, které články se mají zobrazit danému uživateli.

Každá akce je reprezentována vektorem vlastností, které se budou dále označovat jako *funkce*. Kromě funkcí extrahovaných automaticky můžete zadat i nové funkce. Můžete také službě Custom Decision Service dát instrukce, aby některé funkce zapsala, ale pro strojové učení je pak ignorovala.

### <a name="native-vs-internal-features"></a>Nativní vs. interní funkce

Funkce můžete zadat ve formátu nejvíce přirozeném pro vaši aplikaci, ať už to je číslo, řetězec nebo pole. Tyto funkce se nazývají „nativní funkce“. Custom Decision Service překládá jednotlivé nativní funkce na jednu nebo více číselných funkcí označovaných jako „interní funkce“.

Překlad na interní funkce probíhá následovně:

- Číselná funkce zůstane stejná.
- Numerické pole se přeloží na několik číselných funkcí, jednu pro každý prvek pole.
- Funkce s řetězcovou hodnotou `"Name":"Value"` se ve výchozím nastavení přeloží na funkci s názvem `"NameValue"` a hodnotou 1.
- Volitelně může být řetězec reprezentován jako [množina slov (bag-of-words)](https://en.wikipedia.org/wiki/Bag-of-words_model). Potom se vytvoří jedna interní funkce pro každé slovo v řetězci a její hodnota je počet výskytů tohoto slova.
- Interní funkce s nulovou hodnotou jsou vynechány.

### <a name="shared-vs-action-dependent-features"></a>Sdílené funkce a funkce závislé na akci

Některé funkce se vztahují na celé rozhodnutí a jsou stejné pro všechny akce. Ty označujeme jako *sdílené funkce*. Jiné funkce jsou specifické pro konkrétní akci. Označujeme je jako *funkce závislé na akci* (ADF).

V dříve uvedeném příkladu můžou sdílené funkce popisovat uživatele nebo stav okolního světa. Patří sem funkce jako zeměpisná poloha, věk a pohlaví uživatele a významné události, které právě probíhají. Funkce závislé na akci by mohly popisovat vlastnosti daného článku, například témata, kterých se článek týká.

### <a name="interacting-features"></a>Interakce funkcí

Mezi funkcemi dochází často k interakci: výsledek jedné funkce závisí na jiných funkcích. Funkce X je například je, jestli se daný uživatel zajímá o sport. Funkce Y je, jestli je daný článek sportu. Efekt funkce Y je pak vysoce závislý na funkci X.

Z důvodu zohlednění interakcí mezi funkcemi X a Y vytvořte *kvadratickou* funkci, jejíž hodnota je X\*Y. (Můžeme také říci „křížení“ X a Y.) Můžete zvolit, které dvojice funkcí se kříží.

> [!TIP]
> Sdílená funkce by se měla křížit s funkcemi závislými na akci a ovlivňovat jejich hodnocení. Funkce závislé na akcích by se měly křížit se sdílenými funkcemi, aby přispívaly k přizpůsobení.

Jinými slovy: sdílená funkce nekřížená s žádnou funkcí závislou na akci ovlivňuje všechny akce stejným způsobem. Funkce závislá na akci nekřížená s žádnou sdílenou funkcí ovlivňuje také každé rozhodnutí. Tyto typy funkcí můžou snižovat odchylku odhadů odměn.

### <a name="implementation-via-namespaces"></a>Implementace prostřednictvím oborů názvů

Křížené funkce (stejně jako další koncepty funkcionalizace) můžete implementovat na portálu prostřednictvím příkazového řádku VW. Syntaxe je založena na příkazech [Vowpal Wabbit](http://hunch.net/~vw/).

Klíčovým prvkem implementace je koncept *oboru názvů*: pojmenované podmnožiny funkcí. Každá funkce patří do právě jednoho oboru názvů. Obory názvů mohou být explicitně určeny při poskytnutí hodnoty funkce službě Custom Decision Service. To je jediný způsob, jak na příkazovém řádku VW odkazovat na funkci.

Obor názvů je buď „sdílený“, nebo „závislý na akci“: buď se skládá jenom ze sdílených funkcí, nebo se skládá jenom z funkcí závislých na akci, a to sice na stejné akci.

> [!TIP]
> Osvědčený postup je zabalit funkce do explicitně zadaných oborů názvů. Seskupte související funkce do stejného oboru názvů.

Pokud obor názvů není poskytnut, je funkce automaticky přiřazena do výchozího oboru názvů.

> [!IMPORTANT]
> Funkce a obory názvů nemusí být konzistentní napříč různými akcemi. Jinými slovy, obor názvů může mít různé funkce pro různé akce. Kromě toho může být daný obor názvů definován pro některé akce a pro jiné ne.

Více interních funkcí, které pochází ze stejné nativní funkce s hodnotou řetězce, jsou seskupeny do stejného oboru názvů. Jakékoli dvě nativní funkce, které se nacházejí v různých oborech názvů, se berou jako odlišné, i když mají stejný název funkce.

> [!IMPORTANT]
> I když se běžně používají dlouhá a popisná ID oborů názvů, příkazový řádek VW nerozlišuje mezi obory názvů, jejichž ID začíná na stejné písmeno. V následujícím příkladu jsou ID oborů názvů vždy jedno písmeno, například `x` a `y`.

Podrobnosti implementace jsou následující:

- Když chcete křížit obory názvů `x` a `y`, zapište `-q xy` nebo `--quadratic xy`. Každá funkce v `x` je pak zkřížena s každou funkcí v `y`. Zápisem `-q x:` zkřížíte `x` s každým oborem názvů a zápisem `-q ::` zkřížíte všechny páry oborů názvů.

- Pokud chcete ignorovat všechny funkce v oboru názvů `x`, zapište `--ignore x`.

Tyto příkazy se použijí na každou akci samostatně, kdykoli jsou tyto obory názvů definovány.

### <a name="estimated-average-as-a-feature"></a>Odhadovaný průměr jako funkce

Myšlenkový experiment: jaká by byla průměrná odměna pro danou akci, pokud byla vybrána pro všechna rozhodnutí? Taková průměrná odměna může sloužit jako míra „celkové kvality“ této akce. Není přesně známo, jestli byly v některých rozhodnutí místo ní zvoleny jiné akce. Dá se to ale odhadnout pomocí technik zpětnovazebního učení. Kvalita tohoto odhadu obvykle se obvykle časem zlepšuje.

Můžete se rozhodnout zahrnout tuto „odhadovanou průměrnou odměnu“ jako funkci pro danou akci. Služba Custom Decision Service pak bude tento odhad automaticky aktualizovat, kdykoli přijdou nová data. Tato funkce se označuje *mezní funkce* této akce. Mezní funkce je možné použít pro strojové učení a pro audit.

Pokud chcete přidat mezní funkce, do příkazového řádku VW zapište `--marginal <namespace>`. Definujte `<namespace>` ve formátu JSON následujícím způsobem:

```json
{<namespace>: {"mf_name":1 "action_id":1}
```

Vložte tento obor názvů společně s dalšími funkcemi závislými na dané akci. Poskytnete tuto definici pro každé rozhodnutí při použití stejného `mf_name` a `action_id` pro všechna rozhodnutí.

Mezní funkce je přidána pro každou akci s `<namespace>`. `action_id` může být jakýkoli název funkce, který akci jedinečně identifikuje. Název funkce je nastaven na `mf_name`. Je třeba zdůraznit, že mezní funkce s odlišným `mf_name` jsou považovány za různé funkce a učení bude mít pro každé `mf_name` odlišnou váhu.

Výchozí použití je, že `mf_name` je pro všechny akce stejné. Učení pak má jednu váhu pro všechny mezní funkce.

Můžete taky zadat více mezních funkcí pro stejnou akci – se stejnými hodnotami, ale různými názvy funkcí.

```json
{<namespace>: {"mf_name1":1 "action_id":1 "mf_name2":1 "action_id":1}}
```

### <a name="1-hot-encoding"></a>Kódování s 1 horkým bitem

Můžete zvolit, že budou některé funkce reprezentovány jako bitové vektory, kde každý bit odpovídá rozsahu možných hodnot. Tento bit nastaven na hodnotu 1 tehdy a jenom tehdy, když funkce je v tomto rozsahu. Je tam tudíž jeden „horký“ bit, který je nastaven na 1, a ostatní jsou nastaveny na 0. Tato reprezentace se běžně označuje jako *kódování s 1 horkým bitem (1-hot encoding)*.

Kódování s 1 horkým bitem je typické pro kategorické funkce, jako je třeba zeměpisná oblast, které nemají přirozeně smysluplnou číselnou reprezentaci. Je také vhodné pro číselné funkce, jejichž vliv na odměnu bude pravděpodobně nelineární. Daný článek může být například relevantní pro konkrétní věkovou skupinu a irelevantní pro kohokoli staršího nebo mladšího.

Jakákoli funkce s řetězcovou hodnotou používá ve výchozím nastavení kódovaní s 1 horkým bitem: pro každou možnou hodnotu je vytvořena samostatná interní funkce. Automatické kódování s 1 horkým bitem pro číselné funkce nebo s přizpůsobenými rozsahy není aktuálně poskytováno.

> [!TIP]
> Algoritmy strojového učení pracují se všemi možnými hodnotami dané interní funkce jednotným způsobem: s použitím jedné společné „váhy“. Kódování s 1 horkým bitem dovoluje mít oddělenou „váhu“ pro každý rozsah hodnot. Zmenšení rozsahů vede k lepším odměnám, jakmile se nashromáždí dostatek dat, ale může zvýšit objem dat potřebných pro konvergenci k lepším odměnám.

## <a name="feature-specification-format-and-apis"></a>Specifikace funkcí: formát a rozhraní API

Funkce můžete zadat přes několik pomocných rozhraní API. Všechna rozhraní API používají společný formát JSON. Níže jsou uvedena tato rozhraní API a formát na konceptuální úrovní. Specifikace je doplněna schématem typu Swagger.

Základní šablona JSON pro specifikaci funkce je následující:

```json
{
"<name>":<value>, "<name>":<value>, ... ,
"namespace1": {"<name>":<value>, ... },
"namespace2": {"<name>":<value>, ... },
...
}
```

`<name>` a `<value>` tu zastupují název, respektive hodnotu funkce. `<value>` může být řetězec, celé číslo, typ float, logická hodnota nebo pole. Funkce nezabalená do oboru názvů se automaticky přiřadí do výchozího oboru názvů.

Pokud chcete řetězec vyjádřit jako množinu slov (bag-of-words), použijte speciální syntaxi `"_text":"string"` namísto `"<name>":<value>`. Fakticky je pro každé slovo v řetězci vytvořena samostatná interní funkce. Její hodnota je pak počet výskytů tohoto slova.

Pokud `<name>` začíná podtržítkem „_“ (a není to `"_text"`), je tato funkce ignorována.

> [!TIP]
> Někdy sloučíte funkce z více zdrojů JSON. Pro usnadnění práce je můžete vyjádřit následujícím způsobem:
>
> ```json
> {
> "source1":<features>,
> "source2":<features>,
> ...
> }
> ```

`<features>` tu odkazuje na základní specifikaci funkce definovanou dříve. Jsou povoleny i hlubší úrovně „vnořování“. Služba Custom Decision Service automaticky vyhledá „nejhlubší“ objekty JSON, které můžou být interpretovány jako `<features>`.

#### <a name="feature-set-api"></a>Rozhraní Feature Set API

Rozhraní Feature Set API vrací seznam funkcí ve formátu JSON popsaném výše. Můžete použít několik koncových bodů Feature Set API. Každý koncový bod je identifikován pomocí ID sady funkcí a adresy URL. Mapování mezi ID sad funkcí a adresami URL se nastavuje na portálu.

Rozhraní Feature Set API voláte vložením odpovídajícího ID sady funkcí na příslušné místo ve formátu JSON. Pro funkce závislé na akci je volání automaticky parametrizováno pomocí ID akce. Můžete zadat několik ID sad funkcí pro stejnou akci.

#### <a name="action-set-api-json-version"></a>Rozhraní Action Set API (verze JSON)

Rozhraní Action Set API má verzi, ve které jsou akce a funkce zadány ve formátu JSON. Funkce je možné zadat explicitně nebo prostřednictvím rozhraní Feature Set API. Sdílené funkce je možné zadat jednou pro všechny akce.

#### <a name="ranking-api-http-post-call"></a>Rozhraní Ranking API (volání HTTP POST)

Rozhraní Ranking API má verzi, která používá volání HTTP POST. Tělo tohoto volání zadává akce a funkce prostřednictvím flexibilní syntaxe ve formátu JSON.

Akce můžou být zadány explicitně nebo prostřednictvím ID sad akcí. Kdykoli se narazí ID sady akcí, provede se volání na odpovídající koncový bod Action Set API.

Jako u rozhraní Action Set API můžou být akce zadány explicitně nebo prostřednictvím Feature Set API. Sdílené funkce je možné zadat jednou pro všechny akce.
