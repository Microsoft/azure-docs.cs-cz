---
title: Služba Azure branou – směrování pravidlo odpovídající monitorování | Dokumentace Microsoftu
description: Tento článek vám pomůže pochopit, jak služba Azure branou odpovídá které pravidlo směrování pro příchozí požadavek
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
ms.openlocfilehash: eec99bde0ea73a99a9dc1345f938b821a95a7c05
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736275"
---
# <a name="how-front-door-matches-requests-to-a-routing-rule"></a>Jak branou odpovídá požadavků na pravidlo směrování

Po navázání připojení a udělat ověření typu handshake SSL, když jsou požadavek na prostředí s branou, jeden z prvních věcí, které nemá branou je určení ze všech konfigurací, které konkrétní pravidlo směrování tak, aby odpovídaly požadavku a pak můžete definované akce. Následující dokument vysvětluje, jak přední dveře Určuje konfiguraci směrování, kterou chcete použít při zpracování požadavku HTTP.

## <a name="structure-of-a-front-door-route-configuration"></a>Struktura konfiguraci směrování branou
Přední dveře konfigurace pravidla směrování se skládá ze dvou hlavních částí: "levá strana" a "pravá strana". Příchozí požadavek na levé straně trasy porovnávat, zatímco na pravé straně definuje, jak jsme zpracování žádosti.

### <a name="incoming-match-left-hand-side"></a>Příchozí shodu (levá strana)
Následující vlastnosti určují, zda příchozí žádost odpovídá pravidlo směrování (nebo levá strana):

* **Protokoly HTTP** (HTTP/HTTPS)
* **Hostitelé** (například www\.foo.com, \*. bar.com)
* **Cesty** (například /\*, /users/\*, /file.gif)

Tyto vlastnosti jsou rozbaleny interně si tak, aby protokol/hostitele nebo cestu pro každou kombinaci sadu potenciální shoda.

### <a name="route-data-right-hand-side"></a>Data trasy (pravá strana)
Rozhodnutí o tom, jak zpracovat požadavek, závisí na tom, zda je povoleno ukládání do mezipaměti, nebo ne pro konkrétní trasy. Ano, pokud odpověď uložená v mezipaměti pro žádost nemáme, jsme budete tento požadavek předá dál do příslušného back-endu v nakonfigurované back-endový fond.

## <a name="route-matching"></a>Odpovídající trasy
Tato část se zaměří na tom, jak porovnávat dané pravidlo směrování branou. Základním konceptem je, že jsme vždy odpovídat **specifické pro většinu nejprve srovnat** vypadající pouze na "levou stranu".  Jsme první shoda podle protokolu HTTP, pak front-endu hostitele a pak cesta.

### <a name="frontend-host-matching"></a>Párování hostitele front-endu
Při párování hostitele front-endu, můžeme použít logiku jak je uvedeno níže:

1. Vyhledejte všechny směrování s přesnou shodu na hostiteli.
2. Pokud se shodují se žádní hostitelé přesné front-endu, zamítnutí žádosti a odeslat chybu chybný požadavek 400.

Abychom vysvětlili, tento proces dále, Podívejme se na příklad konfigurace branou tras (pouze levá strana):

| Pravidlo směrování | Front-endoví hostitelé | `Path` |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| B | foo.contoso.com | složku /Users/\* |
| C | WWW\.fabrikam.com, foo.adventure works.com  | /\*, /images/\* |

Pokud tyto příchozí požadavky byly branou, by porovnání následující pravidla směrování výše:

| Příchozí hostitele front-endu | Odpovídající pravidla směrování |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www\.fabrikam.com | C |
| images.fabrikam.com | Chyba 400: Chybný požadavek |
| foo.adventure-works.com | C |
| contoso.com | Chyba 400: Chybný požadavek |
| www\.adventure-works.com | Chyba 400: Chybný požadavek |
| www\.northwindtraders.com | Chyba 400: Chybný požadavek |

### <a name="path-matching"></a>Cesta k porovnávání
Po určení konkrétní front-endové hostitele a filtrování možných pravidla směrování pro trasy s front-endu, které jsou hostiteli, vyfiltruje branou pravidla směrování na základě cesty požadavku. Můžeme použít podobnou logiku jako hostitele front-endu:

1. Vyhledejte všechny pravidlo směrování s přesnou shodou v cestě
2. Pokud žádné cesty přesná shoda hledejte pravidla směrování se zástupným znakem cestu, která odpovídá
3. Pokud se žádná pravidla směrování nenajdou odpovídající cestou, zamítnutí žádosti a vrátí 400: Chybný požadavek došlo k chybě odpověď HTTP.

>[!NOTE]
> Všechny cesty bez zástupné znaky jsou považovány za přesnou shodu cesty. I v případě, že cesta končí lomítkem, přesto považuje přesná shoda.

Abychom vysvětlili, dále, Podívejme se na jinou sadu příklady:

| Pravidlo směrování | Hostitel front-endu    | `Path`     |
|-------|---------|----------|
| A     | www\.contoso.com | /        |
| B     | www\.contoso.com | /\*      |
| C     | www\.contoso.com | /ab      |
| D     | www\.contoso.com | /abc     |
| E     | www\.contoso.com | /abc/    |
| F     | www\.contoso.com | /abc/\*  |
| G     | www\.contoso.com | /abc/def |
| H     | www\.contoso.com | /Path/   |

Zadaný tuto konfiguraci, výsledkem bude v následující tabulce odpovídající příkladu:

| Příchozí požadavek    | Porovnávané trasy |
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
> </br> Pokud nejsou žádná pravidla směrování hostitele front-endu přesnou shodu s pokrývající vše směrovat cestu (`/*`), pak nebude existovat shoda pro jakékoli pravidlo směrování.
>
> Příklad konfigurace:
>
> | Trasa | Hostitel             | `Path`    |
> |-------|------------------|---------|
> | A     | profile.contoso.com | /api/\* |
>
> Odpovídající tabulka:
>
> | Příchozí požadavek       | Porovnávané trasy |
> |------------------------|---------------|
> | profile.domain.com/other | Žádné. Chyba 400: Chybný požadavek |

### <a name="routing-decision"></a>Rozhodnutí o směrování
Jakmile zjišťována shoda pro jedno pravidlo směrování branou, pak musíme zvolte, jak zpracovat požadavek. Pokud je pro odpovídající pravidlo směrování branou odpověď uložená v mezipaměti k dispozici pak stejné získá obsluhovat zpět do klienta. V opačném případě je dalším krokem, který získá ohodnocení se, jestli jste nakonfigurovali [přepisu adresy URL (vlastního předávajícího cesta)](front-door-url-rewrite.md) odpovídající směrování je pravidlo nebo ne. Pokud není k dispozici vlastního předávajícího cestu definovanou, získá požadavek předá do příslušného back-endu v nakonfigurované back-endový fond, protože je. V opačném cestu požadavku se aktualizuje podle [vlastního předávajícího cesta](front-door-url-rewrite.md) definovaných a potom přeposílají do back-endu.

## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
