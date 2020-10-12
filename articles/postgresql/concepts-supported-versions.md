---
title: Podporované verze – Azure Database for PostgreSQL – jeden server
description: Popisuje podporované hlavní a dílčí verze Postgres v Azure Database for PostgreSQLm jediném serveru.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/22/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: cfe4b92dbed69440ee2c07cff758faad7e01293f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707910"
---
# <a name="supported-postgresql-major-versions"></a>Podporované hlavní verze PostgreSQL
Společnost Microsoft se zaměřuje na podporu n-2 verzí modulu PostgreSQL v Azure Database for PostgreSQLm jediném serveru. Verze by představovaly aktuální hlavní verzi v Azure (n) a dvě předchozí hlavní verze (-2).

Azure Database for PostgreSQL aktuálně podporuje následující hlavní verze:

## <a name="postgresql-version-11"></a>PostgreSQL verze 11
Aktuální dílčí verze je 11,6. Další informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-6.html) .

## <a name="postgresql-version-10"></a>PostgreSQL verze 10
Aktuální dílčí verze je 10,11. Další informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-11.html) .

## <a name="postgresql-version-96"></a>PostgreSQL verze 9,6
Aktuální dílčí verze je 9.6.16. Další informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html) .

## <a name="postgresql-version-95"></a>PostgreSQL verze 9,5
Aktuální dílčí verze je 9.5.20. Informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-20.html) .

## <a name="managing-upgrades"></a>Správa upgradů
Projekt PostgreSQL pravidelně řeší nahlášené chyby v menších verzích. Azure Database for PostgreSQL automaticky opraví servery s podverzemi v rámci měsíčního nasazení služby. 

Automatické místní upgrady pro hlavní verze se nepodporují. Pokud chcete upgradovat na další hlavní verzi, můžete 
   * Použití [pg_dump a pg_restore](./howto-migrate-using-dump-and-restore.md) k přesunu databáze na server vytvořený pomocí nové verze modulu
   * Alternativně můžete upgradovat z PostgreSQL 10 na 11 pomocí [služby Azure Database Migration Service](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) .

### <a name="version-syntax"></a>Syntaxe verze
Před verzí PostgreSQL verze 10 se [zásady správy verzí PostgreSQL](https://www.postgresql.org/support/versioning/) považují za upgradované na _hlavní verzi_ , aby se zvýšila první _nebo_ druhé číslo. Například 9,5 až 9,6 byl považován za upgrade _Hlavní_ verze. Od verze 10 se jako upgrade hlavní verze považuje jenom změna v prvním čísle. Například 10,0 až 10,1 je _dílčí_ upgrade pro vydání. Upgrade _Hlavní_ verze je od verze 10 do 11.

## <a name="next-steps"></a>Další kroky
Informace o podporovaných rozšířeních PostgreSQL naleznete v [dokumentu rozšíření](concepts-extensions.md).
