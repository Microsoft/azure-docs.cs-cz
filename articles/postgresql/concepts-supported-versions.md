---
title: Podporované verze – Databáze Azure pro PostgreSQL – jeden server
description: Popisuje podporované hlavní a dílčí verze Postgres v Azure Database for PostgreSQL – Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 61dd98028b7342290984615ea19b561b48aaeadb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792232"
---
# <a name="supported-postgresql-major-versions"></a>Podporované hlavní verze PostgreSQL
Microsoft si klade za cíl podporovat n-2 verze modulu PostgreSQL v Azure Database for PostgreSQL - Single Server. Verze by aktuální hlavní verze v Azure (n) a dvě předchozí hlavní verze (-2).

Azure Database for PostgreSQL aktuálně podporuje následující hlavní verze:

## <a name="postgresql-version-11"></a>PostgreSQL verze 11
Aktuální dílčí verze je 11.5. Další informace o vylepšeních a opravách v této dílčí verzi naleznete v dokumentaci k [PostgreSQL.](https://www.postgresql.org/docs/11/static/release-11-5.html)

## <a name="postgresql-version-10"></a>PostgreSQL verze 10
Aktuální dílčí verze je 10.10. Další informace o vylepšeních a opravách v této dílčí verzi naleznete v dokumentaci k [PostgreSQL.](https://www.postgresql.org/docs/10/static/release-10-10.html)

## <a name="postgresql-version-96"></a>PostgreSQL verze 9.6
Aktuální dílčí verze je 9.6.15. Další informace o vylepšeních a opravách v této dílčí verzi naleznete v dokumentaci k [PostgreSQL.](https://www.postgresql.org/docs/9.6/static/release-9-6-15.html)

## <a name="postgresql-version-95"></a>PostgreSQL verze 9.5
Aktuální dílčí verze je 9.5.19. Informace o vylepšeních a opravách v této menší verzi naleznete v dokumentaci k [PostgreSQL.](https://www.postgresql.org/docs/9.5/static/release-9-5-19.html)

## <a name="managing-upgrades"></a>Správa upgradů
Projekt PostgreSQL pravidelně vydává menší verze k opravě hlášených chyb. Azure Database for PostgreSQL automaticky opravuje servery s menšími verzemi během měsíčních nasazení služby. 

Automatický upgrade hlavní verze se nepodporuje. Například z PostgreSQL 9.5 neproběhne automatický upgrade na PostgreSQL 9.6. Pokud chcete upgradovat na další hlavní verzi, vytvořte [výpis databáze a obnovte ji](./howto-migrate-using-dump-and-restore.md) na serveru vytvořeném s novou verzí jádra.

### <a name="version-syntax"></a>Syntaxe verze
Před PostgreSQL verze 10, [PostgreSQL versioning politika](https://www.postgresql.org/support/versioning/) považována za _hlavní verzi_ upgrade za zvýšení první _nebo_ druhé číslo. Například 9.5 až 9.6 byl považován za _hlavní_ verzi upgrade. Od verze 10 je pouze změna prvního čísla považována za hlavní verzi upgradu. Například 10.0 až 10.1 je _menší_ verze upgrade. Verze 10 až 11 je _hlavní_ verze upgrade.

## <a name="next-steps"></a>Další kroky
Informace o podporovaných rozšířeních PostgreSQL naleznete [v dokumentu o rozšířeních](concepts-extensions.md).
