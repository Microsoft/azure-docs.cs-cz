---
title: Přední dvířka Azure – přepsání adresy URL | Microsoft Docs
description: Tento článek vám pomůže porozumět tomu, jak přední dvířka Azure Přepisuje adresu URL pro vaše trasy, pokud jsou nakonfigurované.
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
ms.openlocfilehash: eb5b4ab8a23a374aec54d65dd5390ab3fec3e905
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91445490"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Přepsání adresy URL (vlastní předávací cesta)
Přední dvířka Azure podporují přepis adres URL tím, že nakonfigurují volitelnou **vlastní cestu pro přesměrování** , která se má použít při vytváření žádosti, která se předává do back-endu. Ve výchozím nastavení platí, že pokud není k dispozici vlastní cesta pro přesměrování, pak se na front-dveří zkopíruje adresa URL příchozí adresy URL použité v přesměrovaném požadavku. Hlavička hostitele použitá v přesměrovaném požadavku odpovídá konfiguraci pro vybraný back-end. Přečtěte si [hlavičku back-end hostitele](front-door-backend-pool.md#hostheader) , kde se dozvíte, co dělá a jak ho můžete nakonfigurovat.

Výkonná součást přepsání adresy URL je taková, že vlastní cesta pro přesměrování zkopíruje všechny součásti příchozí cesty, které odpovídají zástupným cestám, do přesměrované cesty (tyto segmenty cesty jsou **zelenými** segmenty v příkladu níže):
</br>

:::image type="content" source="./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg" alt-text="Přepsání adresy URL z předních dveří Azure":::

## <a name="url-rewrite-example"></a>Příklad přepsání adresy URL
Vezměte v úvahu pravidlo směrování s následující kombinací hostitelů front-end a nakonfigurovaných cest:

| Hostitelé      | Cesty       |
|------------|-------------|
| Webová \. contoso.com | /\*   |
|            | /foo        |
|            | foo\*     |
|            | /foo/bar/\* |

První sloupec v tabulce ukazuje příklady příchozích požadavků a druhý sloupec zobrazuje, co by bylo "" nejlépe se "vyhovující" cesta ".  Třetím a následným sloupcem tabulky jsou příklady konfigurovaných **vlastních cest přesměrování**.

Pokud je například čteno v druhém řádku, znamená to, že pro příchozí požadavek se v `www.contoso.com/sub` případě, že byla vytvořena vlastní cesta přesměrování `/` , přesměrovaná cesta bude `/sub` . Pokud byla cesta pro vlastní přesměrování `/fwd/` , přesměrovaná cesta bude `/fwd/sub` . A tak dále pro zbývající sloupce. **Zvýrazněné** části níže uvedených cest představují části, které jsou součástí porovnávání se zástupnými znaky.

| Příchozí žádost       | Konkrétní cesta shody | /          | /fwd/          | foo          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| Webová \. contoso.com/            | /\*                      | /          | /fwd/          | foo          | /foo/bar/          |
| Webová \. contoso.com/**Sub**     | /\*                      | /**jednotk**   | /FWD/**Sub**   | /foo/**Sub**   | /foo/bar/**Sub**   |
| Webová \. contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /FWD/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| Webová \. contoso.com/foo         | /foo                     | /          | /fwd/          | foo          | /foo/bar/          |
| Webová \. contoso.com/foo/        | foo\*                  | /          | /fwd/          | foo          | /foo/bar/          |
| \.contoso.com/foo/**panel** www | foo\*                  | /**příčk**   | **panel** /FWD/   | **panel** /foo/   | **panel** /foo/bar/   |

## <a name="optional-settings"></a>Volitelná nastavení
K dispozici jsou další volitelná nastavení, která můžete zadat také pro všechna zadaná nastavení pravidla směrování:

* **Konfigurace mezipaměti** – Pokud je zakázaná nebo není zadaná, požadavky, které se shodují s tímto pravidlem směrování, se nebudou pokoušet použít obsah uložený v mezipaměti a místo toho se načítají z back-endu. Přečtěte si další informace o [ukládání do mezipaměti s předními dvířky](front-door-caching.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
