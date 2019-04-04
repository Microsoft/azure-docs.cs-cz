---
title: Jak model a oddílu data ve službě Azure Cosmos DB pomocí reálný příklad
description: Zjistěte, jak model a rozdělit na oddíly reálný příklad použití Azure Cosmos DB základní rozhraní API
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 3/27/2019
ms.author: thweiss
ms.openlocfilehash: ac1b94de4b439aab202d53b23b0d0da616a9f851
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "58919613"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>Jak model a oddílu data ve službě Azure Cosmos DB pomocí reálný příklad

Tento článek vychází z několika koncepty služby Azure Cosmos DB, jako jsou [modelování dat](modeling-data.md), [dělení](partitioning-overview.md), a [zřízená propustnost](request-units.md) k ukazují, jak obstarat reálného světa data výkonu návrhu.

Pokud budete obvykle pracovat s relační databáze, který jste vytvořili pravděpodobně zvyklosti a intuitions o tom, jak navrhnout datový model. Z důvodu zvláštní omezení, ale také jedinečné výhody služby Azure Cosmos DB většinu těchto osvědčených postupů není dobře přeložit a může přetáhnout do neoptimální řešení. Cílem tohoto článku je a provede vás procesem jeho dokončení modelování reálného světa – případem použití ve službě Azure Cosmos DB, z položky modelování společné umístění entity a dělení kontejneru.

## <a name="the-scenario"></a>Tento scénář

Pro toto cvičení, budeme vzít v úvahu domény platforma pro blogování kde *uživatelé* můžete vytvořit *účtuje*. Uživatelé mohou také *jako* a přidejte *komentáře* na tyto příspěvky.

> [!TIP]
> Jsme zdůraznily některá slova v *Kurzíva*; tato slova identifikovat jejich druh "věcmi" náš model bude mít k manipulaci s.

Přidání další požadavky našich specifikace:

- Zobrazí se stránka s front-kanál, které nedávno vytvořených příspěvků
- Můžeme načíst všechny příspěvky pro uživatele, všechny komentáře příspěvku a všechny lajky pro příspěvek,
- Příspěvky jsou vráceny pomocí uživatelského jména jejich autorů a přehled o tom, kolik komentáře a lajky mají,
- Komentáře a lajky jsou vráceny také s uživatelským jménem uživatelů, kteří vytvořili
- Pokud se zobrazuje jako seznamy, příspěvky mají pouze prezentovat zkrácený souhrn jejich obsah.

## <a name="identify-the-main-access-patterns"></a>Rozpoznávají vzorce hlavní přístupu

Pokud chcete začít, poskytujeme některé struktura naše počáteční specifikaci Identifikujte vzory přístupu k našemu řešení. Při navrhování datový model pro službu Azure Cosmos DB, je důležité pochopit, jaké požadavky náš model bude mít k obsluze abyste měli jistotu, že model bude sloužit tyto požadavky efektivně.

Chcete-li celkový proces usnadňuje její sledování, jsme zařadit těchto různých požadavků příkazy nebo dotazy, půjčky některé slovník z [modelu CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation) kde příkazy jsou požadavky (to znamená, záměry aktualizace systému) na zápis a dotazy jsou požadavky jen pro čtení.

Tady je seznam požadavků, které bude nutné vystavit naší platformy:

- **[C1]**  Vytvořit či upravit uživatele
- **[1]**  Načíst uživatele
- **[C2]**  Vytvořit či upravit příspěvek
- **[2]**  Načíst příspěvek
- **[3]**  Seznamu příspěvky uživatele v krátkých úseků
- **[C3]**  Vytvořit komentář
- **[4]**  Seznam komentářů na příspěvek
- **[C4]**  Jako příspěvek
- **[5]**  Seznamu vytvoří příspěvek lajků
- **[6]**  Seznamu *x* nejnovější příspěvky vytvořené v krátkém tvaru (informační kanál)

Jako tuto fázi nebyly si co jednotlivých entit (uživatelů, post atd.) bude obsahovat podrobné informace o. Tento krok je obvykle mezi první jejich třeba se zabývat při navrhování proti relačního úložiště, protože musíme zjistit, jak se tyto entity přeložit z hlediska tabulky, sloupce cizích klíčů atd. To je mnohem menší problém s databází dokumentů, který nebude vynucovat žádné schéma při zápisu.

