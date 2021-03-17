---
title: Katalog velkých objemů dat v Azure Data Catalog
description: Postup zvýrazňování vzorů pro použití Azure Data Catalog se zdroji dat s velkými objemy dat, včetně Azure Blob Storage, Azure Data Lake a Hadoop HDFS.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: e79e5e16b300fd02b9c9124f9677747834f22813
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009428"
---
# <a name="how-to-catalog-big-data-in-azure-data-catalog"></a>Katalog velkých objemů dat v Azure Data Catalog

## <a name="introduction"></a>Úvod

**Microsoft Azure Data Catalog** je plně spravovaná cloudová služba, která slouží jako systém registrace a systém zjišťování pro podnikové zdroje dat. Je to vše, co pomáhá lidem zjišťovat, pochopit a používat zdroje dat a pomáhat organizacím získat více hodnot z existujících zdrojů dat, včetně velkých objemů dat.

**Azure Data Catalog** podporuje registraci Azure Storage objektů BLOB a adresářů a taky souborů a adresářů Hadoop HDFS. Částečně strukturovaný charakter těchto zdrojů dat poskytuje skvělou flexibilitu. Chcete-li však získat největší hodnotu z jejich registrace pomocí **Azure Data Catalog**, uživatelé musí zvážit způsob uspořádání zdrojů dat.

## <a name="directories-as-logical-data-sets"></a>Adresáře jako logické datové sady

Běžným vzorem pro uspořádání velkých zdrojů dat je zacházet s adresáři jako s logickými datovými sadami. Adresáře nejvyšší úrovně slouží k definování sady dat, zatímco podsložky definují oddíly a soubory, které obsahují, ukládají samotná data.

Příkladem tohoto modelu může být:

```text
    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...
```

V tomto příkladu vehicle_maintenance_events a location_tracking_events reprezentují logické sady dat. Každá z těchto složek obsahuje datové soubory, které jsou uspořádány podle roku a měsíce do podsložek. Každá z těchto složek může potenciálně obsahovat stovky nebo tisíce souborů.

V tomto vzoru nezpůsobí registrování jednotlivých souborů s **Azure Data Catalog** pravděpodobně smysl. Místo toho Zaregistrujte adresáře, které reprezentují datové sady, které jsou smysluplné pro uživatele, kteří pracují s daty.

## <a name="reference-data-files"></a>Soubory referenčních dat

Doplňkovým vzorem je ukládání referenčních datových sad jako jednotlivých souborů. Tyto datové sady je možné považovat za "malou" stranu velkých objemů dat a často se podobají dimenzím v analytickém datovém modelu. Referenční datové soubory obsahují záznamy, které slouží k poskytnutí kontextu pro hromadnou práci s datovými soubory uloženými jinde v úložišti velkých objemů dat.

Příkladem tohoto modelu může být:

```text
    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv
```

Když analytik nebo datový vědecký pracovník pracuje s daty obsaženými ve větších adresářových strukturách, můžete data v těchto referenčních souborech použít k poskytnutí podrobnějších informací o entitách, které se označují jenom podle názvu nebo ID ve větší datové sadě.

V tomto modelu dává smysl registrovat jednotlivé soubory referenčních dat pomocí **Azure Data Catalog**. Každý soubor představuje datovou sadu a každá z nich může být označená a zjištěná jednotlivě.

## <a name="alternate-patterns"></a>Alternativní vzory

Vzorce popsané v předchozí části představují dva možné způsoby, jak může být velké úložiště dat uspořádané, ale každá implementace je odlišná. Bez ohledu na to, jakým způsobem jsou zdroje dat strukturované, se při registraci velkých zdrojů dat pomocí **Azure Data Catalog** zaměřte na registraci souborů a adresářů, které představují datové sady, které jsou v rámci vaší organizace pro jiné hodnoty. Při registraci všech souborů a adresářů může být katalog nepřehledný, takže uživatelé budou mít těžší najít, co potřebují.

## <a name="summary"></a>Souhrn

Registrace zdrojů dat pomocí **Azure Data Catalog** usnadňuje zjišťování a pochopení. Když zaregistrujete a pokládáte soubory s velkými objemy dat a adresáře, které představují logické datové sady, můžete uživatelům pomáhat při hledání a používání zdrojů s velkými objemy dat, které potřebují.
