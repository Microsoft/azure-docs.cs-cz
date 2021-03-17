---
title: Přední dvířka Azure – pravidlo směrování, které odpovídá monitorování | Microsoft Docs
description: Tento článek vám pomůže porozumět tomu, jak se na nabízených platformách Azure shoduje pravidlo směrování, které se má použít pro příchozí požadavek.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 67940db973f494cd4a12c2f16db528e0b113d656
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449215"
---
# <a name="how-requests-are-matched-to-a-routing-rule"></a>Jak odpovídají požadavky na pravidlo směrování

Po navázání připojení a dokončení metody handshake TLS platí, že když žádost vstoupí do prostředí front-dveří, jedna z prvních věcí, na které se nachází přední dveře, určí, které konkrétní pravidlo směrování bude odpovídat dané žádosti, a pak provede definovanou akci v konfiguraci. Následující dokument vysvětluje, jak přední dveře určují konfiguraci směrování, která se má použít při zpracování požadavku HTTP.

## <a name="structure-of-a-front-door-route-configuration"></a>Struktura konfigurace směrování na front-dveří
Konfigurace pravidla směrování front-dveří se skládá ze dvou hlavních částí: "levá strana" a "pravá strana". Příchozí požadavek odpovídá na levou stranu trasy, zatímco pravá strana definuje, jak zpracováváme požadavek.

### <a name="incoming-match-left-hand-side"></a>Příchozí shoda (levá strana)
Následující vlastnosti určují, zda příchozí požadavek odpovídá pravidlu směrování (nebo k levé straně):

* **Protokoly HTTP** (http/https)
* **Hostitelé** (například www \. foo.com, \* . bar.com)
* **Cesty** (například/ \* ,/users/ \* ,/file.gif)

Tyto vlastnosti jsou vnitřně rozbalené, takže každá kombinace protokolu/hostitele/cesty je možnou sadou shod.

### <a name="route-data-right-hand-side"></a>Směrování dat (pravá strana)
Rozhodnutí o tom, jak žádost zpracovat, závisí na tom, jestli je pro konkrétní trasu povolené ukládání do mezipaměti nebo ne. Takže pokud nemáme odpověď na žádost uloženou v mezipaměti, pošleme požadavek do příslušného back-endu v nakonfigurovaném fondu back-endu.

## <a name="route-matching"></a>Shoda trasy
Tato část se zaměřuje na to, jak se shoduje s daným pravidlem směrování front-dveří. Základním konceptem je, že se vždy shoduje s **nejvíce specifickou shodou** , a to nejdřív, co se díváte jenom na levou stranu.  Nejdřív se porovnáváme na základě protokolu HTTP, potom hostitele s front-endu a pak cestou.

### <a name="frontend-host-matching"></a>Porovnání hostitele front-endu
Při shodě hostitelů front-endu používáme logiku definovanou níže:

1. Vyhledejte jakékoli směrování s přesnou shodou na hostiteli.
2. Pokud se neshodují žádné přesné hostitele front-end, zamítnout žádost a pošle 400 chybnou žádost o chybu.

Chcete-li tento proces dále vysvětlit, Podívejme se na příklad konfigurace tras front-dveří (jenom na levé straně):

| Pravidlo směrování | Hostitelé front-endu | Cesta |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| B | foo.contoso.com | /Users/\* |
| C | Webová \. fabrikam.com, foo.Adventure-Works.com  | /\*, /images/\* |

Pokud se následující příchozí požadavky poslaly do front dveří, odpovídají jim následující pravidla směrování:

| Příchozí hostitel front-endu | Shodná pravidla směrování |
|---------------------|---------------|
| foo.contoso.com | A, B |
| Webová \. fabrikam.com | C |
| images.fabrikam.com | Chyba 400: Chybný požadavek |
| foo.adventure-works.com | C |
| contoso.com | Chyba 400: Chybný požadavek |
| Webová \. Adventure-Works.com | Chyba 400: Chybný požadavek |
| Webová \. northwindtraders.com | Chyba 400: Chybný požadavek |

### <a name="path-matching"></a>Shoda cest
Po určení konkrétního hostitele front-endu a filtrování možných pravidel směrování jenom na trasy s tímto hostitelem front-endu pak vyfiltruje pravidla směrování na základě cesty požadavku. Podobnou logiku používáme jako hostitele front-endu:

1. Vyhledejte v cestě jakékoli pravidlo směrování s přesnou shodou.
2. Pokud cesta přesně neodpovídá, hledejte pravidla směrování se zástupnými znaky, která odpovídá
3. Pokud se nenaleznou žádná pravidla směrování s porovnávací cestou, zamítnout žádost a vrátí 400: Chybná odpověď HTTP.

>[!NOTE]
> Jakékoli cesty bez zástupných znaků se považují za cesty, které se přesně shodují. I v případě, že cesta končí lomítkem, je stále považována za přesnou shodu.

Pokud se chcete podrobněji vysvětlit, Podívejme se na jinou sadu příkladů:

| Pravidlo směrování | Hostitel s front-endu    | Cesta     |
|-------|---------|----------|
| A     | Webová \. contoso.com | /        |
| B     | Webová \. contoso.com | /\*      |
| C     | Webová \. contoso.com | /ab      |
| D     | Webová \. contoso.com | /abc     |
| E     | Webová \. contoso.com | kódem    |
| F     | Webová \. contoso.com | kódem\*  |
| G     | Webová \. contoso.com | /abc/def |
| H     | Webová \. contoso.com | /Path   |

Tato konfigurace má za následek následující příklad odpovídající tabulce:

| Příchozí žádost    | Odpovídající trasa |
|---------------------|---------------|
| Webová \. contoso.com/            | A             |
| Webová \. contoso.com/a           | B             |
| Webová \. contoso.com/AB          | C             |
| Webová \. contoso.com/ABC         | D             |
| Webová \. contoso.com/abzzz       | B             |
| Webová \. contoso.com/ABC/        | E             |
| Webová \. contoso.com/ABC/d       | F             |
| Webová \. contoso.com/abc/def     | G             |
| Webová \. contoso.com/ABC/defzzz  | F             |
| Webová \. contoso.com/abc/def/GHI | F             |
| Webová \. contoso.com/Path        | B             |
| Webová \. contoso.com/Path/       | H             |
| Webová \. contoso.com/Path/ZZZ    | B             |

>[!WARNING]
> </br> Pokud nejsou k dispozici žádná pravidla směrování pro konkrétního hostitele front-endu s cestou k trase ( `/*` ), pak se neshoduje s žádným pravidlem směrování.
>
> Příklad konfigurace:
>
> | Trasa | Hostitel             | Cesta    |
> |-------|------------------|---------|
> | A     | profile.contoso.com | /API\* |
>
> Shodná tabulka:
>
> | Příchozí žádost       | Odpovídající trasa |
> |------------------------|---------------|
> | profile.domain.com/other | Žádné Chyba 400: Chybný požadavek |

### <a name="routing-decision"></a>Rozhodnutí o směrování
Až budeme odpovídat na jedno pravidlo směrování na front-dveří, musíme zvolit způsob zpracování žádosti. Pokud je pro pravidlo párování shodné, má přední dveře k dispozici odpověď uloženou v mezipaměti, takže se stejná vrátí klientovi. V opačném případě bude vyhodnocená další věc, jestli jste pro odpovídající pravidlo směrování nakonfigurovali [přepsání adresy URL (vlastní cesta pro předávání)](front-door-url-rewrite.md) , nebo ne. Pokud není definovaná vlastní cesta pro přesměrování, pak se požadavek přesměruje do příslušného back-endu v nakonfigurovaném back-end fondu tak, jak je. V opačném případě se cesta požadavku aktualizuje podle definovaného [vlastního předávacího umístění](front-door-url-rewrite.md) a pak vpřed do back-endu.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
