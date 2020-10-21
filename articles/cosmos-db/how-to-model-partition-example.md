---
title: Model a rozdělení dat na Azure Cosmos DB s příkladem reálného světa
description: Naučte se modelovat a dělit příklad reálného světa pomocí rozhraní Azure Cosmos DB Core API.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/23/2019
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 8e9d11ed39d6e4dc7ad432659534e7dd14fcf1ec
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92277989"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>Modelování a dělení dat ve službě Azure Cosmos DB s využitím příkladu z reálného světa

Tento článek se sestavuje na několika Azure Cosmos DB konceptech, jako jsou [modelování dat](modeling-data.md), [vytváření oddílů](partitioning-overview.md)a [zajištěná propustnost](request-units.md) , která ukazují, jak se vypořádat s návrhem dat reálného světa.

Pokud obvykle pracujete s relačními databázemi, pravděpodobně jste vytvořili zvyky a intuitionsi, jak navrhnout datový model. Vzhledem k konkrétním omezením, ale i k jedinečným síluem Azure Cosmos DB se většina těchto osvědčených postupů neprojeví dobře a může vás přetáhnout do podoptimálních řešení. Cílem tohoto článku je projít si kompletní proces modelování reálného případu použití na Azure Cosmos DB, z modelování položek až po společné umístění entit a vytváření oddílů kontejnerů.

## <a name="the-scenario"></a>Scénář

Pro toto cvičení bereme v úvahu doménu blogovací platformy, kde můžou *Uživatelé* vytvářet *příspěvky*. Uživatelé můžou také *jako* příspěvky přidávat *Komentáře* .

> [!TIP]
> Některá slova byla v *kurzívě*zvýrazněna. Tato slova identifikují druh "věcí" Náš model bude muset manipulovat.

Přidání dalších požadavků do naší specifikace:

- Na úvodní stránce se zobrazuje informační kanál nedávno vytvořených příspěvků.
- Můžeme načíst všechny příspěvky pro uživatele, všechny komentáře k příspěvku a všechny, podobně jako u příspěvku,
- Příspěvky se vrátí s uživatelským jménem svých autorů a počtem, kolik komentářů a jako mají.
- Komentáře a podobné věci se také vrátí s uživatelským jménem uživatelů, kteří je vytvořili.
- Při zobrazení jako seznamů musí příspěvky obsahovat jenom zkrácený souhrn jejich obsahu.

## <a name="identify-the-main-access-patterns"></a>Identifikujte hlavní vzory přístupu.

Abychom mohli začít, poskytujeme určitou strukturu pro naši počáteční specifikaci tím, že identifikujeme vzory přístupu našich řešení. Když navrhujete datový model pro Azure Cosmos DB, je důležité pochopit, které požadavky má náš model sloužit k tomu, aby se zajistilo, že model bude tyto požadavky efektivně obsluhovat.

Aby bylo možné celkový postup sledovat, kategorizujte tyto různé požadavky jako příkazy nebo dotazy, přičemž jste si vypůjčili některé slovníky z [CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation) , kde příkazy jsou požadavky na zápis (tj. záměry aktualizovat systém) a dotazy jsou požadavky jen pro čtení.

Tady je seznam požadavků, které bude tato platforma muset vystavit:

- **[C1]** Vytvořit/upravit uživatele
- **[Q1]** Načtení uživatele
- **[C2]** Vytvořit nebo upravit příspěvek
- **[Q2]** Načtení příspěvku
- **[Q3]** Seznam příspěvků uživatele v krátké podobě
- **[C3]** Vytvoření komentáře
- **[4. čtvrtletí]** Výpis komentáře příspěvku
- **[C4]** Jako příspěvek
- **[Q5]** Seznam podobných příspěvků
- **[Q6]** Seznam naposledy *vytvořených* příspěvků v krátké formě (informační kanál)

V této fázi jsme si nemysleli, jaké informace o tom, co jednotlivé entity (uživatelé, příspěvky atd.) budou obsahovat. Tento krok je obvykle mezi prvními, aby se provedl při navrhování na relačním úložišti, protože je nutné zjistit, jak budou tyto entity překládat z hlediska tabulek, sloupců, cizích klíčů atd. Je mnohem méně obav s databází dokumentů, která neuplatňuje žádné schéma při zápisu.

