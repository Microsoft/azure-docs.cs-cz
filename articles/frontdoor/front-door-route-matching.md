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
ms.openlocfilehash: 23582215654ff2d5003fe611c7149ad760d72bc5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957036"
---
# <a name="how-front-door-matches-requests-to-a-routing-rule"></a>Jak branou odpovídá požadavků na pravidlo směrování

Po navázání připojení a udělat ověření typu handshake SSL, když jsou požadavek na prostředí s branou, jeden z prvních věcí, které nemá branou je určení ze všech konfigurací, které konkrétní pravidlo směrování tak, aby odpovídaly požadavku a pak můžete definované akce. Následující dokument vysvětluje, jak přední dveře Určuje konfiguraci směrování, kterou chcete použít při zpracování požadavku HTTP.

## <a name="structure-of-a-front-door-route-configuration"></a>Struktura konfiguraci směrování branou
Přední dveře konfigurace pravidla směrování se skládá ze dvou hlavních částí: "levá strana" a "pravá strana". Příchozí požadavek na levé straně trasy porovnávat, zatímco na pravé straně definuje, jak jsme zpracování žádosti.

### <a name="incoming-match-left-hand-side"></a>Příchozí shodu (levá strana)
Následující vlastnosti určují, zda příchozí žádost odpovídá pravidlo směrování (nebo levá strana):

* **Protokoly HTTP** (HTTP/HTTPS)
* **Hostitelé** (například www.foo.com \*. bar.com)
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

| Pravidlo směrování | Hostitele front-endu | Cesta |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| B | foo.contoso.com | složku /Users/\* |
| C | www.Fabrikam.com, foo.adventure works.com  | /\*, /images/\* |

Pokud tyto příchozí požadavky byly branou, by porovnání následující pravidla směrování výše:

| Příchozí hostitele front-endu | Odpovídající pravidla směrování |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www.Fabrikam.com | C |
| Images.Fabrikam.com | 400. Chyba: Chybný požadavek |
| foo.adventure works.com | C |
| contoso.com | 400. Chyba: Chybný požadavek |
| www.Adventure-Works.com | 400. Chyba: Chybný požadavek |
| www.northwindtraders.com | 400. Chyba: Chybný požadavek |

### <a name="path-matching"></a>Cesta k porovnávání
Po určení konkrétní front-endové hostitele a filtrování možných pravidla směrování pro trasy s front-endu, které jsou hostiteli, vyfiltruje branou pravidla směrování na základě cesty požadavku. Můžeme použít podobnou logiku jako hostitele front-endu:

1. Vyhledejte všechny pravidlo směrování s přesnou shodou v cestě
2. Pokud žádné cesty přesná shoda hledejte pravidla směrování se zástupným znakem cestu, která odpovídá
3. Pokud se žádná pravidla směrování nenajdou odpovídající cestou, zamítnutí žádosti a vrátí 400: Chyba chybná žádost odpověď HTTP.

>[!NOTE]
> Všechny cesty bez zástupné znaky jsou považovány za přesnou shodu cesty. I v případě, že cesta končí lomítkem, přesto považuje přesná shoda.

Abychom vysvětlili, dále, Podívejme se na jinou sadu příklady:

| Pravidlo směrování | Hostitele front-endu    | Cesta     |
|-------|---------|----------|
| A     | www.contoso.com | /        |
| B     | www.contoso.com | /\*      |
| C     | www.contoso.com | / AB      |
| D     | www.contoso.com | /ABC     |
| E     | www.contoso.com | /ABC/    |
| F     | www.contoso.com | /ABC/\*  |
| G     | www.contoso.com | / abc/def |
| H     | www.contoso.com | /Path/   |

Zadaný tuto konfiguraci, výsledkem bude v následující tabulce odpovídající příkladu:

| Příchozí požadavek    | Porovnávané trasy |
|---------------------|---------------|
| www.contoso.com/.pozn            | A             |
| www.contoso.com/a           | B             |
| www.contoso.com/AB          | C             |
| www.contoso.com/ABC         | D             |
| www.contoso.com/abzzz       | B             |
| www.contoso.com/ABC/        | E             |
| www.contoso.com/ABC/d       | F             |
| www.contoso.com/ABC/DEF     | G             |
| www.contoso.com/ABC/defzzz  | F             |
| www.contoso.com/ABC/DEF/GHI | F             |
| www.contoso.com/Path        | B             |
| www.contoso.com/Path/       | H             |
| www.contoso.com/Path/zzz    | B             |

>[!WARNING]
> </br> Pokud nejsou žádná pravidla směrování hostitele front-endu přesnou shodu s pokrývající vše směrovat cestu (`/*`), pak nebude existovat shoda pro jakékoli pravidlo směrování.
>
> Příklad konfigurace:
>
> | Trasa | Hostitel             | Cesta    |
> |-------|------------------|---------|
> | A     | Profile.contoso.com | /API/\* |
>
> Odpovídající tabulka:
>
> | Příchozí požadavek       | Porovnávané trasy |
> |------------------------|---------------|
> | Profile.domain.com/Other | Žádné. 400. Chyba: Chybný požadavek |

### <a name="routing-decision"></a>Rozhodnutí o směrování
Jakmile zjišťována shoda pro jedno pravidlo směrování branou, pak musíme zvolte, jak zpracovat požadavek. Pokud je pro odpovídající pravidlo směrování branou odpověď uložená v mezipaměti k dispozici pak stejné získá obsluhovat zpět do klienta. V opačném případě je dalším krokem, který získá ohodnocení se, jestli jste nakonfigurovali [přepisu adresy URL (vlastního předávajícího cesta)](front-door-url-rewrite.md) odpovídající směrování je pravidlo nebo ne. Pokud není k dispozici vlastního předávajícího cestu definovanou, získá požadavek předá do příslušného back-endu v nakonfigurované back-endový fond, protože je. V opačném cestu požadavku se aktualizuje podle [vlastního předávajícího cesta](front-door-url-rewrite.md) definovaných a potom přeposílají do back-endu.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [vytvořit přední dveře](quickstart-create-front-door.md).
- Přečtěte si [fungování branou](front-door-routing-architecture.md).
