---
title: Podporované verze ve službě Azure Database for PostgreSQL
description: Popisuje podporované verze ve službě Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 16064d662c5b101e30f8d2fbb64b39db0848f49f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702311"
---
# <a name="supported-postgresql-database-versions"></a>Podporované verze databáze PostgreSQL
Microsoft, zaměřuje na podporu n-2 verze stroje PostgreSQL služba Azure Database for PostgreSQL. Verze by aktuální hlavní verze v Azure (n) a dvě předchozí hlavní verze (-2).

Azure Database for PostgreSQL aktuálně podporuje následující verze:

## <a name="postgresql-version-107"></a>PostgreSQL Version 10.7
Odkazovat [PostgreSQL dokumentaci](https://www.postgresql.org/docs/10/static/release-10-7.html) Další informace o vylepšeních a opravách v této podverze.

## <a name="postgresql-version-9612"></a>Verze PostgreSQL 9.6.12
Odkazovat [PostgreSQL dokumentaci](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html) Další informace o vylepšeních a opravách v této podverze.

## <a name="postgresql-version-9516"></a>Verze PostgreSQL 9.5.16
Odkazovat [PostgreSQL dokumentaci](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html) se dozvíte o vylepšení a opravy v tomto podverze.

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů
Azure Database for PostgreSQL automaticky spravuje opravy podverze. Upgrade hlavní verze v současné době se nepodporuje. Například upgrade z PostgreSQL 9.5 PostgreSQL 9.6 není podporován. Pokud chcete upgradovat na novou hlavní verzi, vytvořit databázi [výpisu a obnovení](./howto-migrate-using-dump-and-restore.md) ho na server, který byl vytvořen v nové verzi modulu.

## <a name="next-steps"></a>Další postup
Informace o podpoře různých rozšíření PostgreSQL najdete v tématu [rozšíření PostgreSQL](concepts-extensions.md).