Hlavním důvodem, proč je důležité identifikovat naše vzory přístupu od začátku, je to, že tento seznam žádostí bude naší sadou testů. Pokaždé, když procházíme přes náš datový model, projdeme jednotlivé požadavky a zkontrolujeme výkon a škálovatelnost.

## <a name="v1-a-first-version"></a>V1: první verze

Začneme se dvěma kontejnery: `users` a `posts` .

### <a name="users-container"></a>Kontejner uživatelů

Tento kontejner ukládá pouze položky uživatele:

```json
{
    "id": "<user-id>",
    "username": "<username>"
}
```

Tento kontejner nastavíme `id` na oddíly, což znamená, že každý logický oddíl v tomto kontejneru bude obsahovat jenom jednu položku.

### <a name="posts-container"></a>Kontejner příspěvků

Tento kontejner hostuje příspěvky, komentáře a podobné věci:

```json
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
```

Tento kontejner nastavíme `postId` na oddíly, což znamená, že každý logický oddíl v tomto kontejneru bude obsahovat jeden příspěvek, všechny komentáře pro daný příspěvek a všechny podobné položky pro daný příspěvek.

Všimněte si, že jsme `type` v položkách uložených v tomto kontejneru zavedli vlastnost, která rozlišuje tři typy entit, které tento kontejner hostuje.

Také jsme se rozhodli odkázat na související data, aniž byste je vložili (podrobnosti o těchto konceptech najdete v [této části](modeling-data.md) ):

- neexistuje horní limit počtu příspěvků, které může uživatel vytvořit.
- příspěvky můžou být svévolně dlouhé,
- není k dispozici horní limit počtu komentářů a podobně jako u příspěvku může být.
- Chceme být schopni přidat komentář nebo podobně jako příspěvek bez nutnosti aktualizovat příspěvek samotného příspěvku.

## <a name="how-well-does-our-model-perform"></a>Jak to náš model funguje?

Teď je čas vyhodnotit výkon a škálovatelnost naší první verze. Pro všechny dříve identifikované požadavky měříme latenci a počet jednotek požadavků, které spotřebovává. Toto měření se provádí na základě zástupné datové sady obsahující 100 000 uživatelů s 5 až 50 příspěvky na uživatele a až 25 komentářů a 100 jako na příspěvek.

### <a name="c1-createedit-a-user"></a>C1 Vytvořit/upravit uživatele

Tuto žádost je jednoduché implementovat, protože právě vytvoříme nebo aktualizujeme položku v `users` kontejneru. Požadavky budou v rámci všech oddílů v tomto případě bez ohledu na klíč oddílu v tomto případě úhledně rozloženy `id` .

:::image type="content" source="./media/how-to-model-partition-example/V1-C1.png" alt-text="Zápis jedné položky do kontejneru Users" border="false":::

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 7 MS | 5,71 RU | ✅ |

### <a name="q1-retrieve-a-user"></a>Dotazu Načtení uživatele

Načítání uživatele se provádí čtením odpovídající položky z `users` kontejneru.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q1.png" alt-text="Zápis jedné položky do kontejneru Users" border="false":::

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 2 MS | 1 RU | ✅ |

### <a name="c2-createedit-a-post"></a>Řidičské Vytvořit nebo upravit příspěvek

Podobně jako **[C1]** musíme pouze zapisovat do `posts` kontejneru.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Zápis jedné položky do kontejneru Users" border="false":::

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 9 MS | 8,76 RU | ✅ |

### <a name="q2-retrieve-a-post"></a>F2 Načtení příspěvku

Začneme načtením odpovídajícího dokumentu z `posts` kontejneru. Ale to není dostatečné, podle našich specifikací také musí být agregované uživatelské jméno autora příspěvku a počty komentářů a kolika má tento příspěvek obsahovat 3 další dotazy SQL, které mají být vydány.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q2.png" alt-text="Zápis jedné položky do kontejneru Users" border="false":::

Každý z dalších dotazů filtruje klíč oddílu příslušného kontejneru, který přesně odpovídá tomu, co chceme maximalizovat výkon a škálovatelnost. Ale nakonec musíme provést čtyři operace, aby vracely jediný příspěvek, takže ho v další iteraci Vylepšete.

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 9 MS | 19,54 RU | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>Q3 Seznam příspěvků uživatele v krátké podobě

