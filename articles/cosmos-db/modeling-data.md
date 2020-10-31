---
title: Modelování dat v Azure Cosmos DB
titleSuffix: Azure Cosmos DB
description: Přečtěte si o modelování dat v databázích NoSQL, rozdílech mezi daty modelování v relační databázi a databází dokumentů.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 0868b0d3e917b857d09c89e3a35d03872c42a23e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096644"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Modelování dat v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Databáze bez schématu, jako je například Azure Cosmos DB, usnadňují ukládání a dotazování nestrukturovaných a částečně strukturovaných dat, měli byste věnovat si nějaké informace o datovém modelu a získat tak většinu služby z pohledu výkonu a škálovatelnosti a nejnižších nákladů.

Jak se data budou ukládat? Jak vaše aplikace načítá data a dotazuje se na ně? Je vaše aplikace těžká pro čtení, nebo pro zápis?

Po přečtení tohoto článku budete moci zodpovědět následující otázky:

* Co je modelování dat a proč se mám zajímat?
* Jak se data modelování v Azure Cosmos DB liší od relační databáze?
* Návody relace Express data v nerelační databázi?
* Kdy vložit data a kdy se mám propojit s daty?

## <a name="embedding-data"></a>Vkládání dat

Když začnete sestavovat data v Azure Cosmos DB pokusíte se zacházet s entitami jako se **samostatnými položkami** , které jsou reprezentované jako dokumenty JSON.

V případě porovnání si nejdřív projdeme, jak můžeme modelovat data v relační databázi. Následující příklad ukazuje, jak může být osoba uložená v relační databázi.

:::image type="content" source="./media/sql-api-modeling-data/relational-data-model.png" alt-text="Model relační databáze" border="false":::

Při práci s relačními databázemi je strategie normalizovat všechna vaše data. Normalizace dat obvykle zahrnuje pořízení entity, jako je třeba osoba, a její rozdělení do diskrétních součástí. V předchozím příkladu může osoba mít několik záznamů s podrobnostmi kontaktů a také několik záznamů adres. Kontaktní údaje mohou být dále rozděleny další extrakcí společných polí, jako je typ. Totéž platí pro adresu, každý záznam může být typu *Home* nebo *Business* .

Základní GUID při normalizaci dat je **vyhnout se ukládání redundantních dat** u každého záznamu a místo toho je třeba odkazovat na data. Chcete-li v tomto příkladu číst osobu se všemi kontaktními údaji a adresami kontaktů, je nutné použít spojení k efektivnímu psaní (nebo denormalizaci) dat v době běhu.

```sql
SELECT p.FirstName, p.LastName, a.City, cd.Detail
FROM Person p
JOIN ContactDetail cd ON cd.PersonId = p.Id
JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
JOIN Address a ON a.PersonId = p.Id
```

Aktualizace jedné osoby s jejich kontaktními informacemi a adresami vyžaduje operace zápisu napříč mnoha jednotlivými tabulkami.

Teď se podíváme na to, jak by bylo vhodné modelovat stejná data jako samostatná entita v Azure Cosmos DB.

```json
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "addresses": [
        {
            "line1": "100 Some Street",
            "line2": "Unit 1",
            "city": "Seattle",
            "state": "WA",
            "zip": 98012
        }
    ],
    "contactDetails": [
        {"email": "thomas@andersen.com"},
        {"phone": "+1 555 555-5555", "extension": 5555}
    ]
}
```

Pomocí výše uvedeného postupu jsme **denormalizováni** záznam osoby tím, že **vložíte** všechny informace týkající se této osoby, jako jsou kontaktní údaje a adresy, do *jednoho dokumentu JSON* .
Kromě toho, vzhledem k tomu, že jsme neomezeni na pevné schéma, máme flexibilitu v tom, aby bylo možné provádět věci, jako byste měli k dispozici pouze kontaktní údaje různých tvarů.

Načtení záznamu kompletní osoby z databáze je nově **jedna operace čtení** proti jednomu kontejneru a pro jednu položku. Aktualizace záznamu osoby s jeho kontaktními údaji a adresami je zároveň **jedna operace zápisu** na jednu položku.

Po denormalizaci dat může vaše aplikace potřebovat vydávat méně dotazů a aktualizací, aby bylo možné provádět běžné operace.

### <a name="when-to-embed"></a>Kdy vložit

