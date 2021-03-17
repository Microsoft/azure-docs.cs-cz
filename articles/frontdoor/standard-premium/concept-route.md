---
title: Co je postup Azure front-end Standard/Premium?
description: Tento článek vám pomůže pochopit, jakým způsobem se v Azure front-Premium Standard a Premium shodují s pravidlem směrování, které se má použít pro příchozí požadavek.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: db026c4903aa30a0a4c8154af8ad6eeb4b72b706
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099197"
---
# <a name="what-is-azure-front-door-standardpremium-preview-route"></a>Co je trasa Azure front-end Standard/Premium (Preview)?

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

Trasa na úrovni Standard/Premium pro Azure definuje, jak se provoz zpracovává, když příchozí požadavek dorazí do prostředí Azure front-dveří. Prostřednictvím nastavení trasy je přidružení definováno mezi doménou a skupinou původních back-endu. Když zapnete funkce pro průběžné zpracování, jako je například vzor pro sestavování, sada pravidel, podrobnější kontrolu nad provozem je možné dosáhnout.

Konfigurace směrování front-Standard/Premium se skládá ze dvou hlavních částí: "levá strana" a "pravá strana". Porovnáváme příchozí požadavek na levou stranu trasy a pravá strana definuje, jak zpracováváme požadavek.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="incoming-match-left-hand-side"></a>Příchozí shoda (levá strana)

Následující vlastnosti určují, zda příchozí požadavek odpovídá pravidlu směrování (nebo k levé straně):

* **Protokoly HTTP** (http/https)
* **Hostitelé** (například www \. foo.com, \* . bar.com)
* **Cesty** (například/ \* ,/users/ \* ,/file.gif)

Tyto vlastnosti jsou vnitřně rozbalené, takže každá kombinace protokolu/hostitele/cesty je možnou sadou shod.

### <a name="route-data-right-hand-side"></a>Směrování dat (pravá strana)

Rozhodnutí o tom, jak žádost zpracovat, závisí na tom, jestli je pro trasu povolené ukládání do mezipaměti nebo ne. Pokud odpověď uložená v mezipaměti není k dispozici, je žádost předána příslušnému back-endu.

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

Po frontě Azure na úrovni Standard/Premium zjistí konkrétního hostitele front-end a vyfiltruje možná pravidla směrování jenom na trasy s tímto hostitelem front-endu. Přední dveře pak filtruje pravidla směrování na základě cesty požadavku. Podobnou logiku používáme jako hostitele front-endu:

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

Jakmile se služba Azure front-end Standard/Premium shoduje s jedním pravidlem směrování, musí si vybrat, jak se má žádost zpracovat. Pokud má služba Azure front-end Standard/Premium k dispozici odpověď uloženou v mezipaměti pro odpovídající pravidlo směrování, požadavek se vrátí klientovi. Další věc Azure na úrovni Standard/Premium se vyhodnocuje bez ohledu na to, jestli máte pro odpovídající pravidlo směrování nastavené pravidlo. Pokud není definovaná sada pravidel, pak se požadavek přepošle do back-endu fondu, jak je. V opačném případě se sada pravidel spustí v pořadí, v jakém jsou nakonfigurována.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [vytvořit frontu Standard/Premium](create-front-door-portal.md).