Nejdřív musíme načíst požadované příspěvky s dotazem SQL, který načte příspěvky odpovídající tomuto konkrétnímu uživateli. Je ale také nutné vystavit další dotazy pro agregaci uživatelského jména autora a počty komentářů a jako je.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q3.png" alt-text="Zápis jedné položky do kontejneru Users" border="false":::

Tato implementace představuje mnoho nevýhod:

- dotazy, které agreguje počty komentářů a jako je třeba vydat pro každý příspěvek vrácený prvním dotazem,
- hlavní dotaz nefiltruje klíč oddílu `posts` kontejneru, což vede k vyzkoušení ventilátoru a kontrole oddílů v rámci kontejneru.

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 130 MS | 619,41 RU | ⚠ |

### <a name="c3-create-a-comment"></a>C3 Vytvoření komentáře

Komentář je vytvořen zápisem odpovídající položky do `posts` kontejneru.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Zápis jedné položky do kontejneru Users" border="false":::

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 7 MS | 8,57 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>Dotaz Výpis komentáře příspěvku

Začneme s dotazem, který načte všechny komentáře k tomuto příspěvku a znovu je také potřeba agregovat pro každý komentář samostatně.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q4.png" alt-text="Zápis jedné položky do kontejneru Users" border="false":::

I když hlavní dotaz filtruje klíč oddílu kontejneru, agreguje uživatelská jména, a to bez ohledu na celkový výkon. Vylepšete ho později.

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 23 MS | 27,72 RU | ⚠ |

### <a name="c4-like-a-post"></a>C4 Jako příspěvek

Stejně jako **[C3]** vytvoříme odpovídající položku v `posts` kontejneru.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Zápis jedné položky do kontejneru Users" border="false":::

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 6 MS | 7,05 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] Seznam podobných příspěvků

Stejně jako u tohoto příspěvku se jako **[Q4]** dotazuje jako pro tento příspěvek a pak agreguje svá uživatelská jména.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q5.png" alt-text="Zápis jedné položky do kontejneru Users" border="false":::

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 59 MS | 58,92 RU | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Seznam naposledy vytvořených příspěvků v krátké formě (informační kanál)

Nejnovější příspěvky načítáme dotazem na `posts` kontejner seřazený podle data sestupného vytváření a pak agregovaná uživatelská jména a počty komentářů a podobně jako u jednotlivých příspěvků.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q6.png" alt-text="Zápis jedné položky do kontejneru Users" border="false":::

Po opětovném spuštění náš počáteční dotaz nefiltruje klíč oddílu `posts` kontejneru, který aktivuje nákladný ventilátor. Tato jedna je ještě horší, protože cílíme na mnohem větší sadu výsledků a seřadíme výsledky s `ORDER BY` klauzulí, což snižuje náklady na jednotky požadavků.

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 306 MS | 2063,54 RU | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>Reflektování na výkon v1

V případě problémů s výkonem, které jsme provedli v předchozí části, můžeme identifikovat dvě hlavní třídy problémů:

- některé požadavky vyžadují, aby bylo možné vystavit více dotazů za účelem shromáždění všech dat, která potřebujeme vrátit.
- Některé dotazy nefiltrují klíč oddílu kontejnerů, které cílí, což vede k zablokování, které brání naší škálovatelnosti.

Pojďme vyhodnotit každý z těchto problémů, počínaje prvním z nich.

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2: představení denormalizace pro optimalizaci čtení dotazů

Důvodem, proč musíme vydávat další žádosti v některých případech, je, že výsledky počátečního požadavku neobsahují všechna data, která potřebujeme vrátit. Když pracujete s nerelačním úložištěm dat, jako je Azure Cosmos DB, tento druh problému se často vyřeší tím, že se denormalizace dat v naší datové sadě vyřeší.

V našem příkladu upravujeme položky post za účelem přidání uživatelského jména autora příspěvku, počtu komentářů a počtu podobných:

```json
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
```

Také upravujeme komentáře a podobné položky pro přidání uživatelského jména uživatele, který je vytvořil:

```json
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
```

### <a name="denormalizing-comment-and-like-counts"></a>Denormalizace komentářů a podobných počtů

