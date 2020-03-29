---
title: Jak katalogizovat velká data v Azure Data Catalog
description: Postup zvýraznění článku vzory pro použití Azure Data Catalog se zdroji dat "velkých objemů dat", včetně Azure Blob Storage, Azure Data Lake a Hadoop HDFS.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 88dc85003fa2a3e41d8a31055ff8ba9b0fcc7492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71300570"
---
# <a name="how-to-catalog-big-data-in-azure-data-catalog"></a>Jak katalogizovat velká data v Azure Data Catalog

## <a name="introduction"></a>Úvod

**Microsoft Azure Data Catalog** je plně spravovaná cloudová služba, která slouží jako systém registrace a zjišťování pro zdroje podnikových dat. Jde především o to pomoci lidem objevit, pochopit a používat zdroje dat a pomoci organizacím získat větší hodnotu ze stávajících zdrojů dat, včetně velkých objemů dat.

**Azure Data Catalog** podporuje registraci objektů BLOB Azure Blog Storage a adresářů, stejně jako soubory a adresáře Hadoop HDFS. Částečně strukturovaná povaha těchto zdrojů dat poskytuje velkou flexibilitu. Chcete-li však získat co největší hodnotu z jejich registrace v **katalogu dat Azure**, musí uživatelé zvážit, jak jsou uspořádány zdroje dat.

## <a name="directories-as-logical-data-sets"></a>Adresáře jako logické datové sady

Běžným vzorem pro uspořádání zdrojů velkých objemů dat je považovat adresáře za logické sady dat. Nejvyšší úrovně adresářů se používají k definování datové sady, zatímco podsložky definovat oddíly a soubory, které obsahují uložit data sama.

Příkladem tohoto vzoru může být:

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

V tomto příkladu představují vehicle_maintenance_events a location_tracking_events logické sady dat. Každá z těchto složek obsahuje datové soubory, které jsou uspořádány podle roku a měsíce do podsložek. Každá z těchto složek může potenciálně obsahovat stovky nebo tisíce souborů.

V tomto vzoru registrace jednotlivých souborů s **Azure Data Catalog** pravděpodobně nemá smysl. Místo toho zaregistrujte adresáře, které představují sady dat, které jsou smysluplné pro uživatele pracující s daty.

## <a name="reference-data-files"></a>Referenční datové soubory

Doplňkový vzor je ukládání referenčních datových sad jako jednotlivých souborů. Tyto datové soubory lze považovat za "malou" stranu dat velkého objemu a často se podobají rozměrům v analytickém datovém modelu. Referenční datové soubory obsahují záznamy, které slouží k poskytnutí kontextu pro většinu datových souborů uložených jinde v úložišti velkých objemů dat.

Příkladem tohoto vzoru může být:

```text
    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv
```

Pokud analytik nebo datový vědec pracuje s daty obsaženými ve větších adresářových strukturách, data v těchto referenčních souborech lze použít k poskytnutí podrobnějších informací pro entity, které jsou ve větší sadě dat označovány pouze podle názvu nebo ID.

V tomto vzoru má smysl zaregistrovat jednotlivé referenční datové soubory s **Azure Data Catalog**. Každý soubor představuje sadu dat a každý z nich může být anotován a zjištěn jednotlivě.

## <a name="alternate-patterns"></a>Alternativní vzory

Vzory popsané v předchozí části jsou pouze dva možné způsoby, jak úložiště velkých objemů dat může být uspořádány, ale každá implementace se liší. Bez ohledu na to, jak jsou vaše zdroje dat strukturovány, se při registraci zdrojů velkých objemů dat v **katalogu dat Azure**zaměřte na registraci souborů a adresářů, které představují sady dat, které mají hodnotu pro ostatní uživatele ve vaší organizaci. Registrace všech souborů a adresářů může katalog zaneřádit, což uživatelům ztěžuje hledání toho, co potřebují.

## <a name="summary"></a>Souhrn

Registrace zdrojů dat pomocí **Azure Data Catalog** usnadňuje jejich zjišťování a pochopení. Registrací a anotací souborů velkých objemů dat a adresářů, které představují logické datové sady, můžete uživatelům pomoci najít a používat zdroje velkých objemů dat, které potřebují.