Hlavním důvodem, proč je důležité identifikovat naše vzory přístupu k na začátku si je, protože tento seznam požadavků, které bude naši testovací sadu. Pokaždé, když jsme iteraci přes naše datový model, budeme absolvovat jednotlivé požadavky a zkontrolujte jeho výkon a škálovatelnost.

## <a name="v1-a-first-version"></a>V1: První verze

Začínáme se dvěma kontejnery: `users` a `posts`.

### <a name="users-container"></a>Kontejner uživatelů

Tento kontejner se uchovávají pouze položky uživatele:

    {
      "id": "<user-id>",
      "username": "<username>"
    }

Můžeme rozdělit tento kontejner pomocí `id`, což znamená, že každý logický oddíl v tomto kontejneru, bude obsahovat jenom jednu položku.

### <a name="posts-container"></a>Příspěvky kontejneru

Tento kontejner hostuje příspěvky, komentáře a lajků:

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "title": "<post-title>",
      "content": "<post-content>",
      "creationDate": "<post-creation-date>"
    }

    {
      "id": "<comment-id>",
      "type": "comment",
      "postId": "<post-id>",
      "userId": "<comment-author-id>",
      "content": "<comment-content>",
      "creationDate": "<comment-creation-date>"
    }

    {
      "id": "<like-id>",
      "type": "like",
      "postId": "<post-id>",
      "userId": "<liker-id>",
      "creationDate": "<like-creation-date>"
    }

Můžeme rozdělit tento kontejner pomocí `postId`, což znamená, že každý logický oddíl v tomto kontejneru bude obsahovat jeden příspěvek, všechny komentáře u tohoto příspěvku a všechny lajky pro tohoto příspěvku.

Všimněte si, že jsme zavedli `type` vlastnost položky uložené v tomto kontejneru k rozlišení mezi třemi typy entit, že tento hostitelích kontejnerů.

Také jsme jste se rozhodli odkazují na související data namísto jeho vložení (zkontrolujte [v této části](modeling-data.md) podrobnosti o těchto konceptech) protože:

- neexistuje žádná horní omezení počtu příspěvků, které uživatel může vytvořit,
- příspěvky může být libovolně dlouhý
- neexistuje žádné horní omezení na tom, kolik komentáře a lajky příspěvek může mít,
- Chceme mít možnost přidávat komentáře nebo lajk na příspěvek bez nutnosti aktualizovat účtovat, samotný.

## <a name="how-well-does-our-model-perform"></a>Jak dobře náš model provést?

Nyní je čas k vyhodnocení výkonu a škálovatelnosti naši první verze. Pro každou z žádosti už identifikoval měříme značnou latenci a požádat o tom, kolik jednotek spotřebuje. Toto měření se provádí proti fiktivní data sadu obsahující 100 000 uživatelů s 5 až 50 příspěvky na uživatele a až 25 komentáře a lajky 100 na příspěvek.

### <a name="c1-createedit-a-user"></a>[C1] Vytvořit/upravit uživatele

Tento požadavek je jednoduché implementace, jak můžeme stejně vytvořit nebo aktualizovat položky v `users` kontejneru. Požadavky krásně rozloženy všechny oddíly k `id` klíč oddílu.

![Zápis jednu položku do kontejneru Uživatelé](./media/how-to-model-partition-example/V1-C1.png)

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 7 ms | 5.71 RU | ✅ |

### <a name="q1-retrieve-a-user"></a>[1] Načíst uživatele

Načítání uživatele se provádí čtení odpovídající položky z `users` kontejneru.

![Načítání jednu položku z kontejneru Uživatelé](./media/how-to-model-partition-example/V1-Q1.png)

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 2 ms | 1 RU | ✅ |

### <a name="c2-createedit-a-post"></a>[C2] Vytvořit/upravit příspěvek

Podobně jako **[C1]**, máme pouze pro zápis do `posts` kontejneru.

![Zápis jednu položku do kontejneru příspěvky](./media/how-to-model-partition-example/V1-C2.png)

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 9 ms | 8.76 RU | ✅ |

### <a name="q2-retrieve-a-post"></a>[2] Načíst příspěvek

Začneme načtením odpovídající dokumentu z `posts` kontejneru. Ale, který není dostatečně, podle našich specifikace musíme také agregovat uživatelské jméno autora v příspěvku a počty kolik komentáře a kolik lajků tento příspěvek má, která vyžaduje 3 Další dotazy SQL, které mají být vydány.

![Načítají se vytvoří příspěvek a shromažďování dalších dat](./media/how-to-model-partition-example/V1-Q2.png)

Každý z dalších dotazů filtrů klíče oddílu příslušného kontejneru, což je přesně to, co chcete maximalizovat výkon a škálovatelnost. Ale nakonec musíme provést čtyři operace vrátit jeden příspěvek, takže budete zvyšujeme, který v další iteraci.

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 9 ms | 19.54 RU | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>[3] Seznam příspěvků uživatele v krátkých úseků

Nejprve musíme načíst požadovanou příspěvků s dotaz SQL, která načítá příspěvky odpovídající tomuto konkrétnímu uživateli. Ale jsme také vydávat další dotazy k agregaci uživatelské jméno autora a počet komentářů a lajků.

![Načtení všech příspěvků pro uživatele a agregování jejich další data](./media/how-to-model-partition-example/V1-Q3.png)

Tato implementace představuje mnoho nevýhody:

- dotazy agregace počtů komentáře a lajky muset být vydán pro každý příspěvek vrácené dotazem první
- Hlavní dotaz nefiltruje na klíč oddílu `posts` kontejneru, což vede k větveného a skener oddílu v kontejneru.

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 130 ms | 619.41 RU | ⚠ |

### <a name="c3-create-a-comment"></a>[C3] Vytvořit poznámku

Komentář je vytvořen napsáním odpovídající položku `posts` kontejneru.

![Zápis jednu položku do kontejneru příspěvky](./media/how-to-model-partition-example/V1-C2.png)

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 7 ms | 8.57 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[4] Seznam komentářů na příspěvek

Začneme s dotaz, který načte všechny komentáře u tohoto příspěvku a ještě jednou, musíme také agregační uživatelských jmen samostatně pro každý komentář.

![Načítají se všechny komentáře příspěvku a agregace jejich další data](./media/how-to-model-partition-example/V1-Q4.png)

I když hlavního dotazu filtrovat kontejneru klíče oddílu, agregaci uživatelská jména samostatně postihuje celkový výkon. Budete zvyšujeme, který později.

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 23 ms | 27.72 RU | ⚠ |

### <a name="c4-like-a-post"></a>[C4] Stejně jako příspěvek

Stejně jako **[C3]**, vytvoříme odpovídající položku v `posts` kontejneru.

![Zápis jednu položku do kontejneru příspěvky](./media/how-to-model-partition-example/V1-C2.png)

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 6 ms | 7.05 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[5] Seznam vytvoří příspěvek lajků

Stejně jako **[4]**, jsme lajky pro tohoto příspěvku dotazy a pak agregovat jejich uživatelských jmen.

![Načíst všechny lajků příspěvek a agregování jejich další data](./media/how-to-model-partition-example/V1-Q5.png)

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 59 ms | 58.92 RU | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[6] Seznam nejnovějších příspěvků x vytvořené v krátkém tvaru (informační kanál)

Nejnovější příspěvky můžeme načíst pomocí dotazu `posts` kontejneru seřazených podle sestupných datum vytvoření, pak agregační uživatelských jmen a počty komentáře a lajky pro jednotlivé příspěvky.

![Načtení nejnovější příspěvky a agregování jejich další data](./media/how-to-model-partition-example/V1-Q6.png)

Ještě jednou počáteční dotaz nebude filtrovat na klíč oddílu `posts` kontejneru, což je nákladné větveného aktivuje. Tento příklad je ještě horší cíl je mnohem větší sada výsledků dotazu a seřadit výsledky s `ORDER BY` klauzule, díky tomu je nákladnější jednotkách žádosti.

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 306 ms | 2063.54 RU | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>Odráží na výkon V1

Prohlédněte problémy s výkonem, které jsme čelí v předchozí části, abychom mohli identifikovat problémy dva hlavní třídy:

