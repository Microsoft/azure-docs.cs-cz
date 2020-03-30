---
title: Azure Front Door – přepis adresy URL | Dokumenty společnosti Microsoft
description: Tento článek vám pomůže pochopit, jak Azure Front Door provádí přepis adresy URL pro vaše trasy, pokud jsou nakonfigurované.
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
ms.openlocfilehash: 1e5bd565be7a1cabf08ddf33c65eb12b5294249f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471468"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Přepsání adresy URL (vlastní cesta pro předávání)
Azure Front Door podporuje přepsání adresy URL tím, že umožňuje nakonfigurovat volitelnou **vlastní cestu pro předávání,** která se použije při vytváření požadavku na předávání do back-endu. Pokud není zadaná žádná vlastní předávací cesta, ve výchozím nastavení služba Front Door zkopíruje příchozí cestu URL do adresy URL použité v přesměrovaném požadavku. Hlavička hostitele použitá v přesměrovaném požadavku odpovídá konfiguraci pro vybraný back-end. Přečtěte si [záhlaví back-endu hostitele,](front-door-backend-pool.md#hostheader) abyste zjistili, co dělá a jak ho můžete nakonfigurovat.

Výkonná část přepisování adres URL pomocí vlastní cesty pro předávání je, že zkopíruje libovolnou část příchozí cesty, která odpovídá zástupné cestě k předané cestě (tyto segmenty cesty jsou **zelené** segmenty v příkladu níže):
</br>
![Přepsání adresy URL předních dveří Azure][1]

## <a name="url-rewrite-example"></a>Příklad přepisu adresy URL
Zvažte pravidlo směrování s následujícími front-endovými hostiteli a nakonfigurovanými cestami:

| Hostitelé      | Cesty       |
|------------|-------------|
| www\.contoso.com | /\*         |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

První sloupec v následující tabulce ukazuje příklady příchozích požadavků a druhý sloupec ukazuje, co by bylo "nejvíce specifické" odpovídající trasu 'Cesta'.  Třetí a následující sloupce prvního řádku tabulky jsou příklady nakonfigurovaných **vlastních cest pro předávání**, přičemž ostatní řádky v těchto sloupcích představují příklady toho, co by byla cesta předávaného požadavku, pokud by byla spárována s požadavkem v tomto řádku.

Pokud například čteme přes druhý řádek, říká se, `www.contoso.com/sub`že pro příchozí požadavek `/`, pokud byla vlastní `/sub`cesta pro předávání , pak by byla předaná cesta . Pokud byla vlastní cesta `/fwd/`pro předávání , byla `/fwd/sub`by předaná cesta . A tak dále, pro zbývající sloupce. **Zvýrazněné** části cest níže představují části, které jsou součástí zástupné shody.


| Příchozí požadavek       | Cesta nejkonkrétnější shody | /          | /fwd/          | /foo/          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/**sub**     | /\*                      | /**Dílčí**   | /fwd/**dílčí**   | /foo/**sub**   | /foo/bar/**sub**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www\.contoso.com/foo         | /foo                     | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/**bar** | /foo/\*                  | /**Bar**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/**bar**   |


## <a name="optional-settings"></a>Volitelná nastavení
Existují další volitelná nastavení, která můžete také zadat pro libovolné nastavení pravidla směrování:

* **Konfigurace mezipaměti** – Pokud je zakázána nebo není zadána, pak požadavky, které odpovídají tomuto pravidlu směrování, se nepokusí použít obsah uložený v mezipaměti a místo toho se vždy načtou z back-endu. Přečtěte si více o [ukládání do mezipaměti pomocí předních dveří](front-door-caching.md).



## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg