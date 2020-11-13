---
title: Zásady správy verzí – Azure Database for MySQL – jeden server a flexibilní Server (Preview)
description: Popisuje zásady pro hlavní a dílčí verze MySQL v Azure Database for MySQL
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 4903f1e48eb2f33c68d62c635201474b841ed146
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94591508"
---
# <a name="azure-database-for-mysql-versioning-policy"></a>Zásady správy verzí Azure Database for MySQL

Tato stránka popisuje zásady správy verzí Azure Database for MySQL a platí pro režimy nasazení Azure Database for MySQL-Single server a Azure Database for MySQL-flexibilní Server (Preview).

## <a name="supported--mysql-versions"></a>Podporované verze MySQL

Azure Database for MySQL podporuje následující verze databáze.

| Verze | Jeden server | Flexibilní server (Preview) |
| ----- | :------: | :----: |
| MySQL 8 | X |  | 
| MySQL 5,7 | X | X |
| MySQL 5,6| X |  |


## <a name="major-version-support"></a>Podpora hlavních verzí
Každá hlavní verze MySQL bude podporována Azure Database for MySQL od data, kdy Azure začíná podporovat tuto verzi, dokud nebude verze vyřazením komunitou MySQL, jak je uvedeno v [zásadách správy verzí](https://www.mysql.com/support/eol-notice.html).

## <a name="minor-version-support"></a>Podpora dílčí verze
V rámci pravidelné údržby Azure Database for MySQL automaticky provádí upgrady dílčí verze na verzi služby MySQL upřednostňovanou verzí Azure. 

## <a name="major-version-retirement-policy"></a>Zásada vyřazení hlavních verzí
Následující tabulka uvádí podrobnosti o vyřazení hlavních verzí MySQL. Data následují po [zásadách správy verzí MySQL](https://www.mysql.com/support/eol-notice.html).

| Verze | Co je nového? | Počáteční datum podpory Azure | Datum vyřazení|
| ----- | ----- | ------ | ----- |
| [MySQL 5,6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [Funkce](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 20. března 2018 | Únor 2021
| [MySQL 5,7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [Funkce](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | 20. března 2018 | Říjen 2023
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [Funkce](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)) | 11. prosince 2019 | Duben 2026


## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>Vyřazené verze stroje MySQL nejsou v Azure Database for MySQL podporovány

Po datu vyřazení každé verze databáze MySQL, pokud budete pokračovat v vyřazení verze, pamatujte na následující omezení:
- Vzhledem k tomu, že komunita neuvolňuje žádné další opravy chyb ani opravy zabezpečení, Azure Database for MySQL nebude opravovat vyřazený databázový stroj pro všechny chyby nebo problémy se zabezpečením nebo jinak přijímat bezpečnostní opatření s ohledem na vyřazený databázový stroj. Azure ale bude i nadále provádět pravidelná údržba a opravy pro hostitele, operační systém, kontejnery a jakékoli další součásti související se službou.
- Pokud máte problémy s podporou, ke kterým může dojít v souvislosti s databází MySQL, nemůžeme vám poskytnout podporu. V takových případech budete muset upgradovat databázi, abychom vám mohli poskytnout veškerou podporu.
- Pro vyřazenou verzi nebudete moct vytvářet nové databázové servery. Budete ale moci provádět obnovování v čase a vytvářet repliky pro čtení pro vaše stávající servery.
- Nové funkce služby vyvinuté v Azure Database for MySQL můžou být dostupné jenom pro podporované verze databázového serveru.
- Doba provozu SLA bude platit výhradně pro Azure Database for MySQL problémy související se službou a nikoli na výpadky způsobené chybami souvisejícími s databázovým strojem.  
- V extrémních událostech vážné hrozby pro službu způsobenou ohrožením zabezpečení databázového stroje MySQL zjištěným v vyřazené verzi databáze se může Azure rozhodnout zastavit výpočetní uzel vašeho databázového serveru a nejdřív zabezpečit službu. Před přepnutím serveru do režimu online se zobrazí výzva k upgradu serveru. Během procesu upgradu budou vaše data vždycky chráněná pomocí automatických záloh prováděných ve službě, kterou můžete v případě potřeby použít k obnovení zpět na starší verzi. 



## <a name="next-steps"></a>Další kroky
- Viz [verze podporované](./concepts-supported-versions.md) na jednom serveru Azure Database for MySQL.
- Viz [verze podporované](flexible-server/concepts-supported-versions.md) v Azure Database for MySQL-flexibilním serveru (ve verzi Preview)
- Upgrady najdete v tématu [Výpis výpisu a obnovení](./concepts-migrate-dump-restore.md) MySQL.