V obecném případě použijte vložené datové modely v těchto případech:

* Mezi entitami je **obsažena** relace.
* Mezi entitami existuje relace **1:1** .
* K dispozici jsou vložená data, která se **mění zřídka** .
* Existují vložená data, která se nezvětšují **bez vazby** .
* K dispozici jsou vložená data, která se **často dotazují** .

> [!NOTE]
> Typicky denormalizované datové modely poskytují lepší výkon při **čtení** .

### <a name="when-not-to-embed"></a>Kdy Nevkládat

I když pravidlo palce v Azure Cosmos DB slouží k denormalizování všeho a vložení všech dat do jediné položky, může to vést k nějakým situacím, které by se měly vyvarovat.

Proveďte tento fragment kódu JSON.

```json
{
    "id": "1",
    "name": "What's new in the coolest Cloud",
    "summary": "A blog post by someone real famous",
    "comments": [
        {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
        {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
        …
        {"id": 100001, "author": "jane", "comment": "and on we go ..."},
        …
        {"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
        …
        {"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
    ]
}
```

Může to být tím, že entita příspěvku s vloženými komentáři by vypadala jako při vytváření modelů typického blogu nebo CMS, systému. Problém s tímto příkladem je, že pole komentáře je **neohraničené** , což znamená, že neexistuje (praktické) omezení na počet komentářů, který může mít každý příspěvek. Může se jednat o problém, protože velikost položky by mohla růst nekonečně velká.

Jak velikost položky zvětšuje schopnost přenášet data prostřednictvím sítě a také číst a aktualizovat položku ve velkém měřítku, bude to mít vliv na.

V takovém případě by bylo lepší zvážit následující datový model.

```json
Post item:
{
    "id": "1",
    "name": "What's new in the coolest Cloud",
    "summary": "A blog post by someone real famous",
    "recentComments": [
        {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
        {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
        {"id": 3, "author": "jane", "comment": "....."}
    ]
}

Comment items:
{
    "postId": "1"
    "comments": [
        {"id": 4, "author": "anon", "comment": "more goodness"},
        {"id": 5, "author": "bob", "comment": "tails from the field"},
        ...
        {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
    ]
},
{
    "postId": "1"
    "comments": [
        {"id": 100, "author": "anon", "comment": "yet more"},
        ...
        {"id": 199, "author": "bored", "comment": "will this ever end?"}
    ]
}
```

Tento model obsahuje tři nejaktuálnější Komentáře vložené v zásobníku post, což je pole s pevnou sadou atributů. Ostatní komentáře jsou seskupeny do dávek 100 komentářů a uloženy jako samostatné položky. Velikost dávky byla zvolena jako 100, protože naše fiktivní aplikace umožňuje uživateli načíst 100 komentáře v čase.  

Další případ, kdy vkládání dat není dobrý nápad, je, že vložená data se často používají napříč položkami a často se mění.

Proveďte tento fragment kódu JSON.

```json
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "holdings": [
        {
            "numberHeld": 100,
            "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
        },
        {
            "numberHeld": 50,
            "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
        }
    ]
}
```

To může představovat portfolio akcií osoby. Zvolili jsme vložení informací o akcií do každého dokumentu portfolia. V prostředí, kde se často mění související data, jako je burzovní obchodní aplikace, se vkládá data, která se často mění, což znamená, že průběžně aktualizujete každý dokument portfolia při každém obchodování se zásobami.

Burzovní *Zaza* se můžou v jednom dni projednat spoustou času a tisíce uživatelů by na jejich portfolio mohli *Zaza* . S datovým modelem, jako je výše, bychom museli každý den aktualizovat spoustu tisíc dokumentů portfolia, což vede k systému, který se nebude dobře škálovat.

## <a name="referencing-data"></a>Odkazování na data

Vkládání dat v mnoha případech je důležité, ale scénáře denormalizace dat způsobují více problémů, než stojí. Co to teď udělat?

Relační databáze nejsou jediným místem, kde můžete vytvářet relace mezi entitami. V databázi dokumentů můžete mít informace v jednom dokumentu, které se vztahují k datům v jiných dokumentech. Nedoporučujeme vytvářet systémy, které by byly lépe vhodné pro relační databázi v Azure Cosmos DB, nebo v jakékoli jiné databázi dokumentů, ale jednoduché relace jsou přesné a můžou být užitečné.

