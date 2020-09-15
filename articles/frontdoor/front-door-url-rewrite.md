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
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 8f4a6283f762d9792f50651b9caee17795df6d55
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89398933"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Přepsání adresy URL (vlastní předávací cesta)
Přední dvířka Azure podporují přepis adres URL tím, že vám umožní nakonfigurovat volitelnou **cestu pro přesměrování** , která se má použít při vytváření žádosti pro předání do back-endu. Pokud není zadaná žádná vlastní předávací cesta, ve výchozím nastavení služba Front Door zkopíruje příchozí cestu URL do adresy URL použité v přesměrovaném požadavku. Hlavička hostitele použitá v přesměrovaném požadavku odpovídá konfiguraci pro vybraný back-end. Přečtěte si [hlavičku back-end hostitele](front-door-backend-pool.md#hostheader) , kde se dozvíte, co dělá a jak ho můžete nakonfigurovat.

Výkonná součást přepsání adresy URL pomocí vlastního předávacího postupu spočívá v tom, že zkopíruje všechny části příchozí cesty, které odpovídají zástupným cestám, do přesměrované cesty (tyto segmenty cest jsou **zelenými** segmenty v následujícím příkladu):
</br>
![Přepsání adresy URL z předních dveří Azure][1]

## <a name="url-rewrite-example"></a>Příklad přepsání adresy URL
Vezměte v úvahu pravidlo směrování s nakonfigurovanými následujícími hostiteli a cestami front-endu:

| Hostitelé      | Cesty       |
|------------|-------------|
| Webová \. contoso.com | /\*         |
|            | /foo        |
|            | foo\*     |
|            | /foo/bar/\* |

První sloupec v tabulce ukazuje příklady příchozích požadavků a druhý sloupec zobrazuje, co by bylo "" nejlépe se "vyhovující" cesta ".  Třetím a dalším sloupcem prvního řádku tabulky jsou příklady konfigurovaných **vlastních cest předávání**, ve kterých zbylé řádky v těchto sloupcích představují příklady toho, co by byla přesměrovaná cesta požadavku shodná s požadavkem v tomto řádku.

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

* **Konfigurace mezipaměti** – Pokud je zakázaná nebo není zadaná, pak se požadavky odpovídající tomuto pravidlu směrování nebudou pokoušet použít obsah uložený v mezipaměti a místo toho se načte z back-endu. Přečtěte si další informace o [ukládání do mezipaměti s předními dvířky](front-door-caching.md).



## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg