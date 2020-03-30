---
title: Modelujte a rozdělujte data v Azure Cosmos DB s reálným příkladem
description: Naučte se modelovat a rozdělovat reálný příklad pomocí rozhraní Azure Cosmos DB Core API
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 10f8ffd90215a21ca03e112aea463d444c623d06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445379"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>Modelování a dělení dat ve službě Azure Cosmos DB s využitím příkladu z reálného světa

Tento článek vychází z několika konceptů Azure Cosmos DB, jako je [modelování dat](modeling-data.md), [dělení](partitioning-overview.md)a [zřízená propustnost,](request-units.md) aby se ukázalo, jak řešit cvičení návrhu reálných dat.

Pokud obvykle pracujete s relačními databázemi, pravděpodobně jste vytvořili návyky a intuice o tom, jak navrhnout datový model. Vzhledem k konkrétní omezení, ale také jedinečné silné stránky Azure Cosmos DB, většina z těchto osvědčených postupů nepřekládá dobře a může přetáhnout do neoptimální řešení. Cílem tohoto článku je vás provede celý proces modelování reálného případu použití v Azure Cosmos DB, od modelování položek na entity společné umístění a dělení kontejnerů.

## <a name="the-scenario"></a>Scénář

Pro toto cvičení budeme zvažovat doménu blogovací platformy, kde *mohou uživatelé* vytvářet *příspěvky*. Uživatelé mohou také *jako* a přidat *komentáře* k těmto příspěvkům.

> [!TIP]
> Některá slova jsme *zvýraznili kurzívou*; tato slova identifikují druh "věcí", s nimiž bude muset náš model manipulovat.

Přidání dalších požadavků do naší specifikace:

- Na titulní stránce se zobrazí informační kanál nedávno vytvořených příspěvků,
- Můžeme načíst všechny příspěvky pro uživatele, všechny komentáře k příspěvku a všechny lajky pro příspěvek,
- Příspěvky jsou vráceny s uživatelským jménem svých autorů a počítat, kolik komentářů a rád, že mají,
- Komentáře a lajky jsou také vráceny s uživatelským jménem uživatelů, kteří je vytvořili,
- Při zobrazení jako seznamy musí příspěvky pouze prezentovat zkrácený souhrn jejich obsahu.

## <a name="identify-the-main-access-patterns"></a>Identifikace hlavních vzorů přístupu

Chcete-li začít, dáváme určitou strukturu naší počáteční specifikace tím, že identifikujeme přístupové vzory našeho řešení. Při navrhování datového modelu pro Azure Cosmos DB, je důležité pochopit, které požadavky náš model bude muset sloužit k ujistěte se, že model bude obsluhovat tyto požadavky efektivně.

Chcete-li usnadnit sledování celého procesu, kategorizujeme tyto různé požadavky jako příkazy nebo dotazy, vypůjčíme si nějakou slovní zásobu z [CQRS,](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation) kde příkazy jsou požadavky na zápis (to znamená záměry aktualizace systému) a dotazy jsou požadavky jen pro čtení.

Zde je seznam požadavků, které naše platforma bude muset vystavit:

- **[C1]** Vytvoření/úprava uživatele
- **[Č. 1]** Načtení uživatele
- **[C2]** Vytvoření/úprava příspěvku
- **[Č. 2]** Načíst příspěvek
- **[Č. 3]** Seznam příspěvků uživatele v krátké podobě
- **[C3]** Vytvoření komentáře
- **[Č. 4]** Seznam komentářů k příspěvku
- **[C4]** To se mi líbí příspěvek
- **[Č. 5]** Vypsat to se mi líbí příspěvku
- **[Č. 6]** Seznam *x* nejnovější příspěvky vytvořené v krátké podobě (feed)

V této fázi jsme nepřemýšleli o podrobnostech o tom, co bude každá entita (uživatel, příspěvek atd.) obsahovat. Tento krok je obvykle mezi prvními, které je třeba řešit při navrhování proti relačnímu obchodu, protože musíme zjistit, jak se tyto entity budou překládat z hlediska tabulek, sloupců, cizích klíčů atd. Je mnohem méně obavy s databází dokumentů, která nevynucuje žádné schéma při zápisu.

Hlavním důvodem, proč je důležité identifikovat naše přístupové vzory od začátku, je to, že tento seznam požadavků bude naší testovací sadou. Pokaždé, když iterujeme náš datový model, projdeme každý z požadavků a zkontrolujeme jejich výkon a škálovatelnost.

## <a name="v1-a-first-version"></a>V1: První verze

Začneme se dvěma `users` `posts`kontejnery: a .

### <a name="users-container"></a>Kontejner uživatelů

Tento kontejner ukládá pouze uživatelské položky:

    {
      "id": "<user-id>",
      "username": "<username>"
    }

Tento kontejner rozdělíme podle `id`, což znamená, že každý logický oddíl v rámci tohoto kontejneru bude obsahovat pouze jednu položku.

### <a name="posts-container"></a>Kontejner příspěvků

Tento kontejner hostí příspěvky, komentáře a to se mi líbí:

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

Dělíme tento `postId`kontejner podle , což znamená, že každý logický oddíl v rámci tohoto kontejneru bude obsahovat jeden příspěvek, všechny komentáře k tomuto příspěvku a všechny lajky pro tento příspěvek.

Všimněte si, že `type` jsme zavedli vlastnost v položkách uložených v tomto kontejneru rozlišovat mezi tři typy entit, které tento kontejner hostuje.

Také jsme se rozhodli odkazovat na související data namísto jejich vložení (podrobnosti o těchto konceptech naleznete v [této části),](modeling-data.md) protože:

- neexistuje žádný horní limit na to, kolik příspěvků může uživatel vytvořit,
- příspěvky mohou být libovolně dlouhé,
- neexistuje žádný horní limit na to, kolik komentářů a to se mi líbí příspěvek může mít,
- chceme mít možnost přidat komentář nebo líbí se na post, aniž by museli aktualizovat post sám.

## <a name="how-well-does-our-model-perform"></a>Jak dobře si náš model vede?

Nyní je čas posoudit výkon a škálovatelnost naší první verze. Pro každý z dříve identifikovaných požadavků měříme jeho latenci a počet jednotek požadavků, které spotřebovává. Toto měření se provádí na fiktivní datové sadě obsahující 100 000 uživatelů s 5 až 50 příspěvky na uživatele a až 25 komentářů a 100 lajků na příspěvek.

### <a name="c1-createedit-a-user"></a>[C1] Vytvoření/úprava uživatele

Tento požadavek je jednoduché implementovat, jak jsme `users` právě vytvořit nebo aktualizovat položku v kontejneru. Požadavky budou pěkně rozloženy do všech oddílů díky klíči oddílu. `id`

![Zápis jedné položky do kontejneru uživatelů](./media/how-to-model-partition-example/V1-C1.png)

| **Latence** | **ŽP poplatek** | **Výkon** |
| --- | --- | --- |
| 7 ms | 5.71 ŽP | ✅ |

### <a name="q1-retrieve-a-user"></a>[Č. 1] Načtení uživatele

Načítání uživatele se provádí čtením odpovídající `users` položky z kontejneru.

![Načítání jedné položky z kontejneru uživatelů](./media/how-to-model-partition-example/V1-Q1.png)

| **Latence** | **ŽP poplatek** | **Výkon** |
| --- | --- | --- |
| 2 ms | 1 ŽP | ✅ |

### <a name="c2-createedit-a-post"></a>[C2] Vytvoření/úprava příspěvku

Podobně jako **[C1]**, stačí napsat `posts` do kontejneru.

![Zápis jedné položky do kontejneru příspěvků](./media/how-to-model-partition-example/V1-C2.png)

| **Latence** | **ŽP poplatek** | **Výkon** |
| --- | --- | --- |
| 9 ms | 8.76 ŽP | ✅ |

### <a name="q2-retrieve-a-post"></a>[Č. 2] Načíst příspěvek

Začneme načtením odpovídajícího dokumentu `posts` z kontejneru. Ale to nestačí, podle naší specifikace musíme také agregovat uživatelské jméno autora příspěvku a počty, kolik komentářů a kolik má tento příspěvek rád, což vyžaduje, aby byly vydány další dotazy SQL.

![Načítání příspěvku a agregace dalších dat](./media/how-to-model-partition-example/V1-Q2.png)

Každý z dalších dotazů filtruje klíč oddílu příslušného kontejneru, což je přesně to, co chceme maximalizovat výkon a škálovatelnost. Ale nakonec musíme provést čtyři operace, abychom vrátili jeden příspěvek, takže to v příští iteraci zlepšíme.

| **Latence** | **ŽP poplatek** | **Výkon** |
| --- | --- | --- |
| 9 ms | 19.54 ŽP | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>[Č. 3] Seznam příspěvků uživatele v krátké podobě

Za prvé, musíme načíst požadované příspěvky s dotazem SQL, který načte příspěvky odpovídající tomuto konkrétnímu uživateli. Ale musíme také vydat další dotazy pro agregaci uživatelského jména autora a počty komentářů a lajků.

![Načítání všech příspěvků pro uživatele a agregace jejich dalších dat](./media/how-to-model-partition-example/V1-Q3.png)

Tato implementace představuje mnoho nevýhod:

- dotazy, které agregují počty komentářů a lajků, musí být vydány pro každý příspěvek vrácený prvním dotazem,
- hlavní dotaz nefiltruje klíč oddílu `posts` kontejneru, což vede k fan-out a prohledávání oddílu v kontejneru.

| **Latence** | **ŽP poplatek** | **Výkon** |
| --- | --- | --- |
| 130 ms | 619.41 ŽP | ⚠ |

### <a name="c3-create-a-comment"></a>[C3] Vytvoření komentáře

Komentář je vytvořen zápisem odpovídající `posts` položky v kontejneru.

![Zápis jedné položky do kontejneru příspěvků](./media/how-to-model-partition-example/V1-C2.png)

| **Latence** | **ŽP poplatek** | **Výkon** |
| --- | --- | --- |
| 7 ms | 8.57 ŽP | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Č. 4] Seznam komentářů k příspěvku

Začneme s dotazem, který načte všechny komentáře k tomuto příspěvku a opět musíme také agregovat uživatelská jména zvlášť pro každý komentář.

![Načítání všech komentářů k příspěvku a agregace jejich dalších dat](./media/how-to-model-partition-example/V1-Q4.png)

Přestože hlavní dotaz filtruje klíč oddílu kontejneru, agregace uživatelských jmen samostatně penalizuje celkový výkon. Později to vylepšíme.

| **Latence** | **ŽP poplatek** | **Výkon** |
| --- | --- | --- |
| 23 ms | 27.72 ŽP | ⚠ |

### <a name="c4-like-a-post"></a>[C4] To se mi líbí příspěvek

Stejně jako **[C3]** vytvoříme odpovídající `posts` položku v kontejneru.

![Zápis jedné položky do kontejneru příspěvků](./media/how-to-model-partition-example/V1-C2.png)

| **Latence** | **ŽP poplatek** | **Výkon** |
| --- | --- | --- |
| 6 ms | 7.05 ŽP | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Č. 5] Vypsat to se mi líbí příspěvku

Stejně jako **[Q4]**, jsme dotaz rád, že post, pak agregovat jejich uživatelská jména.

![Načítání všech lajků pro příspěvek a agregace jejich dalších dat](./media/how-to-model-partition-example/V1-Q5.png)

| **Latence** | **ŽP poplatek** | **Výkon** |
| --- | --- | --- |
| 59 ms | 58.92 ŽP | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Č. 6] Seznam x nejnovější příspěvky vytvořené v krátké podobě (feed)

