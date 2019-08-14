---
title: Služba front-dveří Azure – přepsání adresy URL | Microsoft Docs
description: Tento článek vám pomůže porozumět tomu, jak služba front-end v Azure dělá pro vaše trasy přepsání adresy URL, pokud jsou nakonfigurované.
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
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "60736173"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Přepsání adresy URL (vlastní cesta přesměrování)
Služba front-endu Azure podporuje přepsání adresy URL tím, že vám umožní nakonfigurovat volitelnou **cestu pro přesměrování** , která se má použít při vytváření žádosti pro předání do back-endu. Pokud není zadaná žádná vlastní předávací cesta, ve výchozím nastavení služba Front Door zkopíruje příchozí cestu URL do adresy URL použité v přesměrovaném požadavku. Hlavička hostitele použitá v přesměrovaném požadavku odpovídá konfiguraci pro vybraný back-end. Přečtěte si [hlavičku back-end hostitele](front-door-backend-pool.md#hostheader) , kde se dozvíte, co dělá a jak ho můžete nakonfigurovat.

Výkonná součást přepsání adresy URL pomocí vlastního předávacího postupu spočívá v tom, že zkopíruje všechny části příchozí cesty, které odpovídají zástupným cestám, do přesměrované cesty (tyto segmenty cest jsou zelenými segmenty v následujícím příkladu):
</br>
![Přepsání adresy URL z předních dveří Azure][1]

## <a name="url-rewrite-example"></a>Příklad přepsání adresy URL
Vezměte v úvahu pravidlo směrování s nakonfigurovanými následujícími hostiteli a cestami front-endu:

| Hostitelé      | Cesty       |
|------------|-------------|
| www\.contoso.com | /\*         |
|            | /foo        |
|            | foo\*     |
|            | /foo/bar/\* |

První sloupec v tabulce ukazuje příklady příchozích požadavků a druhý sloupec zobrazuje, co by bylo "" nejlépe se "vyhovující" cesta ".  Třetím a dalším sloupcem prvního řádku tabulky jsou příklady konfigurovaných **vlastních cest předávání**, ve kterých zbylé řádky v těchto sloupcích představují příklady toho, co by odpovídalo cestě přesměrovaného požadavku, pokud odpovídají žádosti v této aplikaci. řadě.

Pokud je například čteno v druhém řádku, znamená to, že pro příchozí požadavek `www.contoso.com/sub`se v případě, že byla vytvořena `/`vlastní cesta přesměrování, přesměrovaná cesta bude `/sub`. Pokud byla `/fwd/`cesta pro vlastní přesměrování, přesměrovaná cesta bude `/fwd/sub`. A tak dále pro zbývající sloupce. **Zvýrazněné** části níže uvedených cest představují části, které jsou součástí porovnávání se zástupnými znaky.


| Příchozí žádost       | Konkrétní cesta shody | /          | /fwd/          | foo          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /fwd/          | foo          | /foo/bar/          |
| www\.contoso.com/**sub**     | /\*                      | /**jednotk**   | /FWD/**Sub**   | /foo/**Sub**   | /foo/bar/**Sub**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www\.contoso.com/foo         | /foo                     | /          | /fwd/          | foo          | /foo/bar/          |
| Webová\.contoso.com/foo/        | foo\*                  | /          | /fwd/          | foo          | /foo/bar/          |
| www\.contoso.com/foo/**bar** | foo\*                  | /**příčk**   | **panel** /FWD/   | **panel** /foo/   | **panel** /foo/bar/   |


## <a name="optional-settings"></a>Volitelná nastavení
K dispozici jsou další volitelná nastavení, která můžete zadat také pro všechna zadaná nastavení pravidla směrování:

* **Konfigurace mezipaměti** – Pokud je zakázaná nebo není zadaná, pak se požadavky odpovídající tomuto pravidlu směrování nebudou pokoušet použít obsah uložený v mezipaměti a místo toho se načte z back-endu. Přečtěte si další informace o [ukládání do mezipaměti s předními dvířky](front-door-caching.md).



## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg