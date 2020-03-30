---
title: Modelování dat v Azure Cosmos DB
titleSuffix: Azure Cosmos DB
description: Seznamte se s modelováním dat v nosql databázích, o rozdílech mezi modelováním dat v relační databázi a databázi dokumentů.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 523049ea3286445117f41147f3dd12a2c911d1ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72755015"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Modelování dat v Azure Cosmos DB

Zatímco databáze bez schémat, jako je Azure Cosmos DB, usnadňují ukládání a dotazování nestrukturovaných a částečně strukturovaných dat, měli byste strávit nějaký čas přemýšlením o datovém modelu, abyste získali co nejvíce služeb z hlediska výkonu a škálovatelnosti a nejnižší Náklady.

Jak budou data uložena? Jak bude vaše aplikace načítat data a dotazovat se na ně? Je vaše aplikace čitelná nebo zápisná?

Po přečtení tohoto článku budete moci odpovědět na následující otázky:

* Co je modelování dat a proč by mě to mělo zajímat?
* Jak se modelování dat v Azure Cosmos DB liší od relační databáze?
* Jak lze vyjádřit vztahy dat v nerelační databázi?
* Kdy vložím data a kdy se na ně napojím?

## <a name="embedding-data"></a>Vkládání dat

Když začnete modelování dat v Azure Cosmos DB zkuste považovat vaše entity jako **samostatné položky** reprezentované jako dokumenty JSON.

Pro srovnání nejprve se podívejme, jak můžeme modelovat data v relační databázi. Následující příklad ukazuje, jak může být osoba uložena v relační databázi.

![Relační databázový model](./media/sql-api-modeling-data/relational-data-model.png)

Při práci s relačnídatabáze, strategie je normalizovat všechna data. Normalizace dat obvykle zahrnuje převzetí entity, například osoby, a její rozdělení na samostatné součásti. Ve výše uvedeném příkladu může mít osoba více záznamů podrobností kontaktu a také více záznamů adres. Kontaktní údaje lze dále rozdělit dalším extrahováním běžných polí, jako je typ. Totéž platí pro adresu, každý záznam může být typu *Domů* nebo *Business*.

Hlavní předpoklad při normalizaci dat je **zabránit ukládání redundantních dat** na každý záznam a spíše odkazovat na data. V tomto příkladu číst osobu, se všemi jeho kontaktní údaje a adresy, je třeba použít JOINS efektivně sestavit zpět (nebo denormalizovat) data za běhu.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Aktualizace jedné osoby s jejich kontaktními údaji a adresami vyžaduje operace zápisu v mnoha jednotlivých tabulkách.

Teď se podívejme na to, jak bychom modelovali stejná data jako samostatná entita v Azure Cosmos DB.

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

Pomocí výše uvedeného přístupu jsme **denormalizovali** záznam osoby **vložením** všech informací týkajících se této osoby, jako jsou její kontaktní údaje a adresy, do jednoho dokumentu *JSON.*
Kromě toho, protože nejsme omezeni na pevné schéma, máme flexibilitu dělat věci, jako je mít kontaktní údaje různých tvarů úplně.

Načítání úplného záznamu osoby z databáze je nyní **jedna operace čtení** proti jednomu kontejneru a pro jednu položku. Aktualizace záznamu osoby s kontaktními údaji a adresami je také **jedna operace zápisu** proti jedné položce.

Denormalizing data, aplikace může být nutné vydat méně dotazů a aktualizací k dokončení běžných operací.

### <a name="when-to-embed"></a>Kdy vložit

Vběžné řadě používejte vložené datové modely, pokud:

* Existují **obsažené** vztahy mezi entitami.
* Mezi entitami existují vztahy **1:1.**
* Jsou vložená data, která **se mění zřídka**.
* K dispozici jsou vložená data, která nebudou růst **bez vázané**.
* Vložená data jsou **často dotazována společně**.

