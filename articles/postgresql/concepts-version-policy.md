---
title: Zásady správy verzí – Azure Database for PostgreSQL – jeden server a flexibilní Server (Preview)
description: Popisuje zásady týkající se Postgres hlavních a dílčích verzí v Azure Database for PostgreSQLm jednom serveru.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 62fe1b3391eb4cb2d409a92b936fd3f1ae56d992
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518415"
---
# <a name="azure-database-for-postgresql-versioning-policy"></a>Zásady správy verzí služby Azure Database for PostgreSQL

Tato stránka popisuje zásady správy verzí Azure Database for PostgreSQL a platí pro režimy nasazení Azure Database for PostgreSQL-Single server a Azure Database for PostgreSQL-flexibilní Server (Preview).

## <a name="supported--postgresql-versions"></a>Podporované verze PostgreSQL

Azure Database for PostgreSQL podporuje následující verze databáze.

| Verze | Jeden server | Flexibilní server (Preview) |
| ----- | :------: | :----: |
| PostgreSQL 12 |  | ×  | 
| PostgreSQL 11 | × | × |
| PostgreSQL 10 | × |  |
| PostgreSQL 9,6 | × |  |
| *PostgreSQL 9,5 (vyřazeno)* | × |  |

## <a name="major-version-support"></a>Podpora hlavních verzí
Každá hlavní verze PostgreSQL bude podporována nástrojem Azure Database for PostgreSQL od data, kdy Azure začíná podporovat tuto verzi, dokud nebude verze vyřazením komunitou PostgreSQL, jak je uvedeno v [zásadách správy verzí komunity PostgreSQL](https://www.postgresql.org/support/versioning/).

## <a name="minor-version-support"></a>Podpora dílčí verze
Azure Database for PostgreSQL v rámci pravidelné údržby automaticky provádí upgrady podverze na upřednostňovanou verzi PostgreSQL Azure. 

## <a name="major-version-retirement-policy"></a>Zásada vyřazení hlavních verzí
Následující tabulka uvádí podrobnosti o vyřazení hlavních verzí PostgreSQL. Data se řídí [zásadami správy verzí komunity PostgreSQL](https://www.postgresql.org/support/versioning/).

| Verze | Co je nového | Počáteční datum podpory Azure | Datum vyřazení|
| ----- | ----- | ------ | ----- |
| [PostgreSQL 9,5 (vyřazeno)](https://www.postgresql.org/about/news/postgresql-132-126-1111-1016-9621-and-9525-released-2165/)| [Funkce](https://www.postgresql.org/docs/9.5/release-9-5.html)  | 18. dubna 2018   | 11. února 2021
| [PostgreSQL 9,6](https://www.postgresql.org/about/news/postgresql-96-released-1703/) | [Funkce](https://wiki.postgresql.org/wiki/NewIn96) | 18. dubna 2018  | 11. listopadu 2021
| [PostgreSQL 10](https://www.postgresql.org/about/news/postgresql-10-released-1786/) | [Funkce](https://wiki.postgresql.org/wiki/New_in_postgres_10) | 4. června 2018  | 10. listopadu 2022
| [PostgreSQL 11](https://www.postgresql.org/about/news/postgresql-11-released-1894/) | [Funkce](https://www.postgresql.org/docs/11/release-11.html) | 24. července 2019  | 9. listopadu 2023
| [PostgreSQL 12](https://www.postgresql.org/about/news/postgresql-12-released-1976/) | [Funkce](https://www.postgresql.org/docs/12/release-12.html) | Září 22, 2020  | 14. listopadu 2024

## <a name="retired-postgresql-engine-versions-not-supported-in-azure-database-for-postgresql"></a>Vyřazené verze modulu PostgreSQL nejsou podporované v Azure Database for PostgreSQL

Vystaralou verzi můžete nadále spouštět v Azure Database for PostgreSQL. Upozorňujeme však, že po datu vyřazení pro každou verzi databáze PostgreSQL platí následující omezení:
- Vzhledem k tomu, že komunita neuvolňuje žádné další opravy chyb ani opravy zabezpečení, Azure Database for PostgreSQL nebude opravovat vyřazený databázový stroj pro všechny chyby nebo problémy se zabezpečením nebo jinak přijímat bezpečnostní opatření s ohledem na vyřazený databázový stroj. V důsledku toho může docházet k chybám zabezpečení nebo jiným problémům. Azure ale bude i nadále provádět pravidelná údržba a opravy pro hostitele, operační systém, kontejnery a jakékoli další součásti související se službou.
- Pokud máte problémy s podporou, ke kterým může docházet v souvislosti s databází PostgreSQL, můžeme vám poskytnout podporu. V takových případech budete muset upgradovat databázi, abychom vám mohli poskytnout veškerou podporu.
- Pro vyřazenou verzi nebudete moct vytvářet nové databázové servery. Budete ale moci provádět obnovování v čase a vytvářet repliky pro čtení pro vaše stávající servery.
- Nové funkce služby vyvinuté v Azure Database for PostgreSQL můžou být dostupné jenom pro podporované verze databázového serveru.
- Doba provozu SLA bude platit výhradně pro Azure Database for PostgreSQL problémy související se službou a nikoli na výpadky způsobené chybami souvisejícími s databázovým strojem.  
- V extrémní události vážné hrozby pro službu, která je způsobená chybou zabezpečení databázového stroje PostgreSQL identifikovanou v vyřazené verzi databáze, může Azure zvolit zastavení databázového serveru k zabezpečení služby. V takovém případě budete upozorněni na upgrade serveru před přepnutím serveru do režimu online.

## <a name="postgresql-version-syntax"></a>Syntaxe verze PostgreSQL
Před verzí PostgreSQL verze 10 se [zásady správy verzí PostgreSQL](https://www.postgresql.org/support/versioning/) považují za upgradované na _hlavní verzi_ , aby se zvýšila první _nebo_ druhé číslo. Například 9,5 až 9,6 byl považován za upgrade _Hlavní_ verze. Od verze 10 se jako upgrade hlavní verze považuje jenom změna v prvním čísle. Například 10,0 až 10,1 je _dílčí_ upgrade pro vydání. Upgrade _Hlavní_ verze je od verze 10 do 11.

## <a name="next-steps"></a>Další kroky
- Viz [verze podporované](./concepts-supported-versions.md) na jednom serveru Azure Database for PostgreSQL.
- Viz [verze podporované](flexible-server/concepts-supported-versions.md) v Azure Database for PostgreSQL-flexibilním serveru (ve verzi Preview)
- Informace o tom, jak provádět upgrady hlavní verze, najdete v dokumentaci k [inovacím hlavních verzí](how-to-upgrade-using-dump-and-restore.md) .
- Informace o podporovaných rozšířeních PostgreSQL naleznete v [dokumentu rozšíření](concepts-extensions.md).