Ve formátu JSON níže jsme se rozhodli použít příklad portfolia akcií z předchozích verzí, ale tentokrát odkazujeme na skladovou položku v portfoliu místo jejího vložení. To znamená, že pokud se skladová položka často mění v průběhu dne, jediný dokument, který je třeba aktualizovat, je jediný dokument s cenným papírem.

```json
Person document:
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "holdings": [
        { "numberHeld":  100, "stockId": 1},
        { "numberHeld":  50, "stockId": 2}
    ]
}

Stock documents:
{
    "id": "1",
    "symbol": "zaza",
    "open": 1,
    "high": 2,
    "low": 0.5,
    "vol": 11970000,
    "mkt-cap": 42000000,
    "pe": 5.89
},
{
    "id": "2",
    "symbol": "xcxc",
    "open": 89,
    "high": 93.24,
    "low": 88.87,
    "vol": 2970200,
    "mkt-cap": 1005000,
    "pe": 75.82
}
```

Bezprostředním Nevýhodou tohoto přístupu je, že pokud je vaše aplikace nutná k zobrazení informací o každé populaci, která je držena při zobrazení portfolia osoby; v takovém případě byste potřebovali vytvořit více cest k databázi, aby se načetly informace pro každý burzovní dokument. Tady jsme udělali rozhodnutí, abychom vylepšili efektivitu operací zápisu, ke kterým dochází často během dne, ale zároveň se tím přestala ohrozit operace čtení, které mohou mít méně vliv na výkon tohoto konkrétního systému.

> [!NOTE]
> Normalizované datové modely **můžou vyžadovat více zpátečních cest** k serveru.

### <a name="what-about-foreign-keys"></a>Co jsou cizí klíče?

Vzhledem k tomu, že neexistuje koncept omezení, cizí klíč ani jinak, všechny vztahy mezi dokumenty, které máte v dokumentech, jsou efektivně "slabé odkazy" a nebudou ověřovány samotnými databázemi. Pokud chcete zajistit, aby data, která odkazuje na daný dokument, byla skutečně existovat, musíte to provést ve své aplikaci nebo pomocí triggerů na straně serveru nebo uložených procedur na Azure Cosmos DB.

### <a name="when-to-reference"></a>Kdy odkazovat

Obecně používejte normalizované datové modely v těchto případech:

* Reprezentace vztahů **1: n** .
* Reprezentace vztahů **m:n** .
* Změny souvisejících dat jsou **často časté** .
* Odkazovaná data by mohla být **neohraničená** .

> [!NOTE]
> Obvykle normalizace poskytuje lepší výkon **zápisu** .

### <a name="where-do-i-put-the-relationship"></a>Kam se má vztah přesunout?

Nárůst vztahu vám pomůže určit, ve kterém dokumentu se má odkaz uložit.

Pokud se podíváme na JSON níže, které modely vydavatelé a knihy.

```json
Publisher document:
{
    "id": "mspress",
    "name": "Microsoft Press",
    "books": [ 1, 2, 3, ..., 100, ..., 1000]
}

Book documents:
{"id": "1", "name": "Azure Cosmos DB 101" }
{"id": "2", "name": "Azure Cosmos DB for RDBMS Users" }
{"id": "3", "name": "Taking over the world one JSON doc at a time" }
...
{"id": "100", "name": "Learn about Azure Cosmos DB" }
...
{"id": "1000", "name": "Deep Dive into Azure Cosmos DB" }
```

Pokud je počet knih na vydavatele malý s omezeným nárůstem, může být užitečné ukládat odkaz na knihu v dokumentu vydavatele. Pokud je ale počet knih na vydavatele neohraničený, pak tento datový model vede ke proměnlivým a rostoucím polím, jak je uvedeno výše v dokumentu ukázkový Vydavatel výše.

Přepnutím položek do bitu by došlo k tomu, že model, který stále představuje stejná data, ale nyní brání těmto velkým proměnlivým kolekcím.

```json
Publisher document:
{
    "id": "mspress",
    "name": "Microsoft Press"
}

Book documents:
{"id": "1","name": "Azure Cosmos DB 101", "pub-id": "mspress"}
{"id": "2","name": "Azure Cosmos DB for RDBMS Users", "pub-id": "mspress"}
{"id": "3","name": "Taking over the world one JSON doc at a time"}
...
{"id": "100","name": "Learn about Azure Cosmos DB", "pub-id": "mspress"}
...
{"id": "1000","name": "Deep Dive into Azure Cosmos DB", "pub-id": "mspress"}
```