> [!NOTE]
> Obvykle denormalized datové modely poskytují lepší výkon **čtení.**

### <a name="when-not-to-embed"></a>Kdy nevložit

Zatímco pravidlem v Azure Cosmos DB je denormalizovat všechno a vložit všechna data do jedné položky, to může vést k některé situace, které je třeba se vyhnout.

Vezměte si tento úryvek JSON.

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

To by mohlo být to, co post entity s vloženými komentáři by vypadat, kdybychom byli modelování typický blog, nebo CMS, systém. Problém s tímto příkladem je, že komentáře pole je **bez závazků**, což znamená, že neexistuje žádný (praktický) limit na počet komentářů každý jednotlivý příspěvek může mít. To se může stát problémjako velikost položky může růst nekonečně velké.

Vzhledem k tomu, velikost položky roste schopnost přenášet data přes drát, stejně jako čtení a aktualizaci položky, ve velkém měřítku, bude mít vliv.

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

Tento model má tři nejnovější komentáře vložené do kontejneru příspěvku, což je pole s pevnou sadou atributů. Ostatní komentáře jsou seskupeny do dávek 100 komentářů a uloženy jako samostatné položky. Velikost dávky byla vybrána jako 100, protože naše fiktivní aplikace umožňuje uživateli načíst 100 komentářů najednou.  

Dalším případem, kdy vkládání dat není vhodné, je, když jsou vložená data často používána mezi položkami a často se mění.

Vezměte si tento úryvek JSON.

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

To by mohlo představovat akciové portfolio osoby. Rozhodli jsme se vložit informace o akciích do každého dokumentu portfolia. V prostředí, kde se související data často mění, jako je aplikace pro obchodování s akciemi, vkládání dat, která se často mění, bude znamenat, že neustále aktualizujete každý dokument portfolia při každém obchodování s akciemi.

Akcie *zaza* mohou být obchodovány mnoho stokrát za jediný den a tisíce uživatelů by mohly mít *zaza* na svém portfoliu. S datovým modelem, jako je výše, bychom museli aktualizovat mnoho tisíc dokumentů portfolia mnohokrát denně, což vede k systému, který nebude dobře škálovat.

## <a name="referencing-data"></a>Odkazování na data

Vkládání dat funguje v mnoha případech, ale existují scénáře, kdy denormalizace dat způsobí více problémů, než stojí za to. Tak co budeme dělat teď?

Relační databáze nejsou jediným místem, kde můžete vytvářet vztahy mezi entitami. V databázi dokumentů můžete mít informace v jednom dokumentu, které se vztahují k datům v jiných dokumentech. Nedoporučujeme vytváření systémů, které by byly vhodnější pro relační databáze v Azure Cosmos DB nebo jakékoli jiné databázi dokumentů, ale jednoduché vztahy jsou v pořádku a mohou být užitečné.

V JSON níže jsme se rozhodli použít příklad akciového portfolia z dřívějších, ale tentokrát se odkazujeme na akciovou položku na portfoliu namísto jeho vložení. Tímto způsobem, když se skladová položka často mění po celý den, je jediným dokumentem, který je třeba aktualizovat, jeden skladový doklad.

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

Bezprostřední nevýhodou tohoto přístupu však je, pokud vaše aplikace je nutné zobrazit informace o každé populace, která je držena při zobrazování osoby portfolia; V takovém případě budete muset provést více cest do databáze, abyste načetli informace pro každý skladový dokument. Zde jsme se rozhodli zlepšit efektivitu operací zápisu, které se vyskytují často po celý den, ale na oplátku ohroženy na operace čtení, které potenciálně mají menší dopad na výkon tohoto konkrétního systému.

> [!NOTE]
> Normalizované datové modely **mohou vyžadovat více zpátečních cest** na server.

### <a name="what-about-foreign-keys"></a>A co cizí klíče?

