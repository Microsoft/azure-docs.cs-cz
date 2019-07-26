---
title: Modelování dat v Azure Cosmos DB
titleSuffix: Azure Cosmos DB
description: Další informace o modelování dat v databázích NoSQL, rozdíly mezi modelování dat v relační databázi a databázi dokumentů.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.custom: rimman
ms.openlocfilehash: da119b2858c6b6c7bbc99b40d340f79964e0fae3
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467885"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Modelování dat v Azure Cosmos DB

Databáze bez schématu, jako je například Azure Cosmos DB, usnadňují ukládání a dotazování nestrukturovaných a částečně strukturovaných dat, měli byste věnovat si nějaké informace o datovém modelu a získat tak většinu služby z pohledu výkonu a škálovatelnosti a nejnižší ze.

Jak probíhá data k uložení? Jak se vaše aplikace bude k načtení a dotazování dat? Je vaše aplikace těžká pro čtení, nebo pro zápis?

Po přečtení tohoto článku, budou moci odpovědět na následující otázky:

* Co je modelování dat a proč by měli starat?
* Jak se data modelování v Azure Cosmos DB liší od relační databáze?
* Jak můžu express relací mezi daty nerelační databáze?
* Při vkládání dat a když je propojení s daty?

## <a name="embedding-data"></a>Vkládání dat

Když začnete sestavovat data v Azure Cosmos DB pokusíte se zacházet s entitami jako se **samostatnými položkami** , které jsou reprezentované jako dokumenty JSON.

V případě porovnání si nejdřív projdeme, jak můžeme modelovat data v relační databázi. Následující příklad ukazuje, jak osoba můžou být uložená v relační databázi.

![Model relační databáze](./media/sql-api-modeling-data/relational-data-model.png)

Při práci s relačními databázemi je strategie normalizovat všechna vaše data. Normalizace dat obvykle zahrnuje pořízení entity, jako je třeba osoba, a její rozdělení do diskrétních součástí. V předchozím příkladu může osoba mít několik záznamů s podrobnostmi kontaktů a také několik záznamů adres. Kontaktní údaje mohou být dále rozděleny další extrakcí společných polí, jako je typ. Totéž platí pro adresu, každý záznam může být typu *Home* nebo *Business*.

Která bude obsahovat místní normalizace dat se **Vyhněte se ukládání redundantních dat** na každý záznam a místo toho odkazovat na data. Chcete-li v tomto příkladu číst osobu se všemi kontaktními údaji a adresami kontaktů, je nutné použít spojení k efektivnímu psaní (nebo denormalizaci) dat v době běhu.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Aktualizace jedné osobě s jejich kontaktní údaje a adresy vyžaduje operací zápisu napříč mnoha jednotlivé tabulky.

Teď se podíváme na to, jak by bylo vhodné modelovat stejná data jako samostatná entita v Azure Cosmos DB.

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

Pomocí výše uvedeného postupu jsme denormalizováni záznam osoby tím, že **vložíte** všechny informace týkající se této osoby, jako jsou kontaktní údaje a adresy, do *jednoho dokumentu JSON* .
Navíc protože jsme nejsou omezeny na pevné schéma máme flexibilitu k provádění akcí, jako byste měli zcela kontaktní údaje z různých tvarů.

Načtení záznamu kompletní osoby z databáze je nově **jedna operace čtení** proti jednomu kontejneru a pro jednu položku. Aktualizace záznamu osoby s jeho kontaktními údaji a adresami je zároveň **jedna operace zápisu** na jednu položku.

Podle denormalizing dat, vaše aplikace může potřebovat vydat menšího počtu dotazů a aktualizace dokončete běžných operací.

### <a name="when-to-embed"></a>Kdy se má vložit

Vložená data se obvykle používá při modely:

* Mezi entitami je **obsažena** relace.
* Existují **jeden několik** vztahů mezi entitami.
* Je vložená data, která **mění jen zřídka**.
* Existují vložená data, která se nezvětšují **bez vazby**.
* K dispozici jsou vložená data, která se **často dotazují**.

