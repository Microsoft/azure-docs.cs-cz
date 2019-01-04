---
title: Podporované verze ve službě Azure Database for PostgreSQL
description: Popisuje podporované verze ve službě Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 5b06128979bf448a0b85084d5178d9291beb7691
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542782"
---
# <a name="supported-postgresql-database-versions"></a>Podporované verze databáze PostgreSQL
Microsoft, zaměřuje na podporu n-2 verze stroje PostgreSQL služba Azure Database for PostgreSQL. Verze by aktuální hlavní verze v Azure (n) a dvě předchozí hlavní verze (-2).

Azure Database for PostgreSQL aktuálně podporuje následující verze:

## <a name="postgresql-version-105"></a>Verze PostgreSQL 10.5
Odkazovat [PostgreSQL dokumentaci](https://www.postgresql.org/docs/10/static/release-10-5.html) Další informace o vylepšeních a opravách v této podverze.

## <a name="postgresql-version-9610"></a>Verze PostgreSQL 9.6.10
Odkazovat [PostgreSQL dokumentaci](https://www.postgresql.org/docs/9.6/static/release-9-6-10.html) Další informace o vylepšeních a opravách v této podverze.

## <a name="postgresql-version-9514"></a>Verze PostgreSQL 9.5.14
Odkazovat [PostgreSQL dokumentaci](https://www.postgresql.org/docs/9.5/static/release-9-5-14.html) se dozvíte o vylepšení a opravy v tomto podverze.

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů
Azure Database for PostgreSQL automaticky spravuje opravy podverze. Upgrade hlavní verze v současné době se nepodporuje. Například upgrade z PostgreSQL 9.5 PostgreSQL 9.6 není podporován. Pokud chcete upgradovat na novou hlavní verzi, vytvořit databázi [výpisu a obnovení](./howto-migrate-using-dump-and-restore.md) ho na server, který byl vytvořen v nové verzi modulu.

## <a name="next-steps"></a>Další postup
Informace o podpoře různých rozšíření PostgreSQL najdete v tématu [rozšíření PostgreSQL](concepts-extensions.md).
