---
title: Podporované verze – Azure Database for PostgreSQL-flexibilní Server
description: Popisuje podporované hlavní a dílčí verze PostgreSQL v Azure Database for PostgreSQLm flexibilním serveru.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: b139e2f14379e0cd52ca72c347d49bcb4512c473
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542076"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>Podporované hlavní verze PostgreSQL v Azure Database for PostgreSQL – flexibilní Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

Azure Database for PostgreSQL – flexibilní server aktuálně podporuje následující hlavní verze:

## <a name="postgresql-version-12"></a>PostgreSQL verze 12

Aktuální dílčí verze je 12,4. Další informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/12/static/release-12-4.html) .

## <a name="postgresql-version-11"></a>PostgreSQL verze 11

Aktuální dílčí verze je 11,9. Další informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-9.html) .

## <a name="postgresql-version-10-and-older"></a>PostgreSQL verze 10 a starší

Pro Azure Database for PostgreSQL flexibilní Server nepodporujeme PostgreSQL verze 10 a starší. Pokud požadujete starší verze, použijte prosím možnost nasazení na [jeden server](../concepts-supported-versions.md) .

## <a name="managing-upgrades"></a>Správa upgradů

Projekt PostgreSQL pravidelně řeší nahlášené chyby v menších verzích. Azure Database for PostgreSQL automaticky opraví servery s podverzemi v rámci měsíčního nasazení služby.

Automatizace pro upgrade hlavní verze ještě není podporovaná. Například v současnosti není k dispozici automatický upgrade z PostgreSQL 11 na PostgreSQL 12.<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->