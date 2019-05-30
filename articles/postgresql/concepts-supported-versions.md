---
title: Podporované verze ve službě Azure Database for PostgreSQL – jeden Server
description: Popisuje podporované verze ve službě Azure Database for PostgreSQL – jeden Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4dcbaf159fce6b5f5495a6a25a2a3420cad9e5e8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "65067263"
---
# <a name="supported-postgresql-database-versions"></a>Podporované verze databáze PostgreSQL
Cíle společnosti Microsoft pro podporu n-2 verze stroje PostgreSQL v Azure Database for PostgreSQL – jeden Server. Verze by aktuální hlavní verze v Azure (n) a dvě předchozí hlavní verze (-2).

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
