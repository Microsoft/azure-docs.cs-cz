---
title: Azure přední dveře Service – přepsání adresy URL | Dokumentace Microsoftu
description: Tento článek vám pomůže pochopit Azure branou služby jak přepisování adres URL pro trasy, pokud nakonfigurované.
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
ms.openlocfilehash: dc2126276e3e8e0d35ce8ed1f835544386659eff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736173"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Přepsání adresy URL (vlastního předávajícího cesta)
Služba Azure branou podporuje přepsání adresy URL, neboť umožňuje nakonfigurovat volitelnou **předávání cestu k vlastním** používat při vytváření požadavku předat back-endu. Pokud není zadaná žádná vlastní předávací cesta, ve výchozím nastavení služba Front Door zkopíruje příchozí cestu URL do adresy URL použité v přesměrovaném požadavku. Hlavička hostitele použitá v přesměrovaném požadavku odpovídá konfiguraci pro vybraný back-end. Čtení [hlavičku hostitele back-endu](front-door-backend-pool.md#hostheader) se dozvíte, co to dělá a jak ji můžou nakonfigurovat.

Důležitou částí přepsání adresy URL pomocí vlastního předávajícího cesty je, že budou zkopírovány všechny části příchozí cestě, která odpovídá cesta se zástupným znakem na předané cesty (tyto segmenty cesty, které jsou **zelené** segmenty v následujícím příkladu):
</br>
![Přepsání adresy URL branou Azure][1]

## <a name="url-rewrite-example"></a>Příklad přepisování adres URL
Vezměte v úvahu pravidla směrování s následující hostitele front-endu a nakonfigurovat cesty:

| Hostitelé      | Cesty       |
|------------|-------------|
| www\.contoso.com | /\*         |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

První sloupec v tabulce níže jsou uvedeny příklady příchozí požadavky a druhý sloupec zobrazuje, co by být odpovídající trasy "specifické pro většinu" 'Path'.  Třetí a následné sloupce prvního řádku v tabulce jsou příklady nakonfigurované **vlastní předávání cesty**, se zbytkem řádků v těchto sloupců představujícího příklady co předaný požadavek cesta bude-li odpovídat požadavek na daném řádku.

Například, pokud jsme čtení ve druhém řádku, to je chci říct, že pro příchozí požadavek `www.contoso.com/sub`, pokud byla cesta vlastního předávajícího `/`, pak Přesměrovaná cesta bude `/sub`. Pokud byla cesta vlastního předávajícího `/fwd/`, pak Přesměrovaná cesta bude `/fwd/sub`. A tak dále, u zbývajících sloupců. **Oznámil** částí z následujících možností představují části, které jsou součástí shody zástupný znak.


| Příchozí požadavek       | Většina konkrétní shodu cesty | /          | /fwd/          | /foo/          | /foo/řádek /          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /fwd/          | /foo/          | /foo/řádek /          |
| www\.contoso.com/**sub**     | /\*                      | /**sub**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www\.contoso.com/foo         | /foo                     | /          | /fwd/          | /foo/          | /foo/řádek /          |
| www\.contoso.com/foo/        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/řádek /          |
| www\.contoso.com/foo/**bar** | /foo/\*                  | /**bar**   | /FWD/**panelu**   | /foo/**panelu**   | /foo/řádek/**panelu**   |


## <a name="optional-settings"></a>Volitelná nastavení
Existují další volitelné nastavení, můžete také zadat pro jakékoli dané nastavení pravidla směrování:

* **Konfigurace mezipaměti** – Pokud je zakázaná nebo není určeno, požadavky, které odpovídají tohoto pravidla směrování nebude pokoušet použít obsah uložený v mezipaměti a místo toho bude vždy načíst z back-endu. Další informace o [ukládání do mezipaměti s branou](front-door-caching.md).



## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg