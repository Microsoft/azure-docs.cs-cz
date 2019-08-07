---
title: Podporované verze v Azure Database for PostgreSQL – jeden server
description: Popisuje podporované verze v Azure Database for PostgreSQL-jednom serveru.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: b77fd082be43b8cbdedf7cbe5875a8931eb0474a
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68837892"
---
# <a name="supported-postgresql-database-versions"></a>Podporované verze databáze PostgreSQL
Společnost Microsoft se zaměřuje na podporu n-2 verzí modulu PostgreSQL v Azure Database for PostgreSQLm jediném serveru. Verze by představovaly aktuální hlavní verzi v Azure (n) a dvě předchozí hlavní verze (-2).

Azure Database for PostgreSQL aktuálně podporuje následující hlavní verze:

## <a name="postgresql-version-11"></a>PostgreSQL verze 11
Aktuální vedlejší verze je 11,4. Další informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-4.html) .

## <a name="postgresql-version-10"></a>PostgreSQL verze 10
Aktuální vedlejší verze je 10,9. Další informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-9.html) .

## <a name="postgresql-version-96"></a>PostgreSQL verze 9,6
Aktuální vedlejší verze je 9.6.14. Další informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-14.html) .

## <a name="postgresql-version-95"></a>PostgreSQL verze 9,5
Aktuální vedlejší verze je 9.5.18. Informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-18.html) .

## <a name="managing-upgrades"></a>Správa upgradů
Azure Database for PostgreSQL automaticky spravuje upgrady dílčí verze. 

Automatické aktualizace hlavní verze se nepodporují. Nejedná se například o automatický upgrade z PostgreSQL 9,5 na PostgreSQL 9,6. Chcete-li upgradovat na další hlavní verzi, vytvořte [výpis databáze a obnovte](./howto-migrate-using-dump-and-restore.md) ji na server, který byl vytvořen s novou verzí modulu.

### <a name="version-syntax"></a>Syntaxe verze
Před verzí PostgreSQL verze 10 se [zásady správy verzí PostgreSQL](https://www.postgresql.org/support/versioning/) považují za upgradované na _hlavní verzi_ , aby se zvýšila první _nebo_ druhé číslo. Například 9,5 až 9,6 byl považován za upgrade _Hlavní_ verze. Od verze 10 se jako upgrade hlavní verze považuje jenom změna v prvním čísle. Například 10,0 až 10,1 je upgrade _menší_ verze. Upgrade _Hlavní_ verze je od verze 10 do 11.

## <a name="next-steps"></a>Další postup
Informace o podporovaných rozšířeních PostgreSQL naleznete v [dokumentu rozšíření](concepts-extensions.md).