> [!NOTE]
> Obvykle Nenormalizovaná data modely poskytují lepší **čtení** výkonu.

### <a name="when-not-to-embed"></a>Kdy nepoužívat pro vložení

I když pravidlo palce v Azure Cosmos DB slouží k denormalizování všeho a vložení všech dat do jediné položky, může to vést k nějakým situacím, které by se měly vyvarovat.

Využijte tento fragment kódu JSON.

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

To může být to, co entita příspěvek s vložené komentáře může vypadat třeba jsme modelování byly typické blogu nebo systému CMS. Problém s v tomto příkladu je, že je pole komentáře **bez vazby**, což znamená, že neexistuje žádné omezení (praktické) počet komentářů může mít libovolný jeden příspěvek. Může se jednat o problém, protože velikost položky by mohla růst nekonečně velká.

Jak velikost položky zvětšuje schopnost přenášet data prostřednictvím sítě a také číst a aktualizovat položku ve velkém měřítku, bude to mít vliv na.

V takovém případě by bylo lepší zvážit následující datový model.

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

Tento model obsahuje tři nejaktuálnější Komentáře vložené v zásobníku post, což je pole s pevnou sadou atributů. Ostatní komentáře jsou seskupeny do dávek 100 komentářů a uloženy jako samostatné položky. Velikost dávky byla vybrána jako 100 protože fiktivní aplikaci mu umožní načíst 100 komentáře v čase.  

Další případ, kdy vkládání dat není dobrý nápad, je, že vložená data se často používají napříč položkami a často se mění.

Využijte tento fragment kódu JSON.

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

To může představovat uložených portfolia osoby. Zvolili jsme vložení informací o akcií do každého dokumentu portfolia. V prostředí, ve kterém související data se často mění například akcie obchodní aplikace, vkládání dat, která se často mění se to znamenat pokaždé, když se prodávají stejných akcií se neustále aktualizuje každý dokument portfolia.

Stock *zaza* může být prodávají stovky časy v jediném den a tisíce uživatelů může mít *zaza* na jejich portfolia. S datovým modelem, který je uveden výše budeme něco muset aktualizovat tisíce portfolia dokumenty v mnoha případech každý den, což vede k systému, který nebude jednoduše škálovat.

## <a name="referencing-data"></a>Odkazování na data

Vkládání dat v mnoha případech je důležité, ale scénáře denormalizace dat způsobují více problémů, než stojí. Tak co můžeme udělat teď?

Relační databáze nejsou jediným místem, kde můžete vytvářet vztahy mezi entitami. V databázi dokumentů můžete mít informace v jednom dokumentu, které se vztahují k datům v jiných dokumentech. Nedoporučujeme vytvářet systémy, které by byly lépe vhodné pro relační databázi v Azure Cosmos DB, nebo v jakékoli jiné databázi dokumentů, ale jednoduché relace jsou přesné a můžou být užitečné.

V následující JSON jsme se rozhodli používat na příklad, základní portfolio z dříve, ale tentokrát označujeme skladová položka Portfolio místo jeho vložení. Tímto způsobem, při skladová položka mění mnohokrát za den jediný dokument, který je potřeba aktualizovat je jeden uložených dokumentů.

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

Okamžité nevýhodou tohoto přístupu je ale pokud je vaše aplikace vyžaduje k zobrazení informací o každé populace, která se nachází při zobrazení portfolia osoby; v tomto případě je třeba provést více cest k databázi se načíst informace pro každý dokument uložených. Tady jsme rozhodnutí ke zvýšení efektivity operace zápisu, které stát mnohokrát za den, ale pak dojde k ohrožení bezpečnosti na operace čtení, které můžou mít menší dopad na výkon této konkrétní systém.

> [!NOTE]
> Normalizovaná datové modely **může vyžadovat více výměn** k serveru.

### <a name="what-about-foreign-keys"></a>A co cizí klíče?

