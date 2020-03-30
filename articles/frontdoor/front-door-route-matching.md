---
title: Azure Front Door – monitorování párování pravidel směrování | Dokumenty společnosti Microsoft
description: Tento článek vám pomůže pochopit, jak Azure Front Door odpovídá pravidlu směrování, které se má použít pro příchozí požadavek.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 605974e76c3ca878784129f7c9827a78d0642da6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471587"
---
# <a name="how-front-door-matches-requests-to-a-routing-rule"></a>Způsob, jakým přední dveře odpovídají požadavkům s pravidlem směrování

Po navázání připojení a provedení ssl handshake, když požadavek přistane na front door prostředí jedna z prvních věcí, které přední dveře dělá, je určení ze všech konfigurací, které konkrétní směrování pravidlo, aby odpovídaly požadavku a pak s definované akce. Následující dokument vysvětluje, jak front door určuje, kterou konfiguraci trasy použít při zpracování požadavku HTTP.

## <a name="structure-of-a-front-door-route-configuration"></a>Struktura konfigurace trasy předních dveří
Konfigurace pravidel směrování předních dveří se skládá ze dvou hlavních částí: "levá strana" a "pravá strana". Příchozí požadavek přiřazujeme k levé straně trasy, zatímco pravá strana definuje způsob zpracování požadavku.

### <a name="incoming-match-left-hand-side"></a>Příchozí zápas (levá strana)
Následující vlastnosti určují, zda příchozí požadavek odpovídá pravidlu směrování (nebo na levé straně):

* **Protokoly HTTP** (HTTP/HTTPS)
* **Hostitelé** (například\.www \*foo.com, .bar.com)
* **Cesty** (například /\*, /users/\*, /file.gif)

Tyto vlastnosti jsou rozbaleny interně tak, aby každá kombinace protokolu/hostitele/cesty je potenciální sada shody.

### <a name="route-data-right-hand-side"></a>Údaje o trase (pravá strana)
Rozhodnutí o způsobu zpracování požadavku závisí na tom, zda je ukládání do mezipaměti povoleno nebo ne pro konkrétní trasu. Takže pokud nemáme odpověď uloženou v mezipaměti pro požadavek, předáme požadavek příslušnému back-endu v nakonfigurovaném back-endovém fondu.

## <a name="route-matching"></a>Párování trasy
Tato část se zaměří na to, jak odpovídáme danému pravidlu směrování předních dveří. Základním konceptem je, že vždy odpovídáme **nejkonkrétnějšímu zápasu,** který se nejprve dívá pouze na "levou stranu".  Nejprve se shodujeme na základě protokolu HTTP, pak frontendového hostitele a potom na cestě.

### <a name="frontend-host-matching"></a>Párování hostitele front-endu
Při porovnávání hostitelů Frontendu používáme logiku takto:

1. Vyhledejte jakékoli směrování s přesnou shodou na hostiteli.
2. Pokud se neshodují žádní přesní hostitelé front-endu, zamítněte požadavek a odešlete chybu 400 chybný požadavek.

Chcete-li tento proces dále vysvětlit, podívejme se na příklad konfigurace tras předních dveří (pouze na levé straně):

| Pravidlo směrování | Frontendoví hostitelé | Cesta |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| B | foo.contoso.com | /uživatelé/\* |
| C | www\.fabrikam.com, foo.adventure-works.com  | /\*, /obrázky/\* |

Pokud byly do předních dveří odeslány následující příchozí požadavky, porovnávají se s následujícími pravidly směrování shora:

| Příchozí front-end hostitele | Odpovídající pravidla směrování |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www\.fabrikam.com | C |
| images.fabrikam.com | Chyba 400: Chybný požadavek |
| foo.adventure-works.com | C |
| contoso.com | Chyba 400: Chybný požadavek |
| www\.adventure-works.com | Chyba 400: Chybný požadavek |
| www\.northwindtraders.com | Chyba 400: Chybný požadavek |

### <a name="path-matching"></a>Odpovídající cesta
Po určení konkrétního front-endového hostitele a filtrování možných pravidel směrování pouze na trasy s tímto front-endovým hostitelem front door pak front door filtruje pravidla směrování na základě cesty požadavku. Používáme podobnou logiku jako frontendové hostitele:

1. Vyhledejte libovolné pravidlo směrování s přesnou shodou na cestě
2. Pokud není přesná shoda cesty, vyhledejte pravidla směrování se zástupným znakem cesta, která odpovídá
3. Pokud nejsou nalezena žádná pravidla směrování s odpovídající cestou, zamítněte požadavek a vraťte 400: Chybný požadavek chyby HTTP odpověď.

>[!NOTE]
> Všechny cesty bez zástupné znaku jsou považovány za cesty s přesnou shodou. I když cesta končí lomítkem, je stále považována za přesnou shodu.

Chcete-li dále vysvětlit, podívejme se na další sadu příkladů:

| Pravidlo směrování | Front-end hostitele    | Cesta     |
|-------|---------|----------|
| A     | www\.contoso.com | /        |
| B     | www\.contoso.com | /\*      |
| C     | www\.contoso.com | /ab      |
| D     | www\.contoso.com | /abc     |
| E     | www\.contoso.com | /abc/    |
| F     | www\.contoso.com | /abc/\*  |
| G     | www\.contoso.com | /abc/def |
| H     | www\.contoso.com | /cesta/   |

Vzhledem k této konfiguraci by výsledkem následujícípříklad odpovídající tabulky:

| Příchozí požadavek    | Odpovídající trasa |
|---------------------|---------------|
| www\.contoso.com/            | A             |
| www\.contoso.com/a           | B             |
| www\.contoso.com/ab          | C             |
| www\.contoso.com/abc         | D             |
| www\.contoso.com/abzzz       | B             |
| www\.contoso.com/abc/        | E             |
| www\.contoso.com/abc/d       | F             |
| www\.contoso.com/abc/def     | G             |
| www\.contoso.com/abc/defzzz  | F             |
| www\.contoso.com/abc/def/ghi | F             |
| www\.contoso.com/path        | B             |
| www\.contoso.com/path/       | H             |
| www\.contoso.com/path/zzz    | B             |

>[!WARNING]
> </br> Pokud neexistují žádná pravidla směrování pro front-endového hostitele s přesnou shodou s trasou catch-all (`/*`), nebude existovat shoda s žádným pravidlem směrování.
>
> Příklad konfigurace:
>
> | Trasa | Hostitel             | Cesta    |
> |-------|------------------|---------|
> | A     | profile.contoso.com | /api/\* |
>
> Odpovídající tabulka:
>
> | Příchozí požadavek       | Odpovídající trasa |
> |------------------------|---------------|
> | profile.domain.com/other | Žádné. Chyba 400: Chybný požadavek |

### <a name="routing-decision"></a>Rozhodnutí o směrování
Jakmile se přirovnáme k jedinému pravidlu směrování předních dveří, musíme zvolit způsob zpracování požadavku. Pokud pro odpovídající směrovací pravidlo, Front Door má cache odpověď k dispozici pak stejné dostane doručena zpět klientovi. V opačném případě je další věc, která se vyhodnotí, zda jste nakonfigurovali [přepis adresy URL (vlastní cestu pro předávání)](front-door-url-rewrite.md) pro odpovídající pravidlo směrování nebo ne. Pokud není definována vlastní cesta pro předávání, požadavek se předává příslušnému back-endu v nakonfigurovaném back-endovém fondu tak, jak je. Jinak je cesta požadavku aktualizována podle [vlastní cesty předávání](front-door-url-rewrite.md) definované a pak dopředu do back-endu.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
