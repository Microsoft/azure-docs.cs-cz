---
title: Podporované verze – Azure Database for PostgreSQL – jeden server
description: Popisuje podporované hlavní a dílčí verze Postgres v Azure Database for PostgreSQLm jediném serveru.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 61dd98028b7342290984615ea19b561b48aaeadb
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792232"
---
# <a name="supported-postgresql-major-versions"></a>Podporované hlavní verze PostgreSQL
Společnost Microsoft se zaměřuje na podporu n-2 verzí modulu PostgreSQL v Azure Database for PostgreSQLm jediném serveru. Verze by představovaly aktuální hlavní verzi v Azure (n) a dvě předchozí hlavní verze (-2).

Azure Database for PostgreSQL aktuálně podporuje následující hlavní verze:

## <a name="postgresql-version-11"></a>PostgreSQL verze 11
Aktuální dílčí verze je 11,5. Další informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-5.html) .

## <a name="postgresql-version-10"></a>PostgreSQL verze 10
Aktuální dílčí verze je 10,10. Další informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-10.html) .

## <a name="postgresql-version-96"></a>PostgreSQL verze 9,6
Aktuální dílčí verze je 9.6.15. Další informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-15.html) .

## <a name="postgresql-version-95"></a>PostgreSQL verze 9,5
Aktuální dílčí verze je 9.5.19. Informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-19.html) .

## <a name="managing-upgrades"></a>Správa upgradů
Projekt PostgreSQL pravidelně řeší nahlášené chyby v menších verzích. Azure Database for PostgreSQL automaticky opraví servery s podverzemi v rámci měsíčního nasazení služby. 

Automatický upgrade hlavní verze se nepodporuje. Například z PostgreSQL 9.5 neproběhne automatický upgrade na PostgreSQL 9.6. Pokud chcete upgradovat na další hlavní verzi, vytvořte [výpis databáze a obnovte ji](./howto-migrate-using-dump-and-restore.md) na serveru vytvořeném s novou verzí jádra.

### <a name="version-syntax"></a>Syntaxe verze
Před verzí PostgreSQL verze 10 se [zásady správy verzí PostgreSQL](https://www.postgresql.org/support/versioning/) považují za upgradované na _hlavní verzi_ , aby se zvýšila první _nebo_ druhé číslo. Například 9,5 až 9,6 byl považován za upgrade _Hlavní_ verze. Od verze 10 se jako upgrade hlavní verze považuje jenom změna v prvním čísle. Například 10,0 až 10,1 je _dílčí_ upgrade pro vydání. Upgrade _Hlavní_ verze je od verze 10 do 11.

## <a name="next-steps"></a>Další kroky
Informace o podporovaných rozšířeních PostgreSQL naleznete v [dokumentu rozšíření](concepts-extensions.md).