Protože nyní neexistuje koncept omezení, cizí klíč nebo jinak, případné relace mezi dokumentu, které máte v dokumentech jsou účinně "slabé odkazy" a se dá ověřit pomocí samotná databáze. Pokud chcete zajistit, aby data, která odkazuje dokument na skutečně existuje, je potřeba to udělat v aplikaci nebo pomocí aktivační události na straně serveru nebo uložené procedury ve službě Azure Cosmos DB.

### <a name="when-to-reference"></a>Kdy se má odkazovat

Obecně platí, normalizovaná data použít při modely:

* Představující **jeden mnoho** vztahy.
* Představující **many-to-many** vztahy.
* Související data **často mění**.
* Využívaných dat může být **bez vazby**.

> [!NOTE]
> Normalizace obvykle poskytuje lepší **zápisu** výkonu.

### <a name="where-do-i-put-the-relationship"></a>Kam můžu dát relace?

Růst relace vám pomůže určit, který dokument pro uložení odkazu.

Když se podíváte na JSON, níže, který modeluje vydavatele a knihy.

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

Pokud je malá růstu omezený počet knih podle vydavatele, pak ukládání odkaz knihy uvnitř vydavatele dokumentu může být užitečné. Nicméně pokud počet knih podle vydavatele je bez vazby, pak tento datový model povede k měnitelný a rostoucí pole, stejně jako v dokumentu vydavatele příklad výše.

Přepínání věcí kolem trochu způsobovaly v modelu, který představuje stále stejná data, ale teď předchází tyto velké proměnlivé kolekce.

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

V předchozím příkladu jsme vynechali kolekci bez vazby na dokument vydavatele. Místo toho jsme právě obsahovat odkaz na vydavatele u každé knihy dokumentu.

### <a name="how-do-i-model-manymany-relationships"></a>Jak můžu modelovat vztahy m: n?

V relační databázi *m: n* vztahy jsou často modelovány pomocí spojení tabulek, které právě spojení záznamy z jiných tabulek.

![Spojení tabulek](./media/sql-api-modeling-data/join-table.png)

Můžete mít tendenci replikovat stejnou věc použití dokumentů a vytvořit datový model, který bude vypadat nějak takto.

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

To by fungovalo. Ale načítají se buď Autor s jejich knihy nebo načítání knihy s jeho autor by vždy vyžadují alespoň dva další dotazy na databázi. Jeden dotaz na spojovacího dokument a pak další dotaz pro načtení vlastního dokumentu je připojen.

Pokud vše, co dělá tuto tabulku spojení je připevnit společně dva druhy dat, pak případně proč bezpečná není ho můžete vypustit zcela?
Mějte na paměti.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}

Teď, když mám autora, okamžitě poznáte, které knihy napsaly, a naopak, pokud mám načtený dokument z knihy by znal ID autorů. Toto uloží zprostředkující dotazu proti spojení tabulek snižuje počet serverů zaokrouhlit zkracuje dobu odezvy, které má vaše aplikace provést.

## <a name="hybrid-data-models"></a>Hybridní datové modely

Podívali jsme se nyní vkládání (nebo denormalizing) a odkazující (nebo normalizace) data, mají jejich upsides a mají ohrožení, jako jsme viděli.

Vždy nemusí to být buď nebo není možné kombinovat věci trochu děsili toho.

Na základě vzorce konkrétní používání vaší aplikace a procesy, které můžou nastat případy, kde vložené kombinování a využívaných dat dává smysl a může vést k jednodušší aplikace logiky s menším počtem server zaokrouhlit zkracuje dobu odezvy a přitom zachovávat funkční úroveň výkonu.

Vezměte v úvahu následující JSON.

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

Tady jsme postupovali (většinou) vloženým modelem, kde data z jiné entity jsou vložené v nejvyšší úrovni dokumentu, ale ostatní data se odkazuje.

