---
title: Podporované verze – Citus (Scale) – Azure Database for PostgreSQL
description: PostgreSQL verze dostupné v Azure Database for PostgreSQL-škálovatelné (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: d8a584b6ba752e8f9220defa575f519828ba07e6
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023943"
---
# <a name="supported-database-versions-in-azure-database-for-postgresql--hyperscale-citus"></a>Podporované verze databáze v Azure Database for PostgreSQL – škálovatelné (Citus)

## <a name="postgresql-versions"></a>Verze PostgreSQL

> [!IMPORTANT]
> Přizpůsobitelná verze PostgreSQL v Citus () je aktuálně ve verzi Preview.  Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
>
> Úplný seznam dalších nových funkcí najdete v části [funkce ve verzi Preview pro Citus (škálování)](hyperscale-preview-features.md).

Verze PostgreSQL spuštěná ve skupině serverů Citus () je při vytváření přizpůsobitelná. Výběr jakékoli jiné verze než verze 11 je aktuálně funkcí Preview.

Citus () aktuálně podporuje následující hlavní verze:

### <a name="postgresql-version-13-preview"></a>PostgreSQL verze 13 (Preview)

Aktuální dílčí verze je 13,2. Další informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/13/static/release-13-2.html) .

### <a name="postgresql-version-12-preview"></a>PostgreSQL verze 12 (Preview)

Aktuální dílčí verze je 12,6. Další informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/12/static/release-12-6.html) .

### <a name="postgresql-version-11"></a>PostgreSQL verze 11

Aktuální dílčí verze je 11,11. Další informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-11.html) .

### <a name="postgresql-version-10-and-older"></a>PostgreSQL verze 10 a starší

PostgreSQL verze 10 a starší nepodporujeme pro Azure Database for PostgreSQL-měřítko (Citus).

## <a name="citus-and-other-extension-versions"></a>Citus a další verze rozšíření

V závislosti na tom, která verze PostgreSQL je spuštěná ve skupině serverů, se nainstalují taky různé [verze rozšíření Postgres](concepts-hyperscale-extensions.md) .  Konkrétně Postgres 13 přináší Citus 10 a starší verze Postgres přináší Citus 9,5.

## <a name="next-steps"></a>Další kroky

* Podívejte se, která [rozšíření](concepts-hyperscale-extensions.md) jsou nainstalována ve kterých verzích.
* Naučte se [vytvořit skupinu serverů Citus (škálování)](quickstart-create-hyperscale-portal.md).
