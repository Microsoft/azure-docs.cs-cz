---
title: Podporované verze v Azure Database for PostgreSQL – jeden server
description: Popisuje podporované verze v Azure Database for PostgreSQL-jednom serveru.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/26/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: fae8dfb2e2cc532f4aed17f83f13bbee31d29113
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68551355"
---
# <a name="supported-postgresql-database-versions"></a>Podporované verze databáze PostgreSQL
Společnost Microsoft se zaměřuje na podporu n-2 verzí modulu PostgreSQL v Azure Database for PostgreSQLm jediném serveru. Verze by představovaly aktuální hlavní verzi v Azure (n) a dvě předchozí hlavní verze (-2).

Azure Database for PostgreSQL aktuálně podporuje následující verze:

## <a name="postgresql-version-112"></a>PostgreSQL verze 11,2
Další informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-2.html) .

## <a name="postgresql-version-107"></a>PostgreSQL verze 10,7
Další informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-7.html) .

## <a name="postgresql-version-9612"></a>PostgreSQL verze 9.6.12
Další informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html) .

## <a name="postgresql-version-9516"></a>PostgreSQL verze 9.5.16
Informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html) .

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů
Azure Database for PostgreSQL automaticky spravuje opravy dílčí verze. V současné době se upgrade hlavní verze nepodporuje. Například upgrade z PostgreSQL 9,5 na PostgreSQL 9,6 není podporován. Pokud chcete upgradovat na další hlavní verzi, vytvořte [výpis databáze a obnovte](./howto-migrate-using-dump-and-restore.md) ji na server, který byl vytvořen s novou verzí modulu.

> Všimněte si, že před PostgreSQL verze 10 se [zásady správy verzí PostgreSQL](https://www.postgresql.org/support/versioning/) považovaly za upgradované na _hlavní verzi_ , aby se zvýšila první _nebo_ druhé číslo (například 9,5 až 9,6 byla považována za upgrade _hlavní_ verze).
> Od verze 10 se jako upgrade hlavní verze považuje jenom změna v prvním čísle (například 10,0 až 10,1 je upgrade _menší_ verze a 10 až 11 je upgrade _hlavní_ verze).

## <a name="next-steps"></a>Další kroky
Informace o podpoře různých rozšíření PostgreSQL najdete v tématu [rozšíření PostgreSQL](concepts-extensions.md).
