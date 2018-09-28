---
title: Jak pracovat s "velké objemy dat" zdroje dat ve službě Azure Data Catalog
description: Článek vzory pro používání Azure Data Catalog se zdroji dat "velké objemy dat", včetně Azure Blob Storage, Azure Data Lake a systémem souborů HDFS Hadoop zvýraznění.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: 626d1568-0780-4726-bad1-9c5000c6b31a
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 42b002e3494d84084979f2f5b27e9679e61cbeb5
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407718"
---
# <a name="how-to-work-with-big-data-sources-in-azure-data-catalog"></a>Jak pracovat s velkým objemem dat ve službě Azure Data Catalog
## <a name="introduction"></a>Úvod
**Microsoft Azure Data Catalog** je plně spravovaná Cloudová služba, která slouží jako systém registrace a systém zjišťování pro podnikové zdroje dat. To je vše o lidech, pomáhá zjišťovat, pochopit a pomocí zdroje dat a pomáhá organizacím vytěžit více z jejich stávajících zdrojů dat, včetně velkých objemů dat.

**Azure Data Catalog** podporuje registraci Azure Blog Storage BLOB a adresáře a systémem souborů HDFS Hadoop souborů a adresářů. Částečně strukturovaná povahu těchto zdrojů dat poskytuje flexibilitu. Však získat co nejvíce z jejich registrace **Azure Data Catalog**, uživatelé nutné vzít v úvahu uspořádání datových zdrojů.

## <a name="directories-as-logical-data-sets"></a>Adresáře jako logické sady dat
Běžným vzorem pro uspořádání velkým objemem dat je adresáře považovat za logické datové sady. Adresářů nejvyšší úrovně se používají k definování datové sady, zatímco podsložky Definujte oddíly a ukládat soubory, které obsahují vlastní data.

Příkladem tohoto modelu můžou být:

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

V tomto příkladu vehicle_maintenance_events a location_tracking_events představují logické datové sady. Všechny tyto složky obsahují datových souborů, které jsou uspořádány podle roku a měsíce do podsložky. Každá z těchto složek mohou obsahovat stovky nebo tisíce souborů.

V tomto vzoru registrace jednotlivých souborů s **Azure Data Catalog** pravděpodobně nemá smysl. Místo toho zaregistrujte adresáře, které představují datových sad, které smysl uživatelům práci s daty.

## <a name="reference-data-files"></a>Referenční datové soubory
Doplňkové vzor je uložit jako samostatné soubory referenční datové sady. Tyto datové sady mohou představit jako "malé" strana velké objemy dat a jsou často podobné dimenzí v modelu analytická data. Referenční datové soubory obsahují záznamy, které se používají k zajištění kontext pro hromadné datové soubory uloženy jinde velkých objemů dat v úložišti.

Příkladem tohoto modelu můžou být:

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

Když analytik nebo data odborník je práce s daty součástí větší struktury adresářů, data v těchto referenčních souborů je možné poskytnout podrobné informace pro entity, které jsou uvedené pouze podle názvu nebo ID v datové sadě větší.

V tomto vzoru, je vhodné zaregistrovat individuální referenční datové soubory s **Azure Data Catalog**. Každý soubor představuje datové sady a každý z nich je možné s poznámkami a zjistit jednotlivě.

## <a name="alternate-patterns"></a>Alternativní vzory
Jenom dva možné způsoby, které mohou být uspořádány úložišti velkých objemů dat jsou uvedené v předchozí části vzorce, ale každý implementaci se liší. Bez ohledu na to, jak jsou strukturované zdroje dat, při registraci zdrojů velkých objemů dat s **Azure Data Catalog**, zaměřte se na registraci soubory a adresáře, které představují datových sad, které jsou hodnoty ostatním uživatelům v rámci vaší organizace. Registruje všechny soubory a adresáře můžete dál sbližuje tyto katalogu, kvůli tomu je těžší uživatelům najít to, co potřebují.

## <a name="summary"></a>Souhrn
Registrace zdrojů dat s **Azure Data Catalog** usnadňují jejich zjišťování a pochopení. Pomocí registrace a zadávání poznámek k velké objemy dat souborů a adresářů, které představují logické datové sady, můžete pomoci uživatelům najít a používat zdroje velkých objemů dat, které potřebují.