Nejnovější příspěvky načítáme dotazem na `posts` kontejner seřazený podle sestupného data vytvoření, poté agregujeme uživatelská jména a počty komentářů a lajků pro každý z příspěvků.

![Načítání nejnovějších příspěvků a agregace jejich dodatečných údajů](./media/how-to-model-partition-example/V1-Q6.png)

Náš počáteční dotaz opět nefiltruje klíč oddílu `posts` kontejneru, což spouští nákladný ventilátor. Tenje ještě horší, protože se zaměřujeme na mnohem `ORDER BY` větší sadu výsledků a řadíme výsledky s klauzulí, což je dražší, pokud jde o jednotky požadavků.

| **Latence** | **ŽP poplatek** | **Výkon** |
| --- | --- | --- |
| 306 ms | 2063.54 ŽP | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>Úvahy o výkonu V1

Podíváme-li se na problémy s výkonem, kterým jsme čelili v předchozí části, můžeme identifikovat dvě hlavní třídy problémů:

- některé žádosti vyžadují, aby bylo vydáno více dotazů, aby bylo možné shromáždit všechny údaje, které potřebujeme vrátit,
- některé dotazy nefiltrují klíč oddílu kontejnerů, na které cílí, což vede k fan-outu, který brání naší škálovatelnosti.

Pojďme vyřešit každý z těchto problémů, počínaje prvním.

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2: Zavedení denormalizace pro optimalizaci čtení dotazů

Důvod, proč musíme v některých případech vydat další požadavky, je ten, že výsledky původní žádosti neobsahují všechny údaje, které potřebujeme vrátit. Při práci s nerelačníúložiště dat, jako je Azure Cosmos DB, tento druh problému se běžně řeší denormalizing dat v celé naší datové sady.

V našem příkladu upravujeme položky příspěvku tak, aby přidávaly uživatelské jméno autora příspěvku, počet komentářů a počet označení To se mi líbí:

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

Upravujeme také komentáře a podobné položky, abychom přidali uživatelské jméno uživatele, který je vytvořil:

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

### <a name="denormalizing-comment-and-like-counts"></a>Denormalizing komentář a to se počte

Co chceme dosáhnout, je, že pokaždé, když přidáme komentář nebo `commentCount` podobně, také zvýšíme nebo v příslušném příspěvku. `likeCount` Vzhledem `posts` k tomu, `postId`náš kontejner je rozdělen a , nová položka (komentář nebo líbí) a jeho odpovídající post sedět ve stejném logickém oddílu. V důsledku toho můžeme použít [uloženou proceduru](stored-procedures-triggers-udfs.md) k provedení této operace.

Nyní při vytváření komentáře (**[C3]**), namísto `posts` pouhého přidání nové položky v kontejneru nazýváme následující uloženou proceduru v tomto kontejneru:

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

Tato uložená procedura přebírá ID příspěvku a tělo nového komentáře jako parametry, pak:

- načte příspěvek
- přírůstky`commentCount`
- nahrazuje příspěvek
- přidá nový komentář

Jako uložené procedury jsou prováděny jako atomické transakce, je zaručeno, že hodnota `commentCount` a skutečný počet komentářů zůstane vždy synchronizován.

Samozřejmě voláme podobný uložený postup při přidávání `likeCount`nových lajků do zvýšení .

### <a name="denormalizing-usernames"></a>Denormalizace uživatelských jmen

Uživatelská jména vyžadují jiný přístup jako uživatelé nejen sedět v různých oddílech, ale v jiném kontejneru. Když máme denormalizovat data mezi oddíly a kontejnery, můžeme použít zdroj kontejneru [změny krmiva](change-feed.md).

V našem příkladu používáme kanál `users` změn kontejneru reagovat vždy, když uživatelé aktualizovat svá uživatelská jména. Když se to stane, rozšíříme změnu voláním `posts` jiné uložené procedury v kontejneru:

