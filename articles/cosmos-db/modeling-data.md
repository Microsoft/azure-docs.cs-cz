---
title: Modelování dokumentů dat pro databáze NoSQL | Microsoft Docs
description: Další informace o modelování dat pro databáze NoSQL
keywords: modelování dat
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2016
ms.author: sngun
ms.openlocfilehash: ef40bcb473e4d7dbe51f9d8d9ca20265c04d0df6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34612679"
---
# <a name="modeling-document-data-for-nosql-databases"></a>Data dokumentu pro databáze NoSQL modelování
Při databází bez schémat, jako je Azure Cosmos databáze, bylo naprosto snadno použít změny do datového modelu byste se měli stále něco některé čas přemýšlení o vaše data. 

Jak se bude data k uložení? Jak se vaše aplikace bude načíst dotaz na data? Je vaše aplikace silná čtení nebo zápisu těžká? 

Po přečtení tohoto článku, budete moct odpovězte si na následující otázky:

* Jak by měla myslíte o dokument v databázi dokumentů?
* Co je modelování dat a proč by měl vědět? 
* Jak se liší od relační databáze modelování dat v databázi dokumentů
* Jak express relace mezi daty v jiných relační databázi?
* Když vložit data a když je propojení k datům?

## <a name="embedding-data"></a>Vkládání dat
Když spustíte modelování dat v dokumentu úložiště, jako je Azure Cosmos databáze, pokuste se považovat za vaší entity **nezávislý dokumenty** reprezentované ve formátu JSON.

Před jsme podrobné informace příliš mnohem dál, dejte nám provést pár kroků zpět a podívejte se na tom, jak něco v relační databázi, předmět, který se řadu nám obeznámeni s může modelu jsme. Následující příklad ukazuje, jak mohou být uloženy osoby v relační databázi. 

![Model relační databáze.](./media/sql-api-modeling-data/relational-data-model.png)

Při práci s relačních databází, jsme jste byla výukové let normalizaci, normalizaci, normalizují.

Normalizaci dat obvykle zahrnuje přebírá entity, jako je osoba a jeho rozdělení na samostatné části data. V příkladu nahoře uživatel může mít více podrobností kontaktujte záznamů, jakož i více záznamů adresu. Jsme i další jít o krok a rozdělení kontaktní údaje další extrahováním běžné jako typ pole. Stejné pro adresu, každý záznam tady má typu like *Domů* nebo *firmy* 

Místní provede při normalizace dat je **vyhnout ukládání redundantních dat** na každý záznam a místo odkazovat na data. V tomto příkladu číst osoba, s jejich kontaktní údaje a adresy, budete muset použít spojení efektivně agregovat data v době běhu.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType on cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Aktualizace jedna osoba s jejich kontaktní údaje a adresy vyžaduje operace zápisu napříč mnoha jednotlivé tabulky. 

Nyní Podívejme se na tom, jak jsme by modelu stejná data jako samostatná entitu v databázi dokumentů.

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

Pomocí výše uvedených přístup máme teď **nenormalizované** osoba záznam kde jsme **embedded** všechny informace týkající se tato osoba, jako je například jejich kontaktní údaje a adresy v do jednoho dokumentu JSON.
Kromě toho protože jsme nejsou omezeny na pevného schématu máme možnost provádět akce, jako je zcela nutnosti kontaktní údaje různých tvarů. 

Načítání dokončení osoba záznam z databáze je nyní jedné operace proti jedinou kolekci a pro jednotlivý dokument čtení. Aktualizace záznamu osoby s jejich kontaktní údaje a adresy, je také operace zápisu jeden pro jednotlivý dokument.

Při denormalizing dat, může aplikace potřeba vydávat méně dotazů a aktualizace pro dokončení běžných operací. 

### <a name="when-to-embed"></a>Kdy se má vložit
Vložená data se obvykle používá při modelů:

* Existují **obsahuje** vztahy mezi entitami.
* Existují **jeden několik** vztahy mezi entitami.
* Není vložená data, **zřídka mění**.
* Je vložených dat nebude růst **bez vazby**.
* Není vložená data, která je **integrální** k datům v dokumentu.

> [!NOTE]
> Obvykle nenormalizovanou datové modely poskytují lepší **číst** výkonu.
> 
> 

### <a name="when-not-to-embed"></a>Pokud není pro vložení
Když pravidlo v databázi dokumentů je všechno, co denormalize a vložit všechna data v do jednoho dokumentu, to může vést k některé situace, které by se mělo zabránit.

Trvat tohoto fragmentu kódu JSON.

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

To může být to, co bude entita post s embedded komentáře vypadat jsme modelování byly typické blogu nebo CMS, systému. Problém s v tomto příkladu je, že je pole komentáře **bez vazby**, což znamená, že neexistuje žádné (praktické) omezení počtu komentáře může mít žádné jeden post. To začnou problém, protože velikost dokumentu může výrazně zvýší.

Velikost dokumentu s růstem schopnost přenosu dat prostřednictvím sítě a také čtení a aktualizaci dokumentu ve velkém měřítku, bude mít vliv.

V takovém případě je lepší vzít v úvahu následující modelu.

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

Tento model má tři nejnovější komentáře vložených na post sebe, což je pole s pevným vázaný této doby. Další komentáře jsou seskupené do do dávky 100 komentáře a uložené v samostatné dokumenty. Velikost dávky je vybraná jako 100, protože naše fiktivní aplikace umožňuje uživatelům načíst 100 komentáře najednou.  

Jiné případu, kdy vnoření dat není vhodné je při vložených dat se často používá na dokumentech a se často mění. 

Trvat tohoto fragmentu kódu JSON.

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

To by mohl představovat uložených portfolií osoby. Jsme zvolili pro vložení uložené informace v každé portfolií dokumentu. V prostředí, kde se často mění související data jako je stock obchodních aplikací, vkládání dat, která se často mění bude znamenat pokaždé, když se prodávají stock jsou neustále aktualizuje každý dokument portfolií.

Stock *zaza* může být prodávají mnoho stokrát v jediném den a tisíce uživatelů může mít *zaza* na jejich portfolií. S datovým modelem jako výše nám aktualizovat mnoho tisíc portfolií dokumentů tolikrát, kolikrát každý den, což systému, nebude velmi správné škálování. 

## <a id="Refer"></a>Odkazy na data
Ano vkládání dat vhodně funguje u mnoha případech ale je zřejmé, že existují scénáře při denormalizing data způsobí, že další problémy, než je vhodné. Proto co můžeme udělat teď? 

Relační databáze nejsou bude jediným místem, kde můžete vytvořit relace mezi entitami. V databázi dokumentů může mít informace v jeden dokument, který ve skutečnosti se vztahují k datům v jiné dokumenty. Nyní I není zasazovat o i jednu minutu, využijeme systémy, které by být vhodnější pro relační databázi v Azure Cosmos DB nebo jakékoli jiné databáze dokumentu, ale jsou jednoduché vztahy a může být velmi užitečná. 

V níže uvedené JSON jsme se rozhodli použít v příkladu uložených portfolia z dříve, ale tentokrát označujeme skladová položka v celém portfoliu místo vkládání. Tímto způsobem, když se změní skladová položka mnohokrát za den pouze dokumentu, který je třeba aktualizovat je jeden uložených dokumentů. 

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


Tento přístup okamžité nevýhodou ale je, pokud vaše aplikace je vyžadována k zobrazení informací o každé populace, která se nachází při zobrazení osoby portfolií; v takovém případě musíte provést opakované cesty k databázi a načte informace pro každý dokument uložené. Zde jsme provedli rozhodnutí ke zlepšení efektivity operací zápisu, které dojít mnohokrát za den, ale pak dojde k ohrožení na operace čtení, které můžou mít menší dopad na výkon tohoto konkrétního systému.

> [!NOTE]
> Normalized datové modely **může vyžadovat další odezev** k serveru.
> 
> 

### <a name="what-about-foreign-keys"></a>Co cizí klíče?
Vzhledem k tomu, že aktuálně neexistuje žádná koncepce omezení, cizí klíč nebo jinak, všechny vztahy mezi dokumentu, které máte v dokumentech jsou efektivně "slabé odkazy" a nebude možné ověřit podle samotná databáze. Pokud chcete, aby data, která dokument odkazuje na skutečně existuje, budete muset provést ve vaší aplikaci, nebo prostřednictvím serverové aktivační události nebo uložené procedury v Azure Cosmos DB.

