---
title: Podporované verze – Azure Database for PostgreSQL-flexibilní Server
description: Popisuje podporované hlavní a dílčí verze Postgres v Azure Database for PostgreSQLm flexibilním serveru.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 9d651a38361ccaa7f8788fb4a375b39b2953228b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934889"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>Podporované hlavní verze PostgreSQL v Azure Database for PostgreSQL – flexibilní Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

Azure Database for PostgreSQL – flexibilní server aktuálně podporuje následující hlavní verze:

## <a name="postgresql-version-12"></a>PostgreSQL verze 12

Aktuální dílčí verze je 12,1. Další informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/12/static/release-12-1.html) .

## <a name="postgresql-version-11"></a>PostgreSQL verze 11

Aktuální dílčí verze je 11,8. Další informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-8.html) .

## <a name="postgresql-version-10-and-older"></a>PostgreSQL verze 10 a starší

Pro Azure Database for PostgreSQL flexibilní Server nepodporujeme PostgreSQL verze 10 a starší. Pokud požadujete starší verze, použijte prosím možnost nasazení na [jeden server](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) .

## <a name="managing-upgrades"></a>Správa upgradů

Projekt PostgreSQL pravidelně řeší nahlášené chyby v menších verzích. Azure Database for PostgreSQL automaticky opraví servery s podverzemi v rámci měsíčního nasazení služby.

Automatické aktualizace hlavní verze se zatím nepodporují. Například v současnosti není k dispozici automatický upgrade z PostgreSQL 11 na PostgreSQL 12.<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->
