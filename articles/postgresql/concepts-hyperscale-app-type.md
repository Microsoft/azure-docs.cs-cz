---
title: Určení typu aplikace – Citus (Velká měřítko) – Azure Database for PostgreSQL
description: Určení aplikace pro efektivní modelování distribuovaných dat
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 92333857177d33307d6997bfcbdf79787d3ab127
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90895958"
---
# <a name="determining-application-type"></a>Určení typu aplikace

Spouštění efektivních dotazů na skupině serverů Citus () vyžaduje, aby byly tabulky správně distribuovány mezi servery. Doporučená distribuce se liší podle typu aplikace a jejích vzorů dotazů.

Existují široké dva druhy aplikací, které dobře fungují na Citus (s velkou škálou). Prvním krokem při modelování dat je určení, které z nich je lépe podobá vaší aplikaci.

## <a name="at-a-glance"></a>Na první pohled

| Víceklientské aplikace                                 | Aplikace v reálném čase                                |
|-----------------------------------------------------------|-------------------------------------------------------|
| V některých případech schéma zahrnuje desítky nebo stovky tabulek          | Malý počet tabulek                                |
| Dotazy vztahující se k jednomu klientovi (společnosti/úložiště) v čase | Relativně jednoduché analytické dotazy s agregacemi |
| Úlohy OLTP pro obsluhu webových klientů                    | Velké množství přijímaných převážně neměnných dat           |
| Úlohy OLAP obsluhující analytické dotazy pro jednotlivé klienty   | Úlohy se často týkají velkých tabulek událostí            |

## <a name="examples-and-characteristics"></a>Příklady a charakteristiky

**Víceklientské aplikace**

> Obvykle se jedná o aplikace SaaS, které slouží ostatním společnostem, účtům nebo organizacím. Většina SaaSch aplikací je v podstatě relační. Mají přirozený rozměr pro distribuci dat mezi uzly: stačí horizontálních oddílů podle \_ ID tenanta.
>
> Citus () umožňuje horizontální navýšení kapacity databáze na miliony tenantů, aniž by bylo nutné aplikaci znovu architektovat. Můžete zachovat relační sémantiku, kterou potřebujete, jako jsou spojení, omezení cizího klíče, transakce, kyselost a konzistence.
>
> -   **Příklady**: websites, které hostují úložiště proti ostatním firmám, jako je digitální marketingové řešení nebo nástroj pro automatizaci prodeje.
> -   **Charakteristiky**: dotazy týkající se jednoho tenanta nepřipojují informace mezi klienty. To zahrnuje OLTP úlohy pro obsluhu webových klientů a úlohy OLAP, které slouží k analytickým dotazům pro jednotlivé klienty. Existence desítkových nebo stovek tabulek ve schématu databáze je také indikátorem pro více tenantů datových modelů.
>
> Škálování aplikace s více klienty pomocí Citus (s měřítkem) také vyžaduje minimální změny kódu aplikace. Podporujeme oblíbená rozhraní, jako jsou Ruby na železnici a Django.

**Analýzy v reálném čase**

> Aplikace potřebují obrovské paralelismus a koordinují stovky jader pro rychlé výsledky na číselné, statistické nebo počítání dotazů.  Horizontálního dělení a virtuálního dotazy SQL na více uzlech umožňují škálování (Citus) provádět dotazy v reálném čase napříč miliardami záznamů v rámci druhé.
>
> Tabulky v datových modelech analýzy v reálném čase jsou obvykle distribuované pomocí sloupců \_ , jako je ID uživatele, \_ ID hostitele nebo \_ ID zařízení.
>
> -   **Příklady**: řídicí panely pro analytiky orientované na zákazníky vyžadující doby odezvy v sekundách.
> -   **Charakteristiky**: několik tabulek, často se centrují kolem velké tabulky zařízení-, webu nebo uživatelů a vyžadují velký objem příjmu hlavně neproměnlivých dat. Poměrně jednoduché (ale výpočetně náročné) analytické dotazy zahrnující několik agregací a BYs skupin.

Pokud se situace podobá výše uvedeným případům, je dalším krokem rozhodování o tom, jak horizontálních oddílů data ve skupině serverů. \'Volba distribučních sloupců pro správce databáze musí odpovídat vzorům přístupu typických dotazů, aby se zajistil výkon.

## <a name="next-steps"></a>Další kroky

* [Volba distribučního sloupce](concepts-hyperscale-choose-distribution-column.md) pro tabulky v aplikaci pro efektivní distribuci dat