### <a name="when-to-reference"></a>Když chcete-li
Normalizovaný data se obvykle používá při modelů:

* Představující **na více** vztahy.
* Představující **m: n** vztahy.
* Související data **často mění**.
* Odkazovaná dat může být **bez vazby**.

> [!NOTE]
> Obvykle normalizace poskytuje lepší **zápisu** výkonu.
> 
> 

### <a name="where-do-i-put-the-relationship"></a>Kam mohu dát relace?
Růst relace vám pomohou určit v dokumentu, které k uložení odkazu.

Pokud se podíváme na níže uvedené formátu JSON, který modelů vydavatele a knih.

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
    {"id": "1000", "name": "Deep Dive in to Azure Cosmos DB" }

Pokud je číslo knih na vydavatele s omezenou růstu malé, může být užitečné pak ukládání odkaz na kniha uvnitř vydavatele dokumentu. Ale pokud je číslo knih na vydavatele bez vazby, pak tento datový model nezkopírujete, dojde k měnitelný, rostoucí pole, jako třeba vydavatele dokumentu výše. 

Přepínání věcí kolem chvilku by způsobilo model, který stále představuje stejná data, ale teď zabraňuje těchto velkých měnitelný kolekcí.

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
    {"id": "1000","name": "Deep Dive in to Azure Cosmos DB", "pub-id": "mspress"}

V předchozím příkladu jsme mít vyřadit kolekci bez vazby na vydavatele dokumentu. Místo toho jsme právě mít odkaz na vydavatele na každý dokument adresáře.

### <a name="how-do-i-model-manymany-relationships"></a>Jak modelu relace m: n?
V relační databázi *m: n* vztahy jsou často modelován s tabulkami spojení, které právě spojení záznamů z jiných tabulek. 

![Spojování tabulek](./media/sql-api-modeling-data/join-table.png)

Můžete mít tendenci replikovat samé použití dokumentů a vytvoření datového modelu, který bude vypadat podobně jako následující.

    Author documents: 
    {"id": "a1", "name": "Thomas Andersen" }
    {"id": "a2", "name": "William Wakefield" }

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101" }
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "b3", "name": "Taking over the world one JSON doc at a time" }
    {"id": "b4", "name": "Learn about Azure Cosmos DB" }
    {"id": "b5", "name": "Deep Dive in to Azure Cosmos DB" }

    Joining documents: 
    {"authorId": "a1", "bookId": "b1" }
    {"authorId": "a2", "bookId": "b1" }
    {"authorId": "a1", "bookId": "b2" }
    {"authorId": "a1", "bookId": "b3" }

To by fungovat. Ale načítání buď Autor s jejich seznamů nebo načtení knihy s jeho autor by vždy vyžadují alespoň dva další dotazy v databázi. Jeden dotaz spojující dokumentu a pak další dotaz pro načtení skutečné dokumentu je připojený. 

Pokud všechny, které se provádí v této tabulce spojení je připevnění společně dva kusy dat, pak Proč ne ho můžete vypustit úplně?
Pamatujte na následující věci.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents: 
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive in to Azure Cosmos DB", "authors": ["a2"]}

Nyní I měli Autor, okamžitě vím knihy, které uživatel vytvořil, a naopak Pokud I měli načíst kniha dokumentu by vím identifikátorů autory. To umožňuje ušetřit tento zprostředkující dotaz připojení k tabulce snižuje počet serveru zaokrouhlit služebních cest, které vaše aplikace musí být. 

## <a id="WrapUp"></a>Hybridní datové modely
Jste nyní hledá vložení (nebo denormalizing) a odkazující (nebo normalizace) dat, mají jejich upsides a mají ohrožení jak jsme mohli vidět. 

Vždy nemusí to být buď nebo nemusíte být Vystrašené trochu kombinovat věcí. 

Na základě konkrétní vzorce a úloh, které můžou nastat případy, kdy kombinování vložených vaší aplikace a smysl využívaných dat a může vést k jednodušší logiku aplikace s méně serveru zaokrouhlit služebních cest současně se zachovává funkční úroveň výkonu.