To, co chceme dosáhnout, je to, že při každém přidání komentáře nebo podobného příspěvku také zvýšíme `commentCount` nebo `likeCount` v odpovídajícím příspěvku. Jak `posts` je náš kontejner rozdělený na oddíly `postId` , nová položka (komentář nebo jako) a její odpovídající příspěvek do stejného logického oddílu. V důsledku toho můžeme tuto operaci provést pomocí [uložené procedury](stored-procedures-triggers-udfs.md) .

Nyní při vytváření komentáře (**[C3]**) místo pouhého přidávání nové položky do `posts` kontejneru zavoláme následující uloženou proceduru v tomto kontejneru:

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

Tato uložená procedura vezme ID příspěvku a tělo nového komentáře jako parametry a pak:

- Načte příspěvek.
- zvýší `commentCount`
- nahradí příspěvek.
- Přidá nový komentář.

Protože uložené procedury jsou spouštěny jako atomické transakce, hodnota `commentCount` a skutečný počet komentářů bude vždy zůstat synchronizován.

Zjevně můžeme zavolat podobnou uloženou proceduru, když přidáváme nové, podobně jako k zvýšení `likeCount` .

### <a name="denormalizing-usernames"></a>Denormalizace uživatelských jmen

Uživatelské jméno vyžaduje jiný přístup, protože uživatelé nesedí pouze v různých oddílech, ale v jiném kontejneru. Když musíme denormalizovat data napříč oddíly a kontejnery, můžeme použít [kanál změn](change-feed.md)zdrojového kontejneru.

V našem příkladu používáme kanál změn `users` kontejneru, který reaguje vždycky, když uživatelé aktualizují své uživatelské jméno. V takovém případě jsme změnu rozšířili voláním jiné uložené procedury na `posts` kontejneru:

:::image type="content" source="./media/how-to-model-partition-example/denormalization-1.png" alt-text="Zápis jedné položky do kontejneru Users" border="false":::

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

Tato uložená procedura vezme ID uživatele a nového uživatelského jména uživatele jako parametry a potom:

- Načte všechny položky, které odpovídají `userId` (které mohou být příspěvky, komentáře nebo podobné položky).
- pro každou z těchto položek
  - nahrazuje `userUsername`
  - nahradí položku.

> [!IMPORTANT]
> Tato operace je náročná, protože vyžaduje, aby se tato uložená procedura spustila na všech oddílech `posts` kontejneru. Předpokládáme, že většina uživatelů při registraci zvolí vhodné uživatelské jméno a nemění ho, takže se tato aktualizace spustí jenom zřídka.

## <a name="what-are-the-performance-gains-of-v2"></a>Jaké jsou přínosy pro zvýšení výkonu v2?

### <a name="q2-retrieve-a-post"></a>F2 Načtení příspěvku

Teď, když je naše denormalizace na svém místě, musíme jenom načíst jednu položku, která tento požadavek zpracuje.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q2.png" alt-text="Zápis jedné položky do kontejneru Users" border="false":::

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 2 MS | 1 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>Dotaz Výpis komentáře příspěvku

Tady můžeme vyprázdnit žádosti o další požadavky, které načítají uživatelská jména a končí jediným dotazem, který filtruje klíč oddílu.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q4.png" alt-text="Zápis jedné položky do kontejneru Users" border="false":::

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 4 MS | 7,72 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] Seznam podobných příspěvků

Přesná stejná situace při výpisu podobných.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q5.png" alt-text="Zápis jedné položky do kontejneru Users" border="false":::

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 4 MS | 8,92 RU | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3: zajištění škálovatelnosti všech požadavků

V našich celkových vylepšeních výkonu stále existují dvě požadavky, které jsme nedokončili plně optimalizované: **[Q3]** a **[Q6]**. Jedná se o požadavky zahrnující dotazy, které nemají filtr na klíč oddílu kontejnerů, na které cílí.

### <a name="q3-list-a-users-posts-in-short-form"></a>Q3 Seznam příspěvků uživatele v krátké podobě

Tato žádost už přináší výhody vylepšení zavedených ve verzi v2, která vyprázdní další dotazy.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q3.png" alt-text="Zápis jedné položky do kontejneru Users" border="false":::

Ale zbývající dotaz se stále nefiltruje na klíč oddílu `posts` kontejneru.

Způsob, jak si představit tuto situaci, je ve skutečnosti jednoduchá:

1. Tento požadavek *musí* vyfiltrovat, `userId` protože chceme načíst všechny příspěvky pro konkrétního uživatele.
1. Nefunguje dobře, protože se provádí na `posts` kontejneru, který není rozdělený na oddíly `userId`
1. V takovém případě by byl problém s výkonem vyřešen provedením tohoto požadavku na kontejneru, který *je* rozdělen do oddílů. `userId`
1. Tím se zapíná, že tento kontejner již máme `users` .

Proto zavádíme druhou úroveň denormalizace tím, že duplikujete celé příspěvky do `users` kontejneru. Díky tomu máme efektivně kopii našich příspěvků, které jsou rozdělené jenom na oddíly v různých dimenzích. díky tomu je jejich využívání efektivnější `userId` .

`users`Kontejner teď obsahuje 2 druhy položek:

```json
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
```

Poznámky:

- zavedli jsme do `type` položky uživatele pole pro odlišení uživatelů od příspěvků.
- do položky uživatele jsme také přidali `userId` pole, které je redundantní s `id` polem, ale je potřeba, protože `users` kontejner je teď rozdělený na oddíly `userId` (a ne `id` jako dříve).

Pro dosažení této denormalizace znovu použijeme kanál změn. Tentokrát reagujeme na kanál změn `posts` kontejneru, který odesílá nové nebo aktualizované příspěvky do `users` kontejneru. A vzhledem k tomu, že výpis příspěvků nevyžaduje vrácení celého obsahu, můžeme je v procesu zkrátit.

:::image type="content" source="./media/how-to-model-partition-example/denormalization-2.png" alt-text="Zápis jedné položky do kontejneru Users" border="false":::

Nyní můžeme směrovat dotaz do `users` kontejneru a filtrovat klíč oddílu kontejneru.

:::image type="content" source="./media/how-to-model-partition-example/V3-Q3.png" alt-text="Zápis jedné položky do kontejneru Users" border="false":::

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 4 MS | 6,46 RU | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Seznam naposledy vytvořených příspěvků v krátké formě (informační kanál)

Budeme se muset vypořádat s podobným případem: i po tom, co povede k odstranění dalších dotazů, které byly nepotřebné denormalizací představené v v2, zbývající dotaz nefiltruje na klíč oddílu kontejneru:

:::image type="content" source="./media/how-to-model-partition-example/V2-Q6.png" alt-text="Zápis jedné položky do kontejneru Users" border="false":::

Po stejném přístupu vyžaduje maximalizaci výkonu a škálovatelnosti této žádosti, aby se narazí jenom na jeden oddíl. To je možné, protože potřebujeme vracet jenom omezený počet položek; abychom mohli naplnit domovskou stránku vaší domovské platformy, stačí získat nejnovější příspěvky 100, aniž byste museli stránkovat celou datovou sadu.

Takže pro optimalizaci této poslední žádosti zavádíme třetí kontejner pro náš návrh, který je zcela vyhrazený pro obsluhu této žádosti. Naši příspěvky jsme denormalizují na tento nový `feed` kontejner:

```json
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
```

Tento kontejner je rozdělen do oddílů `type` , což bude vždy `post` v našich položkách. Tím se zajistí, že všechny položky v tomto kontejneru budou zasedat do stejného oddílu.

