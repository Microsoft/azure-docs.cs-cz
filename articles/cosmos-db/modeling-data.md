---
title: Modelování dat dokumentů v databázi NoSQL
titleSuffix: Azure Cosmos DB
description: Další informace o modelování dat v databázích NoSQL, rozdíly mezi modelování dat v relační databázi a databázi dokumentů.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: andrl
ms.custom: seodec18
ms.openlocfilehash: a6781c3a94789b26beb85a9a3df3166ec47622bb
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54041573"
---
# <a name="modeling-document-data-for-nosql-databases"></a>Modelování dat dokumentů databází NoSQL

Zatímco databáze bez schémat, jako je Azure Cosmos DB, nastavte ji velice snadné využití změny do datového modelu by měl stále strávíte některé čas přemýšlení o svých datech. 

Jak probíhá data k uložení? Jak se vaše aplikace bude k načtení a dotazování dat? Je silná jen pro čtení nebo zápis? 

Po přečtení tohoto článku, budou moci odpovědět na následující otázky:

* Jak byste uvažovat o dokument v databázi dokumentů?
* Co je modelování dat a proč by měli starat? 
* Jak se liší do relační databáze modelování dat v databázi dokumentů?
* Jak můžu express relací mezi daty nerelační databáze?
* Při vkládání dat a když je propojení s daty?

## <a name="embedding-data"></a>Vkládání dat
Při spuštění modelování dat v úložišti dokumentu, jako jsou služby Azure Cosmos DB, pokuste se považovat za entity **samostatná dokumenty** reprezentovány ve formátu JSON.

Předtím, než se budeme věnovat příliš mnohem víc, dejte nám zpět provést několik kroků a mít podívat, jak něco v relační databázi, předmět, řada z vás se již znáte může model jsme. Následující příklad ukazuje, jak osoba můžou být uložená v relační databázi. 

![Model relační databáze](./media/sql-api-modeling-data/relational-data-model.png)

Při práci s relačními databázemi, jsme jsme se museli celé roky normalizovat normalizovat, normalizovat.

Normalizaci dat obvykle zahrnuje pořizování entity, jako je osoba a jeho rozdělení na samostatných částí dat. V předchozím příkladu osoba může mít více záznamů podrobnosti o kontaktu, jakož i více záznamů adresu. Můžeme dokonce přejděte o krok dál a rozebere kontaktní údaje další extrahováním běžné pole, jako jsou typu. Stejnou adresu, každý záznam tady má typ jako *Domů* nebo *firmy* 

Která bude obsahovat místní normalizace dat se **Vyhněte se ukládání redundantních dat** na každý záznam a místo toho odkazovat na data. V tomto příkladu číst osoba, s jejich kontaktní údaje a adresy, budete muset použít spojení jak efektivně agregovat data v době běhu.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType on cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Aktualizace jedné osobě s jejich kontaktní údaje a adresy vyžaduje operací zápisu napříč mnoha jednotlivé tabulky. 

Nyní Pojďme se podívat, jak jsme by model stejná data jako samostatný entity v databázi dokumentů.

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

Pomocí výše uvedených přístup teď máme **Nenormalizovaná** osoby záznamu, kde jsme **vložený** všechny informace týkající se tomuto uživateli, jako je například své kontaktní údaje a adresy, do jednoho formátu JSON dokument.
Navíc protože jsme nejsou omezeny na pevné schéma máme flexibilitu k provádění akcí, jako byste měli zcela kontaktní údaje z různých tvarů. 

Načítání kompletní osoba záznam z databáze je teď jediný přečíst operace pro jednu kolekci nebo pro jednotlivý dokument. Aktualizace záznamu osoby s jejich kontaktní údaje a adresy, je také operace zápisu jednoho pro jednotlivý dokument.

Podle denormalizing dat, vaše aplikace může potřebovat vydat menšího počtu dotazů a aktualizace dokončete běžných operací. 

### <a name="when-to-embed"></a>Kdy se má vložit
Vložená data se obvykle používá při modely:

* Jsou obsaženy ** vztahů mezi entitami.
* Existují **jeden několik** vztahů mezi entitami.
* Je vložená data, která **mění jen zřídka**.
* Je vložen nebude růst dat **neomezeně**.
* Je vložená data, která je **integrální** s daty v dokumentu.

> [!NOTE]
> Obvykle Nenormalizovaná data modely poskytují lepší **čtení** výkonu.
> 
> 

### <a name="when-not-to-embed"></a>Kdy nepoužívat pro vložení
Pravidlo v databázi dokumentů je všechno, co denormalizovat a všechna data pro vložení do jednoho dokumentu, to může vést k určité situace, které by se jim vyhnout.

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

To může být to, co entita příspěvek s vložené komentáře může vypadat třeba jsme modelování byly typické blogu nebo systému CMS. Problém s v tomto příkladu je, že je pole komentáře **bez vazby**, což znamená, že neexistuje žádné omezení (praktické) počet komentářů může mít libovolný jeden příspěvek. Ten se stane problém, jak může výrazně zvýší velikost dokumentu.