Vezměte v úvahu následující JSON. 

    Author documents: 
    {
        "id": "a1",
        "firstName": "Thomas",
        "lastName": "Andersen",        
        "countOfBooks": 3,
         "books": ["b1", "b2", "b3"],
        "images": [
            {"thumbnail": "http://....png"}
            {"profile": "http://....png"}
            {"large": "http://....png"}
        ]
    },
    {
        "id": "a2",
        "firstName": "William",
        "lastName": "Wakefield",
        "countOfBooks": 1,
        "books": ["b1"],
        "images": [
            {"thumbnail": "http://....png"}
        ]
    }

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
            {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "http://....png"}
        ]
    },
    {
        "id": "b2",
        "name": "Azure Cosmos DB for RDBMS Users",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
        ]
    }

Zde jsme jste (většinou) a potom vložený model, kdy data z jiné entity jsou vloženy do nejvyšší úrovně dokumentu, ale je odkazován další data. 

Pokud se podíváte na dokument adresáře, uvidíme několik zajímavé pole, když se podíváme na pole autoři. Došlo *id* pole, což je pole používáme jako odkaz na dokument Autor běžnou praxí v normalizovaný modelu, ale pak máme také *název* a *thumbnailUrl*. Jsme může s právě jsme *id* a levé aplikace získat žádné další informace o potřeboval z příslušných Autor dokumentu pomocí "odkazu", ale protože naše aplikace zobrazí jméno autora a miniaturu s každou kniha zobrazí jsme uložit dobu odezvy na server za adresáře v seznamu podle denormalizing **některé** data od autora sady.

Opravdu Pokud změníte jméno autora nebo chtěli aktualizovat jejich fotografií nám přejdete aktualizace každých adresáře mohou někdy publikovány, ale pro naši aplikaci založen na předpokladu, že autoři velmi často neměnit jejich názvy Toto je rozhodnutí o návrhu přijatelné.  

V tomto příkladu jsou **předem vypočítat agregace** hodnoty pro uložení nákladné zpracování na operace čtení. V příkladu některá data vložená v Autor dokumentu jsou data, která se vypočítává za běhu. Pokaždé, když je publikována nového seznamu, se vytvoří dokument adresáře **a** countOfBooks pole je nastaveno na hodnotu počítané na základě počtu dokumentů knihy, která platí pro konkrétní autora. Tato optimalizace by dobré v systémech čtení velkou kde jsme si může dovolit provést výpočtů na zápisy k optimalizaci čtení.

Možnost používat model s předem počítaných polí je možné, protože nepodporuje Azure Cosmos DB **transakcí několika dokumentů**. Mnoho NoSQL úložiště nelze provést transakce mezi dokumenty a proto doporučují, rozhodnutí o návrhu, jako je například "vždy vložit všechno", protože toto omezení. V Azure DB Cosmos můžete použít aktivační události na straně serveru nebo uložené procedury, které vložit knihy a aktualizovat autoři všechny v transakci ACID. Teď je prostě **mít** pro vložení vše na jednom z těchto dokumentů právě a zajistit, aby data zůstala konzistentní.

## <a name="NextSteps"></a>Další kroky
Největších takeaways z tohoto článku je k tomu, že je stejně důležité jako někdy data modelování ve světě bez schémat. 

Stejně, jako je jediný způsob představují část dat na obrazovce, neexistuje žádný jediný způsob modelování vaše data. Můžete třeba pochopit, aplikace a jak ji vytvoří, využívat a zpracovat data. Použitím některé pokyny, které jsou tu popsané, můžete nastavit o vytváření model, který řeší okamžitou potřebám vaší aplikace. Pokud vaše aplikace musí změnit, můžete využít flexibilitu bez schémat databáze zapojení, která změnit a snadno vyvíjet datový model. 

Další informace o databázi Cosmos Azure, najdete v tématu služby [dokumentace](https://azure.microsoft.com/documentation/services/cosmos-db/) stránky. 

Abyste pochopili, jak horizontálního oddílu data napříč více oddílů, najdete v tématu [dělení dat v Azure Cosmos DB](sql-api-partition-data.md). 