Ve výše uvedeném příkladu jsme zrušili nevázanou kolekci v dokumentu vydavatele. Místo toho máme v dokumentu knihy odkaz na vydavatele.

### <a name="how-do-i-model-manymany-relationships"></a>Návody model řady: mnoho vztahů?

V relačních databázích *mnoho: mnoho* relací je často modelů s spojovacími tabulkami, které slouží pouze k propojení záznamů z jiných tabulek.


:::image type="content" source="./media/sql-api-modeling-data/join-table.png" alt-text="Model relační databáze" border="false":::

Můžete se rozhodnout, že budete replikovat stejnou věc s použitím dokumentů a vytvořit datový model, který vypadá podobně jako následující.

```json
Author documents:
{"id": "a1", "name": "Thomas Andersen" }
{"id": "a2", "name": "William Wakefield" }

Book documents:
{"id": "b1", "name": "Azure Cosmos DB 101" }
{"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
{"id": "b3", "name": "Taking over the world one JSON doc at a time" }
{"id": "b4", "name": "Learn about Azure Cosmos DB" }
{"id": "b5", "name": "Deep Dive into Azure Cosmos DB" }

Joining documents:
{"authorId": "a1", "bookId": "b1" }
{"authorId": "a2", "bookId": "b1" }
{"authorId": "a1", "bookId": "b2" }
{"authorId": "a1", "bookId": "b3" }
```

To bude fungovat. Načtením autora do svých knih nebo načtením knihy se svým autorem ale bude vždycky vyžadovat aspoň dva další dotazy na databázi. Jeden dotaz do spojovacího dokumentu a pak jiný dotaz k načtení aktuálně připojeného dokumentu.

Pokud je všechny tyto spojovací tabulky spojování společně se dvěma částmi dat, nechcete ji úplně vyřadit?
Vezměte v úvahu následující skutečnosti.

```json
Author documents:
{"id": "a1", "name": "Thomas Andersen", "books": ["b1", "b2", "b3"]}
{"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

Book documents:
{"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
{"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
{"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
{"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}
```

Teď, když mám autora, okamžitě poznáte, které knihy napsaly, a naopak, pokud mám načtený dokument z knihy by znal ID autorů. Tím se uloží tento zprostředkující dotaz proti tabulce JOIN, čímž se sníží počet přenosů serveru, které musí vaše aplikace dělat.

## <a name="hybrid-data-models"></a>Hybridní datové modely

Nyní jsme prohlédli vkládání (nebo denormalizace) a odkazování na data (nebo normalizaci), z nichž každá má své hrany a každá z nich má zabezpečení, jak jsme viděli.

Nemusíte vždycky mít ani jednu z těchto možností, nemusíte děsili.

Na základě specifických vzorů a úloh používání vaší aplikace může dojít k případům, kdy je smysl kombinování integrovaných a odkazovaných dat, a může vést k jednodušší aplikační logice s menším počtem zpátečních cest serveru, přičemž stále udržuje dobrou úroveň výkonu.

Vezměte v úvahu následující JSON.

```json
Author documents:
{
    "id": "a1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "countOfBooks": 3,
    "books": ["b1", "b2", "b3"],
    "images": [
        {"thumbnail": "https://....png"}
        {"profile": "https://....png"}
        {"large": "https://....png"}
    ]
},
{
    "id": "a2",
    "firstName": "William",
    "lastName": "Wakefield",
    "countOfBooks": 1,
    "books": ["b1"],
    "images": [
        {"thumbnail": "https://....png"}
    ]
}

Book documents:
{
    "id": "b1",
    "name": "Azure Cosmos DB 101",
    "authors": [
        {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
        {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "https://....png"}
    ]
},
{
    "id": "b2",
    "name": "Azure Cosmos DB for RDBMS Users",
    "authors": [
        {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
    ]
}
```

Tady jsme (hlavně) následovali vložený model, ve kterém jsou data z jiných entit vložená v dokumentu nejvyšší úrovně, ale odkaz na další data.

