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
ms.openlocfilehash: 00fe3aa7a641b9d07aad90a9d008a99efc6e9d97
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993461"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Přepsání adresy URL (vlastního předávajícího cesta)
Služba Azure branou podporuje přepsání adresy URL, neboť umožňuje nakonfigurovat volitelnou **předávání cestu k vlastním** používat při vytváření požadavku předat back-endu. Ve výchozím nastavení pokud je k dispozici žádná cesta vlastního předávajícího, pak branou zkopíruje příchozí cestě adresy URL na adresu URL používaných pro předaný požadavek. Hlavička hostitele použít v předaný požadavek je nakonfigurované pro vybraný back-endu. Čtení [hlavičku hostitele back-endu](front-door-backend-pool.md#hostheader) se dozvíte, co to dělá a jak ji můžou nakonfigurovat.

Důležitou částí přepsání adresy URL pomocí vlastního předávajícího cesty je, že budou zkopírovány všechny části příchozí cestě, která odpovídá cesta se zástupným znakem na předané cesty (tyto segmenty cesty, které jsou **zelené** segmenty v následujícím příkladu):
</br>
![Přepsání adresy URL branou Azure][1]

## <a name="url-rewrite-example"></a>Příklad přepisování adres URL
Vezměte v úvahu pravidla směrování s následující hostitele front-endu a nakonfigurovat cesty:

| Hostitelé      | Cesty       |
|------------|-------------|
| www.contoso.com | /\*         |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/řádek /\* |

První sloupec v tabulce níže jsou uvedeny příklady příchozí požadavky a druhý sloupec zobrazuje, co by být odpovídající trasy "specifické pro většinu" 'Path'.  Třetí a následné sloupce prvního řádku v tabulce jsou příklady nakonfigurované **vlastní předávání cesty**, se zbytkem řádků v těchto sloupců představujícího příklady co předaný požadavek cesta bude-li odpovídat požadavek na daném řádku.

Například, pokud jsme čtení ve druhém řádku, to je chci říct, že pro příchozí požadavek `www.contoso.com/sub`, pokud byla cesta vlastního předávajícího `/`, pak Přesměrovaná cesta bude `/sub`. Pokud byla cesta vlastního předávajícího `/fwd/`, pak Přesměrovaná cesta bude `/fwd/sub`. A tak dále, u zbývajících sloupců. **Oznámil** částí z následujících možností představují části, které jsou součástí shody zástupný znak.


| Příchozí požadavek       | Většina konkrétní shodu cesty | /          | /FWD/          | /foo/          | /foo/řádek /          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www.contoso.com/.pozn            | /\*                      | /          | /FWD/          | /foo/          | /foo/řádek /          |
| www.contoso.com/.pozn**sub**     | /\*                      | /**Sub**   | /FWD/**sub**   | /foo/**sub**   | /foo/řádek/**sub**   |
| www.contoso.com/.pozn **/ b a c.**   | /\*                      | /**/ b a c.** | /FWD/**/ b a c.** | /foo/**/ b a c.** | /foo/řádek/**/ b a c.** |
| www.contoso.com/foo         | /foo                     | /          | /FWD/          | /foo/          | /foo/řádek /          |
| www.contoso.com/foo/        | /foo/\*                  | /          | /FWD/          | /foo/          | /foo/řádek /          |
| www.contoso.com/foo/**panelu** | /foo/\*                  | /**Panel**   | /FWD/**panelu**   | /foo/**panelu**   | /foo/řádek/**panelu**   |


## <a name="optional-settings"></a>Volitelná nastavení
Existují další volitelné nastavení, můžete také zadat pro jakékoli dané nastavení pravidla směrování:

* **Konfigurace mezipaměti** – Pokud je zakázaná nebo není určeno, požadavky, které odpovídají tohoto pravidla směrování nebude pokoušet použít obsah uložený v mezipaměti a místo toho bude vždy načíst z back-endu. Další informace o [ukládání do mezipaměti s branou](front-door-caching.md).



## <a name="next-steps"></a>Další postup

- Zjistěte, jak [vytvořit přední dveře](quickstart-create-front-door.md).
- Přečtěte si [fungování branou](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg