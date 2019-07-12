---
title: Modelování dat ve službě Azure Cosmos DB
titleSuffix: Azure Cosmos DB
description: Další informace o modelování dat v databázích NoSQL, rozdíly mezi modelování dat v relační databázi a databázi dokumentů.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rimman
ms.custom: rimman
ms.openlocfilehash: 47d519523c7ffd1c0b6329d6b4eb12b052466b35
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657379"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Modelování dat v Azure Cosmos DB

Zatímco databáze bez schémat, jako je Azure Cosmos DB, aby velice snadné ukládat a dotazovat nestrukturovaných a částečně strukturovaných dat, můžete strávit některé čas přemýšlení o datovém modelu k plnému využití služeb z hlediska výkonu a škálovatelnosti a nejnižší náklady.

Jak probíhá data k uložení? Jak se vaše aplikace bude k načtení a dotazování dat? Je to vaše aplikace náročné na čtení nebo zápisu náročná na výkon?

Po přečtení tohoto článku, budou moci odpovědět na následující otázky:

* Co je modelování dat a proč by měli starat?
* Jak se liší do relační databáze modelování dat ve službě Azure Cosmos DB?
* Jak můžu express relací mezi daty nerelační databáze?
* Při vkládání dat a když je propojení s daty?

## <a name="embedding-data"></a>Vkládání dat

Když spustíte modelování dat ve službě Azure Cosmos DB pokusí považovat za entity **samostatné položky** reprezentovaná jako dokumenty JSON.

Pro porovnání nejprve podíváme jak jsme modelovat data ve službě relační databáze. Následující příklad ukazuje, jak osoba můžou být uložená v relační databázi.

![Model relační databáze](./media/sql-api-modeling-data/relational-data-model.png)

Při práci s relačními databázemi, je strategie normalizovat všechna vaše data. Normalizaci dat obvykle zahrnuje pořizování entity, jako je osoba a jeho rozdělení na samostatné součásti. V předchozím příkladu osoba může mít více záznamů podrobnosti o kontaktu, jakož i více záznamů adresu. Kontaktní údaje mohou být dále člení další extrahováním společné pole, jako jsou typu. Totéž platí i pro adresu, každý záznam může být typu *Domů* nebo *obchodní*.

Která bude obsahovat místní normalizace dat se **Vyhněte se ukládání redundantních dat** na každý záznam a místo toho odkazovat na data. V tomto příkladu číst osoba, s jejich kontaktní údaje a adresy budete muset použít spojení efektivně compose zpět (nebo denormalizovat) dat v době běhu.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Aktualizace jedné osobě s jejich kontaktní údaje a adresy vyžaduje operací zápisu napříč mnoha jednotlivé tabulky.

Nyní Pojďme se podívat, jak jsme by model stejná data jako samostatný entity ve službě Azure Cosmos DB.

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

Použití přístup nad budeme mít **Nenormalizovaná** osoby nahrávat, podle **vkládání** všechny informace související s tuto osobu, jako je například své kontaktní údaje a adresy, na *jeden JSON* dokumentu.
Navíc protože jsme nejsou omezeny na pevné schéma máme flexibilitu k provádění akcí, jako byste měli zcela kontaktní údaje z různých tvarů.

Načítání kompletní osoba záznam z databáze je nyní **operace čtení jedním** proti jednoho kontejneru a pro jednu položku. Aktualizace záznamu osoby s jejich kontaktní údaje a adresy, je také **jedna operace zápisu** proti jednu položku.

Podle denormalizing dat, vaše aplikace může potřebovat vydat menšího počtu dotazů a aktualizace dokončete běžných operací.

### <a name="when-to-embed"></a>Kdy se má vložit

Vložená data se obvykle používá při modely:

* Existují **obsažené** vztahů mezi entitami.
* Existují **jeden několik** vztahů mezi entitami.
* Je vložená data, která **mění jen zřídka**.
* Je vložená data, která nebude zvětšovat **neomezeně**.
* Je vložená data, která je **dotazovat často společně**.

> [!NOTE]
> Obvykle Nenormalizovaná data modely poskytují lepší **čtení** výkonu.

### <a name="when-not-to-embed"></a>Kdy nepoužívat pro vložení

Když pravidlo ve službě Azure Cosmos DB je všechno, co denormalizovat a všechna data pro vložení do jedné položky, to může vést k určité situace, které by se jim vyhnout.

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

To může být to, co entita příspěvek s vložené komentáře může vypadat třeba jsme modelování byly typické blogu nebo systému CMS. Problém s v tomto příkladu je, že je pole komentáře **bez vazby**, což znamená, že neexistuje žádné omezení (praktické) počet komentářů může mít libovolný jeden příspěvek. To může stát problémem, jak může zvětšit velikost položky nekonečně velká.

Možnost přenášet data přes přenosové stejně jako čtení a aktualizaci položky ve velkém měřítku, roste velikost položky budou mít vliv.

V takovém případě by bylo lepší vzít v úvahu následující datového modelu.

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

Tento model má tři nejnovější komentáře vložený v příspěvku kontejneru, což je pole s pevnou sadu atributů. Další poznámky jsou seskupená do v dávkách po 100 komentáře a uložené jako samostatné položky. Velikost dávky byla vybrána jako 100 protože fiktivní aplikaci mu umožní načíst 100 komentáře v čase.  

Dalším užitečným, kde vkládání dat není vhodné při vložených dat se často používá napříč položky a bude často měnit.

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

To může představovat uložených portfolia osoby. Jsme zvolili a vloží tyto uložené informace do každého dokumentu portfolia. V prostředí, ve kterém související data se často mění například akcie obchodní aplikace, vkládání dat, která se často mění se to znamenat pokaždé, když se prodávají stejných akcií se neustále aktualizuje každý dokument portfolia.

Stock *zaza* může být prodávají stovky časy v jediném den a tisíce uživatelů může mít *zaza* na jejich portfolia. S datovým modelem, který je uveden výše budeme něco muset aktualizovat tisíce portfolia dokumenty v mnoha případech každý den, což vede k systému, který nebude jednoduše škálovat.

## <a name="referencing-data"></a>Odkazy na data

Vkládání dat krásně funguje u mnoha případech ale existují scénáře, kdy denormalizing data způsobí, že více problémů, než je vhodné. Tak co můžeme udělat teď?

Relační databáze nejsou jediným místem, kde můžete vytvářet vztahy mezi entitami. V databázi dokumentů můžete mít informace v jednom dokumentu, které souvisejí s daty v jiných dokumentech. Nedoporučujeme vytvářet systémy, které by být vhodnější pro relační databáze ve službě Azure Cosmos DB nebo jiné databáze dokumentů, ale jednoduché vztahy jsou v pořádku a můžou být užitečné.

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

Nyní kdybychom měli Autor, které knihy jste napsali okamžitě vím a naopak kdybychom měli načíst knihy dokumentu by vím ID autory. Toto uloží zprostředkující dotazu proti spojení tabulek snižuje počet serverů zaokrouhlit zkracuje dobu odezvy, které má vaše aplikace provést.

## <a name="hybrid-data-models"></a>Hybridních datových modelů

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

Když se podíváte na dokument adresáře, můžeme vidět některé zajímavé pole, když se podíváme na pole autoři. Je `id` pole, který je pole používáme jako zpětná reference na Autor dokumentu, standardním postupem ve normalizované modelu, ale pak budeme také mít `name` a `thumbnailUrl`. Společnost Microsoft může mít zablokuje se `id` a nechat se nepoužije žádné další informace o potřeboval z příslušných Autor dokumentu pomocí "propojení", ale vzhledem k tomu, že naše aplikace zobrazí jméno autora a miniatury s jednotlivé knihy zobrazené jsme odezvy uložit na server za adresáře v seznamu podle denormalizing **některé** dat od autora sady.

Je-li změnit jméno autora nebo jejich vlastním tempem k aktualizaci jejich fotografie jistě, budeme něco muset přejděte a aktualizujte jednotlivé knihy, nikdy publikováno, ale pro naši aplikaci založeno na předpokladu, že autoři často neměnit jejich názvy, toto je rozhodnutí o návrhu přijatelné.  

V tomto příkladu jsou **předem vypočtena agregace** hodnoty ušetřit nákladné zpracování na operace čtení. V tomto příkladu je některá data vloží do dokumentu Autor data, která se počítá v době běhu. Pokaždé, když se publikuje nová kniha, se vytvoří dokument adresáře **a** countOfBooks pole nastavena na počítané hodnoty na základě počtu dokumentů knihy, které existují konkrétní autora. Tyto optimalizace by bylo dobré v systémech čtení náročné kde jsme si může dovolit provádět výpočty na zápis k optimalizaci čtení.

Možnost používat model s předem vypočtené pole je možné, protože Azure Cosmos DB podporuje **transakce s několika dokumenty**. Mnoho úložišť nosql s dvojicí nelze provádět transakce mezi dokumenty a proto pomocníků pro rozhodnutí o návrhu, jako je například "always vložit všechno, co", z důvodu tohoto omezení. Pomocí služby Azure Cosmos DB můžete použít aktivační procedury na straně serveru nebo uložené procedury, které knihy vkládací a aktualizační autoři všechny v modelu ACID transakci. Teď ne **mají** všechno, co vložit do jednoho dokumentu jenom k Ujistěte se, že vaše data zůstanou konzistentní.

## <a name="distinguishing-between-different-document-types"></a>Rozlišování mezi různých typů dokumentů.

V některých případech můžete chtít kombinovat různé typy dokumentů ve stejné kolekci. To je obvykle případ, kdy chcete, aby více, související dokumenty, které se nacházejí ve stejném [oddílu](partitioning-overview.md). Například může vložit obou knihy a recenze ve stejné kolekci a rozdělit na oddíly ji `bookId`. V takové situaci obvykle chcete přidat do dokumentů s polem, které identifikuje jejich typ, aby bylo možné odlišit.

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

Největší takeaways v tomto článku jsou informace o tom, že se modelování ve světě neschematických dat důležité jako dříve.

Stejně jako neexistuje žádný jeden způsob, jak reprezentaci část dat na obrazovce, neexistuje žádný jeden způsob modelování dat. Potřebujete pochopit, aplikace a jak ji vytvoří, využívat a zpracovat data. Použitím některé zde uvedené pokyny, pak můžete nastavit o vytváření modelu, který řeší okamžité potřeby vaší aplikace. Pokud vaše aplikace musí změnit, můžete využít flexibilitu bez schématu databáze a využívat, měnit a snadnému rozšíření datového modelu.

Další informace o službě Azure Cosmos DB, najdete v tématu služby [dokumentaci](https://azure.microsoft.com/documentation/services/cosmos-db/) stránky.

Abyste pochopili, jak horizontálního dělení dat napříč několika oddíly, najdete v tématu [dělení dat ve službě Azure Cosmos DB](sql-api-partition-data.md).
