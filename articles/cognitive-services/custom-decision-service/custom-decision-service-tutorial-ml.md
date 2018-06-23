---
title: Strojového učení - kognitivní služby Azure | Microsoft Docs
description: Kurz pro machine learning v Azure vlastní rozhodnutí Service rozhraní API založené na cloudu kontextové rozhodnutí.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/08/2018
ms.author: slivkins;marcozo;alekh
ms.openlocfilehash: 50814d67ee39c6657954610358462d877843416e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343436"
---
# <a name="machine-learning"></a>Strojové učení

V tomto kurzu řeší pokročilé strojového učení funkce ve službě vlastní rozhodnutí. Tento kurz se skládá ze dvou částí: [featurization](#featurization-concepts-and-implementation) a [funkci specifikace](#feature-specification-format-and-apis). Featurization odkazuje na vaše data jako "funkce" představující pro machine learning. Funkce specifikace popisuje formátu JSON a pomocnou rozhraní API pro zadání funkce.

Ve výchozím nastavení je pro zákazníka transparentní strojového učení v Service vlastního rozhodnutí. Funkce automaticky se extrahují z vašeho obsahu a algoritmu učení standardní posílení slouží. Funkce extrakce využívá několik dalších kognitivní služeb Azure: [propojení Entity](../entitylinking/home.md), [Analýza textu](../text-analytics/overview.md), [rozpoznávání emocí úrovně](../emotion/home.md), a [počítače vize](../computer-vision/home.md). V tomto kurzu mohou být přeskočeny, pokud použijete jen výchozí funkce.

## <a name="featurization-concepts-and-implementation"></a>Featurization: koncepty a implementace

Vlastní služba rozhodnutí rozhoduje po jednom. Každé rozhodnutí zahrnuje výběru mezi několik alternativy, také znám jako, akce. V závislosti na aplikaci rozhodnutí zvolit jednu akci nebo (krátký) seřazený seznam akcí.

Například přizpůsobení výběru článků na titulní stránky webu. Zde akce odpovídat články a které články zobrazíte pro daného uživatele je každé rozhodnutí.

Každá akce je reprezentována vektoru vlastností, volá se od nynějška *funkce*. Můžete zadat nové funkce, kromě funkcí extrahovat automaticky. Můžete také určit, aby služba rozhodnutí vlastní protokolu některé funkce, ale je ignorovat pro machine learning.

### <a name="native-vs-internal-features"></a>Nativní oproti vnitřní funkce

Můžete určit funkce ve formátu nejvíce přirozené pro vaši aplikaci, je-li jej čísla, řetězce nebo pole. Tyto funkce se nazývají "nativní funkce." Vlastní služba rozhodnutí překládá každé nativní funkce do jednoho nebo více číselné funkce názvem "interní funkce."

Překlad do interní funkce vypadá takto:

- Číselné funkce zůstaly stejné.
- Číselná pole překládá několik číselné funkcí, jednu pro každý element pole.
- funkce vracející řetězec `"Name":"Value"` je ve výchozím nastavení přeložit na funkci s názvem `"NameValue"` a hodnotu 1.
- Volitelně můžete vyjádřené řetězec [kontejneru slova](https://en.wikipedia.org/wiki/Bag-of-words_model). Vnitřní funkce vytvoří se pro každého slova v řetězci, jehož hodnota je počet výskytů tato slova.
- Vnitřní funkce s hodnotou nula byly vynechány.

### <a name="shared-vs-action-dependent-features"></a>Sdílené oproti akce – závislé funkce

Některé funkce naleznete celý rozhodnutí a jsou stejné pro všechny akce. Říkáme je *sdílené součásti*. Některé další funkce jsou specifické pro určitou akci. Říkáme je *akce – závislé funkce* (ADFs).

V příkladu spuštěné může sdílené funkce popisují uživatele a/nebo stav na světě. Funkce, jako jsou informace o zeměpisné poloze, stáří a pohlaví uživatele a které hlavní událostem hned teď. Akce – závislé funkce může popisuje vlastnosti daný článek, jako je například v tématech uvedených v tomto článku.

### <a name="interacting-features"></a>Vzájemně komunikující funkce

Funkce často "interakci": účinku jeden závisí na jiné. Například funkce X je uvažujete o tom, zda uživatel je sportu. Funkce Y je, zda je daný článek o sportu. Účinek funkce Y je silně závislá na funkci X.

Aby se zohlednily interakce mezi funkcemi X a Y, vytvoření *kvadratické* funkce, jehož hodnota je X\*Y. (Také říkáme, "křížové" X a Y.) Můžete zvolit, které páry funkcí se překročí.

> [!TIP]
> Sdílená funkce měli překročí s akce – závislé funkce k ovlivnění jejich pořadí. O funkci závislé na akce by měl překročí s funkcí sdílené přispívala k přizpůsobení.

Jinými slovy sdílená funkce není překročen se žádné služby AD FS vliv každá akce stejným způsobem. ADF není překročen s jakékoli sdílené funkce příliš vliv každé rozhodnutí. Tyto typy funkcí může snížit odchylku odhady potřebu.

### <a name="implementation-via-namespaces"></a>Implementace prostřednictvím obory názvů

Na portálu můžete implementovat křížového funkce (stejně jako ostatní featurization koncepty) prostřednictvím "příkazový řádek zobrazit". Syntaxe je založena na [Vowpal k dispozici](http://hunch.net/~vw/) příkazového řádku.

Centrální pro implementaci je koncept *obor názvů*: podmnožinu funkcí s názvem. Jednotlivé funkce patří do přesně jeden obor názvů. Obor názvů lze explicitně zadat, pokud vlastní rozhodnutí služby je zadána hodnota funkce. Je jediným způsobem, jak odkazovat na funkci v příkazovém řádku zobrazit.

Obor názvů je "sdílené" nebo "závislé na akci": buď obsahuje pouze sdílené funkce, nebo obsahuje pouze akce – závislé funkce o stejnou akci.

> [!TIP]
> Je dobrým zvykem zalomení funkce v explicitně zadaných oborů názvů. Seskupte související funkce do stejného oboru názvů.

Pokud není k dispozici obor názvů, funkce se automaticky přiřadí k výchozí obor názvů.

> [!IMPORTANT]
> Funkce a obory názvů nemusíte být konzistentní v rámci akce. Obor názvů, může mít různé funkce pro různé akce. Kromě toho může být definováno daném oboru názvů pro některé akce a ne pro jiná.

Vnitřní funkce, které pocházejí ze stejné nativní funkce s hodnotou řetězce jsou seskupené do stejného oboru názvů. Jakékoli dvě nativní součásti, které se nacházejí v různých oborech názvů jsou zpracovány jako jedinečné, i v případě, že mají stejný název funkce.

> [!IMPORTANT]
> ID oboru názvů dlouhé a popisné jsou běžné, zobrazit příkazového řádku se nerozlišují obory názvů, jehož id při spuštění stejné písmeno. V jaké způsobem, obor názvů jsou ID jednoho písmena, například `x` a `y`.

Podrobnosti implementace jsou následující:

- Obory názvů překříží `x` a `y`, zápis `-q xy` nebo `--quadratic xy`. Potom jednotlivých funkcí v `x` je překročí s jednotlivých funkcí v `y`. Použití `-q x:` překříží `x` s každý obor názvů a `-q ::` překříží všechny páry oborů názvů.

- Ignorovat všechny funkce v oboru názvů `x`, zápis `--ignore x`.

Tyto příkazy se použijí na každou akci samostatně, vždy, když jsou definovány obory názvů.

### <a name="estimated-average-as-a-feature"></a>Odhadované průměr jako funkce

Jako vytvoří myšlenku experiment co by průměrná potřebu dané akce Pokud bylo vybráno k všechna rozhodnutí? Takové průměrná potřebu může jako "celkovou kvalitu" této akce. Nezná přesně vždy, když byla jiné akce vybrali místo v některé rozhodnutí. Však se dá odhadnout prostřednictvím posílení učení techniky. Tento odhad kvality obvykle zlepší v čase.

Můžete zahrnout tento "odhadované průměrná potřebu" jako funkce pro danou akci. Potom vlastní rozhodnutí služba by automaticky aktualizovat tento odhad jako dorazí nová data. Tato funkce je volána *okrajového funkce* této akce. Okrajového funkce lze použít pro machine learning a pro audit.

K přidání okrajového funkcí, zápis `--marginal <namespace>` v příkazovém řádku zobrazit. Definování `<namespace>` ve formátu JSON následujícím způsobem:

```json
{<namespace>: {"mf_name":1 "action_id":1}
```

Vložte tento obor názvů spolu s další akce – závislé funkce dané akce. Zadejte tuto definici pro každé rozhodnutí používající stejný `mf_name` a `action_id` pro všechna rozhodnutí.

Přidá se pro každou akci s funkci okrajového `<namespace>`. `action_id` Může být jakýkoli název funkce, která jednoznačně identifikuje akce. Název funkce je nastaven na `mf_name`. Konkrétně marginal funkcí s použitím různých `mf_name` jsou považovány za různé funkce – různé váhu se naučili pro každou `mf_name`.

Použití výchozí je, že `mf_name` je stejný pro všechny akce. Pak je pro všechny součásti okrajového zjistili jeden váhy.

Také můžete určit více okrajového funkcí pro stejnou akci, s stejné hodnoty, ale názvů různých funkcí.

```json
{<namespace>: {"mf_name1":1 "action_id":1 "mf_name2":1 "action_id":1}}
```

### <a name="1-hot-encoding"></a>1-hot kódování

Můžete k reprezentaci některých funkcí jako vektory bit, kde každý bit odpovídá rozsahu možných hodnot. Tento bit nastaven na 1, pokud funkci spočívá v tomto rozsahu. Proto je jeden bit "horkých", který je nastavený na hodnotu 1 a jiné jsou nastavena na hodnotu 0. Tento zápis se běžně označuje jako *horkou 1 kódování*.

Kódování horkou 1 je typický pro kategorií funkce jako je například "geografické oblasti", které nemají ze své podstaty smysluplný číselné vyjádření. Je také vhodné pro číselné funkce, jejichž vliv na potřebu by mohla mít jiný lineární. Daný článek může být například relevantní pro konkrétní skupinu stáří a závislé na každý, kdo starší nebo mladší.

Všechny funkce s hodnotou řetězce je 1 horkou kódovaný ve výchozím nastavení: odlišné interní funkci se vytvoří pro každou možná hodnota. Automatické horkou 1 kódování pro číselné funkce nebo s vlastní rozsahy aktuálně nezobrazují.

> [!TIP]
> Algoritmy strojového učení považovat všechny možné hodnoty danou interní funkci jednotného: prostřednictvím společného "váhy." Kódování horkou 1 umožňuje samostatné "váhu" pro každý rozsah hodnot. Provedení menší rozsahy vede k lepší výnosu po dostatek data se shromažďují, ale může se zvýšit množství dat potřebných k sloučit k lepší výnosu.

## <a name="feature-specification-format-and-apis"></a>Funkce specifikace: formát a rozhraní API

Můžete zadat funkce prostřednictvím několik pomocných rozhraní API. Všechna rozhraní API pomocí běžných formátu JSON. Níže jsou rozhraní API a formát na koncepční úrovni. Specifikace je doplněno schéma Swagger.

Základní šablona JSON pro funkci specifikace vypadá takto:

```json
{
"<name>":<value>, "<name>":<value>, ... ,
"namespace1": {"<name>":<value>, ... },
"namespace2": {"<name>":<value>, ... },
...
}
```

Zde `<name>` a `<value>` nechá název funkce a funkce hodnota. `<value>` může být řetězec, celé číslo, plovoucí desetinné čárky, logická hodnota nebo pole. Funkce není zabalené do oboru názvů bude automaticky přiřazen do výchozí obor názvů.

K reprezentaci řetězce jako kontejner objektů a dat slova, použijte speciální syntaxi `"_text":"string"` místo `"<name>":<value>`. Samostatné vnitřní funkce efektivně, se vytvoří pro každého slova v řetězci. Její hodnota je počet výskytů tato slova.

Pokud `<name>` začíná "_" (a není `"_text"`), pak tato funkce je ignorována.

> [!TIP]
> Někdy sloučení funkce z více zdrojů JSON. Pro větší pohodlí si můžete vyjádřit je následujícím způsobem:
>
> ```json
> {
> "source1":<features>,
> "source2":<features>,
> ...
> }
> ```

Zde `<features>` odkazuje na základní funkce specifikaci definovanou dříve. Hlubší úrovní vnoření"," jsou příliš povoleny. Vlastní rozhodnutí služby automaticky vyhledá "nejhlubší" objekty JSON, které lze interpretovat jako `<features>`.

#### <a name="feature-set-api"></a>Funkce sadu rozhraní API

Rozhraní API nastavit funkce vrátí seznam funkcí ve formátu JSON, které jsou popsané. Můžete použít několik koncových bodů API nastavit funkce. Každý koncový bod je určený podle id sady funkcí a adresu URL. Mapování mezi funkce ID a adresy URL je nastaven na portálu.

Volání rozhraní API nastavit funkce vložením odpovídající id sady funkcí na příslušné místo ve formátu JSON. Pro akce – závislé funkce je automaticky volání parametry podle id akce. Můžete zadat několik funkci nastavit ID pro stejnou akci.

#### <a name="action-set-api-json-version"></a>Akce nastavit API (JSON verze)

Rozhraní API nastavit akci má na verzi, ve kterém akce a funkce nejsou zadány ve formátu JSON. Funkce lze zadat explicitně nebo přes rozhraní API nastavit funkce. Sdílené funkce lze zadat jednou pro všechny akce.

#### <a name="ranking-api-http-post-call"></a>Řazení rozhraní API (volání HTTP POST)

Řazení API má verzi, která používá volání HTTP POST. Text tohoto volání určuje funkce prostřednictvím flexibilní syntaxe JSON a akce.

Akce může být explicitně zadané nebo pomocí akce nastavit ID. Vždy, když je zjištěna id sady akce, je provést volání do odpovídající koncový bod rozhraní API nastavit akce.

Funkce jako akce nastavit rozhraní API, je možné zadat explicitně nebo přes rozhraní API nastavit funkce. Sdílené funkce lze zadat jednou pro všechny akce.