Vzhledem k tomu, že v současné době neexistuje žádný koncept omezení, cizí klíč nebo jinak, všechny mezidokumentové vztahy, které máte v dokumentech jsou účinně "slabé odkazy" a nebudou ověřeny samotnou databází. Pokud chcete zajistit, že data dokument odkazuje skutečně existuje, pak je třeba provést ve vaší aplikaci nebo pomocí aktivačních událostí na straně serveru nebo uložené procedury na Azure Cosmos DB.

### <a name="when-to-reference"></a>Kdy odkazovat

Obecně platí, že normalizované datové modely používejte v:

* Reprezentace vztahů **1:N.**
* Reprezentující vztahy **N:N.**
* Související data **se často mění**.
* Odkazovaná data mohou být **neohraničená**.

> [!NOTE]
> Normalizace obvykle poskytuje lepší výkon **zápisu.**

### <a name="where-do-i-put-the-relationship"></a>Kam mám dát ten vztah?

Růst vztahu pomůže určit, ve kterém dokumentu chcete odkaz uložit.

Podíváme-li se na JSON níže, že modely vydavatelů a knih.

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

Pokud je počet knih na vydavatele malý s omezeným růstem, může být užitečné uložení odkazu na knihu v dokumentu vydavatele. Pokud je však počet knih na vydavatele neomezený, pak by tento datový model vedl ke proměnlivým, rostoucím polím, jako v příkladu výše uvedeného dokumentu vydavatele.

Přepínání věci kolem bit by mělo za následek model, který stále představuje stejná data, ale nyní se vyhýbá tyto velké proměnlivé kolekce.

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

Ve výše uvedeném příkladu jsme vypadli neohraničené kolekce na dokument vydavatele. Místo toho máme jen odkaz na vydavatele na každém dokumentu knihy.

### <a name="how-do-i-model-manymany-relationships"></a>Jak lze modelovat mnoho:mnoho vztahů?

V relační databázi *mnoho:mnoho* relací jsou často modelovány s tabulkami spojení, které pouze spojit záznamy z jiných tabulek dohromady.

![Spojit tabulky](./media/sql-api-modeling-data/join-table.png)

Můžete být v pokušení replikovat stejnou věc pomocí dokumentů a vytvořit datový model, který vypadá podobně jako následující.

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

Tohle by fungovalo. Načítání autora s jejich knihami nebo načítání knihy s jejím autorem by však vždy vyžadovalo alespoň dva další dotazy týkající se databáze. Jeden dotaz do spojovacího dokumentu a pak jiný dotaz načtení skutečného dokumentu, který je spojen.

Pokud to všechno spojení tabulky dělá, je lepení dohromady dvě části dat, tak proč ne pokles úplně?
Zvažte následující.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}

Teď, když jsem měl autora, okamžitě vím, které knihy, které napsali, a naopak, kdybych měl knihu dokument naložený bych vědět, ID autora (y). Tím se uloží, že zprostředkující dotaz proti tabulce spojení snížení počtu serveru zpáteční cesty aplikace má provést.

## <a name="hybrid-data-models"></a>Hybridní datové modely

Nyní jsme se podívali vkládání (nebo denormalizace) a odkazování (nebo normalizace) data, každý má své výhody a každý má kompromisy, jak jsme viděli.

Nemusí to být vždy buď, nebo, nebojte se míchat věci trochu.

Na základě konkrétních vzorců využití a úloh vaší aplikace mohou nastat případy, kdy má smysl směšování vložených a odkazovaných dat a může vést k jednodušší aplikační logice s menším počtem cest serveru při zachování dobré úrovně výkonu.

Zvažte následující JSON.

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

Zde jsme (většinou) sledovali vložený model, kde jsou data z jiných entit vložena do dokumentu nejvyšší úrovně, ale na jiná data se odkazuje.