- některé požadavky vyžadují více dotazů vydá. aby bylo možné shromáždit všechna data, že budeme muset vrátit,
- Některé dotazy není filtr klíče oddílu kontejnerů, které cílí na, což vede k větveného, který brání naší škálovatelnost.

Můžeme vyřešit, každá z těchto problémů, počínaje první z nich.

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2: Představujeme denormalizace optimalizovat další dotazy

Důvod, proč máme vydávat další požadavky v některých případech je vzhledem k tomu, že výsledky v prvotní žádosti neobsahuje všechna data, která potřebujeme k vrácení. Při práci s úložišť nerelačních dat, například služby Azure Cosmos DB, tento druh problému se běžně řeší denormalizing dat napříč naší datové sady.

V našem příkladu upravíme příspěvek položky, které chcete přidat uživatelské jméno autora na příspěvek, počet komentářů a počet lajků:

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

Můžeme také změnit komentář a položky, které chcete přidat uživatelské jméno uživatele, který je vytvořil, jako jsou:

    {
      "id": "<comment-id>",
      "type": "comment",
      "postId": "<post-id>",
      "userId": "<comment-author-id>",
      "userUsername": "<comment-author-username>",
      "content": "<comment-content>",
      "creationDate": "<comment-creation-date>"
    }

    {
      "id": "<like-id>",
      "type": "like",
      "postId": "<post-id>",
      "userId": "<liker-id>",
      "userUsername": "<liker-username>",
      "creationDate": "<like-creation-date>"
    }

### <a name="denormalizing-comment-and-like-counts"></a>Denormalizing komentář a, jako jsou počty

Co chcete dosáhnout je, že pokaždé, když jsme přidat komentář nebo lajk, jsme také zvýšit `commentCount` nebo `likeCount` v odpovídající příspěvku. Jako naše `posts` kontejneru je rozdělený podle `postId`, nové položky (komentáře nebo, jako jsou) a jeho odpovídající příspěvek sednout před stejného logického oddílu. Díky tomu můžeme použít [uloženou proceduru](stored-procedures-triggers-udfs.md) k provedení této operace.

Teď při vytváření komentář (**[C3]**), namísto pouhého přidání nové položky v `posts` kontejneru říkáme následující uložené procedury v tomto kontejneru:

```javascript
function createComment(postId, comment) {
  var collection = getContext().getCollection();

  collection.readDocument(
    `${collection.getAltLink()}/docs/${postId}`,
    function (err, post) {
      if (err) throw err;

      post.commentCount++;
      collection.replaceDocument(
        post._self,
        post,
        function (err) {
          if (err) throw err;

          comment.postId = postId;
          collection.createDocument(
            collection.getSelfLink(),
            comment
          );
        }
      );
    })
}
```

Tuto uloženou proceduru přijímá ID příspěvku a textem nového komentáře jako parametry, pak:

- načte příspěvku
- zvýší `commentCount`
- nahradí v příspěvku
- Přidá nový komentář

Jako uložené procedury jsou spouštěny jako atomické transakce, je zaručeno, hodnota `commentCount` a skutečný počet komentářů bude vždy synchronizované.

Jsme samozřejmě volání podobně jako uložené procedury, při přidání nových lajků se zvýší `likeCount`.

### <a name="denormalizing-usernames"></a>Denormalizing uživatelských jmen

Uživatelská jména vyžadují jiný přístup, jak uživatelům nejen nacházejí v různých oddílech, ale jiný kontejner. Když máme denormalizovat data napříč oddíly a kontejnery, můžeme použít zdrojového kontejneru [kanálu změn](change-feed.md).

V našem příkladu používáme kanálu změn `users` kontejneru reagovat pokaždé, když se uživatelé aktualizovat jejich uživatelských jmen. Pokud k tomu dojde, můžeme voláním jinou uloženou proceduru v šíření změny `posts` kontejneru:

![Denormalizing uživatelských jmen do kontejneru příspěvky](./media/how-to-model-partition-example/denormalization-1.png)

