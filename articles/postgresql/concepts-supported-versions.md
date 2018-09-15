---
title: Podporované verze ve službě Azure Database for PostgreSQL
description: Popisuje podporované verze ve službě Azure Database for PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/07/2018
ms.openlocfilehash: f337679279f4a3eb6d01808f15a59fee4c8f3cd1
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630571"
---
# <a name="supported-postgresql-database-versions"></a>Verze podporovaných databáze PostgreSQL
Microsoft se zaměřuje na podporu n-2 verze stroje PostgreSQL služba Azure Database for PostgreSQL, to znamená aktuálně vydané hlavní verzi (n) a dvě předchozí hlavní verze (-2).

Azure Database for PostgreSQL aktuálně podporuje následující verze:

## <a name="postgresql-version-104"></a>Verze PostgreSQL 10.4
Odkazovat [PostgreSQL dokumentaci](https://www.postgresql.org/docs/10/static/release-10-4.html) Další informace o vylepšeních a opravách v této podverze.

## <a name="postgresql-version-969"></a>Verze PostgreSQL 9.6.9
Odkazovat [PostgreSQL dokumentaci](https://www.postgresql.org/docs/9.6/static/release-9-6-9.html) Další informace o vylepšeních a opravách v této podverze.

## <a name="postgresql-version-9513"></a>Verze PostgreSQL 9.5.13
Odkazovat [PostgreSQL dokumentaci](https://www.postgresql.org/docs/9.5/static/release-9-5-13.html) se dozvíte o vylepšení a opravy v tomto podverze.

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů
Azure Database for PostgreSQL automaticky spravuje opravy vedlejší verze aktualizace. Upgrade hlavní verze v současné době se nepodporuje. Například upgrade z PostgreSQL 9.5 PostgreSQL 9.6 není podporován. Pokud chcete upgradovat na novou hlavní verzi, [výpisu a obnovení](./howto-migrate-using-dump-and-restore.md) ho na server, který byl vytvořen v nové verzi modulu.

## <a name="next-steps"></a>Další postup
Informace o podpoře různých rozšíření PostgreSQL najdete v tématu [rozšíření PostgreSQL](concepts-extensions.md).