Pokud se podíváte na dokument knihy, můžeme vidět několik zajímavých oblastí, když se podíváme na řadu autorů. Tam je `id` pole, které je pole, které používáme k odkazu zpět na autora dokumentu, standardní praxe v normalizovaném modelu, ale pak máme také `name` a `thumbnailUrl`. Mohli jsme přilepená a `id` opustil aplikaci získat další informace, které potřebuje od příslušného autora dokumentu pomocí "odkaz", ale proto, že naše aplikace zobrazuje jméno autora a miniaturu s každou zobrazenou knihou můžeme uložit zpáteční cestu na server na knihu v seznamu denormalizing **některá** data od autora.

Jistě, pokud se jméno autora změnilo nebo chtěli aktualizovat svou fotografii, museli bychom jít a aktualizovat každou knihu, kterou kdy publikovali, ale pro naši aplikaci, na základě předpokladu, že autoři často nemění své jméno, je to přijatelné rozhodnutí o návrhu.  

V příkladu jsou **předem vypočtené agregační** hodnoty pro uložení nákladné zpracování na operaci čtení. V příkladu jsou některá data vložená do autorského dokumentu data, která se počítají za běhu. Při každém publikování nové knihy je vytvořen dokument knihy **a** pole countOfBooks je nastaveno na vypočtenou hodnotu na základě počtu dokumentů knihy, které existují pro konkrétního autora. Tato optimalizace by bylo dobré v čtení těžkých systémů, kde si můžeme dovolit dělat výpočty na zápisy s cílem optimalizovat čtení.

Možnost mít model s předem počítanými poli je možná, protože Azure Cosmos DB podporuje **transakce s více dokumenty**. Mnoho obchodů NoSQL nemůže dělat transakce mezi dokumenty, a proto obhajovat rozhodnutí o návrhu, jako je například "vždy vložit všechno", kvůli tomuto omezení. S Azure Cosmos DB můžete použít aktivační události na straně serveru nebo uložené procedury, které vkládají knihy a aktualizují autory v rámci transakce ACID. Nyní nemusíte vkládat vše **do** jednoho dokumentu, abyste měli jistotu, že vaše data zůstanou konzistentní.

## <a name="distinguishing-between-different-document-types"></a>Rozlišování mezi různými typy dokumentů

V některých případech můžete chtít kombinovat různé typy dokumentů ve stejné kolekci; to je obvykle případ, kdy chcete více, související dokumenty sedět ve stejném [oddílu](partitioning-overview.md). Můžete například umístit knihy i recenze knih do stejné `bookId`kolekce a rozdělit je podle . V takovém případě obvykle chcete přidat do dokumentů pole, které identifikuje jejich typ, aby bylo možné je odlišit.

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

## <a name="next-steps"></a>Další kroky

Největší stánek s jídlem z tohoto článku je pochopit, že modelování dat ve světě bez schématu je stejně důležité jako vždy.

Stejně jako neexistuje jediný způsob, jak reprezentovat část dat na obrazovce, neexistuje jediný způsob, jak modelovat data. Musíte pochopit vaši aplikaci a jak bude vytvářet, využívat a zpracovávat data. Potom pomocí některé pokyny zde uvedené můžete nastavit o vytvoření modelu, který řeší okamžité potřeby vaší aplikace. Když vaše aplikace potřebují změnit, můžete využít flexibilitu databáze bez schématu, aby přijali tuto změnu a snadno vyvinuli svůj datový model.

Další informace o Azure Cosmos DB najdete na stránce [dokumentace služby.](https://azure.microsoft.com/documentation/services/cosmos-db/)

Informace o tom, jak rozdělit data mezi více oddílů, najdete [v oddílech data v Azure Cosmos DB](sql-api-partition-data.md).

Informace o tom, jak modelovat a rozdělovat data v Azure Cosmos DB pomocí příkladu reálného světa, najdete v [podrobnostech o modelování a dělení dat – příklad reálného světa](how-to-model-partition-example.md).