![Denormalizace uživatelských jmen do kontejneru příspěvků](./media/how-to-model-partition-example/denormalization-1.png)

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

Tato uložená procedura přebírá ID uživatele a nové uživatelské jméno uživatele jako parametry, pak:

- načte všechny položky odpovídající `userId` (což mohou být příspěvky, komentáře nebo to se mi líbí)
- pro každou z těchto položek
  - nahrazuje`userUsername`
  - nahradí položku

> [!IMPORTANT]
> Tato operace je nákladné, protože vyžaduje tuto uloženou proceduru, která má být provedena na každém oddílu kontejneru. `posts` Předpokládáme, že většina uživatelů si během registrace zvolí vhodné uživatelské jméno a nikdy ho nezmění, takže tato aktualizace poběží velmi zřídka.

## <a name="what-are-the-performance-gains-of-v2"></a>Jaké jsou výkonzisky V2?

### <a name="q2-retrieve-a-post"></a>[Č. 2] Načíst příspěvek

Teď, když je naše denormalizace na místě, musíme načíst pouze jednu položku, abychom tuto žádost zpracovali.

![Načítání jedné položky z kontejneru příspěvků](./media/how-to-model-partition-example/V2-Q2.png)

| **Latence** | **ŽP poplatek** | **Výkon** |
| --- | --- | --- |
| 2 ms | 1 ŽP | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Č. 4] Seznam komentářů k příspěvku

Zde opět můžeme ušetřit další požadavky, které načíst uživatelská jména a skončit s jedním dotazem, který filtruje na klíč oddílu.

![Načítání všech komentářů k příspěvku](./media/how-to-model-partition-example/V2-Q4.png)

| **Latence** | **ŽP poplatek** | **Výkon** |
| --- | --- | --- |
| 4 ms | 7.72 ŽP | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Č. 5] Vypsat to se mi líbí příspěvku

Přesně stejná situace, když seznam likes.

![Načítání všech lajků pro příspěvek](./media/how-to-model-partition-example/V2-Q5.png)

| **Latence** | **ŽP poplatek** | **Výkon** |
| --- | --- | --- |
| 4 ms | 8.92 ŽP | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3: Ujistěte se, že všechny požadavky jsou škálovatelné

Podíváme-li se na naše celkové zlepšení výkonu, stále existují dva požadavky, které jsme plně neoptimalizovali: **[Q3]** a **[Q6]**. Jedná se o požadavky zahrnující dotazy, které nejsou filtrovat na klíč oddílu kontejnerů, na které cílí.

### <a name="q3-list-a-users-posts-in-short-form"></a>[Č. 3] Seznam příspěvků uživatele v krátké podobě

Tento požadavek již těží z vylepšení zavedených ve V2, který šetří další dotazy.

![Načítání všech příspěvků pro uživatele](./media/how-to-model-partition-example/V2-Q3.png)

Ale zbývající dotaz stále není filtrování na klíč `posts` oddílu kontejneru.

Způsob, jak přemýšlet o této situaci, je ve skutečnosti jednoduchý:

1. Tento požadavek *musí* `userId` filtrovat, protože chceme načíst všechny příspěvky pro konkrétního uživatele
1. Nefunguje dobře, protože je spuštěn proti `posts` kontejneru, který není rozdělen a to`userId`
1. S uvedením zřejmé, bychom vyřešit náš problém s výkonem spuštěním tohoto požadavku proti kontejneru, který *je* rozdělen do`userId`
1. Ukazuje se, že již máme takový `users` kontejner: kontejner!

Takže zavádíme druhou úroveň denormalizace duplikací celých sloupků do kontejneru. `users` Tím, že jsme účinně získat kopii našich příspěvků, pouze rozdělena podél různých rozměrů, `userId`což je tak efektivnější získat jejich .

Kontejner `users` nyní obsahuje 2 druhy položek:

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