```javascript
function updateUsernames(userId, username) {
  var collection = getContext().getCollection();
  
  collection.queryDocuments(
    collection.getSelfLink(),
    `SELECT * FROM p WHERE p.userId = '${userId}'`,
    function (err, results) {
      if (err) throw err;

      for (var i in results) {
        var doc = results[i];
        doc.userUsername = username;

        collection.upsertDocument(
          collection.getSelfLink(),
          doc);
      }
    });
}
```

Tuto uloženou proceduru přijímá ID uživatele a nové uživatelské jméno uživatele jako parametry, pak:

- načte všechny položky odpovídající `userId` (která může být příspěvky, komentáře, nebo lajků)
- pro každou z těchto položek
  - nahradí `userUsername`
  - nahrazuje položku

> [!IMPORTANT]
> Tato operace je nákladná, protože vyžaduje, aby tuto uloženou proceduru, který se spustí na každý oddíl `posts` kontejneru. Předpokládáme, že většina uživatelů během registrace zvolit vhodnou uživatelské jméno a někdy se nezmění, takže tato aktualizace se spustí jen velmi zřídka.

## <a name="what-are-the-performance-gains-of-v2"></a>Co jsou zvýšení výkonu v2?

### <a name="q2-retrieve-a-post"></a>[2] Načíst příspěvek

Teď, když náš denormalizace na místě, máme pouze načíst jednu položku pro zpracování této žádosti.

![Načítání jednu položku z kontejneru příspěvky](./media/how-to-model-partition-example/V2-Q2.png)

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 2 ms | 1 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[4] Seznam komentářů na příspěvek

Sem znovu, jsme ušetřit dodatečné požadavky načíst uživatelských jmen a end pomocí jediného dotazu, který filtruje klíče oddílu.

![Načítají se všechny komentáře příspěvku](./media/how-to-model-partition-example/V2-Q4.png)

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 4 ms | 7,72 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[5] Seznam vytvoří příspěvek lajků

Přesně stejné situaci při výpisu lajků.

![Načíst všechny lajků příspěvek](./media/how-to-model-partition-example/V2-Q5.png)

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 4 ms | 8.92 RU | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3: Zajišťuje, všechny požadavky jsou škálovatelné

Prohlížení naše celková vylepšení výkonu, pořád existují dva požadavky, které jsme dosud plně optimalizována: **[3]** a **[6]**. Jsou požadavky zahrnující dotazy, které není filtr klíče oddílu, který cílí na kontejnery.

### <a name="q3-list-a-users-posts-in-short-form"></a>[3] Seznam příspěvků uživatele v krátkých úseků

Tento požadavek již využívá vylepšení ve verzi V2, která šetří další dotazy.

![Načítají se všechny příspěvky pro uživatele](./media/how-to-model-partition-example/V2-Q3.png)

Zbývající dotazu není stále filtrování na klíč oddílu, ale `posts` kontejneru.

Je ve skutečnosti jednoduchý způsob, jak uvažovat o této situaci:

1. Tento požadavek *má* k filtrování `userId` protože chceme, aby se načíst všechny příspěvky určitého uživatele
1. Neprovádí dobrých výsledků, protože se provede na `posts` kontejneru, což není rozdělena na oddíly pomocí `userId`
1. S oznámením zřejmé, jsme by vyřešit naše problémy s výkonem zpracování tohoto požadavku proti kontejner, který *je* dělené podle `userId`
1. Ukázalo se, že už máme těchto kontejnerů: `users` kontejneru!

Takže zavedeme druhou úroveň denormalizace tak, že duplikujete celý příspěvků na `users` kontejneru. Tímto způsobem, který, jsme efektivně kopii naše příspěvky pouze dělené podle různých dimenzí, díky kterým jsou stejně, jako efektivnější pro načítání jejich `userId`.

`users` Kontejner nyní obsahuje 2 typy položek:

    {
      "id": "<user-id>",
      "type": "user",
      "userId": "<user-id>",
      "username": "<username>"
    }

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

Poznámky:

- zavedli jsme `type` pole v položce uživatele k odlišení uživatelů z příspěvků,
- jsme přidali i `userId` pole v položce uživatele, což je redundantní pomocí `id` pole, ale je potřeba `users` kontejner je teď rozdělený podle `userId` (a ne `id` jako dříve)