Když se podíváte na dokument adresáře, můžeme vidět některé zajímavé pole, když se podíváme na pole autoři. K dispozici `id` je pole, které používáme k odkazování zpátky na vytvořený dokument, standardní postupy v normalizovaném modelu, ale `name` také máme a `thumbnailUrl`. Mohli jsme aplikaci zablokovat `id` a ponechali ji, aby získala další informace, které potřebuje z příslušného autorského dokumentu pomocí odkazu, ale vzhledem k tomu, že naše aplikace zobrazuje jméno autora a miniaturu s každou knihou. zobrazí se zpráva o tom, že se na server na knihu v seznamu dá ušetřit zpráva tím, že se odnormalizují **data od** autora.

Ujistěte se, že pokud se změnil název autora nebo chce aktualizovat fotografii, musíme si projít a aktualizovat každou knihu, kterou předtím publikovali, ale pro naši aplikaci, a to na základě předpokladu, že autoři nezměnili jejich názvy často, jedná se o přijatelné rozhodnutí o návrhu.  

V tomto příkladu jsou **předem vypočtena agregace** hodnoty ušetřit nákladné zpracování na operace čtení. V tomto příkladu je některá data vloží do dokumentu Autor data, která se počítá v době běhu. Pokaždé, když se publikuje nová kniha, se vytvoří dokument adresáře **a** countOfBooks pole nastavena na počítané hodnoty na základě počtu dokumentů knihy, které existují konkrétní autora. Tyto optimalizace by bylo dobré v systémech čtení náročné kde jsme si může dovolit provádět výpočty na zápis k optimalizaci čtení.

Možnost používat model s předem vypočtené pole je možné, protože Azure Cosmos DB podporuje **transakce s několika dokumenty**. Mnoho úložišť nosql s dvojicí nelze provádět transakce mezi dokumenty a proto pomocníků pro rozhodnutí o návrhu, jako je například "always vložit všechno, co", z důvodu tohoto omezení. Pomocí služby Azure Cosmos DB můžete použít aktivační procedury na straně serveru nebo uložené procedury, které knihy vkládací a aktualizační autoři všechny v modelu ACID transakci. Teď nemusíte vkládat vše do jednoho dokumentu, abyste měli jistotu, že vaše data zůstanou konzistentní.

## <a name="distinguishing-between-different-document-types"></a>Odlišení mezi různými typy dokumentů

V některých scénářích může být vhodné kombinovat různé typy dokumentů ve stejné kolekci. obvykle se jedná o případ, kdy chcete do stejného [oddílu](partitioning-overview.md)zasedat více souvisejících dokumentů. Můžete například do jedné kolekce umístit recenze knih a knih a rozdělit je `bookId`na oddíly. V takové situaci obvykle chcete do dokumentů přidat pole, které určuje jejich typ, aby je bylo možné odlišit.

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

## <a name="next-steps"></a>Další postup

Největší takeaways v tomto článku jsou informace o tom, že se modelování ve světě neschematických dat důležité jako dříve.

Stejně jako neexistuje žádný jeden způsob, jak reprezentaci část dat na obrazovce, neexistuje žádný jeden způsob modelování dat. Potřebujete pochopit, aplikace a jak ji vytvoří, využívat a zpracovat data. Použitím některé zde uvedené pokyny, pak můžete nastavit o vytváření modelu, který řeší okamžité potřeby vaší aplikace. Pokud vaše aplikace musí změnit, můžete využít flexibilitu bez schématu databáze a využívat, měnit a snadnému rozšíření datového modelu.

Další informace o službě Azure Cosmos DB, najdete v tématu služby [dokumentaci](https://azure.microsoft.com/documentation/services/cosmos-db/) stránky.

Abyste pochopili, jak horizontálního dělení dat napříč několika oddíly, najdete v tématu [dělení dat ve službě Azure Cosmos DB](sql-api-partition-data.md).

Pokud chcete zjistit, jak modelovat data a rozdělit je na Azure Cosmos DB pomocí reálného příkladu, přečtěte si téma [modelování a vytváření oddílů dat – příklad reálného světa](how-to-model-partition-example.md).