- zavedli jsme `type` pole v uživatelské položce odlišit uživatele od příspěvků,
- přidali jsme `userId` také pole v položce uživatele, `id` které je redundantní s polem, ale je vyžadováno, protože `users` kontejner je nyní rozdělen `userId` (a ne `id` jako dříve)

K dosažení této denormalizace, jsme opět použít změnu krmiva. Tentokrát reagujeme na změnu kanálu `posts` kontejneru k odeslání nového `users` nebo aktualizovaného příspěvku do kontejneru. A protože výpis příspěvky nevyžaduje vrátit svůj plný obsah, můžeme zkrátit je v tomto procesu.

![Denormalizace příspěvků do kontejneru uživatelů](./media/how-to-model-partition-example/denormalization-2.png)

Nyní můžeme směrovat náš `users` dotaz do kontejneru, filtrování na klíč oddílu kontejneru.

![Načítání všech příspěvků pro uživatele](./media/how-to-model-partition-example/V3-Q3.png)

| **Latence** | **ŽP poplatek** | **Výkon** |
| --- | --- | --- |
| 4 ms | 6.46 ŽP | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Č. 6] Seznam x nejnovější příspěvky vytvořené v krátké podobě (feed)

Musíme se vypořádat s podobnou situací zde: i po zachování dalšídotazy vlevo zbytečné denormalizace zavedené v V2, zbývající dotaz není filtrna klíč oddílu kontejneru:

![Načítání nejnovějších příspěvků](./media/how-to-model-partition-example/V2-Q6.png)

Podle stejného přístupu, maximalizace výkonu tohoto požadavku a škálovatelnost vyžaduje, aby přístupů pouze jeden oddíl. To je myslitelné, protože musíme vrátit pouze omezený počet položek; abychom naplnili domovskou stránku naší blogovací platformy, stačí získat 100 nejnovějších příspěvků, aniž bychom museli stránkovat celou datovou sadu.

Takže pro optimalizaci tohoto posledního požadavku představujeme třetí kontejner do našeho návrhu, který je zcela věnován službě této žádosti. Denormalizujeme naše příspěvky k tomuto novému `feed` kontejneru:

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

Tento kontejner je `type`rozdělen na , `post` který bude vždy v našich položkách. Tím zajistíte, že všechny položky v tomto kontejneru bude sedět ve stejném oddílu.