K dosažení této denormalizace, použijeme znovu kanálu změn. Tentokrát, budeme reagovat na kanálu změn `posts` kontejner pro všechny nové nebo aktualizované příspěvku k odeslání `users` kontejneru. A protože výpis příspěvky nevyžaduje, aby se vraťte jejich celý obsah, můžeme je zkrátit v procesu.

![Denormalizing příspěvků do kontejneru Uživatelé](./media/how-to-model-partition-example/denormalization-2.png)

Teď můžeme směrovat naše dotaz, který `users` kontejneru, filtrování kontejneru klíče oddílu.

![Načítají se všechny příspěvky pro uživatele](./media/how-to-model-partition-example/V3-Q3.png)

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 4 ms | 6.46 RU | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[6] Seznam nejnovějších příspěvků x vytvořené v krátkém tvaru (informační kanál)

Máme řešit podobné situaci: i po Reserve Sparing Table další dotazy zanechaný zbytečné denormalizace zavedené ve verzi V2, zbývající dotazu nefiltruje na klíč oddílu kontejneru:

![Načtení nejnovější příspěvky](./media/how-to-model-partition-example/V2-Q6.png)

Následující stejným způsobem, maximalizovat výkon a škálovatelnost tento požadavek vyžaduje, že nedosáhne pouze jeden oddíl. Toto je představitelný, protože máme pouze omezený počet položek; vrátí aby bylo možné naplnit naše blogovací platforma domovskou stránku, potřebujeme získat 100 nejnovější příspěvky, aniž byste museli stránkování prostřednictvím celou datovou sadu.

Tak optimalizovat tento poslední žádosti, zavedeme třetí kontejner náš návrh, jsou vyhrazeny se, že tento požadavek. Jsme naše příspěvky na, že noví denormalizovat `feed` kontejneru:

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

Tento kontejner je rozdělený podle `type`, který bude vždy `post` v našich položky. To zajistí, že všechny položky v tomto kontejneru se nacházejí ve stejném oddílu.

