---
title: Podporované verze – Azure Database for PostgreSQL – jeden server
description: Popisuje podporované hlavní a dílčí verze Postgres v Azure Database for PostgreSQLm jediném serveru.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/16/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 41662c5e4cc0ed9458f8b1b1279e2753daed789f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100518449"
---
# <a name="supported-postgresql-major-versions"></a>Podporované hlavní verze PostgreSQL

Podrobnosti o zásadách podpory najdete v tématu [zásady správy verzí Azure Database for PostgreSQL](concepts-version-policy.md) .

Azure Database for PostgreSQL aktuálně podporuje následující hlavní verze:

## <a name="postgresql-version-11"></a>PostgreSQL verze 11
Aktuální dílčí verze je 11,6. Další informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-6.html) .

## <a name="postgresql-version-10"></a>PostgreSQL verze 10
Aktuální dílčí verze je 10,11. Další informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-11.html) .

## <a name="postgresql-version-96"></a>PostgreSQL verze 9,6
Aktuální dílčí verze je 9.6.16. Další informace o vylepšeních a opravách v této dílčí verzi najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html) .

## <a name="postgresql-version-95-retired"></a>PostgreSQL verze 9,5 (vyřazeno)
V souladu se [zásadami správy verzí](https://www.postgresql.org/support/versioning/)v komunitě Postgres Azure Database for PostgreSQL vyřazení Postgres verze 9,5 od 11. února 2021. Další podrobnosti a omezení najdete v tématu [zásady správy verzí Azure Database for PostgreSQL](concepts-version-policy.md) . Pokud používáte tuto hlavní verzi, upgradujte prosím na novější verzi, nejlépe do PostgreSQL 11.

## <a name="managing-upgrades"></a>Správa upgradů
Projekt PostgreSQL pravidelně řeší nahlášené chyby v menších verzích. Azure Database for PostgreSQL automaticky opravuje podverze na serverech v rámci měsíčních nasazení služby. 

Automatické místní upgrady pro hlavní verze se nepodporují. Pokud chcete upgradovat na vyšší hlavní verzi, můžete 
   * Použijte jednu z metod popsaných v části [inovace hlavních verzí pomocí výpisu a obnovení](./how-to-upgrade-using-dump-and-restore.md).
   * K přesunu databáze na server vytvořený pomocí nové verze modulu použijte [pg_dump a pg_restore](./howto-migrate-using-dump-and-restore.md) .
   * Použijte [službu Azure Database Migration Service](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) pro online upgrady.

### <a name="version-syntax"></a>Syntaxe verze
Před verzí PostgreSQL verze 10 se [zásady správy verzí PostgreSQL](https://www.postgresql.org/support/versioning/) považují za upgradované na _hlavní verzi_ , aby se zvýšila první _nebo_ druhé číslo. Například 9,5 až 9,6 byl považován za upgrade _Hlavní_ verze. Od verze 10 se jako upgrade hlavní verze považuje jenom změna v prvním čísle. Například 10,0 až 10,1 je _dílčí_ upgrade pro vydání. Upgrade _Hlavní_ verze je od verze 10 do 11.

## <a name="next-steps"></a>Další kroky
Informace o podporovaných rozšířeních PostgreSQL naleznete v [dokumentu rozšíření](concepts-extensions.md).