Možnost přenášet data přes přenosové stejně jako čtení a aktualizace dokumentů ve velkém měřítku, roste velikost dokumentu bude mít vliv.

V takovém případě by bylo lepší vzít v úvahu následující modelu.

    Post document:
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

    Comment documents:
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

Tento model má tři nejnovější komentáře vložený v příspěvku samostatně, což je pole s pevnou vázán tento čas. Další poznámky jsou seskupená do v dávkách po 100 komentáře a uložená v samostatných dokumentech. Velikost dávky byla vybrána jako 100 protože fiktivní aplikaci mu umožní načíst 100 komentáře v čase.  

Dalším užitečným, kde vkládání dat není vhodné při vložených dat se často používá v dokumentech a bude často měnit. 

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

## <a id="Refer"></a>Odkazy na data
Ano vkládání dat funguje krásně řadě případů ale je jasné, že existují scénáře, kdy denormalizing vaše data budou způsobila více problémů, než je vhodné. Tak co můžeme udělat teď? 

Relační databáze nejsou jediným místem, kde můžete vytvářet vztahy mezi entitami. V databázi dokumentů můžete mít informace v jednom dokumentu, který ve skutečnosti má vztah k datům v jiných dokumentech. Teď můžu mi propagaci ještě jednu minutu, že jsme integrovali systémy, které by být vhodnější pro relační databáze ve službě Azure Cosmos DB nebo jiné databáze dokumentů, ale jednoduché vztahy jsou v pořádku a můžou být užitečné. 

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
> 
> 

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
> 
> 

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

## <a id="WrapUp"></a>Hybridních datových modelů
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

Když se podíváte na dokument adresáře, můžeme vidět některé zajímavé pole, když se podíváme na pole autoři. Je *id* pole, který je pole používáme jako zpětná reference na Autor dokumentu, standardním postupem ve normalizované modelu, ale pak budeme také mít *název* a *thumbnailUrl*. Jsme může mít zablokované s *id* a nechat se nepoužije žádné další informace o potřeboval z příslušných Autor dokumentu pomocí "propojení", ale protože naše aplikace zobrazí jméno autora a s Miniatura jednotlivé knihy zobrazí můžeme uložit odezvy na server za adresáře v seznamu podle denormalizing **některé** dat od autora sady.

Je-li změnit jméno autora nebo jejich vlastním tempem k aktualizaci jejich fotografie jistě, budeme něco muset přejít aktualizace jednotlivé knihy, nikdy publikováno, ale pro naši aplikaci založeno na předpokladu, že autoři často neměnit jejich názvy, toto je rozhodnutí o návrhu přijatelné.  

V tomto příkladu jsou **předem vypočtena agregace** hodnoty ušetřit nákladné zpracování na operace čtení. V tomto příkladu je některá data vloží do dokumentu Autor data, která se počítá v době běhu. Pokaždé, když se publikuje nová kniha, se vytvoří dokument adresáře **a** countOfBooks pole nastavena na počítané hodnoty na základě počtu dokumentů knihy, které existují konkrétní autora. Tyto optimalizace by bylo dobré v systémech čtení náročné kde jsme si může dovolit provádět výpočty na zápis k optimalizaci čtení.

Možnost používat model s předem vypočtené pole je možné, protože Azure Cosmos DB podporuje **transakce s několika dokumenty**. Mnoho úložišť nosql s dvojicí nelze provádět transakce mezi dokumenty a proto pomocníků pro rozhodnutí o návrhu, jako je například "always vložit všechno, co", z důvodu tohoto omezení. Pomocí služby Azure Cosmos DB můžete použít aktivační procedury na straně serveru nebo uložené procedury, které knihy vkládací a aktualizační autoři všechny v modelu ACID transakci. Teď ne **mají** všechno, co vložit do jednoho dokumentu jenom k Ujistěte se, že vaše data zůstanou konzistentní.

## <a name="NextSteps"></a>Další kroky
Největší takeaways v tomto článku jsou informace o tom, že se modelování ve světě neschematických dat důležité jako dříve. 

Stejně jako neexistuje žádný jeden způsob, jak reprezentaci část dat na obrazovce, neexistuje žádný jeden způsob modelování dat. Potřebujete pochopit, aplikace a jak ji vytvoří, využívat a zpracovat data. Použitím některé zde uvedené pokyny, pak můžete nastavit o vytváření modelu, který řeší okamžité potřeby vaší aplikace. Pokud vaše aplikace musí změnit, můžete využít flexibilitu bez schématu databáze a využívat, měnit a snadnému rozšíření datového modelu. 

Další informace o službě Azure Cosmos DB, najdete v tématu služby [dokumentaci](https://azure.microsoft.com/documentation/services/cosmos-db/) stránky. 

Abyste pochopili, jak horizontálního dělení dat napříč několika oddíly, najdete v tématu [dělení dat ve službě Azure Cosmos DB](sql-api-partition-data.md). 