K dosažení denormalizace stačilo připojení na kanálu zavedli jsme dříve k odeslání příspěvků na tento nový kontejner kanálu změn. Jeden důležité na holé nezapomeňte je, že potřebujeme, abyste měli jistotu, že uchováváme jenom 100 nejnovější příspěvky v opačném případě může obsah kontejneru růst přesahuje maximální velikost oddílu. To se provádí pomocí volání [po triggeru](stored-procedures-triggers-udfs.md#triggers) pokaždé, když dokument v kontejneru:

![Denormalizing příspěvků do kontejneru informačního kanálu](./media/how-to-model-partition-example/denormalization-3.png)

Tady je text po aktivační událost, která ořízne kolekci:

```javascript
function truncateFeed() {
  const maxDocs = 100;
  var context = getContext();
  var collection = context.getCollection();

  collection.queryDocuments(
    collection.getSelfLink(),
    "SELECT VALUE COUNT(1) FROM f",
    function (err, results) {
      if (err) throw err;

      processCountResults(results);
    });

  function processCountResults(results) {
    // + 1 because the query didn't count the newly inserted doc
    if ((results[0] + 1) > maxDocs) {
      var docsToRemove = results[0] + 1 - maxDocs;
      collection.queryDocuments(
        collection.getSelfLink(),
        `SELECT TOP ${docsToRemove} * FROM f ORDER BY f.creationDate`,
        function (err, results) {
          if (err) throw err;

          processDocsToRemove(results, 0);
        });
    }
  }

  function processDocsToRemove(results, index) {
    var doc = results[index];
    if (doc) {
      collection.deleteDocument(
        doc._self,
        function (err) {
          if (err) throw err;

          processDocsToRemove(results, index + 1);
        });
    }
  }
}
```

Posledním krokem je přesměrovat dotaz do nového `feed` kontejneru:

![Načtení nejnovější příspěvky](./media/how-to-model-partition-example/V3-Q6.png)

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 9 ms | 16.97 RU | ✅ |

## <a name="conclusion"></a>Závěr

Pojďme Podíváme se na celkový výkon a škálovatelnost, kterou jsme zavedli přes různé verze našich návrhu vylepšení.

| | V1 | V2 | V3 |
| --- | --- | --- | --- |
| **[C1]** | 7 ms / 5.71 RU | 7 ms / 5.71 RU | 7 ms / 5.71 RU |
| **[Q1]** | 2 ms / 1 RU | 2 ms / 1 RU | 2 ms / 1 RU |
| **[C2]** | 9 ms / 8.76 RU | 9 ms / 8.76 RU | 9 ms / 8.76 RU |
| **[Q2]** | 9 ms / 19.54 RU | 2 ms / 1 RU | 2 ms / 1 RU |
| **[Q3]** | 130 ms / 619.41 RU | 28 ms / 201.54 RU | 4 ms / 6.46 RU |
| **[C3]** | 7 ms / výška 8,57 RU | 7 ms / 15.27 RU | 7 ms / 15.27 RU |
| **[Q4]** | 23 ms / 27.72 RU | 4 ms / 7.72 RU | 4 ms / 7.72 RU |
| **[C4]** | 6 ms / 7.05 RU | 7 ms / 14.67 RU | 7 ms / 14.67 RU |
| **[Q5]** | 59 ms / 58.92 RU | 4 ms / 8.92 RU | 4 ms / 8.92 RU |
| **[Q6]** | 306 ms / 2063.54 RU | 83 ms / 532.33 RU | 9 ms / 16.97 RU |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>Optimalizovali jsme scénáře náročné na čtení

Mohli jste si všimnout, že jsme koncentrované naše úsilí při zvýšení výkonu požadavků na čtení (dotazy) za cenu požadavky na zápis (příkazů). V mnoha případech operace zápisu teď aktivovat následné denormalizace prostřednictvím změnit informační kanály a díky tomu jsou výpočetně náročná a cílem materializovat delší dobu.

To je podloženo skutečnost, že je platforma pro blogování (jako jsou nejvíce sociální aplikace) čtení náročná na výkon, což znamená, že objem požadavků na čtení, která má sloužit obvykle řádů vyšší než počet požadavků na zápis. Proto je vhodné provést dražší, provést, aby nechat požadavků na zápis na požadavky na čtení být levnější a lepší provádění.

Když se podíváme v nejextrémnějších optimalizace, které jsme udělali, **[6]** pokazilo z 2000 + jednotek ru na právě 17 ru; jsme dosáhli, který podle denormalizing příspěvky a účtuje se cenou asi na 10 jednotek požadavku za položku. Protože zobrazujeme by mnohem více informačního kanálu požadavky než vytvoření nebo aktualizace příspěvků, náklady na tento denormalizace jsou zanedbatelný zvážení celkové úspory.

### <a name="denormalization-can-be-applied-incrementally"></a>Můžete použít přírůstkové denormalizace

Vylepšení škálovatelnosti, které v tomto článku jste Prozkoumali jsme zahrnují denormalizace a replikace dat v datové sadě. Je třeba poznamenat, že tyto optimalizace nemusíte být důraz na místě v 1. den. Dotazy, které filtrování podle klíče oddílů líp fungovat ve velkém měřítku, ale dotazy napříč oddíly může být zcela přijatelné, pokud jsou volány jen zřídka nebo proti omezené datové sady. Pokud jste právě vytvoření prototypu nebo spouštění produktů s malým a řízené uživatelské základny, můžete pravděpodobně náhradní těchto vylepšení pro pozdější; Co je důležité, pak je [monitorování](use-metrics.md) váš model výkonu, abyste se mohli rozhodnout, jestli a kdy je doba na přenesení.

Změna kanálu, kterou používáme k distribuci aktualizací do jiné kontejnery úložiště všech projektů aktualizuje trvale. To umožňuje požádat o všech aktualizací od vytvoření kontejneru a zavedení Nenormalizovaná zobrazení jako jednorázová operace můžete projít i v případě, že velké množství dat již má váš systém.

## <a name="next-steps"></a>Další postup

Po tomto úvodu k praktické dat, modelování a vytváření oddílů můžete chtít zkontrolovat ke kontrole koncepty, kterým jsme probrali v následujících článcích:

- [Práce s databázemi, kontejnery a položkami](databases-containers-items.md)
- [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
- [Změna informačního kanálu ve službě Azure Cosmos DB](change-feed.md)