Pokud se podíváte na dokument knihy, uvidíme několik zajímavých polí, když se podíváme na pole autorů. K dispozici je `id` pole, které používáme k odkazování zpátky na vytvořený dokument, standardní postupy v normalizovaném modelu, ale také máme `name` a `thumbnailUrl` . Mohli jsme aplikaci zablokovat a pojmenovat `id` tak, aby z příslušného autorského dokumentu získali všechny další informace, které potřebuje, pomocí odkazu, ale vzhledem k tomu, že naše aplikace zobrazuje jméno autora a miniaturu, a to s každou otevřenou knihou, můžeme v seznamu odkázat na server na jednu knihu **some** tím, že se odnormalizují data od autora.

Ujistěte se, že pokud se změnil název autora nebo chce aktualizovat fotografii, musíme si projít a aktualizovat každou knihu, kterou předtím publikovali, ale pro naši aplikaci, a to na základě předpokladu, že autoři nezměnili jejich názvy často, jedná se o přijatelné rozhodnutí o návrhu.  

V tomto příkladu jsou **předem vypočtené agregované** hodnoty pro ukládání nákladného zpracování operace čtení. V příkladu jsou některá data vložená v dokumentu autora data počítána za běhu. Pokaždé, když se publikuje nová kniha, vytvoří se dokument knihy **a** pole countOfBooks se nastaví na vypočtenou hodnotu na základě počtu dokumentů knihy, které existují pro určitého autora. Tato optimalizace by byla dobrá pro čtení těžkých systémů, kde můžeme pro účely optimalizace čtení provádět výpočty na zápisy.

Možnost mít model s předem vypočítanými poli je možná, protože Azure Cosmos DB podporuje transakce s **více dokumenty** . Mnoho úložišť NoSQL nemůže dělat transakce napříč dokumenty a proto rozhodování o návrhu, jako je "vždy vkládat vše", z důvodu tohoto omezení. Pomocí Azure Cosmos DB můžete použít triggery na straně serveru nebo uložené procedury, které v rámci transakce s kyselým obsahem vloží všechny knihy a tvůrci aktualizací. Teď **nemusíte** vkládat vše do jednoho dokumentu, abyste měli jistotu, že vaše data zůstanou konzistentní.

## <a name="distinguishing-between-different-document-types"></a>Odlišení mezi různými typy dokumentů

V některých scénářích může být vhodné kombinovat různé typy dokumentů ve stejné kolekci. obvykle se jedná o případ, kdy chcete do stejného [oddílu](partitioning-overview.md)zasedat více souvisejících dokumentů. Můžete například do jedné kolekce umístit recenze knih a knih a rozdělit je na oddíly `bookId` . V takové situaci obvykle chcete do dokumentů přidat pole, které určuje jejich typ, aby je bylo možné odlišit.

```json
Book documents:
{
    "id": "b1",
    "name": "Azure Cosmos DB 101",
    "bookId": "b1",
    "type": "book"
}

Review documents:
{
    "id": "r1",
    "content": "This book is awesome",
    "bookId": "b1",
    "type": "review"
},
{
    "id": "r2",
    "content": "Best book ever!",
    "bookId": "b1",
    "type": "review"
}
```

## <a name="next-steps"></a>Další kroky

Největším poznatky z tohoto článku je pochopit, že modelování dat na světě bez schématu je důležité jako kdy dřív.

Stejně jako neexistuje žádný jediný způsob, jak znázornit data na obrazovce, neexistuje jediný způsob, jak modelovat data. Musíte pochopit, jak vaše aplikace bude vytvářet, spotřebovávat a zpracovávat data. Pak můžete použít některé z uvedených pokynů, které vám pomůžou vytvořit model, který řeší okamžité potřeby vaší aplikace. Když se vaše aplikace musí změnit, můžete využít flexibilitu databáze bez schématu, abyste mohli snadno použít tuto změnu a snadno rozvíjet datový model.

Další informace o Azure Cosmos DB najdete na stránce [dokumentace](https://azure.microsoft.com/documentation/services/cosmos-db/) ke službě.

Další informace o tom, jak horizontálních oddílů data napříč více oddíly, najdete [v tématu dělení dat v Azure Cosmos DB](partitioning-overview.md).

Pokud se chcete dozvědět, jak modelovat data a rozdělit je na Azure Cosmos DB s využitím reálného příkladu, přečtěte si téma [ modelování a dělení dat – příklad Real-World](how-to-model-partition-example.md).