Chcete-li dosáhnout denormalizace, musíme jen připojit kanál kanálu změn, který jsme dříve zavedli k odeslání příspěvků do tohoto nového kontejneru. Jedna důležitá věc, kterou je třeba mít na paměti, je, že musíme zajistit, že uchováme pouze 100 nejnovějších pracovních míst; v opačném případě může obsah kontejneru růst nad maximální velikost oddílu. To se provádí voláním [post-trigger](stored-procedures-triggers-udfs.md#triggers) pokaždé, když je dokument přidán do kontejneru:

![Denormalizační sloupky do nádoby na krmivo](./media/how-to-model-partition-example/denormalization-3.png)

Zde je tělo post-trigger, který zkrátí kolekce:

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

Posledním krokem je přesměrování našeho dotazu do našeho nového `feed` kontejneru:

![Načítání nejnovějších příspěvků](./media/how-to-model-partition-example/V3-Q6.png)

| **Latence** | **ŽP poplatek** | **Výkon** |
| --- | --- | --- |
| 9 ms | 16.97 ŽP | ✅ |

## <a name="conclusion"></a>Závěr

Podívejme se na celkové vylepšení výkonu a škálovatelnosti, které jsme zavedli v různých verzích našeho návrhu.

| | V1 | V2 | V3 |
| --- | --- | --- | --- |
| **[C1]** | 7 ms / 5,71 ŽP | 7 ms / 5,71 ŽP | 7 ms / 5,71 ŽP |
| **[Č. 1]** | 2 ms / 1 ŽP | 2 ms / 1 ŽP | 2 ms / 1 ŽP |
| **[C2]** | 9 ms / 8,76 ŽP | 9 ms / 8,76 ŽP | 9 ms / 8,76 ŽP |
| **[Č. 2]** | 9 ms / 19,54 ŽP | 2 ms / 1 ŽP | 2 ms / 1 ŽP |
| **[Č. 3]** | 130 ms / 619.41 ŽP | 28 ms / 201,54 ŽP | 4 ms / 6,46 ŽP |
| **[C3]** | 7 ms / 8,57 ŽP | 7 ms / 15,27 ŽP | 7 ms / 15,27 ŽP |
| **[Č. 4]** | 23 ms / 27,72 ŽP | 4 ms / 7,72 ŽP | 4 ms / 7,72 ŽP |
| **[C4]** | 6 ms / 7,05 ŽP | 7 ms / 14,67 ŽP | 7 ms / 14,67 ŽP |
| **[Č. 5]** | 59 ms / 58,92 ŽP | 4 ms / 8,92 ŽP | 4 ms / 8,92 ŽP |
| **[Č. 6]** | 306 ms / 2063.54 ŽP | 83 ms / 532.33 ŽP | 9 ms / 16,97 ŽP |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>Optimalizovali jsme scénář s vysokým i pro čtení

Možná jste si všimli, že jsme soustředili naše úsilí na zlepšení výkonu požadavků na čtení (dotazy) na úkor požadavků na zápis (příkazy). V mnoha případech operace zápisu nyní aktivují následnou denormalizaci prostřednictvím kanálu změn, což je činí více výpočtově nákladnými a delšími.

To je odůvodněno skutečností, že blogovací platforma (stejně jako většina sociálních aplikací) je čitelná, což znamená, že množství žádostí o čtení, které musí sloužit, je obvykle řádově vyšší než množství žádostí o zápis. Takže má smysl, aby požadavky na zápis byly dražší, aby bylo možné číst požadavky levnější a výkonnější.

Podíváme-li se na nejextrémnější optimalizaci, kterou jsme provedli, **[Q6]** přešla z 2000+ ru na pouhých 17 RU; toho jsme dosáhli denormalizací příspěvků za cenu přibližně 10 ru na položku. Vzhledem k tomu, že bychom sloužili mnohem více žádostí o krmivo než vytváření nebo aktualizace pracovních míst, náklady na tuto denormalizaci jsou zanedbatelné vzhledem k celkovým úsporám.

### <a name="denormalization-can-be-applied-incrementally"></a>Denormalizaci lze aplikovat postupně

Vylepšení škálovatelnosti, která jsme prozkoumali v tomto článku, zahrnují denormalizaci a duplikaci dat v celé sadě dat. Je třeba poznamenat, že tyto optimalizace nemusí být zavedeny v den 1. Dotazy, které filtrují klíče oddílů, fungují lépe ve velkém měřítku, ale dotazy mezi oddíly mohou být zcela přijatelné, pokud jsou volány zřídka nebo proti omezené datové sadě. Pokud právě vytváříte prototyp nebo uvádíte na trh produkt s malou a řízenou uživatelskou základnou, můžete tato vylepšení pravděpodobně ušetřit na později; Co je důležité pak je [sledovat](use-metrics.md) výkon vašeho modelu, takže se můžete rozhodnout, zda a kdy je čas, aby je v.

Kanál změn, který používáme k distribuci aktualizací do jiných kontejnerů, ukládají všechny tyto aktualizace trvale. To umožňuje požadovat všechny aktualizace od vytvoření kontejneru a bootstrapde denormalized zobrazení jako jednorázová operace catch-up i v případě, že váš systém již obsahuje velké množství dat.

## <a name="next-steps"></a>Další kroky

Po tomto úvodu k praktickému modelování dat a dělení, možná budete chtít zkontrolovat následující články, abyste si prohlédli koncepty, které jsme pokryli:

- [Práce s databázemi, kontejnery a položkami](databases-containers-items.md)
- [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
- [Změna informačního kanálu v Azure Cosmos DB](change-feed.md)