Abychom dosáhli denormalizace, musíme jenom připojit se k kanálu změny kanálu, který jsme předtím zavedli k odeslání příspěvků do tohoto nového kontejneru. Je důležité, abyste měli jistotu, že ukládáme jenom 100 nejnovějších příspěvků. v opačném případě může obsah kontejneru přesáhnout maximální velikost oddílu. To se provádí voláním [post-triggeru](stored-procedures-triggers-udfs.md#triggers) pokaždé, když se do kontejneru přidá dokument:

:::image type="content" source="./media/how-to-model-partition-example/denormalization-3.png" alt-text="Zápis jedné položky do kontejneru Users" border="false":::

Tady je text aktivační události, která tuto kolekci zkrátí:

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

Posledním krokem je přesměrování našeho dotazu na náš nový `feed` kontejner:

:::image type="content" source="./media/how-to-model-partition-example/V3-Q6.png" alt-text="Zápis jedné položky do kontejneru Users" border="false":::

| **Latence** | **Poplatek za RU** | **Výkon** |
| --- | --- | --- |
| 9 MS | 16,97 RU | ✅ |

## <a name="conclusion"></a>Závěr

Pojďme se podívat na celkové vylepšení výkonu a škálovatelnosti, které jsme zavedli v různých verzích našeho návrhu.

| | V1 | V2 | Technologie |
| --- | --- | --- | --- |
| **C1** | 7 MS/5,71 RU | 7 MS/5,71 RU | 7 MS/5,71 RU |
| **Dotazu** | 2 MS/1 RU | 2 MS/1 RU | 2 MS/1 RU |
| **Řidičské** | 9 MS/8,76 RU | 9 MS/8,76 RU | 9 MS/8,76 RU |
| **F2** | 9 MS/19,54 RU | 2 MS/1 RU | 2 MS/1 RU |
| **Q3** | 130 MS/619,41 RU | 28 MS/201,54 RU | 4 MS/6,46 RU |
| **C3** | 7 MS/8,57 RU | 7 MS/15,27 RU | 7 MS/15,27 RU |
| **Dotaz** | 23 MS/27,72 RU | 4 MS/7,72 RU | 4 MS/7,72 RU |
| **C4** | 6 MS/7,05 RU | 7 MS/14,67 RU | 7 MS/14,67 RU |
| **[Q5]** | 59 MS/58,92 RU | 4 MS/8,92 RU | 4 MS/8,92 RU |
| **[Q6]** | 306 MS/2063,54 RU | 83 MS/532,33 RU | 9 MS/16,97 RU |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>Vyoptimalizovali jsme scénář pro čtení a těžký přístup.

Možná jste si všimli, že jsme provedli soustředěné úsilí na zlepšení výkonu žádostí o čtení (dotazů) na úkor požadavků na zápis (příkazy). V mnoha případech teď operace zápisu aktivují následnou denormalizaci prostřednictvím změnových kanálů. díky tomu jsou výpočty mnohem levnější a delší než vyhodnotit.

To je odůvodněné tím, že platforma blogů (podobně jako většina sociálních aplikací) je čitelná, což znamená, že množství požadavků na čtení, které musí zpracovat, je obvykle pořadí, které je vyšší než množství požadavků na zápis. Proto je vhodné zajistit, aby se žádosti o zápis dražší, aby bylo možné zpracovávat žádosti o čtení levnější a lepší výkon.

Pokud se podíváme na nejvíce extrémní optimalizaci, **[Q6]** se z 2000 + ru na pouhých 17 ru; dosáhli jsme, že denormalizace příspěvků za cenu kolem 10 ru na položku. Vzhledem k tomu, že jsme pomohli spoustu dalších žádostí o informační kanál než vytváření nebo aktualizace příspěvků, jsou náklady na tuto denormalizaci zanedbatelné z hlediska celkové úspory.

### <a name="denormalization-can-be-applied-incrementally"></a>Denormalizace se dá použít přírůstkově.

Vylepšení škálovatelnosti, která jsme prozkoumali v tomto článku, zahrnují denormalizaci a duplikaci dat napříč datovou sadou. Je potřeba poznamenat, že tyto optimalizace nemusíte zařadit do 1. dne. Dotazy, které filtrují klíče oddílů, fungují lépe při škálování, ale dotazy napříč oddíly mohou být zcela přijatelné, pokud jsou volány zřídka nebo proti omezené sadě dat. Pokud vytváříte prototyp nebo spouštíte produkt s malou a řízenou uživatelskou základnou, můžete tato vylepšení vylepšit později. důležité je, abyste mohli [monitorovat](use-metrics.md) výkon svého modelu, abyste se mohli rozhodnout, jestli a kdy je čas je uvést do.

Kanál změn, který používáme k distribuci aktualizací do jiných kontejnerů, ukládá všechny aktualizace trvale. Díky tomu je možné vyžádat všechny aktualizace od vytvoření kontejneru a spustit Denormalizovaná zobrazení jako jednorázovou operaci zachytávání i v případě, že váš systém již obsahuje velké množství dat.

## <a name="next-steps"></a>Další kroky

Po tomto úvodu do modelování praktických a segmentace dat můžete v následujících článcích zkontrolovat koncepty, které jsme pokryli:

- [Práce s databázemi, kontejnery a položkami](account-databases-containers-items.md)
- [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
- [Změnit informační kanál v Azure Cosmos DB](change-feed.md)
